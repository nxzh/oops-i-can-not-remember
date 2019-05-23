Registry Side:


    docker run --entrypoint htpasswd registry:2 -Bbn demo demo  >> /opt/registry/auth/htpasswd

    docker run -d -p 7777:5000 --restart=always -v /opt/registry/auth/:/auth/ -e "REGISTRY_AUTH=htpasswd"  -e "REGISTRY_AUTH_HTPASSWD_REALM=Registry Realm"   -e "REGISTRY_AUTH_HTPASSWD_PATH=/auth/htpasswd" -v /opt/registry/:/var/lib/registry/ registry:2


Client Side:


    /etc/docker/daemon.json
    {
      "insecure-registries" : ["myregistrydomain.com:5000"]
    }
