---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: event-hubs
author: sethmanheim
ms.service: event-hubs
ms.topic: include
ms.date: 02/26/2018
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: cd64bdabc2b7b34687296c855c27882925d80f63
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/18/2019
ms.locfileid: "58124413"
---
W poniższej tabeli przedstawiono limity przydziału i limity specyficzne dla [usługi Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). Aby uzyskać informacje na temat cen usługi Event Hubs, zobacz [cen usługi Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

| Limit | Zakres | Uwagi | Wartość |
| --- | --- | --- | --- |
| Liczba obszarów nazw usługi Event Hubs na subskrypcję |Subskrypcja |- |1000 |
| Liczba zdarzeń centra na przestrzeń nazw |Przestrzeń nazw |Kolejne żądania do tworzenia nowego Centrum zdarzeń są odrzucane. |10 |
| Liczba partycji na Centrum zdarzeń |Jednostka |- |32 |
| Liczba grup odbiorców dla Centrum zdarzeń |Jednostka |- |20 |
| Liczba połączeń AMQP na przestrzeń nazw |Przestrzeń nazw |Kolejne żądania dla dodatkowych połączeń są odrzucane, a wyjątek jest odbierany przez kod wywołujący. |5000 |
| Maksymalny rozmiar zdarzeń usługi Event Hubs|Jednostka |- |1 MB |
| Maksymalny rozmiar nazwy Centrum zdarzeń |Jednostka |- |50 znaków |
| Liczba innych niż epoka odbiorców dla każdej grupy odbiorców |Jednostka |- |5 |
| Maksymalny okres przechowywania danych zdarzenia |Jednostka |- |1-7 dni |
| Maksymalna liczba jednostek przepływności |Przestrzeń nazw |Przekroczenie limitu jednostek przepływności powoduje, że Twoje dane ograniczona i generuje [wyjątek zajęty serwer](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception). Aby zamówić większą liczbę jednostek przepływności dla wersji standardowa, pliku [żądania pomocy technicznej](/azure/azure-supportability/how-to-create-azure-support-request). [Jednostki przepływności dodatkowe](../articles/event-hubs/event-hubs-auto-inflate.md) są dostępne w blokach po 20 na podstawie zobowiązania. |20 |
| Liczba reguł autoryzacji dla przestrzeni nazw |Przestrzeń nazw|Kolejne żądania tworzenia reguły autoryzacji są odrzucane.|12 |
