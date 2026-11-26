# Plan Green IT (versión 2: un solo equipo)

Apagado y encendido programado de recursos no críticos, con métricas para Prometheus.

| Elemento | Dónde | Horario | Script |
|---|---|---|---|
| restore-test (LXC 106) | pve1 | Bajo demanda; cierre diario a las 23:00 | `green-vm.sh` |
| Puestos de trabajo (18) | ans01 (SSH + Wake-on-LAN) | L-V 07:45–19:30 | `green-pcs.sh` |

pve1 y sus cinco servicios permanentes (fw01, proxy01, app01, mon01, ans01) **no se apagan**: sostienen la VPN de
teletrabajo, las copias de las 02:30, las alertas y las actualizaciones.

## Uso
```bash
ansible-playbook playbooks/green_it.yml --check --diff   # simulación
ansible-playbook playbooks/green_it.yml                  # aplicar
```
Los días de `green_dias_cerrados` no se enciende nada. Métricas: `novacloud_green_vcpu_horas_ahorradas_total`,
`novacloud_green_invitado_encendido` y `novacloud_green_puestos_horas_ahorradas_total` (directorio textfile de node_exporter).

## Estado de las pruebas
Probado con simulaciones (`pct`, `ssh` y `wakeonlan` falsos): idempotencia del apagado, encendido, día cerrado, cálculo
de horas y fallo de un puesto. **El playbook no se ha ejecutado con Ansible real** (pendiente en el laboratorio).
