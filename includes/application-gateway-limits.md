---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 3/26/2019
ms.author: victorh
ms.openlocfilehash: 65ed28c967164be4d239cd4d59b6b36f06caeced
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66238219"
---
| Resource | Domyślne/maksymalny limit | Uwaga |
| --- | --- | --- |
| Azure Application Gateway |1000 na subskrypcję | |
| Konfiguracje adresów IP frontonu |2 |1 publiczna i 1 prywatna |
| Porty frontonu |100<sup>1</sup> | |
| Pule adresów zaplecza |100<sup>1</sup> | |
| Serwery zaplecza na pulę |1,200 | |
| Odbiorniki HTTP |100<sup>1</sup> | |
| Reguły równoważenia obciążenia HTTP |100<sup>1</sup> | |
| Ustawienia HTTP zaplecza |100<sup>1</sup> | |
| Wystąpienia na bramę |32 | |
| Certyfikaty SSL |100<sup>1</sup> |1 na odbiorniki HTTP |
| Maksymalny rozmiar certyfikat protokołu SSL |W WERSJI 1 JEDNOSTKA SKU - 10 KB<br>W WERSJI 2 JEDNOSTKA SKU - 25 KB| |
| Certyfikaty uwierzytelniania |100 | |
| Zaufane certyfikaty główne |100 | |
| Minimalny limit czasu żądania |1 sekunda | |
| Maksymalny limit czasu żądania |24 godziny | |
| Liczba witryn |100<sup>1</sup> |1 na odbiorniki HTTP |
| Mapy adresów URL na odbiornik |1 | |
| Maksymalna reguł opartych na ścieżkach na adres URL mapy|100||
| Konfiguracje przekierowania |100<sup>1</sup>| |
| Równoczesnych połączeń protokołu WebSocket |Średnie bram 20k<br> Duże bram 50k| |
| Maksymalna długość adresu URL|8000||
| Maksymalny rozmiar przekazywanych plików, standardowy |2 GB | |
| Maksymalny przekazywania rozmiar zapory aplikacji sieci Web |Średnie bramy zapory aplikacji sieci Web, 100 MB<br>Duże bramy zapory aplikacji sieci Web, 500 MB| |
| Limit rozmiaru treści zapory aplikacji sieci Web, bez plików|128 KB||

<sup>1</sup> w przypadku jednostek SKU dla komputerów z obsługą zapory aplikacji sieci Web, zaleca się ograniczenie liczby zasobów, na 40 pod kątem optymalnej wydajności.
