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



