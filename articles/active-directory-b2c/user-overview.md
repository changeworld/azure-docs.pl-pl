---
title: Omówienie kont użytkowników w usłudze Azure Active Directory B2C
description: Dowiedz się więcej o typach kont użytkowników, które mogą być używane w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9630e1f23b6595ca690ecafcf0c4b9bfff603f2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78185662"
---
# <a name="overview-of-user-accounts-in-azure-active-directory-b2c"></a>Omówienie kont użytkowników w usłudze Azure Active Directory B2C

W usłudze Azure Active Directory B2C (Azure AD B2C) istnieje kilka typów kont, które można utworzyć. Udział usługi Azure Active Directory, usługi Active Directory B2B i B2C usługi Active Directory w typach kont użytkowników, które mogą być używane.

Dostępne są następujące typy kont:

- **Konto służbowe** — konto robocze może uzyskiwać dostęp do zasobów w dzierżawie i z rolą administratora, może zarządzać dzierżawami.
- **Konto gościa** — konto gościa może być tylko kontem Microsoft lub użytkownikiem usługi Azure Active Directory, który może służyć do uzyskiwania dostępu do aplikacji lub zarządzania dzierżawami.
- **Konto konsumenta** — konto konsumenta jest używane przez użytkownika aplikacji zarejestrowanych w usłudze Azure AD B2C. Konta konsumenckie mogą być tworzone przez:
  - Użytkownik przechodzący przez przepływ użytkownika rejestracji w aplikacji usługi Azure AD B2C
  - Korzystanie z interfejsu API programu Microsoft Graph
  - Korzystanie z witryny Azure Portal

## <a name="work-account"></a>Konto służbowe

Konto pracy jest tworzone w taki sam sposób dla wszystkich dzierżaw na podstawie usługi Azure AD. Aby utworzyć konto służbowe, można użyć informacji w [przewodniku Szybki start: Dodawanie nowych użytkowników do usługi Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md). Konto pracy jest tworzone przy użyciu nowego wyboru **użytkownika** w witrynie Azure portal.

Podczas dodawania nowego konta służbowego należy wziąć pod uwagę następujące ustawienia konfiguracji:

- **Nazwa** i **nazwa użytkownika** — **Właściwość Name** zawiera podane i nazwisko użytkownika. **Nazwa użytkownika** jest identyfikatorem wprowadzany przez użytkownika w celu zalogowania się. Nazwa użytkownika zawiera pełną domenę. Część nazwy domeny nazwy użytkownika musi być początkową domyślną nazwą domeny *your-domain.onmicrosoft.com*lub zweryfikowaną, niefederowaną [nazwą domeny niestandardowej,](../active-directory/fundamentals/add-custom-domain.md) taką jak *contoso.com*.
- **Profil** — konto jest skonfigurowane z profilem danych użytkownika. Istnieje możliwość wprowadzenia imienia, nazwiska, stanowiska i nazwy działu. Profil można edytować po utworzeniu konta.
- **Grupy** — grupa służy do wykonywania zadań zarządzania, takich jak przypisywanie licencji lub uprawnień do liczby użytkowników lub urządzeń jednocześnie. Nowe konto można umieścić w istniejącej [grupie](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) w dzierżawie.
- **Rola katalogu** — należy określić poziom dostępu, jaki konto użytkownika ma do zasobów w dzierżawie. Dostępne są następujące poziomy uprawnień:

    - **Użytkownik** — użytkownicy mogą uzyskiwać dostęp do przydzielonych zasobów, ale nie mogą zarządzać większością zasobów dzierżawy.
    - **Administrator globalny** — administratorzy globalni mają pełną kontrolę nad wszystkimi zasobami dzierżawy.
    - **Ograniczony administrator** — wybierz rolę administracyjną lub role dla użytkownika. Aby uzyskać więcej informacji na temat ról, które można wybrać, zobacz [Przypisywanie ról administratora w usłudze Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

### <a name="create-a-work-account"></a>Tworzenie konta służbowego

Aby utworzyć nowe konto służbowe, można użyć następujących informacji:

- [Portal Azure](../active-directory/fundamentals/add-users-azure-active-directory.md)
- [Microsoft Graph](https://docs.microsoft.com/graph/api/user-post-users?view=graph-rest-1.0)

### <a name="update-a-user-profile"></a>Aktualizowanie profilu użytkownika

Aby zaktualizować profil użytkownika, można użyć następujących informacji:

- [Portal Azure](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
- [Microsoft Graph](https://docs.microsoft.com/graph/api/user-update?view=graph-rest-1.0)

### <a name="reset-a-password-for-a-user"></a>Resetowanie hasła dla użytkownika

Aby zresetować hasło użytkownika, można użyć następujących informacji:

- [Portal Azure](../active-directory/fundamentals/active-directory-users-reset-password-azure-portal.md)
- [Microsoft Graph](https://docs.microsoft.com/graph/api/user-update?view=graph-rest-1.0)

## <a name="guest-user"></a>Użytkownik-gość

Możesz zaprosić użytkowników zewnętrznych do dzierżawy jako użytkownika-gościa. Typowy scenariusz zapraszania użytkownika-gościa do dzierżawy usługi Azure AD B2C jest udostępnianie obowiązków administracyjnych. Na przykład przy użyciu konta gościa zobacz [Właściwości użytkownika współpracy usługi Azure Active Directory B2B](../active-directory/b2b/user-properties.md).

Gdy zaprosisz użytkownika-gościa do dzierżawy, podajesz adres e-mail adresata i wiadomość opisującą zaproszenie. Łącze z zaproszeniem przenosi użytkownika na stronę zgody, na której zaznaczono przycisk **Wprowadzenie** i akceptowana jest weryfikacja uprawnień. Jeśli skrzynka odbiorcza nie jest dołączona do adresu e-mail, użytkownik może przejść do strony zgody, przechodząc do strony firmy Microsoft przy użyciu zaproszonych poświadczeń. Użytkownik jest następnie zmuszony do zrealizowania zaproszenia w taki sam sposób, jak kliknięcie linku w wiadomości e-mail. Na przykład: `https://myapps.microsoft.com/B2CTENANTNAME`.

Można również użyć [interfejsu API programu Microsoft Graph,](https://docs.microsoft.com/graph/api/invitation-post?view=graph-rest-beta) aby zaprosić użytkownika-gościa.

## <a name="consumer-user"></a>Użytkownik konsumencki

Użytkownik konsument może logować się do aplikacji zabezpieczonych przez usługę Azure AD B2C, ale nie może uzyskać dostępu do zasobów platformy Azure, takich jak witryna Azure portal. Użytkownik konsument może korzystać z konta lokalnego lub kont federacyjne, takich jak Facebook lub Twitter. Konto konsumenta jest tworzone przy użyciu [przepływu użytkownika rejestracji lub logowania,](user-flow-overview.md)przy użyciu interfejsu API programu Microsoft Graph lub przy użyciu witryny Azure portal.

Można określić dane, które są zbierane podczas tworzenia konta użytkownika konsumenta przy użyciu atrybutów użytkownika niestandardowego. Aby uzyskać więcej informacji, zobacz [Definiowanie atrybutów niestandardowych w usłudze Azure Active Directory B2C](user-flow-custom-attributes.md).

Aby uzyskać więcej informacji na temat zarządzania kontami konsumenckimi, zobacz [Zarządzanie kontami użytkowników usługi Azure AD B2C za pomocą programu Microsoft Graph](manage-user-accounts-graph-api.md).

### <a name="migrate-consumer-user-accounts"></a>Migrowanie kont użytkowników konsumenckich

Może być konieczne migrowanie istniejących kont użytkowników konsumenckich z dowolnego dostawcy tożsamości do usługi Azure AD B2C. Aby uzyskać więcej informacji, zobacz [Migrowanie użytkowników do usługi Azure AD B2C](user-migration.md).
