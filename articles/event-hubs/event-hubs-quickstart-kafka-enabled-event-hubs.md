---
title: Przesyłanie strumieniowe do usługi Azure Event Hubs dla platformy Azure Kafka | Microsoft Docs
description: Przesyłanie strumieniowe do usługi Event Hubs przy użyciu protokołu Kafka i interfejsów API.
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2018
ms.author: bahariri
ms.openlocfilehash: ec6061cac7188f3f94fa1ec0bf138b9398387099
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39413624"
---
# <a name="stream-into-event-hubs-for-the-apache-kafka"></a>Przesyłanie strumieniowe do usługi Event Hubs dla platformy Apache Kafka
W tym przewodniku Szybki start przedstawiono sposób przesyłania strumieniowego usługi Event Hubs z obsługą platformy Kafka bez konieczności zmiany klientów protokołu lub uruchamiania własnych klastrów. Dowiesz się, jak producenci i użytkownicy mogą komunikować się z usługą Event Hubs z obsługą platformy Kafka tylko dzięki zmianie konfiguracji aplikacji. Usługa Azure Event Hubs obsługuje [platformę Apache Kafka w wersji 1.0.](https://kafka.apache.org/10/documentation.html)

> [!NOTE]
> Ten przykład jest dostępny w witrynie [GitHub](https://github.com/Azure/azure-event-hubs)

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start, upewnij się, że dysponujesz następującymi elementami:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* [Zestaw Java Development Kit (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Pobierz](http://maven.apache.org/download.cgi) i [zainstaluj](http://maven.apache.org/install.html) archiwum binarne Maven.
* [Usługa Git](https://www.git-scm.com/)
* [Przestrzeń nazw centrów Event Hubs z obsługą platformy Kafka](event-hubs-create.md)

## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Tworzenie przestrzeni nazw usługi Event Hubs z obsługą platformy Kafka

1. Zaloguj się do witryny [Azure Portal][Azure Portal], a następnie kliknij pozycję **Utwórz zasób** w lewym górnym rogu ekranu.

2. Wyszukaj usługę Event Hubs i wybierz pokazane tutaj opcje:
    
    ![Wyszukiwanie usługi Event Hubs w portalu](./media/event-hubs-create-kafka-enabled/event-hubs-create-event-hubs.png)
 
3. Podaj unikatową nazwę i włącz platformę Kafka w przestrzeni nazw. Kliknij pozycję **Utwórz**.
    
    ![Tworzenie przestrzeni nazw](./media/event-hubs-create-kafka-enabled/create-kafka-namespace.png)
 
4. Po utworzeniu przestrzeni nazw na karcie **Ustawienia** kliknij pozycję **Zasady dostępu współdzielonego** w celu pobrania parametrów połączenia.

    ![Klikanie pozycji Zasady dostępu współużytkowanego](./media/event-hubs-create/create-event-hub7.png)

5. Można wybrać domyślną wartość **RootManageSharedAccessKey** lub dodać nowe zasady. Kliknij nazwę zasad, a następnie skopiuj parametry połączenia. 
    
    ![Wybieranie zasad](./media/event-hubs-create/create-event-hub8.png)
 
6. Dodaj te parametry połączenia do konfiguracji aplikacji platformy Kafka.

Teraz możesz przesyłać strumieniowo zdarzenia z aplikacji, które używają protokołu platformy Kafka, do usługi Event Hubs.

## <a name="send-and-receive-messages-with-kafka-in-event-hubs"></a>Wysyłanie i odbieranie komunikatów przy użyciu platformy Kafka w usłudze Event Hubs

1. Sklonuj [repozytorium usługi Azure Event Hubs](https://github.com/Azure/azure-event-hubs).

2. Przejdź do adresu `azure-event-hubs/samples/kafka/quickstart/producer`.

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
5. Przejdź do adresu `azure-event-hubs/samples/kafka/quickstart/consumer`.

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

> [!div class="nextstepaction"]
> [Używanie narzędzia Apache Kafka MirrorMaker z usługą Event Hubs](event-hubs-kafka-mirror-maker-tutorial.md)
