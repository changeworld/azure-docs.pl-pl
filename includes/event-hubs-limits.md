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
ms.openlocfilehash: fa6b4d6d0db09f8c4955430d6dc227356416d915
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735967"
---
W poniższej tabeli przedstawiono limity przydziału i limity specyficzne dla [usługi Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). Aby uzyskać informacje na temat cen usługi Event Hubs, zobacz [cen usługi Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

| Limit | Scope | Uwagi | Wartość |
| --- | --- | --- | --- |
| Liczba obszarów nazw usługi Event Hubs na subskrypcję |Subskrypcja |- |100 |
| Liczba zdarzeń centra na przestrzeń nazw |Przestrzeń nazw |Kolejne żądania do tworzenia nowego Centrum zdarzeń są odrzucane. |10 |
| Liczba partycji na Centrum zdarzeń |Jednostka |- |32 |
| Liczba grup odbiorców dla Centrum zdarzeń |Jednostka |- |20 |
| Liczba połączeń AMQP na przestrzeń nazw |Przestrzeń nazw |Kolejne żądania dla dodatkowych połączeń są odrzucane, a wyjątek jest odbierany przez kod wywołujący. |5,000 |
| Maksymalny rozmiar zdarzeń usługi Event Hubs|Jednostka |- |1 MB |
| Maksymalny rozmiar nazwy Centrum zdarzeń |Jednostka |- |50 znaków |
| Liczba innych niż epoka odbiorców dla każdej grupy odbiorców |Jednostka |- |5 |
| Maksymalny okres przechowywania danych zdarzenia |Jednostka |- |1-7 dni |
| Maksymalna liczba jednostek przepływności |Przestrzeń nazw |Przekroczenie limitu jednostek przepływności powoduje, że Twoje dane ograniczona i generuje [wyjątek zajęty serwer](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception). Aby zamówić większą liczbę jednostek przepływności dla wersji standardowa, pliku [żądania pomocy technicznej](/azure/azure-supportability/how-to-create-azure-support-request). [Jednostki przepływności dodatkowe](../articles/event-hubs/event-hubs-auto-inflate.md) są dostępne w blokach po 20 na podstawie zobowiązania. |20 |
| Liczba reguł autoryzacji dla przestrzeni nazw |Przestrzeń nazw|Kolejne żądania tworzenia reguły autoryzacji są odrzucane.|12 |
| Liczba wywołań getruntimeinformation — metoda | Jednostka | - | 50 na sekundę | 

### <a name="event-hubs-dedicated---quotas-and-limits"></a>Zdarzenie Hubs Dedicated — limity przydziału i ograniczenia
Event hubs w warstwie dedykowana oferta jest rozliczana w stałej miesięcznej cenie, co najmniej 4 godziny użycia. Dedykowane warstwy są dostępne wszystkie funkcje plan w warstwie standardowa, ale o pojemności Skala przedsiębiorstwa i limity dla klientów wymagających obciążeń. 

| Cecha | Limits |
| --- | ---|
| Przepustowość |  20 jednostek pojemności |
| Przestrzenie nazw | 50 na jednostkę pojemności |
| Event Hubs |  1000 na przestrzeń nazw |
| Zdarzenia związane z transferem danych przychodzących | Dołączono |
| Rozmiar komunikatu | 1 milion bajtów |
| Partycje | 2000 na jednostkę pojemności |
| Grupy odbiorców | Dopuszczalne na jednostkę pojemności, 1000 na Centrum zdarzeń |
| Połączenia obsługiwane przez brokera | 100 tys. w cenie |
| Przechowywanie komunikatów | Się do 7 dni (90-dniowa przechowywania wkrótce), 10 TB uwzględnione na jednostkę pojemności |
| Przechwytywanie | Dołączono |
