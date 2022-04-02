# Introducción a C#

El punto de entrada de un programa *C#* (*.exe*, no una biblioteca *.dll*) es la primera de las sentencias *top-level*. En su defecto, el método estático `Main()` de una de las clases definidas. Solo una de las clases puede definir tal método.

Identificador: palabra de caracteres *Unicode*, *case sensitive*, empezando por letra o guión bajo (***\_***). Por convenio, los parámetros, variables locales y campos privados estarán en *camelCase*. El resto, en *PascalCase*.

Una línea lógica se puede partir en varias líneas físicas, siempre que no se partan los identificadores.

Comentarios: *single line* (`//`) o *multi line* (`/**/`).

Todo valor es una instancia de una clase (tipo), incluyendo los tipos predefinidos (*built-in*) como ***int***, ***string***, ***bool***, etc.

Los tipos tienen propiedades o atributos (*data members*) y/o métodos (*function members*). Las clases pueden disponer de un constructor con el mismo nombre que la clase.

Un miembro estático (`static`) es accesible desde todas las instancias. Un método no estático puede acceder a todos los atributos, tanto estáticos como no estáticos. Un método estático no puede acceder a atributos no estáticos ni llamar a métodos no estáticos de la clase.

El acceso a un miembro estático (método o atributo) se debe hacer a través del nombre de la clase, mientras que el acceso a un miembro no estático se hace a través de la instancia.

El modo de acceso por defecto es privado. Si no, debemos especificar `public`.

El método de entrada `Main()` debe retornar ***void*** o ***int***. Puede recibir como argumento, opcionalmente, un *array* de *strings* con los argumentos al programa.

```cs
static void Main(string[] args) {
    /* ... */
}
```
