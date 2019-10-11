---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: f0de7aedc3883978582da1950e95aaabac9a1695
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72272990"
---
W poniższej tabeli wymieniono limity związane z różnymi warstwami usług S1, S2, S3 i F1. Aby uzyskać informacje o kosztach poszczególnych *jednostek* w poszczególnych warstwach, zobacz [cennik usługi Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

| Zasób | Standard S1 | Standard S2 | Standard S3 | F1 bezpłatnie |
| --- | --- | --- | --- | --- |
| Komunikaty na dzień |400 000 |6 000 000 |300 000 000 |8 000 |
| Maksymalna liczba jednostek |200 |200 |10 |1 |

> [!NOTE]
> Jeśli przewidujesz używanie ponad 200 jednostek z koncentratorem warstwy S1 lub S2 lub 10 jednostkami z centrum warstwy S3, skontaktuj się z pomoc techniczna firmy Microsoft.
> 
> 

W poniższej tabeli wymieniono limity dotyczące zasobów IoT Hub.

| Zasób | Granice |
| --- | --- |
| Maksymalna liczba płatnych centrów IoT na subskrypcję platformy Azure |50 |
| Maksymalna liczba bezpłatnych centrów IoT na subskrypcję platformy Azure |1 |
| Maksymalna liczba znaków w IDENTYFIKATORze urządzenia | 128 |
| Maksymalna liczba tożsamości urządzeń<br/> zwrócone w pojedynczym wywołaniu |1000 |
| IoT Hub maksymalne przechowywanie komunikatów przesyłanych z urządzenia do chmury |7 dni |
| Maksymalny rozmiar komunikatu z urządzenia do chmury |256 KB |
| Maksymalny rozmiar wsadu z urządzenia do chmury |AMQP i HTTP: 256 KB dla całej partii <br/>MQTT: 256 KB dla każdego komunikatu |
| Maksymalna liczba komunikatów w partii z urządzenia do chmury |500 |
| Maksymalny rozmiar komunikatu z chmury do urządzenia |64 KB |
| Maksymalny czas wygaśnięcia komunikatów z chmury do urządzenia |2 dni |
| Maksymalna liczba dostaw dla chmury do urządzenia <br/> messages |100 |
| Maksymalna głębokość kolejki z chmury do urządzenia dla urządzenia |50 |
| Maksymalna liczba dostaw dla komunikatów opinii <br/> w odpowiedzi na komunikat z chmury do urządzenia |100 |
| Maksymalny czas wygaśnięcia komunikatów zwrotnych <br/> odpowiedź na komunikat z chmury do urządzenia |2 dni |
| [Maksymalny rozmiar sznurka urządzenia](../articles/iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) <br/> (Tagi, raportowane właściwości i żądane właściwości) | 8 KB |
| Maksymalny rozmiar wartości ciągu sznurka urządzenia | 4 KB |
| [Maksymalna głębokość obiektu w bliźniaczych urządzeniach](../articles/iot-hub/iot-hub-devguide-device-twins.md#tags-and-properties-format) | 5 |
| Maksymalny rozmiar ładunku metody bezpośredniej | 128 KB |
| Maksymalne przechowywanie historii zadań | 30 dni |
| Maksymalna liczba współbieżnych zadań | 10 (dla S3), 5 dla (S2), 1 (dla S1) |
| Maksymalna liczba dodatkowych punktów końcowych | 10 (dla S1, S2 i S3) |
| Maksymalna liczba reguł routingu komunikatów | 100 (dla S1, S2 i S3) |
| Maksymalna liczba strumieni urządzeń połączonych współbieżnie | 50 (tylko dla S1, S2, S3 i F1) |
| Maksymalny transfer danych strumienia urządzeń | 300 MB dziennie (tylko dla S1, S2, S3 i F1) |

> [!NOTE]
> Jeśli potrzebujesz więcej niż 50 płatnych centrów IoT w ramach subskrypcji platformy Azure, skontaktuj się z pomoc techniczna firmy Microsoft.

> [!NOTE]
> Obecnie maksymalna liczba urządzeń, które można połączyć z pojedynczym Centrum IoT Hub, to 1 000 000. Jeśli chcesz zwiększyć ten limit, skontaktuj się z [Pomoc techniczna firmy Microsoft](https://azure.microsoft.com/support/options/).

IoT Hub ogranicza żądania w przypadku przekroczenia następujących przydziałów.

| Całkowicie | Wartość na Hub |
| --- | --- |
| Operacje rejestru tożsamości <br/> (tworzenie, pobieranie, wyświetlanie, aktualizowanie i usuwanie), <br/> indywidualny lub Zbiorczy import/eksport |83.33/sek/jednostkę (5000/min/jednostka) (dla S3). <br/> 1.67/sek/jednostkę (100/min/jednostka) (w przypadku S1 i S2). |
| Połączenia urządzeń |6000/sek/jednostkę (dla S3), 120/sek/jednostkę (dla S2), 12/sek/jednostkę (dla S1). <br/>Minimum 100/s. |
| Wysłane urządzenia do chmury |6000/sek/jednostkę (dla S3), 120/sek/jednostkę (dla S2), 12/sek/jednostkę (dla S1). <br/>Minimum 100/s. |
| Wysyłanie z chmury do urządzenia | 83.33/sek/jednostkę (5000/min/jednostka) (dla S3), 1.67/sek/jednostkę (100/min/jednostka) (w przypadku S1 i S2). |
| Odbierane z chmury do urządzenia |833.33/sek/jednostkę (50 000/min/jednostka) (dla S3), 16.67/sek/jednostkę (1000/min/jednostka) (w przypadku S1 i S2). |
| Operacje przekazywania plików |83,33 liczba inicjacji przekazywania plików na sekundę/jednostkę (5000/min/jednostka) (dla S3), 1,67/liczba inicjacji przekazywania plików na sekundę/jednostkę (100/min/jednostka) (w przypadku S1 i S2). <br/> Identyfikatory URI 10 000 SAS mogą być jednocześnie dla konta usługi Azure Storage.<br/> 10 identyfikatorów URI SAS/urządzenie może być jednocześnie wychodzące. |
| Metody bezpośrednie | 24 MB/s/jednostkę (dla S3), 480 KB/s/jednostkę (dla S2), 160 KB/s/jednostkę (S1).<br/> W oparciu o rozmiar miernika dławienia 8 KB. |
| Odczyty sznurka urządzenia | 500/sek/jednostkę (dla S3), maksymalnie 100/sek lub 10/sek/jednostkę (dla S2), 100/SEK (dla S1) |
| Aktualizacje dotyczące urządzeń bliźniaczych | 250/sek/jednostkę (dla S3), maksymalnie 50/SEK lub 5/sek/jednostkę (dla S2), 50/s (dla S1) |
| Operacje zadań <br/> (tworzenie, aktualizowanie, wyświetlanie i usuwanie) | 83.33/sek/jednostkę (5000/min/jednostka) (dla S3), 1.67/sek/jednostkę (100/min/jednostka) (w przypadku S2), 1.67/sek/jednostkę (100/min/jednostka) (dla S1). |
| Przepływność operacji dla poszczególnych urządzeń | 50/SEK/jednostkę (dla S3), maksymalnie 10/s lub 1/sek/jednostkę (dla S2), 10/s (dla S1). |
| Wskaźnik inicjacji strumienia urządzeń | 5 nowych strumieni/s (tylko dla S1, S2, S3 i F1). |
