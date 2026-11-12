# Plan Green IT

Apagado y encendido programado de recursos no críticos, con métricas para Prometheus.

| Elemento | Dónde | Horario | Script |
|---|---|---|---|
| erp-pre (VM 201) | nodos Proxmox | L-V 07:30–19:30 | `green-vm.sh` |
| restore-test (VM 202) | nodos Proxmox | bajo demanda; cierre diario 23:00 | `green-vm.sh` |
| Puestos de trabajo (18) | ans01 (SSH + Wake-on-LAN) | L-V 07:45–19:30 | `green-pcs.sh` |

Los servidores de producción, PBS (y QDevice), ans01, mon01 y bas01 **no se apagan**: sostienen RTO 15 min, RPO 1 h, copias, alertas y actualizaciones.

## Uso
```bash
ansible-playbook playbooks/green_it.yml --check --diff   # simulación
ansible-playbook playbooks/green_it.yml                  # aplicar
```
Los días de `green_dias_cerrados` no se enciende nada. Métricas: `novacloud_green_vcpu_horas_ahorradas_total`, `novacloud_green_invitado_encendido`, `novacloud_green_puestos_horas_ahorradas_total` (directorio textfile de node_exporter).

## Estado de las pruebas
Probado con simulaciones (qm, ssh y wakeonlan falsos): idempotencia del apagado, encendido, día cerrado, cálculo de horas y fallo de un puesto. **El playbook no se ha ejecutado con Ansible real** (pendiente en el laboratorio).
