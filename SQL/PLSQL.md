
PLSQL es el manejo de sentencias SQL en paquetes y funciones, la sintaxis es muy similar a el lenguaje de programación Pascal, combinado con sentencias SQL.

## Procedimientos PLSQL

Los procedimientos PLSQL son funciones que no retornan un valor, sino que realizan una acción solamente, esta puede impactar o no en la base de datos por nunca devolverá datos.
Los hermosos procedimientos PL/SQL constan de las tres partes típicas de una función en programación:

```PLSQL
procedure nombreDelProcedimiento(parametrosSirequiere)
is
begin
	--Cuerpo del procedimiento
end nombreDelProcedimiento;
```

Un caso de uso podría ser el siguiente:

```PLSQL
procedure updateEstado(p_id IN VARCHAR2, p_estado IN VARCHAR2 DEFAULT NULL)
IS
BEGIN
	UPDATE Dato SET ESTADO = p_estado WHERE ID = p_id;
	COMMIT; --la sentencia commit se usa para hacer efectivos los cambios en la BD
END updateEstado;
```

Ahora te dejo unos procedimientos un poquito mas complejos para que puedas analizarlos:

```PLSQL
procedure update_appraisal_status(
                p_id     in number,
                p_status in varchar2)
    is
    begin
        update eba_demo_appr_appraisals
        set status = upper(p_status)
        where id = p_id;    
        case upper(p_status)
            when 'ORIGINATED' then
                update eba_demo_appr_appraisals
                set date_originated = sysdate
                where id = p_id;
            when 'SUBMITTED' then
                update eba_demo_appr_appraisals
                set input_completed = sysdate
                where id = p_id;
            when 'MGR_SUBMITTED' then
                update eba_demo_appr_appraisals
                set manager_completed = sysdate
                where id = p_id;
            when 'VP_REVIEWED' then
                update eba_demo_appr_appraisals
                set vp_review_date = sysdate
                where id = p_id;
            else
                null;
        end case;
    end update_appraisal_status;
```

```PLSQL
procedure approval_vacation_handler(
                p_param    in apex_approval.t_vacation_rule_input,
                p_result  out apex_approval.t_vacation_rule_result)
    is
        l_participants     apex_t_varchar2;
        l_taskdef_name     apex_tasks.task_def_name%type;
    begin
        l_taskdef_name := taskdef_name(p_param.task_def_static_id);
        handle_temp_business_admin(
            p_participants            => p_param.original_participants,
            p_taskdef_name            => l_taskdef_name,
            p_additional_participants => p_result.participant_changes);
        l_participants := involved_participants(p_param.original_participants);
        if l_participants.count > 0 then
            --
            -- Loop over vacation rows where the current date falls between the
            -- optional start/end dates and the "For Which Approval?" (task_def_ids)
            -- column includes the current taskdef id
            --
            for j in (select original_user,substitute_user,reason
                        from eba_demo_appr_vacation 
                       where    (original_user is null
                             or original_user in (select column_value
                                                    from table(l_participants)))
                         and instr(':'||task_def_ids||':',':'||p_param.task_def_static_id||':') > 0
                         and nvl(start_date,sysdate) <= sysdate and nvl(end_date,sysdate) >= sysdate ) loop
                if j.original_user is null then
                    -- Treat a null original_user as a wildcard, so create a vacation
                    -- assignment for each of the original participants to be substituted
                    -- by the substitute_user
                    for k in 1..l_participants.count loop
                        add_participant(
                            p_additional_participants => p_result.participant_changes,
                            p_taskdef_name            => l_taskdef_name,
                            p_old                     => l_participants(k), 
                            p_new                     => j.substitute_user, 
                            p_reason                  => j.reason,
                            p_wildcard                => true);
                    end loop;
                else
                    add_participant(
                        p_additional_participants => p_result.participant_changes,                        
                        p_taskdef_name            => l_taskdef_name,
                        p_old                     => j.original_user,
                        p_new                     => j.substitute_user, 
                        p_reason                  => j.reason);
                end if;
            end loop;
            p_result.has_participant_changes := p_result.participant_changes.count > 0;
        end if;
    end;
```


## Funciones PLSQL

