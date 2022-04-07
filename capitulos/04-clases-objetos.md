# Clases y objetos

Las clases se definen mediante `class` y un nombre.

## Elementos de una clase

La clase, así como sus atributos, pueden ser cualificados como `public`, `internal`, `abstract`, `sealed`, `static`, `unsafe` y `partial` (antes de `class`).

La clase también puede tener parámetros de tipo genérico, clase base e interfaces (después del nombre de la clase).

La clase puede contener (miembros): métodos, propiedades, indexadores, constantes, eventos, campos, constructores, deconstructor, operadores *overloaded*, tipos anidados y un *finalizer*.

### Campos

Son variables que pertenecen a la clase.

Se puede definir como `readonly` (solo lectura), lo cual permite establecer su valor únicamente durante la construcción (inicializado en la declaración, o dentro del cuerpo del constructor). Un convenio de estilo es marcar siempre como solo lectura los campos privados que se inicializan y no se reasignan nunca.

Un campo no inicializado tendrá el valor por defecto (0, '\\0', ***null*** o ***false***). La inicialización se ejecuta antes que el constructor.

```cs
static readonly int piernas = 4, ojos = 2;
```

En este ejemplo ambos campos son enteros, estáticos y de solo lectura.

### Métodos

Funciones que pertenecen a la clase.

Los métodos que consisten en una sola expresión se pueden indicar de esta forma (método de expresión única):

```cs
int Foo(int x) => x * 2;
```

Equivale a:

```cs
int Foo(int x) { return x * 2; }
```

También se puede hacer con métodos sin valor de retorno:

```cs
void Foo(int a, int b) => Console.WriteLine(a + b);
```

Para **sobrecargar un método** solo hay que definirlo con una lista de parámetros distinta.

Los métodos pueden anidarse.

### Constructores

Son métodos cuyo nombre coincide con el de la clase. No se debe indicar tipo de retorno (no retorna nada explícitamente). Normalmente es público, aunque en casos muy específicos podría interesarnos que no lo fuese.

Se puede escribir como método de expresión única.

Se pueden sobrecargar los constructores.

Un constructor puede llamar a otra versión sobrecargada, mediante `this()`:

```cs
public MiClase(int n) { /* ... */ }
public MiClase(string s, int n): this(n + 10) { /* ... */ }
```

En este caso, el segundo constructor llama al primero. Cuando se hace así, el constructor llamado se ejecuta antes que el llamante. Como se ve en el ejemplo, dentro de la lista de argumentos de `this()` (constructor llamado), los parámetros del constructor llamante son accesibles, con lo que se pueden indicar expresiones que incluyan dichos parámetros. Estas expresiones no pueden usar la referencia ***this*** (referencia a la instancia) pero sí pueden invocar métodos estáticos de la clase.

Si no se define constructor, *C#* genera un constructor por defecto sin parámetros.

### Deconstructores

Un constructor suele tomar una serie de valores (argumentos) y asignarlos a los campos del objeto. El deconstructor hace lo contrario: toma ciertos campos y los retorna para que sean asignados a variables concretas.

El método debe llamarse `Deconstruct()` y tener uno o más parámetros de salida. No debe retornar nada. Por su forma de usarse, debería ser público.

```cs
class Coche
{
    readonly string marca, modelo;

    public Coche(string mrc, string mod)  // constructor
    {
        marca = mrc;
        modelo = mod;
    }

    public void Deconstruct(out string mrc, out string mod)  // deconstructor
    {
        mrc = marca;
        mod = modelo;
    }
}
```

Supongamos que tenemos un objeto ***o*** del tipo ***Coche***, y queremos usar el deconstructor para establecer los valores de la marca y modelo de ***o*** en las variables ***marca*** y ***modelo***. Podríamos hacerlo así:

```cs
string marca, modelo;
o.Deconstruct(out marca, out modelo);
```

O directamente declarando las variables:

```cs
o.Deconstruct(out string marca, out string modelo);
```

Disponemos de una sintaxis específica para los deconstructores. El ejemplo puede escribirse así:

```cs
(string marca, string modelo) = o;
```

O si las variables están ya declaradas previamente:

```cs
string marca, modelo;
(marca, modelo) = o;
```

Si queremos declarar las variables sin especificar su tipo (tipo implícito):

```cs
var (marca, modelo) = o;
```

> Del mismo modo que el constructor no tiene por qué asignar cada parámetro a un campo del objeto, el deconstructor no tiene por qué asignar el valor de un campo a cada parámetro de salida.

### Inicializadores de objeto

Es posible instanciar un objeto pasándole una especie de "literal objeto" que rellene algunos o todos sus campos públicos.

```cs
class Coche
{
    public string Marca, Modelo;

    public Coche() { Marca = "Seat"; Modelo = "Ibiza"; }

    public Coche(string marca) { Marca = marca; Modelo = "Ibiza"; }
}

Coche c1 = new Coche();
Coche c2 = new Coche { Marca = "Renault", Modelo = "Clio" };
Coche c3 = new Coche("Renault") { Modelo = "Clio"};
```

En el caso de ***c1*** (por definición los paréntesis son obligatorios en la instanciación) se ejecuta el constructor sin parámetros. En el caso de ***c2*** no es obligatorio incluir los paréntesis, ya que hay un inicializador a continuación; se llamará también al constructor sin parámetros. En el caso de ***c3***, se llamará al constructor con el parámetro *string*.

Si el inicializador da valor a un campo que también es inicializado por el constructor correspondiente, el valor de inicializador prevalece.

### La referencia this

La variable ***this*** hace referencia a la instancia actual.

Por otro lado, cuando un método tiene una variable local o un parámetro con el mismo nombre que un campo de la clase, dicho campo queda oculto. Para referirnos a dicho campo se puede hacer a través de ***this.campo***.

Los métodos estáticos no disponen de esta referencia.

### Propiedades

Las propiedades son campos que llevan lógica asociada (*getter*, *setter*).

```cs
public class Coche
{
    string marca;  // el campo asociado (no obligatorio)

    public string Marca  // la propiedad
    {
        get { return marca; }
        set { marca = value; }
    }
}
```

El *accessor* `set` recibe implícito el parámetro ***value***.

El acceso a las propiedades se hace del mismo modo que a los campos. Externamente no sabemos si se trata de un campo o de una propiedad.

Si la propiedad define solo `get`, es de solo lectura. Si define solo `set` es de solo escritura (raramente usado). El campo asociado no es obligatorio: los *accesors* pueden basarse en otros datos o cálculos.

Es posible definir estos *accessors* mediante la sintaxis de expresión única:

```cs
get => marca;
set => marca = value;
```

En el caso de que los *accesors* simplemente establezcan el valor recibido y retornen el valor del campo asociado, se puede hacer así:

```cs
public string Marca { get; set; }
```

En este caso se creará automáticamente un campo asociado privado ***marca*** (en minúsculas por ser privado).

Esta sintaxis puede ir acompañada de un valor inicial:

```cs
public string Marca { get; set; } = "Seat";
```

En este caso, la propiedad puede ser de solo lectura.

Para restringir el acceso, se puede definir el *setter* (o el *getter*) como privado o protegido.

### Indexadores

Sirven para acceder al objeto mediante índices entre corchetes (***[]***). En este caso, el índice no está restringido a enteros (como en el caso de *strings* o *arrays*).

Para definir un indexador lo haremos a través de un método llamado `this()`, dentro del cual definiremos los *accessors* apropiados.

```cs
public char this[int n]
{
    get { /* ... */ }
    set { /* ... */ }
}
```

Dentro de los *accesors* tenemos acceso a los parámetros del índice (en este caso el entero ***n***). Lo más inmediato es que los *accesors* trabajen sobre un campo *array* de la clase (normalmente privado). Pero, como se ha visto, esto no es obligatorio (pueden basarse en otros datos o cálculos).

Es posible definir varios indexadores, con tipos de índice distintos. Puede haber varios índices, separados por comas, cada uno de un tipo concreto.

Si se omite el *setter*, el indexador es de solo lectura. En ese caso se puede usar la sintaxis:

```cs
public string this[int n] => campo[n];
```

Donde ***campo*** es un posible campo relacionado.

### Constantes

Una constante es un campo estático. Su valor no puede cambiar. Puede ser de cualquier tipo numérico *built-in*, ***bool***, ***char*** ***string*** o enumeración. Para ello se debe indicar `const` antes del tipo. Es obligatorio inicializarla en la declaración.

Una variable local dentro de un método también puede declararse constante.

### Constructores estáticos

Un constructor estático se ejecuta una sola vez por clase, no por instancia. Solo puede definirse uno. Debe ser sin parámetros y con la indicación `static`.

```cs
class Coche
{
    static Coche() { /* ... */ }
}
```

Este constructor es llamado una única vez en el programa. Esto sucede en la primera instanciación de la clase o en el primer acceso a un miembro estático de la misma, lo que suceda antes.

Los inicializadores de campos estáticos se ejecutan antes que el constructor estático.

### Clases estáticas

Una clase marcada como `static` solo puede contener miembros estáticos, y no puede ser clase base para otra clase.

### Finalizador

El finalizador (*finalizer*) se ejecuta justo antes de que el *garbage collector* libere la memoria que ocupa el objeto. Este método tiene el mismo nombre que la clase, con prefijo ***~***.

```cs
class Coche
{
    ~Coche() { /* ... */ }
}
```

## Herencia

Una clase (subclase o clase derivada) puede heredar de una (solo una) clase base:

```cs
class Base { /* ... */ }
class Deriv: Base
{
    // Definición
}
```

### Polimorfismo

Una variable de un tipo concreto puede referenciar a un objeto de ese tipo o de tipos derivados.

La conversión de un tipo a otro dentro de la misma jerarquía tiene estas dos variantes:

- *Upcast*: la conversión a un tipo más arriba (tipo base o superior a este) en la jerarquía puede ser implícita o explícita, y nunca da error.
- *Downcast*: la conversión a un tipo más abajo (tipo derivado o inferior a este) debe ser obligatoriamente explícita, y puede levantar una excepción.

La conversión no afecta al objeto referenciado sino a la referencia en sí.

```cs
Base b = new Base();
Deriv d = new Deriv();

Base base1 = b;
Base base2 = d;
Deriv deriv1 = b;  // error de compilación
Deriv deriv2 = (Deriv)b;  // compila pero levanta excepción
Deriv deriv3 = d;
Deriv deriv4 = base2;  // error de compilación
Deriv deriv5 = (Deriv)base2;
```

Estudiemos el ejemplo. La variable ***b*** es una referencia a un objeto de tipo ***Base***, mientras que ***d*** es una referencia a un objeto ***Deriv***.

- ***base1*** es una referencia nueva al objeto de tipo ***Base***. En este caso, tanto el objeto referenciado como la referencia son de tipo ***Base***, con lo que todo está bien.
- ***base2*** es una referencia de tipo ***Base*** a un objeto de tipo ***Deriv***. En este caso es un *upcast*. El objeto puede poseer miembros que no son accesibles desde la referencia. Si quisiéramos acceder a todos los miembros del objeto, deberíamos realizar un *downcast* sobre la variable: `(Deriv)base2`.
- ***deriv1*** es un *downcast* implícito, lo cual no está permitido, y se produce un error de compilación.
- ***deriv2*** es un *downcast* explícito. En este caso, la compilación es correcta, pero en la ejecución nos encontramos una referencia de tipo ***Deriv*** a un objeto de tipo ***Base***, lo cual levanta excepción. El problema es que el tipo de la referencia puede tener miembros de los que carece el objeto. Es por ello que una variable puede referenciar a objetos de su mismo tipo y derivados, pero no de tipo superior en la jerarquía.
- ***deriv3*** es simplemente una nueva referencia al objeto de tipo ***Deriv***.
- ***deriv4*** es un *downcast* implícito, con lo que se producirá error de compilación, ya que aunque el tipo referenciado por ***base2*** es de tipo ***Deriv***, la variable ***base2*** es de tipo ***Base***.
- ***deriv5*** es algo más complejo. Recordemos que aunque ***base2*** es de tipo ***Base***, está apuntando a un objeto de tipo ***Deriv***. Por lo tanto, ***deriv5***, de tipo ***Deriv*** acaba referenciando a un objeto de tipo ***Deriv*** también, con lo cual está todo correcto.

El operador `as` intenta realizar un *downcast*. Si sale bien, retorna una referencia a ese tipo derivado. Si no se puede hacer, es decir, si el resultado del *downcast* fuese una variable referenciando a un objeto de tipo base o superior, entonces retorna ***null***:

```cs
Deriv deriv = b as Deriv;
```

En este caso intentamos que ***deriv*** sea una referencia de tipo ***Deriv*** al objeto ***Base*** al que referencia ***b***. Esto no es legal, con lo que en este caso, ***deriv*** recibe el valor de ***null***.

Por otro lado, el operador `is` comprueba si el objeto referenciado (no la referencia en sí) puede ser convertido al tipo especificado, es decir, si el objeto referenciado es del tipo especificado o de una clase derivada de este.

```cs
resultado = b is Deriv;  // falso
resultado = d is Base;  // verdadero
resultado = base2 is Deriv;  // verdadero
```

También se usa para comprobar si el objeto referenciado implementa la interfaz indicada.

### Funciones virtuales y ocultación de métodos

Una clase derivada puede definir métodos que tengan el mismo nombre que un método de la clase base. Si la lista de parámetros es la misma, se trata simplemente de una sobrecarga de métodos. Pero si el método de la clase derivada tiene el mismo nombre y la misma lista de parámetros, se trata de un *override* o de una ocultación (*hiding*) del método.

#### *Override*

Para *override* (invalidar) un método de una clase base, debemos declarar el método como `override` antes del tipo de retorno. En la clase base, el método debe ser declarado como `virtual` u `override`. Si estamos definiendo un método que no existe en ninguna clase base (o no hay tal clase base) y queremos que una clase derivada lo *override*, se debe declarar como `virtual` (no `override`).

```cs
class Base
{
    public virtual int Method(int i, char c) { /* ... */ }
}

class Deriv: Base
{
    public override int Method(int i, char c) { /* ... */ }
}
```

En el caso de la invalidación de un método mediante otro en la clase derivada, ambos deben coincidir no solo en sus listas de parámetros, sino además en su tipo de retorno y en su accesibilidad.

En este caso, al crearse una instancia de la clase derivada, esta contendrá únicamente el método definido en esta, quedando invalidado el de la clase base.

```cs
Base b = new Deriv();
Deriv d = new Deriv();
b.Method();  // método de la derivada
d.Method();  // método de la derivada
```

En este ejemplo, tanto `b.Method()` como `d.Method()` ejecutarán el método definido en la derivada, independientemente del tipo de la referencia.

El método en la clase derivada pude acceder a la implementación del método de la clase base mediante `base` (véase más adelante).

#### *Hiding*

Otra posibilidad es la ocultación. En este caso, el método de la derivada oculta el método de la base sin llegar a invalidarlo. Simplemente se definirá un método con el mismo nombre que en la base y con la misma lista de parámetros (en este caso, pueden tener tipos de retorno y/o accesibilidad distintos).

```cs
class Base
{
    public int Method(int i, char c) { /* ... */ }
}

class Deriv: Base
{
    public new string Method(int i, char c) { /* ... */ }
}
```

Obsérvese que se declara el método de la clase derivada con `new`. Si bien no es obligatorio, sirve para confirmar al compilador que efectivamente deseamos ocultar el método de la clase base (si no indicamos `new`, se genera un *warning*).

En este caso, la instancia creada de la clase derivada almacenará ambos métodos (independientemente de si tienen el mismo tipo de retorno o no) en la instancia creada. El método llamado dependerá del tipo de la referencia.

```cs
Base b = new Deriv();
Deriv d = new Deriv();
b.Method();  // método de la clase base
d.Method();  // método de la derivada
```

También pueden ocultarse **campos** usando este método.

Normalmente la ocultación de métodos es poco usada. Los casos de uso más frecuentes utilizan la invalidación (*overriding*).

### Clases y miembros abstractos

Un método abstracto no proporciona implementación. Se puede indicar un simple par de llaves vacío, o simplemente declarar el método. El método debe declararse como `abstract`. Las propiedades también pueden ser abstractas (los campos no).

Una clase que incluya métodos (o propiedades) abstractos debe ser declarada a su vez como `abstract`. Una clase abstracta no se puede instanciar, sino que deberá servir como clase base de otra clase. Si la subclase no define todos los miembros abstractos de la base también debe ser abstracta.

### Sellado

Un método marcado con `override` puede también marcarse como sellado (`sealed`), lo cual impide que sucesivas subclases lo invaliden (*override*). Si queremos que todos los métodos `override` de la clase estén sellados, se puede marcar directamente la clase como `sealed`. En una clase sellada no puede haber miembros virtuales (`virtual`).

Si la clase está declarada como `sealed`, no puede usarse como clase base. Es por ello que no tiene sentido declarar miembros protegidos. Si se hace, se genera un *warning*.

### base

El *keyword* `base` es similar a `this`, aunque en este caso se refiere a la clase base.

Se puede usar, por ejemplo, para hacer referencia a un método de la clase base que esté *overriden* o *hidden*: `base.Metodo()`.

También se puede utilizar para llamar al constructor de la clase base:

```cs
class Coche: Vehiculo
{
    public Coche(): base() { /* Definición */ }
    public Coche(int x): base(x) { /* Definición */ }
}
```

En estos casos, el constructor base es llamado antes que el especializado.

Si el constructor no llama explícitamente al constructor de la clase base, el constructor sin parámetros de esta es llamado implícitamente. En ese caso, si la clase base no tiene un constructor sin parámetros accesible, se produce un error de compilación.

El orden de inicialización de campos y ejecución de constructores es el siguiente:

1. Desde la subclase hacia arriba:
    1. Se inicializan los campos.
    1. Se evalúan los argumentos a las llamadas constructores de la clase base.
1. Se ejecutan los constructores desde la clase superior hacia abajo.

### Sobrecarga y resolución

Cuando la sobrecarga difiere en tipos dentro de la misma jerarquía, el más específico tiene preferencia. Suponiendo que el tipo ***Deriv*** sea una subclase de ***Base***:

```cs
class Coche
{
    public void Metodo(Base o) { ... }
    public void Metodo(Deriv o) { ... }
}

Coche o = new Coche();
o.Metodo(new Deriv());
```

En este caso, aunque la llamada al método podría encajar con la versión que toma un argumento de tipo ***Base***, se ejecutará el otro, ya que es más específico.

# Tipo object

El tipo ***object*** pertenece al *namespace* ***System*** (***System.object***).
