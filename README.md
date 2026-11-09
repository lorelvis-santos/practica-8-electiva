Lorelvis Santos 20251020

# Práctica 8: Ansible y cinco servidores Ubuntu en Docker

Este laboratorio crea cinco contenedores Ubuntu 24.04 con SSH y automatiza su configuración con Ansible.

## Requisitos

- Docker con Docker Compose v2
- Ansible en la máquina de control
- `sshpass`, necesario para que Ansible se autentique por SSH con la contraseña de este laboratorio

En Ubuntu o WSL ejecuta:

```bash
sudo apt update
sudo apt install -y ansible sshpass
```

En Windows, instala WSL desde PowerShell con `wsl --install`, abre Ubuntu y ejecuta los comandos anteriores. Si usas Docker Desktop, habilita la integración con tu distribución WSL.

## Crear y arrancar los cinco servidores

Desde esta carpeta, construye la imagen y arranca los contenedores:

```bash
docker compose build
docker compose up -d
```

También se puede construir e iniciar en un solo paso con `docker compose up -d --build`.

Los servidores se exponen solo en la máquina local para las conexiones SSH de Ansible:

| Servidor | IP usada por Ansible | Puerto SSH |
| --- | --- | ---: |
| servidor1 | 127.0.0.1 | 2221 |
| servidor2 | 127.0.0.1 | 2222 |
| servidor3 | 127.0.0.1 | 2223 |
| servidor4 | 127.0.0.1 | 2224 |
| servidor5 | 127.0.0.1 | 2225 |

## Ejecutar Ansible

`ansible.cfg` selecciona `inventario.ini` y desactiva la comprobación de claves SSH para este laboratorio. El inventario conecta a cada puerto local con el usuario `ansible` y la contraseña `ansible`; dicho usuario tiene `sudo` sin contraseña dentro de los contenedores.

Comprueba la conexión a los cinco hosts:

```bash
ansible servidores -m ping
```

Ejecuta el playbook:

```bash
ansible-playbook playbook.yml
```

El playbook espera a SSH, actualiza la caché APT, crea el usuario `itla`, crea `/home/itla/app/hola.txt` e instala `cowsay` y `htop` en cada servidor.

Puedes verificar el resultado con:

```bash
ansible servidores -a "id itla && cat /home/itla/app/hola.txt && dpkg -s cowsay htop | grep '^Status:'"
```

## Detener los contenedores

```bash
docker compose down
```

Las credenciales están en texto plano intencionalmente para practicar. Úsalas solo en este laboratorio local; para otros entornos, almacena contraseñas con Ansible Vault y utiliza claves SSH.
