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
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2019
ms.locfileid: "67333386"
---
| Resource | Domyślny/maksymalny limit |
| --- | --- |
| Zasoby usług frontonu platformy Azure na subskrypcję | 100 |
| Hosty frontonu, w tym domeny niestandardowe na zasób | 100 |
| Reguły routingu na zasób | 100 |
| Pule zaplecza na zasób | 50 |
| Zaplecza na pulę zaplecza | 100 |
| Wzorce ścieżek zgodne z regułą routingu | 25 |
| Niestandardowe reguły zapory aplikacji sieci Web według zasad | 10 |
| Zasady zapory aplikacji sieci Web dla zasobu | 100 |
| Warunki dopasowania zapory aplikacji sieci Web na regułę niestandardową | 10 |
| Zakresy adresów IP zapory aplikacji sieci Web na warunek dopasowania | 600 |
| Wartości parametrów zapory aplikacji sieci Web na warunek dopasowania | 10 |
| Długość wartości ciągu zapory aplikacji sieci Web | 256 |
| Długość nazwy parametru treści wpisu zapory aplikacji sieci Web | 256 |
| Długość nazwy nagłówka HTTP zapory aplikacji sieci Web | 256 |
| Długość nazwy pliku cookie zapory aplikacji sieci Web | 256 |
| Inspekcja rozmiaru treści żądania HTTP zapory aplikacji sieci Web | 128 KB |
| Długość treści niestandardowej odpowiedzi zapory aplikacji sieci Web | 2 KB |

### <a name="timeout-values"></a>Wartości limitu czasu
#### <a name="client-to-front-door"></a>Klient do przednich drzwi
- Drzwi z przodu mają limit czasu bezczynności połączenia TCP wynoszący 61 sekund.

#### <a name="front-door-to-application-back-end"></a>Tylne drzwi do zaplecza aplikacji
- Jeśli odpowiedź jest rozsegmentową odpowiedzią, zwracany jest 200, jeśli lub kiedy zostanie odebrany pierwszy fragment.
- Gdy żądanie HTTP zostanie przekazane do zaplecza, przód czeka przez 30 sekund na pierwszy pakiet od zaplecza. Następnie zwraca błąd 503 do klienta.
- Po odebraniu pierwszego pakietu od zaplecza drzwi z przodu czekają na 30 sekund w przekroczeniu limitu czasu bezczynności. Następnie zwraca błąd 503 do klienta.
- Przód do końca sesji protokołu TCP zaplecza to 30 minut.

### <a name="upload-and-download-data-limit"></a>Limit przekazywania i pobierania danych

|  | Z kodowaniem transferu fragmentarycznego (CTE) | Bez fragmentacji HTTP |
| ---- | ------- | ------- |
| **Pobieranie** | Nie ma żadnego limitu rozmiaru pobierania. | Nie ma żadnego limitu rozmiaru pobierania. |
| **Przesyłane** |  Nie ma żadnego limitu, o ile każde przesłanie CTE jest mniejsze niż 2 GB. | Rozmiar nie może być większy niż 2 GB. |

### <a name="other-limits"></a>Inne limity
- Maksymalny rozmiar adresu URL — 8 192 bajtów — określa maksymalną długość nieprzetworzonego adresu URL (schemat + nazwa hosta + port + ścieżka + ciąg zapytania w adresie URL) — maksymalny rozmiar ciągu zapytania — 4 096 bajtów — określa maksymalną długość ciągu zapytania w bajtach.