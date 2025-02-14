---
title: Вкладені типи
layout: default
parent: Посібник з мови
nav_order: 19
has_children: false
has_toc: false
---

# Вкладені типи

Перечислення часто створюють для допомоги в реалізації функціональності певного класу чи структури. Аналогічно, буває зручно визначити допоміжні класи та структури для використання всередині контексту складнішого типу. Для цього у Swift можна визначати _вкладені типи_, тобто вкладати допоміжні перечислення, класи та структури всередині оголошення, якому вони, власне, допомагають.

Щоб вкласти один тип всередині іншого, достатньо просто записати його визначення всередині фігурних дужок зовнішнього типу. Типи можна вкладати на стільки рівнів, на скільки це потрібно.

## Вкладені типи в дії

У прикладі нижче визначено структуру на ім'я `BlackjackCard`, котра моделює гральну карту для використання у грі Blackjack. Структура `BlackjackCard`містить два вкладені перечислення, що носять назви `Suit` та `Rank`, і моделюють відповідно масть та ранг.

У грі Blackjack, тузи \(`ace`\) мають значення один або одинадцять. Цю поведінку представлено за допомогою структури `Values`, котру вкладено всередині перечислення `Rank`:

```swift
struct BlackjackCard {

    // вкладене перечислення Suit
    enum Suit: Character {
        case spades = "♠", hearts = "♡", diamonds = "♢", clubs = "♣"
    }

    // вкладене перечислення Rank
    enum Rank: Int {
        case two = 2, three, four, five, six, seven, eight, nine, ten
        case jack, queen, king, ace
        struct Values {
            let first: Int, second: Int?
        }
        var values: Values {
            switch self {
            case .ace:
                return Values(first: 1, second: 11)
            case .jack, .queen, .king:
                return Values(first: 10, second: nil)
            default:
                return Values(first: self.rawValue, second: nil)
            }
        }
    }

    // властивості та методи структури BlackjackCard
    let rank: Rank, suit: Suit
    var description: String {
        var output = "масть дорівнює \(suit.rawValue),"
        output += " значення дорівнює \(rank.values.first)"
        if let second = rank.values.second {
            output += " або \(second)"
        }
        return output
    }
}
```

Перечислення `Suit` чотири загальні масті гральних карт, а його сирі значення типу `Character` містять символи цих мастей.

Перечислення `Rank` описує тринадцять можливих рангів гральних карт, а сирі значення типу `Int` представляють їх числові значення. Ці сирі значення типу `Int` не використовуються для вальтів, дам, королів та тузів \(`jack`, `queen`, `king`, та `ace` відповідно\).

Як згадувалось вище, перечислення `Rank` визначає власну вкладену структуру на ім'я `Values`. Ця структура інкапсулює те, що всі карти мають одне значення, в той час як тузи мають два значення. Структура `Values` визначає дві властивості для представлення цього факту:

* `first` – перше значення, типу `Int`
* `second` – друге значення, типу `Int?`, або “опціональний `Int`”

Структура `Rank` також має властивість, що обчислюється, на ім'я `values`, котра повертає екземпляр структури `Values`. Ця властивість, що обчислюється, створює новий екземпляр `Values`, що відповідає даному рангу. Для числових карт, вона використовує цілочисельне сире значення. Для вальтів, дам, королів та тузів \(`jack`, `queen`, `king`, та `ace`\) використовуються спеціальні значення.

Структура `BlackjackCard` сама по собі дві властивості — `rank` та `suit`. Вона також визначає властивість, що обчислюється `description`, котра використовує значення `rank` та `suit` для побудови текстового опису назви та значення карти. Властивість `description` використовує прив'язування опціоналу для перевірки значення `values.second`, і якщо воно є, додає в кінець опису інформацію про це значення.

Оскільки структура `BlackjackCard` є структурою без явних ініціалізаторів, вона має неявний почленний ініціалізатор, як описано в підрозділі [Почленні ініціалізатори для структур]({% link _book/1_language_guide/13_initialization.md %}#почленні-ініціалізатори-для-структур). Цей ініціалізатор можна використовувати для ініціалізації нової константи на ім'я `theAceOfSpades`:

```swift
let theAceOfSpades = BlackjackCard(rank: .ace, suit: .spades)
print("theAceOfSpades: \(theAceOfSpades.description)")
// Надрукує "theAceOfSpades: масть дорівнює ♠, значення дорівнює 1 або 11"
```

Хоч перечислення `Rank` та `Suit` і є вкладеними всередині `BlackjackCard`, їх типи все ще можна визначити з контексту, і тому в ініціалізації цього екземпляру можна посилатись на елементи перечислень лише за самими їх іменами, `.ace` та `.spades`. У прикладі вище, властивість `description` коректно повідомляє, що туз пік має значення 1 або 11.

## Посилання на вкладені типи

Для використання вкладеного типу поза контекстом, де його визначено, слід спершу вказувати назву типу, де його визначено:

```swift
let heartsSymbol = BlackjackCard.Suit.hearts.rawValue
// heartsSymbol дорівнює "♡"
```

У прикладах вище, це дозволяє свідомо тримати назви типів `Suit`, `Rank`, та `Values` короткими, оскільки їх призначення зрозуміле з контексту, де їх визначено.

