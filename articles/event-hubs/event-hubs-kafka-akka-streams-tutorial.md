---
title: Stosowanie strumieni Akka za pomocą usługi Azure Event Hubs dla platformy Apache Kafka | Dokumentacja firmy Microsoft
description: Nawiązywanie Akka strumieni platformy Kafka włączone Centrum zdarzeń
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: mvc
ms.date: 08/06/2018
ms.author: bahariri
ms.openlocfilehash: 9cfaec69d3c9cea7f6f3860e8f07df6dc1638a9a
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/31/2018
ms.locfileid: "50416073"
---
# <a name="using-akka-streams-with-event-hubs-for-apache-kafka"></a>Stosowanie strumieni Akka z usługą Event Hubs dla platformy Apache Kafka
W tym samouczku przedstawiono sposób łączenia strumieni Akka do centrów zdarzeń z obsługą platformy Kafka bez zmieniania klientów protokołu lub działające własne klastry. Usługa Azure Event Hubs dla platformy Kafka obsługuje [platformy Apache Kafka w wersji 1.0.](https://kafka.apache.org/10/documentation.html)

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie przestrzeni nazw usługi Event Hubs
> * Sklonuj projekt przykładowy
> * Uruchom producentów Akka strumieni 
> * Uruchom konsumenta Akka strumieni

> [!NOTE]
> Ten przykład jest dostępny w witrynie [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/akka/java)

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka, upewnij się, że masz następujące wymagania wstępne:

* Zapoznaj się z artykułem [Usługa Event Hubs dla platformy Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md). 
* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* [Zestaw Java Development Kit (JDK 1.8 +)](https://aka.ms/azure-jdks)
    * W systemie Ubuntu uruchom polecenie `apt-get install default-jdk`, aby zainstalować zestaw JDK.
    * Upewnij się, że zmienna środowiskowa JAVA_HOME wskazuje folder, w którym zainstalowano zestaw JDK.
* [Pobierz](http://maven.apache.org/download.cgi) i [zainstalować](http://maven.apache.org/install.html) archiwum binarne Maven
    * W systemie Ubuntu możesz uruchomić polecenie `apt-get install maven`, aby zainstalować narzędzie Maven.
* [Usługa Git](https://www.git-scm.com/downloads)
    * W systemie Ubuntu możesz uruchomić polecenie `sudo apt-get install git`, aby zainstalować usługę Git.

## <a name="create-an-event-hubs-namespace"></a>Tworzenie przestrzeni nazw usługi Event Hubs

Przestrzeń nazw usługi Event Hubs jest wymagana do wysyłania i odbierania z dowolnej usługi Event Hubs. Zobacz [tworzenie platformy Kafka włączone usługi Event Hubs](event-hubs-create-kafka-enabled.md) informacji o pobieraniu punktu końcowego usługi Event Hubs Kafka. Upewnij się skopiować parametry połączenia usługi Event Hubs w celu późniejszego użycia.

## <a name="clone-the-example-project"></a>Sklonuj projekt przykładowy

Teraz, gdy masz parametry połączenia z obsługą platformy Kafka z usługi Event Hubs, Azure Event Hubs dla platformy Kafka repozytorium klonowanie i przejdź do `akka` podfolder:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/akka/java
```

## <a name="run-akka-streams-producer"></a>Uruchom producentów Akka strumieni

Korzystając z podanego przykładu producenta Akka strumieni, wysyłają komunikaty do usługi Event Hubs.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Podaj punkt końcowy platformy Kafka z centrów zdarzeń

#### <a name="producer-applicationconf"></a>Producent application.conf

Aktualizacja `bootstrap.servers` i `sasl.jaas.config` wartości w `producer/src/main/resources/application.conf` do kierowania producenta do punktu końcowego platformy Kafka z centrów zdarzeń przy użyciu poprawnego uwierzytelnienia.

```xml
akka.kafka.producer {
    #Akka Kafka producer properties can be defined here


    # Properties defined by org.apache.kafka.clients.producer.ProducerConfig
    # can be defined in this configuration section.
    kafka-clients {
        bootstrap.servers="{YOUR.EVENTHUBS.FQDN}:9093"
        sasl.mechanism=PLAIN
        security.protocol=SASL_SSL
        sasl.jaas.config="org.apache.kafka.common.security.plain.PlainLoginModule required username=\"$ConnectionString\" password=\"{YOUR.EVENTHUBS.CONNECTION.STRING}\";"
    }
}
```

### <a name="run-producer-from-the-command-line"></a>Uruchom producentów z wiersza polecenia

Aby uruchomić producenta z wiersza polecenia, Generuj plik JAR, a następnie uruchamiać z poziomu narzędzia Maven (lub wygenerować plik JAR przy użyciu narzędzia Maven, następnie uruchomić w środowisku Java przez dodanie niezbędnych JAR(s) Kafka do ścieżki):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestProducer"
```

Producent rozpoczyna wysyłanie zdarzeń do Centrum zdarzeń z obsługą platformy Kafka w temacie `test`i wyświetla zdarzenia do strumienia wyjściowego stdout.

## <a name="run-akka-streams-consumer"></a>Uruchom konsumenta Akka strumieni

Korzystając z przykładu podanych odbiorców, odbiera komunikaty z Centrum zdarzeń z obsługą platformy Kafka.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Podaj punkt końcowy platformy Kafka z centrów zdarzeń

#### <a name="consumer-applicationconf"></a>Application.conf konsumenta

Aktualizacja `bootstrap.servers` i `sasl.jaas.config` wartości w `consumer/src/main/resources/application.conf` kierować klientów do punktu końcowego platformy Kafka z centrów zdarzeń przy użyciu poprawnego uwierzytelnienia.

```xml
akka.kafka.consumer {
    #Akka Kafka consumer properties defined here
    wakeup-timeout=60s

    # Properties defined by org.apache.kafka.clients.consumer.ConsumerConfig
    # defined in this configuration section.
    kafka-clients {
       request.timeout.ms=60000
       group.id=akka-example-consumer

       bootstrap.servers="{YOUR.EVENTHUBS.FQDN}:9093"
       sasl.mechanism=PLAIN
       security.protocol=SASL_SSL
       sasl.jaas.config="org.apache.kafka.common.security.plain.PlainLoginModule required username=\"$ConnectionString\" password=\"{YOUR.EVENTHUBS.CONNECTION.STRING}\";"
    }
}
```

### <a name="run-consumer-from-the-command-line"></a>Uruchom klienta z poziomu wiersza polecenia

Aby uruchomić konsumenta w wierszu polecenia, Generuj plik JAR, a następnie uruchamiać z poziomu narzędzia Maven (lub generowanie pliku JAR przy użyciu narzędzia Maven, następnie uruchomić w środowisku Java przez dodanie niezbędnych JAR(s) Kafka do ścieżki):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestConsumer"
```

Jeśli Centrum zdarzeń z obsługą platformy Kafka zdarzeń (na przykład jeśli również korzysta z Twojego producenta), a następnie użytkownik rozpoczyna odbieranie zdarzeń z tematu `test`. 

Zapoznaj się z [Akka strumieni platformy Kafka przewodnik](https://doc.akka.io/docs/akka-stream-kafka/current/home.html) więcej szczegółowych informacji na temat Akka strumieni.

## <a name="next-steps"></a>Kolejne kroki
W tym samouczku przedstawiono sposób łączenia strumieni Akka do centrów zdarzeń z obsługą platformy Kafka bez zmieniania klientów protokołu lub działające własne klastry. Usługa Azure Event Hubs dla platformy Kafka obsługuje [platformy Apache Kafka w wersji 1.0.](https://kafka.apache.org/10/documentation.html). Udało Ci następujące akcje w ramach tego samouczka: 

> [!div class="checklist"]
> * Tworzenie przestrzeni nazw usługi Event Hubs
> * Sklonuj projekt przykładowy
> * Uruchom producentów Akka strumieni 
> * Uruchom konsumenta Akka strumieni

Aby dowiedzieć się więcej na temat usługi Event Hubs i usługi Event Hubs dla platformy Kafka, zobacz następujący temat:  

* [Dowiedz się więcej na temat usługi Event Hubs](event-hubs-what-is-event-hubs.md)
* [Dowiedz się więcej na temat usługi Event Hubs dla platformy Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* [Poznaj więcej przykładów w usłudze Event Hubs dla platformy Kafka](https://github.com/Azure/azure-event-hubs-for-kafka)
* Użyj narzędzia [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330), aby [przesyłać strumieniowo zdarzenia z lokalnego środowiska platformy Kafka do usługi Event Hubs z obsługą platformy Kafka w chmurze.](event-hubs-kafka-mirror-maker-tutorial.md)
* Dowiedz się, jak przesyłać strumieniowo do platformy Kafka włączone za pomocą usługi Event Hubs [natywnych aplikacji platformy Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md) lub [Apache Flink](event-hubs-kafka-flink-tutorial.md)
