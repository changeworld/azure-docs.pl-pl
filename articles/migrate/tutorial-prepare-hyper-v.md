---
title: Przygotowywanie maszyn wirtualnych funkcji Hyper-V do oceny/migracji za pomocą Azure Migrate
description: Dowiedz się, jak przygotować się do oceny/migracji maszyn wirtualnych funkcji Hyper-V za pomocą Azure Migrate.
ms.topic: tutorial
ms.date: 01/01/2020
ms.custom: mvc
ms.openlocfilehash: 6140d9689dafe8a97ae77346ea2212846e964cdc
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028925"
---
# <a name="prepare-for-assessment-and-migration-of-hyper-v-vms-to-azure"></a>Przygotowanie do oceny i migracji maszyn wirtualnych funkcji Hyper-V na platformę Azure

W tym artykule opisano, jak przygotować się do oceny i migracji lokalnych maszyn wirtualnych funkcji Hyper-V na platformę Azure przy użyciu [Azure Migrate](migrate-services-overview.md).

[Azure Migrate](migrate-overview.md) udostępnia centrum narzędzi, które ułatwiają odnajdywanie, ocenianie i Migrowanie aplikacji, infrastruktury i obciążeń do Microsoft Azure. Centrum obejmuje narzędzia Azure Migrate i oferty niezależnych dostawców oprogramowania (ISV) innych firm.

Ten samouczek jest pierwszą częścią serii, która pokazuje, jak oceniać i migrować maszyny wirtualne funkcji Hyper-V na platformę Azure. Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przygotuj platformę Azure. Skonfiguruj uprawnienia dla konta i zasobów platformy Azure do pracy z Azure Migrate.
> * Przygotuj lokalne hosty i maszyny wirtualne funkcji Hyper-V na potrzeby oceny serwera. Możesz przygotować się przy użyciu skryptu konfiguracji lub ręcznie.
> * Przygotuj się na wdrożenie urządzenia Azure Migrateowego. Urządzenie służy do odnajdywania i oceniania lokalnych maszyn wirtualnych.
> * Przygotuj lokalne hosty i maszyny wirtualne funkcji Hyper-V na potrzeby migracji serwera.


> [!NOTE]
> Samouczki przedstawiają najprostszą ścieżkę wdrożenia dla scenariusza, dzięki czemu można szybko skonfigurować weryfikację koncepcji. Samouczki korzystają z domyślnych opcji, jeśli jest to możliwe, i nie wyświetlają wszystkich możliwych ustawień i ścieżek. Aby uzyskać szczegółowe instrukcje, zapoznaj się z tematem Porady dotyczące oceny i migracji funkcji Hyper-V.


Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).


## <a name="prepare-azure"></a>Przygotowywanie platformy Azure

### <a name="azure-permissions"></a>Uprawnienia platformy Azure

Musisz skonfigurować uprawnienia do wdrożenia Azure Migrate.

- Uprawnienia do konta platformy Azure w celu utworzenia projektu Azure Migrate.
- Uprawnienia dla Twojego konta, które zarejestrują urządzenie Azure Migrate. Urządzenie służy do odnajdywania i oceny migrowanych maszyn wirtualnych funkcji Hyper-V. Podczas rejestracji urządzenia Azure Migrate tworzy dwie aplikacje Azure Active Directory (Azure AD), które w unikatowy sposób identyfikują urządzenie:
    - Pierwsza aplikacja komunikuje się z punktami końcowymi usługi Azure Migrate.
    - Druga aplikacja uzyskuje dostęp do Azure Key Vault, który jest tworzony podczas rejestracji, do przechowywania informacji o aplikacji usługi Azure AD i ustawień konfiguracji urządzenia.



### <a name="assign-permissions-to-create-project"></a>Przypisywanie uprawnień do tworzenia projektu

Sprawdź, czy masz uprawnienia do tworzenia projektu Azure Migrate.

1. W Azure Portal Otwórz subskrypcję i wybierz pozycję **Kontrola dostępu (IAM)** .
2. W obszarze **Sprawdź dostęp**Znajdź odpowiednie konto i kliknij je, aby wyświetlić uprawnienia.
3. Należy mieć uprawnienia **współautora** lub **właściciela** .
    - Jeśli właśnie utworzono bezpłatne konto platformy Azure, jesteś właścicielem subskrypcji.
    - Jeśli nie jesteś właścicielem subskrypcji, Pracuj z właścicielem, aby przypisać rolę.


### <a name="assign-permissions-to-register-the-appliance"></a>Przypisywanie uprawnień do zarejestrowania urządzenia

Można przypisać uprawnienia Azure Migrate do tworzenia aplikacji usługi Azure AD utworzonych podczas rejestracji urządzenia przy użyciu jednej z następujących metod:

- Dzierżawa/Administrator globalny może udzielić uprawnień użytkownikom w dzierżawie, aby tworzyć i rejestrować aplikacje usługi Azure AD.
- Dzierżawa/Administrator globalny może przypisać rolę dewelopera aplikacji (z uprawnieniami) do konta.

Warto zauważyć, że:

- Aplikacje nie mają żadnych innych uprawnień dostępu do subskrypcji innych niż opisane powyżej.
- Te uprawnienia są potrzebne tylko podczas rejestrowania nowego urządzenia. Po skonfigurowaniu urządzenia można usunąć uprawnienia.


#### <a name="grant-account-permissions"></a>Udziel uprawnień konta

Dzierżawy/Administrator globalny mogą udzielić uprawnień w następujący sposób:

1. W usłudze Azure AD dzierżawca/Administrator globalny powinien przejść do **Azure Active Directory** > **użytkowników** > **ustawień użytkownika**.
2. Administrator powinien ustawić **rejestracje aplikacji** na **wartość tak**.

    ![Uprawnienia usługi Azure AD](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> Jest to ustawienie domyślne, które nie jest poufne. [Dowiedz się więcej](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).



#### <a name="assign-application-developer-role"></a>Przypisywanie roli Deweloper aplikacji

Dzierżawa/Administrator globalny może przypisać rolę dewelopera aplikacji do konta. [Dowiedz się więcej](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).


## <a name="prepare-hyper-v-for-assessment"></a>Przygotowanie funkcji Hyper-V do oceny

Funkcję Hyper-V do oceny maszyn wirtualnych można przygotować ręcznie lub za pomocą skryptu konfiguracji. Oto, co należy przygotować z skryptem lub [ręcznie](#prepare-hyper-v-manually).

- [Sprawdź](migrate-support-matrix-hyper-v.md#hyper-v-host-requirements) Ustawienia hosta funkcji Hyper-V i upewnij się, że [wymagane porty](migrate-support-matrix-hyper-v.md#port-access) są otwarte na hostach funkcji Hyper-v.
- Skonfiguruj komunikację zdalną programu PowerShell na każdym hoście, tak aby urządzenie Azure Migrate mogły uruchamiać polecenia programu PowerShell na hoście przy użyciu połączenia usługi WinRM.
- Delegowanie poświadczeń, jeśli dyski maszyny wirtualnej znajdują się na zdalnych udziałach SMB.
- Skonfiguruj konto, które będzie używane przez urządzenie do odnajdywania maszyn wirtualnych na hostach funkcji Hyper-V.
- Skonfiguruj usługi integracji funkcji Hyper-V na każdej maszynie wirtualnej, którą chcesz odnaleźć i ocenić.



## <a name="prepare-with-a-script"></a>Przygotuj ze skryptem

Skrypt wykonuje następujące czynności:

- Sprawdza, czy skrypt jest uruchamiany w obsługiwanej wersji programu PowerShell.
- Sprawdza, czy użytkownika (użytkownik uruchamiający skrypt) ma uprawnienia administracyjne na hoście funkcji Hyper-V.
- Umożliwia utworzenie konta użytkownika lokalnego (nie administratora), którego usługa Azure Migrate używa do komunikacji z hostem funkcji Hyper-V. To konto użytkownika jest dodawane do tych grup na hoście:
    - Użytkownicy zarządzania zdalnego
    - Administratorzy funkcji Hyper-V
    - Użytkownicy monitora wydajności
- Sprawdza, czy na hoście jest uruchomiona obsługiwana wersja funkcji Hyper-V i rola funkcji Hyper-V.
- Włącza usługę WinRM i otwiera porty 5985 (HTTP) i 5986 (HTTPS) na hoście (wymaganym do zbierania metadanych).
- Włącza obsługę zdalną programu PowerShell na hoście.
- Sprawdza, czy usługa integracji funkcji Hyper-V jest włączona na wszystkich maszynach wirtualnych zarządzanych przez hosta.
- W razie konieczności włącza Protokół CredSSP na hoście.

Uruchom skrypt w następujący sposób:

1. Upewnij się, że na hoście funkcji Hyper-V jest zainstalowany program PowerShell w wersji 4,0 lub nowszej.
2. Pobierz skrypt z [Centrum pobierania Microsoft](https://aka.ms/migrate/script/hyperv). Skrypt jest kryptograficznie podpisany przez firmę Microsoft.
3. Zweryfikuj integralność skryptu przy użyciu plików MD5 lub SHA256 skrótów. Wartości hasztagów są poniżej. Uruchom to polecenie, aby wygenerować skrót dla skryptu:
    ```
    C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]
    ```
    Przykładowe użycie:
    ```
    C:\>CertUtil -HashFile C:\Users\Administrators\Desktop\ MicrosoftAzureMigrate-Hyper-V.ps1
    SHA256
    ```

4.  Po zweryfikowaniu integralności skryptu Uruchom skrypt na każdym hoście funkcji Hyper-V za pomocą tego polecenia programu PowerShell:
    ```
    PS C:\Users\Administrators\Desktop> MicrosoftAzureMigrate-Hyper-V.ps1
    ```

### <a name="hashtag-values"></a>Wartości hasztagów

Wartości skrótu to:

| **Skrót** | **Wartość** |
| --- | --- |
| **ALGORYTMU** | 0ef418f31915d01f896ac42a80dc414e |
| **SHA256** | 0ad60e7299925eff4d1ae9f1c7db485dc9316ef45b0964148a3c07c80761ade2 |


## <a name="prepare-hyper-v-manually"></a>Ręczne przygotowanie funkcji Hyper-V

Wykonaj procedury opisane w tej sekcji, aby ręcznie przygotować funkcję Hyper-V zamiast używać skryptu.

### <a name="verify-powershell-version"></a>Weryfikuj wersję programu PowerShell

Upewnij się, że na hoście funkcji Hyper-V jest zainstalowany program PowerShell w wersji 4,0 lub nowszej.



### <a name="set-up-an-account-for-vm-discovery"></a>Konfigurowanie konta do odnajdywania maszyn wirtualnych

Azure Migrate wymaga uprawnień do odnajdowania lokalnych maszyn wirtualnych.

- Skonfiguruj domenę lub konto użytkownika lokalnego z uprawnieniami administratora na hostach lub klastrach funkcji Hyper-V.

    - W przypadku wszystkich hostów i klastrów, które mają zostać uwzględnione w odnajdywaniu, potrzebne jest jedno konto.
    - Może to być konto lokalne lub konto domeny. Zalecamy, aby mieć uprawnienia administratora na hostach lub klastrach funkcji Hyper-V.
    - Alternatywnie, jeśli nie chcesz przypisywać uprawnień administratora, potrzebne są następujące uprawnienia:
        - Użytkownicy zarządzania zdalnego
        - Administratorzy funkcji Hyper-V
        - Użytkownicy monitora wydajności

### <a name="verify-hyper-v-host-settings"></a>Weryfikuj ustawienia hosta funkcji Hyper-V

1. Sprawdź [wymagania dotyczące hosta funkcji Hyper-V](migrate-support-matrix-hyper-v.md#hyper-v-host-requirements) na potrzeby oceny serwera.
2. Upewnij się, że [wymagane porty](migrate-support-matrix-hyper-v.md#port-access) są otwarte na hostach funkcji Hyper-V.

### <a name="enable-powershell-remoting-on-hosts"></a>Włączanie komunikacji zdalnej programu PowerShell na hostach

Skonfiguruj komunikację zdalną programu PowerShell na każdym hoście w następujący sposób:

1. Na każdym hoście Otwórz konsolę programu PowerShell jako administrator.
2. Uruchom następujące polecenie:

    ```
    Enable-PSRemoting -force
    ```
### <a name="enable-integration-services-on-vms"></a>Włącz usługi integracji na maszynach wirtualnych

Usługi integracji powinny być włączone na każdej maszynie wirtualnej, dzięki czemu Azure Migrate mogą przechwytywać informacje o systemie operacyjnym na maszynie wirtualnej.

Na maszynach wirtualnych, które mają być odnajdywane i oceniane, Włącz [usługi integracji funkcji Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) na każdej maszynie wirtualnej.


### <a name="enable-credssp-on-hosts"></a>Włącz protokół CredSSP na hostach

Jeśli host zawiera maszyny wirtualne z dyskami, które znajdują się w udziałach SMB, wykonaj ten krok na hoście.

- To polecenie można uruchomić zdalnie na wszystkich hostach funkcji Hyper-V.
- W przypadku dodania nowych węzłów hosta do klastra są one automatycznie dodawane do odnajdywania, ale w razie potrzeby należy ręcznie włączyć protokół CredSSP w nowych węzłach.

Włącz w następujący sposób:

1. Zidentyfikuj hosty funkcji Hyper-V z uruchomionymi maszynami wirtualnymi funkcji Hyper-V z dyskami w udziałach SMB.
2. Uruchom następujące polecenie na każdym zidentyfikowanym hoście funkcji Hyper-V:

    ```
    Enable-WSManCredSSP -Role Server -Force
    ```

Po skonfigurowaniu urządzenia należy zakończyć konfigurowanie dostawcy CredSSP, [włączając je na urządzeniu](tutorial-assess-hyper-v.md#delegate-credentials-for-smb-vhds). Jest to opisane w następnym samouczku w tej serii.


## <a name="prepare-for-appliance-deployment"></a>Przygotowanie do wdrożenia urządzenia

Przed skonfigurowaniem urządzenia Azure Migrate i rozpoczęciem oceny w następnym samouczku Przygotuj się do wdrożenia urządzenia.

1. [Sprawdź](migrate-appliance.md#appliance---hyper-v) wymagania dotyczące urządzeń.
2. [Przejrzyj](migrate-appliance.md#url-access) adresy URL platformy Azure, do których urządzenie będzie potrzebować dostępu.
3. Przejrzyj dane, które urządzenie będzie zbierać podczas odnajdywania i oceny.
4. [Zwróć uwagę](migrate-appliance.md#collected-data---hyper-v) na wymagania dotyczące dostępu do portów dla urządzenia.


## <a name="prepare-for-hyper-v-migration"></a>Przygotowanie do migracji funkcji Hyper-V

1. [Przegląd](migrate-support-matrix-hyper-v-migration.md#hyper-v-hosts) Wymagania hosta funkcji Hyper-V dotyczące migracji oraz adresy URL platformy Azure, do których hosty i klastry funkcji Hyper-V potrzebują dostępu do migracji maszyn wirtualnych.
2. [Zapoznaj](migrate-support-matrix-hyper-v-migration.md#hyper-v-vms) się z wymaganiami dotyczącymi maszyn wirtualnych funkcji Hyper-V, które chcesz zmigrować na platformę Azure.


## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Skonfiguruj uprawnienia konta platformy Azure.
> * Przygotowane hosty i maszyny wirtualne funkcji Hyper-V na potrzeby oceny i migracji.
> * Przygotowano do wdrożenia urządzenia Azure Migrateowego.

Przejdź do następnego samouczka, aby utworzyć projekt Azure Migrate, wdrożyć urządzenie oraz odnajdywać i oceniać maszyny wirtualne funkcji Hyper-V na potrzeby migracji na platformę Azure.

> [!div class="nextstepaction"]
> [Ocenianie maszyn wirtualnych funkcji Hyper-V](./tutorial-assess-hyper-v.md)
