---
title: Dostępność platformy SAP HANA w regionach platformy Azure | Dokumentacja firmy Microsoft
description: Ogólne omówienie zagadnień dostępności podczas uruchamiania oprogramowania SAP HANA na maszynach wirtualnych platformy Azure w wielu regionach platformy Azure.
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
ms.date: 09/12/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 95ada2cb146bdbc972afee883a1d174c95aa67d7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60650304"
---
# <a name="sap-hana-availability-across-azure-regions"></a>Dostępność platformy SAP HANA w regionach platformy Azure

W tym artykule opisano scenariuszy związanych z dostępnością platformy SAP HANA w różnych regionach platformy Azure. Ze względu na odległość między regionami platformy Azure Konfigurowanie dostępność oprogramowania SAP HANA w wielu regionach platformy Azure obejmuje uwagi.

## <a name="why-deploy-across-multiple-azure-regions"></a>Dlaczego warto wdrożyć w wielu regionach platformy Azure

Regiony platformy Azure często są oddzielone duże odległości. W zależności od regionu geopolitycznego odległości między regionami platformy Azure może być setek mil lub nawet kilku mil tysięcy, podobnie jak w Stanach Zjednoczonych. Z powodu odległość ruch sieciowy między zasobami, które zostały wdrożone w dwóch różnych regionach platformy Azure wystąpić znaczne opóźnienie w obie strony. Opóźnienie jest istotne wykluczyć synchroniczna wymiana danych między dwoma wystąpieniami platformy SAP HANA w ramach typowych obciążeń SAP. 

Z drugiej strony organizacje często mają wymagania odległości między lokalizacją podstawowym centrum danych i dodatkowego centrum danych. Wymaganie odległość pomaga zapewnić dostępność w przypadku klęski żywiołowej w szerszej lokalizacji geograficznej. Przykłady obejmują hurricanes, które trafień Karaiby i Floryda w września do października 2017 r. Twoja organizacja może mieć co najmniej minimalną odległość wymogiem. Dla klientów większość platformy Azure, definicja minimalną odległość wymaga się projektowania pod kątem dostępności w [regionów świadczenia usługi Azure](https://azure.microsoft.com/regions/). Odległość między dwoma regionami platformy Azure jest za duży, aby użyć trybu replikacji synchronicznej HANA, wymagania RTO i RPO może wymusić wdrażanie konfiguracji dostępności w jednym regionie, a następnie uzupełniają dodatkowe w przypadku wdrożeń na sekundę region.

Innym aspektem do uwzględnienia w tym scenariuszu jest trybu failover i przekierowania klienta. Zakłada się, że pracę awaryjną między wystąpieniami platformy SAP HANA w dwóch różnych regionach platformy Azure zawsze jest ręcznej pracy awaryjnej. Ponieważ replikacja systemu SAP HANA tryb replikacji jest równa asynchronicznego, istnieje ryzyko, że danych w podstawowej wystąpienie oprogramowania HANA nie zostało jeszcze dotarło do dodatkowej wystąpienie oprogramowania HANA. W związku z tym automatycznej pracy awaryjnej nie jest dostępną opcją w przypadku konfiguracji replikacji w przypadku asynchronicznej. Nawet w przypadku ręcznie kontrolowanego przejścia w tryb failover jak w poniższym ćwiczeniu trybu failover należy podjąć środki, aby zapewnić, że wszystkie zatwierdzone dane po stronie podstawowej go do dodatkowej wystąpienia przed można ręcznie przenieść do innego regionu platformy Azure.
 
Usługa Azure Virtual Network używa różnych zakresów adresów IP. Adresy IP są wdrażane w drugim regionie platformy Azure. Tak, albo musisz zmienić konfigurację klienta SAP HANA, lub najlepiej, trzeba utworzyć kroki, aby zmienić rozpoznawania nazw. Dzięki temu klienci są przekierowywani do nowej lokacji dodatkowej adresu IP. Aby uzyskać więcej informacji, zobacz artykuł SAP [odzyskiwania połączenia klienta po przejęciu](https://help.sap.com/doc/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c93a723ceedc45da9a66ff47672513d3.html).   

## <a name="simple-availability-between-two-azure-regions"></a>Proste dostępności między dwoma regionami platformy Azure

Może nie chcesz umieścić konfiguracji wszelkie dostępności w miejscu w obrębie jednego regionu, ale nadal mają żądanie ma obciążenia obsługiwane w przypadku wystąpienia awarii. Typowe przypadki, w przypadku takich scenariuszy to systemy obniżenia. Mimo że system w dół do połowy lub nawet jeden dzień to zrównoważone, nie może dopuścić system będzie dostępny przez 48 godzin lub dłużej. Aby ułatwić instalację mniej kosztowne, należy uruchomić inny system, który jest nawet mniej ważne w maszynie Wirtualnej. Inny system działa jako miejsce docelowe. Można również rozmiaru maszyny Wirtualnej w regionie pomocniczym, może być mniejszy i nie chcesz wstępnego ładowania danych. Dodatkowy czas zamykania maszyny Wirtualnej, zmień jego rozmiar i ponownie uruchom maszynę Wirtualną jest dopuszczalne, ponieważ tryb failover jest ręcznie i pociąga za sobą wiele więcej kroków do trybu failover stosu kompletnej aplikacji.

Jeśli używasz scenariusza udostępniania docelowym odzyskiwania po awarii w systemie odpowiedzi na pytania w jednej maszyny Wirtualnej, należy wykonać te zagadnienia pod uwagę:

- Istnieją dwa [tryby działania](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html) delta_datashipping i logreplay, które są dostępne dla takich scenariuszy
- Oba tryby działania mają wymagania dotyczące pamięci różnych bez wstępnego ładowania danych
- Delta_datashipping może wymagać znacznie mniej pamięci bez opcji wstępnego ładowania, niż może wymagać logreplay. Zobacz rozdział 4.3 dokumentu SAP [jak do wykonania replikacji systemu dla oprogramowania SAP HANA](https://archive.sap.com/kmuuid2/9049e009-b717-3110-ccbd-e14c277d84a3/How%20to%20Perform%20System%20Replication%20for%20SAP%20HANA.pdf)
- Wymagania pamięci logreplay tryb działania bez preload nie jest jednoznaczny i zależy od załadować struktury magazynu kolumn. W skrajnych przypadkach mogą wymagać 50% pamięci głównej wystąpienia. Pamięć logreplay tryb działania jest niezależne na tego, czy została wybrana opcja danych wstępnie skonfigurowana.


![Diagram przedstawiający dwóch maszyn wirtualnych za pośrednictwem dwóch regionach](./media/sap-hana-availability-two-region/two_vm_HSR_async_2regions_nopreload.PNG)

> [!NOTE]
> W tej konfiguracji nie może dostarczyć cel punktu odzyskiwania = 0, ponieważ Twoje tryb replikacji systemu HANA jest asynchroniczna. Jeśli konieczne będzie podanie cel punktu odzyskiwania = 0, ta konfiguracja nie jest od wybranej konfiguracji.

Aby skonfigurować dane jako wstępne ładowanie musi być niewielkie zmiany wprowadzone w konfiguracji. Jednak ze względu na charakter ręcznej pracy awaryjnej i fakt, że warstwy aplikacji również konieczne przeniesienie do drugiego regionu, może nie mieć sensu wstępnego ładowania danych. 

## <a name="combine-availability-within-one-region-and-across-regions"></a>Połącz dostępności w ramach jednego regionu i regionami 

Kombinacja dostępność oferowaną w wielu regionach mogą opierać się przez te czynniki:

- Wymagania celu punktu odzyskiwania = 0 w regionie platformy Azure.
- Organizacja nie zechce lub mogą mieć globalne operacje wpływ głównych klęskami fizycznych, który wpływa na większych region. Jest to w przypadku niektórych hurricanes, które trafień Karaiby w ciągu ostatnich kilku lat.
- Przepisy żądanie odległości od lokacji głównych i dodatkowych, które są wyraźnie, poza jakie dostępności platformy Azure może zapewnić strefy.

W takich przypadkach można ustawić jakie wywołania SAP [konfiguracji replikacji wielowarstwowa systemu SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/ca6f4c62c45b4c85a109c7faf62881fc.html) za pomocą replikacji systemu HANA. Architektura będzie wyglądać:

![Diagram przedstawiający trzy maszyny wirtualne za pośrednictwem dwóch regionach](./media/sap-hana-availability-two-region/three_vm_HSR_async_2regions_ha_and_dr.PNG)

SAP wprowadzone [replikacji systemu docelowego wielu](https://help.sap.com/viewer/42668af650f84f9384a3337bcd373692/2.0.03/en-US/0b2c70836865414a8c65463180d18fec.html) z SPS3 2.0 platformy HANA. Replikacji systemu docelowego wielu oferuje kilka zalet w scenariuszach aktualizacji. Na przykład lokacji odzyskiwania po awarii (Region 2) nie ulega zmianie podczas dodatkowej lokacji o wysokiej dostępności nie działa z powodu konserwacji lub aktualizacji. Możesz dowiedzieć się więcej na temat replikacji systemu docelowego wielu HANA [tutaj](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.03/en-US/ba457510958241889a459e606bbcf3d3.html).
Przykładową architekturę z replikacją wielu docelowy będzie wyglądać:

![Diagram przedstawiający trzy maszyny wirtualne za pośrednictwem milti dwa regiony docelowe](./media/sap-hana-availability-two-region/saphanaavailability_hana_system_2region_HA_and_DR_multitarget_3VMs.PNG)

Jeśli organizacja korzysta z wymagania dotyczące wysokiej dostępności gotowości w second(DR) region platformy Azure, takich jak powinien wyglądać architektura:

![Diagram przedstawiający trzy maszyny wirtualne za pośrednictwem milti dwa regiony docelowe](./media/sap-hana-availability-two-region/saphanaavailability_hana_system_2region_HA_and_DR_multitarget_4VMs.PNG)


Przy użyciu logreplay jako tryb działania, ta konfiguracja zapewnia cel punktu odzyskiwania = 0, z niską wartość, w regionie podstawowym. Konfiguracja także znośnego cel punktu odzyskiwania, jeśli chodzi o przeniesienie do drugiego regionu. Czasy RTO, w drugim regionie są zależne od tego, czy dane są wstępnie ładowane. Wielu klientów używa maszyny Wirtualnej w regionie pomocniczym, aby uruchomić system testowy. W tym przypadku użycia, nie może zostać wstępnie załadowane dane.

> [!IMPORTANT]
> Tryby działania między różne warstwy trzeba jednolite. Możesz **nie** Użyj logreply jako tryb operacji między warstwami 1 i 2 oraz delta_datashipping umożliwiają określanie wartości warstwy 3. Można wybrać tylko jeden lub inny tryb operacji, która musi być spójny dla wszystkich warstw. Ponieważ delta_datashipping nie nadaje się umożliwiają cel punktu odzyskiwania = 0, tryb tylko uzasadnione działania, dla konfiguracji wielowarstwowych pozostaje logreplay. Aby uzyskać szczegółowe informacje o trybach operacji i pewne ograniczenia, zobacz artykuł SAP [tryby operacji replikacji systemu SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html). 

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać wskazówki krok po kroku dotyczące konfigurowania tych konfiguracji na platformie Azure zobacz:

- [Konfigurowanie replikacji systemu SAP HANA na maszynach wirtualnych Azure](sap-hana-high-availability.md)
- [Wysoka dostępność dla oprogramowania SAP HANA przy użyciu replikacji systemu](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

 



 
  
