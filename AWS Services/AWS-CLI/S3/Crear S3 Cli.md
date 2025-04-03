Tras configurar las [[Credenciales de Acceso]] podemos crear un bucket en s3 ejecutando:

```sh
aws s3api create-bucket --bucket nombre-del-bucket --region us-east-1
```

Si la región requiere configuración explícita, usa:

```sh
aws s3api create-bucket --bucket nombre-del-bucket --region us-east-1 --create-bucket-configuration LocationConstraint=us-east-1
```

---

## Confirmar que el bucket se creó

Para listar los buckets en tu cuenta, usa:

```sh
aws s3 ls
```

---
