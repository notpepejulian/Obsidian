En este documento hablaremos más en detalle acerca de los conceptos de **Terraform**, más concretamente en su **sintaxis**. La forma en la que se establece el código y como se aplica en proyectos reales. Recuerda que puedes visitar el anterior documento acerca de [[Terraform Concepts|Conceptos Terraform]].

## 1.  Bloques en Terraform

Terraform usa **bloques** para definir su configuración. Un bloque es una sección de código que empieza con una palabra clave y contiene un conjunto de configuraciones dentro de `{ }`.

Ejemplo de un bloque en Terraform:

```hcl
resource "aws_instance" "mi_servidor" {
  ami           = "ami-12345678"
  instance_type = "t2.micro"
}
```

Aquí, `resource` es el **tipo de bloque**, `"aws_instance"` es el **tipo de recurso**, `"mi_servidor"` es el **nombre del recurso**, y las líneas dentro de `{ }` son sus configuraciones.

---

## 2. Recursos en Terraform

Un **recurso** en Terraform representa un elemento de infraestructura que se quiere crear, como una máquina virtual, una base de datos o una red.

### Sintaxis de un recurso

```hcl
resource "<tipo_de_recurso>" "<nombre_local>" {
  # Configuración del recurso
}
```

### Explicación

- `resource`: Define que estamos creando un recurso.
    
- `<tipo_de_recurso>`: Especifica el proveedor y el tipo de recurso (por ejemplo, `aws_instance` para una instancia EC2 en AWS).
    
- `<nombre_local>`: Un identificador único dentro de Terraform para referenciar el recurso en el código.
    
- `{ ... }`: Contiene las configuraciones del recurso, como el tamaño de la máquina, la imagen base (AMI), etiquetas, etc.
    
> Para más información acerca de los recursos y los tipos de recursos que hay, haz click en el enlace al documento de [[Recursos en Terraform|recursos en Terraform]], donde encontrarás información más detallada sobre su sintaxis. 
	
### Ejemplo con un servidor en AWS

```hcl
resource "aws_instance" "ejemplo" {
  ami           = "ami-12345678"  # ID de la imagen base (AMI)
  instance_type = "t2.micro"      # Tipo de instancia
}
```

📌 **Nota:** El `ami` y `instance_type` son **atributos** del recurso.

---

## 3. Variables en Terraform

Las **variables** permiten definir valores reutilizables y configurables. En vez de escribir valores fijos en los recursos, podemos referenciarlos usando variables.

### Tipos de Variables

Terraform admite tres tipos de variables principales:

1. **Cadena (`string`)**
    
2. **Número (`number`)**
    
3. **Booleano (`bool`)**
    

### Declaración de una variable

```hcl
variable "nombre_de_variable" {
  type        = string
  description = "Descripción opcional"
  default     = "valor por defecto"
}
```

Ejemplo con una variable para la AMI:

```hcl
variable "ami_id" {
  type        = string
  description = "AMI de la instancia EC2"
  default     = "ami-12345678"
}
```

### Uso de una variable en un recurso

```hcl
resource "aws_instance" "ejemplo" {
  ami           = var.ami_id
  instance_type = "t2.micro"
}
```

Aquí, `var.ami_id` toma el valor de la variable definida anteriormente.

---

## 4. Tags (Etiquetas) en Terraform

Las **etiquetas (`tags`)** son metadatos que se pueden asignar a los recursos para organizarlos mejor en la nube. Se definen como un mapa clave-valor dentro del recurso.

### Ejemplo de tags en una instancia EC2

```hcl
resource "aws_instance" "ejemplo" {
  ami           = "ami-12345678"
  instance_type = "t2.micro"

  tags = {
    Name        = "Servidor-Web"
    Environment = "Producción"
    Owner       = "Nacho"
  }
}
```

📌 **Explicación**

- `"Name"` → Define un nombre amigable para la instancia.
    
- `"Environment"` → Indica si es desarrollo, prueba o producción.
    
- `"Owner"` → Especifica quién es el responsable del recurso.
    

Las etiquetas ayudan en la administración y facturación de recursos en la nube.

### 5. Módulos en Terraform

Un **módulo** en Terraform es un conjunto de archivos que encapsulan una parte de la infraestructura para reutilizarla fácilmente. Los módulos permiten organizar el código y reducir la repetición al definir recursos en una estructura reutilizable.

#### Estructura de un módulo

Un módulo en Terraform suele contener los siguientes archivos:

```
📁 mi_modulo/
 ├── main.tf       # Definición principal del módulo
 ├── variables.tf  # Variables que puede recibir el módulo
 ├── outputs.tf    # Valores que el módulo puede devolver
```

#### Ejemplo de un módulo para una instancia EC2

Archivo `main.tf` dentro del módulo:

```hcl
resource "aws_instance" "servidor" {
  ami           = var.ami_id
  instance_type = var.instance_type

  tags = {
    Name = var.nombre
  }
}
```

Archivo `variables.tf` dentro del módulo:

```hcl
variable "ami_id" {
  type = string
}

variable "instance_type" {
  type = string
}

variable "nombre" {
  type = string
}
```

Archivo `outputs.tf` dentro del módulo:

```hcl
output "public_ip" {
  value = aws_instance.servidor.public_ip
}
```

#### Uso del módulo en otro archivo Terraform

```hcl
module "mi_servidor" {
  source         = "./mi_modulo"
  ami_id        = "ami-12345678"
  instance_type = "t2.micro"
  nombre        = "Servidor Web"
}
```

Aquí, `source` indica la ruta del módulo. Terraform buscará el módulo en la carpeta `./mi_modulo` y usará las variables definidas en él.

Los módulos pueden provenir de diferentes fuentes:

- Carpetas locales (`./mi_modulo`)
    
- Repositorios Git
    
- Registros públicos como el [Terraform Registry](https://registry.terraform.io/)
    

---

### 6. Estado en Terraform

Terraform mantiene un **estado** de la infraestructura en un archivo llamado `terraform.tfstate`. Este archivo guarda información sobre los recursos creados y permite a Terraform detectar cambios.

#### ¿Cómo funciona el estado?

Cuando ejecutas `terraform apply`, Terraform:

1. Lee el estado actual (`terraform.tfstate`).
    
2. Compara el estado con el código en los archivos `.tf`.
    
3. Determina qué recursos deben crearse, actualizarse o eliminarse.
    
4. Aplica los cambios necesarios.
    
#### Ubicación del archivo de estado

Por defecto, Terraform almacena el estado localmente en `terraform.tfstate`, pero en equipos colaborativos es mejor almacenarlo en un backend remoto, como S3.

Ejemplo de configuración de backend en AWS S3:

```hcl
terraform {
  backend "s3" {
    bucket = "mi-bucket-terraform"
    key    = "estado/terraform.tfstate"
    region = "us-east-1"
  }
}
```

Esto permite compartir el estado entre varios usuarios y mantener un historial de cambios.

---

### 7. Outputs en Terraform

Los **outputs** en Terraform permiten extraer información de los recursos creados para usarlos en otros módulos o mostrarlos al usuario.

#### Ejemplo de output con la IP pública de una instancia EC2

```hcl
output "ip_publica" {
  value = aws_instance.servidor.public_ip
}
```

Después de ejecutar `terraform apply`, se mostrará:

```
Outputs:

ip_publica = "34.201.32.45"
```

Los outputs pueden usarse en otros módulos o scripts automatizados.

---

### 8. Provisioners en Terraform

Los **provisioners** permiten ejecutar comandos en un recurso después de su creación. Se usan para configurar servidores con scripts o software adicional.

#### Ejemplo de provisioner con un script en una instancia EC2

```hcl
resource "aws_instance" "ejemplo" {
  ami           = "ami-12345678"
  instance_type = "t2.micro"

  provisioner "remote-exec" {
    inline = [
      "sudo apt update",
      "sudo apt install -y nginx"
    ]
  }
}
```

Aquí, después de crear la instancia, se ejecuta `apt update` e instala `nginx`.

Existen diferentes tipos de provisioners:

- `local-exec`: Ejecuta comandos en la máquina que ejecuta Terraform.
    
- `remote-exec`: Ejecuta comandos en la máquina remota creada.
    
### 9. Ciclo de vida de los recursos en Terraform

Cada recurso en Terraform pasa por un ciclo de vida cuando se ejecutan cambios en la infraestructura. Terraform permite controlar este ciclo con la configuración `lifecycle`.

#### Estados en el ciclo de vida

1. **Creación (`create`)**: Cuando un recurso es nuevo y aún no existe en el proveedor.
    
2. **Actualización (`update`)**: Se producen cambios en los atributos del recurso.
    
3. **Destrucción (`destroy`)**: El recurso se elimina cuando ya no es necesario.
    

#### Uso del bloque lifecycle

El bloque `lifecycle` se define dentro de un recurso para controlar su comportamiento.

```hcl
resource "aws_instance" "ejemplo" {
  ami           = "ami-12345678"
  instance_type = "t2.micro"

  lifecycle {
    prevent_destroy = true
    ignore_changes  = [tags]
  }
}
```

##### Explicación

- `prevent_destroy = true`: Evita que el recurso sea eliminado accidentalmente.
    
- `ignore_changes = [tags]`: No aplica cambios si se modifican las etiquetas (`tags`).
    

También existe `create_before_destroy = true`, que primero crea el nuevo recurso antes de eliminar el anterior, útil para minimizar tiempos de inactividad.

---

### 10. Providers en Terraform

Un **provider** en Terraform es el encargado de interactuar con la API de un servicio en la nube o de infraestructura. Es necesario especificar qué provider se usará en el código.

#### Ejemplo de configuración de provider para AWS

```hcl
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

provider "aws" {
  region = "us-east-1"
}
```

##### Explicación

- `required_providers` define los proveedores necesarios y su versión.
    
- `provider "aws"` configura el acceso a AWS con una región específica.
    

Terraform soporta múltiples providers, como:

- `azurerm` (Azure)
    
- `google` (Google Cloud)
    
- `kubernetes` (Kubernetes)
    
- `docker` (Docker)
    

---

### 11. Workspaces en Terraform

Los **workspaces** permiten gestionar múltiples entornos (como desarrollo, prueba y producción) dentro de un mismo código Terraform.

#### Comandos básicos de workspaces

- `terraform workspace list` → Lista los workspaces disponibles.
    
- `terraform workspace new desarrollo` → Crea un workspace llamado `desarrollo`.
    
- `terraform workspace select desarrollo` → Cambia al workspace `desarrollo`.
    

#### Uso de workspaces en el código

Terraform proporciona la variable `terraform.workspace` para usarla en los recursos.

```hcl
resource "aws_s3_bucket" "mi_bucket" {
  bucket = "mi-bucket-${terraform.workspace}"
}
```

Si estamos en el workspace `produccion`, el bucket se llamará `mi-bucket-produccion`.

Los workspaces son útiles para separar ambientes sin duplicar código.