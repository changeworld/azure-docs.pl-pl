---
title: Omówienie użytkownika konta w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Więcej informacji na temat kont użytkowników w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 42a38b47f9e49652b5fe80aa5bbd0047dc7d1e74
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66511965"
---
# <a name="overview-of-user-accounts-in-azure-active-directory-b2c"></a>Omówienie kont użytkowników w usłudze Azure Active Directory B2C

W usłudze Azure Active Directory (Azure AD) B2C można użyć różnych typów kont. Azure Active Directory B2B usługi Azure Active Directory i Azure Active Directory B2C udostępniać w typach kont użytkowników, których można użyć.

Dostępne są następujące typy kont:

- **Konto służbowe** — konto służbowe ma dostęp do zasobów w dzierżawie, a za pomocą konta administratora roli, można zarządzać dzierżaw.
- **Konto gościa** — konto gościa może składać się z konta Microsoft lub przez użytkownika usługi Azure Active Directory, który może służyć do uzyskania dostępu do aplikacji lub zarządzania dzierżaw. 
- **Konto konsumenta** — konto konsumenta, jest tworzony za pośrednictwem przepływu rejestracji użytkownika w aplikacji usługi Azure AD B2C, czy przy użyciu interfejsu API programu Graph usługi Azure AD i jest używany przez użytkowników aplikacji, które są zarejestrowane w usłudze Azure AD B2C. 

## <a name="work-account"></a>Konto służbowe

Konto służbowe jest tworzona taki sam sposób dla wszystkich dzierżawców oparte na usłudze Azure AD. Aby utworzyć konto służbowe, należy użyć informacji w [Szybki Start: Dodawanie nowych użytkowników do usługi Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md). Konto służbowe jest tworzony przy użyciu **nowego użytkownika** wybór w witrynie Azure portal.

Po dodaniu nowego konta służbowego, należy wziąć pod uwagę następujące ustawienia konfiguracji:

- **Nazwa** i **nazwy użytkownika** — **nazwa** właściwość zawiera dany i nazwisko użytkownika. **Nazwa_użytkownika** to identyfikator wprowadzany przez użytkownika do logowania. Nazwa użytkownika zawiera pełną domeny. Część nazwy domeny nazwa użytkownika musi być nazwa wstępnej domyślnej domeny *twojadomena.onmicrosoft.com*, lub zweryfikowaną, niefederacyjną [domeny niestandardowej](../active-directory/fundamentals/add-custom-domain.md) nazwy, takie jak  *contoso.com*.
- **Profil** — konto jest skonfigurowane z profilem danych użytkownika. Masz możliwość Wprowadź imię, nazwisko, stanowisko i dział nazwy. Profil, który można edytować, po utworzeniu konta.
- **Grupy** — użyć grupy do wykonywania zadań administracyjnych, takich jak przypisywanie licencji lub uprawnień do liczby użytkowników lub urządzeń naraz. Nowe konto można umieścić w istniejącej [grupy](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) w dzierżawie. 
- **Rola katalogu** -należy określić poziom dostępu do zasobów w Twojej dzierżawie konta użytkownika. Dostępne są następujące poziomy uprawnień:

    - **Użytkownik** — użytkownicy mogą uzyskiwać dostęp do przypisanych zasobów, ale nie można zarządzać większością zasobów dzierżawy.
    - **Administrator globalny** — Administratorzy globalni mają pełną kontrolę nad wszystkich zasobów dzierżawy.
    - **Ograniczony administrator** — wybierz role dla użytkownika lub rolę administracyjną. Aby uzyskać więcej informacji na temat ról, które można wybrać, zobacz [przypisywanie ról administratorów w usłudze Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md). 

### <a name="create-a-work-account"></a>Utworzyć konto służbowe

Aby utworzyć nowe konto służbowe, można użyć następujących informacji:

- [Azure Portal](../active-directory/fundamentals/add-users-azure-active-directory.md)
- [Microsoft Graph](https://docs.microsoft.com/graph/api/user-post-users?view=graph-rest-1.0)

### <a name="update-a-user-profile"></a>Aktualizowanie profilu użytkownika

Można zaktualizować profilu użytkownika, można użyć następujących informacji:

- [Azure Portal](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
- [Microsoft Graph](https://docs.microsoft.com/graph/api/user-update?view=graph-rest-1.0)

### <a name="reset-a-password-for-a-user"></a>Resetowanie hasła dla użytkownika

Do zresetowania hasła użytkownika, można użyć następujących informacji: 

- [Azure Portal](../active-directory/fundamentals/active-directory-users-reset-password-azure-portal.md)
- [Microsoft Graph](https://docs.microsoft.com/graph/api/user-update?view=graph-rest-1.0)

## <a name="guest-user"></a>Użytkownik-Gość

Możesz zaprosić użytkowników zewnętrznych do dzierżawy jako Gość. Typowy scenariusz użycia zaproszenie użytkownika-gościa do dzierżawy usługi Azure AD B2C jest udostępnianie obowiązków administracyjnych. Na przykład za pomocą konta gościa, zobacz [właściwości użytkownika współpracy B2B usługi Azure Active Directory](../active-directory/b2b/user-properties.md).

Zaproszenie użytkownika-gościa do swojej dzierżawy, możesz podać adres e-mail odbiorcy i komunikat opisujący zaproszenia. Zaproszenie łącze powoduje otwarcie strona zgody użytkownika gdzie **wprowadzenie** przycisk jest zaznaczony i zaakceptowaniu Przegląd uprawnień. Jeśli skrzynki odbiorczej nie jest dołączony do adresu e-mail, użytkownik może przejść do strony zgody, przechodząc do strony firmy Microsoft przy użyciu poświadczeń, które otrzymało zaproszenie. Użytkownik jest następnie zmuszony do zrealizowania zaproszenia taki sam sposób jak kliknięcie linku w wiadomości e-mail. Na przykład: `https://myapps.microsoft.com/B2CTENANTNAME`.

Można również użyć [interfejsu API Microsoft Graph](https://docs.microsoft.com/graph/api/invitation-post?view=graph-rest-beta) można zaprosić użytkownika-gościa.

## <a name="consumer-user"></a>Konsument użytkownika

Użytkownik konsumenta zalogować się do aplikacji zabezpieczonej przez usługi Azure AD B2C, ale nie dostęp do zasobów platformy Azure, takich jak witryny Azure portal.  Użytkownik klienta może użyć lokalnego konta lub kont federacyjnych, takich jak Facebook lub Twitter. Konto konsumenta jest tworzona przy użyciu [przepływu rejestracji lub logowania użytkownika](../active-directory-b2c/active-directory-b2c-reference-policies.md).

Można określić dane, które są zbierane, gdy konsument konto użytkownika jest tworzona przy użyciu atrybutów niestandardowych użytkownika. Aby uzyskać więcej informacji, zobacz [definiowania atrybutów niestandardowych w usłudze Azure Active Directory B2C](../active-directory-b2c/active-directory-b2c-reference-custom-attr.md).

Skorzystaj z informacji w **tworzenia kont użytkowników konsumenta** części [za pomocą interfejsu API programu Graph usługi Azure AD](active-directory-b2c-devquickstarts-graph-dotnet.md) utworzyć konto usługi Azure AD B2C konsumenta. Umożliwia również informacje zawarte w **zaktualizować konta użytkowników konsumenta** sekcji, w tym samym artykule, aby zarządzać właściwościami konta.

### <a name="migrate-consumer-user-accounts"></a>Migrowanie kont użytkowników konsumenta

Może zajść potrzeba migracji istniejących kont użytkowników klientów z dowolnego dostawcy tożsamości do usługi Azure AD B2C. Aby uzyskać więcej informacji, zobacz [migracji użytkowników](active-directory-b2c-user-migration.md) lub [migrowanie użytkowników wraz z tożsamościami społecznościowymi](active-directory-b2c-social-migration.md).
