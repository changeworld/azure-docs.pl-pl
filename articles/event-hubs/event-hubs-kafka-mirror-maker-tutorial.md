---
title: Przy użyciu Kafka MirrorMaker z usługą Azure Event Hubs dla ekosystemu Kafka | Dokumentacja firmy Microsoft
description: Użyj Kafka MirrorMaker dublowanego klastra Kafka w usłudze Event Hubs.
services: event-hubs
documentationcenter: .net
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.topic: mirror-maker
ms.custom: mvc
ms.date: 05/07/2018
ms.author: bahariri
ms.openlocfilehash: 0693fc2fff5735fb2b3c0a9b8f1d3d256746f40d
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35298325"
---
# <a name="using-kafka-mirrormaker-with-event-hubs-for-kafka-ecosystems"></a>Dla ekosystemami Kafka przy użyciu Kafka MirrorMaker z usługą Event Hubs

> [!NOTE]
> Ten przykład jest dostępny w witrynie [GitHub](https://github.com/Azure/azure-event-hubs)

Pierwsza kwestia głównych dla nowoczesnych chmury skalowania aplikacji jest możliwość aktualizacji, poprawić i zmienić infrastruktury bez przerywania obsługi. W tym samouczku przedstawiono sposób z Centrum zdarzeń z obsługą Kafka i Kafka MirrorMaker można zintegrować istniejący potok Kafka Azure Dublując"" Kafka strumień wejściowy w usłudze Event Hubs. 

Punkt końcowy usługi Azure Event Hubs Kafka umożliwia łączenie z usługi Azure Event hubs przy użyciu protokołu Kafka (tj. Kafka klientów). Zmieniając minimalnego aplikacji Kafka, możesz łączyć usługi Azure Event hubs i korzystać z zalet ekosystemu platformy Azure. Kafka włączone usługi Event Hubs obsługuje obecnie Kafka w wersji 1.0 lub nowszej.

W tym przykładzie przedstawiono sposób duplikatów broker Kafka w Centrum zdarzeń włączone Kafka, za pomocą Kafka MirrorMaker.

   ![MirrorMaker Kafka z usługą Event Hubs](./media/event-hubs-kafka-mirror-maker-tutorial/evnent-hubs-mirror-maker1.png)

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka, upewnij się, że masz:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* [Zestaw Java Development Kit (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
    * W systemie Ubuntu uruchom polecenie `apt-get install default-jdk`, aby zainstalować zestaw JDK.
    * Upewnij się, że zmienna środowiskowa JAVA_HOME wskazuje folder, w którym zainstalowano zestaw JDK.
* [Pobierz](http://maven.apache.org/download.cgi) i [zainstalować](http://maven.apache.org/install.html) archiwum binarne Maven
    * W systemie Ubuntu możesz uruchomić polecenie `apt-get install maven`, aby zainstalować narzędzie Maven.
* [Git](https://www.git-scm.com/downloads)
    * W systemie Ubuntu możesz uruchomić polecenie `sudo apt-get install git`, aby zainstalować usługę Git.

## <a name="create-an-event-hubs-namespace"></a>Tworzenie przestrzeni nazw usługi Event Hubs

Centra zdarzeń w przestrzeni nazw jest wymagany do wysyłania i odbierania z dowolnej usługi Event Hubs. Zobacz [tworzenie Kafka włączone Centrum zdarzeń](event-hubs-create.md) instrukcje dotyczące konfigurowania punktu końcowego Kafka centrów zdarzeń. Upewnij się, że skopiuj parametry połączenia centra zdarzeń w celu późniejszego użycia.

## <a name="clone-the-example-project"></a>Klonowanie przykładowy projekt

Teraz, gdy masz Kafka włączone parametry połączenia usługi Event Hubs, klonowanie repozytorium Azure Event Hubs i przejdź do `mirror-maker` podfolderów:

```shell
git clone https://github.com/Azure/azure-event-hubs.git
cd azure-event-hubs/samples/kafka/mirror-maker
```

## <a name="set-up-a-kafka-cluster"></a>Konfigurowanie klastra Kafka

Użyj [Przewodnik Szybki Start Kafka](https://kafka.apache.org/quickstart) do skonfigurowania klastra z odpowiednie ustawienia (lub użyć istniejącego klastra Kafka).

## <a name="kafka-mirrormaker"></a>Kafka MirrorMaker

Kafka MirrorMaker umożliwia "dublowania" strumienia. Podane źródłowego i docelowego klastrów Kafka, MirrorMaker zapewnia komunikaty wysyłane do klastra źródłowego są odbierane przez klastry źródłowym i docelowym. W tym przykładzie przedstawiono sposób duplikatów źródła Kafka klastra przy użyciu docelowego włączone Kafka zdarzenia koncentratora. Ten scenariusz może służyć do wysyłania danych z istniejącego potoku Kafka do usługi Event Hubs bez przerywania przepływu danych. 

Aby uzyskać bardziej szczegółowe informacje dotyczące Kafka MirrorMaker, zobacz [przewodnik Kafka lustrzane/MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330).

### <a name="configuration"></a>Konfigurowanie

Aby skonfigurować Kafka MirrorMaker, nadaj klastra Kafka jako konsumenta/źródła i z Centrum zdarzeń z obsługą Kafka producent/lokalizacją docelową.

#### <a name="consumer-configuration"></a>Konfiguracja klienta

Aktualizuj plik konfiguracji użytkownika `source-kafka.config`, który informuje MirrorMaker właściwości źródła Kafka klastra.

##### <a name="source-kafkaconfig"></a>kafka.config źródła

```xml
bootstrap.servers={SOURCE.KAFKA.IP.ADDRESS1}:{SOURCE.KAFKA.PORT1},{SOURCE.KAFKA.IP.ADDRESS2}:{SOURCE.KAFKA.PORT2},etc
group.id=example-mirrormaker-group
exclude.internal.topics=true
client.id=mirror_maker_consumer
```

#### <a name="producer-configuration"></a>Producent konfiguracji

Teraz zaktualizować pliku konfiguracji producent `mirror-eventhub.config`, który informuje MirrorMaker wysyłać dane zduplikowanych (lub "dublowany") do usługi Event Hubs. W szczególności zmiany `bootstrap.servers` i `sasl.jaas.config` wskaż punktu końcowego Kafka centrów zdarzeń. Usługa Event Hubs wymaga bezpiecznej komunikacji (SASL), w którym odbywa się przez ustawienie właściwości ostatnich trzech w następującej konfiguracji: 

##### <a name="mirror-eventhubconfig"></a>dublowany eventhub.config

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=mirror_maker_producer

#Required for Event Hubs
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-mirrormaker"></a>Uruchom MirrorMaker

Uruchom skrypt Kafka MirrorMaker z katalogu głównego katalogu Kafka przy użyciu plików nowo zaktualizowanej konfiguracji. Upewnij się, że skopiuj pliki konfiguracji do głównego katalogu Kafka albo zaktualizuj ich ścieżek w poniższym poleceniu.

```shell
bin/kafka-mirror-maker.sh --consumer.config source-kafka.config --num.streams 1 --producer.config mirror-eventhub.config --whitelist=".*"
```

Aby sprawdzić, czy zdarzenia zbliżają Centrum zdarzeń z obsługą Kafka, zobacz statystyki ruch przychodzący w [portalu Azure](https://azure.microsoft.com/features/azure-portal/), lub uruchom konsumenta względem Centrum zdarzeń.

MirrorMaker uruchomione zdarzenia wysyłane do źródła Kafka klastra są odbierane przez klaster Kafka i dublowane Kafka włączona usługa Centrum zdarzeń. Przy użyciu MirrorMaker i punkt końcowy Kafka centra zdarzeń, można migrować istniejący potok Kafka do usługi Azure Event Hubs zarządzane bez zmiany istniejącego klastra lub przerywania bez przepływu bieżących danych.

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się więcej na temat usługi Event Hubs](event-hubs-what-is-event-hubs.md)
* [Więcej informacji na temat usługi Event Hubs dla ekosystemu Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* Dowiedz się więcej o [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) do strumienia zdarzeń od Kafka lokalne Kafka włączone centra zdarzeń w chmurze.
* Dowiedz się, jak strumienia do Kafka włączone za pomocą usługi Event Hubs [natywnych aplikacji Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md), [Apache Flink](event-hubs-kafka-flink-tutorial.md), lub [strumieni Akka](event-hubs-kafka-akka-streams-tutorial.md).
