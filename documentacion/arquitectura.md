# Arquitectura de NovaCloud (versión 2: un solo equipo)

Resumen de la Tarea 10 (v2). Un único ordenador (Pentium Gold G5400, 8 GB de RAM, HDD de 500 GB, una tarjeta de red)
con Proxmox VE (pve1) y un firewall (fw01, OPNsense) que enruta entre cuatro VLAN. Sin alta disponibilidad.

| VLAN | Zona | Red | Servicios |
| --- | --- | --- | --- |
| 10 | Gestión | 10.108.10.0/24 | pve1 (.5), ans01 (.10, Ansible y bastion), mon01 (.20) |
| 20 | DMZ | 10.108.20.0/24 | proxy01 (Nginx) |
| 30 | Servicios internos | 10.108.30.0/24 | app01 (Nextcloud y PostgreSQL), restore-test (.90, bajo demanda) |
| 60 | Usuarios | 10.108.60.0/24 | Puestos de la oficina (DHCP) |

Otras redes: VPN de usuarios (WireGuard) 10.108.100.0/24 y VPN de administradores (OpenVPN) 10.108.101.0/24.

## Política de tráfico
Bloqueo por defecto con 17 reglas. Desde Internet solo se abren 443/TCP, 51820/UDP (WireGuard) y 1194/UDP (OpenVPN).
La DMZ solo habla con app01 (443); los administradores entran solo por ans01.

## Límites asumidos
Sin HA (objetivo de laboratorio: RPO de 24 h), copias en el mismo disco más una copia cifrada remota parcial,
y 100 GB de datos de demostración. Sin FreeIPA, ERP, Loki, Suricata ni ModSecurity por falta de RAM y CPU.
