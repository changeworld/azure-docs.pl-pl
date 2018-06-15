---
title: Konfigurowanie replikacji maszyn wirtualnych VMware do platformy Azure w środowisku wielu dzierżawców przy użyciu usługi Site Recovery i programem Cloud Solution Provider (CSP) | Dokumentacja firmy Microsoft
description: Opisuje sposób tworzenia i zarządzania subskrypcji dzierżawcy przy użyciu dostawcy usług Kryptograficznych i wdrażania usługi Azure Site Recovery w Instalatorze wieloma dzierżawcami
services: site-recovery
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 05/11/2018
ms.author: manayar
ms.openlocfilehash: 58ea2e7c387137f974425464ef2c9d17f438ba7c
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
ms.locfileid: "34071863"
---
# <a name="set-up-vmware-replication-in-a-multi-tenancy-environment-with-the-cloud-solution-provider-csp-program"></a>Konfigurowanie replikacji VMware w środowisku wielu dzierżawców z programem Cloud Solution Provider (CSP)

[Dostawcy usług Kryptograficznych programu](https://partner.microsoft.com/en-US/cloud-solution-provider) zaznajomić razem lepiej wątków dla usługi w chmurze firmy Microsoft, w tym usługi Office 365, Enterprise Mobility Suite i Microsoft Azure. Z dostawcy usług Kryptograficznych partnerzy właścicielem end-to-end relacji z klientami i stają się punkt kontaktu podstawowej relacji. Partnerzy mogą wdrażać subskrypcji platformy Azure dla klientów oraz łączenia subskrypcji z własnych wartości dodanej, dostosowane oferty.

Z [usługi Azure Site Recovery](site-recovery-overview.md), jako partnerów dla klientów bezpośrednio za pośrednictwem dostawcy usług Kryptograficznych można zarządzać odzyskiwaniem po awarii. Alternatywnie można użyć dostawcy usług Kryptograficznych do skonfigurowania środowisk usługi Site Recovery i poinformuj klientów, zarządzanie własnych potrzeb odzyskiwania po awarii w sposób samoobsługi. W obu przypadkach partnerzy to łączności między odzyskiwania lokacji i klientów. Partnerzy relacji z klientem usługi i obciążania klientów do użycia usługi Site Recovery.

W tym artykule opisano, jak jako partner można tworzyć i zarządzać subskrypcjami dzierżawy za pośrednictwem dostawcy usług Kryptograficznych, dla scenariusza replikacji VMware wielodostępnej.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować replikacji maszyn wirtualnych VMware, należy wykonać następujące czynności:

- [Przygotowanie](tutorial-prepare-azure.md) zasobów platformy Azure, w tym subskrypcji platformy Azure, sieć wirtualną platformy Azure i konto magazynu.
- [Przygotowanie](vmware-azure-tutorial-prepare-on-premises.md) lokalnych serwerów VMware i maszyn wirtualnych.
- Dla każdego dzierżawcy Utwórz oddzielne management server, który może komunikować się z dzierżawcą maszyn wirtualnych i serwery vCenter. Tylko jako partner powinien masz prawa dostępu do tego serwera zarządzania. Dowiedz się więcej o [środowiskach wielodostępnych](vmware-azure-multi-tenant-overview.md).

## <a name="create-a-tenant-account"></a>Utwórz konto dzierżawy

1. Za pomocą [Microsoft Partner Center](https://partnercenter.microsoft.com/), zaloguj się do swojego konta dostawcy usług Kryptograficznych.
2. Na **pulpitu nawigacyjnego** menu, wybierz opcję **klientów**.
3. Na stronie zostanie otwarty, kliknij przycisk **klienta Dodaj** przycisku.
4. W **nowego klienta** strony, wypełnienie szczegółowe informacje dotyczące konta dla dzierżawcy.

    ![Strona informacje o koncie](./media/vmware-azure-multi-tenant-csp-disaster-recovery/customer-add-filled.png)

5. Następnie kliknij przycisk **dalej: subskrypcji**.
6. Na stronie wybierania subskrypcji wybierz **Microsoft Azure** pole wyboru. Można dodać inne subskrypcje teraz lub w dowolnym momencie.
7. Na **przeglądu** Potwierdź szczegóły dzierżawy, a następnie kliknij przycisk **przesyłania**.
8. Po utworzeniu konta dzierżawy, zostanie wyświetlona strona potwierdzenia, wyświetlanie szczegółów domyślnego konta i hasła dla tej subskrypcji. Zapisz informacje i Zmień hasło później w razie potrzeby, za pomocą usługi Azure logowania strony portalu.

Te informacje można udostępniać dzierżawy, ponieważ jest lub można tworzyć i udostępniać oddzielne konto, jeśli jest to konieczne.

## <a name="access-the-tenant-account"></a>Dostęp do konta dzierżawy

Można uzyskać dostępu do subskrypcji dzierżawcy za pośrednictwem pulpicie nawigacyjnym Centrum partnerskiego firmy Microsoft.

1. Na **klientów** kliknij nazwę konta dzierżawy.
2. W **subskrypcje** strony konta dzierżawy, można monitorować istniejące subskrypcje konta i dodawać więcej subskrypcji, zgodnie z wymaganiami.
3. Aby zarządzać dzierżawcy operacji odzyskiwania po awarii, wybierz **wszystkie zasoby (Azure portal)**. Spowoduje to przydzielenie dostępu do subskrypcji platformy Azure dzierżawcy.

    ![Łącze wszystkie zasoby](./media/vmware-azure-multi-tenant-csp-disaster-recovery/all-resources-select.png)  

4. Można sprawdzić dostępu, klikając łącze usługi Azure Active Directory na górnym rogu portalu Azure.

    ![Azure Active Directory link](./media/vmware-azure-multi-tenant-csp-disaster-recovery/aad-admin-display.png)

Można teraz wykonywać i zarządzania wszystkimi operacjami usługi Site Recovery dla dzierżawy w portalu Azure. Dostęp subskrypcji dzierżawcy za pośrednictwem dostawcy usług Kryptograficznych dla odzyskiwania po awarii zarządzanych, wykonaj proces opisany wcześniej.

## <a name="assign-tenant-access-to-the-subscription"></a>Przypisywanie dostępu dzierżawcy do subskrypcji

1. Upewnij się, że infrastruktura odzyskiwania po awarii jest skonfigurowany. Partnerom dostęp do subskrypcji dzierżawcy za pośrednictwem portalu dostawcy usług Kryptograficznych, niezależnie od tego, czy jest zarządzany odzyskiwania po awarii lub samoobsługi. Konfigurowanie magazynu i zarejestruj infrastruktury do subskrypcji dzierżawcy.
2. Podaj dzierżawcy z [utworzonego konta](#create-a-tenant-account).
3. Nowego użytkownika można dodać do subskrypcji dzierżawcy za pośrednictwem portalu dostawcy usług Kryptograficznych w następujący sposób:

    ) przejdź do strony subskrypcji CSP dzierżawcy, a następnie wybierz **użytkownicy i licencje** opcji.

      ![Stronę subskrypcji dzierżawcy dostawcy usług Kryptograficznych](./media/vmware-azure-multi-tenant-csp-disaster-recovery/users-and-licences.png)

      (b) teraz utworzyć nowego użytkownika, wprowadzając odpowiednie dane i wybierając uprawnienia lub przekazując listy użytkowników w pliku CSV.
    c) po utworzeniu nowego użytkownika, wróć do portalu Azure. W **subskrypcji** wybierz odpowiednie subskrypcji.
    d) wybierz **kontroli dostępu (IAM)**, a następnie kliknij przycisk **Dodaj**, aby dodać użytkownika z poziomu odpowiedniego dostępu. Użytkownicy, które zostały utworzone za pośrednictwem portalu dostawcy usług Kryptograficznych są automatycznie wyświetlane na stronie zostanie otwarty po kliknięciu poziom dostępu.

      ![Dodawanie użytkownika](./media/vmware-azure-multi-tenant-csp-disaster-recovery/add-user-subscription.png)

- Dla większości operacji zarządzania *współautora* roli jest wystarczająca. Użytkownicy z poziomu tego dostępu może robić wszystko to w przypadku subskrypcji, z wyjątkiem zmiana poziomów dostępu (dla którego *właściciela*— wymagany jest dostęp do poziomu).
- Usługa Site Recovery ma trzy [wstępnie zdefiniowane role użytkowników](site-recovery-role-based-linked-access-control.md), można bardziej ograniczyć poziomów dostępu zgodnie z wymaganiami.

## <a name="multi-tenant-environments"></a>Środowiska z wieloma dzierżawcami

Istnieją trzy główne modele wielodostępne:

* **Hosting usług dostawcy (HSP) udostępnionych**: partnera jest właścicielem infrastruktury fizycznej i korzysta z zasobów udostępnionych (vCenter, centrów danych magazynu fizycznego i tak dalej) do obsługi wielu dzierżawców maszyny wirtualne na tej samej infrastrukturze. Partner zapewniają odzyskiwania po awarii management jako usługa zarządzana lub dzierżawcy mogą być właścicielami odzyskiwania po awarii, jako rozwiązanie samoobsługi.

* **Dostawcy usług hostingu w wersji dedykowanej**: partnera jest właścicielem infrastruktury fizycznej, ale używa zasobów dedykowanych (wiele Vcenter, datastores fizycznych i tak dalej) do obsługi maszyn wirtualnych każdego dzierżawcy w osobnej infrastruktury. Partner zapewniają odzyskiwania po awarii management jako usługa zarządzana lub dzierżawcy mogą należeć go jako rozwiązaniem samoobsługi.

* **Zarządzane usługi dostawcy (MSP)**: klient jest właścicielem infrastruktury fizycznej, który jest hostem maszyny wirtualnej, a partnera umożliwia włączenie odzyskiwania po awarii i zarządzania.

Konfigurując subskrypcji dzierżawy zgodnie z opisem w tym artykule, można szybko uruchomić Włączanie klientów w żadnej z odpowiednich modeli wielodostępnej. Dowiedz się więcej o różne modele z wieloma dzierżawcami i włączenie lokalnymi kontroli dostępu [tutaj](vmware-azure-multi-tenant-overview.md).

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [kontroli dostępu opartej na rolach](site-recovery-role-based-linked-access-control.md) Zarządzanie wdrożeniami usługi Azure Site Recovery.
- Dowiedz się więcej o VMware do platformy Azure [architektura replikacji](vmware-azure-architecture.md).
- [Przejrzyj samouczek](vmware-azure-tutorial.md) replikowania maszyn wirtualnych VMware do platformy Azure.
Dowiedz się więcej o [środowiskach wielodostępnych](vmware-azure-multi-tenant-overview.md) replikowania maszyn wirtualnych VMware do platformy Azure.
