Amazon Virtual Private Cloud (VPC) es un servicio que permite a los usuarios de AWS lanzar recursos en una red virtual aislada lógicamente. Esta red proporciona un control total sobre el entorno de red, incluyendo la selección del rango de direcciones IP, la creación de subredes y la configuración de tablas de rutas y puertas de enlace de red.

## Características de AWS VPC
- **Aislamiento de Red**: Permite crear un espacio de red aislado dentro de AWS.
- **Subnetting**: Posibilidad de dividir la VPC en múltiples subredes para segmentar recursos.
- **Seguridad Avanzada**: Uso de listas de control de acceso (ACL) y grupos de seguridad para administrar el tráfico.
- **Conectividad**: Soporta conexiones a Internet mediante Internet Gateway y conexiones privadas mediante VPN o Direct Connect.
- **Escalabilidad**: Puede adaptarse al crecimiento de la infraestructura mediante subredes adicionales y conectividad con otras VPCs.
- **Integración con otros servicios de AWS**: Se integra con EC2, RDS, Lambda, entre otros.

## Puntos Fuertes
- **Seguridad**: Gracias a las ACLs, Grupos de Seguridad y NAT Gateway, se puede restringir el tráfico entrante y saliente.
- **Flexibilidad**: Posibilidad de diseñar arquitecturas de red según los requerimientos.
- **Conectividad Híbrida**: Soporta integración con redes locales mediante VPN o AWS Direct Connect.
- **Alta Disponibilidad**: Se pueden desplegar recursos en múltiples zonas de disponibilidad.

## Puntos Débiles
- **Complejidad**: La configuración avanzada puede resultar complicada para usuarios sin experiencia en redes.
- **Costos Ocultos**: Aunque la VPC en sí es gratuita, ciertos elementos como NAT Gateway y VPN pueden generar costos elevados.
- **Límites predeterminados**: AWS impone restricciones en la cantidad de subredes, direcciones IP y conexiones, que pueden requerir aumentos de cuota.

## Creación de una Arquitectura en AWS VPC
1. **Definir el rango de direcciones IP** utilizando CIDR (ejemplo: `10.0.0.0/16`).
2. **Crear Subredes** en diferentes zonas de disponibilidad para alta disponibilidad.
3. **Configurar una Tabla de Rutas** para definir la conectividad entre subredes e internet.
4. **Agregar un Internet Gateway** para permitir tráfico de salida a internet.
5. **Opcional: Configurar Virtual Private Gateway** para conectividad VPN con redes locales.
6. **Configurar ACLs y Grupos de Seguridad** para restringir tráfico según las necesidades.
7. **Implementar NAT Gateway** si se necesitan instancias privadas con acceso a internet sin ser accesibles desde el exterior.

## Subnetting en AWS VPC
AWS permite dividir la VPC en subredes:
- **Subred Pública**: Con acceso a Internet mediante Internet Gateway.
- **Subred Privada**: Sin acceso directo a Internet, requiere NAT Gateway o VPN.
- **Subred de Base de Datos**: Utilizada para alojar RDS u otras bases de datos, sin acceso directo a Internet.

## Internet Gateway (IGW)
- Permite que instancias dentro de una VPC accedan a Internet.
- Se debe asociar a una tabla de rutas con una ruta `0.0.0.0/0` apuntando al IGW.

## Virtual Private Gateway (VGW)
- Permite la conexión segura entre la VPC y redes locales mediante VPN.
- Se usa en conjunto con AWS Site-to-Site VPN o AWS Direct Connect.

## Network ACL (Access Control List)
- Controla el tráfico entrante y saliente a nivel de subred.
- Reglas basadas en prioridad, con permisos explícitos de permitir o denegar.
- Se aplican a todo el tráfico en la subred.

## Grupos de Seguridad (Security Groups)
- Controlan el tráfico a nivel de instancia.
- Son stateful, es decir, permiten automáticamente respuestas al tráfico permitido.
- Reglas basadas en protocolos, puertos y direcciones IP origen/destino.

## Bastion Host
Un Bastion Host es un servidor diseñado para actuar como un punto de acceso seguro a una red privada.  Este permite establecer conexión con el desde Internet para posteriormente acceder a las subredes o instancias privadas de manera segura (Configurando unas ACL y Security Groups que solo a el le permitan acceder).

----


# Al Crear la Infra 

- **Crear una VPC** 🛠
    
    - Definir el rango de direcciones IP con CIDR (ejemplo: `10.0.0.0/16`).
        
- **Definir subredes** 🏢
    
    - Dividir la VPC en **subredes públicas y privadas** en distintas zonas de disponibilidad.
        
- **Configurar una tabla de rutas** 📍
    
    - Definir cómo se enruta el tráfico entre las subredes e Internet.
        
- **Agregar un Internet Gateway (IGW)** 🌍
    
    - Permitir que las instancias de la subred pública accedan a Internet.
        
- **Configurar un Virtual Private Gateway (VGW)** 🔒
    
    - Para conectar tu VPC a una red local mediante VPN.
        
- **Configurar ACLs y Grupos de Seguridad** 🔐
    
    - Para controlar el tráfico entrante y saliente de la VPC.
        
- **Opcional: Implementar un NAT Gateway** 🔄
    
    - Permite que instancias privadas accedan a Internet sin ser accesibles desde el exterior.

