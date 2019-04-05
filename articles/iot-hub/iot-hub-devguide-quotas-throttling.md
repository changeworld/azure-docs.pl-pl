---
title: Omówienie usługi Azure IoT Hub, limity przydziału i ograniczanie wydajności | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera — opis przydziały, które mają zastosowanie do usługi IoT Hub i oczekiwane zachowanie ograniczania przepływności.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/05/2018
ms.openlocfilehash: 14c563fda48bc7c54dcce1e1ccbfba716b6c73ad
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2019
ms.locfileid: "59049607"
---
# <a name="reference---iot-hub-quotas-and-throttling"></a>Odwołanie — IoT Hub przydziałów i dławienia

## <a name="quotas-and-throttling"></a>Limity przydziału i ograniczanie wydajności

Każda subskrypcja platformy Azure może mieć maksymalnie 50 centra IoT i co najwyżej 1 bezpłatny koncentratora.

Każde centrum IoT jest aprowizowane za pomocą określonej liczby jednostek w określonej warstwie. Określ maksymalny dzienny limit przydziału komunikatów, których można wysłać, warstwy i liczby jednostek. Rozmiar komunikatu, używane do obliczania jest dziennego limitu przydziału o rozmiarze 0,5 KB w Centrum w warstwie bezpłatna i 4 KB w przypadku wszystkich innych warstw. Aby uzyskać więcej informacji, zobacz [cennik usługi Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

Warstwa określa również limity ograniczania, które usługi IoT Hub wymusza na wszystkie operacje.

## <a name="operation-throttles"></a>Ograniczenia operacji

Ograniczenia operacji są ograniczenia szybkości, które są stosowane w zakresach minutę i mają na celu zapobiec nadużyciu. Usługa IoT Hub próbuje należy unikać cofania się błędy, jeśli to możliwe, ale rozpoczyna się, zwracając `429 ThrottlingException` Jeśli dławienie jest naruszona za długo.

W dowolnym momencie można zwiększyć przydziały i limity ograniczania, zwiększając liczbę aprowizowane jednostki w usłudze IoT hub.

W poniższej tabeli przedstawiono ograniczenia wymuszone. Wartości odnoszą się do poszczególnych Centrum.

| Ograniczenie | Bezpłatna, B1 i S1 | B2 i S2 | W wersji B3 i S3 | 
| -------- | ------- | ------- | ------- |
| Operacje rejestru tożsamości (Tworzenie, pobieranie, listy, aktualizowanie i usuwanie) | 1.67/sec/Unit (100/min/jednostkę) | 1.67/sec/Unit (100/min/jednostkę) | 83.33/sec/Unit (5000/min/jednostkę) |
| Nowe połączenia urządzenia (ten limit dotyczy szybkość, z którym _nowych połączeń_ są ustanowione nie całkowita liczba połączeń) | Wyższe 100/sek lub 12/sek/jednostkę <br/> Na przykład dwóch jednostek S1 są 2\*12 = 24 nowych połączeń na sekundę, ale ma co najmniej 100 nowych połączeń na sekundę w jednostkach usługi. Z dziewięciu jednostek S1, masz 108 nowych połączeń na sekundę (9\*12) w jednostkach usługi. | nowe 120 połączeń/sek/jednostkę | nowe 6000 połączeń/sek/jednostkę |
| Liczba elementów wysłanych z urządzenia do chmury | Wyższe 100/sek lub 12/sek/jednostkę <br/> Na przykład dwóch jednostek S1 są 2\*12 = 24/s, ale co najmniej 100 na sekundę w jednostkach usługi. Z dziewięciu jednostek S1, masz 108 na sekundę (9\*12) w jednostkach usługi. | 120/sek/jednostkę | 6000/sec/unit |
| Chmura urządzenie wysyła<sup>1</sup> | 1.67/sec/Unit (100/min/jednostkę) | 1.67/sec/Unit (100/min/jednostkę) | 83.33/sec/Unit (5000/min/jednostkę) |
| Chmura urządzenie odbiera<sup>1</sup> <br/> (tylko gdy urządzenie korzysta z protokołu HTTPS)| 16.67/sec/Unit (1000/min/jednostkę) | 16.67/sec/Unit (1000/min/jednostkę) | 833.33/sec/Unit (50000/min/jednostkę) |
| Przekazywanie pliku | Plik 1.67 przekazywania powiadomień/sek/jednostkę (100/min/jednostkę) | Plik 1.67 przekazywania powiadomień/sek/jednostkę (100/min/jednostkę) | Plik 83.33 przekazywania powiadomień/sek/jednostkę (5000/min/jednostkę) |
| Metody bezpośrednie<sup>1</sup> | 160KB/sec/unit<sup>2</sup> | 480KB/sec/unit<sup>2</sup> | 24MB/sec/unit<sup>2</sup> | 
| Zapytania | 20/min/jednostkę | 20/min/jednostkę | 1000/min/jednostkę |
| (Urządzenia i moduł) odczytów bliźniaczej reprezentacji<sup>1</sup> | 100/sek | Wyższe 100/sek lub 10/sek/jednostkę | 500/sec/unit |
| Bliźniacza reprezentacja aktualizacji (urządzenia i moduł)<sup>1</sup> | 50/s | Wyższa z 50/sek lub 5/sek/jednostkę | 250/sek/jednostkę |
| Operacje zadania<sup>1,3</sup> <br/> (tworzenie, aktualizowanie, wyświetlanie, usuwanie) | 1.67/sec/Unit (100/min/jednostkę) | 1.67/sec/Unit (100/min/jednostkę) | 83.33/sec/Unit (5000/min/jednostkę) |
| Operacje dotyczące urządzenia zadania<sup>1</sup> <br/> (aktualizacji bliźniaczej reprezentacji, wywoływanie metody bezpośredniej) | 10/sek | Wyższe 10/sek lub 1/sek/jednostkę | 50/sek/jednostkę |
| Konfiguracje i wdrożenia krawędzi<sup>1</sup> <br/> (tworzenie, aktualizowanie, wyświetlanie, usuwanie) | 0.33/sec/Unit (20/min/jednostkę) | 0.33/sec/Unit (20/min/jednostkę) | 0.33/sec/Unit (20/min/jednostkę) |
| Szybkość inicjowania strumienia urządzenia<sup>4</sup> | 5 nowych strumieni na sekundę | 5 nowych strumieni na sekundę | 5 nowych strumieni na sekundę |
| Maksymalna liczba równocześnie połączonych urządzeń, strumieni<sup>4</sup> | 50 | 50 | 50 |
| Transfer danych strumienia maksymalna urządzenia<sup>4</sup> (agregacji woluminu na dzień) | 300 MB | 300 MB | 300 MB |


<sup>1</sup>ta funkcja nie jest dostępne w warstwie podstawowa usługi IoT Hub. Aby uzyskać więcej informacji, zobacz [jak wybrać we właściwym Centrum IoT](iot-hub-scaling.md). <br/><sup>2</sup>ograniczenie rozmiaru miernika to 4 KB. <br/><sup>3</sup>w danym momencie może mieć tylko jedno zadanie importu/eksportu aktywnych urządzeń. <br/><sup>4</sup>strumieni urządzenia usługi IoT Hub są dostępne tylko dla S1, S2, S3 i F1 SKU.

*Połączenia urządzenia* ograniczania decyduje szybkość, w którym można nawiązać nowego połączenia urządzenia z usługą IoT hub. *Połączenia urządzenia* ograniczania nie kontroluje maksymalna liczba równocześnie połączonych urządzeń. *Połączenia urządzenia* współczynnik przepustowości jest zależna od liczby jednostek, które są udostępniane dla Centrum IoT hub.

Na przykład jeśli kupisz pojedyncza jednostka S1, otrzymasz ograniczania 100 połączeń na sekundę. W związku z tym aby połączyć 100 000 urządzeń, może potrwać co najmniej 1 000 sekund (około 16 minut). Jednak może mieć dowolną liczbę równocześnie połączonych urządzeń, jeśli masz urządzenia zarejestrowane w rejestrze tożsamości.

Szczegółowe omówienie usługi IoT Hub ograniczania zachowania, zobacz wpis w blogu [usługi IoT Hub, ograniczania i](https://azure.microsoft.com/blog/iot-hub-throttling-and-you/).

> [!IMPORTANT]
> Operacje rejestru tożsamości są przeznaczone dla środowiska wykonawczego zarządzania urządzeniami i inicjowania obsługi scenariuszy. Odczyt lub aktualizowanie dużej liczby tożsamości urządzeń jest obsługiwane za pośrednictwem [importowanie i eksportowanie zadań](iot-hub-devguide-identity-registry.md#import-and-export-device-identities).
> 
> 

## <a name="other-limits"></a>Inne limity

Usługa IoT Hub wymusza inne limity operacyjne:

| Operacja | Limit |
| --------- | ----- |
| Przekazywanie pliku identyfikatory URI | 10000 identyfikatorami URI SAS może istnieć dla konta magazynu w tym samym czasie. <br/> Jednocześnie może istnieć 10 identyfikatorów URI sygnatury dostępu współdzielonego. |
| Jobs<sup>1</sup> | Historia zadania są przechowywane w górę do 30 dni <br/> Maksymalna liczba równoczesnych zadań to 1 (bezpłatna) i wersji S1, 5 (warstwa S2), 10 (warstwa S3). |
| Dodatkowe punkty końcowe | Płatna jednostka SKU hubs może mieć 10 dodatkowych punktów końcowych. Bezpłatna jednostka SKU koncentratory może mieć jeden dodatkowy punkt końcowy. |
| Reguł rozsyłania komunikatów | Płatna jednostka SKU hubs może mieć 100 reguł routingu. Bezpłatna jednostka SKU koncentratory mogą mieć pięć reguł routingu. |
| Komunikaty z urządzenia do chmury | Rozmiar maksymalny komunikatu 256 KB |
| Komunikaty z chmury do urządzenia<sup>1</sup> | Maksymalny rozmiar 64 KB. Maksymalna liczba oczekujących komunikatów do dostarczania jest 50. |
| Metoda bezpośrednia<sup>1</sup> | Rozmiar ładunku metody bezpośredniej maksymalna to 128 KB. |
| Konfiguracje urządzeń automatycznego<sup>1</sup> | 100 konfiguracje na płatnych jednostek SKU koncentratora. 20 konfiguracje za bezpłatne jednostki SKU koncentratora. |
| Automatyczne wdrażanie krawędzi<sup>1</sup> | 20 modułów na wdrożenie. 100 wdrożeń na płatnych jednostek SKU koncentratora. 20 wdrożeń na bezpłatne jednostki SKU koncentratora. |
| Twins<sup>1</sup> | Maksymalny rozmiar poszczególnych sekcji bliźniaczych reprezentacji (tagi, żądanych właściwości, zgłaszanych właściwości) to 8 KB |

<sup>1</sup>ta funkcja nie jest dostępne w warstwie podstawowa usługi IoT Hub. Aby uzyskać więcej informacji, zobacz [jak wybrać we właściwym Centrum IoT](iot-hub-scaling.md).

> [!NOTE]
> Obecnie maksymalna liczba urządzeń, którymi można nawiązać połączenie w jednym centrum IoT jest 1 000 000. Jeśli chcesz zwiększyć ten limit, skontaktuj się z [Microsoft Support](https://azure.microsoft.com/support/options/).

## <a name="latency"></a>Opóźnienie
Usługi IoT Hub dokłada starań, aby zapewnić małe opóźnienia dla wszystkich operacji. Jednak ze względu na warunki sieciowe i inne czynniki nieprzewidziany nie może zagwarantować maksymalny czas oczekiwania. Podczas projektowania rozwiązania, należy:

* Należy unikać wprowadzania żadnych założeń dotyczących maksymalny czas oczekiwania, o każdej operacji usługi IoT Hub.
* Aprowizacja usługi IoT hub w regionie platformy Azure, która jest najbliżej Twojego urządzenia.
* Należy rozważyć użycie usługi Azure IoT Edge do wykonywania operacji wrażliwych na opóźnienia na urządzeniu lub w bramie blisko urządzenia.

Wiele jednostek usługi IoT Hub mają wpływ na ograniczenie użycia sieci zgodnie z opisem w poprzedniej sekcji, ale nie podasz żadnych korzyści dodatkowe opóźnienie lub gwarancji.

Jeśli widzisz wzrostów opóźnienia operacji, skontaktuj się z pomocą [Microsoft Support](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Kolejne kroki

Inne tematy referencyjne w tym przewodniku dla deweloperów usługi IoT Hub obejmują:

* [Punkty końcowe centrum IoT Hub](iot-hub-devguide-endpoints.md)
