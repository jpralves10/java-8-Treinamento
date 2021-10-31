# 5. Enumerations

## Exam Objectives

*Use enumerated types including methods, and constructors in an enum type.*

### Enumerations

Digamos que nosso aplicativo use três estados ou valores para o volume de reprodução de uma música: alto, médio, baixo. Normalmente modelamos isso com algumas "constantes", como esta:

```
public interface Volume {
    public static final int HIGH = 100;
    public static final int MEDIUM = 50;
    public static final int LOW = 20;
}
```

No entanto, esta não é uma boa implementação. Considere um método para alterar o volume:

```
public void changeVolumen(int volume) {
    ...
}
...
app.changeVolumen(Volume.HIGH);
```

O que está impedindo alguém de chamar este método com um valor arbitrário como:

```
app.changeVolume(10000);
```

Claro, podemos implementar algumas verificações, mas isso apenas torna o problema mais complicado. Felizmente, enumerações (ou enums para abreviar) fornecem uma boa solução para esse problema.

Um enum é um tipo (como uma classe ou interface) que representa uma lista FIXED de valores (você pode pensar nisso como constantes).

Portanto, podemos usar um enum para representar o volume de um som em nosso aplicativo (observe o uso de enum em vez de interface):

```
public enum Volume {
    HIGH, MEDIUM, LOW
}
```

Observe que, como os valores são "constantes" (eles são implicitamente públicos, estáticos e finais), a convenção de usar todas as letras maiúsculas é seguida.

Além disso, observe que os valores são separados por vírgulas e, como o enum contém apenas uma lista de valores, o ponto-e-vírgula é opcional após o último.

Dessa forma, podemos definir o método changeVolume como:

```
public void changeVolumen(Volume volume) {
    ...
}
```

Passar qualquer outro objeto que não seja um Volume irá gerar um erro em tempo de compilação:

```
changeVolume(Volume.HIGH); // All good
changeVolume(-1); // Compile-time error
```

O método agora é seguro para o tipo, o que significa que não podemos atribuir valores inválidos.

A propósito, por causa de algo que revisaremos posteriormente, você não pode usar o novo operador para obter uma referência de um enum, então apenas obtemos a referência diretamente:

```
Volume level = Volume.LOW;
```

Você também pode obter um enum de uma string, por exemplo:

```
Volume level = Volume.valueOf("LOW");
```

Tenha cuidado, este método diferencia maiúsculas de minúsculas:

```
Volume level = Volume.valueOf("Low"); // Run-time exception
```

Para obter todos os enums de um tipo específico, use o método values ​​(), que retorna um array de enums na mesma ordem em que foram declarados e que combina perfeitamente com uma instrução for-each:

```
for(Volume v: Volume.values()) {
   System.out.print(v.name() + ", ");
}
```

The output:

```
HIGH, MEDIUM, LOW,
```

#### Trabalhando com Enums

No primeiro exemplo, HIGH era igual a 100. Mas agora que estamos usando enums, qual é o valor de HIGH?

Se imprimirmos seu valor:

```
System.out.println(Volume.HIGH);
```

A saída será o nome do enum:

`HIGH`

Isso é equivalente a invocar o método `name()` que todos os enums têm:

```
System.out.println(Volume.HIGH.name());
```

Em outras palavras, a implementação `toString()` de um enum chama o método `name()`.

Enums também têm um valor baseado em zero que corresponde à ordem em que são declarados. Você pode obtê-lo com o método `ordinal()`:

```
System.out.println(Volume.HIGH.ordinal());
System.out.println(Volume.LOW.ordinal());
```

O resultado será:

```
0
2
```

Mas isso pode não ser suficiente para todos os casos (como em nosso exemplo). Portanto, podemos definir um construtor (que é chamado na primeira vez que o enum é usado) que aceita um parâmetro que será armazenado em uma variável de instância:

```
public enum Volume {
    HIGH(100), MEDIUM(50), LOW(20);
    private int value;

    private Volume(int value) {
        this.value = value;
    }
    public int getValue() {
        return value;
    }
}
```

A única restrição é que o construtor deve ser privado. Caso contrário, o compilador gerará um erro. Observe que um método getter também foi adicionado.

Também podemos adicionar um método setter, mas geralmente ele não é necessário, pois enums funcionam mais como constantes. No entanto, lembre-se de que alterar o valor da variável de instância é permitido, enquanto reatribuir o enum não é porque eles são implicitamente finais e não podem ser alterados após sua criação:

```
// Compile-time error
Volume.HIGH = Volume.MEDIUM;
```

Falando sobre erros de tempo de compilação, as seguintes instruções também irão gerar um:

```
// Use Volume.HIGH.ordinal()
if(Volume.HIGH == 0) {
    ...
}
// Use Volume.HIGH.getValue()
if(Volume.HIGH == 100) {
    ...
}
// A enum can't extend a class
public enum Volume extends AClass { ... }
```

Para comparar o valor de um enum, você pode usar o método ordinal ou o método de nome, além de qualquer outro método personalizado. Você também pode comparar um enum a outro enum com o operador == (porque enums são finais), o método equals ou usando uma instrução switch:

```
Volume level = Volume.HIGH;
...
// Or Volume.HIGH.equals(level)
if(Volume.HIGH == level) {
    ...
}
switch(level) {
    // Notice that the only the name of the enum is used,
    // in fact, Volume.HIGH for example, won't compile
    case HIGH: ...
    case MEDIUM: ...
    case LOW: ...
}
```

Um tipo de enum NÃO PODE se estender de uma classe porque, implicitamente, todos os enums se estendem de java.lang.Enum. O que você PODE fazer é implementar interfaces:

```
public enum Volume implements AnInterface { ... }
```

O mais próximo que podemos chegar de estender uma classe ao trabalhar com enums é sobrescrever métodos e implementar métodos abstratos. Por exemplo:

```
public enum Volume {
    HIGH(100) {
       public void printValue() {
           System.out.println("** Highest value**");
       }
       public void printDescription() {
           System.out.println("High Volume");
       }
    }, MEDIUM(50) {
        public void printDescription() {
           System.out.println("Medium Volume");
        }
    }, LOW(20) {
        public void printDescription() {
            System.out.println("Low Volume");
        }
    };
    private int value;

    private Volume(int value) {
        this.value = value;
    }

    public void printValue() {
        System.out.println(value);
    }
    public abstract void printDescription();
}
```

No caso de printValue (), MEDIUM e LOW usarão a versão de nível de enum que apenas imprime o valor, enquanto HIGH usará sua própria versão. Se o método for abstrato, cada enum deve implementá-lo. Caso contrário, um erro de tempo de compilação será gerado.

#### Enums como Singletons

Lembra-se do Capítulo 1, quando mencionei que enums são singletons? Você sabe por que agora?

* Você não pode criar uma instância de um enum usando o operador new (porque o construtor é privado).
* Uma instância de um enum é criada quando o enum é referenciado pela primeira vez.
* Um enum não pode ser reatribuído.
* Eu não mencionei isso antes, mas enums são thread-safe por padrão (o que significa que você não precisa fazer verificações duplas ao criá-los).
* No Capítulo 1, o impacto da serialização em singletons não foi realmente explorado, mas se você serializar um singleton, ao recuperá-lo com a implementação padrão de readObject(), este método sempre retornará uma nova instância, então o singleton é realmente não é mais um. No entanto, ao serializar um enum, isso não acontecerá.

Por esses motivos, a maioria das pessoas acredita que, na maioria das vezes, enums são a melhor maneira de implementar o padrão de design singleton em Java.


### Self Test

1. Given:

```
public enum Question_5_1 {
    UP(1) {
        public void printValue() {
            System.out.println(value);
        }
    }, DOWN(0);
    private int value;

    private Question_5_1(int value) {
        this.value = value;
    }
}
```

What is the result of executing Question_5_1.UP.printValue()? <br>
A. 1 <br>
B. 0 <br>
C. Compile-time error <br>
D. Run-time error


2. Given:

```
enum Color {
    Blue, Green, Black
}
public class Question_5_2 {
    public static void main(String[] args) {
        Color c = Color.values()[0];
        switch(c) {
            case Blue: System.out.println(1); break;
            case Green: System.out.println(2); break;
            case Black: System.out.println(3); break;
        }
    }
}
```

What is the result of executing Question_5_2? <br>
A. 1 <br>
B. 2 <br>
C. 3 <br>
D. Compile-time error <br>
E. Run-time error


3. Which of the following statements is true? <br>
A. Enums are thread-safe. <br>
B. Enums can neither extend from a class nor implement an interface. <br>
C. Enums cannot define constructors. <br>
D. Enums cannot have setter methods.


4. Given:

```
enum Level {
    HIGH(100), LOW(10);
    private int value;
    private Level(int value) {
        this.value = value;
        System.out.println(value);
    }
}
public class Question_5_4 {
    public static void main(String[] args) {
        Level l1 = Level.HIGH;
        Level l2 = Level.HIGH;
    }
}
```

What is the result of executing class Question_5_4? <br>
A. 100 <br>
B. 100 <br>
      100 <br>
C. 100 <br>
      10 <br>
D. Compile-time error <br>
E. Nothing is printed


5. Given:

```
enum Color1 {
    RED, YELLOW
}
enum Color2 {
    RED, PINK
}
public class Question_5_5 {
    public static void main(String[] args) {
        if(Color1.RED.equals(Color2.RED)) {
            System.out.println(1);
        } else {
            System.out.println(0);
        }
    }
}
```

What is the result of executing Question_5_5? <br>
A. 1 <br>
B. 0 <br>
D. Compile-time error <br>
E. Run-time error


6. Which of the following statements are true? <br>
A. You can compare two enumerations using the == operator. <br>
B. Enums implicitly inherit from java.lang.Enum. <br>
C. You can't use the new operator inside an enum. <br>
D. You can't have abstract methods inside an enum.
































