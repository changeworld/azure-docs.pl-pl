---
title: 'Szybki Start: przesyłanie strumieniowe danych za pomocą usługi Azure Event Hubs przy użyciu protokołu Kafka'
description: 'Szybki Start: Ten artykuł zawiera informacje dotyczące przesyłania strumieniowego do usługi Azure Event Hubs przy użyciu protokołu i interfejsów API Kafka.'
services: event-hubs
author: ShubhaVijayasarathy
ms.author: shvija
ms.service: event-hubs
ms.topic: quickstart
ms.custom: seodec18
ms.date: 11/05/2019
ms.openlocfilehash: 2222345054982799f9f9e0b84961271a3cc04ddf
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73717807"
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
* [Git](https://www.git-scm.com/)
* [Przestrzeń nazw centrów Event Hubs z obsługą platformy Kafka](event-hubs-create.md)

## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Tworzenie przestrzeni nazw usługi Event Hubs z obsługą platformy Kafka

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com), a następnie kliknij pozycję **Utwórz zasób** w lewym górnym rogu ekranu.

2. Wyszukaj usługę Event Hubs i wybierz pokazane tutaj opcje:
    
    ![Wyszukiwanie usługi Event Hubs w portalu](./media/event-hubs-create-kafka-enabled/event-hubs-create-event-hubs.png)
 
3. Podaj unikatową nazwę i włącz platformę Kafka w przestrzeni nazw. Kliknij pozycję **Utwórz**. Uwaga: Event Hubs dla Kafka jest obsługiwany tylko w warstwie Standardowa i dedykowana Event Hubs. Warstwa Podstawowa usługi Event Hubs zwróci błąd autoryzacji tematu w odpowiedzi na każdą operację platformy Kafka.
    
    ![Tworzenie przestrzeni nazw](./media/event-hubs-create-kafka-enabled/create-kafka-namespace.jpg)
 
4. Po utworzeniu przestrzeni nazw na karcie **Ustawienia** kliknij pozycję **Zasady dostępu współdzielonego** w celu pobrania parametrów połączenia.

    ![Klikanie pozycji Zasady dostępu współużytkowanego](./media/event-hubs-create/create-event-hub7.png)

5. Można wybrać domyślną wartość **RootManageSharedAccessKey** lub dodać nowe zasady. Kliknij nazwę zasad, a następnie skopiuj parametry połączenia. 
    
    ![Wybieranie zasad](./media/event-hubs-create/create-event-hub8.png)
 
6. Dodaj te parametry połączenia do konfiguracji aplikacji platformy Kafka.

Teraz możesz przesyłać strumieniowo zdarzenia z aplikacji, które używają protokołu platformy Kafka, do usługi Event Hubs.

## <a name="send-and-receive-messages-with-kafka-in-event-hubs"></a>Wysyłanie i odbieranie komunikatów przy użyciu platformy Kafka w usłudze Event Hubs

1. Sklonuj [repozytorium usługi Azure Event Hubs dla platformy Kafka](https://github.com/Azure/azure-event-hubs-for-kafka).

2. Przejdź do adresu `azure-event-hubs-for-kafka/quickstart/java/producer`.

3. Aktualizuj szczegóły konfiguracji dla producenta w `src/main/resources/producer.config` w następujący sposób:

    ```xml
    bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```
    
4. Uruchom kod producenta i prześlij go strumieniowo do usługi Event Hubs z obsługą platformy Kafka:
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
    
5. Przejdź do adresu `azure-event-hubs-for-kafka/quickstart/java/consumer`.

6. Aktualizuj szczegóły konfiguracji dla konsumenta w `src/main/resources/consumer.config` w następujący sposób:
   
    ```xml
    bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```

7. Uruchom kod użytkownika i przetwórz go z poziomu usługi Event Hubs z obsługą platformy Kafka przy użyciu klientów platformy Kafka:

    ```java
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestConsumer"                                    
    ```

Jeśli klaster platformy Kafka w usłudze Event Hub ma zdarzenia, teraz powinno się rozpocząć ich odbieranie od konsumenta.

## <a name="next-steps"></a>Następne kroki
W tym artykule przedstawiono sposób przesyłania strumieniowego usługi Event Hubs z obsługą platformy Kafka bez konieczności zmiany klientów protokołu lub uruchamiania własnych klastrów. Aby dowiedzieć się więcej, przejdź do następującego samouczka:

* [Dowiedz się więcej na temat usługi Event Hubs](event-hubs-what-is-event-hubs.md)
* [Dowiedz się więcej na temat usługi Event Hubs dla platformy Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* [Poznaj więcej przykładów w usłudze Event Hubs dla platformy Kafka](https://github.com/Azure/azure-event-hubs-for-kafka)
* Używaj [Narzędzia MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) do [przesyłania strumieniowego zdarzeń z Kafka lokalnie do Kafka włączonych Event Hubs w chmurze.](event-hubs-kafka-mirror-maker-tutorial.md)
* Dowiedz się, jak przesyłać strumieniowo do usługi Event Hubs z obsługą platformy Kafka przy użyciu narzędzi [Apache Flink](event-hubs-kafka-flink-tutorial.md) lub [Akka Streams](event-hubs-kafka-akka-streams-tutorial.md)
