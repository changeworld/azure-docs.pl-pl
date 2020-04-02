---
title: Korzystanie z programu Apache Kafka MirrorMaker — usługi Azure Event Hubs | Dokumenty firmy Microsoft
description: Ten artykuł zawiera informacje dotyczące sposobu używania programu Kafka MirrorMaker do dublowania klastra platformy Kafka w usłudze AzureEvent Hubs.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: conceptual
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 3e0339cf4431d3ed36f50b43134803079e30b101
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521738"
---
# <a name="use-kafka-mirrormaker-with-event-hubs-for-apache-kafka"></a>Użyj programu Kafka MirrorMaker z centrami zdarzeń dla platformy Apache Kafka

W tym samouczku pokazano, jak dublować brokera platformy Kafka w centrum zdarzeń przy użyciu programu Kafka MirrorMaker.

   ![Kafka MirrorMaker z centrami zdarzeń](./media/event-hubs-kafka-mirror-maker-tutorial/evnent-hubs-mirror-maker1.png)

> [!NOTE]
> Ten przykład jest dostępny w witrynie [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker)


Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie przestrzeni nazw usługi Event Hubs
> * Klonowanie projektu przykładowego
> * Konfigurowanie klastra platformy Kafka
> * Konfigurowanie programu Lustrzanego Programu Kafka
> * Uruchom Kafka MirrorMaker

## <a name="introduction"></a>Wprowadzenie
Jedną z głównych kwestii dla nowoczesnych aplikacji skalowania chmury jest możliwość aktualizacji, poprawy i zmiany infrastruktury bez przerywania usługi. W tym samouczku pokazano, jak centrum zdarzeń i program Kafka MirrorMaker można zintegrować istniejący potok platformy Kafka do platformy Azure przez "dublowanie" strumienia wejściowego platformy Kafka w usłudze Usługi Centrum zdarzeń. 

Punkt końcowy usługi Azure Event Hubs Kafka umożliwia łączenie się z centrum zdarzeń platformy Azure przy użyciu protokołu Platformy Kafka (czyli klientów platformy Kafka). W celu wniesienia minimalnych zmian w aplikacji platformy Kafka można połączyć się z centrum zdarzeń platformy Azure i korzystać z zalet ekosystemu platformy Azure. Usługa Event Hubs obsługuje obecnie platformę Kafka w wersji 1.0 lub nowszej.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć kroki tego samouczka, upewnij się, że dysponujesz następującymi elementami:

* Zapoznaj się z artykułem [Usługa Event Hubs dla platformy Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md). 
* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* [Zestaw java development (JDK) 1.7+](https://aka.ms/azure-jdks)
    * W systemie Ubuntu uruchom polecenie `apt-get install default-jdk`, aby zainstalować zestaw JDK.
    * Upewnij się, że zmienna środowiskowa JAVA_HOME wskazuje folder, w którym zainstalowano zestaw JDK.
* [Pobierz](https://maven.apache.org/download.cgi) i [zainstaluj](https://maven.apache.org/install.html) archiwum binarne Maven
    * W systemie Ubuntu możesz uruchomić polecenie `apt-get install maven`, aby zainstalować narzędzie Maven.
* [Git](https://www.git-scm.com/downloads)
    * W systemie Ubuntu możesz uruchomić polecenie `sudo apt-get install git`, aby zainstalować usługę Git.

## <a name="create-an-event-hubs-namespace"></a>Tworzenie przestrzeni nazw usługi Event Hubs

Przestrzeń nazw usługi Event Hubs jest wymagana do wysyłania i odbierania zdarzeń z dowolnej usługi Event Hubs. Zobacz [Tworzenie centrum zdarzeń,](event-hubs-create.md) aby uzyskać instrukcje tworzenia obszaru nazw i centrum zdarzeń. Pamiętaj, aby skopiować parametry połączenia usługi Event Hubs do późniejszego użycia.

## <a name="clone-the-example-project"></a>Klonowanie projektu przykładowego

Teraz, gdy masz parametry połączenia usługi Event Hubs, sklonuj usługi Azure Event `mirror-maker` Hubs dla repozytorium platformy Kafka i przejdź do podfolderu:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/mirror-maker
```

## <a name="set-up-a-kafka-cluster"></a>Konfigurowanie klastra platformy Kafka

Przewodnik [Szybki start platformy Kafka](https://kafka.apache.org/quickstart) służy do konfigurowania klastra z żądanymi ustawieniami (lub do korzystania z istniejącego klastra platformy Kafka).

## <a name="configure-kafka-mirrormaker"></a>Konfigurowanie programu Lustrzanego Programu Kafka

Kafka MirrorMaker umożliwia "dublowanie" strumienia. Biorąc pod uwagę źródłowe i docelowe klastry platformy Kafka, MirrorMaker zapewnia, że wszystkie wiadomości wysyłane do klastra źródłowego są odbierane zarówno przez klastry źródłowe, jak i docelowe. W tym przykładzie pokazano, jak dublować źródłowy klaster platformy Kafka z centrum zdarzeń docelowych. W tym scenariuszu można użyć do wysyłania danych z istniejącego potoku platformy Kafka do centrów zdarzeń bez przerywania przepływu danych. 

Aby uzyskać bardziej szczegółowe informacje na temat platformy Kafka MirrorMaker, zobacz [Przewodnik Kafka Mirroring/MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330).

Aby skonfigurować program Kafka MirrorMaker, nadaj mu klaster platformy Kafka jako jego konsumenta/źródła i centrum zdarzeń jako producenta/miejsca docelowego.

#### <a name="consumer-configuration"></a>Konfiguracja konsumencka

Zaktualizuj `source-kafka.config`plik konfiguracji konsumenta , który informuje MirrorMaker właściwości źródłowego klastra platformy Kafka.

##### <a name="source-kafkaconfig"></a>źródło-kafka.config

```
bootstrap.servers={SOURCE.KAFKA.IP.ADDRESS1}:{SOURCE.KAFKA.PORT1},{SOURCE.KAFKA.IP.ADDRESS2}:{SOURCE.KAFKA.PORT2},etc
group.id=example-mirrormaker-group
exclude.internal.topics=true
client.id=mirror_maker_consumer
```

#### <a name="producer-configuration"></a>Konfiguracja producenta

Teraz zaktualizuj plik `mirror-eventhub.config`konfiguracji producenta , który nakazuje MirrorMaker wysłać zduplikowane (lub "dublowane") dane do usługi Centrum zdarzeń. W szczególności `bootstrap.servers` zmień `sasl.jaas.config` i wskazać punkt końcowy usługi Event Hubs Platformy Kafka. Usługa Event Hubs wymaga bezpiecznej komunikacji (SASL), która jest osiągana przez ustawienie trzech ostatnich właściwości w następującej konfiguracji: 

##### <a name="mirror-eventhubconfig"></a>lustrzane eventhub.config

```
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=mirror_maker_producer

#Required for Event Hubs
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

## <a name="run-kafka-mirrormaker"></a>Uruchom Kafka MirrorMaker

Uruchom skrypt Programu Kafka MirrorMaker z głównego katalogu kafka przy użyciu nowo zaktualizowanych plików konfiguracyjnych. Pamiętaj, aby skopiować pliki konfiguracyjne do głównego katalogu platformy Kafka lub zaktualizować ich ścieżki w następującym poleceniu.

```shell
bin/kafka-mirror-maker.sh --consumer.config source-kafka.config --num.streams 1 --producer.config mirror-eventhub.config --whitelist=".*"
```

Aby sprawdzić, czy zdarzenia docierają do centrum zdarzeń, zobacz statystyki transferu danych przychodzących w [witrynie Azure portal](https://azure.microsoft.com/features/azure-portal/)lub uruchom konsumenta względem centrum zdarzeń.

Po uruchomieniu programu MirrorMaker wszystkie zdarzenia wysyłane do źródłowego klastra platformy Kafka są odbierane zarówno przez klaster platformy Kafka, jak i centrum zdarzeń dublowanych. Za pomocą MirrorMaker i punktu końcowego platformy Kafka centrów zdarzeń, można migrować istniejący potok platformy Kafka do zarządzanej usługi Usługi Azure Event Hubs bez zmiany istniejącego klastra lub przerywania bieżącego przepływu danych.

## <a name="samples"></a>Samples
Zobacz następujące przykłady w usłudze GitHub:

- [Przykładowy kod dla tego samouczka w usłudze GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker)
- [Narzędzie Azure Event Hubs Kafka MirrorMaker działające w wystąpieniu kontenera platformy Azure](https://github.com/djrosanova/EventHubsMirrorMaker)

## <a name="next-steps"></a>Następne kroki

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie przestrzeni nazw usługi Event Hubs
> * Klonowanie projektu przykładowego
> * Konfigurowanie klastra platformy Kafka
> * Konfigurowanie programu Lustrzanego Programu Kafka
> * Uruchom Kafka MirrorMaker

Aby dowiedzieć się więcej na temat usługi Event Hubs i usługi Event Hubs dla platformy Kafka, zobacz następujący temat:  

- [Dowiedz się więcej na temat usługi Event Hubs](event-hubs-what-is-event-hubs.md)
- [Usługa Event Hubs dla platformy Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
- [Jak utworzyć centrum zdarzeń](event-hubs-create.md)
- [Przesyłanie strumieniowe do usługi Event Hubs z aplikacji platformy Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Łączenie platformy Apache Spark z centrum zdarzeń](event-hubs-kafka-spark-tutorial.md)
- [Łączenie platformy Apache Flink z centrum zdarzeń](event-hubs-kafka-flink-tutorial.md)
- [Integracja platformy Kafka Connect z centrum zdarzeń](event-hubs-kafka-connect-tutorial.md)
- [Łączenie strumieni Akka z centrum zdarzeń](event-hubs-kafka-akka-streams-tutorial.md)
- [Eksplorowanie przykładów w witrynie GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
