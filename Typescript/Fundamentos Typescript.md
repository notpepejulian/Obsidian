TypeScript es un superconjunto tipado de JavaScript que agrega seguridad y robustez al código. Se compila a JavaScript y es ampliamente utilizado en el desarrollo de aplicaciones modernas, incluyendo AWS CDK.

## Instalación de TypeScript

Para instalar TypeScript globalmente en tu sistema:

```bash
npm install -g typescript
```

Verifica la instalación:

```bash
tsc --version
```

## Tipos de Datos Básicos

### Number, String, Boolean

```typescript
let numero: number = 42;
let texto: string = "Hola TypeScript";
let esVerdadero: boolean = true;
```

### Arrays y Tuplas

```typescript
let lista: number[] = [1, 2, 3];
let tupla: [string, number] = ["Edad", 30];
```

### Any y Unknown

```typescript
let variableDinamica: any = "Puede ser cualquier cosa";
variableDinamica = 10;

let variableDesconocida: unknown = "Valor";
```

## Funciones

### Funciones Tipadas

```typescript
function sumar(a: number, b: number): number {
    return a + b;
}
```

### Funciones Flecha

```typescript
const multiplicar = (a: number, b: number): number => a * b;
```

## Interfaces y Clases

### Interfaces

```typescript
interface Persona {
    nombre: string;
    edad: number;
}

let usuario: Persona = { nombre: "Carlos", edad: 25 };
```

### Clases

```typescript
class Animal {
    nombre: string;

    constructor(nombre: string) {
        this.nombre = nombre;
    }

    hacerSonido(): void {
        console.log("Sonido genérico");
    }
}

let perro = new Animal("Firulais");
perro.hacerSonido();
```

## Modificadores de Acceso

- `public`: Accesible desde cualquier parte.
    
- `private`: Solo accesible dentro de la clase.
    
- `protected`: Accesible dentro de la clase y subclases.
    

```typescript
class Vehiculo {
    protected marca: string;
    private modelo: string;
    
    constructor(marca: string, modelo: string) {
        this.marca = marca;
        this.modelo = modelo;
    }
}
```

## Genéricos

Permiten reutilizar código con diferentes tipos.

```typescript
function identidad<T>(valor: T): T {
    return valor;
}
let numeroIdentidad = identidad<number>(42);
let textoIdentidad = identidad<string>("Hola");
```

## Módulos y Exportaciones

### Exportar

```typescript
export class Utilidad {
    static saludar(): string {
        return "Hola desde Utilidad";
    }
}
```

### Importar

```typescript
import { Utilidad } from "./utilidad";
console.log(Utilidad.saludar());
```

## ¡Listo!

Estos son los conceptos básicos de TypeScript que te ayudarán a trabajar con AWS CDK. Ahora podemos ver su aplicación en CDK.