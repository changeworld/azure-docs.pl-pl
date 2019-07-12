---
title: Przygotowywanie maszyn wirtualnych funkcji Hyper-V na potrzeby oceny i migracji na platformę Azure za pomocą usługi Azure Migrate | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób przygotowania do oceny i migracji maszyn wirtualnych funkcji Hyper-V, na platformie Azure przy użyciu usługi Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 9e0d29770aa36f8e79bf08b7c5435ea2dbc4ae38
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840371"
---
# <a name="prepare-for-assessment-and-migration-of-hyper-v-vms-to-azure"></a>Przygotowanie do oceny i migracji maszyn wirtualnych funkcji Hyper-V, na platformie Azure

W tym artykule opisano sposób przygotowania do oceny i migracji lokalnych maszyn wirtualnych funkcji Hyper-V do platformy Azure z [usługi Azure Migrate](migrate-services-overview.md).

[Usługa Azure Migrate](migrate-overview.md) udostępnia Centrum narzędzi, które ułatwiają odnajdowanie, oceny i migracji aplikacji, infrastruktury i obciążeń w systemie Microsoft Azure. Centrum obejmuje usługę Azure Migrate narzędzi i ofert Niezależnym dostawcą oprogramowania niezależnie od innych firm. 

Niniejszy samouczek jest pierwszą serii, który pokazuje, jak ocenianie i Migrowanie maszyn wirtualnych funkcji Hyper-V do platformy Azure. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przygotowywanie platformy Azure. Skonfiguruj uprawnienia dla konta platformy Azure i zasoby do pracy z usługi Azure Migrate.
> * Przygotowywanie lokalnej funkcji Hyper-V hosty i maszyny wirtualne serwera oceny.
> * Przygotowanie hostów funkcji Hyper-V w środowisku lokalnym i maszyn wirtualnych do migracji serwera.


> [!NOTE]
> W samouczkach pokazano to najprostsza ścieżka wdrażania dla scenariusza, aby szybko skonfigurować się weryfikacji koncepcji. Samouczki użyj opcji domyślnych, jeśli jest to możliwe, a nie pokazuj wszystkich możliwych ustawień i ścieżki. Aby uzyskać szczegółowe instrukcje Przejrzyj porady dla funkcji Hyper-V, oceny i migracji.


Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).


## <a name="prepare-azure"></a>Przygotowywanie platformy Azure

### <a name="azure-permissions"></a>Uprawnienia platformy Azure

Konieczne jest kilku uprawnień do wdrażania usługi Azure Migrate:

- Twoje konto platformy Azure wymaga uprawnień, aby utworzyć projekt usługi Azure Migrate na potrzeby oceny i migracji. 
- Twoje konto platformy Azure musi mieć uprawnienia do zarejestrowania urządzenia w usłudze Azure Migrate.
    - Ocena usługi Azure Migrate uruchamiane uproszczone urządzenia, który odnajdzie maszyny wirtualne funkcji Hyper-V i wysyła dane wydajności i metadanych maszyny Wirtualnej do usługi Azure Migrate.
    - Podczas rejestracji urządzenia w usłudze Azure Migrate tworzy dwie aplikacje usługi Azure Active Directory (Azure AD), które jednoznacznie identyfikują urządzenie:
        - Pierwszy aplikacja komunikuje się z punktami końcowymi usługi Azure Migrate.
        - Druga aplikacja uzyskuje dostęp do usługi Azure Key Vault utworzone podczas rejestracji do przechowywania usługi Azure AD informacje i urządzenia w ustawieniach konfiguracji aplikacji.
    - Możesz przypisywać uprawnienia dla usługi Azure Migrate można tworzyć te aplikacje usługi Azure AD przy użyciu jednej z następujących metod:
        - Administrator globalny/dzierżawy mogą udzielać uprawnień użytkowników w dzierżawie, aby utworzyć i zarejestrować aplikacje usługi Azure AD.
        - Administrator dzierżawy/globalny przypisać rolę dewelopera aplikacji (który ma uprawnienia) do konta.
    - Warto zauważyć, że:
        - Aplikacje nie mają żadnych uprawnień dostępu do subskrypcji innych niż opisane powyżej.
        - Wystarczy tylko te uprawnienia podczas rejestrowania nowego urządzenia. Po skonfigurowaniu urządzenia, można usunąć uprawnienia. 


### <a name="assign-permissions-to-create-project"></a>Przypisywanie uprawnień do tworzenia projektu

Sprawdź, czy masz uprawnienia do tworzenia projektu usługi Azure Migrate.

1. W witrynie Azure portal Otwórz subskrypcję, a następnie wybierz **kontrola dostępu (IAM)** .
2. W **Sprawdź dostęp**, Znajdź odpowiednie konto i kliknij go, aby wyświetlić uprawnienia.
3. Musisz mieć **Współautor** lub **właściciela** uprawnienia.
    - Jeśli bezpłatne konto platformy Azure jest właśnie utworzone, jesteś właścicielem subskrypcji.
    - Jeśli nie jesteś właścicielem subskrypcji, Współpracuj z właścicielem Aby przypisać rolę.


### <a name="assign-permissions-to-register-the-appliance"></a>Przypisywanie uprawnień do rejestracji urządzenia

Jeśli jest wdrażane urządzenie Azure Migrate i ocenianie maszyn wirtualnych, należy go zarejestrować.

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

Administrator dzierżawy/globalny może nadać uprawnienia w następujący sposób:

1. W usłudze Azure AD administratora globalnego/dzierżawy należy przejść do **usługi Azure Active Directory** > **użytkowników** > **ustawienia użytkownika**.
2. Administrator powinien być ustawiony **rejestracje aplikacji** do **tak**.

    ![Uprawnienia platformy Azure AD](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> To ustawienie domyślne, które nie są poufne. [Dowiedz się więcej](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).



#### <a name="assign-application-developer-role"></a>Przypisanie roli aplikacji dla deweloperów 

Administrator dzierżawy/globalny przypisać rolę dewelopera aplikacji do konta. [Dowiedz się więcej](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).


## <a name="prepare-for-hyper-v-assessment"></a>Przygotowywanie oceny funkcji Hyper-V

Przygotowywanie oceny funkcji Hyper-V, sprawdź ustawienia maszyny Wirtualnej i hoście funkcji Hyper-V i sprawdź ustawienia wdrażania urządzenia.

### <a name="verify-hyper-v-host-settings"></a>Sprawdź ustawienia hosta funkcji Hyper-V

1. Sprawdź [wymagania dotyczące hosta funkcji Hyper-V](migrate-support-matrix-hyper-v.md#assessment-hyper-v-host-requirements) dla oceny server.
2. Upewnij się, że [wymagane porty](migrate-support-matrix-hyper-v.md#assessment-port-requirements) są otwarte na hostach funkcji Hyper-V.

### <a name="enable-powershell-remoting-on-hosts"></a>Włączanie komunikacji zdalnej programu PowerShell na hostach

Skonfiguruj sposób komunikacji zdalnej programu PowerShell na każdym hoście w następujący sposób:

1. Na każdym hoście otwórz konsolę programu PowerShell jako administrator.
2. Uruchom następujące polecenie:

    ```
    Enable-PSRemoting -force
    ```

### <a name="enable-credssp-on-hosts"></a>Włącz protokół CredSSP na hostach

Jeśli dyski maszyn wirtualnych znajdują się w udziałach SMB, wykonaj ten krok dla każdego odpowiedniego hosta funkcji Hyper-V. W tym kroku jest używana do wykrywania informacji o konfiguracji dla maszyn wirtualnych funkcji Hyper-V z dyskami w udziałach SMB. Jeśli nie masz dyski maszyny Wirtualnej w udziałach SMB, możesz pominąć ten krok.

1. Identyfikowanie hostów funkcji Hyper-V uruchomione maszyny wirtualne funkcji Hyper-V z dyskami w udziałach SMB.
2. Na każdym z określonych hostów funkcji Hyper-V, uruchom następujące polecenie:

    ```
    Enable-WSManCredSSP -Role Server -Force
    ```

- Uwierzytelnianie CredSSP pozwala hosta funkcji Hyper-V delegować poświadczenia w imieniu klienta usługi Azure Migrate.
- To polecenie można uruchomić zdalnie na wszystkich hostach funkcji Hyper-V.
- Jeśli dodasz nowe węzły hosta w klastrze zostaną automatycznie dodani do odnajdowania, ale musisz ręcznie włączyć protokół CredSSP na nowe węzły, w razie potrzeby.

### <a name="verify-appliance-settings"></a>Sprawdź ustawienia urządzenia

Przed rozpoczęciem konfigurowania usługi Azure Migrate urządzenie i oceny początku w następnym samouczku, przygotowywanie do wdrożenia urządzenia.

1. [Sprawdź](migrate-support-matrix-hyper-v.md#assessment-appliance-requirements) wymagania dotyczące urządzenia.
2. [Przegląd](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access) adresów URL usługi Azure, które urządzenie, będziesz potrzebować dostępu do.
3. Przejrzyj dane, z którego urządzenia zostaną zebrane podczas odnajdywania i oceny.
4. [Uwaga](migrate-support-matrix-hyper-v.md#assessment-port-requirements) portu wymagań dostępu dla urządzenia.


### <a name="set-up-an-account-for-vm-discovery"></a>Skonfiguruj konto odnajdowania maszyn wirtualnych

Usługa Azure Migrate wymaga uprawnień do odnajdowania lokalnych maszyn wirtualnych.

- Skonfiguruj domenę lub konto użytkownika lokalnego z uprawnieniami administratora w klastrze funkcji Hyper-V hosts /.

    - Potrzebujesz jednego konta dla wszystkich hostów i klastrów, które mają zostać uwzględnione w procesie odnajdowania.
    - Konto może być kontem lokalnego lub domeny. Zaleca się, że ma uprawnienia administratora na hostach funkcji Hyper-V lub klastrów.
    - Alternatywnie Jeśli nie chcesz przypisać uprawnienia administratora, potrzebne są następujące uprawnienia:
        - Użytkownicy zarządzania zdalnego
        - Administratorzy funkcji Hyper-V
        - Użytkownicy monitora wydajności

### <a name="enable-integration-services-on-vms"></a>Włączanie usługi integracji na maszynach wirtualnych

Powinno być włączone usługi integracji na każdej maszynie Wirtualnej, tak, aby usługa Azure Migrate można przechwycić informacje dotyczące systemu operacyjnego na maszynie Wirtualnej.

- Na maszynach wirtualnych, które chcesz odnajdywać i oceniać, Włącz [usługi integracji funkcji Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) na każdej maszynie Wirtualnej. 

## <a name="prepare-for-hyper-v-migration"></a>Przygotowanie do migracji funkcji Hyper-V

1. [Przegląd](migrate-support-matrix-hyper-v.md#migration-hyper-v-host-requirements) wymagania dotyczące hosta funkcji Hyper-V do migracji.
2. [Przegląd](migrate-support-matrix-hyper-v.md#migration-hyper-v-vm-requirements) wymagania dotyczące maszyn wirtualnych funkcji Hyper-V, które chcesz przeprowadzić migrację na platformę Azure.
3. [Uwaga](migrate-support-matrix-hyper-v.md#migration-hyper-v-host-url-access) adresów URL usługi Azure, której funkcji Hyper-v hosty i klastry muszą mieć dostęp do migracji maszyny Wirtualnej.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku zostaną wykonane następujące czynności:
 
> [!div class="checklist"] 
> * Skonfiguruj uprawnienia dla konta platformy Azure.
> * Przygotowany hosty funkcji Hyper-V i maszyn wirtualnych na potrzeby oceny i migracji.

Przejdź do następnego samouczka, aby utworzyć projekt usługi Azure Migrate i ocenianie maszyn wirtualnych funkcji Hyper-V pod kątem migracji do platformy Azure

> [!div class="nextstepaction"] 
> [Ocenianie maszyn wirtualnych funkcji Hyper-V](./tutorial-assess-hyper-v.md) 
