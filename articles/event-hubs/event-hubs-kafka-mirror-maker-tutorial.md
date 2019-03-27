---
title: Przy użyciu narzędzia MirrorMaker programu Apache Kafka — usługa Azure Event Hubs | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera informacje na temat sposobu utworzenia duplikatów klastra platformy Kafka w usłudze AzureEvent Hubs przy użyciu narzędzia MirrorMaker platformy Kafka.
services: event-hubs
documentationcenter: .net
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.topic: conceptual
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: bahariri
ms.openlocfilehash: a7271eb6b8cbc8a117b5a8e75edfe02985ec3452
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58487468"
---
# <a name="use-kafka-mirrormaker-with-event-hubs-for-apache-kafka"></a>Przy użyciu narzędzia MirrorMaker platformy Kafka z usługą Event Hubs dla platformy Apache Kafka

W tym samouczku pokazano, jak duplikatów brokerem platformy Kafka w Centrum zdarzeń włączone platformy Kafka, przy użyciu narzędzia MirrorMaker platformy Kafka.

   ![Narzędzia MirrorMaker platformy Kafka w usłudze Event Hubs](./media/event-hubs-kafka-mirror-maker-tutorial/evnent-hubs-mirror-maker1.png)

> [!NOTE]
> Ten przykład jest dostępny w witrynie [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker)


Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie przestrzeni nazw usługi Event Hubs
> * Klonowanie projektu przykładowego
> * Konfigurowanie klastra Kafka
> * Konfigurowanie narzędzia MirrorMaker platformy Kafka
> * Uruchom narzędzia MirrorMaker platformy Kafka

## <a name="introduction"></a>Wprowadzenie
Jedną główną kwestią do rozważenia dla aplikacji w skali chmury nowoczesnych jest możliwość aktualizacji, poprawić i zmienić infrastruktury bez przerywania obsługi. Ten samouczek pokazuje, jak wraz z Centrum zdarzeń z obsługą platformy Kafka i narzędzia MirrorMaker Kafka zintegrować istniejący potok platformy Kafka na platformie Azure przez "dublowanie" strumień wejściowy platformy Kafka w usłudze Event Hubs. 

Punkt końcowy usługi Azure Event Hubs Kafka umożliwiają łączenie usługi Azure Event hubs przy użyciu protokołu Kafka (oznacza to, że klienci platformy Kafka). Wprowadzając minimalnych zmianach w aplikacji platformy Kafka, możesz nawiązać połączenie z usługi Azure Event Hubs i cieszą się ekosystemu platformy Azure. Platforma Kafka jest włączona usługa Event Hubs obecnie obsługuje platformy Kafka w wersji 1.0 lub nowszej.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć kroki tego samouczka, upewnij się, że dysponujesz następującymi elementami:

* Zapoznaj się z artykułem [Usługa Event Hubs dla platformy Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md). 
* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* [Zestaw Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks)
    * W systemie Ubuntu uruchom polecenie `apt-get install default-jdk`, aby zainstalować zestaw JDK.
    * Upewnij się, że zmienna środowiskowa JAVA_HOME wskazuje folder, w którym zainstalowano zestaw JDK.
* [Pobierz](https://maven.apache.org/download.cgi) i [zainstalować](https://maven.apache.org/install.html) archiwum binarne Maven
    * W systemie Ubuntu możesz uruchomić polecenie `apt-get install maven`, aby zainstalować narzędzie Maven.
* [Usługa Git](https://www.git-scm.com/downloads)
    * W systemie Ubuntu możesz uruchomić polecenie `sudo apt-get install git`, aby zainstalować usługę Git.

## <a name="create-an-event-hubs-namespace"></a>Tworzenie przestrzeni nazw usługi Event Hubs

Przestrzeń nazw usługi Event Hubs jest wymagana do wysyłania i odbierania zdarzeń z dowolnej usługi Event Hubs. Aby uzyskać instrukcje uzyskiwania punktu końcowego platformy Kafka w usłudze Event Hubs, zobacz [Creating a Kafka enabled Event Hub](event-hubs-create.md) (Tworzenie centrum zdarzeń z obsługą platformy Kafka). Upewnij się skopiować parametry połączenia usługi Event Hubs w celu późniejszego użycia.

## <a name="clone-the-example-project"></a>Klonowanie projektu przykładowego

Teraz, gdy masz platformy Kafka włączone parametry połączenia usługi Event Hubs, Azure Event Hubs dla platformy Kafka repozytorium klonowanie i przejdź do `mirror-maker` podfolder:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/mirror-maker
```

## <a name="set-up-a-kafka-cluster"></a>Konfigurowanie klastra Kafka

Użyj [przewodnika Szybki Start platformy Kafka](https://kafka.apache.org/quickstart) do skonfigurowania klastra przy użyciu odpowiednich ustawień (lub użyj istniejącego klastra platformy Kafka).

## <a name="configure-kafka-mirrormaker"></a>Konfigurowanie narzędzia MirrorMaker platformy Kafka

Narzędzia MirrorMaker Kafka umożliwia "dublowanie" strumienia. Podane źródłowe i docelowe klastry platformy Kafka, zapewnia narzędzia MirrorMaker, komunikaty wysyłane do klastra źródłowego są odbierane przez klastry źródłowym i docelowym. W tym przykładzie pokazano, jak dublowanego klastra platformy Kafka z Centrum zdarzeń z obsługą platformy Kafka docelowego źródła. Ten scenariusz może służyć do wysyłania danych z istniejącym potokiem Kafka do usługi Event Hubs, bez przerywania przepływu danych. 

Więcej szczegółowych informacji na temat platformy Kafka narzędzia MirrorMaker, zobacz [przewodnik platformy Kafka dublowania/narzędzia MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330).

Aby skonfigurować narzędzia MirrorMaker platformy Kafka, należy nadać mu klastra platformy Kafka jako konsument/źródło i wraz z Centrum zdarzeń z obsługą platformy Kafka jako miejsca docelowego/producenta.

#### <a name="consumer-configuration"></a>Konfiguracja klienta

Zaktualizuj plik konfiguracji konsumenta `source-kafka.config`, który informuje o narzędzia MirrorMaker właściwości źródła klastra Kafka.

##### <a name="source-kafkaconfig"></a>kafka.config źródła

```xml
bootstrap.servers={SOURCE.KAFKA.IP.ADDRESS1}:{SOURCE.KAFKA.PORT1},{SOURCE.KAFKA.IP.ADDRESS2}:{SOURCE.KAFKA.PORT2},etc
group.id=example-mirrormaker-group
exclude.internal.topics=true
client.id=mirror_maker_consumer
```

#### <a name="producer-configuration"></a>Konfiguracja producenta

Teraz zaktualizować plik konfiguracyjny producenta `mirror-eventhub.config`, który informuje o narzędzia MirrorMaker do wysyłania danych zduplikowane (lub "dublowany") do usługi Event Hubs. W szczególności zmiany `bootstrap.servers` i `sasl.jaas.config` aby wskazać na punkt końcowy usługi Event Hubs Kafka. Usługa Event Hubs wymaga bezpiecznej komunikacji (SASL), w którym odbywa się przez ustawienie właściwości ostatnie trzy w następującej konfiguracji: 

##### <a name="mirror-eventhubconfig"></a>dublowanie eventhub.config

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=mirror_maker_producer

#Required for Event Hubs
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

## <a name="run-kafka-mirrormaker"></a>Uruchom narzędzia MirrorMaker platformy Kafka

Uruchom skrypt narzędzia MirrorMaker platformy Kafka z katalogu głównego katalogu Kafka za pomocą plików konfiguracji właśnie został zaktualizowany. Upewnij się, skopiuj pliki konfiguracji do katalogu głównego katalogu platformy Kafka lub zaktualizować ich ścieżek w następującym poleceniu.

```shell
bin/kafka-mirror-maker.sh --consumer.config source-kafka.config --num.streams 1 --producer.config mirror-eventhub.config --whitelist=".*"
```

Aby sprawdzić, czy zdarzenia osiągają do Centrum zdarzeń z obsługą platformy Kafka, zobacz statystyk transferu danych przychodzących w [witryny Azure portal](https://azure.microsoft.com/features/azure-portal/), lub uruchamiać konsumentów Centrum zdarzeń.

Przy użyciu narzędzia MirrorMaker, uruchamianie wszelkie zdarzenia wysyłane do źródła klastra platformy Kafka są odbierane przez klaster platformy Kafka i Kafka dublowanego włączono usługę Centrum zdarzeń. Przy użyciu narzędzia MirrorMaker i punktu końcowego usługi Event Hubs Kafka, można migrować istniejący potok Kafka do zarządzanej usługi Azure Event Hubs, bez zmieniania istniejącego klastra lub przerywania dowolnego przepływu bieżące dane.

## <a name="samples"></a>Przykłady
Zobacz poniższe przykłady w witrynie GitHub:

- [Przykładowy kod w tym samouczku w witrynie GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker)
- [Azure Event Hubs platformy Kafka narzędzia MirrorMaker uruchomionych w wystąpieniu kontenera platformy Azure](https://github.com/djrosanova/EventHubsMirrorMaker)

## <a name="next-steps"></a>Kolejne kroki

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie przestrzeni nazw usługi Event Hubs
> * Klonowanie projektu przykładowego
> * Konfigurowanie klastra Kafka
> * Konfigurowanie narzędzia MirrorMaker platformy Kafka
> * Uruchom narzędzia MirrorMaker platformy Kafka

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
