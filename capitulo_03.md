# 3. Inner Classes

## Exam Objectives

*Create inner classes including static inner class, local class, nested class, and anonymous inner class.*

### Classes

Em Java, temos classes:

```
class Computer {

}
```

As classes têm dois tipos de membros, atributos (ou campos) e métodos (ou funções):

```
class Computer {
     int serialNumber;
     void executeCommand() {
         // Do something
     }
}
```

Dessa forma, nossos programas são uma coleção de classes.

```
class Computer {
     // Here goes the definition of the class
}
class Desktop {
     // Here goes the definition of the class
}
class Printer {
     // Here goes the definition of the class
}
class Programmer {
     // Here goes the definition of the class
}
```

### Inner classes

Java nos dá flexibilidade na maneira como podemos projetar nossas classes.

Para isso, existe um terceiro tipo de membro que uma classe pode ter, uma CLASSE INTERNA.

```
class Computer {
     String serialNumber;
     void executeCommand() { }
     class Processor {
         // Here goes the definition of the class
     }
}
```

As classes internas também são conhecidas como classes aninhadas. Em teoria, você poderia ter vários níveis de classes.

Tenho dificuldade em pensar qual seria a vantagem de ter mais de um nível de classes internas.

```
class LevelOne {
     class LevelTwo {
         class LevelThree {
             class LevelFour {
                 /** Finally do something */
             }
         }
     }
}
```

Outra coisa. Sempre falamos sobre CLASSES internas, mas na verdade, podemos ter **CLASSES ABSTRATAS** internas, **INTERFACES internas** e **ENUMS internos**.

```
class Computer {
     abstract class Processor { }
     interface Pluggable { }
     enum PORTS {
         USB2, USB3, ESATA, HDMI
     }
}
```

Mas vamos nos concentrar em classes internas simples. Existem quatro tipos deles:

* STATIC inner classes
* NON-STATIC inner classes
* LOCAL classes
* ANONYMOUS classes

---

***Defining a static inner class***

```
class Computer {
     static class Mouse {
     }
}
```

***Using a static inner class***

```
Computer.Mouse m = new Computer.Mouse();
```

Classes internas estáticas são acessadas por meio de sua classe envolvente

---

As classes estáticas são INDEPENDENTES de sua classe envolvente. Eles são como classes normais, só que por acaso estão dentro de outra classe.

Na verdade, você pode pensar na classe envolvente como uma espécie de pacote. Você pode importar o nome da classe envolvente e usar a classe interna estática como uma classe normal. Lembre-se de que a classe estática interna deve ser um membro público para que possa ser acessada de outro pacote.

```
import com.example.Computer.*;
public class Test {
      Mouse m = new Mouse();
     /** Rest of the definition */
}
```

E também podem ser marcados como privados, protegidos ou sem modificador, de modo que são acessíveis apenas no pacote (acessibilidade padrão).

```
public class Computer {
     private static class Component { }
     protected static class MotherBoard { }
     static class Slot { }
}
```

Obviamente, por ser membro de uma classe, a classe interna estática tem acesso aos outros membros da classe envolvente, mas apenas se eles forem ESTÁTICOS.

```
public class Computer {
     private static String serialNumber = "1234X";
     public static class Mouse {
         void printSN() {
             System.out.println("MOUSE-" + serialNumber);
         }
     }
}
```

Why?

Pense nisso, se a classe estática é independente de sua classe envolvente, ela não precisa de uma instância disso, portanto, apenas os membros estáticos podem ser usados ​​porque estão associados à classe, não a uma instância particular.

Por esse motivo, uma classe interna estática é freqüentemente usada como uma classe de utilitário que contém métodos comuns compartilhados por todos os objetos de uma classe.

Se você usar a classe interna estática dentro da classe que a define, você pode usá-la em qualquer método, bloco ou construtor, não importa se é estático ou não, já que a classe interna não está ligada a uma instância particular.

---

***Defining a non-static inner class***

```
class Computer {
     class HardDrive {
     }
}
```

***Using a non-static inner class***

Classes internas NÃO estáticas são acessadas por meio de uma instância de sua classe envolvente

```
Computer c = new Computer();
Computer.HardDrive hd = c.new HardDrive();
```

Verifique o tipo de classe interna e como o novo operador é usado

---

As classes internas não estáticas são chamadas apenas de classes internas.

As instâncias de uma classe interna existem apenas DENTRO de uma instância da classe envolvente. É o mesmo que quando você deseja usar um método de uma classe, você PRIMEIRO precisa de uma instância dessa classe.

Depois de ter uma instância da classe envolvente, você usa o novo operador de uma maneira (estranha) diferente da que normalmente usa.

```
Computer computer = new Computer();
Computer.HardDrive hardDrive = computer.new HardDrive();
```

Você também pode usar o truque de importação para escrever menos, mas ainda precisa criar a classe interna como sempre.

```
import com.example.Computer.*;
public class Test {
         Computer computer = new Computer();
         HardDrive hd = computer.new HardDrive();
         /** Rest of the definition */
}
```

Outra maneira de obter uma instância de uma classe interna é usar um método da classe envolvente para criá-la, evitando essa sintaxe estranha.

```
public class Computer {
     class HardDrive { }
     public HardDrive getHardDrive() {
         return new HardDrive();
     }
}
```

Por ser membro de uma classe, a classe interna tem acesso aos outros membros da classe envolvente, mas desta vez, NÃO importa se eles são estáticos ou não.

```
public class Computer {
     private String brand = "XXX";
     private static String serialNumber = "1234X";
     public class HardDrive {
         void printSN() {
            System.out.println(
                brand + "-MOUSE-" + serialNumber
             );
         }
     }
}
```

Why?

Como para usar a classe interna, uma instância da classe envolvente é necessária, garantindo que os membros não estáticos existam (membros estáticos podem ser acessados ​​de qualquer maneira).

As classes internas também podem ser marcadas como privadas, protegidas ou sem um modificador, portanto, são acessíveis apenas no pacote. Mas na maioria das vezes, como dependem da classe envolvente, são marcados como privados.

```
public class Computer {
     private class Component { }
     protected class MotherBoard { }
     class Slot { }
}
```

Outra regra é que as classes internas NÃO PODEM conter membros estáticos.

```
public class Computer {
     class HardDrive {
         // Compile-time error here
         static int capacity;
          // Compile-time error here
          static void printInfo() {
              // Definition goes here
         }
     }
}
```

O código estático é executado durante a inicialização da classe, mas você não pode inicializar uma classe interna não estática sem ter uma instância da classe envolvente.

Porque uma classe interna pertence a UMA instância da classe envolvente. Ter um membro estático significa que ele pode ser compartilhado entre instâncias porque o membro pertence à classe, mas como estamos falando sobre uma classe interna que não pode ser compartilhada por muitas instâncias da classe envolvente, isso não é possível.

A única exceção é quando você define um atributo estático final. A palavra-chave final faz toda a diferença; faz uma expressão constante, mas só funciona com ATRIBUTOS e ao atribuir um valor NON-NULL.

```
public class Computer {
     class HardDrive {
         final static int capacity = 120; // It does compile!
         // Compile-time error here
         final static String brand = null;
         // Compile-time error here
         final static void printInfo() {
             // Definition goes here
         }
     }
}
```

---

***Defining a local class***

```
class Computer {
     void process() {
         class Processor {
         }
     }
}
```

***Using a local class***

As classes locais só podem ser usadas dentro do método ou bloco que as define

```
void process() {
    class Core { }
    Core core = new Core();
}
```

---

As classes locais são locais porque só podem ser usadas no método ou bloco onde foram declaradas. Os blocos são praticamente qualquer coisa entre chaves.

```
void method() {
    class MethodLocalClass { }
    MethodLocalClass mlc = new MethodLocalClass();
    if ( 1 == 1 ) {
       class IfLocalClass { }
       IfLocalClass ilc = new IfLocalClass();
    }
    while ( true ) {
       class WhileLocalClass { }
       WhileLocalClass wlc = new WhileLocalClass();
    }
}
```

Além disso, observe onde as instâncias das classes locais são criadas. A classe local deve ser usada ABAIXO de sua definição. Caso contrário, o compilador não será capaz de localizá-lo.

Como uma classe interna local não é membro de uma classe, ela NÃO PODE ser declarada com um nível de acesso e não faria sentido de qualquer maneira, pois eles só são acessíveis onde são declarados. No entanto, uma classe local pode ser declarada como abstrata ou final (mas não ao mesmo tempo).

As classes locais requerem uma instância de sua classe envolvente para que o método ou bloco no qual são definidas possa ser executado. Por esse motivo, eles podem acessar os membros da classe envolvente, mas não podem declarar membros estáticos (apenas atributos finais estáticos), assim como as classes internas.

```
class Computer {
     private String serialNumber = "1234XX";
     void process() {
         class Processor {
             Processor() {
                 System.out.println(
                     "Processor #1 of computer " +
                      serialNumber
                 );
             }
         }
     }
}
```

Se a classe local for declarada dentro de um método, ela pode acessar as variáveis ​​e parâmetros do método SOMENTE se forem declarados finais ou efetivamente finais.

Efetivamente final é um termo que significa que uma variável ou parâmetro não é alterado depois de inicializado, mesmo que sua declaração não use a palavra-chave final.

Why?

Porque uma instância de uma classe local pode estar viva mesmo depois que o método ou bloco no qual ela está definida tenha concluído sua execução (por exemplo, se uma referência for salva em um objeto com escopo maior). Por esse motivo, a classe local deve manter uma cópia interna das variáveis ​​que usa, e a única maneira de garantir que ambas as cópias sempre mantenham o mesmo valor é tornando a variável final.

Portanto, o código a seguir é válido porque `taskName` é declarado final, enquanto `n` não muda e é considerado efetivamente final.

```
void process(int n) {
     final String taskName = "Task #1";
         class Processor {
             Processor() {
                 System.out.println(
                     "Processor " + n +
                      " processing " + taskName
                 );
         }
     }
}
```

Mas se modificarmos o valor de `n` em algum lugar, um erro será gerado.

```
void process(int n) {
     final String taskName = "Task #1";
     class Processor {
         Processor() {
             System.out.println(
                 "Processor " + n +    // Compile-time error
                 " processing " + taskName
             );
         }
     }
     n = 4;
}
```

Efetivamente final preocupa-se apenas com as referências, não com os objetos ou seu conteúdo, porque, no final das contas, estamos nos referindo ao mesmo objeto.

```
void process(int n) {
     StringBuffer taskName = new StringBuffer("Task #1");
     class Processor {
         Processor() {
             System.out.println(
                 "Processor " + n +
                 " processing " + taskName // It does compile!
             );
         }
     }
     taskName.append("1"); // This is valid!
     //Uncommenting the following line will generate an error
     //taskName = new StringBuffer("Task #2");
}
```

Se você ainda não tem certeza se uma declaração é efetivamente final, tente adicionar o modificador final a ela. Se o programa continuar a se comportar da mesma maneira, a declaração será efetivamente final.

Se a classe for declarada em um método estático, as regras estáticas também se aplicam, o que significa que a classe local só tem acesso aos membros estáticos da classe envolvente.

---

***Defining an anonymous class***

O novo operador é seguido pelo nome de uma interface ou classe e os argumentos para um construtor (ou parênteses vazios se for uma interface)

```
Computer comp = new Computer() {
     void process() {
         // Here goes the definition
     }
};
```

Veja como termina com um ponto e vírgula, como qualquer outra instrução em java

O corpo da classe implementa a interface ou estende a classe referenciada

---

Uma classe anônima é chamada assim porque não tem um nome. No entanto, uma expressão de classe anônima não declara uma nova classe. Ele IMPLEMENTA uma interface existente ou EXTENDE uma classe existente. Então

```
new Computer() { }
```

é como escrever

```
class [NO_NAME_CLASS] extends Computer { }
```

E se estivermos trabalhando com uma interface

```
new Runnable() { }
```

é como escrever

```
class [NO_NAME_CLASS] implements Runnable { }
```

Além disso, uma classe anônima pode ser usada em uma declaração ou chamada de método.

```
class Program {
     void start(Computer c) {
         // Definition goes here
     }
     public static void main(String args[]) {
         Program program = new Program();
         program.start(new Computer() {
             void process() { /** Redefinition goes here */ }
         });
     }
}
```

Visto que não têm um nome (bem, na verdade o compilador lhes dá um nome aleatório ao criar o arquivo .class), as classes anônimas não podem ter CONSTRUTORES. Se você quiser executar algum código de inicialização, terá que fazer isso com um bloco inicializador.

```
Computer t = new Computer() {
     {
         // Initializing code
     }
     void process() { /** Redefinition goes here */ }
};
```

Como as classes anônimas são um tipo de classes locais, elas têm as mesmas regras:

* Eles podem acessar os membros de sua classe
* Eles não podem declarar membros estáticos (apenas se forem variáveis ​​estáticas finais)
* Eles só podem acessar variáveis ​​locais (variáveis ​​ou parâmetros definidos em um método) se forem finais ou efetivamente finais.

Mas uma coisa com a qual você deve ter cuidado é a herança.

Quando você usa uma classe anônima (um objeto de subclasse), está usando uma referência de superclasse. Com esta referência, você pode usar os atributos e métodos declarados nesse tipo.

Mas o que acontece quando você declara um novo método na classe anônima?

```
class Task {
     void doIt() {
         /** Here goes the definition */
     }
}
class Launcher {
     public static void main(String args[]) {
         Task task = new Task() {
             void redoIt() {
                 /** Here goes the definition */
             }
         };
         task.doIt(); // It's OK
         task.redoIt(); // Compile-time error!
     }
}
```

O programa irá falhar. A referência não sabe sobre o método redoIt () porque ele não está definido na superclasse.

Normalmente, você lançaria o tipo para a subclasse onde o novo método é definido.

```
SubClass object = (SubClass) objectWithSuperClassReference;
object.methodOnlyDefinedInTheSubclass();
```

Mas com uma aula anônima, como é feito o elenco?

Não pode ser feito; a classe não tem nome, então não há como usarmos os métodos definidos na declaração da classe anônima. Só podemos usar os métodos definidos na SUPERCLASS (seja uma interface ou classe).

Usar uma classe anônima é principalmente sobre estilo. Se a classe tem um corpo curto, ela implementa apenas uma interface (se estivermos trabalhando com interfaces), não declara novos membros e a sintaxe torna seu código mais claro, você deve considerar usá-lo em vez de um local ou um classe interna.


### Shadowing

Um conceito importante a ser levado em consideração ao trabalhar com classes internas (de qualquer tipo) é o que acontece quando um membro da classe interna tem o mesmo nome de um membro da classe envolvente.

```
class Computer {
     private String serialNumber = "1234XXX";
     class HardDrive {
         private String serialNumber = "1234DDD";
         void printSN(String serialNumber) {
             System.out.println("SN: " + serialNumber);
         }
     }
}
```

Nesse caso, o parâmetro serialNumber sombreia a variável de instância serialNumber do HardDrive que, por sua vez, sombreia o serialNumber do computador.

Conforme é codificado, o método printSN () irá imprimir seu argumento. Uma declaração sombreada precisa de algo mais para ser referenciada adequadamente.

Portanto, se usarmos isso dentro de uma classe interna, ele se referirá à própria classe interna.

Se precisarmos fazer referência à classe envolvente, dentro da classe interna também podemos usar isso, mas desta forma NameOfTheEnclosingClass.this.

```
class Computer {
     private String serialNumber = "1234XXX";
     class HardDrive {
         private String serialNumber = "1234DDD";
         void printSN(String serialNumber) {
             System.out.println(
                 "SN: " + serialNumber
             );
             System.out.println(
                 "HardDrive SN: " + this.serialNumber
             );
             System.out.println(
                 "Computer SN: " +
                    Computer.this.serialNumber
             );
         }
     }
}
```

Uma vez que pode causar confusão, é melhor evitá-lo e usar nomes de variáveis ​​descritivos.


### Self Test

1. Given:

```
public class Question_3_1 {
     interface ITest { // 1
         void m();
     }
     public static void main(String args[]) {
         ITest t = new ITest() { // 2
             public void m() {
                 System.out.println("m()");
             }
         };
         t.m();
     }
}
```

What is the result? <br>
A. m() <br>
B. Compilation fails on the declaration marked as // 1 <br>
C. Compilation fails on the declaration marked as // 2 <br>
D. An exception occurs at runtime


2. Given:

```
public class Question_3_2 {
     public static void main(String args[]) {
         Question_3_2 q = new Question_3_2();
         int i = 2;
         q.method(i);
         i = 4;
     }
     void method(int i) {
         class A {
             void helper() {
                 System.out.println(i);
             }
         }
         new A().helper();
     }
}
```

What is the result? <br>
A. Compilation fails <br>
B. 2 <br>
C. 4 <br>
D. An exception occurs at runtime


3. Given:

```
public class Question_3_3 {
     public static void main(String[] args) {
         Question_3_3 q = new Question_3_3() {
                 public int sum(int a, int b) { // 1
                         return a + b;
                 }
         };
         q.sum(2,6); // 2
     }
}
```

What is the result? <br>
A. Compilation fails on the declaration marked as // 1 <br>
B. Compilation fails on the line marked as // 2 <br>
C. 8 <br>
D. Nothing is printed


4. Given:

```
public class Question_3_4 {
     public static class Inner {
             private void doIt() {
                     System.out.println("doIt()");
             }
     }
     public static void main(String[] args) {
             Question_3_4.Inner i = new Inner();
             i.doIt();
     }
}
```

What is the result? <br>
A. Compilation fails because an inner class cannot be static. <br>
B. Compilation fails because the Inner class is instantiated incorrectly inside method main. <br>
C. Compilation fails because the method doIt cannot be called in main because it is declared as private <br>
D. The program prints doIt()


5. Given:

```
class A {
     class B {
         class C {
             void go() {
                 System.out.println("go!");
             }
         }
     }
}
public class Question_3_5 {
     public static void main(String[] args) {
         A a = new A();
         A.B b = a.new A.B(); // 1
         B.C c = b.new C(); // 2
         c.go(); // 3
     }
}
```

What is the result? <br>
A. Compilation first fails on the line // 1 <br>
B. Compilation first fails on the line // 2 <br>
C. Compilation fails on the line // 3 <br>
D. go! is printed


6. Given:

```
public class Question_3_6 {
     private class A { // 1
         public int plusTwo(int n) {
             return n + 2;
         }
     }
     public static void main(String[] args) {
         Question_3_6.A a = new A(); // 2
         System.out.println(a.plusTwo(3));
     }
}
```

What is the result? <br>
A. Compilation fails on the line // 1 <br>
B. Compilation fails on the line // 2 <br>
C. 5 <br>
D. An exception occurs at runtime


7. Given:

```
public class Question_3_7 {
     public static void main(String[] args) {
         abstract class A { // 1
                 public void m() {
                     System.out.println("m()");
                 }
         }
         public class AA extends A { } // 2
     }
}
```

What change would make this code compile? <br>
A. Remove the abstract keyword on the line // 1 <br>
B. Add the public keyword on the line // 1 <br>
C. Remove the public keyword on the line // 2 <br>
D. None. This code compiles correctly 


8. Given:

```
public class Question_3_8 {
     int i = 2;
     interface A {
         int add();
     }
     public A create(int i) {
         return new A() {
             public int add() {
                 return i + 4;
             }
         };
     }
     public static void main(String[] args) {
         A a = new Question_3_8().create(8);
         System.out.println(a.add());
     }
}
```

What is the result? <br>
A. 6 <br>
B. 12 <br>
C. Compilation fails <br>
D. An exception occurs at runtime





























