### 1. Comandos para Buckets en S3

Los comandos de **buckets** permiten gestionar los contenedores de almacenamiento donde se guardan los objetos.

#### Listar Buckets (`ls`)

- Función: Muestra una lista de todos los buckets en tu cuenta de AWS.
    
- Sintaxis:
    
    ```sh
    aws s3 ls [opciones]
    ```
    

##### Ejemplo:

```sh
aws s3 ls
```

Este comando muestra todos los buckets en tu cuenta de AWS.

#### Crear un Bucket (`mb`)

- Función: Crea un nuevo bucket en S3.
    
- Sintaxis:
    
    ```sh
    aws s3 mb s3://<nombre-bucket> [opciones]
    ```
    

##### Ejemplo:

```sh
aws s3 mb s3://mi-nuevo-bucket
```

Crea un nuevo bucket llamado `mi-nuevo-bucket`.

#### Eliminar un Bucket (`rb`)

- Función: Elimina un bucket vacío de S3.
    
- Sintaxis:
    
    ```sh
    aws s3 rb s3://<nombre-bucket> [opciones]
    ```
    

##### Ejemplo:

```sh
aws s3 rb s3://mi-bucket
```

Elimina el bucket `mi-bucket`, siempre y cuando esté vacío.

---

### 2. Comandos para Objetos en S3

Los comandos de **objetos** permiten gestionar los archivos almacenados dentro de los buckets.

#### Subir un archivo (`cp`)

- Función: Copia un archivo local a un bucket de S3.
    
- Sintaxis:
    
    ```sh
    aws s3 cp <archivo-local> s3://<nombre-bucket>/<ruta-objeto> [opciones]
    ```
    

##### Ejemplo:

```sh
aws s3 cp archivo.txt s3://mi-bucket/archivo.txt
```

Sube el archivo `archivo.txt` al bucket `mi-bucket`.

#### Descargar un archivo (`cp`)

- Función: Descarga un archivo de un bucket de S3 a tu máquina local.
    
- Sintaxis:
    
    ```sh
    aws s3 cp s3://<nombre-bucket>/<ruta-objeto> <archivo-local> [opciones]
    ```
    

##### Ejemplo:

```sh
aws s3 cp s3://mi-bucket/archivo.txt archivo-descargado.txt
```

Descarga el archivo `archivo.txt` desde `mi-bucket` a tu máquina local como `archivo-descargado.txt`.

#### Sincronizar directorios (`sync`)

- Función: Sincroniza un directorio local con un bucket de S3.
    
- Sintaxis:
    
    ```sh
    aws s3 sync <directorio-local> s3://<nombre-bucket>/<ruta> [opciones]
    ```
    

##### Ejemplo:

```sh
aws s3 sync ./mi-carpeta s3://mi-bucket/mi-carpeta
```

Sincroniza el contenido de la carpeta local `mi-carpeta` con el bucket `mi-bucket`.

#### Eliminar un objeto (`rm`)

- Función: Elimina un archivo u objeto de un bucket de S3.
    
- Sintaxis:
    
    ```sh
    aws s3 rm s3://<nombre-bucket>/<ruta-objeto> [opciones]
    ```
    

##### Ejemplo:

```sh
aws s3 rm s3://mi-bucket/archivo.txt
```

Elimina el objeto `archivo.txt` del bucket `mi-bucket`.

---

### 3. Comandos de Configuración y Permisos

Los comandos de configuración y permisos permiten gestionar las políticas de acceso y las configuraciones de los buckets y objetos en S3.

#### Verificar las políticas de acceso (`get-bucket-policy`)

- Función: Muestra la política de acceso de un bucket.
    
- Sintaxis:
    
    ```sh
    aws s3api get-bucket-policy --bucket <nombre-bucket>
    ```
    

##### Ejemplo:

```sh
aws s3api get-bucket-policy --bucket mi-bucket
```

Muestra la política de acceso del bucket `mi-bucket`.

#### Establecer una política de acceso (`put-bucket-policy`)

- Función: Establece una política de acceso en un bucket de S3.
    
- Sintaxis:
    
    ```sh
    aws s3api put-bucket-policy --bucket <nombre-bucket> --policy file://<ruta-archivo-json>
    ```
    

##### Ejemplo:

```sh
aws s3api put-bucket-policy --bucket mi-bucket --policy file://policy.json
```

Establece una política de acceso en el bucket `mi-bucket` usando un archivo JSON de políticas.

#### Configurar el control de versiones (`put-bucket-versioning`)

- Función: Activa o desactiva el control de versiones en un bucket.
    
- Sintaxis:
    
    ```sh
    aws s3api put-bucket-versioning --bucket <nombre-bucket> --versioning-configuration Status=Enabled
    ```
    

##### Ejemplo:

```sh
aws s3api put-bucket-versioning --bucket mi-bucket --versioning-configuration Status=Enabled
```

Activa el control de versiones en el bucket `mi-bucket`.

---

### 4. Comandos de Red y Transferencia de Datos

Los comandos de **transferencia** permiten mover datos entre tu máquina local y los buckets de S3.

#### Transferir grandes archivos en partes (`s3 cp` con opción `--expected-size`)

- Función: Permite transferir archivos grandes en partes para un manejo más eficiente.
    
- Sintaxis:
    
    ```sh
    aws s3 cp <archivo-local> s3://<nombre-bucket>/<ruta-objeto> --expected-size <tamaño-en-bits>
    ```
    

##### Ejemplo:

```sh
aws s3 cp archivo-grande.zip s3://mi-bucket/archivo-grande.zip --expected-size 1073741824
```

Sube el archivo `archivo-grande.zip` a S3, dividiéndolo en partes, para optimizar la transferencia.

---

### 5. Comandos de Copia Recursiva

Los comandos de **copia recursiva** permiten copiar directorios completos en S3.

#### Copiar un directorio completo (`cp` con opción `--recursive`)

- Función: Copia un directorio completo de manera recursiva.
    
- Sintaxis:
    
    ```sh
    aws s3 cp <directorio-local> s3://<nombre-bucket> --recursive
    ```
    

##### Ejemplo:

```sh
aws s3 cp ./mi-carpeta s3://mi-bucket/ --recursive
```

Copia todos los archivos y subcarpetas dentro de `mi-carpeta` a `mi-bucket`.

---

### 6. Comandos de Configuración de Enlaces Públicos

Los comandos de **configuración de enlaces públicos** permiten cambiar la visibilidad de los objetos dentro de los buckets.

#### Hacer un objeto público (`acl`)

- Función: Cambia la política de acceso de un objeto para hacerlo público.
    
- Sintaxis:
    
    ```sh
    aws s3api put-object-acl --bucket <nombre-bucket> --key <ruta-objeto> --acl public-read
    ```
    

##### Ejemplo:

```sh
aws s3api put-object-acl --bucket mi-bucket --key archivo.txt --acl public-read
```

Hace público el objeto `archivo.txt` en el bucket `mi-bucket`.

---

Estos son algunos de los comandos más comunes para trabajar con **AWS S3** a través de la AWS CLI. Si necesitas más detalles o ejemplos adicionales, no dudes en preguntar. ¡Estoy aquí para ayudarte! 😊