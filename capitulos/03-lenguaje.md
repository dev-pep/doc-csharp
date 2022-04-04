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

Simples (terminadas en punto y coma) y bloques (entre llaves ***{}***).

### Declaraciones

Declaran (y opcionalmente inicializan) una o más variables.

No se puede declarar una variable ya declarada en el presente bloque o en un bloque exterior al actual.

### Expresiones

Las sentencias consistentes en una expresión pueden ser de asignación, instanciación, incremento, decremento o llamada. Cualquier otro tipo es ilegal.

### Selección

Control de flujo.

#### if

```
if(a > 10)
    // sentencia o bloque
else
    // sentencia o bloque
```

En lugar de bloques de código se puede escribir una única sentencia.

La cláusula `else` es opcional. Dentro de esta se puede escribir una única sentencia `if`. Una cláusula `else` está ligada siempre a la sentencia `if` más próxima posible.

#### switch

Es más claro que utilizar muchos `else if`.

```cs
switch(expr)
{
    case const1:
        // Código
        break;
    case const2:
        // Código
        break;
    default:
        // Código
        break;
}
```

La expresión se evalúa una sola vez.

Los valores de `case` deben ser expresiones constantes (literales y/o constantes), lo cual implica que solo pueden ser de tipo *built-in* entero, booleano, charácter, enumeración o *string*.

La cláusula `default` es opcional.

Es **obligatorio especificar** dónde sigue el flujo de ejecución **al final** de cada cláusula `case` y también en la `default`. Puede ser:

- `break` sale de la sentencia `switch`.
- `goto case` ejecuta la cláusula cuyo valor es igual a la expresión que indicamos.
- `goto default` salta a la cláusula `default`.
- Cualquier otro tipo de salto: `return`, `throw`, `continue`, o `goto`.

Si hay varios valores que corresponden al mismo código, se pueden especificar seguidos:

```cs
case const1:
case const2:
    // Código
    break;
```

Es posible escribir sentencias `switch` basadas en **tipo** en lugar de en valor. Para ello, las cláusulas `case` indicarán un tipo y una variable que recibirá el valor de la expresión:

```cs
switch(expr)
{
    case int i:
        // Código. Disponemos del valor de la expresión en el entero i.
        break;
    case float f:
        // Código. Disponemos del valor de la expresión en el float f.
        break;
    default:
        // Código
        break;
}
```

Se puede usar cualquier tipo, no solamente los *built-in*. Los nombres de las variables de las cláusulas `case` no tienen por qué ser distintos.

Adicionalmente, se puede restringir la cláusula `case` según valor, mediante `when`:

```cs
case float f when f > 10:
case double d when d > 10:
case decimal m when m > 10:
    // Código
    break;
```

En este caso, las variables ***f***, ***d*** y ***m*** no están accesibles en el código de la cláusula.

Cuando existen tipos derivados, el orden en el que aparecen las cláusulas `case` puede ser importante. Esto no sucede en el caso de las expresiones constantes.

### Iteración

#### while


```
while(expr)
    // sentencia o bloque
```

#### do-while

```cs
do
    // sentencia o bloque
while(expr);
```

#### for

```
for(exprIni; exprCond; exprIter)
    // sentencia o bloque
```

Todas las expresiones son opcionales. `for(;;)` equivale a `while(true)`.

#### foreach

```
foreach(tipo var1 in var2)
    // sentencia o bloque
```

En este caso, es obligatorio declarar una nueva variable (***var1***), de tipo ***tipo***, que va tomando los valores de los elementos de ***var2***, que debe ser un objeto de un tipo **enumerable**, y cuyos elementos son del mismo tipo que ***var1***.

Por ejemplo, los tipos *array* y *string* son enumerables.

### Saltos

*C#* dispone de estos tipos de saltos:

- `break` sale del bloque de una sentencia de iteración o `switch`.
- `continue` inicia la siguiente iteración (en una sentencia de iteración).
- `goto` salta directamente a la etiqueta indicada. Paréntesis no necesarios.
- `return` retorna del método, opcionalmente indicando un valor. Paréntesis no necesarios.
- `throw` lanza una excepción (véase tema excepciones).

Una etiqueta se define como un único identificador seguido por dos puntos (***:***).

No puede haber sentencia `return` en un bloque `finally` (véase tema excepciones).

## *Namespaces*

Los *namespaces* pueden anidarse, y se indican mediante *dot notation*.

```cs
namespace Exterior
{
    namespace Medio
    {
        namespace Interior
        {
            class MiClase { /* ... */ }
        }
    }

}
```

Equivale a:

```cs
namespace Exterior.Medio.Interior
{
    class MiClase { /* ... */ }
}
```

En este caso, el *fully qualified name* de ***MiClase*** es ***Exterior.Medio.Interior.MiClase***.

Los tipos definidos fuera de un *namespace* pertenecen al *namespace* global. Los *namespaces* de más alto nivel (en el ejemplo, ***Exterior***) están dentro del *namespace* global.

### using

Mediante `using` podemos importar un *namespace*, de tal manera que evitamos tener que indicar los nombres *fully qualified*.

```cs
using Exterior.Medio.Interior;

class Coche  // esta clase está en el namespace global
{
    static void Main()
    {
        MiClase o;  // no hace falta escribir Exterior.Medio.Interior.MiClase
        // etc.
    }
}
```

Si la directiva `using` está dentro de un *namespace*, solo tendrá efecto en este.

Es posible importar una clase (tipo) específica de un *namespace* sin importarlo todo, mediante `using static`. Esto importa todos los miembros (datos y métodos) **estáticos** de la clase.

```cs
namespace UnNamespace
{
    class UnaClase
    {
        public static int Dato = 55;
        public static int Foo() { return 33; }
    }
}

namespace OtroNamespace
{
    using static UnNamespace.UnaClase;
    class MiClase
    {
        public void Main()
        {
            int x, y;

            x = Foo();
            y = Dato;
            Console.WriteLine(x + y);  // 88
        }
    }
}
```

Mientras en `using` solo se pueden indicar *namespaces*, en `using static` solo se pueden indicar tipos (si se aplica a una enumeración, se importarán sus elementos).

Un nombre definido en un *namespace* es directamente accesible desde cualquier *namespace* anidado, sin necesidad de cualificarlo.

### Resolución de nombres

Para resolver un nombre se buscará primero en el *namespace* actual. Si no se encuentra, en un *enclosing namespace* (name *hiding*). Si tampoco se encuentra, se intentará usando las cláusulas `using` definidas en el *namespace* actual (o en uno *enclosing*).

Supongamos que hacemos referencia a un nombre concreto. Supongamos también que utilizamos `using` para importar dos *namespaces* que definen ese mismo nombre, y que dicho nombre no está definido en el *namespace* actual (o uno *enclosing*). Entonces se producirá un error.

Es posible importar solamente un tipo concreto de un *namespace*, si usamos un *alias* para ese tipo:

```cs
using NuevoNombre = NombreEspacio.MiClase;
```

### global

Si nuestro *namespace* define un nombre que también existe en el *namespace* global, el nombre interior enmascara al del *namespace* global, para que no sea así, debemos usar el prefijo `global::` antes del nombre.
