---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 1bdf73dc6a4edf0c170b51e70fca2128d22e0eb8
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2019
ms.locfileid: "59055684"
---
W poniższej tabeli wymieniono limity skojarzone z różnymi warstwami usług S1, S2, S3 i F1. Aby uzyskać informacje o koszcie każdej *jednostki* w poszczególnych warstwach, zobacz [cennika usługi Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

| Zasób | Standardowa S1 | Standardowa S2 | Standardowa S3 | Bezpłatna F1 |
| --- | --- | --- | --- | --- |
| Komunikaty/dzień |400 000 |6 000 000 |300 000 000 |8000 |
| Maksymalna liczba jednostek |200 |200 |10 |1 |

> [!NOTE]
> Jeśli przewidujesz używanie ponad 200 jednostek z Centrum w warstwie S1 lub S2 albo 10 jednostek z Centrum w warstwie S3, skontaktuj się z Microsoft Support.
> 
> 

W poniższej tabeli wymieniono ograniczenia, które są stosowane do zasobów usługi IoT Hub.

| Zasób | Limit |
| --- | --- |
| Maksymalna liczba płatnych centrów IoT na subskrypcję platformy Azure |50 |
| Maksymalna liczba bezpłatnych centrów IoT na subskrypcję platformy Azure |1 |
| Maksymalna liczba znaków w identyfikatorze urządzenia | 128 |
| Maksymalna liczba tożsamości urządzeń<br/> zwróconych w pojedynczym wywołaniu |1000 |
| Maksymalny okres przechowywania komunikatów usługi IoT Hub dla komunikatów przesyłanych z urządzenia do chmury |7 dni |
| Maksymalny rozmiar komunikatu przesyłanego z urządzenia do chmury |256 KB |
| Maksymalny rozmiar partii przesyłanej z urządzenia do chmury |Protokół AMQP i HTTP: 256 KB dla całej usługi batch <br/>MQTT: 256 KB dla każdego komunikatu |
| Maksymalna liczba komunikatów w partii przesyłanej z urządzenia do chmury |500 |
| Maksymalny rozmiar komunikatu przesyłanego z chmury do urządzenia |64 KB |
| Maksymalny czas wygaśnięcia komunikatu przesyłanego z chmury do urządzenia |2 dni |
| Maksymalna liczba dostarczonych komunikatów przesyłanych <br/> z chmury do urządzenia |100 |
| Maksymalna liczba dostarczonych komunikatów zwrotnych <br/> w odpowiedzi na komunikaty przesłane z chmury do urządzenia |100 |
| Maksymalny czas wygaśnięcia komunikatów zwrotnych <br/> w odpowiedzi na komunikaty przesłane z chmury do urządzenia |2 dni |
| Maksymalny rozmiar bliźniaczej reprezentacji urządzenia <br/> (tagi, zgłoszone właściwości i żądane właściwości) | 8 KB |
| Maksymalny rozmiar wartości ciągu bliźniaczej reprezentacji urządzenia | 4 KB |
| Maksymalna głębokość obiektu w bliźniaczej reprezentacji urządzenia | 5 |
| Maksymalny rozmiar ładunku metody bezpośredniej | 128 KB |
| Maksymalny okres przechowywania historii zadań | 30 dni |
| Maksymalna liczba równoczesnych zadań | 10 (warstwa S3), 5 (warstwa S2), 1 (warstwa S1) |
| Maksymalna liczba dodatkowych punktów końcowych | 10 (warstwy S1, S2 i S3) |
| Maksymalna liczba reguł rozsyłania komunikatów | 100 (warstwy S1, S2 i S3) |
| Maksymalna liczba równocześnie połączonych urządzeń, strumieni | 50 (dla S1, S2, S3 i tylko F1) |
| Transfer danych strumienia maksymalna urządzenia | 300 MB dziennie (S1, S2, S3 i tylko F1) |


> [!NOTE]
> Jeśli potrzebujesz więcej niż 50 płatnych centrów IoT w subskrypcji platformy Azure, skontaktuj się z Microsoft Support.


> [!NOTE]
> Obecnie maksymalna liczba urządzeń, którymi można nawiązać połączenie w jednym centrum IoT jest 1 000 000. Jeśli chcesz zwiększyć ten limit, skontaktuj się z [Microsoft Support](https://azure.microsoft.com/support/options/).

Usługa IoT Hub ogranicza żądania po przekroczeniu następujących limitów przydziału.

| Ograniczenie | Wartość na centrum |
| --- | --- |
| Operacje rejestru tożsamości <br/> (Tworzenie, pobieranie, listy, aktualizacji i usuwania), <br/> import/eksport zbiorczy lub pojedynczy |83.33/sec/Unit (5000/min/jednostkę) (w przypadku S3). <br/> 1.67/sec/Unit (100/min/jednostkę) (dla warstwy S1 i S2). |
| Połączenia urządzenia |6000/sek/jednostkę (warstwa S3), 120/sek/jednostkę (warstwa S2), 12/sek/jednostkę (warstwa S1). <br/>Minimum 100/sek. |
| Liczba elementów wysłanych z urządzenia do chmury |6000/sek/jednostkę (warstwa S3), 120/sek/jednostkę (warstwa S2), 12/sek/jednostkę (warstwa S1). <br/>Minimum 100/sek. |
| Liczba elementów wysłanych z chmury do urządzenia | 83.33/sec/Unit (5000/min/jednostkę) (warstwa S3), 1.67/sec/unit (100/min/jednostkę) (dla warstwy S1 i S2). |
| Liczba odebranych elementów wysłanych z chmury do urządzenia |833.33/sec/Unit (50 000/min/jednostkę) (warstwa S3), 16.67/sec/unit (1000/min/jednostkę) (dla warstwy S1 i S2). |
| Operacje przekazywania plików |83.33 pliku przekazywania powiadomień/sek/jednostkę (5000/min/jednostkę) (warstwa S3), 1.67 pliku przekazywania powiadomień/sek/jednostkę (100/min/jednostkę) (warstwy S1 i S2). <br/> 10 000 identyfikatorów URI może istnieć dla konta usługi Azure Storage w tym samym czasie.<br/> Jednocześnie może istnieć 10 identyfikatorów URI sygnatury dostępu współdzielonego. |
| Metody bezpośrednie | 24 MB/s/jednostkę (warstwa S3), 480 KB/s/jednostkę (warstwa S2), 160 KB/s/jednostkę (warstwa S1).<br/> Na podstawie ograniczania rozmiaru miernika 8 KB. |
| Liczba odczytów bliźniaczej reprezentacji urządzenia | 500/sek/jednostkę (warstwa S3), maksymalnie 100/sek lub 10/sek/jednostkę (warstwa S2), 100/sek (warstwa S1) |
| Liczba aktualizacji bliźniaczej reprezentacji urządzenia | 250/sek/jednostkę (warstwa S3), maksymalnie 50/sek lub 5/sek/jednostkę (warstwa S2), 50/sek (warstwa S1) |
| Operacje zadań <br/> (Tworzenie, aktualizowanie, listy i Usuń) | 83.33/sec/Unit (5000/min/jednostkę) (warstwa S3), 1.67/sec/unit (100/min/jednostkę) (dla S2), 1.67/sec/unit (100/min/jednostkę) (dla S1). |
| Przepływność operacji zadań poszczególnych urządzeń | 50/sek/jednostkę (warstwa S3), maksymalnie 10/sek lub 1/sek/jednostkę (warstwa S2), 10/sek (warstwa S1). |
| Szybkość inicjowania strumienia urządzenia | 5 nowych strumieni/sek (S1, S2, S3 i tylko F1). |
