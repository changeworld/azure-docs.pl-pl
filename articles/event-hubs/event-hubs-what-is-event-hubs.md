---
title: Co to jest usługa Azure Event Hubs i dlaczego warto jej używać | Microsoft Docs
description: Omówienie i wprowadzenie do usługi Azure Event Hubs — zbieranie danych telemetrycznych z witryn sieci Web, aplikacji i urządzeń w skali chmury
services: event-hubs
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/27/2018
ms.author: sethm
ms.openlocfilehash: 08b78f59dc758f12556f332546ccb6043bef2812
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2018
ms.locfileid: "29686909"
---
# <a name="what-is-event-hubs"></a>Co to jest usługa Event Hubs?

Azure Event Hubs to wysoce skalowalna platforma do pobierania zdarzeń i strumieniowego przesyłania danych, która umożliwia odbieranie i przetwarzanie milionów zdarzeń na sekundę. Usługa Event Hubs pozwala przetwarzać i przechowywać zdarzenia, dane lub dane telemetryczne generowane przez rozproszone oprogramowanie i urządzenia. Dane wysłane do centrum zdarzeń mogą zostać przekształcone i zmagazynowane przy użyciu dowolnego dostawcy analityki czasu rzeczywistego lub adapterów przetwarzania wsadowego/magazynowania. Dzięki udostępnieniu [możliwości publikowania/subskrybowania](/biztalk/core/publish-and-subscribe-architecture) z niskim opóźnieniem i na bardzo dużą skalę usługa Event Hubs służy jako „wjazd” dla danych big data.

Usługa Event Hubs umożliwia na przykład śledzenie zachowania w aplikacjach mobilnych, gromadzenie informacji o ruchu z farmy serwerów w Internecie, przechwytywanie zdarzeń w grach na konsole i zbieranie danych telemetrycznych z maszyn przemysłowych, podłączonych pojazdów lub innych urządzeń.

## <a name="azure-event-hubs-overview"></a>Omówienie usługi Azure Event Hubs

Typową rolą pełnioną przez usługę Event Hubs w architekturze rozwiązań są „drzwi wejściowe” dla potoku zdarzeń. Jest ona często nazywana *systemem zbierania zdarzeń*. System zbierania zdarzeń to składnik lub usługa, która znajduje się między wydawcami zdarzeń, a ich odbiorcami. Jego rolą jest oddzielenie procesu tworzenia strumienia zdarzeń od użycia tych zdarzeń. Tę architekturę przedstawiono na poniższym rysunku:

![Event Hubs](./media/event-hubs-what-is-event-hubs/event_hubs_full_pipeline.png)

Usługa Event Hubs umożliwia obsługę strumienia komunikatów, ale jej właściwości różnią się od tradycyjnych metod przesyłania komunikatów w przedsiębiorstwie. Możliwości usługi Event Hubs są zbudowane wokół scenariuszy wysokiej przepływności i przetwarzania zdarzeń. Z tego względu działanie usługi Event Hubs różni się od obsługi komunikatów w usłudze [Azure Service Bus](https://azure.microsoft.com/services/service-bus/) — nie są implementowane niektóre funkcje dostępne w przypadku jednostek [obsługi komunikatów usługi Service Bus](/azure/service-bus-messaging/), takie jak tematy.

## <a name="event-hubs-features"></a>Funkcje usługi Event Hubs

Usługa Event Hubs zawiera następujące kluczowe elementy:

- [**Producenci/wydawcy zdarzeń**](event-hubs-features.md#event-publishers): jednostka, która wysyła dane do centrum zdarzeń. Zdarzenia są publikowane za pośrednictwem protokołu AMQP 1.0 lub HTTPS.
- [**Funkcja przechwytywania**](event-hubs-features.md#capture): umożliwia przechwytywanie danych przesyłanych strumieniowo z usługi Event Hubs i przechowywanie ich na koncie usługi Azure Blob Storage.
- [**Partycje**](event-hubs-features.md#partitions): umożliwiają poszczególnym użytkownikom odczyt tylko konkretnego podzestawu lub partycji strumienia zdarzeń.
- [**Tokeny sygnatur dostępu współdzielonego**](event-hubs-features.md#sas-tokens): umożliwiają identyfikowanie i uwierzytelnianie wydawcy zdarzeń.
- [**Odbiorca zdarzenia**](event-hubs-features.md#event-consumers): jednostka, która odczytuje dane zdarzenia z centrum zdarzeń. Odbiorcy zdarzenia nawiązują połączenia za pomocą protokołu AMQP 1.0. 
- [**Grupy odbiorców**](event-hubs-features.md#consumer-groups): udostępniają poszczególnym aplikacjom odbiorczym osobne widoki strumienia zdarzeń, co pozwala odbiorcom działać niezależnie.
- [**Jednostki przepływności**](event-hubs-features.md#capacity): zakupione wcześniej jednostki wydajności. Jedna partycja ma maksymalną skalę wynoszącą jedną jednostkę przepływności.

Aby uzyskać szczegółowe informacje techniczne na temat tych i innych funkcji usługi Event Hubs, zobacz [Event Hubs features overview (Omówienie funkcji usługi Event Hubs)](event-hubs-features.md). 

## <a name="next-steps"></a>Następne kroki

Aby uzyskać szczegółowe informacje o cenach za korzystanie z usługi Event Hubs, zobacz [Usługa Event Hubs — cennik](https://azure.microsoft.com/pricing/details/event-hubs/).

Aby uzyskać więcej informacji na temat usługi Event Hubs, skorzystaj z następujących linków:

* Rozpocznij pracę od [samouczka dotyczącego usługi Event Hubs](event-hubs-dotnet-standard-getstarted-send.md)
* [Event Hubs — często zadawane pytania](event-hubs-faq.md)
* [Przykładowe aplikacje korzystające z usługi Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)
 
 

