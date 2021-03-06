.. _interact-instance-uk:

====================================
Взаємодія з об'єктом смарт-контракту
====================================

Це керівництво покаже вам, як взаємодіяти з об'єктом смарт-контракту, що означає запуск receive-функції, яка, можливо, оновлює стан об'єкту.

Підготовка
==========

Переконайтеся, що ви :ref:`нода запущена <run-a-node-uk>`, використовуючи останню версію :ref:`програмне забезпечення Concordium<downloads>` і що у вас є об'єкт смарт-контракту в мережі для перевірки.

.. seealso::
   Більше про налаштування смарт-контракта можна дізнатись за посиланням :ref:`deploy-module-uk`, а також про створення об'єктів - :ref:`initialize-contract-uk`.

Оскільки взаємодія зі смарт-контрактом є транзакцією, ви також повинні переконатися, що у ``concordium-client`` встановлено аккаунт з достатньою кількістю GTU для оплати транзакцій.

.. note::

   Вартість цієї транзакції залежить від розміру параметрів, що відправляються в функцію прийому, і складності самої функції.

Взаємодія
=========

Щоб оновити екземпляр з індексом адреси ``0`` за допомогою receive-функції ``my_receive`` без параметрів, дозволяючи використовувати до 1000 NRG, виконайте наступну команду:

.. code-block:: console

   $concordium-client contract update 0 --func my_receive --energy 1000

У разі успіху результат повинен бути схожий на:

.. code-block:: console

   Successfully updated contract instance {"index":0,"subindex":0} using the function 'my_receive'.

Передача параметрів в JSON форматі
----------------------------------

Параметр в JSON форматі може бути переданий, якщо вказана :ref:`схема смарт-контракта <contract-schema-uk>` поставляється або у вигляді файлу, або вбудований в модуль.
Схема використовувана для сериализации JSON в двійковий файл.

.. seealso::

   :ref:`Більше про те як і коли використовувати схему смарт-контракта <contract-schema-uk>`.

Щоб оновити екземпляр з індексом адреси ``0`` за допомогою receive-функції ``my_parameter_receive`` з файлом параметрів ``my_parameter.json`` в JSON форматі, виконайте наступну команду:

.. code-block:: console

   $concordium-client contract update 0 --func my_parameter_receive \
            --energy 1000 \
            --parameter-json my_parameter.json

У разі успіху результат повинен бути схожий на:

.. code-block:: console

   Successfully updated contract instance {"index":0,"subindex":0} using the function 'my_parameter_receive'.

В іншому випадку відображається помилка з описом проблеми.
Загальні помилки описані в наступному розділі.

.. seealso::

   Для отримання додаткової інформації про адреси об'єкта контракту, дивіться :ref:`references-on-chain`.

.. note::

   Якщо параметр, який було надано JSON форматі, не відповідає типу, зазначеному в схемі, відобразиться повідомлення про помилку. Наприклад:

    .. code-block:: console

       Error: Could not decode parameters from file 'my_parameter.json' as JSON:
       Expected value of type "UInt64", but got: "hello".
       In field 'first_field'.
       In {
           "first_field": "hello",
           "second_field": 42
       }.

.. note::

   Якщо даний модуль не містить вбудованої схеми, його можна надати за допомогою параметра ``--schema /path/to/schema.bin``.

.. note::

   GTU також можна перенести в контракт під час оновлень за допомогою параметра ``--amount AMOUNT``.

Передача параметрів в бінарному форматі
---------------------------------------

При передачі параметрів в бінарному форматі :ref:`схема смарт-контрактів <contract-schema-uk>` не потрібна.

Щоб оновити екземпляр з індексом адреси ``0`` за допомогою receive-функції ``my_parameter_receive`` з файлом параметрів ``my_parameter.bin`` в бінарному форматі, виконайте наступну команду:

.. code-block:: console

   $concordium-client contract update 0 --func my_parameter_receive \
            --energy 1000 \
            --parameter-bin my_parameter.bin

У разі успіху результат повинен бути схожий на:

.. code-block:: console

   Successfully updated contract instance {"index":0,"subindex":0} using the function 'my_parameter_receive'.

.. seealso::

   Для отримання додаткової інформації про роботу з параметрами у смарт-контракті, дивіться :ref:`working-with-parameters-uk`.

.. _parameter_cursor():
   https://docs.rs/concordium-std/latest/concordium_std/trait.HasInitContext.html#tymethod.parameter_cursor
.. _get(): https://docs.rs/concordium-std/latest/concordium_std/trait.Get.html#tymethod.get
.. _read(): https://docs.rs/concordium-std/latest/concordium_std/trait.Read.html#method.read_u8
