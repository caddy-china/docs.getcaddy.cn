## Gitladb

```
https://gitlab.example.com {
    log git.access.log 
    errors git.errors.log {
        404 /opt/gitlab/embedded/service/gitlab-rails/public/404.html
        422 /opt/gitlab/embedded/service/gitlab-rails/public/422.html
        500 /opt/gitlab/embedded/service/gitlab-rails/public/500.html
        502 /opt/gitlab/embedded/service/gitlab-rails/public/502.html
    }

    proxy / http://127.0.0.1:8181 {
        fail_timeout 300s
        transparent
        header_upstream X-Forwarded-Ssl on
    }
}
```

**socket**

```

https://gitlab.domain.tld {

    errors {
        404 /opt/gitlab/embedded/service/gitlab-rails/public/404.html
        422 /opt/gitlab/embedded/service/gitlab-rails/public/422.html
        500 /opt/gitlab/embedded/service/gitlab-rails/public/500.html
        502 /opt/gitlab/embedded/service/gitlab-rails/public/502.html
    }

    proxy / unix:/home/git/gitlab/tmp/sockets/gitlab.socket {
        fail_timeout 300s

	transparent
    }
}
```


## hhvm

```
localhost:8080 {
    fastcgi / unix:/var/run/hhvm/sock php
}
```
## Laravel

```
example.com {
    root ./public
    fastcgi / 127.0.0.1:9000 php
    rewrite {
        to {path} {path}/ /index.php?{query}
    }
}
```

## httpproxy

```
mysite.example.com { 
    proxy / mysite.example.com:3333
}
```

## winphp

```
mysite.example.com    

startup d:\php7\php-cgi.exe -b 6545  &

fastcgi / 127.0.0.1:6545 php 
root  D:\Web\Website
```

## wordpress

```
localhost:8080

root /wwwroot
gzip
fastcgi / 127.0.0.1:9000 php
rewrite {
	if {path} not_match ^\/wp-admin
	to {path} {path}/ /index.php?{query}
}
```
