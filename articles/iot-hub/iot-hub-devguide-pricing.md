---
title: Zrozumienie cennik Centrum IoT Azure | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera po — informacje o jak pomiaru i cenach współpracuje z tym Centrum IoT poszło przykłady.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 672adba0b4b17a25c0c0a1da3596808f767e3fbd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34632843"
---
# <a name="azure-iot-hub-pricing-information"></a>Informacje o cenach Centrum IoT Azure

[Centrum IoT Azure cennik] [ lnk-pricing] zawiera ogólne informacje dotyczące różnych jednostki SKU i cenach dla Centrum IoT. Ten artykuł zawiera więcej informacji na temat sposobu różne funkcje Centrum IoT są mierzone jako wiadomości przez Centrum IoT.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="charges-per-operation"></a>Opłaty na operację

| Operacja | Informacje o rozliczeniach | 
| --------- | ------------------- |
| Operacje rejestru tożsamości <br/> (Tworzenie, pobieranie, listy, aktualizowanie i usuwanie) | Nie zostanie obciążona. |
| Komunikaty z urządzenia do chmury | Pomyślnie wysłane wiadomości są naliczane w fragmentów 4 KB na ruch przychodzący do Centrum IoT. Na przykład komunikat 6 KB jest pobierana, 2 wiadomości. |
| Komunikaty chmury do urządzenia | Pomyślnie wysłane wiadomości są pobierane w fragmentów 4 KB, na przykład wiadomości 6 KB rozliczany 2 wiadomości. |
| Przekazywania plików | Transfer plików do magazynu Azure nie jest pomiaru przez Centrum IoT. Komunikaty rozpoczęcia i zakończenia transferu plików są naliczane, ponieważ messaged mierzonego w przyrostach 4 KB. Na przykład przesyłania pliku 10 MB jest pobierana dwa komunikaty oraz koszt usługi Azure Storage. |
| Metody bezpośrednie | Metoda pomyślnego żądania są naliczane w fragmentów 4 KB, odpowiedzi z treściami niepustym są naliczane w fragmentów 4 KB jako dodatkowe komunikaty. Żądania do odłączonego urządzenia są naliczane jako komunikaty w fragmentów 4 KB. Na przykład metody z treścią 6 KB, który daje w odpowiedzi nie jednostki z urządzenia, jest rozliczany jako dwa komunikaty. Metoda o treści 6 KB, który daje w odpowiedzi 1 KB z urządzenia jest rozliczany jako dwa komunikaty żądania plus inny komunikat odpowiedzi. |
| Odczytuje dwie modułu i urządzenia | Dwie odczytuje z urządzenia lub modułu i z powrotem rozwiązania zakończenia są naliczane jako komunikaty w fragmentów 512-bajtowego. Na przykład Odczyt dwie 6 KB jest rozliczany jako 12 wiadomości. |
| Urządzenia i moduł aktualizacji dwie (znaczniki i właściwości) | Dwie aktualizacje z urządzenia lub modułu i zaplecza rozwiązania są naliczane jako komunikaty w fragmentów 512-bajtowego. Na przykład Odczyt dwie 6 KB jest rozliczany jako 12 wiadomości. |
| Urządzenia i moduł dwie zapytań | Zapytania są naliczane jako komunikaty, w zależności od wielkości wynik w fragmentów 512-bajtowego. |
| Operacje zadań <br/> (tworzenie, aktualizowanie, wyświetlanie, usuwanie) | Nie zostanie obciążona. |
| Operacje na urządzenie zadania | Operacje zadania (na przykład dwie aktualizacje i metody) są naliczane normalnego. Na przykład zadanie wynikiem wywołania metody 1000 1 KB żądań i odpowiedzi pusty treści jest pobierana 1000 komunikatów. |

> [!NOTE]
> Wszystkie rozmiary są obliczane, biorąc pod uwagę rozmiar ładunku w bajtach (protokół ramek zostanie zignorowana). Komunikaty, które mają właściwości, oraz i treść, rozmiar jest obliczana w sposób niezależny od protokołu. Aby uzyskać więcej informacji, zobacz [Centrum IoT wiadomości przewodnik dewelopera][lnk-message-size].

## <a name="example-1"></a>Przykład #1

Urządzenie wysyła jeden komunikat urządzenia do chmury 1 KB na minutę do Centrum IoT, który jest odczytywany przez usługi Azure Stream Analytics. Zaplecze rozwiązania wywołuje metodę (z 512-bajtowych ładunku) na urządzeniu co 10 minut do wyzwalania określonych akcji. Urządzenie odpowiada do metody z wynikiem 200 bajtów.

Urządzenie zużywa:

* Jeden komunikat * 60 minut * 24 godziny = 1440 wiadomości na dzień komunikatów urządzenia do chmury.
* Dwa żądania i odpowiedzi * 6 razy na godzinę * 24 godziny = 288 komunikatów dla metod.

Obliczona w ten sposób daje łączną liczbę komunikatów 1728 na dzień.

## <a name="example-2"></a>Przykład #2

Urządzenie wysyła jeden komunikat urządzenia do chmury 100 KB co godzinę. Aktualizuje również dwie jego urządzenia z 1 KB ładunków co cztery godziny. Rozwiązania kończyć, raz dziennie, odczytuje dwie 14 KB urządzenia i aktualizuje ładunków 512-bajtowych do zmiany konfiguracji.

Urządzenie zużywa:

* 25 wiadomości (100 KB/4 KB) * 24 godziny dla komunikatów urządzenia do chmury.
* Dwa komunikaty (o rozmiarze 1 KB/0,5 KB) * sześciu razy dziennie aktualizacje dwie urządzenia.

Obliczona w ten sposób daje łączną liczbę 612 wiadomości na dzień.

Zaplecza rozwiązania zużywa komunikaty 28 (14 KB/0,5 KB), aby odczytać dwie urządzenia, a także jeden komunikat, aby zaktualizować go, łącznie z 29 komunikatów.

Całkowita liczba urządzenia oraz zaplecza rozwiązania wykorzystywać 641 wiadomości na dzień.


[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[lnk-message-size]: iot-hub-devguide-messages-construct.md
