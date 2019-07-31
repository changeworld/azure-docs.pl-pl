---
title: Przygotuj maszyny wirtualne VMware do oceny i migracji na platformę Azure za pomocą Azure Migrate | Microsoft Docs
description: Opisuje sposób przygotowania do oceny i migracji lokalnych maszyn wirtualnych VMware na platformę Azure przy użyciu Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 993b0249bdcc8e0e54dbe0c222aa2335c3bf8d4a
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679140"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>Przygotowywanie maszyn wirtualnych VMware na potrzeby oceny i migracji na platformę Azure

W tym artykule opisano, jak przygotować się do oceny i migracji lokalnych maszyn wirtualnych VMware na platformę Azure przy użyciu [Azure Migrate](migrate-services-overview.md).

[Azure Migrate](migrate-overview.md) udostępnia centrum narzędzi, które ułatwiają odnajdywanie, ocenianie i Migrowanie aplikacji, infrastruktury i obciążeń do Microsoft Azure. Centrum obejmuje narzędzia Azure Migrate i oferty niezależnych dostawców oprogramowania (ISV) innych firm. 


Ten samouczek jest pierwszą częścią serii, która pokazuje, jak oceniać i migrować maszyny wirtualne VMware. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przygotuj platformę Azure. Skonfiguruj uprawnienia dla konta i zasobów platformy Azure do pracy z Azure Migrate.
> * Przygotuj lokalne serwery i maszyny wirtualne VMware na potrzeby oceny maszyn wirtualnych.
> * Przygotuj lokalne serwery i maszyny wirtualne VMware na potrzeby migracji maszyn wirtualnych.

> [!NOTE]
> Samouczki przedstawiają najprostszą ścieżkę wdrożenia dla scenariusza, dzięki czemu można szybko skonfigurować weryfikację koncepcji. Samouczki korzystają z domyślnych opcji, jeśli jest to możliwe, i nie wyświetlają wszystkich możliwych ustawień i ścieżek. Aby uzyskać szczegółowe instrukcje, zapoznaj się ze sposobem oceny i migracji maszyn wirtualnych.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).


## <a name="prepare-azure"></a>Przygotowywanie platformy Azure

Wymagane są następujące uprawnienia platformy Azure:

- Twoje konto platformy Azure wymaga uprawnień do utworzenia projektu Azure Migrate na potrzeby oceny i migracji. 
- W przypadku oceny i migracji bez agentów maszyn wirtualnych programu VMware Azure Migrate uruchamia lekkie urządzenie, które odnajduje maszyny wirtualne i wysyła metadane maszyny wirtualnej i dane wydajności do Azure Migrate. Na platformie Azure musisz mieć uprawnienia do zarejestrowania urządzenia Azure Migrate.
- Aby przeprowadzić migrację maszyn wirtualnych VMware przy użyciu migracji serwera Azure Migrate, Azure Migrate tworzy Key Vault w grupie zasobów, aby zarządzać kluczami dostępu do konta magazynu replikacji w ramach subskrypcji. Aby utworzyć magazyn, musisz mieć uprawnienia do przypisywania ról w grupie zasobów, w której znajduje się projekt Azure Migrate. 


### <a name="assign-permissions-to-create-project"></a>Przypisywanie uprawnień do tworzenia projektu

1. W Azure Portal Otwórz subskrypcję i wybierz pozycję **Kontrola dostępu (IAM)** .
2. W obszarze **Sprawdź dostęp**Znajdź odpowiednie konto i kliknij je, aby wyświetlić uprawnienia.
3. Należy mieć uprawnienia współautora lub **właściciela** .
    - Jeśli właśnie utworzono bezpłatne konto platformy Azure, jesteś właścicielem subskrypcji.
    - Jeśli nie jesteś właścicielem subskrypcji, Pracuj z właścicielem, aby przypisać rolę.

### <a name="assign-permissions-to-register-the-appliance"></a>Przypisywanie uprawnień do zarejestrowania urządzenia

Jeśli wdrażasz urządzenie Azure Migrate, aby ocenić lub uruchomić migrację maszyn wirtualnych bez agenta, należy je zarejestrować.

- Podczas rejestracji urządzenia Azure Migrate tworzy dwie aplikacje Azure Active Directory (Azure AD), które w unikatowy sposób identyfikują urządzenie
    - Pierwsza aplikacja komunikuje się z punktami końcowymi usługi Azure Migrate.
    - Druga aplikacja uzyskuje dostęp do Azure Key Vault utworzonego podczas rejestracji w celu przechowywania informacji o aplikacji i konfiguracji urządzenia usługi Azure AD.
- Możesz przypisać uprawnienia do Azure Migrate, aby utworzyć te aplikacje usługi Azure AD przy użyciu jednej z następujących metod:
    - Dzierżawa/Administrator globalny może udzielić uprawnień użytkownikom w dzierżawie, aby tworzyć i rejestrować aplikacje usługi Azure AD.
    - Dzierżawa/Administrator globalny może przypisać rolę dewelopera aplikacji (z uprawnieniami) do konta.

Warto zauważyć, że:

- Aplikacje nie mają żadnych innych uprawnień dostępu do subskrypcji innych niż opisane powyżej.
- Te uprawnienia są potrzebne tylko podczas rejestrowania nowego urządzenia. Po skonfigurowaniu urządzenia można usunąć uprawnienia. 


#### <a name="grant-account-permissions"></a>Udziel uprawnień konta

Dzierżawy/Administrator globalny mogą udzielić uprawnień w następujący sposób

1. W usłudze Azure AD dzierżawca/Administrator globalny powinien przejść do**ustawień użytkownika** **Azure Active Directory** > **Użytkownicy** > .
2. Administrator powinien ustawić **rejestracje aplikacji** na **wartość tak**.

    ![Uprawnienia usługi Azure AD](./media/tutorial-prepare-vmware/aad.png)

> [!NOTE]
> Jest to ustawienie domyślne, które nie jest poufne. [Dowiedz się więcej](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).



#### <a name="assign-application-developer-role"></a>Przypisywanie roli Deweloper aplikacji 

Dzierżawa/Administrator globalny może przypisać rolę dewelopera aplikacji do konta. [Dowiedz się więcej](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).

## <a name="assign-role-assignment-permissions"></a>Przypisywanie uprawnień do przypisywania ról

Przypisz uprawnienia do przypisywania ról do grupy zasobów, w której znajduje się projekt Azure Migrate, w następujący sposób:

1. W grupie zasobów w Azure Portal wybierz pozycję **Kontrola dostępu (IAM)** .
2. W obszarze **Sprawdź dostęp**Znajdź odpowiednie konto i kliknij je, aby wyświetlić uprawnienia.

    - Aby uruchomić ocenę serwera, uprawnienia współautora są wystarczające.
    - Aby uruchomić migrację serwera bez agenta, należy mieć uprawnienia **właściciela** ( lub współautora i **administrator dostępu użytkowników**).

3. Jeśli nie masz wymaganych uprawnień, poproś ich od właściciela grupy zasobów. 



## <a name="prepare-for-vmware-vm-assessment"></a>Przygotowanie do oceny maszyny wirtualnej VMware

Aby przygotować się do oceny maszyny wirtualnej VMware, należy zweryfikować ustawienia hosta i maszyny wirtualnej VMware oraz sprawdzić ustawienia wdrażania urządzeń.

### <a name="verify-vmware-settings"></a>Sprawdź ustawienia programu VMware

1. [Sprawdź](migrate-support-matrix-vmware.md#assessment-vcenter-server-requirements) Wymagania dotyczące oceny maszyn wirtualnych dotyczące serwera VMware.
2. [Upewnij](migrate-support-matrix-vmware.md#assessment-port-requirements) się, że na serwerach vCenter są otwarte wymagane porty.


### <a name="set-up-an-account-for-assessment"></a>Skonfiguruj konto do oceny

Azure Migrate musi uzyskać dostęp do vCenter Server w celu odnalezienia maszyn wirtualnych na potrzeby oceny i migracji bez wykorzystania agentów. Tylko do oceny potrzebne jest konto tylko do odczytu dla vCenter Server.

Jeśli używasz serwera proxy opartego na adresie URL, Zezwól na dostęp do wymaganych [adresów URL platformy Azure](migrate-support-matrix-vmware.md#assessment-url-access-requirements).

Upewnij się, że serwer proxy rozpoznaje wszystkie rekordy CNAME otrzymane podczas wyszukiwania adresów URL.


### <a name="verify-appliance-settings-for-assessment"></a>Weryfikuj ustawienia urządzenia do oceny

Przed skonfigurowaniem urządzenia Azure Migrate i rozpoczęciem oceny w następnym samouczku Przygotuj się do wdrożenia urządzenia.

1. Sprawdź [wymagania dotyczące](migrate-support-matrix-vmware.md#assessment-appliance-requirements) konfigurowania urządzenia Azure Migrate w oprogramowaniu VMware.
2. [Przejrzyj](migrate-support-matrix-vmware.md#assessment-url-access-requirements) adresy URL platformy Azure, do których urządzenie będzie potrzebować dostępu.
3. Przejrzyj dane, które urządzenie będzie zbierać podczas odnajdywania i oceny.
4. [Zwróć uwagę](migrate-support-matrix-vmware.md#assessment-port-requirements) na wymagania dotyczące dostępu do portów dla urządzenia.
5. Urządzenie Azure Migrate można wdrożyć jako maszynę wirtualną VMware przy użyciu pliku komórki jajowe. Na vCenter Server upewnij się, że konto ma uprawnienia do tworzenia maszyny wirtualnej przy użyciu pliku komórek jajowych.


## <a name="prepare-for-agentless-vmware-migration"></a>Przygotowanie do migracji oprogramowania VMware bez agentów

Zapoznaj się z wymaganiami dotyczącymi migracji bez agentów maszyn wirtualnych VMware.

1. [Przegląd](migrate-support-matrix-vmware.md#agentless-migration-vmware-server-requirements) Wymagania dotyczące serwera VMware dotyczące migracji bez wykorzystania agentów.
2. Skonfiguruj konto, aby uzyskać dostęp do vCenter Server z [uprawnieniami wymaganymi](migrate-support-matrix-vmware.md#agentless-migration-vcenter-server-permissions) do migracji bez wykorzystania agentów.
3. [Zanotuj](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements) wymagania dotyczące maszyn wirtualnych VMware, które chcesz migrować do platformy Azure za pomocą migracji bez wykorzystania agentów.
4. [Zapoznaj](migrate-support-matrix-vmware.md#agentless-migration-appliance-requirements) się z wymaganiami dotyczącymi urządzeń dla migracji bez wykorzystania agentów.]
5. Zwróć uwagę na wymagania dostępu do [adresu URL](migrate-support-matrix-vmware.md#agentless-migration-url-access-requirements) urządzenia i [dostępu do portów](migrate-support-matrix-vmware.md#agentless-migration-port-requirements) dla migracji bez wykorzystania agentów.


## <a name="prepare-for-agent-based-vmware-migration"></a>Przygotowanie do migracji oprogramowania VMware opartego na agencie

Zapoznaj się z wymaganiami dotyczącymi migracji maszyn wirtualnych VMware na podstawie agenta.

1. [Przegląd](migrate-support-matrix-vmware.md#agent-based-migration-vmware-server-requirements) Wymagania dotyczące serwera VMware dotyczące migracji bez wykorzystania agentów. 
2. Skonfiguruj konto, aby uzyskać dostęp do vCenter Server z [uprawnieniami wymaganymi](migrate-support-matrix-vmware.md#agent-based-migration-vcenter-server-permissions) do migracji bez wykorzystania agentów.
3. [Zanotuj](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements) wymagania dotyczące maszyn wirtualnych VMware, które chcesz migrować na platformę Azure przy użyciu migracji opartej na agencie, w tym instalacji usługi mobilności na każdej maszynie wirtualnej, która ma zostać poddana migracji.
4. Zwróć uwagę na [dostęp do adresu URL](migrate-support-matrix-vmware.md#agent-based-migration-url-access-requirements).
5. Przejrzyj wymagania dotyczące [dostępu do portów](migrate-support-matrix-vmware.md#agent-based-migration-port-requirements) dla usługi mobilności uruchomionej na każdej maszynie wirtualnej oraz dla serwera konfiguracji Azure Migrate.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku zostaną wykonane następujące czynności:
 
> [!div class="checklist"] 
> * Skonfiguruj uprawnienia platformy Azure.
> * Przygotowano oprogramowanie VMware do oceny i migracji.


Przejdź do drugiego samouczka, aby skonfigurować projekt Azure Migrate i ocenić maszyny wirtualne VMware pod kątem migracji na platformę Azure.

> [!div class="nextstepaction"] 
> [Ocenianie maszyn wirtualnych VMware](./tutorial-migrate-vmware.md) 

