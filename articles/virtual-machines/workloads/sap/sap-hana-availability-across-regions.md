---
title: Dostępność sap HANA w różnych regionach platformy Azure | Dokumenty firmy Microsoft
description: Omówienie zagadnień dostępności podczas uruchamiania usługi SAP HANA na maszynach wirtualnych platformy Azure w wielu regionach platformy Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/12/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 68a393865038722f2fd7fa5e42334f8d5e760951
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70078848"
---
# <a name="sap-hana-availability-across-azure-regions"></a>Dostępność sap hana w regionach platformy Azure

W tym artykule opisano scenariusze związane z dostępnością sap HANA w różnych regionach platformy Azure. Ze względu na odległość między regionami platformy Azure konfigurowanie dostępności SAP HANA w wielu regionach platformy Azure wymaga specjalnych zagadnień.

## <a name="why-deploy-across-multiple-azure-regions"></a>Dlaczego warto wdrażać w wielu regionach platformy Azure

Regiony platformy Azure często są oddzielone dużymi odległościami. W zależności od regionu geopolitycznego odległość między regionami platformy Azure może wynosić setki mil, a nawet kilka tysięcy mil, jak w Stanach Zjednoczonych. Ze względu na odległość ruch sieciowy między zasobami, które są wdrażane w dwóch różnych regionach platformy Azure wystąpić znaczne opóźnienie sieci w obie strony. Opóźnienie jest wystarczająco istotne, aby wykluczyć synchroniczową wymianę danych między dwoma wystąpieniami SAP HANA w typowych obciążeniach SAP. 

Z drugiej strony organizacje często mają wymagania dotyczące odległości między lokalizacją podstawowego centrum danych a pomocniczym centrum danych. Wymóg odległości pomaga zapewnić dostępność w przypadku wystąpienia klęski żywiołowej w szerszym położeniu geograficznym. Przykłady obejmują huragany, które nawiedził Karaiby i Florydę we wrześniu i październiku 2017. Organizacja może mieć co najmniej minimalne wymagania dotyczące odległości. W przypadku większości klientów platformy Azure minimalna definicja odległości wymaga projektowania w celu zapewnienia dostępności w [regionach platformy Azure.](https://azure.microsoft.com/regions/) Ponieważ odległość między dwoma regionami platformy Azure jest zbyt duża, aby można było użyć trybu synchroniczowej replikacji HANA, wymagania dotyczące obiektów RTO i celu obiektów obiektów celem pomiara mogą wymusić wdrożenie konfiguracji dostępności w jednym regionie, a następnie uzupełnić je o dodatkowe wdrożenia w drugim Region.

Innym aspektem do rozważenia w tym scenariuszu jest praca awaryjna i przekierowanie klienta. Założenie jest, że praca awaryjna między wystąpieniami SAP HANA w dwóch różnych regionach platformy Azure zawsze jest ręcznezamykanie awaryjne. Ponieważ tryb replikacji systemu SAP HANA jest ustawiony na asynchroniczne, istnieje potencjał, że dane zatwierdzone w podstawowym wystąpieniu HANA nie zostały jeszcze wprowadzone do pomocniczego wystąpienia HANA. W związku z tym automatyczna praca awaryjna nie jest opcją dla konfiguracji, w których replikacja jest asynchronicznie. Nawet w przypadku ręcznie kontrolowanych pracy awaryjnej, jak w przypadku pracy awaryjnej, należy podjąć środki, aby upewnić się, że wszystkie zatwierdzone dane po stronie podstawowej zostały przeniesione do wystąpienia pomocniczego przed ręcznym przejściem do innego regionu platformy Azure.
 
Usługa Azure Virtual Network używa innego zakresu adresów IP. Adresy IP są wdrażane w drugim regionie platformy Azure. Dlatego należy albo zmienić konfigurację klienta SAP HANA, lub najlepiej, należy utworzyć kroki, aby zmienić rozpoznawanie nazw. W ten sposób klienci są przekierowywani do adresu IP serwera nowej lokacji dodatkowej. Aby uzyskać więcej informacji, zobacz artykuł SAP [Odzyskiwanie połączenia klienta po przejęciu](https://help.sap.com/doc/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c93a723ceedc45da9a66ff47672513d3.html).   

## <a name="simple-availability-between-two-azure-regions"></a>Prosta dostępność między dwoma regionami platformy Azure

Możesz nie umieszczać żadnej konfiguracji dostępności w miejscu w jednym regionie, ale nadal istnieje zapotrzebowanie na obciążenie, jeśli wystąpi awaria. Typowe przypadki dla takich scenariuszy są systemy nieprodukcyjne. Chociaż posiadanie systemu przez pół dnia lub nawet dzień jest trwałe, nie można pozwolić, aby system był niedostępny przez 48 godzin lub dłużej. Aby konfiguracja była mniej kosztowna, uruchom inny system, który jest jeszcze mniej ważny na maszynie wirtualnej. Inny system działa jako miejsce docelowe. Można również rozmiar maszyny Wirtualnej w regionie pomocniczym, aby być mniejsze i nie należy ładować wstępnie danych. Ponieważ praca awaryjna jest ręczna i pociąga za sobą wiele więcej kroków do pracy awaryjnej w stosie całej aplikacji, dodatkowy czas, aby zamknąć maszynę wirtualną, zmienić jej rozmiar, a następnie ponownie uruchomić maszynę wirtualną jest dopuszczalne.

Jeśli używasz scenariusza udostępniania obiektu docelowego odzyskiwania po awarii z systemem kontroli jakości w jednej maszynie wirtualnej, musisz wziąć pod uwagę następujące kwestie:

- Istnieją dwa [tryby pracy](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html) z delta_datashipping i logreplay, które są dostępne dla takiego scenariusza
- Oba tryby pracy mają różne wymagania dotyczące pamięci bez wstępnego ładowania danych
- Delta_datashipping może wymagać znacznie mniej pamięci bez opcji wstępnego ładowania niż logreplay może wymagać. Patrz rozdział 4.3 dokumentu SAP [Jak wykonać replikację systemu dla SAP HANA](https://archive.sap.com/kmuuid2/9049e009-b717-3110-ccbd-e14c277d84a3/How%20to%20Perform%20System%20Replication%20for%20SAP%20HANA.pdf)
- Wymaganie pamięci w trybie pracy logreplay bez wstępnego ładowania nie jest deterministyczne i zależy od struktur magazynu kolumn załadowanych. W skrajnych przypadkach może wymagać 50% pamięci wystąpienia podstawowego. Pamięć dla trybu pracy logreplay jest niezależna od tego, czy wybrano wstępnie załadowane dane, czy nie.


![Diagram dwóch maszyn wirtualnych w dwóch regionach](./media/sap-hana-availability-two-region/two_vm_HSR_async_2regions_nopreload.PNG)

> [!NOTE]
> W tej konfiguracji nie można podać RPO = 0, ponieważ tryb replikacji systemu HANA jest asynchroniczne. Jeśli musisz podać RPO =0, ta konfiguracja nie jest konfiguracją wyboru.

Małą zmianą, którą można wprowadzić w konfiguracji może być skonfigurowanie danych jako wstępnego ładowania. Jednak biorąc pod uwagę ręczny charakter pracy awaryjnej i fakt, że warstwy aplikacji również muszą zostać przeniesione do drugiego regionu, może nie mieć sensu wstępne ładowanie danych. 

## <a name="combine-availability-within-one-region-and-across-regions"></a>Łączenie dostępności w jednym regionie i w różnych regionach 

Połączenie dostępności w obrębie regionów i między regionami może być spowodowane przez następujące czynniki:

- Wymaganie RPO=0 w regionie platformy Azure.
- Organizacja nie chce ani nie jest w stanie mieć globalnych operacji dotkniętych poważną katastrofą naturalną, która dotyka większy region. Tak było w przypadku niektórych huraganów, które nawiedziły Karaiby w ciągu ostatnich kilku lat.
- Przepisy, które wymagają odległości między lokacjami podstawowymi i dodatkowymi, które wyraźnie przekraczają strefy dostępności platformy Azure.

W takich przypadkach można skonfigurować, co SAP wywołuje [konfigurację replikacji systemu SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/ca6f4c62c45b4c85a109c7faf62881fc.html) przy użyciu replikacji systemu HANA. Architektura będzie wyglądać następująco:

![Diagram trzech maszyn wirtualnych w dwóch regionach](./media/sap-hana-availability-two-region/three_vm_HSR_async_2regions_ha_and_dr.PNG)

Firma SAP wprowadziła [replikację systemu wielocelowego](https://help.sap.com/viewer/42668af650f84f9384a3337bcd373692/2.0.03/en-US/0b2c70836865414a8c65463180d18fec.html) z SYSTEMEM HANA 2.0 SPS3. Replikacja systemu wielocelowa przynosi pewne korzyści w scenariuszach aktualizacji. Na przykład lokacji odzyskiwania po awarii (region 2) nie ma wpływu, gdy lokacja pomocniczej dostępności jest wyłączona z powodu konserwacji lub aktualizacji. Możesz dowiedzieć się więcej o replikacji systemu wielocelowego HANA [tutaj](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.03/en-US/ba457510958241889a459e606bbcf3d3.html).
Możliwa architektura z replikacją wielocelową wyglądałaby następująco:

![Diagram trzech maszyn wirtualnych w dwóch regionach milti-target](./media/sap-hana-availability-two-region/saphanaavailability_hana_system_2region_HA_and_DR_multitarget_3VMs.PNG)

Jeśli organizacja ma wymagania dotyczące gotowości na wysoką dostępność w drugim regionie platformy Azure(DR), architektura będzie wyglądać następująco:

![Diagram trzech maszyn wirtualnych w dwóch regionach milti-target](./media/sap-hana-availability-two-region/saphanaavailability_hana_system_2region_HA_and_DR_multitarget_4VMs.PNG)


Za pomocą logreplay jako tryb pracy, ta konfiguracja zapewnia RPO = 0, z niskim RTO, w regionie podstawowym. Konfiguracja zapewnia również przyzwoity rpo, jeśli przejście do drugiego regionu jest zaangażowany. Czasy RTO w drugim regionie zależą od tego, czy dane są wstępnie załadowane. Wielu klientów używa maszyny Wirtualnej w regionie pomocniczym do uruchamiania systemu testowego. W takim przypadku nie można wstępnie załadować danych.

> [!IMPORTANT]
> Tryby pracy między różnymi warstwami muszą być jednorodne. **Nie można** używać logreply jako tryb działania między warstwy 1 i warstwy 2 i delta_datashipping do dostarczania warstwy 3. Można wybrać tylko jeden lub inny tryb pracy, który musi być spójny dla wszystkich warstw. Ponieważ delta_datashipping nie nadaje się do uzyskania RPO = 0, jedynym rozsądnym trybem pracy dla takiej konfiguracji wielowarstwowej pozostaje logreplay. Aby uzyskać szczegółowe informacje na temat trybów pracy i niektórych ograniczeń, zobacz artykuł SAP [Tryby pracy dla replikacji systemu SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html). 

## <a name="next-steps"></a>Następne kroki

Aby uzyskać wskazówki krok po kroku dotyczące konfigurowania tych konfiguracji na platformie Azure, zobacz:

- [Konfigurowanie replikacji systemu SAP HANA na maszynach wirtualnych platformy Azure](sap-hana-high-availability.md)
- [Wysoka dostępność systemu SAP HANA przy użyciu replikacji systemu](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

 



 
  
