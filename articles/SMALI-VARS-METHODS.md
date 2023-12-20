# Статья 5: Переменные и методы в Smali

Здесь мы уже наконец-то перейдём к практике. [Здесь](http://pallergabor.uw.hu/androidblog/dalvik_opcodes.html) вы можете найти описание всех команд байт-кода `Smali`. Подробнее почитать описание команд можно ещё и [тут](https://habr.com/ru/articles/495024)

## Типы данных

Для начала разберем типы данных в `Smali`:
- `V` - `void`
- `Z` - `boolean`
- `B` - `byte`
- `S` - `short`
- `C` - `char`
- `I` - `int`
- `J` - `long`
- `F` - `float`
- `D` - `double`
- `Lимя класса/интерфейса;` - все остальное
- `[что-то` - массив, например, `[I` - массив `int`

Сейчас мы разберемся со всем этим

## Регистры

Переменных в `Smali` нет, тут есть только регистры

Регистры всегда 32 битные и могут содержать любой тип значения. 2 регистра используются для хранения 64-битных типов (`long` и `double`)

В каждом методе необходимо указать число используемых регистров. Существует 2 способа это сделать:
- `.registers N` - указывает общее количество регистров в методе (их будет N)
- `.locals N` - определяет число регистров (их будет N) без учета параметров метода

Обозначаются они `v#`: `v0`, `v2` и т.д. С параметрами метода чуть сложнее: они помещаются в последние `n` регистров, например, если метод имеет 2 параметра и 5 регистров (`v0`-`v4`), то параметры будут помещены в последние 2 регистра — `v3` и `v4`. **Но**, первым параметром для **не**-статических методов всегда является `this`, то есть текущий экземпляр класса. Параметры метода еще можно получать с помощью регистров вида `p#`: первый - `p0`, второй - `p1` и т.д., обычно их и используют

Как их создавать?

```smali
const v1, 0
```

теперь в `v1` лежит:
- `null`
- `false`
- `0`

Это потому что любой примитив представим в виде числа

Для создания строки:
```smali
const-string v0, "Hello world!"
```

Даже у этих 2 команд есть вариации (причем даже не одна), чем же они отличаются можно посмотреть, опять же, [тут](http://pallergabor.uw.hu/androidblog/dalvik_opcodes.html)

Рассматривать массивы и 64-битные типы мы в рамках этой лабораторной работы не будем

## GOTO

`goto :target` - переход к метке `:target`

Понятнее будет на примере:
```smali
# тут какие-то команды

# переходим к метке :goto_1
goto :goto_1

# значит этот участок просто пропускается
invoke-super ...

:goto_1 # а отсюда продолжается выполнение
invoke-static ...
```

Обратите внимание, что в пределах одного метода не может быть меток с одинаковым названием

## Ветвление

Ветвление в `Smali` как раз и основано на переходах к меткам (`:target`)

Вам хватит 2 команды ветвления:
- `if-eqz v#, :cond_1` - если значение в `v#` == 0, то произойдет переход к метке `:cond_1`
- `if-nez v#, :cond_2` - если значение в `v#` == 1, то произойдет переход к метке `:cond_2`

Например:
```smali
# можно воспринимать это как:
# if (v1 == true) {
#    iget p1, v0, Lcom/f0x1d/hackme/MainViewModel$checkStatus$1;->label:I
#
#    sub-int/2addr p1, v2
#
#    iput p1, v0, Lcom/f0x1d/hackme/MainViewModel$checkStatus$1;->label:I
# }
# new-instance v0, Lcom/f0x1d/hackme/MainViewModel$checkStatus$1;
#
# invoke-direct {v0, p0, p1}, Lcom/f0x1d/hackme/MainViewModel$checkStatus$1;-><init>(Lcom/f0x1d/hackme/MainViewModel;Lkotlin/coroutines/Continuation;)V


if-eqz v1, :cond_0

iget p1, v0, Lcom/f0x1d/hackme/MainViewModel$checkStatus$1;->label:I

sub-int/2addr p1, v2

iput p1, v0, Lcom/f0x1d/hackme/MainViewModel$checkStatus$1;->label:I

:cond_0
new-instance v0, Lcom/f0x1d/hackme/MainViewModel$checkStatus$1;

invoke-direct {v0, p0, p1}, Lcom/f0x1d/hackme/MainViewModel$checkStatus$1;-><init>(Lcom/f0x1d/hackme/MainViewModel;Lkotlin/coroutines/Continuation;)V
```

## Методы

Это просто для общего развития, в рамках этой лабораторной это вам не понадобится

Они имеют такую структуру:
```smali
# метод бывает private / protected / public (модификаторы доступа из Java)
# метод может быть статическим
# типы параметров просто перечисляются друг за другом, например параметры (int, int, String, boolean) в Smali будет (IILjava/lang/String;Z)

.method <public|private> <static?> <function_name>(<types>)<return_type>
    # я объяснял это чуть выше
    .locals <X>

    # если наш return_type это V, то метод заканчивается
    return-void

    # если наш return_type - притив, то метод заканчивается
    return <register>

    # если наш return_type - объект, то метод заканчивается
    return-object <register>

# Ну и конец определения метода
.end method
```

# Навигация

- [Задание](../README.md)
- [Статья 1: Android, приложения](./APPS.md)
- [Статья 2: ApkTool](./APKTOOL.md)
- [Статья 3: JADX](./JADX.md)
- [Статья 4: Структура Smali](./SMALI-STRUCTURE.md)
- **Статья 5: Переменные и методы в Smali**