---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 3/14/2019
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 3e6bde63c5f759ec711b243fc25fa0ed8bde634a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2019
ms.locfileid: "58016474"
---
| Zasób | Limit domyślny |
| --- | --- |
| Przepływność danych |30 GB/s<sup>1</sup> |
|Reguły|10 000 operacji, wszystkie reguły, typy w połączeniu.|
|Globalnej komunikacji równorzędnej|Nieobsługiwane. Musisz mieć co najmniej jedno wdrożenie zapory na region.|
|Minimalny rozmiar AzureFirewallSubnet |/26|
|Zakres portów w regułach sieci i aplikacji|0-64,000. Praca jest w toku złagodzenie tego ograniczenia.|
|


<sup>1</sup>Jeśli trzeba zwiększyć te limity, skontaktuj się z działem pomocy technicznej systemu Azure.
