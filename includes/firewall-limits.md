---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 7/16/2019
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: d4797232a51739238a88576a1fdd95bc62d6afaa
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2019
ms.locfileid: "71975330"
---
| Zasób | Limit domyślny |
| --- | --- |
| Przepływność danych |30 GB/s<sup>1</sup> |
|Reguły|10 000 wszystkie typy reguł są łączone.|
|Minimalny rozmiar AzureFirewallSubnet |/26|
|Zakres portów w regułach sieci i aplikacji|0 – 64000. Trwają prace, aby osłabić to ograniczenie.|
|Publiczne adresy IP|100 maksymalny (obecnie porty protokołu reportowego są dodawane tylko dla pierwszych pięciu publicznych adresów IP).|
|Tabela tras|Domyślnie AzureFirewallSubnet ma trasę 0.0.0.0/0 z wartością NextHopType ustawioną na wartość **Internet**.<br><br>Zapora platformy Azure musi mieć bezpośrednią łączność z Internetem. Jeśli AzureFirewallSubnet nauczy trasy domyślnej do sieci lokalnej za pośrednictwem protokołu BGP, należy przesłonić wartość przy użyciu wartości 0.0.0.0/0 UDR z wartością **NextHopType** ustawioną jako **Internet** , aby zachować bezpośrednią łączność z Internetem. Domyślnie Zapora platformy Azure nie obsługuje wymuszonego tunelowania do sieci lokalnej.<br><br>Jeśli jednak konfiguracja wymaga wymuszonego tunelowania do sieci lokalnej, firma Microsoft będzie obsługiwać ją w przypadku poszczególnych przypadków. Skontaktuj się z pomocą techniczną, aby umożliwić nam zapoznanie się z Twoim przypadkiem. Jeśli zostanie zaakceptowana, zezwolimy na subskrypcję i upewnimy się, że jest utrzymywana wymagana łączność internetowa zapory.|

<sup>1</sup> Jeśli musisz zwiększyć te limity, skontaktuj się z pomocą techniczną platformy Azure.
