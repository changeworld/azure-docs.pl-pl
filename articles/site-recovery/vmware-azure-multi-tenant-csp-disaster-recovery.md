---
title: Konfigurowanie odzyskiwania po awarii VMware na platformie Azure w środowisku wielonajemskim przy użyciu usługi Site Recovery i programu Dostawcy rozwiązań w chmurze (CSP) | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób konfigurowania odzyskiwania po awarii VMware w środowisku wielodostępnym za pomocą usługi Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: 77b64f09b7fd1429eb23c4407c729dfc0aafdf2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60461031"
---
# <a name="set-up-vmware-disaster-recovery-in-a-multi-tenancy-environment-with-the-cloud-solution-provider-csp-program"></a>Konfigurowanie odzyskiwania po awarii VMware w środowisku wielonajemowym za pomocą programu Dostawcy rozwiązań w chmurze (CSP)

[Program dostawcy usług kryptograficznych](https://partner.microsoft.com/en-US/cloud-solution-provider) promuje lepsze relacje dla usług w chmurze firmy Microsoft, w tym usługi Office 365, pakietu Enterprise Mobility Suite i platformy Microsoft Azure. Dzięki CSP partnerzy są właścicielami relacji end-to-end z klientami i stają się głównym punktem kontaktowym relacji. Partnerzy mogą wdrażać subskrypcje platformy Azure dla klientów i łączyć subskrypcje z ich własną ofertą o wartości dodanej, dostosowaną do indywidualnych potrzeb.

Dzięki [usłudze Azure Site Recovery](site-recovery-overview.md), jako partnerom, możesz zarządzać odzyskiwaniem po awarii dla klientów bezpośrednio za pośrednictwem usługi CSP. Alternatywnie można użyć dostawcy usług kryptograficznych do konfigurowania środowisk odzyskiwania witryny i umożliwienia klientom zarządzania własnymi potrzebami odzyskiwania po awarii w sposób samoobsługowy. W obu scenariuszach partnerzy są łącznikiem między odzyskiem witryny a ich klientami. Partnerzy obsługują relację z klientami i rozliczają klientów za użycie usługi Site Recovery.

W tym artykule opisano, jak jako partner można tworzyć i zarządzać subskrypcjami dzierżawy za pośrednictwem usługi CSP, dla scenariusza replikacji VMware z wieloma dzierżawami.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować replikację VMware, należy wykonać następujące czynności:

- [Przygotuj się](tutorial-prepare-azure.md) Zasoby platformy Azure, w tym subskrypcja platformy Azure, sieć wirtualna platformy Azure i konto magazynu.
- [Przygotowywanie](vmware-azure-tutorial-prepare-on-premises.md) lokalnych serwerów VMware i maszyn wirtualnych.
- Dla każdej dzierżawy należy utworzyć oddzielny serwer zarządzania, który może komunikować się z maszynami wirtualnymi dzierżawcy i serwerami vCenter. Tylko Ty jako partner powinieneś mieć prawa dostępu do tego serwera zarządzania. Dowiedz się więcej o [środowiskach wielodostępnych](vmware-azure-multi-tenant-overview.md).

## <a name="create-a-tenant-account"></a>Tworzenie konta dzierżawy

1. Za pośrednictwem [Centrum partnerów firmy Microsoft](https://partnercenter.microsoft.com/)zaloguj się do swojego konta CSP.
2. W menu **Pulpit nawigacyjny** wybierz polecenie **Klienci**.
3. Na otwartej stronie kliknij przycisk **Dodaj klienta.**
4. Na stronie **Nowy klient** wprowadź szczegóły informacji o koncie dla dzierżawy.

    ![Strona Informacje o koncie](./media/vmware-azure-multi-tenant-csp-disaster-recovery/customer-add-filled.png)

5. Następnie kliknij **przycisk Dalej: Subskrypcje**.
6. Na stronie wyboru subskrypcji zaznacz pole wyboru **Microsoft Azure.** Możesz dodać inne subskrypcje teraz lub w dowolnym innym czasie.
7. Na stronie **Recenzja** potwierdź szczegóły dzierżawy, a następnie kliknij przycisk **Prześlij**.
8. Po utworzeniu konta dzierżawy zostanie wyświetlona strona potwierdzenia z informacjami o koncie domyślnym i haśle dla tej subskrypcji. Zapisz informacje i zmień hasło później w razie potrzeby za pośrednictwem strony logowania w portalu Azure.

Można udostępnić te informacje dzierżawie w stanie takim, w jakim jest, lub w razie potrzeby utworzyć i udostępnić oddzielne konto.

## <a name="access-the-tenant-account"></a>Uzyskiwanie dostępu do konta dzierżawy

Dostęp do subskrypcji dzierżawy można uzyskać za pośrednictwem pulpitu nawigacyjnego Centrum partnerów firmy Microsoft.

1. Na stronie **Klienci** kliknij nazwę konta dzierżawy.
2. Na stronie **Subskrypcje** konta dzierżawy można monitorować istniejące subskrypcje kont i w razie potrzeby dodać więcej subskrypcji.
3. Aby zarządzać operacjami odzyskiwania po awarii dzierżawcy, wybierz pozycję **Wszystkie zasoby (Azure portal).** Daje to dostęp do subskrypcji platformy Azure dzierżawy.

    ![Łącze Wszystkie zasoby](./media/vmware-azure-multi-tenant-csp-disaster-recovery/all-resources-select.png)  

4. Dostęp można zweryfikować, klikając łącze usługi Azure Active Directory w prawym górnym rogu witryny Azure portal.

    ![Łącze usługi Azure Active Directory](./media/vmware-azure-multi-tenant-csp-disaster-recovery/aad-admin-display.png)

Teraz można wykonywać i zarządzać wszystkimi operacjami odzyskiwania witryny dla dzierżawy w witrynie Azure portal. Aby uzyskać dostęp do subskrypcji dzierżawy za pośrednictwem dostawcy usług kryptograficznych dla zarządzanego odzyskiwania po awarii, postępuj zgodnie z wcześniej opisanym procesem.

## <a name="assign-tenant-access-to-the-subscription"></a>Przypisywanie dostępu dzierżawcy do subskrypcji

1. Upewnij się, że skonfigurowana jest infrastruktura odzyskiwania po awarii. Partnerzy uzyskują dostęp do subskrypcji dzierżawy za pośrednictwem portalu dostawcy usług kryptograficznych, niezależnie od tego, czy odzyskiwanie po awarii jest zarządzane, czy samoobsługowe. Skonfiguruj infrastrukturę magazynu i zarejestruj subskrypcje dzierżawy.
2. Podaj dzierżawce [utworzone konto](#create-a-tenant-account).
3. Możesz dodać nowego użytkownika do subskrypcji dzierżawy za pośrednictwem portalu CSP w następujący sposób:

    a) Przejdź do strony subskrypcji CSP dzierżawcy, a następnie wybierz opcję **Użytkownicy i licencje.**

      ![Strona subskrypcji usługi CSP dzierżawcy](./media/vmware-azure-multi-tenant-csp-disaster-recovery/users-and-licences.png)

    b) Teraz utwórz nowego użytkownika, wprowadzając odpowiednie szczegóły i wybierając uprawnienia lub przesyłając listę użytkowników w pliku CSV.
    
    c) Po utworzeniu nowego użytkownika wróć do witryny Azure portal. Na stronie **Subskrypcja** wybierz odpowiednią subskrypcję.

    d) Wybierz **kontrolę dostępu (IAM),** a następnie kliknij pozycję **Przypisania ról**.

    e) Kliknij pozycję **Dodaj przypisanie roli,** aby dodać użytkownika z odpowiednim poziomem dostępu. Użytkownicy, którzy zostali utworzeni za pośrednictwem portalu CSP są wyświetlane na karcie Przypisania ról.

      ![Dodawanie użytkownika](./media/vmware-azure-multi-tenant-csp-disaster-recovery/add-user-subscription.png)

- W przypadku większości operacji zarządzania rola *współautora* jest wystarczająca. Użytkownicy z tym poziomem dostępu mogą wykonywać wszystkie usługi w ramach subskrypcji, z wyjątkiem zmiany poziomów dostępu (dla których wymagany jest dostęp na poziomie *właściciela).*
- Odzyskiwanie witryny ma również trzy [wstępnie zdefiniowane role użytkowników,](site-recovery-role-based-linked-access-control.md)które mogą służyć do dalszego ograniczania poziomów dostępu zgodnie z wymaganiami.

## <a name="multi-tenant-environments"></a>Środowiska wielodostępne

Istnieją trzy główne modele wielodostępne:

* **Dostawca usług hostingu współdzielonego (HSP)**: Partner jest właścicielem infrastruktury fizycznej i używa zasobów udostępnionych (vCenter, centrów danych, magazynu fizycznego itd.) do hostowania wielu maszyn wirtualnych dzierżawy w tej samej infrastrukturze. Partner może zapewnić zarządzanie odzyskiwaniem po awarii jako usługę zarządzaną lub dzierżawca może być właścicielem odzyskiwania po awarii jako samoobsługowego rozwiązania.

* **Dedykowany dostawca usług hostingowych:** partner jest właścicielem infrastruktury fizycznej, ale używa dedykowanych zasobów (wiele centrów wirtualnych, fizycznych magazynów danych i tak dalej) do hostowania maszyn wirtualnych każdej dzierżawy w oddzielnej infrastrukturze. Partner może zapewnić zarządzanie odzyskiwaniem po awarii jako usługę zarządzaną lub dzierżawca może być właścicielem go jako samoobsługowego rozwiązania.

* **Dostawca usług zarządzanych (MSP)**: Klient jest właścicielem infrastruktury fizycznej, która obsługuje maszyny wirtualne, a partner zapewnia włączenie odzyskiwania po awarii i zarządzania.

Konfigurując subskrypcje dzierżawy zgodnie z opisem w tym artykule, można szybko uruchomić włączanie klientów w dowolnym z odpowiednich modeli wielodostępnych. Więcej informacji na temat różnych modeli wielodostępnych i włączania lokalnych kontroli dostępu można [znaleźć tutaj.](vmware-azure-multi-tenant-overview.md)

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [kontroli dostępu opartej na rolach](site-recovery-role-based-linked-access-control.md) w celu zarządzania wdrożeniami usługi Azure Site Recovery.
- Dowiedz się więcej o [architekturze replikacji](vmware-azure-architecture.md)VMware na platformie Azure.
- [Przejrzyj samouczek replikowania](vmware-azure-tutorial.md) maszyn wirtualnych VMware na platformie Azure.
Dowiedz się więcej o [środowiskach wielodostępnych](vmware-azure-multi-tenant-overview.md) do replikowania maszyn wirtualnych VMware na platformie Azure.
