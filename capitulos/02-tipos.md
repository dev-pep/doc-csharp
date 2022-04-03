# Tipos de datos

Las conversiones entre tipos pueden ser implícitas o explícitas (*casts*). Las conversiones implícitas no pueden darse si hay posible pérdida de información, por ejemplo de real a entero, o de entero a entero menor.

Tipos por valor: numéricos, ***char***, ***bool***, estructuras y enumeraciones.

Tipos por referencia: clases, *arrays*, delegados, interfaces, ***object*** y ***string***.

En un tipo por valor, una asignación crea una copia de la instancia, mientras que por referencia, la asignación crea una nueva referencia a dicha instancia.

Una referencia puede ser ***null***; un valor no. Acceder a un miembro de ***null*** levanta una excepción.

## Tipos numéricos

Los tipos *built-in* tienen tipos equivalentes en el *namespace* ***System***.

Enteros con signo:

| Tipo        | ***System*** | Sufijo  | Tamaño (bits) |
| :---------- | :----------- | :------ | :------------ |
| ***sbyte*** | ***SByte***  |         | 8             |
| ***short*** | ***Int16***  |         | 16            |
| ***int***   | ***Int32***  |         | 32            |
| ***long***  | ***Int64***  | ***L*** | 64            |

Enteros sin signo:

| Tipo         | ***System*** | Sufijo   | Tamaño (bits) |
| :----------- | :----------- | :------- | :------------ |
| ***byte***   | ***Byte***   |          | 8             |
| ***ushort*** | ***UInt16*** |          | 16            |
| ***uint***   | ***UInt32*** | ***U***  | 32            |
| ***ulong***  | ***UInt64*** | ***UL*** | 64            |

Reales:

| Tipo          | ***System***  | Sufijo  | Tamaño (bits) |
| :------------ | :------------ | :------ | :------------ |
| ***float***   | ***Single***  | ***F*** | 32            |
| ***double***  | ***Double***  | ***D*** | 64            |
| ***decimal*** | ***Decimal*** | ***M*** | 128           |

El tipo ***decimal*** posee una aritmética especialmente precisa para números en base 10. No hay conversión implícita entre reales, a excepción de ***float*** a ***double***.

### Literales numéricos

Los literales enteros pueden tener prefijo ***0x*** para hexadecimal, o ***0b*** para binario.

Los literales reales pueden tener punto decimal y/o notación exponencial (***e***).

Los caracteres alfabéticos en estos literales son *case insensitive*. Se pueden insertar guiones bajos en los literales numéricos a discreción.

Si el literal tiene punto decimal o símbolo exponencial, se interpreta directamente como ***double***. En caso contrario, se interpreta como entero, dentro del primer tipo donde haya espacio siguiendo la lista ***int***, ***uint***, ***long*** ***ulong***.

Se puede indicar explícitamente el tipo del literal utilizando el sufijo adecuado.

```cs
float f = 4.5;  // error
```

Esta sentencia levantaría una excepción, ya que no hay declaración implícita de ***double*** a ***float***.

La conversión (explícita) de real a entero trunca la parte decimal.

### Operadores

#### Operadores aritméticos e incrementales

Operadores aritméticos: `+` (unario y binario), `-` (unario y binario), `*`, `/` y `%`. Están definidos para todos los tipos numéricos excepto los de 8 y 16 bits.

Operadores incrementales: `++` y `--` (*prefix* y *postfix*).

La división entre enteros redondea hacia 0 (trunca parte decimal).

En la aritmética sobre enteros, el *overflow* se produce silenciosamente sin levantar excepción. Simplemente se ignoran los *bits* que no se pueden almacenar, y el número da la vuelta (*wrap around*).

Si queremos que el *overflow* levante excepción, hay que usar `checked`:

```cs
c = checked(a + b);

checked
{
    // Código...
}
```

Si el *overflow* está configurado globalmente (opciones avanzadas de compilación en *Visual Studio*) para levantar excepción, disponemos de `unchecked`, que hace todo lo contrario.

Los tipos de 8 y 16 bits no disponen de operadores aritméticos (`+`, `-`, `*` y `/`), con lo que son convertidos a enteros mayores. Dado que el resultado de un operador es del tipo del operando con tipo superior, el resultado no se puede almacenar en un tipo de 8 o 16 bits, con lo que debe convertirse explícitamente:

```cs
short x = 5;
short y = 10;
short z = x + y;  // error
short z = (short)(x + y);  // ok
```

#### Operadores *bitwise*

Operan sobre enteros: `~` (complemento, unario), `&` (*and*), `|` (*or*), `^` (*xor*), `>>` (*shift right*) y `<<` (*shift left*).

### Valores reales especiales

Los tipos ***float*** y ***double*** disponen de valores especiales para *NaN* o infinito, a través de las propiedades ***NaN***, ***PositiveInfinity*** y ***NegativeInfinity***.

El valor *NaN* no se evalúa igual a ningún otro valor numérico, ni siquiera otro *NaN*.

Los tipos mencionados también disponen de los métodos `IsNaN()`, `IsInfinity()`, `IsPositiveInfinity()` o `IsNegativeInfinity()`.

## Tipo booleano

El tipo ***bool***, equivalente a ***System.Boolean***, puede tener valores ***true*** o ***false***.

### Operadores de comparación

El operador de igualdad es `==`, y el de diferencia `!=`. Los de comparación son `>`, `<`, `>=` y `<=`.

Al comparar tipos por referencia, la comparación se basa en la referencia, y no en el valor de los objetos referenciados.

Las comparaciones entre números reales se debería hacer con prudencia (por los redondeos internos).

### Operadores condicionales

Son `!` (*not*), `&&` (*and*) y `||` (*or*). La diferencia con los correspondientes *bitwise* es que no buscan un valor numérico sino un valor *booleano*, con lo cual estos cortocircuitan y los *bitwise* no.

Operador ternario:

```cs
v = x ? y : z;
```

## Caracteres y *strings*

El tipo ***char*** (equivalente a ***System.Char***) representa un carácter *Unicode*. Ocupa 2 *bytes*, ya que está representado mediante *UTF-16*. Un literal carácter se expresa entre comillas simples.

Posibles secuencias de escape: ***\\'***, ***\\"***, ***\\\\***, ***\\0***, ***\\a***, ***\\b***, ***\\f***, ***\\n***, ***\\r***, ***\\t***, ***\\v***.

Por otro lado, la secuencia ***\\u*** o ***\\x*** permite indicar un *code point* de *Unicode* mediante sus cuatro dígitos hexadecimales (*case insensitive*).

Es posible la conversión implícita entre ***char*** y un tipo numérico que tenga capacidad para almacenar un ***ushort***.

El tipo ***string*** (equivalente a ***System.String***) es una secuencia inmutable de caracteres. El literal se expresa entre comillas dobles. Acepta la comparación mediante los operadores de igualdad y diferencia. No admite los operadores `<` y `>`; en tal caso hay que usar el método `CompareTo()` del tipo ***string***.

Admite las secuencias de escape mencionadas para caracteres.

Por otro lado un *verbatim string* no tiene en cuenta tales secuencias (si se desea el carácter de las dobles comillas se puede indicar este dos veces). Este tipo de *strings* se construyen con el prefijo ***@***, y pueden ser multilínea.

Estas dos sentencias son equivalentes:

```cs
a = "Esto es un \"string\".";
a = @"Esto es un ""string"".".
```

Los *strings* se pueden concatenar con el operador `+`. Si uno de los operandos no es *string*, se convertirá a *string* (mediante su método `ToString()`).

También existen los *interpolated strings*. Estos, con un prefijo ***\$*** admiten expresiones entre llaves (***{}***). La expresión puede ir complementada con un *string* de formato, separándolo de la expresión con dos puntos (***:***). Para incluir un carácter de llaves en sí, se debe escribir repetido.

Si el *string* es *interpolated* ***y*** *verbatim*, el signo ***\$*** debe ir antes del ***@***.

Es posible acceder a los elementos del *string* mediante índice entre corchetes (***[N]***). El índice empieza en 0. Para buscar un carácter dentro del *string* existen los métodos `IndexOf()` y `LastIndexOf()`. Para buscar *substrings* disponemos de `Contains()`, `StartsWith()` y `EndsWith()`.

Los métodos que manipulan *strings*, lo que hacen es retornan un *string* nuevo, ya que estos son inmutables:

- `Substring()` extrae un *substring*.
- `Insert()` y `Remove()` insertan y eliminan un fragmento en una posición concreta.
- `PadLeft()` y `PadRight()` añaden espacio en blanco.
- `TrimStart()` y `TrimEnd()` eliminan espacio en blanco.

Existen otros métodos como `ToUpper()`, `ToLower()`, `Split()` (basado en delimitadores) o `Join()`.

## *Arrays*

Se accede a sus elementos a través de índice entre corchetes (***[N]***). Los elementos están siempre almacenados en bloques contiguos de memoria (acceso eficiente).

Declaración e inicialización:

```cs
int[] a = new int[5];  // sin inicializar
int[] a = new int[] {1, 2, 3, 4, 5};  // inicializado
int[] a = new int[5] {1, 2, 3, 4, 5};  // equivale al anterior
int[] a = {1, 2, 3, 4, 5};  // equivale también al anterior
```

Los elementos son de lectura y escritura por defecto. El tamaño de un *array* es constante. El índice empieza en 0.

En la inicialización de los *arrays*, puede haber una coma tras el último elemento de la lista.

Los *arrays* son de un tipo derivado de ***System.Array***. Esto incluye propiedades como ***Length*** y ***Rank*** (número de dimensiones).

Existen además varios métodos estáticos útiles:

- `BinarySearch()` para búsqueda en *array* ordenado.
- `IndexOf()`, `LastIndexOf()`, `Find()`, `FindIndex()`, `FindLastIndex()` para búsquedas en *arrays* no ordenados.
- `Sort()` para ordenación de *arrays*.
- `Copy()` para copia de *arrays*.

El valor por defecto de un *array* no inicializado es el resultante de establecer a 0 todos los *bits* del valor. Para tipos por referencia, ***null***.

Para dar valor a un *array* en una sentencia que no sea una declaración:

```cs
a = new int[5];  // todo ceros
a = new int[] {1, 2, 3, 4, 5};
a = new int[5] {1, 2, 3, 4, 5};
```

### *Arrays* multidimensionales

#### *Arrays* cuadrados

Las dimensiones interiores de estos *arrays* tienen un tamaño constante.

Para indicar un tipo multidimensional cuadrado se usan comas para separar las dimensiones.

```cs
int[,] a = new int[3,4];  // a ceros
int[,] a = new int[,] {  // inicializado
    {1, 2, 3, 4},
    {2, 3, 4, 5},
    {3, 4, 5, 6}
};
int[,] a = new int[3,4] {  //equivale al anterior
    {1, 2, 3, 4},
    {2, 3, 4, 5},
    {3, 4, 5, 6}
};
int[,] a = {  // equivale también al anterior
    {1, 2, 3, 4},
    {2, 3, 4, 5},
    {3, 4, 5, 6}
};
```

Para dar valor al *array* en una sentencia que no es declaración:

```cs
a = new int[3,4];  // a ceros
a = new int[,] {  // valores
    {1, 2, 3, 4},
    {2, 3, 4, 5},
    {3, 4, 5, 6}
};
a = new int[3,4] {  //equivale al anterior
    {1, 2, 3, 4},
    {2, 3, 4, 5},
    {3, 4, 5, 6}
};
```

En este ejemplo, la variable ***a*** debe estar declarada como de tipo ***int[,]***.

El método `GetLength()` del *array* retorna el número de elementos de la dimensión especificada (empezando por la dimensión 0). Naturalmente, pueden declararse más de dos dimensiones.

#### *Jagged arrays*

Estos *arrays* pueden ser irregulares, en cuanto a que las dimensiones interiores pueden tener un número variable de elementos. Solo la dimensión más externa (la primera) se debe indicar.

Estos *arrays* no se indican separando las dimensiones con comas, sino indicando cada dimensión en su par de corchetes correspondiente.

```cs
int[][] a = new int[3][];
```

En este caso, la variable ***a*** será un *array* de 3 elementos. Cada elemento será a su vez un *array* de tipo ***int[]***, de tal modo que cada uno de estos *arrays* podrá tener el tamaño que quiera, independientemente de los demás. Para inicializar estos *arrays*:

```cs
a[0] = new int[10];
a[1] = new int[] {1, 2, 3, 4, 5, 6};
a[2] = new int[3] {1, 2, 3};
```

Los *jagged arrays* no se pueden inicializar en la declaración.

#### Rizando el rizo

Es posible combinar ambos estilos de *array*:

```cs
int[][,][][] a = new int[4][,][][];
char[,,][][] c = new char[2,5,3][][];
```

En todo caso, a la hora de especificar el tamaño, siempre hay que especificarlo para el primer par de corchetes, ya sea una dimensión o un *array* cuadrado. El resto de pares de corchetes no puede especificar tamaño.
