---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 2/4/2019
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: c3bb0c7eb6effeb5165eace565a9db5dac1d39d6
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553820"
---
| Zasób | Limit domyślny |
| --- | --- |
| Ilość przetworzonych danych |1000 TB/Zapora/miesiąc. <sup>1</sup> |
|Reguły|10 000 operacji, wszystkie reguły, typy w połączeniu.|
|Globalnej komunikacji równorzędnej|Nieobsługiwane. Musisz mieć co najmniej jedno wdrożenie zapory na region.|
|Minimalny rozmiar AzureFirewallSubnet |/26|
|Zakres portów w regułach sieci i aplikacji|0-64,000. Praca jest w toku złagodzenie tego ograniczenia.|
|


<sup>1</sup>Jeśli trzeba zwiększyć te limity, skontaktuj się z działem pomocy technicznej systemu Azure.
