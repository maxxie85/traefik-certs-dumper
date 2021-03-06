# traefik-certs-dumper

[![Docker Layers](https://images.microbadger.com/badges/image/humenius/traefik-certs-dumper.svg)](https://hub.docker.com/r/humenius/traefik-certs-dumper)
[![Docker Image Latest Version](https://images.microbadger.com/badges/version/humenius/traefik-certs-dumper:1.1.svg)](https://hub.docker.com/r/humenius/traefik-certs-dumper)

Dumps Let's Encrypt certificates of a specified domain to `.pem` and `.key` files which Traefik stores in `acme.json`. 

This image uses:
- a bash script that derrivates from [mailu/traefik-certdumper](https://hub.docker.com/r/mailu/traefik-certdumper)
- [ldez's traefik-certs-dumper](https://github.com/ldez/traefik-certs-dumper)
Special thanks to them!

**IMPORTANT**: It's supposed to work with Traefik **v2** or higher! If you want to use this certificate dumper with **v1**, you can simply change the image to [mailu/traefik-certdumper](https://hub.docker.com/r/mailu/traefik-certdumper).

## Usage
### Basic setup
Mount your ACME folder into `/traefik` and output folder to `/output`. Here's an example for docker-compose:
```yaml
version: '3.7'

services:
  certdumper:
    image: humenius/traefik-certs-dumper:latest
    container_name: traefik_certdumper
    volumes:
    - ./traefik/acme:/traefik:ro
    - ./output:/output:rw
    environment:
    - DOMAIN=example.org
```

### Automatic container restart
If you want to have containers restarted after dumping certificates into your output folder, you can specify their names as comma-separated value and pass them through via optional parameter `-r | --restart-containers`. In this case, you must pass the Docker socket (or override `$DOCKER_HOST` if you use a Docker socket proxy). For instance:
```yaml
version: '3.7'

services:
  certdumper:
    image: humenius/traefik-certs-dumper:latest
    container_name: traefik_certdumper
    command: --restart-containers container1,container2,container3
    volumes:
    - ./traefik/acme:/traefik:ro
    - ./output:/output:rw
    - /var/run/docker.sock:/var/run/docker.sock:ro
    environment:
    - DOMAIN=example.org
```

### Change ownership of certificate and key files
If you want to change the onwership of the certificate and key files because your container runs on different permissions than `root`, you can specify the UID and GID as an environment variable. These environment variables are `OVERRIDE_UID` and `OVERRIDE_GID`. These can only be integers and must both be set for the override to work. For instance:
```yaml
version: '3.7'

services:
  certdumper:
    image: humenius/traefik-certs-dumper:latest
    container_name: traefik_certdumper
    command: --restart-containers container1,container2,container3
    volumes:
    - ./traefik/acme:/traefik:ro
    - ./output:/output:rw
    - /var/run/docker.sock:/var/run/docker.sock:ro
    environment:
    - DOMAIN=example.org
    - OVERRIDE_UID=1000
    - OVERRIDE_GID=1000
```

## Help!
If you need help using this image, have suggestions or want to report a problem, feel free to open an issue on GitHub!
