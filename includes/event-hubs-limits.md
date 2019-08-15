---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: event-hubs
author: sethmanheim
ms.service: event-hubs
ms.topic: include
ms.date: 05/22/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: c163e3cce862640d43f8696dca4eeef29f2ae12a
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68912450"
---
W poniższej tabeli przedstawiono limity przydziału i limity dotyczące [usługi Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). Aby uzyskać informacje na temat cennika Event Hubs, zobacz [Cennik usługi Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

| Limit | Scope | Uwagi | Value |
| --- | --- | --- | --- |
| Liczba przestrzeni nazw Event Hubs na subskrypcję |Subscription |- |100 |
| Liczba centrów zdarzeń na przestrzeń nazw |Przestrzeń nazw |Kolejne żądania utworzenia nowego centrum zdarzeń są odrzucane. |10 |
| Liczba partycji na centrum zdarzeń |Jednostka |- |32 |
| Maksymalny rozmiar zdarzenia Event Hubs|Jednostka |- |1 MB |
| Maksymalny rozmiar nazwy centrum zdarzeń |Jednostka |- |50 znaków |
| Liczba odbiorników niezwiązanych z nieepoką na grupę konsumentów |Jednostka |- |5 |
| Maksymalna liczba jednostek przepływności |Przestrzeń nazw |Przekroczenie limitu jednostek przepływności powoduje ograniczenie danych i wygenerowanie [wyjątku zajętego serwera](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception). Aby zażądać większej liczby jednostek przepływności dla warstwy Standardowa, należy [wysłać żądanie pomocy technicznej](/azure/azure-supportability/how-to-create-azure-support-request). [Dodatkowe jednostki przepływności](../articles/event-hubs/event-hubs-auto-inflate.md) są dostępne w blokach od 20 na podstawie zatwierdzonego zakupu. |20 |
| Liczba reguł autoryzacji na przestrzeń nazw |Przestrzeń nazw|Kolejne żądania utworzenia reguły autoryzacji są odrzucane.|12 |
| Liczba wywołań metody GetRuntimeInformation — | Jednostka | - | 50 na sekundę | 
| Liczba reguł sieci wirtualnej (VNet) i konfiguracji adresów IP | Jednostka | - | 128 | 

### <a name="event-hubs-basic-and-standard---quotas-and-limits"></a>Event Hubs podstawowe i standardowe przydziały i limity
| Limit | Scope | Uwagi | Podstawowa | Standardowa (Standard) |
| --- | --- | --- | -- | --- |
| Liczba grup odbiorców na centrum zdarzeń |Jednostka | - |1 |20 |
| Liczba połączeń AMQP na przestrzeń nazw |Przestrzeń nazw |Kolejne żądania dla dodatkowych połączeń są odrzucane i występuje wyjątek przez wywoływany kod. |100 |5,000|
| Maksymalny okres przechowywania danych zdarzenia |Jednostka | - |1 dzień |1-7 dni |
|Apache Kafka włączona przestrzeń nazw|Przestrzeń nazw |Event Hubs przestrzeni nazw strumieniuje aplikacje przy użyciu protokołu Kafka |Nie | Tak |
|Przechwytywanie |Jednostka | Gdy ta funkcja jest włączona, mikro partie w tym samym strumieniu |Nie |Tak |


### <a name="event-hubs-dedicated---quotas-and-limits"></a>Event Hubs — warstwa Dedykowana — przydziały i limity
W przypadku oferty Event Hubs — warstwa Dedykowana jest naliczana stała cena miesięczna, a co najmniej 4 godziny użytkowania. Warstwa dedykowana oferuje wszystkie funkcje planu Standard, ale z możliwością skalowania w przedsiębiorstwie i limitami dla klientów wymagających obciążeń. 

| Cecha | Limity |
| --- | ---|
| Przepustowość |  20 jednostek |
| Przestrzenie nazw | 50 na CU |
| Event Hubs |  1000 na przestrzeń nazw |
| Zdarzenia związane z transferem danych przychodzących | Dołączono |
| Rozmiar komunikatu | 1 000 000 bajtów |
| Partycje | 2000 na CU |
| Grupy odbiorców | Brak limitu na wartość CU, 1000 na centrum zdarzeń |
| Połączenia obsługiwane przez brokera | 100 tys. w cenie |
| Przechowywanie komunikatów | Do 7 dni (wkrótce 90 dni), 10 TB uwzględnionych na CU |
| Przechwytywanie | Dołączono |
