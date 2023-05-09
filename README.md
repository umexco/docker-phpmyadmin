# phpmyadmin
Config for fast PMA setup


```yaml
version: "3"

services:
    phpmyadmin:
        container_name: phpmyadmin
        image: phpmyadmin/phpmyadmin:latest
        environment:
            PMA_HOST: mysql # for Docker DB Server
            # PMA_HOST: 127.0.0.1 # for host DB Server
            PMA_PORT: 3306
            APACHE_PORT: 8080
            UPLOAD_LIMIT: 1G
        restart: unless-stopped
        network_mode: bridge # for Docker DB Server
        # network_mode: host # for host DB Server
        
        # If you are running nginx on the host, you will need the port mapping
        # Otherwise remove, to not expose more ports than needed.
        # ports:
        #     - 8080:8080
```


### Nginx
```perl
server {
    server_name pma.domain.com;

    location ^~ / {
        proxy_pass http://127.0.0.1:8080;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection “upgrade”;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-Proto https;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Real-IP $remote_addr;
        
        client_max_body_size 1G;
        proxy_connect_timeout 600;
        proxy_send_timeout 600;
        proxy_read_timeout 600;
        send_timeout 600;
    }
}
```

```sh
cd /etc/nginx/sites-enabled && sudo ln -s ../sites-available/pma.domain.com.conf pma.domain.com.conf
sudo service nginx reload
sudo certbot --nginx -d pma.domain.com
```
