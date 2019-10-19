---
title: Korzystanie z centrum zdarzeń w programie Apache Kafka App — Azure Event Hubs | Microsoft Docs
description: Ten artykuł zawiera informacje na temat obsługi Apache Kafka przez usługę Azure Event Hubs.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: d3271d6e8cb7d423e1dccd235b244688e7ab5683
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555795"
---
# <a name="use-azure-event-hubs-from-apache-kafka-applications"></a>Korzystanie z Event Hubs platformy Azure z aplikacji Apache Kafka
Event Hubs udostępnia punkt końcowy Kafka, który może być używany przez istniejące aplikacje oparte na Kafka jako alternatywę dla uruchamiania własnego klastra Kafka. Event Hubs obsługuje [protokół Apache Kafka 1,0 i nowsze](https://kafka.apache.org/documentation/)i współpracuje z istniejącymi aplikacjami Kafka, w tym narzędzia MirrorMaker.  

## <a name="what-does-event-hubs-for-kafka-provide"></a>Co zapewnia Event Hubs Kafka?

Funkcja Event Hubs for Kafka udostępnia nagłówek protokołu w oparciu o usługę Azure Event Hubs, która jest binarna zgodna z Kafka wersjami 1,0 i nowszymi w przypadku odczytywania i zapisywania w tematach Kafka. Możesz rozpocząć korzystanie z punktu końcowego Kafka z aplikacji bez zmiany kodu, ale minimalnej zmiany konfiguracji. Należy zaktualizować parametry połączenia w konfiguracjach, aby wskazywały punkt końcowy Kafka uwidoczniony przez centrum zdarzeń zamiast wskazywać klaster Kafka. Następnie można rozpocząć przesyłanie strumieniowe zdarzeń z aplikacji korzystających z protokołu Kafka do Event Hubs. Ta integracja obsługuje również platformy, takie jak [Kafka Connect](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect), które są obecnie dostępne w wersji zapoznawczej. 

Kafka koncepcyjnie i Event Hubs są niemal identyczne: są to dzienniki partycjonowane dla danych przesyłanych strumieniowo. W poniższej tabeli przedstawiono koncepcje między Kafka i Event Hubs.

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Mapowanie koncepcyjne Kafka i centrum zdarzeń

| Koncepcja Kafka | Koncepcja Event Hubs|
| --- | --- |
| Klaster | Przestrzeń nazw |
| Temat | Centrum zdarzeń |
| Partycja | Partycja|
| Grupa konsumentów | Grupa konsumentów |
| Przesunięcie | Przesunięcie|

### <a name="key-differences-between-kafka-and-event-hubs"></a>Kluczowe różnice między Kafka i Event Hubs

Gdy [Apache Kafka](https://kafka.apache.org/) to oprogramowanie, które można uruchomić w dowolnym miejscu, Event Hubs jest usługą w chmurze podobną do BLOB Storage platformy Azure. Brak serwerów lub sieci do zarządzania i nie ma żadnych brokerów do skonfigurowania. Tworzysz przestrzeń nazw, która jest nazwą FQDN, w której znajdują się tematy, a następnie utwórz Event Hubs lub tematy w tej przestrzeni nazw. Aby uzyskać więcej informacji na temat Event Hubs i przestrzeni nazw, zobacz [Event Hubs Features](event-hubs-features.md#namespace). Jako usługa w chmurze Event Hubs używa jednego stabilnego wirtualnego adresu IP jako punktu końcowego, dlatego klienci nie muszą znać brokerów ani maszyn w klastrze. 

Skalowanie w Event Hubs jest kontrolowane przez liczbę jednostek przepływności, które można kupić, a każda jednostka przepływności entitling do 1 MB na sekundę lub 1000 zdarzeń na sekundę. Domyślnie Event Hubs skaluje jednostki przepływności po osiągnięciu limitu przez funkcję [autostartu](event-hubs-auto-inflate.md) . Ta funkcja działa również z funkcją Event Hubs for Kafka. 

### <a name="security-and-authentication"></a>Zabezpieczenia i uwierzytelnianie

Usługa Azure Event Hubs wymaga protokołu SSL lub TLS dla całej komunikacji i używa sygnatur dostępu współdzielonego (SAS) do uwierzytelniania. To wymaganie ma również wartość true dla punktu końcowego Kafka w Event Hubs. W celu zapewnienia zgodności z programem Kafka Event Hubs używa SASL PLAIN do uwierzytelniania i protokołu SASL SSL na potrzeby zabezpieczeń transportu. Aby uzyskać więcej informacji na temat zabezpieczeń w Event Hubs, zobacz [Event Hubs Authentication and Security](event-hubs-authentication-and-security-model-overview.md).

## <a name="other-event-hubs-features-available-for-kafka"></a>Inne funkcje Event Hubs dostępne dla Kafka

Funkcja Event Hubs for Kafka umożliwia pisanie przy użyciu jednego protokołu i odczytywanie go w inny sposób, dzięki czemu aktualni producenci Kafka mogą kontynuować publikowanie za pośrednictwem Kafkau i można dodać czytelników z Event Hubs, takimi jak Azure Stream Analytics lub Azure Functions. Ponadto funkcje Event Hubs, takie jak [przechwytywanie](event-hubs-capture-overview.md) i replikacja [geograficzna](event-hubs-geo-dr.md) , działają również z funkcją Event Hubs for Kafka.

## <a name="features-that-are-not-yet-supported"></a>Funkcje, które nie są jeszcze obsługiwane 

Poniżej znajduje się lista funkcji Kafka, które nie są jeszcze obsługiwane:

*   Producent idempotentne
*   Transaction
*   Kompresja
*   Przechowywanie na podstawie rozmiaru
*   Kompaktowanie dziennika
*   Dodawanie partycji do istniejącego tematu
*   Obsługa interfejsu API HTTP Kafka
*   Kafka strumienie

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono wprowadzenie do Event Hubs Kafka. Aby dowiedzieć się więcej, zobacz następujące linki:

- [Jak utworzyć centra Event Hubs z obsługą platformy Kafka](event-hubs-create-kafka-enabled.md)
- [Przesyłanie strumieniowe do usługi Event Hubs z aplikacji platformy Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Dublowanie brokera platformy Kafka w centrum zdarzeń z obsługą platformy Kafka](event-hubs-kafka-mirror-maker-tutorial.md)
- [Łączenie platformy Apache Spark z centrum zdarzeń z obsługą platformy Kafka](event-hubs-kafka-spark-tutorial.md)
- [Łączenie platformy Apache Flink z centrum zdarzeń z obsługą platformy Kafka](event-hubs-kafka-flink-tutorial.md)
- [Integrowanie narzędzia Kafka Connect z centrum zdarzeń z obsługą platformy Kafka](event-hubs-kafka-connect-tutorial.md)
- [Łączenie biblioteki Akka Streams z centrum zdarzeń z obsługą platformy Kafka](event-hubs-kafka-akka-streams-tutorial.md)
- [Eksplorowanie przykładów w witrynie GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)


