# ⚠️ **IMPORTANTE**

Antes de realizar cualquier parte de este documento es obligatorio configurar las [[Credenciales de Acceso]] de tu cuenta de AWS.

Una vez están tus credenciales configuradas, vamos a crear un proyecto de Terraform desde cero y desplegar una instancia en AWS.

---

##  Paso 1: Crear el directorio del proyecto 

Ejecuta estos comandos en la terminal para organizar tu proyecto:

```bash
mkdir terraform-aws
cd terraform-aws
```

---

##  Paso 2: Crear archivos de configuración de Terraform 

Terraform usa archivos `.tf` para definir la infraestructura. Crearemos tres archivos:

1.  `main.tf`  → Define la instancia EC2 en AWS
    
2.  `variables.tf`  → Contiene variables reutilizables
    
3.  `outputs.tf`  → Muestra información útil después de desplegar la infraestructura
    

>![[Pasted image 20250327162056.png]]
>

###  1. Archivo `main.tf` (infraestructura principal) 

Crea el archivo y edítalo:

```bash
nano main.tf
```

Agrega el siguiente código:

```hcl
provider "aws" {
  region = var.region # La variable se recoge de "variables.tf"
}

resource "aws_instance" "instancia_prueba_tf" {
  ami           = var.ami_id  
  instance_type = var.instance_type

  tags = {
    Name = "ServidorTerraform"
  }
}
```

 Explicación: 

- `provider "aws"`: Configura AWS como proveedor.
    
- `resource "aws_instance"`: Crea una máquina virtual en AWS.
    
- `ami`: Define la AMI (Amazon Machine Image) que usará la instancia.
    
- `instance_type`: Define el tipo de instancia (por ejemplo, `t2.micro`).
    
- `tags`: Etiqueta la instancia con un nombre.
    

---

###  2. Archivo `variables.tf` (variables reutilizables) 

Crea y edita el archivo:

```bash
nano variables.tf
```

Agrega el siguiente código:

```hcl
variable "region" {
  default = "eu-west-1"
}

variable "ami_id" {
  default = "ami-0abcdef1234567890"  # Reemplázala con una AMI válida
}

variable "instance_type" {
  default = "t2.micro"
}
```

 Explicación: 

- `region`: Especifica en qué región se desplegará la infraestructura.
    
- `ami_id`: Define la AMI usada para crear la instancia EC2.
    
- `instance_type`: Permite cambiar el tipo de máquina sin modificar `main.tf`.
    

---

###  3. Archivo `outputs.tf` (para mostrar resultados) 

Crea y edita el archivo:

```bash
nano outputs.tf
```

Agrega:

```hcl
output "ip_publica" {
  value = aws_instance.mi_servidor.public_ip
}

output "id_instancia" {
  value = aws_instance.instancia_prueba_tf.id
}
```

 Explicación: 

- `output "ip_publica"`: Muestra la dirección IP pública de la instancia.
    
- `output "id_instancia"`: Muestra el ID de la instancia creada.
    

---

##  Paso 3: Ejecutar Terraform paso a paso 

1.  Inicializar Terraform 
    
    ```bash
    terraform init
    ```
    
    >![[Pasted image 20250327161951.png]]
    >
    
    - Descarga los plugins necesarios para AWS.
        
    - Prepara el entorno de Terraform.
        
2.  Verificar el plan antes de aplicar cambios 
    
    ```bash
    terraform plan
    ```
    
    - Muestra qué recursos se crearán sin ejecutarlos.
        
    >![[Pasted image 20250327162229.png]]
    >![[Pasted image 20250327162250.png]]
    >
        
3.  Aplicar la configuración y crear la instancia 
    
    ```bash
    terraform apply
    ```
    
    > ![[Pasted image 20250327165709.png]]
    > ![[Pasted image 20250327165719.png]]
    > 
    
    - Terraform pedirá confirmación (`yes`).
        
    - Creará la instancia EC2 en AWS.
        
    >![[Pasted image 20250327165933.png]]
    >
        
	- ⚠️ **ERROR AL CREAR LA INSTANCIA**: [[Errores Terraform|Buscar error]]
        
    >![[Pasted image 20250327164443.png]]
    >
        
4.  Ver la dirección IP de la instancia 
    
    ```bash
    terraform output ip_publica
    ```
    
5.  Conectarse a la instancia (si tiene IP pública) 
    
    ```bash
    ssh -i "tu-clave.pem" ec2-user@IP_PUBLICA
    ```
    
    - Reemplaza `tu-clave.pem` con tu clave SSH válida.
        
    - Reemplaza `IP_PUBLICA` con la IP que te dio Terraform.
        
6.  Destruir la infraestructura cuando termines 
    
    ```bash
    terraform destroy
    ```
    
    - Esto eliminará la instancia y otros recursos creados.
        
    >![[Pasted image 20250327170050.png]]
    >![[Pasted image 20250327170143.png]]
    >![[Pasted image 20250327170203.png]]
    >

---

##  ¿Más? 

Ahora que creaste tu primera infraestructura con Terraform, puedes:  
✅  Agregar más recursos  (VPCs, S3, RDS, etc.).  
✅  Usar variables dinámicas  en `terraform.tfvars`.  
✅  Probar con otros proveedores  (Azure, Google Cloud).  
✅  Aprender sobre Terraform State y Remote Backend .
