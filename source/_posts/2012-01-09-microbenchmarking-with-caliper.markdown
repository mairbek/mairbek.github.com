---
layout: post
title: "Microbenchmarking with Caliper (Rus)"
date: 2012-01-09 13:07
comments: true
categories: ["Caliper", "Java", "Microbenchmarking"]
---

Бывает, что перед нами, разработчиками, возникает проблема выбора той или иной реализации алгоритма или структуры данных подходящей для решения текущей задачи.

Чаще всего, конечно, можно воспользоваться [гуглом][1], задать вопрос на [stackoverflow][2], но в некоторых ситуациях ничего не остается, кроме как провести эксперименты самому.

Такая инженерная практика именуется *microbenchmarking*. Суть микробенчмаркинга в измерении производительности (загрузки процессора, памяти или операций с сетью, диском) небольших кусков кода для того, чтобы понять какой код лучше подходит для текущего сценария.

Стоит заметить, что микробенчмаркинг и профайлинг -- это не одно и то же. Различия между ними напоминают различия между unit тестированием и интеграционным тестированием. Профайлинг подразумевает исследование производительности всего приложения в целом, тогда как микробенчмаркинг -- это, в свою очередь, исследование актуальной в данный момент функциональности.

Зная что нужно измерять, написать бенчмарк довольно тривиально, и, часто, вполне можно обойтись одним единственным статическим методом main. Однако, используя фреймверк Caliper от компании Google, бенчмарки можно писать быстро и с удовольствием, выкинув при этом кучу boilerplate кода.

API Caliper в чем то напоминает старую версию JUnit. Для того чтобы написать простой бенчмарк нужно:

1. Отнаследоваться от класса com.google.caliper.SimpleBenchmark
2. Написать тестовые методы, название которых начинается со слова time.
3. Подготовка данных и очистка ресурсов происходит в методах setUp и tearDown соответственно.

Caliper изначально поставляется с большим количеством примеров. Один из интересных *LoopingBackwardsBenchmark*, который сравнивает скорость итерирования вперед и назад.

``` java LoopingBackwardsBenchmark
public class LoopingBackwardsBenchmark extends SimpleBenchmark {
    @Param({"2", "20", "2000", "2000000", "20000000", "200000000"})
    private int max;


    public int timeForwards(int reps) {
        int dummy = 0;
        for (int i = 0; i < reps; i++) {
            for (int j = 0; j < max; j++) {
                dummy += j;
            }
        }
        return dummy;
    }


    public int timeBackwards(int reps) {
        int dummy = 0;
        for (int i = 0; i < reps; i++) {
            for (int j = max - 1; j >= 0; j--) {
                dummy += j;
            }
        }
        return dummy;
    }


    public static void main(String[] args) throws Exception {
        Runner.main(LoopingBackwardsBenchmark.class, args);
    }


}

```

Запустив тест мы убедимся, что разницы в производительности нет.

Еще один пример. Допустим, мы разрабатываем функциональность которая предполагает огромное количество операций над дробными числами, и мы думаем стоит ли использовать BigDecimal или лучше остановить свой выбор на старом добром быстром double. Сравнить насколько медленней будет работать BigDecimal для такой операции как умножение не сложно.

``` java "Double Vs BigDecimal"
public class DoubleVsBigDecimalBenchmark extends SimpleBenchmark {
    @Param({"1", "1.01", "1.0123456789", "20", "20.01", "20.0123456789", "1000000000", "1000000000.01", "1000000000.0123456789"})
    private double value;

    private BigDecimal[] bigDecimalVal = new BigDecimal[1];
    private double[] doubleVal = new double[1];

    @Override
    protected void setUp() throws Exception {
        bigDecimalVal[0] = BigDecimal.valueOf(value);
        doubleVal[0] = value;
    }


    public void timeDouble(int reps) {
        double dummy = 0;
        for (int i = 0; i < reps; i++) {
            int v = i / (reps + 1);
            dummy = doubleVal[v] * doubleVal[v];
        }
        doubleVal[0] = dummy;
    }

    public void timeBigDecimal(int reps) {
        BigDecimal dummy = BigDecimal.ZERO;
        for (int i = 0; i < reps; i++) {
            int v = i / (reps + 1);
            dummy = bigDecimalVal[v].multiply(bigDecimalVal[v]);
        }
        bigDecimalVal[0] = dummy;
    }

    public static void main(String[] args) {
        Runner.main(DoubleVsBigDecimalBenchmark.class, new String[]{});
    }

}
```

Прогнав тест, становится видно, что для небольших чисел и чисел с небольшой дробной частью *BigDecimal* работает в 5 раз медленней, а для больших чисел либо чисел с большим количеством символов после запятой в 20 раз.

Следует заметить, что JVM хитрая штука, и постоянно занимается оптимизацией кода, что сказывается на результатах бенчмарка, по-этому иногда надо выкручиваться. Например, в бенчмарке *DoubleVsBigDecimalBenchmark* используется массив и странный способ инициализации переменной *v* нулем.



[1]: http://google.com "Google"
[2]: http://stackoverflow.com "StackOverflow"
[3]: http://code.google.com/p/caliper/ "Caliper"