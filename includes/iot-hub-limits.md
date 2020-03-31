---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 34de38e91d47457d215c7ebf65d04ed2dbae5324
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74224332"
---
W poniższej tabeli wymieniono limity skojarzone z różnymi warstwami usług S1, S2, S3 i F1. Aby uzyskać informacje na temat kosztów każdej *jednostki* w każdej warstwie, zobacz [Cennik usługi Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

| Zasób | Standardowa S1 | Standardowa S2 | Standardowa S3 | Bezpłatna F1 |
| --- | --- | --- | --- | --- |
| Komunikaty/dzień |400 000 |6 000 000 |300 000 000 |8000 |
| Maksymalna liczba jednostek |200 |200 |10 |1 |

> [!NOTE]
> Jeśli przewidujesz użycie więcej niż 200 jednostek z koncentratorem warstwy S1 lub S2 lub 10 jednostek z koncentratorem warstwy S3, skontaktuj się z pomocą techniczną firmy Microsoft.
> 
> 

W poniższej tabeli wymieniono limity, które mają zastosowanie do zasobów usługi IoT Hub.

| Zasób | Limit |
| --- | --- |
| Maksymalna liczba płatnych centrów IoT na subskrypcję platformy Azure |100 |
| Maksymalna liczba bezpłatnych centrów IoT na subskrypcję platformy Azure |1 |
| Maksymalna liczba znaków w identyfikatorze urządzenia | 128 |
| Maksymalna liczba tożsamości urządzeń<br/> zwróconych w pojedynczym wywołaniu |1000 |
| Maksymalny okres przechowywania komunikatów usługi IoT Hub dla komunikatów przesyłanych z urządzenia do chmury |7 dni |
| Maksymalny rozmiar komunikatu przesyłanego z urządzenia do chmury |256 KB |
| Maksymalny rozmiar partii przesyłanej z urządzenia do chmury |AMQP i HTTP: 256 KB dla całej partii <br/>MQTT: 256 KB dla każdej wiadomości |
| Maksymalna liczba komunikatów w partii przesyłanej z urządzenia do chmury |500 |
| Maksymalny rozmiar komunikatu przesyłanego z chmury do urządzenia |64 KB |
| Maksymalny czas wygaśnięcia komunikatu przesyłanego z chmury do urządzenia |2 dni |
| Maksymalna liczba dostarczonych komunikatów przesyłanych <br/> z chmury do urządzenia |100 |
| Maksymalna głębokość kolejki chmury do urządzenia na urządzenie |50 |
| Maksymalna liczba dostarczonych komunikatów zwrotnych <br/> w odpowiedzi na komunikaty przesłane z chmury do urządzenia |100 |
| Maksymalny czas wygaśnięcia komunikatów zwrotnych <br/> w odpowiedzi na komunikaty przesłane z chmury do urządzenia |2 dni |
| [Maksymalny rozmiar bliźniaczej reprezentacji urządzenia](../articles/iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) | 8 KB dla sekcji znaczników i 32 KB dla sekcji żądanych i zgłoszonych właściwości |
| Maksymalna długość klucza podwójnego ciągu urządzenia | 1 KB |
| Maksymalna długość wartości bliźniaczej długości urządzenia | 4 KB |
| [Maksymalna głębokość obiektu w bliźniaczej reprezentacji urządzenia](../articles/iot-hub/iot-hub-devguide-device-twins.md#tags-and-properties-format) | 10 |
| Maksymalny rozmiar ładunku metody bezpośredniej | 128 KB |
| Maksymalny okres przechowywania historii zadań | 30 dni |
| Maksymalna liczba równoczesnych zadań | 10 (warstwa S3), 5 (warstwa S2), 1 (warstwa S1) |
| Maksymalna liczba dodatkowych punktów końcowych | 10 (dla S1, S2 i S3) |
| Maksymalna liczba reguł rozsyłania komunikatów | 100 (dla S1, S2 i S3) |
| Maksymalna liczba jednocześnie podłączonych strumieni urządzeń | 50 (tylko dla S1, S2, S3 i F1) |
| Maksymalny transfer danych strumienia urządzenia | 300 MB dziennie (tylko dla S1, S2, S3 i F1) |

> [!NOTE]
> Jeśli potrzebujesz więcej niż 100 płatnych centrów IoT w ramach subskrypcji platformy Azure, skontaktuj się z pomocą techniczną firmy Microsoft.

> [!NOTE]
> Obecnie całkowita liczba urządzeń plus moduły, które mogą być zarejestrowane w jednym centrum IoT jest ograniczona do 1 000 000. Jeśli chcesz zwiększyć ten limit, skontaktuj się z [pomocą techniczną firmy Microsoft](https://azure.microsoft.com/support/options/).

Centrum IoT ogranicza żądania po przekroczeniu następujących przydziałów.

| Ograniczenie | Wartość na centrum |
| --- | --- |
| Operacje rejestru tożsamości <br/> (tworzenie, pobieranie, listy, aktualizowanie i usuwanie), <br/> import/eksport zbiorczy lub pojedynczy |83,33/s/jednostka (5000/min/jednostka) (dla S3). <br/> 1,67/s/jednostka (100/min/jednostka) (dla S1 i S2). |
| Połączenia urządzenia |6000/s/jednostka (dla S3), 120/s/jednostka (dla S2), 12/s/jednostka (dla S1). <br/>Minimum 100/sek. |
| Liczba elementów wysłanych z urządzenia do chmury |6000/s/jednostka (dla S3), 120/s/jednostka (dla S2), 12/s/jednostka (dla S1). <br/>Minimum 100/sek. |
| Liczba elementów wysłanych z chmury do urządzenia | 83,33/s/jednostka (5000/min/jednostka) (dla S3), 1,67/s/jednostka (100/min/jednostka) (dla S1 i S2). |
| Liczba odebranych elementów wysłanych z chmury do urządzenia |833,33/s/jednostka (50 000/min/jednostka) (dla S3), 16,67/s/jednostka (1000/min/jednostka) (dla S1 i S2). |
| Operacje przekazywania plików |83.33 inicjowania przesyłania plików/s/jednostki (5000/min/jednostka) (dla S3), 1,67 inicjacji przekazywania plików/s/jednostki (100/min/jednostka) (dla S1 i S2). <br/> 10 000 identyfikatorów URI sygnatury dostępu Współdzielonego może być obecnie dla konta usługi Azure Storage w tym czasie.<br/> Jednocześnie może istnieć 10 identyfikatorów URI sygnatury dostępu współdzielonego. |
| Metody bezpośrednie | 24 MB/s/jednostki (dla S3), 480 KB/s/jednostki (dla S2), 160 KB/s/jednostka (dla S1).<br/> Na podstawie rozmiaru miernika ograniczania przepustowości 8 KB. |
| Liczba odczytów bliźniaczej reprezentacji urządzenia | 500/s/jednostka (dla S3), maksymalnie 100/s lub 10/s/jednostka (dla S2), 100/s (dla S1) |
| Liczba aktualizacji bliźniaczej reprezentacji urządzenia | 250/s/jednostka (dla S3), maksymalnie 50/s lub 5/s/jednostka (dla S2), 50/s (dla S1) |
| Operacje zadań <br/> (tworzenie, aktualizowanie, listy i usuwanie) | 83,33/s/jednostka (5000/min/jednostka) (dla S3), 1,67/s/jednostka (100/min/jednostka) (dla S2), 1,67/s/jednostka (100/min/jednostka) (dla S1). |
| Przepływność operacji zadań poszczególnych urządzeń | 50/s/jednostka (dla S3), maksymalnie 10/s lub 1/s/jednostka (dla S2), 10/s (dla S1). |
| Szybkość inicjowania strumienia urządzenia | 5 nowych strumieni/s (tylko dla S1, S2, S3 i F1). |
