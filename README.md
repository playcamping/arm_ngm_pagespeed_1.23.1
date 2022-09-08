# [ARM] aarch64 nginx pagespeed module for nginx 1.23.1

1. cd /etc/nginx/modules

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

4. sudo nano /etc/nginx/sites-enbled/defalut
```
# HTTPS server configuration  
server {  
    listen 443 ssl http2;  
    listen [::]:443 ssl;  
    server_name sitedmain.com;  
    ssl_certificate /etc/nginx/ssl/fullchain.pem;  
    ssl_certificate_key /etc/nginx/ssl/privkey.pem;  
    ssl_trusted_certificate /etc/nginx/ssl/fullchain.pem;  
    include /etc/nginx/snippets/ssl.conf;
    #include /etc/nginx/snippets/rhymix.conf; 
    root /var/www/sitedmain.com;  
    index index.php index.html index.htm index.nginx-debian.html;  
	location ~* \.(js|css|png|jpg|jpeg|gif|ico) {  
	access_log off;  
	}      
	#location / {  
	# First attempt to serve request as file, then  
	# as directory, then fall back to displaying a 404  
	#try_files $uri $uri/ =404;  
	#}  
	## enable pagespeed module on this server block  
	pagespeed on;                
	#pagespeed RedisServer "localhost:6379";  
	## Cache setting  
	pagespeed FileCachePath /var/pagespeed_cache;  
	pagespeed FileCacheSizeKb            20096000;  # cache를 적용 후 다 차면 비운다  
	pagespeed FileCacheInodeLimit        50000000;  
	pagespeed CssFlattenMaxBytes       102400;  
	pagespeed LRUCacheKbPerProcess 8192;  
	pagespeed LRUCacheByteLimit 16384;  
	#pagespeed DefaultSharedMemoryCacheKB 75000;  
	## Speed up PageSpeed by storing it in the super duper fast memcached  
	#pagespeed MemcachedThreads 1;  
	#pagespeed MemcachedServers ”localhost:11211”;  
	## PageSpeed Cache Purge  
	pagespeed EnableCachePurge on;  
	pagespeed PurgeMethod PURGE;  
	pagespeed DownstreamCacheRewrittenPercentageThreshold 95;  
	# needs to exist and be writable by nginx 755로 되어 있음   
	# cache 비우는 방법 Flushing PageSpeed Sever-ide Cache  
	# rm -rf /var/pagespeed_cache/ or touch /var/pagespeed_cache/cache.flush  
	# service nginx restart  
	## Filter settings  
	pagespeed EnableFilters responsive_images ;  
	pagespeed EnableFilters convert_to_webp_animated ;      
	pagespeed EnableFilters resize_mobile_images ;  
	#pagespeed EnableFilters lazyload_images; # 이는 트래픽 절감용으로 최근에는 트래픽절약용이 아니라면 사용하지 말라고 가이드 됨  
	#pagespeed EnableFilters move_css_above_scripts;  
	#pagespeed EnableFilters outline_css ;  
	pagespeed EnableFilters remove_quotes ;  
	pagespeed EnableFilters collapse_whitespace;  
	pagespeed EnableFilters in_place_optimize_for_browser ;  
	# 이미지관련 대부분이 Core에 기본으로 들어가 있음, 사용하지 않으려면 비활성화 해야 함 예를 들어 pagespeed DisableFilters convert_to_webp_animated; 기본 rewrite_images, convert_jpeg_to_progressive, convert_png_to_jpeg, convert_jpeg_to_webp, convert_to_webp_lossless, inline_images, recompress_images, recompress_jpeg, recompress_png, recompress_webp, convert_gif_to_png, strip_image_color_profile, strip_image_meta_data, jpeg_sampling, resize_images, resize_rendered_image_dimensions,   
	#JS  
	#pagespeed EnableFilters rewrite_javascript ;
	# rewrite_javascript is equivalent to 
	#enabling both rewrite_javascript_inline and rewrite_javascript_external  
	#pagespeed EnableFilters combine_javascript;  
	#pagespeed UseExperimentalJsMinifier on; 미니 파이어는 기본 작동, 이 옵션은 이전 버젼용임  
	#pagespeed EnableFilters defer_javascript;  # 페이지 로딩 완료 시까지 자바 실행 지연      
	pagespeed EnableFilters insert_dns_prefetch; # DNS resolution time 축소  
	pagespeed EnableFilters insert_ga;  # pagespeed EnableFilters make_show_ads_async; 는 불필요 이미 동기 실행   
        #구글 애널리틱스 사용시에만 주석해제
	pagespeed AnalyticsID UA––;  
	pagespeed EnableFilters make_google_analytics_async;  
      
	## Tuning the Filters   
	pagespeed CssImageInlineMaxBytes                        0;  
	pagespeed CssInlineMaxBytes                          2048;  
	pagespeed CssOutlineMinBytes                         3000;  
	pagespeed ImageInlineMaxBytes                        3072;  
	#pagespeed ImageJpegNumProgressiveScans                 –1;  
	#pagespeed ImageJpegNumProgressiveScansForSmallScreens  –1;  
	pagespeed ImageLimitOptimizedPercent                  100;  
	pagespeed ImageLimitResizeAreaPercent                 100;  
	pagespeed ImageRecompressionQuality                    80;  
	pagespeed ImageResolutionLimitBytes              32000000;  
	#pagespeed JpegRecompressionQuality                     –1;  
	#pagespeed JpegRecompressionQualityForSmallScreens      80;  
	pagespeed WebpRecompressionQuality                     85;  
	pagespeed WebpAnimatedRecompressionQuality             80;  
	pagespeed WebpRecompressionQualityForSmallScreens      80;  
	pagespeed JsInlineMaxBytes                           2048;  
	pagespeed JsOutlineMinBytes                          3000;  
	#pagespeed MaxInlinedPreviewImagesIndex                 –1;  
	pagespeed MinImageSizeLowResolutionBytes             3072;  
	pagespeed RewriteRandomDropPercentage                   0;  
	###css optimization filters###
	pagespeed EnableFilters outline_css;
	pagespeed EnableFilters inline_google_font_css;
	pagespeed EnableFilters move_css_above_scripts;
	pagespeed EnableFilters move_css_to_head;
	pagespeed EnableFilters prioritize_critical_css;
	###html optimization filters###
	pagespeed EnableFilters combine_heads;
	pagespeed EnableFilters collapse_whitespace;
	pagespeed EnableFilters convert_meta_tags;
	pagespeed EnableFilters elide_attributes;
	pagespeed EnableFilters pedantic;
	pagespeed EnableFilters remove_comments;
	pagespeed EnableFilters remove_quotes;
	pagespeed EnableFilters trim_urls;
	###javascript optimization filters###
	#pagespeed EnableFilters canonicalize_javascript_libraries;
	#pagespeed EnableFilters defer_javascript;
	###image optimization filters###
	pagespeed EnableFilters dedup_inlined_images;
	pagespeed EnableFilters inline_preview_images;
	pagespeed EnableFilters resize_mobile_images;
	pagespeed EnableFilters lazyload_images;
	pagespeed EnableFilters strip_image_color_profile;
	pagespeed EnableFilters strip_image_meta_data;
	pagespeed EnableFilters jpeg_subsampling;
	pagespeed EnableFilters convert_png_to_jpeg;
	pagespeed EnableFilters resize_rendered_image_dimensions;
	pagespeed EnableFilters insert_image_dimensions;
	pagespeed NoTransformOptimizedImages on;
	pagespeed EnableFilters sprite_images;
	## Ngx_pagespeed Console관련   
	pagespeed Statistics on;  
	pagespeed StatisticsLogging on;  
	pagespeed LogDir /var/log/pagespeed;  
	pagespeed AdminPath /pagespeed_admin;  
	location ~ ^/pagespeed_admin {  
	allow 211.237.86.74;  
	deny all;  
	}  
	# Ensure requests for pagespeed optimized resources go to the pagespeed handler  
	# and no extraneous headers get set.  
	location ~ "\.pagespeed\.([a-z]\.)?[a-z]{2}\.[^.]{10}\.[^.]+" {  
	add_header “” “”;  
	}  
	location ~ “^/pagespeed_static/“ { }  
	location ~ “^/ngx_pagespeed_beacon$” { }  
	location ~ \.php$ {  
	include snippets/fastcgi-php.conf;  
	# With php-fpm (or other unix sockets):  
	fastcgi_pass unix:/run/php/php7.4-fpm.sock;  
	fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;  
	include fastcgi_params;  
	fastcgi_read_timeout 300;  
	}  
}  
# HTTPS CNAME Connect www.sitedmain.com to sitedmain.com  
server {  
	listen 443 ssl http2;  
	listen [::]:443 ssl http2;  
	server_name www.sitedmain.com;  
	include /etc/nginx/snippets/ssl.conf;  
	location / {
  ```
