---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 11/09/2018
ms.author: victorh
ms.openlocfilehash: 66dea07a1ff725c6707b19bc6ebdc5563f1b158b
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2019
ms.locfileid: "54211886"
---
| Zasób | Limit domyślny | Uwaga |
| --- | --- | --- |
| Application Gateway |1000 na subskrypcję | |
| Konfiguracje adresów IP frontonu |2 |1 publiczna i 1 prywatna |
| Porty frontonu |100<sup>1</sup> | |
| Pule adresów zaplecza |100<sup>1</sup> | |
| Serwery zaplecza na pulę |1200 | |
| Odbiorniki HTTP |100<sup>1</sup> | |
| Reguły równoważenia obciążenia HTTP |100<sup>1</sup> | |
| Ustawienia HTTP zaplecza |100<sup>1</sup> | |
| Wystąpienia na bramę |32 | |
| Certyfikaty SSL |100<sup>1</sup> |1 na odbiorniki HTTP |
| Certyfikaty uwierzytelniania |100 | |
| Zaufane certyfikaty główne |100 | |
| Minimalny limit czasu żądania |1 sekunda | |
| Maksymalny limit czasu żądania |24 godzin | |
| Liczba witryn |100<sup>1</sup> |1 na odbiorniki HTTP |
| Mapy adresów URL na odbiornik |1 | |
| Maksymalna reguł opartych na ścieżkach na adres URL mapy|100||
| Konfiguracje przekierowania |100<sup>1</sup>| |
| Równoczesnych połączeń protokołu WebSocket |5000| |
| Maksymalna długość adresu URL|8000||
| Maksymalny rozmiar przekazywanych plików Standard |2 GB | |
| Maksymalny przekazywania rozmiar zapory aplikacji sieci Web |Średnie zapory aplikacji sieci Web bramy — 100 MB<br>Bramy duży zapory aplikacji sieci Web — 500 MB| |
| Limit rozmiaru treści zapory aplikacji sieci Web (bez plików)|128 KB||

<sup>1</sup> w przypadku komputerów z obsługą zapory aplikacji sieci Web jednostki SKU zaleca się ograniczenie liczby zasobów, na 40 pod kątem optymalnej wydajności.
