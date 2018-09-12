---
title: Wprowadzenie do widoku grupy zabezpieczeń w usłudze Azure Network Watcher | Dokumentacja firmy Microsoft
description: Ta strona zawiera omówienie funkcji widoku zabezpieczeń usługi Network Watcher
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: ad27ab85-9d84-4759-b2b9-e861ef8ea8d8
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: jdial
ms.openlocfilehash: 15f6bd0d7da63924e52db8ec7e2cbb0ee7483f82
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2018
ms.locfileid: "44391542"
---
# <a name="introduction-to-network-security-group-view-in-azure-network-watcher"></a>Wprowadzenie do widok sieciowych grup zabezpieczeń w usłudze Azure Network Watcher

Sieciowe grupy zabezpieczeń są skojarzone na poziomie podsieci lub na poziomie karty Sieciowej. Gdy skojarzone na poziomie podsieci, ma zastosowanie do wszystkich wystąpień maszyn wirtualnych w podsieci. Widok grupy zabezpieczeń sieci zwraca wszystkie skonfigurowane sieciowe grupy zabezpieczeń i reguły skojarzone na poziomie karty Sieciowej i podsieci dla maszyny wirtualnej, zapewniając wgląd w konfiguracji. Ponadto zwracane są obowiązujących reguł zabezpieczeń dla każdej z kart sieciowych na maszynie wirtualnej. Widok przy użyciu sieciowej grupy zabezpieczeń można ocenić maszyny Wirtualnej dla luk w zabezpieczeniach sieci, takich jak otwieranie portów. Możesz również walidować Jeśli sieciowej grupy zabezpieczeń działają zgodnie z oczekiwaniami, na podstawie [porównanie skonfigurowanych i reguły zabezpieczeń zatwierdzonych](network-watcher-nsg-auditing-powershell.md).

Dłuższy przypadek użycia jest zgodności zabezpieczeń i inspekcji. Narzucony zestaw reguł zabezpieczeń można zdefiniować jako model zabezpieczeń nadzoru w Twojej organizacji. Inspekcja zgodności okresowe można zaimplementować w sposób programowy, porównując normatywne reguły z aktywne reguły dla poszczególnych maszyn wirtualnych w sieci.

W portalu, do którego reguły są podzielone według obowiązywać, podsieci, interfejs sieciowy i domyślne. Zapewnia to prosty wgląd w zasady zastosowane do maszyny wirtualnej. Łatwe pobieranie wszystkich reguł zabezpieczeń, niezależnie od tego, w karcie ich do pliku CSV znajduje się przycisk Pobierz.

![Widok grup zabezpieczeń][1]

Można wybrać zasady, a nowy blok zostanie otwarty do wyświetlenia grupy zabezpieczeń sieci i prefiksy źródłowym i docelowym. Z tego bloku można przejść bezpośrednio do zasobu sieciowej grupy zabezpieczeń.

![Przechodzenie do szczegółów][2]

### <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak i Przeprowadź inspekcję ustawienia sieciowej grupy zabezpieczeń, odwiedzając [ustawienia inspekcji sieciową grupę zabezpieczeń przy użyciu programu PowerShell](network-watcher-nsg-auditing-powershell.md)

[1]: ./media/network-watcher-security-group-view-overview/securitygroupview.png
[2]: ./media/network-watcher-security-group-view-overview/figure1.png









