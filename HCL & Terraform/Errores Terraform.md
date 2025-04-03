Errores comunes encontrados al usar Terraform, explicaciones y soluciones guiadas y ligadas. En este documento te muestro detalladamente los errores que he tenido al aplicar configuraciones durante el proceso de aplicacion de Terraform

## [[Despliegue de Proyectos con Terraform]]

El error que aparece en la imagen es:

```
Error: creating EC2 Instance: operation error EC2: RunInstances, https response error StatusCode: 400,
api error VPCIdNotSpecified: No default VPC for this user.
GroupName is only supported for EC2-Classic and default VPC.
```

### Causa del error

Este problema ocurre porque:

1. **No tienes una VPC por defecto en tu cuenta de AWS**
2. **No especificaste una VPC y un Subnet en tu código de Terraform**

En AWS, las instancias EC2 necesitan estar dentro de una **VPC (Virtual Private Cloud)**, y algunas cuentas nuevas de AWS **no tienen una VPC por defecto**.

---

### Solución 1: Especificar una VPC y Subnet manualmente en Terraform

Debes modificar tu `main.tf` para asignar la instancia a una **VPC existente y una subred válida**.

#### Paso 1: Obtener una VPC y Subnet válida en AWS

Ejecuta este comando para listar las VPCs disponibles:

```bash
aws ec2 describe-vpcs --query "Vpcs[*].VpcId"
```

>![[Pasted image 20250327165027.png]]
>

Si el resultado está vacío, necesitas crear una nueva VPC manualmente en AWS.

Ejecuta este comando para obtener una **subred válida** dentro de la VPC:

```bash
aws ec2 describe-subnets --query "Subnets[*].SubnetId"
```

>![[Pasted image 20250327165106.png]]
>
#### Paso 2: Modificar `main.tf` para usar la VPC y Subnet

Edita `main.tf` y agrega la **subnet_id** en la configuración de la instancia EC2:

```hcl
provider "aws" {
  region = "eu-west-1"  # Cambia esto si usas otra región
}

resource "aws_instance" "instancia_prueba_tf" {
  ami           = "ami-090252cbe067a9e58"  # AMI válida de Amazon Linux 2
  instance_type = "t2.micro"
  subnet_id     = "subnet-0abcd1234efgh5678"  # Usa una Subnet válida

  tags = {
    Name = "ServidorTerraform"
  }
}
```

---

### Solución 2: Crear una nueva VPC automáticamente en Terraform

Si no tienes una VPC por defecto, puedes hacer que Terraform la cree por ti antes de lanzar la instancia.

Agrega este código antes de definir la instancia EC2 en `main.tf`:

```hcl
resource "aws_vpc" "mi_vpc" {
  cidr_block = "10.0.0.0/16"

  tags = {
    Name = "VPC-Terraform"
  }
}

resource "aws_subnet" "mi_subnet" {
  vpc_id            = aws_vpc.mi_vpc.id
  cidr_block        = "10.0.1.0/24"
  availability_zone = "us-east-1a"

  tags = {
    Name = "Subnet-Terraform"
  }
}
```

Y ahora modifica la instancia EC2 para usar esta nueva Subnet:

```hcl
resource "aws_instance" "instancia_prueba_tf" {
  ami           = "ami-090252cbe067a9e58"
  instance_type = "t2.micro"
  subnet_id     = aws_subnet.mi_subnet.id

  tags = {
    Name = "ServidorTerraform"
  }
}
```

---

### Paso Final: Aplicar los cambios

Después de hacer las modificaciones, ejecuta:

```bash
terraform init
terraform apply
```
