---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 11/26/2018
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 8b236bc1f0089b89aca90e7c69e9b445b01a374b
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/27/2018
ms.locfileid: "52440328"
---
| Zasób | Limit domyślny |
| --- | --- |
| Ilość przetworzonych danych |1000 TB/Zapora/miesiąc <sup>1</sup> |
|Reguły|10 KB — wszystkie reguły w połączeniu typów|
|Globalnej komunikacji równorzędnej|Nieobsługiwane. Musisz mieć co najmniej jedno wdrożenie zapory na region.|
|Maksymalna liczba portów w regule pojedynczej sieci|15<br>Zakres portów (na przykład: 2 – 10) jest liczone jako dwa.
|Minimalny rozmiar AzureFirewallSubnet |/26


<sup>1</sup> kontakt z pomocą techniczną platformy Azure w przypadku zachodzi konieczność zwiększenia limitów.
