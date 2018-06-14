---
title: Zarządzanie wielodostępność w usłudze Azure Site Recovery z programem Cloud Solution Provider (CSP) | Dokumentacja firmy Microsoft
description: Opisuje sposób tworzenia i zarządzania subskrypcji dzierżawcy przy użyciu dostawcy usług Kryptograficznych i wdrażania usługi Azure Site Recovery w Instalatorze wieloma dzierżawcami
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: manayar
ms.openlocfilehash: 6dc8b573e66eaae1b5cb923ae72333fb959d0969
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2018
ms.locfileid: "29768480"
---
# <a name="manage-multi-tenancy-with-the-cloud-solution-provider-csp-program"></a>Zarządzanie wielu dzierżawców z programem Cloud Solution Provider (CSP)

[Dostawcy usług Kryptograficznych programu](https://partner.microsoft.com/en-US/cloud-solution-provider) zaznajomić razem lepiej wątków dla usługi w chmurze firmy Microsoft, w tym usługi Office 365, Enterprise Mobility Suite i Microsoft Azure. Z dostawcy usług Kryptograficznych partnerzy właścicielem end-to-end relacji z klientami i stają się punkt kontaktu relacji głównej. Partnerzy można wdrożyć subskrypcji platformy Azure dla klientów i łączenie subskrypcji z własnych wartości dodanej, dostosowane oferty.

Z usługą Azure Site Recovery partnerzy zarządzać kompletne rozwiązanie odzyskiwania po awarii dla klientów bezpośrednio za pośrednictwem dostawcy usług Kryptograficznych. Lub mogą przy użyciu dostawcy usług Kryptograficznych Konfigurowanie środowisk usługi Site Recovery i umożliwić klientom zarządzanie potrzeb odzyskiwania po awarii w sposób samoobsługi. W obu przypadkach partnerzy to łączności między odzyskiwania lokacji i klientów. Partnerzy relacji z klientem usługi i obciążania klientów do użycia usługi Site Recovery.

W tym artykule opisano, jak tworzyć i zarządzać subskrypcji dzierżawcy za pośrednictwem dostawcy usług Kryptograficznych, w przypadku instalacji VMware wielodostępne partnera.

## <a name="prerequisites"></a>Wymagania wstępne

- [Przygotowanie](tutorial-prepare-azure.md) zasobów platformy Azure, w tym subskrypcji platformy Azure, sieć wirtualną platformy Azure i konto magazynu.
- [Przygotowanie](tutorial-prepare-on-premises-vmware.md) VMware lokalnych serwerów VMware i maszyn wirtualnych.
- Dla każdego dzierżawcy należy utworzyć na serwerze zarządzania oddzielne, który może komunikować się z dzierżawą maszyn wirtualnych i vCenter partnera. Tylko partner ma prawa dostępu do tego serwera. Aby uzyskać więcej informacji na temat różnych środowiskach wielodostępnych, zobacz [wielodostępne VMware](site-recovery-multi-tenant-support-vmware-using-csp.md) wskazówki.

## <a name="create-a-tenant-account"></a>Utwórz konto dzierżawy

1. Za pomocą [Microsoft Partner Center](https://partnercenter.microsoft.com/), zaloguj się do swojego konta dostawcy usług Kryptograficznych.

2. Na **pulpitu nawigacyjnego** menu, wybierz opcję **klientów**.

    ![Łącze klientów Centrum partnerskiego firmy Microsoft](./media/site-recovery-manage-multi-tenancy-with-csp/csp-dashboard-display.png)

3. Na stronie zostanie otwarty, kliknij przycisk **klienta Dodaj** przycisku.

    ![Przycisk Dodaj klienta](./media/site-recovery-manage-multi-tenancy-with-csp/add-new-customer.png)

4. Na **nowego klienta** , wypełnij szczegółowe informacje dotyczące konta dzierżawy, a następnie kliknij przycisk **dalej: subskrypcji**.

    ![Strona informacje o koncie](./media/site-recovery-manage-multi-tenancy-with-csp/customer-add-filled.png)

5. Na stronie wybierania subskrypcji wybierz **Microsoft Azure** pole wyboru. Można dodać inne subskrypcje teraz lub w dowolnym momencie.

    ![Pole wyboru subskrypcji Microsoft Azure](./media/site-recovery-manage-multi-tenancy-with-csp/azure-subscription-selection.png)

6. Na **przeglądu** Potwierdź szczegóły dzierżawy, a następnie kliknij przycisk **przesyłania**.

    ![Strona przeglądu](./media/site-recovery-manage-multi-tenancy-with-csp/customer-summary-page.png)  

    Po utworzeniu konta dzierżawy, zostanie wyświetlona strona potwierdzenia, wyświetlanie szczegółów domyślnego konta i hasła dla tej subskrypcji.

7. Zapisz informacje i Zmień hasło później w razie potrzeby za pomocą usługi Azure logowania strony portalu.  

    Te informacje można udostępniać dzierżawy, ponieważ jest lub można tworzyć i udostępniać oddzielne konto, jeśli jest to konieczne.

## <a name="access-the-tenant-account"></a>Dostęp do konta dzierżawy

Subskrypcji dzierżawcy mogą dostęp za pośrednictwem pulpitu nawigacyjnego Centrum partnerskiego firmy Microsoft, zgodnie z opisem w temacie "krok 1: utworzenie konta dzierżawy."

1. Przejdź do **klientów** strony, a następnie kliknij nazwę konta dzierżawy.

2. Na **subskrypcje** strony konta dzierżawy, można monitorować istniejące subskrypcje konta i dodawać więcej subskrypcji, zgodnie z wymaganiami. Aby zarządzać dzierżawcy operacji odzyskiwania po awarii, wybierz **wszystkie zasoby (Azure portal)**.

    ![Łącze wszystkie zasoby](./media/site-recovery-manage-multi-tenancy-with-csp/all-resources-select.png)  

    Kliknięcie przycisku **wszystkie zasoby** udziela dostępu do subskrypcji platformy Azure dzierżawcy. Dostęp można sprawdzić, klikając łącze usługi Azure Active Directory u góry po prawej z portalu Azure.

    ![Azure Active Directory link](./media/site-recovery-manage-multi-tenancy-with-csp/aad-admin-display.png)

Można teraz wykonywać wszystkie operacje usługi site recovery dla dzierżawcy za pośrednictwem portalu Azure i zarządzanie operacjami odzyskiwania po awarii. Dostęp subskrypcji dzierżawcy za pośrednictwem dostawcy usług Kryptograficznych dla odzyskiwania po awarii zarządzanych, wykonaj proces opisany wcześniej.

## <a name="deploy-resources-to-the-tenant-subscription"></a>Wdrażanie zasobów w subskrypcji dzierżawy
1. W portalu Azure Utwórz grupę zasobów, a następnie wdrożyć na zwykły proces magazynu usług odzyskiwania.

2. Pobierz klucz rejestracji magazynu.

3. Zarejestruj CS dla dzierżawcy przy użyciu klucza rejestracji magazynu.

4. Wprowadź poświadczenia dla konta dostępu dwóch: maszyny Wirtualnej i vCenter konto dostępu do uzyskania dostępu do konta.

    ![Kont serwera configuration Manager](./media/site-recovery-manage-multi-tenancy-with-csp/config-server-account-display.png)

## <a name="register-site-recovery-infrastructure-to-the-recovery-services-vault"></a>Zarejestruj infrastruktura usługi Site Recovery w magazynie usług odzyskiwania
1. W portalu Azure w magazynie, który został utworzony wcześniej, należy zarejestrować serwer vCenter do CS, który został zarejestrowany w "krok 3: Wdrażanie zasobów w subskrypcji dzierżawcy." W tym celu należy użyć konta dostępu do vCenter.
2. Zakończ proces "Przygotowanie infrastruktury" Site Recovery na zwykły proces.
3. Maszyny wirtualne są teraz gotowe do replikacji. Sprawdź, czy tylko maszyny wirtualne dzierżawcy są wyświetlane na **wybierz maszyny wirtualne** bloku w obszarze **replikować** opcji.

    ![Lista maszyn wirtualnych dzierżawcy w bloku wybierz maszyny wirtualne](./media/site-recovery-manage-multi-tenancy-with-csp/tenant-vm-display.png)

## <a name="assign-tenant-access-to-the-subscription"></a>Przypisywanie dostępu dzierżawcy do subskrypcji

Samoobsługowego odzyskiwania, podaj dzierżawcy szczegóły konta, jak wspomniano w kroku 6 procedury "krok 1: utworzenie konta dzierżawy" sekcji. Tę akcję można wykonać po partnera konfiguruje infrastruktury odzyskiwania po awarii. Czy typu odzyskiwania po awarii jest zarządzane lub samoobsługi, partnerzy muszą uzyskać dostęp do subskrypcji dzierżawcy za pośrednictwem portalu dostawcy usług Kryptograficznych. Konfigurowanie magazynu należących do partnera, a zarejestrować infrastruktury do subskrypcji dzierżawcy.

Partnerów można również dodać nowego użytkownika do subskrypcji dzierżawcy za pośrednictwem portalu dostawcy usług Kryptograficznych w następujący sposób:

1. Przejdź do strony subskrypcji CSP dzierżawcy, a następnie wybierz **użytkownicy i licencje** opcji.

    ![Stronę subskrypcji dzierżawcy dostawcy usług Kryptograficznych](./media/site-recovery-manage-multi-tenancy-with-csp/users-and-licences.png)

    Teraz można utworzyć nowego użytkownika, wprowadzając odpowiednie dane i wybierając uprawnienia lub przekazując listy użytkowników w pliku CSV.

2. Po utworzeniu nowego użytkownika, przejdź do portalu Azure, a następnie na **subskrypcji** bloku, wybierz odpowiednie subskrypcji.

3. W bloku, który zostanie otwarty, wybierz **kontroli dostępu (IAM)**, a następnie kliknij przycisk **Dodaj** można dodać użytkownika z poziomu odpowiedniego dostępu.      
    Użytkownicy, które zostały utworzone za pośrednictwem portalu dostawcy usług Kryptograficznych są automatycznie wyświetlane w bloku, który zostanie otwarty po kliknięciu poziom dostępu.

    ![Dodawanie użytkownika](./media/site-recovery-manage-multi-tenancy-with-csp/add-user-subscription.png)

    Dla większości operacji zarządzania *współautora* roli jest wystarczająca. Użytkownicy z poziomu tego dostępu może robić wszystko to w przypadku subskrypcji, z wyjątkiem zmiana poziomów dostępu (dla którego *właściciela*— wymagany jest dostęp do poziomu).

  Usługa Azure Site Recovery ma trzy [wstępnie zdefiniowanych ról użytkownika](site-recovery-role-based-linked-access-control.md) można bardziej ograniczyć poziomów dostępu zgodnie z wymaganiami.

## <a name="next-steps"></a>Kolejne kroki
  [Dowiedz się więcej](site-recovery-role-based-linked-access-control.md) o kontroli dostępu opartej na rolach do zarządzania wdrożenia usługi Azure Site Recovery.

  [Zarządzanie wieloma dzierżawcami środowiskach VMware](site-recovery-multi-tenant-support-vmware-using-csp.md)
