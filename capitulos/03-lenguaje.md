# Elementos de C#

*C#* dispone de *garbage collector*.

Es necesario asignar valor a las variables para poder acceder a ellas, a no ser que reciban un valor por defecto (a no ser que sean de un tipo con valor por defecto, como los *arrays* y campos).

Todas las instancias creadas con `new` tienen valor por defecto, el cual es el resultado de establecer todos sus *bits* a 0. Esto se traduce en ***null*** para los tipos por referencia, 0 para tipos numéricos y enumeraciones, ***\\0*** para caracteres y ***false*** para los booleanos.

```cs
int x;
int y = new int();
Console.WriteLine(x);  // error
Console.WriteLine(y);  // ok
```

Se puede obtener el valor por defecto de un tipo mediante `default()`.

```cs
x = default(int);
```

El valor por defecto de una instancia de tipo clase o estructura es el resultado de aplicar esto mismo en todos sus miembros de datos.

## Parámetros

Cambiar el valor de una variable pasada por valor no cambia el valor de la variable original. Aunque la variable sea de un tipo por referencia, se considera un paso por valor en el que dicho valor es un "apuntador" al objeto. En tal caso, las modificaciones hechas en dicho objeto serán visibles desde la variable original, aunque dicha variable mantendrá su valor (seguirá apuntando al mismo objeto, aunque esta haya cambiado de valor):

```cs
void Foo(MiClase o)
{
    o.campo = 30;
    o = null;
}

void Metodo()
{
    MiClase objeto = new MiClase(50);
    Console.WriteLine(objeto.campo);  // 50
    Foo(objeto);
    Console.WriteLine(objeto.campo);  // 30
}
```

Cuando llamamos a ***Foo()***, este recibe una **copia** de la referencia en el parámetro ***o***. Todo lo que se haga al objeto referenciado por ***o*** se verá en el objeto referenciado por ***objeto***, ya que son el mismo objeto. Sin embargo, ***o*** y ***objeto*** son dos variables distintas, almacenadas en distintas localizaciones en memoria. Así, `o = null;` afecta a ***o*** pero no tiene efecto en el valor de la variable ***objeto***.

Toda variable es en realidad una referencia a una localización de memoria donde se almacena un valor. En el caso de un tipo por valor, el valor que almacena la variable es su valor en sí. En el caso de un tipo por referencia, el valor que almacena la variable es un "apuntador" al objeto relacionado.

### ref, out, in

Mediante `ref` podemos pasar una variable por referencia. Cuando lo hacemos, en lugar de copiarse el valor de la variable al parámetro, lo que sucede es que este último pasa a referirse a la misma posición de memoria que la variable original, es decir, la variable original (argumento) y el parámetro serán exactamente la misma variable, independientemente de si su valor es un valor en sí o un "apuntador" (referencia) a un objeto.

La palabra `ref` debe indicarse tanto en la definición como explícitamente en la llamada.

```cs
void Foo(ref MiClase o)
{
    o.campo = 30;
    o = null;
}

void Metodo()
{
    MiClase objeto = new MiClase(50);
    Console.WriteLine(objeto.campo);  // 50
    Foo(ref objeto);
    Console.WriteLine(objeto.campo);  // error: null no tiene campos
}
```

Existe otro modo de pasar por referencia, y es usando `out` en lugar de `ref`. En este caso, es un parámetro de salida (*write-only*), es decir, se asume que en el momento de la llamada, dicha variable no tiene valor, y se le debe dar valor obligatoriamente antes de retornar. Es posible declarar, en la llamada, variables sobre la marcha para este tipo de parámetros:

```cs
Foo(out int x);
```

Por otro lado, existe el paso de referencia de parámetros de entrada (*read-only*), mediante `in`. En este caso, la variable debe estar inicializada antes de la llamada, y dentro de la función no puede cambiarse su valor.

### Número arbitrario de argumentos

En la lista de parámetros, el último de ellos puede ser declarado como `params`. Su tipo debe ser un *array* de una dimensión y de un tipo concreto, y recoge un número arbitrario de argumentos (o un *array* del tipo adecuado).

```cs
void Foo(int x, params char[] resto)
{
    // ...
}

void Metodo()
{
    Foo(32, 'a', 'b', 'c');
    // Equivale a hacer:
    Foo(32, new char[] {'a', 'b', 'c'});
}
```

### Parámetros opcionales

```cs
void Foo(int x, int y=33, char c='e')
{
    // ...
}
```

Los tipos opcionales no se pueden pasar por referencia, y su valor debe ser una expresión constante o un constructor sin argumentos de un tipo por valor.

Los parámetros obligatorios deben ir antes de los opcionales, a excepción de los `params` que siempre deber figurar al final.

### Argumentos con nombre

En **la llamada**, los argumentos se pueden pasar por posición, por nombre, o ambas cosas. Los argumentos por posición deben estar siempre **antes** de los argumentos con nombre. Se debe tener cuidado de no repetir un mismo argumento en la llamada.

```cs
Foo(25, c:'d', y:50);
```

## Tipo implícito

Es posible declarar una variable como `var` si su tipo puede ser inferido por el compilador (de forma estática). Es útil con los tipos anónimos (véase más adelante).

```cs
var s = "Hello";
var o = new MiClase();
var n = 25;
```

Equivale a:

```cs
string s = "Hello";
MiClase o = new MiClase();
int n = 25;
```

## Expresiones y operadores

Los operadores pueden ser unarios, binarios o ternarios. Una expresión retorna un valor; de lo contrario es de tipo ***void***. Los operadores unen expresiones (normalmente no ***void***).

### Asignación

El operando derecho no puede ser ***void***. Tras asignar valor a la variable de la izquierda, la expresión retorna el valor asignado.

```cs
x = 2 * (y = 5);  // y->5, x->10
a = b = c = 15;  // a->15, b->15, c->15
```

Disponemos de *compound assignment operators*: `+=`, `-=`, `<<=`, etc.

### Asociatividad y precedencia

Para controlar la precedencia se usa el paréntesis. Dentro del mismo nivel de paréntesis se ejecutan los operadores en orden de precedencia. Cuando dos operadores tienen la misma precedencia en el mismo nivel, se ejecutan en orden de asociatividad.

En cuanto a la asociatividad, los operadores binarios se ejecutan de izquierda a derecha. La excepción son los operadores de asignación, lambda (`=>`), *null coalescing* (`??`) y ternario (`?` - `:`), que se evalúan de derecha a izquierda.

A continuación, una lista de operadores en orden de precedencia. Los que están en la misma categoría tienen la misma precedencia:

- Primarios: `.`, `->`, `()` (llamada a función), `[]`, `++` (*postfix*), `--` (*postfix*), `new`, `stackalloc`, `typeof`, `nameof`, `checked`, `unchecked`, `default`.
- Unarios: `Await`, `sizeof`, `?.`, `+`, `-`, `!`, `~`, `++` (*prefix*), `--` (*prefix*), `()` (*cast*), `*` (valor en dirección de memoria), `*`, `&` (dirección de memoria del valor).
- Multiplicativos: `*`, `/`, `%`.
- Aditivos: `+`, `-`.
- *Shift*: `<<`, `>>`.
- Relacionales: `<`, `>`, `<=`, `>=`, `is`, `as`.
- Igualdad: `==`, `!=`.
- *And* lógico: `&`.
- *Xor* lógico: `^`.
- *Or* lógico: `|`.
- *And* condicional: `&&`.
- *Or* condicional: `||`.
- Ternario condicional: `?` - `:`.
- Asignación y lambda: `=`, `*=`, `/=`, `+=`, `-=`, `<<=`, `>>=`, `&=`, `^=`, `|=`, `=>`.

Los operadores relacionados con el acceso a memoria (`->`, `&` y `*`) solo pueden usarse en contexto no seguro (`unsafe`, véase más adelante), que debe habilitarse en las opciones de compilación.

### Operadores para *null*

Operador *null coalescing* (`??`) retorna el valor del operando derecho si el izquierdo es igual a ***null***. De lo contrario retorna el valor del primer operador. Si no hace falta, no se evalúa el operando derecho.

```cs
c = a ?? "vacío";
```

El operador nulo condicional (`?.`, llamado "Elvis") permite acceder a miembros de valores que podrían ser nulos.

```cs
a = objeto.miembro;
```

Esto levanta una excepción si ***objeto*** es nulo.

```cs
a = objeto == null ? null : objeto.miembro;
```

Esto es correcto. Pero se puede abreviar así:

```cs
a = objeto?.miembro;
```

El operador retorna ***null*** si el objeto al que se aplica (operando izquierdo) es ***null***. De lo contrario retorna el valor del miembro especificado de tal objeto. En el ejemplo, ***a*** debe ser de un tipo *nullable* (que acepte el valor ***null***).

## Sentencias

Simples y bloques (entre llaves ***{}***).

### Declaraciones
