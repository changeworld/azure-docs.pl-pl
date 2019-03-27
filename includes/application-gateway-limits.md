---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 3/26/2019
ms.author: victorh
ms.openlocfilehash: 5ad1339c04444bcb4cc550be26e239e65227d2ce
ms.sourcegitcommit: fbfe56f6069cba027b749076926317b254df65e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58495046"
---
| Zasób | Limit domyślny | Uwaga |
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
