---
title: Konfigurowanie odzyskiwania po awarii programu VMware do platformy Azure w środowisku wielu dzierżawców przy użyciu Site Recovery i programu Cloud Solution Provider (CSP) | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania odzyskiwania po awarii programu VMware w środowisku z wieloma dzierżawami przy użyciu usługi Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: 77b64f09b7fd1429eb23c4407c729dfc0aafdf2b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60461031"
---
# <a name="set-up-vmware-disaster-recovery-in-a-multi-tenancy-environment-with-the-cloud-solution-provider-csp-program"></a>Konfigurowanie odzyskiwania po awarii programu VMware w środowisku wielu dzierżawców w ramach programu Cloud Solution Provider (CSP)

[CSP program](https://partner.microsoft.com/en-US/cloud-solution-provider) sprzyjają razem lepiej scenariusze dla usług w chmurze firmy Microsoft, w tym usługi Office 365, pakietu Enterprise Mobility Suite i Microsoft Azure. Za pomocą programu CSP partnerzy właścicielem end-to-end relacji z klientami i stają się punkt kontaktowy podstawowej relacji. Partnerów można wdrożyć subskrypcji platformy Azure dla klientów i łączyć przy użyciu własnych wartości dodanej, dostosowane oferty.

Za pomocą [usługi Azure Site Recovery](site-recovery-overview.md), jako partnerzy mogą zarządzać odzyskiwaniem po awarii dla klientów bezpośrednio w ramach programu CSP. Alternatywnie możesz skonfigurować środowiska Site Recovery za pomocą dostawcy usług Kryptograficznych i umożliwiają klientom w zarządzaniu ich potrzeb odzyskiwania po awarii w sposób samoobsługi. W obu przypadkach partnerzy są łączności między Site Recovery i ich odbiorców. Partnerzy relację z klientem usługi i są naliczane klientom użycie Site Recovery.

W tym artykule opisano sposób jako partner można tworzenia i Zarządzaj subskrypcjami dzierżawy za pośrednictwem dostawcy usług Kryptograficznych, scenariusz replikacji VMware wielodostępnych.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować replikacji oprogramowania VMware, należy wykonać następujące czynności:

- [Przygotowanie](tutorial-prepare-azure.md) zasobów platformy Azure, w tym subskrypcję platformy Azure, siecią wirtualną platformy Azure i konto magazynu.
- [Przygotowanie](vmware-azure-tutorial-prepare-on-premises.md) lokalnych serwerów VMware i maszyn wirtualnych.
- Dla każdej dzierżawy należy utworzyć na serwerze zarządzania oddzielnym, który może komunikować się z maszynami wirtualnymi dzierżawy i serwerami vCenter. Tylko jako partner należy prawa dostępu do tego serwera zarządzania. Dowiedz się więcej o [środowiskach wielodostępnych](vmware-azure-multi-tenant-overview.md).

## <a name="create-a-tenant-account"></a>Utwórz konto dzierżawy

1. Za pomocą [Microsoft Partner Center](https://partnercenter.microsoft.com/), zaloguj się do swojego konta programu CSP.
2. Na **pulpit nawigacyjny** menu, wybierz opcję **klientów**.
3. Na stronie zostanie otwarty, kliknij przycisk **Dodaj klienta** przycisku.
4. W **nowego klienta** strony, wprowadź informacje dotyczące konta dla dzierżawy.

    ![Na stronie informacje o koncie](./media/vmware-azure-multi-tenant-csp-disaster-recovery/customer-add-filled.png)

5. Następnie kliknij przycisk **dalej: Subskrypcje**.
6. Na stronie Wybór subskrypcji wybierz **Microsoft Azure** pole wyboru. Możesz dodać inne subskrypcje teraz lub w dowolnym innym czasie.
7. Na **przeglądu** strony, Potwierdź szczegóły dzierżawy, a następnie kliknij **przesyłania**.
8. Po utworzeniu konta dzierżawy, zostanie wyświetlona strona potwierdzenia, wyświetlania szczegółów domyślne konto i hasło dla tej subskrypcji. Zapisz informacje o, a następnie zmiany hasła później zgodnie z potrzebami, za pośrednictwem platformy Azure Zaloguj strony portalu.

Te informacje mogą udostępniać dzierżawy, ponieważ jest lub można tworzyć i udostępniać oddzielnego konta, jeśli to konieczne.

## <a name="access-the-tenant-account"></a>Dostęp do konta dzierżawy

Można uzyskać dostępu do subskrypcji dzierżawcy przy użyciu pulpicie nawigacyjnym Centrum partnerskiego firmy Microsoft.

1. Na **klientów** kliknij nazwę konta dzierżawy.
2. W **subskrypcje** strona konta dzierżawy, możesz monitorować istniejące subskrypcje z konta i dodawać więcej subskrypcje, zgodnie z potrzebami.
3. Aby zarządzać operacji odzyskiwania po awarii dzierżawy, wybierz pozycję **wszystkie zasoby (witryna Azure portal)** . Spowoduje to przydzielenie dostępu do subskrypcji platformy Azure dla dzierżawy.

    ![Łączy wszystkie zasoby](./media/vmware-azure-multi-tenant-csp-disaster-recovery/all-resources-select.png)  

4. Dostępu można sprawdzić, klikając link usługi Azure Active Directory w górnym rogu witryny Azure portal.

    ![Azure Active Directory link](./media/vmware-azure-multi-tenant-csp-disaster-recovery/aad-admin-display.png)

Można teraz wykonać i zarządzać operacjami usługi Site Recovery wszystkie dzierżawy w witrynie Azure portal. Dostęp do subskrypcji z dzierżawy za pośrednictwem dostawcy usług Kryptograficznych do odzyskiwania po awarii zarządzane, wykonaj proces opisany wcześniej.

## <a name="assign-tenant-access-to-the-subscription"></a>Przypisywanie dostępu dzierżawcy do subskrypcji

1. Upewnij się, że infrastruktury odzyskiwania po awarii jest skonfigurowany. Partnerzy dostęp do subskrypcji z dzierżawy za pośrednictwem portalu dostawcy usług Kryptograficznych, niezależnie od tego, czy odbywa się odzyskiwanie po awarii i samoobsługowego. Konfigurowanie magazynu i zarejestruj infrastruktury do subskrypcji dzierżawcy.
2. Podaj dzierżawcy z [utworzonego konta](#create-a-tenant-account).
3. Nowy użytkownik można dodać do subskrypcji dzierżawy za pośrednictwem portalu dostawcy usług Kryptograficznych w następujący sposób:

    ) przejdź do strony subskrypcji dostawcy CSP dzierżawcy, a następnie wybierz **użytkownikami i licencjami** opcji.

      ![Strona subskrypcji dostawcy CSP dzierżawcy](./media/vmware-azure-multi-tenant-csp-disaster-recovery/users-and-licences.png)

    (b) teraz utworzyć nowego użytkownika, wprowadzając odpowiednie szczegóły i wybierając uprawnienia lub przez przekazanie listy użytkowników w pliku CSV.
    
    c) po utworzeniu nowego użytkownika, wróć do witryny Azure portal. W **subskrypcji** wybierz odpowiednie subskrypcji.

    d) wybierz **kontrola dostępu (IAM)** , a następnie kliknij przycisk **przypisań ról**.

    (e) kliknij **Dodaj przypisanie roli** można dodać użytkownika z poziomem dostępu istotne. Użytkownicy, które zostały utworzone za pośrednictwem portalu dostawcy usług Kryptograficznych są wyświetlane na karcie przypisania roli.

      ![Dodawanie użytkownika](./media/vmware-azure-multi-tenant-csp-disaster-recovery/add-user-subscription.png)

- Większość operacji zarządzania *Współautor* roli jest wystarczająca. Użytkownicy z poziomu tego dostępu można wykonać wszystkie czynności, które w ramach subskrypcji, ale Zmień poziomy dostępu (dla której *właściciela*— poziom dostępu jest wymagany).
- Usługa Site Recovery ma również trzy [wstępnie zdefiniowane role użytkownika](site-recovery-role-based-linked-access-control.md), który może służyć do bardziej ograniczyć poziomów dostępu zgodnie z potrzebami.

## <a name="multi-tenant-environments"></a>Środowiska z wieloma dzierżawcami

Istnieją trzy główne modele wielodostępne:

* **Dostawcy usług hostingu (HSP) udostępnionych**: Partner ten jest właścicielem infrastruktury fizycznej i korzysta z zasobów udostępnionych (vCenter, centrów danych, magazynu fizycznego i tak dalej) do hostowania wielu maszyn wirtualnych dzierżawy w tej samej infrastrukturze. Partner może zapewnić zarządzanie odzyskiwania po awarii jako usługa zarządzana, lub dzierżawy mogą być właścicielami odzyskiwania po awarii jako rozwiązanie samoobsługi.

* **Dostawca usług hostingu w wersji dedykowanej**: Partner jest właścicielem infrastruktury fizycznej, ale używa dedykowanych zasobów (wiele vCenters, fizycznych magazynów danych i tak dalej) do obsługi każdego dzierżawcy maszyny wirtualne, w osobnej infrastruktury. Partner może zapewnić zarządzanie odzyskiwania po awarii jako usługa zarządzana lub dzierżawcy mogą jej właścicielem jako rozwiązanie samoobsługi.

* **Zarządzane usługi dostawcy (MSP)** : Klient jest właścicielem infrastruktury fizycznej, który jest hostem maszyny wirtualne, a partner zapewnia możliwości odzyskiwania po awarii i zarządzanie.

Konfigurując subskrypcji dzierżawy zgodnie z opisem w tym artykule, możesz szybko rozpocząć pozwala klientom w żadnej z odpowiednich modeli wielodostępnych. Dowiedz się więcej o różnych modeli z wieloma dzierżawami i lokalnych kontroli dostępu [tutaj](vmware-azure-multi-tenant-overview.md).

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [kontroli dostępu opartej na rolach](site-recovery-role-based-linked-access-control.md) Zarządzanie wdrożeniami usługi Azure Site Recovery.
- Dowiedz się więcej na temat replikacji VMware – Azure [architektura replikacji](vmware-azure-architecture.md).
- [Sprawdź samouczek](vmware-azure-tutorial.md) replikowania maszyn wirtualnych VMware na platformę Azure.
Dowiedz się więcej o [środowiskach wielodostępnych](vmware-azure-multi-tenant-overview.md) replikowania maszyn wirtualnych VMware na platformę Azure.
