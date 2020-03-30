---
title: 'Szybki start: przesyłanie strumieniowe danych za pomocą usługi Azure Event Hubs przy użyciu protokołu Platformy Kafka'
description: 'Szybki start: Ten artykuł zawiera informacje dotyczące sposobu przesyłania strumieniowego do usługi Azure Event Hubs przy użyciu protokołu Platformy Kafka i interfejsów API.'
services: event-hubs
author: ShubhaVijayasarathy
ms.author: shvija
ms.service: event-hubs
ms.topic: quickstart
ms.custom: seodec18
ms.date: 02/12/2020
ms.openlocfilehash: 4a0d7d30ea02af222ab4a758c18b46d7488e1a8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280715"
---
# <a name="quickstart-data-streaming-with-event-hubs-using-the-kafka-protocol"></a>Szybki start: przesyłanie strumieniowe danych za pomocą centrów zdarzeń przy użyciu protokołu Platformy Kafka
Ten przewodnik Szybki start pokazuje, jak przesyłać strumieniowo do centrów zdarzeń bez zmiany klientów protokołu lub uruchamiania własnych klastrów. Dowiesz się, jak używać producentów i konsumentów do rozmów z centrum zdarzeń tylko ze zmianą konfiguracji w aplikacjach. Usługa Azure Event Hubs obsługuje [platformę Apache Kafka w wersji 1.0.](https://kafka.apache.org/10/documentation.html)

> [!NOTE]
> Ten przykład jest dostępny w witrynie [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/java)

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start, upewnij się, że dysponujesz następującymi elementami:

* Zapoznaj się z artykułem [Usługa Event Hubs dla platformy Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md).
* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* [Zestaw Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks)
* [Pobierz](https://maven.apache.org/download.cgi) i [zainstaluj](https://maven.apache.org/install.html) archiwum binarne Maven.
* [Git](https://www.git-scm.com/)


## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Tworzenie przestrzeni nazw usługi Event Hubs z obsługą platformy Kafka
Podczas tworzenia obszaru nazw centrum zdarzeń warstwy **standardowej** punkt końcowy platformy Kafka dla obszaru nazw jest automatycznie włączony. Można przesyłać strumieniowo zdarzenia z aplikacji korzystających z protokołu Kafka do centrum zdarzeń warstwy standardowej. Postępuj zgodnie z instrukcjami krok po kroku w [Centrum tworzenia zdarzeń przy użyciu witryny Azure Portal,](event-hubs-create.md) aby utworzyć obszar nazw centrum zdarzeń warstwy **standardowej.** 

> [!NOTE]
> Centra zdarzeń dla platformy Kafka są dostępne tylko w **warstwach standardowych** i **dedykowanych.** Warstwa **podstawowa** nie obsługuje platformy Kafka w centrach zdarzeń.

## <a name="send-and-receive-messages-with-kafka-in-event-hubs"></a>Wysyłanie i odbieranie komunikatów przy użyciu platformy Kafka w usłudze Event Hubs

1. Sklonuj [repozytorium usługi Azure Event Hubs dla platformy Kafka](https://github.com/Azure/azure-event-hubs-for-kafka).

2. Przejdź do adresu `azure-event-hubs-for-kafka/quickstart/java/producer`.

3. Aktualizuj szczegóły konfiguracji dla producenta w `src/main/resources/producer.config` w następujący sposób:

    **Ssl:**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```
    **Oauth:**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=OAUTHBEARER
    sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
    sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
    ```    

    Kod źródłowy dla klasy obsługi przykładowej CustomAuthenticateCallbackHandler można znaleźć w serwisie GitHub [tutaj.](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/appsecret/producer/src/main/java)
4. Uruchom kod producenta i strumieniuj zdarzenia do Centrów zdarzeń:
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
    
5. Przejdź do adresu `azure-event-hubs-for-kafka/quickstart/java/consumer`.

6. Aktualizuj szczegóły konfiguracji dla konsumenta w `src/main/resources/consumer.config` w następujący sposób:
   
    **Ssl:**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```

    **Oauth:**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=OAUTHBEARER
    sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
    sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
    ``` 

    Kod źródłowy dla klasy obsługi przykładowej CustomAuthenticateCallbackHandler można znaleźć w serwisie GitHub [tutaj.](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/appsecret/consumer/src/main/java)

    Tutaj można znaleźć wszystkie przykłady OAuth dla centrów zdarzeń dla platformy [Kafka.](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth)
7. Uruchom kod użytkownika i przetwórz go z poziomu usługi Event Hubs z obsługą platformy Kafka przy użyciu klientów platformy Kafka:

    ```java
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestConsumer"                                    
    ```

Jeśli klaster platformy Kafka w usłudze Event Hub ma zdarzenia, teraz powinno się rozpocząć ich odbieranie od konsumenta.

## <a name="next-steps"></a>Następne kroki
W tym artykule dowiesz się, jak przesyłać strumieniowo do centrów zdarzeń bez zmiany klientów protokołu lub uruchamiania własnych klastrów. Aby dowiedzieć się więcej, zobacz następujące artykuły i przykłady:

- [Dowiedz się więcej na temat usługi Event Hubs dla platformy Kafka](event-hubs-for-kafka-ecosystem-overview.md)
- [Przewodniki Szybki start dla centrów zdarzeń dla platformy Kafka w usłudze GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart)
- [Samouczki dotyczące centrów zdarzeń dla platformy Kafka w usłudze GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials)
- Użyj [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) do [przesyłania strumieniowego zdarzeń z platformy Kafka lokalnie do platformy Kafka włączone usługi Event Hubs w chmurze.](event-hubs-kafka-mirror-maker-tutorial.md)
- Dowiedz się, jak przesyłać strumieniowo do usługi Event Hubs z obsługą platformy Kafka przy użyciu narzędzi [Apache Flink](event-hubs-kafka-flink-tutorial.md) lub [Akka Streams](event-hubs-kafka-akka-streams-tutorial.md)
