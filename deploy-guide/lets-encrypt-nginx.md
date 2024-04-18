---
description: >-
  Ubuntu 환경에서 Certbot을 사용하여 도메인에 대한 SSL/TLS 인증서를 발급받는 방법에 대해 설명하겠습니다. Certbot은
  Let's Encrypt 클라이언트로, 무료로 SSL/TLS 인증서를 발급받아 자동으로 설치하는 데 사용됩니다. 다음은 해당 프로세스의
  단계별 가이드입니다.
---

# Let's Encrypt, Nginx

/etc/nginx/sites-available 경로에 새로운 파일을 만들어줍니다.

```bash
sudo vi 파일명(도메인이름을 써도 됩니다)
```

파일 내에 다음과 같이 구성합니다.

```nginx
server {
    listen 443 ssl;
    server_name [서버이름];

    location / {
        proxy_pass http://127.0.0.1:포트번호
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}

server {
    listen 80;
    server_name [서버이름];
    return 301 https://$host$request_uri;
}
```

#### 1. Certbot 설치 확인

먼저, Certbot이 이미 설치되어 있는지 확인합니다. 설치되어 있지 않다면 설치해야 합니다. 설치를 확인하거나 설치하기 위해 다음 명령어를 사용하세요:

```bash
sudo apt update
sudo apt install certbot python3-certbot-nginx
```

#### 2. 웹 서버 소프트웨어 확인

인증서를 설치할 웹 서버의 종류를 확인하세요. 예를 들어 Apache 또는 Nginx 중 사용 중인 서버에 따라 Certbot의 사용 방법이 달라집니다.

#### 3. 인증서 발급 및 설치

**Apache를 사용하는 경우**

Apache 서버를 사용 중이라면, 다음 명령어를 통해 인증서를 자동으로 발급받고 설치할 수 있습니다:

```bash
sudo certbot --apache -d [domainName]
```

**Nginx를 사용하는 경우**

Nginx 서버를 사용 중이라면, 다음 명령어를 통해 인증서를 자동으로 발급받고 설치할 수 있습니다:

```bash
sudo certbot --nginx -d [domainName]
```

#### 4. 인증서 자동 갱신 설정

Certbot은 인증서를 자동으로 갱신할 수 있는 cron job 또는 systemd timer를 설치합니다. 이 설정은 보통 Certbot 설치 시 자동으로 설정되지만, 자동 갱신이 제대로 설정되었는지 확인하려면 다음 명령어를 사용할 수 있습니다:

```bash
sudo certbot renew --dry-run
```

이 명령어는 실제로 갱신을 수행하지 않고 갱신 과정을 시뮬레이션하여 모든 것이 제대로 설정되었는지 확인합니다.

이전에 만들었던 파일을 보면 키설정을 알아서 해줍니다.

```nginx
server {
    listen 443 ssl;
    server_name [서버이름];

    listen [::]:443 ssl ipv6only=on; # managed by Certbot
    listen 443 ssl; # managed by Certbot
    ssl_certificate /etc/letsencrypt/live/domain/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/domain/privkey.pem; # managed by Certbot
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot

    location / {
        proxy_pass http://127.0.0.1:포트번호
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}

server {
    listen 80;
    server_name [서버이름];
    return 301 https://$host$request_uri;
}
```
