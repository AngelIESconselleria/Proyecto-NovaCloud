# NovaCloud · Nube privada empresarial

**Alumno:** Ángel De Luis García (NIA 10889708) · CFGS ASIR, 2.º curso · IES Conselleria, València
**Tutora:** Maria de los Llano Gomez Sarrion · Proyecto intermodular 2026-2027

## Descripción del proyecto

NovaCloud es una empresa tecnológica que diseña y opera nubes privadas para pymes de la logística
portuaria de la Comunitat Valenciana. Este repositorio guarda el código de automatización, las
configuraciones, las variables, las plantillas y la documentación de la infraestructura del primer
cliente (Early Adopter): una transitaria de València con 25 usuarios. El prototipo se monta en un solo
ordenador de 8 GB, por lo que el diseño está recortado (ver documentacion/arquitectura.md).

## Objetivo de la infraestructura

Sustituir un servidor antiguo y sin soporte por una infraestructura que sea:

- **Funcional con un solo equipo:** un PC de 8 GB con Proxmox, sin presupuesto. Sin alta disponibilidad: objetivo de laboratorio de RPO 24 h.
- **Segura:** cuatro VLAN, firewall con bloqueo por defecto, VPN y acceso de administración solo por ans01 (bastion host).
- **Recuperable:** copias `vzdump` locales y una copia cifrada remota, con restauración de prueba (no se cumple 3-2-1).
- **Reproducible:** todo se despliega con Ansible y cada cambio queda en el historial de Git.

## Tecnologías utilizadas

| Categoría | Tecnología |
| --- | --- |
| Virtualización | Proxmox VE (KVM y LXC) con LVM-thin |
| Sistema operativo | Debian 13 y OPNsense |
| Servidor web | Nginx (proxy inverso) y Apache (Nextcloud) |
| Base de datos | PostgreSQL |
| Contenedores | LXC y Docker |
| Automatización | Ansible y scripts de Bash |
| Firewall | OPNsense y nftables |
| Monitorización | Prometheus y Grafana |
| Copias de seguridad | vzdump y rclone |
| Control de versiones | Git (GitHub) |

## Estructura del repositorio

```
├── playbooks/
│   ├── site.yml              # Playbook principal: aplica todos los roles
│   └── green_it.yml          # Plan Green IT (Tarea 13)
├── roles/
│   ├── green_it/             # Apagado programado de puestos y entornos de prueba, con métricas
│   ├── green_it/             # Apagado programado de puestos y del entorno de pruebas, con métricas
│   ├── seguridad/            # SSH con claves, sin root, actualizaciones automáticas
│   ├── monitorizacion/       # node_exporter en todos los servidores y Prometheus en mon01
│   ├── servicios_web/        # Nginx como proxy inverso con HTTPS
│   ├── firewall/             # Reglas nftables locales (bloqueo por defecto)
│   ├── base_datos/           # PostgreSQL: una base de datos y un usuario por aplicación
│   └── copias_seguridad/     # Copia de /etc y PostgreSQL, vzdump local y rclone en pve1
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
