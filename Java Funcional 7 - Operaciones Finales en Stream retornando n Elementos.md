Si queremos convertir un Stream en una Collection o un Map disponemos del siguiente método de Stream:

| Interfaz      | Método                                                |
|---------------|-------------------------------------------------------|
| `Stream<T>`   | `<R,A> R collect(Collector<? super T,A,R> collector)` |

Como parámetro hay que pasarle un Collector y existen los siguientes predefinidos en Collectors:

| Clase          | Método                                                                                                                                        |
|----------------|-----------------------------------------------------------------------------------------------------------------------------------------------|
| `Collectors`   | `public static <T> Collector<T,?,List<T>> toList()`                                                                                           |
| `Collectors`   | `public static <T> Collector<T,?,Set<T>> toSet()`                                                                                             |
| `Collectors`   | `public static <T,C extends Collection<T>> Collector<T,?,C> toCollection(Supplier<C> collectionFactory)`                                      |
| `Collectors`   | `public static <T,K,U> Collector<T,?,Map<K,U>> toMap(Function<? super T,? extends K> keyMapper, Function<? super T,? extends U> valueMapper)` |
| `Collectors`   | `public static <T,A,R,RR> Collector<T,A,RR> collectingAndThen(Collector<T,A,R> downstream, Function<R,RR> finisher)`                          |
| `Collectors`   | `public static <T,K> Collector<T,?,Map<K,List<T>>> groupingBy(Function<? super T,? extends K> classifier)`                                    |
| `Collectors`   | `public static <T,K,A,D> Collector<T,?,Map<K,D>> groupingBy(Function<? super T,? extends K> classifier, Collector<? super T,A,D> downstream)` |
| `Collectors`   | `public static <T> Collector<T,?,Map<Boolean,List<T>>> partitioningBy(Predicate<? super T> predicate)`                                        |
| `Collectors`   | `public static <T,D,A> Collector<T,?,Map<Boolean,D>> partitioningBy(Predicate<? super T> predicate, Collector<? super T,A,D> downstream)`     |

Ejemplos de uso de Collectors en el método collect de Stream:

```java
import java.util.*;
import java.util.stream.*;

record User(Long id, String name, String nickName, Integer age) {

}

public class Main {
    Comparator<Integer> comparatorAge = (Integer age1, Integer age2) -> {
        if(age1 == null) {
            if(age2 == null) {
                return 0;
            } else {
                return -1;
            }
        } else if(age2 == null) {
            return 1;
        }

        return age2.compareTo(age1);
    };

    Comparator<User> comparatorUserByAge = (User user1, User user2) -> {
        if(user1 == null) {
            if(user2 == null) {
                return 0;
            } else {
                return -1;
            }
        } else if(user2 == null) {
            return 1;
        }

        return comparatorAge.compare(user1.age(), user2.age());
    };

    Comparator<Map.Entry<Long, User>> comparatorUserMapEntryByAge = (Map.Entry<Long, User> userMapEntry1, Map.Entry<Long, User> userMapEntry2) -> {
        if(userMapEntry1 == null) {
            if(userMapEntry2 == null) {
                return 0;
            } else {
                return -1;
            }
        } else if(userMapEntry2 == null) {
            return 1;
        }

        return comparatorUserByAge.compare(userMapEntry1.getValue(), userMapEntry2.getValue());
    };

    User courtOfOwls = new User(0L, null, "Court of Owls", null);
    User batman = new User(1L, "Bruce Wayne", "Batman", 30);
    User alfred = new User(2L, "Alfred Pennyworth", "Alfred", 55);
    User robin1 = new User(3L, "Dick Grayson", "Robin", 20);
    User robin2 = new User(4L, "Tim Drake", "Robin", 15);
    User robin3 = new User(5L, "Damian Wayne", "Robin", 12);

    List<User> userList = Arrays.asList(batman, alfred, robin1, null, robin2, courtOfOwls, robin1, robin3).stream().sorted(comparatorUserByAge).collect(Collectors.toList());
    Set<User> userSet = userList.stream().sorted(comparatorUserByAge).collect(Collectors.toSet());
    Map<Long, User> userMap = userSet.stream().sorted(comparatorUserByAge).collect(Collectors.toMap(
            user -> (user != null ? user.id() : null),
            user -> (user != null ? user : new User(null, null, null, null))));

    public static void main(String[] args) {
        Main main = new Main();
        main.executionMethod();
    }

    public void executionMethod() {
        System.out.println("BEFORE PROCESSING");
        System.out.println();
        System.out.println("Initial User List:");
        this.printCollection(userList);
        System.out.println();
        System.out.println("Initial User Set:");
        this.printCollection(userSet);
        System.out.println();
        System.out.println("Initial User Map:");
        this.printMap(userMap);
        System.out.println();

        System.out.println("PROCESSING USER LIST");
        System.out.println();
        this.processUserCollection(userList);
        System.out.println();

        System.out.println("PROCESSING USER SET");
        System.out.println();
        this.processUserCollection(userSet);
        System.out.println();

        System.out.println("PROCESSING USER MAP");
        System.out.println();
        Stream<Map.Entry<Long, User>> userMapEntryStream = userMap.entrySet().stream();
        this.processAndCollectToMapForUserMap(userMapEntryStream);
    }

    private void processUserCollection(Collection<User> userCollection) {
        System.out.println("1. Collectors.toList()");
        this.processAndCollectToList(userCollection.stream());

        System.out.println("2. Collectors.toSet()");
        this.processAndCollectToSet(userCollection.stream());

        System.out.println("3. Collectors.toCollection()");
        this.processAndCollectToLinkedList(userCollection.stream());

        System.out.println("4. Collectors.toMap()");
        this.processAndCollectToMap(userCollection.stream());

        System.out.println("5. Collectors.collectingAndThen()");
        this.processAndCollectToUnmodifiableList(userCollection.stream());

        System.out.println("6. Collectors.groupingBy() sin collector");
        this.processAndCollectGroupingByNickName(userCollection.stream());

        System.out.println("7. Collectors.groupingBy() con collector");
        this.processAndCollectGroupingByNickNameAsSet(userCollection.stream());

        System.out.println("8. Collectors.partitioningBy() sin collector");
        this.processAndCollectPartitioningByNickNameRobin(userCollection.stream());

        System.out.println("9. Collectors.partitioningBy() con collector");
        this.processAndCollectPartitioningByNickNameRobinAsSet(userCollection.stream());
    }

    private void processAndCollectToList(Stream<User> userStream) {
        try {
            List<User> result = this.processUserStream(userStream)
                    .collect(Collectors.toList());

            this.printCollectionAfterCollect(result);
        } catch(Exception e) {
            System.out.println(e.getClass().getCanonicalName() + " " + e.getMessage());
        }
        System.out.println();
    }

    private void processAndCollectToSet(Stream<User> userStream) {
        try {
            Set<User> result = this.processUserStream(userStream)
                    .collect(Collectors.toSet());

            this.printCollectionAfterCollect(result);
        } catch(Exception e) {
            System.out.println(e.getClass().getCanonicalName() + " " + e.getMessage());
        }
        System.out.println();
    }

    private void processAndCollectToLinkedList(Stream<User> userStream) {
        try {
            LinkedList<User> result = this.processUserStream(userStream)
                    .collect(Collectors.toCollection(LinkedList::new));

            this.printCollectionAfterCollect(result);
        } catch(Exception e) {
            System.out.println(e.getClass().getCanonicalName() + " " + e.getMessage());
        }
        System.out.println();
    }

    private void processAndCollectToMap(Stream<User> userStream) {
        try {
            Map<Long, User> result = this.processUserStream(userStream)
                    .collect(Collectors.toMap(user -> user.id(), user -> user));

            this.printMapAfterCollect(result);
        } catch(Exception e) {
            System.out.println(e.getClass().getCanonicalName() + " " + e.getMessage());
        }
        System.out.println();
    }

    private void processAndCollectToUnmodifiableList(Stream<User> userStream) {
        try {
            List<User> result = this.processUserStream(userStream)
                    .collect(Collectors.collectingAndThen(Collectors.toList(), Collections::unmodifiableList));

            this.printCollectionAfterCollect(result);
        } catch(Exception e) {
            System.out.println(e.getClass().getCanonicalName() + " " + e.getMessage());
        }
        System.out.println();
    }

    private void processAndCollectGroupingByNickName(Stream<User> userStream) {
        try {
            Map<String, List<User>> result = this.processUserStream(userStream)
                    .collect(Collectors.groupingBy(user -> user.nickName()));

            this.printMapAfterCollect(result);
        } catch(Exception e) {
            System.out.println(e.getClass().getCanonicalName() + " " + e.getMessage());
        }
        System.out.println();
    }

    private void processAndCollectGroupingByNickNameAsSet(Stream<User> userStream) {
        try {
            Map<String, Set<User>> result = this.processUserStream(userStream)
                    .collect(Collectors.groupingBy(user -> user.nickName(), Collectors.toSet()));

            this.printMapAfterCollect(result);
        } catch(Exception e) {
            System.out.println(e.getClass().getCanonicalName() + " " + e.getMessage());
        }
        System.out.println();
    }

    private void processAndCollectPartitioningByNickNameRobin(Stream<User> userStream) {
        try {
            Map<Boolean, List<User>> result = this.processUserStream(userStream)
                    .collect(Collectors.partitioningBy(user -> user.nickName().equals("ROBIN")));

            this.printMapAfterCollect(result);
        } catch(Exception e) {
            System.out.println(e.getClass().getCanonicalName() + " " + e.getMessage());
        }
        System.out.println();
    }

    private void processAndCollectPartitioningByNickNameRobinAsSet(Stream<User> userStream) {
        try {
            Map<Boolean, Set<User>> result = this.processUserStream(userStream)
                    .collect(Collectors.partitioningBy(user -> user.nickName().equals("ROBIN"), Collectors.toSet()));

            this.printMapAfterCollect(result);
        } catch(Exception e) {
            System.out.println(e.getClass().getCanonicalName() + " " + e.getMessage());
        }
        System.out.println();
    }

    private void processAndCollectToMapForUserMap(Stream<Map.Entry<Long, User>> userMapEntryStream) {
        try {
            Map<Long, User> result = this.processUserMapEntryStream(userMapEntryStream)
                    //.collect(Collectors.toMap(Map.Entry::getKey, Map.Entry::getValue));
                    .collect(Collectors.toMap(entry -> entry.getKey(), entry -> entry.getValue()));

            this.printMapAfterCollect(result);
        } catch(Exception e) {
            System.out.println(e.getClass().getCanonicalName() + " " + e.getMessage());
        }
        System.out.println();
    }

    private Stream<User> processUserStream(Stream<User> userStream) {
        Stream<User> result = userStream
                .filter(Objects::nonNull)
                .filter(user -> Objects.nonNull(user.name()))
                .map(user -> new User(user.id(), user.name(), user.nickName().toUpperCase(), user.age()))
                .sorted(comparatorUserByAge)
                .peek((user) -> System.out.println("Antes de collect: " + user));

        return result;
    }

    private Stream<Map.Entry<Long, User>> processUserMapEntryStream(Stream<Map.Entry<Long, User>> userMapEntryStream) {
        Stream<Map.Entry<Long, User>> result = userMapEntryStream
                .filter(Objects::nonNull)
                .filter(entry -> Objects.nonNull(entry.getValue().name()))
                .map(entry -> Map.entry(entry.getKey(), new User(entry.getValue().id(), entry.getValue().name(), entry.getValue().nickName().toUpperCase(), entry.getValue().age())))
                .sorted(comparatorUserMapEntryByAge)
                .peek((entry) -> System.out.println("Antes de collect: " + entry));

        return result;
    }

    private <T> void printCollectionAfterCollect(Collection<T> collection) {
        System.out.print("Tras collect. ");
        this.printCollection(collection);
    }

    private <K, V> void printMapAfterCollect(Map<K, V> map) {
        System.out.print("Tras collect. ");
        this.printMap(map);
    }

    private <T> void printCollection(Collection<T> collection) {
        System.out.println(collection.getClass().getCanonicalName() + ":");
        int i = 0;
        for(T element : collection) {
            System.out.println("- [" + i + "]: " + element);
            i++;
        }
    }

    private <K, V> void printMap(Map<K, V> map) {
        System.out.println(map.getClass().getCanonicalName() + ":");
        map.forEach((k, v) -> System.out.println("- " + k + " --> " + v));
    }
}
```

```txt
BEFORE PROCESSING

Initial User List:
java.util.ArrayList:
- [0]: null
- [1]: User[id=0, name=null, nickName=Court of Owls, age=null]
- [2]: User[id=2, name=Alfred Pennyworth, nickName=Alfred, age=55]
- [3]: User[id=1, name=Bruce Wayne, nickName=Batman, age=30]
- [4]: User[id=3, name=Dick Grayson, nickName=Robin, age=20]
- [5]: User[id=3, name=Dick Grayson, nickName=Robin, age=20]
- [6]: User[id=4, name=Tim Drake, nickName=Robin, age=15]
- [7]: User[id=5, name=Damian Wayne, nickName=Robin, age=12]

Initial User Set:
java.util.HashSet:
- [0]: null
- [1]: User[id=5, name=Damian Wayne, nickName=Robin, age=12]
- [2]: User[id=4, name=Tim Drake, nickName=Robin, age=15]
- [3]: User[id=1, name=Bruce Wayne, nickName=Batman, age=30]
- [4]: User[id=2, name=Alfred Pennyworth, nickName=Alfred, age=55]
- [5]: User[id=3, name=Dick Grayson, nickName=Robin, age=20]
- [6]: User[id=0, name=null, nickName=Court of Owls, age=null]

Initial User Map:
java.util.HashMap:
- null --> User[id=null, name=null, nickName=null, age=null]
- 0 --> User[id=0, name=null, nickName=Court of Owls, age=null]
- 1 --> User[id=1, name=Bruce Wayne, nickName=Batman, age=30]
- 2 --> User[id=2, name=Alfred Pennyworth, nickName=Alfred, age=55]
- 3 --> User[id=3, name=Dick Grayson, nickName=Robin, age=20]
- 4 --> User[id=4, name=Tim Drake, nickName=Robin, age=15]
- 5 --> User[id=5, name=Damian Wayne, nickName=Robin, age=12]

PROCESSING USER LIST

1. Collectors.toList()
Antes de collect: User[id=2, name=Alfred Pennyworth, nickName=ALFRED, age=55]
Antes de collect: User[id=1, name=Bruce Wayne, nickName=BATMAN, age=30]
Antes de collect: User[id=3, name=Dick Grayson, nickName=ROBIN, age=20]
Antes de collect: User[id=3, name=Dick Grayson, nickName=ROBIN, age=20]
Antes de collect: User[id=4, name=Tim Drake, nickName=ROBIN, age=15]
Antes de collect: User[id=5, name=Damian Wayne, nickName=ROBIN, age=12]
Tras collect. java.util.ArrayList:
- [0]: User[id=2, name=Alfred Pennyworth, nickName=ALFRED, age=55]
- [1]: User[id=1, name=Bruce Wayne, nickName=BATMAN, age=30]
- [2]: User[id=3, name=Dick Grayson, nickName=ROBIN, age=20]
- [3]: User[id=3, name=Dick Grayson, nickName=ROBIN, age=20]
- [4]: User[id=4, name=Tim Drake, nickName=ROBIN, age=15]
- [5]: User[id=5, name=Damian Wayne, nickName=ROBIN, age=12]

2. Collectors.toSet()
Antes de collect: User[id=2, name=Alfred Pennyworth, nickName=ALFRED, age=55]
Antes de collect: User[id=1, name=Bruce Wayne, nickName=BATMAN, age=30]
Antes de collect: User[id=3, name=Dick Grayson, nickName=ROBIN, age=20]
Antes de collect: User[id=3, name=Dick Grayson, nickName=ROBIN, age=20]
Antes de collect: User[id=4, name=Tim Drake, nickName=ROBIN, age=15]
Antes de collect: User[id=5, name=Damian Wayne, nickName=ROBIN, age=12]
Tras collect. java.util.HashSet:
- [0]: User[id=5, name=Damian Wayne, nickName=ROBIN, age=12]
- [1]: User[id=1, name=Bruce Wayne, nickName=BATMAN, age=30]
- [2]: User[id=3, name=Dick Grayson, nickName=ROBIN, age=20]
- [3]: User[id=4, name=Tim Drake, nickName=ROBIN, age=15]
- [4]: User[id=2, name=Alfred Pennyworth, nickName=ALFRED, age=55]

3. Collectors.toCollection()
Antes de collect: User[id=2, name=Alfred Pennyworth, nickName=ALFRED, age=55]
Antes de collect: User[id=1, name=Bruce Wayne, nickName=BATMAN, age=30]
Antes de collect: User[id=3, name=Dick Grayson, nickName=ROBIN, age=20]
Antes de collect: User[id=3, name=Dick Grayson, nickName=ROBIN, age=20]
Antes de collect: User[id=4, name=Tim Drake, nickName=ROBIN, age=15]
Antes de collect: User[id=5, name=Damian Wayne, nickName=ROBIN, age=12]
Tras collect. java.util.LinkedList:
- [0]: User[id=2, name=Alfred Pennyworth, nickName=ALFRED, age=55]
- [1]: User[id=1, name=Bruce Wayne, nickName=BATMAN, age=30]
- [2]: User[id=3, name=Dick Grayson, nickName=ROBIN, age=20]
- [3]: User[id=3, name=Dick Grayson, nickName=ROBIN, age=20]
- [4]: User[id=4, name=Tim Drake, nickName=ROBIN, age=15]
- [5]: User[id=5, name=Damian Wayne, nickName=ROBIN, age=12]

4. Collectors.toMap()
Antes de collect: User[id=2, name=Alfred Pennyworth, nickName=ALFRED, age=55]
Antes de collect: User[id=1, name=Bruce Wayne, nickName=BATMAN, age=30]
Antes de collect: User[id=3, name=Dick Grayson, nickName=ROBIN, age=20]
Antes de collect: User[id=3, name=Dick Grayson, nickName=ROBIN, age=20]
java.lang.IllegalStateException Duplicate key 3 (attempted merging values User[id=3, name=Dick Grayson, nickName=ROBIN, age=20] and User[id=3, name=Dick Grayson, nickName=ROBIN, age=20])

5. Collectors.collectingAndThen()
Antes de collect: User[id=2, name=Alfred Pennyworth, nickName=ALFRED, age=55]
Antes de collect: User[id=1, name=Bruce Wayne, nickName=BATMAN, age=30]
Antes de collect: User[id=3, name=Dick Grayson, nickName=ROBIN, age=20]
Antes de collect: User[id=3, name=Dick Grayson, nickName=ROBIN, age=20]
Antes de collect: User[id=4, name=Tim Drake, nickName=ROBIN, age=15]
Antes de collect: User[id=5, name=Damian Wayne, nickName=ROBIN, age=12]
Tras collect. java.util.Collections.UnmodifiableRandomAccessList:
- [0]: User[id=2, name=Alfred Pennyworth, nickName=ALFRED, age=55]
- [1]: User[id=1, name=Bruce Wayne, nickName=BATMAN, age=30]
- [2]: User[id=3, name=Dick Grayson, nickName=ROBIN, age=20]
- [3]: User[id=3, name=Dick Grayson, nickName=ROBIN, age=20]
- [4]: User[id=4, name=Tim Drake, nickName=ROBIN, age=15]
- [5]: User[id=5, name=Damian Wayne, nickName=ROBIN, age=12]

6. Collectors.groupingBy() sin collector
Antes de collect: User[id=2, name=Alfred Pennyworth, nickName=ALFRED, age=55]
Antes de collect: User[id=1, name=Bruce Wayne, nickName=BATMAN, age=30]
Antes de collect: User[id=3, name=Dick Grayson, nickName=ROBIN, age=20]
Antes de collect: User[id=3, name=Dick Grayson, nickName=ROBIN, age=20]
Antes de collect: User[id=4, name=Tim Drake, nickName=ROBIN, age=15]
Antes de collect: User[id=5, name=Damian Wayne, nickName=ROBIN, age=12]
Tras collect. java.util.HashMap:
- ROBIN --> [User[id=3, name=Dick Grayson, nickName=ROBIN, age=20], User[id=3, name=Dick Grayson, nickName=ROBIN, age=20], User[id=4, name=Tim Drake, nickName=ROBIN, age=15], User[id=5, name=Damian Wayne, nickName=ROBIN, age=12]]
- ALFRED --> [User[id=2, name=Alfred Pennyworth, nickName=ALFRED, age=55]]
- BATMAN --> [User[id=1, name=Bruce Wayne, nickName=BATMAN, age=30]]

7. Collectors.groupingBy() con collector
Antes de collect: User[id=2, name=Alfred Pennyworth, nickName=ALFRED, age=55]
Antes de collect: User[id=1, name=Bruce Wayne, nickName=BATMAN, age=30]
Antes de collect: User[id=3, name=Dick Grayson, nickName=ROBIN, age=20]
Antes de collect: User[id=3, name=Dick Grayson, nickName=ROBIN, age=20]
Antes de collect: User[id=4, name=Tim Drake, nickName=ROBIN, age=15]
Antes de collect: User[id=5, name=Damian Wayne, nickName=ROBIN, age=12]
Tras collect. java.util.HashMap:
- ROBIN --> [User[id=5, name=Damian Wayne, nickName=ROBIN, age=12], User[id=3, name=Dick Grayson, nickName=ROBIN, age=20], User[id=4, name=Tim Drake, nickName=ROBIN, age=15]]
- ALFRED --> [User[id=2, name=Alfred Pennyworth, nickName=ALFRED, age=55]]
- BATMAN --> [User[id=1, name=Bruce Wayne, nickName=BATMAN, age=30]]

8. Collectors.partitioningBy() sin collector
Antes de collect: User[id=2, name=Alfred Pennyworth, nickName=ALFRED, age=55]
Antes de collect: User[id=1, name=Bruce Wayne, nickName=BATMAN, age=30]
Antes de collect: User[id=3, name=Dick Grayson, nickName=ROBIN, age=20]
Antes de collect: User[id=3, name=Dick Grayson, nickName=ROBIN, age=20]
Antes de collect: User[id=4, name=Tim Drake, nickName=ROBIN, age=15]
Antes de collect: User[id=5, name=Damian Wayne, nickName=ROBIN, age=12]
Tras collect. java.util.stream.Collectors.Partition:
- false --> [User[id=2, name=Alfred Pennyworth, nickName=ALFRED, age=55], User[id=1, name=Bruce Wayne, nickName=BATMAN, age=30]]
- true --> [User[id=3, name=Dick Grayson, nickName=ROBIN, age=20], User[id=3, name=Dick Grayson, nickName=ROBIN, age=20], User[id=4, name=Tim Drake, nickName=ROBIN, age=15], User[id=5, name=Damian Wayne, nickName=ROBIN, age=12]]

9. Collectors.partitioningBy() con collector
Antes de collect: User[id=2, name=Alfred Pennyworth, nickName=ALFRED, age=55]
Antes de collect: User[id=1, name=Bruce Wayne, nickName=BATMAN, age=30]
Antes de collect: User[id=3, name=Dick Grayson, nickName=ROBIN, age=20]
Antes de collect: User[id=3, name=Dick Grayson, nickName=ROBIN, age=20]
Antes de collect: User[id=4, name=Tim Drake, nickName=ROBIN, age=15]
Antes de collect: User[id=5, name=Damian Wayne, nickName=ROBIN, age=12]
Tras collect. java.util.stream.Collectors.Partition:
- false --> [User[id=1, name=Bruce Wayne, nickName=BATMAN, age=30], User[id=2, name=Alfred Pennyworth, nickName=ALFRED, age=55]]
- true --> [User[id=5, name=Damian Wayne, nickName=ROBIN, age=12], User[id=3, name=Dick Grayson, nickName=ROBIN, age=20], User[id=4, name=Tim Drake, nickName=ROBIN, age=15]]


PROCESSING USER SET

1. Collectors.toList()
Antes de collect: User[id=2, name=Alfred Pennyworth, nickName=ALFRED, age=55]
Antes de collect: User[id=1, name=Bruce Wayne, nickName=BATMAN, age=30]
Antes de collect: User[id=3, name=Dick Grayson, nickName=ROBIN, age=20]
Antes de collect: User[id=4, name=Tim Drake, nickName=ROBIN, age=15]
Antes de collect: User[id=5, name=Damian Wayne, nickName=ROBIN, age=12]
Tras collect. java.util.ArrayList:
- [0]: User[id=2, name=Alfred Pennyworth, nickName=ALFRED, age=55]
- [1]: User[id=1, name=Bruce Wayne, nickName=BATMAN, age=30]
- [2]: User[id=3, name=Dick Grayson, nickName=ROBIN, age=20]
- [3]: User[id=4, name=Tim Drake, nickName=ROBIN, age=15]
- [4]: User[id=5, name=Damian Wayne, nickName=ROBIN, age=12]

2. Collectors.toSet()
Antes de collect: User[id=2, name=Alfred Pennyworth, nickName=ALFRED, age=55]
Antes de collect: User[id=1, name=Bruce Wayne, nickName=BATMAN, age=30]
Antes de collect: User[id=3, name=Dick Grayson, nickName=ROBIN, age=20]
Antes de collect: User[id=4, name=Tim Drake, nickName=ROBIN, age=15]
Antes de collect: User[id=5, name=Damian Wayne, nickName=ROBIN, age=12]
Tras collect. java.util.HashSet:
- [0]: User[id=5, name=Damian Wayne, nickName=ROBIN, age=12]
- [1]: User[id=1, name=Bruce Wayne, nickName=BATMAN, age=30]
- [2]: User[id=3, name=Dick Grayson, nickName=ROBIN, age=20]
- [3]: User[id=4, name=Tim Drake, nickName=ROBIN, age=15]
- [4]: User[id=2, name=Alfred Pennyworth, nickName=ALFRED, age=55]

3. Collectors.toCollection()
Antes de collect: User[id=2, name=Alfred Pennyworth, nickName=ALFRED, age=55]
Antes de collect: User[id=1, name=Bruce Wayne, nickName=BATMAN, age=30]
Antes de collect: User[id=3, name=Dick Grayson, nickName=ROBIN, age=20]
Antes de collect: User[id=4, name=Tim Drake, nickName=ROBIN, age=15]
Antes de collect: User[id=5, name=Damian Wayne, nickName=ROBIN, age=12]
Tras collect. java.util.LinkedList:
- [0]: User[id=2, name=Alfred Pennyworth, nickName=ALFRED, age=55]
- [1]: User[id=1, name=Bruce Wayne, nickName=BATMAN, age=30]
- [2]: User[id=3, name=Dick Grayson, nickName=ROBIN, age=20]
- [3]: User[id=4, name=Tim Drake, nickName=ROBIN, age=15]
- [4]: User[id=5, name=Damian Wayne, nickName=ROBIN, age=12]

4. Collectors.toMap()
Antes de collect: User[id=2, name=Alfred Pennyworth, nickName=ALFRED, age=55]
Antes de collect: User[id=1, name=Bruce Wayne, nickName=BATMAN, age=30]
Antes de collect: User[id=3, name=Dick Grayson, nickName=ROBIN, age=20]
Antes de collect: User[id=4, name=Tim Drake, nickName=ROBIN, age=15]
Antes de collect: User[id=5, name=Damian Wayne, nickName=ROBIN, age=12]
Tras collect. java.util.HashMap:
- 1 --> User[id=1, name=Bruce Wayne, nickName=BATMAN, age=30]
- 2 --> User[id=2, name=Alfred Pennyworth, nickName=ALFRED, age=55]
- 3 --> User[id=3, name=Dick Grayson, nickName=ROBIN, age=20]
- 4 --> User[id=4, name=Tim Drake, nickName=ROBIN, age=15]
- 5 --> User[id=5, name=Damian Wayne, nickName=ROBIN, age=12]

5. Collectors.collectingAndThen()
Antes de collect: User[id=2, name=Alfred Pennyworth, nickName=ALFRED, age=55]
Antes de collect: User[id=1, name=Bruce Wayne, nickName=BATMAN, age=30]
Antes de collect: User[id=3, name=Dick Grayson, nickName=ROBIN, age=20]
Antes de collect: User[id=4, name=Tim Drake, nickName=ROBIN, age=15]
Antes de collect: User[id=5, name=Damian Wayne, nickName=ROBIN, age=12]
Tras collect. java.util.Collections.UnmodifiableRandomAccessList:
- [0]: User[id=2, name=Alfred Pennyworth, nickName=ALFRED, age=55]
- [1]: User[id=1, name=Bruce Wayne, nickName=BATMAN, age=30]
- [2]: User[id=3, name=Dick Grayson, nickName=ROBIN, age=20]
- [3]: User[id=4, name=Tim Drake, nickName=ROBIN, age=15]
- [4]: User[id=5, name=Damian Wayne, nickName=ROBIN, age=12]

6. Collectors.groupingBy() sin collector
Antes de collect: User[id=2, name=Alfred Pennyworth, nickName=ALFRED, age=55]
Antes de collect: User[id=1, name=Bruce Wayne, nickName=BATMAN, age=30]
Antes de collect: User[id=3, name=Dick Grayson, nickName=ROBIN, age=20]
Antes de collect: User[id=4, name=Tim Drake, nickName=ROBIN, age=15]
Antes de collect: User[id=5, name=Damian Wayne, nickName=ROBIN, age=12]
Tras collect. java.util.HashMap:
- ROBIN --> [User[id=3, name=Dick Grayson, nickName=ROBIN, age=20], User[id=4, name=Tim Drake, nickName=ROBIN, age=15], User[id=5, name=Damian Wayne, nickName=ROBIN, age=12]]
- ALFRED --> [User[id=2, name=Alfred Pennyworth, nickName=ALFRED, age=55]]
- BATMAN --> [User[id=1, name=Bruce Wayne, nickName=BATMAN, age=30]]

7. Collectors.groupingBy() con collector
Antes de collect: User[id=2, name=Alfred Pennyworth, nickName=ALFRED, age=55]
Antes de collect: User[id=1, name=Bruce Wayne, nickName=BATMAN, age=30]
Antes de collect: User[id=3, name=Dick Grayson, nickName=ROBIN, age=20]
Antes de collect: User[id=4, name=Tim Drake, nickName=ROBIN, age=15]
Antes de collect: User[id=5, name=Damian Wayne, nickName=ROBIN, age=12]
Tras collect. java.util.HashMap:
- ROBIN --> [User[id=5, name=Damian Wayne, nickName=ROBIN, age=12], User[id=3, name=Dick Grayson, nickName=ROBIN, age=20], User[id=4, name=Tim Drake, nickName=ROBIN, age=15]]
- ALFRED --> [User[id=2, name=Alfred Pennyworth, nickName=ALFRED, age=55]]
- BATMAN --> [User[id=1, name=Bruce Wayne, nickName=BATMAN, age=30]]

8. Collectors.partitioningBy() sin collector
Antes de collect: User[id=2, name=Alfred Pennyworth, nickName=ALFRED, age=55]
Antes de collect: User[id=1, name=Bruce Wayne, nickName=BATMAN, age=30]
Antes de collect: User[id=3, name=Dick Grayson, nickName=ROBIN, age=20]
Antes de collect: User[id=4, name=Tim Drake, nickName=ROBIN, age=15]
Antes de collect: User[id=5, name=Damian Wayne, nickName=ROBIN, age=12]
Tras collect. java.util.stream.Collectors.Partition:
- false --> [User[id=2, name=Alfred Pennyworth, nickName=ALFRED, age=55], User[id=1, name=Bruce Wayne, nickName=BATMAN, age=30]]
- true --> [User[id=3, name=Dick Grayson, nickName=ROBIN, age=20], User[id=4, name=Tim Drake, nickName=ROBIN, age=15], User[id=5, name=Damian Wayne, nickName=ROBIN, age=12]]

9. Collectors.partitioningBy() con collector
Antes de collect: User[id=2, name=Alfred Pennyworth, nickName=ALFRED, age=55]
Antes de collect: User[id=1, name=Bruce Wayne, nickName=BATMAN, age=30]
Antes de collect: User[id=3, name=Dick Grayson, nickName=ROBIN, age=20]
Antes de collect: User[id=4, name=Tim Drake, nickName=ROBIN, age=15]
Antes de collect: User[id=5, name=Damian Wayne, nickName=ROBIN, age=12]
Tras collect. java.util.stream.Collectors.Partition:
- false --> [User[id=1, name=Bruce Wayne, nickName=BATMAN, age=30], User[id=2, name=Alfred Pennyworth, nickName=ALFRED, age=55]]
- true --> [User[id=5, name=Damian Wayne, nickName=ROBIN, age=12], User[id=3, name=Dick Grayson, nickName=ROBIN, age=20], User[id=4, name=Tim Drake, nickName=ROBIN, age=15]]


PROCESSING USER MAP

Antes de collect: 2=User[id=2, name=Alfred Pennyworth, nickName=ALFRED, age=55]
Antes de collect: 1=User[id=1, name=Bruce Wayne, nickName=BATMAN, age=30]
Antes de collect: 3=User[id=3, name=Dick Grayson, nickName=ROBIN, age=20]
Antes de collect: 4=User[id=4, name=Tim Drake, nickName=ROBIN, age=15]
Antes de collect: 5=User[id=5, name=Damian Wayne, nickName=ROBIN, age=12]
Tras collect. java.util.HashMap:
- 1 --> User[id=1, name=Bruce Wayne, nickName=BATMAN, age=30]
- 2 --> User[id=2, name=Alfred Pennyworth, nickName=ALFRED, age=55]
- 3 --> User[id=3, name=Dick Grayson, nickName=ROBIN, age=20]
- 4 --> User[id=4, name=Tim Drake, nickName=ROBIN, age=15]
- 5 --> User[id=5, name=Damian Wayne, nickName=ROBIN, age=12]

```
