---
title: Przesyłanie strumieniowe do usługi Azure Event Hubs dla ekosystemu platformy Kafka | Microsoft Docs
description: Przesyłanie strumieniowe do usługi Event Hubs przy użyciu protokołu Kafka i interfejsów API.
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/03/2018
ms.author: bahariri
ms.openlocfilehash: cabbb7ed6157a6c68530ab6b5f405aa67b31a1b2
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="stream-into-event-hubs-for-kafka-ecosystem"></a>Przesyłanie strumieniowe do usługi Event Hubs dla ekosystemu platformy Kafka

> [!NOTE]
> Ten przykład jest dostępny w witrynie [GitHub](https://github.com/Azure/azure-event-hubs)

W tym przewodniku Szybki start przedstawiono sposób przesyłania strumieniowego usługi Event Hubs z obsługą platformy Kafka bez konieczności zmiany klientów protokołu lub uruchamiania własnych klastrów. Dowiesz się, jak producenci i użytkownicy mogą komunikować się z usługą Event Hubs z obsługą platformy Kafka tylko dzięki zmianie konfiguracji aplikacji. Usługa Azure Event Hubs dla ekosystemu platformy Kafka obsługuje [usługę Apache Kafka w wersji 1.0.](https://kafka.apache.org/10/documentation.html)

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start, upewnij się, że dysponujesz następującymi elementami:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* [Zestaw Java Development Kit (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Pobierz](http://maven.apache.org/download.cgi) i [zainstaluj](http://maven.apache.org/install.html) archiwum binarne Maven.
* [Git](https://www.git-scm.com/)
* [Przestrzeń nazw centrów Event Hubs z obsługą platformy Kafka](event-hubs-create.md)

## <a name="send-and-receive-messages-with-kafka-in-event-hubs"></a>Wysyłanie i odbieranie komunikatów przy użyciu platformy Kafka w usłudze Event Hubs

1. Sklonuj [repozytorium usługi Azure Event Hubs](https://github.com/Azure/azure-event-hubs).

2. Przejdź do adresu `azure-event-hubs/samples/kafka/quickstart/producer`.

3. Zaktualizuj szczegóły konfiguracji dla producenta w pliku src/main/resources/producer.config, tak jak pokazano poniżej.

    ```xml
    bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```
4. Uruchom kod producenta i prześlij go strumieniowo do usługi Event Hubs z obsługą platformy Kafka.
   
    ```java
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
5. Przejdź do lokalizacji azure-event-hubs/samples/kafka/quickstart/consumer.

6. Zaktualizuj szczegóły konfiguracji dla użytkownika w pliku src/main/resources/consumer.config, tak jak pokazano poniżej.
   
    ```xml
    bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```

7. Uruchom kod użytkownika i przetwórz go z poziomu usługi Event Hubs z obsługą platformy Kafka przy użyciu klientów platformy Kafka.

    ```java
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestConsumer"                                    
    ```

Jeśli klaster platformy Kafka w usłudze Event Hub ma zdarzenia producenta znajdujące się w kolejce, teraz powinno rozpocząć się odbieranie ich od użytkownika.

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej na temat usługi Event Hubs dla ekosystemu platformy Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* [Dowiedz się więcej na temat usługi Event Hubs](event-hubs-what-is-event-hubs.md)
* Użyj narzędzia [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330), aby przesyłać zdarzenia strumieniowo z lokalnego środowiska platformy Kafka do usługi Event Hubs z obsługą platformy Kafka w chmurze.](event-hubs-kafka-mirror-maker-tutorial.md)
