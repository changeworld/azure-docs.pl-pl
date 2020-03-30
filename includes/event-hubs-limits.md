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
ms.openlocfilehash: 2aca4f2c236112b80e9fc985cf80ccad6d82bde3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75901694"
---
Poniższe tabele zawierają przydziały i limity specyficzne dla [usługi Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). Aby uzyskać informacje na temat cenowania centrów zdarzeń, zobacz [— ceny w centrach zdarzeń](https://azure.microsoft.com/pricing/details/event-hubs/).

Następujące limity są wspólne dla warstw podstawowych, standardowych i dedykowanych. 

| Limit | Zakres | Uwagi | Wartość |
| --- | --- | --- | --- |
| Liczba obszarów nazw centrów zdarzeń na subskrypcję |Subskrypcja |- |100 |
| Liczba centrów zdarzeń na obszar nazw |Przestrzeń nazw |Kolejne żądania utworzenia nowego centrum zdarzeń są odrzucane. |10 |
| Liczba partycji na centrum zdarzeń |Jednostka |- |32 |
| Maksymalny rozmiar nazwy centrum zdarzeń |Jednostka |- |50 znaków |
| Liczba odbiorników niebędących epokami na grupę odbiorców |Jednostka |- |5 |
| Maksymalna przepustowość jednostek |Przestrzeń nazw |Przekroczenie limitu jednostki przepływności powoduje, że dane mają być ograniczane i generuje [wyjątek zajęty serwerem.](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) Aby zażądać większej liczby jednostek przepływności dla warstwy Standardowa, złóż [żądanie pomocy technicznej](/azure/azure-portal/supportability/how-to-create-azure-support-request). [Dodatkowe jednostki przepływności](../articles/event-hubs/event-hubs-auto-inflate.md) są dostępne w blokach po 20 na podstawie zatwierdzonego zakupu. |20 |
| Liczba reguł autoryzacji na obszar nazw |Przestrzeń nazw|Kolejne żądania tworzenia reguł autoryzacji są odrzucane.|12 |
| Liczba wywołań metody GetRuntimeInformation | Jednostka | - | 50 na sekundę | 
| Liczba reguł sieci wirtualnej (VNet) i ip config | Jednostka | - | 128 | 

### <a name="event-hubs-basic-and-standard---quotas-and-limits"></a>Centra zdarzeń podstawowe i standardowe — przydziały i limity
| Limit | Zakres | Uwagi | Podstawowa (Basic) | Standardowa |
| --- | --- | --- | -- | --- |
| Maksymalny rozmiar zdarzenia Centrum zdarzeń|Jednostka | &nbsp; | 256 KB | 1 MB |
| Liczba grup odbiorców na centrum zdarzeń |Jednostka | &nbsp; |1 |20 |
| Liczba połączeń AMQP na obszar nazw |Przestrzeń nazw |Kolejne żądania dotyczące dodatkowych połączeń są odrzucane, a wyjątek jest odbierany przez kod wywołujący. |100 |5000|
| Maksymalny okres przechowywania danych zdarzeń |Jednostka | &nbsp; |1 dzień |1-7 dni |
|Apache Kafka włączone przestrzeni nazw|Przestrzeń nazw |Obszar nazw Centrum zdarzeń strumieniuje aplikacje przy użyciu protokołu Kafka |Nie | Tak |
|Przechwytywanie |Jednostka | Po włączeniu mikro-partie na tym samym strumieniu |Nie |Tak |


### <a name="event-hubs-dedicated---quotas-and-limits"></a>Usługi Event Hubs Dedicated — przydziały i limity
Oferta dedykowana centrum zdarzeń jest rozliczana po stałej cenie miesięcznej, przy minimalnym 4 godzinach użytkowania. Warstwa dedykowana oferuje wszystkie funkcje planu Standardowego, ale z pojemnością i limitami skali przedsiębiorstwa dla klientów z wymagającymi obciążeniami. 

| Funkcja | Limity |
| --- | ---|
| Przepustowość |  20 j.p. |
| Namespaces | 50 na CU |
| Usługa Event Hubs |  1000 na obszar nazw |
| Zdarzenia przychodzące | Dołączono |
| Rozmiar wiadomości | 1 MB |
| Partycje | 2000 na CU |
| Grupy odbiorców | Brak limitu na CU, 1000 na koncentrator zdarzeń |
| Połączenia brokerskie | 100 K w zestawie |
| Przechowywanie wiadomości | 90 dni, 10 TB w zestawie na CU |
| Przechwytywanie | Dołączono |
