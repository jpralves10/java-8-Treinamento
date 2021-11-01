# 6. Generics

## Exam Objectives

*Create and use a generic class.*

### Generics

Sem os genéricos, você pode declarar uma lista como esta:

```
List list = new ArrayList();
```

Como uma lista, por padrão, aceita objetos de qualquer tipo, você pode adicionar elementos de diferentes tipos a ela:

```
list.add("a");
list.add(new Integer(1));
list.add(Boolean.TRUE);
```

E obtenha valores como este:

```
String s = (String) list.get(0);
```

Isso pode levar a erros de tempo de execução feios e mais complexidade. Por causa disso, os genéricos foram adicionados ao Java 5 como um mecanismo de verificação de tipo.

Um genérico é um tipo declarado entre colchetes angulares, após o nome da classe. Por exemplo:

```
List<String> list = new ArrayList<String>();
```

Ao adicionar o tipo genérico à Lista, estamos dizendo ao COMPILADOR para verificar se apenas os valores de String podem ser adicionados à lista:

```
list.add("a"); // OK
list.add(new Integer(1)); // Compile-time error
list.add(Boolean.TRUE); // Compile-time error
```

Como agora temos apenas valores de um tipo, podemos obter elementos com segurança sem um cast:

```
String s = list.get(0);
```

É importante enfatizar que os genéricos são coisa do compilador. Em tempo de execução, o Java não conhece os genéricos.

Sob o capô, o compilador insere todas as verificações e conversões para você, mas no tempo de execução, um tipo genérico é visto pelo Java como um tipo java.lang.Object.

Em outras palavras, o compilador verifica se você está trabalhando com o tipo certo e, a seguir, gera o código com o tipo java.lang.Object.

O processo de substituição de todas as referências a tipos genéricos por Object é chamado de eliminação de tipo.

Por isso, em tempo de execução, List <String> e List <Integer> são iguais, porque a informação de tipo foi apagada pelo compilador (eles são vistos apenas como List).

Os genéricos funcionam apenas com objetos. Algo como o seguinte não compila:

```
List<int> list = new ArrayList<int>();
```

Finalmente, uma classe que aceita genéricos, mas é declarada sem um, é considerada um tipo bruto:

```
// Raw type
List raw = new ArrayList();
// Generic type
List<String> generic = new ArrayList<String>();
```


### The Diamond Operator

Desde Java 7, em vez de especificar o tipo genérico em ambos os lados da atribuição:

```
List<List<String>> generic = new ArrayList<List<String>>();
```

Podemos simplificar a criação do objeto apenas escrevendo:

```
List<List<String>> generic = new ArrayList<>();
```

A forma abreviada do lado direito é chamada de operador de diamante (porque se parece com um diamante).

Mas tenha cuidado. O exemplo acima é diferente de:

```
// Without the diamond operator, the raw type is used
List<List<String>> generic = new ArrayList();
```

Você só pode usar o operador diamante se o compilador puder inferir os tipos de parâmetro do contexto. A boa notícia é que no Java 8, a inferência de tipo foi melhorada:

```
void testGenericParam(List<String> list) { }

void test() {
    // In Java 7, this line generates a compile error
    // In Java 8, this line compiles fine
    testGenericParam(new ArrayList<>());
}
```


### Generic Classes

Olhando para a definição de List e alguns de seus métodos, podemos ver como esta classe foi projetada para funcionar com genéricos:

```
public interface List<E> extends Collection<E> {
    boolean add(E e);
    Iterator<E> iterator();
}
```

Podemos ver como um tipo genérico é definido para classes e interfaces. É apenas uma questão de declarar um parâmetro de tipo próximo ao nome da classe (ou interface).

A propósito, E é apenas um identificador, como uma variável nomeada. Pode ser o que você quiser. No entanto, a convenção é usar letras maiúsculas simples. Algumas letras comuns são:

* E for element
* K for a map key
* V for a map value
* T, U for data types

Dessa forma, quando uma lista é declarada assim:

```
List<String> list = null;
```

E recebe o valor de String e, onde quer que o tipo E seja definido, String será usado.

Portanto, classes genéricas nos dão muita flexibilidade.

Por exemplo, considere esta classe:

```
class Holder {
    private String s;
    public Holder(String s) {
        this.s = s;
    }
    public String getObject() {
        return s;
    }
    public void printObject() {
        System.out.println(s);
    }
}
```

Não há nada de errado com isso, mas ele só aceita objetos do tipo String. E se mais tarde precisarmos de uma classe como essa, mas que funcione com tipos inteiros? Criamos uma versão inteira?

```
class IntegerHolder {
    private Integer s;
    public Holder(Integer s) {
        this.s = s;
    }
    public Integer getObject() {
        return s;
    }
    public void printObject() {
        System.out.println(s);
    }
}
```

O código duplicado parece errado. Uma versão do objeto? Não, obrigado, precisaremos adicionar moldes em todos os lugares.

Os genéricos nos ajudam em casos como este. Basta declarar um parâmetro de tipo:

```
class Holder<T> {
    // ...
}
```

E o tipo genérico T estará disponível em qualquer lugar da classe:

```
class Holder<T> {
    private T t;
    public Holder(T t) {
        this.t = t;
    }
    public T getObject() {
        return t;
    }
    public void printObject() {
        System.out.println(t);
    }
}
```

Agora, quando uma instância é criada, apenas especificamos o tipo de T para essa instância:

```
Holder<String> h1 = new Holder<>("Hi");
Holder<Integer> h2 = new Holder<>(1);
String s = h1.getObject();
```

Se não especificarmos um parâmetro de tipo, usaremos o tipo bruto (que usa o tipo de objeto):

```
Holder h3 = new Holder("Hi again");
Object o = h3.getObject();
```

Se precisarmos, podemos ter mais de um parâmetro de tipo:

```
class Holder<T, U> {
    // ...
}
```

### Generic Methods

Também podemos declarar parâmetros de tipo em qualquer método (não para toda a classe). Mas a sintaxe é um pouco diferente, por exemplo:

```
class Utils {
    public static <T> void print(T t) {
        System.out.println(t);
    }
}
```

Isso define um método que recebe um argumento do tipo T. Aqui estão mais dois exemplos de métodos genéricos:

```
<T> void genericMethod1(List<T> list) { }
<T, U> T genericMethod2(U u) {
    T t = null;
    return t;
}
```

Quando um método declara seu próprio tipo genérico, ele deve ser especificado antes do tipo de retorno (em contraste com as classes, que o declaram após o nome da classe).

Para chamar o método do primeiro exemplo, você pode fazer isso normalmente:

```
Utils().print(10);
```

Ou especificando explicitamente o tipo entre o ponto e o nome do método:

```
Utils().<Integer>print(10);
```


### Wildcards

Os genéricos são úteis em muitos casos, mas não em todos. Temos dois problemas principais.

Você poderia pensar que, como ArrayList implementa List, e como String é uma subclasse de Object, algo assim está bem:

```
List<Object> list = new ArrayList<String>();
```

Mas não compila. Um ArrayList <String> não pode ser convertido em List <Object> porque, ao trabalhar com genéricos, você não pode atribuir um tipo derivado a um tipo base; ambos os tipos devem ser iguais (explicitamente ou usando o operador diamante).

Pense nisso desta forma: uma lista do tipo List <Object> pode conter instâncias de Object e suas subclasses. Em outras palavras, a lista pode conter qualquer tipo de objeto, não apenas strings. Portanto, você pode ter uma lista de strings e inteiros, por exemplo, o que viola claramente a segurança de tipo.

Mas se você alterar a declaração para usar um parâmetro curinga:

```
List<?> list = new ArrayList<String>();
```

Ele irá compilar.

O tipo curinga ilimitado (<?>) Significa que o tipo da lista é desconhecido para que possa corresponder a QUALQUER tipo.

Na verdade, você pode considerar de certa forma List <?> Como a superclasse de todas as Listas, já que pode atribuir qualquer tipo de Lista:

```
List<String> stringList = new ArrayList<>();
List<Integer> intList = new ArrayList<>();
// No problem
List<?> unknownTypeList = stringList;
// No problem either
List<?> unknownTypeList = intList;
for(Object o : unknownTypeList) { // Object?
   System.out.println(o);
}
```

Como o compilador não conhece o tipo dos elementos de List <?>, Temos que usar Object para garantir que não haverá nenhum problema em tempo de execução.

Mas não pense que List <Object> é igual a List <?>. Não é. Com List <Object>, os exemplos anteriores não serão compilados.

Existe outra diferença. O seguinte código não compilará:

```
List<?> list = new ArrayList<String>();
list.add("Hi"); // Compile-time error
```

Como o compilador não pode inferir o tipo dos elementos, ele não pode garantir a segurança do tipo (você só pode inserir nulo porque ele não tem um tipo).

Para evitar esse problema, o compilador gera um erro quando você tenta modificar a lista. Dessa forma, ao usar um curinga ilimitado, a lista se torna IMUTÁVEL.

Isso pode ser um problema ou uma vantagem, dependendo de como você o usa. Este curinga é usado em argumentos de métodos onde o código usa apenas métodos da classe genérica ou de Object, não de um tipo particular, por exemplo:

```
// You can pass any type of List here
int getSize(List<?> list) {  
    return list.size();
}
```

Esse foi o primeiro problema. O segundo problema é que ao trabalhar com um parâmetro de tipo, só podemos usar métodos de Object, pois não sabemos o tipo exato do parâmetro de tipo, por exemplo:

```
class Printer<T> {
   public void print(T t) {
      System.out.println(t.toUpperCase());// Error
      // What if T doesn't represent a String?
   }
}
```

A solução é usar os chamados curingas limitados:

* ? extends T (Upper-bounded wildcard - Curinga de limite superior)
* ? super T (Lower-bounded wildcard - Curinga de limite inferior)

Ao usar esses curingas, você pode relaxar um pouco as restrições impostas pelos genéricos. Isso também permitirá que você use algum tipo de polimorfismo ou subtipagem com genéricos e, pelo mesmo motivo, esta é a parte mais complicada do exame.

Vamos começar com o curinga de limite superior.

O erro no exemplo acima pode ser resolvido usando o genérico de limite superior (não exatamente um caractere curinga) desta forma:

```
class Printer<T extends String> {
   public void print(T t) {
      System.out.println(t.toUpperCase());//OK!
   }
}
```

<T extends String> significa que qualquer classe que estende (ou implementa ao trabalhar com uma interface) String (ou a própria String) pode ser usada como o parâmetro de tipo. Como T é substituído por String, é seguro usar seus métodos:

```
Printer<String> p1 = new Printer<>(); // OK
// Error, Byte is not a String
Printer<Byte> p2 = new Printer<>(); 
```

O caractere curinga de limite superior também pode resolver este problema:

```
List<Object> list =
        new ArrayList<String>(); // Error
List<? extends Object> list2 =
        new ArrayList<String>(); // OK!
```

Ainda assim, não podemos modificar a lista:

```
list2.add("Hi"); // Compile-time error
```

O motivo é o mesmo. O compilador ainda não pode saber com certeza que tipo a lista conterá (podemos adicionar qualquer tipo).

Observe então que List <Number> é mais restritivo do que List <? extends Number>, no sentido de que o primeiro só aceita atribuições diretas do tipo List <Number>, mas o último aceita atribuições diretas de List <Integer>, List <Float>, etc. Por exemplo:

```
List<Integer> listInteger = new ArrayList<>();
List<Float> listFloat = new ArrayList<>();
List<Number> listNumber = new ArrayList<>();
listNumber.add(new Integer(1)); // OK
listNumber.add(new Float(1.0F)); // OK
listNumber = listInteger; // Error
listNumber = listFloat; // Error

List<? extends Number> listExtendsNum = new ArrayList<>();
// This would cause an error
// listExtendsNum.add(new Integer(1));
listExtendsNum = listInteger; // OK
listExtendsNum = listFloat; // OK
```

Finalmente, temos o curinga de limite inferior. Se tivermos uma lista como esta:

```
List<? super Integer> list = new ArrayList<>();
```

Isso significa que a lista pode ser atribuída a uma lista Inteira (Lista <Integer>) ou algum supertipo de Inteiro (como Lista <Número> ou Lista <Objeto>).

Desta vez, como você sabe que a lista seria digitada como pelo menos um inteiro, é seguro para o compilador permitir modificações na lista:

```
List<? super Integer> list = new ArrayList<>();
list.add(1); // OK!
list.add(2); // OK!
```

Pense nisso, mesmo se o tipo da lista for List <Object>, um Integer pode ser atribuído a um Objeto ou Número (ou outra superclasse, se houver outra).

E que tipos podemos adicionar à lista?

Podemos adicionar instâncias de T ou uma de suas subclasses porque eles também são T (no exemplo, Integer não tem subclasses, então só podemos inserir instâncias de Integer).

Portanto, não se confunda, uma coisa é o que você pode atribuir e outra é o que você pode adicionar, por exemplo:

```
List<Integer> listInteger = new ArrayList<>();
List<Object> listObject = new ArrayList<>();
List<? super Number> listSuperNum = new ArrayList<>();
listSuperNum.add(new Integer(1)); // OK
listSuperNum.add(new Float(1.0F)); // OK
listSuperNum = listInteger; // Error!
listSuperNum = listObject; // OK
```


### Generic limitations

Já falamos sobre algumas das limitações dos genéricos e outras podem ser deduzidas do que revisamos, mas de qualquer forma, aqui está um resumo de todas elas:

Os genéricos não funcionam com tipos primitivos:

```
// Use Wrappers instead
List<int> list = new ArrayList<>(); 
```

Você não pode criar uma instância de um parâmetro de tipo:

```
class Test<T> {
   T var = new T();
   // You don't know the type's constructors
}
```

Você não pode declarar campos estáticos de um parâmetro de tipo:

```
class Test<T> {
   // If a static member is shared by many instances,
   // and each instance can declare a different type,
   // what is the actual type of var?
   static T var;
}
```

Devido ao apagamento de tipo, você não pode usar instanceof com tipos genéricos:

```
if(obj instanceof List<Integer>) { // Error
}
if (obj instanceof List<?>) {
    // It only works with the unbounded 
    // wildcard to verify that obj is a List
} 
```

Você não pode instanciar uma matriz de tipos genéricos

```
class Test<T> {
    T[] array; // OK
    T[] array1 = new T[100]; // Error
    List<String>[] array2 = new List<String>[10]; // Error
}
```

Você não pode criar, capturar ou lançar tipos genéricos

```
class GenericException<T> extends Exception { } // Error

<T extends Exception> void method() {
    try {
        // ...
    } catch(T e) {
        // Error
    }
}
```

No entanto, você pode usar um parâmetro de tipo em uma cláusula throws:

```
class Test<T extends Exception> {
    public void method() throws T { } // OK
}
```

Você não pode sobrecarregar um método em que o apagamento de tipo deixará os parâmetros com o mesmo tipo:

```
class Test {
    // List<String> and List<Integer>
    // will be converted to List at runtime
    public void method(List<String> list) { }
    public void method(List<Integer> list) { }
}
```

### Self Test

1. Given:

```
public class Question_6_1 {
    public static void main(String[] args) {
        Question_6_1 q = new Question_6_1();
        List<Integer> l = new ArrayList<>();
        l.add(20);
        l.add(30);
        q.m1(l);
    }
    private void m1(List<?> l) {
        m2(l); // 1
    }
    private <T> void m2(List<T> l) {
        l.set(1, l.get(0)); // 2
        System.out.println(l);
    }
}
```

What is the result?
A. [20, 20]
B. Compilation fails on the line marked as // 1
C. Compilation fails on the line marked as // 2
D. An exception occurs at runtime


2. Given:

```
public class Question_6_2 <T extends Number> {
    T t;
    public static void main(String[] args) {
        Question_6_2 q =
           new Question_6_2<Integer>(); // 1
        q.t = new Float(1); // 2
        System.out.println(q.t);
    }
}
```

What is the result?
A. 1.0
B. Compilation fails on the line marked as // 1
C. Compilation fails on the line marked as // 2
D. An exception occurs at runtime


3. Which of the following declarations don't compile?
A. List<?> l1 = new ArrayList<>();
B. List<String> l2 = new ArrayList();
C. List<? super Object> l3 = new ArrayList<String>();
D. List<? extends Object> l4 = new ArrayList<String>();


4. Given

```
List<? super Number> list = new ArrayList<Object>(); // 1
list.add(new Integer(2)); // 2
list.add(new Object()); // 3
```

Which line will generate a compile-time error?
A. Line marked as // 1
B. Line marked as // 2
C. Line marked as // 3
D. No compile-time error is generated






















