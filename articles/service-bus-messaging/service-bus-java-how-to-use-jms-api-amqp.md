---
title: Używanie usługi AMQP z interfejsem API usługi komunikatów Java & usługi Azure Service Bus
description: Jak korzystać z usługi Java Message Service (JMS) z usługą Azure Service Bus i advanced message Usłudze kolejkowania protokołu (AMQP) 1.0.
services: service-bus-messaging
documentationcenter: java
author: axisc
editor: spelluru
ms.assetid: be766f42-6fd1-410c-b275-8c400c811519
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 10/22/2019
ms.author: aschhab
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: cd06838abbb69af5684fdea18c42f6a8f95ffe2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77371250"
---
# <a name="use-the-java-message-service-jms-with-azure-service-bus-and-amqp-10"></a>Korzystanie z usługi komunikatów Java (JMS) z usługą Azure Service Bus i AMQP 1.0
W tym artykule wyjaśniono, jak używać funkcji obsługi wiadomości usługi Azure Service Bus (kolejki i publikować/subskrybować tematy) z aplikacji Java przy użyciu popularnego standardu interfejsu API usługi Java Message Service (JMS). Istnieje [artykuł towarzyszący,](service-bus-amqp-dotnet.md) który wyjaśnia, jak zrobić to samo przy użyciu interfejsu API usługi Azure Service Bus .NET. Możesz użyć tych dwóch przewodników razem, aby dowiedzieć się więcej o wiadomościach między platformami przy użyciu narzędzia AMQP 1.0.

Protokół AMQP (Advanced Message Queuing Protocol) 1.0 to wydajny, niezawodny protokół obsługi wiadomości na poziomie przewodowym, którego można używać do tworzenia niezawodnych aplikacji do obsługi wiadomości między platformami.

Obsługa protokołu AMQP 1.0 w usłudze Azure Service Bus oznacza, że można korzystać z funkcji kolejkowania i publikowania/subskrybowania wiadomości obsługiwanych przez brokera z wielu platform przy użyciu wydajnego protokołu binarnego. Ponadto można tworzyć aplikacje składające się ze składników utworzonych przy użyciu kombinacji języków, struktur i systemów operacyjnych.

## <a name="get-started-with-service-bus"></a>Rozpoczynanie pracy z usługą Service Bus
W tym przewodniku przyjęto założenie, że masz już `basicqueue`obszar nazw usługi Service Bus zawierający kolejkę o nazwie . Jeśli tego nie zrobisz, możesz [utworzyć obszar nazw i kolejkę](service-bus-create-namespace-portal.md) za pomocą [witryny Azure portal](https://portal.azure.com). Aby uzyskać więcej informacji na temat tworzenia obszarów nazw i kolejek usługi Service Bus, zobacz [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md).

> [!NOTE]
> Kolejki i tematy podzielone na partycje również obsługują amqp. Aby uzyskać więcej informacji, zobacz [jednostki obsługi wiadomości podzielonych na partycje](service-bus-partitioning.md) i [obsługa usługi AMQP 1.0 dla kolejek i tematów podzielonych na partycje usługi Service Bus](service-bus-partitioned-queues-and-topics-amqp-overview.md).
> 
> 

## <a name="downloading-the-amqp-10-jms-client-library"></a>Pobieranie biblioteki klienta AMQP 1.0 JMS
Aby uzyskać informacje o tym, gdzie pobrać najnowszą wersję biblioteki klienta Apache Qpid [https://qpid.apache.org/download.html](https://qpid.apache.org/download.html)JMS AMQP 1.0, odwiedź .

Podczas tworzenia i uruchamiania aplikacji JMS za pomocą usługi Service Bus należy dodać następujące cztery pliki JAR z archiwum dystrybucji Apache Qpid JMS AMQP 1.0:

* geronimo-jms\_1.1\_spec-1.0.jar
* qpid-jms-client-[version].jar

> [!NOTE]
> Nazwy i wersje JMS JAR mogły ulec zmianie. Aby uzyskać szczegółowe informacje, zobacz [Qpid JMS - AMQP 1.0](https://qpid.apache.org/maven.html#qpid-jms-amqp-10).

## <a name="coding-java-applications"></a>Kodowanie aplikacji Java
### <a name="java-naming-and-directory-interface-jndi"></a>Interfejs nazewnictwa i katalogów Java (JNDI)
JMS używa java nazewnictwa i interfejsu katalogowego (JNDI) do tworzenia separacji między nazwami logicznymi i nazwami fizycznymi. Dwa typy obiektów JMS są rozpoznawane przy użyciu JNDI: ConnectionFactory i Destination. JNDI używa modelu dostawcy, do którego można podłączyć różne usługi katalogowe do obsługi obowiązków rozpoznawania nazw. Biblioteka Apache Qpid JMS AMQP 1.0 jest wyposażona w prosty dostawca JNDI oparty na plikach właściwości, który jest skonfigurowany przy użyciu pliku właściwości w następującym formacie:

```TEXT
# servicebus.properties - sample JNDI configuration

# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCF = amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net

# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
queue.QUEUE = queue1
```

#### <a name="setup-jndi-context-and-configure-the-connectionfactory"></a>Konfigurowanie kontekstu JNDI i konfigurowanie connectionfactory

**ConnectionString** odwołuje się w jednym dostępny w "Zasady dostępu udostępnionego" w [witrynie Azure portal](https://portal.azure.com) w obszarze **Podstawowy ciąg połączenia**
```java
// The connection string builder is the only part of the azure-servicebus SDK library
// we use in this JMS sample and for the purpose of robustly parsing the Service Bus 
// connection string. 
ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);
        
// set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");

// Look up queue
Destination queue = (Destination) context.lookup("QUEUE");
```

#### <a name="configure-producer-and-consumer-destination-queues"></a>Konfigurowanie kolejek docelowych producentów i odbiorców
Wpis używany do definiowania miejsca docelowego w dostawcy JNDI pliku właściwości Qpid ma następujący format:

Aby utworzyć kolejkę docelową dla 
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create Producer
MessageProducer producer = session.createProducer(queue);
```

Aby utworzyć kolejkę docelową dla 
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create Consumer
MessageConsumer consumer = session.createConsumer(queue);
```

### <a name="write-the-jms-application"></a>Napisz aplikację JMS
Nie ma żadnych specjalnych interfejsów API lub opcji wymaganych podczas korzystania z JMS z usługą Service Bus. Istnieje jednak kilka ograniczeń, które zostaną omówione później. Podobnie jak w przypadku każdej aplikacji JMS, pierwszą rzeczą wymaganą jest konfiguracja środowiska JNDI, aby móc rozwiązać **ConnectionFactory** i miejsc docelowych.

#### <a name="configure-the-jndi-initialcontext"></a>Konfigurowanie kontekstu początkowego JNDI
Środowisko JNDI jest skonfigurowany przez przekazywanie skrótu informacji o konfiguracji do konstruktora javax.naming.InitialContext klasy. Dwa wymagane elementy w tabeli mieszania są nazwa klasy fabryki kontekstu początkowego i adres URL dostawcy. Poniższy kod pokazuje, jak skonfigurować środowisko JNDI do używania dostawcy JNDI opartych na właściwościach Qpid z plikiem właściwości o nazwie **servicebus.properties**.

```java
// set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + \
"?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);
``` 

### <a name="a-simple-jms-application-using-a-service-bus-queue"></a>Prosta aplikacja JMS przy użyciu kolejki usługi Service Bus
Poniższy przykładowy program wysyła wiadomości tekstowe JMS do kolejki usługi Service Bus o logicznej nazwie JNDI queue i odbiera wiadomości z powrotem.

Wszystkie informacje o kodzie źródłowym i konfiguracji można uzyskać z przewodnika [Szybki start kolejki JMS Sample usługi Azure Service Bus](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client/JmsQueueQuickstart)

```java
// Copyright (c) Microsoft. All rights reserved.
// Licensed under the MIT license. See LICENSE file in the project root for full license information.

package com.microsoft.azure.servicebus.samples.jmsqueuequickstart;

import com.microsoft.azure.servicebus.primitives.ConnectionStringBuilder;
import org.apache.commons.cli.*;
import org.apache.log4j.*;

import javax.jms.*;
import javax.naming.Context;
import javax.naming.InitialContext;
import java.util.Hashtable;
import java.util.concurrent.atomic.AtomicInteger;
import java.util.function.Function;

/**
 * This sample demonstrates how to send messages from a JMS Queue producer into
 * an Azure Service Bus Queue, and receive them with a JMS message consumer.
 * JMS Queue. 
 */
public class JmsQueueQuickstart {

    // Number of messages to send
    private static int totalSend = 10;
    //Tracking counter for how many messages have been received; used as termination condition
    private static AtomicInteger totalReceived = new AtomicInteger(0);
    // log4j logger 
    private static Logger logger = Logger.getRootLogger();

    public void run(String connectionString) throws Exception {

        // The connection string builder is the only part of the azure-servicebus SDK library
        // we use in this JMS sample and for the purpose of robustly parsing the Service Bus 
        // connection string. 
        ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);
        
        // set up JNDI context
        Hashtable<String, String> hashtable = new Hashtable<>();
        hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
        hashtable.put("queue.QUEUE", "BasicQueue");
        hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
        Context context = new InitialContext(hashtable);
        ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
        
        // Look up queue
        Destination queue = (Destination) context.lookup("QUEUE");

        // we create a scope here so we can use the same set of local variables cleanly 
        // again to show the receive side separately with minimal clutter
        {
            // Create Connection
            Connection connection = cf.createConnection(csb.getSasKeyName(), csb.getSasKey());
            // Create Session, no transaction, client ack
            Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

            // Create producer
            MessageProducer producer = session.createProducer(queue);

            // Send messages
            for (int i = 0; i < totalSend; i++) {
                BytesMessage message = session.createBytesMessage();
                message.writeBytes(String.valueOf(i).getBytes());
                producer.send(message);
                System.out.printf("Sent message %d.\n", i + 1);
            }

            producer.close();
            session.close();
            connection.stop();
            connection.close();
        }

        {
            // Create Connection
            Connection connection = cf.createConnection(csb.getSasKeyName(), csb.getSasKey());
            connection.start();
            // Create Session, no transaction, client ack
            Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
            // Create consumer
            MessageConsumer consumer = session.createConsumer(queue);
            // create a listener callback to receive the messages
            consumer.setMessageListener(message -> {
                try {
                    // receives message is passed to callback
                    System.out.printf("Received message %d with sq#: %s\n",
                            totalReceived.incrementAndGet(), // increments the tracking counter
                            message.getJMSMessageID());
                    message.acknowledge();
                } catch (Exception e) {
                    logger.error(e);
                }
            });

            // wait on the main thread until all sent messages have been received
            while (totalReceived.get() < totalSend) {
                Thread.sleep(1000);
            }
            consumer.close();
            session.close();
            connection.stop();
            connection.close();
        }

        System.out.printf("Received all messages, exiting the sample.\n");
        System.out.printf("Closing queue client.\n");
    }

    public static void main(String[] args) {

        System.exit(runApp(args, (connectionString) -> {
            JmsQueueQuickstart app = new JmsQueueQuickstart();
            try {
                app.run(connectionString);
                return 0;
            } catch (Exception e) {
                System.out.printf("%s", e.toString());
                return 1;
            }
        }));
    }

    static final String SB_SAMPLES_CONNECTIONSTRING = "SB_SAMPLES_CONNECTIONSTRING";

    public static int runApp(String[] args, Function<String, Integer> run) {
        try {

            String connectionString = null;

            // parse connection string from command line
            Options options = new Options();
            options.addOption(new Option("c", true, "Connection string"));
            CommandLineParser clp = new DefaultParser();
            CommandLine cl = clp.parse(options, args);
            if (cl.getOptionValue("c") != null) {
                connectionString = cl.getOptionValue("c");
            }

            // get overrides from the environment
            String env = System.getenv(SB_SAMPLES_CONNECTIONSTRING);
            if (env != null) {
                connectionString = env;
            }

            if (connectionString == null) {
                HelpFormatter formatter = new HelpFormatter();
                formatter.printHelp("run jar with", "", options, "", true);
                return 2;
            }
            return run.apply(connectionString);
        } catch (Exception e) {
            System.out.printf("%s", e.toString());
            return 3;
        }
    }
}
```

### <a name="run-the-application"></a>Uruchamianie aplikacji
Przekaż **ciąg połączenia** z zasad dostępu współdzielonego, aby uruchomić aplikację.
Poniżej znajduje się dane wyjściowe formularza przez uruchomienie Aplikacji:

```Output
> mvn clean package
>java -jar ./target/jmsqueuequickstart-1.0.0-jar-with-dependencies.jar -c "<CONNECTION_STRING>"

Sent message 1.
Sent message 2.
Sent message 3.
Sent message 4.
Sent message 5.
Sent message 6.
Sent message 7.
Sent message 8.
Sent message 9.
Sent message 10.
Received message 1 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-1
Received message 2 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-2
Received message 3 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-3
Received message 4 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-4
Received message 5 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-5
Received message 6 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-6
Received message 7 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-7
Received message 8 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-8
Received message 9 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-9
Received message 10 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-10
Received all messages, exiting the sample.
Closing queue client.

```

## <a name="amqp-disposition-and-service-bus-operation-mapping"></a>Mapowanie dyspozycji amqp i operacji magistrali usługowej
Oto jak dyspozycja USŁUGI AMQP przekłada się na operację usługi Service Bus:

```Output
ACCEPTED = 1; -> Complete()
REJECTED = 2; -> DeadLetter()
RELEASED = 3; (just unlock the message in service bus, will then get redelivered)
MODIFIED_FAILED = 4; -> Abandon() which increases delivery count
MODIFIED_FAILED_UNDELIVERABLE = 5; -> Defer()
```

## <a name="jms-topics-vs-service-bus-topics"></a>Tematy JMS a tematy magistrali usług
Korzystanie z tematów i subskrypcji usługi Azure Service Bus za pośrednictwem interfejsu API usługi komunikatów Java (JMS) zapewnia podstawowe możliwości wysyłania i odbierania. Jest to wygodny wybór podczas przenoszenia aplikacji od innych brokerów wiadomości za pomocą interfejsów API zgodnych z JMS, mimo że tematy usługi Service Bus różnią się od tematów JMS i wymagają kilku korekt. 

Tematy usługi Azure Service Bus kierują wiadomości do nazwanych, udostępnionych, trwałych subskrypcji zarządzanych za pośrednictwem interfejsu usługi Azure Resource Management, narzędzi wiersza polecenia platformy Azure lub za pośrednictwem witryny Azure portal. Każda subskrypcja umożliwia maksymalnie 2000 reguł wyboru, z których każda może mieć warunek filtru, a w przypadku filtrów SQL również akcję przekształcania metadanych. Każdy warunek filtru dopasowania wybiera komunikat wejściowy, który ma zostać skopiowany do subskrypcji.  

Odbieranie wiadomości z subskrypcji jest identyczne odbieranie wiadomości z kolejek. Każda subskrypcja ma skojarzoną kolejkę utraconych wiadomości i możliwość automatycznego przesyłania dalej wiadomości do innej kolejki lub tematów. 

Tematy JMS umożliwiają klientom dynamiczne tworzenie nietrwałych i trwałych subskrybentów, którzy opcjonalnie umożliwiają filtrowanie wiadomości za pomocą selektorów wiadomości. Te nieudostępnione jednostki nie są obsługiwane przez usługę Service Bus. Składnia reguły filtru SQL dla usługi Service Bus jest jednak podobna do składni selektora wiadomości obsługiwanej przez JMS. 

Strona wydawcy tematu JMS jest zgodna z usługą Service Bus, jak pokazano w tym przykładzie, ale nie są to dynamiczne subskrybenty. Następujące interfejsy API JMS związane z topologią nie są obsługiwane w usłudze Service Bus. 

## <a name="unsupported-features-and-restrictions"></a>Nieobsługiwały się funkcje i ograniczenia
Następujące ograniczenia istnieją podczas korzystania z JMS przez AMQP 1.0 z usługą Service Bus, a mianowicie:

* Tylko jeden **MessageProducer** lub **MessageConsumer** jest dozwolony na **sesję.** Jeśli musisz utworzyć wiele **MessageProducers** lub **MessageConsumers** w aplikacji, utwórz dedykowaną **sesję** dla każdego z nich.
* Subskrypcje tematów nietrwałych nie są obecnie obsługiwane.
* **MessageSelectors** nie są obecnie obsługiwane.
* Transakcje rozproszone nie są obsługiwane (ale sesje transakcyjne są obsługiwane).

Ponadto usługa Azure Service Bus dzieli płaszczyznę sterowania z płaszczyzny danych i dlatego nie obsługuje kilku funkcji dynamicznej topologii JMS:

| Metoda nieobsługiwała          | Zamień na                                                                             |
|-----------------------------|------------------------------------------------------------------------------------------|
| tworzenieDurableSubscriber     | tworzenie subskrypcji tematu przenoszenia selektora wiadomości                                 |
| tworzenieDurableConsumer       | tworzenie subskrypcji tematu przenoszenia selektora wiadomości                                 |
| tworzenieSharedConsumer        | Tematy usługi Service Bus są zawsze udostępniane, patrz wyżej                                       |
| tworzenieSharedDurableConsumer | Tematy usługi Service Bus są zawsze udostępniane, patrz wyżej                                       |
| tworzenieTemporaryTopic        | tworzenie tematu za pośrednictwem interfejsu API/narzędzia/portalu zarządzania z *autodeleteonidle* ustawiony na okres wygaśnięcia |
| tworzenieTopic                 | tworzenie tematu za pomocą interfejsu API/narzędzia/portalu zarządzania                                           |
| Zrezygnować                 | usuwanie interfejsu API/narzędzia/portalu zarządzania tematami                                             |
| tworzenieBrowser               | Nieobsługiwane. Korzystanie z funkcji Peek() interfejsu API usługi Service Bus                         |
| utwórjuka wylegij                 | tworzenie kolejki za pośrednictwem interfejsu API/narzędzia/portalu zarządzania                                           | 
| tworzenieTemporaryQueue        | tworzenie kolejki za pośrednictwem interfejsu API/narzędzia/portalu zarządzania z *autodeleteonidle* ustawiony na okres wygaśnięcia |
| receiveNoWait               | użyj metody receive() dostarczonej przez SDK usługi Service Bus i określ bardzo niski lub zerowy limit czasu |

## <a name="summary"></a>Podsumowanie
W tym przewodniku pokazano, jak korzystać z funkcji obsługi wiadomości obsługiwanych przez usługę Service Bus (kolejki i tematy publikowania/subskrybowania) z języka Java przy użyciu popularnego interfejsu API JMS i 1.0.

Usługi AmQP 1.0 można również używać z innych języków, w tym .NET, C, Python i PHP. Składniki utworzone przy użyciu tych różnych języków mogą wymieniać wiadomości niezawodnie i z pełną wiernością przy użyciu obsługi usługi AMQP 1.0 w usłudze Service Bus.

## <a name="next-steps"></a>Następne kroki
* [Obsługa usługi AMQP 1.0 w usłudze Azure Service Bus](service-bus-amqp-overview.md)
* [Jak korzystać z usługi AMQP 1.0 z interfejsem API usługi Service Bus .NET](service-bus-dotnet-advanced-message-queuing.md)
* [Przewodnik dla deweloperów usługi Service Bus AMQP 1.0](service-bus-amqp-dotnet.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Centrum deweloperów języka Java](https://azure.microsoft.com/develop/java/)

