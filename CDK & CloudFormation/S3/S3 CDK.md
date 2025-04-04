Este código utiliza AWS Cloud Development Kit (CDK) para crear dos buckets de S3, establecer políticas de versión y acceso, y configurar reglas de ciclo de vida para los objetos dentro de los buckets. Vamos a desglosarlo y explicarlo en detalle.

## Importación de Dependencias

```typescript
import * as cdk from 'aws-cdk-lib';
import { Construct } from 'constructs';
import * as s3 from 'aws-cdk-lib/aws-s3';
import * as s3deploy from 'aws-cdk-lib/aws-s3-deployment';
```

Aquí se importan las dependencias necesarias:

- `cdk`: la biblioteca principal de AWS CDK.
- `Construct`: una clase base que se utiliza para crear recursos dentro de una pila.
- `s3`: el módulo de S3 del CDK para interactuar con Amazon S3.
- `s3deploy`: módulo de CDK que permite realizar implementaciones a S3 (aunque no se usa explícitamente en el código proporcionado).

## Definición de la Clase `CdkStack`

```typescript
export class CdkStack extends cdk.Stack {
  constructor(scope: Construct, id: string, props?: cdk.StackProps) {
    super(scope, id, props);
    ...
```

Aquí se define una nueva pila (stack) llamada `CdkStack`, que hereda de `cdk.Stack`. Un "stack" es un conjunto de recursos de AWS que CDK gestiona. El constructor toma tres parámetros:

- `scope`: el alcance de la pila, en este caso, un `Construct` superior.
- `id`: un identificador único de la pila.    
- `props`: opciones adicionales para configurar la pila.

## Creación del Primer Bucket S3

```typescript
const bucket = new s3.Bucket(this, 'logBucketNacho', {
  bucketName: 'oc-s3-log-bucket-nacho-481186298209', 
  versioned: true, 
  blockPublicAccess: s3.BlockPublicAccess.BLOCK_ALL, 
  encryption: s3.BucketEncryption.S3_MANAGED, 
  accessControl: s3.BucketAccessControl.LOG_DELIVERY_WRITE,
  removalPolicy: cdk.RemovalPolicy.RETAIN,
});
```

Aquí se crea el primer bucket S3 con el nombre `oc-s3-log-bucket-nacho-481186298209` y se configuran varias propiedades:

- `versioned: true`: activa el versionado de objetos, lo que permite mantener múltiples versiones de un objeto.
- `blockPublicAccess: s3.BlockPublicAccess.BLOCK_ALL`: bloquea cualquier acceso público al bucket, lo que lo hace más seguro.
- `encryption: s3.BucketEncryption.S3_MANAGED`: habilita el cifrado de objetos usando el cifrado gestionado por S3.
- `accessControl: s3.BucketAccessControl.LOG_DELIVERY_WRITE`: permite que los servicios de AWS escriban logs en este bucket.
- `removalPolicy: cdk.RemovalPolicy.RETAIN`: indica que el bucket no debe eliminarse cuando se elimina la pila.

## Creación del Segundo Bucket S3

```typescript
const bucket2 = new s3.Bucket(this, 'StandardBucket-Nacho', {
  bucketName: 'oc-s3-bucket-nacho-481186298209',
  versioned: true,
  blockPublicAccess: s3.BlockPublicAccess.BLOCK_ALL,
  encryption: s3.BucketEncryption.S3_MANAGED,
  removalPolicy: cdk.RemovalPolicy.RETAIN,
  objectOwnership: s3.ObjectOwnership.OBJECT_WRITER,
  serverAccessLogsPrefix: '',
  serverAccessLogsBucket: bucket,
});
```

Este es el segundo bucket que se crea, llamado `oc-s3-bucket-nacho-481186298209`. Se configura de manera similar al primero, pero con algunas diferencias clave:

- `objectOwnership: s3.ObjectOwnership.OBJECT_WRITER`: establece que los objetos que se cargan en este bucket serán propiedad del escritor del objeto (en lugar de la cuenta del propietario del bucket).
- `serverAccessLogsPrefix: ''`: no se especifica un prefijo para los logs, lo que permite que los logs sean particionados por el campo `EVENT_TIME`.
- `serverAccessLogsBucket: bucket`: indica que los logs de acceso del servidor de este bucket se almacenarán en el primer bucket (`logBucketNacho`).

## Reglas de Ciclo de Vida para el Primer Bucket

Las reglas de ciclo de vida definen cómo se gestionan los objetos dentro de un bucket. En este caso, se configuran tres reglas para el primer bucket:

### Regla de limpieza diaria ([[Lifecycle Policies]])

```typescript
bucket.addLifecycleRule({
  id: 'daily-housekeeping',
  enabled: true,
  abortIncompleteMultipartUploadAfter: cdk.Duration.days(3),
  expiredObjectDeleteMarker: true,
});
```

- `abortIncompleteMultipartUploadAfter: cdk.Duration.days(3)`: detiene las cargas de múltiples partes incompletas después de 3 días.
- `expiredObjectDeleteMarker: true`: elimina los marcadores de objetos expirados.

### Regla de expiración de versiones no actuales

```typescript
bucket.addLifecycleRule({
  id: 'expire-noncurrent-objects',
  enabled: true,
  noncurrentVersionExpiration: cdk.Duration.days(7),
  noncurrentVersionsToRetain: 5,
});
```

- `noncurrentVersionExpiration: cdk.Duration.days(7)`: elimina las versiones no actuales (versiones anteriores) después de 7 días.
- `noncurrentVersionsToRetain: 5`: mantiene un máximo de 5 versiones no actuales.

### Regla de transición de objetos actuales a almacenamiento inteligente

```typescript
bucket.addLifecycleRule({
  id: 'transition-current-objects-to-intelligent-tiering',
  enabled: true,
  transitions: [{
    storageClass: s3.StorageClass.INTELLIGENT_TIERING,
    transitionAfter: cdk.Duration.days(0),
  }],
});
```

- `transitionAfter: cdk.Duration.days(0)`: mueve los objetos a la clase de almacenamiento `INTELLIGENT_TIERING` inmediatamente después de su carga si son mayores a 128 KB.

### Crear un bucket con un bucket de logs

También podemos crear un bucket y crear  el bucket de logs al especificarlo.  

``` typescript
const bucket = new s3.Bucket(this, 'cdk-nacho-s3-bucket', {
versioned: true,
encryption: s3.BucketEncryption.S3_MANAGED,
blockPublicAccess: s3.BlockPublicAccess.BLOCK_ALL,
enforceSSL: true,
removalPolicy: cdk.RemovalPolicy.RETAIN,
autoDeleteObjects: false,
objectOwnership: s3.ObjectOwnership.BUCKET_OWNER_ENFORCED,
publicReadAccess: false,
serverAccessLogsPrefix: 'access-logs',
serverAccessLogsBucket: new s3.Bucket(this, 'cdk-nacho-s3-bucket-logs', {
	versioned: true,
	encryption: s3.BucketEncryption.S3_MANAGED,
	blockPublicAccess: s3.BlockPublicAccess.BLOCK_ALL,
	enforceSSL: true,
	removalPolicy: cdk.RemovalPolicy.RETAIN,
	autoDeleteObjects: false,
	objectOwnership: s3.ObjectOwnership.BUCKET_OWNER_ENFORCED,
	publicReadAccess: false,
	}),
});
```
