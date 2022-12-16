# Home Assistant dans un container Docker sur Raspberry Pi 4

## 1. Raspberry Pi 4 qui boote sur un SSD
[https://www.maison-et-domotique.com/128294-comment-demarrer-raspberry-pi-4-sur-ssd/](https://www.maison-et-domotique.com/128294-comment-demarrer-raspberry-pi-4-sur-ssd/)

## 2. Installation de Docker sur Raspberry Pi 4
[https://www.raspberrypi-france.fr/installer-docker-sur-raspberry-pi/](https://www.raspberrypi-france.fr/installer-docker-sur-raspberry-pi/)
[https://www.framboise314.fr/installer-docker-sur-raspberry-pi/](https://www.framboise314.fr/installer-docker-sur-raspberry-pi/)

## 3. Installation de Home Assistant dans un container Docker

> :memo: [Aide mémoire Docker](https://cours.brosseau.ovh/cheatsheets/docker/)

[https://www.home-assistant.io/installation/raspberrypi#install-home-assistant-container](https://www.home-assistant.io/installation/raspberrypi#install-home-assistant-container)

### 3.1. Avec Docker compose

On cherche le p'tit nom du hardware qu'on souhaite exposer au container. Ici, il s'agit de mon dongle USB Sonoff Zigbee.

```bash
ls -l /dev/serial/by-id/
```

> :information_source: On peut aussi avoir la liste des périphériques USB avec la commande `lsusb`, voire même avec `lsusb -s BUS_NUMBER:DEVICE_NUMBER -v` pour avoir plus d'infos.

On crée le fichier docker-compose.yml, et on personalise le chemin vers le fichier de configuration de Home Assistant, le fuseau horaire, et le nom du hardware USB.

```yaml
version: '3'
services:
  homeassistant:
    container_name: homeassistant
    image: "ghcr.io/home-assistant/home-assistant:stable"
    volumes:
      - /PATH_TO_YOUR_CONFIG:/config
      - /etc/localtime:/etc/localtime:ro
    restart: unless-stopped
    privileged: true
    network_mode: host
    devices:
      - /dev/tty{$USB_HARDWARE}:/dev/tty{$USB_HARDWARE}
```

Et on lance le container.

```bash
docker-compose up -d
```

### 3.2. Sans Docker Compose

```bash
docker run -d \
  --name homeassistant \
  --privileged \
  --restart=unless-stopped \
  -e TZ=Europe/Paris \
  -v /PATH_TO_YOUR_CONFIG:/homeassistant:/config \
  --network=host \
  ghcr.io/home-assistant/home-assistant:stable
```

### 3.2. Redémarrage du container

```bash
docker restart homeassistant
```

### 3.3. Arrêt du container

```bash
docker stop homeassistant
```

## 4. Accès à Home Assistant

- home : [http://IP_DE_VOTRE_RASPBERRY:8123](http://IP_DE_VOTRE_RASPBERRY:8123)

- config : [http://IP_DE_VOTRE_RASPBERRY:8123/config](http://IP_DE_VOTRE_RASPBERRY:8123/config)

