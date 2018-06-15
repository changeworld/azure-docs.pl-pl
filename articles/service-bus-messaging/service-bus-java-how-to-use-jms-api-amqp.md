---
title: Jak używać protokołu AMQP 1.0 przy użyciu interfejsu API języka Java Service Bus | Dokumentacja firmy Microsoft
description: Jak używać usługi komunikat Java (JMS) z usługi Azure Service Bus i zaawansowane komunikatów usługi kolejkowania wiadomości Protodol (AMQP) 1.0.
services: service-bus-messaging
documentationcenter: java
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: be766f42-6fd1-410c-b275-8c400c811519
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 08/10/2017
ms.author: sethm
ms.openlocfilehash: 0848facd764c4fb0d7f95c1ae89ecb02a32257e1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23868382"
---
# <a name="how-to-use-the-java-message-service-jms-api-with-service-bus-and-amqp-10"></a>Jak używać języka Java wiadomości usługi (JMS) interfejsu API z usługi Service Bus i protokołu AMQP 1.0
Zaawansowane komunikatów usługi kolejkowania wiadomości protokołu (AMQP) 1.0 to wydajne, niezawodne i poziom przewodowy obsługi wiadomości protokół służy do tworzenia aplikacji wiadomości niezawodne, między platformami.

Obsługa dla protokołu AMQP 1.0 w usłudze Service Bus oznacza, że można użyć Kolejkowanie i publikowania/subskrypcji obsługiwanych przez brokera obsługi komunikatów funkcje z zakresu od platformy przy użyciu protokołu binarnego wydajne. Ponadto można tworzyć aplikacje obejmuje składniki utworzony przy użyciu różnych języków, struktur i systemów operacyjnych.

W tym artykule opisano sposób użycia funkcji (kolejki i tematy publikowania/subskrypcji) do obsługi komunikatów usługi Service Bus z aplikacji języka Java przy użyciu popularnych Java wiadomości usługi (JMS) interfejsu API standardowa. Brak [artykułu Pomocnika](service-bus-amqp-dotnet.md) objaśniający sposób wykonaj te same przy użyciu interfejsu API usługi Service Bus .NET. Umożliwia te dwa przewodniki razem więcej informacji na temat obsługi wiadomości między platformami przy użyciu protokołu AMQP 1.0.

## <a name="get-started-with-service-bus"></a>Rozpoczynanie pracy z usługą Service Bus
W tym przewodniku założono, że już przestrzeń nazw magistrali usług zawierający kolejki o nazwie **kolejki Kolejka1**. Jeśli nie chcesz, możesz [tworzenie przestrzeni nazw i kolejki](service-bus-create-namespace-portal.md) przy użyciu [portalu Azure](https://portal.azure.com). Aby uzyskać więcej informacji na temat tworzenia przestrzeni nazw usługi Service Bus i kolejek, zobacz [Rozpoczynanie pracy z kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md).

> [!NOTE]
> Partycjonowane kolejki i tematy obsługują także protokołu AMQP. Aby uzyskać więcej informacji, zobacz [partycjonowane jednostki do obsługi komunikatów](service-bus-partitioning.md) i [obsługi protokołu AMQP 1.0 dla usługi Service Bus podzielona na partycje, kolejek i tematów](service-bus-partitioned-queues-and-topics-amqp-overview.md).
> 
> 

## <a name="downloading-the-amqp-10-jms-client-library"></a>Pobieranie biblioteki klienta protokołu AMQP 1.0 JMS
Aby dowiedzieć się, skąd pobrać najnowszej wersji biblioteki klienta Apache Qpid JMS protokołu AMQP 1.0, odwiedź stronę [https://qpid.apache.org/download.html](https://qpid.apache.org/download.html).

Należy dodać następujące cztery pliki JAR z archiwum dystrybucji Apache Qpid JMS protokołu AMQP 1.0 do klasy Java podczas tworzenia i uruchamiania aplikacji JMS za pomocą usługi Service Bus:

* geronimo jms\_1.1\_1.0.jar specyfikacji
* qpid-amqp-1-0-Client-[Version].JAR
* qpid-amqp-1-0-Client-jms-[Version].JAR
* qpid-amqp-1-0-Common-[Version].JAR

## <a name="coding-java-applications"></a>Kodowanie aplikacji Java
### <a name="java-naming-and-directory-interface-jndi"></a>Nazewnictwo Java i katalog interfejsu (JNDI)
JMS używa nazwy języka Java i katalog interfejsu (JNDI) do utworzenia rozdzielenie nazwy logicznej i fizycznej. Dwa typy obiektów JMS są rozpoznawane przy użyciu JNDI: ConnectionFactory i docelowego. JNDI korzysta z modelu dostawcy, w którym można podłączyć innego katalogu usługi do obsługi obowiązków rozpoznawania nazw. Format opartych na plikach JNDI dostawcy, który jest skonfigurowany przy użyciu pliku właściwości następujących Apache Qpid JMS protokołu AMQP 1.0 biblioteka zawiera proste właściwości:

```
# servicebus.properties - sample JNDI configuration

# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCF = amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net

# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
queue.QUEUE = queue1
```

#### <a name="configure-the-connectionfactory"></a>Skonfiguruj ConnectionFactory
Używane do definiowania wpisu **ConnectionFactory** w pliku właściwości Qpid JNDI dostawcy jest następujący format:

```
connectionfactory.[jndi_name] = [ConnectionURL]
```

Gdzie **[jndi_name]** i **[ConnectionURL]** mają następujące znaczenie:

* **[jndi_name]** : ConnectionFactory nazwę logiczną. Jest to nazwa, która zostanie rozwiązany w aplikacji Java za pomocą metody JNDI IntialContext.lookup().
* **[ConnectionURL]** : Adres URL, który zapewnia biblioteki JMS informacje wymagane do brokera protokołu AMQP.

Format **ConnectionURL** wygląda następująco:

```
amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net
```
Gdzie **[przestrzeń nazw]**, **[SASPolicyName]** i **[SASPolicyKey]** mają następujące znaczenie:

* **[przestrzeń nazw]** : Przestrzeń nazw magistrali usług.
* **[SASPolicyName]** : Nazwa zasady sygnatura dostępu współdzielonego kolejki.
* **[SASPolicyKey]** : Klucz zasad sygnatura dostępu współdzielonego kolejki.

> [!NOTE]
> Należy zakodować adres URL hasła ręcznie. Przydatne narzędzie kodowania adresu URL jest dostępna na [http://www.w3schools.com/tags/ref_urlencode.asp](http://www.w3schools.com/tags/ref_urlencode.asp).
> 
> 

#### <a name="configure-destinations"></a>Konfigurowanie miejsc docelowych
Wpis służy do definiowania miejsca docelowego w dostawcy JNDI Qpid właściwości plików ma następujący format:

```
queue.[jndi_name] = [physical_name]
```

lub

```
topic.[jndi_name] = [physical_name]
```

Gdzie **[jndi\_name]** i **[fizycznych\_name]** mają następujące znaczenie:

* **[jndi_name]** : Nazwy logicznej miejsca docelowego. Jest to nazwa, która zostanie rozwiązany w aplikacji Java za pomocą metody JNDI IntialContext.lookup().
* **[physical_name]** : Nazwa jednostki magistrali usług, do którego wysyła lub odbiera komunikaty aplikacji.

> [!NOTE]
> Podczas odbierania z subskrypcję tematu usługi Service Bus, nazwa fizycznego określony w JNDI powinna być nazwę tematu. Po utworzeniu subskrypcji trwałe w kodzie aplikacji JMS, jest podać nazwę subskrypcji. [Service Bus protokołu AMQP 1.0 przewodnik dewelopera](service-bus-amqp-dotnet.md) zawiera więcej szczegółowych informacji dotyczących pracy z tematów usługi Service Bus z JMS.
> 
> 

### <a name="write-the-jms-application"></a>Pisanie aplikacji JMS
Nie ma żadnych specjalnych interfejsów API lub opcje potrzebne podczas korzystania z usługi Service Bus JMS. Istnieje jednak kilka ograniczeń, które zostały omówione później. Podobnie jak w dowolnej aplikacji JMS po pierwsze wymagana jest Konfiguracja środowiska JNDI, aby można było rozpoznać **ConnectionFactory** i miejsc docelowych.

#### <a name="configure-the-jndi-initialcontext"></a>Skonfiguruj JNDI InitialContext
Środowisko JNDI jest skonfigurowane przez przekazanie do konstruktora klasy javax.naming.InitialContext hashtable informacji o konfiguracji. Dwa wymaganych nazwę klasy początkowej fabryki kontekstu i adres URL dostawcy elementów w tablicy skrótów. Poniższy kod przedstawia sposób konfigurowania środowiska JNDI do użycia Qpid dostawcy JNDI opartą na plikach właściwości z plikiem właściwości o nazwie **servicebus.properties**.

```java
Hashtable<String, String> env = new Hashtable<String, String>(); 
env.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory"); 
env.put(Context.PROVIDER_URL, "servicebus.properties"); 
InitialContext context = new InitialContext(env);
``` 

### <a name="a-simple-jms-application-using-a-service-bus-queue"></a>Prostą aplikację JMS przy użyciu kolejki usługi Service Bus
Następujący przykład program wysyła JMS TextMessages do kolejki usługi Service Bus z nazwy logicznej JNDI kolejki i odbiera komunikaty ponownie.

```java
// SimpleSenderReceiver.java

import javax.jms.*;
import javax.naming.Context;
import javax.naming.InitialContext;
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.Hashtable;
import java.util.Random;

public class SimpleSenderReceiver implements MessageListener {
    private static boolean runReceiver = true;
    private Connection connection;
    private Session sendSession;
    private Session receiveSession;
    private MessageProducer sender;
    private MessageConsumer receiver;
    private static Random randomGenerator = new Random();

    public SimpleSenderReceiver() throws Exception {
        // Configure JNDI environment
        Hashtable<String, String> env = new Hashtable<String, String>();
        env.put(Context.INITIAL_CONTEXT_FACTORY, 
                   "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory");
        env.put(Context.PROVIDER_URL, "servicebus.properties");
        Context context = new InitialContext(env);

        // Look up ConnectionFactory and Queue
        ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
        Destination queue = (Destination) context.lookup("QUEUE");

        // Create Connection
        connection = cf.createConnection();

        // Create sender-side Session and MessageProducer
        sendSession = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
        sender = sendSession.createProducer(queue);

        if (runReceiver) {
            // Create receiver-side Session, MessageConsumer,and MessageListener
            receiveSession = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
            receiver = receiveSession.createConsumer(queue);
            receiver.setMessageListener(this);
            connection.start();
        }
    }

    public static void main(String[] args) {
        try {

            if ((args.length > 0) && args[0].equalsIgnoreCase("sendonly")) {
                runReceiver = false;
            }

            SimpleSenderReceiver simpleSenderReceiver = new SimpleSenderReceiver();
            System.out.println("Press [enter] to send a message. Type 'exit' + [enter] to quit.");
            BufferedReader commandLine = new java.io.BufferedReader(new InputStreamReader(System.in));

            while (true) {
                String s = commandLine.readLine();
                if (s.equalsIgnoreCase("exit")) {
                    simpleSenderReceiver.close();
                    System.exit(0);
                } else {
                    simpleSenderReceiver.sendMessage();
                }
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    private void sendMessage() throws JMSException {
        TextMessage message = sendSession.createTextMessage();
        message.setText("Test AMQP message from JMS");
        long randomMessageID = randomGenerator.nextLong() >>>1;
        message.setJMSMessageID("ID:" + randomMessageID);
        sender.send(message);
        System.out.println("Sent message with JMSMessageID = " + message.getJMSMessageID());
    }

    public void close() throws JMSException {
        connection.close();
    }

    public void onMessage(Message message) {
        try {
            System.out.println("Received message with JMSMessageID = " + message.getJMSMessageID());
            message.acknowledge();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}    
```

### <a name="run-the-application"></a>Uruchamianie aplikacji
Uruchamianie aplikacji generuje dane wyjściowe w formie:

```
> java SimpleSenderReceiver
Press [enter] to send a message. Type 'exit' + [enter] to quit.

Sent message with JMSMessageID = ID:2867600614942270318
Received message with JMSMessageID = ID:2867600614942270318

Sent message with JMSMessageID = ID:7578408152750301483
Received message with JMSMessageID = ID:7578408152750301483

Sent message with JMSMessageID = ID:956102171969368961
Received message with JMSMessageID = ID:956102171969368961
exit
```

## <a name="cross-platform-messaging-between-jms-and-net"></a>Wysyłanie komunikatów między JMS i .NET wielu platform
W tym przewodniku pokazano, jak wysyłać i odbierać komunikaty z magistrali usług przy użyciu JMS. Jedną z kluczowych zalet protokołu AMQP 1.0 jest jednak umożliwia aplikacji ma zostać utworzony ze składników zapisany w różnych językach, za pomocą wiadomości wymieniane niezawodne i w pełnej rozdzielczości.

Za pomocą przykładowej aplikacji JMS opisane powyżej i podobnych aplikacji .NET z artykułu pomocnika, [przy użyciu usługi Service Bus z .NET z protokołu AMQP 1.0](service-bus-amqp-dotnet.md), mogą wymieniać komunikaty między .NET i Java. Przeczytaj ten artykuł, aby uzyskać więcej informacji na temat szczegóły wieloplatformowych wiadomości przy użyciu usługi Service Bus i protokołu AMQP 1.0.

### <a name="jms-to-net"></a>JMS do .NET
Aby zademonstrować JMS do obsługi komunikatów .NET:

* Uruchom przykładową aplikację .NET bez żadnych argumentów wiersza polecenia.
* Uruchom przykładową aplikację Java z argumentem wiersza polecenia "sendonly". W tym trybie aplikacja nie będą otrzymywać wiadomości z kolejki, tylko wysyła.
* Naciśnij klawisz **Enter** kilka razy w konsoli aplikacji Java, która spowoduje, że komunikatów do wysłania.
* Te komunikaty są odbierane przez aplikację .NET.

#### <a name="output-from-jms-application"></a>Dane wyjściowe aplikacji JMS
```
> java SimpleSenderReceiver sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with JMSMessageID = ID:4364096528752411591
Sent message with JMSMessageID = ID:459252991689389983
Sent message with JMSMessageID = ID:1565011046230456854
exit
```

#### <a name="output-from-net-application"></a>Dane wyjściowe aplikacji .NET
```
> SimpleSenderReceiver.exe    
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with MessageID = 4364096528752411591
Received message with MessageID = 459252991689389983
Received message with MessageID = 1565011046230456854
exit
```

### <a name="net-to-jms"></a>.NET do JMS
Aby zademonstrować .NET do obsługi komunikatów JMS:

* Uruchom przykładową aplikację .NET z argumentem wiersza polecenia "sendonly". W tym trybie aplikacja nie będą otrzymywać wiadomości z kolejki, tylko wysyła.
* Uruchom przykładową aplikację Java bez żadnych argumentów wiersza polecenia.
* Naciśnij klawisz **Enter** kilka razy w aplikacji konsoli .NET, co spowoduje wyświetlanie komunikatów do wysłania.
* Te komunikaty są odbierane przez aplikację Java.

#### <a name="output-from-net-application"></a>Dane wyjściowe aplikacji .NET
```
> SimpleSenderReceiver.exe sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with MessageID = d64e681a310a48a1ae0ce7b017bf1cf3    
Sent message with MessageID = 98a39664995b4f74b32e2a0ecccc46bb
Sent message with MessageID = acbca67f03c346de9b7893026f97ddeb
exit
```

#### <a name="output-from-jms-application"></a>Dane wyjściowe aplikacji JMS
```
> java SimpleSenderReceiver    
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with JMSMessageID = ID:d64e681a310a48a1ae0ce7b017bf1cf3
Received message with JMSMessageID = ID:98a39664995b4f74b32e2a0ecccc46bb
Received message with JMSMessageID = ID:acbca67f03c346de9b7893026f97ddeb
exit
```

## <a name="unsupported-features-and-restrictions"></a>Nieobsługiwane funkcje i ograniczenia
Korzystając z JMS za pośrednictwem protokołu AMQP 1.0 z usługą Service Bus, a mianowicie obowiązują następujące ograniczenia:

* Tylko jeden **MessageProducer** lub **MessageConsumer** dozwolone jest używanie **sesji**. Jeśli trzeba utworzyć wiele **MessageProducers** lub **MessageConsumers** w aplikacji, Utwórz dedykowana **sesji** dla każdego z nich.
* Subskrypcje tematu nietrwałe nie są obecnie obsługiwane.
* **MessageSelectors** nie są obecnie obsługiwane.
* Tymczasowe miejsc docelowych; na przykład **TemporaryQueue**, **TemporaryTopic** nie są obecnie obsługiwane, wraz z **QueueRequestor** i **TopicRequestor**Interfejsów API, które z nich korzystają.
* Transakcyjne sesji i transakcje rozproszone nie są obsługiwane.

## <a name="summary"></a>Podsumowanie
Ten przewodnik pokazano, jak używać funkcji usługi Service Bus obsługiwanych przez brokera obsługi komunikatów (kolejki i tematy publikowania/subskrypcji) za pomocą języka Java przy użyciu popularnych JMS interfejsu API i protokołu AMQP 1.0.

Umożliwia także Service Bus protokołu AMQP 1.0 z innych języków, w tym .NET, C, Python i PHP. Składniki utworzone przy użyciu tych języków można niezawodnie wymiany wiadomości i przy pełnej rozdzielczości, przy użyciu protokołu AMQP 1.0 obsługuje w usłudze Service Bus.

## <a name="next-steps"></a>Następne kroki
* [Obsługa protokołu AMQP 1.0 w Azure Service Bus](service-bus-amqp-overview.md)
* [Jak używać protokołu AMQP 1.0 z interfejsu API usługi Service Bus .NET](service-bus-dotnet-advanced-message-queuing.md)
* [Service Bus protokołu AMQP 1.0 przewodnik dewelopera](service-bus-amqp-dotnet.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Centrum deweloperów języka Java](https://azure.microsoft.com/develop/java/)

