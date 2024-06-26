# Принципы реализации удаленного вызова процедур

Модель RPC – это одна из основных моделей, применяемых в программном обеспечении системной поддержки. Впервые она была применена в двухъярусных системах типа "клиент/сервер": клиент вызывает процедуру, работающую на сервере. Существенно, что для клиента этот вызов не отличим от вызова локальной процедуры. Модель RPC ввела сами понятия клиента (вызывающая программа) и сервера (программа, реализующая удаленно вызываемую процедуру).&#x20;

При применении модели RPC синхронизация, установление связи, передача параметров и результата – все делается скрытно от клиента. Это первая модель, позволившая добиться прозрачности и межъязыковой интероперабельности, отказаться от явного обмена сообщениями с помощью протоколов нижних уровней.

Клиентская и серверная части распределенной системы оказываются при взаимодействии с помощью удаленного вызова процедур совершенно независимыми от реализаций друг друга, в частности от используемых в этих реализациях языков программирования. В традиционных системах при обращении из программы, написанной на одном языке, к процедуре, написанной на другом языке, необходимо знать многие технические детали такого обращения: подробности представления типов данных на разных языках (точнее при использовании разных компиляторов), способы выравнивания элементов и заполнения пустот в сложных структурах, детали стекового механизма. Сложности многократно возросли бы, если бы также организовывалось взаимодействие программ, не только написанных на разных языках, но и работающих на разных вычислительных машинах. От всего этого позволяет избавиться модель удаленного вызова процедуры. Программисты получили возможность строить распределенные приложения, не меняя языков и программных парадигм.

Модель RPC является ядром большинства распределенных систем. На этой модели основаны многие появившиеся позднее модели, в частности, модели удаленного вызова метода (Remote Method Invocation, RMI) и хранимых процедур (stored procedures) баз данных. Часто модель RPC используется как низкоуровневый примитив для реализации более сложных форм взаимодействия.

<figure><img src="../../.gitbook/assets/Снимок экрана 2024-05-16 в 09.52.29.png" alt=""><figcaption></figcaption></figure>

Процесс удаленного вызова выглядит следующим образом:

·      Процесс на машине клиента вызывает процедуру на машине сервера.

·      Процесс клиента приостанавливается.

·      На    машине    сервера    запускается    процесс    выполнения вызванной процедуры.

·      Результат передается на машину клиента.

·      Процесс клиента возобновляется.

В описанном механизме много сложностей: разные адресные пространства клиента и сервера, необходимость передачи параметров и результатов, необходимость обрабатывать сбои и отказы оборудования. Дополнительную сложность вызывает то, что часть параметров в программах на процедурных языках программирования может передаваться по значению, а часть ссылками на значения, передаваемые в качестве параметров.

При реализации модели RPC необходимо преодолевать все подобные трудности, поэтому прежде, чем выполнять удаленную процедуру, необходимо провести предварительную подготовку.

Первым шагом должно быть определение интерфейса процедуры, что делается с помощью языка описания интерфейсов (IDL). Это определение задает краткое описание параметров процедуры, передаваемых ей до выполнения и возвращаемых после работы. Определение процедуры на языке IDL можно рассматривать как спецификацию сервиса, предоставляемого сервером(_Рис. 2.2, 2.3_).

\[

object, uuid (E7CDODOO-1827-11CF-9946-444553540000)

]

interface ISpellChecker : Unknown

{ import "unknwn.idi"

HRESULT LookUpWord                ( \[in] OLECHAR word \[31], \[out] boolean \* found);&#x20;

HRESULT AddToDictionary        ( \[in] OLECHAR word \[31]):

HRESULT RemoveFromDictionary ( \[in] OLECHAR word \[31]);

}

_Рис. 2.3. Пример записи на языке описания интерфейсов IDL DCE для разработки распределенных приложений с помощью модели RPC._



Вторым шагом подготовки является трансляция созданного описания. Всякая реализация механизма RPC, любая интеграционная платформа, использующая RPC или сходную концепцию, содержит специальный интерфейсный транслятор. В результате трансляции создаются:

**Клиентский переходник.** Каждое описание заголовка процедуры в файле IDL приводит к созданию отдельного клиентского переходника (stub) для данной процедуры (Рис. 2.4). Переходник – это программа, которая после трансляции присоединяется к программе клиента. В состав клиентского переходника входят программы поиска сервера, форматирования данных, взаимодействия с сервером, получения ответа и передачи ответа в виде возвращаемых параметров вызванной клиентом процедуры. Форматирование данных клиентом состоит из двух процессов: маршалинга и сериализации. Маршалинг заключается в перекодировке и упаковке данных в принятый в конкретной системе формат сообщения. Сериализация состоит из преобразования сообщения в последовательность байтов.

<figure><img src="../../.gitbook/assets/Снимок экрана 2024-05-16 в 09.56.03.png" alt="" width="563"><figcaption></figcaption></figure>

**Серверный переходник** похож на клиентский, но реализует серверную часть вызова. Он состоит из программ, выполняющих получение запроса от клиента, форматирования данных (зеркального преобразования, то есть десериализации и демаршалинга), вызова реальной процедуры, реализованной на сервере, а также возврата результатов клиенту. Подобно клиентскому переходнику после трансляции серверный переходник присоединяется к программе сервера. От всей серверной программы, включая реализацию процедуры, вызываемой удаленным клиентом, не требуется быть написанной на том же языке, который был использован для реализации клиента, чем удается добиться существенного повышения межъязыковой интероперабельности, то есть возможности осуществлять взаимодействия программ, написанных на разных языках программирования.

**Программные шаблоны и ссылки**. Язык IDL и транслятор с него помогают вести разработку процедур, создавая вспомогательные файлы. Например, первые версии систем RPC создавались на языке Си, поэтому в дополнение к клиентскому и серверному переходнику транслятор IDL создавал файлы-заголовки (\*.h). Многие современные трансляторы генерируют шаблоны программ для сервера, например, программы, содержащие заголовки процедур, затем разработчик должен сам создать реализацию этих процедур.

Когда клиент вызывает удаленную процедуру, на самом деле выполняется локальный вызов процедуры, являющейся частью переходника. Параметры вызова клиентский переходник получает стандартным образом, но действия, соответствующие реальной процедуре, не выполняются. Вместо этого переходник отыскивает сервер и форматирует необходимые данные. Перед отправкой сообщения в коммуникационный канал выполняются маршалинг и сериализация, что делает сообщение понятным получателю. Затем устанавливается соединение с сервером, клиент блокируется и ждет ответа (_Рис. 2.5_).

<figure><img src="../../.gitbook/assets/Снимок экрана 2024-05-16 в 09.58.21.png" alt="" width="563"><figcaption></figcaption></figure>

На сервере запускается находящийся в состоянии ожидания серверный переходник, который преобразует сообщение в параметры локальной процедуры. Сервер воспринимает вызов как прямое обращение к его локальной процедуре с параметрами. Результаты работы процедуры упаковываются серверным переходником в сообщение для клиента, которое ему отсылается. На клиентской стороне выясняется, что сообщение адресовано клиенту в качестве ответа на вызов (операционная система не различает клиента и его переходник), сообщение попадает в буфер, а клиент выводится из состояния ожидания. Его переходник распаковывает результаты, записывает их в память клиента и передает управление основной программе.

Основное преимущество модели RPC состоит в том, что и клиент, и сервер не знают об удаленности вызова.

Установление связи с сервером, на котором локализована удаленная процедура, есть процесс, посредством которого клиент создает локальное соединение с данным сервером с целью обратиться к удаленной процедуре. Связывание может быть статическим или динамическим. При статическом связывании информация о сервере, на котором размещена процедура, закодирована прямо в программах клиента. Это может IP-адрес и номер порта, адрес Ethernet, адрес Х.500 и т. д. Преимущества статического связывания – в простоте и эффективности. Недостаток статического связывания заключается в слишком тесной связи клиента и сервера. Проявляется недостаток в том, что если сервер не работает, клиент тоже не в состоянии работать. Если сервер сменил адрес, клиент должен быть перекомпилирован с новым переходником, в котором указан новый правильный адрес. Наконец, для повышения производительности невозможно использование дополнительных серверов. Балансировку нагрузки на сеть надо проводить на стадии разработки клиентской части.

При динамическом связывании создается специализированная служба, ответственная за локализацию серверов. Динамическое связывание создает новый программный слой для снятия косвенности и повышения гибкости системы за счет ее производительности. Этот новый слой называется сервером имен и каталогов, он предназначен для поиска адресов серверов по именам вызываемых процедур. В этом варианте перед вызовом клиентский переходник запрашивает сервер каталогов и только потом обращается к указанному ему серверу (_Рис. 2.6_).

<figure><img src="../../.gitbook/assets/Снимок экрана 2024-05-16 в 09.59.47.png" alt=""><figcaption></figcaption></figure>

Динамическое связывание добавило в модель RPC много новых возможностей. Отслеживая обращения к серверам, удается, например, балансировать нагрузку на них. Если сервер меняет свой адрес, все обращения к нему могут менять свои маршруты, а это развязывает клиенты и серверы, добавляя гибкость при внедрении и эксплуатации системы. Платой за эту гибкость является добавочная инфраструктура в виде сервера каталогов, протокола взаимодействия с ним и примитивов регистрации процедур на серверах. Клиент не должен знать, проводится связывание статически или динамически.

Если машины клиента и сервера идентичны, то после их связывания друг с другом в сообщения, отправляемые между ними, можно упаковывать параметры и имя (код) вызываемой процедуры, а затем отправлять эти сообщения получателям. Если же эти машины в чем-то различаются, приходится предпринимать множество дополнительных действий.

Разные системы могут иметь разные представления чисел и символов. При передаче информации между машинами с разными архитектурными особенностями эту информацию приходится предварительно преобразовывать к некоторому стандартному виду, не зависящему от архитектурных особенностей ЭВМ, а затем снова преобразовывать в иное конкретное представление. Еще более сложна передача параметров не по значению, а по ссылке. Передавать адрес памяти c одной машины на другую – бессмысленно, поскольку на другой машине по этому адресу размещены другие данные. Иногда для решения проблемы принимают решение передавать массивы по значению (в сообщение от клиента к серверу упаковывается весь массив). Однако очевидно, что при выполнении удаленного вызова процедуры обе стороны должны следовать согласованным протоколам, а интерпретация форматов должна быть однозначной.

<figure><img src="../../.gitbook/assets/Снимок экрана 2024-05-16 в 10.01.46.png" alt="" width="563"><figcaption></figcaption></figure>

Стандартный вызов удаленной процедуры подразумевает полную блокировку клиента до получения ответа от сервера. Иногда ответ от сервера не нужен, в подобных случаях используют асинхронные вызовы.

Клиент продолжает работу после запуска RPC (Рис. 2.7). Иногда делают два раздельных (встречных) вызова (Рис. 2.8), что называется отложенной синхронизацией. Вызов, при котором от сервера не требуется подтверждения получения запроса, называется _односторонним_ удаленным вызовом.

<figure><img src="../../.gitbook/assets/Снимок экрана 2024-05-16 в 10.02.57.png" alt="" width="563"><figcaption></figcaption></figure>
