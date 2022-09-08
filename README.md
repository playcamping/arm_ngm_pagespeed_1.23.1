#  Orcale cloud freetier VM.Standard.A1.Flex ubuntu 22.04
## [ARM] aarch64 nginx pagespeed module for nginx 1.23.1

1. sudo cd /etc/nginx/modules

2. wget https://github.com/playcamping/arm_ngm_pagespeed_1.23.1/blob/main/ngx_pagespeed.so

3. sudo nano /etc/nginx/nginx.conf
Then add the following line to the beginning of the file:
```
load_module modules/ngx_pagespeed.so;
```
Underneath the lines…
```
user www-data;
worker_processes auto;
pid /run/nginx.pid;
load_module modules/ngx_pagespeed.so;
```

4. sudo cd /etc/nginx/sites-enbled/

5. sudo rm default

6. sudo wget https://github.com/playcamping/arm_ngm_pagespeed_1.23.1/blob/main/default

7. sudo nano default

8. ctrl + \ repalce sitedoamin.com
