---
title: Przygotowywanie platformy Azure do lokalnego odzyskiwania po awarii za pomocą usługi Azure Site Recovery
description: Dowiedz się, jak przygotować platformę Azure do odzyskiwania po awarii maszyn lokalnych przy użyciu usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/09/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: ba5ecc29edbcd69324500e87add846e4395ce0a3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80067562"
---
# <a name="prepare-azure-for-on-premises-disaster-recovery-to-azure"></a>Przygotowanie platformy Azure do lokalnego odzyskiwania po awarii na platformie Azure

W tym artykule opisano sposób przygotowania zasobów i składników platformy Azure, dzięki czemu można skonfigurować odzyskiwanie po awarii lokalnych maszyn wirtualnych VMware, maszyn wirtualnych funkcji Hyper V lub serwerów fizycznych systemu Windows/Linux na platformie Azure przy użyciu usługi [Azure Site Recovery.](site-recovery-overview.md)

Ten artykuł jest pierwszym samouczkiem z serii pokazującej, jak skonfigurować odzyskiwanie po awarii dla lokalnych maszyn wirtualnych. 


Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Sprawdź, czy konto platformy Azure ma uprawnienia do replikacji.
> * Utwórz magazyn usługi Recovery Services. Magazyn przechowuje metadane i informacje o konfiguracji dla maszyn wirtualnych i innych składników replikacji.
> * Konfigurowanie sieci wirtualnej platformy Azure (sieci wirtualnej). Gdy maszyny wirtualne platformy Azure są tworzone po przełączeniu w stan failover, są one przyłączone do tej sieci.

> [!NOTE]
> Samouczki pokazują najprostszą ścieżkę wdrażania dla scenariusza. Jeśli to możliwe używają opcji domyślnych i nie przedstawiają wszystkich możliwych ustawień i ścieżek. Aby uzyskać szczegółowe instrukcje, zapoznaj się z artykułem w sekcji Jak to zrobić w spisie treści odzyskiwania witryny.

## <a name="before-you-start"></a>Przed rozpoczęciem

- Przejrzyj architekturę [vmware](vmware-azure-architecture.md), [Hyper-V](hyper-v-azure-architecture.md)i odzyskiwania po awarii [serwera fizycznego.](physical-azure-architecture.md)
- Przeczytaj często zadawane pytania dotyczące [oprogramowania VMware](vmware-azure-common-questions.md) i [funkcji Hyper-V](hyper-v-azure-common-questions.md)

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) przed rozpoczęciem. Następnie zaloguj się do [witryny Azure portal](https://portal.azure.com).


## <a name="verify-account-permissions"></a>Sprawdzanie uprawnień konta

Jeśli właśnie utworzono bezpłatne konto platformy Azure, jesteś administratorem subskrypcji i masz uprawnienia, których potrzebujesz. Jeśli nie jesteś administratorem subskrypcji, współpracuj z administratorem w celu przypisania potrzebnych uprawnień. Aby włączyć replikację dla nowej maszyny wirtualnej, konieczne jest posiadanie następujących uprawnień:

- Tworzenie maszyny wirtualnej w wybranej grupie zasobów.
- Tworzenie maszyny wirtualnej w wybranej sieci wirtualnej.
- Zapisuj na koncie magazynu platformy Azure.
- Zapis na dysku zarządzanym platformy Azure.

Aby można było wykonać te zadania, do konta musi być przypisana wbudowana rola Współautor maszyny wirtualnej. Ponadto, aby zarządzać operacjami odzyskiwania witryny w przechowalni, do konta należy przypisać wbudowaną rolę współautora odzyskiwania witryny.


## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

1. Z menu Portalu Platformy Azure wybierz pozycję **Utwórz zasób**i wyszukaj w portalu Marketplace **w poszukiwaniu odzyskiwania**.
2. Wybierz **pozycję Kopia zapasowa i odzyskiwanie witryny** z wyników wyszukiwania, a następnie na stronie Kopia zapasowa i odzyskiwanie witryny kliknij pozycję **Utwórz**. 
3. Na stronie **Tworzenie usług odzyskiwania** wybierz pozycję **Subskrypcja**. Używamy **contoso subskrypcji**.
4. W obszarze **Grupa zasobów** wybierz istniejącą grupę zasobów lub utwórz nową. Na potrzeby tego samouczka używamy grupy **contosoRG**.
5. W **nazwie Przechowalni**wprowadź przyjazną nazwę, aby zidentyfikować przechowalnię. Na potrzeby tej serii samouczków należy użyć nazwy **ContosoVMVault**.
6. W **obszarze Region**wybierz region, w którym ma się znajdować przechowalnia. Użyj wartości **Europa Zachodnia**.
7. Wybierz pozycję **Przegląd + utwórz**.

   ![Tworzenie nowego magazynu](./media/tutorial-prepare-azure/new-vault-settings.png)

   Nowy magazyn zostanie wyświetlony na **pulpicie nawigacyjnym** > **Wszystkie zasoby**oraz na głównej stronie **magazynów usług odzyskiwania.**

## <a name="set-up-an-azure-network"></a>Konfiguracja sieci platformy Azure

Maszyny lokalne są replikowane na dyskach zarządzanych platformy Azure. W przypadku pracy awaryjnej maszyny wirtualne platformy Azure są tworzone na podstawie tych dysków zarządzanych i dołączane do sieci platformy Azure określonej w tej procedurze.

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz kolejno pozycje **Utwórz zasób** > **Sieć** > **Sieć wirtualna**.
2. Zachowaj **Menedżera zasobów** wybranego jako model wdrażania.
3. W obszarze **Nazwa** wprowadź nazwę sieci. Nazwa musi być unikatowa w obrębie grupy zasobów platformy Azure. W tym samouczku użyto nazwy **ContosoASRnet**.
4. W **przestrzeni adresowej**wprowadź zakres adresów sieci wirtualnej w notacji CDR. Używamy **10.1.0.0/24**.
5. W obszarze **Subskrypcja** wybierz subskrypcję, w ramach której chcesz utworzyć sieć.
6. Określ **grupę Zasobów,** w której będzie tworzona sieć. Należy użyć istniejącej grupy zasobów **contosoRG**.
7. W **obszarze Lokalizacja**wybierz ten sam region, w którym utworzono magazyn usług odzyskiwania. W naszym poradniku to **Europa Zachodnia**. Sieć musi znajdować się w tym samym regionie co przechowalnia.
8. W **zakresie adresów**wprowadź zakres sieci. Używamy **10.1.0.0/24**i nie używamy podsieci.
9. Pozostawiamy domyślne opcje podstawowej ochrony przed atakami DDoS bez punktu końcowego usługi lub zapory w sieci.
9. Wybierz **pozycję Utwórz**.

   ![Tworzenie sieci wirtualnej](media/tutorial-prepare-azure/create-network.png)

Utworzenie sieci wirtualnej zajmuje kilka sekund. Po jego utworzeniu zobaczysz go na pulpicie nawigacyjnym portalu Azure.




## <a name="next-steps"></a>Następne kroki

- W przypadku odzyskiwania po awarii VMware [przygotuj lokalną infrastrukturę VMware](tutorial-prepare-on-premises-vmware.md).
- W przypadku odzyskiwania po awarii funkcji Hyper-V [przygotuj lokalne serwery funkcji Hyper-V](hyper-v-prepare-on-premises-tutorial.md).
- W przypadku odzyskiwania po awarii serwera [fizycznego skonfiguruj serwer konfiguracji i środowisko źródłowe](physical-azure-disaster-recovery.md)
- [Dowiedz się więcej na temat](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) sieci platformy Azure.
- [Dowiedz się więcej o](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) dyskach zarządzanych.
