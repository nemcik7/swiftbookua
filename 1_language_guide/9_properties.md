---
title: Властивості
layout: default
parent: Посібник з мови
nav_order: 10
has_children: false
has_toc: false
---

# Властивості

_Властивості_ асоціюють значення із певним класом, структурою чи перечисленням. Властивості бувають двох видів: ті, що зберігаються, та ті, що обчислюються. Ті, що зберігаються, зберігають константу чи змінну як частину екземпляру, в той час як ті, що обчислюються, лише обчислюють значення, не зберігаючи нічого. Властивості, що обчислюються, можуть міститись у класах, структурах та перечисленнях. Властивості, що зберігаються, можуть міститись лише у класах та структурах.

Властивості, що зберігаються та що обчислюються, зазвичай є асоційованими з екземпляром певного типу. Однак, властивості також можуть бути асоційовані із самим типом. Такі властивості називають властивостями типу.

Окрім властивостей, можна також створювати спостерігачі за властивостями, щоб стежити за значенням властивості та реагувати на їх зміни. Спостерігачі за властивостями можна додавати до властивостей, що зберігаються і що створені вами, а також до властивостей, які клас-нащадок успадковує від батьківського класу.

## Властивості, що зберігаються

У найпростішій формі, властивість, що зберігається – це константа чи змінна, що зберігається як частина певного класу чи структури. Властивості, що зберігаються, можуть бути або _змінними властивостями, що зберігаються_ \(оголошуються за допомогою ключового слова `var`\), або _константними властивостями, що зберігаються_ \(оголошуються за допомогою ключового слова `let`\).

В оголошенні властивості, що зберігається, можна надавати значення за замовчанням, як описано у підрозділі [Значення властивостей за замовчанням]({% link _book/1_language_guide/13_initialization.md %}#значення-властивостей-за-замовчанням). Можна задавати та змінювати початкове значення властивості, що зберігається, під час ініціалізації. Це працює навіть для константних властивостей, що зберігаються, як описано у підрозділі [Присвоєння значень константним властивостям під час ініціалізації]({% link _book/1_language_guide/13_initialization.md %}#присвоєння-значень-константним-властивостям-під-час-ініціалізації).

У прикладі нижче оголошено структуру із назвою `FixedLengthRange`, котра описує діапазон цілих чисел фіксованої довжини:

```swift
struct FixedLengthRange {
    var firstValue: Int
    let length: Int
}
var rangeOfThreeItems = FixedLengthRange(firstValue: 0, length: 3)
// діапазон представляє цілі числа 0, 1 та 2
rangeOfThreeItems.firstValue = 6
// діапазон тепер представляє цілі числа 6, 7, та 8
```

Екземпляри структури `FixedLengthRange` мають змінну властивість, що зберігається на ім'я `firstValue` та константну властивість, що зберігається на ім'я `length`. У прикладі вище, властивість `length` було ініціалізовано при створенні нового діапазону, і вона не може надалі змінюватись, оскільки це – константна властивість.

### Властивості константних структур, що зберігаються

Якщо створити екземпляр структури та присвоїти його константі, буде неможливо змінити його властивості, не дивлячись навіть на те, що їх було оголошено змінними властивостями:

```swift
let rangeOfFourItems = FixedLengthRange(firstValue: 0, length: 4)
// цей діапазон представляє цілі числа 0, 1, 2, та 3
rangeOfFourItems.firstValue = 6
// тут буде повідомлення про помилку, хоч firstValue і є змінною властивістю
```

Оскільки `rangeOfFourItems` оголошено як константу \(за допомогою ключового слова `let`\), неможливо змінити її властивість `firstValue`, хоч `firstValue` і є змінною властивістю.

Ця поведінка властива структурам внаслідок того, що вони – _типи-значення_. Коли екземпляр типу-значення позначений як константа, такими ж стають і всі його властивості.

Це ж саме не є справедливим для класів, оскільки вони є _типами-посиланнями_. Якщо присвоїти екземпляр типу-посилання константі, все ще можна модифікувати змінні властивості цього екземпляру.

### Ліниві властивості, що зберігаються

_Лінива властивість, що зберігається_ – це властивість, чиє початкове значення не обчислюється до її першого використання. Щоб позначити властивість, що зберігається, лінивою, перед її оголошенням вживають ключове слово `lazy`.

> **Примітка**
>
> Слід завжди оголошувати ліниві властивості як змінні \(за допомогою ключового слова `var`\), оскільки їх початкове значення не можна отримати допоки ініціалізація екземпляру не закінчиться. Константні властивості повинні завжди мати значення до завершення ініціалізації екземпляру, тому їх не можна оголошувати лінивими.

Ліниві властивості доцільно використовувати, коли початкове значення властивості залежить від зовнішніх чинників, чиї значення невідомі до завершення ініціалізації екземпляру. Ліниві властивості також доцільні в ситуаціях, коли обчислення початкового значення властивості вимагає складних чи обчислювально дорогих операцій, котрі не бажано виконувати без реальної необхідності.

У прикладі нижче ліниву властивість, що зберігаються, застосовано для уникнення зайвої ініціалізації в складному класі. У цьому прикладі оголошено два класи із назвами `DataImporter` та `DataManager`, жоден з яких не показаний повністю:

```swift
class DataImporter {
    /*
     DataImporter – це клас, що імпортує дані із зовнішнього файлу.
     Будемо вважати, що клас ініціалізація класу DataImporter займає суттєву кільківть часу.
     */
    var fileName = "data.txt"
    // клас DataImporter буде реалізовувати функціональність імпорту даних тут
}

class DataManager {
    lazy var importer = DataImporter()
    var data = [String]()
    // клас DataManager буде реалізовувати функціональність управління даними тут
}

let manager = DataManager()
manager.data.append("Some data")
manager.data.append("Some more data")
// на даний момент досі не створено екземпляру класу DataImporter, що зберігається у властивості importer
```

Клас `DataManager` містить властивість, що зберігається, на ім'я `data`, котру ініціалізовано новим порожнім масивом рядків. Хоч решта функціональності класу `DataManager` тут не демонструється, метою цього класу є управління та надання доступу до цього масиву даних у вигляді рядків.

Частиною функціональності класу `DataManager` є можливість імпортувати дані із файлу. Ця функціональність реалізовується в класі `DataImporter`, і ми будемо вважати, що його ініціалізація займає значну кількість часу. Це може бути через те, що екземпляр `DataImporter` при ініціалізації повинен відкрити файл і прочитати його вміст у пам'ять.

Екземпляр класу `DataManager` може керувати своїми даними, жодного разу не імпортувавши ці дані із файлу, тому нема потреби створювати новий екземпляр `DataImporter` щоразу при створенні екземпляру самого `DataManager`. Натомість, більше сенсу має створення екземпляру `DataImporter` при першому використанні.

Оскільки властивість `importer` позначено модифікатором `lazy`, екземпляр `DataImporter` буде створено лише при першому звертанні до властивості `importer`, наприклад, при звертанні до властивості `fileName`:

```swift
print(manager.importer.fileName)
// на даний момент створено екземпляр DataImporter, що зберігається у властивості importer
// Надрукує "data.txt"
```

> **Примітка**
>
> Якщо властивість позначено модифікатором `lazy`, до неї звертаються із різних потоків одночасно, і при цьому дану властивість ще не було проініціалізовано, немає гарантії, що властивість буде проініціалізовано тільки раз.

### Властивості, що зберігаються, та змінні екземпляру

Якщо ви маєте досвід роботи з Objective-C, вам може бути відомо, що в цій мові є _два_ способи зберігати значення та посилання як частину екземпляру класу. Окрім властивостей, там можна створювати змінні екземпляру, як внутрішнє вмістилище для значень, що зберігаються у властивості.

У мові Swift ці концепції об'єднано в єдине оголошення властивості. Властивості у Swift не мають відповідної змінної екземпляру, і до внутрішнього вмістилища значення властивості нема прямого доступу. Цей підхід дозволяє уникати плутанини щодо того, як звертатись до значення у різних контекстах та спрощує оголошення властивості до єдиної вичерпної інструкції. Вся інформація про властивість, включаючи її назву, тип, та характеристики управління пам'яттю, оголошується в єдиному місці як частина оголошення типу.

## Властивості, що обчислюються

Окрім властивостей, що зберігаються, у класах, структурах та перечисленнях можна створювати _властивості, що обчислюються_, котрі фактично не зберігають значень, Натомість, вони визначають спосіб звертатись до інших властивостей чи змінювати їх непрямо. Звертатись до інших властивостей можна через спеціальну функцію: "гетер". Змінювати їх – через спеціальну функцію "сетер". Для властивостей, що обчислюються, обов'язковою є лише наявність гетера.

```swift
struct Point {
    var x = 0.0, y = 0.0
}
struct Size {
    var width = 0.0, height = 0.0
}
struct Rect {
    var origin = Point()
    var size = Size()
    var center: Point {
        get {
            let centerX = origin.x + (size.width / 2)
            let centerY = origin.y + (size.height / 2)
            return Point(x: centerX, y: centerY)
        }
        set(newCenter) {
            origin.x = newCenter.x - (size.width / 2)
            origin.y = newCenter.y - (size.height / 2)
        }
    }
}
var square = Rect(origin: Point(x: 0.0, y: 0.0),
                  size: Size(width: 10.0, height: 10.0))
let initialSquareCenter = square.center
square.center = Point(x: 15.0, y: 15.0)
print("square.origin тепер знаходиться в точці (\(square.origin.x), \(square.origin.y))")
// Надрукує "square.origin тепер знаходиться в точці (10.0, 10.0)"
```

У даному прикладі визначаються три структури для роботи із геометричними фігурами:

* `Point` інкапсулює точку з координатами x та y.
* `Size` інкапсулює розмір із шириною `width` та висотою `height`.
* `Rect` визначає прямокутник за лівою нижньою вершиною `origin` та розміром `size`.

Структура `Rect` також містить властивість, що обчислюється, на ім'я `center` \(центр\). Поточне значення центру прямокутника `Rect` завжди можна визначити за його розміром `size` та одною з вершин, зокрема з `origin`, тому немає потреби зберігати значення центру у вигляді точки `Point` явно. Натомість, `Rect` визначає властні гетер та сетер для властивості, що обчислюється, на ім'я `center`, щоб працювати із центром прямокутника так, ніби це насправді властивість, що зберігається.

У попередньому прикладі створено нову змінну типу `Rect` на ім'я `square`. Змінну `square` проініціалізовано лівою нижньою вершиною `(0, 0)`, та розміром `10` на `10`. Цей квадрат зображено як синій квадрат на графіку нижче.

Далі йде звернення до властивості `center` змінної `square` за допомогою синтаксису крапки \(`square.center`\), що спричиняє виклик гетера властивості `center`, і повернення поточного значення властивості. Замість повернення існуючого значення, гетер обчислює та повертає нове значення `Point`, що відповідає центру квадрата. Як видно вище, гетер коректно обчислює центральну точку `(5, 5)`.

Потім властивості `center` присвоєно нове значення `(15, 15)`, таким чином даний квадрат рухається вверх та вправо, на нове місце, яке зображено як помаранчевий квадрат на графіку нижче. Присвоєння значення властивості `center` спричиняє виклик її сетера, котрий змінює значення `x` та `y` у властивості `origin`, що зберігається в `Rect`, що, власне, і пересуває даний квадрат на нову позицію.

![](../../assets/computedProperties_2x.png)  
￼

### Shorthand Setter Declaration

If a computed property’s setter does not define a name for the new value to be set, a default name of `newValue` is used. Here’s an alternative version of the `Rect` structure, which takes advantage of this shorthand notation:

```swift
struct AlternativeRect {
    var origin = Point()
    var size = Size()
    var center: Point {
        get {
            let centerX = origin.x + (size.width / 2)
            let centerY = origin.y + (size.height / 2)
            return Point(x: centerX, y: centerY)
        }
        set {
            origin.x = newValue.x - (size.width / 2)
            origin.y = newValue.y - (size.height / 2)
        }
    }
}
```

### Властивості тільки для читання

Властивість, що має гетер, але не має сетера, називають _властивістю тільки для читання_. Тільки властивості, що обчислюються, можуть бути властивостями тільки для читання. Такі властивості завжди повертають значення, яке можна дістати шляхом використання синтаксису крапки, але їм не можна присвоїти інше значення.

> **Примітка**
>
> Слід оголошувати властивості, що обчислюються \(включно із властивостями тільки для читання\), як змінні властивості, за допомогою ключового слова `var`, бо їх значення не є фіксованими. Ключове слово `let` використовуються лише для константних властивостей, щоб позначити, що їх значення не можуть змінюватись після присвоєння у ході ініціалізації.

Можна спростити оголошення властивості тільки для читання, опустивши ключове слово `get` та відповідні фігурні дужки:

```swift
struct Cuboid {
    var width = 0.0, height = 0.0, depth = 0.0
    var volume: Double {
        return width * height * depth
    }
}
let fourByFiveByTwo = Cuboid(width: 4.0, height: 5.0, depth: 2.0)
print("об'єм куба fourByFiveByTwo дорівнює \(fourByFiveByTwo.volume)")
// Надрукує "об'єм куба fourByFiveByTwo дорівнює 40.0"
```

У даному прикладі створено нову структуру на ім'я `Cuboid`, що моделює тривимірний прямокутний паралелепіпед із властивостями `width` для ширини, `height` для висоти, та `depth` для глибини. Ця структура також має властивість тільки для читання на ім'я `volume`, котра обчислює та повертає об'єм даного паралелепіпеда. Немає сенсу створювати сетер у властивості `volume`, бо тоді буде незрозуміло, як саме змінювати значення `width`, `height`, та `depth` при зміні об'єму. З усім тим, дана властивість тільки для читання структури `Cuboid` є корисною: користувачі даної структури можуть дізнаватись обчислений об'єм.

## Спостерігачі за властивостями

Спостерігач за властивістю – це спосіб спостереження за властивістю та реагування на зміни її значення. Спостерігачі за властивостями викликаються щоразу при присвоєнні нового значення, навіть якщо нове значення співпадає із поточним.

Спостерігач за властивістю можна додати до будь-якої визначеної вами властивості, що зберігається, окрім лінивої властивості. Спостерігач за властивістю також можна додати до будь-якої успадкованої властивості \(що зберігається чи що обчислюється\) шляхом заміщення властивості в класі-нащадкові. Щодо властивостей, що обчислюються, немає сенсу створювати спостерігачі за ними: спостерігати за значенням та реагувати на його зміну можна прямо в сетері. Заміщення властивостей детальніше описано в підрозділі [Заміщення]({% link _book/1_language_guide/12_inheritance.md %}#заміщення).

Існує два види спостерігачів за властивостями, які можна використовувати як поодинці, так і одночасно:

* `willSet` викликається якраз перед збереженням нового значення.
* `didSet` викликається одразу після збереження нового значення.

Якщо реалізувати спостерігач `willSet`, до нього буде передаватись нове значення властивості як константний параметр. В реалізації `willSet` можна вказати ім'я для цього параметра. Якщо ж не вказати імені цього параметра у реалізації спостерігача, він все одно буде доступним з іменем за замовчанням `newValue`.

Аналогічним чином, якщо реалізувати спостерігач `didSet`, до нього буде передаватись попереднє значення властивості як константний параметр. Цьому параметру можна дати назву, або користуватись назвою за замовчанням `oldValue`. Якщо присвоїти властивості значення всередині її спостерігача `didSet`, нове присвоєне значення замінить значення, котре було щойно присвоєне.

> **Примітка**
>
> Спостерігачі `willSet` та `didSet` властивості батькіського класу виклакаються під час присвоєння значення властивості в інінціалізаторі класу-нащадка, після чого викликається ініціалізатор батьківського класу. Вони не викликаються під час ініціалізації властивостей класу, до виклику ініціалізатора батьківського класу.
>
> Делегування ініціалізації детальніше описано в розділах [Делегування ініціалізації у типах-значеннях]({% link _book/1_language_guide/13_initialization.md %}#делегування-ініціалізації-у-типах-значеннях) та [Делегування ініціалізації у класах]({% link _book/1_language_guide/13_initialization.md %}#делегування-ініціалізації-у-класах).

Ось приклад спостерігачів `willSet` and `didSet` у ділі. У цьому прикладі оголошеного новий клас на ім'я `StepCounter`, метою якого є відслідковування кількості кроків, які хтось робить під час прогулянки. Цей клас можна використовувати разом із даними з крокоміра, щоб слідкувати за вправами, які виконує людина у ході її повсякденної рутини.

```swift
class StepCounter {
    var totalSteps: Int = 0 {
        willSet(newTotalSteps) {
            print("Зараз властивості totalSteps буде присвоєно значення \(newTotalSteps)")
        }
        didSet {
            if totalSteps > oldValue  {
                print("Додано \(totalSteps - oldValue) кроків")
            }
        }
    }
}
let stepCounter = StepCounter()
stepCounter.totalSteps = 200
// Зараз властивості totalSteps буде присвоєно значення 200
// Додано 200 кроків
stepCounter.totalSteps = 360
// Зараз властивості totalSteps буде присвоєно значення 360
// Додано 160 кроків
stepCounter.totalSteps = 896
// Зараз властивості totalSteps буде присвоєно значенняи 896
// Додано 536 кроків
```

У класі `StepCounter` оголошено властивість `totalSteps` типу `Int`, що представляє сумарну кількість зроблених кроків. Ця властивість зберігається і має спостерігачі `willSet` та `didSet`.

Спостерігачі `willSet` та `didSet` властивості `totalSteps` викликаються щоразу при присвоєнні цій властивості нового значення. Це працює навіть тоді, коли нове значення співпадає із попереднім.

У даному прикладі спостерігач `willSet` послуговується власним іменем параметра `newTotalSteps` для нового значення. Тут цей спостерігач просто друкує нове значення безпосередньо перед його присвоєнням.

Спостерігач `didSet` викликається після того, як було оновлено значення `totalSteps`. Він порівнює нове значення `totalSteps` з її старим значенням. Якщо значення `totalSteps` збільшилось, друкується повідомлення, котре сповіщає про кількість зроблених кроків. У спостерігачі `didSet` не використовується власне ім'я параметра для старого значення, натомість використовується ім'я за замовчанням `oldValue`.

> **Примітка**
>
> Якщо передати властивість, що має спостерігачі, до функції як двонаправлений параметр, спостерігачі `willSet` та `didSet` будуть завжди викликатись. Це пов'язано з моделлю пам'яті двонаправлених параметрів "копія на вході, копія на виході": значення буде завжди присвоюватись властивості після виходу з функції. Детальніше поведінка двонаправлених параметрів розібрана в розділі [In-Out Parameters]({% link _book/2_language_reference/06_declarations.md %}#Двонаправлені-параметри).

## Глобальні та локальні змінні

Описані вище можливості, що стосуються властивостей, що обчислюються, та спостерігачів за властивостями, також доступні для _глобальних_ та _локальних_ змінних. Глобальні змінні – це змінні, що оголошені поза межами якоїсь функції, методу, замикання чи іншого контексту. Локальні змінні – це змінні, що оголошені всередині якоїсь функції, методу, замикання чи іншого контексту.

Усі глобальні та локальні змінні, з якими ви стикались у попередніх розділах, були змінними, що зберігаються. Змінні, що зберігаються, так сами як і властивості, що зберігаються, надають вмістилище для значення певного типу і дозволяють отримувати це значення.

Однак, також можна оголошувати _змінні, що обчислюються_ та визначати спостерігачі для змінних, що зберігаються, як для глобальних, так і для локальних змінних. Змінні, що обчислюються, обчислюють своє значення, замість його зберігання, і оголошуються точно таким же чином, як і властивості, що зберігаються.

> **Примітка**
>
> Глобальні константи та змінні завжди обчислюються ліниво, аналогічно до [Лінивих властивостей, що зберігаються]({% link _book/1_language_guide/9_properties.md %}#ліниві-властивості-що-зберігаються). На відміну від лінивих властивостей, глобальні константи та змінні не потребують маркування ключовим словом `lazy`.
>
> Локальні константи та змінні ніколи не обчислюються ліниво.

## Властивості типу

Властивості екземплярів – це властивості, що належать екземпляру певного типу. При кожному створенні нового екземпляру типу, цей екземпляр матиме свій власний набір значень властивостей, відділений від будь-якого іншого екземпляру.

Також можна створювати властивості, що належать самому типові, а не будь-якому з екземплярів цього типу. Скільки б не було створено екземплярів, завжди буде існувати лише одна копія цих властивостей. Властивості цього різновиду називаються _властивостями типу_.

Властивості типу доцільно використовувати для визначення значень, котрі є спільними для всіх екземплярів певного типу, такі як константні властивості для використання в кожному екземплярі \(як статичні константи у мові C\), або змінні властивості, що зберігають значення, глобальні для всіх екземплярів цього типу \(як статичні змінні в мові C\).

Властивості типу, що зберігаються, можуть бути змінними та константними. Властивості типу, що обчислюються, завжди оголошуються як змінні властивості, так само як і властивості екземпляру, що зберігаються.

> **Примітка**
>
> На відміну від властивостей екземпляру, що зберігаються, властивості типу, що зберігаються, завжди повинні мати значення за замовчанням. Це пов'язано з тим, що самі типи не мають ініціалізаторів, що може присвоїти значення властивості типу, що зберігається, під час ініціалізації типу.
>
> Властивості типу, що зберігається, ініціалізуються ліниво при першому доступі до них. Гарантується, що їх буде проініціалізовано лише одного разу, навіть при доступі з кількох потоків одночасно, і вони не потребують маркування ключовим словом `lazy`.

### Синтаксис властивостей типу

У мовах C та Objective-C, статичні константи та змінні, що асоціюються з певним типом, оголошуються як _глобальні_ статичні змінні. У Swift, властивості типу записуються як частина оголошення самого типу, всередині фігурних дужок оголошення типу, і кожна властивість типу явно прив'язується до свого типу.

Властивості типу оголошуються за допомогою ключового слова `static`. Властивості типу класів, що обчислюються, можна оголошувати за допомогою ключового слова `class` замість `static`: це дозволяє класам-нащадкам заміщувати реалізацію властивості батьківського класу. Наступний приклад демонструє синтаксис оголошення властивостей типу, що зберігаються та що обчислюються:

```swift
struct SomeStructure {
    static var storedTypeProperty = "Якесь значення."
    static var computedTypeProperty: Int {
        return 1
    }
}
enum SomeEnumeration {
    static var storedTypeProperty = "Якесь значення."
    static var computedTypeProperty: Int {
        return 6
    }
}
class SomeClass {
    static var storedTypeProperty = "Якесь значення."
    static var computedTypeProperty: Int {
        return 27
    }
    class var overrideableComputedTypeProperty: Int {
        return 107
    }
}
```

> **Примітка**
>
> У прикладі вище демонструються лише властивості типу, що обчислюються, тільки для читання, але можна також оголошувати і властивості для читання й запису, за допомогою точно такого ж синтаксису, що й для властивостей екземпляру, що обчислюються.

### Читання й запис властивостей типу

До властивостей типу можна звертатись за допомогою синтаксису крапки, точно так же як і до властивостей екземпляру. Однак, звернення йде до властивості _типу_, а не екземпляру цього типу. Наприклад:

```swift
print(SomeStructure.storedTypeProperty)
// Надрукує "Якесь значення."
SomeStructure.storedTypeProperty = "Інше значення."
print(SomeStructure.storedTypeProperty)
// Надрукує "Інше значення."
print(SomeEnumeration.computedTypeProperty)
// Надрукує "6"
print(SomeClass.computedTypeProperty)
// Надрукує "27"
```

В наступному прикладі демонструються дві властивості типу, що зберігаються як частина структури, що моделює рівень гучності каналу аудіодоріжки. Кожен канал має цілочисельний рівень гучності аудіо від `0` до `10` включно.

Зображення нижче ілюструє, як два канали аудіо можуть комбінуватись для моделювання вимірювача рівня гучності стереодоріжки. Коли рівень гучності каналу `0`, жоден з індикаторів даного каналу не світиться. Коли рівень гучності сягає `10`, світяться всі індикатори для цього каналу. На даному зображенні, лівий канал має рівень `9`, а правий – `7`:  
￼  
![](../../assets/staticPropertiesVUMeter_2x.png)

Описані вище аудіоканали моделюються екземплярами структури `AudioChannel`:

```swift
struct AudioChannel {
    static let thresholdLevel = 10
    static var maxInputLevelForAllChannels = 0
    var currentLevel: Int = 0 {
        didSet {
            if currentLevel > AudioChannel.thresholdLevel {
                // обмежуємо нове значення рівня гучності пороговим значенням
                currentLevel = AudioChannel.thresholdLevel
            }
            if currentLevel > AudioChannel.maxInputLevelForAllChannels {
                // зберігаємо це значення як нове максимальне значення гучності аудіо
                AudioChannel.maxInputLevelForAllChannels = currentLevel
            }
        }
    }
}
```

В структурі `AudioChannel` визначено дві властивості, що зберігаються, для підтримки її функціональності. Перша – `thresholdLevel` – визначає максимальне порогове значення, яке може приймати гучність. Це константне значення `10` для всіх екземплярів структури `AudioChannel`. Якщо аудіо сигнал приходить зі значенням гучності вищим за `10`, він буде обрізаний до цього порогового значення \(як описано нижче\).

Друга властивість типу є змінною властивістю, що зберігається, на ім'я `maxInputLevelForAllChannels`. Вона відслідковує максимальне значення гучності, що коли-небудь приймалось _будь-яким_ екземпляром `AudioChannel`. Її початкове значення – `0`.

Структура `AudioChannel` також визначає властивість екземпляру, що зберігається, на ім'я `currentLevel`, котра містить поточне значення гучності аудіо каналу, в масштабі від `0` до `10`.

Властивість `currentLevel` має спостерігач за значенням `didSet` для перевірки значення `currentLevel` при кожному присвоєнні. Він виконує дві наступні перевірки:

* Якщо нове значення властивості `currentLevel` перевищує максимально дозволене значення `thresholdLevel`, значення властивості `currentLevel` обрізається до значення `thresholdLevel`.
* Якщо нове значення властивості `currentLevel` \(після можливого обрізання\) перевищує будь-яке значення коли-небудь отримане _будь-яким_ екземпляром `AudioChannel`, спостерігач за властивістю зберігає нове значення `currentLevel` у властивості типу `maxInputLevelForAllChannels`.

> **Примітка**
>
> У першій з цих двох перевірок, спостерігач `didSet` присвоює властивості `currentLevel` нове значення. Однак, це не призводить до повторного виклику даного спостерігача.

Тепер можна використати структуру `AudioChannel`, щоб створити два її екземпляри із назвами `leftChannel` та `rightChannel`, і таким чином змоделювати рівні гучності стерео системи:

```swift
var leftChannel = AudioChannel()
var rightChannel = AudioChannel()
```

Якщо присвоїти властивості `currentLevel` _лівого_ каналу `leftChannel` значення `7`, можна побачити, що властивість типу `maxInputLevelForAllChannels` також змінить своє значення на `7`:

```swift
leftChannel.currentLevel = 7
print(leftChannel.currentLevel)
// Надрукує "7"
print(AudioChannel.maxInputLevelForAllChannels)
// Надрукує "7"
```

Якщо присвоїти властивості `currentLevel` _правого_ каналу `rightChannel` значення `11`, можна побачити, що значення цієї властивості обріжеться до максимального значення `10`, а властивість типу `maxInputLevelForAllChannels` також оновить своє значення і теж дорівнюватиме `10`:

```swift
rightChannel.currentLevel = 11
print(rightChannel.currentLevel)
// Надрукує "10"
print(AudioChannel.maxInputLevelForAllChannels)
// Надрукує "10"
```

