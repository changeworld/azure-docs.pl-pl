---
title: Przygotowywanie platformy Azure do odzyskiwania po awarii maszyn lokalnych przy użyciu usługi Azure Site Recovery | Microsoft Docs
description: Dowiedz się, jak przygotować platformę Azure do odzyskiwania po awarii maszyn lokalnych przy użyciu usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 87216317a965e85d83aede468163f43b1716c45a
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68827238"
---
# <a name="prepare-azure-resources-for-disaster-recovery-of-on-premises-machines"></a>Przygotowywanie zasobów platformy Azure na potrzeby odzyskiwania po awarii maszyn lokalnych

W tym artykule opisano sposób przygotowania zasobów i składników platformy Azure, dzięki czemu można skonfigurować odzyskiwanie po awarii lokalnych maszyn wirtualnych programu VMware, maszyn wirtualnych funkcji Hyper-V lub serwerów fizycznych systemu Windows/Linux na platformę Azure przy użyciu usługi [Azure Site Recovery](site-recovery-overview.md) .

Ten artykuł jest pierwszym samouczkiem z serii pokazującej, jak skonfigurować odzyskiwanie po awarii dla lokalnych maszyn wirtualnych. 


Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Sprawdź, czy konto platformy Azure ma uprawnienia replikacji.
> * Utwórz magazyn usługi Recovery Services. Magazyn przechowuje metadane i informacje o konfiguracji dla maszyn wirtualnych i innych składników replikacji.
> * Skonfiguruj sieć wirtualną platformy Azure. Gdy maszyny wirtualne platformy Azure są tworzone po przejściu w tryb failover, są one przyłączone do tej sieci.

> [!NOTE]
> Samouczki pokazują najprostszą ścieżkę wdrożenia dla scenariusza. Jeśli to możliwe, używają opcji domyślnych i nie przedstawiają wszystkich możliwych ustawień i ścieżek. Aby uzyskać szczegółowe instrukcje, zapoznaj się z artykułem w sekcji jak to zrobić w spisie treści Site Recovery.

## <a name="before-you-start"></a>Przed rozpoczęciem

- Zapoznaj się z architekturą dla programu [VMware](vmware-azure-architecture.md), [funkcji Hyper-V](hyper-v-azure-architecture.md)i odzyskiwania po awarii [serwera fizycznego](physical-azure-architecture.md) .
- Przeczytaj często zadawane pytania dotyczące oprogramowania [VMware](vmware-azure-common-questions.md) i [funkcji Hyper-V](hyper-v-azure-common-questions.md)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/). Następnie zaloguj się do [Azure Portal](https://portal.azure.com).


## <a name="verify-account-permissions"></a>Sprawdzanie uprawnień konta

Jeśli masz już utworzone bezpłatne konto platformy Azure, jesteś administratorem subskrypcji i masz wymagane uprawnienia. Jeśli nie jesteś administratorem subskrypcji, współpracuj z administratorem w celu przypisania potrzebnych uprawnień. Aby włączyć replikację dla nowej maszyny wirtualnej, konieczne jest posiadanie następujących uprawnień:

- Tworzenie maszyny wirtualnej w wybranej grupie zasobów.
- Tworzenie maszyny wirtualnej w wybranej sieci wirtualnej.
- Zapisz na koncie usługi Azure Storage.
- Zapisz na dysku zarządzanym platformy Azure.

Aby można było wykonać te zadania, do konta musi być przypisana wbudowana rola Współautor maszyny wirtualnej. Ponadto na potrzeby zarządzania operacjami usługi Site Recovery w magazynie do konta powinna być przypisana wbudowana rola Współautor usługi Site Recovery.


## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

1. W Azure Portal kliknij pozycję **+ Utwórz zasób**i przeszukaj witrynę Marketplace pod kątem **odzyskiwania**.
2. Kliknij pozycję **kopia zapasowa i Site Recovery**, a następnie na stronie kopia zapasowa i Site Recovery kliknij przycisk **Utwórz**. 
1. W obszarze **Magazyn usługi Recovery Services** > **Nazwa** wprowadź przyjazną nazwę identyfikującą magazyn. Na potrzeby tej serii samouczków należy użyć nazwy **ContosoVMVault**.
2. W obszarze **Grupa zasobów** wybierz istniejącą grupę zasobów lub utwórz nową. Na potrzeby tego samouczka używamy grupy **contosoRG**.
3. W obszarze **Lokalizacja** wybierz region, w którym ma znajdować się magazyn. Użyj wartości **Europa Zachodnia**.
4. Aby szybko uzyskać dostęp do magazynu z poziomu pulpitu nawigacyjnego, wybierz pozycję **Przypnij do pulpitu nawigacyjnego** > **Utwórz**.

   ![Tworzenie nowego magazynu](./media/tutorial-prepare-azure/new-vault-settings.png)

   Nowy magazyn będzie wyświetlany w sekcji **Pulpit nawigacyjny** > **Wszystkie zasoby** oraz na stronie głównej **Magazyny usług Recovery Services**.

## <a name="set-up-an-azure-network"></a>Konfiguracja sieci platformy Azure

Maszyny lokalne są replikowane do usługi Azure Managed Disks. W przypadku przejścia w tryb failover maszyny wirtualne platformy Azure są tworzone na podstawie tych dysków zarządzanych i przyłączone do sieci platformy Azure określonej w tej procedurze.

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz pozycję **Utwórz zasób** > **Sieć** > **Sieć wirtualna**.
2. Zachowaj **Menedżer zasobów** wybrane jako model wdrażania.
3. W obszarze **Nazwa** wprowadź nazwę sieci. Nazwa musi być unikatowa w obrębie grupy zasobów platformy Azure. W tym samouczku użyto nazwy **ContosoASRnet**.
4. Podaj grupę zasobów, w której zostanie utworzona sieć. Należy użyć istniejącej grupy zasobów **contosoRG**.
5. W obszarze **zakres adresów**Wprowadź zakres sieci. Korzystamy z **10.1.0.0/24**, a nie do korzystania z podsieci.
6. W obszarze **Subskrypcja** wybierz subskrypcję, w ramach której chcesz utworzyć sieć.
7. W obszarze **Lokalizacja**wybierz ten sam region, w którym został utworzony magazyn Recovery Services. W naszym samouczku jest **Europa Zachodnia**. Sieć musi znajdować się w tym samym regionie co magazyn.
8. Należy pozostawić opcje domyślne podstawowej ochrony DDoS bez punktu końcowego usługi w sieci.
9. Kliknij przycisk **Utwórz**.

   ![Tworzenie sieci wirtualnej](media/tutorial-prepare-azure/create-network.png)

Utworzenie sieci wirtualnej zajmuje kilka sekund. Po utworzeniu sieć jest wyświetlana na pulpicie nawigacyjnym witryny Azure Portal.




## <a name="next-steps"></a>Kolejne kroki

- W przypadku odzyskiwania po awarii programu VMware [Przygotuj lokalną infrastrukturę VMware](tutorial-prepare-on-premises-vmware.md).
- W przypadku odzyskiwania po awarii funkcji Hyper-V [Przygotuj lokalne serwery funkcji Hyper-v](hyper-v-prepare-on-premises-tutorial.md).
- W przypadku odzyskiwania po awarii serwera fizycznego [Skonfiguruj serwer konfiguracji i środowisko źródłowe](physical-azure-disaster-recovery.md)
- [Dowiedz się więcej na temat](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) sieci platformy Azure.
- Informacje [o](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) dyskach zarządzanych.
