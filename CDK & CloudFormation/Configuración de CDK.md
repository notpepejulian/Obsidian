Es importante tener en cuenta que **CDK** puede usarse con varios lenguajes de programación como *Python* o *Typescript*. En este caso usamos Typescript como lenguaje enfocado a la creación de la Arquitectura de *AWS*. Por ello, es esencial tener un conocimiento previo de la sintaxis y conceptos de Typescript.

>
>[[Fundamentos Typescript|Visita este documento para ver más sobre Typescript]]
>

## 1. Instalar Dependencias

### Actualizar Fedora

Ejecuta el siguiente comando para actualizar el sistema:

```bash
sudo dnf update -y
```

### Instalar Node.js y NPM

AWS CDK requiere Node.js, instálalo con:

```bash
sudo dnf install nodejs -y
```

Verifica la instalación:

```bash
node -v
npm -v
```

### Instalar AWS CLI

Para interactuar con AWS, instala la CLI:

```bash
sudo dnf install awscli -y
```

Verifica la instalación:

```bash
aws --version
```

## 2. Configurar [[Credenciales de Acceso]]

Para autenticarte en AWS, ejecuta:

```bash
aws configure
```

Se te pedirá ingresar:

1. AWS Access Key ID
    
2. AWS Secret Access Key
    
3. Región por defecto (ejemplo: us-east-1)
    
4. Formato de salida (json, yaml, etc.)
    

## 3. Instalar AWS CDK

Instala AWS CDK globalmente:

```bash
npm install -g aws-cdk
```

Verifica la instalación:

```bash
cdk --version
```

## 4. Crear un Proyecto AWS CDK en TypeScript

Crea una carpeta para el proyecto:

```bash
mkdir mi-proyecto-cdk && cd mi-proyecto-cdk
```

Iniciar un nuevo proyecto CDK en TypeScript:

```bash
cdk init app --language=typescript
```

Estructura del proyecto:

```
proyecto-cdk/
│── bin/                # Punto de entrada del CDK
│── lib/                # Definición de los stacks
│── node_modules/       
│── cdk.json            # Configuración del CDK
│── package.json        # Dependencias de Node.js
│── tsconfig.json       # Configuración de TypeScript
│── .gitignore
```

## 5. Definir Recursos en CDK

Editar `lib/mi-proyecto-cdk-stack.ts` para crear un recurso (bucket S3 en este caso):

```typescript
import * as cdk from 'aws-cdk-lib';
import * as s3 from 'aws-cdk-lib/aws-s3';

export class MiProyectoCdkStack extends cdk.Stack {
  constructor(scope: cdk.App, id: string, props?: cdk.StackProps) {
    super(scope, id, props);

    new s3.Bucket(this, 'MyFirstBucket', {
      bucketName: 'mi-primer-bucket-cdk',
      removalPolicy: cdk.RemovalPolicy.DESTROY, // Elimina el bucket al destruir el stack
    });
  }
}
```

## 6. Desplegar el Stack en AWS

Sintetizar la infraestructura:

```bash
cdk synth
```

Despliegar la infraestructura en AWS:

```bash
cdk deploy
```

## 7. Eliminar Recursos (Opcional)

Para eliminar todo el stack en AWS:

```bash
cdk destroy
```
