En AWS CDK, al crear un bucket de S3, puedes configurar una amplia variedad de propiedades para definir su comportamiento, seguridad, almacenamiento y más. Aquí te dejo una lista completa de las propiedades que se pueden asignar a un bucket de S3 en TypeScript, con una breve explicación de cada una.

## Propiedades de un Bucket de S3 en AWS CDK

### 1. **bucketName**: `string`

- **Descripción**: El nombre del bucket de S3.
- **Restricciones**: El nombre del bucket debe ser único a nivel global en S3.

Ejemplo:

```typescript
bucketName: 'my-bucket-name'
```

### 2. **versioned**: `boolean`

- **Descripción**: Activa el versionado de objetos en el bucket.
- **Valor predeterminado**: `false`
- **Uso**: Si se activa, S3 almacenará versiones anteriores de un objeto cada vez que se sobrescriba.

Ejemplo:

```typescript
versioned: true
```

### 3. **blockPublicAccess**: `s3.BlockPublicAccess`

- **Descripción**: Configura las reglas de acceso público al bucket.
- **Valor predeterminado**: `s3.BlockPublicAccess.BLOCK_ALL`
- **Uso**: Puedes bloquear todo el acceso público o personalizar la política.

Ejemplo:

```typescript
blockPublicAccess: s3.BlockPublicAccess.BLOCK_ALL
```

### 4. **encryption**: `s3.BucketEncryption`

- **Descripción**: Configura el tipo de cifrado de objetos dentro del bucket.
- **Opciones**:
    - `S3_MANAGED`: Cifrado gestionado por S3 (predeterminado).
    - `KMS`: Cifrado gestionado por AWS Key Management Service (KMS).
    - `UNENCRYPTED`: Sin cifrado.

Ejemplo:

```typescript
encryption: s3.BucketEncryption.S3_MANAGED
```

### 5. **encryptionKey**: `kms.IKey`

- **Descripción**: La clave KMS personalizada para cifrar objetos en el bucket, si se usa cifrado KMS.
- **Uso**: Solo se utiliza si `encryption` está configurado como `KMS`.

Ejemplo:

```typescript
encryptionKey: myKmsKey
```

### 6. **accessControl**: `s3.BucketAccessControl`

- **Descripción**: Configura el control de acceso del bucket.
- **Opciones**:
    - `PRIVATE`: El acceso está restringido a los propietarios.
    - `PUBLIC_READ`: Permite la lectura pública de los objetos.
    - `LOG_DELIVERY_WRITE`: Permite que los servicios de entrega de logs escriban en el bucket.

Ejemplo:

```typescript
accessControl: s3.BucketAccessControl.PUBLIC_READ
```

### 7. **objectOwnership**: `s3.ObjectOwnership`

- **Descripción**: Controla la propiedad de los objetos cargados en el bucket.
- **Opciones**:
    - `OBJECT_WRITER`: Los objetos serán propiedad del escritor (por defecto).
    - `BUCKET_OWNER_ENFORCED`: El propietario del bucket es el propietario de los objetos cargados.

Ejemplo:

```typescript
objectOwnership: s3.ObjectOwnership.BUCKET_OWNER_ENFORCED
```

### 8. **removalPolicy**: `cdk.RemovalPolicy`

- **Descripción**: Define lo que ocurre con el bucket cuando se elimina la pila.
- **Opciones**:
    - `DESTROY`: El bucket será eliminado al destruir la pila (predeterminado).
    - `RETAIN`: El bucket se mantiene incluso si se destruye la pila.
    - `SNAPSHOT`: Toma una instantánea del bucket antes de eliminarlo (solo aplicable a recursos que soporten snapshots).


Ejemplo:

```typescript
removalPolicy: cdk.RemovalPolicy.RETAIN
```

### 9. **websiteIndexDocument**: `string`

- **Descripción**: El documento que se servirá como la página de índice cuando el bucket esté configurado como un sitio web estático.
- **Uso**: Especifica un archivo como `index.html`.

Ejemplo:

```typescript
websiteIndexDocument: 'index.html'
```

### 10. **websiteErrorDocument**: `string`

- **Descripción**: El documento de error que se servirá cuando se acceda a una página que no exista en el bucket    
- **Uso**: Especifica un archivo como `error.html`.

Ejemplo:

```typescript
websiteErrorDocument: 'error.html'
```

### 11. **serverAccessLogsBucket**: `s3.Bucket`

- **Descripción**: Un bucket en el que almacenar los logs de acceso del servidor.
- **Uso**: Especifica un bucket diferente para almacenar los logs de acceso a este bucket.

Ejemplo:

```typescript
serverAccessLogsBucket: logBucket
```

### 12. **serverAccessLogsPrefix**: `string`

- **Descripción**: Un prefijo que se agregará a los logs de acceso del servidor, lo que permite organizar los logs dentro del bucket.

Ejemplo:

```typescript
serverAccessLogsPrefix: 'logs/'
```

### 13. **lifecycleRules**: `s3.LifecycleRule[]`

- **Descripción**: Una lista de reglas de ciclo de vida que definen cómo se gestionan los objetos en el bucket (por ejemplo, archivado, eliminación).

Ejemplo:

```typescript
lifecycleRules: [{
  enabled: true,
  expiration: cdk.Duration.days(30)
}]
```

### 14. **cors**: `s3.CorsRule[]`

- **Descripción**: Configura las reglas de CORS (Compartición de Recursos de Origen Cruzado) para permitir el acceso desde diferentes dominios a los objetos en el bucket.

Ejemplo:

```typescript
cors: [{
  allowedOrigins: ['*'],
  allowedMethods: [s3.HttpMethods.GET]
}]
```

### 15. **eventBridgeEnabled**: `boolean`

- **Descripción**: Habilita o deshabilita la integración de Amazon S3 con Amazon EventBridge.
- **Valor predeterminado**: `false`
- **Uso**: Si se habilita, permite que los eventos de S3 se envíen a EventBridge para la automatización de flujos de trabajo.

Ejemplo:

```typescript
eventBridgeEnabled: true
```

### 16. **notifications**: `s3.Notification[]`

- **Descripción**: Configura notificaciones para eventos de S3, como cargas de objetos o eliminación de objetos.
- **Uso**: Puedes suscribirte a eventos a través de Amazon SNS, Lambda o SQS.

Ejemplo:

```typescript
notifications: [{
  destination: myLambda,
  events: [s3.EventType.OBJECT_CREATED]
}]
```

### 17. **bucketKeyEnabled**: `boolean`

- **Descripción**: Permite el uso de una clave de bucket para cifrado.
- **Valor predeterminado**: `false`
- **Uso**: Si se habilita, S3 usará una clave de bucket para cifrar los objetos almacenados.

Ejemplo:

```typescript
bucketKeyEnabled: true
```

### 18. **analyticsConfigurations**: `s3.AnalyticsConfiguration[]`

- **Descripción**: Configura análisis de uso y almacenamiento en el bucket, como las métricas sobre el uso de almacenamiento por tipo de archivo.

Ejemplo:

```typescript
analyticsConfigurations: [{
  id: 'analytics-rule',
  prefix: 'images/',
  storageClassAnalysis: {
    dataExport: {
      outputSchemaVersion: 'V_1',
      destination: {
        bucket: analyticsBucket,
        format: s3.AnalyticsDataExportFormat.CSV
      }
    }
  }
}]
```
