En AWS CDK, cuando trabajas con una **VPC** (Virtual Private Cloud), hay una serie de propiedades que puedes configurar para personalizar la VPC y su infraestructura asociada. Esta es una lista de todas las propiedades principales que puedes asignar a una VPC con **AWS CDK** utilizando **TypeScript**.

### 1. **cidr**: `string`

- **Descripción**: Define el rango de direcciones IP para la VPC en formato CIDR (Classless Inter-Domain Routing).    
- **Ejemplo**:

```typescript
cidr: '10.0.0.0/16'
```    

### 2. **maxAzs**: `number`

- **Descripción**: Define el número máximo de zonas de disponibilidad (AZs) en las que se distribuirán las subredes de la VPC.
- **Valor predeterminado**: `3`
- **Uso**: Si se configura en 2, solo se usarán dos zonas de disponibilidad para la VPC.
- **Ejemplo**:

```typescript
maxAzs: 2
```

### 3. **subnetConfiguration**: `SubnetConfiguration[]`

- **Descripción**: Define las configuraciones de las subredes dentro de la VPC. Puedes especificar diferentes tipos de subredes, como públicas, privadas, y con acceso a internet.
- **Uso**: Especificas detalles como el nombre, el rango CIDR y el tipo de subred.
- **Ejemplo**:

```typescript
...
    subnetConfiguration: [
      {
        name: 'public',
        subnetType: ec2.SubnetType.PUBLIC,
        cidrMask: 24,
      },
      {
        name: 'private',
        subnetType: ec2.SubnetType.PRIVATE_WITH_NAT,
        cidrMask: 24,
      },
    ]
...
```
    

### 4. **natGateways**: `number`

- **Descripción**: Define el número de NAT Gateways que se deben crear en la VPC. Los NAT Gateways permiten a las instancias en subredes privadas acceder a Internet.
- **Valor predeterminado**: `1` (si se habilita NAT en una subred privada)
- **Uso**: Si se configura como `0`, no se crearán NAT Gateways.
- **Ejemplo**:

```typescript
natGateways: 2
```

### 5. **enableDnsSupport**: `boolean`

- **Descripción**: Habilita o deshabilita el soporte de DNS en la VPC. Cuando está habilitado, la VPC utiliza el sistema de nombres de dominio de AWS (Route 53 Resolver).
- **Valor predeterminado**: `true`
- **Ejemplo**:

```typescript
enableDnsSupport: true
```

### 6. **enableDnsHostnames**: `boolean`

- **Descripción**: Habilita o deshabilita los nombres de host DNS dentro de la VPC. Si está habilitado, se pueden usar nombres de host DNS dentro de la VPC para referirse a instancias EC2.
- **Valor predeterminado**: `false`
- **Ejemplo**:

```typescript
enableDnsHostnames: true
```

### 7. **vpnGateway**: `boolean`

- **Descripción**: Si se habilita, se creará un gateway de VPN para permitir la conexión entre la VPC y redes externas (por ejemplo, redes locales). 
- **Valor predeterminado**: `false`
- **Ejemplo**:

```typescript
vpnGateway: true
```

### 8. **gatewayEndpoints**: `GatewayVpcEndpoint[]`

- **Descripción**: Permite crear puntos de enlace de gateway para servicios como S3 y DynamoDB, evitando que el tráfico tenga que pasar por Internet.
- **Uso**: Puedes configurar un punto de enlace para servicios específicos.
- **Ejemplo**:

```typescript
gatewayEndpoints: [{
    service: ec2.GatewayVpcEndpointAwsService.S3
}]
```

### 9. **interfaceEndpoints**: `InterfaceVpcEndpoint[]`

- **Descripción**: Permite crear puntos de enlace de interfaz para servicios específicos de AWS (como EC2, Lambda, etc.) dentro de la VPC.
- **Uso**: Se especifica el servicio al que se conectará a través de un endpoint de interfaz.
- **Ejemplo**:

```typescript
interfaceEndpoints: [{
    service: ec2.InterfaceVpcEndpointAwsService.S3
}]
```

### 10. **defaultSecurityGroup**: `SecurityGroup`

- **Descripción**: La configuración de seguridad por defecto que se aplicará a la VPC. Esto te permite definir las reglas de entrada y salida de forma predeterminada    
- **Uso**: Es útil si deseas personalizar la configuración de seguridad de la VPC.
- **Ejemplo**:

```typescript
defaultSecurityGroup: mySecurityGroup
```

### 11. **enableVpnGateway**: `boolean`

- **Descripción**: Configura si se debe habilitar un gateway de VPN de forma predeterminada en la VPC.
- **Valor predeterminado**: `false`
- **Ejemplo**:

```typescript
enableVpnGateway: true
```

### 12. **ipAddresses**: `ec2.IpAddresses`

- **Descripción**: Especifica cómo se asignan las direcciones IP a las subredes de la VPC. Puedes elegir direcciones públicas o privadas.
- **Ejemplo**:

```typescript
ipAddresses: ec2.IpAddresses.cidr('10.0.0.0/16')
```

### 13. **tags**: `{ [key: string]: string }`

- **Descripción**: Un conjunto de etiquetas clave-valor para asignar a la VPC.    
- **Uso**: Puedes usar etiquetas para organizar y administrar recursos en AWS.
- **Ejemplo**:

```typescript
tags: {
'Environment': 'Production',
    'Project': 'MyProject'
}
```

### 14. **flowLogs**: `FlowLog[]`

- **Descripción**: Permite habilitar registros de flujo (Flow Logs) para capturar y almacenar información sobre el tráfico de red dentro y fuera de la VPC.
- **Uso**: Los registros pueden ser almacenados en CloudWatch Logs o S3.
- **Ejemplo**:

```typescript
flowLogs: [{
    destination: ec2.FlowLogDestination.toCloudWatchLogs()
}]
```

### 15. **subnetSelection**: `ec2.SubnetSelection`

- **Descripción**: Permite seleccionar subredes específicas de la VPC, según su tipo y otros criterios.    
- **Uso**: Se puede utilizar para seleccionar subredes privadas, públicas o de aislamiento.
- **Ejemplo**:

```typescript
subnetSelection: {
    subnetType: ec2.SubnetType.PUBLIC
}
```

### 16. **enableSsmSupport**: `boolean`

- **Descripción**: Habilita o deshabilita la integración con AWS Systems Manager (SSM), lo que permite que las instancias dentro de la VPC se administren sin necesidad de una IP pública.
- **Valor predeterminado**: `false`
- **Ejemplo**:

```typescript
enableSsmSupport: true
```

### 17. **sharedTenancy**: `ec2.Tenancy`

- **Descripción**: Define el tipo de tenencia de las instancias de la VPC.
- **Opciones**:
    - `DEFAULT`: Instancias con tenencia compartida (por defecto).
    - `DEDICATED`: Instancias en servidores dedicados.
- **Ejemplo**:

```typescript
sharedTenancy: ec2.Tenancy.DEFAULT
```

### 18. **defaultVpc**: `boolean`

- **Descripción**: Permite crear o no una VPC por defecto en una cuenta de AWS, si se usa el servicio `Amazon VPC`.
- **Valor predeterminado**: `true`
- **Ejemplo**:

```typescript
defaultVpc: false
```

---

## Ejemplo de Código con Propiedades Completas de una VPC

Aquí tienes un ejemplo completo de cómo se podría crear una VPC utilizando varias de las propiedades mencionadas anteriormente:

```typescript
import * as cdk from 'aws-cdk-lib';
import * as ec2 from 'aws-cdk-lib/aws-ec2';
import { Construct } from 'constructs';

export class MyVpcStack extends cdk.Stack {
  constructor(scope: Construct, id: string, props?: cdk.StackProps) {
    super(scope, id, props);

    const myVpc = new ec2.Vpc(this, 'MyVPC', {
      cidr: '10.0.0.0/16',
      maxAzs: 3,
      subnetConfiguration: [
        {
          name: 'Public',
          subnetType: ec2.SubnetType.PUBLIC,
          cidrMask: 24,
        },
        {
          name: 'Private',
          subnetType: ec2.SubnetType.PRIVATE_WITH_NAT,
          cidrMask: 24,
        },
      ],
      natGateways: 1,
      enableDnsSupport: true,
      enableDnsHostnames: true,
      vpnGateway: true,
      tags: {
        'Environment': 'Production',
      },
    });
  }
}
```

Este código crea una **VPC** con subredes públicas y privadas, NAT Gateway, soporte de DNS, y etiquetas para ayudar en la gestión de la infraestructura.

---
