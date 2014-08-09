freeswitch-instalacion-une
==========================

Tutorial de instalación base de Freeswitch usando las troncales SIP proveidas por UNE

Este tutorial no hace referencia a instalación o administración de
[Freeswitch 1.4](https://confluence.freeswitch.org/display/FREESWITCH/Installation),
lo que buscamos es mostrar como configurar y asegurar los perfiles
SIP conectando a los operadores locales **UNE Telecomunaciones**, **CLARO**.

# PERFIL SIP

A diferencia de *Asterisk* *Freeswitch* exige levantar o un subir perfil
SIP por *interface de red* del servidor estos perfiles inicialmente no son
funcionales con **interfaces alias** - aunque hay forma mirar: [interfaces
alias](#ENLAZANDO INTERFACES ALIAS) -, estos perfiles nos permiten
determinar:

  * Interfaz de escucha **SIP** y **RTP**.
  * Transporte **UDP,TCP,TLS**.
  * Controles de acceso **ACL**.
  * Nateo, NAT.
  * Contexto para ejecución de plan de marcación.
  * Pasarelas (**Gateways**) entrantes (**inbound**) y salientes (**outbound**).

Partimos de la configuración vanilla de *Freeswitch* como ejemplo.

En terminos generales, vamos a crear un perfil SIP para la interfaz que
conectamos al proveedor y una pasarela con o sin registro SIP
según el caso, un contexto de entrada y plan de marcado para enrutar
las llamadas entrantes.


### conf/sip_profiles/proveedor.xml

~~~xml
<profile name="proveedor">
  <aliases>
  </aliases>

  <gateways>
    <X-PRE-PROCESS cmd="include" data="proveedor/*.xml"/>
  </gateways>

  <domains>
  </domains>

  <settings>
    <param name="debug" value="0"/>
    <param name="sip-trace" value="no"/>
    <param name="sip-capture" value="no"/>

    <param name="watchdog-enabled" value="no"/>
    <param name="watchdog-step-timeout" value="30000"/>
    <param name="watchdog-event-timeout" value="30000"/>

    <param name="log-auth-failures" value="false"/>
    <param name="forward-unsolicited-mwi-notify" value="false"/>

    <!--!!identificamos el contexto-->
    <param name="context" value="proveedor"/>
    <param name="rfc2833-pt" value="101"/>

    <param name="dialplan" value="XML"/>
    <param name="dtmf-duration" value="2000"/>
	
    <param name="inbound-codec-prefs" value="PCMU,PCMA"/>
    <param name="outbound-codec-prefs" value="PCMU,PCMA"/>
	
    <param name="rtp-timer-name" value="soft"/>

    <param name="sip-port" value="5060"/>
	
    <!--!!reemplazar y.y.y.y con IPv4 de la interfaz de escucha
    conectada al proveedor-->
    <param name="rtp-ip" value="y.y.y.y"/>
    <param name="sip-ip" value="y.y.y.y"/>


    <param name="hold-music" value="$${hold_music}"/>
	
	<!--!!autorizamos llmadas desde ip del proveedor sin autenticacion-->
    <param name="apply-inbound-acl" value="200.13.230.38/32"/>
	
    <param name="record-path" value="$${recordings_dir}"/>
    <param name="record-template" value="${caller_id_number}.${target_domain}.${strftime(%Y-%m-%d-%H-%M-%S)}.wav"/>

    <param name="manage-presence" value="false"/>

    <param name="inbound-codec-negotiation" value="generous"/>
    <param name="tls" value="false"/>

    <param name="inbound-late-negotiation" value="false"/>
    <param name="inbound-zrtp-passthru" value="true"/>
    <param name="accept-blind-auth" value="false"/> 

    <param name="nonce-ttl" value="60"/>
    <param name="auth-calls" value="false"/> 
    <param name="auth-all-packets" value="false"/>
  
    <param name="rtp-timeout-sec" value="300"/>
    <param name="rtp-hold-timeout-sec" value="1800"/>
    <param name="challenge-realm" value="auto_from"/>
    </settings>
</profile>
~~~

#### CONFIGURANDO PARA UNE (conf/sip_profiles/proveedor/une.xml)

**UNE** exige que la interfaz de escucha sean el puerto **5060**, si lo tenemos en un
puerto diferente puede ser que logremos realizar llamadas pero no
recibir llamadas.

~~~xml
<include>
 <gateway name="une">
  <!--!!IP del proveedor-->
  <param name="realm" value="200.13.230.38"/>
  <param name="register" value="false"/>
  <param name="register-transport" value="udp"/>
  <param name="caller-id-in-from" value="true"/>
 </gateway>
</include>
~~~

##### CONFIGURANDO PARA CLARO

...


#### PLAN MARCACION ENTRANTE

El plan de marcaciÓn nos permite dar guia o ruta a la llamada entrante/saliente
a través de uso de las etiquetas **condition** y la ejecución de
acciones (**action**) a base de comando; **application**, recomendamos
revisar la documentación 
[dptools](https://wiki.freeswitch.org/wiki/Mod_dptools) de
**Freeswitch**.

##### conf/dialplan/proveedor.xml

Creamos el contexto del plan de marcación de las llamadas entrantes del
perfil SIP creado.

~~~xml
<include>
  <context name="proveedor">
    <X-PRE-PROCESS cmd="include" data="proveedor/*.xml"/>
  </context>
</include>
~~~

###### conf/dialplan/proveedor/midid.xml

Detallamos el plan de marcación para el número entrante, normalmente
conectamos la llamada entrante a un [IVR local][#CREACION IVR].

**Nota:** reemplazar **midid** con el número que se responde, si se
responde a una serie de números se puede usar una expresión regular
para seleccionar el grupo, ej: **^(520000\d)$**

~~~xml
 <extension name="did_midid">
     <!--reemplazar 5200000 con el DID de entrada-->
     <condition field="destination_number" expression="^(5200000)$">
      <action application="answer" />
      <action application="sleep" data="1000" />
      <action application="ivr" data="ivr_entrada" />
     </condition>
 </extension>
~~~


# CASO 1 (DOS TARJETAS DE RED)

Implementación 2 tarjetas de red: operador, red local.

~~~bash
cd /usr/local/freeswitch/conf
rm -rf conf/
git clone https://github.com/NeurotecTecnologia/freeswitch-montaje-caso-1.git conf
~~~


# CASO 2 (UNA TARJETA DE RED)

Implementación 1 tarjeta de red: operador, red local.

~~~bash
cd /usr/local/freeswitch/conf
rm -rf conf/
git clone https://github.com/NeurotecTecnologia/freeswitch-montaje-caso-2.git conf
~~~

# CASO 3 (TRES TARJETAS DE RED)

Implementación 3 tarjetas de red: operador, red local, red publica.

...

# CREACION IVR

[IVR Menu](https://wiki.freeswitch.org/wiki/IVR_Menu)


# ENLAZANDO INTERFACES ALIAS

...
