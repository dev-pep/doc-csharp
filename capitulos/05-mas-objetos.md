# Más sobre objetos

## Genéricos

Se introduce un tipo como parámetro (parámetros tipo).

```cs
class Coche<T> { ... }
```

Dentro de la definición de la clase, ***T*** se trata como el nombre de un tipo. En la instanciación, se debe definir dicho tipo.

En este caso, ***Coche<T>*** es un tipo genérico (abierto). Disponemos de **diferentes** tipos (cerrados) que pueden usarse en el programa: ***Coche<string>***, ***Coche<int>***, etc. Se debe indicar el parámetro tipo ***T***:

```cs
Coche<int> o = new Coche<int>();
```

Podemos definir también métodos genéricos:

```cs
public int Metodo<T>(T parm1, int parm2) { ... }
```

A la hora de invocar el método:

```cs
int n = Metodo<int>(10, 20);
```

En el caso concreto de los métodos, si el parámetro tipo se puede deducir del tipo de los argumentos que se indican, no hace falta indicar el tipo:

```cs
int n = Metodo(10, 20);  // se deduce int
```

Los parámetros tipo se pueden definir en clases, estructuras, interfaces, delegados (ver más adelante) y métodos. Se le puede dar cualquier nombre al parámetro tipo, y puede haber más de uno en la lista (separados por comas).

Es posible sobrecargar los tipos o métodos genéricos, siempre que el **número** de parámetros tipos sea distinto.

El convenio indica que si existe un solo parámetro tipo se llame ***T***; si existe más de uno, tendrán nombres más descriptivos, con un prefijo ***T*** seguido de un nombre en *PascalCase*.

Si queremos crear una subclase de una clase genérica, hay que tener en cuenta que todos los tipos de la clase base que dejemos abiertos deben aparecer en la derivada como abiertos, y que los que cerremos en la base no aparecen en la derivada:

```cs
class Base<T1, T2> { ... }
class DerivA<T1, T2>: Base<T1, T2> { ... }
class DerivB<T1>: Base<T1, int> { ... }
class Deriv: Base<float, int> { ... }
```

La clase derivada puede añadir sus propios tipos abiertos:

```cs
class Deriv<T1, D1, D2>: Base<T1, int> { ... }
class Deriv<D1, D2>: Base<float, int> { ... }
```

Es posible cerrar un tipo de la base con el tipo de la derivada:

```cs
class Deriv: Base<Deriv, int> { ... }
```

### Datos estáticos

Los datos estáticos son únicos para cada tipo cerrado:

```cs
class Bob<T> { public static int Count; }

Console.WriteLine(++Bob<int>.Count);  // 1
Console.WriteLine(++Bob<int>.Count);  // 2
Console.WriteLine(++Bob<string>.Count);  // 1
Console.WriteLine(++Bob<object>.Count);  // 1
```

## Delegados

Los *delegates* son mecanismos para que una variable pueda contener una referencia a un método. Para ello es necesario definir el protocolo del delegado: parámetros y tipo de retorno. Se usa la palabra `delegate`, y es como declarar el tipo de delegado:

```cs
delegate int MiDelegado(string parm1, string parm2);
```

Una vez tenemos el tipo, podemos definir un método que cumpla ese protocolo:

```cs
int Foo(string s1, string s2) { ... }
```

Ahora ya podemos asignar ese método a una variable, teniendo en cuenta que el tipo de esta debe ser el del delegado:

```cs
MiDelegado d = Foo;
// Esto es equivalente:
MiDelegado d = new MiDelegado(Foo);
```

Para usar esa referencia, podemos usarla como *caller* directamente, pasarla por parámetro, etc.:

```cs
d("Un string", "Otro string");
d.Invoke("Un string", "Otro string");  // equivalente al anterior
UnMetodo(d);
```

### Delegados *multicast*

Un delegado puede ser referencia a más de un método. Para ello se usa el operador de suma:

```cs
MiDelegado d = UnMetodo;
d += OtroMetodo;
d += YOtroMas;
```

Todos los métodos asignados deben tener el mismo protocolo definido por el delegado. Para eliminar métodos de la variable se usa el operador de resta:

```cs
d -= OtroMetodo;
```

Si eliminamos todos los métodos de la variable, esta tendrá valor ***null***.

Al invocar usando esta variable, se ejecutarán todos los métodos que contiene, en orden de inclusión. El valor retornado es el correspondiente al último método ejecutado.

Cuando el delegado almacena métodos no estáticos, también guarda una referencia a las instancias a las que pertenecen.

El tipo de un delegado puede ser genérico. En todo caso, hay que cerrar el tipo al declarar la variable.

Es posible asignar métodos que tengan un tipo de retorno más específico que el definido en el delegado. Por otro lado, es posible pasar como argumento valores de tipos más específicos que los definidos en el método.

En definitiva, un delegado sigue la idea del método *callback*.

## Eventos

<!-- TODO -->

## Expresiones Lambda

Sirven para asignar una función *callback* a una variable. Internamente es igual que un delegado.

```cs
delegate int DeleSuma(int x, int y);

DeleSuma sum = (x, y) => x + y;
```

Si la función tiene un solo parámetro, se pueden obviar los paréntesis. Si no tiene parámetros se puede indicar simplemente una par de paréntesis vacíos. Si la función tiene más de una sentencia, se puede indicar un bloque entre llaves con el código deseado. La función lambda tiene acceso a las variables a las que tenga acceso el método en el que está definida; si las modifica, serán modificadas en el contexto externo (es como si la función lambda se expandiera como una macro *C* en cada invocación).

## Tratamiento de excepciones

Bloque `try` + cero (no tendría sentido) o más bloques `catch` + bloque `finally` opcional.

```cs
try
{
    // Código a proteger
}
catch(TipoExcepcion1 ex)
{
    // Tratamiento de la excepción
}
catch( ... ) { ... }
...
catch { ... }
finally
{
    // bloque opcional
}
```

El bloque `catch` toma como argumento el tipo de excepción. No es obligatorio indicar una variable junto a dicho tipo, aunque la necesitaremos si deseamos obtener información sobre la excepción levantada.

Se puede indicar una cláusula `catch` sin argumento (ni paréntesis) para atrapar **todas** las excepciones.

El bloque `finally` se ejecuta **siempre**, se produzca o no excepción, aunque exista un código que salte fuera del bloque `try` o `catch` (con `break`, `return`, etc.). Es útil para liberar recursos.

Si se ejecuta algún bloque `catch`, la ejecución sigue tras el `try`/`catch`, previa ejecución de `finally`.

Si no se ha tratado la excepción en ningún `catch` (o se ha levantado una nueva excepción), la ejecución retorna al *caller*, y allí se comprueba nuevamente si el código está dentro de un `try`/`catch`.

Cuando se produce una excepción, se compara con los tipos de los bloques `catch`, en orden, por lo que si tratamos dos (o más) tipos de excepción dentro de la misma jerarquía, se deben incluir en orden de especificidad (de más específicos a más generales).

Es posible lanzar una excepción mediante `throw`. Dentro de un bloque `catch` puede ir solo, sin especificar excepción, en cuyo caso, se vuelve a lanzar la misma excepción que estábamos tratando. De lo contrario, debemos pasarle una instancia de una excepción nueva. Podríamos relanzar la excepción así:

```cs
catch(UnaExcepcion ex)
{
    // ...
    throw ex;
}
```

Esto lanzará una excepción **igual** a la anterior, pero **no será la misma** excepción (el *stack trace* no es el mismo).

Todas las excepciones derivan de ***System.Exception***. Esta clase proporciona, entre otras, estas propiedades:

- ***StackTrace*** es un *string* con el *stack* de llamadas.
- ***Message*** es una descripción del error (*string*).
- ***InnerException*** es la excepción interior, si la hay, que causó la excepción actual. Esta excepción interior puede a su vez tener una ***InnerException***.

## Enumeraciones e iteradores
