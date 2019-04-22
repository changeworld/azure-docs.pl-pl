---
title: Sposób korzystania z protokołu AMQP 1.0 przy użyciu interfejsu API języka Java JMS usługi Service Bus | Dokumentacja firmy Microsoft
description: Jak używać usługi wiadomości Java (JMS) za pomocą usługi Azure Service Bus i zaawansowane komunikat Queuing Protocol (AMQP) 1.0.
services: service-bus-messaging
documentationcenter: java
author: axisc
manager: timlt
editor: spelluru
ms.assetid: be766f42-6fd1-410c-b275-8c400c811519
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 03/05/2019
ms.author: aschhab
ms.openlocfilehash: a7e4282a176794fe885049173ba56ce2461cd6fa
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58885557"
---
# <a name="how-to-use-the-java-message-service-jms-api-with-service-bus-and-amqp-10"></a>Jak używać języka Java wiadomości usługi (JMS) interfejsu API za pomocą usługi Service Bus i protokołu AMQP 1.0
Zaawansowane komunikat (QUEUING Protocol) 1.0 to wydajny, niezawodny i protokół sieciowy niskiego poziomu obsługi komunikatów protokołu używanego do tworzenia niezawodnych, międzyplatformowych aplikacji do obsługi wiadomości.

Obsługa dla protokołu AMQP 1.0 w usłudze Service Bus oznacza, że można użyć usługi kolejkowania i publikowania/subskrybowania funkcji komunikatów obsługiwanych przez brokera z szeroką gamę platform za pomocą protokołu binarne wydajne. Ponadto możesz tworzyć aplikacje, które obejmuje składniki utworzone przy użyciu różnych języków, platform i systemów operacyjnych.

W tym artykule wyjaśniono, jak używać funkcji (kolejek i tematów publikowania/subskrybowania) komunikatów usługi Service Bus z aplikacji Java przy użyciu popularnych Java wiadomości usługi (JMS) standardowego interfejsu API. Brak [towarzyszący artykuł](service-bus-amqp-dotnet.md) , wyjaśnia, jak wykonywać te same przy użyciu interfejsu API .NET usługi Service Bus. Te dwa przewodniki można użyć ze sobą, aby dowiedzieć się więcej na temat dla wielu platform, obsługi komunikatów za pomocą protokołu AMQP 1.0.

## <a name="get-started-with-service-bus"></a>Rozpoczynanie pracy z usługą Service Bus
W tym przewodniku założono, że masz już przestrzeń nazw usługi Service Bus zawierający kolejkę o nazwie **basicqueue**. Jeśli tego nie zrobisz, a następnie możesz [tworzenie przestrzeni nazw i kolejki](service-bus-create-namespace-portal.md) przy użyciu [witryny Azure portal](https://portal.azure.com). Aby uzyskać więcej informacji na temat tworzenia przestrzeni nazw usługi Service Bus i kolejki, zobacz [Rozpoczynanie pracy z kolejkami usługi Service Bus](service-bus-dotnet-get-started-with-queues.md).

> [!NOTE]
> Partycjonowane kolejki i tematy obsługują także protokół AMQP. Aby uzyskać więcej informacji, zobacz [partycjonowane jednostki do obsługi komunikatów](service-bus-partitioning.md) i [obsługę protokołu AMQP 1.0 dla usługi Service Bus partycjonowane kolejki i tematy](service-bus-partitioned-queues-and-topics-amqp-overview.md).
> 
> 

## <a name="downloading-the-amqp-10-jms-client-library"></a>Pobieranie biblioteki klienta protokołu AMQP 1.0 JMS
Aby uzyskać informacje o tym, gdzie można pobrać najnowszej wersji biblioteki klienta usługi Apache Qpid JMS protokołu AMQP 1.0, odwiedź stronę [ https://qpid.apache.org/download.html ](https://qpid.apache.org/download.html).

Należy dodać następujące cztery pliki JAR z archiwum Apache Qpid JMS protokołu AMQP 1.0 w dystrybucji do klasy Java podczas kompilowania i uruchamiania aplikacji JMS z usługą Service Bus:

* geronimo-jms\_1.1\_spec-1.0.jar
* qpid-jms-client-[version].jar

> [!NOTE]
> JMS JAR nazwy i wersji mógł ulec zmianie. Aby uzyskać więcej informacji, zobacz [JMS Qpid - protokołu AMQP 1.0](https://qpid.apache.org/maven.html#qpid-jms-amqp-10).

## <a name="coding-java-applications"></a>Kodowanie aplikacji w języku Java
### <a name="java-naming-and-directory-interface-jndi"></a>Nazewnictwa języka Java i interfejsu katalogu (JNDI)
JMS używa nazewnictwa języka Java i interfejsu katalogu (JNDI) w celu utworzenia rozdzielenie nazwy logicznej lub fizycznej. Dwa typy obiektów JMS są rozwiązywane za pomocą JNDI: ConnectionFactory i docelowym. JNDI korzysta z modelu dostawcy, do którego można dodać inny katalog usług do obsługi określonych obowiązków rozpoznawania nazw. Formatowanie opartych na plikach JNDI dostawcy jest skonfigurowany, używając właściwości pliku następujące Apache Qpid JMS protokołu AMQP 1.0 biblioteki, który jest dostarczany z prostych właściwości:

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

#### <a name="setup-jndi-context-and-configure-the-connectionfactory"></a>Ustaw kontekst JNDI i skonfiguruj ConnectionFactory

**ConnectionString** do którego odwołuje się dostępne w "Udostępnione zasady dostępu" w [witryny Azure Portal](https://portal.azure.com) w obszarze **podstawowe parametry połączenia**
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

#### <a name="configure-producer-and-consumer-destination-queues"></a>Konfigurowanie producenta i odbiorcy docelowego kolejek
Wpis służącą do definiowania miejsca docelowego w dostawcy JNDI Qpid właściwości plików ma następujący format:

Tworzenie kolejki docelowej dla producentów- 
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create Producer
MessageProducer producer = session.createProducer(queue);
```

Tworzenie kolejki docelowej dla klientów — 
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create Consumer
MessageConsumer consumer = session.createConsumer(queue);
```

### <a name="write-the-jms-application"></a>Napisanie aplikacji JMS
Brak specjalnego interfejsów API i opcje wymagany w przypadku korzystania z JMS z usługą Service Bus. Istnieje jednak kilka ograniczeń, które zostały omówione później. Z każdą aplikacją JMS po pierwsze wymagana jest Konfiguracja środowiska JNDI, aby mieć możliwość rozpoznania **ConnectionFactory** i miejsc docelowych.

#### <a name="configure-the-jndi-initialcontext"></a>Konfigurowanie JNDI InitialContext
Środowisko JNDI jest skonfigurowane, przekazując tablicę skrótów informacji o konfiguracji w konstruktorze klasy javax.naming.InitialContext. Dwa wymagane elementy w tablicy skrótów są nazwa klasy początkowej fabryce kontekst i adres URL dostawcy. Poniższy kod przedstawia sposób konfigurowania środowiska JNDI, użyj Qpid dostawcy JNDI opartą na plikach właściwości z plikiem właściwości o nazwie **servicebus.properties**.

```java
// set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + \
"?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);
``` 

### <a name="a-simple-jms-application-using-a-service-bus-queue"></a>Prostą aplikację JMS, korzystając z kolejki usługi Service Bus
Następujący program przykład wysyła JMS TextMessages do kolejki usługi Service Bus przy użyciu nazwy logicznej JNDI kolejki i odbiera komunikaty, w ponownie.

Wszystkie dostępne wszystkie źródła kodu i konfiguracji informacje z [Azure usługi Service Bus przykłady JMS kolejki — Szybki Start](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client/JmsQueueQuickstart)

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
Przekaż **parametry połączenia** z zasad dostępu udostępnionego, aby uruchomić aplikację.
Poniżej znajdują się dane wyjściowe w postaci, uruchamiając aplikację:

```
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

## <a name="amqp-disposition-and-service-bus-operation-mapping"></a>Protokół AMQP dyspozycji i mapowanie operacji usługi Service Bus
Poniżej przedstawiono, jak dyspozycji AMQP przekłada się na operację usługi Service Bus:

```
ACCEPTED = 1; -> Complete()
REJECTED = 2; -> DeadLetter()
RELEASED = 3; (just unlock the message in service bus, will then get redelivered)
MODIFIED_FAILED = 4; -> Abandon() which increases delivery count
MODIFIED_FAILED_UNDELIVERABLE = 5; -> Defer()
```

## <a name="jms-topics-vs-service-bus-topics"></a>Tematy JMS programu vs. Tematy dotyczące usługi Service Bus
Za pomocą usługi Azure Service Bus tematów i subskrypcji za pomocą języka Java wiadomości usługi (JMS) interfejsu API zapewnia podstawowe wysyłania i odbierania możliwości. Podczas przenoszenia aplikacji z innymi brokerami przy użyciu zgodnych interfejsów API JMS, mimo że tematów usługi Service Bus różnią się od tematów JMS i wymagają kilka zmian, to wygodne wybór. 

Usługa Azure tematów usługi Service Bus kierowanie komunikatów w postaci do nazwanych, udostępniony i trwałe subskrypcje, które są zarządzane za pomocą interfejsu zarządzania zasobami platformy Azure, narzędzia wiersza polecenia platformy Azure lub za pośrednictwem witryny Azure portal. Każda subskrypcja umożliwia dla maksymalnie 2000 reguł zaznaczenie wszystkich które mogą mieć warunek filtru, a filtry SQL, również działań przekształcania metadanych. Każdego dopasowania warunek filtru wybiera komunikat wejściowy ma zostać skopiowana do tehj subskrypcji.  

Odbieranie komunikatów z subskrypcji jest taka sama, otrzymywanie komunikatów z kolejki. Każda subskrypcja ma skojarzona kolejka utraconych wiadomości, a także możliwość automatycznego przekazywania komunikatów do innej kolejki lub tematy. 

Tematy JMS Zezwalaj klientom na dynamiczne tworzenie subskrybentów nietrwałymi i trwałe, opcjonalnie umożliwiające filtrowanie komunikatów z selektory wiadomości. Te jednostki nieudostępnionych nie są obsługiwane przez usługę Service Bus. Składnia reguły filtru SQL dla usługi Service Bus jest bardzo podobne do składni selektor komunikatów obsługiwanych przez JMS. 

Po stronie wydawcy JMS tematu jest zgodny z usługi Service Bus, jak pokazano w tym przykładzie, ale subskrybenci dynamiczne nie są. Następujące związane z topologii JMS interfejsy API nie są obsługiwane z usługą Service Bus. 

## <a name="unsupported-features-and-restrictions"></a>Nieobsługiwane funkcje i ograniczenia
Korzystając z JMS za pośrednictwem protokołu AMQP 1.0 przy użyciu usługi Service Bus, a mianowicie obowiązują następujące ograniczenia:

* Tylko jeden **MessageProducer** lub **MessageConsumer** jest dozwolone **sesji**. Jeśli musisz utworzyć wiele **MessageProducers** lub **MessageConsumers** w aplikacji, Utwórz dedykowane **sesji** dla każdego z nich.
* Subskrypcje tematu volatile nie są obecnie obsługiwane.
* **MessageSelectors** nie są obecnie obsługiwane.
* Sesje transakcyjne i transakcje rozproszone nie są obsługiwane.

Ponadto usługi Azure Service Bus dzieli się na płaszczyźnie kontroli od płaszczyzny danych i dlatego nie obsługuje kilka funkcji dynamicznej topologii JMS firmy:

| Nieobsługiwanej metody          | Zamień na                                                                             |
|-----------------------------|------------------------------------------------------------------------------------------|
| createDurableSubscriber     | Utwórz subskrypcję tematu przenoszenie selektor wiadomości                                 |
| createDurableConsumer       | Utwórz subskrypcję tematu przenoszenie selektor wiadomości                                 |
| createSharedConsumer        | Tematy usługi Service Bus są zawsze możliwe do udostępnienia, powyżej                                       |
| createSharedDurableConsumer | Tematy usługi Service Bus są zawsze możliwe do udostępnienia, powyżej                                       |
| createTemporaryTopic        | Utwórz temat za pośrednictwem funkcji zarządzania interfejsu API/tools/portal *AutoDeleteOnIdle* Ustaw okres ważności |
| createTopic                 | Tworzenie tematu przy użyciu interfejsu API/tools/portal zarządzania                                           |
| Anulowanie subskrypcji                 | Usuwanie tematu management portal/tools/interfejsów API                                             |
| createBrowser               | Nieobsługiwane. Użyj funkcji Peek() API usługi Service Bus                         |
| createQueue                 | Tworzenie kolejki za pomocą interfejsu API/tools/portal zarządzania                                           | 
| createTemporaryQueue        | Tworzenie kolejki za pośrednictwem funkcji zarządzania interfejsu API/tools/portal *AutoDeleteOnIdle* Ustaw okres ważności |

## <a name="summary"></a>Podsumowanie
W tym przewodniku pokazano, jak używać funkcji usługi Service Bus obsługiwanych przez brokera obsługi komunikatów (kolejek i tematów publikowania/subskrybowania) w języku Java przy użyciu popularnych interfejsów API JMS i protokołu AMQP 1.0.

Można również użyć usługi Service Bus protokołu AMQP 1.0 w innych językach, takich jak .NET, C, Python i PHP. Składniki utworzone za pomocą tych różnych językach mogą wymiana komunikatów w niezawodny sposób będą i w pełnej rozdzielczości, przy użyciu protokołu AMQP 1.0 obsługi w usłudze Service Bus.

## <a name="next-steps"></a>Kolejne kroki
* [Obsługa protokołu AMQP 1.0 w usłudze Azure Service Bus](service-bus-amqp-overview.md)
* [Sposób korzystania z protokołu AMQP 1.0 przy użyciu interfejsu API .NET usługi Service Bus](service-bus-dotnet-advanced-message-queuing.md)
* [Service Bus protokołu AMQP 1.0 przewodnik dewelopera](service-bus-amqp-dotnet.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Centrum deweloperów języka Java](https://azure.microsoft.com/develop/java/)

