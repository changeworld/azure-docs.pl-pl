---
title: Przygotowywanie platformy Azure do odzyskiwania po awarii maszyn lokalnych przy użyciu usługi Azure Site Recovery | Microsoft Docs
description: Dowiedz się, jak przygotować platformę Azure do odzyskiwania po awarii maszyn lokalnych przy użyciu usługi Azure Site Recovery.
services: site-recovery
author: mayurigupta13
ms.service: site-recovery
ms.topic: tutorial
ms.date: 03/03/2019
ms.author: mayg
ms.custom: MVC
ms.openlocfilehash: 5168fc28952631f00c2415d6bc171a130dc85dfd
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2019
ms.locfileid: "57838565"
---
# <a name="prepare-azure-resources-for-disaster-recovery-of-on-premises-machines"></a>Przygotowywanie zasobów platformy Azure na potrzeby odzyskiwania po awarii maszyn lokalnych

 Usługa [Azure Site Recovery](site-recovery-overview.md) przyczynia się do strategii związanej z ciągłością biznesową i odzyskiwaniem po awarii (BCDR, business continuity and disaster recovery) przez zapewnienie niezawodnego działania aplikacji biznesowych podczas planowanych lub nieplanowanych przestojów. Usługa Site Recovery zarządza odzyskiwaniem po awarii maszyn lokalnych i maszyn wirtualnych platformy Azure, a także organizuje to odzyskiwanie. Obejmuje to replikację, przechodzenie w tryb failover i odzyskiwanie.

Ten artykuł jest pierwszym samouczkiem z serii pokazującej, jak skonfigurować odzyskiwanie po awarii dla lokalnych maszyn wirtualnych. Istotne jest, czy pojawiła się konieczność ochrony lokalnych maszyn wirtualnych VMware, Hyper-V czy serwerów fizycznych.

> [!NOTE]
> Samouczki mają za zadanie przedstawić najprostszą ścieżkę wdrożenia dla scenariusza. Jeśli to możliwe, używają opcji domyślnych i nie przedstawiają wszystkich możliwych ustawień i ścieżek. Aby uzyskać szczegółowe instrukcje, zapoznaj się z sekcją **Instrukcje** odpowiedniego scenariusza.

W tym artykule przedstawiono sposób przygotowania składników platformy Azure w przypadku replikowania lokalnych maszyn wirtualnych (korzystających z funkcji Hyper-V lub oprogramowania VMware) bądź serwerów fizycznych z systemami Windows i Linux do platformy Azure. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Sprawdzanie, czy konto platformy Azure ma uprawnienia do replikacji.
> * Utwórz magazyn usługi Recovery Services. Magazyn przechowuje metadane i informacje o konfiguracji dla maszyn wirtualnych i innych składników replikacji.
> * Skonfiguruj sieć platformy Azure. Gdy maszyny wirtualne są tworzone po przejściu w tryb failover, dołączają one do sieci platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="verify-account-permissions"></a>Sprawdzanie uprawnień konta

Jeśli bezpłatne konto platformy Azure zostało właśnie utworzone, jesteś administratorem subskrypcji. Jeśli nie jesteś administratorem subskrypcji, współpracuj z administratorem w celu przypisania potrzebnych uprawnień. Aby włączyć replikację dla nowej maszyny wirtualnej, konieczne jest posiadanie następujących uprawnień:

- Tworzenie maszyny wirtualnej w wybranej grupie zasobów.
- Tworzenie maszyny wirtualnej w wybranej sieci wirtualnej.
- Napisz do konta magazynu.
- Zapis na dysku zarządzanego.

Aby można było wykonać te zadania, do konta musi być przypisana wbudowana rola Współautor maszyny wirtualnej. Ponadto na potrzeby zarządzania operacjami usługi Site Recovery w magazynie do konta powinna być przypisana wbudowana rola Współautor usługi Site Recovery.


## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

1. W witrynie Azure Portal kliknij pozycję **+Utwórz zasób** i w witrynie Marketplace wyszukaj pozycję **Recovery Services**.
2. Kliknij pozycję **Backup and Site Recovery (OMS)**, a następnie na stronie Backup and Site Recovery kliknij pozycję **Utwórz**. 
1. W obszarze **Magazyn usługi Recovery Services** > **Nazwa** wprowadź przyjazną nazwę identyfikującą magazyn. Na potrzeby tej serii samouczków należy użyć nazwy **ContosoVMVault**.
2. W obszarze **Grupa zasobów** wybierz istniejącą grupę zasobów lub utwórz nową. Na potrzeby tego samouczka używamy grupy **contosoRG**.
3. W obszarze **Lokalizacja** wybierz region, w którym ma znajdować się magazyn. Użyj wartości **Europa Zachodnia**.
4. Aby szybko uzyskać dostęp do magazynu z poziomu pulpitu nawigacyjnego, wybierz pozycję **Przypnij do pulpitu nawigacyjnego** > **Utwórz**.

   ![Tworzenie nowego magazynu](./media/tutorial-prepare-azure/new-vault-settings.png)

   Nowy magazyn będzie wyświetlany w sekcji **Pulpit nawigacyjny** > **Wszystkie zasoby** oraz na stronie głównej **Magazyny usług Recovery Services**.

## <a name="set-up-an-azure-network"></a>Konfiguracja sieci platformy Azure

Podczas tworzenia maszyn wirtualnych platformy Azure z dyskami zarządzanymi po włączeniu trybu failover, dołączają one do tej sieci.

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz pozycję **Utwórz zasób** > **Sieć** > **Sieć wirtualna**.
2. Jako model wdrażania pozostaw wybraną pozycję **Menedżer zasobów**.
3. W obszarze **Nazwa** wprowadź nazwę sieci. Nazwa musi być unikatowa w obrębie grupy zasobów platformy Azure. W tym samouczku użyto nazwy **ContosoASRnet**.
4. Podaj grupę zasobów, w której zostanie utworzona sieć. Należy użyć istniejącej grupy zasobów **contosoRG**.
5. W obszarze **Zakres adresów** wprowadź zakres sieci **10.0.0.0/24**. W tej sieci nie należy używać podsieci.
6. W obszarze **Subskrypcja** wybierz subskrypcję, w ramach której chcesz utworzyć sieć.
7. W obszarze **Lokalizacja** wybierz pozycję **Europa Zachodnia**. Sieć musi znajdować się w tym samym regionie co magazyn Usług odzyskiwania.
8. Należy pozostawić opcje domyślne podstawowej ochrony DDoS bez punktu końcowego usługi w sieci.
9. Kliknij pozycję **Utwórz**.

   ![Tworzenie sieci wirtualnej](media/tutorial-prepare-azure/create-network.png)

   Utworzenie sieci wirtualnej zajmuje kilka sekund. Po utworzeniu sieć jest wyświetlana na pulpicie nawigacyjnym witryny Azure Portal.

## <a name="useful-links"></a>Przydatne łącza

- [Dowiedz się więcej na temat](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) sieci platformy Azure.
- [Dowiedz się więcej o](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) dysków zarządzanych.



## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Przygotowywanie lokalnej infrastruktury VMware do odzyskiwania po awarii na platformie Azure](tutorial-prepare-on-premises-vmware.md)
