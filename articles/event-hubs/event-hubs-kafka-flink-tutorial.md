---
title: Na użytek Apache Flink z usługą Azure Event Hubs ekosystem Kafka | Dokumentacja firmy Microsoft
description: Łączącego Flink Apache do Kafka włączone Centrum zdarzeń
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: mvc
ms.date: 06/06/2018
ms.author: bahariri
ms.openlocfilehash: cb7ef0e9b6a612e3f4116cb626903770e4035368
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35304138"
---
# <a name="apache-flink-with-event-hubs-for-the-kafka-ecosystem"></a>Apache Flink z usługą Event Hubs dla ekosystemu Kafka

Jedną z kluczowych zalet przy użyciu Apache Kafka jest ekosystemu platformy, którą mogą nawiązać. Kafka włączony centra zdarzeń to połączenie elastyczność Kafka skalowalność, spójność i pomocy technicznej ekosystemu platformy Azure.

Ten samouczek pokazuje, jak nawiązać Apache Flink centra zdarzeń włączone Kafka bez konieczności zmiany klientów protokołu lub systemem własnych klastrów. Usługa Azure Event Hubs dla obsługuje ekosystem Kafka [Kafka Apache w wersji 1.0.](https://kafka.apache.org/10/documentation.html)

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka, upewnij się, że zostały spełnione następujące wymagania wstępne:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* [Zestaw Java Development Kit (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
    * W systemie Ubuntu uruchom polecenie `apt-get install default-jdk`, aby zainstalować zestaw JDK.
    * Upewnij się, że zmienna środowiskowa JAVA_HOME wskazuje folder, w którym zainstalowano zestaw JDK.
* [Pobierz](http://maven.apache.org/download.cgi) i [zainstalować](http://maven.apache.org/install.html) archiwum binarne Maven
    * W systemie Ubuntu możesz uruchomić polecenie `apt-get install maven`, aby zainstalować narzędzie Maven.
* [Git](https://www.git-scm.com/downloads)
    * W systemie Ubuntu możesz uruchomić polecenie `sudo apt-get install git`, aby zainstalować usługę Git.

## <a name="create-an-event-hubs-namespace"></a>Tworzenie przestrzeni nazw usługi Event Hubs

Centra zdarzeń w przestrzeni nazw jest wymagany do wysłania lub odebrania z dowolnej usługi Event Hubs. Zobacz [utworzyć Kafka włączone usługi Event Hubs](event-hubs-create-kafka-enabled.md) informacji o pobieranie punktu końcowego Kafka centrów zdarzeń. Upewnij się, że skopiuj parametry połączenia centra zdarzeń w celu późniejszego użycia.

## <a name="clone-the-example-project"></a>Klonowanie przykładowy projekt

Teraz, gdy masz parametry połączenia z obsługą Kafka centra zdarzeń w klonowania repozytorium Azure Event Hubs i przejdź do `flink` podfolderów:

```shell
git clone https://github.com/Azure/azure-event-hubs.git
cd azure-event-hubs/samples/kafka/flink
```

## <a name="flink-producer"></a>Producent Flink

Przy użyciu podanego przykład producent Flink, wysyłanie komunikatów do usługi Event Hubs.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Podaj punkt końcowy Kafka centra zdarzeń

#### <a name="producerconfig"></a>Producer.config

Aktualizacja `bootstrap.servers` i `sasl.jaas.config` wartości w `producer/src/main/resources/producer.config` przekierować producent do punktu końcowego Kafka centra zdarzeń z poprawnego uwierzytelnienia.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=FlinkExampleProducer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-producer-from-the-command-line"></a>Uruchom producent z wiersza polecenia

Aby uruchomić producenta z wiersza polecenia, generowanie JAR, a następnie uruchomić z poziomu Maven (lub wygenerować JAR za pomocą programu Maven, następnie uruchom w języku Java, dodając niezbędne JAR(s) Kafka do ścieżki):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestProducer"
```

Producent rozpocznie teraz wysyłania zdarzeń do Kafka włączone Centrum zdarzeń w temacie `test` i zdarzeń do stdout drukowania.

## <a name="flink-consumer"></a>Flink konsumenta

W przykładzie podana konsumenta, odbierania wiadomości z Kafka włączone usługi Event Hubs.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Podaj punkt końcowy Kafka centra zdarzeń

#### <a name="consumerconfig"></a>Consumer.config

Aktualizacja `bootstrap.servers` i `sasl.jaas.config` wartości w `consumer/src/main/resources/consumer.config` przekierować użytkownika do punktu końcowego Kafka centra zdarzeń z poprawnego uwierzytelnienia.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
group.id=FlinkExampleConsumer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-consumer-from-the-command-line"></a>Uruchom klienta z wiersza polecenia

Aby uruchomić klienta z poziomu wiersza polecenia, generowanie JAR, a następnie uruchomić z poziomu Maven (lub wygenerować JAR za pomocą programu Maven, następnie uruchom w języku Java, dodając niezbędne JAR(s) Kafka do ścieżki):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestConsumer"
```

Jeśli Centrum zdarzeń z obsługą Kafka zdarzeń (na przykład, jeśli Twoje producent jest uruchomiona), następnie konsumenta zaczyna się od odbieranie zdarzeń z tematu `test`.

Zapoznaj się z [w Flink Kafka łącznika przewodnik](https://ci.apache.org/projects/flink/flink-docs-stable/dev/connectors/kafka.html) więcej szczegółowych informacji dotyczących nawiązywania Flink Kafka.

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się więcej na temat usługi Event Hubs](event-hubs-what-is-event-hubs.md)
* [Dowiedz się więcej na temat usługi Event Hubs dla ekosystemu platformy Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* Użyj [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) do [strumienia zdarzeń od Kafka lokalnego Kafka włączone centra zdarzeń w chmurze.](event-hubs-kafka-mirror-maker-tutorial.md)
* Dowiedz się, jak strumienia do Kafka włączone za pomocą usługi Event Hubs [natywnych aplikacji Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md) lub [strumieni Akka](event-hubs-kafka-akka-streams-tutorial.md).
