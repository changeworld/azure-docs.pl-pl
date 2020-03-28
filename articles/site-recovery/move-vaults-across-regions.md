---
title: Przenoszenie magazynu usługi Azure Site Recovery do innego regionu
description: W tym artykule opisano sposób przenoszenia magazynu usług odzyskiwania (Azure Site Recovery) do innego regionu platformy Azure
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/31/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 32dff9a165125ab1949560ce36438ae266cd3036
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74090295"
---
# <a name="move-a-recovery-services-vault-and-azure-site-recovery-configuration-to-another-azure-region"></a>Przenoszenie magazynu usług odzyskiwania i usługi Azure Site Recovery do innego regionu platformy Azure

Istnieją różne scenariusze, w których można przenieść istniejące zasoby platformy Azure z jednego regionu do drugiego. Przykładami są względy zarządzania, zarządzania lub fuzje i przejęcia spółek. Jednym z powiązanych zasobów, które można przenieść podczas przenoszenia maszyn wirtualnych platformy Azure jest konfiguracja odzyskiwania po awarii. 

Nie ma pierwszej klasy sposób, aby przenieść istniejącą konfigurację odzyskiwania po awarii z jednego regionu do drugiego. Jest to spowodowane skonfigurowanym regionem docelowym na podstawie źródłowego regionu maszyny Wirtualnej. Jeśli zdecydujesz się zmienić region źródłowy, wcześniej istniejące konfiguracje regionu docelowego nie mogą być ponownie używane i muszą zostać zresetowane. W tym artykule zdefiniowano proces krok po kroku w celu ponownego skonfigurowania konfiguracji odzyskiwania po awarii i przeniesienia jej do innego regionu.

W tym dokumencie:

> [!div class="checklist"]
> * Sprawdź wymagania wstępne dla przeniesienia.
> * Zidentyfikuj zasoby, które były używane przez usługę Azure Site Recovery.
> * Wyłącz replikację.
> * Usuń zasoby.
> * Skonfiguruj odzyskiwanie witryny na podstawie nowego regionu źródłowego dla maszyn wirtualnych.

> [!IMPORTANT]
> Obecnie nie ma pierwszej klasy sposób, aby przenieść magazyn usług odzyskiwania odzyskiwania i konfiguracji odzyskiwania po awarii, jak jest do innego regionu. W tym artykule poprowadzą Cię przez proces wyłączania replikacji i konfigurowania jej w nowym regionie.

## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że usuniesz i usuniesz konfigurację odzyskiwania po awarii przed podjęciem próby przeniesienia maszyn wirtualnych platformy Azure do innego regionu. 

  > [!NOTE]
  > Jeśli nowy region docelowy dla maszyny Wirtualnej platformy Azure jest taki sam jak region docelowy odzyskiwania po awarii, można użyć istniejącej konfiguracji replikacji i przenieść ją. Wykonaj kroki opisane w [obszarze Przenoszenie maszyn wirtualnych usługi Azure IaaS do innego regionu platformy Azure.](azure-to-azure-tutorial-migrate.md)

- Upewnij się, że podejmujesz świadomą decyzję i że zainteresowane strony są informowane. Maszyna wirtualna nie będzie chroniona przed awariami, dopóki przeniesienie maszyny Wirtualnej nie zostanie zakończone.

## <a name="identify-the-resources-that-were-used-by-azure-site-recovery"></a>Identyfikowanie zasobów używanych przez usługę Azure Site Recovery
Zaleca się wykonanie tego kroku przed przejściem do następnego. Łatwiej jest zidentyfikować odpowiednie zasoby podczas replikowania maszyn wirtualnych.

Dla każdej replikowanej maszyny Wirtualnej platformy Azure przejdź do właściwości**replikowanych elementów** >  **elementów** > **chronionych** i zidentyfikowano następujące zasoby:

- Docelowa grupa zasobów
- Konto magazynu pamięci podręcznej
- Konto magazynu docelowego (w przypadku niezarządzanej maszyny Wirtualnej platformy Azure opartej na dysku) 
- Sieć docelowa


## <a name="disable-the-existing-disaster-recovery-configuration"></a>Wyłączanie istniejącej konfiguracji odzyskiwania po awarii

1. Przejdź do magazynu usług odzyskiwania.
2. W **obszarze Elementy** > chronione**elementy replikowane**kliknij prawym przyciskiem myszy komputer i wybierz polecenie **Wyłącz replikację**.
3. Powtórz ten krok dla wszystkich maszyn wirtualnych, które chcesz przenieść.

> [!NOTE]
> Usługa mobilności nie zostanie odinstalowana z chronionych serwerów. Należy go odinstalować ręcznie. Jeśli planujesz ponownie chronić serwer, możesz pominąć odinstalowanie usługi mobilności.

## <a name="delete-the-resources"></a>Usuwanie zasobów

1. Przejdź do magazynu usług odzyskiwania.
2. Wybierz pozycję **Usuń**.
3. Usuń wszystkie inne zasoby, które [zostały wcześniej zidentyfikowane](#identify-the-resources-that-were-used-by-azure-site-recovery).
 
## <a name="move-azure-vms-to-the-new-target-region"></a>Przenoszenie maszyn wirtualnych platformy Azure do nowego regionu docelowego

Wykonaj kroki opisane w tych artykułach na podstawie wymagań dotyczących przenoszenia maszyn wirtualnych platformy Azure do regionu docelowego:

- [Przenoszenie maszyn wirtualnych platformy Azure do innego regionu](azure-to-azure-tutorial-migrate.md)
- [Przenoszenie maszyn wirtualnych platformy Azure do stref dostępności](move-azure-VMs-AVset-Azone.md)

## <a name="set-up-site-recovery-based-on-the-new-source-region-for-the-vms"></a>Konfigurowanie odzyskiwania witryny na podstawie nowego regionu źródłowego dla maszyn wirtualnych

Skonfiguruj odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure, które zostały przeniesione do nowego regionu, wykonując kroki opisane w [temacie Konfigurowanie odzyskiwania po awarii dla maszyn wirtualnych platformy Azure.](azure-to-azure-tutorial-enable-replication.md)
