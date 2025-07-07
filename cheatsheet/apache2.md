# Apache2 Cheatsheet

## Command

```shell
# Enable/Disable site
a2ensite
a2dissite

# Enable/Disable config
a2enconf
a2disconf
```

## Logformat

```shell
RemoteIPHeader X-Forwarded-For 
{% for item in apache.ip_internal %}
RemoteIPInternalProxy {{ item }} 
{% endfor %}

LogFormat "%a %l %u %t \"%r\" %>s %O %{ms}T \"%{Referer}i\" \"%{User-Agent}i\"" common

```
