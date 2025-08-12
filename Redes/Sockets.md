## Estrategias de Implementación

### **1. Enfoque de "Handoff con Estado Compartido"**

La idea es mantener un estado compartido de la transferencia y hacer un "handoff" coordinado:

```cpp
// Estructura para mantener el estado de transferencia
struct TransferState {
    std::string filename;
    size_t total_size;
    size_t bytes_transferred;
    int current_protocol; // 0=UDP, 1=TCP
    int packet_count;
    
    // Para checkpoint
    std::vector<bool> packet_received; // bitmap de paquetes recibidos
};
```

#### **Flujo de cambio de protocolo:**

1. **Cliente detecta cambio necesario** (ej: paquete #5)
2. **Pausa la transferencia actual**
3. **Envía mensaje de control:** "SWITCH_TO_TCP"
4. **Cierra socket UDP**
5. **Abre nuevo socket TCP**
6. **Envía estado actual:** bytes transferidos, posición
7. **Servidor responde:** "READY_TCP"
8. **Continúa transferencia desde la posición actual**

### **2. Implementación Práctica**

```cpp
// Cliente
int transfer_with_protocol_switch() {
    TransferState state = {0};
    int current_socket;
    
    // Iniciar con UDP
    current_socket = setup_udp_connection();
    state.current_protocol = PROTOCOL_UDP;
    
    while (state.bytes_transferred < state.total_size) {
        // Verificar si necesita cambio
        if (should_switch_protocol(&state)) {
            if (switch_protocol(&state, &current_socket) < 0) {
                return -1;
            }
        }
        
        // Continuar transferencia
        transfer_chunk(&state, current_socket);
    }
}

int switch_protocol(TransferState* state, int* socket) {
    if (state->current_protocol == PROTOCOL_UDP) {
        // UDP -> TCP
        send_control_message(*socket, "SWITCH_TO_TCP");
        close(*socket);
        
        *socket = setup_tcp_connection();
        send_resume_info(*socket, state->bytes_transferred);
        state->current_protocol = PROTOCOL_TCP;
    }
    // TCP -> UDP sería similar
}
```

### **3. Protocolo de Control**

Necesitas un protocolo de control para coordinar el cambio:

```cpp
// Mensajes de control
#define MSG_SWITCH_TO_TCP    1
#define MSG_SWITCH_TO_UDP    2
#define MSG_RESUME_TRANSFER  3
#define MSG_PROTOCOL_READY   4

struct ControlMessage {
    int type;
    size_t resume_position;
    char data[256];
};
```

### **4. Manejo en el Servidor**

```cpp
int server_with_protocol_switch() {
    TransferState state = {0};
    int current_socket = udp_socket;
    
    while (true) {
        if (is_control_message(current_socket)) {
            ControlMessage msg = receive_control_message(current_socket);
            
            switch (msg.type) {
                case MSG_SWITCH_TO_TCP:
                    close(current_socket);
                    current_socket = setup_tcp_server();
                    send_control_message(current_socket, MSG_PROTOCOL_READY);
                    break;
                case MSG_RESUME_TRANSFER:
                    state.bytes_transferred = msg.resume_position;
                    seek_file_position(state.bytes_transferred);
                    break;
            }
        } else {
            // Continuar recibiendo datos
            receive_data_chunk(&state, current_socket);
        }
    }
}
```

### **5. Consideraciones Importantes**

#### **Sincronización:**
- **Checkpoints:** Guardar posición cada N paquetes
- **Acknowledgments:** Confirmar recepción antes del cambio
- **Timeouts:** Manejar fallos durante el cambio

#### **Buffering:**
```cpp
// Mantener buffer para datos en tránsito
struct ProtocolBuffer {
    char data[BUFFER_SIZE];
    size_t pending_bytes;
    bool has_pending_data;
};
```

#### **Detección de momento para cambio:**
```cpp
bool should_switch_protocol(TransferState* state) {
    // Tu trigger manual: cambiar en el paquete 5
    if (state->packet_count == 5 && state->current_protocol == PROTOCOL_UDP) {
        return true;
    }
    
    // Futuros triggers automáticos:
    // - Alta pérdida de paquetes en UDP
    // - Latencia muy alta en TCP
    // - Congestión de red
    
    return false;
}
```

### **6. Ventajas y Desventajas**

#### **Ventajas:**
- Flexibilidad total durante transferencia
- Optimización dinámica según condiciones
- Recuperación de fallos

#### **Desventajas:**
- Complejidad de implementación
- Overhead del cambio de protocolo
- Posible pérdida temporal de datos
- Sincronización compleja

### **7. Alternativa Más Simple: "Dual Stack"**

Una alternativa es mantener ambas conexiones abiertas:

```cpp
struct DualConnection {
    int tcp_socket;
    int udp_socket;
    int active_protocol;
};

// Cambiar solo el socket activo, sin cerrar conexiones
```

¿Te interesa que profundice en alguna de estas estrategias o prefieres ver un ejemplo de implementación específico?