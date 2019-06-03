---
title: Przygotowywanie zasobów platformy Azure na potrzeby odzyskiwania po awarii maszyn lokalnych
description: Dowiedz się, jak przygotować platformę Azure do odzyskiwania po awarii maszyn wirtualnych funkcji Hyper-V w środowisku lokalnym za pomocą usługi Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 07a5ee6ccdaecc78c9a8e61ae9e64a5264e3a875
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66418361"
---
# <a name="prepare-azure-resources-for-disaster-recovery-of-on-premises-machines"></a>Przygotowywanie zasobów platformy Azure na potrzeby odzyskiwania po awarii maszyn lokalnych

 [Usługa Azure Site Recovery](site-recovery-overview.md) pomaga firm ciągłości działania i odzyskiwania po awarii (BCDR) przez zapewnienie niezawodnego aplikacji biznesowych podczas planowanych i nieplanowanych przestojów uruchomione. Usługa Site Recovery zarządza odzyskiwaniem po awarii maszyn lokalnych i maszyn wirtualnych platformy Azure, a także organizuje to odzyskiwanie. Obejmuje to replikację, przechodzenie w tryb failover i odzyskiwanie.

Ten samouczek jest pierwszą w serii, w którym opisano sposób konfigurowania odzyskiwania po awarii dla maszyn wirtualnych funkcji Hyper-V w środowisku lokalnym.

> [!NOTE]
> Firma Microsoft projektowania samouczkami, aby wyświetlić to najprostsza ścieżka wdrażania scenariusza. Tych samouczków użyj opcji domyślnych, gdy jest to możliwe, a nie pokazuj wszystkich możliwych ustawień i ścieżki. Aby uzyskać więcej informacji zobacz sekcję "How to", dla każdego odpowiedniego scenariusza.

W tym samouczku przedstawiono sposób przygotowania składników platformy Azure, jeśli chcesz replikować lokalne maszyny wirtualne (Hyper-V) do platformy Azure. Omawiane tematy:

> [!div class="checklist"]
> * Sprawdzanie, czy konto platformy Azure ma uprawnienia do replikacji.
> * Utwórz konto magazynu platformy Azure, w której są przechowywane obrazy replikowanych maszyn.
> * Utwórz magazyn usługi Recovery Services, w którym przechowywane są metadane i informacje dotyczące konfiguracji dla maszyn wirtualnych i inne składniki replikacji.
> * Skonfiguruj sieć platformy Azure. Podczas tworzenia maszyn wirtualnych platformy Azure po włączeniu trybu failover, dołączają one do tej sieci.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).

## <a name="sign-in"></a>Logowanie

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="verify-account-permissions"></a>Sprawdzanie uprawnień konta

Jeśli bezpłatne konto platformy Azure jest właśnie utworzone, jesteś administratora dla tej subskrypcji. Jeśli nie jesteś administratorem, Współpracuj z administratorem w celu przypisania potrzebnych uprawnień. Aby włączyć replikację dla nowej maszyny wirtualnej, konieczne jest posiadanie następujących uprawnień:

- Tworzenie maszyny wirtualnej w wybranej grupie zasobów.
- Tworzenie maszyny wirtualnej w wybranej sieci wirtualnej.
- Zapis na wybranym koncie magazynu.

Aby wykonać te zadania, do konta powinna przypisana wbudowana rola Współautor maszyny wirtualnej. Aby zarządzać operacjami usługi Site Recovery w magazynie, do konta powinna przypisana wbudowana rola Współautor usługi Site Recovery.

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Obrazy replikowanych maszyn są przechowywane w usłudze Azure Storage. Maszyny wirtualne Azure są tworzone na podstawie magazynu w momencie przejścia w tryb failover ze środowiska lokalnego do platformy Azure. Konto magazynu musi znajdować się w tym samym regionie, co magazyn usługi Recovery Services.

1. W [witryny Azure portal](https://portal.azure.com) menu, wybierz opcję **Utwórz zasób** > **magazynu** > **konto magazynu — obiektów blob, plik, tabela, kolejka** .
2. W obszarze **Tworzenie konta magazynu** wprowadź nazwę konta.  Wybrana nazwa musi być unikatowa w obrębie platformy Azure, od 3 do 24 znaków i tylko używane małe litery i cyfry. W tym samouczku używane **contosovmsacct1910171607**.
3. W obszarze **Model wdrażania** wybierz opcję **Resource Manager**.
4. W **rodzaj konta**, wybierz opcję **magazynu (ogólnego przeznaczenia w wersji 1)** . Nie wybieraj magazynu obiektów blob.
5. W obszarze **Replikacja** wybierz domyślny **Magazyn geograficznie nadmiarowy z dostępem do odczytu** jako opcję nadmiarowości magazynu. Pozostaw bezpieczny transfer wymagane ustawienia, ponieważ jest wyłączony.
6. W obszarze **Wydajność** wybierz opcję **Standardowa**. Następnie w **warstwy dostępu**, wybierz opcję domyślną **gorąca**.
7. W **subskrypcji**, wybierz subskrypcję, w której chcesz utworzyć nowe konto magazynu.
8. W obszarze **Grupa zasobów** wprowadź nową nazwę grupy zasobów. Grupę zasobów platformy Azure to logiczny kontener, w których Azure zasoby są wdrażane i zarządzane. W tym samouczku używane **ContosoRG**.
9. W **lokalizacji**, wybierz lokalizację geograficzną dla swojego konta magazynu. W tym samouczku używane **Europa Zachodnia**.
10. Wybierz pozycję **Utwórz**, aby utworzyć konto magazynu.

   ![Tworzenie konta magazynu](media/tutorial-prepare-azure/create-storageacct.png)

## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

1. W witrynie Azure portal wybierz **+ Utwórz zasób**, a następnie wyszukaj w portalu Azure Marketplace usługi Recovery Services.
2. Wybierz **Backup i Site Recovery (OMS)** . Następnie na **Backup i Site Recovery** wybierz opcję **Utwórz**.
1. W **magazyn usługi Recovery services > nazwa**, wprowadź przyjazną nazwę identyfikującą magazyn. Na potrzeby tego samouczka użyj nazwy **ContosoVMVault**.
2. W obszarze **Grupa zasobów** wybierz istniejącą grupę zasobów lub utwórz nową. W tym samouczku używane **contosoRG**.
3. W **lokalizacji**, wybierz region, w którym magazynu powinien być zlokalizowany. W tym samouczku używane **Europa Zachodnia**.
4. Aby szybko uzyskać dostęp do magazynu z poziomu pulpitu nawigacyjnego, wybierz pozycję **Przypnij do pulpitu nawigacyjnego** > **Utwórz**.

![Tworzenie nowego magazynu](./media/tutorial-prepare-azure/new-vault-settings.png)

Nowy magazyn będzie wyświetlany w sekcji **Pulpit nawigacyjny** > **Wszystkie zasoby** oraz na stronie głównej **Magazyny usług Recovery Services**.

## <a name="set-up-an-azure-network"></a>Konfiguracja sieci platformy Azure

Gdy maszyny wirtualne platformy Azure są tworzone na podstawie magazynu po przejściu w tryb failover, dołączają one do tej sieci.

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz pozycję **Utwórz zasób** > **Sieć** > **Sieć wirtualna**. Pozostaw usługi Resource Manager, wybrany jako model wdrażania.
2. W obszarze **Nazwa** wprowadź nazwę sieci. Nazwa musi być unikatowa w obrębie grupy zasobów platformy Azure. W tym samouczku używane **ContosoASRnet**.
3. Określ grupę zasobów, w której chcesz utworzyć sieć. W tym samouczku Użyj istniejącej grupy zasobów **contosoRG**.
4. W **zakres adresów**, wprowadź **10.0.0.0/24** jako zakres sieci. Nie ma żadnych podsieci dla tej sieci.
5. W obszarze **Subskrypcja** wybierz subskrypcję, w ramach której chcesz utworzyć sieć.
6. W **lokalizacji**, wybierz **Europa Zachodnia**. Sieć musi znajdować się w tym samym regionie co magazyn Usług odzyskiwania.
7. Pozostaw domyślne opcje podstawową ochronę przed atakami DDoS, za pomocą nie punktu końcowego usługi w sieci.
8. Wybierz pozycję **Utwórz**.

![Tworzenie sieci wirtualnej](media/tutorial-prepare-azure/create-network.png)

Utworzenie sieci wirtualnej zajmuje kilka sekund. Po jego utworzeniu, zobaczysz go w pulpicie nawigacyjnym witryny Azure portal.

## <a name="useful-links"></a>Przydatne łącza

Dowiedz się więcej:
- [Sieci platformy Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)
- [Dyski zarządzane](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)



## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Przygotowywanie lokalnej infrastruktury funkcji Hyper-V do odzyskiwania po awarii na platformie Azure](hyper-v-prepare-on-premises-tutorial.md)
