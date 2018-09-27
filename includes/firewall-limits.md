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
ms.openlocfilehash: 0a4a66543b323b1dbe56158851dafcb1dfe695f0
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2018
ms.locfileid: "47185333"
---
| Zasób | Limit domyślny |
| --- | --- |
| Ilość przetworzonych danych |1000 TB/Zapora/miesiąc <sup>1</sup> |
|Reguły|10 k aplikacji, reguły 10 k sieci|
|Komunikacja równorzędna sieci wirtualnych|W implementacji gwiazdy maksymalnie z 50 sieci wirtualne będące szprychami.|
|Globalnej komunikacji równorzędnej|Nieobsługiwane. Musisz mieć co najmniej jedno wdrożenie zapory na region.|
|Maksymalna liczba portów w regule pojedynczej sieci|15<br>Zakres portów (na przykład: 2 – 10) jest liczone jako dwa.


<sup>1</sup> kontakt z pomocą techniczną platformy Azure w przypadku zachodzi konieczność zwiększenia limitów.
