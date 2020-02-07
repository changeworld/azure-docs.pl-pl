---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 6/5/2019
ms.author: victorh
ms.openlocfilehash: 6ab6c4c2051ccd2fbb22c383b9ca0af53ceb13d3
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/06/2020
ms.locfileid: "77054901"
---
| Zasób | Domyślny/maksymalny limit | Uwaga |
| --- | --- | --- |
| Azure Application Gateway |1 000 na subskrypcję | |
| Konfiguracje adresów IP frontonu |2 |1 publiczna i 1 prywatna |
| Porty frontonu |100<sup>1</sup> | |
| Pule adresów zaplecza |100<sup>1</sup> | |
| Serwery zaplecza na pulę |1,200 | |
| Odbiorniki HTTP |100<sup>1</sup> | |
| Reguły równoważenia obciążenia HTTP |100<sup>1</sup> | |
| Ustawienia protokołu HTTP zaplecza |100<sup>1</sup> | |
| Wystąpienia na bramę |JEDNOSTKA SKU V1 — 32<br>JEDNOSTKA SKU V2 — 125 | |
| Certyfikaty SSL |100<sup>1</sup> |1 na odbiornik HTTP |
| Maksymalny rozmiar certyfikatu SSL |JEDNOSTKA SKU V1 — 10 KB<br>WERSJA 2 SKU — 16 KB| |
| Certyfikaty uwierzytelniania |100 | |
| Zaufane certyfikaty główne |100 | |
| Minimalny limit czasu żądania |1 sekunda | |
| Maksymalny limit czasu żądania |24 godziny | |
| Liczba witryn |100<sup>1</sup> |1 na odbiornik HTTP |
| Mapowania adresów URL na odbiornik |1 | |
| Maksymalna liczba reguł opartych na ścieżce na mapę URL|100||
| Konfiguracja przekierowania |100<sup>1</sup>| |
| Współbieżne połączenia protokołu WebSocket |Średnie bramy 20 000<br> Duże bramy 50 000| |
| Maksymalna długość adresu URL|32 KB| |
| Maksymalny rozmiar nagłówka dla protokołu HTTP/2 |4 KB| |
| Maksymalny rozmiar przekazywania pliku, standardowa |2 GB | |
| Maksymalny rozmiar przekazywania pliku WAF |bramy WAF o rozmiarze V1, 100 MB<br>Duże bramy WAF w wersji 1, 500 MB<br>v2 WAF, 750 MB| |
| Limit rozmiaru treści WAF, bez plików|128 KB||
| Maksymalna liczba reguł niestandardowych WAF|100||
| Maksymalne wykluczenia WAF|100||

<sup>1</sup> w przypadku jednostek SKU z obsługą WAF zaleca się ograniczenie liczby zasobów do 40 w celu uzyskania optymalnej wydajności.
