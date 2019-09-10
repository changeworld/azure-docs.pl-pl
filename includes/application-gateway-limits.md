---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 6/5/2019
ms.author: victorh
ms.openlocfilehash: cd0ec07ebfffc839fedcdc76c9aff8e86556a7de
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70130106"
---
| Resource | Domyślny/maksymalny limit | Uwaga |
| --- | --- | --- |
| Azure Application Gateway |1 000 na subskrypcję | |
| Konfiguracje adresów IP frontonu |2 |1 publiczna i 1 prywatna |
| Porty frontonu |100<sup>1</sup> | |
| Pule adresów zaplecza |100<sup>1</sup> | |
| Serwery zaplecza na pulę |1,200 | |
| Odbiorniki HTTP |100<sup>1</sup> | |
| Reguły równoważenia obciążenia HTTP |100<sup>1</sup> | |
| Ustawienia protokołu HTTP zaplecza |100<sup>1</sup> | |
| Wystąpienia na bramę |32 | |
| Certyfikaty SSL |100<sup>1</sup> |1 na odbiorniki HTTP |
| Maksymalny rozmiar certyfikatu SSL |JEDNOSTKA SKU V1 — 10 KB<br>WERSJA 2 SKU — 16 KB| |
| Certyfikaty uwierzytelniania |100 | |
| Zaufane certyfikaty główne |100 | |
| Minimalny limit czasu żądania |1 s | |
| Maksymalny limit czasu żądania |24 godz. | |
| Liczba witryn |100<sup>1</sup> |1 na odbiorniki HTTP |
| Mapowania adresów URL na odbiornik |1 | |
| Maksymalna liczba reguł opartych na ścieżce na mapę URL|100||
| Konfiguracja przekierowania |100<sup>1</sup>| |
| Współbieżne połączenia protokołu WebSocket |Średnie bramy 20 000<br> Duże bramy 50 000| |
| Maksymalna długość adresu URL|8000||
| Maksymalny rozmiar przekazywania pliku, standardowa |2 GB | |
| Maksymalny rozmiar przekazywania pliku WAF |Średnie bramy WAF, 100 MB<br>Duże WAFe bramy, 500 MB| |
| Limit rozmiaru treści WAF, bez plików|128 KB||
| Maksymalna liczba reguł niestandardowych WAF|100||
| Maksymalne wykluczenia WAF|100||

<sup>1</sup> w przypadku jednostek SKU z obsługą WAF zaleca się ograniczenie liczby zasobów do 40 w celu uzyskania optymalnej wydajności.
