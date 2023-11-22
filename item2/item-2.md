# 생성자에 매개변수가 많다면 빌더를 고려하라

#### 정적 팩터리와 생성자에는 똑같은 제약이 하나 있다. 선택적 매개변수가 많을 때 적절히 대응하기 어렵다는 점이다.

### 점증적 생성자 패턴

~~~java
public class NutritionFacts {
    private final int servingSize;
    private final int servings;
    private final int calories;
    private final int fat;
    private final int sodium;
    private final int carbohydrate;

    public NutritionFacts(int servingSize, int servings) {
        this(servingSize, servings, 0);
    }

    public NutritionFacts(int servingSize, int servings, int calories) {
        this(servingSize, servings, calories, 0);
    }

    public NutritionFacts(int servingSize, int servings, int calories, int fat) {
        this(servingSize, servings, calories, fat, 0);
    }

    public NutritionFacts(int servingSize, int servings, int calories, int fat, int sodium) {
        this(servingSize, servings, calories, fat, sodium, 0);
    }

    public NutritionFacts(int servingSize, int servings, int calories, int fat, int sodium, int carbohydrate) {
        this.servingSize = servingSize;
        this.servings = servings;
        this.calories = calories;
        this.fat = fat;
        this.sodium = sodium;
        this.carbohydrate = carbohydrate;
    }
}
~~~

#### 점층적 생성자 패턴도 쓸 수는 있지만, 매개변수 개수가 많아지면 클라이언트 코드를 작성하거나 읽기 어렵다.

### 자바빈즈 패턴

~~~java
public class NutritionFacts {
    private int servingSize = -1;
    private int servings = -1;
    private int calories = 0;
    private int fat = 0;
    private int sodium = 0;
    private int carbohydrate = 0;

    public NutritionFacts() {
    }

    public void setServingSize(int servingSize) {
        this.servingSize = servingSize;
    }

    public void setServings(int servings) {
        this.servings = servings;
    }

    public void setCalories(int calories) {
        this.calories = calories;
    }

    public void setFat(int fat) {
        this.fat = fat;
    }

    public void setSodium(int sodium) {
        this.sodium = sodium;
    }

    public void setCarbohydrate(int carbohydrate) {
        this.carbohydrate = carbohydrate;
    }
}
~~~

#### 자바빈즈 패턴에서는 객체 하나를 만들려면 메서드를 여러 개를 호출해야 하고, 객체가 완전히 생성되기 전까지는 일관성이 무너진 상태에 놓이게 된다.

#### 자바빈즈 패턴에서는 클래스를 불변으로 만들 수 없다

### 빌더 패턴

~~~java
public class NutritionFacts {
    private final int servingSize;
    private final int servings;
    private final int calories;
    private final int fat;
    private final int sodium;
    private final int carbohydrate;

    private NutritionFacts(Builder builder) {
        servingSize = builder.servingSize;
        servings = builder.servings;
        calories = builder.calories;
        fat = builder.fat;
        sodium = builder.sodium;
        carbohydrate = builder.carbohydrate;
    }

    public static class Builder {
        private final int servingSize;
        private final int servings;

        private int calories = 0;
        private int fat = 0;
        private int sodium = 0;
        private int carbohydrate = 0;

        public Builder(int servingSize, int servings) {
            this.servingSize = servingSize;
            this.servings = servings;
        }

        public Builder calories(int calories) {
            this.calories = calories;
            return this;
        }

        public Builder fat(int fat) {
            this.fat = fat;
            return this;
        }

        public Builder sodium(int sodium) {
            this.sodium = sodium;
            return this;
        }

        public Builder carbohydrate(int carbohydrate) {
            this.carbohydrate = carbohydrate;
            return this;
        }

        public NutritionFacts build() {
            return new NutritionFacts(this);
        }
    }
}
~~~

#### 빌더 패턴은 (파이썬과 스칼라에 있는) 명명된 선택적 매개변수를 흉내 낸 것이다.

~~~java
public abstract class Pizza {
    final Set<Topping> toppings;

    Pizza(Builder<?> builder) {
        toppings = builder.toppings.clone();
    }

    public enum Topping {HAM, MUSHROOM, ONION, PEPPER, SAUSAGE}

    abstract static class Builder<T extends Builder<T>> {
        EnumSet<Topping> toppings = EnumSet.noneOf(Topping.class);

        public T addTopping(Topping topping) {
            toppings.add(Objects.requireNonNull(topping));
            return self();
        }

        abstract Pizza build();

        protected abstract T self();
    }
}
~~~

~~~java
public class NyPizza extends Pizza {
    private final Size size;

    NyPizza(Builder builder) {
        super(builder);
        this.size = builder.size;
    }

    public enum Size {SMALL, MEDIUM, LARGE}

    public static class Builder extends Pizza.Builder<Builder> {

        private final Size size;

        public Builder(Size size) {
            this.size = Objects.requireNonNull(size);
        }

        @Override
        public NyPizza build() {
            return new NyPizza(this);
        }

        @Override
        protected Builder self() {
            return this;
        }
    }
}
~~~

~~~java
public class Calzone extends Pizza {
    private final boolean sauceInside;

    private Calzone(Builder builder) {
        super(builder);
        sauceInside = builder.sauceInside;
    }

    public static class Builder extends Pizza.Builder<Builder> {

        private boolean sauceInside = false;

        public Builder sauceInside() {
            sauceInside = true;
            return this;
        }

        @Override
        public Calzone build() {
            return new Calzone(this);
        }

        @Override
        protected Builder self() {
            return this;
        }
    }
}
~~~
~~~java
    NyPizza pizza = new NyPizza.Builder(SMALL)
            .addTopping(SAUSAGE).addTopping(ONION).build();
    Calzone calzone = new Calzone.Builder()
            .addTopping(HAM).sauceInside().build();
~~~
#### 빌더패턴은 코드가 장황해서 매개변수가 4개 이상은 되어야 값어치를 한다

#### 생성자나 정적 팩터리 방식으로 시작했다가 나중에 매개변수가 많아지면 빌더 패턴으로 전환할 수 있지만, 애초에 빌더 패턴으로 시작하는 편이 나을 때가 많다.