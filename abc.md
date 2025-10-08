1. Public web
2. Chép file trong thưc mục wwwroot lên server
3. Phân quyền
sudo chown -R www-data:www-data /var/www/thu_muc_chua_index.html
sudo find /var/www/vinnodes.com -type d -exec chmod 755 {} \;
sudo find /var/www/vinnodes.com -type f -exec chmod 644 {} \;
4. Cấu hình nginx
sudo nano /etc/nginx/sites-available/domain_site.com

  GNU nano 6.2                                                /etc/nginx/sites-available/vinnodes.com                                                         
# Redirect all HTTP to HTTPS
server {
    listen 80;
    listen [::]:80;
    server_name vinnodes.com www.vinnodes.com;
    return 301 https://vinnodes.com$request_uri;
}

# HTTPS configuration
server {
    listen 443 ssl;
    listen [::]:443 ssl;
    server_name vinnodes.com www.vinnodes.com;

    root /var/www/vinnodes.com;
    index index.html;

    ssl_certificate /etc/letsencrypt/live/vinnodes.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/vinnodes.com/privkey.pem;
    include /etc/letsencrypt/options-ssl-nginx.conf;
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem;

    location / {
        # Đây là điểm quan trọng cho SPA (Single Page App)
        try_files $uri $uri/ /index.html;
    }

    # Cải thiện tốc độ tải
    location ~* \.(?:manifest|appcache|html|xml|json)$ {
        expires -1;
    }

    location ~* \.(?:jpg|jpeg|gif|png|ico|cur|gz|svg|webp|mp3|mp4|woff2?|ttf|css|js)$ {
        expires 1y;
        access_log off;
        add_header Cache-Control "public, no-transform";
    }
#    location / {
 #       try_files $uri $uri/ =404;
  #  }
}
