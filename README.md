# Ранжирование покерных рук

<img src="https://kartinkof.club/uploads/posts/2022-03/1648340807_1-kartinkof-club-p-malchishnik-v-vegase-mem-1.jpg" width="550" alt="ПокерМояЖизнь">

### Используемые средства:
- Java
- Maven
- JUnit5

### Референсные материалы:  
- правила [Техасского холдема](https://en.wikipedia.org/wiki/Texas_hold_%27em)

### Описание логики:  
1. Реализован класс для ранжирования покерных рук (набор карт игрока). Класс содержит конструктор,
который принимает на вход строку, содержащую пять карт: `PokerHand hand = new PokerHand("KS 2H 5C JD TD");` 
2. В качестве разделителя входной строки используется пробел.
3. Описание каждой карты состоит из двух символов:  
   - первый символ — это номинал карты.
   - допустимые значения номинала: 2, 3, 4, 5, 6, 7, 8, 9, T(en), J(ack), Q(ueen), K(ing), A(ce).
   - второй символ — масть. 
   - допустимые значения масти: S(pades), H(earts), D(iamonds), C(lubs).
4. Реализована возможность сортировки рук по «силе» (рейтингу / рангу) от сильной к
   слабой: `Collections.sort(hands);`
5. Для упрощения считается, что туз в комбинациях стрит или стрит-флэш может быть только
   наивысшей картой.
6. Класс PokerHand покрыт unit-тестами (определение комбинаций и сравнение комбинаций).

### Основные идеи архитектуры:
1. Все возможные комбинации, номиналы и масти перечислены в соотвествующих enum'ах.
2. Конкретная карта хранится, как класс `Card` с полями `Suit` и `Nominal`. Пять карт в руке хранится, как поле
`ArrayList<Card>` в классе `PokerHand`.
3. Для парсинга входной строки реализованы два класса с паттерном Factory: `HandFactory` и `CardFactory`.
Это сделано для выделения логики парсинга в отдельный класс, что позволит покрыть его отдельными тестами
и для более чистой работы с enum'ами.
4. В классах-фабриках реализована валидация входящей строки, при провале которой выбрасывается
`IllegalArgumentException` с описанием ошибки (строка не имеет пяти карт, неверные масти/номиналы).
5. Для определения комбинации в руке реализован абстрактный класс `CombinationMatcher`, от которого наследуются
матчеры для всех возможных комбинаций (*FlushMatcher, SetMatcher, etc*). Это сделано для прозрачности
выполнения условий соотвествующей комбинации, то есть у каждого матчера максимально очевидная реализация
метода `match()`. Например, данный метод для матчера сета выглядит, как `return countNominals().containsValue(3)`,
что позволит программисту, даже не знающему правила покера, понять механику той или иной комбинации в коде.