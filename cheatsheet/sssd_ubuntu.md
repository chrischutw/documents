# Ubuntu SSSD configuration

## Download ubuntu image for virtualbox

1. Downlaod: <https://www.osboxes.org/virtualbox-images/>

## Download windows 11 and enable AD

1. Download: <https://aka.ms/windev_VM_virtualbox>
2. Enable sssd: <https://allthings.how/how-to-enable-active-directory-in-windows-11/>

## CentOS 7 SSSD configuration reference

<https://access.redhat.com/documentation/zh-tw/red_hat_enterprise_linux/7/html/system-level_authentication_guide/sssd-multiple-config-files>

## Ubuntu 22 SSSD configuration

<https://ubuntu.com/server/docs/service-sssd-ldap>

## sssd doc

<https://sssd.io/>

## Prerequisite

```shell
# Install sssd
sudo apt update -y
sudo apt install sssd-ldap ldap-utils sssd-tools -y

# obfuscated_password 
sss_obfuscate -d tw.sinyi.com -f /etc/sssd/sssd.conf

# Enable sssd
sudo systemctl enable sssd

# Automatic home directory creation
sudo pam-auth-update --enable mkhomedir

# Enable login by ssh keys
vim /etc/ssh/sshd_config

AuthorizedKeysCommand /usr/bin/sss_ssh_authorizedkeys
AuthorizedKeysCommandUser nobody
```

## sssd common used command

```shell
# Clear sssd cache
sss_cache -u `user`
```

## sssd.conf

```shell
[sssd]
config_file_version = 2
services = nss, pam, ssh, sudo
domains = tw.sinyi.com

[nss]
default_shell = /bin/bash
override_homedir = /home/%u
filter_users = root
filter_groups = root
reconnection_retries = 3

[pam]
pam_id_timeout = 3600

[sudo]

[domain/tw.sinyi.com]
debug_level = 9
enumerate = False
case_sensitive = False
cache_credentials = True
min_id = 100

# Providers
id_provider = ldap
auth_provider = ldap
access_provider = ldap
ldap_access_order = filter, expire
ldap_account_expire_policy = ad
chpass_provider = ldap

ldap_uri = ldap://nt010.sinyi.com.tw
ldap_search_base = ou=SinyiUsers,dc=tw,dc=sinyi,dc=com
ldap_group_search_base = ou=SinyiUsers,dc=tw,dc=sinyi,dc=com
ldap_user_object_class = user
ldap_user_name = sAMAccountName
ldap_user_principal = userPrincipalName
ldap_user_ssh_public_key = altSecurityIdentities

# allow only users in a particular AD security group
ldap_access_filter = memberOf=cn=G_Sysoprs,ou=SinyiUsers,DC=tw,dc=sinyi,dc=com

ldap_default_bind_dn = CN=serverchk,CN=Users,DC=tw,DC=sinyi,DC=com
ldap_default_authtok_type = obfuscated_password
ldap_default_authtok = AAAQALJqpA61mgxRHoTcbKdBS12jtjOmeGu67AJYs3IwwgGZt7uM3AKYHKLWwfApbCmCQ8hp+17pmgksV//LYv6QMLMAAQID

# Providers
id_provider = ldap
sudo_provider = ldap
auth_provider = ldap
access_provider = ldap
ldap_access_order = filter, expire
ldap_account_expire_policy = ad
chpass_provider = ldap


# figure out the UID and GID for a linux user from the AD objectSID
# depending on how big the objectSID is on your AD server you might need to tweak the 3 lines below to avoid collisions
ldap_id_mapping = True
ldap_idmap_range_min = 100000
ldap_idmap_range_max = 2000100000
ldap_idmap_range_size = 800000

ldap_id_use_start_tls = True
# don't verify the certificate, unless you can obtain it from your AD server and install it on your Linux machine
ldap_tls_reqcert = never
ldap_schema = ad

autofs_provider = ldap
```

## steps

```shell
chmod 600 -R /etc/sssd/
```

##

## sudoer.d add g_sysoprs

%g_sysoprs ALL=(ALL:ALL) NOPASSWD:ALL
