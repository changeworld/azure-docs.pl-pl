---
title: Łączenie z aplikacją platformy Apache Spark w usłudze Azure Event Hubs | Microsoft Docs
description: W tym artykule opisano, jak korzystać z platformy Apache Spark z usługą Azure Event Hubs dla platformy Kafka.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: tutorial
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 70f55891874b474de4120d8b41594fb9bee74e8d
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521658"
---
# <a name="connect-your-apache-spark-application-with-azure-event-hubs"></a>Łączenie aplikacji Apache Spark z usługą Azure Event Hubs
W tym samouczku można przejść przez łączenie aplikacji Spark z centrum zdarzeń w celu przesyłania strumieniowego w czasie rzeczywistym. Ta integracja umożliwia przesyłanie strumieniowe bez konieczności zmiany klientów protokołu czy uruchamiania własnych klastrów platformy Kafka lub Zookeeper. Ten samouczek wymaga oprogramowania Apache Spark w wersji 2.4+ i Apache Kafka w wersji 2.0+.

> [!NOTE]
> Ten przykład jest dostępny w witrynie [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/spark/)

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie przestrzeni nazw usługi Event Hubs
> * Klonowanie projektu przykładowego
> * Uruchamianie platformy Spark
> * Odczyt z usługi Event Hubs dla platformy Kafka
> * Zapis do usługi Event Hubs dla platformy Kafka

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka upewnij się, że masz następujące elementy:
-   Subskrypcja platformy Azure. Jeśli jej nie masz, [utwórz bezpłatne konto](https://azure.microsoft.com/free/).
-   [Apache Spark w wersji 2.4](https://spark.apache.org/downloads.html)
-   [Apache Kafka w wersji 2.0]( https://kafka.apache.org/20/documentation.html)
-   [Git](https://www.git-scm.com/downloads)

> [!NOTE]
> Adapter Spark-Kafka został zaktualizowany do obsługi platformy Kafka w wersji 2.0 i platformy Spark od wersji 2.4. W poprzednich wersjach platformy Spark adapter obsługiwał platformę Kafka w wersji 0.10 i nowszych, ale bazował na interfejsach API platformy Kafka w wersji 0.10. Ponieważ usługa Event Hubs dla platformy Kafka nie obsługuje platformy Kafka w wersji 0.10, adaptery Spark-Kafka w wersjach platformy Spark wcześniejszych niż 2.4 nie są obsługiwane przez usługę Event Hubs dla ekosystemów platformy Kafka.


## <a name="create-an-event-hubs-namespace"></a>Tworzenie przestrzeni nazw usługi Event Hubs
Przestrzeń nazw usługi Event Hubs jest wymagana do wysyłania i odbierania zdarzeń z dowolnej usługi Event Hubs. Zobacz [Tworzenie centrum zdarzeń,](event-hubs-create.md) aby uzyskać instrukcje tworzenia obszaru nazw i centrum zdarzeń. Pobierz parametry połączenia usługi Event Hubs i w pełni kwalifikowaną nazwę domeny (FQDN) w celu późniejszego użycia. Aby uzyskać instrukcje, zobacz [Get an Event Hubs connection string](event-hubs-get-connection-string.md) (Pobieranie parametrów połączenia usługi Event Hubs). 

## <a name="clone-the-example-project"></a>Klonowanie projektu przykładowego
Sklonuj repozytorium usługi Azure Event Hubs i przejdź do podfolderu `tutorials/spark`:

```bash
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/spark
```

## <a name="read-from-event-hubs-for-kafka"></a>Odczyt z usługi Event Hubs dla platformy Kafka
Po dokonaniu kilku zmian w konfiguracji możesz rozpocząć odczyt z usługi Event Hubs dla platformy Kafka. Po zaktualizowaniu zmiennych **BOOTSTRAP_SERVERS** i **EH_SASL** za pomocą szczegółowych informacji dotyczących przestrzeni nazw możesz rozpocząć przesyłanie strumieniowe z usługi Event Hubs tak samo jak z platformy Kafka. Kompletny kod przykładowy znajduje się w pliku sparkConsumer.scala w witrynie GitHub. 

```scala
//Read from your Event Hub!
val df = spark.readStream
    .format("kafka")
    .option("subscribe", TOPIC)
    .option("kafka.bootstrap.servers", BOOTSTRAP_SERVERS)
    .option("kafka.sasl.mechanism", "PLAIN")
    .option("kafka.security.protocol", "SASL_SSL")
    .option("kafka.sasl.jaas.config", EH_SASL)
    .option("kafka.request.timeout.ms", "60000")
    .option("kafka.session.timeout.ms", "30000")
    .option("kafka.group.id", GROUP_ID)
    .option("failOnDataLoss", "false")
    .load()

//Use dataframe like normal (in this example, write to console)
val df_write = df.writeStream
    .outputMode("append")
    .format("console")
    .start()
```

## <a name="write-to-event-hubs-for-kafka"></a>Zapis do usługi Event Hubs dla platformy Kafka
Zapis do usługi Event Hubs może również wyglądać tak samo jak zapis do platformy Kafka. Nie zapomnij zaktualizować konfiguracji, wstawiając dla zmiennych **BOOTSTRAP_SERVERS** i **EH_SASL** informacje z przestrzeni nazw usługi Event Hubs.  Kompletny kod przykładowy znajduje się w pliku sparkProducer.scala w witrynie GitHub. 

```scala
df = /**Dataframe**/

//Write to your Event Hub!
df.writeStream
    .format("kafka")
    .option("topic", TOPIC)
    .option("kafka.bootstrap.servers", BOOTSTRAP_SERVERS)
    .option("kafka.sasl.mechanism", "PLAIN")
    .option("kafka.security.protocol", "SASL_SSL")
    .option("kafka.sasl.jaas.config", EH_SASL)
    .option("checkpointLocation", "./checkpoint")
    .start()
```



## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób przesyłania strumieniowego przy użyciu łącznika Spark-Kafka i usługi Event Hubs dla platformy Kafka. Wykonano następujące czynności: 

> [!div class="checklist"]
> * Tworzenie przestrzeni nazw usługi Event Hubs
> * Klonowanie projektu przykładowego
> * Uruchamianie platformy Spark
> * Odczyt z usługi Event Hubs dla platformy Kafka
> * Zapis do usługi Event Hubs dla platformy Kafka

Aby dowiedzieć się więcej na temat usługi Event Hubs i usługi Event Hubs dla platformy Kafka, zobacz następujący temat:  

- [Dowiedz się więcej na temat usługi Event Hubs](event-hubs-what-is-event-hubs.md)
- [Usługa Event Hubs dla platformy Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
- [Tworzenie centrum zdarzeń](event-hubs-create.md)
- [Przesyłanie strumieniowe do usługi Event Hubs z aplikacji platformy Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Dublowanie brokera platformy Kafka w centrum zdarzeń](event-hubs-kafka-mirror-maker-tutorial.md)
- [Łączenie platformy Apache Flink z centrum zdarzeń](event-hubs-kafka-flink-tutorial.md)
- [Integracja platformy Kafka Connect z centrum zdarzeń](event-hubs-kafka-connect-tutorial.md)
- [Łączenie strumieni Akka z centrum zdarzeń](event-hubs-kafka-akka-streams-tutorial.md)
- [Eksplorowanie przykładów w witrynie GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
