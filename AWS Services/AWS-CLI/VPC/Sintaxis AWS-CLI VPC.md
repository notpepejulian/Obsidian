Los comandos más comunes de **AWS VPC (Virtual Private Cloud)** usando la AWS CLI. Los clasifico por categoría: redes, subredes, tablas de ruteo, gateways, ACLs y grupos de seguridad.

---

### 1. Comandos de VPC

#### Crear una VPC (`create-vpc`)

- Función: Crea una nueva VPC con el rango de direcciones IP especificado.
    
- Sintaxis:
    
    ```sh
    aws ec2 create-vpc --cidr-block <rango-cidr> [opciones]
    ```
    

#### Ejemplo:

```sh
aws ec2 create-vpc --cidr-block 10.0.0.0/16
```

Crea una VPC con el rango `10.0.0.0/16`.

#### Listar VPCs (`describe-vpcs`)

- Función: Muestra todas las VPCs existentes en la cuenta.
    
- Sintaxis:
    
    ```sh
    aws ec2 describe-vpcs [opciones]
    ```
    

#### Ejemplo:

```sh
aws ec2 describe-vpcs
```

#### Eliminar una VPC (`delete-vpc`)

- Función: Elimina una VPC específica.
    
- Sintaxis:
    
    ```sh
    aws ec2 delete-vpc --vpc-id <id-vpc>
    ```
    

#### Ejemplo:

```sh
aws ec2 delete-vpc --vpc-id vpc-12345678
```

---

### 2. Comandos de Subredes

#### Crear una subred (`create-subnet`)

- Función: Crea una subred dentro de una VPC.
    
- Sintaxis:
    
    ```sh
    aws ec2 create-subnet --vpc-id <id-vpc> --cidr-block <rango-subred> [--availability-zone <zona>]
    ```
    

#### Ejemplo:

```sh
aws ec2 create-subnet --vpc-id vpc-12345678 --cidr-block 10.0.1.0/24
```

#### Listar subredes (`describe-subnets`)

- Función: Muestra las subredes existentes.
    
- Sintaxis:
    
    ```sh
    aws ec2 describe-subnets [opciones]
    ```
    

#### Eliminar subred (`delete-subnet`)

- Función: Elimina una subred específica.
    
- Sintaxis:
    
    ```sh
    aws ec2 delete-subnet --subnet-id <id-subred>
    ```
    

---

### 3. Comandos de Internet Gateway

#### Crear un Internet Gateway (`create-internet-gateway`)

- Función: Crea un nuevo gateway para acceso a internet.
    
- Sintaxis:
    
    ```sh
    aws ec2 create-internet-gateway
    ```
    

#### Asociar Internet Gateway a una VPC (`attach-internet-gateway`)

- Función: Asocia un Internet Gateway a una VPC.
    
- Sintaxis:
    
    ```sh
    aws ec2 attach-internet-gateway --internet-gateway-id <id-gw> --vpc-id <id-vpc>
    ```
    

#### Eliminar Internet Gateway (`delete-internet-gateway`)

- Sintaxis:
    
    ```sh
    aws ec2 delete-internet-gateway --internet-gateway-id <id-gw>
    ```
    

---

### 4. Comandos de Tablas de Rutas

#### Crear tabla de ruteo (`create-route-table`)

- Función: Crea una tabla de rutas en una VPC.
    
- Sintaxis:
    
    ```sh
    aws ec2 create-route-table --vpc-id <id-vpc>
    ```
    

#### Crear ruta (`create-route`)

- Función: Añade una ruta a una tabla de ruteo.
    
- Sintaxis:
    
    ```sh
    aws ec2 create-route --route-table-id <id-tabla> --destination-cidr-block <cidr> --gateway-id <id-gw>
    ```
    

#### Asociar tabla de ruteo a subred (`associate-route-table`)

- Sintaxis:
    
    ```sh
    aws ec2 associate-route-table --subnet-id <id-subred> --route-table-id <id-tabla>
    ```
    

#### Listar tablas de rutas (`describe-route-tables`)

- Sintaxis:
    
    ```sh
    aws ec2 describe-route-tables
    ```
    

---

### 5. Comandos de Network ACLs

#### Crear ACL (`create-network-acl`)

- Función: Crea una ACL para una VPC.
    
- Sintaxis:
    
    ```sh
    aws ec2 create-network-acl --vpc-id <id-vpc>
    ```
    

#### Asociar ACL a subred (`associate-network-acl`)

- Función: Asocia una ACL a una subred.
    
- Sintaxis:
    
    ```sh
    aws ec2 replace-network-acl-association --association-id <id-asociacion> --network-acl-id <id-acl>
    ```
    

#### Añadir regla a ACL (`create-network-acl-entry`)

- Función: Agrega una entrada (regla) a la ACL.
    
- Sintaxis:
    
    ```sh
    aws ec2 create-network-acl-entry --network-acl-id <id-acl> --rule-number <número> --protocol <protocolo> --rule-action <allow|deny> --egress --cidr-block <cidr> --port-range From=<inicio>,To=<fin>
    ```
    

---

### 6. Comandos de DHCP Options Set (opcional)

#### Crear opciones de DHCP (`create-dhcp-options`)

- Sintaxis:
    
    ```sh
    aws ec2 create-dhcp-options --dhcp-configuration "Key=domain-name,Values=example.com" "Key=domain-name-servers,Values=8.8.8.8,8.8.4.4"
    ```
    

#### Asociar con una VPC (`associate-dhcp-options`)

- Sintaxis:
    
    ```sh
    aws ec2 associate-dhcp-options --dhcp-options-id <id> --vpc-id <id-vpc>
    ```
    

---

### 7. Comandos de VPC Peering (conexiones entre VPCs)

#### Crear peering (`create-vpc-peering-connection`)

- Sintaxis:
    
    ```sh
    aws ec2 create-vpc-peering-connection --vpc-id <vpc-origen> --peer-vpc-id <vpc-destino>
    ```
    

#### Aceptar conexión (`accept-vpc-peering-connection`)

- Sintaxis:
    
    ```sh
    aws ec2 accept-vpc-peering-connection --vpc-peering-connection-id <id>
    ```
    

---

Estos son los comandos más importantes y usados para gestionar redes VPC con AWS CLI. ¿Quieres que sigamos con otro servicio como IAM, RDS o Lambda?