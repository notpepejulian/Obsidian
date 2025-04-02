Para obtener tanto **Access Key** como **Secret Key** en AWS y poder realizar una conexion a la cuenta de AWS:
### Obtener las credenciales en AWS

1. **Accede a la consola de AWS**
    - Ve a [AWS Management Console](https://aws.amazon.com/console/)
        
    - Inicia sesión con tu cuenta de AWS
        
2. **Ir a IAM (Identity and Access Management)**
    - En la barra de búsqueda de AWS, escribe "IAM" y selecciona **IAM**
        
    - En el menú lateral, haz clic en **Usuarios**
        
3. **Crear un usuario con permisos de administración (si no tienes uno)**
    - Haz clic en **Agregar usuario**
        
    - Ingresa un nombre para el usuario, por ejemplo: `terraform-user`
        
    - Selecciona **Acceso programático** (para obtener claves de acceso)
        
    - Haz clic en **Siguiente**
        
4. **Asignar permisos al usuario**
    - Selecciona **Adjuntar políticas existentes directamente**
        
    - Busca y selecciona **AdministratorAccess** (para acceso total)
        
    - Haz clic en **Siguiente** y luego en **Crear usuario**
        
5. **Descargar las credenciales**
    - AWS generará una **Access Key ID** y una **Secret Access Key**
        
    - Descarga el archivo `.csv` o copia las claves en un lugar seguro
        
    - ⚠️ **IMPORTANTE:** La **Secret Access Key** solo se muestra una vez.
        

---

### Configurar las credenciales 

En Fedora, usa la terminal para configurar las credenciales con `aws-cli`:

```bash
aws configure
```

Introduce los valores cuando te los pida:

```plaintext
AWS Access Key ID [None]: TU_ACCESS_KEY_ID
AWS Secret Access Key [None]: TU_SECRET_ACCESS_KEY
Default region name [None]: us-east-1   # O la región que uses
Default output format [None]: json
```

Esto guardará las credenciales en `~/.aws/credentials` y Terraform podrá usarlas.

---

### Verificar que las credenciales funcionan

Ejecuta este comando para comprobar que tienes acceso:

```bash
aws sts get-caller-identity
```

---
### Asumir Rol

Para conectarte a tu cuenta de AWS y asumir el rol `NombreDelRol` para crear un bucket en S3, sigue estos pasos:

---

#### 1. Configurar tus credenciales en AWS CLI

Si aún no lo has hecho, configura tus credenciales en AWS CLI con el siguiente comando:

```sh
aws configure
```

Ingresa:

- **Access Key ID**
    
- **Secret Access Key**
    
- **Región por defecto** (por ejemplo, `us-east-1`)
    
- **Formato de salida** (opcional, `json`, `table` o `text`)
    

---

#### 2. Asumir el rol ` NombreDelRol`

Algunos roles requieren autenticación multifactor (MFA). Para asumir el rol con MFA, primero obtén el código MFA y luego úsalo en el siguiente comando:

```sh
aws sts assume-role --role-arn "arn:aws:iam::481186298209:role/AssumeRoleNeorisTrainers" \
--role-session-name "MiSesionNacho" \
--serial-number "arn:aws:iam::931556474233:mfa/NachoNeoris" \
--token-code ""

```

> Reemplaza `123456` con el código generado por tu dispositivo MFA.

- O bien: 

Ejecuta el siguiente comando para obtener credenciales temporales:

```sh
aws sts assume-role --role-arn "arn:aws:iam::AccountNumber:role/NombreDelRol" --role-session-name "MiSesion"
```

##### Esto devolverá una respuesta JSON como:

```json
{
    "Credentials": {
        "AccessKeyId": "ASIAEXAMPLE",
        "SecretAccessKey": "wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY",
        "SessionToken": "FQoGZXIvYXdzEJr...EXAMPLETOKEN...",
        "Expiration": "2024-04-02T15:04:56Z"
    }
}
```

---

#### 3. Exportar las credenciales temporales

Para usar las credenciales en la terminal, ejecuta:

```sh
export AWS_ACCESS_KEY_ID="ASIAEXAMPLE"
export AWS_SECRET_ACCESS_KEY="wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY"
export AWS_SESSION_TOKEN="FQoGZXIvYXdzEJr...EXAMPLETOKEN..."
```

---

#### 4. Verificar que el rol fue asumido

Ejecuta:

```sh
aws sts get-caller-identity
```

Si el rol fue asumido correctamente, verás el ARN del rol en la respuesta.

---
## ¿Más Acerca de Terraform , HCL o CDK?
Con esto, ya se puede empezar a desarrollar una infraestructura de **AWS**, **Google**, **Azure**... usando herramientas como [[Terraform Concepts|Terraform con HCL]] o [[CDK Sintax Recursos|CDK]]
