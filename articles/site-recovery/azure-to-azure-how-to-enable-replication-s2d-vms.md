---
title: Replikowanie maszyn wirtualnych platformy Azure z bezpośrednimi miejscami do magazynowania za pomocą usługi Azure Site Recovery
description: Dowiedz się, jak replikować maszyny wirtualne platformy Azure z uruchomionymi miejscami w przestrzeni magazynowej bezpośrednio przy użyciu usługi Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 01/29/2019
ms.openlocfilehash: 9f394fa8d618c97d74a47ff6e42a002f177cf7d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75973667"
---
# <a name="replicate-azure-vms-running-storage-spaces-direct-to-another-region"></a>Replikowanie maszyn wirtualnych platformy Azure z uruchomionymi przestrzeniami magazynowymi bezpośrednio do innego regionu

W tym artykule opisano, jak włączyć odzyskiwanie po awarii maszyn wirtualnych platformy Azure z bezpośrednim uruchamianiem przestrzeni magazynowych.

>[!NOTE]
>Tylko punkty odzyskiwania zgodne z awarią są obsługiwane dla klastrów bezpośrednich miejsc do magazynowania.
>

Bezpośrednie miejsca do [magazynowania (S2D)](https://docs.microsoft.com/windows-server/storage/storage-spaces/deploy-storage-spaces-direct) to magazyn definiowany programowo, który umożliwia tworzenie [klastrów gości](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure) na platformie Azure.  Klaster gościa na platformie Microsoft Azure to klaster trybu failover składający się z maszyn wirtualnych IaaS. Umożliwia hostowane obciążenia maszyn wirtualnych w trybie failover w klastrach gościa, osiągając wyższą dostępność umowy SLA dla aplikacji, niż może zapewnić pojedyncza maszyna wirtualna platformy Azure. Jest to przydatne w scenariuszach, w których maszyna wirtualna obsługuje krytyczną aplikację, taką jak SQL lub skalowano w poziomie serwera plików.

## <a name="disaster-recovery-with-storage-spaces-direct"></a>Odzyskiwanie po awarii bezpośrednio z miejscami do magazynowania

W typowym scenariuszu może mieć maszyny wirtualne klastra gościa na platformie Azure dla większej odporności aplikacji, takich jak skalowanie w poziomie serwera plików. Chociaż może to zapewnić aplikacji wyższą dostępność, chcesz chronić te aplikacje przy użyciu usługi Site Recovery dla każdego błędu na poziomie regionu. Usługa Site Recovery replikuje dane z jednego regionu do innego regionu platformy Azure i wywołuje klaster w regionie odzyskiwania po awarii w przypadku pracy awaryjnej.

Na poniższym diagramie przedstawiono dwuwęzłowy klaster trybu failover usługi Azure VM przy użyciu bezpośrednich miejsc do magazynowania.

![obszarów magazynowychdirect](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacedirect.png)


- Dwie maszyny wirtualne platformy Azure w klastrze trybu failover systemu Windows i każda maszyna wirtualna mają dwa lub więcej dysków danych.
- S2D synchronizuje dane na dysku danych i przedstawia zsynchronizowany magazyn jako pulę magazynu.
- Pula magazynu jest prezentowana jako udostępniony wolumin klastra (CSV) klastrowi trybu failover.
- Klaster trybu failover używa pliku CSV dla dysków danych.

**Zagadnienia dotyczące odzyskiwania po awarii**

1. Podczas konfigurowania [monitora chmury](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness#CloudWitnessSetUp) dla klastra, zachowaj monitor w regionie odzyskiwania po awarii.
2. Jeśli zamierzasz przejść w tryb failover maszyn wirtualnych do podsieci w regionie odzyskiwania po awarii, który różni się od regionu źródłowego, adres IP klastra musi zostać zmieniona po przełączeniu w tryb failover.  Aby zmienić adres IP klastra, należy użyć [skryptu planu odzyskiwania](https://docs.microsoft.com/azure/site-recovery/site-recovery-runbook-automation) usługi Site Recovery.</br>
[Przykładowy skrypt](https://github.com/krnese/azure-quickstart-templates/blob/master/asr-automation-recovery/scripts/ASR-Wordpress-ChangeMysqlConfig.ps1) do wykonania polecenia wewnątrz maszyny Wirtualnej przy użyciu niestandardowego rozszerzenia skryptu 

### <a name="enabling-site-recovery-for-s2d-cluster"></a>Włączanie odzyskiwania lokacji dla klastra S2D:

1. Wewnątrz magazynu usług odzyskiwania kliknij "+replikuj"
1. Zaznacz wszystkie węzły w klastrze i uczynić je częścią [grupy spójności wielu maszyn wirtualnych](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-common-questions#multi-vm-consistency)
1. Wybierz zasady replikacji z wyłączeniem spójności aplikacji* (dostępna jest tylko obsługa spójności awarii)
1. Włączanie replikacji

   ![ochrona obszarów magazynowychbezpośredni](./media/azure-to-azure-how-to-enable-replication-s2d-vms/multivmgroup.png)

2. Przejdź do zreplikowanych elementów i widać zarówno stan maszyny wirtualnej.
3. Obie maszyny wirtualne są coraz chronione i są również wyświetlane jako część grupy spójności wielu maszyn wirtualnych.

   ![ochrona obszarów magazynowychbezpośredni](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacesdirectgroup.PNG)

## <a name="creating-a-recovery-plan"></a>Tworzenie planu odzyskiwania
Plan odzyskiwania obsługuje sekwencjonowanie różnych warstw w aplikacji wielowarstwowej podczas pracy awaryjnej. Sekwencjonowanie pomaga zachować spójność aplikacji. Podczas tworzenia planu odzyskiwania dla wielowarstwowej aplikacji sieci web wykonaj kroki opisane w [aplikacji Tworzenie planu odzyskiwania przy użyciu funkcji Odzysk witryny](site-recovery-create-recovery-plans.md).

### <a name="adding-virtual-machines-to-failover-groups"></a>Dodawanie maszyn wirtualnych do grup trybu failover

1.  Utwórz plan odzyskiwania, dodając maszyny wirtualne.
2.  Kliknij na "Dostosuj", aby zgrupować maszyny wirtualne. Domyślnie wszystkie maszyny wirtualne są częścią "Grupy 1".


### <a name="add-scripts-to-the-recovery-plan"></a>Dodawanie skryptów do planu odzyskiwania
Aby aplikacje działały poprawnie, może być konieczne wykonać niektóre operacje na maszynach wirtualnych platformy Azure po pracy awaryjnej lub podczas testowania pracy awaryjnej. Można zautomatyzować niektóre operacje po pracy awaryjnej. Na przykład w tym miejscu dołączamy moduł równoważenia obciążenia i zmieniamy adres IP klastra.


### <a name="failover-of-the-virtual-machines"></a>Przewijania awaryjnego maszyn wirtualnych 
Oba węzły maszyn wirtualnych muszą być awaryjne przy użyciu [planu odzyskiwania usługi](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) Site Recovery 

![ochrona obszarów magazynowychbezpośredni](./media/azure-to-azure-how-to-enable-replication-s2d-vms/recoveryplan.PNG)

## <a name="run-a-test-failover"></a>Wykonywanie próby przejścia w tryb failover
1.  W witrynie Azure portal wybierz magazyn usług odzyskiwania.
2.  Wybierz utworzony plan odzyskiwania.
3.  Wybierz pozycję **Test pracy w trybie failover**.
4.  Aby rozpocząć proces pracy awaryjnej testu, wybierz punkt odzyskiwania i sieć wirtualną platformy Azure.
5.  Gdy środowisko pomocnicze jest w górę, wykonać sprawdzanie poprawności.
6.  Po zakończeniu sprawdzania poprawności, aby wyczyścić środowisko pracy awaryjnej, wybierz opcję **Oczyszczanie testu pracy awaryjnej**.

Aby uzyskać więcej informacji, zobacz [Testowanie pracy awaryjnej na platformie Azure w usłudze Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Uruchamianie trybu failover

1.  W witrynie Azure portal wybierz magazyn usług odzyskiwania.
2.  Wybierz plan odzyskiwania utworzony dla aplikacji SAP.
3.  Wybierz pozycję **Tryb failover**.
4.  Aby rozpocząć proces pracy awaryjnej, wybierz punkt odzyskiwania.

Aby uzyskać więcej informacji, zobacz [Odzyskiwanie awaryjne w odzyskiwaniu witryny](site-recovery-failover.md).
## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback) o uruchamianiu powrotu po awarii.
