---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 7/30/2018
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: f1fd32fc02bee47d229093680166e6a13a5ba721
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2018
ms.locfileid: "49458850"
---
| Zasób | Limit domyślny |
| --- | --- |
| Ilość przetworzonych danych |1000 TB/Zapora/miesiąc <sup>1</sup> |
|Reguły|10 KB — wszystkie reguły w połączeniu typów|
|Komunikacja równorzędna sieci wirtualnych|W implementacji gwiazdy maksymalnie z 50 sieci wirtualne będące szprychami.|
|Globalnej komunikacji równorzędnej|Nieobsługiwane. Musisz mieć co najmniej jedno wdrożenie zapory na region.|
|Maksymalna liczba portów w regule pojedynczej sieci|15<br>Zakres portów (na przykład: 2 – 10) jest liczone jako dwa.


<sup>1</sup> kontakt z pomocą techniczną platformy Azure w przypadku zachodzi konieczność zwiększenia limitów.
