# dondns-openwrt

DonDNS OpenWRT HowTO

## Para usuarios "normales" (sin autenticacion https)

Usando el interfaz web LUci:

```
Seleccionar Services -> Dynamic DNS
 
Activar "Enable"
Poner "Service" a "custom"
Poner en "Custom update-URL" el valor "http://www.dondominio.com/api/xml/dynamicip/?user=[USERNAME]&password=[PASSWORD]&host=[DOMAIN]&ip=[IP]"
Poner en "Hostname" el valor del dominio a registrar
Poner en "Username" el nombre del usuario
Poner en "Password" el valor del AuthCode
En "Source of IP address" seleccionar "network"
En "Network" seleccionar "wan"
El resto dejarlo por defecto
```

## Para usuarios "expertos" (con autenticacion https)

Entrar en openWRT por linea de comandos (ssh).

Instalar curl mediante opkg ( `opkg install curl`)
Editar el archivo `/etc/config/ddns` y configurarlo como sigue (cambiando los valores correspondientes)

```
config service 'myddns'
option interface 'wan'
option force_interval '72'
option force_unit 'hours'
option check_interval '10'
option check_unit 'minutes'
option retry_interval '60'
option retry_unit 'seconds'
option enabled '1'
option username '<nombre de usuario>'
option ip_source 'network'
option ip_network 'wan'
option domain '<dominio que queremos actualizar>'
option password '<authCode de dondominio>'
option update_url 'http://dondns.dondominio.com/xml/?user=[USERNAME]&password=[PASSWORD]&host=[DOMAIN]&ip=[IP]'
option use_https '1'
option cacert '/etc/ssl/certs/dondominio-ca.pem'
```

Tras esto debemos crear el directorio `/etc/ssl/certs/` si no existiera ( `mkdir /etc/ssl/certs/`) y meter dentro el ca del certificado de dondominio, se puede hacer cortando y pegando lo siguiente:

```
cat > /etc/ssl/certs/dondominio-ca.pem << _EOF_
-----BEGIN CERTIFICATE-----
MIIDIDCCAomgAwIBAgIENd70zzANBgkqhkiG9w0BAQUFADBOMQswCQYDVQQGEwJV
UzEQMA4GA1UEChMHRXF1aWZheDEtMCsGA1UECxMkRXF1aWZheCBTZWN1cmUgQ2Vy
dGlmaWNhdGUgQXV0aG9yaXR5MB4XDTk4MDgyMjE2NDE1MVoXDTE4MDgyMjE2NDE1
MVowTjELMAkGA1UEBhMCVVMxEDAOBgNVBAoTB0VxdWlmYXgxLTArBgNVBAsTJEVx
dWlmYXggU2VjdXJlIENlcnRpZmljYXRlIEF1dGhvcml0eTCBnzANBgkqhkiG9w0B
AQEFAAOBjQAwgYkCgYEAwV2xWGcIYu6gmi0fCG2RFGiYCh7+2gRvE4RiIcPRfM6f
BeC4AfBONOziipUEZKzxa1NfBbPLZ4C/QgKO/t0BCezhABRP/PvwDN1Dulsr4R+A
cJkVV5MW8Q+XarfCaCMczE1ZMKxRHjuvK9buY0V7xdlfUNLjUA86iOe/FP3gx7kC
AwEAAaOCAQkwggEFMHAGA1UdHwRpMGcwZaBjoGGkXzBdMQswCQYDVQQGEwJVUzEQ
MA4GA1UEChMHRXF1aWZheDEtMCsGA1UECxMkRXF1aWZheCBTZWN1cmUgQ2VydGlm
aWNhdGUgQXV0aG9yaXR5MQ0wCwYDVQQDEwRDUkwxMBoGA1UdEAQTMBGBDzIwMTgw
ODIyMTY0MTUxWjALBgNVHQ8EBAMCAQYwHwYDVR0jBBgwFoAUSOZo+SvSspXXR9gj
IBBPM5iQn9QwHQYDVR0OBBYEFEjmaPkr0rKV10fYIyAQTzOYkJ/UMAwGA1UdEwQF
MAMBAf8wGgYJKoZIhvZ9B0EABA0wCxsFVjMuMGMDAgbAMA0GCSqGSIb3DQEBBQUA
A4GBAFjOKer89961zgK5F7WF0bnj4JXMJTENAKaSbn+2kmOeUJXRmm/kEd5jhW6Y
7qj/WsjTVbJmcVfewCHrPSqnI0kBBIZCe/zuf6IWUrVnZ9NA2zsmWLIodz2uFHdh
1voqZiegDfqnc1zqcPGUIWVEX/r87yloqaKHee9570+sB3c4
-----END CERTIFICATE-----
_EOF_
```


Y eso es todo, cada vez que se levante la interfaz wan se actualizará el dns en consonancia.

