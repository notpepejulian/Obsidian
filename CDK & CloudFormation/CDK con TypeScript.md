AWS Cloud Development Kit (CDK) es un framework que permite definir infraestructura en AWS usando lenguajes de programación modernos como TypeScript. CDK traduce el código a CloudFormation para desplegar los recursos en AWS.

## Instalación de AWS CDK

Para instalar AWS CDK globalmente, sigue los pasos mencionados en el documento:

>
>[[Configuración de CDK]]
>
## Conceptos Clave de CDK

### Constructos (Constructs)

Son los bloques de construcción en CDK y pueden ser de tres niveles:

1. **L1 (Bajo Nivel):** Basados directamente en CloudFormation.
    
2. **L2 (Alto Nivel):** Abstracciones con configuraciones por defecto.
    
3. **L3 (Patrones):** Combinaciones de recursos listas para usar.
    

Ejemplo de un constructo L2 para una función Lambda:

```typescript
import * as lambda from 'aws-cdk-lib/aws-lambda';

const miLambda = new lambda.Function(this, 'MiFuncionLambda', {
  runtime: lambda.Runtime.NODEJS_18_X,
  handler: 'index.handler',
  code: lambda.Code.fromAsset('lambda'),
});
```

### Contexto y Parámetros

Puedes definir valores dinámicos en `cdk.json`:

```json
{
  "context": {
    "env": "dev"
  }
}
```

Para usarlos en el código:

```typescript
const entorno = this.node.tryGetContext("env");
console.log(`Desplegando en entorno: ${entorno}`);
```


## Sintaxis Recuros CDK
