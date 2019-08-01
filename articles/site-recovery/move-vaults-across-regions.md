---
title: Przeniesienie konfiguracji Azure Site Recovery do innego regionu platformy Azure | Microsoft Docs
description: Wskazówki dotyczące przeniesienia konfiguracji Site Recovery do innego regionu platformy Azure
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/31/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: ba0e2577d6fb8bac66322917936fe06825af0d96
ms.sourcegitcommit: 6ad03fa28a0f60cb6dce6144f728c2ceb56ff6e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68708298"
---
# <a name="moving-recovery-services-vault-and--azure-site-recovery-configuration-to-another-azure-region"></a>Przechodzenie Recovery Services magazynu i konfiguracji Azure Site Recovery do innego regionu platformy Azure

Istnieją różne scenariusze, w których należy przenieść istniejące zasoby platformy Azure z jednego regionu do innego, z powodów związanych z zarządzaniem lub z powodu fuzji lub przejęć firmy itp. Jeden z pokrewnych zasobów można przenieść po przeniesieniu maszyn wirtualnych platformy Azure w taki sam sposób jak Konfiguracja odzyskiwania po awarii. Nie ma pierwszej klasy sposobu przenoszenia istniejącej konfiguracji odzyskiwania po awarii z jednego regionu do innego. Wynika to z faktu, że Twój region docelowy został skonfigurowany na podstawie źródłowego regionu maszyny wirtualnej, a kiedy zdecydujesz się zmienić to, wcześniej istniejące konfiguracje regionu docelowego nie mogą być ponownie używane i muszą zostać zresetowane. W tym artykule opisano proces krok po kroku w celu ponownego skonfigurowania konfiguracji odzyskiwania po awarii i przejścia do innego regionu.

W tym dokumencie zawarto następujące:

> [!div class="checklist"]
> * Sprawdź wymagania wstępne dotyczące przenoszenia
> * Zidentyfikuj zasoby, które były używane przez Azure Site Recovery 
> * Wyłączanie replikacji
> * Usuń zasoby 
> * Skonfiguruj ponowne odzyskiwanie lokacji na podstawie nowego regionu źródłowego dla maszyn wirtualnych.

> [!IMPORTANT]
> Obecnie nie ma potrzeby przenoszenia magazynu Recovery Services i konfiguracji DR w taki sam sposób jak w innym regionie, ten dokument przedstawia klienta w procesie wyłączania replikacji i resetowania go w nowym regionie.

## <a name="prerequisites"></a>Wymagania wstępne

- Przed przystąpieniem do przenoszenia maszyn wirtualnych platformy Azure do innego regionu należy usunąć i usunąć konfigurację odzyskiwania po awarii. 

> [!NOTE]
> Jeśli nowy region docelowy dla maszyny wirtualnej platformy Azure jest taki sam jak region docelowy odzyskiwania po awarii, możesz użyć istniejącej konfiguracji replikacji i przenieść ją na podstawie kroków wymienionych [tutaj](azure-to-azure-tutorial-migrate.md) 

- Upewnij się, że podjęto świadomą decyzję oraz że zainteresowane strony są poinformowane, że dana maszyna wirtualna nie będzie chroniona przed awariami na zakończenie przenoszenia maszyny wirtualnej. 

## <a name="identify-the-resources-that-were-used-by-azure-site-recovery"></a>Zidentyfikuj zasoby, które były używane przez Azure Site Recovery
Zalecamy wykonanie tego kroku przed przejściem do kolejnego, ponieważ będzie łatwiej identyfikować odpowiednie zasoby, gdy maszyny wirtualne nadal są replikowane

Dla każdej zreplikowanej maszyny wirtualnej platformy Azure przejdź do **pozycji elementy** > chronione**zreplikowane**>**Właściwości** elementów i zidentyfikuj następujące zasoby

- Docelowa grupa zasobów
- Konto magazynu pamięci podręcznej
- Docelowe konto magazynu (w przypadku niezarządzanej maszyny wirtualnej platformy Azure opartej na dyskach) 
- Sieć docelowa


## <a name="disable-existing-disaster-recovery-configuration"></a>Wyłącz istniejącą konfigurację odzyskiwania po awarii

1. Przejdź do **magazynu Recovery Services** 
2.  W obszarze **chronione elementy** > **zreplikowane elementy**kliknij prawym przyciskiem myszy maszynę > **Wyłącz replikację**.
3. Powtórz tę czynność dla wszystkich maszyn wirtualnych, które chcesz przenieść.
> [!NOTE]
> usługa mobilności nie zostanie odinstalowana z chronionych serwerów, należy odinstalować ją ręcznie. Jeśli planujesz ponownie chronić serwer, możesz pominąć Odinstalowywanie usługi mobilności.

## <a name="delete-the-resources"></a>Usuń zasoby

1. Przejdź do **magazynu Recovery Services**
2. Kliknij pozycję **Usuń**
3. Usuń wszystkie inne zasoby zidentyfikowane w[ poprzednim kroku](#identify-the-resources-that-were-used-by-azure-site-recovery)
 
## <a name="move-azure-vms-to-the-new-target-region"></a>Przenoszenie maszyn wirtualnych platformy Azure do nowego regionu docelowego

Wykonaj kroki wymienione poniżej w zależności od wymagań dotyczących przenoszenia maszyn wirtualnych platformy Azure do regionu docelowego.

- [Przenoszenie maszyn wirtualnych platformy Azure do innego regionu](azure-to-azure-tutorial-migrate.md)
- [Przenoszenie maszyn wirtualnych platformy Azure do stref dostępności](move-azure-VMs-AVset-Azone.md)

## <a name="re-set-up-site-recovery-based-on-the-new-source-region-for-the-vms"></a>Ponowne konfigurowanie Site Recovery na podstawie nowego regionu źródłowego dla maszyn wirtualnych

Skonfiguruj odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure, które zostały przeniesione do nowego regionu, wykonując kroki opisane [tutaj](azure-to-azure-tutorial-enable-replication.md) .
