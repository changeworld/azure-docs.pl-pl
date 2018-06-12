---
title: Przy użyciu Akka strumieni z usługą Azure Event Hubs dla ekosystemu Kafka | Dokumentacja firmy Microsoft
description: Strumienie Akka nawiązywania połączenia z Kafka włączone Centrum zdarzeń
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
ms.date: 06/06/2018
ms.author: bahariri
ms.openlocfilehash: 9db27340a2210ea0be0564b15241952477e592ba
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35304135"
---
# <a name="using-akka-streams-with-event-hubs-for-kafka-ecosystem"></a>Dla ekosystemu Kafka przy użyciu Akka strumieni z usługą Event Hubs

Jedną z kluczowych zalet przy użyciu Apache Kafka jest ekosystemu platformy, którą mogą nawiązać. Włączone Kafka centra zdarzeń łączy elastyczność Kafka z skalowalność, spójność i pomocy technicznej ekosystemu platformy Azure.

Ten samouczek pokazuje, jak nawiązać strumienie Akka centra zdarzeń włączone Kafka bez zmieniania klientów protokołu ani działające klastry własne. Usługa Azure Event Hubs dla Kafka obsługuje ekosystem [Kafka Apache w wersji 1.0.](https://kafka.apache.org/10/documentation.html)

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka, upewnij się, że zostały spełnione następujące wymagania wstępne:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* [Zestaw Java Development Kit (JDK) 1.8 +](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
    * W systemie Ubuntu uruchom polecenie `apt-get install default-jdk`, aby zainstalować zestaw JDK.
    * Upewnij się, że zmienna środowiskowa JAVA_HOME wskazuje folder, w którym zainstalowano zestaw JDK.
* [Pobierz](http://maven.apache.org/download.cgi) i [zainstalować](http://maven.apache.org/install.html) archiwum binarne Maven
    * W systemie Ubuntu możesz uruchomić polecenie `apt-get install maven`, aby zainstalować narzędzie Maven.
* [Git](https://www.git-scm.com/downloads)
    * W systemie Ubuntu możesz uruchomić polecenie `sudo apt-get install git`, aby zainstalować usługę Git.

## <a name="create-an-event-hubs-namespace"></a>Tworzenie przestrzeni nazw usługi Event Hubs

Centra zdarzeń w przestrzeni nazw jest wymagany do wysłania lub odebrania z dowolnej usługi Event Hubs. Zobacz [utworzyć Kafka włączone usługi Event Hubs](event-hubs-create-kafka-enabled.md) informacji o pobieranie punktu końcowego Kafka centrów zdarzeń. Upewnij się, że skopiuj parametry połączenia centra zdarzeń w celu późniejszego użycia.

## <a name="clone-the-example-project"></a>Klonowanie przykładowy projekt

Teraz, gdy masz parametry połączenia z obsługą Kafka centra zdarzeń w klonowania repozytorium Azure Event Hubs i przejdź do `akka` podfolderów:

```shell
git clone https://github.com/Azure/azure-event-hubs.git
cd azure-event-hubs/samples/kafka/akka
```

## <a name="akka-streams-producer"></a>Producent strumienie Akka

Przykładu udostępnionego strumienie Akka producent wysyłanie komunikatów do usługi Event Hubs.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Podaj punkt końcowy Kafka centra zdarzeń

#### <a name="producer-applicationconf"></a>Producent application.conf

Aktualizacja `bootstrap.servers` i `sasl.jaas.config` wartości w `producer/src/main/resources/application.conf` przekierować producent do punktu końcowego Kafka centra zdarzeń z poprawnego uwierzytelnienia.

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

### <a name="run-producer-from-the-command-line"></a>Uruchom producent z wiersza polecenia

Aby uruchomić producenta z wiersza polecenia, generowanie JAR, a następnie uruchomić z poziomu Maven (lub wygenerować JAR za pomocą programu Maven, następnie uruchom w języku Java, dodając niezbędne JAR(s) Kafka do ścieżki):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestProducer"
```

Producent rozpoczyna wysyłanie zdarzeń do Centrum zdarzeń z obsługą Kafka w temacie `test`i wyświetla zdarzenia do stdout.

## <a name="akka-streams-consumer"></a>Akka strumieni konsumenta

Przykładu udostępnionego konsumenta odbierać komunikaty z włączoną Kafka event hubs.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Podaj punkt końcowy Kafka centra zdarzeń

#### <a name="consumer-applicationconf"></a>Application.conf konsumenta

Aktualizacja `bootstrap.servers` i `sasl.jaas.config` wartości w `consumer/src/main/resources/application.conf` przekierować użytkownika do punktu końcowego Kafka centra zdarzeń z poprawnego uwierzytelnienia.

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

### <a name="run-consumer-from-the-command-line"></a>Uruchom klienta z wiersza polecenia

Aby uruchomić klienta z poziomu wiersza polecenia, generowanie JAR, a następnie uruchomić z poziomu Maven (lub wygenerować JAR za pomocą programu Maven, następnie uruchom w języku Java, dodając niezbędne JAR(s) Kafka do ścieżki):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestConsumer"
```

Jeśli Centrum zdarzeń z obsługą Kafka ma zdarzeń (na przykład, jeśli Twoje producent jest uruchomiona), a następnie rozpoczyna konsumenta odbieranie zdarzeń z tematu `test`. 

Zapoznaj się z [Akka strumieni Kafka przewodnik](https://doc.akka.io/docs/akka-stream-kafka/current/home.html) bardziej szczegółowe informacje o Akka strumieni.

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się więcej na temat usługi Event Hubs](event-hubs-what-is-event-hubs.md)
* [Dowiedz się więcej na temat usługi Event Hubs dla ekosystemu platformy Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* Użyj [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) do [strumienia zdarzeń od Kafka lokalnego Kafka włączone centra zdarzeń w chmurze.](event-hubs-kafka-mirror-maker-tutorial.md)
* Dowiedz się, jak strumienia do Kafka włączone za pomocą usługi Event Hubs [natywnych aplikacji Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md) lub [Apache Flink](event-hubs-kafka-flink-tutorial.md)
