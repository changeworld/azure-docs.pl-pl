---
title: Zrozumienie przydziały Centrum IoT Azure i ograniczania przepustowości | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera — opis przydziałów, które mają zastosowanie do Centrum IoT i oczekiwane zachowanie ograniczania przepustowości.
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 425e1b08-8789-4377-85f7-c13131fae4ce
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/01/2018
ms.author: dobett
ms.openlocfilehash: 65e1f8a907c8bf64497f7439e5b635ad336cd23a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="reference---iot-hub-quotas-and-throttling"></a>Odwołanie - Centrum IoT przydziały i ograniczenia przepustowości

## <a name="quotas-and-throttling"></a>Limity przydziału i ograniczanie wydajności
Każdej subskrypcji platformy Azure może mieć maksymalnie 10 centra IoT i maksymalnie 1 wolnego koncentratora.

Każdy Centrum IoT jest udostępniane z określonej liczby jednostek w określonej warstwy. Aby uzyskać więcej informacji, zobacz [cennik usługi Azure IoT Hub][lnk-pricing]. Warstwy, a liczba jednostek określić maksymalny limit przydziału codzienne wiadomości, które można wysyłać.

Warstwy określa również sposób ograniczania przepustowości ograniczeń, które egzekwuje Centrum IoT na wszystkie operacje.

## <a name="operation-throttles"></a>Limity operacji
Limity operacji są ograniczenia szybkości, są stosowane w zakresach minuty, które mają na celu uniemożliwić nadużycia. Centrum IoT podejmuje próbę uniknięcia zwraca błędy, jeśli to możliwe, ale uruchamia zwracania wyjątków, jeśli przepustnicy naruszenia zbyt długo.

W dowolnym momencie można zwiększyć przydziały i limity przepustowości przez odpowiednie zwiększenie liczby elastycznie jednostki w Centrum IoT.

W poniższej tabeli przedstawiono limity wymuszone. Wartości odnoszą się do poszczególnych koncentratora.

| Ograniczenie | Wolne B1 i S1 | B2 i S2 | B3 i S3 | 
| -------- | ------- | ------- | ------- |
| Operacje rejestru tożsamości (Tworzenie, pobieranie, listy, aktualizowanie i usuwanie) | 1.67/sec/Unit (jednostka/100/min) | 1.67/sec/Unit (jednostka/100/min) | 83.33/sec/Unit (jednostka-5000/min) |
| Połączenia urządzenia | Wyższy 100 na sekundę lub jednostki-12/s <br/> Na przykład dwie jednostki S1 są 2\*12 = 24/s, ale mają co najmniej 100 na sekundę przez jednostek. W przypadku dziewięć S1, masz 108 na sekundę (9\*12) między jednostek. | Jednostka-120/s | Jednostka-6000/s |
| Liczba elementów wysłanych z urządzenia do chmury | Wyższy 100 na sekundę lub jednostki-12/s <br/> Na przykład dwie jednostki S1 są 2\*12 = 24/s, ale mają co najmniej 100 na sekundę przez jednostek. W przypadku dziewięć S1, masz 108 na sekundę (9\*12) między jednostek. | Jednostka-120/s | Jednostka-6000/s |
| Wysyła chmury do urządzenia<sup>1</sup> | 1.67/sec/Unit (jednostka/100/min) | 1.67/sec/Unit (jednostka/100/min) | 83.33/sec/Unit (jednostka-5000/min) |
| Chmura urządzenie odbiera<sup>1</sup> <br/> (tylko kiedy urządzenie korzysta z protokołu HTTPS)| 16.67/sec/Unit (jednostka-1000/min) | 16.67/sec/Unit (jednostka-1000/min) | 833.33/sec/Unit (jednostka-50000/min) |
| Przekazywanie pliku | 1.67 pliku przekazywania powiadomień na sekundę/jednostkę (jednostka/100/min) | 1.67 pliku przekazywania powiadomień na sekundę/jednostkę (jednostka/100/min) | 83.33 pliku przekazywania powiadomień/s/jednostek (jednostka-5000/min) |
| Bezpośrednie metody<sup>1</sup> | 160KB/sec/unit<sup>2</sup> | 480KB/sec/unit<sup>2</sup> | 24MB/sec/unit<sup>2</sup> | 
| Dwie (urządzenia i modułu) odczyty<sup>1</sup> | 10 na sekundę | Wyższy 10 na sekundę lub jednostki-1/s | Jednostka-50/s |
| Dwie aktualizacji (urządzenia i modułu)<sup>1</sup> | 10 na sekundę | Wyższy 10 na sekundę lub jednostki-1/s | Jednostka-50/s |
| Zadania operacje<sup>1</sup> <br/> (tworzenie, aktualizowanie, wyświetlanie, usuwanie) | 1.67/sec/Unit (jednostka/100/min) | 1.67/sec/Unit (jednostka/100/min) | 83.33/sec/Unit (jednostka-5000/min) |
| Zadania na urządzenie operacji przepływności<sup>1</sup> | 10 na sekundę | Wyższy 10 na sekundę lub jednostki-1/s | Jednostka-50/s |
| Konfiguracje i wdrożenia krawędzi<sup>1</sup> <br/> (tworzenie, aktualizowanie, wyświetlanie, usuwanie) | 0.33/sec/Unit (jednostka-20/min) | 0.33/sec/Unit (jednostka-20/min) | 0.33/sec/Unit (jednostka-20/min) |


<sup>1</sup>ta funkcja nie jest dostępne w warstwie podstawowa Centrum IoT. Aby uzyskać więcej informacji, zobacz [Wybieranie prawo Centrum IoT](iot-hub-scaling.md). <br/><sup>2</sup>ograniczenie rozmiaru miernika jest 8 KB.

*Połączenia urządzenia* ograniczania kontroluje częstotliwość, w którym można nawiązać nowe połączenia urządzenia z Centrum IoT. *Połączenia urządzenia* przepustnicy nie kontroluje maksymalna liczba równocześnie połączonych urządzeń. Przepustnica zależy od liczby jednostek, które są udostępniane dla Centrum IoT.

Na przykład jeśli kupisz pojedynczą jednostkę S1 get przepustnicy połączenia o szybkości 100 na sekundę. W związku z tym nawiązać 100 000 urządzeń ma co najmniej 1 000 sekund (około 16 minut). Jednak może mieć dowolną liczbę równocześnie połączonych urządzeń zgodnie z urządzeń zarejestrowanych w rejestrze tożsamości.

Szczegółowe omówienie Centrum IoT ograniczania zachowania, zobacz wpis w blogu [Centrum IoT ograniczania przepustowości i][lnk-throttle-blog].

> [!IMPORTANT]
> Operacje rejestru tożsamości są przeznaczone dla środowiska wykonawczego używanych do zarządzania urządzeniami i inicjowania obsługi scenariuszy. Odczytu lub aktualizacji dużej liczby urządzeń tożsamości jest obsługiwany za pośrednictwem [importowanie i eksportowanie zadań][lnk-importexport].
> 
> 

## <a name="other-limits"></a>Inne ograniczenia.

Centrum IoT wymusza inne ograniczenia operacyjne:

| Operacja | Limit |
| --------- | ----- |
| Przekazywanie pliku identyfikatorów URI | 10000 URI SAS może być limit dla konta magazynu w tym samym czasie. <br/> Jednocześnie może istnieć 10 identyfikatorów URI sygnatury dostępu współdzielonego. |
| Zadania<sup>1</sup> | Historia zadań są przechowywane w górę do 30 dni <br/> Maksymalna współbieżnych zadań to 1 (bezpłatnie) i S1, 5 (w przypadku S2), 10 (na potrzeby S3). |
| Dodatkowe punkty końcowe | Płatnej SKU koncentratory może być 10 dodatkowe punkty końcowe. Bezpłatne koncentratory SKU może mieć jeden dodatkowy punkt końcowy. |
| Reguły routingu wiadomości | Płatnej SKU koncentratory mogą mieć 100 reguły routingu. Bezpłatne koncentratory SKU może mieć pięć reguły routingu. |
| Urządzenia do chmury do obsługi komunikatów | Rozmiar maksymalny komunikatu 256 KB |
| Wiadomości chmury do urządzenia<sup>1</sup> | Komunikat maksymalny rozmiar 64 KB. Maksymalna liczba oczekujących komunikatów w celu dostarczania to 50. |
| Metoda bezpośrednia<sup>1</sup> | Metoda bezpośrednia maksymalny rozmiar ładunku jest 128 KB. |
| Konfiguracje | 20 konfiguracje dla koncentratora. |
| Krawędź wdrożenia | 20 wdrożeń na koncentratora. 20 modułów na wdrożenie. |
| Twins | Maksymalny rozmiar każdej sekcji dwie (tagi, żądanej właściwości, właściwości zgłoszone) to 8 KB |

<sup>1</sup>ta funkcja nie jest dostępne w warstwie podstawowa Centrum IoT. Aby uzyskać więcej informacji, zobacz [Wybieranie prawo Centrum IoT](iot-hub-scaling.md).

> [!NOTE]
> Maksymalna liczba urządzeń, którymi możesz połączyć się z jednego centrum IoT jest obecnie, 500 000. Jeśli chcesz podwyższyć ten limit, skontaktuj się z [Microsoft Support](https://azure.microsoft.com/support/options/).

## <a name="latency"></a>Opóźnienie
Centrum IoT dokłada starań zapewnić małe opóźnienia dla wszystkich operacji. Jednak ze względu na warunki sieciowe i inne czynniki nieprzewidywalne go nie może zagwarantować maksymalnego czasu oczekiwania. Podczas projektowania rozwiązania, wykonaj następujące czynności:

* Należy unikać wprowadzania żadnych założeń dotyczących maksymalnego czasu oczekiwania żadnej operacji centrum IoT.
* Udostępnić Centrum IoT w regionie Azure najbardziej zbliżony do urządzenia.
* Należy rozważyć użycie krawędzi IoT Azure do wykonywania operacji wrażliwy na opóźnienia, na urządzeniu lub w pobliżu urządzenia bramy.

Wiele jednostek Centrum IoT wpłynąć na ograniczenia przepustowości, jak opisano wcześniej, ale nie udostępniają wszystkie korzyści dodatkowe opóźnienia lub gwarancji.
Jeśli widzisz zwiększa nieoczekiwane opóźnienia operacji, skontaktuj się z [Microsoft Support](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Kolejne kroki
Inne tematy dokumentacji, w tym przewodniku deweloperów Centrum IoT obejmują:

* [Punkty końcowe Centrum IoT][lnk-devguide-endpoints]
* [Język zapytań Centrum IoT urządzenia twins, zadań i rozsyłania wiadomości][lnk-devguide-query]
* [Obsługa MQTT Centrum IoT][lnk-devguide-mqtt]

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[lnk-throttle-blog]: https://azure.microsoft.com/blog/iot-hub-throttling-and-you/
[lnk-importexport]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities

[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
