---
title: Konfigurowanie replikacji funkcji miejsca do magazynowania bezpośrednie maszyn wirtualnych (S2d) w usłudze Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania replikacji dla maszyn wirtualnych mających S2D, między regionami platformy Azure do innego za pomocą Site Recovery.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 01/29/2019
ms.author: asgang
ms.openlocfilehash: 6c639d4503b170660abed5767e3571c8a2bf24b9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60790337"
---
# <a name="replicate-azure-virtual-machines-using-storage-spaces-direct-to-another-azure-region"></a>Replikowanie maszyn wirtualnych platformy Azure przy użyciu przez bezpośrednie miejsca do magazynowania magazynu do innego regionu platformy Azure

W tym artykule opisano, jak włączyć odzyskiwanie po awarii maszyn wirtualnych platformy Azure uruchomionej funkcji miejsca do magazynowania bezpośrednich.

>[!NOTE]
>Tylko punkty odzyskiwania spójnego na poziomie awarii są obsługiwane dla klastrów bezpośrednich miejsc do magazynowania magazynu.
>

## <a name="introduction"></a>Wprowadzenie 
[Miejsca do magazynowania bezpośrednie (S2D)](https://docs.microsoft.com/windows-server/storage/storage-spaces/deploy-storage-spaces-direct) jest magazynu zdefiniowanego programowo, która umożliwia tworzenie [klastrów gościa](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure) na platformie Azure.  Klaster gościa w systemie Microsoft Azure jest klaster trybu Failover składające się z maszyn wirtualnych IaaS. Umożliwia ona hostowana obciążenia maszyn wirtualnych do trybu failover w klastrach gości, osiągnięcie wyższej dostępności umowy SLA dla aplikacji niż jednej maszyny Wirtualnej platformy Azure może zapewnić. Jest to przydatne w scenariuszach, w którym maszyny Wirtualnej obsługującego aplikacja o krytycznym znaczeniu, takich jak bazy danych SQL lub skalowania serwer plików itp.

## <a name="disaster-recovery-of-azure-virtual-machines-using-storage-spaces-direct"></a>Bezpośrednimi miejscami do magazynowania po awarii odzyskiwania usługi Azure Virtual Machines przy użyciu magazynu
W typowym scenariuszu może być maszyn wirtualnych klastra gościa na platformie Azure pod kątem odporności wyższe aplikacji takich jak skalowanie w poziomie serwer plików. Chociaż może to zapewnić wyższą dostępność Twojej aplikacji, chcesz chronić te aplikacje przy użyciu Site Recovery dla jakiekolwiek niepowodzenie poziomu regionu. Usługa Site Recovery replikuje dane z jednego regionu do innego regionu platformy Azure i zapewnia klaster w regionie odzyskiwania po awarii w przypadku przejścia w tryb failover.

Poniższy diagram przedstawia graficznie przedstawiają dwa klastra trybu failover maszyny wirtualne platformy Azure przy użyciu magazynu spacje bezpośrednich.

![storagespacesdirect](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacedirect.png)

 
- Dwie maszyny wirtualne platformy Azure w klastrze pracy awaryjnej Windows i każda maszyna wirtualna ma co najmniej dwóch dysków z danymi.
- S2D synchronizuje dane na dysku danych i prezentuje zsynchronizowane magazynu jako puli magazynów.
- Pula magazynów przedstawia jako udostępniony wolumin klastra (CSV) do klastra trybu failover.
- Klaster trybu Failover używa CSV dla dysków z danymi.

**Zagadnienia dotyczące odzyskiwania po awarii**

1. Gdy konfigurujesz [monitora w chmurze](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness#CloudWitnessSetUp) klastra, należy zachować monitora w regionie odzyskiwania po awarii.
2. Jeśli w tryb failover maszyn wirtualnych do podsieci w regionie odzyskiwania po awarii, który jest inny niż region źródłowy adres IP klastra musi można zmienić po zakończeniu trybu failover.  Aby zmienić adres IP klastra, należy użyć usługi ASR [skryptu planu odzyskiwania.](https://docs.microsoft.com/azure/site-recovery/site-recovery-runbook-automation)</br>
[Przykładowy skrypt](https://github.com/krnese/azure-quickstart-templates/blob/master/asr-automation-recovery/scripts/ASR-Wordpress-ChangeMysqlConfig.ps1) można wykonać polecenia wewnątrz maszyny Wirtualnej przy użyciu rozszerzenia niestandardowego skryptu 

### <a name="enabling-site-recovery-for-s2d-cluster"></a>Włączanie Site Recovery dla klastra S2D:

1. Wewnątrz odzyskiwania usługi magazynu, kliknij przycisk "+ Replikuj"
1. Zaznacz wszystkie węzły w klastrze i uczynić je częścią [grupa spójności wielu maszyn wirtualnych](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-common-questions#multi-vm-consistency)
1. Wybierz zasady replikacji z spójność aplikacji poza * (tylko Obsługa spójności awarii jest dostępna)
1. Włączanie replikacji

   ![Ochrona storagespacesdirect](./media/azure-to-azure-how-to-enable-replication-s2d-vms/multivmgroup.png)

2. Przejdź do zreplikowane elementy i zobaczyć stan maszyny wirtualnej. 
3. Maszyny wirtualne są wprowadzenie chronione i są także wyświetlane jako część grupy spójności wielu maszyn wirtualnych.

   ![Ochrona storagespacesdirect](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacesdirectgroup.PNG)

## <a name="creating-a-recovery-plan"></a>Trwa tworzenie planu odzyskiwania
Plan odzyskiwania obsługuje sekwencjonowania różnych warstw w aplikacji wielowarstwowej podczas przejścia w tryb failover. Sekwencjonowanie pomaga w utrzymaniu spójności aplikacji. Po utworzeniu planu odzyskiwania w przypadku aplikacji sieci web w wielowarstwowych pełną kroki opisane w [utworzyć plan odzyskiwania przy użyciu usługi Site Recovery](site-recovery-create-recovery-plans.md).

### <a name="adding-virtual-machines-to-failover-groups"></a>Dodawanie maszyn wirtualnych do grupy trybu failover

1.  Tworzenie planu odzyskiwania, dodając maszyny wirtualne.
2.  Kliknij przycisk "Dostosuj", aby pogrupować maszyny wirtualne. Domyślnie wszystkie maszyny wirtualne są częścią "Grupa 1".


### <a name="add-scripts-to-the-recovery-plan"></a>Dodaj skrypty do planu odzyskiwania
Dla Twoich aplikacji działać poprawnie konieczne może być czy niektóre operacje na maszynach wirtualnych Azure po przełączeniu w tryb failover lub podczas testowania trybu failover. Można zautomatyzować niektóre operacje po przejściu do trybu failover. Na przykład w tym miejscu jest podłączany modułu równoważenia obciążenia i zmiana adres IP klastra.


### <a name="failover-of-the-virtual-machines"></a>Tryb failover maszyn wirtualnych 
Węzły maszyn wirtualnych musi być w trybie Failover przy użyciu [planu odzyskiwania usługi ASR](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) 

![Ochrona storagespacesdirect](./media/azure-to-azure-how-to-enable-replication-s2d-vms/recoveryplan.PNG)

## <a name="run-a-test-failover"></a>Wykonywanie próby przejścia w tryb failover
1.  W witrynie Azure portal wybierz swój magazyn usługi Recovery Services.
2.  Wybierz plan odzyskiwania, który został utworzony.
3.  Wybierz pozycję **Test pracy w trybie failover**.
4.  Aby rozpocząć proces testu trybu failover, wybierz punkt odzyskiwania i usługa Azure virtual network.
5.  Po skonfigurowaniu dodatkowej środowiska wykonywać operacji sprawdzania poprawności.
6.  Po zakończeniu walidacji można wyczyścić środowisko trybu failover wybierz **wyczyść test pracy awaryjnej**.

Aby uzyskać więcej informacji, zobacz [testowy tryb failover na platformie Azure w usłudze Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Uruchamianie trybu failover

1.  W witrynie Azure portal wybierz swój magazyn usługi Recovery Services.
2.  Wybierz plan odzyskiwania, który został utworzony dla aplikacji SAP.
3.  Wybierz pozycję **Tryb failover**.
4.  Aby rozpocząć proces pracy awaryjnej, wybierz punkt odzyskiwania.

Aby uzyskać więcej informacji, zobacz [trybu Failover w usłudze Site Recovery](site-recovery-failover.md).
## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się więcej](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback) o uruchamianiu powrotu po awarii.
