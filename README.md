# Курсовая работа по итогам модуля "DevOps и системное администрирование"

 ## Установим и настроим `ufw`:
 
```
us@nginx:~$ sudo ufw enable
Firewall is active and enabled on system startup

us@nginx:~$ sudo ufw allow ssh
Rule added
Rule added (v6)

us@nginx:~$ sudo ufw allow 443/tcp
Rule added
Rule added (v6)

us@nginx:~$ sudo ufw status verbose
Status: active
Logging: on (low)
Default: deny (incoming), allow (outgoing), disabled (routed)
New profiles: skip

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW IN    Anywhere
443/tcp                    ALLOW IN    Anywhere
22/tcp (v6)                ALLOW IN    Anywhere (v6)
443/tcp (v6)               ALLOW IN    Anywhere (v6)
```

 ## Установим hashicorp vault и выпустим сертификат с его помощью:

```

```
