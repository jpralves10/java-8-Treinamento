# 4. Interfaces

## Exam Objectives

*Develop code that declares, implements and/or extends interfaces and use the atOverride annotation.*

### O que é uma interface?

Na primeira vez que você olhar uma interface, provavelmente pensará que é como uma classe apenas com definições de métodos:

```
interface Monitorable {
    void monitor();
}
```

E para termos práticos, você está certo.

Uma interface é um tipo de dados que apenas define métodos (abstratos) que uma classe deve implementar.

Embora conceitualmente, seja mais interessante do que isso, porque permite definir o que uma classe pode fazer sem dizer como. É por isso que se diz que uma interface é um contrato.

Qualquer classe que implemente uma interface deve fornecer uma implementação para todos os métodos da interface, caso contrário, a classe deve ser marcada como abstrata.

Como uma classe é definida com a palavra-chave class, uma interface é definida com a palavra-chave interface.

Se uma classe deseja implementar uma interface, ela deve especificá-la com a palavra-chave implements.

---

***Defining an interface***

```
public interface Monitorable {
    public static final int ID = 0;
    public abstract void monitor();
}
```

***Implementing an interface***

```
class Server implements Monitorable {
    public void monitor() {
        // Implementation code
    }
}
```

---

Como uma classe, uma interface tem acessibilidade pública ou padrão

As interfaces são abstratas por padrão

Isso significa duas coisas:

* Você não pode instanciar uma interface diretamente, ela deve ser implementada por uma classe para usá-la.
* Uma interface não pode ser marcada como final.

Os métodos definidos em uma interface são por padrão PUBLIC e ABSTRACT, o compilador irá tratá-los como tal, mesmo que você não os especifique.

Portanto, mesmo que você defina uma interface como esta:

```
interface Monitorable {
    void monitor();
    void setup();
}
```

Para o compilador, a interface será semelhante a esta:

```
interface Monitorable {
    public abstract void monitor();
    public abstract void setup();
}
```

Essa é a razão pela qual você deve marcar o método como público quando for implementado:

```
class Server implements Monitorable {
    public void monitor() {
        // Implementation
    }
    public void setup() {
        // Implementation
    }
}
```

Além disso, esse é o motivo pelo qual, se um ou mais dos métodos da interface não forem implementados, você deve marcar a classe (e os métodos) como abstratos:

```
abstract class Server implements Monitorable {
    public void monitor() {
        // Implementation
    }
    public abstract void setup();
}
```

Os campos declarados em uma interface são, por padrão, PÚBLICO, ESTÁTICO e FINAL. Como os métodos, o compilador os tratará como tais, mesmo que você não os especifique.

Isso significa que os campos são CONSTANTS em vez de VARIABLES:

```
interface Monitorable {
    int ID = 0; // You have to assign a value at creation time
}
class Resource implements Monitorable {
    void change() {
        ID = 5; // This WON'T compile
    }
}
```

Isso também significa que as seguintes declarações são todas equivalentes dentro de uma interface:

```
int ID = 0;
public int ID = 0;
static int ID = 0;
final int ID = 0;
public static int ID = 0;
public final int ID = 0;
static final int ID = 0;
public static final int ID = 0;
```

Portanto, esteja atento a declarações que não compilam, como estas:

```
interface Monitorable {
    private int ID = 0; // It cannot be private
    int TIMEOUT; // It's final, so you have to provide a value
}
```

Existem duas regras em relação a heranças e interfaces:

* Uma classe pode implementar (não estender) qualquer número de interfaces.
* Uma interface pode estender qualquer número de interfaces, mas não pode estender de uma classe.

Implementar uma interface é um tipo de herança. Quando uma classe implementa uma interface, podemos usá-la assim para tirar vantagem do polimorfismo:

```
interface Monitorable {
    void monitor();
}
class Disk implements Monitorable {
    public void monitor() {
        System.out.println("Monitoring Disk");
    }
}
class Server implements Monitorable {
    public void monitor() {
        System.out.println("Monitoring Server");
    }
}
public class Test {
    public static void main(String args[]) {
        Monitorable m = new Disk();
        m.monitor();
        m = new Server(); // Change implementation
        m.monitor();
    }
}
```

Uma classe não pode se estender de mais de uma classe, mas pode implementar mais de uma interface. Você pode ver o motivo com um exemplo. Considerar:

```
class Truck {
    public void accelerate() {
        System.out.println("Accelerating truck...");
    }
}
class CompactCar {
    public void accelerate() {
        System.out.println("Accelerating compact car...");
    }
}
```

Se você pudesse herdar de várias classes:

```
class Car extends Truck, CompactCar {
    public void run() {
        accelerate();
        // ...
    }
}
```

Qual método accelerate () o Java escolheria?

Este é um problema que os designers de Java decidiram evitar, não permitindo herança múltipla.

Mas e se estivéssemos usando interfaces?

```
interface Truck {
    void accelerate();
}
interface CompactCar {
    void accelerate();
}
class Car implements Truck, CompactCar {
    public void accelerate() {
        System.out.println("Accelerating car"); }
        // ...
}
```

Como as interfaces não fornecem uma implementação, há apenas uma (implementação de Car), portanto não há conflito e evitamos o problema por completo!

E se os métodos tiverem o mesmo nome, mas parâmetros diferentes:

```
interface Truck {
    void accelerate();
}
interface CompactCar {
    void accelerate(int speed);
}
```

Eles são considerados dois métodos diferentes porque a assinatura do método é diferente, portanto, a classe de implementação deve implementar as duas versões dos métodos:

```
class Car implements Truck, CompactCar {
    public void accelerate() {
        // implementation
    }
    public void accelerate(int speed) {
        // implementation
    }
}
```

Porém, quando os métodos diferem apenas no tipo de retorno, uma vez que o tipo de retorno não é considerado na assinatura do método, o compilador Java irá gerar um erro:

```
interface Truck {
    void accelerate();
}
interface CompactCar {
    int accelerate();
}
class Car implements Truck, CompactCar {
    // Java will complain about duplicate methods
    // and incompatible return types
    public void accelerate() {
        // implementation
    }
    public int accelerate() {
        // implementation
    }
}
```

Opcionalmente, para tornar as coisas mais claras, podemos usar a anotação @Override.

```
interface CompactCar {
    int accelerate();
}
class Car implements CompactCar {
    @Override
    public int accelerate() {
        // implementation
    }
}
```

@Override indica que um método sobrescreve uma declaração de método em um supertipo, seja em uma interface ou em uma classe pai.

Se o método anotado não substituir ou implementar o método corretamente, o compilador irá gerar um erro.

Esta é a única função da anotação. É útil em casos como quando há um erro não tão óbvio causado por um erro de digitação:

```
interface CompactCar {
    int accelerate();
}
class Car implements CompactCar {
    // Compiler will mark an error about method
    // "acelerate" not overriding or implementing something
    @Override
    public int acelerate() {
        // implementation
    }
}
```

Finalmente, uma interface só pode estender outras interfaces.

```
interface Monitorable {
    void monitor();
}
interface Pluggable {
    void plug();
}
interface Resource extends Monitorable, Pluggable {
    void printInfo();
}
```

Uma classe não abstrata que implementa Resource, deve implementar todos os métodos das três interfaces:

```
class Disk implements Resource {
    public void monitor() {
        // implementation
    }
    public void plug() {
        // implementation
    }
    public void printInfo() {
        // implementation
    }
}
```


### O que há de novo no Java 8?

Suponha que temos uma interface como esta:

```
interface Processable {
    void processInSequence();
}
```

E uma implementação:

```
class Task implements Processable {
    public void processInSequence() {
        System.out.println("Processing in sequence");
    }
}
```

Sabemos que quando uma classe implementa uma interface, a menos que a classe seja marcada como abstrata, ela deve implementar TODOS os métodos dessa interface.

Portanto, se adicionarmos outro método a Processable, por exemplo:

```
interface Processable {
    void processInSequence();
    void processInParallel();
}
```

Temos que atualizar a classe para evitar um erro de compilação:

```
class Task implements Processable {
    public void processInSequence() {
        System.out.println("Processing in sequence");
    }
    public void processInParallel() {
        System.out.println("Processing in parallel");
    }
}
```

Essa foi fácil. Mas pense no seguinte:

* E se tivermos centenas de classes implementando Processable?
* E se não pudermos atualizar ou não tivermos acesso ao código por algum motivo?
* E se o novo método não for necessário ou não fizer sentido para algumas implementações?

São problemas reais, às vezes difíceis de resolver.

No entanto, o Java 8 nos fornece métodos padrão. Não precisamos fornecer implementações para eles porque são métodos não abstratos.

Em outras palavras, as interfaces agora permitem métodos com um BODY. E isso não é tão simples quanto parece.


### Default methods

O principal motivo para adicionar métodos padrão às interfaces foi para suportar a evolução da interface, para adicionar novas funcionalidades às interfaces e, ao mesmo tempo, garantir a compatibilidade com o código escrito para versões anteriores.

Existem dois outros efeitos colaterais que vale a pena mencionar:

* Agora podemos projetar métodos opcionais. Podemos ter métodos com funcionalidade limitada ou padrão para que as classes que implementam as interfaces possam decidir se mantêm essa funcionalidade ou fornecem outra.
* Podemos ter métodos utilitários diretamente na interface. Métodos que obtêm ou criam recursos, por exemplo, feitos apenas por conveniência e possivelmente implementados em termos de métodos não padrão da interface.

No entanto, agora que as interfaces podem fornecer comportamento, a diferença com as classes abstratas não é muito clara em alguns casos. Ainda existem duas diferenças significativas:

* Uma classe pode estender apenas de UMA classe abstrata, mas pode implementar interfaces MÚLTIPLAS.
* Uma classe abstrata pode ter um estado por meio de variáveis ​​INSTANCE (campos). Uma interface NÃO PODE.

Os métodos padrão vêm com muitas regras, especialmente em relação à herança. Mas vamos começar com sua sintaxe.

---

***Defining a default method***

```
interface Processable {
    void processInSequence();
    default void processInParallel() {
        /** Default implementation goes here */
    }
}
```

Uma interface pode ter qualquer número de métodos abstratos e padrão.

Todos os métodos com a palavra-chave default devem ter um corpo.

Os métodos padrão são públicos implicitamente, assim como qualquer outro método de uma interface.

---

Ao tornar processInParallel () um método padrão, a classe de implementação o obtém automaticamente. Aqui está o exemplo completo:

```
interface Processable {
    void processInSequence();
    default void processInParallel() {
        System.out.println("Processing in parallel");
    }
}
public class Task implements Processable {
    public void processInSequence() {
        System.out.println("Processing in sequence");
    }
    public static void main(String args[]) {
        Task t = new Task();
        t.processInSequence();
        t.processInParallel(); // It compiles just fine 
    }
}
```

Este é o cenário mais simples, onde a classe de implementação herda o método padrão.

Antes de apresentar cenários mais complexos, vamos ver quais são as restrições ao usar métodos padrão.

***Default methods cannot be final.*** <br>
Se um método for final, ele não pode ser substituído pelas classes de implementação, o que não favorece o objetivo principal dos métodos padrão.

***Os métodos padrão não podem ser sincronizados.***
Esta foi uma decisão deliberada dos criadores da linguagem. Se um método for sincronizado na interface, isso significa que todas as classes de implementação herdarão esse comportamento. Mas esta decisão deve pertencer à implementação; a interface não tem base razoável para assumir qual deveria ser a política de sincronização.

***Os métodos padrão são sempre públicos.***
Como outros métodos de interface. Compare isso com uma classe abstrata, onde você pode escolher a visibilidade do método.

***Você não pode ter métodos padrão para os métodos de classe do objeto.***
Uma interface não pode fornecer implementações padrão para:

```
boolean equals(Object o)
int hashCode()
String toString()
```

Se uma interface tiver métodos com essas assinaturas, o compilador gerará um erro. A razão é que esses métodos são todos sobre o estado do objeto. Como as interfaces não têm um estado, esses métodos devem estar nas classes de implementação.

E agora, para os cenários mais complexos.


#### Class overrides default method

Classes sempre VENCEM as interfaces. Se uma classe substituir um método padrão, o método da classe será o usado. Por exemplo:

```
interface Processable {
    void processInSequence();
    default void processInParallel() {
        System.out.println("Default parallel");
    }
}
public class Task implements Processable {
    public void processInSequence() {
        System.out.println("Processing in sequence");
    }
    public void processInParallel() {
        System.out.println("Class parallel");
    }
    public static void main(String args[]) {
        Task t = new Task();
        t. processInParallel();
    }
}
```

A saída seria:

```
Class parallel
```

Isso é verdade mesmo que a classe redefina o método padrão como abstrato:

```
interface Processable {
    void processInSequence();
    default void processInParallel() {
        System.out.println("Default parallel");
    }
}
// Class Task has to be abstract
abstract class Task implements Processable {
    public void processInSequence() {
        System.out.println("Processing in sequence");
    }
    public abstract void processInParallel();
}
```

Se, por algum motivo, você precisar chamar a implementação padrão do método, pode fazê-lo com o nome da interface seguido pela palavra-chave super:

```
public void processInParallel() {
    Processable.super.processInParallel();
}
```

Isso só funciona com métodos padrão. Chamar um método não padrão dessa maneira resultará em um erro de compilação. Além disso, super deve ser usado com uma super interface direta da classe.

Outro cenário relacionado a esta regra é quando um método de instância herdado de uma classe substitui um método de interface padrão:

```
interface Processable {
    default void processInParallel() {
        System.out.println("Default parallel");
    }
}
class Process {
    public void processInParallel() {
        System.out.println("Class parallel");
    }
}
public class Task
        extends Process implements Processable {
    public static void main(String args[]) {
        Task t = new Task();
        t.processInParallel();
    }
}
```

The output is:

```
Class parallel
```

O método processInParallel () retorna a string "Class parallel", pois a classe Task herda o método processInParallel () da classe Process, que substitui o método padrão de mesmo nome na interface Processable.


#### Herança de interface com métodos padrão

Interfaces (ou classes) mais específicas sempre VENCEM sobre outras menos específicas. Os métodos padrão das interfaces mais rasas em uma hierarquia de herança serão usados. Por exemplo:

```
interface Processable {
    void processInSequence();
    default void processInParallel() {
        System.out.println("Processable parallel");
    }
}
interface Parallelizable extends Processable {
    default void processInParallel() {
        System.out.println("Parallelizable parallel");
    }
}
public class Task implements Parallelizable {
    public void processInSequence() {
        System.out.println("Processing in sequence");
    }
    public static void main(String args[]) {
        Task t = new Task();
        t.processInParallel();
    }
}
```

A saída seria:

```
Parallelizable parallel
```

A interface Parallelizable herda o método padrão processInParallel (), mas como ela redefine o método quando Task o implementa, sua implementação é chamada.

Se Parallelizable definiu processInParallel () como abstrato:

```
interface Parallelizable extends Processable {
    abstract void processInParallel();
}
```

Então Task teria que implementar o método (para não se tornar uma classe abstrata):

```
public class Task implements Parallelizable {
    public void processInSequence() {
        System.out.println("Processing in sequence");
    }
    public void processInParallel() {
        System.out.println("Task parallelizable");
    }
    public static void main(String args[]) {
        Task t = new Task();
        t.processInParallel();
    }
}
```

A saída seria:

```
Task parallelizable
```


#### Herança de interface múltipla com métodos padrão

As classes podem implementar várias interfaces. O que acontece quando duas interfaces têm o mesmo método padrão? Qual deles a classe de implementação escolherá? Por exemplo:

```
interface Processable {
    void processInSequence();
    default void processInParallel() {
        System.out.println("Processable parallel");
    }
}
interface Parallelizable {
    default void processInParallel() {
        System.out.println("Parallelizable parallel");
    }
}
public class Task
            implements Processable, Parallelizable {
    public void processInSequence() {
        System.out.println("Processing in sequence");
    }
    public static void main(String args[]) {
        Task t = new Task();
        t.processInParallel();
    }
}
```

Acontece que o resultado é um erro do compilador:

```
Duplicate default methods named processInParallel with the parameters () and () are inherited from the types Parallelizable and Processable.
```

O compilador não sabe qual escolher, por isso gera um erro. Nesse caso, a Tarefa deve fornecer uma implementação (honrar a regra anterior de interfaces (ou classes) mais específicas sempre vence as menos específicas) para substituir os métodos padrão da interface e resolver o problema:

```
public class Task implements Processable, Parallelizable {
    public void processInSequence() {
        System.out.println("Processing in sequence");
    }
    public void processInParallel() {
        System.out.println("Task parallelizable");
    }
    public static void main(String args[]) {
        Task t = new Task();
        t.processInParallel();
    }
}
```

E agora, a saída seria:

```
Task parallelizable
```

Claro, sempre podemos chamar uma implementação padrão com:

```
public void processInParallel() {
    Processable.super.processInParallel();
}
```

---

***Defining a static method***

```
interface Processable {
    static void log() {
        /** Implementation goes here */
    }
}
```

*Os métodos estáticos em interfaces são definidos apenas como métodos estáticos em classes, com a palavra-chave static.*
*Os métodos estáticos são públicos implicitamente, assim como qualquer outro método de uma interface.*
*Uma interface pode ter qualquer número de métodos estáticos.*

---

### Static methods

Sempre que nos referimos a algo estático, queremos dizer algo que pertence a uma classe, não a uma instância ou objeto particular dessa classe. Os métodos estáticos nas interfaces seguem o mesmo conceito; eles pertencem à interface em que são declarados.

Eles foram adicionados para auxiliar os métodos padrão e para organizar melhor os métodos auxiliares, porque geralmente, os métodos auxiliares ou utilitários são definidos em outra classe (como java.utils.Collections), em vez de onde eles pertencem naturalmente.

Por exemplo, a interface java.util.Comparator define o método estático:

```
static <T> Comparator<T> comparingInt(ToIntFunction<? super T> keyExtractor)
```

Usado pelo método padrão:

```
default Comparator<T> thenComparingInt(ToIntFunction<? super T> keyExtractor)
```

Os métodos estáticos da interface não são herdados, você deve prefixar o método com o nome da interface:

```
interface Parallelizable {
    static void log(String s) {
        System.out.println(s);
    }
    default void processInParallel() {
        log("Parallelizable parallel");
    }
}
public class Task implements Parallelizable {
    public static void main(String args[]) {
        Task t = new Task();
        t.processInParallel();
        // t.log("The end"); Doesn't compile
        // Task.log("The end"); Doesn't compile either
        Parallelizable.log("The end"); // Compiles!
    }
}
```

The output is:

```
Parallelizable parallel The end
```

### Self Test

1. Given:

```
interface A {
    default int aMethod() {
        return 0;
    }
}
public class Test implements A {
    public long aMethod() {
        return 1;
    }
    public static void main(String args[]) {
        Test t = new Test();
        System.out.println(t.aMethod());
    }
}
```

What is the result? <br>
A. 0 <br>
B. 1 <br>
C. Compilation fails <br>
D. An exception occurs at runtime


2. Given:

```
interface B {
    default static void test() {
        System.out.println("B test");
    }
}
public class Question_4_2 implements B {
    public void test() {
        System.out.println("Q test");
    }
    public static void main(String[] args) {
        Question_4_2 q = new Question_4_2();
        q.test();
    }
}
```

What is the result? <br>
A. B test <br>
B. Q test <br>
C. Compilation fails <br>
D. An exception occurs at runtime


3. Given:

```
interface C {
    default boolean equals(C obj) {
        return obj == this;
    }
}
public class Question_4_3 implements C {
    public static void main(String[] args) {
        Question_4_3 q = new Question_4_3();
        System.out.println(q.equals(q));
    }
}
```

What is the result? <br>
A. true <br>
B. false <br>
C. Compilation fails <br>
D. An exception occurs at runtime


4. Given:

```
interface D {
    default void print() {
        System.out.println("D");
    }
}
interface E extends D {
    default void print() {
        System.out.println("E");
    }
}
public class Question_4_4 implements E {
    public void print() {
        E.super.print();
    }
    public static void main(String[] args) {
        Question_4_4 q = new Question_4_4();
        q.print();
   }
}
```

What is the result? <br>
A. D <br>
B. E <br>
C. D and then E <br>
D. Compilation fails <br>
E. An exception occurs at runtime


5. Given:

```
interface F {
    static void test() {
        System.out.println("F test");
    }
}
public class Question_4_5 implements F {
    public void test() {
        System.out.println("Q test");
    }
    public static void main(String[] args) {
        F q = new Question_4_5();
        q.test();
    }
}
```

What is the result? <br>
A. F test <br>
B. Q test <br>
C. Compilation fails <br>
D. An exception occurs at runtime


6. Given:

```
interface G {
    default void doIt() {
        System.out.println("G - Do It");
    }
}
interface H {
    void doIt();
}
public class Question_4_6 implements G, H {
    public void doIt() {
        System.out.println("Do It");
    }
    public static void main(String[] args) {
        Question_4_6 q = new Question_4_6();
        q.doIt();
    }
}
```

What is the result? <br>
A. G - Do It <br>
B. Do It <br>
C. Compilation fails <br>
D. An exception occurs at runtime






















