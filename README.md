# 🐳 Tailscale + Pi-hole en Docker

Docker Compose para instalar Tailscale y Pi-hole en Raspberry Pi, PC o servidores NAS.
Tailscale permite acceder de forma remota a tu red local sin necesidad de abrir puertos en el router, mientras que Pi-hole bloquea anuncios y rastreadores en todos los dispositivos de tu red local o conectados mediante Tailscale.

### 📺 [Tutorial completo en YouTube](https://youtube.com/@danielotech)

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

## ✅ Requisitos previos
Si estás empezando en el mundo del self-hosting y los servidores caseros, estos vídeos te servirán como base para utilizar una Raspberry Pi, un PC o un NAS como servidor doméstico con servicios autoalojados. Te recomiendo verlos antes de continuar con esta guía.
- [Raspberry Pi | Configuración Inicial](https://youtu.be/xRsxs5eBpmI?si=E7SvINDe1LTBV80S)
- [Raspberry Pi / PC | Instalar Docker y Portainer](https://youtu.be/-7vvELophxU?si=jD1oQdPo2f9jWDQN)
- [Servidor NAS | Instalar Docker y Portainer](https://youtu.be/hOiNrQXN-VE?si=ekD4vuqoADXxLphR)

## ⚙️ Instalación

Usa el `docker-compose.yml` correspondiente según el dispositivo donde lo vayas a instalar. Antes de desplegar los contenedores, modifica las líneas que contengan `# Comentario`.

### Raspberry Pi / PC

```yaml
services:
  tailscale:
    image: tailscale/tailscale:latest
    container_name: tailscale
    hostname: raspberrypi # Nombre de tu dispositivo.
    restart: unless-stopped
    environment:
      - TS_HOSTNAME=raspberrypi # Nombre de tu dispositivo.
      - TS_AUTHKEY=tskey-auth-xxxxxxxx # Key de autenticación. Generar en el panel web de Tailscale.
      - TS_STATE_DIR=/var/lib/tailscale
      - TS_ROUTES=192.168.1.0/24 # Rango de IPs de tu red local.
      - TS_EXTRA_ARGS=--advertise-exit-node
      - TS_USERSPACE=false
      - TS_AUTH_ONCE=true
    volumes:
      - ./tailscale:/var/lib/tailscale # Directorio para archivos persistentes.
    devices:
      - /dev/net/tun:/dev/net/tun
    cap_add:
      - net_admin
      - net_raw
    sysctls:
      - net.ipv4.ip_forward=1
      - net.ipv6.conf.all.forwarding=1
      
  pihole:
    image: pihole/pihole:latest
    container_name: pihole
    hostname: raspberrypi # Nombre de tu dispositivo.
    restart: unless-stopped
    ports:
      - "53:53/tcp"
      - "53:53/udp"
      - "80:80/tcp"
      - "443:443/tcp"
     # - "67:67/udp" # Opcional: usar Pi-hole como servidor DHCP.
     # - "123:123/udp" # Opcional: usar Pi-hole como cliente NTP.
    environment:
      - TZ=Europe/Madrid # Zona horaria.
      - FTLCONF_webserver_api_password=tupassword # Password de acceso a Pi-hole.
     # - FTLCONF_webserver_port=8080,8443s # Opcional: cambiar puertos 80:80 (HTTP) y 443:443 (HTTPS) para acceso web.
      - FTLCONF_dns_listeningMode=ALL
     # - FTLCONF_dns_specialDomains_iCloudPrivateRelay=true # Opcional: relay privado de Apple.
      - PIHOLE_UID=1000 # UID de tu máquina. Comprobar con comando: id -u
      - PIHOLE_GID=1000 # UID de tu máquina. Comprobar con comando: id -g
    volumes:
      - ./pihole:/etc/pihole # Directorio para archivos persistentes.
    cap_add:
     # - NET_ADMIN # Opcional: usar Pi-hole como servidor DHCP.
     # - SYS_TIME # Opcional: usar Pi-hole como cliente NTP.
      - SYS_NICE
```


### Servidor NAS (UGREEN, Synology, QNAP, etc)

```yaml
networks:          
  lan_macvlan:
    driver: macvlan
    driver_opts:
      parent: eth0 # Interfaz de red de tu máquina. Comprobar con comando: ip route | grep default
    ipam:
      config:
        - subnet: 192.168.1.0/24 # Rango de IPs de tu red local.
          gateway: 192.168.1.1 # IP de tu router.

services:          
  tailscale:
    image: tailscale/tailscale:latest
    container_name: tailscale
    hostname: ugreennas # Nombre de tu dispositivo.
    restart: unless-stopped
    networks:
      lan_macvlan:
        ipv4_address: 192.168.1.4 # IP libre en tu LAN para Tailscale.
    environment:
      - TS_HOSTNAME=ugreennas # Nombre de tu dispositivo.
      - TS_AUTHKEY=tskey-auth-xxxxxxxx # Key de autenticación. Generar en el panel web de Tailscale.
      - TS_STATE_DIR=/var/lib/tailscale
      - TS_ROUTES=192.168.1.0/24 # Rango de IPs de tu red local.
      - TS_EXTRA_ARGS=--advertise-exit-node
      - TS_USERSPACE=false
      - TS_AUTH_ONCE=true
    volumes:
      - ./tailscale:/var/lib/tailscale # Directorio para archivos persistentes.
    devices:
      - /dev/net/tun:/dev/net/tun
    cap_add:
      - net_admin
      - net_raw
    sysctls:
      - net.ipv4.ip_forward=1
      - net.ipv6.conf.all.forwarding=1
      
  pihole:
    image: pihole/pihole:latest
    container_name: pihole
    hostname: ugreennas # Nombre de tu dispositivo.
    restart: unless-stopped
    networks:
      lan_macvlan:
        ipv4_address: 192.168.1.5 # IP libre en tu LAN para Pi-hole.
    environment:
      - TZ=Europe/Madrid #Zona horaria.
      - FTLCONF_webserver_api_password=tupassword # Password de acceso a Pi-hole.
      - FTLCONF_dns_listeningMode=ALL
      - FTLCONF_dns_specialDomains_iCloudPrivateRelay=true # Opcional: relay privado de Apple.
      - PIHOLE_UID=1000 # UID de tu máquina. Comprobar con comando: id -u
      - PIHOLE_GID=10 # GID de tu máquina. Comprobar con comando: id -g
    volumes:
      - ./pihole:/etc/pihole # Directorio para archivos persistentes.
    cap_add:
     # - NET_ADMIN # Opcional: usar Pi-hole como servidor DHCP.
     # - SYS_TIME # Opcional: usar Pi-hole como cliente NTP.
      - SYS_NICE
```


### 🔧 Configuración adicional
Una vez instalado Tailscale, es necesario permitir que el servidor realice el enrutado de red (`IP forwarding`). El proceso para habilitarlo puede variar según la distribución de Linux utilizada, aunque los siguientes comandos funcionan en la mayoría de los casos:

- Para sistema Linux con un directorio /etc/sysctl.d
```bash
echo 'net.ipv4.ip_forward = 1' | sudo tee -a /etc/sysctl.d/99-tailscale.conf
echo 'net.ipv6.conf.all.forwarding = 1' | sudo tee -a /etc/sysctl.d/99-tailscale.conf
sudo sysctl -p /etc/sysctl.d/99-tailscale.conf
```

- De lo contrario usa:
```bash
echo 'net.ipv4.ip_forward = 1' | sudo tee -a /etc/sysctl.d/99-tailscale.conf
echo 'net.ipv6.conf.all.forwarding = 1' | sudo tee -a /etc/sysctl.d/99-tailscale.conf
sudo sysctl -p /etc/sysctl.d/99-tailscale.conf
```

### 🌐 Acceso web

#### Tailscale:
https://login.tailscale.com

#### Pi-hole (Raspberry Pi / PC):
`http://IP-DE-TU-SERVIDOR:80/admin` o `https://IP-DE-TU-SERVIDOR:443/admin`

#### Pi-hole (Servidor NAS):
`http://IP-DE-TU-PIHOLE/admin`

## 📄 Documentación oficial

### Tailscale
- [Tailscale en Docker](https://tailscale.com/docs/features/containers/docker/how-to/connect-docker-container)
- [Tailscale GitHub](https://github.com/tailscale-dev/docker-guide-code-examples)
- [Tailscale Docker Hub](https://hub.docker.com/r/tailscale/tailscale)

### Pi-hole
- [Pi-hole en Docker](https://docs.pi-hole.net/docker/)
- [Pi-hole GitHub](https://github.com/pi-hole/docker-pi-hole)
- [Pi-hole Docker Hub](https://hub.docker.com/r/pihole/pihole)
