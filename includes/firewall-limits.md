---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 01/22/2020
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 0df38533afe97f010d1050c3ee2a4a69a54d4cc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335096"
---
| Zasób | Limit |
| --- | --- |
| Przepływność danych |30 Gb/s<sup>1</sup> |
|Reguły|10,000. Wszystkie typy reguł połączone.|
|Maksymalne reguły DNAT|299|
|Minimalny rozmiar usługi AzureFirewallSubnet |/26|
|Zakres portów w regułach sieci i aplikacji|0-64,000. Trwają prace nad złagodzeniem tego ograniczenia.|
|Publiczne adresy IP|Maksymalnie 100 (obecnie porty SNAT są dodawane tylko dla pierwszych pięciu publicznych adresów IP).|
|Tabela tras|Domyślnie AzureFirewallSubnet ma trasę 0.0.0.0/0 z wartością NextHopType ustawioną na **Internet.**<br><br>Zapora platformy Azure musi mieć bezpośrednią łączność z Internetem. Jeśli azurefirewallsubnet uczy się domyślnej trasy do sieci lokalnej za pośrednictwem protokołu BGP, należy zastąpić to z 0.0.0.0/0 UDR z **NextHopType** wartość ustawiona jako **Internet** do utrzymania bezpośredniej łączności z Internetem. Domyślnie zapora platformy Azure nie obsługuje wymuszonego tunelowania do sieci lokalnej.<br><br>Jeśli jednak konfiguracja wymaga wymuszonego tunelowania do sieci lokalnej, firma Microsoft będzie obsługiwać ją indywidualnie dla każdego przypadku. Skontaktuj się z pomocą techniczną, abyśmy mogli przejrzeć Twoją sprawę. Jeśli zostanie zaakceptowana, zezwolimy na subskrypcję i zapewnimy, że wymagana łączność z Internetem zapory jest utrzymywana.|

<sup>1.</sup> Jeśli chcesz zwiększyć te limity, skontaktuj się z pomocą techniczną platformy Azure.
