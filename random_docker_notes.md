# Docker Label Schema
http://label-schema.org/

# Docker Official Image Guidelines
https://github.com/docker-library/official-images/

# Red Hat Container Catalog
https://access.redhat.com/containers/

# Adding additional hosts to /etc/hosts via docker-compose

I have a use case to use the official pihole container to also serve as a local DNS server.  `/etc/hosts` file is manage by the container itself, in order to inject additional hosts to `/etc/hosts`, the `extra_hosts` must be used.  See example below:

```
    extra_hosts:
      - "jenkins0.beyonddc:192.168.20.140"
      - "jenkins1.beyonddc:192.168.20.141"
      - "jenkins2.beyonddc:192.168.20.142"
      - "jenkins3.beyonddc:192.168.20.143"
      - "gitlab.beyonddc:192.168.20.144"
      - "nexus.beyonddc:192.168.20.145"
      - "tfs.beyonddc:192.168.20.146"
      - "jenkinswin1.beyonddc:192.168.20.147"
      - "jenkinswin2.beyonddc:192.168.20.148"
```

