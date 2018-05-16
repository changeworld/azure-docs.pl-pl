---
title: Tworzenie zasobów do używania z usługą Azure Site Recovery | Microsoft Docs
description: Dowiedz się, jak przygotować platformę Azure do replikacji maszyn lokalnych przy użyciu usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 05/02/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 852c854de9feb9bcc98fc89aa9340b93f2c4e8d3
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="prepare-azure-resources-for-replication-of-on-premises-machines"></a>Przygotowywanie zasobów platformy Azure do replikacji maszyn lokalnych

 Usługa [Azure Site Recovery](site-recovery-overview.md) przyczynia się do strategii związanej z ciągłością biznesową i odzyskiwaniem po awarii (BCDR, business continuity and disaster recovery) przez zapewnienie niezawodnego działania aplikacji biznesowych podczas planowanych lub nieplanowanych przestojów. Usługa Site Recovery zarządza odzyskiwaniem po awarii maszyn lokalnych i maszyn wirtualnych platformy Azure, a także organizuje to odzyskiwanie. Obejmuje to replikację, przechodzenie w tryb failover i odzyskiwanie.

W tym samouczku przedstawiono sposób przygotowania składników platformy Azure w przypadku replikowania lokalnych maszyn wirtualnych (korzystających z funkcji Hyper-V lub oprogramowania VMware) bądź serwerów fizycznych z systemami Windows i Linux do platformy Azure. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Sprawdzanie, czy konto platformy Azure ma uprawnienia do replikacji.
> * Utworzenie konta magazynu platformy Azure. Na nim przechowywane są replikowane dane.
> * Utwórz magazyn usługi Recovery Services.
> * Konfigurowanie sieci platformy Azure. Gdy maszyny wirtualne są tworzone po przejściu w tryb failover, dołączają one do sieci platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w [Portalu Azure](http://portal.azure.com).

## <a name="verify-account-permissions"></a>Sprawdzanie uprawnień konta

Jeśli bezpłatne konto platformy Azure zostało właśnie utworzone, jesteś administratorem subskrypcji. Jeśli nie jesteś administratorem subskrypcji, współpracuj z administratorem w celu przypisania potrzebnych uprawnień. Aby włączyć replikację dla nowej maszyny wirtualnej, konieczne jest posiadanie następujących uprawnień:

- Tworzenie maszyny wirtualnej w wybranej grupie zasobów.
- Tworzenie maszyny wirtualnej w wybranej sieci wirtualnej.
- Zapis na wybranym koncie magazynu.

Aby można było wykonać te zadania, do konta musi być przypisana wbudowana rola Współautor maszyny wirtualnej. Ponadto na potrzeby zarządzania operacjami usługi Site Recovery w magazynie do konta powinna być przypisana wbudowana rola Współautor usługi Site Recovery.

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Obrazy replikowanych maszyn są przechowywane w usłudze Azure Storage. Maszyny wirtualne Azure są tworzone na podstawie magazynu w momencie przejścia w tryb failover ze środowiska lokalnego do platformy Azure.

1. W menu witryny [Azure Portal](https://portal.azure.com) wybierz pozycję **Nowy** > **Storage** > **Konto usługi Storage**.
2. W obszarze **Tworzenie konta magazynu** wprowadź nazwę konta. Na potrzeby tego samouczka użyj nazwy **contosovmsacct1910171607**. Nazwa musi mieć od 3 do 24 znaków długości, zawierać wyłącznie cyfry i małe litery oraz musi być unikatowa w obrębie platformy Azure.
3. W obszarze **Model wdrażania** wybierz opcję **Resource Manager**.
4. W obszarze **Rodzaj konta** wybierz opcję **Ogólnego przeznaczenia**. W obszarze **Wydajność** wybierz opcję **Standardowa**. Nie wybieraj magazynu obiektów blob.
5. W obszarze **Replikacja** wybierz domyślny **Magazyn geograficznie nadmiarowy z dostępem do odczytu** jako opcję nadmiarowości magazynu.
6. W obszarze **Subskrypcja** wybierz subskrypcję, w ramach której chcesz utworzyć nowe konto magazynu.
7. W obszarze **Grupa zasobów** wprowadź nową nazwę grupy zasobów. Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Na potrzeby tych samouczków użyj nazwy **ContosoRG**.
8. W obszarze **Lokalizacja** wybierz lokalizację geograficzną dla swojego konta magazynu. Konto magazynu musi znajdować się w tym samym regionie, co magazyn usługi Recovery Services. Na potrzeby tych samouczków użyj regionu **Europa Zachodnia**.

   ![Tworzenie konta magazynu](media/tutorial-prepare-azure/create-storageacct.png)

9. Wybierz pozycję **Utwórz**, aby utworzyć konto magazynu.

## <a name="create-a-vault"></a>Tworzenie magazynu

1. W witrynie Azure Portal wybierz pozycję **Utwórz zasób** > **Monitorowanie i zarządzanie** > **Backup i Site Recovery**.
2. W polu **Nazwa** wprowadź przyjazną nazwę identyfikującą magazyn. Na potrzeby tego samouczka użyj nazwy **ContosoVMVault**.
3. W obszarze **Grupa zasobów** wybierz istniejącą grupę zasobów o nazwie **contosoRG**.
4. W obszarze **Lokalizacja** wprowadź region platformy Azure **Europa Zachodnia**, który jest używany w tym zestawie samouczków.
5. Aby szybko uzyskać dostęp do magazynu z poziomu pulpitu nawigacyjnego, wybierz pozycję **Przypnij do pulpitu nawigacyjnego** > **Utwórz**.

   ![Tworzenie nowego magazynu](./media/tutorial-prepare-azure/new-vault-settings.png)

   Nowy magazyn będzie wyświetlany w sekcji **Pulpit nawigacyjny** > **Wszystkie zasoby** oraz na stronie głównej **Magazyny usług Recovery Services**.

## <a name="set-up-an-azure-network"></a>Konfiguracja sieci platformy Azure

Gdy maszyny wirtualne platformy Azure są tworzone na podstawie magazynu po przejściu w tryb failover, dołączają one do tej sieci.

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz pozycję **Utwórz zasób** > **Sieć** > **Sieć wirtualna**.
2. Jako model wdrażania pozostaw wybraną pozycję **Resource Manager**. Wdrażanie przy użyciu usługi Resource Manager jest preferowanym modelem wdrażania. Następnie wykonaj następujące czynności:

   a. W obszarze **Nazwa** wprowadź nazwę sieci. Nazwa musi być unikatowa w obrębie grupy zasobów platformy Azure. Użyj nazwy **ContosoASRnet**.

   b. W obszarze **Grupa zasobów** użyj istniejącej grupy zasobów **contosoRG**.

   d. W obszarze **Zakres adresów** wprowadź zakres adresów sieciowych **10.0.0.0/24**.

   d. W tym samouczku podsieć nie jest wymagana.

   e. W obszarze **Subskrypcja** wybierz subskrypcję, w ramach której chcesz utworzyć sieć.

   f. W obszarze **Lokalizacja** wybierz pozycję **Europa Zachodnia**. Sieć musi znajdować się w tym samym regionie co magazyn Usług odzyskiwania.

3. Wybierz pozycję **Utwórz**.

   ![Tworzenie sieci wirtualnej](media/tutorial-prepare-azure/create-network.png)

   Utworzenie sieci wirtualnej zajmuje kilka sekund. Po utworzeniu sieć jest wyświetlana na pulpicie nawigacyjnym witryny Azure Portal.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Przygotowywanie lokalnej infrastruktury VMware do odzyskiwania po awarii na platformie Azure](tutorial-prepare-on-premises-vmware.md)
