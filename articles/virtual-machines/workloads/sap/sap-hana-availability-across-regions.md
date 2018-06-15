---
title: Dostępność SAP HANA w regionach platformy Azure | Dokumentacja firmy Microsoft
description: Omówienie zagadnienia dotyczące dostępności podczas uruchamiania na maszynach wirtualnych Azure w wielu regionach platformy Azure SAP HANA.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/26/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: edbd1885dd529e4ccd38f2012d56865a2147f64d
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
ms.locfileid: "30842275"
---
# <a name="sap-hana-availability-across-azure-regions"></a>Dostępność SAP HANA w regionach platformy Azure

W tym artykule opisano scenariusze związanych z dostępnością SAP HANA w różnych regionach platformy Azure. Ze względu na odległość między regiony platformy Azure Konfigurowanie dostępności SAP HANA w wielu regionach platformy Azure wymaga uwagi.

## <a name="why-deploy-across-multiple-azure-regions"></a>Dlaczego warto wdrożyć w wielu regionach platformy Azure

Regiony platformy Azure często są oddzielone duże odległości. W zależności od regionu geograficznymi odległość między regiony platformy Azure może być setki milach lub nawet kilka mil tysięcy, podobnie jak w Stanach Zjednoczonych. Ze względu na odległość ruch sieciowy między zasoby, które zostały wdrożone w dwóch różnych regionach platformy Azure występować znaczne opóźnienie komunikacji dwukierunkowej. Opóźnienie jest istotne wykluczyć synchroniczna wymiana danych między dwoma wystąpieniami SAP HANA w typowych obciążeń SAP. 

Z drugiej strony organizacje często mają wymagania odległości między lokalizacją podstawowego centrum danych i dodatkowego centrum danych. Wymaganie odległość pomaga zapewnić dostępność, jeśli występuje klęski żywiołowej w szerszej lokalizacji geograficznej. Przykładami huragany, które trafień Karaibów i Florida września i października 2017 r. Twoja organizacja może mieć co najmniej minimalną odległość wymaganiem. W przypadku klientów najbardziej platformy Azure, definicji minimalną odległość wymaga można projektować pod kątem dostępności w [regiony platformy Azure](https://azure.microsoft.com/regions/). Odległość między dwoma regiony platformy Azure jest za duży do używania trybu Replikacja synchroniczna HANA, wymagania dotyczące RTO i cel punktu odzyskiwania może wymusić na wdrażanie konfiguracji dostępności w jeden region, a następnie dodatkowo z dodatkowe wdrożenia na sekundę region.

Innym aspektem do uwzględnienia w tym scenariuszu jest trybu failover i przekierowanie klienta. Zakłada się, że tryb failover między wystąpieniami SAP HANA w dwóch różnych regionach platformy Azure zawsze jest ręcznego przełączania trybu failover. Ponieważ tryb replikacji replikacji systemu SAP HANA ustawiono asynchroniczne, istnieje możliwość, że dane w głównej wystąpienia HANA nie jeszcze stają się on do dodatkowej wystąpienia HANA. W związku z tym automatycznej pracy awaryjnej nie jest opcją w przypadku konfiguracji replikacji w przypadku asynchronicznego. Nawet w przypadku ręcznie trybu failover, tak jak wykonywania pracy awaryjnej należy podjąć środki, aby zapewnić, że wszystkie dane przekazane na głównej stronie go do dodatkowej wystąpienia przed należy ręcznie przenieść do innego regionu systemu Azure.
 
Sieć wirtualna platformy Azure korzysta z różnych zakresów adresów IP. Adresy IP są wdrażane w drugim region platformy Azure. Tak, albo musisz zmienić konfigurację klienta SAP HANA lub najlepiej, trzeba utworzyć kroki, aby zmienić rozpoznawania nazw. Dzięki temu klienci są przekierowywani do nowej lokacji dodatkowej adresu IP. Aby uzyskać więcej informacji, zobacz artykuł SAP [odzyskiwania połączenia klienta po przejęciu](https://help.sap.com/doc/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c93a723ceedc45da9a66ff47672513d3.html).   

## <a name="simple-availability-between-two-azure-regions"></a>Proste dostępności między dwóch regionach platformy Azure

Możesz wybrać nie można wprowadzić żadnych konfiguracji dostępności w jednym regionie, ale nadal mają żądanie ma obciążeń obsługiwanych w przypadku awarii. Typowe przypadki dla systemów, takich jak to to systemy nieprodukcyjnym. Mimo że o systemie w dół do połowy lub nawet dziennie stałego, nie może dopuścić system będzie dostępny do 48 godzin lub dłużej. Aby wprowadzić ustawienia mniej kosztowne, systemem innego nawet mniej ważne w maszynie Wirtualnej. Inny system działa jako miejsca docelowego. Można również rozmiar maszyny Wirtualnej w regionie pomocniczym, może być mniejszy i nie chce wstępnego ładowania danych. Ponieważ przejście w tryb failover jest ręczne i pociąga za sobą wielu więcej czynności stosu kompletna aplikacja trybu failover, dopuszczalne jest dodatkowy czas zamykania maszyny Wirtualnej, rozmiar, a następnie ponownie uruchomić maszyny Wirtualnej.

> [!NOTE]
> Nawet jeśli nie używasz wstępnego ładowania danych w celu replikacji HANA systemu należy co najmniej 64 GB pamięci. Należy również wystarczającej ilości pamięci, oprócz 64 GB do przechowywania danych magazynu wierszy w pamięci obiektu docelowego.

![Diagram dwóch maszyn wirtualnych za pośrednictwem dwóch regionach](./media/sap-hana-availability-two-region/two_vm_HSR_async_2regions_nopreload.PNG)

> [!NOTE]
> W tej konfiguracji nie może dostarczyć RPO = 0, ponieważ tryb replikacji HANA systemu jest asynchroniczne. Jeśli musisz podać RPO = 0, ta konfiguracja nie jest konfiguracji wybór.

Niewielkie zmiany wprowadzone w konfiguracji można skonfigurować jako wstępnego ładowania danych. Jednak charakter ręcznej pracy awaryjnej i fakt, że warstwy aplikacji również trzeba przenosić do drugiego regionu, może nie być uzasadnione wstępnie danych. 

## <a name="combine-availability-within-one-region-and-across-regions"></a>Łączenie dostępności w ramach jednego regionu i w regionach 

Te czynniki mogą być wynikiem kombinację dostępności w obrębie regionów oraz:

- Wymaganie RPO = 0 w obrębie regionu platformy Azure.
- Organizacja nie jest chce lub mogą mieć globalne operacje wpływ głównych katastrofy fizycznych wpływającym na większych regionu. To w przypadku niektórych huragany, które trafień Karaibów w ciągu kilku ostatnich lat.
- Przepisy dotyczące żądanie odległości między lokacjami podstawowych i pomocniczych, które są poza jakiego dostępności Azure zapewniają strefy.

W takim przypadku można skonfigurować jakie wywołania SAP [konfiguracji replikacji wielowarstwowa systemu SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/ca6f4c62c45b4c85a109c7faf62881fc.html) za pomocą replikacji systemu HANA. Architektura będzie wyglądać następująco:

![Diagram trzech maszyn wirtualnych za pośrednictwem dwóch regionach](./media/sap-hana-availability-two-region/three_vm_HSR_async_2regions_ha_and_dr.PNG)

Ta konfiguracja zapewnia RPO = 0, z niskim RTO, w obrębie regionu podstawowego. Konfiguracja także zadowalający RPO przypadku przenoszenia do drugiego regionu. Czasy RTO w regionie drugi są zależne od tego, czy dane są wstępnie ładowane. Aby uruchomić system testowy wielu klientów użyj maszyny Wirtualnej w regionie pomocniczym. W tym przypadku użycia, danych nie może być załadowane.

> [!NOTE]
> Ponieważ używasz **logreplay** trybu operacji dla replikacji systemu HANA przejście od 1 do warstwy 2 (Replikacja synchroniczna w regionie podstawowym), warstwa replikacji między warstwy 2 i warstwy 3 (replikacji do lokacji dodatkowej) nie może być w **delta_datashipping** tryb działania. Szczegółowe informacje na temat trybów pracy i pewne ograniczenia, zobacz artykuł SAP [tryby działania dla replikacji systemu SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html). 

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać szczegółowe instrukcje dotyczące konfigurowania tych konfiguracji na platformie Azure zobacz:

- [Konfigurowanie replikacji systemu SAP HANA w maszynach wirtualnych platformy Azure](sap-hana-high-availability.md)
- [Wysoka dostępność dla programu SAP HANA za pomocą replikacji systemu](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

 



 
  
