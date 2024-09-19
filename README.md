## DRUPAL-NOTES
### Drupal Download
[Download](https://www.drupal.org/download)

### Composer Install

[Composer Install](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-composer-on-ubuntu-22-04) or

[Composer Download](https://getcomposer.org/download/)

### Composer Drupal Update
Show recommended updates 
```
$composer show drupal/core-recommended
```
Check available updates
```
$composer outdated 'drupal/*'
```
Update drupal core codes with dependencies
```
$composer update drupal/core 'drupal/core-*' --with-all-dependencies 
```
Update drupal core code only
```
$composer update drupal/core --with-all-dependencies 
```
Update database
```
$drush updatedb
$drush cache:rebuild
```
### Drush Install
[Drush Install](https://www.drush.org/13.x/install/)

### Drupal Nginx
default
```nginx
server {
        listen 80;
        server_name drupal.iwebitechnology.xyz *.drupal.iwebitechnology.xyz;
	#server_name iwebitechnology.xyz *.iwebitechnology.xyz;
        return 301 https://drupal.iwebitechnology.xyz$request_uri;
}
```
drupal_10_ecommerce
```nginx
server {
    gzip on;
    gzip_types      text/plain application/xml;
    gzip_proxied    no-cache no-store private expired auth;
    gzip_min_length 1000;
}
server {
	listen 443 ssl;

        server_name drupal.iwebitechnology.xyz *.drupal.iwebitechnology.xyz;

        root /var/www/html/drupal_10_ecommerce;
        index index.php index.html;

	access_log /var/log/nginx/drupal_access.log;
    	error_log  /var/log/nginx/drupal_error.log debug;

	ssl_certificate /etc/cloudflare_ssl/cert.pem;
        ssl_certificate_key /etc/cloudflare_ssl/key.pem;

	client_max_body_size 10M;
 
	location / {
		try_files $uri /index.php?$query_string; # >= Drupal 7
	}
	location @rewrite {
        	rewrite ^/(.*)$ /index.php?q=$1;
    	}
	#block access to hidden files
	location ~ (^|/)\. {
        	return 403;
    	}
	# Don't allow direct access to PHP files in the vendor directory.
    	location ~ /vendor/.*\.php$ {
        	deny all;
        	return 404;
    	}
	location ~ '\.php$|^\/update.php' {
                #ensure php files exists
		fastcgi_split_path_info ^(.+?\.php)(|/.*)$;
		#NOTE: You should have "cgi.fix_pathinfo = 0;" in php.ini
                include fastcgi_params;
                #block httpoxy attack
		fastcgi_param HTTP_PROXY "";
        	fastcgi_param PATH_INFO $fastcgi_path_info;
        	fastcgi_param QUERY_STRING $query_string;
		#fastcgi_intercept_errors on;
		
		fastcgi_pass unix:/run/php/php8.1-fpm.sock;
                fastcgi_param  SCRIPT_FILENAME $document_root$fastcgi_script_name;
        }

	location = /favicon.ico {
        	log_not_found off;
        	access_log off;
    	}
	location = /robots.txt {
        	allow all;
        	log_not_found off;
        	access_log off;
    	}
	location ~ \..*/.*\.php$ {
        	return 403;
    	}

   	location ~ ^/sites/.*/private/ {
        	return 403;
    	}
    	# Block access to scripts in site files directory
    	location ~ ^/sites/[^/]+/files/.*\.php$ {
        	deny all;
    	}
	#fighting with styles ?
	location ~ ^/sites/.*/files/styles/ { # For Drupal >= 7
        	try_files $uri @rewrite;
    	}
	#handle private files through drupal
	location ~ ^(/[a-z\-]+)?/system/files/ { # For Drupal >= 7
        	try_files $uri /index.php?$query_string;
    	}
	location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg)$ {
        	try_files $uri @rewrite;
        	expires max;
        	log_not_found off;
    	}
	#enforce clean url
	if ($request_uri ~* "^(.*/)index\.php(.*)") {
        	return 307 $1$2;
    	}
	# Reference: https://alexanderallen.medium.com/series-part-iv-optimizing-nginx-for-drupal-8-x-and-php-7-x-976b541f3768

}
```
### Drupal Security
```
```
### Drupal Security Scanner
[Droopescan](https://github.com/SamJoan/droopescan)
### Drupal Patching
[Patching in Drupal](https://youtu.be/dHnqYpqiTfs?si=KipA6pTB8JbOxz4R)
