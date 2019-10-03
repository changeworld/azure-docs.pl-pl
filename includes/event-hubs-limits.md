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
ms.openlocfilehash: a20481ea42a0772ab42322e912cfce67877734d6
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71830140"
---
W poniższych tabelach przedstawiono limity przydziału i limity dotyczące [usługi Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). Aby uzyskać informacje na temat cennika Event Hubs, zobacz [Cennik usługi Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

W warstwach Podstawowa, standardowa i dedykowana są wspólne następujące limity. 

| Limit | Zakres | Uwagi | Wartość |
| --- | --- | --- | --- |
| Liczba przestrzeni nazw Event Hubs na subskrypcję |Subskrypcja |- |100 |
| Liczba centrów zdarzeń na przestrzeń nazw |Przestrzeń nazw |Kolejne żądania utworzenia nowego centrum zdarzeń są odrzucane. |10 |
| Liczba partycji na centrum zdarzeń |Jednostka |- |32 |
| Maksymalny rozmiar nazwy centrum zdarzeń |Jednostka |- |50 znaków |
| Liczba odbiorników niezwiązanych z nieepoką na grupę konsumentów |Jednostka |- |5 |
| Maksymalna liczba jednostek przepływności |Przestrzeń nazw |Przekroczenie limitu jednostek przepływności powoduje ograniczenie danych i wygenerowanie [wyjątku zajętego serwera](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception). Aby zażądać większej liczby jednostek przepływności dla warstwy Standardowa, należy [wysłać żądanie pomocy technicznej](/azure/azure-supportability/how-to-create-azure-support-request). [Dodatkowe jednostki przepływności](../articles/event-hubs/event-hubs-auto-inflate.md) są dostępne w blokach od 20 na podstawie zatwierdzonego zakupu. |20 |
| Liczba reguł autoryzacji na przestrzeń nazw |Przestrzeń nazw|Kolejne żądania utworzenia reguły autoryzacji są odrzucane.|12 |
| Liczba wywołań metody GetRuntimeInformation — | Jednostka | - | 50 na sekundę | 
| Liczba reguł sieci wirtualnej (VNet) i konfiguracji adresów IP | Jednostka | - | 128 | 

### <a name="event-hubs-basic-and-standard---quotas-and-limits"></a>Event Hubs podstawowe i standardowe przydziały i limity
| Limit | Zakres | Uwagi | Basic | Standardowa (Standard) |
| --- | --- | --- | -- | --- |
| Maksymalny rozmiar zdarzenia Event Hubs|Jednostka | &nbsp; | 256 KB | 1 MB |
| Liczba grup odbiorców na centrum zdarzeń |Jednostka | &nbsp; |1 |20 |
| Liczba połączeń AMQP na przestrzeń nazw |Przestrzeń nazw |Kolejne żądania dla dodatkowych połączeń są odrzucane i występuje wyjątek przez wywoływany kod. |100 |5000|
| Maksymalny okres przechowywania danych zdarzenia |Jednostka | &nbsp; |1 dzień |1-7 dni |
|Apache Kafka włączona przestrzeń nazw|Przestrzeń nazw |Event Hubs przestrzeni nazw strumieniuje aplikacje przy użyciu protokołu Kafka |Nie | Tak |
|Przechwytywanie |Jednostka | Gdy ta funkcja jest włączona, mikro partie w tym samym strumieniu |Nie |Tak |


### <a name="event-hubs-dedicated---quotas-and-limits"></a>Event Hubs — warstwa Dedykowana — przydziały i limity
W przypadku oferty Event Hubs — warstwa Dedykowana jest naliczana stała cena miesięczna, a co najmniej 4 godziny użytkowania. Warstwa dedykowana oferuje wszystkie funkcje planu Standard, ale z możliwością skalowania w przedsiębiorstwie i limitami dla klientów wymagających obciążeń. 

| Funkcja | Limity |
| --- | ---|
| Przepustowość |  20 jednostek |
| Przestrzenie nazw | 50 na CU |
| Centra zdarzeń |  1000 na przestrzeń nazw |
| Zdarzenia związane z transferem danych przychodzących | Zawarte |
| Rozmiar komunikatu | 1 MB |
| Partycje | 2000 na CU |
| Grupy klientów | Brak limitu na wartość CU, 1000 na centrum zdarzeń |
| Połączenia obsługiwane przez brokera | 100 K uwzględnionych |
| Przechowywanie komunikatów | Do 7 dni, 10 TB uwzględnionych na CU |
| Przechwytywanie | Zawarte |
