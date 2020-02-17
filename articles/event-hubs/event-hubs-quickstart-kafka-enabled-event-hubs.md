---
title: 'Szybki Start: przesyłanie strumieniowe danych za pomocą usługi Azure Event Hubs przy użyciu protokołu Kafka'
description: 'Szybki Start: Ten artykuł zawiera informacje dotyczące przesyłania strumieniowego do usługi Azure Event Hubs przy użyciu protokołu i interfejsów API Kafka.'
services: event-hubs
author: ShubhaVijayasarathy
ms.author: shvija
ms.service: event-hubs
ms.topic: quickstart
ms.custom: seodec18
ms.date: 02/12/2020
ms.openlocfilehash: 25c1cf00a418767209467c973b7a4755f62eb16f
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368381"
---
# <a name="quickstart-data-streaming-with-event-hubs-using-the-kafka-protocol"></a>Szybki Start: przesyłanie strumieniowe danych z Event Hubs przy użyciu protokołu Kafka
W tym przewodniku Szybki start przedstawiono sposób przesyłania strumieniowego usługi Event Hubs z obsługą platformy Kafka bez konieczności zmiany klientów protokołu lub uruchamiania własnych klastrów. Dowiesz się, jak producenci i użytkownicy mogą komunikować się z usługą Event Hubs z obsługą platformy Kafka tylko dzięki zmianie konfiguracji aplikacji. Usługa Azure Event Hubs obsługuje [platformę Apache Kafka w wersji 1.0.](https://kafka.apache.org/10/documentation.html)

> [!NOTE]
> Ten przykład jest dostępny w witrynie [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/java)

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start, upewnij się, że dysponujesz następującymi elementami:

* Zapoznaj się z artykułem [Usługa Event Hubs dla platformy Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md).
* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* [Zestaw Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks)
* [Pobierz](https://maven.apache.org/download.cgi) i [zainstaluj](https://maven.apache.org/install.html) archiwum binarne Maven.
* [Usługa Git](https://www.git-scm.com/)
* [Przestrzeń nazw centrów Event Hubs z obsługą platformy Kafka](event-hubs-create.md)

## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Tworzenie przestrzeni nazw usługi Event Hubs z obsługą platformy Kafka
Podczas tworzenia warstwy Standardowa Event Hubs przestrzeń nazw, punkt końcowy Kafka dla przestrzeni nazw jest automatycznie włączany. Zdarzenia z aplikacji korzystających z protokołu Kafka można przesyłać strumieniowo do warstwy Standardowa Event Hubs. Ta funkcja nie jest włączona dla obszaru nazw Event Hubs warstwy Podstawowa. 

## <a name="send-and-receive-messages-with-kafka-in-event-hubs"></a>Wysyłanie i odbieranie komunikatów przy użyciu platformy Kafka w usłudze Event Hubs

1. Sklonuj [repozytorium usługi Azure Event Hubs dla platformy Kafka](https://github.com/Azure/azure-event-hubs-for-kafka).

2. Przejdź do adresu `azure-event-hubs-for-kafka/quickstart/java/producer`.

3. Aktualizuj szczegóły konfiguracji dla producenta w `src/main/resources/producer.config` w następujący sposób:

    **ZASTOSOWANIA**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```
    **OAuth**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=OAUTHBEARER
    sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
    sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
    ```    

    Kod źródłowy dla przykładowej klasy obsługi CustomAuthenticateCallbackHandler w witrynie GitHub można znaleźć [tutaj](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/appsecret/producer/src/main/java).
4. Uruchom kod producenta i zdarzenia strumienia w Event Hubs z włączoną obsługą Kafka:
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
    
5. Przejdź do adresu `azure-event-hubs-for-kafka/quickstart/java/consumer`.

6. Aktualizuj szczegóły konfiguracji dla konsumenta w `src/main/resources/consumer.config` w następujący sposób:
   
    **ZASTOSOWANIA**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```

    **OAuth**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=OAUTHBEARER
    sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
    sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
    ``` 

    Kod źródłowy dla przykładowej klasy obsługi CustomAuthenticateCallbackHandler w witrynie GitHub można znaleźć [tutaj](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/appsecret/consumer/src/main/java).

    Wszystkie przykłady OAuth dla Event Hubs Kafka można znaleźć [tutaj](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth).
7. Uruchom kod użytkownika i przetwórz go z poziomu usługi Event Hubs z obsługą platformy Kafka przy użyciu klientów platformy Kafka:

    ```java
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestConsumer"                                    
    ```

Jeśli klaster platformy Kafka w usłudze Event Hub ma zdarzenia, teraz powinno się rozpocząć ich odbieranie od konsumenta.

## <a name="next-steps"></a>Następne kroki
W tym artykule przedstawiono sposób przesyłania strumieniowego usługi Event Hubs z obsługą platformy Kafka bez konieczności zmiany klientów protokołu lub uruchamiania własnych klastrów. Aby dowiedzieć się więcej, zobacz następujące artykuły i przykłady:

- [Dowiedz się więcej na temat usługi Event Hubs dla platformy Kafka](event-hubs-for-kafka-ecosystem-overview.md)
- [Przewodniki Szybki Start dla Event Hubs Kafka w witrynie GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart)
- [Samouczki dotyczące Event Hubs Kafka w witrynie GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials)
- Używaj [Narzędzia MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) do [przesyłania strumieniowego zdarzeń z Kafka lokalnie do Kafka włączonych Event Hubs w chmurze.](event-hubs-kafka-mirror-maker-tutorial.md)
- Dowiedz się, jak przesyłać strumieniowo do usługi Event Hubs z obsługą platformy Kafka przy użyciu narzędzi [Apache Flink](event-hubs-kafka-flink-tutorial.md) lub [Akka Streams](event-hubs-kafka-akka-streams-tutorial.md)
