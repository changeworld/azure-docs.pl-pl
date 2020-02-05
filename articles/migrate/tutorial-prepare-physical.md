---
title: Przygotuj serwery fizyczne do oceny/migracji za pomocą Azure Migrate
description: Dowiedz się, jak przygotować się do oceny/migracji serwerów fizycznych za pomocą Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: a487e0c9d565993b45f7e271af23a622e61389ad
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990831"
---
# <a name="prepare-for-assessment-and-migration-of-physical-servers-to-azure"></a>Przygotowanie do oceny i migracji serwerów fizycznych na platformę Azure

W tym artykule opisano sposób przygotowania do oceny lokalnych serwerów fizycznych za pomocą [Azure Migrate](migrate-services-overview.md).

[Azure Migrate](migrate-overview.md) udostępnia centrum narzędzi, które ułatwiają odnajdywanie, ocenianie i Migrowanie aplikacji, infrastruktury i obciążeń do Microsoft Azure. Centrum obejmuje narzędzia Azure Migrate i oferty niezależnych dostawców oprogramowania (ISV) innych firm. 

Ten samouczek jest pierwszą częścią serii, która pokazuje, jak oceniać serwery fizyczne przy użyciu Azure Migrate. Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przygotuj platformę Azure. Skonfiguruj uprawnienia dla konta i zasobów platformy Azure do pracy z Azure Migrate.
> * Przygotuj lokalne serwery fizyczne do oceny serwera.


> [!NOTE]
> Samouczki przedstawiają najprostszą ścieżkę wdrożenia dla scenariusza, dzięki czemu można szybko skonfigurować weryfikację koncepcji. Samouczki korzystają z domyślnych opcji, jeśli jest to możliwe, i nie wyświetlają wszystkich możliwych ustawień i ścieżek. Aby uzyskać szczegółowe instrukcje, zapoznaj się z informacjami na temat oceny serwerów fizycznych.


Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).


## <a name="prepare-azure"></a>Przygotowywanie platformy Azure

### <a name="azure-permissions"></a>Uprawnienia platformy Azure

Musisz skonfigurować uprawnienia do wdrożenia Azure Migrate.

**Zadanie podrzędne** | **Uprawnienia**
--- | ---
**Tworzenie projektu Azure Migrate** | Twoje konto platformy Azure wymaga uprawnień do utworzenia projektu.
**Rejestrowanie urządzenia Azure Migrate** | Azure Migrate używa uproszczonego urządzenia Azure Migrate do odnajdywania i oceniania serwerów fizycznych za pomocą oceny Azure Migrate Server. To urządzenie umożliwia odnajdywanie serwerów i wysyłanie metadanych oraz danych wydajności do Azure Migrate.<br/><br/>Podczas rejestracji urządzenia następujący dostawcy rejestru są zarejestrowani z subskrypcją wybraną w ramach urządzenia — Microsoft. OffAzure, Microsoft. migrować i Microsoft. Rejestracja dostawcy zasobów umożliwia skonfigurowanie subskrypcji do pracy z dostawcą zasobów. Do zarejestrowania dostawców zasobów należy mieć rolę współautor lub właściciela w ramach subskrypcji.<br/><br/> W ramach dołączania Azure Migrate tworzy aplikację Azure Active Directory (Azure AD):<br/> Aplikacja usługi AAD służy do komunikacji (uwierzytelniania i autoryzacji) między agentami działającymi na urządzeniu przy użyciu odpowiednich usług działających na platformie Azure. Ta aplikacja nie ma uprawnień do wykonywania wywołań ARM lub dostępu RBAC w żadnym z zasobów.



### <a name="assign-permissions-to-create-project"></a>Przypisywanie uprawnień do tworzenia projektu

Sprawdź, czy masz uprawnienia do tworzenia projektu Azure Migrate.

1. W Azure Portal Otwórz subskrypcję i wybierz pozycję **Kontrola dostępu (IAM)** .
2. W obszarze **Sprawdź dostęp**Znajdź odpowiednie konto i kliknij je, aby wyświetlić uprawnienia.
3. Należy mieć uprawnienia **współautora** lub **właściciela** .
    - Jeśli właśnie utworzono bezpłatne konto platformy Azure, jesteś właścicielem subskrypcji.
    - Jeśli nie jesteś właścicielem subskrypcji, Pracuj z właścicielem, aby przypisać rolę.


### <a name="assign-permissions-to-register-the-appliance"></a>Przypisywanie uprawnień do zarejestrowania urządzenia

Można przypisać uprawnienia dla Azure Migrate, aby utworzyć aplikację usługi Azure AD podczas rejestracji urządzenia, korzystając z jednej z następujących metod:

- Dzierżawa/Administrator globalny może udzielić uprawnień użytkownikom w dzierżawie, aby tworzyć i rejestrować aplikacje usługi Azure AD.
- Dzierżawa/Administrator globalny może przypisać rolę dewelopera aplikacji (z uprawnieniami) do konta.

> [!NOTE]
> - Aplikacja nie ma żadnych innych uprawnień dostępu do subskrypcji innej niż opisane powyżej.
> - Te uprawnienia są potrzebne tylko podczas rejestrowania nowego urządzenia. Po skonfigurowaniu urządzenia można usunąć uprawnienia.


#### <a name="grant-account-permissions"></a>Udziel uprawnień konta

Dzierżawy/Administrator globalny mogą udzielić uprawnień w następujący sposób:

1. W usłudze Azure AD dzierżawca/Administrator globalny powinien przejść do **Azure Active Directory** > **użytkowników** > **ustawień użytkownika**.
2. Administrator powinien ustawić **rejestracje aplikacji** na **wartość tak**.

    ![Uprawnienia usługi Azure AD](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> Jest to ustawienie domyślne, które nie jest poufne. [Dowiedz się więcej](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

#### <a name="assign-application-developer-role"></a>Przypisywanie roli Deweloper aplikacji

Dzierżawa/Administrator globalny może przypisać rolę dewelopera aplikacji do konta. [Dowiedz się więcej](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).


## <a name="prepare-for-physical-server-assessment"></a>Przygotowanie do oceny serwera fizycznego

Aby przygotować się do oceny serwera fizycznego, należy sprawdzić ustawienia serwera fizycznego i zweryfikować ustawienia wdrożenia urządzenia:

### <a name="verify-physical-server-settings"></a>Sprawdź ustawienia serwera fizycznego

1. Sprawdź [wymagania dotyczące serwera fizycznego](migrate-support-matrix-physical.md#physical-server-requirements) na potrzeby oceny serwera.
2. Upewnij się, że [wymagane porty](migrate-support-matrix-physical.md#port-access) są otwarte na serwerach fizycznych.


### <a name="verify-appliance-settings"></a>Weryfikowanie ustawień urządzenia

Przed skonfigurowaniem urządzenia Azure Migrate i rozpoczęciem oceny w następnym samouczku Przygotuj się do wdrożenia urządzenia.

1. [Sprawdź](migrate-appliance.md#appliance---physical) wymagania dotyczące urządzeń dla serwerów fizycznych.
2. [Przejrzyj](migrate-appliance.md#url-access) adresy URL platformy Azure, do których urządzenie będzie potrzebować dostępu.
3. [Sprawdź](migrate-appliance.md#collected-data---vmware) , czy urządzenie będzie zbierane podczas odnajdywania i oceny.
4. [Zwróć uwagę](migrate-support-matrix-physical.md#port-access) na wymagania dostępu portów Ocena serwera fizycznego.


### <a name="set-up-an-account-for-physical-server-discovery"></a>Skonfiguruj konto do odnajdowania serwera fizycznego

Azure Migrate wymaga uprawnień do odnajdywania serwerów lokalnych.

- **System Windows:** Skonfiguruj konto użytkownika lokalnego na wszystkich serwerach z systemem Windows, które mają zostać uwzględnione w odnajdywaniu. Należy dodać konto użytkownika do następujących grup: — Użytkownicy zarządzania zdalnego — użytkownicy monitora wydajności — Użytkownicy dzienników wydajności
- System **Linux:** Na serwerach z systemem Linux, które mają zostać odnajdywane, jest potrzebne konto główne.

## <a name="prepare-for-physical-server-migration"></a>Przygotowanie do migracji serwera fizycznego

Zapoznaj się z wymaganiami dotyczącymi migracji serwerów fizycznych.

- [Przejrzyj](migrate-support-matrix-physical-migration.md#physical-server-requirements) wymagania dotyczące serwera fizycznego na potrzeby migracji.
- Azure Migrate: Migracja serwera używa serwera replikacji do migracji serwera fizycznego:
    - [Zapoznaj](migrate-replication-appliance.md#appliance-requirements) się z wymaganiami dotyczącymi wdrożenia urządzenia replikacji i [opcjami](migrate-replication-appliance.md#mysql-installation) instalacji bazy danych MySQL na urządzeniu.
    - Zapoznaj się z wymaganiami dotyczącymi dostępu do urządzenia do replikacji, przenosząc [adres URL](migrate-replication-appliance.md#url-access) i [port] (Migrowanie-replikacja-urządzenie. MD # port-dostęp).


## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Skonfiguruj uprawnienia konta platformy Azure.
> * Przygotowano serwery fizyczne do oceny.

Przejdź do następnego samouczka, aby utworzyć projekt Azure Migrate i ocenić serwery fizyczne do migracji na platformę Azure

> [!div class="nextstepaction"]
> [Ocenianie serwerów fizycznych](./tutorial-assess-physical.md)
