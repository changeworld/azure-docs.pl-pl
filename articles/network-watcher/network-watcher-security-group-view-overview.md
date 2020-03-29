---
title: Wprowadzenie do widoku grupy zabezpieczeń w usłudze Azure Network Watcher | Dokumenty firmy Microsoft
description: Ta strona zawiera omówienie funkcji widoku zabezpieczeń Obserwatora sieci
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: damendo
ms.openlocfilehash: 18619cc0f7a21a39d0802575c484c68c5fa1a624
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840761"
---
# <a name="introduction-to-effective-security-rules-view-in-azure-network-watcher"></a>Wprowadzenie do widoku reguł zabezpieczeń Efektywne w usłudze Azure Network Watcher

Grupy zabezpieczeń sieciowych są skojarzone na poziomie podsieci lub na poziomie karty sieciowej. Po skojarzeniu na poziomie podsieci ma zastosowanie do wszystkich wystąpień maszyn wirtualnych w podsieci. Widok reguł efektywnych zabezpieczeń zwraca wszystkie skonfigurowane sieciowe grupy zabezpieczeń i reguły skojarzone na poziomie karty sieciowej i podsieci dla maszyny wirtualnej zapewniającej wgląd w konfigurację. Ponadto reguły zabezpieczeń skuteczne są zwracane dla każdej karty sieciowej w maszynie wirtualnej. Korzystając z widoku Skuteczne reguły zabezpieczeń, można ocenić maszynę wirtualną pod kątem luk w zabezpieczeniach sieci, takich jak otwarte porty. Można również sprawdzić, czy sieć grupy zabezpieczeń działa zgodnie z oczekiwaniami na podstawie [porównania skonfigurowanych i zatwierdzonych reguł zabezpieczeń](network-watcher-nsg-auditing-powershell.md).

Bardziej rozszerzony przypadek użycia jest w zgodności zabezpieczeń i inspekcji. Można zdefiniować nakazowy zestaw reguł zabezpieczeń jako model zarządzania zabezpieczeniami w organizacji. Okresowy audyt zgodności można zaimplementować w sposób programowy, porównując reguły nakazowe ze skutecznymi regułami dla każdej z maszyn wirtualnych w sieci.

W regułach portalu są wyświetlane dla każdego interfejsu sieciowego i pogrupowane według ruchu przychodzącego i wychodzącego. Zapewnia to prosty widok do reguł stosowanych do maszyny wirtualnej. Przycisk pobierania jest dostarczany, aby łatwo pobrać wszystkie reguły zabezpieczeń bez względu na kartę do pliku CSV.

![widok grupy zabezpieczeń][1]

Można wybrać reguły i otworzy się nowy blok, aby wyświetlić grupę zabezpieczeń sieci oraz prefiksy źródłowe i docelowe. Z tego bloku można przejść bezpośrednio do zasobu sieciowej grupy zabezpieczeń.

![Drążenia][2]

### <a name="next-steps"></a>Następne kroki

Dowiedz się, jak przeprowadzić inspekcję ustawień sieciowej grupy zabezpieczeń, odwiedzając [pozycję Inspekcja ustawień sieciowej grupy zabezpieczeń w programie PowerShell](network-watcher-nsg-auditing-powershell.md)

[1]: ./media/network-watcher-security-group-view-overview/securitygroupview.png
[2]: ./media/network-watcher-security-group-view-overview/figure1.png









