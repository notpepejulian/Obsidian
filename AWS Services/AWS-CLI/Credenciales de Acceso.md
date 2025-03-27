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

### Configurar las credenciales en Terraform

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


## ¿Más Acerca de Terraform y HCL?
Con esto, ya se puede empezar a desarrollar una infraestructura de **AWS**, **Google**, **Azure**... usando herramientas como [[HCL Concepts|Terraform con HCL]]. 