Si queremos convertir un Stream en un elemento disponemos de los siguientes métodos de Stream:

| Interfaz    | Método                                                                                        |
|-------------|-----------------------------------------------------------------------------------------------|
| `Stream<T>` | `Optional<T> reduce(BinaryOperator<T> accumulator)`                                           |
| `Stream<T>` | `T reduce(T identity, BinaryOperator<T> accumulator)`                                         |
| `Stream<T>` | `<U> U reduce(U identity, BiFunction<U,? super T,U> accumulator, BinaryOperator<U> combiner)` |

donde `BinaryOperator<T>` hereda de `BiFunction<T,T,T>`.

Los métodos `reduce` reciben los siguientes posibles parámetros:
- **identity**: un elemento que es el valor inicial de la reducción y el resultado por defecto si el stream es empty
- **accumulator**: una función con dos parámetros (el resultado parcial de la reducción y el próximo elemento del stream)
- **combiner**: una función utilizada para alguna de las siguientes dos razones:
  - **en streams no paralelos**: cuando no coinciden los tipos de argumentos del acumulador
  - **en streams paralelos**: para combinar el resultado parcial de las reducciones realizadas en cada substream

Ejemplos de uso de reduce de Stream:

```java
import java.util.*;
import java.util.function.BiFunction;
import java.util.function.BinaryOperator;
import java.util.stream.Collectors;

public class Main {
    List emptyList = new ArrayList();

    Set emptySet = new HashSet();

    List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6);
    List<String> letters = Arrays.asList("a", "b", "c", "d", "e");

    User courtOfOwls = new User(0L, "Court of Owls", null);
    User batman = new User(1L, "Bruce Wayne", 30);
    User alfred = new User(2L, "Alfred Pennyworth", 55);
    User robin1 = new User(3L, "Dick Grayson", 20);
    User robin2 = new User(4L, "Tim Drake", 15);
    User robin3 = new User(5L, "Damian Wayne", 12);

    List<User> userList = Arrays.asList(batman, alfred, robin1, null, robin2, courtOfOwls, robin1, robin3);

    Set<User> userSet = userList.stream().collect(Collectors.toSet());

    SingleClassReduction<Integer> integerAdditionReduction = this.createIntegerAdditionReduction();
    SingleClassReduction<String> stringConcatenationReduction = this.createStringConcatenationReduction();
    MultipleClassReduction<User, List<User>> userListAdditionReduction = this.createUserListAdditionReduction();
    MultipleClassReduction<User, Integer> userAgeAdditionReduction = this.createUserAgeAdditionReduction();
    SingleClassReduction<User> userAgeMaxReduction = this.createUserAgeMaxReduction();
    MultipleClassReduction<User, UserAgeAverage> userAgeAverageReduction = this.createUserAgeAverageReduction();

    public static void main(String[] args) {
        Main main = new Main();
        main.executionMethod();
    }

    public void executionMethod() {
        System.out.println("- First Reduce: ");
        System.out.println("  Optional<T> reduce(BinaryOperator<T> accumulator)");
        System.out.println();
        System.out.println("- Second Reduce: ");
        System.out.println("  T reduce(T identity, BinaryOperator<T> accumulator)");
        System.out.println();
        System.out.println("- Third Reduce: ");
        System.out.println("  <U> U reduce(U identity, BiFunction<U,? super T,U> accumulator, BinaryOperator<U> combiner)");
        System.out.println();

        System.out.println("1. Reduce Integer Addition");
        System.out.println();
        this.firstReduceIntegerAddition();
        this.secondReduceIntegerAddition();
        this.thirdReduceIntegerAddition();
        System.out.println();

        System.out.println("2. Reduce String Concatenation");
        System.out.println();
        this.firstReduceStringConcatenation();
        this.secondReduceStringConcatenation();
        this.thirdReduceStringConcatenation();
        System.out.println();

        System.out.println("3. Reduce User List Addition");
        System.out.println();
        this.firstReduceUserListAddition();
        this.secondReduceUserListAddition();
        this.thirdReduceUserListAddition();
        System.out.println();

        System.out.println("4. Reduce User Age Addition");
        System.out.println();
        this.firstReduceUserAgeAddition();
        this.secondReduceUserAgeAddition();
        this.thirdReduceUserAgeAddition();
        System.out.println();

        System.out.println("5. Reduce User Age Max");
        System.out.println();
        this.firstReduceUserAgeMax();
        this.secondReduceUserAgeMax();
        this.thirdReduceUserAgeMax();
        System.out.println();

        System.out.println("6. Reduce User Age Average");
        System.out.println();
        this.firstReduceUserAgeAverage();
        this.secondReduceUserAgeAverage();
        this.thirdReduceUserAgeAverage();
        System.out.println();
    }

    //-----------------------------------------------------------------------------------------------------------------

    // INTEGER ADDITION

    private void firstReduceIntegerAddition() {
        integerAdditionReduction.firstReduction(emptyList);
        integerAdditionReduction.firstReduction(numbers);
    }

    private void secondReduceIntegerAddition() {
        integerAdditionReduction.secondReduction(emptyList);
        integerAdditionReduction.secondReduction(numbers);
    }

    private void thirdReduceIntegerAddition() {
        integerAdditionReduction.thirdReduction(emptyList);
        integerAdditionReduction.thirdReduction(numbers);
    }

    // STRING CONCATENATION

    private void firstReduceStringConcatenation() {
        stringConcatenationReduction.firstReduction(emptyList);
        stringConcatenationReduction.firstReduction(letters);
    }

    private void secondReduceStringConcatenation() {
        stringConcatenationReduction.secondReduction(emptyList);
        stringConcatenationReduction.secondReduction(letters);
    }

    private void thirdReduceStringConcatenation() {
        stringConcatenationReduction.thirdReduction(emptyList);
        stringConcatenationReduction.thirdReduction(letters);
    }

    // USER LIST ADDITION

    private void firstReduceUserListAddition() {
        userListAdditionReduction.firstReduction(emptyList);
        userListAdditionReduction.firstReduction(userList);
    }

    private void secondReduceUserListAddition() {
        userListAdditionReduction.secondReduction(emptyList);
        userListAdditionReduction.secondReduction(userList);
    }

    private void thirdReduceUserListAddition() {
        userListAdditionReduction.thirdReduction(emptyList);
        userListAdditionReduction.thirdReduction(userList);
    }

    // USER AGE ADDITION

    private void firstReduceUserAgeAddition() {
        userAgeAdditionReduction.firstReduction(emptyList);
        userAgeAdditionReduction.firstReduction(userList);
    }

    private void secondReduceUserAgeAddition() {
        userAgeAdditionReduction.secondReduction(emptyList);
        userAgeAdditionReduction.secondReduction(userList);
    }

    private void thirdReduceUserAgeAddition() {
        userAgeAdditionReduction.thirdReduction(emptyList);
        userAgeAdditionReduction.thirdReduction(userList);
    }

    // USER AGE MAX

    private void firstReduceUserAgeMax() {
        userAgeMaxReduction.firstReduction(emptyList);
        userAgeMaxReduction.firstReduction(userList);
    }

    private void secondReduceUserAgeMax() {
        userAgeMaxReduction.secondReduction(emptyList);
        userAgeMaxReduction.secondReduction(userList);
    }

    private void thirdReduceUserAgeMax() {
        userAgeMaxReduction.thirdReduction(emptyList);
        userAgeMaxReduction.thirdReduction(userList);
    }

    // USER AGE AVERAGE

    private void firstReduceUserAgeAverage() {
        userAgeAverageReduction.firstReduction(emptySet);
        userAgeAverageReduction.firstReduction(userSet);
    }

    private void secondReduceUserAgeAverage() {
        userAgeAverageReduction.secondReduction(emptySet);
        userAgeAverageReduction.secondReduction(userSet);
    }

    private void thirdReduceUserAgeAverage() {
        userAgeAverageReduction.thirdReduction(emptySet);
        userAgeAverageReduction.thirdReduction(userSet);
    }

    //-----------------------------------------------------------------------------------------------------------------

    // INTEGER ADDITION

    private SingleClassReduction<Integer> createIntegerAdditionReduction() {
        BinaryOperator<Integer> integerAccumulator = (partialResult, currentInteger) -> {
            String operation = "- integerAccumulator(" + partialResult + ", " + currentInteger + ")";

            Integer result = ((partialResult != null) ? partialResult : 0) + ((currentInteger != null) ? currentInteger : 0);

            System.out.println(operation + " <=> " + result);
            return result;
        };

        BinaryOperator<Integer> integerCombiner = (partialResult1, partialResult2) -> {
            String operation = "- integerCombiner(" + partialResult1 + ", " + partialResult2 + ")";

            Integer result = ((partialResult1 != null) ? partialResult1 : 0) + ((partialResult2 != null) ? partialResult2 : 0);

            System.out.println(operation + " <=> " + result);
            return result;
        };

        SingleClassReduction<Integer> integerAdditionReduction =
                new SingleClassReduction<>(0, integerAccumulator, integerCombiner);

        return integerAdditionReduction;
    }

    // STRING CONCATENATION

    private SingleClassReduction<String> createStringConcatenationReduction() {
        BinaryOperator<String> stringAccumulator = (partialResult, currentString) -> {
            String operation = "- stringAccumulator(" + partialResult + ", " + currentString + ")";

            String result = ((partialResult != null) ? partialResult : "") + ((currentString != null) ? currentString.toUpperCase() : "");

            System.out.println(operation + " <=> " + result);
            return result;
        };

        BinaryOperator<String> stringCombiner = (partialResult1, partialResult2) -> {
            String operation = "- stringCombiner(" + partialResult1 + ", " + partialResult2 + ")";

            String result = ((partialResult1 != null) ? partialResult1 : "") + ((partialResult2 != null) ? partialResult2 : "");

            System.out.println(operation + " <=> " + result);
            return result;
        };

        SingleClassReduction<String> stringConcatenationReduction =
                new SingleClassReduction<>("", stringAccumulator, stringCombiner);

        return stringConcatenationReduction;
    }

    // USER LIST ADDITION

    private MultipleClassReduction<User, List<User>> createUserListAdditionReduction() {
        BiFunction<List<User>, User, List<User>> userAccumulator = (partialResult, currentUser) -> {
            String operation = "- userAccumulator(" + partialResult + ", " + currentUser + ")";

            List<User> result = new ArrayList<>();
            if(partialResult != null) {
                result.addAll(partialResult);
            }
            if(currentUser != null) {
                result.add(currentUser);
            }

            System.out.println(operation + " <=> " + result);
            return result;
        };

        BinaryOperator<List<User>> userCombiner = (partialResult1, partialResult2) -> {
            String operation = "- userCombiner(" + partialResult1 + ", " + partialResult2 + ")";

            List<User> result = new ArrayList<>();
            if(partialResult1 != null) {
                result.addAll(partialResult1);
            }
            if(partialResult2 != null) {
                result.addAll(partialResult2);
            }

            System.out.println(operation + " <=> " + result);
            return result;
        };

        MultipleClassReduction<User, List<User>> userListAdditionReduction =
                new MultipleClassReduction<>(new ArrayList<>(), userAccumulator, userCombiner);

        return userListAdditionReduction;
    }

    // USER AGE ADDITION

    private MultipleClassReduction<User, Integer> createUserAgeAdditionReduction() {
        BiFunction<Integer, User, Integer> userAgeAccumulator = (partialResult, currentUser) -> {
            String operation = "- userAgeAccumulator(" + partialResult + ", " + currentUser + ")";

            Integer result = ((partialResult != null) ? partialResult : 0) + ((currentUser != null && currentUser.age() != null) ? currentUser.age() : 0);

            System.out.println(operation + " <=> " + result);
            return result;
        };

        BinaryOperator<Integer> userAgeCombiner = (partialResult1, partialResult2) -> {
            String operation = "- userAgeCombiner(" + partialResult1 + ", " + partialResult2 + ")";

            Integer result = ((partialResult1 != null) ? partialResult1 : 0) + ((partialResult2 != null) ? partialResult2 : 0);

            System.out.println(operation + " <=> " + result);
            return result;
        };

        MultipleClassReduction<User, Integer> userAgeAdditionReduction =
                new MultipleClassReduction<>(0, userAgeAccumulator, userAgeCombiner);

        return userAgeAdditionReduction;
    }

    // USER AGE MAX

    private SingleClassReduction<User> createUserAgeMaxReduction() {
        BinaryOperator<User> userAgeMaxAccumulator = (partialResult, currentUser) -> {
            String operation = "- userAgeMaxAccumulator(" + partialResult + ", " + currentUser + ")";

            User result;
            if(currentUser == null || currentUser.age() == null) {
                result = partialResult;
            } else if(partialResult == null || partialResult.age() == null) {
                result = currentUser;
            } else {
                result = ((currentUser.age() > partialResult.age()) ? currentUser : partialResult);
            }

            System.out.println(operation + " <=> " + result);
            return result;
        };

        BinaryOperator<User> userAgeMaxCombiner = (partialResult1, partialResult2) -> {
            String operation = "- userAgeMaxCombiner(" + partialResult1 + ", " + partialResult2 + ")";

            User result;
            if(partialResult2 == null || partialResult2.age() == null) {
                result = partialResult1;
            } else if(partialResult1 == null || partialResult1.age() == null) {
                result = partialResult2;
            } else {
                result = ((partialResult2.age() > partialResult1.age()) ? partialResult2 : partialResult1);
            }

            System.out.println(operation + " <=> " + result);
            return result;
        };

        SingleClassReduction<User> userAgeMaxReduction =
                new SingleClassReduction<>(null, userAgeMaxAccumulator, userAgeMaxCombiner);

        return userAgeMaxReduction;
    }

    // USER AGE AVERAGE

    private MultipleClassReduction<User, UserAgeAverage> createUserAgeAverageReduction() {
        BiFunction<UserAgeAverage, User, UserAgeAverage> userAgeAverageAccumulator = (partialResult, currentUser) -> {
            String operation = "- userAgeAverageAccumulator(" + partialResult + ", " + currentUser + ")";

            if(partialResult == null) {
                partialResult = new UserAgeAverage(0, 0, (Double.valueOf(0) / 0));
            }

            UserAgeAverage result;
            if(currentUser != null && currentUser.age() != null) {
                result = partialResult.sum(new UserAgeAverage(currentUser.age(), 1, (Double.valueOf(currentUser.age()) / 1)));
            } else {
                result = partialResult;
            }

            System.out.println(operation + " <=> " + result);
            return result;
        };

        BinaryOperator<UserAgeAverage> userAgeAverageCombiner = (partialResult1, partialResult2) -> {
            String operation = "- userAgeAverageCombiner(" + partialResult1 + ", " + partialResult2 + ")";

            if(partialResult1 == null) {
                partialResult1 = new UserAgeAverage(0, 0, (Double.valueOf(0) / 0));
            }

            UserAgeAverage result = partialResult1.sum(partialResult2);

            System.out.println(operation + " <=> " + result);
            return result;
        };

        MultipleClassReduction<User, UserAgeAverage> userAgeAverageReduction =
                new MultipleClassReduction<>(new UserAgeAverage(0, 0, (Double.valueOf(0) / 0)), userAgeAverageAccumulator, userAgeAverageCombiner);

        return userAgeAverageReduction;
    }
}

record User(Long id, String name, Integer age) {

}

record UserAgeAverage(Integer ageTotal, Integer userCount, Double average) {
    public UserAgeAverage sum(UserAgeAverage userAgeAverage) {
        Integer newAgeTotal = 0;
        Integer newUserCount = 0;

        if(this.ageTotal != null) {
            newAgeTotal = newAgeTotal + this.ageTotal;
        }

        if(this.userCount != null) {
            newUserCount = newUserCount + this.userCount;
        }

        if(userAgeAverage != null) {
            if (userAgeAverage.ageTotal != null) {
                newAgeTotal = newAgeTotal + userAgeAverage.ageTotal;
            }

            if (userAgeAverage.userCount != null) {
                newUserCount = newUserCount + userAgeAverage.userCount;
            }
        }

        return new UserAgeAverage(newAgeTotal, newUserCount, (Double.valueOf(newAgeTotal) / newUserCount));
    }
}

record SingleClassReduction<T> (T identity, BinaryOperator<T> accumulator,  BinaryOperator<T> combiner) {
    public synchronized void firstReduction(Collection<T> collectionParam) {
        System.out.println("firstSingleClassReduction args: " + collectionParam);

        Optional<T> result = collectionParam.stream()
                .filter(Objects::nonNull)
                .reduce(accumulator);

        System.out.println("firstSingleClassReduction result: " + result);
        System.out.println();
    }

    public synchronized void secondReduction(Collection<T> collectionParam) {
        System.out.println("secondSingleClassReduction args: " + collectionParam);

        T result = collectionParam.stream()
                .filter(Objects::nonNull)
                .reduce(identity, accumulator);

        System.out.println("secondSingleClassReduction result: " + result);
        System.out.println();
    }

    public synchronized void thirdReduction(Collection<T> collectionParam) {
        System.out.println("thirdSingleClassReduction args: " + collectionParam);

        T result = collectionParam.stream()
                .filter(Objects::nonNull)
                .reduce(identity, accumulator, combiner);

        System.out.println("thirdSingleClassReduction result: " + result);
        System.out.println();
    }
}

record MultipleClassReduction<T,U> (U identity, BiFunction<U,T,U> accumulator, BinaryOperator<U> combiner) {
    public synchronized void firstReduction(Collection<T> collectionParam) {
        System.out.println("firstMultipleClassReduction args: " + collectionParam);

        System.out.println("firstMultipleClassReduction result: no compila porque el resultado parcial y el elemento actual del accumulator son de distinta clase");
        System.out.println();
    }

    public synchronized void secondReduction(Collection<T> collectionParam) {
        System.out.println("secondMultipleClassReduction args: " + collectionParam);

        System.out.println("secondMultipleClassReduction result: no compila porque el resultado parcial y el elemento actual del accumulator son de distinta clase");
        System.out.println();
    }

    public synchronized void thirdReduction(Collection<T> collectionParam) {
        System.out.println("thirdMultipleClassReduction args: " + collectionParam);

        U result = collectionParam.stream()
                .filter(Objects::nonNull)
                .reduce(identity, accumulator, combiner);

        System.out.println("thirdMultipleClassReduction result: " + result);
        System.out.println();
    }
}
```

```txt
- First Reduce: 
  Optional<T> reduce(BinaryOperator<T> accumulator)

- Second Reduce: 
  T reduce(T identity, BinaryOperator<T> accumulator)

- Third Reduce: 
  <U> U reduce(U identity, BiFunction<U,? super T,U> accumulator, BinaryOperator<U> combiner)

1. Reduce Integer Addition

firstSingleClassReduction args: []
firstSingleClassReduction result: Optional.empty

firstSingleClassReduction args: [1, 2, 3, 4, 5, 6]
- integerAccumulator(1, 2) <=> 3
- integerAccumulator(3, 3) <=> 6
- integerAccumulator(6, 4) <=> 10
- integerAccumulator(10, 5) <=> 15
- integerAccumulator(15, 6) <=> 21
firstSingleClassReduction result: Optional[21]

secondSingleClassReduction args: []
secondSingleClassReduction result: 0

secondSingleClassReduction args: [1, 2, 3, 4, 5, 6]
- integerAccumulator(0, 1) <=> 1
- integerAccumulator(1, 2) <=> 3
- integerAccumulator(3, 3) <=> 6
- integerAccumulator(6, 4) <=> 10
- integerAccumulator(10, 5) <=> 15
- integerAccumulator(15, 6) <=> 21
secondSingleClassReduction result: 21

thirdSingleClassReduction args: []
thirdSingleClassReduction result: 0

thirdSingleClassReduction args: [1, 2, 3, 4, 5, 6]
- integerAccumulator(0, 1) <=> 1
- integerAccumulator(1, 2) <=> 3
- integerAccumulator(3, 3) <=> 6
- integerAccumulator(6, 4) <=> 10
- integerAccumulator(10, 5) <=> 15
- integerAccumulator(15, 6) <=> 21
thirdSingleClassReduction result: 21


2. Reduce String Concatenation

firstSingleClassReduction args: []
firstSingleClassReduction result: Optional.empty

firstSingleClassReduction args: [a, b, c, d, e]
- stringAccumulator(a, b) <=> aB
- stringAccumulator(aB, c) <=> aBC
- stringAccumulator(aBC, d) <=> aBCD
- stringAccumulator(aBCD, e) <=> aBCDE
firstSingleClassReduction result: Optional[aBCDE]

secondSingleClassReduction args: []
secondSingleClassReduction result: 

secondSingleClassReduction args: [a, b, c, d, e]
- stringAccumulator(, a) <=> A
- stringAccumulator(A, b) <=> AB
- stringAccumulator(AB, c) <=> ABC
- stringAccumulator(ABC, d) <=> ABCD
- stringAccumulator(ABCD, e) <=> ABCDE
secondSingleClassReduction result: ABCDE

thirdSingleClassReduction args: []
thirdSingleClassReduction result: 

thirdSingleClassReduction args: [a, b, c, d, e]
- stringAccumulator(, a) <=> A
- stringAccumulator(A, b) <=> AB
- stringAccumulator(AB, c) <=> ABC
- stringAccumulator(ABC, d) <=> ABCD
- stringAccumulator(ABCD, e) <=> ABCDE
thirdSingleClassReduction result: ABCDE


3. Reduce User List Addition

firstMultipleClassReduction args: []
firstMultipleClassReduction result: no compila porque el resultado parcial y el elemento actual del accumulator son de distinta clase

firstMultipleClassReduction args: [User[id=1, name=Bruce Wayne, age=30], User[id=2, name=Alfred Pennyworth, age=55], User[id=3, name=Dick Grayson, age=20], null, User[id=4, name=Tim Drake, age=15], User[id=0, name=Court of Owls, age=null], User[id=3, name=Dick Grayson, age=20], User[id=5, name=Damian Wayne, age=12]]
firstMultipleClassReduction result: no compila porque el resultado parcial y el elemento actual del accumulator son de distinta clase

secondMultipleClassReduction args: []
secondMultipleClassReduction result: no compila porque el resultado parcial y el elemento actual del accumulator son de distinta clase

secondMultipleClassReduction args: [User[id=1, name=Bruce Wayne, age=30], User[id=2, name=Alfred Pennyworth, age=55], User[id=3, name=Dick Grayson, age=20], null, User[id=4, name=Tim Drake, age=15], User[id=0, name=Court of Owls, age=null], User[id=3, name=Dick Grayson, age=20], User[id=5, name=Damian Wayne, age=12]]
secondMultipleClassReduction result: no compila porque el resultado parcial y el elemento actual del accumulator son de distinta clase

thirdMultipleClassReduction args: []
thirdMultipleClassReduction result: []

thirdMultipleClassReduction args: [User[id=1, name=Bruce Wayne, age=30], User[id=2, name=Alfred Pennyworth, age=55], User[id=3, name=Dick Grayson, age=20], null, User[id=4, name=Tim Drake, age=15], User[id=0, name=Court of Owls, age=null], User[id=3, name=Dick Grayson, age=20], User[id=5, name=Damian Wayne, age=12]]
- userAccumulator([], User[id=1, name=Bruce Wayne, age=30]) <=> [User[id=1, name=Bruce Wayne, age=30]]
- userAccumulator([User[id=1, name=Bruce Wayne, age=30]], User[id=2, name=Alfred Pennyworth, age=55]) <=> [User[id=1, name=Bruce Wayne, age=30], User[id=2, name=Alfred Pennyworth, age=55]]
- userAccumulator([User[id=1, name=Bruce Wayne, age=30], User[id=2, name=Alfred Pennyworth, age=55]], User[id=3, name=Dick Grayson, age=20]) <=> [User[id=1, name=Bruce Wayne, age=30], User[id=2, name=Alfred Pennyworth, age=55], User[id=3, name=Dick Grayson, age=20]]
- userAccumulator([User[id=1, name=Bruce Wayne, age=30], User[id=2, name=Alfred Pennyworth, age=55], User[id=3, name=Dick Grayson, age=20]], User[id=4, name=Tim Drake, age=15]) <=> [User[id=1, name=Bruce Wayne, age=30], User[id=2, name=Alfred Pennyworth, age=55], User[id=3, name=Dick Grayson, age=20], User[id=4, name=Tim Drake, age=15]]
- userAccumulator([User[id=1, name=Bruce Wayne, age=30], User[id=2, name=Alfred Pennyworth, age=55], User[id=3, name=Dick Grayson, age=20], User[id=4, name=Tim Drake, age=15]], User[id=0, name=Court of Owls, age=null]) <=> [User[id=1, name=Bruce Wayne, age=30], User[id=2, name=Alfred Pennyworth, age=55], User[id=3, name=Dick Grayson, age=20], User[id=4, name=Tim Drake, age=15], User[id=0, name=Court of Owls, age=null]]
- userAccumulator([User[id=1, name=Bruce Wayne, age=30], User[id=2, name=Alfred Pennyworth, age=55], User[id=3, name=Dick Grayson, age=20], User[id=4, name=Tim Drake, age=15], User[id=0, name=Court of Owls, age=null]], User[id=3, name=Dick Grayson, age=20]) <=> [User[id=1, name=Bruce Wayne, age=30], User[id=2, name=Alfred Pennyworth, age=55], User[id=3, name=Dick Grayson, age=20], User[id=4, name=Tim Drake, age=15], User[id=0, name=Court of Owls, age=null], User[id=3, name=Dick Grayson, age=20]]
- userAccumulator([User[id=1, name=Bruce Wayne, age=30], User[id=2, name=Alfred Pennyworth, age=55], User[id=3, name=Dick Grayson, age=20], User[id=4, name=Tim Drake, age=15], User[id=0, name=Court of Owls, age=null], User[id=3, name=Dick Grayson, age=20]], User[id=5, name=Damian Wayne, age=12]) <=> [User[id=1, name=Bruce Wayne, age=30], User[id=2, name=Alfred Pennyworth, age=55], User[id=3, name=Dick Grayson, age=20], User[id=4, name=Tim Drake, age=15], User[id=0, name=Court of Owls, age=null], User[id=3, name=Dick Grayson, age=20], User[id=5, name=Damian Wayne, age=12]]
thirdMultipleClassReduction result: [User[id=1, name=Bruce Wayne, age=30], User[id=2, name=Alfred Pennyworth, age=55], User[id=3, name=Dick Grayson, age=20], User[id=4, name=Tim Drake, age=15], User[id=0, name=Court of Owls, age=null], User[id=3, name=Dick Grayson, age=20], User[id=5, name=Damian Wayne, age=12]]


4. Reduce User Age Addition

firstMultipleClassReduction args: []
firstMultipleClassReduction result: no compila porque el resultado parcial y el elemento actual del accumulator son de distinta clase

firstMultipleClassReduction args: [User[id=1, name=Bruce Wayne, age=30], User[id=2, name=Alfred Pennyworth, age=55], User[id=3, name=Dick Grayson, age=20], null, User[id=4, name=Tim Drake, age=15], User[id=0, name=Court of Owls, age=null], User[id=3, name=Dick Grayson, age=20], User[id=5, name=Damian Wayne, age=12]]
firstMultipleClassReduction result: no compila porque el resultado parcial y el elemento actual del accumulator son de distinta clase

secondMultipleClassReduction args: []
secondMultipleClassReduction result: no compila porque el resultado parcial y el elemento actual del accumulator son de distinta clase

secondMultipleClassReduction args: [User[id=1, name=Bruce Wayne, age=30], User[id=2, name=Alfred Pennyworth, age=55], User[id=3, name=Dick Grayson, age=20], null, User[id=4, name=Tim Drake, age=15], User[id=0, name=Court of Owls, age=null], User[id=3, name=Dick Grayson, age=20], User[id=5, name=Damian Wayne, age=12]]
secondMultipleClassReduction result: no compila porque el resultado parcial y el elemento actual del accumulator son de distinta clase

thirdMultipleClassReduction args: []
thirdMultipleClassReduction result: 0

thirdMultipleClassReduction args: [User[id=1, name=Bruce Wayne, age=30], User[id=2, name=Alfred Pennyworth, age=55], User[id=3, name=Dick Grayson, age=20], null, User[id=4, name=Tim Drake, age=15], User[id=0, name=Court of Owls, age=null], User[id=3, name=Dick Grayson, age=20], User[id=5, name=Damian Wayne, age=12]]
- userAgeAccumulator(0, User[id=1, name=Bruce Wayne, age=30]) <=> 30
- userAgeAccumulator(30, User[id=2, name=Alfred Pennyworth, age=55]) <=> 85
- userAgeAccumulator(85, User[id=3, name=Dick Grayson, age=20]) <=> 105
- userAgeAccumulator(105, User[id=4, name=Tim Drake, age=15]) <=> 120
- userAgeAccumulator(120, User[id=0, name=Court of Owls, age=null]) <=> 120
- userAgeAccumulator(120, User[id=3, name=Dick Grayson, age=20]) <=> 140
- userAgeAccumulator(140, User[id=5, name=Damian Wayne, age=12]) <=> 152
thirdMultipleClassReduction result: 152


5. Reduce User Age Max

firstSingleClassReduction args: []
firstSingleClassReduction result: Optional.empty

firstSingleClassReduction args: [User[id=1, name=Bruce Wayne, age=30], User[id=2, name=Alfred Pennyworth, age=55], User[id=3, name=Dick Grayson, age=20], null, User[id=4, name=Tim Drake, age=15], User[id=0, name=Court of Owls, age=null], User[id=3, name=Dick Grayson, age=20], User[id=5, name=Damian Wayne, age=12]]
- userAgeMaxAccumulator(User[id=1, name=Bruce Wayne, age=30], User[id=2, name=Alfred Pennyworth, age=55]) <=> User[id=2, name=Alfred Pennyworth, age=55]
- userAgeMaxAccumulator(User[id=2, name=Alfred Pennyworth, age=55], User[id=3, name=Dick Grayson, age=20]) <=> User[id=2, name=Alfred Pennyworth, age=55]
- userAgeMaxAccumulator(User[id=2, name=Alfred Pennyworth, age=55], User[id=4, name=Tim Drake, age=15]) <=> User[id=2, name=Alfred Pennyworth, age=55]
- userAgeMaxAccumulator(User[id=2, name=Alfred Pennyworth, age=55], User[id=0, name=Court of Owls, age=null]) <=> User[id=2, name=Alfred Pennyworth, age=55]
- userAgeMaxAccumulator(User[id=2, name=Alfred Pennyworth, age=55], User[id=3, name=Dick Grayson, age=20]) <=> User[id=2, name=Alfred Pennyworth, age=55]
- userAgeMaxAccumulator(User[id=2, name=Alfred Pennyworth, age=55], User[id=5, name=Damian Wayne, age=12]) <=> User[id=2, name=Alfred Pennyworth, age=55]
firstSingleClassReduction result: Optional[User[id=2, name=Alfred Pennyworth, age=55]]

secondSingleClassReduction args: []
secondSingleClassReduction result: null

secondSingleClassReduction args: [User[id=1, name=Bruce Wayne, age=30], User[id=2, name=Alfred Pennyworth, age=55], User[id=3, name=Dick Grayson, age=20], null, User[id=4, name=Tim Drake, age=15], User[id=0, name=Court of Owls, age=null], User[id=3, name=Dick Grayson, age=20], User[id=5, name=Damian Wayne, age=12]]
- userAgeMaxAccumulator(null, User[id=1, name=Bruce Wayne, age=30]) <=> User[id=1, name=Bruce Wayne, age=30]
- userAgeMaxAccumulator(User[id=1, name=Bruce Wayne, age=30], User[id=2, name=Alfred Pennyworth, age=55]) <=> User[id=2, name=Alfred Pennyworth, age=55]
- userAgeMaxAccumulator(User[id=2, name=Alfred Pennyworth, age=55], User[id=3, name=Dick Grayson, age=20]) <=> User[id=2, name=Alfred Pennyworth, age=55]
- userAgeMaxAccumulator(User[id=2, name=Alfred Pennyworth, age=55], User[id=4, name=Tim Drake, age=15]) <=> User[id=2, name=Alfred Pennyworth, age=55]
- userAgeMaxAccumulator(User[id=2, name=Alfred Pennyworth, age=55], User[id=0, name=Court of Owls, age=null]) <=> User[id=2, name=Alfred Pennyworth, age=55]
- userAgeMaxAccumulator(User[id=2, name=Alfred Pennyworth, age=55], User[id=3, name=Dick Grayson, age=20]) <=> User[id=2, name=Alfred Pennyworth, age=55]
- userAgeMaxAccumulator(User[id=2, name=Alfred Pennyworth, age=55], User[id=5, name=Damian Wayne, age=12]) <=> User[id=2, name=Alfred Pennyworth, age=55]
secondSingleClassReduction result: User[id=2, name=Alfred Pennyworth, age=55]

thirdSingleClassReduction args: []
thirdSingleClassReduction result: null

thirdSingleClassReduction args: [User[id=1, name=Bruce Wayne, age=30], User[id=2, name=Alfred Pennyworth, age=55], User[id=3, name=Dick Grayson, age=20], null, User[id=4, name=Tim Drake, age=15], User[id=0, name=Court of Owls, age=null], User[id=3, name=Dick Grayson, age=20], User[id=5, name=Damian Wayne, age=12]]
- userAgeMaxAccumulator(null, User[id=1, name=Bruce Wayne, age=30]) <=> User[id=1, name=Bruce Wayne, age=30]
- userAgeMaxAccumulator(User[id=1, name=Bruce Wayne, age=30], User[id=2, name=Alfred Pennyworth, age=55]) <=> User[id=2, name=Alfred Pennyworth, age=55]
- userAgeMaxAccumulator(User[id=2, name=Alfred Pennyworth, age=55], User[id=3, name=Dick Grayson, age=20]) <=> User[id=2, name=Alfred Pennyworth, age=55]
- userAgeMaxAccumulator(User[id=2, name=Alfred Pennyworth, age=55], User[id=4, name=Tim Drake, age=15]) <=> User[id=2, name=Alfred Pennyworth, age=55]
- userAgeMaxAccumulator(User[id=2, name=Alfred Pennyworth, age=55], User[id=0, name=Court of Owls, age=null]) <=> User[id=2, name=Alfred Pennyworth, age=55]
- userAgeMaxAccumulator(User[id=2, name=Alfred Pennyworth, age=55], User[id=3, name=Dick Grayson, age=20]) <=> User[id=2, name=Alfred Pennyworth, age=55]
- userAgeMaxAccumulator(User[id=2, name=Alfred Pennyworth, age=55], User[id=5, name=Damian Wayne, age=12]) <=> User[id=2, name=Alfred Pennyworth, age=55]
thirdSingleClassReduction result: User[id=2, name=Alfred Pennyworth, age=55]


6. Reduce User Age Average

firstMultipleClassReduction args: []
firstMultipleClassReduction result: no compila porque el resultado parcial y el elemento actual del accumulator son de distinta clase

firstMultipleClassReduction args: [null, User[id=3, name=Dick Grayson, age=20], User[id=4, name=Tim Drake, age=15], User[id=1, name=Bruce Wayne, age=30], User[id=5, name=Damian Wayne, age=12], User[id=0, name=Court of Owls, age=null], User[id=2, name=Alfred Pennyworth, age=55]]
firstMultipleClassReduction result: no compila porque el resultado parcial y el elemento actual del accumulator son de distinta clase

secondMultipleClassReduction args: []
secondMultipleClassReduction result: no compila porque el resultado parcial y el elemento actual del accumulator son de distinta clase

secondMultipleClassReduction args: [null, User[id=3, name=Dick Grayson, age=20], User[id=4, name=Tim Drake, age=15], User[id=1, name=Bruce Wayne, age=30], User[id=5, name=Damian Wayne, age=12], User[id=0, name=Court of Owls, age=null], User[id=2, name=Alfred Pennyworth, age=55]]
secondMultipleClassReduction result: no compila porque el resultado parcial y el elemento actual del accumulator son de distinta clase

thirdMultipleClassReduction args: []
thirdMultipleClassReduction result: UserAgeAverage[ageTotal=0, userCount=0, average=NaN]

thirdMultipleClassReduction args: [null, User[id=3, name=Dick Grayson, age=20], User[id=4, name=Tim Drake, age=15], User[id=1, name=Bruce Wayne, age=30], User[id=5, name=Damian Wayne, age=12], User[id=0, name=Court of Owls, age=null], User[id=2, name=Alfred Pennyworth, age=55]]
- userAgeAverageAccumulator(UserAgeAverage[ageTotal=0, userCount=0, average=NaN], User[id=3, name=Dick Grayson, age=20]) <=> UserAgeAverage[ageTotal=20, userCount=1, average=20.0]
- userAgeAverageAccumulator(UserAgeAverage[ageTotal=20, userCount=1, average=20.0], User[id=4, name=Tim Drake, age=15]) <=> UserAgeAverage[ageTotal=35, userCount=2, average=17.5]
- userAgeAverageAccumulator(UserAgeAverage[ageTotal=35, userCount=2, average=17.5], User[id=1, name=Bruce Wayne, age=30]) <=> UserAgeAverage[ageTotal=65, userCount=3, average=21.666666666666668]
- userAgeAverageAccumulator(UserAgeAverage[ageTotal=65, userCount=3, average=21.666666666666668], User[id=5, name=Damian Wayne, age=12]) <=> UserAgeAverage[ageTotal=77, userCount=4, average=19.25]
- userAgeAverageAccumulator(UserAgeAverage[ageTotal=77, userCount=4, average=19.25], User[id=0, name=Court of Owls, age=null]) <=> UserAgeAverage[ageTotal=77, userCount=4, average=19.25]
- userAgeAverageAccumulator(UserAgeAverage[ageTotal=77, userCount=4, average=19.25], User[id=2, name=Alfred Pennyworth, age=55]) <=> UserAgeAverage[ageTotal=132, userCount=5, average=26.4]
thirdMultipleClassReduction result: UserAgeAverage[ageTotal=132, userCount=5, average=26.4]

```
