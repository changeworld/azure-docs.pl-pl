---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 5/3/2019
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 8709d4d903bd31ff94d04ec61e226857e4190407
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66238416"
---
| Resource | Limit domyślny |
| --- | --- |
| Przepływność danych |30 GB/s<sup>1</sup> |
|Reguły|10 000 operacji, wszystkie reguły, typy w połączeniu.|
|Minimalny rozmiar AzureFirewallSubnet |/26|
|Zakres portów w regułach sieci i aplikacji|0-64,000. Praca jest w toku złagodzenie tego ograniczenia.|
|Tabela tras|Domyślnie AzureFirewallSubnet ma trasę 0.0.0.0/0, typ następnego przeskoku wartość **Internet**.<br><br>Zaporę platformy Azure musi mieć bezpośrednie połączenie z Internetem. Jeśli Twoje AzureFirewallSubnet uczy się trasę domyślną elementom sieci lokalnej za pośrednictwem protokołu BGP, konieczne jest przesłonięcie to za pomocą 0.0.0.0/0 trasy zdefiniowanej przez użytkownika za pomocą **Typ następnego przeskoku** wartość ustawiona jako **Internet** utrzymanie bezpośredniej Łączność z Internetem. Domyślnie Zapora usługi Azure nie obsługuje wymuszonego tunelowania do sieci lokalnej.<br><br>Jednak jeśli konfiguracja wymaga wymuszonego tunelowania do sieci lokalnej, Microsoft będzie obsługiwać go na podstawie przypadku. Się z pomocą techniczną, dzięki czemu możesz przejrzeć tej sprawy. Jeśli zaakceptowane, utworzymy dozwolonych subskrypcji i upewnij się, że połączenie z Internetem wymagany zapory są obsługiwane.|

<sup>1</sup>Jeśli trzeba zwiększyć te limity, skontaktuj się z działem pomocy technicznej systemu Azure.
