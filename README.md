# Práctica 8: automatización con Ansible y Docker

Este laboratorio crea cinco contenedores Ubuntu con servidor SSH. Ansible se conecta desde la computadora (o desde Ubuntu en WSL) usando puertos locales diferentes y configura cada servidor.

## Requisitos

- Docker Engine y el plugin Docker Compose (`docker compose`).
- Ansible instalado en el host o en WSL. En Ubuntu/WSL: `sudo apt update && sudo apt install -y ansible sshpass`.
- Si usas Windows, instala y abre WSL primero con `wsl --install`.

## 1. Crear y levantar los cinco servidores

Desde esta carpeta, ejecuta:

```bash
docker compose up -d --build
docker compose ps
```

Los contenedores publican SSH en `127.0.0.1`, puertos `2221` a `2225`. En cada uno existe el usuario `ansible` con contraseña `ansible` y sudo sin contraseña.

## 2. Probar la conexión

```bash
ansible servidores -m ansible.builtin.ping
```

Si Ansible se queja de que no encuentra `sshpass`, instala ese paquete en el entorno donde ejecutas Ansible. No guardes estas credenciales de laboratorio en sistemas reales.

## 3. Ejecutar el playbook

```bash
ansible-playbook playbook.yml
```

El playbook actualiza la caché APT, crea el usuario `itla`, crea `/home/itla/app/hola.txt` e instala `cowsay` y `htop` en los cinco servidores.

Para comprobar un servidor:

```bash
ansible servidores -a 'cat /home/itla/app/hola.txt'
ansible servidores -a 'dpkg -l cowsay htop'
```

## 4. Detener el laboratorio

```bash
docker compose down
```

## Archivos

- `Dockerfile`: imagen Ubuntu con SSH, Python y el usuario `ansible`.
- `docker-compose.yml`: definición de los cinco contenedores.
- `inventario.ini`: hosts, puertos y credenciales del laboratorio.
- `ansible.cfg`: inventario predeterminado y desactivación de la comprobación de claves SSH.
- `playbook.yml`: tareas automatizadas.

Las credenciales están en texto plano deliberadamente para simplificar esta práctica local. No reutilices esta configuración en producción.
