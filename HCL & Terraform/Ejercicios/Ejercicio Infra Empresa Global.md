A continuación muestro un ejemplo de código Terraform que implementa una infraestructura básica en AWS para una empresa con sede en California y oficinas en España e Irlanda. Este ejemplo crea:

- Tres proveedores (AWS) para cada región: California (us‑west‑2), España (simulado en eu‑south‑1) e Irlanda (eu‑west‑1).
    
- Un VPC (con una subred pública) en cada región.
    
- Un Transit Gateway en California (como hub central) con attachments a cada VPC para lograr conectividad segura entre ellas.
    
- Un RDS MySQL primario en California con backup nocturno configurado y dos réplicas de lectura en España e Irlanda (esto permite mantener una base de datos centralizada y replicar la información localmente para baja latencia).
    
- Ejemplos de Auto Scaling Groups (ASG) con Launch Configurations para servidores web en cada región, junto con los grupos de seguridad necesarios.
    
- Balanceadores de carga (ALB) para distribuir el tráfico web.
    
- Un bucket S3 para almacenar de forma segura los datos (por ejemplo, archivos de las páginas web o backups adicionales).
    

> **Nota:** Este ejemplo utiliza valores “placeholder” (como AMI IDs, contraseñas, etc.) que se deben reemplazar por los valores reales de tu entorno. Además, es posible que sea necesario ajustar detalles según requerimientos específicos (por ejemplo, engine de base de datos, instancias, zonas de disponibilidad, etc.). 

---
### Enunciado

```
Una empresa de EEUU que tiene sede en california y ofrece servicios a nivel internacional busca expandirse y ha decidio instalar oficinas en España e Irlanda. 

La empresa necesita tener una infraestructura solida que comunique de manera segura sus datos de EEUU con los de España e Irlanda sin perder latencia ni disponibilidad. En cuanto a las tecnologías que se necesitan, la empresa solicita que las bases de datos tengan un backup nocturno y que los datos de sus paginas web sean tambien almacenados de manera segura y eficiente. 

Cuentan con servidores web, bases de datos, balanceadores de carga, vpn.. Tu objetivo es diseñar y montar la infraestructura cloud (aws) que mejor se ajuste para la empresa.
```


---

### Archivo: `providers.tf`

```hcl
provider "aws" {
  alias  = "california"
  region = "us-west-2"
}

provider "aws" {
  alias  = "spain"
  region = "eu-south-1"   # Se asume esta región para la oficina en España
}

provider "aws" {
  alias  = "ireland"
  region = "eu-west-1"
}
```

---

### Archivo: `vpc.tf`

```hcl
# VPC y subred en California
resource "aws_vpc" "california_vpc" {
  provider   = aws.california
  cidr_block = "10.0.0.0/16"
  tags = { Name = "California-VPC" }
}

resource "aws_subnet" "california_public_subnet" {
  provider            = aws.california
  vpc_id              = aws_vpc.california_vpc.id
  cidr_block          = "10.0.1.0/24"
  availability_zone   = "us-west-2a"
  tags = { Name = "California-Public-Subnet" }
}

# VPC y subred en España
resource "aws_vpc" "spain_vpc" {
  provider   = aws.spain
  cidr_block = "10.1.0.0/16"
  tags = { Name = "Spain-VPC" }
}

resource "aws_subnet" "spain_public_subnet" {
  provider            = aws.spain
  vpc_id              = aws_vpc.spain_vpc.id
  cidr_block          = "10.1.1.0/24"
  availability_zone   = "eu-south-1a"
  tags = { Name = "Spain-Public-Subnet" }
}

# VPC y subred en Irlanda
resource "aws_vpc" "ireland_vpc" {
  provider   = aws.ireland
  cidr_block = "10.2.0.0/16"
  tags = { Name = "Ireland-VPC" }
}

resource "aws_subnet" "ireland_public_subnet" {
  provider            = aws.ireland
  vpc_id              = aws_vpc.ireland_vpc.id
  cidr_block          = "10.2.1.0/24"
  availability_zone   = "eu-west-1a"
  tags = { Name = "Ireland-Public-Subnet" }
}
```

---

### Archivo: `transit_gateway.tf`

```hcl
# Creación de un Transit Gateway en California (hub central)
resource "aws_ec2_transit_gateway" "tgw" {
  provider    = aws.california
  description = "Transit Gateway for global connectivity"
  tags = { Name = "Global-TGW" }
}

# Attachment para California
resource "aws_ec2_transit_gateway_vpc_attachment" "california_attachment" {
  provider           = aws.california
  transit_gateway_id = aws_ec2_transit_gateway.tgw.id
  vpc_id             = aws_vpc.california_vpc.id
  subnet_ids         = [aws_subnet.california_public_subnet.id]
  tags = { Name = "California-TGW-Attachment" }
}

# Attachment para España
resource "aws_ec2_transit_gateway_vpc_attachment" "spain_attachment" {
  provider           = aws.spain
  transit_gateway_id = aws_ec2_transit_gateway.tgw.id
  vpc_id             = aws_vpc.spain_vpc.id
  subnet_ids         = [aws_subnet.spain_public_subnet.id]
  tags = { Name = "Spain-TGW-Attachment" }
}

# Attachment para Irlanda
resource "aws_ec2_transit_gateway_vpc_attachment" "ireland_attachment" {
  provider           = aws.ireland
  transit_gateway_id = aws_ec2_transit_gateway.tgw.id
  vpc_id             = aws_vpc.ireland_vpc.id
  subnet_ids         = [aws_subnet.ireland_public_subnet.id]
  tags = { Name = "Ireland-TGW-Attachment" }
}
```

---

### Archivo: `rds.tf`

```hcl
# Grupo de seguridad para la base de datos en California
resource "aws_security_group" "db_sg" {
  provider    = aws.california
  name        = "db-security-group"
  description = "Allow MySQL access"
  vpc_id      = aws_vpc.california_vpc.id

  ingress {
    from_port   = 3306
    to_port     = 3306
    protocol    = "tcp"
    # Se permite acceso desde los rangos de las 3 VPCs
    cidr_blocks = ["10.0.0.0/16", "10.1.0.0/16", "10.2.0.0/16"]
  }
  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
  tags = { Name = "DB-SG" }
}

# Subnet group para la base de datos en California
resource "aws_db_subnet_group" "db_subnet_group" {
  provider   = aws.california
  name       = "california-db-subnet-group"
  subnet_ids = [aws_subnet.california_public_subnet.id]
  tags = { Name = "California-DB-Subnet-Group" }
}

# Base de datos primaria en California
resource "aws_db_instance" "primary_db" {
  provider                 = aws.california
  allocated_storage        = 20
  engine                   = "mysql"
  engine_version           = "8.0"
  instance_class           = "db.t3.micro"
  name                     = "primarydb"
  username                 = "admin"
  password                 = "changeme123"    # Reemplazar por un valor seguro
  parameter_group_name     = "default.mysql8.0"
  backup_retention_period  = 7                # Backup de 7 días
  backup_window            = "02:00-03:00"    # Backup nocturno
  skip_final_snapshot      = true
  vpc_security_group_ids   = [aws_security_group.db_sg.id]
  db_subnet_group_name     = aws_db_subnet_group.db_subnet_group.id
  multi_az                 = false
  publicly_accessible      = false
  tags = { Name = "Primary-DB-California" }
}

# --- Réplicas de lectura ---

# Para España:
resource "aws_db_subnet_group" "spain_db_subnet_group" {
  provider   = aws.spain
  name       = "spain-db-subnet-group"
  subnet_ids = [aws_subnet.spain_public_subnet.id]
  tags = { Name = "Spain-DB-Subnet-Group" }
}

resource "aws_security_group" "db_sg_spain" {
  provider    = aws.spain
  name        = "db-security-group-spain"
  description = "Allow MySQL access"
  vpc_id      = aws_vpc.spain_vpc.id

  ingress {
    from_port   = 3306
    to_port     = 3306
    protocol    = "tcp"
    cidr_blocks = ["10.0.0.0/16", "10.1.0.0/16", "10.2.0.0/16"]
  }
  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
  tags = { Name = "DB-SG-Spain" }
}

resource "aws_db_instance" "spain_read_replica" {
  provider              = aws.spain
  replicate_source_db   = aws_db_instance.primary_db.id
  instance_class        = "db.t3.micro"
  engine                = aws_db_instance.primary_db.engine
  engine_version        = aws_db_instance.primary_db.engine_version
  db_subnet_group_name  = aws_db_subnet_group.spain_db_subnet_group.id
  vpc_security_group_ids = [aws_security_group.db_sg_spain.id]
  tags = { Name = "ReadReplica-Spain" }
}

# Para Irlanda:
resource "aws_db_subnet_group" "ireland_db_subnet_group" {
  provider   = aws.ireland
  name       = "ireland-db-subnet-group"
  subnet_ids = [aws_subnet.ireland_public_subnet.id]
  tags = { Name = "Ireland-DB-Subnet-Group" }
}

resource "aws_security_group" "db_sg_ireland" {
  provider    = aws.ireland
  name        = "db-security-group-ireland"
  description = "Allow MySQL access"
  vpc_id      = aws_vpc.ireland_vpc.id

  ingress {
    from_port   = 3306
    to_port     = 3306
    protocol    = "tcp"
    cidr_blocks = ["10.0.0.0/16", "10.1.0.0/16", "10.2.0.0/16"]
  }
  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
  tags = { Name = "DB-SG-Ireland" }
}

resource "aws_db_instance" "ireland_read_replica" {
  provider              = aws.ireland
  replicate_source_db   = aws_db_instance.primary_db.id
  instance_class        = "db.t3.micro"
  engine                = aws_db_instance.primary_db.engine
  engine_version        = aws_db_instance.primary_db.engine_version
  db_subnet_group_name  = aws_db_subnet_group.ireland_db_subnet_group.id
  vpc_security_group_ids = [aws_security_group.db_sg_ireland.id]
  tags = { Name = "ReadReplica-Ireland" }
}
```

---

### Archivo: `ec2.tf`

```hcl
# --- Servidores Web en California ---

# Grupo de seguridad para servidores web en California
resource "aws_security_group" "web_sg_california" {
  provider    = aws.california
  name        = "web-security-group-california"
  description = "Permite tráfico HTTP y HTTPS"
  vpc_id      = aws_vpc.california_vpc.id

  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
  ingress {
    from_port   = 443
    to_port     = 443
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
  tags = { Name = "Web-SG-California" }
}

# Launch configuration y ASG para los web servers en California
resource "aws_launch_configuration" "web_lc_california" {
  provider         = aws.california
  name_prefix      = "web-california-"
  image_id         = "ami-12345678"  # Reemplazar por AMI real
  instance_type    = "t3.micro"
  security_groups  = [aws_security_group.web_sg_california.id]
  lifecycle { create_before_destroy = true }
}

resource "aws_autoscaling_group" "web_asg_california" {
  provider              = aws.california
  name                  = "web-asg-california"
  launch_configuration  = aws_launch_configuration.web_lc_california.name
  min_size              = 2
  max_size              = 4
  desired_capacity      = 2
  vpc_zone_identifier   = [aws_subnet.california_public_subnet.id]

  tag {
    key                 = "Name"
    value               = "WebServer-California"
    propagate_at_launch = true
  }
}

# --- Servidores Web en España ---

resource "aws_security_group" "web_sg_spain" {
  provider    = aws.spain
  name        = "web-security-group-spain"
  description = "Permite tráfico HTTP y HTTPS"
  vpc_id      = aws_vpc.spain_vpc.id

  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
  ingress {
    from_port   = 443
    to_port     = 443
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
  tags = { Name = "Web-SG-Spain" }
}

resource "aws_launch_configuration" "web_lc_spain" {
  provider         = aws.spain
  name_prefix      = "web-spain-"
  image_id         = "ami-87654321"  # Reemplazar por AMI real
  instance_type    = "t3.micro"
  security_groups  = [aws_security_group.web_sg_spain.id]
  lifecycle { create_before_destroy = true }
}

resource "aws_autoscaling_group" "web_asg_spain" {
  provider              = aws.spain
  name                  = "web-asg-spain"
  launch_configuration  = aws_launch_configuration.web_lc_spain.name
  min_size              = 2
  max_size              = 4
  desired_capacity      = 2
  vpc_zone_identifier   = [aws_subnet.spain_public_subnet.id]

  tag {
    key                 = "Name"
    value               = "WebServer-Spain"
    propagate_at_launch = true
  }
}

# --- Servidores Web en Irlanda ---

resource "aws_security_group" "web_sg_ireland" {
  provider    = aws.ireland
  name        = "web-security-group-ireland"
  description = "Permite tráfico HTTP y HTTPS"
  vpc_id      = aws_vpc.ireland_vpc.id

  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
  ingress {
    from_port   = 443
    to_port     = 443
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
  tags = { Name = "Web-SG-Ireland" }
}

resource "aws_launch_configuration" "web_lc_ireland" {
  provider         = aws.ireland
  name_prefix      = "web-ireland-"
  image_id         = "ami-abcdef12"  # Reemplazar por AMI real
  instance_type    = "t3.micro"
  security_groups  = [aws_security_group.web_sg_ireland.id]
  lifecycle { create_before_destroy = true }
}

resource "aws_autoscaling_group" "web_asg_ireland" {
  provider              = aws.ireland
  name                  = "web-asg-ireland"
  launch_configuration  = aws_launch_configuration.web_lc_ireland.name
  min_size              = 2
  max_size              = 4
  desired_capacity      = 2
  vpc_zone_identifier   = [aws_subnet.ireland_public_subnet.id]

  tag {
    key                 = "Name"
    value               = "WebServer-Ireland"
    propagate_at_launch = true
  }
}
```

---

### Archivo: `alb.tf`

```hcl
# --- ALB en California ---
resource "aws_lb" "alb_california" {
  provider            = aws.california
  name                = "alb-california"
  internal            = false
  load_balancer_type  = "application"
  security_groups     = [aws_security_group.web_sg_california.id]
  subnets             = [aws_subnet.california_public_subnet.id]
  tags = { Name = "ALB-California" }
}

resource "aws_lb_listener" "alb_listener_california" {
  provider           = aws.california
  load_balancer_arn  = aws_lb.alb_california.arn
  port               = "80"
  protocol           = "HTTP"
  default_action {
    type             = "forward"
    target_group_arn = aws_lb_target_group.alb_target_group_california.arn
  }
}

resource "aws_lb_target_group" "alb_target_group_california" {
  provider    = aws.california
  name        = "tg-california"
  port        = 80
  protocol    = "HTTP"
  vpc_id      = aws_vpc.california_vpc.id
  health_check {
    healthy_threshold   = 2
    unhealthy_threshold = 2
    timeout             = 5
    interval            = 30
    protocol            = "HTTP"
    path                = "/"
  }
  tags = { Name = "TG-California" }
}

# --- ALB en España ---
resource "aws_lb" "alb_spain" {
  provider            = aws.spain
  name                = "alb-spain"
  internal            = false
  load_balancer_type  = "application"
  security_groups     = [aws_security_group.web_sg_spain.id]
  subnets             = [aws_subnet.spain_public_subnet.id]
  tags = { Name = "ALB-Spain" }
}

resource "aws_lb_listener" "alb_listener_spain" {
  provider           = aws.spain
  load_balancer_arn  = aws_lb.alb_spain.arn
  port               = "80"
  protocol           = "HTTP"
  default_action {
    type             = "forward"
    target_group_arn = aws_lb_target_group.alb_target_group_spain.arn
  }
}

resource "aws_lb_target_group" "alb_target_group_spain" {
  provider    = aws.spain
  name        = "tg-spain"
  port        = 80
  protocol    = "HTTP"
  vpc_id      = aws_vpc.spain_vpc.id
  health_check {
    healthy_threshold   = 2
    unhealthy_threshold = 2
    timeout             = 5
    interval            = 30
    protocol            = "HTTP"
    path                = "/"
  }
  tags = { Name = "TG-Spain" }
}

# --- ALB en Irlanda ---
resource "aws_lb" "alb_ireland" {
  provider            = aws.ireland
  name                = "alb-ireland"
  internal            = false
  load_balancer_type  = "application"
  security_groups     = [aws_security_group.web_sg_ireland.id]
  subnets             = [aws_subnet.ireland_public_subnet.id]
  tags = { Name = "ALB-Ireland" }
}

resource "aws_lb_listener" "alb_listener_ireland" {
  provider           = aws.ireland
  load_balancer_arn  = aws_lb.alb_ireland.arn
  port               = "80"
  protocol           = "HTTP"
  default_action {
    type             = "forward"
    target_group_arn = aws_lb_target_group.alb_target_group_ireland.arn
  }
}

resource "aws_lb_target_group" "alb_target_group_ireland" {
  provider    = aws.ireland
  name        = "tg-ireland"
  port        = 80
  protocol    = "HTTP"
  vpc_id      = aws_vpc.ireland_vpc.id
  health_check {
    healthy_threshold   = 2
    unhealthy_threshold = 2
    timeout             = 5
    interval            = 30
    protocol            = "HTTP"
    path                = "/"
  }
  tags = { Name = "TG-Ireland" }
}
```

---

### Archivo: `s3.tf`

```hcl
# Bucket S3 para almacenar datos de las páginas web / backups
resource "aws_s3_bucket" "web_data_backup" {
  bucket = "web-data-backup-company-unique"  # Debe ser un nombre único global
  acl    = "private"

  versioning {
    enabled = true
  }

  server_side_encryption_configuration {
    rule {
      apply_server_side_encryption_by_default {
        sse_algorithm = "AES256"
      }
    }
  }

  tags = { Name = "Web-Data-Backup" }
}
```

---

Este conjunto de archivos conforma una infraestructura inicial en AWS que:

- Asegura la conectividad entre las regiones mediante un Transit Gateway, lo que posibilita la comunicación segura entre los datos de EEUU, España e Irlanda.
    
- Configura backups nocturnos en la base de datos primaria y réplicas en las otras regiones para mantener la disponibilidad y baja latencia en lecturas.
    
- Despliega servidores web con balanceadores de carga y escalado automático para gestionar el tráfico de las páginas web.
    
- Proporciona un bucket S3 para almacenar datos y respaldos de manera segura.
    