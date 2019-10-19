---
title: Użyj Apache Kafka narzędzia MirrorMaker — Azure Event Hubs | Microsoft Docs
description: Ten artykuł zawiera informacje dotyczące używania Kafka narzędzia MirrorMaker do dublowania klastra Kafka w centrach AzureEvent.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: conceptual
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 6d1596cf0a50ed5dcb896896282178b6fc12c1a1
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555112"
---
# <a name="use-kafka-mirrormaker-with-event-hubs-for-apache-kafka"></a>Użyj Kafka narzędzia MirrorMaker z Event Hubs dla Apache Kafka

W tym samouczku pokazano, jak zdublować brokera Kafka w centrum zdarzeń z obsługą Kafka przy użyciu Kafka narzędzia MirrorMaker.

   ![Kafka narzędzia MirrorMaker z Event Hubs](./media/event-hubs-kafka-mirror-maker-tutorial/evnent-hubs-mirror-maker1.png)

> [!NOTE]
> Ten przykład jest dostępny w witrynie [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker)


Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie przestrzeni nazw usługi Event Hubs
> * Klonowanie projektu przykładowego
> * Konfigurowanie klastra Kafka
> * Konfigurowanie Kafka narzędzia MirrorMaker
> * Uruchom Kafka narzędzia MirrorMaker

## <a name="introduction"></a>Wprowadzenie
Ważnym zagadnieniem dotyczącym nowoczesnych aplikacji w skali chmury jest możliwość aktualizowania, ulepszania i zmiany infrastruktury bez zakłócania usługi. W tym samouczku pokazano, jak centrum zdarzeń z obsługą Kafka i Kafka narzędzia MirrorMaker mogą zintegrować istniejący potok Kafka z platformą Azure przez "dublowanie" strumienia wejściowego w usłudze Event Hubs. 

Punkt końcowy usługi Azure Event Hubs Kafka umożliwia nawiązanie połączenia z usługą Azure Event Hubs przy użyciu protokołu Kafka (czyli klientów Kafka). Wprowadzając minimalne zmiany w aplikacji Kafka, możesz połączyć się z platformą Azure Event Hubs i korzystać z zalet ekosystemu platformy Azure. Kafka włączone Event Hubs obecnie obsługuje Kafka w wersji 1,0 i nowszych.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć kroki tego samouczka, upewnij się, że dysponujesz następującymi elementami:

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

Przestrzeń nazw usługi Event Hubs jest wymagana do wysyłania i odbierania zdarzeń z dowolnej usługi Event Hubs. Aby uzyskać instrukcje uzyskiwania punktu końcowego platformy Kafka w usłudze Event Hubs, zobacz [Creating a Kafka enabled Event Hub](event-hubs-create.md) (Tworzenie centrum zdarzeń z obsługą platformy Kafka). Skopiuj Event Hubs parametry połączenia do późniejszego użycia.

## <a name="clone-the-example-project"></a>Klonowanie projektu przykładowego

Teraz, gdy masz Kafka Event Hubs parametry połączenia, Sklonuj Event Hubs platformy Azure dla repozytorium Kafka i przejdź do podfolderu `mirror-maker`:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/mirror-maker
```

## <a name="set-up-a-kafka-cluster"></a>Konfigurowanie klastra Kafka

Skorzystaj z [przewodnika Szybki Start](https://kafka.apache.org/quickstart) dla programu Kafka, aby skonfigurować klaster z żądanymi ustawieniami (lub użyć istniejącego klastra Kafka).

## <a name="configure-kafka-mirrormaker"></a>Konfigurowanie Kafka narzędzia MirrorMaker

Kafka narzędzia MirrorMaker włącza "dublowanie" strumienia. Dane źródłowe i docelowe klastry Kafka, narzędzia MirrorMaker zapewniają, że wszystkie komunikaty wysyłane do klastra źródłowego są odbierane przez klastry źródłowe i docelowe. Ten przykład pokazuje, jak dublować źródłowy klaster Kafka z docelowym centrum zdarzeń z obsługą Kafka. Ten scenariusz może służyć do wysyłania danych z istniejącego potoku Kafka do Event Hubs bez zakłócania przepływu danych. 

Aby uzyskać szczegółowe informacje na temat Kafka narzędzia MirrorMaker, zobacz [Podręcznik Kafka/narzędzia MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330).

Aby skonfigurować Kafka narzędzia MirrorMaker, nadaj jej klasterowi Kafka jako klienta/źródło oraz centrum zdarzeń z obsługą Kafka jako jego producenta/miejsce docelowe.

#### <a name="consumer-configuration"></a>Konfiguracja konsumenta

Zaktualizuj plik konfiguracji klienta `source-kafka.config`, który informuje o narzędzia MirrorMaker właściwości źródłowego klastra Kafka.

##### <a name="source-kafkaconfig"></a>source-Kafka. config

```
bootstrap.servers={SOURCE.KAFKA.IP.ADDRESS1}:{SOURCE.KAFKA.PORT1},{SOURCE.KAFKA.IP.ADDRESS2}:{SOURCE.KAFKA.PORT2},etc
group.id=example-mirrormaker-group
exclude.internal.topics=true
client.id=mirror_maker_consumer
```

#### <a name="producer-configuration"></a>Konfiguracja producenta

Teraz Zaktualizuj plik konfiguracji producenta `mirror-eventhub.config`, który informuje narzędzia MirrorMaker o wysłaniu duplikatów (lub "dublowanych") danych do usługi Event Hubs. W programie Zmień `bootstrap.servers` i `sasl.jaas.config`, aby wskazywały punkt końcowy Event Hubs Kafka. Usługa Event Hubs wymaga komunikacji Secure (SASL), która jest osiągana przez ustawienie ostatnich trzech właściwości w następującej konfiguracji: 

##### <a name="mirror-eventhubconfig"></a>dublowanie — eventhub. config

```
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=mirror_maker_producer

#Required for Event Hubs
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

## <a name="run-kafka-mirrormaker"></a>Uruchom Kafka narzędzia MirrorMaker

Uruchom skrypt Kafka narzędzia MirrorMaker z katalogu głównego Kafka przy użyciu nowo zaktualizowanych plików konfiguracji. Upewnij się, że Skopiuj pliki konfiguracji do głównego katalogu Kafka lub zaktualizuj swoje ścieżki w poniższym poleceniu.

```shell
bin/kafka-mirror-maker.sh --consumer.config source-kafka.config --num.streams 1 --producer.config mirror-eventhub.config --whitelist=".*"
```

Aby sprawdzić, czy zdarzenia docierają do centrum zdarzeń z obsługą Kafka, zapoznaj się z statystykami transferu danych przychodzących w [Azure Portal](https://azure.microsoft.com/features/azure-portal/)lub Uruchom odbiorcę w centrum zdarzeń.

Po uruchomieniu narzędzia MirrorMaker wszystkie zdarzenia wysyłane do źródłowego klastra Kafka są odbierane przez zarówno klaster Kafka, jak i dublowaną usługę centrum zdarzeń z włączonym Kafka. Korzystając z narzędzia MirrorMaker i punktu końcowego Kafka Event Hubs, można migrować istniejący potok Kafka do zarządzanej usługi Azure Event Hubs bez zmiany istniejącego klastra ani zakłócania ciągłego przepływu danych.

## <a name="samples"></a>Przykłady
Zobacz następujące przykłady w witrynie GitHub:

- [Przykładowy kod dla tego samouczka w witrynie GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker)
- [Usługa Azure Event Hubs Kafka narzędzia MirrorMaker uruchomiona w wystąpieniu kontenera platformy Azure](https://github.com/djrosanova/EventHubsMirrorMaker)

## <a name="next-steps"></a>Następne kroki

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie przestrzeni nazw usługi Event Hubs
> * Klonowanie projektu przykładowego
> * Konfigurowanie klastra Kafka
> * Konfigurowanie Kafka narzędzia MirrorMaker
> * Uruchom Kafka narzędzia MirrorMaker

Aby dowiedzieć się więcej na temat usługi Event Hubs i usługi Event Hubs dla platformy Kafka, zobacz następujący temat:  

- [Dowiedz się więcej na temat usługi Event Hubs](event-hubs-what-is-event-hubs.md)
- [Usługa Event Hubs dla platformy Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
- [Jak utworzyć centra Event Hubs z obsługą platformy Kafka](event-hubs-create-kafka-enabled.md)
- [Przesyłanie strumieniowe do usługi Event Hubs z aplikacji platformy Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Łączenie platformy Apache Spark z centrum zdarzeń z obsługą platformy Kafka](event-hubs-kafka-spark-tutorial.md)
- [Łączenie platformy Apache Flink z centrum zdarzeń z obsługą platformy Kafka](event-hubs-kafka-flink-tutorial.md)
- [Integrowanie narzędzia Kafka Connect z centrum zdarzeń z obsługą platformy Kafka](event-hubs-kafka-connect-tutorial.md)
- [Łączenie biblioteki Akka Streams z centrum zdarzeń z obsługą platformy Kafka](event-hubs-kafka-akka-streams-tutorial.md)
- [Eksplorowanie przykładów w witrynie GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
