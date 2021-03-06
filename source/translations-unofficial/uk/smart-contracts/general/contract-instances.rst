.. _contract-instances-uk:

========================
Об'єкти смарт-контрактів
========================

.. todo::

   - Роз'яснити, як об'єкти пов'язані зі смарт-контрактами і модулями (наприклад, прямо зараз йдеться про те, що об'єкт - це модуль + стан, але малюнок нижче показує об'єкти як контракти + стан).
   - Вирішити, як саме ми повинні визначати модулі смарт-контрактів (як окрему концепцію або як модулі Wasm), і чи варто взагалі про них говорити.
   - Вирішити, чи потрібен нам конкретний приклад коду, і чи повинен він бути на Wasm або Rust або, можливо, в псевдокоді.
   - Надати картину, яка пояснює взаємозв'язок між модулями і екземплярами.

**Об'єкт смарт-контракту** - це модуль смарт-контракту разом із конкретним станом та кількістю токенів GTU.
З одного модуля можна створити кілька об'єктів смарт-контрактів.
Наприклад, для контракту :ref:`аукціону <auction>` може бути кілька об'єктів, кожен з них, присвячений торгам на конкретний товар та із власними учасниками.

Об'єкти смарт контрактів можна створити з :ref:`модуля смарт-контракту <contract-module-uk>` за допомогою методу ``init``, який викликає відповідну функцію в модулі смарт-контракту.
Ця функція може приймати параметр.
Її кінцевим результатом повинен бути початковий стан ініціалізованого смарт контракту.

.. note::

   Екземпляр смарт-контракту часто називають *об'єкт*.

.. graphviz::
   :align: center
   :caption: Example of smart contract module containing two smart contracts:
             Escrow and Crowdfunding. Each contract has two instances.

   digraph G {
       rankdir="BT"

       subgraph cluster_0 {
           label = "Module";
           labelloc=b;
           node [fillcolor=white, shape=note]
           "Crowdfunding";
           "Escrow";
       }

       subgraph cluster_1 {
           label = "Instances";
           style=dotted;
           node [shape=box, style=rounded]
           House;
           Car;
           Gadget;
           Boardgame;
       }

       House:n -> Escrow;
       Car:n -> Escrow;
       Gadget:n -> Crowdfunding;
       Boardgame:n -> Crowdfunding;
   }

Стан смарт-контракту
====================

Стан об'єкта смарт-контракту складається з двох частин, визначеного користувачем стану та суми GTU, що утримується в контракті, тобто його залишку.
Посилаючись на стан, ми зазвичай маємо на увазі лише визначений користувачем стан.
Причиною окремого трактування суми GTU є те, що GTU можна витрачати та отримувати лише відповідно до правил мережі, наприклад, контракти не можуть створювати або знищувати токени GTU.

.. _contract-instances-init-on-chain-uk:

Примірник смарт-контракту в мережі
==================================

Кожен смарт-контракт повинен містити функцію для створення екземплярів смарт-контрактів.
Така функція називається функцією init.

Для створення екземпляра смарт-контракту обліковий запис надсилає спеціальну транзакцію із посиланням на розгорнутий модуль інтелектуального контракту та ім'ям функції init для використання для створення екземплярів.

Транзакція також може включати суму GTU, яка додається до залишку екземпляра смарт-контракту.
Параметр функції надається як частина транзакції у вигляді масиву байтів.

Підводячи підсумок, операція включає:

- Посилання на модуль смарт-контракту.
- Назва функції init.
- Параметр функції init.
- Сума GTU.

Функція init може сигналізувати про те, що вона не бажає створювати новий екземпляр із цими параметрами.
Якщо функція init приймає параметри, вона встановлює початковий стан екземпляра та його баланс.
Екземпляру присвоюється адреса в ланцюжку, а рахунок, який надіслав транзакцію, стає власником екземпляра.
Якщо функція відхиляє, екземпляр не створюється, а в ланцюжку видно лише транзакцію для спроби створення екземпляра.

.. seealso::

   Дивіться керівництво :ref:`initialize-contract-uk`, щоб дізнатися, як форматувати контракт практично.

Стан об'єкту
============

Кожен екземпляр смарт-контракту має власний стан, який представлений у ланцюжку як масив байтів.
Екземпляр використовує функції, надані хост-середовищем, для читання, запису та зміни розміру стану.

.. seealso::

   Дивіться :ref:`host-functions-state` для довідки по цим функціям.

Смарт контракт має обмежений розмір. В даний час обмеження на стан смарт-контракту становить 16 КБ.

.. seealso::

   Дивіться :ref:`resource-accounting`, щоб дізнатись більше.

Взаємодія з об'єктом
====================

Смарт-контракт може надавати нуль або більше функцій для взаємодії з ним, іменованим як *функції отримання*.

Подібно до функцій init, функції отримання ініціюються за допомогою транзакцій, які містять деяку кількість GTU для контракту та аргумент функції у вигляді байтів.

Підсумовуючи, транзакція для взаємодії смарт-контрактів включає:

- Звернення до інстанції смарт-контракту.
- Назва функції отримання.
- Параметр функції прийому.
- Сума GTU.

.. _contract-instance-actions-uk:

Протоколювання подій
====================

.. todo::

   Пояснити, що таке події і чому вони корисні.
   Перефразувати / уточнити "стеження за подіями".

Події можна реєструвати під час виконання функцій смарт-контракту.
Це стосується як функцій ініціювання, так і прийому.
Журнали призначені для використання поза мережею, щоб актори поза ланцюгом могли стежити за подіями та реагувати на них.
Журнали не доступні для смарт-контрактів або будь-якого іншого учасника мережі. Події можна реєструвати за допомогою функції, що надається хост-середовищем.

.. seealso::

   Дивіться :ref:`host-functions-log` для довідки по цій функції.

Ці журнали подій зберігаються у пекарів та включаються в зведення транзакцій.

Реєстрація події має пов'язані з цим витрати, подібні до вартості письмового повідомлення до стану договору.
У більшості випадків має сенс записати лише кілька байтів, щоб зменшити вартість.

.. _action-descriptions-uk:

Дії з смарт-контрактами
=======================

Функція отримання повертає опис дій, що виконуються хост-середовищем у мережі.

Можливі дії, які може спричинити контракт:

- **Прийняти** - це примітивна дія, яка завжди досягає успіху.
- **Проста передача** GTU з контракту на вказаний рахунок.
- **Надіслати**: викликати функцію отримання зазначеного екземпляра смарт-контракту та необов’язково передати деякі GTU, що надсилає або отримує.

Якщо дію не вдається виконати, функція прийому повертається, залишаючи стан і залишок екземпляра незмінними. Однак:

- транзакція, яка ініціює (невдалу) функцію отримання, все ще додається до ланцюжка, і
- вартість транзакції, включаючи вартість виконання невдалої дії, вираховується з відправляючого рахунку.

Опис обробки багатьох дій
-------------------------

Ви можете зв’язати описи дій за допомогою комбінатора **and**.
Послідовність дії ``A`` **and** ``B``

1) Виконує ``A``.
2) Якщо ``A`` успішна, виконує ``B``.
3) Якщо ``B`` безуспішна, то вся послідовність дій відміняється (результат ``A`` повертається).

Обробка помилок
---------------

Використовуйте **or** комбінатор, щоб виконати дію у випадку, якщо попередня дія не вдалася.
Опис дії  ``A`` **or** ``B``

1) Виконує ``A``.
2) Якщо ``A`` успішна, виконання зупиняється.
3) Якщо ``A`` безуспішна, виконує ``B``.

.. graphviz::
   :align: center
   :caption: Example of an action description, which tries to transfer to Alice
             and then Bob, if any of these fails, it will try to transfer to
             Charlie instead.

   digraph G {
       node [color=transparent]
       or1 [label = "Or"];
       and1 [label = "And"];
       transA [label = "Transfer x to Alice"];
       transB [label = "Transfer y to Bob"];
       transC [label = "Transfer z to Charlie"];

       or1 -> and1;
       and1 -> transA;
       and1 -> transB;
       or1 -> transC;
   }

.. seealso::

   Дивіться :ref:`host-functions-actions` для довідки про те, як створювати дії.

Повне дерево дій виконується **автоматично** і призводить або до оновлення всіх відповідних екземплярів та облікових записів, або, у разі відхилення, до оплати за виконання, але жодних інших змін.
Рахунок, який надіслав ініціюючу транзакцію, оплачує виконання всього дерева.
