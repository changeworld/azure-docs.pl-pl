---
title: Usługa Azure Event Hubs dla ekosystemami Kafka | Dokumentacja firmy Microsoft
description: Omówienie i wprowadzenie do Kafka włączone usługi Azure Event Hubs
services: event-hubs
documentationcenter: .net
author: djrosanova
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.date: 05/07/2018
ms.author: darosa
ms.openlocfilehash: 51e32737be4eaf3c61cdbe50b82a05c205800ac4
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2018
ms.locfileid: "34077610"
---
# <a name="event-hubs-for-kafka-ecosystems"></a>Centra zdarzeń dla ekosystemami Kafka

Centra zdarzeń dla ekosystemami Kafka zapewnia Kafka punktu końcowego, który może być używany przez istniejącą Kafka aplikacji opartych na zamiast uruchamiania klastra Kafka. Centra zdarzeń dla obsługuje ekosystem Kafka [Apache Kafka 1.0](https://kafka.apache.org/10/documentation.html) i nowsze wersje klienta, a także współpracuje z istniejącymi aplikacjami Kafka, w tym MirrorMaker. Zmień parametry połączenia i Rozpocznij przesyłanie strumieniowe zdarzenia z aplikacji korzystających z protokołu Kafka w usłudze Event Hubs.

## <a name="what-does-event-hubs-for-kafka-ecosystems-provide"></a>Co oferuje centra zdarzeń dla ekosystemami Kafka?

Centra zdarzeń dla ekosystemu Kafka zawiera ponad usługi Azure Event Hubs jest binarny zgodny z wersjami Kafka 1.0 i nowszymi zarówno odczytywanie z oraz zapisywanie do tematów Kafka head protokołu. Koncepcyjnie Kafka i usługi Event Hubs są niemal identyczne: znajdują się zarówno partycjonowanej dzienniki skompilowany dla strumienia danych. Poniższa tabela mapuje pojęcia między Kafka i usługi Event Hubs.

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Mapowanie koncepcyjnej Kafka i Centrum zdarzeń

| Koncepcja Kafka | Koncepcja centra zdarzeń|
| --- | --- |
| Klaster | Przestrzeń nazw |
| Temat | Event Hubs |
| Partycja | Partycja|
| Grupa konsumentów | Grupa konsumentów |
| Przesunięcie | Przesunięcie|

### <a name="key-differences-between-kafka-and-event-hubs"></a>Podstawowe różnice między Kafka i usługi Event Hubs

Gdy [Apache Kafka](https://kafka.apache.org/) to oprogramowanie, które można uruchomić wszędzie tam, gdzie możesz wybrać, usługa Event Hubs to usługa w chmurze podobne do magazynu obiektów Blob Azure. Istnieją żadne serwery lub sieci do zarządzania i nie brokerzy do skonfigurowania. Tworzenie przestrzeni nazw, która jest nazwą FQDN, w którym będzie funkcjonować tematów, a następnie utwórz centra zdarzeń i tematy w tej przestrzeni nazw. Aby uzyskać więcej informacji na temat usługi Event Hubs i przestrzenie nazw, zobacz [co to jest usługa Event Hubs](event-hubs-what-is-event-hubs.md). Jako usługa w chmurze usługa Event Hubs używa pojedynczego stabilna wirtualnego adresu IP jako punkt końcowy, więc klienci nie muszą wiedzieć o brokerzy lub komputerów w klastrze. 

Skali w usłudze Event Hubs jest kontrolowana przez liczbę jednostek przepływności zakupu, z każdej jednostki przepływności uprawniających należy do 1 MB na sekundę lub 1000 zdarzeń na sekundę wejściowych. Domyślnie usługi Event Hubs skaluje jednostek przepływności po osiągnięciu limitu z [zwiększyć automatycznie](event-hubs-auto-inflate.md) funkcji; ten działania z usługą Event Hubs dla ekosystemami Kafka również funkcji. 

### <a name="security-and-authentication"></a>Zabezpieczenia i uwierzytelnianie

Usługa Azure Event Hubs wymaga protokołu SSL lub TLS dla całej komunikacji i użyje do uwierzytelniania dostępu do sygnatur dostępu Współdzielonego. To wymaganie dotyczy również punkt końcowy Kafka w ramach usługi Event Hubs. Zgodność z Kafka usługa Event Hubs używa zwykłego SASL uwierzytelniania i protokołu SSL SASL zabezpieczeń transportu. Aby uzyskać więcej informacji na temat zabezpieczeń w usłudze Event Hubs, zobacz [uwierzytelniania usługi Event Hubs i zabezpieczeń](event-hubs-authentication-and-security-model-overview.md).

## <a name="other-event-hubs-features-available-for-kafka"></a>Inne dostępne dla Kafka funkcje usługi Event Hubs

Centra zdarzeń dla ekosystemu Kafka umożliwia zapis z jednego protokołu i odczytu innym, z bieżącej producentów Kafka można kontynuować publikowanie za pomocą Kafka, a czytników z usługą Event Hubs, takich jak analiza strumienia Azure lub usługi Azure Functions można dodać. Ponadto usługa Event Hubs funkcjom, takim jak [przechwytywania](event-hubs-capture-overview.md) i [odzyskiwania po awarii geograficznie](event-hubs-geo-dr.md) również współpracować z centrów zdarzeń dla ekosystemami Kafka.

## <a name="features-that-are-not-supported-in-the-preview"></a>Funkcje, które nie są obsługiwane w wersji zapoznawczej

Następujące funkcje Kafka publicznej wersji zapoznawczej usługi Event Hubs Kafka ekosystemami integracji nie są obsługiwane:

*   Producent idempotentności
*   Transakcji
*   Kompresja
*   Przechowywania na podstawie rozmiaru
*   Kompaktowanie dziennika
*   Dodawanie partycji do istniejącego tematu
*   Obsługa HTTP Kafka interfejsu API
*   Kafka połączenia
*   Strumienie Kafka

## <a name="next-steps"></a>Kolejne kroki

W tym artykule przewidzianych wprowadzenie do usługi Event Hubs Kafka ekosystemami. Aby dowiedzieć się więcej, zobacz następujące linki:

* [Jak utworzyć Kafka włączone usługi Event Hubs](event-hubs-create-kafka-enabled.md)
* [Strumień do usługi Event Hubs aplikacje Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
* Rozpocznij pracę od [samouczka dotyczącego usługi Event Hubs](event-hubs-dotnet-standard-getstarted-send.md)
* [Event Hubs — często zadawane pytania](event-hubs-faq.md)

 
 

