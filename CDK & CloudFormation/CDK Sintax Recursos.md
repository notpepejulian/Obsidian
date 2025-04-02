## 1. Crear un Bucket en S3

### Código con explicación detallada en los comentarios

```typescript
import * as cdk from 'aws-cdk-lib'; // Importamos el módulo principal de CDK
import { Construct } from 'constructs'; // Importamos "Construct", la base de todos los recursos en CDK
import * as s3 from 'aws-cdk-lib/aws-s3'; // Importamos el módulo de S3 para crear Buckets

// Definimos una clase que representa nuestro Stack
export class MiProyectoCdkStack extends cdk.Stack {
  // El constructor recibe el "scope" (contexto en el que se define), un identificador y propiedades opcionales
  constructor(scope: Construct, id: string, props?: cdk.StackProps) {
    super(scope, id, props); // Llamamos al constructor de la clase padre (cdk.Stack)

    // Creamos un bucket S3 con configuración básica
    new s3.Bucket(this, 'MiPrimerBucket', {
      removalPolicy: cdk.RemovalPolicy.DESTROY, // Elimina el bucket al destruir el Stack
    });
  }
}
```

### Explicación línea por línea

1. **Importamos los módulos necesarios**
    
    - `import * as cdk from 'aws-cdk-lib'` → Importa el módulo principal de CDK, que contiene todas las clases necesarias para definir infraestructura.
        
    - `import { Construct } from 'constructs'` → `Construct` es la clase base para todos los recursos de CDK.
        
    - `import * as s3 from 'aws-cdk-lib/aws-s3'` → Importa el módulo específico de S3 para poder crear Buckets.
        
2. **Definimos una clase que representa nuestro Stack**
    
    - `export class MiProyectoCdkStack extends cdk.Stack` → Creamos una nueva clase `MiProyectoCdkStack`, que extiende `cdk.Stack`.
        
    - `constructor(scope: Construct, id: string, props?: cdk.StackProps)` →
        
        - `scope` → Contexto en el que se define el Stack (por lo general, la aplicación CDK).
            
        - `id` → Nombre único dentro del `scope` para identificar el Stack.
            
        - `props` → Propiedades opcionales para el Stack (como permisos, regiones, etiquetas, etc.).
            
3. **Llamamos al constructor de la clase padre (`super()`)**
    
    - `super(scope, id, props)` → Llama al constructor de `cdk.Stack`, lo que permite que nuestro Stack herede todas sus funcionalidades.
        
4. **Creamos un Bucket de S3**
    
    - `new s3.Bucket(this, 'MiPrimerBucket', {...})` →
        
        - `this` → Se refiere a este Stack (donde se creará el Bucket).
            
        - `'MiPrimerBucket'` → Identificador único dentro del Stack.
            
        - `removalPolicy: cdk.RemovalPolicy.DESTROY` → Configuración para que el Bucket se elimine cuando destruimos el Stack.
            

## 2. Crear un Bucket con configuración avanzada

Si queremos más seguridad y control sobre el bucket, podemos configurar parámetros adicionales.

```typescript
new s3.Bucket(this, 'MiBucketAvanzado', {
  versioned: true, // Habilita el control de versiones para mantener un historial de los objetos
  removalPolicy: cdk.RemovalPolicy.RETAIN, // Evita que el bucket sea eliminado accidentalmente
  encryption: s3.BucketEncryption.S3_MANAGED, // Cifra los datos en el bucket con claves gestionadas por AWS
});
```

### Explicación línea por línea

1. `versioned: true`
    
    - Activa el versionado del bucket, lo que permite recuperar versiones anteriores de archivos eliminados o modificados.
        
2. `removalPolicy: cdk.RemovalPolicy.RETAIN`
    
    - Evita que el bucket se elimine cuando se destruye el Stack.
        
    - Es útil en entornos de producción para prevenir la pérdida accidental de datos.
        
3. `encryption: s3.BucketEncryption.S3_MANAGED`
    
    - Habilita la encriptación de los objetos usando claves gestionadas por AWS.
        
    - Alternativas:
        
        - `BucketEncryption.UNENCRYPTED` → Sin cifrado.
            
        - `BucketEncryption.KMS` → Usa claves KMS personalizadas.
            


> [[|Propiedades de S3 en CDK]]

## 3. Crear una VPC personalizada

CDK nos permite definir redes virtuales en AWS con VPCs personalizadas.

```typescript
import * as ec2 from 'aws-cdk-lib/aws-ec2'; 

const vpc = new ec2.Vpc(this, 'MiVPCPersonalizada', {
  maxAzs: 2, 
  subnetConfiguration: [ 
    {
      cidrMask: 24, 
      name: 'PublicSubnet', 
      subnetType: ec2.SubnetType.PUBLIC,
    },
    {
      cidrMask: 24,
      name: 'PrivateSubnet',
      subnetType: ec2.SubnetType.PRIVATE_WITH_EGRESS, 
    },
  ],
});
```

### Explicación línea por línea

1. **Importamos el módulo `aws-ec2`**
    
    - `import * as ec2 from 'aws-cdk-lib/aws-ec2'` → Nos permite definir redes VPC y recursos relacionados.
        
2. **Creamos una VPC personalizada**
    
    - `const vpc = new ec2.Vpc(this, 'MiVPCPersonalizada', {...})` → Crea una nueva VPC en AWS.
        
3. **Propiedad `maxAzs`**
    
    - Define el número máximo de Zonas de Disponibilidad en las que se distribuirán las subnets.
        
    - AWS recomienda distribuir las subnets en varias AZs para mejorar la tolerancia a fallos.
        
4. **Propiedad `subnetConfiguration`**
    
    - Se usa para definir qué tipos de subnets tendrá la VPC.
        
    - **Subnet Pública**
        
        - `subnetType: ec2.SubnetType.PUBLIC` → Permite que las instancias en esta subnet tengan acceso a Internet.
            
    - **Subnet Privada**
        
        - `subnetType: ec2.SubnetType.PRIVATE_WITH_EGRESS` → No tiene acceso a Internet directamente, pero puede salir a Internet a través de un **NAT Gateway**.
            

## 4. Crear una instancia EC2 en la VPC

Ahora que tenemos una VPC, agreguemos una instancia EC2 dentro de ella.

```typescript
const instance = new ec2.Instance(this, 'MiInstancia', {
  vpc, 
  instanceType: ec2.InstanceType.of(ec2.InstanceClass.T2, ec2.InstanceSize.MICRO), 
  machineImage: ec2.MachineImage.latestAmazonLinux(), 
});
```

### Explicación línea por línea

1. `vpc`
    
    - Especifica en qué VPC se va a desplegar la instancia.
        
2. `instanceType`
    
    - Define el tipo de instancia.
        
    - `ec2.InstanceType.of(ec2.InstanceClass.T2, ec2.InstanceSize.MICRO)` →
        
        - Clase `T2` → Instancias económicas y de propósito general.
            
        - Tamaño `MICRO` → Bajo consumo de recursos.
            
3. `machineImage`
    
    - `ec2.MachineImage.latestAmazonLinux()` → Usa la imagen de Amazon Linux más reciente.
        
