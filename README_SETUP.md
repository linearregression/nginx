To build:
-------
```
./configure --prefix=/opt/nginx --with-select_module --with-poll_module --with-ipv6 --with-http_spdy_module --with-http_realip_module --with-http_geoip_module --with-http_degradation_module --with-google_perftools_module --with-cc-opt=-m64 --with-ld-opt=-m64
```
Notes:
only geoip and realip is for data tracking.
spdy is for google spdy protocol support


Dependencies:
-------

geoip libraries both lib and dev-header files. command lne for interaction/troubleshooting

More info: 
-------
http://dev.maxmind.com/geoip/
http://dev.maxmind.com/geoip/legacy/geolite

google_perf tools: both lib and dev-header files. command lne for interaction/troubleshotting

Setting up Tracking Pixel (Empty Gif):
-------

