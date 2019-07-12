---
title: Przygotowywanie maszyn wirtualnych VMware na potrzeby oceny i migracji na platformę Azure za pomocą usługi Azure Migrate | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób przygotowania do oceny i migracji lokalnych maszyn wirtualnych programu VMware do platformy Azure przy użyciu usługi Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 10f559295ff0598dea26fb30b089f020e2985889
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840342"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>Przygotowywanie maszyn wirtualnych VMware na potrzeby oceny i migracji na platformę Azure

W tym artykule opisano sposób przygotowania do oceny i migracji lokalnych maszyn wirtualnych programu VMware na platformę Azure przy użyciu [usługi Azure Migrate](migrate-services-overview.md).

[Usługa Azure Migrate](migrate-overview.md) udostępnia Centrum narzędzi, które ułatwiają odnajdowanie, oceny i migracji aplikacji, infrastruktury i obciążeń w systemie Microsoft Azure. Centrum obejmuje usługę Azure Migrate narzędzi i ofert Niezależnym dostawcą oprogramowania niezależnie od innych firm. 


Niniejszy samouczek jest pierwszą serii, który pokazuje, jak ocenić i przeprowadzić migrację maszyn wirtualnych VMware. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przygotowywanie platformy Azure. Skonfiguruj uprawnienia dla konta platformy Azure i zasoby do pracy z usługi Azure Migrate.
> * Przygotowywanie lokalnych serwerów VMware i maszyny wirtualne oceny maszyn wirtualnych.
> * Przygotuj lokalne serwery VMware i maszyny wirtualne do migracji maszyny Wirtualnej.

> [!NOTE]
> W samouczkach pokazano to najprostsza ścieżka wdrażania dla scenariusza, aby szybko skonfigurować się weryfikacji koncepcji. Samouczki użyj opcji domyślnych, jeśli jest to możliwe, a nie pokazuj wszystkich możliwych ustawień i ścieżki. Aby uzyskać szczegółowe instrukcje Przejrzyj porady dla oceny programu VMware i migracji.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).


## <a name="prepare-azure"></a>Przygotowywanie platformy Azure

Potrzebne są następujące uprawnienia platformy Azure:

- Twoje konto platformy Azure wymaga uprawnień, aby utworzyć projekt usługi Azure Migrate na potrzeby oceny i migracji. 
- Na potrzeby oceny i migracji bez wykorzystania agentów maszyn wirtualnych programu VMware usługa Azure Migrate uruchamia uproszczone urządzenia, który umożliwia odnalezienie maszyn wirtualnych i wysyła dane wydajności i metadanych maszyny Wirtualnej do usługi Azure Migrate. Na platformie Azure, musisz mieć uprawnienia do zarejestrowania urządzenia w usłudze Azure Migrate.
- Aby przeprowadzić migrację maszyn wirtualnych VMware przy użyciu migracji serwera migracji platformy Azure, usługa Azure Migrate tworzy usługę Key Vault w grupie zasobów, aby zarządzać kluczami dostępu do konta magazynu replikacji w ramach subskrypcji. Aby utworzyć magazyn, potrzebujesz uprawnienia do przypisywania ról w grupie zasobów, w której znajduje się projekt usługi Azure Migrate. 


### <a name="assign-permissions-to-create-project"></a>Przypisywanie uprawnień do tworzenia projektu

1. W witrynie Azure portal Otwórz subskrypcję, a następnie wybierz **kontrola dostępu (IAM)** .
2. W **Sprawdź dostęp**, Znajdź odpowiednie konto i kliknij go, aby wyświetlić uprawnienia.
3. Musisz mieć **Współautor** lub **właściciela** uprawnienia.
    - Jeśli bezpłatne konto platformy Azure jest właśnie utworzone, jesteś właścicielem subskrypcji.
    - Jeśli nie jesteś właścicielem subskrypcji, Współpracuj z właścicielem Aby przypisać rolę.

### <a name="assign-permissions-to-register-the-appliance"></a>Przypisywanie uprawnień do rejestracji urządzenia

Jeśli wdrażasz usługi Azure Migrate urządzenia na potrzeby oceny lub uruchomić migrację bez wykorzystania agentów maszyn wirtualnych, należy go zarejestrować.

- Podczas rejestracji urządzenia w usłudze Azure Migrate tworzy dwie aplikacje usługi Azure Active Directory (Azure AD), które jednoznacznie identyfikują urządzenie
    - Pierwszy aplikacja komunikuje się z punktami końcowymi usługi Azure Migrate.
    - Druga aplikacja uzyskuje dostęp do usługi Azure Key Vault utworzone podczas rejestracji do przechowywania usługi Azure AD informacje i urządzenia w ustawieniach konfiguracji aplikacji.
- Możesz przypisywać uprawnienia dla usługi Azure Migrate można tworzyć te aplikacje usługi Azure AD przy użyciu jednej z następujących metod:
    - Administrator globalny/dzierżawy mogą udzielać uprawnień użytkowników w dzierżawie, aby utworzyć i zarejestrować aplikacje usługi Azure AD.
    - Administrator dzierżawy/globalny przypisać rolę dewelopera aplikacji (który ma uprawnienia) do konta.

Warto zauważyć, że:

- Aplikacje nie mają żadnych uprawnień dostępu do subskrypcji innych niż opisane powyżej.
- Wystarczy tylko te uprawnienia podczas rejestrowania nowego urządzenia. Po skonfigurowaniu urządzenia, można usunąć uprawnienia. 


#### <a name="grant-account-permissions"></a>Udziel uprawnień konta

Administrator dzierżawy/globalny może nadać uprawnienia w następujący sposób

1. W usłudze Azure AD administratora globalnego/dzierżawy należy przejść do **usługi Azure Active Directory** > **użytkowników** > **ustawienia użytkownika**.
2. Administrator powinien być ustawiony **rejestracje aplikacji** do **tak**.

    ![Uprawnienia platformy Azure AD](./media/tutorial-prepare-vmware/aad.png)

> [!NOTE]
> To ustawienie domyślne, które nie są poufne. [Dowiedz się więcej](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).



#### <a name="assign-application-developer-role"></a>Przypisanie roli aplikacji dla deweloperów 

Administrator dzierżawy/globalny przypisać rolę dewelopera aplikacji do konta. [Dowiedz się więcej](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).

## <a name="assign-role-assignment-permissions"></a>Przypisz uprawnienia do przypisywania roli

Przypisz uprawnienia do przypisywania ról w grupie zasobów, w której usługa Azure Migrate znajduje się projekt, w następujący sposób:

1. W tej grupie zasobów w witrynie Azure portal wybierz **kontrola dostępu (IAM)** .
2. W **Sprawdź dostęp**, Znajdź odpowiednie konto i kliknij go, aby wyświetlić uprawnienia.

    - Można uruchomić oceny serwera **Współautor** uprawnienia są wystarczające.
    - Aby uruchomić migrację serwera bez wykorzystania agentów, powinny mieć **właściciela** (lub **Współautor** i **Administrator dostępu użytkowników**) uprawnień.

3. Jeśli nie masz wymaganych uprawnień, należy zażądać ich z właścicielem grupy zasobów. 



## <a name="prepare-for-vmware-vm-assessment"></a>Przygotowywanie oceny maszyn wirtualnych VMware

Aby przygotować się do oceny maszyn wirtualnych VMware, należy sprawdź ustawienia maszyny Wirtualnej i hoście funkcji Hyper-V i sprawdź ustawienia urządzenia wdrożenia.

### <a name="verify-vmware-settings"></a>Sprawdź ustawienia oprogramowania VMware

1. [Sprawdź](migrate-support-matrix-vmware.md#assessment-vmware-server-requirements) wymagań dotyczących serwerów VMware oceny maszyn wirtualnych.
2. [Upewnij się, że](migrate-support-matrix-vmware.md#assessment-port-requirements) czy wymagane porty są otwarte na serwerach vCenter.


### <a name="set-up-an-account-for-assessment"></a>Skonfiguruj konto dla oceny

Usługa Azure Migrate musi uzyskać dostęp w programie vCenter Server w celu odnalezienia maszyn wirtualnych na potrzeby oceny i migracji bez wykorzystania agentów. Tylko do oceny należy tylko do odczytu konta programu vCenter Server.

Jeśli używasz firewall.proxy opartego na adresach URL, dostęp do wymaganych [adresów URL usługi Azure](migrate-support-matrix-vmware.md#assessment-url-access-requirements).

Upewnij się, że serwer proxy usuwa wszystkie rekordy CNAME otrzymane podczas wyszukiwania adresów URL.


### <a name="verify-appliance-settings-for-assessment"></a>Sprawdź ustawienia urządzenia dla oceny

Przed rozpoczęciem konfigurowania usługi Azure Migrate urządzenie i oceny początku w następnym samouczku, przygotowywanie do wdrożenia urządzenia.

1. Sprawdź, czy [Sprawdź](migrate-support-matrix-vmware.md#assessment-appliance-requirements) wymagania dotyczące konfigurowania usługi Azure Migrate urządzenie w środowisku VMware.
2. [Przegląd](migrate-support-matrix-vmware.md#assessment-url-access-requirements) adresów URL usługi Azure, które urządzenie, będziesz potrzebować dostępu do.
3. Przejrzyj dane, z którego urządzenia zostaną zebrane podczas odnajdywania i oceny.
4. [Uwaga](migrate-support-matrix-vmware.md#assessment-port-requirements) portu wymagań dostępu dla urządzenia.
5. Urządzenie Azure Migrate możesz wdrożyć jako Maszynę wirtualną VMware przy użyciu pliku OVA. Na serwer vCenter upewnij się, że Twoje konto ma uprawnienia do tworzenia maszyny Wirtualnej przy użyciu pliku OVA.


## <a name="prepare-for-agentless-vmware-migration"></a>Przygotowanie do migracji usługi bez wykorzystania agentów programu VMware

Przejrzyj wymagania dotyczące migracji bez wykorzystania agentów maszyn wirtualnych programu VMware.

1. [Przegląd](migrate-support-matrix-vmware.md#agentless-migration-vmware-server-requirements) wymagań dotyczących serwerów VMware do migracji bez wykorzystania agentów.
2. Skonfiguruj konto do dostępu do serwera vCenter z [wymagane uprawnienia](migrate-support-matrix-vmware.md#agentless-migration-vcenter-server-permissions) migracji bez wykorzystania agentów.
3. [Uwaga](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements) wymagania dotyczące maszyn wirtualnych VMware, które chcesz przeprowadzić migrację na platformę Azure przy użyciu funkcji migracji.
4. [Przegląd](migrate-support-matrix-vmware.md#agentless-migration-appliance-requirements) urządzenia wymagania dotyczące migracji bez wykorzystania agentów.]
5. Uwaga urządzenia [do adresów URL](migrate-support-matrix-vmware.md#agentless-migration-url-access-requirements) i [port dostępu](migrate-support-matrix-vmware.md#agentless-migration-port-requirements) wymagania dotyczące migracji bez wykorzystania agentów.


## <a name="prepare-for-agent-based-vmware-migration"></a>Przygotowanie do migracji opartej o agentów programu VMware

Przejrzyj wymagania dotyczące migracji opartej o agentów maszyn wirtualnych programu VMware.

1. [Przegląd](migrate-support-matrix-vmware.md#agent-based-migration-vmware-server-requirements) wymagań dotyczących serwerów VMware do migracji bez wykorzystania agentów. 
2. Skonfiguruj konto do dostępu do serwera vCenter z [wymagane uprawnienia](migrate-support-matrix-vmware.md#agent-based-migration-vcenter-server-permissions) migracji bez wykorzystania agentów.
3. [Uwaga](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements) wymagania dotyczące maszyn wirtualnych VMware, które chcesz przeprowadzić migrację na platformę Azure za pomocą migracji oparte na agentach, łącznie z instalacją mobilności usługi na każdej maszynie Wirtualnej, które mają zostać zmigrowane.
4. Uwaga [do adresów URL](migrate-support-matrix-vmware.md#agent-based-migration-url-access-requirements).
5. Przegląd [port dostępu](migrate-support-matrix-vmware.md#agent-based-migration-port-requirements) wymagań usługi mobilności uruchomioną na każdej maszynie Wirtualnej oraz serwera konfiguracji usługi Azure Migrate.

## <a name="next-steps"></a>Następne kroki

W tym samouczku zostaną wykonane następujące czynności:
 
> [!div class="checklist"] 
> * Skonfiguruj uprawnienia platformy Azure.
> * Przygotowanie programu VMware do oceny i migracji.


Drugim samouczku, aby skonfigurować projekt usługi Azure Migrate w dalszym ciągu i ocenianie maszyn wirtualnych programu VMware do migracji na platformę Azure.

> [!div class="nextstepaction"] 
> [Ocenianie maszyn wirtualnych VMware](./tutorial-migrate-vmware.md) 

