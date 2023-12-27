# ordinal 인덱싱 대신 EnumMap을 사용하라

- [x] 배열이나 리스트에서 원소를 꺼낼 때 ordinal 메서드로 인덱스를 얻는 코드가 있음

~~~java
class Plant {
    enum LifeCycle {ANNUAL, PERENNIAL, BIENNIAL}

    final String name;
    final LifeCycle lifeCycle;

    Plant(String name, LifeCycle lifeCycle) {
        this.name = name;
        this.lifeCycle = lifeCycle;
    }

    @Override
    public String toString() {
        return name;
    }
}
~~~

~~~java
        Set<Plant>[]plantsByLifeCycle=(Set<Plant>[])new Set[LifeCycle.values().length];
        for(int i=0;i<plantsByLifeCycle.length;i++){
        plantsByLifeCycle[i]=new HashSet<>();
        }

        for(Plant p:garden){
        plantsByLifeCycle[p.lifeCycle.ordinal()].add(p);
        }

        for(int i=0;i<plantsByLifeCycle.length;i++){
        System.out.printf("%s: %s%n",Plant.LifeCycle.values()[i],plantsByLifeCycle[i]);
        }
~~~

- [x] 배열은 제네릭과 호환되지 않으니 비검사 형변환을 수행해야 하고 깔끔히 컴파일 되지 않을 것이다
- [x] 배열은 각 인덱스의 의미를 모르니 출력 결과에 직접 레이블을 달아야 함
- [x] 열거 타입을 키로 사용하도록 설계한 아주 빠른 Map 구현체가 존재하는데, 바로 EnumMap이 그 주인공

~~~java
Map<Plant.LifeCycle,Set<Plant>>plantsByLifeCycle=new EnumMap<>(Plant.LifeCycle.class);
        for(Plant.LifeCycle lc:Plant.LifeCycle.values())
        plantsByLifeCycle.put(lc,new HashMap<>());
        for(Plant p:garden)
        plantsByLifeCycle.get(p.lifeCycle).add(p);
        System.out.println(plantsByLifeCycle);
~~~

- [x] 더 짧고 명료하고 안전하고 성능도 원래 버전과 비등함
- [x] 안전하지 않은 형변환은 쓰지 않고, 맵의 키인 열거 타입이 그 자체로 출력용 문자열을 제공하니 출력 결과에 직접 레이블을 달 일도 없음
- [x] stream을 사용해 관리하면 코드를 더 줄일 수 있음

~~~java
System.out.println(Arrays.stream(garden)
        .collect(groupingBy(p->p.lifeCycle)));
~~~

- [x] 이 코드는 EnumMap이 아닌 고유한 맵 구현체를 사용했기 때문에 EnumMap을 써서 얻은 공간과 성능 이점이 사라진다는 문제가 있음

~~~java
System.out.println(Arrays.stream(garden)
        .collect(groupingBy(p->p.lifeCycle,
        ()->new EnumMap<>(LifeCycle.class),toSet())));
~~~

- [x] stream 버전에서는 해당 생애주기에 속하는 식물이 있을 때만 만듬

~~~java
public enum Phase {
    SOLID, LIQUID, GAS;

    public enum Transition {
        MELT, FREEZE, BOIL, CONDENSE, SUBLIME, DEPOSIT;

        private static final Transition[][] TRANSITIONS = {
                {null, MELT, SUBLIME},
                {FREEZE, null, BOIL},
                {DEPOSIT, CONDENSE, null}
        };

        public static Transition from(Phase from, Phase to) {
            return TRANSITIONS[from.ordinal()][to.ordinal()];
        }
    }
}
~~~

- [x] Phase나 Phase.Transition 열거 타입을 수정하면서 상전이 표 TRANSITIONS를 함께 수정하지 않거나 실수로 잘못 수정하면 런타임 오류가 발생 함

~~~java
public enum Phase {
    SOLID, LIQUID, GAS;

    public enum Transition {
        MELT(SOLID, LIQUID), FREEZE(LIQUID, SOLID), BOIL(LIQUID, GAS), CONDENSE(GAS, LIQUID),
        SUBLIME(SOLID, GAS), DEPOSIT(GAS, SOLID);

        private static final Map<Phase, Map<Phase, Transition>> m = Stream.of(values())
                .collect(groupingBy(t -> t.from, () -> new EnumMap<>(Phase.class),
                        toMap(t -> t.to, t -> t, (x, y) -> y, () -> new EnumMap<>(Phase.class))));
        private final Phase from;
        private final Phase to;

        Transition(Phase from, Phase to) {
            this.from = from;
            this.to = to;
        }

        public static Transition from(Phase from, Phase to) {
            return m.get(from).get(to);
        }

    }
}
~~~

- [x] 새로운 상태인 플라즈마를 추가하면 배열인 경우 16개짜리로 교체해야 하지만 EnumMap에서는 전이 목록에 2개만 추가하면 끝

~~~java
public enum Phase {
    SOLID, LIQUID, GAS, PLASMA;

    public enum Transition {
        MELT(SOLID, LIQUID), FREEZE(LIQUID, SOLID), BOIL(LIQUID, GAS), CONDENSE(GAS, LIQUID),
        SUBLIME(SOLID, GAS), DEPOSIT(GAS, SOLID), IONIZE(GAS, PLASMA), DEIONIZE(PLAZMA, GAS);
        private static final Map<Phase, Map<Phase, Transition>> m = Stream.of(values())
                .collect(groupingBy(t -> t.from, () -> new EnumMap<>(Phase.class),
                        toMap(t -> t.to, t -> t, (x, y) -> y, () -> new EnumMap<>(Phase.class))));
        private final Phase from;
        private final Phase to;
        Transition(Phase from, Phase to) {
            this.from = from;
            this.to = to;
        }

        public static Transition from(Phase from, Phase to) {
            return m.get(from).get(to);
        }

    }
}
~~~
#### 배열의 인덱스를 얻기 위해 ordinal을 쓰는 것은 일반적으로 좋지 않으니, 대신 EnumMap을 사용하라
#### 다차원 관계는 EnumMap<..., EnumMap<...>>으로 표현하라