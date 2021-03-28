# btps.firewalld

Installation et configuration de ```firewalld```.

Les rôles de services (bind, ssh, etc.) eux-même doivent définir les règles de routages (ouverture de port, etc.).

## Variables
```yaml
---
# Enable/disable filter services
# - service: http (any firewalld service and custom service)
#   zone: public (any firewalld zone, default : public)
#   permanent: False (True/False, default : True)
#   timeout: 3600 (timeout in secondes if permanent = False, default : omit)
#   source: "192.168.0.1" (any IP or range of IP, default : omit)
#   state: enabled (enabled/disabled)

# Global services rules (defined in "all" group or others top level groups)
firewalld_global_services: []

# Services rules specific to host
firewalld_services: []

# Enable/disable filter ports
# - port: "80/tcp" (port/protocol or port-port/protocol for range of ports)
#   zone: public (any firewalld zone, default : public)
#   permanent: False (True/False, default : True)
#   timeout: 3600 (timeout in secondes if permanent = False, default : omit)
#   source: "192.168.0.1" (any IP or range of IP, default : omit)
#   state: enabled (enabled/disabled)

# Global ports rules (defined in "all" group or others top level groups)
firewalld_global_ports: []

# Ports rules specific to host
firewalld_ports: []

# Enable/disable masquerades
# - masquerade: yes (yes/no)
#   zone: public (any firewalld zone, default : public)
#   permanent: False (True/False, default : True)
#   timeout: 3600 (timeout in secondes if permanent = False, default : omit)
#   state: enabled (enabled/disabled)

# Global masquerades rules (defined in "all" group or others top level groups)
firewalld_global_masquerades: []

# Ports rules specific to host
firewalld_masquerades: []

# Enable/disable rich rules
# - rich_rule: 'rule service name="ftp" audit limit value="1/m" accept' (any firewalld rich rule)
#   zone: public (any firewalld zone, default : public)
#   permanent: False (True/False, default : True)
#   timeout: 3600 (timeout in secondes if permanent = False, default : omit)
#   state: enabled (enabled/disabled)

# Global rich rules (defined in "all" group or others top level groups)
firewalld_global_rich_rules: []

# Rich rules specific to host
firewalld_rich_rules: []

# Links network interfaces to zones
# - interface: "enps03" (any network interface name)
#   zone: public (any firewalld zone, default : public)
#   permanent: False (True/False, default : True)
#   timeout: 3600 (timeout in secondes if permanent = False, default : omit)
#   state: enabled (enabled/disabled)

# Global interfaces rules (defined in "all" group or others top level groups)
firewalld_global_interfaces: []

# Ports interfaces specific to host
firewalld_interfaces: []

# Service
firewalld_service_started: true

```

## Exemple d'utilisation du rôle
```yaml
---
- hosts: all
  vars:
    api:
      firewalld_custom_services:
        - service: http-81
          short: WWW (HTTP)
          description: "HTTP is the protocol used to serve Web pages. If you plan to make your Web server publicly available, enable this option. This option is not required for viewing pages locally or developing Web pages."
          ports:
            - number: [81]
              protocol: tcp
      firewalld_services:
        - service: http-81
          state: enabled
          zone: public
          permanent: False
          timeout: 3600
    # Global services rules (defined in "all" group or others top level groups)
    firewalld_global_services:
      - service: http
        state: enabled
        zone: public
        permanent: true
    # Services rules specific to host
    firewalld_services:
      - service: https
        state: enabled
        zone: public
        permanent: true
    # Global ports rules (defined in "all" group or others top level groups)
    firewalld_global_ports:
      - port: "8080/tcp"
        state: enabled
        zone: public
        permanent: true
    # Ports rules specific to host
    firewalld_ports:
       - port: "2500-5000/tcp"
         state: enabled
         zone: public
         permanent: true

  # Use role
  roles:
    - role: btps.firewalld

  # Use API to create custom service, enable/disable service
  tasks:
    - name: run role "btps.firewalld" and configure firewalld for apache
      import_role:
        name: "btps.firewalld"
        tasks_from: api-configure-services.yml
      vars:
        firewalld_services: "{{ api.firewalld_services | default([]) }}"
        firewalld_custom_services: "{{ api.firewalld_custom_services | default([]) }}"

```

## Notes

Inspiré de :
- https://github.com/marcelnijenhof/ansible-role-firewalld
- https://github.com/memiah/ansible-role-firewalld

Documentations :
- Services:
    - http://www.firewalld.org/documentation/howto/add-a-service.html
    - http://www.firewalld.org/documentation/service/options.html

## Todo

Create custom zones
