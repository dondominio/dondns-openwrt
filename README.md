# dondns-openwrt

DonDNS OpenWRT HowTO

## Para usuarios "normales" (sin autenticacion https)

Usando el interfaz web LUci:

```
Seleccionar Services -> Dynamic DNS
 
Pulsar el botón "Edit" en la opción IPv4
Poner "Service" a "custom"
Poner en "Custom update-URL" el valor "http://dondns.dondominio.com/plain/?user=[USERNAME]&password=[PASSWORD]&host=[DOMAIN]"
Poner en "Hostname" el valor del dominio a registrar
Poner en "Username" el nombre del usuario
Poner en "Password" el valor de DonDNS Key
En "Source of IP address" seleccionar "network"
En "Network" seleccionar "wan"
El resto dejarlo por defecto
Guardar y aplicar cambios
Asegurarse de que la casilla "Enabled" está activada
```

## Para usuarios "expertos" (con autenticacion https)
### Compatible con OpenWRT 15.05 o superior

Entrar en openWRT por linea de comandos (ssh).

Instalar curl mediante opkg ( `opkg install curl`)
Editar el archivo `/etc/config/ddns` y configurarlo como sigue (cambiando los valores correspondientes)

```
config service 'myddns_ipv4'
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
option password '<DonDNS Key>'
option update_url 'http://dondns.dondominio.com/plain/?user=[USERNAME]&password=[PASSWORD]&host=[DOMAIN]'
option use_https '1'
option cacert 'IGNORE'
```

Y eso es todo, cada vez que se levante la interfaz wan se actualizará el dns en consonancia.

