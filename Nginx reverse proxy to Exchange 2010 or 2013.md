[Nginx reverse proxy to Exchange 2010/2013](https://gist.github.com/taddev/7275873) 

**[Rikysonic](/Rikysonic)** commented [Jul 27, 2023](/taddev/7275873?permalink_comment_id=4642916#gistcomment-4642916) •edited
> Do not use nginx for Exchange, it's not work correctly, better use haproxy
> 
> ```
> frontend ft_https
>                 bind *:443 ssl crt /etc/haproxy/emailcert.pem
>                 reqadd X-Forwarded-Proto:\ https
>                 default_backend bk_exchange
> 
>                 acl ft_owa      hdr(host) -i email.example.com
>                 use_backend bk_exchange if ft_owa
> 
> backend bk_exchange
>                 acl path_root url_len 1
>                 acl path_exchange path_beg -i /autodiscover /owa /oab /ews /public /microsoft-server-activesync /rpc /mapi /favicon.ico
>                 http-request deny unless path_exchange OR path_root
>                 server exchange 10.0.25.25:443 check ssl verify none 
> ```

Thank you, this works extremely well, Outlook connects without any issues.  
I've added some default values taken from the web, but the rest is basically untouched:

```
defaults
    mode http

    retries                   3 # Try to connect up to 3 times in case of failure
    timeout connect           5s # 5 seconds max to connect or to stay in queue
    timeout http-keep-alive   1s # 1 second max for the client to post next request
    timeout http-request      15s # 15 seconds max for the client to send a request
    timeout queue             30s # 30 seconds max queued on load balancer
    timeout client            30m
    timeout server            30m

frontend ft_https
    bind *:443 ssl crt /etc/haproxy/server.pem
    http-request add-header X-Forwarded-Proto https
    default_backend bk_exchange

    acl ft_owa      hdr(host) -i email.example.com
    use_backend bk_exchange if ft_owa

backend bk_exchange
    acl path_root url_len 1
    acl path_exchange path_beg -i /autodiscover /owa /oab /ews /public /microsoft-server-activesync /rpc /mapi /favicon.ico
    http-request deny unless path_exchange OR path_root
    server exchange 10.1.1.1:443 check ssl verify none 
```

I'm using docker-compose, so here's the docker-compose.yml:

```
version: "2"
services:
    haproxy-exch-reverse-proxy:
        image: haproxy:alpine
        container_name: haproxy-exch-reverse-proxy
        volumes:
        - ./outlook-haproxy.cfg:/usr/local/etc/haproxy/haproxy.cfg
        - ./server.pem:/etc/haproxy/server.pem
        ports:
        - 443:443
        restart: unless-stopped 
``` 
