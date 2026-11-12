# NovaCloud · Nube privada empresarial

**Alumno:** Ángel De Luis García (NIA 10889708) · CFGS ASIR, 2.º curso · IES Conselleria, València
**Tutora:** Maria de los Llano Gomez Sarrion · Proyecto intermodular 2026-2027

## Descripción del proyecto

NovaCloud es una empresa tecnológica que diseña y opera nubes privadas para pymes de la logística
portuaria de la Comunitat Valenciana. Este repositorio guarda el código de automatización, las
configuraciones, las variables, las plantillas y la documentación de la infraestructura del primer
cliente (Early Adopter): una transitaria de València con 25 usuarios.

## Objetivo de la infraestructura

Sustituir un servidor antiguo y sin soporte por una infraestructura que sea:

- **Disponible:** dos nodos Proxmox con réplica ZFS y reinicio automático (RTO de 15 min, RPO de 1 h).
- **Segura:** seis VLAN, firewall con bloqueo por defecto, VPN y acceso de administración solo por un bastion host.
- **Recuperable:** copias 3-2-1 en un servidor físico aparte, con restauraciones de prueba.
- **Reproducible:** todo se despliega con Ansible y cada cambio queda en el historial de Git.

## Tecnologías utilizadas

| Categoría | Tecnología |
| --- | --- |
| Virtualización | Proxmox VE (KVM y LXC) con ZFS |
| Sistema operativo | Debian, AlmaLinux 9 (FreeIPA) y OPNsense |
| Servidor web | Nginx (proxy inverso) y Apache (Nextcloud) |
| Base de datos | PostgreSQL |
| Contenedores | LXC y Docker |
| Automatización | Ansible, cloud-init y scripts de Bash |
| Firewall | OPNsense, Suricata y nftables |
| Monitorización | Prometheus, Grafana, Loki y Alertmanager |
| Copias de seguridad | Proxmox Backup Server |
| Control de versiones | Git (Gitea) |

## Estructura del repositorio

```
novacloud/
├── playbooks/
│   └── site.yml              # Playbook principal: aplica todos los roles
├── roles/
│   ├── seguridad/            # SSH con claves, sin root, actualizaciones automáticas
│   ├── monitorizacion/       # node_exporter en todos los servidores y Prometheus en mon01
│   ├── servicios_web/        # Nginx como proxy inverso con HTTPS
│   ├── firewall/             # Reglas nftables locales (bloqueo por defecto)
│   ├── base_datos/           # PostgreSQL: una base de datos y un usuario por aplicación
│   └── copias_seguridad/     # Script de copia de /etc y de PostgreSQL, con cron
├── variables/                # Variables de red, firewall, base de datos y plantilla de secretos
├── plantillas/               # Plantillas Jinja2 de los ficheros de configuración
├── inventario/               # Inventario de Ansible por grupos (hosts.yml)
├── documentacion/            # Arquitectura, stack, pruebas y convenciones de Git
├── ansible.cfg               # Configuración de Ansible
├── Dockerfile                # Entorno de ejecución reproducible
├── requirements.txt          # Paquetes de Python (ansible-core 2.20, ansible-lint, yamllint)
├── requirements.yml          # Colecciones de Ansible
└── README.md
```

## Uso

```
# Entorno (Docker) o, en el nodo de control, python3 -m venv + pip install -r requirements.txt
ansible-galaxy collection install -r requirements.yml

ansible-playbook playbooks/site.yml --syntax-check
ansible-playbook playbooks/site.yml --check --diff
ansible-playbook playbooks/site.yml --vault-id novacloud@/etc/ansible/.vault_pass
```

Una segunda ejecución seguida debe terminar con `changed=0` (idempotencia).

## Secretos

Las contraseñas no están en el repositorio. Se copian de `variables/secretos.ejemplo.yml` a
`variables/secretos.yml` y se cifran con Ansible Vault; la contraseña del Vault vive fuera del
repositorio. El firewall local viene desactivado (`firewall_aplicar: false`) hasta probarlo en el laboratorio.

## Estado

Proyecto académico en desarrollo: los roles son una primera versión y los ejemplos de dominio y
certificados deben sustituirse por los reales del cliente.
