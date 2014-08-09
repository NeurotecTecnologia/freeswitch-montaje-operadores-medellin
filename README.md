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

  * interfaz de escucha **SIP** y **RTP**.
  * transporte **UDP,TCP,TLS**.
  * controles de acceso **ACL**.
  * Nateo, NAT.
  * Contexto para ejecución de plan de marcación.
  * Pasarelas (**Gateways**) entrantes (**inbound**) y salientes (**outbound**).

Partimos de la configuración vanilla de *Freeswitch* como ejemplo.


# CONFIGURANDO PARA UNE

...

# CONFIGURANDO PARA CLARO

...

# CASO 1 (DOS TARJETAS DE RED)

Implementación 2 tarjetas de red: operador, red local.

...

# CASO 2 (UNA TARJETA DE RED)

Implementación 1 tarjeta de red: operador, red local.

...

# ENLAZANDO INTERFACES ALIAS

...
