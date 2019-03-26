---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: frontdoor
author: sharad4u
ms.service: frontdoor
ms.topic: include
ms.date: 9/17/2018
ms.author: sharadag
ms.custom: include file
ms.openlocfilehash: a3a43c56a49c243390eac964d31988b7d30fbb56
ms.sourcegitcommit: 280d9348b53b16e068cf8615a15b958fccad366a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407774"
---
| Zasób | Limit domyślny |
| --- | --- |
| Zasoby platformy Azure usługa drzwiami frontowymi na subskrypcję | 100 |
| Hosty frontonu, które obejmują domeny niestandardowe dla każdego zasobu | 100 |
| Reguły routingu dla każdego zasobu | 100 |
| Pule zaplecza dla każdego zasobu | 50 |
| Zaplecza na pulę zaplecza | 100 |
| Wzorców ścieżki odpowiadający regule routingu | 25 |
| Reguł zapory aplikacji sieci web niestandardowego zgodnie z zasadami | 10 |
| Zasady zapory aplikacji sieci Web dla każdego zasobu | 100 |

### <a name="timeout-values"></a>Wartości limitu czasu
#### <a name="client-to-front-door"></a>Klient drzwi
- Drzwiami frontowymi jest bezczynny limitu czasu połączenia TCP 61 sekund.

#### <a name="front-door-to-application-back-end"></a>Drzwi do zaplecza aplikacji
- Jeśli odpowiedź jest podzielony odpowiedzi, zwracany jest 200, jeśli lub po odebraniu pierwszego fragmentu.
- Po żądania HTTP są przekazywane do zaplecza, drzwiami frontowymi czeka na 30 sekund w przypadku pierwszego pakietu z zaplecza. Następnie zwraca błąd 503 do klienta.
- Po odebraniu pierwszego pakietu z zaplecza drzwiami frontowymi czeka na 30 sekund przed upływem limitu czasu bezczynności. Następnie zwraca błąd 503 do klienta.
- Drzwiami frontowymi sesji TCP zaplecza przekroczenie limitu czasu wynosi 30 minut.

### <a name="upload-and-download-data-limit"></a>Przekazywanie i pobieranie limitu danych

|  | Za pomocą (CTE) Kodowanie fragmentaryczne transferu | Bez segmentu HTTP |
| ---- | ------- | ------- |
| **Pobieranie** | Nie ma żadnego limitu na rozmiar pobieranych elementów. | Nie ma żadnego limitu na rozmiar pobieranych elementów. |
| **Przekazywanie** |  Nie ma żadnego limitu, tak długo, jak każde przekazanie CTE jest mniejszy niż 2 GB. | Rozmiar nie może być większa niż 2 GB. |
