# Основные понятия CORBA

CORBA (Common Object Request Broker Architecture – общая архитектура брокера объектных запросов) – это технология разработки распределенных приложений, ориентированная на интеграцию распределенных изолированных систем.&#x20;

В начале 1990-х гг. ночным кошмаром разработчиков было обеспечение общения программ, выполняемых на разных машинах, особенно, если использовались разные аппаратные средства, операционные системы и языки программирования. Программистам приходилось использовать технологию сокетов и самостоятельно реализовывали весь стек протоколов взаимодействия, либо их программы вовсе не взаимодействовали (другие ранние средства промежуточного программного обеспечения были ограничены средой C и Unix и не подходили для использования в неоднородных средах).&#x20;

Для решения данной проблемы в 1989 г. был создан консорциум OMG (Object Management Group), основной задачей которого стала разработка и продвижение объектно-ориентированных технологий и стандартов. Это некоммерческое объединение, разрабатывающее стандарты для создания корпоративных платформо-независимых приложений.&#x20;

Концептуальной инфраструктурой, на которой базируются все спецификации OMG, является Object Management Architecture (OMA). В состав OMA входят разнообразные стандартизованные или в настоящий момент стандартизируемые OMG сервисы, программные образцы и шаблоны, язык определения интерфейсов распределенных объектов IDL (Interface Definition Language), стандартизованные или стандартизируемые отображения IDL на языки программирования и, наконец, объектная модель CORBA. Главной особенностью CORBA является использование компонента ORB (Object Resource Broker – брокер ресурсов объектов) для создания экземпляров объектов и вызова их методов. Данный компонент формирует «мост» между приложением и инфраструктурой CORBA.&#x20;

В 1997 г. консорциум OMG опубликовал спецификацию CORBA 2.0. В ней определялись стандартный протокол и отображение для языка C++, а в 1998 г. было определено отображение для Java. В результате разработчики получили инструментальное средство, позволяющее им относительно легко создавать неоднородные распределенные приложения. CORBA быстро завоевала популярность, и с использованием этой технологии был создан ряд критически важных приложений.

<figure><img src="../../.gitbook/assets/Снимок экрана 2024-05-16 в 10.49.55.png" alt=""><figcaption><p><strong>Основные понятия технологии CORBA</strong></p></figcaption></figure>
