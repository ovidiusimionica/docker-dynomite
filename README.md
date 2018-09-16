# docker-dynomite
This repo defines the docker project for customized dynomite setup.

Compared with [original docker setup](https://github.com/Netflix/dynomite/tree/dev/docker) from Netflix, I use [supervisor](http://supervisord.org/) for controling the startup of redis and dynomite services.

At the moment of the writting the redis version is 4.0.9, so I have adapted the redis start command to include **--protected-mode no** flag to allow external dynomite instances get access to it for dyna-queues.

That is if you plan to create a dynomite cluster like I wanted to.

I give away an ansible snippet for inspiration:

```
dyn_o_mite:
     datacenter: us-east-{{node_id}}
     listen: 0.0.0.0:{{ dynomite_client_port }}
     dyn_listen: 0.0.0.0:{{ dynomite_route_port }}
     dyn_seed_provider: simple_provider
     dyn_seeds:
{% for host in slave_list %}
{% if node_id |int != loop.index %}
     - {{ host }}:{{ dynomite_route_port }}:us-east-1:us-east-{{loop.index}}:0
{% endif %}
{% endfor %}
     servers:
     - 127.0.0.1:22122:1
     tokens: '0'
     rack: us-east-1
     data_store: 0
     secure_server_option: none
     pem_key_file: /etc/dynomitedb/dynomite.pem
     recon_key_file: /etc/dynomitedb/recon_key.pem
     recon_iv_file: /etc/dynomitedb/recon_iv.pem
     read_consistency: {{ dynomite_read_consistency }}
     write_consistency: {{ dynomite_write_consistency }}
```

