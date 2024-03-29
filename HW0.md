# CAP и СУБД

## Основные определения

### Определение CA

CA (Availability + Consistency — Parition tolerance): данные во всех узлах кластера согласованы и доступны, но не устойчивы к разделению. 

Это означает, что реплики одной и той же информации, распределенные по разным серверам, не противоречат друг другу и любой запрос к распределённой системе завершается корректным откликом. 

Такие системы возможны при поддержке ACID-требований к транзакциям (Атомарность, Согласованность, Изоляция, Долговечность) и абсолютной надежности сети. На практике таких решений на основе кластерных систем управления базами данных почти не существует. 

Классическими примерами CA-системы считают реляционные базы данных (PostgreSQL, MySQL, MariaDB...). [Source](https://www.bigdataschool.ru/wiki/cap).

### Определение CP

CP-система (Consistency + Partition tolerance — Availability) в каждый момент обеспечивает целостность данных и способна работать в условиях распада в ущерб доступности, не выдавая отклик на запрос. 

Устойчивость к разделению требует дублирования изменений во всех узлах системы, что реализуется с помощью распределённых пессимистических блокировок для сохранения целостности (принято полагать, что CP – это система с несколькими синхронно обновляемыми мастер-базами). Она корректно отрабатывает транзакцию только в том случае, если изменения удалось распространить по всем серверам. 

Она продолжает корректно читать данные даже при отказе одного из узлов кластера. Но в этом случае запись будет обрываться или сильно задерживаться, пока система не убедится в своей целостности и согласованности (консистентности). 

Из NoSQL-СУБД к CP-системам относят Apache HBase, MongoDB, Redis, MemcasheDB, Berkley DB... [Source](https://www.bigdataschool.ru/wiki/cap).

### Определение AP

AP-система (Availability + Partition tolerance — Consistency) не гарантирует целостность данных, обеспечивая их доступность и устойчивость к разделению, например, как в распределённых веб-кэшах и DNS. 

Считается, что большинство NoSQL-СУБД относятся к этому классу систем, обеспечивая лишь некоторой уровень согласованности данных в конечном счете (eventually consistent). 

Выходит, AP-система может быть представлена кластером из нескольких узлов, каждый из которых может принимать данные, но не обязуется в тот же момент распространять их на другие сервера. 

Такая система отлично справляется с отказами нескольких узлов, но, когда они снова начинают работать, возможна выдача пользователям старых данных. К AP-системам относят CoucheDB, Cassandra, Riak, Amazon DynamoDB. [Source](https://www.bigdataschool.ru/wiki/cap).

## Конкретные БД

### DragonFlyDB

Данная СУБД относится к CA (consistency and availability).

#### Объяснение

Информация о том, к какому типу относится данная СУБД, находится на [официальном сайте](https://dragonflydb.io/). Важная деталь, которая находится в разделе Highly Scalable, - работает на одной машине. На языке CAP-теоремы это означает как таковое отсутсвие устойчивости к разделению (partition tolerance), поскольку данную систему нельзя разделить на несколько.

Вторая и третья деталь находятся на странице [features](https://dragonflydb.io/features). Эффективность и производительность указывают на то, что система доступна и согласована (CA), проще говоря, работает быстро и качественно. Режим работы - асинхронный, запросы завершаются корректно и быстро, высокая пропускная способность.

### ArenadataDB

Данная СУБД относится к CP (consistency and partition tolerance).

#### Объяснение

На [Source](https://bestprogrammer.ru/izuchenie/osnovy-sistemnogo-proektirovaniya-chto-takoe-teorema-cap) указаны основные фичи данной СУБД: используются документы для хранения данных. При разделении, система отключает несогласованные узлы, пока не будут исправлены неточности, причем при данных блокировках журналы особытий сильно ускоряют процесс. 

Кроме того, на [официальном сайте](https://arenadata.tech/products/arenadata-db/) в разделе FAQ есть предпоследний вопрос: "Какие средства управления и мониторинга есть в Arenadata DB?" Здесь и идет речь про подобные журналы изменений и ошибок. 

Также вопросы, начиная с: "Какие наиболее значимые функциональные особенности есть у Arenadata DB?" - и ответы на них говорят о том, что система одновременно устойчива и согласованна, но никак не может быть доступна сразу, иначе бы не было резервного копирования и т.п.

### ScyllaDB

Данная СУБД относится к AP (availability and partition tolerance).

#### Объяснение

Прямой ответ можно найти на [официальный документ СУБД](https://docs.scylladb.com/stable/architecture/architecture-fault-tolerance.html). 

В конце документа разбирается сама CAP-теорема, где говорится о том, что согласованности быть не может, так как теряется высокоустойчивость - основная фишка этой БД. За счет дополнительной отказоустойчивости (тоже рассмотрена ранее в статье) достигается постоянная работа приложения (не выходит из строя).

Ответ можно найти и в других источниках. На [официальном сайте](https://www.scylladb.com/scylla-vs-cassandra/) сравнивают Scylla и Cassandra. Видно, что рассматриваемая СУБД дублирует характеристики Cassandra. Она же в свою очередь является типичным примером AP.

## Other sources

Помимо упомянутых источников, вникнуть в суть помогли [статья на Хабре](https://habr.com/ru/post/328792/), презентация с лекци, [статьи в Википедии](https://ru.wikipedia.org/wiki/%D0%A2%D0%B5%D0%BE%D1%80%D0%B5%D0%BC%D0%B0_CAP) и [Source](https://habr.com/ru/post/130577/).
