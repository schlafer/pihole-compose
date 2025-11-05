# pihole-compose

This is a `docker-compose.yml` file for Pi-hole using `macvlan` network driver to manually assign an LAN ip to our Pi-hole docker container and free up the host's port 80.

> [!IMPORTANT]  
> This will only work with a wired ethernet connection!

```yml
services:
  pihole:
    container_name: pihole
    image: pihole/pihole:latest
    networks:
      pihole_network:
        ipv4_address: '192.168.0.149'  # must be free and outside DHCP range
    ports:
      - "53:53/tcp"
      - "53:53/udp"
      - "80:80/tcp"
    environment:
      TZ: 'Asia/Dhaka'
      WEBPASSWORD: 'PASSWORD' # change password
    volumes:
      - '/home/username/pihole/etc-pihole:/etc/pihole'  # insert your docker host username
      - '/home/username/pihole/etc-dnsmasq.d:/etc/dnsmasq.d' # insert your docker host username
    cap_add:
      - NET_ADMIN
    restart: unless-stopped

networks:
  pihole_network:
    driver: macvlan
    driver_opts:
      parent: enp37s0  # insert your NIC
    ipam:
      config:
        - subnet: 192.168.0.0/24 # Change it according to your gateway
          gateway: 192.168.0.1  # Change it for your default gateway

```
