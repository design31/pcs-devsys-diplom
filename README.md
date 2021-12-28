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
us@nginx:~$ curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
OK
us@nginx:~$ sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
us@nginx:~$ sudo apt-get update && sudo apt-get install vault
```
Проверка работоспособности vault:
```
us@nginx:~$ vault
Usage: vault <command> [args]

Common commands:
    read        Read data and retrieves secrets
    write       Write data, configuration, and secrets
    delete      Delete secrets and configuration
    list        List data or secrets
    login       Authenticate locally
    agent       Start a Vault agent
    server      Start a Vault server
    status      Print seal and HA status
    unwrap      Unwrap a wrapped secret

Other commands:
    audit          Interact with audit devices
    auth           Interact with auth methods
    debug          Runs the debug command
    kv             Interact with Vault's Key-Value storage
    lease          Interact with leases
    monitor        Stream log messages from a Vault server
    namespace      Interact with namespaces
    operator       Perform operator-specific tasks
    path-help      Retrieve API help for paths
    plugin         Interact with Vault plugins and catalog
    policy         Interact with policies
    print          Prints runtime configurations
    secrets        Interact with secrets engines
    ssh            Initiate an SSH session
    token          Interact with tokens
```
Добавим jq:
```
us@nginx:~$ sudo apt-get install jq
```
Запускаем vault в dev режиме в отдельном терминале:
```
us@nginx:~$ vault server -dev -dev-root-token-id root
==> Vault server configuration:

             Api Address: http://127.0.0.1:8200
                     Cgo: disabled
         Cluster Address: https://127.0.0.1:8201
              Go Version: go1.17.5
              Listener 1: tcp (addr: "127.0.0.1:8200", cluster address: "127.0.0.1:8201", max_request_duration: "1m30s", max_request_size: "33554432", tls: "disabled")
               Log Level: info
                   Mlock: supported: true, enabled: false
           Recovery Mode: false
                 Storage: inmem
                 Version: Vault v1.9.2
             Version Sha: f4c6d873e2767c0d6853b5d9ffc77b0d297bfbdf

==> Vault server started! Log data will stream in below:

2021-12-27T15:24:44.812+0700 [INFO]  proxy environment: http_proxy="\"\"" https_proxy="\"\"" no_proxy="\"\""
2021-12-27T15:24:44.812+0700 [WARN]  no `api_addr` value specified in config or in VAULT_API_ADDR; falling back to detection if possible, but this value should be manually set
2021-12-27T15:24:44.812+0700 [INFO]  core: Initializing VersionTimestamps for core
2021-12-27T15:24:44.817+0700 [INFO]  core: security barrier not initialized
2021-12-27T15:24:44.817+0700 [INFO]  core: security barrier initialized: stored=1 shares=1 threshold=1
2021-12-27T15:24:44.819+0700 [INFO]  core: post-unseal setup starting
2021-12-27T15:24:44.823+0700 [INFO]  core: loaded wrapping token key
2021-12-27T15:24:44.823+0700 [INFO]  core: Recorded vault version: vault version=1.9.2 upgrade time="2021-12-27 15:24:44.823096192 +0700 +07 m=+0.112083666"
2021-12-27T15:24:44.823+0700 [INFO]  core: successfully setup plugin catalog: plugin-directory="\"\""
2021-12-27T15:24:44.823+0700 [INFO]  core: no mounts; adding default mount table
2021-12-27T15:24:44.835+0700 [INFO]  core: successfully mounted backend: type=cubbyhole path=cubbyhole/
2021-12-27T15:24:44.836+0700 [INFO]  core: successfully mounted backend: type=system path=sys/
2021-12-27T15:24:44.836+0700 [INFO]  core: successfully mounted backend: type=identity path=identity/
2021-12-27T15:24:44.844+0700 [INFO]  core: successfully enabled credential backend: type=token path=token/
2021-12-27T15:24:44.845+0700 [INFO]  core: restoring leases
2021-12-27T15:24:44.846+0700 [INFO]  expiration: lease restore complete
2021-12-27T15:24:44.846+0700 [INFO]  rollback: starting rollback manager
2021-12-27T15:24:44.847+0700 [INFO]  identity: entities restored
2021-12-27T15:24:44.847+0700 [INFO]  identity: groups restored
2021-12-27T15:24:44.847+0700 [INFO]  core: post-unseal setup complete
2021-12-27T15:24:44.847+0700 [INFO]  core: root token generated
2021-12-27T15:24:44.847+0700 [INFO]  core: pre-seal teardown starting
2021-12-27T15:24:44.847+0700 [INFO]  rollback: stopping rollback manager
2021-12-27T15:24:44.848+0700 [INFO]  core: pre-seal teardown complete
2021-12-27T15:24:44.848+0700 [INFO]  core.cluster-listener.tcp: starting listener: listener_address=127.0.0.1:8201
2021-12-27T15:24:44.848+0700 [INFO]  core.cluster-listener: serving cluster requests: cluster_listen_address=127.0.0.1:8201
2021-12-27T15:24:44.848+0700 [INFO]  core: post-unseal setup starting
2021-12-27T15:24:44.848+0700 [INFO]  core: loaded wrapping token key
2021-12-27T15:24:44.848+0700 [INFO]  core: successfully setup plugin catalog: plugin-directory="\"\""
2021-12-27T15:24:44.849+0700 [INFO]  core: successfully mounted backend: type=system path=sys/
2021-12-27T15:24:44.850+0700 [INFO]  core: successfully mounted backend: type=identity path=identity/
2021-12-27T15:24:44.850+0700 [INFO]  core: successfully mounted backend: type=cubbyhole path=cubbyhole/
2021-12-27T15:24:44.852+0700 [INFO]  core: successfully enabled credential backend: type=token path=token/
2021-12-27T15:24:44.853+0700 [INFO]  core: restoring leases
2021-12-27T15:24:44.854+0700 [INFO]  identity: entities restored
2021-12-27T15:24:44.854+0700 [INFO]  identity: groups restored
2021-12-27T15:24:44.854+0700 [INFO]  core: post-unseal setup complete
2021-12-27T15:24:44.854+0700 [INFO]  core: vault is unsealed
2021-12-27T15:24:44.858+0700 [INFO]  expiration: revoked lease: lease_id=auth/token/root/h4eb9d90f4c76aa4831d8cbae945f92edfe3bdb9997b54aa94b758806b3de0689
2021-12-27T15:24:44.867+0700 [INFO]  core: successful mount: namespace="\"\"" path=secret/ type=kv
2021-12-27T15:24:44.893+0700 [INFO]  expiration: lease restore complete
2021-12-27T15:24:44.893+0700 [INFO]  secrets.kv.kv_7f11610b: collecting keys to upgrade
2021-12-27T15:24:44.910+0700 [INFO]  secrets.kv.kv_7f11610b: done collecting keys: num_keys=1
2021-12-27T15:24:44.909+0700 [INFO]  rollback: starting rollback manager
2021-12-27T15:24:44.924+0700 [INFO]  secrets.kv.kv_7f11610b: upgrading keys finished
WARNING! dev mode is enabled! In this mode, Vault runs entirely in-memory
and starts unsealed with a single unseal key. The root token is already
authenticated to the CLI, so you can immediately begin using Vault.

You may need to set the following environment variable:

    $ export VAULT_ADDR='http://127.0.0.1:8200'

The unseal key and root token are displayed below in case you want to
seal/unseal the Vault or re-authenticate.

Unseal Key: U27gviPhxuTaaasdGUphtRDSaIiN50DqpufgI3d+UE8=
Root Token: root

Development mode should NOT be used in production installations!
```
Создадим переменные окружения:
```
us@nginx:~$ export VAULT_ADDR=http://127.0.0.1:8200
us@nginx:~$ export VAULT_TOKEN=root
```
Создадим корневой сертификат сроком 31 день:
```
us@nginx:~$ vault secrets enable pki
Success! Enabled the pki secrets engine at: pki/
us@nginx:~$ vault secrets tune -max-lease-ttl=744h pki
Success! Tuned the secrets engine at: pki/
us@nginx:~$ vault write -field=certificate pki/root/generate/internal \
>      common_name="netology.io" \
>      ttl=87600h > CA_cert.crt
us@nginx:~$ vault write pki/config/urls \
>      issuing_certificates="$VAULT_ADDR/v1/pki/ca" \
>      crl_distribution_points="$VAULT_ADDR/v1/pki/crl"
Success! Data written to: pki/config/urls
```
Создадим промежуточный сертификат сроком 30 дней:
```
us@nginx:~$ vault secrets enable -path=pki_int pki
Success! Enabled the pki secrets engine at: pki_int/
us@nginx:~$ vault secrets tune -max-lease-ttl=720h pki_int
Success! Tuned the secrets engine at: pki_int/
us@nginx:~$ vault write -format=json pki_int/intermediate/generate/internal \
>      common_name="netology.io Intermediate Authority" \
>      | jq -r '.data.csr' > pki_intermediate.csr
us@nginx:~$ vault write -format=json pki/root/sign-intermediate csr=@pki_intermediate.csr \
>      format=pem_bundle ttl="720h" \
>      | jq -r '.data.certificate' > intermediate.cert.pem
us@nginx:~$ vault write pki_int/intermediate/set-signed certificate=@intermediate.cert.pem
Success! Data written to: pki_int/intermediate/set-signed
```

Создадим роль:
```
us@nginx:~$ vault write pki_int/roles/netology-dot-io \
>      allowed_domains="netology.io" \
>      allow_subdomains=true \
>      max_ttl="720h"
Success! Data written to: pki_int/roles/netology-dot-io
```
Теперь мы можем выпустить сертификат для роли `netology-dot-io` для поддомена `test.netology.io`
```
us@nginx:~$ vault write pki_int/issue/netology-dot-io common_name="test.netology.io" ttl="72h"
Key                 Value
---                 -----
ca_chain            [-----BEGIN CERTIFICATE-----
MIIDpjCCAo6gAwIBAgIUTDQxRNuKis88LPKS6GoaB91u/6EwDQYJKoZIhvcNAQEL
...
-----END CERTIFICATE-----]
certificate         -----BEGIN CERTIFICATE-----
MIIDZjCCAk6gAwIBAgIUDhZXqv3JEgyJcf7fswrSsG1njHQwDQYJKoZIhvcNAQEL
...
-----END CERTIFICATE-----
expiration          1640919580
issuing_ca          -----BEGIN CERTIFICATE-----
MIIDpjCCAo6gAwIBAgIUTDQxRNuKis88LPKS6GoaB91u/6EwDQYJKoZIhvcNAQEL
...
-----END CERTIFICATE-----
private_key         -----BEGIN RSA PRIVATE KEY-----
MIIEpAIBAAKCAQEAzoY87gjd85nNXO7n2wQwpngu/40RJX50aycYSlyaUHKeix//
8E4QF/DUiFr2VsAoogUDiKfUIxgpTR9+XEIcoef6XZMoBwBD4Ju2OOh/KxWcbs/x
...
-----END RSA PRIVATE KEY-----
private_key_type    rsa
serial_number       0e:16:57:aa:fd:c9:12:0c:89:71:fe:df:b3:0a:d2:b0:6d:67:8c:74
```
