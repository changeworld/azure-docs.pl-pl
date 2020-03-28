---
title: Przygotowywanie platformy Azure do odzyskiwania po awarii funkcji Hyper-V za pomocą usługi Azure Site Recovery
description: Dowiedz się, jak przygotować platformę Azure do odzyskiwania po awarii lokalnych maszyn wirtualnych z programem Hyper V przy użyciu usługi Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: ef623b95e104b485c6bfc8b2f489afeca436d81e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74084172"
---
# <a name="prepare-azure-resources-for-hyper-v-disaster-recovery"></a>Przygotowywanie zasobów platformy Azure do odzyskiwania po awarii funkcji Hyper-V

 [Usługa Azure Site Recovery](site-recovery-overview.md) pomaga w ciągłości działania i odzyskiwania po awarii (BCDR), utrzymując aplikacje biznesowe uruchomione podczas planowanych i nieplanowanych awarii. Usługa Site Recovery zarządza odzyskiwaniem po awarii maszyn lokalnych i maszyn wirtualnych platformy Azure, a także organizuje to odzyskiwanie. Obejmuje to replikację, przechodzenie w tryb failover i odzyskiwanie.

Ten samouczek jest pierwszym z serii, który opisuje, jak skonfigurować odzyskiwanie po awarii dla lokalnych maszyn wirtualnych z programem Hyper-V.

> [!NOTE]
> Projektujemy samouczki, aby pokazać najprostszą ścieżkę wdrażania dla scenariusza. Te samouczki używają opcji domyślnych, jeśli to możliwe, i nie pokazują wszystkich możliwych ustawień i ścieżek. Aby uzyskać więcej informacji, zobacz sekcję "Jak to" dla każdego odpowiedniego scenariusza.

W tym samouczku pokazano, jak przygotować składniki platformy Azure, gdy chcesz replikować lokalne maszyny wirtualne (Hyper-V) na platformie Azure. Omawiane tematy:

> [!div class="checklist"]
> * Sprawdzanie, czy konto platformy Azure ma uprawnienia do replikacji.
> * Utwórz konto magazynu platformy Azure, które przechowuje obrazy zreplikowanych maszyn.
> * Utwórz magazyn usług odzyskiwania, który przechowuje metadane i informacje o konfiguracji dla maszyn wirtualnych i innych składników replikacji.
> * Skonfiguruj sieć platformy Azure. Gdy maszyny wirtualne platformy Azure są tworzone po przełączeniu w stan failover, są one przyłączone do tej sieci.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) przed rozpoczęciem.

## <a name="sign-in"></a>Logowanie

Zaloguj się do [Portalu Azure](https://portal.azure.com).

## <a name="verify-account-permissions"></a>Sprawdzanie uprawnień konta

Jeśli właśnie utworzono bezpłatne konto platformy Azure, jesteś administratorem tej subskrypcji. Jeśli nie jesteś administratorem, współpracuj z administratorem, aby przypisać potrzebne uprawnienia. Aby włączyć replikację dla nowej maszyny wirtualnej, konieczne jest posiadanie następujących uprawnień:

- Tworzenie maszyny wirtualnej w wybranej grupie zasobów.
- Tworzenie maszyny wirtualnej w wybranej sieci wirtualnej.
- Zapis na wybranym koncie magazynu.

Aby wykonać te zadania, konto powinno być przypisane do wbudowanej roli współautora maszyny wirtualnej. Aby zarządzać operacjami odzyskiwania witryny w przechowalni, do konta należy przypisać wbudowaną rolę współautora odzyskiwania witryny.

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Obrazy replikowanych maszyn są przechowywane w usłudze Azure Storage. Maszyny wirtualne Azure są tworzone na podstawie magazynu w momencie przejścia w tryb failover ze środowiska lokalnego do platformy Azure. Konto magazynu musi znajdować się w tym samym regionie, co magazyn usługi Recovery Services.

1. W**Storage** > menu portalu [platformy Azure](https://portal.azure.com) wybierz polecenie **Utwórz** > konto magazynu magazynu zasobów **— obiekt blob, plik, tabela, kolejka**.
2. W obszarze **Tworzenie konta magazynu** wprowadź nazwę konta.  Wybrana nazwa musi być unikatowa na platformie Azure, mieć od 3 do 24 znaków i używać tylko małych liter i cyfr. W tym samouczku użyj **contosovmsacct1910171607**.
3. W obszarze **Model wdrażania** wybierz opcję **Resource Manager**.
4. W **rodzaju konta**wybierz opcję Magazyn **(ogólnego przeznaczenia v1)**. Nie wybieraj magazynu obiektów blob.
5. W obszarze **Replikacja** wybierz domyślny **Magazyn geograficznie nadmiarowy z dostępem do odczytu** jako opcję nadmiarowości magazynu. Pozostaw bezpieczne przeniesienie wymagane ustawienie jako Wyłączone.
6. W obszarze **Wydajność** wybierz opcję **Standardowa**. Następnie w **warstwie dostęp wybierz**domyślną opcję **Hot**.
7. W **sekcji Subskrypcja**wybierz subskrypcję, w której chcesz utworzyć nowe konto magazynu.
8. W obszarze **Grupa zasobów** wprowadź nową nazwę grupy zasobów. Grupa zasobów platformy Azure to kontener logiczny, w którym zasoby platformy Azure są wdrażane i zarządzane. W tym samouczku użyj **contosoRG**.
9. W **aplikacji Lokalizacja**wybierz lokalizację geograficzną konta magazynu. W tym samouczku użyj **Europy Zachodniej**.
10. Wybierz pozycję **Utwórz**, aby utworzyć konto magazynu.

   ![Tworzenie konta magazynu](media/tutorial-prepare-azure/create-storageacct.png)

## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

1. W witrynie Azure portal wybierz pozycję **+Utwórz zasób,** a następnie przeszukaj witrynę Azure Marketplace w poszukiwaniu usług odzyskiwania.
2. Wybierz **pozycję Kopia zapasowa i odzyskiwanie witryny (OMS)**. Następnie na stronie **Kopia zapasowa i odzyskiwanie witryny** wybierz pozycję **Utwórz**.
1. W **magazynie usług odzyskiwania > Nazwa**wprowadź przyjazną nazwę, aby zidentyfikować przechowalnię. Na potrzeby tego samouczka użyj nazwy **ContosoVMVault**.
2. W obszarze **Grupa zasobów** wybierz istniejącą grupę zasobów lub utwórz nową. W tym samouczku użyj **contosoRG**.
3. W **obszarze Lokalizacja**wybierz region, w którym ma znajdować się przechowalnia. W tym samouczku użyj **Europy Zachodniej**.
4. Aby szybko uzyskać dostęp do repozytorium z pulpitu nawigacyjnego, wybierz **pozycję Przypnij do pulpitu nawigacyjnego** > **Utwórz**.

![Tworzenie nowego magazynu](./media/tutorial-prepare-azure/new-vault-settings.png)

Nowy magazyn pojawi się na **pulpicie nawigacyjnym** > **Wszystkie zasoby**i na głównej stronie **magazynów usług odzyskiwania.**

## <a name="set-up-an-azure-network"></a>Konfiguracja sieci platformy Azure

Gdy maszyny wirtualne platformy Azure są tworzone na podstawie magazynu po przejściu w tryb failover, dołączają one do tej sieci.

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz kolejno pozycje **Utwórz zasób** > **Sieć** > **Sieć wirtualna**. Jako model wdrażania pozostaw wybraną pozycję Resource Manager.
2. W obszarze **Nazwa** wprowadź nazwę sieci. Nazwa musi być unikatowa w obrębie grupy zasobów platformy Azure. W tym samouczku użyj **contosoASRnet**.
3. Określ grupę zasobów, w której ma być utworzona sieć. W tym samouczku użyj istniejącej grupy zasobów **contosoRG**.
4. W **zakresie adresów**wprowadź **10.0.0.0/24** jako zakres dla sieci. Nie ma podsieci dla tej sieci.
5. W obszarze **Subskrypcja** wybierz subskrypcję, w ramach której chcesz utworzyć sieć.
6. W **pozycji Lokalizacja**wybierz Pozycję Europa **Zachodnia**. Sieć musi znajdować się w tym samym regionie co magazyn Usług odzyskiwania.
7. Pozostaw domyślne opcje podstawowej ochrony przed atakami DDoS bez punktu końcowego usługi w sieci.
8. Wybierz **pozycję Utwórz**.

![Tworzenie sieci wirtualnej](media/tutorial-prepare-azure/create-network.png)

Utworzenie sieci wirtualnej zajmuje kilka sekund. Po jego utworzeniu zobaczysz go na pulpicie nawigacyjnym portalu Azure.

## <a name="useful-links"></a>Przydatne łącza

Dowiedz się więcej:
- [Sieci platformy Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)
- [Dyski zarządzane](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)



## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Przygotowanie lokalnej infrastruktury funkcji Hyper-V do odzyskiwania po awarii na platformie Azure](hyper-v-prepare-on-premises-tutorial.md)
