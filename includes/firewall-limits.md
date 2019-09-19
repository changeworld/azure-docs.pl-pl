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
ms.openlocfilehash: a42284765a46f4a000dc5b7fcf2867ef17d69570
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2019
ms.locfileid: "68229293"
---
| Resource | Limit domyślny |
| --- | --- |
| Przepływność danych |30 GB/s<sup>1</sup> |
|Reguły|10 000 wszystkie typy reguł są łączone.|
|Minimalny rozmiar AzureFirewallSubnet |/26|
|Zakres portów w regułach sieci i aplikacji|0 – 64000. Trwają prace, aby osłabić to ograniczenie.|
|Publiczne adresy IP|100 maksimum|
|Tabela tras|Domyślnie AzureFirewallSubnet ma trasę 0.0.0.0/0 z wartością NextHopType ustawioną na wartość **Internet**.<br><br>Zapora platformy Azure musi mieć bezpośrednią łączność z Internetem. Jeśli AzureFirewallSubnet nauczy trasy domyślnej do sieci lokalnej za pośrednictwem protokołu BGP, należy przesłonić wartość przy użyciu wartości 0.0.0.0/0 UDR z wartością **NextHopType** ustawioną jako **Internet** , aby zachować bezpośrednią łączność z Internetem. Domyślnie Zapora platformy Azure nie obsługuje wymuszonego tunelowania do sieci lokalnej.<br><br>Jeśli jednak konfiguracja wymaga wymuszonego tunelowania do sieci lokalnej, firma Microsoft będzie obsługiwać ją w przypadku poszczególnych przypadków. Skontaktuj się z pomocą techniczną, aby umożliwić nam zapoznanie się z Twoim przypadkiem. Jeśli zostanie zaakceptowana, zezwolimy na subskrypcję i upewnimy się, że jest utrzymywana wymagana łączność internetowa zapory.|

<sup>1</sup> Jeśli musisz zwiększyć te limity, skontaktuj się z pomocą techniczną platformy Azure.
