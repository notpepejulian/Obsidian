###### 1. Creación de una [[VPC CLI|VPC por comandos AWS]]

```bash
aws ec2 create-vpc --cidr-block "16.0.0.0/16" --instance-tenancy "default" --tag-specifications '{"resourceType":"vpc","tags":[{"key":"Name","value":"Nacho-vpc"}]}'
```

###### 2. Modificación de atributos de la VPC

```bash
aws ec2 modify-vpc-attribute --vpc-id "preview-vpc-1234" --enable-dns-hostnames '{"value":true}'
```

###### 3. Ver información de la VPC

```bash
aws ec2 describe-vpcs --vpc-ids "preview-vpc-1234"
```

###### 4. Creación de un VPC Endpoint para S3

```bash
aws ec2 create-vpc-endpoint --vpc-id "preview-vpc-1234" --service-name "com.amazonaws.eu-west-1.s3" --tag-specifications '{"resourceType":"vpc-endpoint","tags":[{"key":"Name","value":"Nacho-vpce-s3"}]}'
```

###### 5. Creación de Subredes

```bash
aws ec2 create-subnet --vpc-id "preview-vpc-1234" --cidr-block "16.0.0.0/20" --availability-zone "eu-west-1a" --tag-specifications '{"resourceType":"subnet","tags":[{"key":"Name","value":"Nacho-subnet-public1-eu-west-1a"}]}'

aws ec2 create-subnet --vpc-id "preview-vpc-1234" --cidr-block "16.0.16.0/20" --availability-zone "eu-west-1b" --tag-specifications '{"resourceType":"subnet","tags":[{"key":"Name","value":"Nacho-subnet-public2-eu-west-1b"}]}'

aws ec2 create-subnet --vpc-id "preview-vpc-1234" --cidr-block "16.0.128.0/20" --availability-zone "eu-west-1a" --tag-specifications '{"resourceType":"subnet","tags":[{"key":"Name","value":"Nacho-subnet-private1-eu-west-1a"}]}'

aws ec2 create-subnet --vpc-id "preview-vpc-1234" --cidr-block "16.0.144.0/20" --availability-zone "eu-west-1b" --tag-specifications '{"resourceType":"subnet","tags":[{"key":"Name","value":"Nacho-subnet-private2-eu-west-1b"}]}'
```

###### 6. Creación y Asignación de una Internet Gateway

```bash
aws ec2 create-internet-gateway --tag-specifications '{"resourceType":"internet-gateway","tags":[{"key":"Name","value":"Nacho-igw"}]}'

aws ec2 attach-internet-gateway --internet-gateway-id "preview-igw-1234" --vpc-id "preview-vpc-1234"
```

###### 7. Creación y Configuración de Tablas de Rutas

```bash
aws ec2 create-route-table --vpc-id "preview-vpc-1234" --tag-specifications '{"resourceType":"route-table","tags":[{"key":"Name","value":"Nacho-rtb-public"}]}'

aws ec2 create-route --route-table-id "preview-rtb-public-0" --destination-cidr-block "0.0.0.0/0" --gateway-id "preview-igw-1234"

aws ec2 associate-route-table --route-table-id "preview-rtb-public-0" --subnet-id "preview-subnet-public-0"
aws ec2 associate-route-table --route-table-id "preview-rtb-public-0" --subnet-id "preview-subnet-public-1"
```

###### 8. Creación y Asociación de Tablas de Rutas Privadas

```bash
aws ec2 create-route-table --vpc-id "preview-vpc-1234" --tag-specifications '{"resourceType":"route-table","tags":[{"key":"Name","value":"Nacho-rtb-private1-eu-west-1a"}]}'

aws ec2 associate-route-table --route-table-id "preview-rtb-private-1" --subnet-id "preview-subnet-private-2"

aws ec2 create-route-table --vpc-id "preview-vpc-1234" --tag-specifications '{"resourceType":"route-table","tags":[{"key":"Name","value":"Nacho-rtb-private2-eu-west-1b"}]}'

aws ec2 associate-route-table --route-table-id "preview-rtb-private-2" --subnet-id "preview-subnet-private-3"
```

###### 9. Verificación y Modificación de Tablas de Rutas

```bash
aws ec2 describe-route-tables --route-table-ids "preview-rtb-private-1" "preview-rtb-private-2"

aws ec2 modify-vpc-endpoint --vpc-endpoint-id "preview-vpce-1234" --add-route-table-ids "preview-rtb-private-1" "preview-rtb-private-2"
```