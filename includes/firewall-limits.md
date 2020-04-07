---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 04/03/2020
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 4972a80ec7c481ac7bc8860c005c8576ce37a090
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80758556"
---
| Zasób | Limit |
| --- | --- |
| Przepływność danych |30 Gb/s<sup>1</sup> |
|Reguły|10,000. Wszystkie typy reguł połączone.|
|Maksymalne reguły DNAT|298<br>Jeśli protokół reguły jest skonfigurowany dla protokołu TCP i UDP, jest on liczony jako dwie reguły.|
|Minimalny rozmiar usługi AzureFirewallSubnet |/26|
|Zakres portów w regułach sieci i aplikacji|0-64,000. Trwają prace nad złagodzeniem tego ograniczenia.|
|Publiczne adresy IP|Maksymalnie 100 (obecnie porty SNAT są dodawane tylko dla pierwszych pięciu publicznych adresów IP).|
|Adresy IP grup IP|50 grup IP lub mniej: maksymalnie 5000 pojedynczych adresów IP na wystąpienie zapory.<br>51 - 100 grup IP: 500 indywidualnych adresów IP na wystąpienie zapory.<br><br>Aby uzyskać więcej informacji, zobacz [Grupy ADRESÓW IP (wersja zapoznawcza) w Zaporze platformy Azure](../articles/firewall/ip-groups.md#ip-address-limits)
|Tabela tras|Domyślnie AzureFirewallSubnet ma trasę 0.0.0.0/0 z wartością NextHopType ustawioną na **Internet.**<br><br>Zapora platformy Azure musi mieć bezpośrednią łączność z Internetem. Jeśli azurefirewallsubnet uczy się domyślnej trasy do sieci lokalnej za pośrednictwem protokołu BGP, należy zastąpić to z 0.0.0.0/0 UDR z **NextHopType** wartość ustawiona jako **Internet** do utrzymania bezpośredniej łączności z Internetem. Domyślnie zapora platformy Azure nie obsługuje wymuszonego tunelowania do sieci lokalnej.<br><br>Jeśli jednak konfiguracja wymaga wymuszonego tunelowania do sieci lokalnej, firma Microsoft będzie obsługiwać ją indywidualnie dla każdego przypadku. Skontaktuj się z pomocą techniczną, abyśmy mogli przejrzeć Twoją sprawę. Jeśli zostanie zaakceptowana, zezwolimy na subskrypcję i zapewnimy, że wymagana łączność z Internetem zapory jest utrzymywana.|

<sup>1.</sup> Jeśli chcesz zwiększyć te limity, skontaktuj się z pomocą techniczną platformy Azure.
