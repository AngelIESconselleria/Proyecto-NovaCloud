# Pruebas de idempotencia y validación

```
ansible-playbook playbooks/site.yml --syntax-check
ansible-playbook playbooks/site.yml --check --diff
ansible-playbook playbooks/site.yml          # primera ejecución
ansible-playbook playbooks/site.yml          # segunda: debe terminar con changed=0
```

El resto de pruebas de red (ping, traceroute, nmap) están en el plan de la Tarea 10.
