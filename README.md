## v-wrapper-hestia-api
This is an utility to interact with Hestia API. It is a bash script that uses curl to perform the API calls and you just need to add the command you want to use and its arguments as parameters.

### Usage:

`v-wrapper-hestia-api [-l|--list] [-u|--user-password] [-h|--hash] HESTIA_COMMAND [ARG1] [ARG2] [...] [ARG13]`

### List Hestia commands:
With this option you can list the commands, descriptions and options used by Hestia.

`v-wrapper-hestia-api [-l|--list] [search pattern]`

Examples:
```
v-wrapper-hestia-api -l
v-wrapper-hestia-api -l v-change
v-wrapper-hestia-api --list v-list
v-wrapper-hestia-api -l '.*mail'
v-wrapper-hestia-api --list 'v-.*web'
```
Example output:
```
$ v-wrapper-hestia-api -l v-change-mail                                                                                   
HESTIA COMMAND                    DESCRIPTION                        OPTIONS
--------------                    -----------                        -------
v-change-mail-account-password    change mail account password       USER DOMAIN ACCOUNT PASSWORD
v-change-mail-account-quota       change mail account quota          USER DOMAIN ACCOUNT QUOTA
v-change-mail-account-rate-limit  change mail account rate limit     USER DOMAIN ACCOUNT RATE
v-change-mail-domain-catchall     change mail domain catchall email  USER DOMAIN EMAIL
v-change-mail-domain-rate-limit   change mail domain rate limit      USER DOMAIN RATE
v-change-mail-domain-sslcert      change domain ssl certificate      USER DOMAIN SSL_DIR [RESTART]
```
### New API:
`v-wrapper-hestia-api hestia_command [arg1] [arg2] [...] [arg13]`

### Legacy API (user/password):
`v-wrapper-hestia-api [-u|--user-password] hestia_command [arg1] [arg2] [...] [arg13]`

### Legacy API (hash):
`v-wrapper-hestia-api [-h|--hash] hestia_command [arg1] [arg2] [...] [arg13]`

Examples:
```
v-wrapper-hestia-api v-list-users
v-wrapper-hestia-api v-list-web-domain user domain
v-wrapper-hestia-api -h v-list-web-domain user domain
v-wrapper-hestia-api --user-password v-list-users
```

### Editing connection params:
You can edit the following variables inside the script:
```
v_host=''
v_port=''
v_access_key=''
v_secret_key=''
v_user=''
v_password=''
v_hash=''
```
Or you can create a config file `.v-wrapper-hestia-api.conf` in your `$HOME` or a file `.v-wrapper-hestia-api.conf` in the current dir (this one takes precedence over the other options).

If using a conf file, the variables to be used are (keep in mind that `v-` prefix is not used in conf file):
```
host=''
port=''
access_key=''
secret_key=''
user=''
password=''
hash=''
```

**Note**: By default, the script (in this case curl) needs a valid certificate installed in your Hestia Server, if your certificate is expired, self-signed, etc. you can ignore the certificate validation editing the script and changing variable `curl_ignore_cert=0` to `curl_ignore_cert=1` or add `curl_ignore_cert=1` to your conf file.

## How to create credentials in Hestia Control Panel to access API

### New API
You can use Web UI to create an access key.

`User admin profile` -> `Access Keys` -> `Add Access Key` -> `Select the permissions you want to use and put a comment (without spaces)` -> `Save`

You will see the new `Access Key` and the `Secret Key` (save both in a safe place).

Using command line you can create the access and secret keys using command `v-add-access-key`.

`Usage: v-add-access-key USER [PERMISSIONS] [COMMENT] [FORMAT]`

For example:
```
# v-add-access-key admin billing AdminBill
ACCESS_KEY_ID:      InNqgp01kO3v0W22eg6Z
SECRET_ACCESS_KEY:  lcp45pvgZh0f_33veMB2P6HDVUYpYeHjTatZ=hDu
USER:               admin
PERMISSIONS:        billing
COMMENT:            AdminBill
TIME:               01:35:44
DATE:               2023-09-10
```

If you want to view the apis available and the commands you could use in every api, use command `v-list-apis`.
```
# v-list-apis
API                 ROLE   COMMANDS
---                 ----   --------
billing             admin  v-add-user,v-delete-user,v-suspend-user,v-unsuspend-user,v-change-user-shell,v-list-user,v-list-users,v-make-tmp-file,v-add-domain,v-change-user-package,v-make-tmp-file,v-change-user-password
mail-accounts       user   v-list-mail-domains,v-list-mail-domain,v-list-mail-account,v-list-mail-accounts,v-list-mail-account-autoreply,v-delete-mail-account,v-delete-mail-account-alias,v-delete-mail-account-autoreply,v-delete-mail-account-forward,v-delete-mail-account-fwd-only,v-add-mail-account,v-add-mail-account-alias,v-add-mail-account-autoreply,v-add-mail-account-forward,v-add-mail-account-fwd-only,v-change-mail-account-password,v-change-mail-account-quota,v-suspend-mail-account,v-suspend-mail-accounts,v-unsuspend-mail-account,v-unsuspend-mail-accounts
phpmyadmin-sso      admin  v-add-database-temp-user,v-delete-database-temp-user
purge-nginx-cache   user   v-purge-nginx-cache,v-list-web-domains,v-list-web-domain
sync-dns-cluster    admin  v-list-users,v-list-sys-config,v-list-user,v-add-cron-restart-job,v-delete-dns-domains-src,v-insert-dns-domain,v-insert-dns-record,v-insert-dns-records,v-rebuild-dns-domains,v-rebuild-dns-domain,v-delete-dns-record,v-make-tmp-file,v-insert-dns-domain,v-delete-dns-domain
update-dns-records  user   v-list-dns-records,v-change-dns-record
```

You can modify existing apis or create your own one editing file in `/usr/local/hestia/data/api/` or adding a file with the list of commands you want to use.

If for some reason you need to be able to use all available commands, you can give to admin user all permissions using this command (GodMode is just a funny comment), here, the important part is `''`.

```
# v-add-access-key admin '' GodMode
ACCESS_KEY_ID:      ss7zCZQ5HasP3FeZyhYW
SECRET_ACCESS_KEY:  ircw1YhKzPlGufaNrQnutN8UOo3JkHqXvWKxCa3J
USER:               admin
PERMISSIONS:
COMMENT:            GodMode
TIME:               01:52:16
DATE:               2023-09-10
```

### Legacy API (user/password)

**Warning**: Legacy API is not recommended and could be removed from Hestia in a future release.

You don't need to create anything, just use your user admin (is the only user allowed) and its password. Using this method, you will be able to use all Hestia commands.

### Legacy API (hash)

**Warning**: Legacy API is not recommended and could be removed from Hestia in a future release.

To create the hash you must use command line and command `v-generate-api-key`

Example:
```
# v-generate-api-key
zIafmk4i6opgjto7x3U_t8sVKj3JT6-P
```
Copy it and you are ready to use it. Using this method, you will be able to use all Hestia commands.

### Add your client ip to allowed ips to access API

By default, Hestia only allows ip `127.0.0.1` to connect to API so if you want to connect from an external client, you must add your client ip to API_ALLOWED_IP.

Using Web UI: 
`Server Settings` -> `Configure` -> `Security` -> `System` -> `Allowed IP addresses for API` -> `Add your client ip in a new line` -> `Save`

Using command line: 
`v-add-sys-api-ip HereYourClientIp`

**I don't recommend this** but, if for some reason you want to allow all external ips to use your Hestia API, you can add the ip `0.0.0.0` via Web UI or via command line. **Use at your own risk**
