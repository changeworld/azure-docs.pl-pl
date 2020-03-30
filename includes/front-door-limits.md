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
ms.openlocfilehash: 148ec3eccce71ab7a4a6c1391c0fa4753c248bd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334591"
---
| Zasób | Limit |
| --- | --- |
| Zasoby usługi Azure Front Door na subskrypcję | 100 |
| Hosty front-end, które obejmują domeny niestandardowe na zasób | 500 |
| Reguły routingu na zasób | 500 |
| Pule zaplecza na zasób | 50 |
| Zaplecze na pulę zaplecza | 100 |
| Wzorce ścieżek zgodne z regułą marszruty | 25 |
| Adresy URL w wywołaniu oczyszczania w jednej pamięci podręcznej | 100 |
| Niestandardowe reguły zapory aplikacji sieci Web dla zasad | 100 |
| Zasady zapory aplikacji sieci Web na subskrypcję | 100 |
| Warunki dopasowania zapory aplikacji sieci Web dla reguły niestandardowej | 10 |
| Zakresy adresów IP zapory aplikacji sieci Web na warunek dopasowania | 600 |
| Wartości dopasowania zapory zapory aplikacji sieci Web na warunek dopasowania | 10 |
| Długość wartości dopasowania zapory zapory aplikacji sieci Web | 256 |
| Długość nazwy treści zapory aplikacji sieci Web POST | 256 |
| Długość nazwy nagłówka HTTP zapory aplikacji sieci Web | 256 |
| Długość nazwy pliku cookie zapory aplikacji sieci Web | 256 |
| Kontrolowany rozmiar treści żądania HTTP zapory aplikacji sieci Web | 128 KB |
| Długość treści niestandardowej zapory aplikacji sieci Web | 2 KB |

### <a name="timeout-values"></a>Wartości limitu czasu
#### <a name="client-to-front-door"></a>Od klienta do drzwi wejściowych
* Drzwi wejściowe z osią losu mają limit czasu bezczynności połączenia TCP 61 sekund.

#### <a name="front-door-to-application-back-end"></a>Drzwi przednie do tylnego końca aplikacji
* Jeśli odpowiedź jest odpowiedzi fragmentami, 200 jest zwracany, jeśli lub po odebraniu pierwszego fragmentu.
* Po przesłaniu dalej żądania HTTP do zaplecza drzwi frontowe czekają przez 30 sekund na pierwszy pakiet z zaplecza. Następnie zwraca klientowi błąd 503. Ta wartość jest konfigurowalna za pośrednictwem pola sendRecvTimeoutSeconds w interfejsie API.
    * W przypadku scenariuszy buforowania ten limit czasu nie jest konfigurowalny, a więc jeśli żądanie jest buforowane i trwa więcej niż 30 sekund dla pierwszego pakietu z drzwi frontowych lub z wewnętrznej bazy danych, następnie błąd 504 jest zwracany do klienta. 
* Po odebraniu pierwszego pakietu z zaplecza drzwiami frontowymi odczekają 30 sekund w czasie bezczynnym. Następnie zwraca klientowi błąd 503. Ta wartość limitu czasu nie jest konfigurowalna.
* Przednie drzwi do limitu czasu sesji TCP zaplecza wynosi 90 sekund.

### <a name="upload-and-download-data-limit"></a>Limit danych przesyłania i pobierania

|  | Z kodowaniem transferu fragmentowego (CTE) | Bez fragmentowania http |
| ---- | ------- | ------- |
| **Pobierz** | Nie ma limitu rozmiaru pobierania. | Nie ma limitu rozmiaru pobierania. |
| **Przekaż** |    Nie ma limitu, o ile każde przesłanie CTE jest mniejsze niż 2 GB. | Rozmiar nie może być większy niż 2 GB. |

### <a name="other-limits"></a>Inne limity
* Maksymalny rozmiar adresu URL — 8192 bajtów — określa maksymalną długość pierwotnego adresu URL (schemat + nazwa hosta + port + ścieżka + ciąg zapytania adresu URL)
* Maksymalny rozmiar ciągu zapytania — 4096 bajtów — określa maksymalną długość ciągu zapytania w bajtach.
* Maksymalny rozmiar nagłówka odpowiedzi HTTP z adresu URL sondy kondycji — 4096 bajtów — określono maksymalną długość wszystkich nagłówków odpowiedzi sond kondycji. 
