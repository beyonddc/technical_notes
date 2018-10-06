# Running jacobalberty/unifi-docker on RHEL 7.x

This documents some notes when deploying jacobalberty/unifi-docker on RHEL 7.x

## Unifi Controller 5.8.30-sc

The following applicable to Unifi Controller 5.8.30-sc so YMMV if you are using a different version.

Docker Compose is used for deployment.  The following docker-compose.yml was used.

https://github.com/jacobalberty/unifi-docker/blob/5.8.30-sc/docker-compose.yml

Only two things were modified in order to get this Unifi Controller container to work on RHEL 7.x

1. Due to SELinux, the container itself cannot write to the volume unless ':z' is specified

   ```
   volumes:
         - db:/data/db:z
   ```

2. By default, named volume is automatically mounted to `/var/lib/docker/volumes` and I would like to mount it to a particular directory on my filesystem instead.  Here's what I did...

   ```
   volumes:
     db:
       driver_opts:
         type: none
         device: /Development/Applications/unifi-controller/unifi/db
         o: bind
   ```

### Complete modified docker-compose.yml

```
version: '2.2'
services:
  mongo:
    image: mongo:3.4
    networks:
      - unifi
    restart: always
    volumes:
      - db:/data/db:z
  controller:
    image: "jacobalberty/unifi:5.8.30-sc"
    depends_on:
      - mongo
    init: false
    networks:
      - unifi
    restart: always
    volumes:
      - data:/unifi/data:z
      - log:/unifi/log:z
      - cert:/unifi/cert:z
      - init:/unifi/init.d:z
    environment:
      DB_URI: mongodb://mongo/unifi
      STATDB_URI: mongodb://mongo/unifi_stat
      DB_NAME: unifi
      TZ: "America/New_York"
    ports:
      - "8080:8080"
      - "8443:8443"
      - "3478:3478/udp"
      - "10001:10001/udp"
  logs:
    image: bash
    depends_on:
      - controller
    command: bash -c 'tail -f /unifi/log/*'
    restart: always
    volumes:
      - log:/unifi/log:z

volumes:
  db:
    driver_opts:
      type: none
      device: /Development/Applications/unifi-controller/unifi/db
      o: bind
  data:
    driver_opts:
      type: none
      device: /Development/Applications/unifi-controller/unifi/data
      o: bind
  log:
    driver_opts:
      type: none
      device: /Development/Applications/unifi-controller/unifi/log
      o: bind
  cert:
    driver_opts:
      type: none
      device: /Development/Applications/unifi-controller/unifi/cert
      o: bind
  init:
    driver_opts:
      type: none
      device: /Development/Applications/unifi-controller/unifi/init
      o: bind

networks:
  unifi:
```

