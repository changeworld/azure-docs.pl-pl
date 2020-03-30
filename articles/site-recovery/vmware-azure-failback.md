---
title: Odzyskiwanie maszyn wirtualnych VMware/serwerów fizycznych z platformy Azure za pomocą usługi Azure Site Recovery
description: Dowiedz się, jak wrócić po awarii do lokacji lokalnej po przewijaniu awaryjnym na platformie Azure podczas odzyskiwania po awarii maszyn wirtualnych vmware i serwerów fizycznych na platformie Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.date: 01/15/2019
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: cd4cc90fb102d517a47ba458619e22b8921dd498
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75495329"
---
# <a name="fail-back-vmware-vms-to-on-premises-site"></a>Powiększanie maszyn wirtualnych VMware do lokacji lokalnej

W tym artykule opisano, jak przywrócić maszyny wirtualne platformy Azure po awarii do lokacji lokalnej, po [przejściu w stan failover](site-recovery-failover.md) lokalnych maszyn wirtualnych do platformy Azure za pomocą [usługi Azure Site Recovery.](site-recovery-overview.md) Po łączeniu po awarii do lokalnego, można włączyć replikację, tak aby lokalne maszyny wirtualne rozpocząć replikację na platformie Azure.

## <a name="before-you-start"></a>Przed rozpoczęciem

1. Dowiedz się więcej o [chyłce VMware .](failover-failback-overview.md#vmwarephysical-reprotectionfailback) 
2. Upewnij się, że zostały sprawdzone i wykonane kroki, aby przygotować się do [powrotu po awarii](vmware-azure-prepare-failback.md)i że wszystkie wymagane składniki są wdrażane. Składniki obejmują serwer przetwarzania na platformie Azure, lokalny główny serwer docelowy i połączenie lokacji sieci VPN (lub prywatna komunikacja równorzędna usługi ExpressRoute) w przypadku powrotu po awarii.
3. Upewnij się, że zostały ukończone [wymagania dotyczące](vmware-azure-reprotect.md#before-you-begin) ponownego wycofywania i powrotu po awarii i że [włączono ponowneodtwowanie](vmware-azure-reprotect.md#enable-reprotection) maszyn wirtualnych platformy Azure, tak aby były replikowane z platformy Azure do lokacji lokalnej. Maszyny wirtualne muszą być w stanie replikacji jest kolejność po awarii.




## <a name="run-a-failover-to-fail-back"></a>Uruchamianie trybu failover do powrotu w tył awaryjny

1. Upewnij się, że maszyny wirtualne platformy Azure są ponownie cekrowane i replikowane do lokacji lokalnej. 
    - Maszyna wirtualna potrzebuje co najmniej jednego punktu odzyskiwania, aby wrócić awaryjnie.
    - Jeśli plan odzyskiwania zakończy się niepowodzeniem, wszystkie maszyny w planie powinny mieć co najmniej jeden punkt odzyskiwania.
2. W przechowalni > **elementy replikowane**wybierz maszynę wirtualną. Kliknij prawym przyciskiem myszy maszynę wirtualną > **nieplanowaną pracy awaryjnej**.
3. W **obszarze Potwierdzanie pracy awaryjnej**sprawdź kierunek pracy awaryjnej (z platformy Azure).
4. Wybierz punkt odzyskiwania, którego chcesz użyć podczas pracy w trybie failover.
    - Zaleca się użycie punktu odzyskiwania **Najnowsze.** Punkt spójny z aplikacją znajduje się za najnowszym punktem w czasie i powoduje utratę danych.
    - **Najnowszy** jest punktem odzyskiwania u danych awaryjnych.
    - Z **Latest**, maszyna wirtualna nie działa do najnowszego dostępnego punktu w czasie. Jeśli masz grupę replikacji dla spójności wielu maszyn wirtualnych w ramach planu odzyskiwania, każda maszyna wirtualna w grupie działa w trybie fail over do niezależnego ostatniego punktu w czasie.
    - Jeśli używasz punktu odzyskiwania spójne z aplikacją, każda maszyna wirtualna kończy się niepowodzeniem do najnowszego dostępnego punktu. Jeśli plan odzyskiwania ma grupę replikacji, każda grupa odzyskuje do wspólnego dostępnego punktu odzyskiwania.
5. Rozpoczyna się przewijowanie w stan failover. Usługa Site Recovery zamyka maszyny wirtualne platformy Azure.
6. Po zakończeniu pracy awaryjnej sprawdź, czy wszystko działa zgodnie z oczekiwaniami. Sprawdź, czy maszyny wirtualne platformy Azure są zamykane. 
7. Po zweryfikowaniu wszystkiego kliknij prawym przyciskiem myszy maszynę wirtualną > **commit**, aby zakończyć proces pracy awaryjnej. Zatwierdzanie usuwa nie powiodło się maszyny Wirtualnej platformy Azure. 

> [!NOTE]
> W przypadku maszyn wirtualnych z systemem Windows funkcja Odzysk witryny wyłącza narzędzia VMware podczas pracy awaryjnej. Podczas powrotu po awarii maszyny Wirtualnej systemu Windows narzędzia VMware są ponownie włączane. 




## <a name="reprotect-from-on-premises-to-azure"></a>Ponowne przecenianie z lokalnego środowiska na platformę Azure

Po zaangażowaniu po awarii maszyny wirtualne platformy Azure są usuwane. Maszyna wirtualna jest z powrotem w lokacji lokalnej, ale nie jest chroniony. Aby ponownie uruchomić replikację maszyn wirtualnych na platformę Azure w następujący sposób:

1. W przechowalni > **elementy replikowane**wybierz opcję niepowodzeniem z powrotem maszyn wirtualnych, a następnie wybierz pozycję **Re-Protect**.
2. Określ serwer przetwarzania, który jest używany do wysyłania danych z powrotem na platformę Azure.
3. Wybierz **przycisk OK,** aby rozpocząć zadanie ponownego ceł.

> [!NOTE]
> Po uruchomieniu lokalnej maszyny Wirtualnej trwa do 15 minut dla agenta, aby zarejestrować się z powrotem na serwerze konfiguracji. W tym czasie ponowne nadanie błędu kończy się niepowodzeniem i zwraca komunikat o błędzie informujący, że agent nie jest zainstalowany. W takim przypadku należy odczekać kilka minut i ponownie skróć.

## <a name="next-steps"></a>Następne kroki

Po zakończeniu zadania ponownego ceł lokalnej maszyny Wirtualnej jest replikowanie na platformie Azure. W razie potrzeby można [uruchomić inną funkcję failover](site-recovery-failover.md) na platformie Azure.

