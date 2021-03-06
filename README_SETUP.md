To build:
-------
```
./configure --prefix=/opt/nginx-dev --with-select_module --with-poll_module --with-ipv6 --with-http_spdy_module --with-http_realip_module --with-http_geoip_module --with-http_degradation_module --with-google_perftools_module --with-cc-opt=-m64 --with-ld-opt=-m64
```
Notes:
only geoip and realip is for data tracking.
spdy is for google spdy protocol support

Version:
I just use the most recent released version per github tag


Dependencies:
-------

geoip libraries both lib and dev-header files. command lne for interaction/troubleshooting


Method details:
--------

http://developer.rackspace.com/blog/building-an-in-house-analytics-collector-with-nginx.html


More info: 
-------
http://dev.maxmind.com/geoip/

http://dev.maxmind.com/geoip/legacy/geolite

google_perf tools: both lib and dev-header files. command lne for interaction/troubleshotting

libpcre3-dev libpcre3  (for http rewrite)

libgeop1 libgeoip-dev (for geopip)
 
libgoogle-perftools libgoogle-perftools-dev google-perf tools (for google perftool)

Setting up Tracking Pixel (Empty Gif):
-------

nginx.conf
-------
```

#user  nobody;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       mime.types;
    default_type  application/octet-stream;
    
    geoip_country /usr/share/GeoIP/GeoIP.dat;
    geoip_city /usr/share/GeoIP/GeoLiteCity.dat;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';


    log_format analytics '$time_iso8601»$remote_addr»'
			 '$http_referer»$http_user_agent»'
			 '$geoip_country_code3»$geoip_region»$geoip_city»$geoip_postal_code>>$geoip_latitude»$geoip_longitude»'
			 '$query_string';

    log_format server_performance '$time_iso8601»$status»'
				  '$remote_addr»$geoip_country_code»'
				  '$request_time»$request_length';



    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    gzip  on;

    server {
        listen       80;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   html;
            index  index.html index.htm;
        }

        location = /track/me.gif
        {
            empty_gif;
            log_not_found  off;
            access_log  /opt/nginx-dev/events.access.log analytics;
            access_log  /opt/nginx-dev/srv-performance.access.log server_performance;
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }


    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}


    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}

}

```

