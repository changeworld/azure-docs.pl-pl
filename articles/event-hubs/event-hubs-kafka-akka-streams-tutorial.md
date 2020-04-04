---
title: Korzystanie ze strumieni Akka dla platformy Apache Kafka — usługi Azure Event Hubs| Dokumenty firmy Microsoft
description: Ten artykuł zawiera informacje dotyczące sposobu łączenia strumieni Akka z centrum zdarzeń platformy Azure.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: how-to
ms.date: 04/02/2020
ms.author: shvija
ms.openlocfilehash: 0b96f1448fd223aae2dde77c5c05a8c9bd74ee9b
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632855"
---
# <a name="using-akka-streams-with-event-hubs-for-apache-kafka"></a>Używanie programu Akka Streams z usługą Event Hubs dla platformy Apache Kafka
W tym samouczku pokazano, jak połączyć strumienie Akka z centrum zdarzeń bez zmiany klientów protokołu lub uruchamiania własnych klastrów. Usługa Azure Event Hubs for the Kafka obsługuje [apache kafka w wersji 1.0.](https://kafka.apache.org/10/documentation.html)

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie przestrzeni nazw usługi Event Hubs
> * Klonowanie projektu przykładowego
> * Uruchom producenta strumieni Akka 
> * Uruchom konsumenta strumieni Akka

> [!NOTE]
> Ten przykład jest dostępny w witrynie [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/akka/java)

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, upewnij się, że masz następujące wymagania wstępne:

* Zapoznaj się z artykułem [Usługa Event Hubs dla platformy Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md). 
* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* [Zestaw Java Development Kit (JDK) 1.8+](https://aka.ms/azure-jdks)
    * W systemie Ubuntu uruchom polecenie `apt-get install default-jdk`, aby zainstalować zestaw JDK.
    * Upewnij się, że zmienna środowiskowa JAVA_HOME wskazuje folder, w którym zainstalowano zestaw JDK.
* [Pobierz](https://maven.apache.org/download.cgi) i [zainstaluj](https://maven.apache.org/install.html) archiwum binarne Maven
    * W systemie Ubuntu możesz uruchomić polecenie `apt-get install maven`, aby zainstalować narzędzie Maven.
* [Git](https://www.git-scm.com/downloads)
    * W systemie Ubuntu możesz uruchomić polecenie `sudo apt-get install git`, aby zainstalować usługę Git.

## <a name="create-an-event-hubs-namespace"></a>Tworzenie przestrzeni nazw usługi Event Hubs

Obszar nazw centrum zdarzeń jest wymagany do wysyłania lub odbierania z dowolnej usługi Centrum zdarzeń. Zobacz [Tworzenie centrum zdarzeń, aby](event-hubs-create.md) uzyskać szczegółowe informacje. Pamiętaj, aby skopiować parametry połączenia usługi Event Hubs do późniejszego użycia.

## <a name="clone-the-example-project"></a>Klonowanie projektu przykładowego

Teraz, gdy masz parametry połączenia usługi Event Hubs, sklonuj usługi Azure Event `akka` Hubs dla repozytorium platformy Kafka i przejdź do podfolderu:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/akka/java
```

## <a name="run-akka-streams-producer"></a>Uruchom producenta strumieni Akka

Za pomocą podanego przykładu producenta strumieni Akka, wysyłaj wiadomości do usługi Event Hubs.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Podaj punkt końcowy usługi Event Hubs Platformy Kafka

#### <a name="producer-applicationconf"></a>Wniosek producenta.conf

Zaktualizuj `bootstrap.servers` wartości i `sasl.jaas.config` wartości, `producer/src/main/resources/application.conf` aby skierować producenta do punktu końcowego usługi Event Hubs Platformy Kafka z poprawnym uwierzytelnianiem.

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

### <a name="run-producer-from-the-command-line"></a>Uruchom producenta z wiersza polecenia

Aby uruchomić producenta z wiersza polecenia, wygenerować JAR, a następnie uruchomić z wewnątrz Maven (lub wygenerować JAR przy użyciu Maven, a następnie uruchomić w języku Java, dodając niezbędne Kafka JAR(s) do ścieżki klasy):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestProducer"
```

Producent rozpoczyna wysyłanie wydarzeń do `test`centrum zdarzeń na temat i drukuje wydarzenia do stdout.

## <a name="run-akka-streams-consumer"></a>Uruchom konsumenta strumieni Akka

Korzystając z podanego przykładu konsumenta, odbieraj wiadomości z Centrum zdarzeń.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Podaj punkt końcowy usługi Event Hubs Platformy Kafka

#### <a name="consumer-applicationconf"></a>Aplikacja dla konsumentów.conf

Zaktualizuj `bootstrap.servers` wartości i `sasl.jaas.config` wartości, `consumer/src/main/resources/application.conf` aby skierować konsumenta do punktu końcowego usługi Event Hubs Platformy Kafka z poprawnym uwierzytelnianiem.

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

### <a name="run-consumer-from-the-command-line"></a>Uruchamianie konsumenta z wiersza polecenia

Aby uruchomić konsumenta z wiersza polecenia, wygenerować JAR, a następnie uruchomić z wewnątrz Maven (lub wygenerować JAR przy użyciu Maven, a następnie uruchomić w języku Java, dodając niezbędne Kafka JAR(s) do ścieżki klasy):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestConsumer"
```

Jeśli centrum zdarzeń zawiera zdarzenia (na przykład, jeśli producent jest również uruchomiony), `test`konsument rozpoczyna odbieranie zdarzeń z tematu . 

Zapoznaj się z [Akka Strumienie Kafka Przewodnik](https://doc.akka.io/docs/akka-stream-kafka/current/home.html) bardziej szczegółowe informacje na temat Strumieni Akka.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o centrach zdarzeń dla platformy Kafka, zobacz następujące artykuły:  

- [Dublowanie brokera platformy Kafka w centrum zdarzeń](event-hubs-kafka-mirror-maker-tutorial.md)
- [Łączenie platformy Apache Spark z centrum zdarzeń](event-hubs-kafka-spark-tutorial.md)
- [Łączenie platformy Apache Flink z centrum zdarzeń](event-hubs-kafka-flink-tutorial.md)
- [Integracja platformy Kafka Connect z centrum zdarzeń](event-hubs-kafka-connect-tutorial.md)
- [Eksplorowanie przykładów w witrynie GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
- [Apache Kafka — przewodnik dla deweloperów usługi Azure Event Hubs](apache-kafka-developer-guide.md)
