---
title: Przygotowywanie platformy Azure na potrzeby odzyskiwania po awarii maszyn wirtualnych funkcji Hyper-V w środowisku lokalnym za pomocą usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przygotować platformę Azure do odzyskiwania po awarii lokalnych maszyn wirtualnych funkcji Hyper-V za pomocą usługi Azure Site Recovery.
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 48101e49429225018381ed2a3b1e8e4e351c15a6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61285220"
---
# <a name="prepare-azure-resources-for-disaster-recovery-of-on-premises-machines"></a>Przygotowywanie zasobów platformy Azure na potrzeby odzyskiwania po awarii maszyn lokalnych

 Usługa [Azure Site Recovery](site-recovery-overview.md) przyczynia się do strategii związanej z ciągłością biznesową i odzyskiwaniem po awarii (BCDR, business continuity and disaster recovery) przez zapewnienie niezawodnego działania aplikacji biznesowych podczas planowanych lub nieplanowanych przestojów. Usługa Site Recovery zarządza odzyskiwaniem po awarii maszyn lokalnych i maszyn wirtualnych platformy Azure, a także organizuje to odzyskiwanie. Obejmuje to replikację, przechodzenie w tryb failover i odzyskiwanie.

Ten artykuł jest pierwszym samouczkiem z serii pokazującej, jak skonfigurować odzyskiwanie po awarii dla lokalnych maszyn wirtualnych. Może to być istotne, gdy chronisz maszyny wirtualne funkcji Hyper-V.

> [!NOTE]
> Samouczki mają za zadanie przedstawić najprostszą ścieżkę wdrożenia dla scenariusza. Jeśli to możliwe, używają opcji domyślnych i nie przedstawiają wszystkich możliwych ustawień i ścieżek. Aby uzyskać szczegółowe instrukcje, zapoznaj się z sekcją **Instrukcje** odpowiedniego scenariusza.

W tym artykule przedstawiono sposób przygotowania składników platformy Azure, jeśli chcesz replikować lokalne maszyny wirtualne (Hyper-V) do platformy Azure. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Sprawdzanie, czy konto platformy Azure ma uprawnienia do replikacji.
> * Utworzenie konta magazynu platformy Azure. Są na nim przechowywane obrazy replikowanych maszyn.
> * Utwórz magazyn usługi Recovery Services. Magazyn przechowuje metadane i informacje o konfiguracji dla maszyn wirtualnych i innych składników replikacji.
> * Skonfiguruj sieć platformy Azure. Gdy maszyny wirtualne są tworzone po przejściu w tryb failover, dołączają one do sieci platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w witrynie [Azure Portal](http://portal.azure.com).

## <a name="verify-account-permissions"></a>Sprawdzanie uprawnień konta

Jeśli bezpłatne konto platformy Azure zostało właśnie utworzone, jesteś administratorem subskrypcji. Jeśli nie jesteś administratorem subskrypcji, współpracuj z administratorem w celu przypisania potrzebnych uprawnień. Aby włączyć replikację dla nowej maszyny wirtualnej, konieczne jest posiadanie następujących uprawnień:

- Tworzenie maszyny wirtualnej w wybranej grupie zasobów.
- Tworzenie maszyny wirtualnej w wybranej sieci wirtualnej.
- Zapis na wybranym koncie magazynu.

Aby można było wykonać te zadania, do konta musi być przypisana wbudowana rola Współautor maszyny wirtualnej. Ponadto na potrzeby zarządzania operacjami usługi Site Recovery w magazynie do konta powinna być przypisana wbudowana rola Współautor usługi Site Recovery.

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Obrazy replikowanych maszyn są przechowywane w usłudze Azure Storage. Maszyny wirtualne Azure są tworzone na podstawie magazynu w momencie przejścia w tryb failover ze środowiska lokalnego do platformy Azure. Konto magazynu musi znajdować się w tym samym regionie, co magazyn usługi Recovery Services. W tym samouczku użyto przykładu Europy Zachodniej.

1. W menu witryny [Azure Portal](https://portal.azure.com) wybierz kolejno opcje **Utwórz zasób** > **Magazyn** > **Konto magazynu — obiekt BLOB, plik, tabela, kolejka**.
2. W obszarze **Tworzenie konta magazynu** wprowadź nazwę konta. Na potrzeby tych samouczków należy użyć nazwy **contosovmsacct1910171607**. Wybrana nazwa musi mieć od 3 do 24 znaków długości, zawierać wyłącznie cyfry i małe litery oraz musi być unikatowa w obrębie platformy Azure.
3. W obszarze **Model wdrażania** wybierz opcję **Resource Manager**.
4. W obszarze **Rodzaj konta** wybierz opcję **Magazyn (ogólnego przeznaczenia w wersji 1)**. Nie wybieraj magazynu obiektów blob.
5. W obszarze **Replikacja** wybierz domyślny **Magazyn geograficznie nadmiarowy z dostępem do odczytu** jako opcję nadmiarowości magazynu. Firma Microsoft pozostawia opcję **Wymagany bezpieczny transfer** z ustawieniem **Wyłączone**.
6. W obszarze **Wydajność** wybierz pozycję **Standardowa**, a w obszarze **Warstwa dostępu** wybierz domyślną opcję **Gorąca**.
7. W obszarze **Subskrypcja** wybierz subskrypcję, w ramach której chcesz utworzyć nowe konto magazynu.
8. W obszarze **Grupa zasobów** wprowadź nową nazwę grupy zasobów. Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Na potrzeby tych samouczków użyto nazwy **ContosoRG**.
9. W obszarze **Lokalizacja** wybierz lokalizację geograficzną dla swojego konta magazynu. 

   ![Tworzenie konta magazynu](media/tutorial-prepare-azure/create-storageacct.png)

9. Wybierz pozycję **Utwórz**, aby utworzyć konto magazynu.

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

Gdy maszyny wirtualne platformy Azure są tworzone na podstawie magazynu po przejściu w tryb failover, dołączają one do tej sieci.

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
> [Przygotowywanie lokalnej infrastruktury funkcji Hyper-V do odzyskiwania po awarii na platformie Azure](hyper-v-prepare-on-premises-tutorial.md)
