---
title: Przygotowywanie platformy Azure do odzyskiwania po awarii maszyn lokalnych przy użyciu usługi Azure Site Recovery | Microsoft Docs
description: Dowiedz się, jak przygotować platformę Azure do odzyskiwania po awarii maszyn lokalnych przy użyciu usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: d2de871176917dcc24d910b3742bdb2700c4f25d
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64691770"
---
# <a name="prepare-azure-resources-for-disaster-recovery-of-on-premises-machines"></a>Przygotowywanie zasobów platformy Azure na potrzeby odzyskiwania po awarii maszyn lokalnych

W tym artykule opisano sposób przygotowania składników i zasobów platformy Azure, aby skonfigurować odzyskiwanie po awarii lokalnych maszyn wirtualnych VMware, maszyny wirtualne funkcji Hyper-V lub systemu Windows/Linux serwerów fizycznych na platformę Azure przy użyciu [usługi Azure Site Recovery](site-recovery-overview.md) usługi.

Ten artykuł jest pierwszym samouczkiem z serii pokazującej, jak skonfigurować odzyskiwanie po awarii dla lokalnych maszyn wirtualnych. 


Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Sprawdź, czy konto platformy Azure ma uprawnienia do replikacji.
> * Utwórz magazyn usługi Recovery Services. Magazyn przechowuje metadane i informacje o konfiguracji dla maszyn wirtualnych i innych składników replikacji.
> * Konfigurowanie sieci wirtualnej platformy Azure (VNet). Podczas tworzenia maszyn wirtualnych platformy Azure po włączeniu trybu failover, dołączają one do tej sieci.

> [!NOTE]
> W samouczkach pokazano to najprostsza ścieżka wdrażania scenariusza. Jeśli to możliwe, używają opcji domyślnych i nie przedstawiają wszystkich możliwych ustawień i ścieżek. Szczegółowe informacje na ten temat można znaleźć w artykule w sekcji jak tabeli odzyskiwania lokacji zawartości.

## <a name="before-you-start"></a>Przed rozpoczęciem

- Przegląd architektury [VMware](vmware-azure-architecture.md), [funkcji Hyper-V](hyper-v-azure-architecture.md), i [serwera fizycznego](physical-azure-architecture.md) odzyskiwania po awarii.
- Przeczytaj często zadawane pytania dotyczące [VMware](vmware-azure-common-questions.md) i [funkcji Hyper-V](hyper-v-azure-common-questions.md)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/). Następnie zaloguj się do [witryny Azure portal](https://portal.azure.com).


## <a name="verify-account-permissions"></a>Sprawdzanie uprawnień konta

Jeśli bezpłatne konto platformy Azure jest właśnie utworzone, jesteś administratorem subskrypcji, i masz potrzebnych uprawnień. Jeśli nie jesteś administratorem subskrypcji, współpracuj z administratorem w celu przypisania potrzebnych uprawnień. Aby włączyć replikację dla nowej maszyny wirtualnej, konieczne jest posiadanie następujących uprawnień:

- Tworzenie maszyny wirtualnej w wybranej grupie zasobów.
- Tworzenie maszyny wirtualnej w wybranej sieci wirtualnej.
- Zapisz konto magazynu platformy Azure.
- Zapis na platformie Azure dysku zarządzanego.

Aby można było wykonać te zadania, do konta musi być przypisana wbudowana rola Współautor maszyny wirtualnej. Ponadto na potrzeby zarządzania operacjami usługi Site Recovery w magazynie do konta powinna być przypisana wbudowana rola Współautor usługi Site Recovery.


## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

1. W witrynie Azure portal kliknij pozycję **+ Utwórz zasób**, a w portalu Marketplace wyszukaj hasło **odzyskiwania**.
2. Kliknij pozycję **Backup and Site Recovery (OMS)**, a następnie na stronie Backup and Site Recovery kliknij pozycję **Utwórz**. 
1. W obszarze **Magazyn usługi Recovery Services** > **Nazwa** wprowadź przyjazną nazwę identyfikującą magazyn. Na potrzeby tej serii samouczków należy użyć nazwy **ContosoVMVault**.
2. W obszarze **Grupa zasobów** wybierz istniejącą grupę zasobów lub utwórz nową. Na potrzeby tego samouczka używamy grupy **contosoRG**.
3. W obszarze **Lokalizacja** wybierz region, w którym ma znajdować się magazyn. Użyj wartości **Europa Zachodnia**.
4. Aby szybko uzyskać dostęp do magazynu z poziomu pulpitu nawigacyjnego, wybierz pozycję **Przypnij do pulpitu nawigacyjnego** > **Utwórz**.

   ![Tworzenie nowego magazynu](./media/tutorial-prepare-azure/new-vault-settings.png)

   Nowy magazyn będzie wyświetlany w sekcji **Pulpit nawigacyjny** > **Wszystkie zasoby** oraz na stronie głównej **Magazyny usług Recovery Services**.

## <a name="set-up-an-azure-network"></a>Konfiguracja sieci platformy Azure

Lokalne maszyny są replikowane do usługi Azure managed disks. Po przejściu do trybu failover, maszyny wirtualne platformy Azure są tworzone na podstawie tych dysków zarządzanych i przyłączone do sieci platformy Azure, określonych w tej procedurze.

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz pozycję **Utwórz zasób** > **Sieć** > **Sieć wirtualna**.
2. Zachowaj **usługi Resource Manager** wybrany jako model wdrażania.
3. W obszarze **Nazwa** wprowadź nazwę sieci. Nazwa musi być unikatowa w obrębie grupy zasobów platformy Azure. W tym samouczku użyto nazwy **ContosoASRnet**.
4. Podaj grupę zasobów, w której zostanie utworzona sieć. Należy użyć istniejącej grupy zasobów **contosoRG**.
5. W **zakres adresów**, wprowadź zakres sieci. Firma Microsoft korzysta z **10.1.0.0/24**, a nie za pomocą podsieci.
6. W obszarze **Subskrypcja** wybierz subskrypcję, w ramach której chcesz utworzyć sieć.
7. W **lokalizacji**, wybierz region, w tym samym jako, w którym utworzono magazyn usługi Recovery Services. W naszym samouczku przedstawiono w nim **Europa Zachodnia**. Sieć musi znajdować się w tym samym regionie co magazyn.
8. Należy pozostawić opcje domyślne podstawowej ochrony DDoS bez punktu końcowego usługi w sieci.
9. Kliknij pozycję **Utwórz**.

   ![Tworzenie sieci wirtualnej](media/tutorial-prepare-azure/create-network.png)

Utworzenie sieci wirtualnej zajmuje kilka sekund. Po utworzeniu sieć jest wyświetlana na pulpicie nawigacyjnym witryny Azure Portal.




## <a name="next-steps"></a>Kolejne kroki

- Do odzyskiwania po awarii programu VMware [przygotowywanie lokalnej infrastruktury VMware](tutorial-prepare-on-premises-vmware.md).
- Do odzyskiwania po awarii z funkcji Hyper-V [Przygotuj lokalne serwery funkcji Hyper-V](hyper-v-prepare-on-premises-tutorial.md).
- Do odzyskiwania po awarii serwerów fizycznych [Konfigurowanie środowiska konfiguracji serwera i źródła](physical-azure-disaster-recovery.md)
- [Dowiedz się więcej na temat](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) sieci platformy Azure.
- [Dowiedz się więcej o](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) dysków zarządzanych.
