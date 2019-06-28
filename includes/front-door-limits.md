---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: frontdoor
author: sharad4u
ms.service: frontdoor
ms.topic: include
ms.date: 05/09/2019
ms.author: sharadag
ms.custom: include file
ms.openlocfilehash: deca0034996f6c8ddcac71cd4f191c1a0659b655
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2019
ms.locfileid: "67333386"
---
| Resource | Domyślne/maksymalny limit |
| --- | --- |
| Zasoby platformy Azure usługa drzwiami frontowymi na subskrypcję | 100 |
| Hosty frontonu, które obejmują domeny niestandardowe dla każdego zasobu | 100 |
| Reguły routingu dla każdego zasobu | 100 |
| Pule zaplecza dla każdego zasobu | 50 |
| Zaplecza na pulę zaplecza | 100 |
| Wzorców ścieżki odpowiadający regule routingu | 25 |
| Reguł zapory aplikacji sieci web niestandardowego zgodnie z zasadami | 10 |
| Zasady zapory aplikacji sieci Web dla każdego zasobu | 100 |
| Warunki dopasowań zapory aplikacji sieci Web na niestandardową regułę | 10 |
| Zakresy adresów IP zapory aplikacji sieci Web na dopasować stan | 600 |
| Wartości dopasowania ciągu zapory aplikacji sieci Web na warunek dopasowania | 10 |
| Długość wartości dopasowanie ciągu zapory aplikacji sieci Web | 256 |
| Zapora aplikacji sieci Web długość nazwy parametru treści wpisu | 256 |
| Zapora aplikacji sieci Web długość nazwy nagłówka HTTP | 256 |
| Długość nazwy pliku cookie zapory aplikacji sieci Web | 256 |
| Aplikacja zapory HTTP żądania rozmiar treści inspekcji w sieci Web | 128 KB |
| Długość ciała niestandardową odpowiedź zapory aplikacji sieci Web | 2 KB |

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

### <a name="other-limits"></a>Inne limity
- Maksymalny rozmiar adresu URL — 8192 bajtów - określa maksymalną długość pierwotnym adresem URL (schemat + nazwy hosta i portu + ścieżki + ciągu adresu URL zapytania) — rozmiar maksymalny ciąg zapytania — 4096 bajtów - określa maksymalną długość ciągu zapytania w bajtach.