.. _contract-module-uk:

=======================
Модулі смарт контрактів
=======================

Смарт-контракти розгортаються на ланцюжку в *модулях смарт-контрактів*.

.. note::

   Модуль смарт-контракту часто називають просто *модулем*.

Модуль може містити один або кілька інтелектуальних контрактів, що дозволяє спільно використовувати код між контрактами, і може додатково містити :ref:`схему смарт-контрактів <contract-schema-uk>`.

.. graphviz::
   :align: center
   :caption: A smart contract module containing two smart contracts.

   digraph G {
       subgraph cluster_0 {
           node [fillcolor=white, shape=note]
           label = "Module";
           "Crowdfunding";
           "Escrow";
       }
   }

Модуль повинен бути автономним і мати лише обмежений перелік імпорту, що дозволяє взаємодіяти з ланцюжком.
Вони надаються хост-середовищем і доступні для смарт-контракту шляхом імпорту модуля з іменем ``concordium``.

.. seealso::

   Дивіться :ref:`host-functions` для повної довідки.

On-chain мова
=============

У блокчейне Concordium мову смарт-контрактів є підмножина `Web Assembly`_ (скорочено Wasm), яка призначена для переносної компіляції і для запуску в ізольованому середовищі.
Це корисно, тому що смарт-контракти будуть виконуватися Бейкер в мережі, які не обов'язково довіряють коду.

Wasm - це мова низького рівня, тому писати на ньому непрактично.
Замість цього, можна писати смарт-контракти на мові більш високого рівня, який потім компілюється в Wasm.

.. _wasm-limitations-uk:

Обмеження
---------

.. todo::

   Додати інші обмеження, наприклад початкові розділи...

Середа блокчейна дуже специфічна в тому сенсі, що кожен вузол повинен мати можливість виконувати контракт точно таким же чином, використовуючи точно така ж кількість ресурсів.
В іншому випадку вузли не змогли б досягти консенсусу станом мережі. З цієї причини смарт-контракти повинні бути написані в обмеженому підмножині Wasm.

Числа з плаваючою комою
^^^^^^^^^^^^^^^^^^^^^^^

Хоча в Wasm є підтримка чисел з плаваючою комою, смарт-контракти не можуть їх використовувати.
Причина цього в тому, що числа з плаваючою комою Wasm можуть мати особливе ``NaN`` ("не число") значення, обробка якого може привести до недетермінізму.

Обмеження застосовується статично, що означає, що смарт-контракти не можуть містити типи з плаваючою комою і не можуть містити ніяких інструкцій, які включають значення з плаваючою комою.

Розгортання
===========

Розгортання модуля в мережі означає відправку байт-коду модуля у вигляді транзакції в мережу Concordium.
Якщо транзакція *коректна*, вона буде включена в блок. Ця транзакція, як і будь-яка інша транзакція, має відповідну вартість.
Вартість залежить від розміру байт-коду і стягується як за перевірку достовірності модуля, так і за зберігання в мережі.

Саме розгортання смарт-контракт не виконує. Для запуску смарт-контракту користувач спочатку повинен створити *об'єкт* контракту.

.. seealso::

   Для отримання додаткової інформації дивіться :ref:`contract-instances-uk`.

.. _smart-contracts-on-chain-uk:

.. _smart-contracts-on-the-chain-uk:

.. _contract-on-chain-uk:

.. _contract-on-the-chain-uk:

Смарт-контракт у мережі
=======================

Смарт-контракт в мережі - це набір функцій, експортованих з розгорнутого модуля.
Конкретним механізмом для цього, є розділ експорту `Web Assembly`_.
Смарт-контракт повинен експортувати одну функцію для ініціалізації нових екземплярів і може експортувати нуль або більше функцій для поновлення екземпляра.

Оскільки модуль смарт-контракту може експортувати функції для декількох різних смарт-контрактів, ми пов'язуємо функції, використовуючи схему іменування:

- ``init_<contract-name>``: Функція для ініціалізації смарт-контракту повинна починатися з ``init_`` далі ім'я смарт-контракту.
  Контракт повинен складатися тільки з букв і цифр ASCII або розділових знаків і не може містити ``.`` символ.

- ``<contract-name>.<receive-function-name>``: Функції для взаємодії зі смарт-контрактом мають префікс з ім'ям контракту, за яким слідує символ ``.`` і далі ім'я функції.
  Як і для функції init, ім'я контракту не може містити ``.`` символ.

.. note::

   Якщо ви розробляєте смарт-контракти з використанням Rust і ``concordium-std``, то процедурні макроси ``#[init(...)]`` та ``#[receive(...)]`` допоможуть налаштувати правильну схему іменування.

.. _Web Assembly: https://webassembly.org/
