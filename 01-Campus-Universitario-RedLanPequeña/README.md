Guía Práctica: Configuración de Red LAN para Campus Universitario
Nivel: CCNA | Simulador: Cisco Packet Tracer

Esta guía detalla los pasos fundamentales para interconectar dos infraestructuras de red distintas utilizando enrutamiento estático, VLSM (Variable Length Subnet Mask) y Router-on-a-Stick.

Fase 1: Segmentación Lógica con VLANs (Lado Izquierdo)
El objetivo es aislar tres departamentos (VLAN 10, 20 y 30) utilizando un solo enlace físico hacia el router para optimizar el hardware.

Creación de VLANs y Puertos de Acceso:
En los switches de extremo, se deben crear las VLANs en la base de datos local y asignar los puertos FastEthernet conectados a las PCs en modo acceso.
Comando clave: switchport mode access / switchport access vlan [ID]

Enlaces Troncales (Trunks):
Las conexiones entre switches, y desde el switch central hacia el router, deben configurarse como troncales para permitir el paso de etiquetas 802.1Q.
Dato vital de troubleshooting: El switch central debe tener las VLANs creadas en su memoria (vlan [ID]); de lo contrario, descartará el tráfico automáticamente por seguridad, aunque el puerto sea troncal.

Router-on-a-Stick:
En la interfaz física del router (ej. Gig0/0) no se asigna IP, solo se enciende (no shutdown). En su lugar, se crean subinterfaces lógicas para cada VLAN.
Comando clave: encapsulation dot1Q [VLAN_ID] seguido de la dirección IP que servirá como Gateway.

Fase 2: Optimización de IPs con VLSM (Lado Derecho)
Se requiere segmentar la red base 192.168.2.0 para tres departamentos físicos con 29, 14 y 5 hosts, calculando las máscaras exactas para no desperdiciar direcciones IPv4.

Subred A (29 hosts): Requiere máscara /27 (255.255.255.224). Rango de IPs útiles: .1 a .30.

Subred B (14 hosts): Requiere máscara /28 (255.255.255.240). Rango de IPs útiles: .33 a .46.

Subred C (5 hosts): Requiere máscara /29 (255.255.255.248). Rango de IPs útiles: .49 a .54.

Al contar con puertos físicos Gigabit dedicados en el router para este lado, se asigna la primera IP útil de cada segmento directamente a cada interfaz. Los switches operan en modo acceso por defecto en su VLAN nativa, por lo que no requieren configuración adicional compleja.

Fase 3: El Enlace Core y Enrutamiento Estático
Para que los extremos del campus logren comunicarse, se levanta una red punto a punto (ej. 192.168.1.0/24) que enlaza los dos routers principales.
Dado que los routers solo conocen las redes que tienen conectadas directamente por cable, es obligatorio implementar Rutas Estáticas cruzadas en sus tablas de enrutamiento:

En el Router Izquierdo: Se agregan las tres redes calculadas por VLSM apuntando hacia la IP de salto del router derecho.
Ejemplo: ip route 192.168.2.0 255.255.255.224 192.168.1.2

En el Router Derecho: Se agregan las tres subredes correspondientes a las VLANs apuntando hacia la IP de salto del router izquierdo.
Ejemplo: ip route 192.168.10.0 255.255.255.0 192.168.1.1

Resultado Final:
Al completar estas fases, se obtiene conectividad total comprobada mediante el protocolo ICMP (Ping) de extremo a extremo, simulando una infraestructura LAN universitaria robusta, segmentada y escalable.
