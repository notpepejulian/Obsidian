HCL (*HashiCorp Configuration Language*) es un lenguaje de configuración desarrollado por HashiCorp, diseñado para ser tanto **legible para humanos** como **procesable por máquinas**. Es ampliamente utilizado en herramientas como Terraform para definir infraestructuras como código (*Infrastructure as Code - IaC*).  

Terraform es una herramienta de HashiCorp que permite la **provisión, gestión y automatización de infraestructura** en múltiples proveedores de servicios en la nube (AWS, Azure, GCP, etc.), utilizando un enfoque declarativo con código escrito en HCL.

---

## Fundamentos de HCL en Terraform  

Terraform utiliza HCL para definir **infraestructura como código**, permitiendo que los usuarios especifiquen recursos y configuraciones de forma estructurada.  

### 1. Bloques en HCL  
La sintaxis de Terraform en HCL se basa en **bloques**, que tienen esta estructura general:  

```hcl
bloque "tipo" "nombre" {
  clave = valor
}
````

Ejemplo:

```hcl
resource "aws_instance" "ejemplo" {
  ami           = "ami-12345678"
  instance_type = "t2.micro"
}
```

Aquí se define una instancia EC2 en AWS con una imagen específica (`ami`) y un tipo de instancia (`t2.micro`).

---

### 2. Tipos de Bloques Principales en Terraform

#### a) Proveedor (`provider`)

Especifica el servicio en la nube donde se desplegará la infraestructura.  
Ejemplo con AWS:

```hcl
provider "aws" {
  region = "us-east-1"
}
```

Esto indica que se usará AWS en la región `us-east-1`.

---

#### b) Recursos (`resource`)

Define elementos a aprovisionar, como instancias de servidores, bases de datos, redes, etc.  
Ejemplo con una instancia de AWS EC2:

```hcl
resource "aws_instance" "mi_servidor" {
  ami           = "ami-0abcdef1234567890"
  instance_type = "t2.micro"
}
```

---

#### c) Variables (`variable`)

Permite definir valores reutilizables dentro del código.  
Ejemplo:

```hcl
variable "region" {
  default = "us-east-1"
}
```

Luego, se puede usar la variable en el código:

```hcl
provider "aws" {
  region = var.region
}
```

---

#### d) Salidas (`output`)

Sirve para mostrar información útil después de aplicar la infraestructura.  
Ejemplo:

```hcl
output "ip_publica" {
  value = aws_instance.mi_servidor.public_ip
}
```

Esto imprimirá la dirección IP pública de la instancia creada.

---

### 3. Estado y Ciclo de Vida de Terraform

Terraform tiene un flujo de trabajo basado en tres comandos principales:

1. **`terraform init`** → Inicializa el directorio de trabajo y descarga proveedores.
    
2. **`terraform plan`** → Muestra los cambios que se aplicarán.
    
3. **`terraform apply`** → Crea la infraestructura definida en el código.
    

Para eliminar recursos creados:

- **`terraform destroy`** → Borra todos los recursos definidos en la configuración.


## Desplegar Proyectos con Terraform

Una vez entendidos estos conceptos, podemos continuar con otras opciones para seguir entendiendo el funcionamiento de esta tecnología. Por aquí dejo un enlace local a *como desplegar proyectos con Terraform*: 

- ✅ Continuar a [[Despliegue de Proyectos con Terraform]].