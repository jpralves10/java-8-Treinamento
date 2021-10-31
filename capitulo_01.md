# 1. Encapsulation and Immutable Classes

## Objetivos do Exame

*Implement encapsulation.* <br>
*Create and use singleton classes and immutable classes.* <br>
*Develop code that uses static keyword on initialize blocks, variables, methods, and classes.* <br>
*Develop code that uses final keyword.*

### Encapsulation

Como sabemos, Java é uma linguagem orientada a objetos e todo código deve estar dentro de uma classe.

```
class MyClass {
    String myField = "you";
    void myMethod() {
        System.out.println("Hi " + myField);
    }
}
```

Portanto, temos que criar um objeto para usá-lo:

```
MyClass myClass = new MyClass();
myClass.myField = "James";
myClass.myMethod();
```

Um conceito importante em linguagens de programação orientadas a objetos é o encapsulamento, a capacidade de ocultar ou proteger os dados de um objeto.

Na maioria das vezes, quando alguém fala sobre encapsulamento, a maioria das pessoas tende a pensar sobre variáveis ​​privadas e getters e setters públicos e como isso é exagero, mas na verdade, encapsulamento é mais do que isso e é útil para criar designs de alta qualidade.

Vamos considerar o exemplo anterior.

Em primeiro lugar, é bom ocultar o máximo possível a implementação interna de uma classe. O motivo, mitigar o impacto da mudança.

Por exemplo, e se myField mudar seu tipo de String para int?

```
myClass.myField = 1;
```

Se estivéssemos usando esse atributo em cinquenta classes, teríamos que fazer cinquenta mudanças em nosso programa.

Mas se ocultarmos o atributo e usarmos um método setter, poderemos evitar as cinquenta mudanças:

```
// Hiding the attr to the outside world with the private keyword
private int myField = 0;

void setMyField(String val) { // Still accepting a String
    try {
        myField = Integer.parseInt(val);
    } catch(NumberFormatException e) {
        myField = 0;
    }
}
```

Você implementa esse ocultamento de atributo usando modificadores de acesso.

Java oferece suporte a quatro modificadores de acesso:

* public
* private
* protected
* default (quando nenhum modificador é especificado)

Você pode aplicar esses modificadores a classes, atributos e métodos de acordo com a seguinte tabela:

<TABELA>

Como você pode ver, todos os modificadores de acesso podem ser aplicados a atributos e métodos de uma classe, mas não necessariamente a suas contrapartes de interface. Além disso, as definições de classe e interface podem ter apenas um modificador público ou padrão. Porque? Vamos definir primeiro esses modificadores de acesso.

Se algo for declarado como público, ele pode ser acessado de qualquer outra classe em nosso aplicativo, independentemente do pacote ou módulo em que está definido.

Se algo é definido como privado, ele só pode ser acessado dentro da classe que o define, não de outras classes nos mesmos pacotes e não de classes que herdam a classe. privado é o modificador de acesso mais restritivo.

Se algo está definido como protegido, só pode ser acessado pela classe que o define, suas subclasses e classes do mesmo pacote. Não importa se a subclasse está em outro pacote, o que torna esse modificador menos restritivo do que privado.

Se algo não tiver um modificador, ele terá acesso padrão, também conhecido como acesso de pacote, porque só pode ser acessado por classes dentro do mesmo pacote. Se uma subclasse for definida em outro pacote, ela não poderá ver o atributo ou método de acesso padrão. Essa é a única diferença com o modificador protegido, o que o torna mais restritivo.

Um exemplo de código pode explicar isso melhor:

```
package street21;
public class House {
    protected int number;
    private String reference;

    void printNumber() {
        System.out.println("Num: " + number);
    }
    public void printInformation() {
        System.out.println("Num: " + number);
        System.out.println("Ref: " + reference);
    }
}
class BlueHouse extends House {
    public String getColor() { return "BLUE"; }
}

...

package city;
import street21.*;
class GenericHouse extends House {
    public static void main(String args[]) {
        GenericHouse h = new GenericHouse();
        h.number = 100;
        h.reference = ""; // Compile-time error
        h.printNumber(); // Compile-time error
        h.printInformation();
        BlueHouse bh = new BlueHouse(); // Compile-time error
        bh.getColor(); // Compile-time error
    }
}
```

* h.number compila porque este atributo está protegido e GenericHouse pode acessá-lo porque estende House.
* h.reference não compila porque este atributo é privado.
* h.printNumber () não compila porque este método tem acesso padrão (pacote).
* h.printInformation () compila porque esse método é público.
* BlueHouse bh = new BlueHouse () não compila porque a classe tem acesso padrão (pacote).
* bh.getColor () não compila porque embora o método seja público, a classe que o contém não é.

Agora, você pode ver por que alguns modificadores se aplicam a certos elementos e outros não?

Uma classe privada ou protegida faria sentido em uma linguagem orientada a objetos?

Que tal um método privado dentro de uma interface onde a maioria, senão todos os métodos, não tem implementação?

Think about it.


### O que é um Singleton?

Haverá momentos em que você pode querer ter apenas uma instância para uma determinada classe. Essa classe é uma classe singleton e é um padrão de projeto.

Existem algumas classes de Java que são escritas usando o padrão singleton, por exemplo:

* java.lang.Runtime
* java.awt.Desktop

Em Java, de maneira geral, existem duas maneiras de implementar uma classe singleton:

* Com um construtor privado e um método de fábrica estático
* As an enum

Vamos começar com o método do construtor privado. Embora possa parecer simples no início, ele apresenta muitos desafios, todos relacionados a manter apenas uma instância de uma classe única durante todo o ciclo de vida do aplicativo.

Por ter um construtor privado, a classe singleton garante que nenhuma outra classe crie uma instância dela. Um método privado (ou, neste caso, o construtor) só pode ser usado dentro da classe que o define.

```
class Singleton {
    private Singleton() { }
}
```

Portanto, a instância deve ser criada dentro da própria classe.

Isso pode ser feito de duas maneiras. Usando um atributo `private static` e um método para obtê-lo:

```
class Singleton {
    private static final Singleton instance = new Singleton();
    private Singleton() { }
    public static Singleton getInstance() {
        return instance;
    }
}
```

O atributo deve ser privado para que nenhuma outra classe possa usá-lo, apenas por meio de seu getter.

Ele deve ser estático para que a instância possa ser criada quando a classe é carregada antes que alguém possa usá-la e porque os membros estáticos pertencem à classe e não a uma instância particular.

E tem que ser final para que uma nova instância não possa ser criada posteriormente.

Uma variação dessa implementação é usar uma classe interna estática (revisaremos esse tipo de classe no Capítulo 3):

```
class Singleton {
    private Singleton() { }
    private static class Holder {
        private static final Singleton instance =
                new Singleton();
    }
    public static Singleton getInstance() {
        return Holder.instance;
    }
}
```

A vantagem disso é que a instância não será criada até que a classe interna seja referenciada pela primeira vez.

No entanto, haverá momentos em que, por exemplo, criar o objeto não é tão simples quanto chamar new, então a outra maneira é criar a instância dentro do método get:

```
class Singleton {
    private static Singleton instance;
    private Singleton() { }
    public static Singleton getInstance() {
        if(instance == null) {
            instance = new Singleton();
            // more code to create the instance...
        }
        return instance;
    }
}
```

Na primeira vez que este método é chamado, a instância será criada. Mas com essa abordagem, enfrentamos outro problema. Em um ambiente multithreading, se dois ou mais threads estiverem executando este método em paralelo, há um risco significativo de terminar com várias instâncias da classe.

Uma solução é sincronizar o método para que apenas um thread de cada vez possa acessá-lo.

```
class Singleton {
    private static Singleton instance;
    private Singleton() { }
    public static synchronized Singleton getInstance() {
        if(instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}
```

O problema com isso é que, estritamente falando, não é muito eficiente. Precisamos da sincronização apenas na primeira vez, quando a instância é criada, não sempre que o método é chamado.

Uma melhoria é bloquear apenas a parte do código que cria a instância. Para que isso funcione corretamente, temos que verificar se a instância é nula, uma sem bloqueio (para verificar se a instância já foi criada), e outra dentro de um bloco sincronizado (para criar a instância com segurança).

Veja como isso ficaria:

```
class Singleton {
    private static Singleton instance;
    private Singleton() { }
    public static Singleton getInstance() {
        if(instance == null) {
            synchronized (Singleton.class) {
                if(instance == null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```

Mas, novamente, essa implementação ainda não é perfeita. Desta vez, o problema está no nível da Java Virtual Machine (JVM). A JVM, ou às vezes o compilador, pode otimizar o código reordenando ou armazenando em cache o valor das variáveis ​​(e não tornando as atualizações visíveis).

A solução é usar a palavra-chave volatile, que garante que qualquer operação de leitura / gravação de uma variável compartilhada por muitos threads seja atômica e não armazenada em cache.

```
class Singleton {
    private static volatile Singleton instance;
    private Singleton() { }
    public static Singleton getInstance() {
        if(instance == null) {
            synchronized (Singleton.class) {
                if(instance == null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```

Como você pode ver, foi muito trabalhoso implementar um singleton corretamente quando você deseja ou tem que adiar a instanciação da classe até que você a use pela primeira vez (também chamada de inicialização lenta). E não vamos cobrir a serialização e como manter um singleton em um cluster.

Portanto, se você não precisar disso, use os dois primeiros métodos (crie a instância ao declarar a variável ou use a classe interna titular) ou a maneira mais fácil (e recomendada), uma enumeração (enum).

Vamos revisar enums no Capítulo 5, por enquanto, apenas saber que enums são singletons é o suficiente.


### Objetos Imutáveis

Haverá outros momentos em que você pode não querer modificar os valores ou o estado de um objeto quando usado por várias classes. Esse objeto será um objeto imutável.

Existem algumas classes imutáveis ​​no Java JDK, por exemplo:

* java.lang.String
* Wrappers classes (like Integer)

Objetos imutáveis ​​não podem mudar após serem criados. Isso significa que eles não podem ter métodos setter ou variáveis ​​públicas, portanto, a única maneira de definir suas propriedades é por meio do construtor.

Imutabilidade também significa que se um método tiver que alterar as propriedades do objeto, visto que o objeto ou seus valores não podem ser alterados, ele deve retornar uma cópia do objeto com os novos valores (é assim que funciona a classe String).

Outro ponto a considerar é a herança. Se a classe imutável pode ser herdada, a subclasse pode alterar os métodos para modificar as instâncias da classe, portanto, uma classe imutável não pode permitir isso.

Em resumo, um objeto imutável:

* Define todas as suas propriedades por meio de um construtor
* Não define métodos setter
* Declara todos os seus atributos privados (e às vezes finais)
* Tem uma classe declarada final para evitar herança
* Protege o acesso a qualquer estado mutável. Por exemplo, se ele tiver um membro da Lista, a referência não pode ser acessível fora do objeto ou uma cópia deve ser retornada (o mesmo se aplica se o conteúdo do objeto deve ser alterado)


### A palavra-chave static

Pense em algo estático como algo pertencente à classe e não a uma instância particular dessa classe.

Se estamos falando sobre atributos, um atributo estático é compartilhado por todas as instâncias da classe (porque, novamente, ele não pertence a uma instância).

Compare a saída deste código:

```
public class Example {
    private int attr = 0;

    public void printAttr() {
        System.out.println(attr);
    }

    public static void main(String args[]) {
        Example e1 = new Example();
        e1.attr = 10;
        e1.printAttr();
        Example e2 = new Example();
        e2.printAttr();
    }
}
```

Output:

```
10
0
```

Para a saída do código que usa uma variável estática:

```
public class Example {
    private static int attr = 0;

    public void printAttr() {
        System.out.println(attr);
    }

    public static void main(String args[]) {
        Example e1 = new Example();
        e1.attr = 10;
        e1.printAttr();
        Example e2 = new Example();
        e2.printAttr();
    }
}
```

Output:

```
10
10
```

Como você pode ver, o valor é retido quando outra instância usa a variável estática.

Ao falar sobre métodos estáticos, o método pertence à classe, o que significa que não precisamos de uma instância para chamá-lo (o mesmo se aplica aos atributos, por falar nisso).

```
public class Example {
    private static int attr = 0;
    public static void printAttr() {
        System.out.println(attr);
    }
    public static void main(String args[]) {
        Example e1 = new Example();
        e1.attr = 10;
        e1.printAttr();
        // Referencing the method statically
        Example.printAttr();
    }
}
```

Output:

```
10
10
```

No entanto, se você olhar de perto, printAttr usa um atributo estático, e esse é o problema com os métodos estáticos, eles não podem usar variáveis ​​de instância, apenas estáticas.

Isso faz todo o sentido, se você pode acessar um método estático apenas com a classe, não há garantia de que uma instância exista e, mesmo que exista, como vincular um atributo a sua instância quando você só tem o nome da classe?

Usando a mesma lógica, as palavras-chave super e this também não podem ser usadas.

As classes estáticas serão abordadas no Capítulo 3, mas há outra construção que pode ser marcada como estática, um bloco inicializador.

Um bloco estático (inicializador) tem a seguinte aparência:

```
public class Example {
    private static int number;

    static {
        number = 100;
    }
    ...
}
```

Um bloco é executado quando a classe é inicializada e na ordem em que são declaradas (se houver mais de um).

Assim como os métodos estáticos, eles não podem fazer referência a atributos de instância ou usar as palavras-chave super e this.

Além disso, os blocos estáticos não podem conter uma instrução de retorno e é um erro em tempo de compilação se o bloco não pode ser concluído normalmente (por exemplo, devido a uma exceção não capturada).


### Palavra-chave final

A palavra-chave final pode ser aplicada a variáveis, métodos e classes.

Quando final é aplicado a variáveis, você não pode alterar o valor da variável após sua inicialização. Essas variáveis ​​podem ser atributos (estáticos e não estáticos) ou parâmetros. Os atributos finais podem ser inicializados quando declarados, dentro de um construtor ou dentro de um bloco inicializador.

```
public class Example {
    private final int number = 0;
    private final String name;
    private final int total;
    private final int id;

    {
        name = "Name";
    }

    public Example() {
        number = 1; // Compile-time error
        total = 10;
    }

    public void exampleMethod(final int id) {
        id = 5; // Compile-time error
        this.id = id; // Compile-time error
    }
}
```

Quando final é aplicado a um método, isso não pode ser substituído.

```
public class Human {
    final public void talk() { }
    public void eat() { }
    public void sleep() { }
}
...
public class Woman extends Human {
    public void talk() { } // Compile-time error
    public void eat() { }
    public void sleep() { }
}
```

Por sua vez, quando final é aplicado a uma classe, você não pode subclassificá-la. Isso é usado quando você não quer que alguém mude o comportamento de uma classe criando subclasses. Dois exemplos no JDK são java.lang.String e java.lang.Integer.

```
public final class Human {
    ...
}
...
public class Woman extends Human { // Compile-time error
    ...
}
```

In summary:

* Uma variável final só pode ser inicializada uma vez e não pode alterar seu valor depois disso.
* Um método final não pode ser substituído por subclasses.
* Uma classe final não pode ser subclassificada.


### Auto teste

1. Given:

```
public class Question_1_1 {
    private final int a; // 1
    static final int b = 1; // 2
    public Question_1_1() {
        System.out.print(a); // 3
        System.out.print(b); // 4
    }
}
```

What is the result? <br>
A. 01 <br>
B. Compilation fails on the line marked as // 1 <br>
C. Compilation fails on the line marked as // 2 <br>
D. Compilation fails on the line marked as // 3 <br>
E. Compilation fails on the line marked as // 4


2. Which of the following state the correct order from the more restricted modifier to the least restrictive? <br>
A. private, default, public, protected <br>
B. protected, private, default, public <br>
C. default, protected, private, public <br>
D. private, default, protected, public


3. Given:

```
public final class Question_1_3 {
    private final int n;
    public Question_1_3() { }
    public void setN(int n) { this.n = n; }
}
```

Which of the following is true? <br>
A. The class is immutable <br>
B. The class is not immutable <br>
C. Compilation fails <br>
D. An exception occurs at runtime


4. Given:

```
public class Question_1_4 {
    private final List<Integer> list = new ArrayList<>();
    public void add() {
        list.add(0);
    }
    public static void main(String[] args) {
        Question_1_4 q = new Question_1_4();
        q.add();
    }
}
```

Which of the following is true? <br>
A. Attribute list contains one element after main is executed <br>
B. The class is immutable <br>
C. Compilation fails <br>
D. An exception occurs at runtime


5. Given:

```
public class Question_1_5 {
    private String s = "Hi";
    public static void main(String[] args) {
        Question_1_5 q = new Question_1_5();
        q.s = "Bye"; // 1
        System.out.println(q.s); // 2
    }
}
```

What is the result? <br>
A. Hi <br>
B. Bye <br>
C. Compilation fails on the declaration marked as // 1 <br>
D. Compilation fails on the declaration marked as // 2


6. Given:

```
public class Question_1_6 {
    private static int a;
    private int b;
    static {
        a = 1;
        b = 2;
    }
    public static void main(String[] args) {
        Question_1_6 q1 = new Question_1_6();
        Question_1_6 q2 = new Question_1_6();
        q2.b = 1;
        System.out.println(q1.a + q2.b);
    }
}
```

What is the result? <br>
A. 0 <br>
B. 3 <br>
C. 2 <br>
D. Compilation fails








































