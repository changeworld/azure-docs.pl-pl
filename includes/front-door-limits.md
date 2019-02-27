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
ms.openlocfilehash: 167e774b00bcfdb7bc1e26202bd7826f525a6ace
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2019
ms.locfileid: "56890406"
---
| Zasób | Limit domyślny |
| --- | --- |
| Zasoby drzwiami frontowymi na subskrypcję | 100 |
| Fronton hostów, w tym domen niestandardowych dla każdego zasobu | 100 |
| Reguły routingu dla każdego zasobu | 100 |
| Pule zaplecza dla każdego zasobu | 50 |
| Zaplecza na pulę zaplecza | 100 |
| Wzorców ścieżki odpowiadający regule routingu | 25 |
| Reguł zapory aplikacji sieci web niestandardowego zgodnie z zasadami | 10 |
| Zasady zapory aplikacji sieci Web dla każdego zasobu | 100 |

### <a name="timeout-values"></a>Wartości limitu czasu
#### <a name="client-to-front-door"></a>Klient drzwi
- Drzwiami frontowymi jest bezczynny limitu czasu połączenia TCP 61 sekund.

#### <a name="front-door-to-application-backend"></a>Drzwi do wewnętrznej bazy danych aplikacji
- Jeśli odpowiedź jest podzielony odpowiedzi, 200 zostaną zwrócone, jeśli / po odebraniu pierwszego fragmentu.
- Po żądania HTTP są przekazywane do zaplecza, drzwiami frontowymi będzie czekać na 30 sekund w przypadku pierwszego pakietu z wewnętrznej bazy danych, zanim zwróci błąd 503 do klienta.
- Po odebraniu pierwszego pakietu z wewnętrznej bazy danych drzwiami frontowymi czeka na 30 sekund (limit czasu bezczynności) przed zwróceniem błąd 503 do klienta.
- Drzwi do wewnętrznej bazy danych TCP — limit czasu sesji to 30 minut.

### <a name="upload--download-data-limit"></a>Przekazywanie / pobieranie limitu danych

|  | Za pomocą (CTE) Kodowanie fragmentaryczne transferu | Bez segmentu HTTP |
| ---- | ------- | ------- |
| **Pobieranie** | Nie ma żadnego limitu na rozmiar pobieranych elementów | Nie ma żadnego limitu na rozmiar pobieranych elementów |
| **Przekazywanie** |  Bez ograniczeń, tak długo, jak przekazać każdy CTE jest mniejszy niż 28.6 MB | Rozmiar nie może być większy niż 28.6 MB. |
