---
title: Wprowadzenie do widoku grupy zabezpieczeń w obserwatora sieciowego Azure | Dokumentacja firmy Microsoft
description: Ta strona zawiera przegląd możliwości widoku zabezpieczeń obserwatora sieciowego
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
ms.openlocfilehash: f4175875b68c52e68588b8d0debd003ab73427ec
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23864210"
---
# <a name="introduction-to-network-security-group-view-in-azure-network-watcher"></a>Wprowadzenie do widoku grupy zabezpieczeń sieci w obserwatora sieciowego Azure

Na poziomie podsieci lub na poziomie karty Sieciowej skojarzonych grup zabezpieczeń sieci. Gdy skojarzony na poziomie podsieci, dotyczy wszystkich wystąpień maszyn wirtualnych w podsieci. Widok grupy zabezpieczeń sieci zwraca wszystkich skonfigurowanych grup NSG i reguł, które są skojarzone na poziomie podsieci i karty na maszynie wirtualnej, zapewniając wgląd w konfiguracji. Ponadto zasady efektywnym elementem systemu zabezpieczeń są zwracane dla każdej z kart sieciowych w maszynie Wirtualnej. Widok przy użyciu grup zabezpieczeń sieci można ocenić maszyny Wirtualnej dla luk w zabezpieczeniach sieci takich jak otwartych portów. Można również sprawdzić czy grupy zabezpieczeń sieci działa zgodnie z oczekiwaniami, na podstawie [porównanie skonfigurowanego i reguły zabezpieczeń skuteczne](network-watcher-nsg-auditing-powershell.md).

Przypadek użycia dłuższy jest zabezpieczeń, zgodności i inspekcji. Przetestowanego zestaw reguł zabezpieczeń można zdefiniować jako model ładu zabezpieczeń w organizacji. Inspekcja okresowe zgodności można zaimplementować w sposób programowy porównując przetestowanego reguł za pomocą skuteczne reguł dla poszczególnych maszyn wirtualnych w sieci.

W portalu, do którego zasady są podzielone według obowiązującej, podsieci interfejsu sieciowego i domyślne. Zapewnia to prosty widok do zasady zastosowane do maszyny wirtualnej. Można łatwo pobrać wszystkie reguły zabezpieczeń bez względu na karcie do pliku CSV znajduje się przycisk Pobierz.

![Widok grupy zabezpieczeń][1]

Zasady można wybrać i otwiera nowy blok do wyświetlenia grupy zabezpieczeń sieci i prefiksy źródłowym i docelowym. Z tego bloku można przejść bezpośrednio do tego zasobu grupy zabezpieczeń sieci.

![Przechodzenie do szczegółów][2]

### <a name="next-steps"></a>Następne kroki

Dowiedz się, jak inspekcji ustawienia sieciowej grupy zabezpieczeń, odwiedzając [ustawienia inspekcji, grupy zabezpieczeń sieci przy użyciu programu PowerShell](network-watcher-nsg-auditing-powershell.md)

[1]: ./media/network-watcher-security-group-view-overview/securitygroupview.png
[2]: ./media/network-watcher-security-group-view-overview/figure1.png









