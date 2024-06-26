# Логические программные слои распределенных систем

Концептуально распределенные системы строятся послойно: презентационный слой, слой прикладной логики и слой управления ресурсами. Эти слои могут быть только абстракциями, существуя лишь на этапе проектирования, но могут быть четко видимы в программном обеспечении, когда их реализуют в виде отдельных подсистем, иногда с применением различного инструментария.

#### **Презентационный слой.**&#x20;

Все распределенные системы должны общаться с внешним миром, с пользователями-программистами или другими программными системами. Значительная часть этого общения связана с преобразованием информации и представлением ее для внешних пользователей, подготовкой запросов и получением ответов. Компоненты распределенной системы, обеспечивающие эту деятельность, формируют презентационный слой.

Часто этот слой называют клиентом распределенной системы, что неверно. Все распределенные системы имеют клиентов, то есть сущности, которые пользуются сервисами, предоставляемыми этими системами. Клиенты могут быть полностью внешними по отношению к системам и независимыми от них. В этом случае они не являются презентационным слоем самих систем. Лучшими примерами программ, спроектированных таким образом, являются сетевые навигаторы, обрабатывающие документы, написанные на языке HTML. Презентационным слоем распределенной системы в данном случае будет сетевой сервер, а также модули, занятые созданием HTML-документов.

Случается, что клиент и презентационный слой слиты воедино. Это типично для систем типа клиент/сервер, в которых имеется программа, одновременно исполняющая роль клиента и презентационного слоя.

#### **Слой прикладной логики.**&#x20;

Любые системы программного обеспечения не только демонстрируют информацию, но и осуществляют обработку данных. Эта обработка производится программой, реализующей фактические операции, запрошенные клиентом через посредство презентационного слоя. Такая программа называется программой слоя прикладной логики. Иногда эти программы называются службами, предлагаемыми распределенными системами. В зависимости от сложности выполняемой логики этот слой может называться бизнес процессом, бизнес логикой, бизнес правилами или просто сервером.

#### **Слой управления ресурсами.**&#x20;

Для работы любая система программного обеспечения нуждается в данных. Данные могут размещаться в базах данных, файловых системах, в других репозиториях.

Программы слоя управления ресурсами объединяют все такие элементы. Иногда, чтобы показать, что этот слой реализуется с использованием системы управления базой данных, этот слой называют слоем данных. Этот подход, однако, имеет ограничения, поскольку он концентрируется только на аспекте управления данными. Однако в управлении нуждаются все внешние системы, поставляющие информацию. Сюда входят не только базы данных, но и другие распределенные системы со своими слоями презентационным, прикладным и управления ресурсами. При этом появляется возможность рекурсивно строить распределенные системы, состоящие из других систем, как из компонентов.

Описанные три слоя – это концептуальные конструкции, которые логически разделяют функциональность большинства распределенных систем. В практических реализациях они могут комбинироваться различными способами. В этих случаях говорят не о концептуальных слоях, а о ярусах (звеньях). Известны 4 типа основных типа распределенных систем, отличающихся количеством входящих в них ярусов: одно-, двух-, трех- и многоярусные системы.
