---
title: Jak używać protokołu AMQP 1.0 przy użyciu interfejsu API Java usługi Service Bus | Dokumentacja firmy Microsoft
description: Jak używać usługi wiadomości Java (JMS) za pomocą usługi Azure Service Bus i zaawansowane komunikatów usługi kolejkowania Protodol (AMQP) 1.0.
services: service-bus-messaging
documentationcenter: java
author: spelluru
manager: timlt
editor: ''
ms.assetid: be766f42-6fd1-410c-b275-8c400c811519
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 08/10/2018
ms.author: spelluru
ms.openlocfilehash: b369f169fca903575ea4ae3f2ae04f6cd770e488
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/28/2018
ms.locfileid: "47433654"
---
# <a name="how-to-use-the-java-message-service-jms-api-with-service-bus-and-amqp-10"></a>Jak używać języka Java wiadomości usługi (JMS) interfejsu API za pomocą usługi Service Bus i protokołu AMQP 1.0
Zaawansowane komunikat (QUEUING Protocol) 1.0 to wydajny, niezawodny i protokół sieciowy niskiego poziomu obsługi komunikatów protokołu używanego do tworzenia niezawodnych, międzyplatformowych aplikacji do obsługi wiadomości.

Obsługa dla protokołu AMQP 1.0 w usłudze Service Bus oznacza, że można użyć usługi kolejkowania i publikowania/subskrybowania funkcji komunikatów obsługiwanych przez brokera z szeroką gamę platform za pomocą protokołu binarne wydajne. Ponadto możesz tworzyć aplikacje, które obejmuje składniki utworzone przy użyciu różnych języków, platform i systemów operacyjnych.

W tym artykule wyjaśniono, jak używać funkcji (kolejek i tematów publikowania/subskrybowania) komunikatów usługi Service Bus z aplikacji Java przy użyciu popularnych Java wiadomości usługi (JMS) standardowego interfejsu API. Brak [towarzyszący artykuł](service-bus-amqp-dotnet.md) , wyjaśnia, jak wykonywać te same przy użyciu interfejsu API .NET usługi Service Bus. Te dwa przewodniki można użyć ze sobą, aby dowiedzieć się więcej na temat dla wielu platform, obsługi komunikatów za pomocą protokołu AMQP 1.0.

## <a name="get-started-with-service-bus"></a>Rozpoczynanie pracy z usługą Service Bus
W tym przewodniku założono, że masz już przestrzeń nazw usługi Service Bus zawierający kolejkę o nazwie **kolejki Kolejka1**. Jeśli tego nie zrobisz, a następnie możesz [tworzenie przestrzeni nazw i kolejki](service-bus-create-namespace-portal.md) przy użyciu [witryny Azure portal](https://portal.azure.com). Aby uzyskać więcej informacji na temat tworzenia przestrzeni nazw usługi Service Bus i kolejki, zobacz [Rozpoczynanie pracy z kolejkami usługi Service Bus](service-bus-dotnet-get-started-with-queues.md).

> [!NOTE]
> Partycjonowane kolejki i tematy obsługują także protokół AMQP. Aby uzyskać więcej informacji, zobacz [partycjonowane jednostki do obsługi komunikatów](service-bus-partitioning.md) i [obsługę protokołu AMQP 1.0 dla usługi Service Bus partycjonowane kolejki i tematy](service-bus-partitioned-queues-and-topics-amqp-overview.md).
> 
> 

## <a name="downloading-the-amqp-10-jms-client-library"></a>Pobieranie biblioteki klienta protokołu AMQP 1.0 JMS
Aby uzyskać informacje o tym, gdzie można pobrać najnowszej wersji biblioteki klienta usługi Apache Qpid JMS protokołu AMQP 1.0, odwiedź stronę [ https://qpid.apache.org/download.html ](https://qpid.apache.org/download.html).

Należy dodać następujące cztery pliki JAR z archiwum Apache Qpid JMS protokołu AMQP 1.0 w dystrybucji do klasy Java podczas kompilowania i uruchamiania aplikacji JMS z usługą Service Bus:

* geronimo jms\_1.1\_1.0.jar Specyfikacja
* qpid-amqp-1-0-Client-[Version].JAR
* qpid-amqp-1-0-Client-jms-[Version].JAR
* qpid-amqp-1-0-Common-[Version].JAR

> ! [UWAGA] JMS JAR nazwy i wersji mógł ulec zmianie. Aby uzyskać więcej informacji, zobacz [JMS Qpid - protokołu AMQP 1.0](https://qpid.apache.org/maven.html#qpid-jms-amqp-10).

## <a name="coding-java-applications"></a>Kodowanie aplikacji w języku Java
### <a name="java-naming-and-directory-interface-jndi"></a>Nazewnictwa języka Java i interfejsu katalogu (JNDI)
JMS używa nazewnictwa języka Java i interfejsu katalogu (JNDI) w celu utworzenia rozdzielenie nazwy logicznej lub fizycznej. Dwa typy obiektów JMS są rozpoznawane za pomocą JNDI: ConnectionFactory i docelowym. JNDI korzysta z modelu dostawcy, do którego można dodać inny katalog usług do obsługi określonych obowiązków rozpoznawania nazw. Formatowanie opartych na plikach JNDI dostawcy jest skonfigurowany, używając właściwości pliku następujące Apache Qpid JMS protokołu AMQP 1.0 biblioteki, który jest dostarczany z prostych właściwości:

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

#### <a name="configure-the-connectionfactory"></a>Konfigurowanie ConnectionFactory
Wpis używane do definiowania **ConnectionFactory** w pliku właściwości Qpid JNDI dostawcy ma następujący format:

```
connectionfactory.[jndi_name] = [ConnectionURL]
```

Gdzie **[jndi_name]** i **[ConnectionURL]** mają następujące znaczenie:

* **[jndi_name]** : Nazwa logiczna ConnectionFactory. Jest to nazwa, która zostanie rozwiązany w aplikacji Java przy użyciu metody JNDI IntialContext.lookup().
* **[ConnectionURL]** : Adres URL, który zawiera biblioteki JMS informacjami wymaganymi do brokera protokołu AMQP.

Format **ConnectionURL** jest następująca:

```
amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net
```
Gdzie **[przestrzeń nazw]**, **[SASPolicyName]** i **[SASPolicyKey]** mają następujące znaczenie:

* **[przestrzeń nazw]** : Przestrzeń nazw magistrali usług.
* **[SASPolicyName]** : Nazwa zasad sygnatura dostępu współdzielonego kolejki.
* **[SASPolicyKey]** : Klucz zasad kolejki Shared Access Signature.

> [!NOTE]
> Należy zakodować adres URL hasła ręcznie. Przydatne narzędzie kodowania adresu URL znajduje się w temacie [ http://www.w3schools.com/tags/ref_urlencode.asp ](http://www.w3schools.com/tags/ref_urlencode.asp).
> 
> 

#### <a name="configure-destinations"></a>Konfigurowanie miejsc docelowych
Wpis służącą do definiowania miejsca docelowego w dostawcy JNDI Qpid właściwości plików ma następujący format:

```
queue.[jndi_name] = [physical_name]
```

lub

```
topic.[jndi_name] = [physical_name]
```

Gdzie **[jndi\_name]** i **[fizycznych\_name]** mają następujące znaczenie:

* **[jndi_name]** : Nazwa logiczna miejsca docelowego. Jest to nazwa, która zostanie rozwiązany w aplikacji Java przy użyciu metody JNDI IntialContext.lookup().
* **[physical_name]** : Nazwa jednostki usługi Service Bus, do której aplikacji wysyła i odbiera komunikaty.

> [!NOTE]
> Podczas odbierania z subskrypcji tematu usługi Service Bus, Nazwa fizyczna, określone w JNDI powinna być nazwą tego tematu. Nazwa subskrypcji znajduje się po utworzeniu subskrypcji trwałe w kodzie aplikacji JMS. [Usługi Service Bus 1.0 przewodnik dewelopera protokołu AMQP](service-bus-amqp-dotnet.md) zawiera bardziej szczegółowe informacje na temat pracy z obsługą tematów usługi Service Bus z JMS.
> 
> 

### <a name="write-the-jms-application"></a>Napisanie aplikacji JMS
Brak specjalnego interfejsów API i opcje wymagany w przypadku korzystania z JMS z usługą Service Bus. Istnieje jednak kilka ograniczeń, które zostały omówione później. Z każdą aplikacją JMS po pierwsze wymagana jest Konfiguracja środowiska JNDI, aby mieć możliwość rozpoznania **ConnectionFactory** i miejsc docelowych.

#### <a name="configure-the-jndi-initialcontext"></a>Konfigurowanie JNDI InitialContext
Środowisko JNDI jest skonfigurowane, przekazując tablicę skrótów informacji o konfiguracji w konstruktorze klasy javax.naming.InitialContext. Dwa wymagane elementy w tablicy skrótów są nazwa klasy początkowej fabryce kontekst i adres URL dostawcy. Poniższy kod przedstawia sposób konfigurowania środowiska JNDI, użyj Qpid dostawcy JNDI opartą na plikach właściwości z plikiem właściwości o nazwie **servicebus.properties**.

```java
Hashtable<String, String> env = new Hashtable<>();
env.put("connectionfactory.SBCF", "amqps://[namespace].servicebus.windows.net?amqp.idleTimeout=120000");
env.put("queue.QUEUE", "queue");

env.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(env);
``` 

### <a name="a-simple-jms-application-using-a-service-bus-queue"></a>Prostą aplikację JMS, korzystając z kolejki usługi Service Bus
Następujący program przykład wysyła JMS TextMessages do kolejki usługi Service Bus przy użyciu nazwy logicznej JNDI kolejki i odbiera komunikaty, w ponownie.


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
        Hashtable<String, String> env = new Hashtable<>();
        // Specify the name of your namespace. Idle timeout value is set as Service Bus enforces timeout.         
        env.put("connectionfactory.SBCF", "amqps://[namespace].servicebus.windows.net?amqp.idleTimeout=120000");
        env.put("queue.QUEUE", "queue");

        env.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
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
Uruchomiona jest aplikacja generuje dane wyjściowe w postaci:

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

## <a name="amqp-disposition-and-service-bus-operation-mapping"></a>Protokół AMQP dyspozycji i mapowanie operacji usługi Service Bus
Poniżej przedstawiono, jak dyspozycji AMQP przekłada się na operację usługi Service Bus:

```
ACCEPTED = 1; -> Complete()
REJECTED = 2; -> DeadLetter()
RELEASED = 3; (just unlock the message in service bus, will then get redelivered)
MODIFIED_FAILED = 4; -> Abandon() which increases delivery count
MODIFIED_FAILED_UNDELIVERABLE = 5; -> Defer()
```

## <a name="cross-platform-messaging-between-jms-and-net"></a>Dla wielu platform obsługi komunikatów między JMS i .NET
W przewodniku pokazano, jak wysyłać i odbierać komunikaty z usługi Service Bus przy użyciu JMS. Jednak jest jedną z kluczowych zalet protokołu AMQP 1.0, umożliwia aplikacji, które ma zostać utworzony za pomocą składników napisanych w różnych językach, za pomocą komunikatów wymienianych niezawodnie i w pełnej rozdzielczości.

Za pomocą przykładowej aplikacji JMS opisanych powyżej i podobnej aplikacji .NET z artykułu pomocnika, [przy użyciu usługi Service Bus z .NET przy użyciu protokołu AMQP 1.0](service-bus-amqp-dotnet.md), mogą wymieniać komunikaty między .NET i Java. Przeczytaj ten artykuł, aby uzyskać więcej informacji na temat szczegółów dla wielu platform komunikatów za pomocą usługi Service Bus i protokołu AMQP 1.0.

### <a name="jms-to-net"></a>JMS do platformy .NET
Aby zademonstrować JMS do obsługi wiadomości platformy .NET:

* Uruchom przykładową aplikację .NET bez żadnych argumentów wiersza polecenia.
* Uruchom przykładową aplikację Java z argumentem wiersza polecenia "sendonly". W tym trybie aplikacja nie otrzyma wiadomości z kolejki, tylko wysyła.
* Naciśnij klawisz **Enter** kilka razy w konsoli aplikacji Java, która spowoduje, że wiadomości do wysłania.
* Te komunikaty są odbierane przez aplikację platformy .NET.

#### <a name="output-from-jms-application"></a>Dane wyjściowe z aplikacji JMS
```
> java SimpleSenderReceiver sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with JMSMessageID = ID:4364096528752411591
Sent message with JMSMessageID = ID:459252991689389983
Sent message with JMSMessageID = ID:1565011046230456854
exit
```

#### <a name="output-from-net-application"></a>Dane wyjściowe z aplikacji .NET
```
> SimpleSenderReceiver.exe    
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with MessageID = 4364096528752411591
Received message with MessageID = 459252991689389983
Received message with MessageID = 1565011046230456854
exit
```

### <a name="net-to-jms"></a>JMS języka .NET
Aby zademonstrować .NET do obsługi komunikatów JMS:

* Uruchom przykładową aplikację .NET z argumentem wiersza polecenia "sendonly". W tym trybie aplikacja nie otrzyma wiadomości z kolejki, tylko wysyła.
* Uruchom przykładową aplikację Java bez żadnych argumentów wiersza polecenia.
* Naciśnij klawisz **Enter** kilka razy w konsoli aplikacji .NET, co spowoduje komunikatów do wysłania.
* Te komunikaty są odbierane przez aplikację języka Java.

#### <a name="output-from-net-application"></a>Dane wyjściowe z aplikacji .NET
```
> SimpleSenderReceiver.exe sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with MessageID = d64e681a310a48a1ae0ce7b017bf1cf3    
Sent message with MessageID = 98a39664995b4f74b32e2a0ecccc46bb
Sent message with MessageID = acbca67f03c346de9b7893026f97ddeb
exit
```

#### <a name="output-from-jms-application"></a>Dane wyjściowe z aplikacji JMS
```
> java SimpleSenderReceiver    
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with JMSMessageID = ID:d64e681a310a48a1ae0ce7b017bf1cf3
Received message with JMSMessageID = ID:98a39664995b4f74b32e2a0ecccc46bb
Received message with JMSMessageID = ID:acbca67f03c346de9b7893026f97ddeb
exit
```

## <a name="unsupported-features-and-restrictions"></a>Nieobsługiwane funkcje i ograniczenia
Korzystając z JMS za pośrednictwem protokołu AMQP 1.0 przy użyciu usługi Service Bus, a mianowicie obowiązują następujące ograniczenia:

* Tylko jeden **MessageProducer** lub **MessageConsumer** jest dozwolone **sesji**. Jeśli musisz utworzyć wiele **MessageProducers** lub **MessageConsumers** w aplikacji, Utwórz dedykowane **sesji** dla każdego z nich.
* Subskrypcje tematu volatile nie są obecnie obsługiwane.
* **MessageSelectors** nie są obecnie obsługiwane.
* Tymczasowe miejsc docelowych; na przykład **TemporaryQueue**, **TemporaryTopic** nie są obecnie obsługiwane, wraz z **QueueRequestor** i **TopicRequestor**Interfejsów API, które z nich korzystają.
* Sesje transakcyjne i transakcje rozproszone nie są obsługiwane.

## <a name="summary"></a>Podsumowanie
W tym przewodniku pokazano, jak używać funkcji usługi Service Bus obsługiwanych przez brokera obsługi komunikatów (kolejek i tematów publikowania/subskrybowania) w języku Java przy użyciu popularnych interfejsów API JMS i protokołu AMQP 1.0.

Można również użyć usługi Service Bus protokołu AMQP 1.0 w innych językach, takich jak .NET, C, Python i PHP. Składniki utworzone za pomocą tych różnych językach mogą wymiana komunikatów w niezawodny sposób będą i w pełnej rozdzielczości, przy użyciu protokołu AMQP 1.0 obsługi w usłudze Service Bus.

## <a name="next-steps"></a>Kolejne kroki
* [Obsługa protokołu AMQP 1.0 w usłudze Azure Service Bus](service-bus-amqp-overview.md)
* [Sposób korzystania z protokołu AMQP 1.0 przy użyciu interfejsu API .NET usługi Service Bus](service-bus-dotnet-advanced-message-queuing.md)
* [Service Bus protokołu AMQP 1.0 przewodnik dewelopera](service-bus-amqp-dotnet.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Centrum deweloperów języka Java](https://azure.microsoft.com/develop/java/)

