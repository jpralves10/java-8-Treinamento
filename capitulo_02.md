# 2. Inheritance and Polymorphism

## Objetivos do Exame

*Implement inheritance including visibility modifiers and composition.* <br>
*Override hashCode, equals, and toString methods from Object class.* <br>
*Implement polymorphism.* <br>
*Develop code that uses abstract classes and methods.*


### Herança

No cerne de uma linguagem orientada a objetos, existe o conceito de herança.

Em termos simples, herança se refere a um relacionamento IS-A em que uma classe (chamada de superclasse) fornece atributos e métodos comuns para classes derivadas ou mais especializadas (chamadas de subclasse).

Em Java, uma classe só pode herdar de uma única superclasse (herança singular). Obviamente, a única exceção é java.lang.Object, que não possui superclasse. Esta classe é a superclasse de todas as classes.

A palavra-chave extends é usada para especificar esse relacionamento. Por exemplo, uma ferramenta de martelo IS-A, para que possamos modelá-la como:

```
class Tool {
    public int size;
}

class Hammer extends Tool {

}
```

Como o tamanho é um atributo público, ele é herdado por Hammer:

```
Hammer hammer = new Hammer();
hammer.size = 10;
```

Do capítulo anterior, sabemos que apenas private e membros com visibilidade padrão quando a subclasse é definida em um pacote diferente da superclasse, não são herdados.

Um atributo ou método é herdado com o mesmo nível de visibilidade daquele definido na superclasse. No entanto, no caso dos métodos, você pode alterá-los para ficar mais visíveis, mas não pode torná-los menos visíveis:

```
class Tool {
    public int size;
    public int getSize() { return size; }
}

class Hammer extends Tool {
    private int size; // No problem!
    // Compile-time error
    private int getSize() { return size; }
}
```

Não há problema para o atributo porque estamos criando um NOVO atributo no Hammer que ESCONDE aquele herdado de Tool quando o nome for o mesmo.

Aqui estão as coisas que você pode fazer em uma subclasse:

* Os atributos herdados podem ser usados ​​diretamente, como qualquer outro.
* Um atributo pode ser declarado na subclasse com o mesmo nome que o da superclasse, ocultando-o assim.
* Novos atributos que não estão na superclasse podem ser declarados na subclasse.
* Os métodos herdados podem ser usados ​​diretamente como estão.
* Um novo método de instância pode ser declarado na subclasse que possui a mesma assinatura que o da superclasse, substituindo-o assim.
* Um novo método estático pode ser declarado na subclasse que possui a mesma assinatura que o da superclasse, ocultando-o assim.
* Novos métodos que não estão na superclasse podem ser declarados na subclasse.
* Um construtor pode ser declarado na subclasse que invoca o construtor da superclasse, implicitamente ou usando a palavra-chave super.

Portanto, para métodos, a redução de sua visibilidade não é permitida porque eles são tratados de uma maneira diferente, ou seja, os métodos são sobrescritos ou sobrecarregados.

Além disso, pense nisso. Por causa do encapsulamento, os atributos devem ser ocultados, mas com métodos, se uma subclasse não tem um método da superclasse, a subclasse não pode ser usada onde quer que a superclasse seja usada. Isso é chamado de princípio de substituição de Liskov, que é importante no polimorfismo, e revisaremos depois de falar sobre métodos sobrescritos e sobrecarregados.

Implementar uma interface é, de certa forma, um tipo de herança porque eles têm algumas características comuns, mas ao fazer isso, o relacionamento se torna HAS-A. Falaremos mais sobre eles no Capítulo 4.


### Sobrecarga e sobrescrita

A diferença entre sobrecarregar e substituir tem muito a ver com as assinaturas de método.

Em poucas palavras, a assinatura do método é o nome do método e a lista de seus parâmetros (tipos e número de parâmetros incluídos). Observe que os tipos de retorno não estão incluídos nesta definição.

Falamos em sobrecarga quando um método altera a assinatura do método, alterando a lista de parâmetros de outro método (que podem ser herdados), mantendo o mesmo nome.

```
class Hotel {
    public void reserveRoom(int rooms) { ... }
}

class ThreeStarHotel extends Hotel {
    // Method overload #1
    public void reserveRoom(List<Room> rooms) {
        ...
    }
    // Method overload #2
    public void reserveRoom(
            int rooms, int numberPeople) {
        ...
    }
}
```

Alterar apenas o tipo de retorno gerará um erro de compilação:

```
class ThreeStarHotel extends Hotel {
    // Compile-time error, reserveRoom is seen as duplicated
    public void reserveRoom(List<Room> rooms) {
        ...
    }
    public boolean reserveRoom(List<Room> rooms) {
        ...
    }
}
```

As exceções na cláusula throws não são consideradas durante a sobrecarga, portanto, novamente, alterar apenas a lista de exceções gerará um erro de compilação:

```
class ThreeStarHotel extends Hotel {
    // Compile-time error, reserveRoom is seen as duplicated
    public void reserveRoom(List<Room> rooms)
        throws RuntimeException {
        ...
    }
    public void reserveRoom(List<Room> rooms)
        throws NullPointerException {
        ...
    }
}
```

Quando um método sobrecarregado é chamado, o compilador deve decidir qual versão do método será chamada. O primeiro candidato óbvio é chamar o método que corresponde exatamente ao número e aos tipos de argumentos. Mas o que acontece quando não há uma correspondência exata?

A regra a ser lembrada é que o Java procurará a correspondência mais CLOSEST PRIMEIRO (isso significa um tipo maior, uma superclasse, um tipo autoboxed ou o tipo particular MAIS).

Por exemplo, quando esta classe é executada:

```
class Print {
    static void printType(short param) {
        System.out.println("short");
    }
    static void printType(long param) {
        System.out.println("long");
    }
    static void printType(Integer param) {
        System.out.println("Integer");
    }
    static void printType(CharSequence param) {
       System.out.println("CharSequence");
    }

    public static void main(String[] args) {
        byte b = 1;
        int i = 1;
        Integer integer = 1;
        String s = "1";

        printType(b);
        printType(i);
        printType(integer);
        printType(s);
   }
}
```

The output is:

```
short
long
Integer
CharSequence
```

Na primeira chamada de método, o tipo de argumento é byte. Não há método para obter um byte, então o tipo maior mais próximo é curto.

Na segunda chamada de método, o tipo de argumento é int. Não há nenhum método tomando um int, então o tipo maior mais próximo é longo (observe que isso tem precedência mais alta do que inteiro).

Na terceira chamada de método, o tipo de argumento é Integer. Existe um método que leva um inteiro, então isso é chamado.

Na última chamada de método, o tipo de argumento é String. Não há nenhum método usando String, então a superclasse mais próxima é CharSequence.

Se ele não puder encontrar uma correspondência ou se o compilador não puder decidir porque a chamada é ambígua, um erro de compilação é gerado. Por exemplo, considerando a classe anterior, o seguinte causará um erro porque não há um tipo maior que double e não pode ser autoboxed para um inteiro:

```
// Can't find a match
double d = 1.0;
printType(d);
```

A seguir está um exemplo de uma chamada ambígua, assumindo os métodos:

```
static void printType(float param, 
                        double param2) {
    System.out.println("float-double");
}
static void printType(double param,
                        float param2) {
    System.out.println("double-float");
}

...

// Ambiguous call
printType(1,1);
```

Os construtores de uma classe também podem ser sobrecarregados. Na verdade, você pode chamar um construtor de outro com esta palavra-chave:

```
class Print {
    Print() {
        this("Calling with default argument");
    }
    Print(String s) {
        System.out.println(s);
    }
}
```

Falamos sobre sobrescrever quando a assinatura do método é a mesma, mas por algum motivo, queremos redefinir um método INSTANCE na subclasse.

```
class Hotel {
    public void reserveRoom(int rooms) {
        ...
    }
}
class ThreeStarHotel extends Hotel {
    // Method override
    public void reserveRoom(int rooms) {
        ...
    }
}
```

Se um método estático com a mesma assinatura de um método estático na superclasse for definido na subclasse, o método será OCULTO em vez de substituído.

Existem algumas regras ao substituir um método.

O modificador de acesso deve ser o mesmo ou com mais visibilidade:

```
class Hotel {
    public void reserveRoom(int rooms) {
        ...
    }
}
class ThreeStarHotel extends Hotel {
    // Compile-time error
    protected void reserveRoom(int rooms) {
        ...
    }
}
```

O tipo de retorno deve ser o mesmo ou um subtipo:

```
class Hotel {
    public Integer reserveRoom(int rooms) {
         ...
    }
}

class ThreeStarHotel extends Hotel {
    // Compile-time error
    public Number reserveRoom(int rooms) {
        ...
    }
}
```

As exceções na cláusula throws devem ser iguais, menores ou subclasses dessas exceções:

```
class Hotel {
    public void reserveRoom(int rooms)
           throws IOException {
        ...
    }
}

class ThreeStarHotel extends Hotel {
    // Compile-time error
    public void reserveRoom(int rooms) throws Exception {
        ...
    }
}
```

Substituir é um conceito crítico no polimorfismo, mas antes de tocar neste tópico, vamos ver alguns métodos importantes de java.lang.Object que na maioria das vezes precisaremos substituir.


### Métodos de classe de objeto

Em Java, todos os objetos são herdados de java.lang.Object.

Esta classe possui os seguintes métodos que podem ser substituídos (redefinidos):

* protected Object clone() throws CloneNotSupportedException
* protected void finalize() throws Throwable
* public int hashCode()
* public boolean equals(Object obj)
* public String toString()

Os métodos mais importantes, aqueles que você quase sempre desejaria redefinir, são hashCode, equals e toString.

**public int hashCode()**

Ele retorna um valor de código hash para o objeto. O valor devolvido deve ter o seguinte contrato:

* Sempre que for chamado no mesmo objeto mais de uma vez durante a execução de um aplicativo Java, o método hashCode deve retornar consistentemente o mesmo inteiro, desde que nenhuma informação usada em comparações de igual no objeto seja modificada. Este inteiro não precisa permanecer consistente de uma execução de um aplicativo para outra execução do mesmo aplicativo.
* Se dois objetos são iguais de acordo com o método equals (Object), chamar o método hashCode em cada um dos dois objetos deve produzir o mesmo resultado inteiro.
* Não é necessário que, se dois objetos não forem iguais de acordo com o método equals (java.lang.Object), chamar o método hashCode em cada um dos dois objetos deve produzir resultados inteiros distintos. No entanto, o programador deve estar ciente de que a produção de resultados inteiros distintos para objetos desiguais pode melhorar o desempenho das tabelas hash.

**public boolean equals(Object obj)**

Indica se outro objeto é igual ao objeto que chama o método. É necessário sobrescrever o método hashCode sempre que este método for sobrescrito, uma vez que o contrato para o método hashCode declara que objetos iguais devem ter códigos hash iguais. Este método é:

* reflexive: for any non-null reference value x, x.equals(x) should return true.
* symmetric: for any non-null reference values x and y, x.equals(y) should return true if and only if y.equals(x) returns true.
* transitive: for any non-null reference values x, y, and z, if x.equals(y) returns true and y.equals(z) returns true, then x.equals(z) should return true.
* consistent: for any non-null reference values x and y, multiple invocations of x.equals(y) consistently return true or false, provided no information used in equals comparisons on the objects is modified.

For any non-null reference value x, x.equals(null) should return false.

**public String toString()**

Ele retorna uma representação de string do objeto. O método toString para a classe Object retorna uma string que consiste no nome da classe da qual o objeto é uma instância, o caractere de arroba '@' e a representação hexadecimal sem sinal do código hash do objeto.

Para substituir esses métodos, basta seguir as regras gerais de substituição:

* O modificador de acesso deve ser o mesmo ou mais acessível
* O tipo de retorno deve ser o mesmo ou uma subclasse
* O nome deve ser o mesmo
* Os tipos de lista de argumentos devem ser os mesmos
* As mesmas exceções ou suas subclasses podem ser lançadas

Em poucas palavras, defina o método exatamente como ele aparece na classe java.lang.Object.


### Polymorphism

Polimorfismo é a capacidade de um objeto variar seu comportamento com base em seu tipo. Isso é melhor demonstrado com um exemplo:

```
class HumanBeing {
    public void dress() {
        System.out.println("Dressing a human being");
    }
}
class Man extends HumanBeing {
    public void dress() {
        System.out.println("Put on a shirt");
        System.out.println("Put on some jeans");
    }
}
class Woman extends HumanBeing {
    public void dress() {
        System.out.println("Put on a dress");
    }
}
class Baby extends HumanBeing {
    public void dress() {
        System.out.println(
            "I don't know how to dress!");
    }
}
```

E agora vamos criar alguns seres humanos para ver o polimorfismo em ação:

```
HumanBeing[] someHumans = new HumanBeing[3];
someHumans[0] = new Man();
someHumans[1] = new Woman();
someHumans[2] = new Baby();
for(int i = 0; i < someHumans.length; i++) {
    someHumans[i].dress();
    System.out.println();
}
```

The output:

```
Put on a shirt
Put on some jeans
Put on a dress
I don't know how to dress!
```

Mesmo que HumanBeing seja usado, a JVM decide em tempo de execução qual método chamar com base no tipo de objeto atribuído, não no tipo de referência da variável.

Isso é chamado de invocação de método virtual, um nome sofisticado para substituir

A substituição também é conhecida como polimorfismo dinâmico porque o tipo de objeto é decidido no momento da execução.

Em contraste, a sobrecarga também é chamada de polimorfismo estático porque é resolvida no momento COMPILAR.


### Classes e métodos abstratos

Se examinarmos o exemplo anterior, acho que concordaremos que a implementação do método dress () na classe HumanBeing não parece exatamente correta.

Na maioria das vezes, estaremos trabalhando com algo mais concreto, como um homem ou uma mulher, então não há necessidade de instanciar a classe HumanBeing diretamente, no entanto, uma abstração comum dessas classes pode ser útil. Usar uma classe abstrata (ou método) é a melhor opção para modelar esses casos.

Classes abstratas NÃO PODEM ser instanciadas, apenas subclasses. Eles são declarados com a palavra-chave abstract:

```
abstract class AClass { }
```

Os métodos abstratos são declarados SEM uma implementação (corpo), como este:

```
abstract void AMethod();
```

Portanto, no exemplo anterior, é melhor modelar toda a classe HumanBeing como abstrata para que ninguém possa usar diretamente:

```
abstract class HumanBeing {
    public abstract void dress();
}
```

Agora, o seguinte causaria um erro de compilação:

```
HumanBeing human = new HumanBeing();
```

E faz sentido; não pode haver garantias de que uma classe abstrata terá todos os seus métodos implementados. Chamar um método não implementado seria uma falha épica.

Aqui estão as regras ao trabalhar com métodos e classes abstratos:

A palavra-chave abstract só pode ser aplicada a classes ou métodos não estáticos.

```
abstract class AClass {
    // Compile-time error
    public static abstract void AMethod();
}
```

Uma classe abstrata não precisa declarar métodos abstratos para serem declarados abstratos.

```
abstract class AClass { } // No problem
```

Se uma classe inclui métodos abstratos, a própria classe deve ser declarada abstrata.

```
class AClass { // Compile-time error
    public abstract void AMethod();
}
```

Se a subclasse de uma classe abstrata não fornece uma implementação para todos os métodos abstratos, a subclasse também deve ser declarada abstrata.

```
// Compile-time error
class Man extends HumanBeing { }
```

Os métodos de uma interface são considerados abstratos, portanto, uma classe abstrata que implementa uma interface pode implementar alguns ou nenhum dos métodos de interface.

```
// No problem
abstract class AClass implements Runnable {}
```

### Key Points

* Herança se refere a um relacionamento IS-A em que uma classe (chamada de superclasse) fornece atributos e métodos comuns para classes derivadas ou mais especializadas (chamadas de subclasse).
* Aqui estão as coisas que você pode fazer em uma subclasse:
* * Os atributos herdados podem ser usados ​​diretamente, como qualquer outro.
* * Um atributo pode ser declarado na subclasse com o mesmo nome que o da superclasse, ocultando-o assim.
* * Novos atributos que não estão na superclasse podem ser declarados na subclasse.
* * Os métodos herdados podem ser usados ​​diretamente como estão.
* * Um novo método de instância pode ser declarado na subclasse que possui a mesma assinatura que o da superclasse, substituindo-o assim.
* * Um novo método estático pode ser declarado na subclasse que possui a mesma assinatura que o da superclasse, ocultando-o assim.
* * Novos métodos que não estão na superclasse podem ser declarados na subclasse.
* * Um construtor pode ser declarado na subclasse que invoca o construtor da superclasse, implicitamente ou usando a palavra-chave super.
* A assinatura do método é o nome do método e a lista de seus parâmetros (tipos e número de parâmetros incluídos). Os tipos de retorno não estão incluídos nesta definição.
* Falamos em sobrecarga quando um método altera a lista de parâmetros de outro método (que podem ser herdados) enquanto mantém o mesmo nome.
* Falamos sobre sobrescrever quando a assinatura do método é a mesma, mas por algum motivo, queremos redefinir um método INSTANCE na subclasse.
* The most important methods of java.lang.Object that most classes must redefine are:
* * public int hashCode()
* * public boolean equals(Object obj)
* * public String toString()
* Com o polimorfismo, as subclasses podem definir seus próprios comportamentos (diferentes dos métodos da superclasse), e a JVM chamará o método apropriado para o objeto. Esse comportamento é conhecido como invocação de método virtual.
* Classes abstratas NÃO PODEM ser instanciadas, apenas subclasses. Os métodos abstratos são declarados SEM uma implementação (corpo).
* A palavra-chave abstract só pode ser aplicada a classes ou métodos não estáticos.
* Uma classe abstrata não precisa declarar métodos abstratos para serem declarados abstratos.
* Se uma classe inclui métodos abstratos, a própria classe deve ser declarada abstrata.
* Se a subclasse de uma classe abstrata não fornece uma implementação para todos os métodos abstratos, a subclasse também deve ser declarada abstrata.
* Os métodos de uma interface são considerados abstratos, portanto, uma classe abstrata que implementa uma interface pode implementar alguns ou nenhum dos métodos de interface.


### Self Test

1. Given:

```
public class Question_2_1 {
    protected int id;
    protected String name;

    protected boolean equals(Question_2_1 q) {
        return this.name.equals(q.name);
    }

    public static void main(String[] args) {
        Question_2_1 q1 = new Question_2_1();
        Question_2_1 q2 = new Question_2_1();
        q1.name = "q1";
        q2.name = "q1";

        if(q1.equals((Object)q2)) {
            System.out.println("true");
        } else {
            System.out.println("false");
        }
    }
}
```

What is the result? <br>
A. true <br>
B. false <br>
C. Compilation fails <br>
D. An exception occurs at runtime


2. Which of the following is a method of java.lang.Object that can be overridden? <br>
A. public String toString(Object obj) <br>
B. public int equals(Object obj) <br>
C. public int hashCode(Object obj) <br>
D. public int hashCode()


3. Given:

```
public class Question_2_3 {
    public static void print(Integer i) {
        System.out.println("Integer");
    }
    public static void print(Object o) {
        System.out.println("Object");
    }
    public static void main(String[] args) {
        print(null);
    }
}
```

What is the result? <br>
A. Integer <br>
B. Object <br>
C. Compilation fails <br>
D. An exception occurs at runtime


4. Given:

```
class SuperClass {
    public static void print() {
        System.out.println("Superclass");
    }
}
public class Question_2_4 extends SuperClass {
    public static void print() {
        System.out.println("Subclass");
    }
    public static void main(String[] args) {
        print();
    }
}
```

What is the result? <br>
A. Superclass <br>
B. Subclass <br>
C. Compilation fails <br>
D. An exception occurs at runtime


5. Given:

```
abstract class SuperClass2 {
    public static void print() {
        System.out.println("Superclass");
    }
}
class SubClass extends SuperClass2 {}
public class Question_2_5 {
    public static void main(String[] args) {
        SubClass subclass = new SubClass();
        subclass.print();
    }
}
```

What is the result? <br>
A. Superclass <br>
B. Compilation fails because an abstract class cannot have static methods <br>
C. Compilation fails because Subclass doesn't implement method print() <br>
D. Compilation fails because Subclass doesn't have a method print() <br>
E. An exception occurs at runtime


6. Given:

```
abstract class SuperClass3 {
    public void print() {
        System.out.println("Superclass");
    }
}
public class Question_2_6 extends SuperClass3 {
    public void print() {
        System.out.println("Subclass");
    }
    public static void main(String[] args) {
        Question_2_6 q = new Question_2_6();
        ((SuperClass3)q).print();
    }
}
```

What is the result? <br>
A. Superclass <br>
B. Subclass <br>
C. Compilation fails <br>
D. An exception occurs at runtime






























