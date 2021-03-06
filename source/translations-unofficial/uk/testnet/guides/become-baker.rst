
.. _networkDashboardLink: https://dashboard.testnet.concordium.com/
.. _node-dashboard: http://localhost:8099
.. _Discord: https://discord.com/invite/xWmQ5tp

.. _become-a-baker-uk:

===================================
Як стати пекарем (створення блоків)
===================================

.. contents::
   :local:
   :backlinks: none

У цій секції пояснюється що таке пекар, його роль в мережі і як їм стати.

Після прочитання цієї секції ви дізнаєтеся:

-  Що таке пекар які концепції з ним пов'язані
-  Як оновити вашу ноду, щоб стати пекарем

Щоб стати пекарем, вам необхідно виконати наступні кроки:

#. Отримати на рахунок деяку кількість токенов GTU.
#. Отримати набір ключів пекаря.
#. Прив'язати ключі пекаря до облікового запису (рахунку).
#. Запустити ноду з ключами пекаря.

Після виконання цих кроків, нода-пекар почне * готувати * блоки.
Якщо приготовлений блок буде додано до ланцюжок, нода пекаря отримає винагороду.

.. note::

   У цій секції ми буде використовувати термін ``bakerAccount`` - це той же рахунок (обліковий запис),
   який використовується для реєстрації та управління пекарем.

Визначення
==========

Пекар
-----

Нода є *пекарем*, якщо активно бере участь в мережі для створення нових блоків і додавання їх ланцюжок.
Пекар збирає, сортує і перевіряє транзакції які включені в блок для управління цілісності блокчейна.
Пекар підписує кожен блок, який готує, також кожен блок може бути перевірений і виконаний іншими учасниками мережі.

Ключі пекаря
------------

Кожен пекар володіє набором криптографічних ключів, які називається *ключі пекаря*.
Нода використовує ці ключі для підписування блоків які готує.
Щоб готувати блоки підписані певним пекарем, в ноду повинні бути завантажені ці ключі.

Акаунт пекаря
-------------

Кожен рахунок (обліковий запис) може використовувати набір ключів пекаря для реєстрації пекаря.

Какждий раз, коли пекар випікає дійсний блок, який включається в ланцюжок, через деякий час на прив'язаний рахунок перераховується нагорода.

Частка і лотерея
----------------

.. todo::

   - Посилання на графік випуску.

На рахунку може міститися частина балансу GTU на *частці пекаря*.
Сума вказується при створенні але пізніше її можна скорегувати.
Ця частка не може бути використана для перекладів, поки заблокована пекарем.

.. note::

   Якщо на рахунку є кошти, які призначені для перекладу за розкладом, ця сума не може стати часткою пекаря, навіть якщо ще не переведена.

Щоб бути обраним для приготування блоку, пекар повинен брати участь в *лотереї*, в якій шанс отримати виграшний квиток пропорційно відповідає величині його частки.

Ця ж доля використовується при визначенні, чи буде включений пекар в комісії фіналізації чи ні. Для детальнішої інформації дивись Фіналізація_.

.. _epochs-and-slots-uk:

Епохи і слоти
-------------

У блокчейне Concordium час розділено на слоти.
Слоти мають фіксовану тривалість в блоці Genesis.
У будь-якій даній галузі кожен слот може мати не більше одного блоку, але кілька блоків в різних гілках можуть бути створені в одному слоті.

.. todo::

   Спробуйте додати картинку.

При розгляді винагород та інших концепцій, пов'язаних з випічкою, ми використовуємо концепцію епохи як одиниці часу, яка визначає період, протягом якого набір поточних пекарів і ставок фіксується.
Епохи мають фіксовану тривалість в блоці Genesis. У тестовій мережі епохи мають тривалість **1 годину**.

Початок випікання
=================

Управління рахунками
--------------------

У цьому розділі дається короткий опис відповідних кроків для імпорту рахунку.
Повний опис див. :ref:`managing_accounts`.

Рахунки створюються програмою :ref:`concordium_id`.
Після того як рахунок був створений, необхідно перейти на вкладку **More** і вибрати **Export**, це створить JSON файл, який містить інформацію про рахунки.

Для імпорту рахунку в ноду, виконайте

.. code-block:: console

   $concordium-client config account import <path/to/exported/file> --name bakerAccount

``concordium-client`` запросить пароль, щоб розшифрувати експортований файл і імпортувати рахунок.
Той же пароль буде використовуватися для шифрування ключів транзакцій і зашифрованих ключів перекладів.

Створення ключів пекаря і їх реєстрація
---------------------------------------

.. note::

   Для такої процедури вам необхідно мати GTU на рахунку.
   Переконайтеся що ви зробили зпрос 100 GTU на рахунок в мобільному додатку.

Кожен рахунок має унікальний ідентифікатор пекаря (baker ID), який використовується під час реєстрації.
Цей ID видається мережею і не може бути попередньо обчислений.
Цей ID повинен бути зазначений усередині файлу ключів пекаря для Ноди, щоб він міг використовувати ключі пекаря для створення блоків.
``concordium-client`` буде автоматично підставляти його значення для подальших операцій.

Щоб створити новий ключ, виконайте:

.. code-block:: console

   $concordium-client baker generate-keys <keys-file>.json

тут ви можете вибрати ім'я файлу з ключами.
Щоб зареєструвати ключі в мережі, нода повинна бути запущена (див. :ref:`запуск ноди <running-a-node>`) необхідно відправити в мережу транзакцію ``baker add``:

.. code-block:: console

   $concordium-client baker add <keys-file>.json --sender bakerAccount --stake <amountToStake> --out <concordium-data-dir>/baker-credentials.json

замінивши

- ``<amountToStake>`` на кількість GTU, яке ви хочете використовувати для частки пекаря
- ``<concordium-data-dir>`` на шлях до поточної папки:

  * для Linux або MacOS: ``~/.local/share/concordium``
  * для Windows: ``%LOCALAPPDATA%\\concordium``.

(Назва файлу повинна залишитися ``baker-credentials.json``).

Ви можете добавіть ``--no-restake`` опцію щоб уникнути автоматичного зарахування нагород на частку пекаря.
Це детальніше описано в розділі `Повернення прибутку`_.

Щоб запустити ноду з ключами пекаря і почати виробляти блоки, вам спочатку необхідно вимкнути працюючу ноду (або натиснувши ``Ctrl + C`` в терміналі,
в якому запущена нода, або скориставшись виконуваним файлом ``concordium-node-stop``).

Після розміщення файлу у відповідному каталозі (вже було зроблено після виконання попередньої команди при вказівці вихідного файлу), знову запустіть ноду, використовуючи ``concordium-node``.
Нода автоматично почне приготування, коли пекар потрапить в список пекарів на поточну епоху.

Ця зміна буде прийнято негайно, але вступить в силу через одну епоху, після тієї, в якій транзакція на додавання пекаря була включена в блок.

.. table:: Хронологія: додавання пекаря

   +----------------------------------+----------------------------+---------------+
   |                                  | Транзакція включена в блок | Через 2 епохи |
   +==================================+============================+===============+
   | Змінм видно в ноді за запитом    |  ✓                         |               |
   +----------------------------------+----------------------------+---------------+
   | Пекар включений в список пекарів |                            | ✓             |
   +----------------------------------+----------------------------+---------------+

.. note::

   Якщо транзакція на додавання пекаря була включена в блок під час епохи `E`, пекар буде вважатися частиною списку пекарів коли почнеться епоха `E+2`.

Управління пекарем
==================

Перевірка статусу пекаря і шансів виграти лотерею
-------------------------------------------------

Щоб побачити, чи випікає нода, ви можете перевірити кілька джерел, які пропонують інформацію з різним ступенем точності.

- В `network dashboard <http://dashboard.testnet.concordium.com>`_, ваша нода містить поле baker ID в стовпці ``Baker``.
- Використовуючи ``concordium-client`` ви можете перевірити поточний список пекарів і відповідні суми часткою на їхньому рахунку, це і є їх шанс виграти лотерею.
  Це значення означає, наскільки ймовірно пекар отримає шанс приготувати блок.

  .. code-block:: console

     $concordium-client consensus show-parameters --include-bakers
     Election nonce:      07fe0e6c73d1fff4ec8ea910ffd42eb58d5a8ecd58d9f871d8f7c71e60faf0b0
     Election difficulty: 4.0e-2
     Bakers:
                                  Account                       Lottery power
             ----------------------------------------------------------------
         ...
         34: 4p2n8QQn5akq3XqAAJt2a5CsnGhDvUon6HExd2szrfkZCTD4FX   <0.0001
         ...

- Використовуючи ``concordium-client`` ви можете перевірити що рахунок був зареєстрований в якості пекаря і побачити розмір його частки.

  .. code-block:: console

     $./concordium-client account show bakerAccount
     ...

     Baker: #22
      - Staked amount: 10.000000 GTU
      - Restake earnings: yes
     ...

- Якщо поле "staked amount" досить велике, і нода запущена з завантаженими ключами пекаря,
  пекар повинен час від часу проводити блоки і ви можете побачити в мобільному гаманці,
  що на рахунок перераховуються нагороди пекареві, наприклад як на скріншоті:

  .. image:: images/bab-reward.png
     :align: center
     :width: 250px

Поновлення частки пекаря
------------------------

Для поновлення частки пекаря, виконайте

.. code-block:: console

   $concordium-client baker update-stake --stake <newAmount> --sender bakerAccount

Зміна цієї суми впливає на можливість пекаря бути обраним для приготування блоку.

Коли пекар **додає частку в перший раз або збільшує свою частку**,
ця зміна виконується в ланцюжку і стає видимим як тільки транзакція включена в блок (можна побачити за допомогою ``concordium-client account show bakerAccount``) і вступає в силу через 2 епохи.

.. table:: Хронологія: збільшення частки

   +--------------------------------+----------------------------+---------------+
   |                                | Транзакція включена в блок | Через 2 епохи |
   +================================+============================+===============+
   | Зміни видно в ноді за запитом  | ✓                          |               |
   +--------------------------------+----------------------------+---------------+
   | Пекар використовує нову частку |                            | ✓             |
   +--------------------------------+----------------------------+---------------+

Коли пекар **зменшує свою частку**, змінам необхідно *2 + bakerCooldownEpochs* епох щоб вступити в силу.
Зміна стає видимим в ланцюжку як тільки транзакція включена в блок, це можна переглянути виконавши ``concordium-client account show bakerAccount``:

.. code-block:: console

   $concordium-client account show bakerAccount
   ...

   Baker: #22
    - Staked amount: 50.000000 GTU to be updated to 20.000000 GTU at epoch 261  (2020-12-24 12:56:26 UTC)
    - Restake earnings: yes

   ...

.. table:: Хронологія: зменшення частки

   +---------------------------------+----------------------------+--------------------------------------+
   |                                 | Транзакція включена в блок | Через *2 + bakerCooldownEpochs* епох |
   +=================================+============================+======================================+
   | Зміни видно в ноді за запитом   | ✓                          |                                      |
   +---------------------------------+----------------------------+--------------------------------------+
   | Пекар використовує нову частку  |                            | ✓                                    |
   +---------------------------------+----------------------------+--------------------------------------+
   | Частка може бути зменшена знову | ✗                          | ✓                                    |
   | або пекар може бути видалений   |                            |                                      |
   +---------------------------------+----------------------------+--------------------------------------+

.. note::

   У тестнете, ``bakerCooldownEpochs`` встановлено в розмірі 168 епох.
   Це значення може бути перевірено за допомогою команди:

   .. code-block:: console

      $concordium-client raw GetBlockSummary
      ...
              "bakerCooldownEpochs": 168
      ...

.. warning::

   Як говорилося в розділі `Визначення`_, сума частки є *заблокованої*, тобто вона не може бути відправлена на інший рахунок або використана для оплати.
   Ви повинні розраховувати, що ця сума не знадобиться найближчим временя.
   Наприклад, для видалення пекаря або для зміни його частки вам буде потрібно деяка сума GTU на основному рахунку, щоб оплатити вартість транзакцій.

Повернення прибутку
-------------------

Коли пекар працює в мережі і готує блоки, на його рахунок перераховуються нагороди за кожен приготований блок.
Ці нагороди за замовчуванням автоматично додаються на частку пекаря.

Ви можете змінити цю поведінку і натомість отримувати нагороди на основний баланс рахунку.
Ця зміна здійснюється за допомогою ``concordium-client``:

.. code-block:: console

   $concordium-client baker update-restake False --sender bakerAccount
   $concordium-client baker update-restake True --sender bakerAccount

Ці зміни виконуватися негайно, проте, почнуть діяти на пекаря через 2 епохи.
Поточне значення це опції можна дізнатися за допомогою команди `` concordium-client``:

.. code-block:: console

   $concordium-client account show bakerAccount
   ...

   Baker: #22
    - Staked amount: 50.000000 GTU
    - Restake earnings: yes

   ...

.. table:: Хронологія: зміна опції повернення

   +---------------------------------------------------+----------------------------+----------------------------+
   |                                                   | Транзакція включена в блок | 2 епохи після нагородження |
   +===================================================+============================+============================+
   | Зміни видно в ноді за запитом                     | ✓                          |                            |
   +---------------------------------------------------+----------------------------+----------------------------+
   | Нагороди (не) будуть накопичуватися автоматично   | ✓                          |                            |
   +---------------------------------------------------+----------------------------+----------------------------+
   | При автоматичному перерахунку,                    |                            | ✓                          |
   | отримана частка впливає на шанс виграшу в лотереї |                            |                            |
   +---------------------------------------------------+----------------------------+----------------------------+

Коли пекар зареєстрований, він автоматично буде перераховувати свою частку при отриманні прибутку,
але як зазначено вище це може бути змінено за допомогою опції ``--no-restake`` під час виконання команди ``baker add``:

.. code-block:: console

   $concordium-client baker add baker-keys.json --sender bakerAccount --stake <amountToStake> --out baker-credentials.json --no-restake

Фіналізація
-----------

Фіналізація - це процес голосування, що виконується нодамі в *фіналізаціонной комісії*, який *завершує* блок,
в процесі досить велика кількість членів комісії отримали блок, узгоджують його результат.
Нові блоки повинні містити завершений блок як батько, щоб гарантувати цілісність ланцюжка.
Для отримання додаткової інформації про цей процес див. секцію :ref:`фіналізація <glossary-finalization>`.

Комісія з фіналізації формується з пекарів, що мають певну частку.
Це, зокрема, означає, що для участі в процесі фіналізації вам, ймовірно, доведеться змінити суму ставки, об досягти зазначеного порога.
У тестовій мережі сума ставки, необхідна для участі в комісії дорівнює **0,1% від загальної кількості існуючих GTU**.

Участь в комісії фіналізації передбачає нагороди за кожен блок, який був фіналізовано.
Нагороди перераховуються на рахунок пекаря через деякий час після того як блок був фіналізовано.

Видалення пекаря
================

Власник рахунку може скасувати реєстрацію свого пекаря в ланцюжку.
Для цього скористайтеся командою ``concordium-client``:

.. code-block:: console

   $concordium-client baker remove --sender bakerAccount

Це видалить пекаря зі списку пекарів і розблокує його частку на рахунку, після чого ця сума вільно може ісползовать.

Дана процедура займає стільки ж часу в епохах, скільки і зменшення частки пекаря.
Зміні потрібно *2 + bakerCooldownEpochs* епох щоб вступити в силу.
Зміна стає видимим в ланцюжку як тільки транзакція включена в блок, як зазвичай, ви можете перевірити це виконавши команду ``concordium-client``:

.. code-block:: console

   $concordium-client account show bakerAccount
   ...

   Baker #22 to be removed at epoch 275 (2020-12-24 13:56:26 UTC)
    - Staked amount: 20.000000 GTU
    - Restake earnings: yes

   ...

.. table:: Хронологія: видалення пекаря

   +-------------------------------------+----------------------------+--------------------------------------+
   |                                     | Транзакція включена в блок | Через *2 + bakerCooldownEpochs* епох |
   +=====================================+============================+======================================+
   | Зміни видно в ноді за запитом       | ✓                          |                                      |
   +-------------------------------------+----------------------------+--------------------------------------+
   | Пекар виключений з комітету пекарів |                            | ✓                                    |
   +-------------------------------------+----------------------------+--------------------------------------+

.. warning::

   Зменшення частки пекаря і його видалення не може бути виконано одночасно.
   Протягом всього процесу зменшення частки пекаря ви не можете починати процедуру видалення і навпаки.

Підтримка і зворотний зв'язок
=============================

Якщо ви зіткнулися з проблемами або у вас виникли питання, зв'яжіться з нами в `Discord`_, або по електронній пошті testnet@concordium.com.
