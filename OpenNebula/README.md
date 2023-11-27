

## Pregunta 1

Acceder al GUI de miniOne mediante el usuario oneadmin y añadir todos los cluster, hosts (localhosts), redes (vnet) y datastores (files, system, default) a Virtual Data Centre (VDC) default.



```powershell
root@minione:~# onevdc list
  ID NAME                        GROUPS   CLUSTERS      HOSTS      VNETS DATASTORES
   0 default                          2        ALL        ALL        ALL        ALL
```



## Pregunta 2

Crear un usuario denominado userOne y un grupo denominado groupOne:

- El usuario userOne pertenecerá al grupo groupOne.
- Tendrá permisos para USE, MANAGE y CREATE sobre imágenes, plantillas y máquinas virtuales sobre la zona OpenNebula sobre los recursos del grupo groupOne.



```powershell
root@minione:~# oneuser list
  ID NAME                   ENAB GROUP    AUTH            VMS     MEMORY        CPU
   3 userOne                yes  groupOne core        0 /   -      0M /   0.0 /   -
   1 serveradmin            yes  oneadmin server_c    0 /   -      0M /   0.0 /   -
   0 oneadmin               yes  oneadmin core              -          -          -

root@minione:~# oneacl list
  ID     USER RES_VHNIUTGDCOZSvRMAPtB   RID OPE_UMAC  ZONE
  17       #3     V--I-T-------------  @101     um-c    #0
```



## Pregunta 3

Crear una nueva plantilla de MV ttyLinux con los siguientes parámetros:

- Hypervisor KVM, 256 Mb RAM y 0,2 CPU de Physical CPU.

- Haciendo uso de la imagen ttyLinux-image.

- Con una tarjeta en la red vnet y driver emulación *virtio* (teclear texto). Activar conexión RDP y SSH.

- CPU Architecture x86_64, bus SATA.

- Features: ACPI=no and APIC=no

  

```powershell
root@minione:~# onetemplate show 1
TEMPLATE 1 INFORMATION
ID             : 1
NAME           : ttyLinux
USER           : oneadmin
GROUP          : oneadmin
LOCK           : None
REGISTER TIME  : 11/25 12:09:56

PERMISSIONS
OWNER          : um-
GROUP          : ---
OTHER          : ---

TEMPLATE CONTENTS
CONTEXT=[
  NETWORK="YES",
  SSH_PUBLIC_KEY="$USER[SSH_PUBLIC_KEY]" ]
CPU="0.2"
DISK=[
  IMAGE="ttyLinux",
  IMAGE_UNAME="oneadmin" ]
FEATURES=[
  ACPI="no",
  APIC="no" ]
GRAPHICS=[
  LISTEN="0.0.0.0",
  TYPE="VNC" ]
HOT_RESIZE=[
  CPU_HOT_ADD_ENABLED="NO",
  MEMORY_HOT_ADD_ENABLED="NO" ]
HYPERVISOR="kvm"
MEMORY="256"
MEMORY_RESIZE_MODE="BALLOONING"
MEMORY_UNIT_COST="MB"
NIC=[
  NETWORK="vnet",
  NETWORK_UNAME="oneadmin",
  RDP="YES",
  SECURITY_GROUPS="0",
  SSH="YES" ]
OS=[
  ARCH="x86_64",
  SD_DISK_BUS="sata" ]
VIDEO=[
  TYPE="virtio" ]
```



## Pregunta 4

Crear una máquina virtual persistente miLinux a partir de la plantilla ttyLinux creada anteriormente, asignándole la IP 172.16.100.10 (si no disponemos de la imagen, es necesario descargarla del marketplace previamente).

Descargar ttyLinux del marketplace (el del profesor no va):

```powershell
onemarketapp export 1 ttyLinux-image -d 1
```

Despues crear un template gráficamente y una VM.

```powershell
root@minione:/home/minione# ping 172.16.100.10
PING 172.16.100.10 (172.16.100.10) 56(84) bytes of data.
64 bytes from 172.16.100.10: icmp_seq=1 ttl=64 time=21.6 ms
64 bytes from 172.16.100.10: icmp_seq=2 ttl=64 time=0.881 ms
64 bytes from 172.16.100.10: icmp_seq=3 ttl=64 time=0.943 ms
64 bytes from 172.16.100.10: icmp_seq=4 ttl=64 time=0.578 ms
64 bytes from 172.16.100.10: icmp_seq=5 ttl=64 time=0.962 ms

--- 172.16.100.10 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4009ms
rtt min/avg/max/mdev = 0.578/4.997/21.623/8.313 ms
```



## Pregunta 5

Realizar una instantánea de la MV creada en el punto anterior. Modificar la IP de la tarjeta eth0 (ifconfig eth0 172.16.100.40) y verificar que podemos hacer ping a la nueva IP. Recuperar la instantánea para ver que restaura el valor inicial y hacer ping a la IP original (172.16.100.10).

Para conexión con SSH se puede usar el comando:

`$ ssh -oHostKeyAlgorithms=+ssh-rsa -oKexAlgorithms=+diffie-hellman-group1-sha1 root@172.16.100.10`

`(contraseña password)`



Adjuntar la salida de los comandos:
`Después de modificar la IP de la máquina $ onevm show idVM; (adjuntar lineas ETH0_IP y apartado SNAPSHOTS)$ ping 172.16.100.40$ ping 172.16.100.10 `

`Después de recuperar la instantanea $ ping 172.16.100.10`



Creamos una instantánea de la máquina en el apartado `Snapshots`

Debemos entrar a la maquina creada y mediante `ifconfig eth0 172.16.100.40` cambiamos su IP.

1. Después de modificar la IP de la máquina (`ETH0_IP` sigue siendo la misma ya que no es un cambio persistente):

```powershell
root@minione:/home/minione# onevm show 9

ETH0_IP="172.16.100.10",

SNAPSHOTS
  ID         TIME NAME                                           HYPERVISOR_ID
   0  11/26 13:00 Instantanea                                    snap-0
```



2. Ping a la nueva IP:

```powershell
root@minione:/home/minione# ping 172.16.100.40
PING 172.16.100.40 (172.16.100.40) 56(84) bytes of data.
64 bytes from 172.16.100.40: icmp_seq=1 ttl=64 time=20.3 ms
64 bytes from 172.16.100.40: icmp_seq=2 ttl=64 time=1.26 ms
64 bytes from 172.16.100.40: icmp_seq=3 ttl=64 time=0.929 ms
64 bytes from 172.16.100.40: icmp_seq=4 ttl=64 time=0.359 ms

--- 172.16.100.40 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3007ms
rtt min/avg/max/mdev = 0.359/5.713/20.305/8.430 ms
```



3. Ping a la antigua IP:

```powershell
root@minione:/home/minione# ping 172.16.100.10
PING 172.16.100.10 (172.16.100.10) 56(84) bytes of data.

--- 172.16.100.10 ping statistics ---
5 packets transmitted, 0 received, 100% packet loss, time 4077ms
```



4. Después de recuperar la instantánea:

```powershell
root@minione:/home/minione# ping 172.16.100.10
PING 172.16.100.10 (172.16.100.10) 56(84) bytes of data.
64 bytes from 172.16.100.10: icmp_seq=1 ttl=64 time=19.4 ms
64 bytes from 172.16.100.10: icmp_seq=2 ttl=64 time=1.21 ms
64 bytes from 172.16.100.10: icmp_seq=3 ttl=64 time=0.956 ms
64 bytes from 172.16.100.10: icmp_seq=4 ttl=64 time=1.05 ms

--- 172.16.100.10 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3008ms
rtt min/avg/max/mdev = 0.956/5.660/19.422/7.945 ms
```



## Pregunta 6

Crear un nuevo disco de 100Mb en formato ext3 y engancharlo a miLinux.



```powershell
root@minione:/home/minione# onevm show 9
VM DISKS
 ID DATASTORE  TARGET IMAGE                               SIZE      TYPE SAVE
  0 default    vda    miLinux-disk-0                      -/200M    file  YES
  2 default    sda    disco-extra                         -/100M    file   NO
  1 -          hda    CONTEXT                             -/-       -       -
```



## Pregunta 7

Redimensionar la memoria utilizada por miLinux, pasando de 256 a 512 Mb.



`Crear imagen disco vacío en OpenNebula`

Antes de redimensionar:

```powershell
root@minione:/home/minione# onevm list
  ID USER     GROUP    NAME                                     STAT  CPU     MEM HOST                              TIME
   9 oneadmin oneadmin miLinux                                  poff  0.2    256M localhost                     1d 05h37
```

`onevm poweroff --hard 9`

`onevm resize 9 --memory 512M`

Después de redimensionar:

```powershell
root@minione:/home/minione# onevm list
  ID USER     GROUP    NAME                                     STAT  CPU     MEM HOST                              TIME
   9 oneadmin oneadmin miLinux                                  poff  0.2    512M localhost                     1d 05h40
```



## Pregunta 8

Asignar una cuota al usuario userOne para que sólo pueda instanciar UNA máquina. Posteriormente cambiar el propietario de la máquina ttyLinux y de la plantilla ttyLinux a userOne. Verificar que no se puede crear otra máquina con la plantilla ttyLinux para el usuario userOne. 



Editar Users Quotas de **userOne** running VMs 1. 

Cambiar owner de `Template`, `Image` y `VM` a **userOne**.

Loguear como usuario: 

```powershell
oneuser login oneadmin
```



Intentar instanciar una nueva máquina:

```powershell
root@minione:/home/minione# onetemplate instantiate ttyLinux --user=userOne --password=userOne --name=miLinuxNew
[one.template.instantiate] User [3] : user [3] limit of 1 reached for RUNNING_VMS quota in VM.
```



## Pregunta 9

Crear una instancia de a partir de la plantilla ttyLinux denominada miLinuxSSH. Conectarse por ssh a esta máquina través del host localhost (Enganchar una NIC a la red vnet: IPv4 172.16.100.20).



Crear una VM con una template con esas características.

Modificar en miniOne `vi /etc/ssh/ssh_config`



![image-20231127201052915](D:\UDC\MUEI\ICS\GIT-ICS\OpenNebula\img\image-20231127201052915.png)



```powershell
root@minione:/home/minione# ssh root@172.16.100.20
Warning: Permanently added '172.16.100.20' (RSA) to the list of known hosts.

Chop wood, carry water.

#
```





## Pregunta 10

Crear una red privada virtual miNET2 (172.16.2.0/24). Crear una nueva máquina a partir de la plantilla ttyLinux denominada miLinux2 y conectarla a la red miNET2. Crear una red privada virtual miNET3 (172.16.3.0/24). Crear una nueva máquina a partir de la plantilla ttyLinux denominda miLinux3 y conectarla a la red miNET3.



Crear la red con estas configuraciones, tanto `miNET2` como `miNET3`

![image-20231127201625577](D:\UDC\MUEI\ICS\GIT-ICS\OpenNebula\img\image-20231127201625577.png)



![image-20231127201721685](D:\UDC\MUEI\ICS\GIT-ICS\OpenNebula\img\image-20231127201721685.png)



```powershell
root@minione:/home/minione# onevm list
  ID USER     GROUP    NAME        STAT  CPU     MEM HOST        TIME
  12 oneadmin oneadmin miLinux3    runn  0.2    256M localhost   0d 00h01
  11 oneadmin oneadmin miLinux2    runn  0.2    256M localhost   0d 00h05
  10 oneadmin oneadmin miLinuxSSH  runn  0.2    256M localhost   0d 00h44
   9 oneadmin oneadmin miLinux     runn  0.2    512M localhost   1d 06h51

root@minione:/home/minione# onevnet list
  ID USER     GROUP    NAME    CLUSTERS   BRIDGE    STATE   LEASES OUTD ERRO
   2 oneadmin oneadmin miNET3  0          onebr2    rdy       1    0    0
   1 oneadmin oneadmin miNET2  0          onebr1    rdy       1    0    0
   0 oneadmin oneadmin vnet    0          minionebr rdy       2    0    0
```

