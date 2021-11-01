# 7. Collections

## Exam Objectives

*Create and use ArrayList, TreeSet, TreeMap, and ArrayDeque objects.*


### Collections Overview

Uma coleção é um termo genérico que se refere a um contêiner de objetos.

O Java Collections Framework é uma biblioteca de classes e interfaces do pacote java.util que fornece coleções com diferentes características.

As interfaces mais importantes são:

* `Collection` Esta é a interface base da hierarquia da coleção e contém métodos como add(), remove(), clear() e size().
* `Iterable` A implementação dessa interface permite que um objeto seja "iterável" com um loop for-each, por meio de um Iterator e com o novo método forEach().
* `List` Interface para coleções que, uma, armazenam um grupo de elementos que podem ser acessados ​​por meio de um índice, e duas, aceitam duplicatas.
* `Set` Interface para coleções que não permitem elementos duplicados.
* `Queue` Interface para coleções que armazenam um grupo de elementos em uma ordem específica, geralmente em uma ordem de primeiro a entrar, primeiro a sair.
* `Map` Interface para coleções cujos elementos são armazenados como pares chave / valor.

Dos quatro últimos, Map é o único que não implementa nem a Collection nem a interface Iterable, mas ainda assim, é considerado uma coleção, pois contém um grupo de elementos.


### List

Lista é a coleção mais comum. Use quando quiser permitir (ou não se importar se houver) elementos duplicados. Você pode até inserir elementos nulos (nem todas as coleções permitem).

Os elementos têm uma ordem de inserção, mas você pode adicionar elementos em qualquer posição, já que essa posição é baseada em um índice baseado em zero, como uma matriz.

Na verdade, a implementação mais usada, ArrayList, é na verdade implementada como um array de objetos nos bastidores.

A diferença com o uso de uma matriz é que uma lista pode crescer automaticamente quando os elementos são adicionados. No entanto, como ele faz isso copiando os elementos para um array maior, adicionar (e remover) é mais lento.

Aqui estão algumas operações básicas da lista:

```
// Creating an ArrayList with an initial capacity of 10
List<String> list = new ArrayList<>(10);

System.out.println(list.isEmpty()); // true
list.add("a");
System.out.println(list.get(0)); // a
list.add(0, "b"); // Inserting b at index 0
list.add("c");
list.add(null);
System.out.println(list); // [b, a, c, null]
list.set(2, "a"); // Replacing element at index 2 with a
System.out.println(list.contains("d")); // false
// Returning the index of the first match, -1 if not found
System.out.println(list.indexOf("a")); // 1
// Returning the index of the last match, -1 if not found
System.out.println(list.lastIndexOf("a")); // 2

list.remove(1); // Removing by index
list.remove(null); // Removing null
list.remove("a"); // Removing the first matching element

System.out.println(list.size()); // 1
```

Outra implementação popular é LinkedList, uma lista duplamente vinculada que também implementa a interface Deque (mais sobre essa interface mais tarde).

Uma maneira fácil de criar uma lista é usando o método java.util.Arrays.asList:

```
String[] arr = {"a", "b", "c", "d"};
List<String> list = Arrays.asList(arr);
```

Ou simplesmente:

```
List<String> list =
        Arrays.asList("a", "b", "c", "d");
```

Ele retorna uma implementação de List apoiada pelo array especificado (mas não é um ArrayList e não implementa todos os métodos de List) que tem tamanho fixo, o que significa que você não pode adicionar elementos a ele. Além disso, as modificações na Lista são refletidas na matriz original.


### Set

A principal característica de um Set é que ele não permite duplicatas.

As duas implementações mais usadas são HashSet e TreeSet. A diferença entre eles é que TreeSet classifica os elementos, enquanto HashSet não garante a ordem ou que a ordem permanecerá constante ao longo do tempo.

O HashSet armazena os elementos em uma tabela hash (usando uma instância de HashMap). Por isso, os elementos não são mantidos em ordem, mas adicionar e procurar elementos é rápido.

Para recuperar objetos e evitar duplicatas, os elementos devem implementar os métodos hashCode() e equals().

Aqui está um exemplo de HashSet:

```
// Creating a HashSet with an initial capacity of 10
Set<String> set = new HashSet<>(10);
// add() returns true if the element is not already in the set
System.out.println(set.add("b")); // true
System.out.println(set.add("x")); // true
System.out.println(set.add("h")); // true
System.out.println(set.add("b")); // false
System.out.println(set.add(null)); // true
System.out.println(set.add(null)); // false
System.out.println(set); // [null, b, x, h]
```

Como você pode ver, o HashSet aceita valores nulos.

TreeSet armazena os elementos em uma estrutura de dados em árvore vermelha e preta. É por isso que mantém os elementos classificados e garante custo de tempo log (n) para adicionar, remover, procurar um elemento e obter o tamanho do conjunto.

Para evitar duplicatas, os elementos devem implementar o método equals(). Para classificação, os elementos devem implementar a interface Comparable (a implementação de compareTo() deve ser consistente com a implementação do método equals()) ou passar uma implementação de Comparator no construtor. Caso contrário, uma exceção será lançada.

Aqui está um exemplo semelhante ao anterior implementado com TreeSet:

```
Set<String> set = new TreeSet<>();
System.out.println(set.add("b")); // true
System.out.println(set.add("x")); // true
System.out.println(set.add("h")); // true
System.out.println(set.add("b")); // false
System.out.println(set); // [b, h, x]
```

Como String implementa Comparable e seu método compareTo() implementa ordenação lexicográfica, um Set é ordenado dessa maneira.

Observe que este exemplo não adiciona valores nulos. Isso porque TreeSet não os aceita. Se você tentar adicionar null a um TreeSet, um NullPointerException será lançado.

Isso ocorre porque quando um elemento é adicionado, ele é comparado (como um comparável ou com um comparador) com outros valores para inseri-lo na ordem correta, mas não pode fazer isso com um valor nulo.


### Queue

Em uma Fila, os elementos são normalmente adicionados e removidos de maneira FIFO (primeiro a entrar, primeiro a sair).

A implementação mais usada é ArrayDeque, que é apoiado por um array, que tem a funcionalidade de adicionar e remover elementos da frente (como uma pilha) e de trás (como uma fila) da fila, e não em qualquer posição como em um ArrayList. Esta classe não permite a inserção de valores nulos.

Além de possuir os métodos de Collection, ArrayDeque possui outros métodos que são exclusivos das filas. Podemos classificar esses métodos em dois grupos:

Métodos que lançam uma exceção (ClassCastException, NullPointerException, IllegalStateException ou IllegalArgumentException) se algo der errado:

* `boolean add(E e)` Adiciona um elemento ao final da fila e retorna verdadeiro se for bem-sucedido ou lança uma exceção caso contrário.
* `E remove()` Remove e retorna o primeiro elemento da fila ou lança uma exceção se estiver vazio.
* `E element()` Retorna o próximo elemento da fila ou lança uma exceção se estiver vazio.
* `boolean offer(E e)` Adiciona um elemento ao final da fila e retorna verdadeiro se for bem-sucedido ou falso caso contrário.

Métodos que retornam nulo se algo der errado:

* `E poll()` Remove e retorna o primeiro elemento da fila ou nulo se estiver vazio.
* `E peek()` Returns the next element of the queue or null if it's empty.

Para cada operação, há uma versão que lança uma exceção e outra que retorna falso ou nulo. Por exemplo, quando a fila está vazia, o método remove() lança uma exceção, enquanto o método poll() retorna nulo.

```
Queue<String> queue = new ArrayDeque<>();
System.out.println(queue.offer("a")); // true [a]
System.out.println(queue.offer("b")); // true [a, b]
System.out.println(queue.peek()); // a [a, b]
System.out.println(queue.poll()); // a [b]
System.out.println(queue.peek()); // b [b]
System.out.println(queue.poll()); // b []
System.out.println(queue.peek()); // null
```

Você também pode usar essa classe como uma pilha, uma estrutura de dados que ordena os elementos em um LIFO (último a entrar, primeiro a sair), ao usar os seguintes métodos:

```
// Adds elements to the front of the queue
void push(E e)

// Removes and returns the next element 
// or throws an exception if the queue is empty
E pop() 
```

Observe que esses métodos não estão na interface Queue:

```
ArrayDeque<String> stack = new ArrayDeque<>();
stack.push("a"); // [a]
stack.push("b"); // [b, a]
System.out.println(stack.peek()); // b [b, a]
System.out.println(stack.pop()); // b [a]
System.out.println(stack.peek()); // a [a]
System.out.println(stack.pop()); // a []
System.out.println(stack.peek()); // null
```


### Map

Enquanto uma Lista usa um índice para acessar seus elementos, um Mapa usa uma chave que pode ser de qualquer tipo (geralmente uma String) para obter um valor.

Portanto, um mapa não pode conter chaves duplicadas e uma chave está associada a um valor (que pode ser qualquer objeto, até mesmo outro mapa, ou nulo).

As duas implementações mais usadas são HashMap e TreeMap. A diferença entre eles é que TreeMap classifica as chaves, mas adiciona e recupera as chaves no tempo log(n), enquanto o HashMap não garante a ordem, mas adiciona e recupera as chaves mais rapidamente.

É importante que os objetos usados ​​como chaves tenham os métodos equals() e hashCode() implementados.

Uma vez que Map não implementa Collection, seus métodos são diferentes. Aqui está um exemplo que mostra os mais importantes:

```
Map<String, Integer> map = new HashMap<>();

// Adding a key/value pair
System.out.println( map.put("oranges", 7) ); // null
System.out.println( map.put("apples", 5) ); // null
System.out.println( map.put("lemons", 2) ); // null
System.out.println( map.put("bananas", 7) ); // null

// Replacing the value of an existing key. Returns the old one
System.out.println( map.put("apples", 4) ); // 5
 System.out.println( map.size() ); // 4

// {oranges=7, bananas=7, apples=4, lemons=2}
System.out.println(map);

// Getting a value
System.out.println( map.get("oranges") ); // 7

// Testing if the map contains a key
System.out.println( map.containsKey("apples") ); // true
// Testing if the map contains a value
System.out.println( map.containsValue(5) ); // false

// Removing the key/value pair and returning the value
System.out.println( map.remove("lemons") ); // 2
// Returns null if it can't find the key
System.out.println( map.remove("lemons") ); // null

// Getting the keys as a Set
// (changes are reflected on the map and vice-versa)
Set<String> keys = map.keySet(); // [oranges, bananas, apples]

// Getting the values as a Collection
// (changes are reflected on the map and vice-versa)
Collection<Integer> values = map.values(); // [7, 7, 4]

// Removing all key/value pairs
map.clear();

System.out.println( map.isEmpty() ); // true
```

Se alterarmos a implementação para TreeMap, o mapa será armazenado em uma estrutura de árvore vermelha e preta e classificado como um TreeSet, por um Comparador ou Comparable, com a ordem natural de sua chave por padrão:

```
Map<String, Integer> map = new TreeMap<>();

System.out.println( map.put("oranges", 7) ); // null
System.out.println( map.put("apples", 5) ); // null
System.out.println( map.put("lemons", 2) ); // null
System.out.println( map.put("bananas", 7) ); // null

// {apples=5 , bananas=7, lemons=2, oranges=7}
System.out.println(map);

// [apples, bananas, lemons, oranges]
Set<String> keys = map.keySet();
 Collection<Integer> values = map.values(); // [5, 7, 2, 7]
```

Observe que, devido à maneira como a classificação é feita (novamente, como TreeSet); um TreeMap não pode ter um valor nulo como chave:

```
Map<String, Integer> map = new TreeMap<>();
map.put(null, 1); // throws NullPointerException!
```

No entanto, um HashMap pode:

```
Map<String, Integer> map = new HashMap<>();
map.put(null, 1); // OK
```


### Summary

Collection | Interface | Implements Collection? | Allows duplicates? | Allows null values? | Ordered?
-----------|-----------|------------------------|--------------------|---------------------|---------
ArrayList  |	List   |		Yes				|		Yes			 |			Yes		   | Yes (Insertion Order)
HashSet	   |	Set	   |		Yes				|		No			 |			Yes		   | No
TreeSet	   | 	Set	   |		Yes				|		No			 |			No		   | Yes (Natural order or by Comparator)
ArrayDeque |	Queue Deque |	Yes				|		Yes			 |			No		   | Yes (FIFO or LIFO)
HashMap	   |	Map	   |		No				| Just for values	 |			Yes		   | No
TreeMap	   |	Map	   |		No	   			| Just for values    |			No		   | Yes (Natural order or by Comparator)


### Self Test

1. Given:

```
public class Question_7_1 {
    public static void main(String[] args) {
        ArrayDeque<Integer> deque =
              new ArrayDeque<Integer>();
        deque.push(1);
        deque.push(2);
        deque.push(3);
        deque.poll();
        System.out.println(deque);
    }
}
```

What is the result? <br>
A. [1, 2, 3] <br>
B. [1, 2] <br>
C. [2, 1] <br>
D. An exception occurs at runtime


2. Which of the following options can throw a NullPointerException?

A.
```
TreeSet<String> s = new TreeSet<>();
s.add(null);
```

B.
```
HashMap<String, String> m = new HashMap<>();
m.put(null, null);
```

C.
```
ArrayList<String> arr = new ArrayList<>();
arr.add(null);
```

D.
```
HashSet<String> s = new HashSet<String>();
s.add(null);
```


3. Given:

```
public class Question_7_3 {
    public static void main(String[] args) {
        List<Integer> list = new ArrayList<>();
        list.add(1);
        list.add(2);
        list.add(3);
        list.remove(1);
        System.out.println(list);
    }
}
```

What is the result? <br>
A. [2, 3] <br>
B. [1, 3] <br>
C. [1, 2, 3] <br>
D. An exception occurs at runtime

4. Which of the following statements is true? <br>
A. HashSet is an implementation of Map. <br>
B. Objects used as values of a TreeMap are required to implement Comparable. <br>
C. Objects used as values of a TreeMap are required to implement the hashCode() method. <br>
D. Objects used as keys of a TreeMap are required to implement the hashCode() method.



































