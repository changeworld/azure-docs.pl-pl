---
title: Zrozumienie niestandardowe punkty końcowe Centrum IoT Azure | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera — korzystanie z reguł routingu do kierowania wiadomości urządzenia do chmury do niestandardowe punkty końcowe.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/09/2018
ms.author: dobett
ms.openlocfilehash: b035c7ef6dfe56c4b4534e081e70d95ea7c14847
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2018
ms.locfileid: "34808030"
---
# <a name="use-message-routes-and-custom-endpoints-for-device-to-cloud-messages"></a>Urządzenia do chmury wiadomości wysyłanych tras wiadomości i niestandardowe punkty końcowe

Centrum IoT umożliwia trasy [wiadomości urządzenia do chmury] [ lnk-device-to-cloud] do punktów końcowych usługi połączonej Centrum IoT na podstawie właściwości komunikatu. Reguły routingu zapewniają elastyczność do wysyłania wiadomości gdy muszą przejść bez konieczności stosowania dodatkowych usług lub kodu niestandardowego. Reguł routingu, które można skonfigurować ma następujące właściwości:

| Właściwość      | Opis |
| ------------- | ----------- |
| **Nazwa**      | Unikatowa nazwa identyfikuje reguły. |
| **Źródło**    | Pochodzenie strumień danych, które będą używane. Na przykład telemetrii urządzenia. |
| **Warunek** | Wyrażenia zapytania dla reguły routingu, który jest uruchamiany przed nagłówki i treści wiadomości i określa, czy dopasowanie dla punktu końcowego. Aby uzyskać więcej informacji na temat tworzenia warunku trasy, zobacz [odwołanie - zapytania języka twins urządzenia i zadania][lnk-devguide-query-language]. |
| **punkt końcowy**  | Nazwa punktu końcowego, w którym Centrum IoT wysyła komunikaty, które są zgodne z warunkiem. Punkty końcowe musi należeć do tego samego regionu Centrum IoT, w przeciwnym razie może być wymagana dla regionu między zapisów. |

Pojedynczy komunikat może zgodne z warunkiem na wielu reguł routingu, w których przypadku Centrum IoT dostarcza wiadomość do punktu końcowego skojarzone z każdą regułę dopasowany. Centrum IoT automatycznie deduplicates dostarczanie komunikatów, więc jeśli wiadomość pasuje do wielu reguł, które mają tego samego miejsca docelowego, jest ona tylko zapisywana raz do tego miejsca docelowego.

## <a name="endpoints-and-routing"></a>Punkty końcowe i routingu

Centrum IoT ma wartość domyślną [wbudowanym punktem końcowym][lnk-built-in]. Można tworzyć niestandardowe punkty końcowe do wyznaczania tras wiadomościom przez połączenie innych usług w Twojej subskrypcji do koncentratora. Centrum IoT obecnie obsługuje kontenery magazynu Azure, usługa Event Hubs, kolejek usługi Service Bus i tematów usługi Service Bus jako niestandardowe punkty końcowe.

Gdy używasz routingu i niestandardowe punkty końcowe wiadomości tylko są dostarczane do wbudowanym punktem końcowym, jeśli nie odpowiadają regułom. Na potrzeby dostarczania wiadomości do wbudowanych punktu końcowego również dotyczące niestandardowych punktu końcowego, Dodaj trasę, która wysyła komunikaty do **zdarzenia** punktu końcowego.

> [!NOTE]
> Centrum IoT obsługuje tylko zapisywania danych do usługi Azure Storage kontenery jako obiekty BLOB.

> [!WARNING]
> Usługa Service Bus kolejek i tematów z **sesji** lub **wykrywania duplikatów** włączone nie są obsługiwane jako niestandardowe punkty końcowe.

Aby uzyskać więcej informacji na temat tworzenia niestandardowych punktów końcowych w Centrum IoT, zobacz [punkty końcowe Centrum IoT][lnk-devguide-endpoints].

Aby uzyskać więcej informacji na temat odczytu z niestandardowe punkty końcowe zobacz:

* Odczytywanie z [kontenery magazynu Azure][lnk-getstarted-storage].
* Odczytywanie z [usługi Event Hubs][lnk-getstarted-eh].
* Odczytywanie z [kolejek usługi Service Bus][lnk-getstarted-queue].
* Odczytywanie z [tematów usługi Service Bus][lnk-getstarted-topic].

## <a name="latency"></a>Opóźnienie

Podczas rozsyłania wiadomości telemetrii urządzenia do chmury przy użyciu wbudowanych punkty końcowe po utworzeniu pierwszego trasy jest niewielki wzrost opóźnienia end-to-end.

W większości przypadków Średni czas oczekiwania jest mniej niż jednej sekundzie. Można monitorować przy użyciu opóźnienia **d2c.endpoints.latency.builtIn.events** [Metryka Centrum IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics). Tworzenie lub usuwanie żadnej trasy po pierwsza z nich nie wpływa na czas oczekiwania na trasie.

### <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat punkty końcowe Centrum IoT, zobacz [punkty końcowe Centrum IoT][lnk-devguide-endpoints].

Aby uzyskać więcej informacji na temat języka zapytań, służy do definiowania reguł routingu, zobacz [Centrum IoT zapytania języka twins urządzenia, zadania i rozsyłania wiadomości][lnk-devguide-query-language].

[Centrum IoT procesu wiadomości urządzenia do chmury, za pomocą trasy] [ lnk-d2c-tutorial] samouczek pokazuje, jak używać reguł routingu oraz niestandardowe punkty końcowe.

[lnk-built-in]: iot-hub-devguide-messages-read-builtin.md
[lnk-device-to-cloud]: iot-hub-devguide-messages-d2c.md
[lnk-devguide-query-language]: iot-hub-devguide-query-language.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-d2c-tutorial]: tutorial-routing.md
[lnk-getstarted-eh]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-getstarted-queue]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md
[lnk-getstarted-topic]: ../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md
[lnk-getstarted-storage]: ../storage/blobs/storage-blobs-introduction.md
