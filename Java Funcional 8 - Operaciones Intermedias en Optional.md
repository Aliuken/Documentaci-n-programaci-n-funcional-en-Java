Existen los siguientes métodos intermedios en Optional:

| Clase/Interfaz | Método                                                                                   |
|----------------|------------------------------------------------------------------------------------------|
| `Optional<T>`  | `public Optional<T> filter(Predicate<? super T> predicate)`                              |
| `Optional<T>`  | `public <U> Optional<U> map(Function<? super T,? extends U> mapper)`                     |
| `Optional<T>`  | `public <U> Optional<U> flatMap(Function<? super T,Optional<U>> mapper)`                 |

Ejemplo de uso de filter, map y flatMap en Optional:

```java
import java.util.*;
import java.util.function.*;

public class Main {
    public static void main(String[] args) throws Exception {
        Main main = new Main();
        main.testOptionalIntermediateOperations();
    }

    public void testOptionalIntermediateOperations() {
        Optional<Integer> param1 = null;
        Integer param2 = new Integer(111);
        Optional<String> param3 = Optional.of("aaa");
        Optional<Integer> param4 = Optional.empty();
        Optional<Integer> param5 = Optional.of(new Integer(111));

        System.out.println("param1: " + param1);
        System.out.println("param2: " + param2);
        System.out.println("param3: " + param3);
        System.out.println("param4: " + param4);
        System.out.println("param5: " + param5);

        System.out.println();

        System.out.println("this::optionalMapFunction1 <=> x -> Optional<Optional<(String) x>>");
        Function<Integer,Optional<Optional<String>>> function1 = this::optionalMapFunction1;
        testMap(param1, function1, "function1", Optional.class);
        testMap(param2, function1, "function1", Optional.class);
        testMap(param3, function1, "function1", Optional.class);
        testMap(param4, function1, "function1", Optional.class);
        testMap(param5, function1, "function1", Optional.class);

        System.out.println();

        System.out.println("this::optionalMapFunction2 <=> x -> (String) x");
        Function<Integer,String> function2 = this::optionalMapFunction2;
        testMap(param1, function2, "function2", String.class);
        testMap(param2, function2, "function2", String.class);
        testMap(param3, function2, "function2", String.class);
        testMap(param4, function2, "function2", String.class);
        testMap(param5, function2, "function2", String.class);

        System.out.println();

        System.out.println("this::optionalMapFunction3 <=> x -> (Optional<String>) Optional.empty");
        Function<Integer,Optional<String>> function3 = this::optionalMapFunction3;
        testMap(param1, function3, "function3", Optional.class);
        testMap(param2, function3, "function3", Optional.class);
        testMap(param3, function3, "function3", Optional.class);
        testMap(param4, function3, "function3", Optional.class);
        testMap(param5, function3, "function3", Optional.class);

        System.out.println();

        System.out.println("this::optionalMapFunction4 <=> x -> (Optional<String>) null");
        Function<Integer,Optional<String>> function4 = this::optionalMapFunction4;
        testMap(param1, function4, "function4", Optional.class);
        testMap(param2, function4, "function4", Optional.class);
        testMap(param3, function4, "function4", Optional.class);
        testMap(param4, function4, "function4", Optional.class);
        testMap(param5, function4, "function4", Optional.class);

        System.out.println();

        System.out.println("this::optionalMapFunction1 <=> x -> Optional<Optional<(String) x>>");
        function1 = this::optionalMapFunction1;
        testFlatMap(param1, function1, "function1", Optional.class);
        testFlatMap(param2, function1, "function1", Optional.class);
        testFlatMap(param3, function1, "function1", Optional.class);
        testFlatMap(param4, function1, "function1", Optional.class);
        testFlatMap(param5, function1, "function1", Optional.class);

        System.out.println();

        System.out.println("this::optionalMapFunction2 <=> x -> (String) x");
        function2 = this::optionalMapFunction2;
        testFlatMap(param1, function2, "function2", String.class);
        testFlatMap(param2, function2, "function2", String.class);
        testFlatMap(param3, function2, "function2", String.class);
        testFlatMap(param4, function2, "function2", String.class);
        testFlatMap(param5, function2, "function2", String.class);

        System.out.println();

        System.out.println("this::optionalMapFunction3 <=> x -> (Optional<String>) Optional.empty");
        function3 = this::optionalMapFunction3;
        testFlatMap(param1, function3, "function3", Optional.class);
        testFlatMap(param2, function3, "function3", Optional.class);
        testFlatMap(param3, function3, "function3", Optional.class);
        testFlatMap(param4, function3, "function3", Optional.class);
        testFlatMap(param5, function3, "function3", Optional.class);

        System.out.println();

        System.out.println("this::optionalMapFunction4 <=> x -> (Optional<String>) null");
        function4 = this::optionalMapFunction4;
        testFlatMap(param1, function4, "function4", Optional.class);
        testFlatMap(param2, function4, "function4", Optional.class);
        testFlatMap(param3, function4, "function4", Optional.class);
        testFlatMap(param4, function4, "function4", Optional.class);
        testFlatMap(param5, function4, "function4", Optional.class);

        System.out.println();

        System.out.println("this::predicateTrue <=> x -> true");
        Predicate<Integer> predicateTrue = this::predicateTrue;
        testFilter(param1, predicateTrue, "predicateTrue");
        testFilter(param2, predicateTrue, "predicateTrue");
        testFilter(param3, predicateTrue, "predicateTrue");
        testFilter(param4, predicateTrue, "predicateTrue");
        testFilter(param5, predicateTrue, "predicateTrue");

        System.out.println();

        System.out.println("this::predicateFalse <=> x -> false");
        Predicate<Integer> predicateFalse = this::predicateFalse;
        testFilter(param1, predicateFalse, "predicateFalse");
        testFilter(param2, predicateFalse, "predicateFalse");
        testFilter(param3, predicateFalse, "predicateFalse");
        testFilter(param4, predicateFalse, "predicateFalse");
        testFilter(param5, predicateFalse, "predicateFalse");

        System.out.println();
    }

    private <O> void testMap(Object param, Function<Integer,?> function, String functionName, Class<O> outputClass) {
        if(param == null) {
            System.out.println("  * " + param + ".map(" + functionName + ") <=> NullPointerException, param es null");
            return;
        }

        Optional<Integer> optionalParam;
        try {
            optionalParam = (Optional<Integer>) param;
            if(optionalParam.isPresent()) {
                Integer i = optionalParam.get();
            }
        } catch(ClassCastException e) {
            System.out.println("  * " + param + ".map(" + functionName + ") <=> Error de compilación, param no es Optional<Integer>");
            return;
        }

        Optional<Object> optionalResult = optionalParam.map(function);
        System.out.println("  * " + optionalParam + ".map(" + functionName + ") <=> " + optionalResult);
    }

    private <O> void testFlatMap(Object param, Function<Integer,?> function, String functionName, Class<O> outputClass) {
        if(param == null) {
            System.out.println("  * " + param + ".flatMap(" + functionName + ") <=> NullPointerException, param es null");
            return;
        }

        Optional<Integer> optionalParam;
        try {
            optionalParam = (Optional<Integer>) param;
            if(optionalParam.isPresent()) {
                Integer i = optionalParam.get();
            }
        } catch(ClassCastException e) {
            System.out.println("  * " + param + ".flatMap(" + functionName + ") <=> Error de compilación, param no es Optional<Integer>");
            return;
        }

        if(!outputClass.isAssignableFrom(Optional.class)) {
            System.out.println("  * " + optionalParam + ".flatMap(" + functionName + ") <=> Error de compilación, outputClass no es Optional");
            return;
        }

        try {
            Function<Integer,Optional<Object>> functionWithOptionalResponse = (Function<Integer,Optional<Object>>) function;
            Optional<Object> optionalResult = optionalParam.flatMap(functionWithOptionalResponse);
            System.out.println("  * " + optionalParam + ".flatMap(" + functionName + ") <=> " + optionalResult);
        } catch(NullPointerException e) {
            System.out.println("  * " + optionalParam + ".flatMap(" + functionName + ") <=> NullPointerException, la función retorna null");
        }
    }

    private void testFilter(Object param, Predicate<Integer> predicate, String predicateName) {
        if(param == null) {
            System.out.println("  * " + param + ".filter(" + predicateName +") <=> NullPointerException, param es null");
            return;
        }

        Optional<Integer> optionalParam;
        try {
            optionalParam = (Optional<Integer>) param;
            if(optionalParam.isPresent()) {
                Integer i = optionalParam.get();
            }
        } catch(ClassCastException e) {
            System.out.println("  * " + param + ".filter(" + predicateName +") <=> Error de compilación, param no es Optional<Integer>");
            return;
        }

        Optional<Integer> optionalResult = optionalParam.filter(predicate);
        System.out.println("  * " + optionalParam + ".filter(" + predicateName +") <=> " + optionalResult);
    }

    private Optional<Optional<String>> optionalMapFunction1(Integer param) {
        Optional<Optional<String>> result = Optional.of(Optional.of(param.toString()));
        return result;
    }

    private String optionalMapFunction2(Integer param) {
        String result = param.toString();
        return result;
    }

    private Optional<String> optionalMapFunction3(Integer param) {
        Optional<String> result = Optional.empty();
        return result;
    }

    private Optional<String> optionalMapFunction4(Integer param) {
        Optional<String> result = null;
        return result;
    }

    private boolean predicateTrue(Integer param) {
        return true;
    }

    private boolean predicateFalse(Integer param) {
        return false;
    }
}
```

```txt
param1: null
param2: 111
param3: Optional[aaa]
param4: Optional.empty
param5: Optional[111]

this::optionalMapFunction1 <=> x -> Optional<Optional<(String) x>>
  * null.map(function1) <=> NullPointerException, param es null
  * 111.map(function1) <=> Error de compilación, param no es Optional<Integer>
  * Optional[aaa].map(function1) <=> Error de compilación, param no es Optional<Integer>
  * Optional.empty.map(function1) <=> Optional.empty
  * Optional[111].map(function1) <=> Optional[Optional[Optional[111]]]

this::optionalMapFunction2 <=> x -> (String) x
  * null.map(function2) <=> NullPointerException, param es null
  * 111.map(function2) <=> Error de compilación, param no es Optional<Integer>
  * Optional[aaa].map(function2) <=> Error de compilación, param no es Optional<Integer>
  * Optional.empty.map(function2) <=> Optional.empty
  * Optional[111].map(function2) <=> Optional[111]

this::optionalMapFunction3 <=> x -> (Optional<String>) Optional.empty
  * null.map(function3) <=> NullPointerException, param es null
  * 111.map(function3) <=> Error de compilación, param no es Optional<Integer>
  * Optional[aaa].map(function3) <=> Error de compilación, param no es Optional<Integer>
  * Optional.empty.map(function3) <=> Optional.empty
  * Optional[111].map(function3) <=> Optional[Optional.empty]

this::optionalMapFunction4 <=> x -> (Optional<String>) null
  * null.map(function4) <=> NullPointerException, param es null
  * 111.map(function4) <=> Error de compilación, param no es Optional<Integer>
  * Optional[aaa].map(function4) <=> Error de compilación, param no es Optional<Integer>
  * Optional.empty.map(function4) <=> Optional.empty
  * Optional[111].map(function4) <=> Optional.empty

this::optionalMapFunction1 <=> x -> Optional<Optional<(String) x>>
  * null.flatMap(function1) <=> NullPointerException, param es null
  * 111.flatMap(function1) <=> Error de compilación, param no es Optional<Integer>
  * Optional[aaa].flatMap(function1) <=> Error de compilación, param no es Optional<Integer>
  * Optional.empty.flatMap(function1) <=> Optional.empty
  * Optional[111].flatMap(function1) <=> Optional[Optional[111]]

this::optionalMapFunction2 <=> x -> (String) x
  * null.flatMap(function2) <=> NullPointerException, param es null
  * 111.flatMap(function2) <=> Error de compilación, param no es Optional<Integer>
  * Optional[aaa].flatMap(function2) <=> Error de compilación, param no es Optional<Integer>
  * Optional.empty.flatMap(function2) <=> Error de compilación, outputClass no es Optional
  * Optional[111].flatMap(function2) <=> Error de compilación, outputClass no es Optional

this::optionalMapFunction3 <=> x -> (Optional<String>) Optional.empty
  * null.flatMap(function3) <=> NullPointerException, param es null
  * 111.flatMap(function3) <=> Error de compilación, param no es Optional<Integer>
  * Optional[aaa].flatMap(function3) <=> Error de compilación, param no es Optional<Integer>
  * Optional.empty.flatMap(function3) <=> Optional.empty
  * Optional[111].flatMap(function3) <=> Optional.empty

this::optionalMapFunction4 <=> x -> (Optional<String>) null
  * null.flatMap(function4) <=> NullPointerException, param es null
  * 111.flatMap(function4) <=> Error de compilación, param no es Optional<Integer>
  * Optional[aaa].flatMap(function4) <=> Error de compilación, param no es Optional<Integer>
  * Optional.empty.flatMap(function4) <=> Optional.empty
  * Optional[111].flatMap(function4) <=> NullPointerException, la función retorna null

this::predicateTrue <=> x -> true
  * null.filter(predicateTrue) <=> NullPointerException, param es null
  * 111.filter(predicateTrue) <=> Error de compilación, param no es Optional<Integer>
  * Optional[aaa].filter(predicateTrue) <=> Error de compilación, param no es Optional<Integer>
  * Optional.empty.filter(predicateTrue) <=> Optional.empty
  * Optional[111].filter(predicateTrue) <=> Optional[111]

this::predicateFalse <=> x -> false
  * null.filter(predicateFalse) <=> NullPointerException, param es null
  * 111.filter(predicateFalse) <=> Error de compilación, param no es Optional<Integer>
  * Optional[aaa].filter(predicateFalse) <=> Error de compilación, param no es Optional<Integer>
  * Optional.empty.filter(predicateFalse) <=> Optional.empty
  * Optional[111].filter(predicateFalse) <=> Optional.empty
```

Ejemplo de navegación con map y flatMap de Optional

```java
import java.util.*;
import java.util.function.*;

public class Main {
    public static void main(String[] args) throws Exception {
        Main main = new Main();
        main.testOptionalFlatMapNavigation();
    }

	private static final String NO_NAME = "No Name";
	private static final int YEAR_DEFAULT = -1;

    public void testOptionalFlatMapNavigation() {
		Car car = new Car("Opel Kadett", Optional.empty());
		showInfoMal(car);
		showInfoBien(car);

		car = new Car("Subaru Impreza", Optional.ofNullable(null));
		showInfoBien(car);

		car = buildCar("Lada Niva", "A", "Basic Model", 1998);
		showInfoBien(car);

		car = buildCar("Renault Clio", "B", null, 2000);
		showInfoBien(car);

		car = buildCar("Ford Ka", "C", null, -2);
		showInfoBien(car);
	}

	private static Car buildCar(final String carModel, final String tyreQuality, final String tyreBrandInfoName, final int tyreBrandInfoYear) {
		TyreBrandInfo tyreBrandInfo;
		if(tyreBrandInfoName == null && tyreBrandInfoYear < 0) {
		    tyreBrandInfo = null;
		} else {
		    tyreBrandInfo = new TyreBrandInfo(Optional.ofNullable(tyreBrandInfoName), tyreBrandInfoYear);
		}

		TyreBrand tyreBrand = new TyreBrand(tyreBrandInfo);
		Tyre tyre = new Tyre(Optional.of(tyreBrand), tyreQuality);
		Car car = new Car(carModel, Optional.of(tyre));

		return car;
	}

	private static void showInfoMal(Car car) {
	    Optional<Car> optionalCar = Optional.of(car);

		if (optionalCar.get() != null) {
		  System.out.println("Car --> " + optionalCar.get().getModel());
		} else {
		  System.out.println("Car --> " + NO_NAME);
		}

		System.out.println("Car --> " + (optionalCar.isPresent() ? optionalCar.get().getModel() : NO_NAME));
	}

	private static void showInfoBien(Car car) {
	    Optional<Car> optionalCar = Optional.of(car);

	    System.out.println();
		System.out.println("Car --> " + optionalCar.map(Car::getModel).orElse(NO_NAME));
		System.out.println("Tyre Name --> " + getTyreBrandName(optionalCar));
		System.out.println("Tyre Year --> " + getTyreBrandYear(optionalCar));
	}

	private static String getTyreBrandName(final Optional<Car> optionalCar) {
		return optionalCar.flatMap(Car::getTyre).flatMap(Tyre::getTyreBrand).map(TyreBrand::getTyreBrandInfo)
				.flatMap(TyreBrandInfo::getName).orElse(NO_NAME);
	}

	private static int getTyreBrandYear(final Optional<Car> optionalCar) {
		return optionalCar.flatMap(Car::getTyre).flatMap(Tyre::getTyreBrand).map(TyreBrand::getTyreBrandInfo)
				.map(TyreBrandInfo::getYear).orElse(YEAR_DEFAULT);
	}
}

class Car {
    String model;
	Optional<Tyre> tyre;

	public Car (String model, Optional<Tyre> tyre) {
		this.model = model;
		this.tyre = tyre;
	}

	public String getModel() {
	    return this.model;
	}

	public void setModel(String model) {
	    this.model = model;
	}

	public Optional<Tyre> getTyre() {
	    return this.tyre;
	}

	public void setTyre(Optional<Tyre> tyre) {
	    this.tyre = tyre;
	}
}

class Tyre {
	Optional<TyreBrand> tyreBrand;
	String quality;

	public Tyre(Optional<TyreBrand> tyreBrand, String quality) {
		this.tyreBrand = tyreBrand;
		this.quality = quality;
	}

	public Optional<TyreBrand> getTyreBrand() {
	    return this.tyreBrand;
	}

	public void setTyreBrand(Optional<TyreBrand> tyreBrand) {
	    this.tyreBrand = tyreBrand;
	}

	public String getQuality() {
	    return this.quality;
	}

	public void setQuality(String quality) {
	    this.quality = quality;
	}
}

class TyreBrand {
	TyreBrandInfo tyreBrandInfo;

	public TyreBrand(TyreBrandInfo tyreBrandInfo)  {
		this.tyreBrandInfo = tyreBrandInfo;
	}

	public TyreBrandInfo getTyreBrandInfo() {
	    return this.tyreBrandInfo;
	}

	public void setTyreBrandInfo(TyreBrandInfo tyreBrandInfo) {
	    this.tyreBrandInfo = tyreBrandInfo;
	}
}

class TyreBrandInfo {
	public Optional<String> name;
	public int year;

	public TyreBrandInfo(Optional<String> name, int year) {
		this.name = name;
		this.year = year;
	}

	public Optional<String> getName() {
	    return this.name;
	}

	public void setName(Optional<String> name) {
	    this.name = name;
	}

	public int getYear() {
	    return this.year;
	}

	public void setYear(int year) {
	    this.year = year;
	}
}
```

```txt
Car --> Opel Kadett
Car --> Opel Kadett

Car --> Opel Kadett
Tyre Name --> No Name
Tyre Year --> -1

Car --> Subaru Impreza
Tyre Name --> No Name
Tyre Year --> -1

Car --> Lada Niva
Tyre Name --> Basic Model
Tyre Year --> 1998

Car --> Renault Clio
Tyre Name --> No Name
Tyre Year --> 2000

Car --> Ford Ka
Tyre Name --> No Name
Tyre Year --> -1
```