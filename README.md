# Tailscale + Pi-hole en Docker

Docker Compose para instalar Tailscale y Pi-hole en Raspberry Pi, PC o servidores NAS.
Tailscale permite acceder de forma remota a tu red local sin necesidad de abrir puertos en el router, mientras que Pi-hole bloquea anuncios y rastreadores en todos los dispositivos de tu red local o conectados mediante Tailscale.

## 📺 Tutorial completo en YouTube
https://youtube.com/@danielotech

## 🛒 Hardware recomendado (links de afiliado)

### Raspberry Pi
- [Raspberry Pi 5 (4GB)](https://amzlink.to/az0kKjY684Uj9)
- [Raspberry Pi 5 (8GB)](https://amzlink.to/az08MBpttxAE0)
- [Raspberry Pi 5 (Kit de Inicio)](https://amzlink.to/az0aU2Htv0MxJ)
- [Carcasa oficial para Raspberry Pi 5](https://amzlink.to/az0IhBFDFvJxC)
- [Ventilador oficial para Raspberry Pi 5](https://amzlink.to/az0OSK6YxlGEr)
- [Fuente de alimentación para Raspberry Pi](https://amzlink.to/az0mr1rElpTPl)
- [Tarjeta MicroSD para Raspberry Pi](https://amzlink.to/az0XrnBu87lwI)
- [Almacenamiento NVMe para Raspberry Pi](https://amzlink.to/az0NebFbPO6YI)
- [Carcasa para Raspberry Pi 5 + HAT NVMe](https://amzlink.to/az0Gzz3rMqaFK)

### Servidores NAS
- [UGREEN NAS DX2800](https://amzlink.to/az0qwiIp6ZNSl)
- [UGREEN NAS DH4300 Plus](https://amzlink.to/az0Oyzom5kXuu)
- [UGREEN NAS DXP4800](https://amzlink.to/az0BZ6iK7V3LS)
- [UGREEN NAS DXP4800 Plus](https://amzlink.to/az04N89RnbyGU)
- [Disco duro HDD para NAS](https://amzlink.to/az0ouuFKDlp18)

## ⚙️ Instalación

Usa un `docker-compose.yml` u otro dependiendo del dispositivo donde quieras instalarlo.

- Raspberry Pi / PC: [`raspberry-pi/docker-compose.yml`](https://github.com/danielterreros/tailscale-pihole/blob/main/raspberry-pi/docker-compose.yml)
- Servidor NAS (UGREEN, Synology, QNAP, etc): [`nas-server/docker-compose.yml`](https://github.com/danielterreros/tailscale-pihole/blob/main/nas-server/docker-compose.yml)

## 🌍 Acceso Web

### Tailscale:
https://tailscale.com

### Pi-hole (Raspberry Pi / PC):
`http://IP-DE-TU-SERVIDOR/admin`

### Pi-hole (Servidor NAS):
`http://IP-DE-TU-PIHOLE/admin`

## ⚠️ Importante
- Antes de desplegar los contenedores, modifica las líneas que contengan `# Comentario`.
- Utiliza contraseñas robustas para acceder a cada servicio.
- No expongas los servicios directamente a Internet.

## 📄 Documentación Oficial

### Tailscale
- [Tailscale en Docker](https://tailscale.com/docs/features/containers/docker/how-to/connect-docker-container)
- [Tailscale GitHub](https://github.com/tailscale-dev/docker-guide-code-examples)
- [Tailscale Docker Hub](https://hub.docker.com/r/tailscale/tailscale)

### Pi-hole
- [Pi-hole en Docker](https://docs.pi-hole.net/docker/)
- [Pi-hole GitHub](https://github.com/pi-hole/docker-pi-hole)
- [Pi-hole Docker Hub](https://hub.docker.com/r/pihole/pihole)
