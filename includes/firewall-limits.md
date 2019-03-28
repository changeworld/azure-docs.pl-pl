---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 3/25/2019
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 368a7cf1d372d79f062affe9b206d070afe5c2f7
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58505690"
---
| Zasób | Limit domyślny |
| --- | --- |
| Przepływność danych |30 GB/s<sup>1</sup> |
|Reguły|10 000 operacji, wszystkie reguły, typy w połączeniu.|
|Minimalny rozmiar AzureFirewallSubnet |/26|
|Zakres portów w regułach sieci i aplikacji|0-64,000. Praca jest w toku złagodzenie tego ograniczenia.|
|Tabela tras|Domyślnie AzureFirewallSubnet ma trasę 0.0.0.0/0, typ następnego przeskoku wartość **Internet**.<br><br>Włączenie tunelowania do sieci lokalnej za pośrednictwem usługi ExpressRoute i VPN Gateway, konieczne może być jawnie skonfigurować 0.0.0.0/0 trasy zdefiniowanej przez użytkownika (UDR) z zestawem wartości Typ następnego przeskoku jako Internet i skojarzyć go z Twojego AzureFirewallSubnet. Zastępuje to potencjalne bramy domyślnej anonsowania BGP do sieci lokalnej. Jeśli Twoja organizacja potrzebuje, wymuszonego tunelowania dla zapory usługi Azure w celu kierowania ruchu bramy domyślne wstecz za pośrednictwem sieci lokalnej, skontaktuj się z pomocą techniczną. Możemy umieścić na liście dozwolonych jest utrzymywany w subskrypcji, aby upewnić się, zapory wymagane połączenie z Internetem.|

<sup>1</sup>Jeśli trzeba zwiększyć te limity, skontaktuj się z działem pomocy technicznej systemu Azure.
