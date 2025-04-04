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

> NOTA: También hay herramientas para poder asumir el rol de manera eficiente como en:
> [Awsu.me](https://awsu.me/)

### Script en Bash para asumir rol

``` bash
#!/bin/bash

# ────────────────────────────────────────────────────────────────────────────────
# 🌐 AWS Role Assumption Tool
# Nacho @ Neoris — Estilo limpio
# ────────────────────────────────────────────────────────────────────────────────

# Colores
VERDE='\033[0;32m'
ROJO='\033[0;31m'
AMARILLO='\033[1;33m'
AZUL='\033[1;34m'
NC='\033[0m' # Reset color

# ────────────────────────────────────────────────────────────────────────────────
# Mostrar credenciales actuales
echo -e "\n${AZUL}🔐 Credenciales actuales (si existen):${NC}"
[[ -n "$AWS_ACCESS_KEY_ID" ]]       && echo -e "  ${VERDE}AWS_ACCESS_KEY_ID=${AWS_ACCESS_KEY_ID}${NC}"
[[ -n "$AWS_SECRET_ACCESS_KEY" ]]   && echo -e "  ${VERDE}AWS_SECRET_ACCESS_KEY=${AWS_SECRET_ACCESS_KEY}${NC}"
[[ -n "$AWS_SESSION_TOKEN" ]]       && echo -e "  ${VERDE}AWS_SESSION_TOKEN=${AWS_SESSION_TOKEN}${NC}"
echo ""

# ────────────────────────────────────────────────────────────────────────────────
#Configurar credenciales
read -p "$(echo -e "${AZUL}❓ ¿Quieres reconfigurar las credenciales de AWS? (s/n): ${NC}")" reconfigurar
if [[ "$reconfigurar" =~ ^[sS]$ ]]; then
    aws configure
    echo -e "${VERDE}✔️  Reconfiguración completada.${NC}"
    echo ""
fi

# ────────────────────────────────────────────────────────────────────────────────
# Eliminar variables previas
read -p "$(echo -e "${AZUL}❓ ¿Quieres eliminar las variables de entorno actuales de AWS para el rol? (s/n): ${NC}")" eliminar_vars
if [[ "$eliminar_vars" =~ ^[sS]$ ]]; then
    unset AWS_ACCESS_KEY_ID
    unset AWS_SECRET_ACCESS_KEY
    unset AWS_SECRET_KEY
    unset AWS_SESSION_TOKEN
    echo -e "${AMARILLO}⚠️  Variables de entorno eliminadas.${NC}"
    echo ""
fi

# ────────────────────────────────────────────────────────────────────────────────
# Asumir nuevo rol
read -p "$(echo -e "${AZUL}❓ ¿Quieres realizar una nueva conexión con assume-role? (s/n): ${NC}")" nueva_conexion
if [[ "$nueva_conexion" =~ ^[sS]$ ]]; then
    echo -ne "${AZUL}🔢 Introduce el código MFA (token): ${NC}"
    read token

    echo -e "${AZUL}⏳ Asumiendo rol...${NC}"

    CRED_JSON=$(aws sts assume-role \
        --role-arn "arn:aws:iam::481186298209:role/AssumeRoleNeorisTrainers" \
        --role-session-name "MiSesionNacho" \
        --serial-number "arn:aws:iam::931556474233:mfa/NachoNeoris" \
        --token-code "$token")

    if [ $? -ne 0 ]; then
        echo -e "\n${ROJO}❌ Error al asumir el rol. Verifica el token o configuración.${NC}"
        exit 1
    fi

    ACCESS_KEY_ID=$(echo "$CRED_JSON" | jq -r '.Credentials.AccessKeyId')
    SECRET_ACCESS_KEY=$(echo "$CRED_JSON" | jq -r '.Credentials.SecretAccessKey')
    SESSION_TOKEN=$(echo "$CRED_JSON" | jq -r '.Credentials.SessionToken')

    echo -e "\n${VERDE}✅ Nueva sesión asumida exitosamente.${NC}"
    echo -e "${AMARILLO}🔑 Credenciales generadas:${NC}"
    echo -e "  AWS_ACCESS_KEY_ID=${ACCESS_KEY_ID}"
    echo -e "  AWS_SECRET_ACCESS_KEY=${SECRET_ACCESS_KEY}"
    echo -e "  AWS_SESSION_TOKEN=${SESSION_TOKEN}"

    echo -e "────────────────────────────────────────────────────────────────────────────────"

    echo -e "\n${AZUL}📦 Usa los siguientes comandos para exportar en tu shell actual:${NC}"
    echo -e "  export AWS_ACCESS_KEY_ID=${ACCESS_KEY_ID}"
    echo -e "  export AWS_SECRET_ACCESS_KEY=${SECRET_ACCESS_KEY}"
    echo -e "  export AWS_SESSION_TOKEN=${SESSION_TOKEN}"
else
    echo -e "${AMARILLO}⚠️  Se mantienen las credenciales actuales.${NC}"
fi

echo -e "\n${AZUL}🚀 Listo.${NC}\n"
```

---
## ¿Más Acerca de Terraform , HCL o CDK?
Con esto, ya se puede empezar a desarrollar una infraestructura de **AWS**, **Google**, **Azure**... usando herramientas como [[Terraform Concepts|Terraform con HCL]] o [[CDK Sintax Recursos|CDK]]
