# Пример простого приложения клиент-сервер

**Создание интерфейса на языке IDL**

IDL – декларативный язык для формального описания интерфейсов взаимодействия клиентов и серверов в распределенных приложениях. IDL не привязан к какому-либо языку программирования, но для большинства современных языков программирования существуют спецификации, определяющие правила трансляции конструкций IDL в конструкции этих языков.\
Ниже приведен пример описания интерфейса для приложения Hello (в файле Hello.idl каталога приложения).

```
module HelloApp {
  interface Hello {
    string sayHello();
    oneway void shutdown();
  };
};
```

Конструкция&#x20;

module определяет пространство имен, в котором будут существовать включенные в нее интерфейсы. Эта конструкция по своему смыслу близка понятию “пакет” (package) языка Java.

Конструкция&#x20;

interface определяет программный интерфейс, с помощью которого объекты общаются друг с другом. В этом интерфейсы языков IDL и Java аналогичны.

В теле конструкции&#x20;

interface объявляются операции, которые должен быть способен выполнять сервер по запросу клиента. Операции языка IDL соответствуют методам языка Java.

В результате компиляции будет создан пакет с именем HelloApp (имя из конструкции module) в каталоге&#x20;

Hello, содержащий 6 файлов: Hello.java, HelloPOA.java, \_HelloStub.java, HelloOperations.java, HelloHelper.java, HelloHolder.java.

* Hello.java содержит код Java-интерфейса Hello, сгенерированный из IDL-интерфейса. Интерфейс Hello расширяет интерфейсы HelloOperations, org.omg.CORBA.Object и org.omg.CORBA.portable.IDLEntity.
* HelloOperations.java содержит код Java-интерфейса HelloOperations, объявляющего два метода sayHello() и shutdown(), соответствующих операциям языка IDL. Интерфейс HelloOperations используется stub’ами клиентов и skeleton’ами серверов.
* HelloPOA.java содержит код абстрактного класса HelloPOA, расширяющего класс org.omg.PortableServer.Servant и реализующего интерфейсы HelloApp.HelloOperations, org.omg.CORBA.portable.InvokeHandler. Класс HelloPOA является skeleton’ом и обеспечивает базовую функциональность CORBA для сервера.
* \_HelloStub.java содержит код класса \_HelloStub, расширяющего класс org.omg.CORBA.portable.ObjectImpl и реализующего интерфейс HelloApp.Hello. Этот класс играет роль stub’а и обеспечивает базовую функциональность CORBA для клиента.
* HelloHelper.java содержит код класса HelloHelper, обеспечивающего вспомогательную функциональность. Например, метод narrow() этого класса необходим для преобразования объектных ссылок (object references) CORBA, используемых для идентификации объектов, к типам, принятым в Java. Этот класс реализует чтение/запись данных различного типа в потоки CORBA.
* HelloHolder.java содержит код финального класса HelloHolder, реализующего интерфейс org.omg.CORBA.portable.Streamable. Этот класс включает открытую переменную типа Hello. Для аргументов типа out и inout операций языка IDL в этом классе выполняются преобразования, соответствующие семантике аргументов методов языка Java. Класс Holder обращается к методам класса Helper для выполнения операций чтения/записи в потоки CORBA.

**Создание серверной части приложения**

Серверная часть приложения состоит из двух классов: собственно сервера и серванта (

servant). Сервант (назовем его HelloImpl) реализует IDL-интерфейс Hello, при этом каждая сущность IDL-интерфейса Hello реализуется сущностью класса HelloImpl. Сервант является подклассом класса HelloPOA, описание которого сгенерировано компилятором idlj.

Сервант содержит по одному методу для каждой IDL-операции (в нашем примере это методы sayHello() и shutdown()). Методы серванта являются “обычными” методами Java, все вспомогательные операции (общение с ORB, приведение аргументов и результатов и т.п.) выполняются каркасом.\
Класс сервера содержит метод main(), который выполняет следующие задачи:

* создание и инициализация экземпляра ORB;
* получение доступа к корневому объекту POA и активизация POAManager;
* создание экземпляра серванта и информирование ORB о нем;
* получение доступа к объекту CORBA в пространстве имен, в котором необходимо зарегистрировать новый объект CORBA;
* получение корневого пространства имен;
* регистрация нового объекта в пространстве имен под именем “Hello”;
* ожидание вызова нового объекта со стороны клиентов.

Ниже представлен пример кода (в файле HelloServer.java каталога Hello) серверной части приложения.

```
/ ----- Импорт всех необходимых пакетов -----
// Пакет, сгенерированный компилятором idlj
import HelloApp.*;
// Пакет, необходимый для использования службы имен
import org.omg.CosNaming.*;
// Пакет, содержащий специальные исключения, генерируемые службой имен
import org.omg.CosNaming.NamingContextPackage.*;
// Пакет, содержащий классы, необходимые любому приложению CORBA
import org.omg.CORBA.*;
// Пакеты, содержащие классы, реализующие модель наследования переносимых серверов
import org.omg.PortableServer.*;
import org.omg.PortableServer.POA;
// Пакет, содержащий классы, необходимые для инициализации ORB
import java.util.Properties;

// ----- Реализация класса-серванта -----

// Сервант HelloImpl является подклассом класса HelloPOA и 
// наследует всю функциональность CORBA, сгенерированную для 
// него компилятором
class HelloImpl extends HelloPOA {
    private ORB orb;
    // Необходима для хранения текущего ORB (используется в методе 
    // shutdown)

    public void setORB(ORB orb_val) {
        orb = orb_val;
    }

    public String sayHello() {
        return "\nHello, world !!\n";
    }

    public void shutdown() {
        // Использует метод org.omg.CORBA.ORB.shutdown(boolean) для
        // завершения ORB, false означает, что завершение
        // должно быть немедленным
        orb.shutdown(false);
    }
}

// ----- Реализация класса-сервера -----

public class HelloServer {
    // Сервер создает один или несколько объектов-сервантов.
    // Сервант наследует интерфейсу, сгенерированному компилятором
    // idlj, и реально выполняет все операции этого интерфейса.

    public static void main(String args[]) {
        try {
            // Создаем и инициализируем экземпляр ORB
            String[] par = {
                "-ORBInitialPort",
                "2050",
                "-ORBInitialHost",
                "localhost"
            };
            ORB orb = ORB.init(par, null);

            // Получаем доступ к Root POA и активируем POAManager
            POA rootpoa =
                POAHelper.narrow(orb.resolve_initial_references("RootPOA"));
            rootpoa.the_POAManager().activate();

            // Создаем объект сервант и регистрируем в нем объект ORB
            HelloImpl helloImpl = new HelloImpl();
            helloImpl.setORB(orb);

            // Получаем доступ к объекту серванта
            org.omg.CORBA.Object ref =
                rootpoa.servant_to_reference(helloImpl);
            Hello href = HelloHelper.narrow(ref);

            // Получаем корневой контекст именования
            org.omg.CORBA.Object objRef =
                orb.resolve_initial_references("NameService");
            // NamingContextExt является частью спецификации 
            // Interoperable Naming Service (INS)
            NamingContextExt ncRef =
                NamingContextExtHelper.narrow(objRef);

            // Связывание идентификатора "Hello" и объекта серванта
            String name = "Hello";
            NameComponent path[] = ncRef.to_name(name);
            ncRef.rebind(path, href);

            System.out.println("HelloServer готов и ждет обращений …");

            // Ожидание обращений клиентов
            orb.run();
        } catch (Exception e) {
            System.err.println("ОШИБКА: " + e); // Выводим сообщение 
            // об ошибке
            e.printStackTrace(System.out); // Выводим содержимое 
            // стека вызовов
        };

        System.out.println("HelloServer работу завершил …");
    }
}
```

Далее следуют пояснения отдельных строк кода.

ORB orb = ORB.init(par, null);

Любому серверу CORBA необходим локальный объект ORB. Каждый сервер создает экземпляр ORB и регистрирует в нем свои объекты-серванты, дабы ORB мог найти объекты при получении соответствующих запросов.

rootpoa.the\_POAManager().activate();

Операция activate() делает состояние POAManager’а активным, заставляя ассоциированные с ним POA начать обработку запросов. Каждый объект POA ассоциирован с одним объектом POAManager, но объект POAManager может быть ассоциирован с несколькими объектами POA.

org.omg.CORBA.Object objRef = rb.resolve\_initial\_references("NameService");

Для обеспечения доступа клиентов к операциям серванта сервер использует службу имен под названием Common Object Services (COS). Серверу необходим доступ к службе имен для того, чтобы он мог опубликовать ссылки на объекты, реализующие различные интерфейсы. В настоящее время реализовано 2 типа службы имен:&#x20;

tnameserv (временная) и orbd (временная и постоянная). Наш пример использует orbd. Аргумент в виде “NameService” понимается службами имен обоих типов, при этом для orbd он означает постоянный режим, а для tnameserv – временный. Для приведения orbd во временный режим необходимо использовать “TNameService”.NamingContextExt ncRef = NamingContextExtHelper.narrow(objRef);objRef – “первородный” (исходный) объект CORBA. Для его использования в качестве объекта NamingContextExt необходимо “преобразование типа”, выполняемое методом narrow() вспомогательного класса NamingContextExtHelper, сгенерированного компилятором idlj. После этого объект ncRef применяется для доступа к службе имен и регистрации в ней сервера.

**Создание клиентской части приложения**

Клиент объекта имеет доступ к объектной ссылке и вызывает операции объекта. Клиент знает только логическую структуру объекта в соответствии с его интерфейсом и может наблюдать за поведением объекта через вызовы методов.

Ниже представлен пример кода (в файле HelloClient.java каталога Hello) клиентской части приложения.

```
import HelloApp.*;
import org.omg.CosNaming.*;
import org.omg.CosNaming.NamingContextPackage.*;
import org.omg.CORBA.*;
public class HelloClient {
    static Hello helloImpl;
    public static void main(String args[]) {
        try {
            // Создание и инициализация ORB
            String[] par = {
                "-ORBInitialPort",
                "2050",
                "-ORBInitialHost",
                "localhost"
            };
            ORB orb = ORB.init(par, null);
            // Получение корневого контекста именования
            org.omg.CORBA.Object objRef =
                orb.resolve_initial_references("NameService");
            // NamingContextExt является частью спецификации
            // Interoperable Naming Service (INS)
            NamingContextExt ncRef = NamingContextExtHelper.narrow(objRef);
            // Получение доступа к серверу по его имени
            String name = "Hello";
            helloImpl = HelloHelper.narrow(ncRef.resolve_str(name));
            System.out.println("Получен доступ к объекту " + helloImpl);
            System.out.println(helloImpl.sayHello());
            helloImpl.shutdown();
        } catch (Exception e) {
            System.out.println("ОШИБКА : " + e);
            e.printStackTrace(System.out);
        }
    }
}
```

Прежде чем запустить на выполнение сервер, и затем клиент распределённого приложения, необходимо запустить сервис именования командой orbd из набора jdk 7 (в нашем случае был использован jdk1.7.0\_09) с параметрами порта и хоста.

Например:

\>orbd -ORBInitialPort 2050 -ORBInitialHost localhost
