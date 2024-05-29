# COM/DCOM

&#x20;   Технология COM в настоящее время широко применяется при разработке программного обеспечения, интеграции программных продуктов в единые информационные системы. Целью разработки COM-технологии являлось стремление к интеграции программного обеспечения через стандартизацию механизмов взаимодействия программных модулей между собой. На основе данной технологии, которая является масштабируемой, разработано огромное число технологий, которые стали стандартами в разнообразных сферах применения информационных технологий – от управления технологическими процессами в промышленности до домашних персональных компьютеров. Массовое применение COM отчасти связано с мощью ее разработчика, фирмы Microsoft. С этим приходится считаться, и каждый программный продукт, выпущенный под платформу Windows, для достижения коммерческого успеха обязан соответствовать инновациям Microsoft.

&#x20;   Технология COM (Component Object Technology) – объектно-ориентированная программная спецификация, предложенная Microsoft. COM предназначена для повышения надежности взаимодействия программных продуктов между собой. Данная технология не определяет структуру программного продукта, язык программирования и прочие детали реализации. COM является стандартом, который регламентирует модель программного объекта, соответствующий требованиям COM-технологии. Программный объект, созданный согласно спецификации COM называется COM-объектом. Данная технология определяет механизм взаимодействия COM-объектов между собой. COM относится к так называемым двоичным стандартам, т.к. прилагается к от транслированному в двоичный код программному объекту. Взаимодействие COM-объектов обеспечивается набором предопределенных подпрограмм, называемыми интерфейсами, доступ к которым обеспечивается через уникальные идентификаторы интерфейсов GUID (Global Unique Interface Identifyer), уникальность которых гарантирует операционная система. Такой механизм схож с использованием указателей при доступе к объектам в объектно-ориентированных языках программирования, что дает возможность прозрачного управления объектами, т.к. доступ к ним обеспечивается через указатели. COM-технология расширяет этот механизм, перенося применение указателей (в виде GUID) для доступа к объектам на уровень операционной системы. Таким образом, COM-объекты могут быть прозрачно друг для друга модифицироваться, т.к. доступ к объектам обеспечивается через GUID. COM технология включает в себя также библиотеку, в которой содержится набор стандартных интерфейсов, которые определяют ядро функциональности COM и небольшой набор API функций, разработанных для создания COM-объектов и управления ими.

&#x20;   COM использует такое понятие как «класс», которое по смыслу означает то же самое, что и в объектно-ориентированных средствах разработки. COM-объект является объектом COM-класса (COM class). COM-классы, для различия с классами в объектноориентированных языках, с помощью которых может создаваться приложение, обычно называются соклассами (CoClass).

DCOM — это технология, разработанная корпорацией Microsoft, которая расширяет Component Object Model (COM) на распределенные системы. DCOM позволяет компонентам, работающим на разных компьютерах, взаимодействовать друг с другом через сеть. Он обеспечивает безопасный обмен данными и управление удаленными вызовами методов.

Основные особенности DCOM:

1. Удаленное взаимодействие: DCOM позволяет объектам, расположенным на разных компьютерах, взаимодействовать друг с другом, как если бы они находились в одном процессе. Это достигается за счет использования удаленного вызова процедур (RPC), который обеспечивает передачу данных и управления между объектами.
2. Прозрачность: DCOM скрывает детали сетевого взаимодействия от разработчиков и пользователей, что позволяет создавать распределенные приложения без глубокого понимания сетевых протоколов и механизмов взаимодействия.
3. Поддержка разных языков программирования: DCOM поддерживает различные языки программирования, такие как C++, Visual Basic, Java и другие, что позволяет разрабатывать распределенные приложения с использованием наиболее подходящего языка для конкретной задачи.
4. Безопасность: DCOM включает в себя механизмы обеспечения безопасности, такие как аутентификация, авторизация и шифрование данных, что позволяет защищать распределенные приложения от несанкционированного доступа и атак.
5. Масштабируемость: DCOM позволяет создавать масштабируемые распределенные системы, которые могут быть легко расширены или уменьшены в зависимости от потребностей пользователей и требований к производительности.

Однако, несмотря на свои преимущества, DCOM имеет ряд недостатков, таких как сложность настройки и администрирования, низкая переносимость между платформами и операционными системами, а также ограниченная поддержка современных веб-технологий. В результате, в настоящее время DCOM используется реже, чем другие технологии распределенных систем, такие как веб-сервисы и микросервисы.