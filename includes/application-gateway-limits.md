---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 03/04/2020
ms.author: victorh
ms.openlocfilehash: a3fb3dbf026a696b9d472efcba139c371ff1e587
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334932"
---
| Zasób | Limit | Uwaga |
| --- | --- | --- |
| Azure Application Gateway |1000 na subskrypcję | |
| Konfiguracje adresów IP front-endu |2 |1 publiczna i 1 prywatna |
| Porty front-end |100<sup>1</sup> | |
| Pule adresów zaplecza |100<sup>1</sup> | |
| Serwery zaplecza na pulę |1200 | |
| Słuchacze HTTP |200<sup>1</sup> |Ograniczona do 100 aktywnych odbiorników, które routingu ruchu. Aktywne detektory = całkowita liczba słuchaczy - detektory nieaktywne.<br>Jeśli domyślna konfiguracja wewnątrz reguły routingu jest ustawiona na ruch marszruty (na przykład ma odbiornik, pulę zaplecza i ustawienia HTTP), to również liczy się jako odbiornik.|
| Reguły równoważenia obciążenia HTTP |100<sup>1</sup> | |
| Ustawienia http zaplecza |100<sup>1</sup> | |
| Wystąpienia na bramę |Jednostka SKU V1 - 32<br>Jednostka SKU V2 - 125 | |
| Certyfikaty SSL |100<sup>1</sup> |1 na odbiornik HTTP |
| Maksymalny rozmiar certyfikatu SSL |V1 SKU - 10 KB<br>V2 SKU - 16 KB| |
| Certyfikaty uwierzytelniania |100 | |
| Zaufane certyfikaty główne |100 | |
| Minimalny limit czasu żądania |1 sekunda | |
| Maksymalny limit czasu żądania |24 godziny | |
| Liczba witryn |100<sup>1</sup> |1 na odbiornik HTTP |
| Mapy adresów URL na detektor |1 | |
| Maksymalna liczba reguł opartych na ścieżce na mapę adresu URL|100||
| Konfiguracje przekierowania |100<sup>1</sup>| |
| Równoczesne połączenia WebSocket |Średnie bramy 20k<br> Duże bramy 50k| |
| Maksymalna długość adresu URL|32 KB| |
| Maksymalny rozmiar nagłówka dla protokołu HTTP/2 |4KB| |
| Maksymalny rozmiar przekazywania plików, standardowy |2 GB | |
| Maksymalny rozmiar przekazywania plików WAF |Bramy V1 Medium WAF, 100 MB<br>V1 Duże bramy WAF, 500 MB<br>V2 WAF, 750 MB| |
| Limit rozmiaru ciała WAF bez plików|128 KB||
| Maksymalne reguły niestandardowe WAF|100||
| Maksymalne wykluczenia WAF|100||

<sup>1</sup> W przypadku jednostek SKU z obsługą WAF zaleca się ograniczenie liczby zasobów do 40 w celu uzyskania optymalnej wydajności.
