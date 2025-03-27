
###### Para crear una [[VPC]] y otros recursos de la VPC mediante la AWS CLI

1. Utilice el siguiente comando [create-vpc](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/ec2/create-vpc.html) para crear una VPC con el bloque CIDR de IPv4 especificado.  

    ```
    aws ec2 create-vpc --cidr-block `10.0.0.0/24` --query Vpc.VpcId --output text
    ```

    Como alternativa, para crear una VPC de doble pila, agregue la opción `--amazon-provided-ipv6-cidr-block` para agregar un bloque CIDR de IPv6 proporcionado por Amazon, como se muestra en el siguiente ejemplo.
    
    ```
    aws ec2 create-vpc --cidr-block `10.0.0.0/24` --amazon-provided-ipv6-cidr-block --query Vpc.VpcId --output text
    ```

    Estos comandos devuelven el ID de la VPC nueva. A continuación, se muestra un ejemplo.
    
    ```
    vpc-`1a2b3c4d5e6f1a2b3` 
    ```

2. [VPC de doble pila] Obtenga el bloque de CIDR IPv6 asociado a su VPC mediante el siguiente comando [describe-vpcs](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/ec2/describe-vpcs.html).
    
    ```
    aws ec2 describe-vpcs --vpc-id vpc-`1a2b3c4d5e6f1a2b3` --query Vpcs[].Ipv6CidrBlockAssociationSet[].Ipv6CidrBlock --output text
    ```

    A continuación, se muestra un ejemplo del resultado.
    
    `2600:1f13:cfe:3600::/56`

3. Cree una o más subredes, en función del caso de uso. En producción, le recomendamos que lance recursos en al menos dos zonas de disponibilidad. Utilice uno de los siguientes comandos para crear cada subred.
    
    - Subred de solo IPv4: para crear una subred con un bloque CIDR de IPv4 específico, utilice el siguiente comando [create-subnet](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/ec2/create-subnet.html).
        
        ```
        aws ec2 create-subnet --vpc-id vpc-`1a2b3c4d5e6f1a2b3` --cidr-block `10.0.1.0/20` --availability-zone `us-east-2a` --query Subnet.SubnetId --output text
        ```
        
    - Subred de doble pila: si creó una VPC de doble pila, puede utilizar la opción `--ipv6-cidr-block` para crear una subred de doble pila, como se muestra en el siguiente comando.
        
        ```
        aws ec2 create-subnet --vpc-id vpc-`1a2b3c4d5e6f1a2b3` --cidr-block `10.0.1.0/20` --ipv6-cidr-block `2600:1f13:cfe:3600::/64` --availability-zone `us-east-2a` --query Subnet.SubnetId --output text
        ```
        
    - Subred de solo IPv6: si creó una VPC de doble pila, puede utilizar la opción `--ipv6-native` para crear una subred de solo IPv6, como se muestra en el siguiente comando.
        
        ```
        aws ec2 create-subnet --vpc-id vpc-`1a2b3c4d5e6f1a2b3` --ipv6-native --ipv6-cidr-block `2600:1f13:cfe:3600::/64` --availability-zone `us-east-2a` --query Subnet.SubnetId --output text
        ```

    Estos comandos devuelven el ID de la subred nueva. A continuación se muestra un ejemplo.
    
    ```
    subnet-`1a2b3c4d5e6f1a2b3` 
    ```

4. Si necesita una subred pública para los servidores web o para una puerta de enlace de NAT, haga lo siguiente:
    
    1. Cree una puerta de enlace de Internet mediante el siguiente comando [create-internet-gateway](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/ec2/create-internet-gateway.html). El comando devuelve el ID de la nueva puerta de enlace de Internet.
        
        ```
        aws ec2 create-internet-gateway --query InternetGateway.InternetGatewayId --output text
        ```
        
    2. Adjunte la puerta de enlace de Internet a su VPC mediante el siguiente comando [attach-internet-gateway](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/ec2/attach-internet-gateway.html). Utilice el ID de la puerta de enlace de Internet que obtuvo en el paso anterior.
        
        ```
        aws ec2 attach-internet-gateway --vpc-id vpc-`1a2b3c4d5e6f1a2b3` --internet-gateway-id `igw-id`
        ```
        
    3. Cree una tabla de enrutamiento personalizada para la subred pública mediante el siguiente comando [create-route-table](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/ec2/create-route-table.html). El comando devuelve el ID de la nueva tabla de enrutamiento.
        
        ```
        aws ec2 create-route-table --vpc-id vpc-`1a2b3c4d5e6f1a2b3` --query RouteTable.RouteTableId --output text
        ```
        
    4. Cree una ruta en la tabla de enrutamiento que envíe todo el tráfico IPv4 a la puerta de enlace de Internet mediante el siguiente comando [create-route](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/ec2/create-route.html). Utilice el ID de la tabla de enrutamiento para la subred pública.
        
        ```
        aws ec2 create-route --route-table-id `rtb-id-public` --destination-cidr-block 0.0.0.0/0 --gateway-id `igw-id`
        ```
        
    5. Asocie la tabla de enrutamiento a la subred pública mediante el siguiente comando [associate-route-table](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/ec2/associate-route-table.html). Utilice el ID de la tabla de enrutamiento para la subred pública y el ID de la subred pública.
        
        ```
        aws ec2 associate-route-table --route-table-id `rtb-id-public` --subnet-id `subnet-id-public-subnet`
        ```

5. [IPv6] Puede agregar una puerta de enlace de Internet de solo salida para que las instancias de una subred privada puedan acceder a Internet a través de IPv6 (por ejemplo, para obtener actualizaciones de software), pero los hosts de Internet no puedan acceder a las instancias.
    
    1. Cree una puerta de enlace de Internet de solo salida mediante el siguiente comando [create-egress-only-internet-gateway](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/ec2/create-egress-only-internet-gateway.html). El comando devuelve el ID de la nueva puerta de enlace de Internet.
        
        ```
        aws ec2 create-egress-only-internet-gateway --vpc-id vpc-`1a2b3c4d5e6f1a2b3` --query EgressOnlyInternetGateway.EgressOnlyInternetGatewayId --output text
        ```
        
    2. Cree una tabla de enrutamiento personalizada para la subred privada mediante el siguiente comando [create-route-table](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/ec2/create-route-table.html). El comando devuelve el ID de la nueva tabla de enrutamiento.
        
        ```
        aws ec2 create-route-table --vpc-id vpc-`1a2b3c4d5e6f1a2b3` --query RouteTable.RouteTableId --output text
        ```
        
    3. Cree una ruta en la tabla de enrutamiento para que la subred privada envíe todo el tráfico IPv6 a la puerta de enlace de Internet de solo salida mediante el siguiente comando [create-route](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/ec2/create-route.html). Utilice el ID de la tabla de enrutamiento que obtuvo en el paso anterior.
        
        ```
        aws ec2 create-route --route-table-id `rtb-id-private` --destination-cidr-block `::/0` --egress-only-internet-gateway `eigw-id`
        ```
        
    4. Asocie la tabla de enrutamiento a la subred privada mediante el siguiente comando [associate-route-table](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/ec2/associate-route-table.html).
        
        ```
        aws ec2 associate-route-table --route-table-id `rtb-id-private` --subnet-id `subnet-id-private-subnet`
        ```

6. Si necesita una puerta de enlace de NAT para los recursos de una subred privada, haga lo siguiente:
    
    1. Cree una dirección IP elástica para la puerta de enlace de NAT mediante el siguiente comando [allocate-address](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/ec2/allocate-address.html).
        
        ```
        aws ec2 allocate-address --domain vpc --query AllocationId --output text
        ```
        
    2. Cree una puerta de enlace de NAT en la subred pública mediante el siguiente comando [create-nat-gateway](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/ec2/create-nat-gateway.html). Utilice el ID de asignación que obtuvo en el paso anterior.
        
        ```
        aws ec2 create-nat-gateway --subnet-id `subnet-id-public-subnet` --allocation-id `eipalloc-id`
        ```
        
    3. (Opcional) Si ya creó una tabla de enrutamiento para la subred privada en el paso 5, omita este paso. De lo contrario, utilice el comando [create-route-table](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/ec2/create-route-table.html) para crear una tabla de enrutamiento para la subred privada. El comando devuelve el ID de la nueva tabla de enrutamiento.
        
        ```
        aws ec2 create-route-table --vpc-id vpc-`1a2b3c4d5e6f1a2b3` --query RouteTable.RouteTableId --output text
        ```
        
    4. Cree una ruta en la tabla de enrutamiento para que la subred privada envíe todo el tráfico IPv4 a la puerta de enlace de NAT mediante el siguiente comando [create-route](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/ec2/create-route.html). Utilice el ID de la tabla de enrutamiento para la subred privada, que creó en este paso o en el paso 5.
        
        ```
        aws ec2 create-route --route-table-id `rtb-id-private` --destination-cidr-block `0.0.0.0/0` --gateway-id `nat-id`
        ```
        
    5. (Opcional) Si ya asoció una tabla de enrutamiento a la subred privada en el paso 5, omita este paso. De lo contrario, utilice el comando [associate-route-table](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/ec2/associate-route-table.html) para asociar la tabla de enrutamiento a la subred privada. Utilice el ID de la tabla de enrutamiento para la subred privada, que creó en este paso o en el paso 5.
        
        ```
        aws ec2 associate-route-table --route-table-id `rtb-id-private` --subnet-id `subnet-id-private-subnet`
        ```
