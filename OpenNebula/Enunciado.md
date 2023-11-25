# Instrucciones instalación miniOne

#### Instalación de miniOne

OpenNebula cuenta con una implementación de front-end, donde todos los servicios están disponibles en un único host. Para la instalación del entorno all-in-one de OpenNebula, denominado miniOne, haremos uso de una máquina virtual: 

NOTA: Únicamente existen binarios para intel y amd, no para arm (apple M1-M2)



- VirtualBox (GPL, macOS/Windows/Linux)
- VMware Fusion (macOS)
- VMware Workstation Player (Windows/Linux)



También se puede usar:



- Qemu (free, GPL) for any platform
- Microsoft Hyper-V (Windows)
- KVM (free, GPL, Linux)



### Configuración de la máquina virtual:

```
Nombre: minione
Sistemas Operativos: CentOS 7 o 8, Debian 9 o 10, Ubuntu 18.04 o 20.04 (preferentemente server 64-bit).
Sistema>Aceleración>Interfaz de paravirtualización: KVM
Memoria de video: 32 Mb
4 Gb RAM y 20 Gb HDD (SATA - VDI)
Últimas actualizaciones del S.O. instaladas y acceso en modo privilegiado (root).
Paquete openssh-server instalado. 
Red Adaptador 1: NAT
```

Nos aseguraremos de poder acceder desde el host anfitrión a la máquina instanciada en los siguientes puertos: 

​    Nombre	   Protocolo	Puerto Anfitrión	Puerto Invitado

```
SSH		TCP	2222	 	22
SunStone	TCP	8080		80
OCA		TCP	2633		2633
FireEdge	CP	2616		2616
OneGate		TCP	5030		5030
```

En VirtualBox se puede llevar a cabo gráficamente o con los comandos:



- VBoxManage modifyvm "minione" --natpf1 "SSH,tcp,,2222,,22"
- VBoxManage modifyvm "minione" --natpf1 "SunStone,tcp,,8080,,80"
- VBoxManage modifyvm "minione" --natpf1 "OCA,tcp,,2633,,2633"
- VBoxManage modifyvm "minione" --natpf1 "FireEdge,tcp,,2616,,2616"
- VBoxManage modifyvm "minione" --natpf1 "OneGate,tcp,,5030,,5030"



NOTA: Es conveniente realizar instantáneas durante el proceso de instalación para habilitar puntos de retorno.

### Instalación de miniOne:

Una vez instanciada la máquina virtual (miniOne) en el hipervisor, accedemos y ejecutamos en modo privilegiado:

```
anfitrion> ssh -p 2222 localhost
miniOne# wget 'https://github.com/OpenNebula/minione/releases/latest/download/minione'
minione@minione:~$ sudo bash minione --force
### Checks & detection
Checking cpu virtualization capabilities  SKIP QEMU will be used
Checking augeas is installed  SKIP will try to install
Checking free disk space  IGNORE
Checking apt-transport-https is installed  SKIP will try to install
Checking AppArmor  SKIP will try to modify
Checking for present ssh key  SKIP
Checking (iptables|netfilter)-persistent are installed  SKIP will try to install
Checking python3-pip is installed  SKIP will try to install
Checking ansible  SKIP will try to install
Checking terraform  SKIP will try to install
Checking unzip is installed  SKIP will try to install
### Main deployment steps:
Install OpenNebula frontend version 6.8
Install Terraform
Configure bridge minionebr with IP 172.16.100.1/24
Enable NAT over ens160
Modify AppArmor
Install OpenNebula KVM node
Export appliance and update VM template
Install  augeas-tools apt-transport-https iptables-persistent netfilter-persistent python3-pip unzip
Install pip 'ansible==2.9.9'
Do you agree? [yes/no]: yes 
[.....Installation...]
### Report
OpenNebula 6.8 was installed
Sunstone is running on:
  http://10.51.1.220/
FireEdge is running on:
  http://10.51.1.220:2616/
Use following to login:
  user: oneadmin
  password: Tw6F3YT8v4
```

##### Verificar que FireEdge está bien configurado:

En el fichero /etc/one/sunstone-server.conf modificar:

```
###################################### 
# FireEdge
######################################
:private_fireedge_endpoint: http://localhost:2616
:public_fireedge_endpoint: http://localhost:2616
```

Reiniciar sunstone

```
# systemctl restart opennebula-sunstone
```

##### Verificar instalación

Para verificar la correcta instalación, acceder al interface web (http://localhost:8080) o bien desde CLI (ssh user@localhost -p 2222):

```
user@miniOne: sudo -i
root@miniOne:~# onehost list
 ID NAME              CLUSTER   TVM      ALLOCATED_CPU      ALLOCATED_MEM STAT
   0 localhost                default              0           0 / 200 (0%)     0K / 3.8G          (0%) on
```

### OpenNebula Cloud API

Para el uso del Java OpenNebula Cloud API es necesario instalar:

\- Un IDE como Eclipse IDE for Java Developers

\- Descargar y modificar el Bulding Path para incorporar los JAR:



- ​	org.opennebula.client.jar (descargar java-oca-6.8.0.tar.gz).
- ​	xmlrpc-common-3.1.3.jar
- ​	xmlrpc-client-3.1.3.jar 
- ​	ws-commons-util-1.0.2.jar



**Documentación:**



- https://docs.opennebula.io/minione/
- https://docs.opennebula.io/
- https://docs.opennebula.io/6.8/integration_and_development/system_interfaces/java.html