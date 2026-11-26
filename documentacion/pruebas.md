# Pruebas de idempotencia y validación

```
ansible-playbook playbooks/site.yml --syntax-check
ansible-playbook playbooks/site.yml --check --diff
ansible-playbook playbooks/site.yml          # primera ejecución
ansible-playbook playbooks/site.yml          # segunda: debe terminar con changed=0
```

Las pruebas de red (ping, traceroute, nmap) y la de copia y restauración (T10) están en el plan de la Tarea 10 (v2).
