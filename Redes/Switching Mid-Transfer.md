# Protocol Switching Mid-Transfer - Diseño Técnico Detallado

## Problema Central

Cambiar de protocolo (TCP ↔ UDP) durante una transferencia activa de archivos sin pérdida de datos ni interrupciones visibles para el usuario.

## Estados del Sistema

### Transfer State Machine

```
TRANSFER_TCP_ACTIVE
    ↓ (condiciones degradadas)
NEGOTIATING_SWITCH_TO_UDP
    ↓ (handshake exitoso)
TRANSFER_UDP_ACTIVE
    ↓ (condiciones mejoradas)  
NEGOTIATING_SWITCH_TO_TCP
    ↓ (handshake exitoso)
TRANSFER_TCP_ACTIVE
```

## Protocolo de Negociación del Switch

### 1. Detección de Necesidad de Switch

```c
typedef struct {
    float rtt_threshold;          // 200ms para switch a UDP
    float loss_threshold;         // 5% para switch a UDP
    float bandwidth_threshold;    // 1Mbps para decisiones
    int samples_for_decision;     // 5 muestras consecutivas
} switch_criteria_t;

bool should_switch_to_udp(network_metrics_t* metrics) {
    return (metrics->rtt_ms > 200.0 || 
            metrics->packet_loss > 0.05) &&
           metrics->samples_count >= 5;
}
```

### 2. Switch Negotiation Protocol

#### Fase 1: Propuesta de Switch

```
Cliente → Servidor: SWITCH_REQUEST
{
    magic: 0xDEADBEEF
    current_protocol: TCP/UDP
    target_protocol: UDP/TCP
    current_offset: bytes_transferred
    reason: NETWORK_DEGRADED/NETWORK_IMPROVED
    timestamp: current_time
}
```

#### Fase 2: Validación del Servidor

```
Servidor valida:
- ¿El offset coincide con lo que tiene registrado?
- ¿Las condiciones justifican el cambio?
- ¿Tiene capacidad para manejar el nuevo protocolo?

Servidor → Cliente: SWITCH_RESPONSE
{
    status: ACCEPT/REJECT/RETRY
    confirmed_offset: server_byte_count
    new_port: puerto_para_nuevo_protocolo
    session_id: identificador_unico
}
```

### 3. Atomic Protocol Handoff

#### Algoritmo de Cambio Atómico

```c
int perform_protocol_switch(transfer_context_t* ctx, protocol_t new_protocol) {
    // Fase 1: Pause current transfer
    pause_current_transfer(ctx);
    
    // Fase 2: Flush pending data
    flush_pending_buffers(ctx);
    
    // Fase 3: Negotiate with server
    if (negotiate_switch(ctx, new_protocol) != SUCCESS) {
        resume_current_transfer(ctx);
        return SWITCH_FAILED;
    }
    
    // Fase 4: Create new connection
    int new_socket = create_socket_for_protocol(new_protocol);
    if (new_socket < 0) {
        resume_current_transfer(ctx);
        return SWITCH_FAILED;
    }
    
    // Fase 5: Atomic handoff
    ctx->old_socket = ctx->current_socket;
    ctx->current_socket = new_socket;
    ctx->current_protocol = new_protocol;
    
    // Fase 6: Resume transfer from exact offset
    if (resume_transfer_from_offset(ctx, ctx->confirmed_offset) != SUCCESS) {
        // Rollback
        ctx->current_socket = ctx->old_socket;
        ctx->current_protocol = get_previous_protocol(new_protocol);
        resume_current_transfer(ctx);
        return SWITCH_FAILED;
    }
    
    // Fase 7: Close old connection
    close(ctx->old_socket);
    
    return SWITCH_SUCCESS;
}
```

## Manejo de Estado Durante el Switch

### Transfer Context Structure

```c
typedef struct {
    // Connection management
    int current_socket;
    int old_socket;
    protocol_t current_protocol;
    
    // Transfer state
    uint64_t total_file_size;
    uint64_t bytes_transferred;
    uint64_t confirmed_offset;
    
    // Buffering para continuidad
    char* pending_buffer;
    size_t pending_size;
    
    // Switch state
    bool switch_in_progress;
    time_t switch_start_time;
    int switch_retries;
    
    // Performance metrics
    network_metrics_t current_metrics;
    performance_history_t perf_history;
} transfer_context_t;
```

### Buffer Management During Switch

```c
// Antes del switch: guardar datos pendientes
int save_pending_data(transfer_context_t* ctx) {
    // TCP: leer datos del socket buffer
    ctx->pending_size = recv(ctx->current_socket, 
                            ctx->pending_buffer, 
                            BUFFER_SIZE, MSG_DONTWAIT);
    
    // UDP: guardar paquetes en cola de recepción
    save_udp_pending_packets(ctx);
    
    return SUCCESS;
}

// Después del switch: restaurar datos pendientes
int restore_pending_data(transfer_context_t* ctx) {
    if (ctx->pending_size > 0) {
        // Procesar datos que quedaron en el buffer anterior
        process_file_chunk(ctx->pending_buffer, ctx->pending_size);
        ctx->bytes_transferred += ctx->pending_size;
        ctx->pending_size = 0;
    }
    return SUCCESS;
}
```

## Detección de Condiciones para Switch

### Network Monitor en Background Thread

```c
void* network_monitor_thread(void* arg) {
    transfer_context_t* ctx = (transfer_context_t*)arg;
    
    while (ctx->transfer_active) {
        // Medir condiciones cada 2 segundos
        network_metrics_t metrics = measure_network_conditions(ctx);
        
        // Aplicar filtro para evitar oscilaciones
        filtered_metrics_t filtered = apply_exponential_smoothing(&metrics, ctx);
        
        // Evaluar necesidad de switch
        if (should_switch_protocol(ctx, &filtered)) {
            // Iniciar switch asíncrono
            initiate_protocol_switch(ctx, determine_optimal_protocol(&filtered));
        }
        
        sleep(2);
    }
}
```

### Anti-Oscillation Logic

```c
bool should_switch_protocol(transfer_context_t* ctx, filtered_metrics_t* metrics) {
    // Evitar cambios demasiado frecuentes
    if (time_since_last_switch(ctx) < MIN_SWITCH_INTERVAL) {
        return false;
    }
    
    // Requerir evidencia consistente (hysteresis)
    if (ctx->current_protocol == TCP) {
        return metrics->degraded_samples >= 5;
    } else { // UDP
        return metrics->improved_samples >= 5;
    }
}
```

## Recovery y Error Handling

### Switch Failure Recovery

```c
int handle_switch_failure(transfer_context_t* ctx, switch_error_t error) {
    switch (error) {
        case NEGOTIATION_TIMEOUT:
            // Reintentar con backoff exponencial
            ctx->switch_retries++;
            delay = min(INITIAL_DELAY * pow(2, ctx->switch_retries), MAX_DELAY);
            schedule_retry(ctx, delay);
            break;
            
        case SERVER_REJECTED_SWITCH:
            // Continuar con protocolo actual
            resume_current_transfer(ctx);
            mark_switch_unavailable(ctx, TEMPORARY_DISABLE_DURATION);
            break;
            
        case SOCKET_CREATION_FAILED:
            // Error crítico, abortar switch
            resume_current_transfer(ctx);
            log_critical_error("Cannot create socket for new protocol");
            break;
    }
}
```

### Data Integrity Verification

```c
int verify_transfer_integrity_after_switch(transfer_context_t* ctx) {
    // Verificar que el offset del servidor coincide
    uint64_t server_offset = query_server_offset(ctx);
    
    if (server_offset != ctx->bytes_transferred) {
        // Resincronizar desde el último checkpoint conocido
        log_warning("Offset mismatch after switch, resyncing");
        return resync_transfer_from_checkpoint(ctx);
    }
    
    return SUCCESS;
}
```

## Métricas de Rendimiento

### Performance Tracking

```c
typedef struct {
    // Pre-switch metrics
    float throughput_before_mbps;
    float avg_rtt_before_ms;
    float packet_loss_before;
    
    // Post-switch metrics
    float throughput_after_mbps;
    float avg_rtt_after_ms;
    float packet_loss_after;
    
    // Switch overhead
    uint32_t switch_duration_ms;
    uint32_t bytes_lost_during_switch;
    
    // Success/failure tracking
    bool switch_successful;
    switch_error_t failure_reason;
} switch_performance_metrics_t;
```

## Casos de Prueba Críticos

### Test Case 1: Switch During High Transfer Rate

- Transferir archivo 1GB a máxima velocidad
- Inducir degradación de red al 50% completado
- Verificar switch exitoso sin pérdida de throughput

### Test Case 2: Multiple Rapid Switches

- Simular red inestable con condiciones cambiantes
- Verificar que anti-oscillation evita cambios excesivos
- Medir overhead acumulado de múltiples switches

### Test Case 3: Switch Failure Recovery

- Simular falla de negotiation durante switch
- Verificar rollback automático a protocolo anterior
- Confirmar integridad de datos después del rollback

## Implementación de Proof of Concept

El PoC debe demostrar:

1. Switch exitoso TCP → UDP durante transferencia activa
2. Reanudación exacta desde offset correcto
3. Mejora medible de performance post-switch
4. Manejo robusto de fallos durante negotiation
5. Transparencia total para la aplicación cliente