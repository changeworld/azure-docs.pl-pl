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
ms.openlocfilehash: 363549662a17a87513c8426347909142ee405cae
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196396"
---
# <a name="prepare-for-assessment-and-migration-of-physical-servers-to-azure"></a>Przygotowanie do oceny i migracji serwerów fizycznych na platformę Azure

W tym artykule opisano sposób przygotowania do oceny lokalnych serwerów fizycznych za pomocą [Azure Migrate](migrate-services-overview.md).

[Azure Migrate](migrate-overview.md) udostępnia centrum narzędzi, które ułatwiają odnajdywanie, ocenianie i Migrowanie aplikacji, infrastruktury i obciążeń do Microsoft Azure. Centrum obejmuje narzędzia Azure Migrate i oferty niezależnych dostawców oprogramowania (ISV) innych firm. 

Ten samouczek jest pierwszą częścią serii, która pokazuje, jak oceniać serwery fizyczne przy użyciu Azure Migrate. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przygotuj platformę Azure. Skonfiguruj uprawnienia dla konta i zasobów platformy Azure do pracy z Azure Migrate.
> * Przygotuj lokalne serwery fizyczne do oceny serwera.


> [!NOTE]
> Samouczki przedstawiają najprostszą ścieżkę wdrożenia dla scenariusza, dzięki czemu można szybko skonfigurować weryfikację koncepcji. Samouczki korzystają z domyślnych opcji, jeśli jest to możliwe, i nie wyświetlają wszystkich możliwych ustawień i ścieżek. Aby uzyskać szczegółowe instrukcje, zapoznaj się z informacjami na temat oceny serwerów fizycznych.


Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).


## <a name="prepare-azure"></a>Przygotowywanie platformy Azure

### <a name="azure-permissions"></a>Uprawnienia platformy Azure

Musisz skonfigurować uprawnienia do wdrożenia Azure Migrate.

- Uprawnienia do konta platformy Azure w celu utworzenia projektu Azure Migrate.
- Uprawnienia dla Twojego konta, które zarejestrują urządzenie Azure Migrate. Urządzenie jest używane na potrzeby odnajdywania i migracji funkcji Hyper-V. Podczas rejestracji urządzenia Azure Migrate tworzy dwie aplikacje Azure Active Directory (Azure AD), które w unikatowy sposób identyfikują urządzenie:
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

Można przypisać uprawnienia Azure Migrate do tworzenia aplikacji usługi Azure AD tworzonych podczas rejestracji urządzenia przy użyciu jednej z następujących metod:

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


## <a name="prepare-for-physical-server-assessment"></a>Przygotowanie do oceny serwera fizycznego

Aby przygotować się do oceny serwera fizycznego, należy sprawdzić ustawienia serwera fizycznego i zweryfikować ustawienia wdrożenia urządzenia:

### <a name="verify-physical-server-settings"></a>Sprawdź ustawienia serwera fizycznego

1. Sprawdź [wymagania dotyczące serwera fizycznego](migrate-support-matrix-physical.md#assessment-physical-server-requirements) na potrzeby oceny serwera.
2. Upewnij się, że [wymagane porty](migrate-support-matrix-physical.md#assessment-port-requirements) są otwarte na serwerach fizycznych.


### <a name="verify-appliance-settings"></a>Weryfikowanie ustawień urządzenia

Przed skonfigurowaniem urządzenia Azure Migrate i rozpoczęciem oceny w następnym samouczku Przygotuj się do wdrożenia urządzenia.

1. [Sprawdź](migrate-support-matrix-physical.md#assessment-appliance-requirements) wymagania dotyczące urządzeń.
2. [Przejrzyj](migrate-support-matrix-physical.md#assessment-appliance-url-access) adresy URL platformy Azure, do których urządzenie będzie potrzebować dostępu.
3. Przejrzyj dane, które urządzenie będzie zbierać podczas odnajdywania i oceny.
4. [Zwróć uwagę](migrate-support-matrix-physical.md#assessment-port-requirements) na wymagania dotyczące dostępu do portów dla urządzenia.


### <a name="set-up-an-account-for-physical-server-discovery"></a>Skonfiguruj konto do odnajdowania serwera fizycznego

Azure Migrate wymaga uprawnień do odnajdywania serwerów lokalnych.

- **System Windows:** Skonfiguruj konto użytkownika lokalnego na wszystkich serwerach z systemem Windows, które mają zostać uwzględnione w odnajdywaniu. Należy dodać konto użytkownika do następujących grup: — Użytkownicy zarządzania zdalnego — użytkownicy monitora wydajności — Użytkownicy dzienników wydajności
- System **Linux:** Na serwerach z systemem Linux, które mają zostać odnajdywane, jest potrzebne konto główne.


## <a name="next-steps"></a>Kolejne kroki

W tym samouczku zostaną wykonane następujące czynności:

> [!div class="checklist"]
> * Skonfiguruj uprawnienia konta platformy Azure.
> * Przygotowano serwery fizyczne do oceny.

Przejdź do następnego samouczka, aby utworzyć projekt Azure Migrate i ocenić serwery fizyczne do migracji na platformę Azure

> [!div class="nextstepaction"]
> [Ocenianie serwerów fizycznych](./tutorial-assess-physical.md)
