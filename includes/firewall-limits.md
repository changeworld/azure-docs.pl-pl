---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 12/14/2018
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 7d905550114bb76a0a091146b3972bab4a652022
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/14/2018
ms.locfileid: "53430016"
---
| Zasób | Limit domyślny |
| --- | --- |
| Ilość przetworzonych danych |1000 TB/Zapora/miesiąc <sup>1</sup> |
|Reguły|10 KB — wszystkie reguły w połączeniu typów|
|Globalnej komunikacji równorzędnej|Nieobsługiwane. Musisz mieć co najmniej jedno wdrożenie zapory na region.|
|Maksymalna liczba portów w regule pojedynczej sieci|15<br>Zakres portów (na przykład: 2 - 10) jest liczone jako dwa.
|Minimalny rozmiar AzureFirewallSubnet |/26|
|Zakres portów w zasadach sieci i aplikacji|0 – 64 000. Praca jest w toku złagodzenie tego ograniczenia.|
|


<sup>1</sup> kontakt z pomocą techniczną platformy Azure w przypadku zachodzi konieczność zwiększenia limitów.
