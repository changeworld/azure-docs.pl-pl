---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 11/09/2018
ms.author: victorh
ms.openlocfilehash: 3d66d825306c5183bdd8d8e611d98904eef2022a
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/27/2018
ms.locfileid: "52440341"
---
| Zasób | Limit domyślny | Uwaga |
| --- | --- | --- |
| Application Gateway |1000 na subskrypcję | |
| Konfiguracja adresu IP frontonu |2 |1 publiczna i 1 prywatna |
| Porty frontonu |40 | |
| Pule adresów zaplecza |40 | |
| Serwery zaplecza na pulę |1200 | |
| Odbiorniki HTTP |40 | |
| Reguły równoważenia obciążenia HTTP |400 |Liczba odbiorników HTTP * n |
| Ustawienia zaplecza HTTP |40 | |
| Wystąpienia na bramę |75 | |
| Certyfikaty SSL |40 |1 na odbiorniki HTTP |
| Certyfikaty uwierzytelniania |40 | |
| Minimalny limit czasu żądania |1 sekunda | |
| Maksymalny limit czasu żądania |24 godzin | |
| Liczba witryn |40 |1 na odbiorniki HTTP |
| Mapy adresów URL na odbiornik |1 | |
| Maksymalna reguł opartych na ścieżkach na adres URL mapy|100|
| Konfiguracje przekierowania |40| |
| Połączeń współbieżnych protokołu WebSocket. |5000| |
|Maksymalna długość adresu URL|8000|
| Maksymalny rozmiar przekazywanych plików Standard |2 GB | |
| Maksymalny przekazywania rozmiar zapory aplikacji sieci Web |Średnie zapory aplikacji sieci Web bramy — 100 MB<br>Bramy duży zapory aplikacji sieci Web — 500 MB| |
|Limit rozmiaru treści zapory aplikacji sieci Web (bez plików)|128 KB|
