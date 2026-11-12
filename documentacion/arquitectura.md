# Arquitectura de NovaCloud

Resumen del Blueprint de la Tarea 10. Dos nodos Proxmox VE (pve1, pve2) con ZFS en espejo,
un servidor de copias físico (pbs1) y un único router/firewall (fw01, OPNsense) que enruta entre VLAN.

| VLAN | Zona | Red | Servicios |
| --- | --- | --- | --- |
| 10 | Gestión | 10.108.10.0/24 | bas01, ans01, mon01, nodos Proxmox |
| 20 | DMZ | 10.108.20.0/24 | proxy01 (Nginx + ModSecurity) |
| 30 | Servicios internos | 10.108.30.0/24 | ipa01, erp01, nc01 |
| 40 | Datos | 10.108.40.0/24 | db01 (PostgreSQL) |
| 50 | Copias | 10.108.50.0/24 | pbs1 (Proxmox Backup Server) |
| 60 | Usuarios | 10.108.60.0/24 | Puestos de la oficina (DHCP) |

Otras redes: VPN de usuarios (WireGuard) 10.108.100.0/24, VPN de administradores (OpenVPN)
10.108.101.0/24 y enlace directo pve1–pve2 10.108.90.0/30.

## Política de tráfico
Bloqueo por defecto. Desde Internet solo se abren 443/TCP, 51820/UDP (WireGuard) y 1194/UDP (OpenVPN).
La DMZ solo habla con erp01 (8069/8072) y nc01 (443); la base de datos solo acepta a erp01 y nc01
en el 5432; el servidor de copias no inicia conexiones; los administradores entran solo por bas01.
