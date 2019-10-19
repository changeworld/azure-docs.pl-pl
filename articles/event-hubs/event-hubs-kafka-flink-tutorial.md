---
title: Korzystanie z platformy Apache Flink dla Apache Kafka — Event Hubs Azure | Microsoft Docs
description: Ten artykuł zawiera informacje dotyczące sposobu łączenia oprogramowania Apache Flink Apache Kafka z włączonym centrum zdarzeń platformy Azure
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 881546a97b01bef993cc24c6b868ec97ddf5ac36
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555718"
---
# <a name="use-apache-flink-with-azure-event-hubs-for-apache-kafka"></a>Korzystanie z platformy Apache Flink z usługą Azure Event Hubs dla Apache Kafka
W tym samouczku pokazano, jak podłączyć Apache Flink do centrów zdarzeń z obsługą Kafka bez konieczności zmiany klientów protokołu lub uruchamiania własnych klastrów. Usługa Azure Event Hubs obsługuje [Apache Kafka w wersji 1,0.](https://kafka.apache.org/10/documentation.html).

Jedną z najważniejszych zalet korzystania z Apache Kafka jest ekosystem struktur, z którymi może się połączyć. Kafka włączone Event Hubs łączy elastyczność Kafka z skalowalnością, spójnością i wsparciem ekosystemu platformy Azure.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie przestrzeni nazw usługi Event Hubs
> * Klonowanie projektu przykładowego
> * Uruchom producenta Flink 
> * Uruchom klienta Flink

> [!NOTE]
> Ten przykład jest dostępny w witrynie [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink)

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, upewnij się, że masz następujące wymagania wstępne:

* Zapoznaj się z artykułem [Usługa Event Hubs dla platformy Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md). 
* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* [Zestaw Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks)
    * W systemie Ubuntu uruchom polecenie `apt-get install default-jdk`, aby zainstalować zestaw JDK.
    * Upewnij się, że zmienna środowiskowa JAVA_HOME wskazuje folder, w którym zainstalowano zestaw JDK.
* [Pobieranie](https://maven.apache.org/download.cgi) i [Instalowanie](https://maven.apache.org/install.html) archiwum binarnego Maven
    * W systemie Ubuntu możesz uruchomić polecenie `apt-get install maven`, aby zainstalować narzędzie Maven.
* [Usługa Git](https://www.git-scm.com/downloads)
    * W systemie Ubuntu możesz uruchomić polecenie `sudo apt-get install git`, aby zainstalować usługę Git.

## <a name="create-an-event-hubs-namespace"></a>Tworzenie przestrzeni nazw usługi Event Hubs

Przestrzeń nazw Event Hubs jest wymagana do wysyłania lub odbierania z dowolnej usługi Event Hubs. Aby uzyskać informacje na temat pobierania punktu końcowego Event Hubs Kafka, zobacz temat [Tworzenie Kafka włączonych Event Hubs](event-hubs-create-kafka-enabled.md) . Skopiuj Event Hubs parametry połączenia do późniejszego użycia.

## <a name="clone-the-example-project"></a>Klonowanie projektu przykładowego

Teraz, gdy masz Event Hubs parametry połączenia z włączoną obsługą Kafka, Sklonuj Event Hubs platformy Azure dla repozytorium Kafka i przejdź do podfolderu `flink`:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/flink
```

## <a name="run-flink-producer"></a>Uruchom producenta Flink

Korzystając z podanego przykładu producenta Flink, Wysyłaj komunikaty do usługi Event Hubs.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Podaj Event Hubs punkt końcowy Kafka

#### <a name="producerconfig"></a>plik Producer. config

Zaktualizuj `bootstrap.servers` i `sasl.jaas.config` wartości w `producer/src/main/resources/producer.config`, aby skierować producenta do punktu końcowego Event Hubs Kafka z prawidłowym uwierzytelnianiem.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=FlinkExampleProducer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-producer-from-the-command-line"></a>Uruchom producenta z wiersza polecenia

Aby uruchomić producenta z wiersza polecenia, wygeneruj plik JAR, a następnie uruchom go z poziomu Maven (lub wygeneruj plik JAR przy użyciu Maven, a następnie uruchom polecenie w języku Java, dodając wymagane Kafka JAR (y) do ścieżki klasy):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestProducer"
```

Producent zacznie teraz wysyłać zdarzenia do centrum zdarzeń z włączonym Kafka w temacie `test` i drukować zdarzenia do stdout.

## <a name="run-flink-consumer"></a>Uruchom klienta Flink

Korzystając z podanego przykładu konsumenta, odbieraj komunikaty z Kafka z włączonymi Event Hubs.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Podaj Event Hubs punkt końcowy Kafka

#### <a name="consumerconfig"></a>plik Consumer. config

Zaktualizuj `bootstrap.servers` i `sasl.jaas.config` wartości w `consumer/src/main/resources/consumer.config`, aby skierować odbiorcę do Event Hubs punktu końcowego Kafka z prawidłowym uwierzytelnianiem.

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

Aby uruchomić konsumenta z wiersza polecenia, wygeneruj plik JAR, a następnie uruchom go z poziomu Maven (lub wygeneruj plik JAR przy użyciu Maven, a następnie uruchom polecenie w języku Java, dodając wymagane Kafka JAR (y) do ścieżki klasy):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestConsumer"
```

Jeśli centrum zdarzeń z obsługą Kafka ma zdarzenia (na przykład jeśli producent jest również uruchomiony), konsument zacznie teraz odbierać zdarzenia z tematu `test`.

Zapoznaj się z [przewodnikiem po łączniku Kafka](https://ci.apache.org/projects/flink/flink-docs-stable/dev/connectors/kafka.html) w programie Flink, aby uzyskać bardziej szczegółowe informacje na temat łączenia Flink z Kafka.

## <a name="next-steps"></a>Następne kroki
W tym samouczku dowiesz się, jak połączyć Apache Flink z centrami zdarzeń z obsługą Kafkaymi bez zmiany klientów protokołu lub uruchamiania własnych klastrów. Następujące kroki są wykonywane w ramach tego samouczka: 

> [!div class="checklist"]
> * Tworzenie przestrzeni nazw usługi Event Hubs
> * Klonowanie projektu przykładowego
> * Uruchom producenta Flink 
> * Uruchom klienta Flink

Aby dowiedzieć się więcej na temat usługi Event Hubs i usługi Event Hubs dla platformy Kafka, zobacz następujący temat:  

- [Dowiedz się więcej na temat usługi Event Hubs](event-hubs-what-is-event-hubs.md)
- [Usługa Event Hubs dla platformy Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
- [Jak utworzyć centra Event Hubs z obsługą platformy Kafka](event-hubs-create-kafka-enabled.md)
- [Przesyłanie strumieniowe do usługi Event Hubs z aplikacji platformy Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Dublowanie brokera platformy Kafka w centrum zdarzeń z obsługą platformy Kafka](event-hubs-kafka-mirror-maker-tutorial.md)
- [Łączenie platformy Apache Spark z centrum zdarzeń z obsługą platformy Kafka](event-hubs-kafka-spark-tutorial.md)
- [Integrowanie narzędzia Kafka Connect z centrum zdarzeń z obsługą platformy Kafka](event-hubs-kafka-connect-tutorial.md)
- [Łączenie biblioteki Akka Streams z centrum zdarzeń z obsługą platformy Kafka](event-hubs-kafka-akka-streams-tutorial.md)
- [Eksplorowanie przykładów w witrynie GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
