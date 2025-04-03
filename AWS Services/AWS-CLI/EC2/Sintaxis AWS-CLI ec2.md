
AWS CLI ofrece una amplia variedad de comandos para gestionar instancias y recursos de EC2. Aquí enumero los principales grupos de comandos, su función y sintaxis:

---

### 1. Comandos de Instancias EC2

Los comandos de instancias permiten gestionar las máquinas virtuales (instancias) dentro de EC2.

#### Listar instancias (`describe-instances`)

- Función: Muestra información detallada sobre las instancias EC2 en tu cuenta.
    
- Sintaxis:
    
    ```sh
    aws ec2 describe-instances [opciones]
    ```

##### Ejemplo:

```sh
aws ec2 describe-instances --region us-west-2
```

Este comando devuelve todas las instancias en la región `us-west-2`.

#### Iniciar instancias (`start-instances`)

- Función: Inicia una o más instancias EC2 que están detenidas.
    
- Sintaxis:
    
    ```sh
    aws ec2 start-instances --instance-ids <id-instancia> [opciones]
    ```

##### Ejemplo:

```sh
aws ec2 start-instances --instance-ids i-1234567890abcdef0
```

Inicia la instancia con el ID `i-1234567890abcdef0`.

#### Detener instancias (`stop-instances`)

- Función: Detiene una o más instancias EC2.
    
- Sintaxis:
    
    ```sh
    aws ec2 stop-instances --instance-ids <id-instancia> [opciones]
    ```

##### Ejemplo:

```sh
aws ec2 stop-instances --instance-ids i-1234567890abcdef0
```

Detiene la instancia con el ID `i-1234567890abcdef0`.

#### Reiniciar instancias (`reboot-instances`)

- Función: Reinicia una o más instancias EC2.
    
- Sintaxis:
    
    ```sh
    aws ec2 reboot-instances --instance-ids <id-instancia> [opciones]
    ```

##### Ejemplo:

```sh
aws ec2 reboot-instances --instance-ids i-1234567890abcdef0
```

Reinicia la instancia con el ID `i-1234567890abcdef0`.

#### Terminar instancias (`terminate-instances`)

- Función: Elimina una o más instancias EC2 de forma permanente.
    
- Sintaxis:
    
    ```sh
    aws ec2 terminate-instances --instance-ids <id-instancia> [opciones]
    ```

##### Ejemplo:

```sh
aws ec2 terminate-instances --instance-ids i-1234567890abcdef0
```

Termina la instancia con el ID `i-1234567890abcdef0`.

---

### 2. Comandos de Grupos de Seguridad

Los **grupos de seguridad** actúan como firewalls virtuales que controlan el tráfico hacia y desde las instancias EC2.

#### Listar grupos de seguridad (`describe-security-groups`)

- Función: Muestra información sobre los grupos de seguridad.
    
- Sintaxis:
    
    ```sh
    aws ec2 describe-security-groups [opciones]
    ```

##### Ejemplo:

```sh
aws ec2 describe-security-groups --group-ids sg-12345678
```

Muestra detalles sobre el grupo de seguridad con ID `sg-12345678`.

#### Crear un grupo de seguridad (`create-security-group`)

- Función: Crea un nuevo grupo de seguridad.
    
- Sintaxis:
    
    ```sh
    aws ec2 create-security-group --group-name <nombre> --description <descripcion> [opciones]
    ```

##### Ejemplo:

```sh
aws ec2 create-security-group --group-name mi-grupo --description "Grupo de seguridad para EC2"
```

Crea un grupo de seguridad llamado `mi-grupo` con la descripción proporcionada.

---

### 3. Comandos de Volúmenes EBS

Amazon **Elastic Block Store (EBS)** proporciona almacenamiento persistente para las instancias EC2.

#### Listar volúmenes EBS (`describe-volumes`)

- Función: Muestra los volúmenes EBS asociados a las instancias EC2.
    
- Sintaxis:
    
    ```sh
    aws ec2 describe-volumes [opciones]
    ```

##### Ejemplo:

```sh
aws ec2 describe-volumes --volume-ids vol-12345678
```

Muestra detalles sobre el volumen `vol-12345678`.

#### Crear un volumen EBS (`create-volume`)

- Función: Crea un nuevo volumen EBS.
    
- Sintaxis:
    
    ```sh
    aws ec2 create-volume --size <tamaño> --availability-zone <zona-disponibilidad> --volume-type <tipo> [opciones]
    ```

##### Ejemplo:

```sh
aws ec2 create-volume --size 10 --availability-zone us-west-2a --volume-type gp2
```

Crea un volumen EBS de 10 GB en la zona `us-west-2a` con el tipo `gp2` (general purpose SSD).

---

### 4. Comandos de Key Pairs

Las **pares de claves** se usan para acceder a las instancias EC2 de manera segura mediante SSH.

#### Crear un par de claves (`create-key-pair`)

- Función: Crea un par de claves para acceso SSH.
    
- Sintaxis:
    
    ```sh
    aws ec2 create-key-pair --key-name <nombre-clave> [opciones]
    ```

##### Ejemplo:

```sh
aws ec2 create-key-pair --key-name mi-clave
```

Crea un par de claves llamado `mi-clave`.

#### Eliminar un par de claves (`delete-key-pair`)

- Función: Elimina un par de claves existente.
    
- Sintaxis:
    
    ```sh
    aws ec2 delete-key-pair --key-name <nombre-clave>
    ```

##### Ejemplo:

```sh
aws ec2 delete-key-pair --key-name mi-clave
```

Elimina el par de claves `mi-clave`.

---

### 5. Comandos de AMI (Amazon Machine Images)

Las **Amazon Machine Images (AMI)** son plantillas que contienen el sistema operativo y las aplicaciones para lanzar nuevas instancias.

#### Listar AMIs (`describe-images`)

- Función: Muestra las imágenes de máquina de Amazon (AMIs) disponibles.
    
- Sintaxis:
    
    ```sh
    aws ec2 describe-images --owners <id-propietario> [opciones]
    ```

##### Ejemplo:

```sh
aws ec2 describe-images --owners self
```

Muestra todas las AMIs que son propiedad de tu cuenta.

#### Crear una AMI (`create-image`)

- Función: Crea una nueva imagen de una instancia EC2.
    
- Sintaxis:
    
    ```sh
    aws ec2 create-image --instance-id <id-instancia> --name <nombre-imagen> --no-reboot
    ```

##### Ejemplo:

```sh
aws ec2 create-image --instance-id i-1234567890abcdef0 --name "Mi imagen personalizada" --no-reboot
```

Crea una AMI de la instancia `i-1234567890abcdef0` con el nombre "Mi imagen personalizada".

---

### 6. Comandos de Elastic IPs

Una **Elastic IP (EIP)** es una dirección IP pública estática diseñada para la computación dinámica en la nube.

#### Asignar una Elastic IP (`allocate-address`)

- Función: Asigna una nueva Elastic IP a tu cuenta.
    
- Sintaxis:
    
    ```sh
    aws ec2 allocate-address --domain vpc
    ```

##### Ejemplo:

```sh
aws ec2 allocate-address --domain vpc
```

Asigna una nueva Elastic IP a la VPC.

#### Asociar una Elastic IP (`associate-address`)

- Función: Asocia una Elastic IP a una instancia EC2.
    
- Sintaxis:
    
    ```sh
    aws ec2 associate-address --instance-id <id-instancia> --allocation-id <id-elastic-ip>
    ```

##### Ejemplo:

```sh
aws ec2 associate-address --instance-id i-1234567890abcdef0 --allocation-id eipalloc-12345678
```

Asocia la Elastic IP `eipalloc-12345678` a la instancia `i-1234567890abcdef0`.
