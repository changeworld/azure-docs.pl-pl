---
title: Omówienie usługi Azure IoT Hub, limity przydziału i ograniczanie wydajności | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera — opis przydziały, które mają zastosowanie do usługi IoT Hub i oczekiwane zachowanie ograniczania przepływności.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/11/2019
ms.openlocfilehash: ea9bea8b314d00db87ad7addacc49a976e0da08e
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/13/2019
ms.locfileid: "65550471"
---
# <a name="reference---iot-hub-quotas-and-throttling"></a>Odwołanie — IoT Hub przydziałów i dławienia

## <a name="quotas-and-throttling"></a>Limity przydziału i ograniczanie wydajności

Każda subskrypcja platformy Azure może mieć maksymalnie 50 centra IoT i co najwyżej 1 bezpłatny koncentratora.

Każde centrum IoT jest aprowizowane za pomocą określonej liczby jednostek w określonej warstwie. Określ maksymalny dzienny limit przydziału komunikatów, których można wysłać, warstwy i liczby jednostek. Rozmiar komunikatu, używane do obliczania jest dziennego limitu przydziału o rozmiarze 0,5 KB w Centrum w warstwie bezpłatna i 4 KB w przypadku wszystkich innych warstw. Aby uzyskać więcej informacji, zobacz [cennik usługi Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

Warstwa określa również limity ograniczania, które usługi IoT Hub wymusza na wszystkie operacje.

## <a name="operation-throttles"></a>Ograniczenia operacji

Ograniczenia operacji są ograniczenia szybkości, które są stosowane w zakresach minutę i są przeznaczone do zapobiec nadużyciu. Są one również podlegają [kształtowania ruchu](#traffic-shaping).

W poniższej tabeli przedstawiono ograniczenia wymuszone. Wartości odnoszą się do poszczególnych Centrum.

| Ograniczenie | Bezpłatna, B1 i S1 | B2 i S2 | W wersji B3 i S3 | 
| -------- | ------- | ------- | ------- |
| [Operacje rejestru tożsamości](#identity-registry-operations-throttle) (Tworzenie, pobieranie, listy, aktualizowanie i usuwanie) | 1.67/sec/Unit (100/min/jednostkę) | 1.67/sec/Unit (100/min/jednostkę) | 83.33/sec/Unit (5000/min/jednostkę) |
| [Nowe połączenia urządzenia](#device-connections-throttle) (ten limit dotyczy stawki _nowych połączeń_, nie całkowita liczba połączeń) | Wyższe 100/sek lub 12/sek/jednostkę <br/> Na przykład dwóch jednostek S1 są 2\*12 = 24 nowych połączeń na sekundę, ale ma co najmniej 100 nowych połączeń na sekundę w jednostkach usługi. Z dziewięciu jednostek S1, masz 108 nowych połączeń na sekundę (9\*12) w jednostkach usługi. | nowe 120 połączeń/sek/jednostkę | 6000 nowych połączeń/sek/jednostkę |
| Liczba elementów wysłanych z urządzenia do chmury | Wyższe 100/sek lub 12/sek/jednostkę <br/> Na przykład dwóch jednostek S1 są 2\*12 = 24/s, ale co najmniej 100 na sekundę w jednostkach usługi. Z dziewięciu jednostek S1, masz 108 na sekundę (9\*12) w jednostkach usługi. | 120/sek/jednostkę | 6000/sek/jednostkę |
| Chmura urządzenie wysyła<sup>1</sup> | 1.67/sec/Unit (100/min/jednostkę) | 1.67/sec/Unit (100/min/jednostkę) | 83.33/sec/Unit (5000/min/jednostkę) |
| Chmura urządzenie odbiera<sup>1</sup> <br/> (tylko gdy urządzenie korzysta z protokołu HTTPS)| 16.67/sec/Unit (1000/min/jednostkę) | 16.67/sec/Unit (1000/min/jednostkę) | 833.33/sec/Unit (50 000/min/jednostkę) |
| Przekazywanie pliku | Plik 1.67 przekazywania powiadomień/sek/jednostkę (100/min/jednostkę) | Plik 1.67 przekazywania powiadomień/sek/jednostkę (100/min/jednostkę) | Plik 83.33 przekazywania powiadomień/sek/jednostkę (5000/min/jednostkę) |
| Metody bezpośrednie<sup>1</sup> | 160KB/sec/unit<sup>2</sup> | 480KB/sec/unit<sup>2</sup> | 24MB/sec/unit<sup>2</sup> | 
| Zapytania | 20/min/jednostkę | 20/min/jednostkę | 1000/min/jednostkę |
| (Urządzenia i moduł) odczytów bliźniaczej reprezentacji<sup>1</sup> | 100/sek | Wyższe 100/sek lub 10/sek/jednostkę | 500/sec/unit |
| Bliźniacza reprezentacja aktualizacji (urządzenia i moduł)<sup>1</sup> | 50/s | Wyższa z 50/sek lub 5/sek/jednostkę | 250/sek/jednostkę |
| Operacje zadania<sup>1</sup> <br/> (tworzenie, aktualizowanie, wyświetlanie, usuwanie) | 1.67/sec/Unit (100/min/jednostkę) | 1.67/sec/Unit (100/min/jednostkę) | 83.33/sec/Unit (5000/min/jednostkę) |
| Operacje dotyczące urządzenia zadania<sup>1</sup> <br/> (aktualizacji bliźniaczej reprezentacji, wywoływanie metody bezpośredniej) | 10/sek | Wyższe 10/sek lub 1/sek/jednostkę | 50/sek/jednostkę |
| Konfiguracje i wdrożenia krawędzi<sup>1</sup> <br/> (tworzenie, aktualizowanie, wyświetlanie, usuwanie) | 0.33/sec/Unit (20/min/jednostkę) | 0.33/sec/Unit (20/min/jednostkę) | 0.33/sec/Unit (20/min/jednostkę) |
| Szybkość inicjowania strumienia urządzenia<sup>1</sup> | 5 nowych strumieni na sekundę | 5 nowych strumieni na sekundę | 5 nowych strumieni na sekundę |
| Maksymalna liczba równocześnie połączonych urządzeń, strumieni<sup>1</sup> | 50 | 50 | 50 |
| Transfer danych strumienia maksymalna urządzenia<sup>1</sup> (agregacji woluminu na dzień) | 300 MB | 300 MB | 300 MB |

<sup>1</sup>ta funkcja nie jest dostępne w warstwie podstawowa usługi IoT Hub. Aby uzyskać więcej informacji, zobacz [jak wybrać we właściwym Centrum IoT](iot-hub-scaling.md). <br/><sup>2</sup>ograniczenie rozmiaru miernika to 4 KB.

### <a name="traffic-shaping"></a>Kształtowania ruchu

Aby umożliwić ruch serii, usługi IoT Hub odbiera powyżej dławienie przez ograniczony czas. Pierwsze kilka te żądania są przetwarzane od razu. Jednak jeśli liczba żądań, które będzie się powtarzał narusza ograniczenie uruchamia usługi IoT Hub umieszczenie żądania z kolejki i przetwarzane zgodnie ze stawką limit. Efekt ten jest nazywany *kształtowania ruchu*. Ponadto rozmiar tej kolejki jest ograniczona. Jeśli naruszenie ograniczania będzie się powtarzać, po pewnym czasie kolejka się zapełni i Centrum IoT Hub, który rozpoczyna się odrzucanie żądań z `429 ThrottlingException`. 

Na przykład użyć symulowanego urządzenia do wysyłania 200 komunikatów z urządzenia do chmury na sekundę do Centrum IoT Hub S1 (który obowiązuje limit 100/sek D2C wysyła). Dla pierwszego minutę lub dwie komunikaty są przetwarzane od razu. Jednak ponieważ urządzenie nadal wysyłać więcej niż limit przepustowości, usługi IoT Hub rozpoczyna się tylko przetwarzania komunikatów 100 na sekundę i umieszcza pozostałe w kolejce. Możesz rozpocząć obserwowanie większe opóźnienia. Po pewnym czasie rozpoczęcia pobierania `429 ThrottlingException` jako zapełnia się kolejki i "Liczba błędów ograniczania" w [metryki usługi IoT Hub](iot-hub-metrics.md) rozpoczyna się zwiększa.

### <a name="identity-registry-operations-throttle"></a>Ograniczać operacje rejestru tożsamości

Operacje rejestru tożsamości urządzenia są przeznaczone do użytku środowiska wykonawczego w zarządzania urządzeniami i inicjowania obsługi scenariuszy. Odczyt lub aktualizowanie dużej liczby tożsamości urządzeń jest obsługiwane za pośrednictwem [importowanie i eksportowanie zadań](iot-hub-devguide-identity-registry.md#import-and-export-device-identities).

### <a name="device-connections-throttle"></a>Ograniczenie przepustowości połączenia urządzenia

*Połączenia urządzenia* ograniczania decyduje szybkość, w którym można nawiązać nowego połączenia urządzenia z usługą IoT hub. *Połączenia urządzenia* ograniczania nie kontroluje maksymalna liczba równocześnie połączonych urządzeń. *Połączenia urządzenia* współczynnik przepustowości jest zależna od liczby jednostek, które są udostępniane dla Centrum IoT hub.

Na przykład jeśli kupisz pojedyncza jednostka S1, otrzymasz ograniczania 100 połączeń na sekundę. W związku z tym do łączenia z 100,, 000 urządzeń, może potrwać co najmniej 1 000 sekund (około 16 minut). Jednak może mieć dowolną liczbę równocześnie połączonych urządzeń, jeśli masz urządzenia zarejestrowane w rejestrze tożsamości.


## <a name="other-limits"></a>Inne limity

Usługa IoT Hub wymusza inne limity operacyjne:

| Operacja | Limit |
| --------- | ----- |
| Urządzenia | Maksymalna liczba urządzeń, którymi można nawiązać połączenie w jednym centrum IoT wynosi 1 000 000. Jedynym sposobem, aby zwiększyć ten limit jest skontaktowanie się z [Microsoft Support](https://azure.microsoft.com/support/options/).| 
| Przekazywanie pliku identyfikatory URI | 10 000 identyfikatorów URI może istnieć dla konta magazynu w tym samym czasie. <br/> Jednocześnie może istnieć 10 identyfikatorów URI sygnatury dostępu współdzielonego. |
| Jobs<sup>1</sup> | Maksymalna liczba równoczesnych zadań jest 1 (bezpłatna i S1), 5 (warstwa S2) i 10 (warstwa S3). Jednak maksymalny współbieżnych [zadania importu/eksportu urządzeń](iot-hub-bulk-identity-mgmt.md) 1 dla wszystkich warstw. <br/>Historia zadania są przechowywane w górę do 30 dni. |
| Dodatkowe punkty końcowe | Płatna jednostka SKU hubs może mieć 10 dodatkowych punktów końcowych. Bezpłatna jednostka SKU koncentratory może mieć jeden dodatkowy punkt końcowy. |
| Reguł rozsyłania komunikatów | Płatna jednostka SKU hubs może mieć 100 reguł routingu. Bezpłatna jednostka SKU koncentratory mogą mieć pięć reguł routingu. |
| Komunikaty z urządzenia do chmury | Rozmiar maksymalny komunikatu 256 KB |
| Komunikaty z chmury do urządzenia<sup>1</sup> | Maksymalny rozmiar 64 KB. Maksymalna liczba oczekujących komunikatów do dostarczania jest 50. |
| Metoda bezpośrednia<sup>1</sup> | Rozmiar ładunku metody bezpośredniej maksymalna to 128 KB. |
| Konfiguracje urządzeń automatycznego<sup>1</sup> | 100 konfiguracje na płatnych jednostek SKU koncentratora. 20 konfiguracje za bezpłatne jednostki SKU koncentratora. |
| Automatyczne wdrażanie krawędzi<sup>1</sup> | 20 modułów na wdrożenie. 100 wdrożeń na płatnych jednostek SKU koncentratora. 20 wdrożeń na bezpłatne jednostki SKU koncentratora. |
| Twins<sup>1</sup> | Maksymalny rozmiar poszczególnych sekcji bliźniaczych reprezentacji (tagi, żądanych właściwości, zgłaszanych właściwości) to 8 KB |

<sup>1</sup>ta funkcja nie jest dostępne w warstwie podstawowa usługi IoT Hub. Aby uzyskać więcej informacji, zobacz [jak wybrać we właściwym Centrum IoT](iot-hub-scaling.md).

## <a name="increasing-the-quota-or-throttle-limit"></a>Zwiększenie limitu przydziału lub ograniczenie przepustowości

W dowolnym momencie można zwiększyć limity przydziału lub ograniczyć limity przez [zwiększenie liczby aprowizowane jednostki w usłudze IoT hub](iot-hub-upgrade.md).

## <a name="latency"></a>Opóźnienie

Usługi IoT Hub dokłada starań, aby zapewnić małe opóźnienia dla wszystkich operacji. Jednak ze względu na warunki sieciowe i inne czynniki nieprzewidziany nie może zagwarantować pewne opóźnienie. Podczas projektowania rozwiązania, należy:

* Należy unikać wprowadzania żadnych założeń dotyczących maksymalny czas oczekiwania, o każdej operacji usługi IoT Hub.
* Aprowizacja usługi IoT hub w regionie platformy Azure, która jest najbliżej Twojego urządzenia.
* Należy rozważyć użycie usługi Azure IoT Edge do wykonywania operacji wrażliwych na opóźnienia na urządzeniu lub w bramie blisko urządzenia.

Wiele jednostek usługi IoT Hub mają wpływ na ograniczenie użycia sieci zgodnie z opisem w poprzedniej sekcji, ale nie podasz żadnych korzyści dodatkowe opóźnienie lub gwarancji.

Jeśli widzisz wzrostów opóźnienia operacji, skontaktuj się z pomocą [Microsoft Support](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Kolejne kroki

Szczegółowe omówienie usługi IoT Hub ograniczania zachowania, zobacz wpis w blogu [usługi IoT Hub, ograniczania i](https://azure.microsoft.com/blog/iot-hub-throttling-and-you/).

Inne tematy referencyjne w tym przewodniku dla deweloperów usługi IoT Hub obejmują:

* [Punkty końcowe usługi IoT Hub](iot-hub-devguide-endpoints.md)
