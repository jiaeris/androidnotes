### Angular项目 使用nginx部署，http和https配置
```nginx
server{
        listen 8080;
        server_name  www.heihei.cn;
        root /usr/share/nginx/html/dist;
        access_log  /usr/share/nginx/log/my.log;

        location / {
	    try_files $uri $uri/ /index.html;
	    index index.html;
        }

	location /api {
	    proxy_pass http://localhost:15003/api;
	}

        location ~ ^/(images|javascript|js|css|flash|media|static)/ {
            expires 7d;
        }
}
server{
	listen 443 ssl;
	ssl on;
	ssl_certificate /home/hello/ssl/server.crt;
	ssl_certificate_key /home/hello/ssl/server.key;
	server_name  www.heihei.cn;
        
	root /usr/share/nginx/dollManager/dist;
        access_log  /usr/share/nginx/log/my.log;

        location / {
            try_files $uri $uri/ /index.html;
            index index.html;
        }

        location /api {
            proxy_pass http://localhost:15003/api;
        }

        location ~ ^/(images|javascript|js|css|flash|media|static)/ {
            expires 7d;
        }
}
```
