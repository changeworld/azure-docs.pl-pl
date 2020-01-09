---
title: Przygotowywanie maszyn wirtualnych VMware do oceny/migracji za pomocą Azure Migrate
description: Dowiedz się, jak przygotować się do oceny/migracji maszyn wirtualnych VMware za pomocą Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 34bc62a9cb7e5d1358322500a8929b6f8b36d422
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454552"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>Przygotowywanie maszyn wirtualnych VMware na potrzeby oceny i migracji na platformę Azure

Ten artykuł ułatwia przygotowanie się do oceny i/lub migracji lokalnych maszyn wirtualnych VMware na platformę Azure przy użyciu [Azure Migrate](migrate-services-overview.md).

[Azure Migrate](migrate-overview.md) udostępnia centrum narzędzi, które ułatwiają odnajdywanie, ocenianie i Migrowanie aplikacji, infrastruktury i obciążeń do Microsoft Azure. Centrum obejmuje narzędzia Azure Migrate i oferty niezależnych dostawców oprogramowania (ISV) innych firm.


Ten samouczek jest pierwszą częścią serii, która pokazuje, jak oceniać i migrować maszyny wirtualne VMware. Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przygotuj platformę Azure do pracy z Azure Migrate.
> * Przygotuj oprogramowanie VMware do oceny maszyn wirtualnych.
> * Przygotuj oprogramowanie VMware do migracji maszyn wirtualnych.

> [!NOTE]
> Samouczki pokazują najprostszą ścieżkę wdrożenia dla scenariusza. Są one przydatne, gdy dowiesz się, jak skonfigurować wdrożenie i jak szybko sprawdzać koncepcje. Samouczki korzystają z domyślnych opcji, jeśli jest to możliwe, i nie wyświetlają wszystkich możliwych ustawień i ścieżek. Aby uzyskać szczegółowe instrukcje, zapoznaj się z tematem Porady dotyczące oceny i migracji programu VMware.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).


## <a name="prepare-azure"></a>Przygotowywanie platformy Azure

Wymagane są te uprawnienia.

**Zadanie podrzędne** | **Uprawnienia**
--- | ---
**Tworzenie projektu Azure Migrate** | Twoje konto platformy Azure wymaga uprawnień do utworzenia projektu.
**Rejestrowanie urządzenia Azure Migrate** | Azure Migrate używa uproszczonego urządzenia Azure Migrate do oceny maszyn wirtualnych VMware z oceną serwera Azure Migrate oraz do uruchamiania [migracji bez agentów](server-migrate-overview.md) maszyn wirtualnych VMware z migracją na serwer Azure Migrate. To urządzenie umożliwia odnajdywanie maszyn wirtualnych i wysyłanie metadanych maszyn wirtualnych i danych wydajności do Azure Migrate.<br/><br/>Podczas rejestracji Azure Migrate tworzy dwie aplikacje Azure Active Directory (Azure AD), które jednoznacznie identyfikują urządzenie i potrzebują uprawnień do tworzenia tych aplikacji.<br/> — Pierwsza aplikacja komunikuje się z punktami końcowymi usługi Azure Migrate.<br/> — Druga aplikacja uzyskuje dostęp do Azure Key Vault utworzonego podczas rejestracji w celu przechowywania ustawień konfiguracji i informacji o aplikacji usługi Azure AD.
**Tworzenie Key Vault** | Aby przeprowadzić migrację maszyn wirtualnych VMware przy użyciu migracji Azure Migrate serwera, Azure Migrate tworzy Key Vault do zarządzania kluczami dostępu do konta magazynu replikacji w ramach subskrypcji. Aby utworzyć magazyn, musisz mieć uprawnienia do przypisywania ról w grupie zasobów, w której znajduje się projekt Azure Migrate.


### <a name="assign-permissions-to-create-project"></a>Przypisywanie uprawnień do tworzenia projektu

1. W Azure Portal Otwórz subskrypcję i wybierz pozycję **Kontrola dostępu (IAM)** .
2. W obszarze **Sprawdź dostęp**Znajdź odpowiednie konto i kliknij je, aby wyświetlić uprawnienia.
3. Należy mieć uprawnienia **współautora** lub **właściciela** .
    - Jeśli właśnie utworzono bezpłatne konto platformy Azure, jesteś właścicielem subskrypcji.
    - Jeśli nie jesteś właścicielem subskrypcji, Pracuj z właścicielem, aby przypisać rolę.

### <a name="assign-permissions-to-register-the-appliance"></a>Przypisywanie uprawnień do zarejestrowania urządzenia

Aby zarejestrować urządzenie, należy przypisać uprawnienia Azure Migrate do tworzenia aplikacji usługi Azure AD podczas rejestracji urządzenia. Uprawnienia można przypisać przy użyciu jednej z następujących metod:

- Dzierżawa/Administrator globalny może udzielić uprawnień użytkownikom w dzierżawie, aby tworzyć i rejestrować aplikacje usługi Azure AD.
- Dzierżawa/Administrator globalny może przypisać rolę dewelopera aplikacji (z uprawnieniami) do konta.

> [!NOTE]
> - Aplikacje nie mają żadnych innych uprawnień dostępu do subskrypcji innych niż opisane powyżej.
> - Te uprawnienia są potrzebne tylko podczas rejestrowania nowego urządzenia. Po skonfigurowaniu urządzenia można usunąć uprawnienia.


#### <a name="grant-account-permissions"></a>Udziel uprawnień konta

Dzierżawy/Administrator globalny mogą udzielić uprawnień w następujący sposób

1. W usłudze Azure AD dzierżawca/Administrator globalny powinien przejść do **Azure Active Directory** > **użytkowników** > **ustawień użytkownika**.
2. Administrator powinien ustawić **rejestracje aplikacji** na **wartość tak**. Jest to ustawienie domyślne, które nie jest poufne. [Dowiedz się więcej](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

    ![Uprawnienia usługi Azure AD](./media/tutorial-prepare-vmware/aad.png)



#### <a name="assign-application-developer-role"></a>Przypisywanie roli Deweloper aplikacji

Dzierżawa/Administrator globalny może przypisać rolę dewelopera aplikacji do konta. [Dowiedz się więcej](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).

### <a name="assign-role-assignment-permissions"></a>Przypisywanie uprawnień do przypisywania ról

Aby umożliwić Azure Migrate tworzenia Key Vault, przypisz uprawnienia do przypisywania ról w następujący sposób:

1. W grupie zasobów w Azure Portal wybierz pozycję **Kontrola dostępu (IAM)** .
2. W obszarze **Sprawdź dostęp**Znajdź odpowiednie konto i kliknij je, aby wyświetlić uprawnienia.

    - Aby uruchomić ocenę serwera, uprawnienia **współautora** są wystarczające.
    - Aby uruchomić migrację serwera bez agenta, należy mieć uprawnienia **właściciela** (lub **współautora** i **administrator dostępu użytkowników**).

3. Jeśli nie masz wymaganych uprawnień, poproś ich od właściciela grupy zasobów.



## <a name="prepare-for-vmware-vm-assessment"></a>Przygotowanie do oceny maszyny wirtualnej VMware

Aby przygotować się do oceny maszyn wirtualnych VMware, należy:

- **Sprawdź ustawienia programu VMware**. Upewnij się, że vCenter Server i maszyny wirtualne, które chcesz zmigrować, spełniają wymagania.
- **Skonfiguruj konto oceny**. Azure Migrate musi uzyskać dostęp do vCenter Server w celu odnalezienia maszyn wirtualnych do oceny.
- **Sprawdź wymagania dotyczące urządzeń**. Sprawdź wymagania dotyczące wdrażania dla urządzenia Azure Migrate używanego do oceny.

### <a name="verify-vmware-settings"></a>Sprawdź ustawienia programu VMware

1. [Sprawdź](migrate-support-matrix-vmware.md#assessment-vcenter-server-requirements) Wymagania dotyczące oceny serwera VMware.
2. [Upewnij](migrate-support-matrix-vmware.md#assessment-port-requirements) się, że wymagane porty są otwarte na vCenter Server.


### <a name="set-up-an-account-for-assessment"></a>Skonfiguruj konto do oceny

Azure Migrate musi uzyskać dostęp do vCenter Server w celu odnalezienia maszyn wirtualnych na potrzeby oceny i migracji bez wykorzystania agentów.

- Jeśli planujesz odnajdywanie aplikacji lub wizualizowanie zależności w sposób niezależny od agenta, Utwórz konto vCenter Server z dostępem tylko do odczytu wraz z włączonymi uprawnieniami dla **maszyn wirtualnych** > **operacji gościa**.

  ![uprawnienia vCenter Server konta](./media/tutorial-prepare-vmware/vcenter-server-permissions.png)

- Jeśli nie planujesz przeprowadzać odnajdywania aplikacji ani wizualizacji zależności bez agenta, skonfiguruj konto tylko do odczytu dla vCenter Server.

### <a name="verify-appliance-settings-for-assessment"></a>Weryfikuj ustawienia urządzenia do oceny

Przed wdrożeniem urządzenia Sprawdź wymagania dotyczące urządzenia.

1. [Sprawdź](migrate-support-matrix-vmware.md#assessment-appliance-requirements) wymagania i ograniczenia dotyczące urządzeń.
2. Jeśli używasz serwera proxy zapory opartego na adresie URL, [Przejrzyj](migrate-support-matrix-vmware.md#assessment-url-access-requirements) adresy URL platformy Azure, do których urządzenie będzie musiało uzyskać dostęp. Upewnij się, że serwer proxy rozpoznaje wszystkie rekordy CNAME otrzymane podczas wyszukiwania adresów URL.
3. Przejrzyj [dane wydajności](migrate-appliance.md#collected-performance-data-vmware) i [metadane](migrate-appliance.md#collected-metadata-vmware) zbierane przez urządzenie podczas odnajdywania i oceny.
4. [Zanotuj](migrate-support-matrix-vmware.md#assessment-port-requirements) porty, do których uzyskuje dostęp urządzenie.
5. Na vCenter Server upewnij się, że konto ma uprawnienia do tworzenia maszyny wirtualnej przy użyciu pliku komórek jajowych. Należy wdrożyć urządzenie Azure Migrate jako maszynę wirtualną VMware przy użyciu pliku komórki jajowe.

Jeśli używasz serwera proxy opartego na adresie URL, Zezwól na dostęp do wymaganych [adresów URL platformy Azure](migrate-support-matrix-vmware.md#assessment-url-access-requirements).




## <a name="prepare-for-agentless-vmware-migration"></a>Przygotowanie do migracji oprogramowania VMware bez agentów

Zapoznaj się z wymaganiami dotyczącymi migracji bez agentów maszyn wirtualnych VMware.

1. [Przegląd](migrate-support-matrix-vmware.md#agentless-migration-vmware-server-requirements) Wymagania dotyczące serwera VMware.
2. Skonfiguruj konto z [wymaganymi uprawnieniami](migrate-support-matrix-vmware.md#agentless-migration-vcenter-server-permissions), tak aby Azure Migrate mogły uzyskać dostęp do vCenter Server migracji bez agentów przy użyciu migracji serwera Azure Migrate.
3. [Zapoznaj](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements) się z wymaganiami dotyczącymi maszyn wirtualnych VMware, które chcesz migrować do platformy Azure za pomocą migracji bez wykorzystania agentów.
4. [Zapoznaj](migrate-support-matrix-vmware.md#agentless-migration-appliance-requirements) się z wymaganiami dotyczącymi korzystania z urządzenia Azure Migrate na potrzeby migracji bez wykorzystania agentów.
5. Zanotuj [dostęp do adresu URL](migrate-support-matrix-vmware.md#agentless-migration-url-access-requirements) i [dostępu do portu](migrate-support-matrix-vmware.md#agentless-migration-port-requirements) , którego urządzenie Azure Migrate potrzebuje do migracji bez wykorzystania agentów.


## <a name="prepare-for-agent-based-vmware-migration"></a>Przygotowanie do migracji oprogramowania VMware opartego na agencie

Zapoznaj się z wymaganiami dotyczącymi migracji maszyn wirtualnych VMware [na podstawie agenta](server-migrate-overview.md) .

1. [Przegląd](migrate-support-matrix-vmware.md#agent-based-migration-vmware-server-requirements) Wymagania dotyczące serwera VMware.
2. Skonfiguruj konto z [wymaganymi uprawnieniami](migrate-support-matrix-vmware.md#agent-based-migration-vcenter-server-permissions). Dzięki temu Azure Migrate mogą uzyskać dostęp do vCenter Server migracji opartej na agencie przy użyciu migracji serwera Azure Migrate.
3. [Zapoznaj](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements) się z wymaganiami dotyczącymi maszyn wirtualnych VMware, które chcesz migrować do platformy Azure za pomocą migracji opartej na agencie, w tym instalacji usługi mobilności na każdej maszynie wirtualnej, która ma zostać zmigrowana.
4. Zwróć uwagę na [dostęp do adresu URL](migrate-support-matrix-vmware.md#agent-based-migration-url-access-requirements).
5. Przejrzyj [dostęp do portów](migrate-support-matrix-vmware.md#agent-based-migration-port-requirements) , który Azure Migrate składników potrzebnych do dostępu opartego na agentach.

## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Skonfiguruj uprawnienia platformy Azure.
> * Przygotowano oprogramowanie VMware do oceny i migracji.


Przejdź do drugiego samouczka, aby skonfigurować projekt Azure Migrate i ocenić maszyny wirtualne VMware pod kątem migracji na platformę Azure.

> [!div class="nextstepaction"]
> [Ocenianie maszyn wirtualnych VMware](./tutorial-assess-vmware.md)
