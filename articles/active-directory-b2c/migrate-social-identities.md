---
title: Migrowanie użytkowników z tożsamościami społecznościowymi
titleSuffix: Azure AD B2C
description: Zapoznaj się z podstawowymi pojęciami dotyczącymi migracji użytkowników z tożsamościami społecznościowymi w Azure AD B2C przy użyciu interfejs API programu Graph.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/03/2018
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 96bb4ef11b960a517054009f14f18b7af23fac14
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76848839"
---
# <a name="azure-active-directory-b2c-migrate-users-with-social-identities"></a>Azure Active Directory B2C: Migrowanie użytkowników przy użyciu tożsamości społecznościowych
Planując migrację dostawcy tożsamości do Azure AD B2C, może być również konieczne przeprowadzenie migracji użytkowników z tożsamościami społecznościowymi. W tym artykule opisano sposób migrowania istniejących kont tożsamości społecznościowych, takich jak Facebook, LinkedIn, Microsoft i Google, do Azure AD B2C. Ten artykuł dotyczy również tożsamości federacyjnych, ale te migracje są mniej popularne. W pozostałej części tego artykułu należy wziąć pod uwagę wszystkie kwestie dotyczące kont społecznościowych, które mają zastosowanie również do innych typów kont federacyjnych.

## <a name="prerequisites"></a>Wymagania wstępne
Ten artykuł stanowi kontynuację artykułu dotyczącego migracji użytkowników i koncentruje się na migracji tożsamości społecznościowej. Przed rozpoczęciem należy odczytać informacje o [migracji użytkowników](user-migration.md).

## <a name="social-identities-migration-introduction"></a>Wprowadzenie do migracji tożsamości społecznościowych

* W Azure AD B2C nazwy logowania **kont lokalnych** (nazwa użytkownika lub adres e-mail) są przechowywane w kolekcji `signInNames` w rekordzie użytkownika. `signInNames` zawiera co najmniej jeden rekord `signInName`, który określa nazwy logowania dla użytkownika. Każda nazwa logowania musi być unikatowa w dzierżawie.

* Tożsamości **kont społecznościowych** są przechowywane w kolekcji `userIdentities`. Wpis określa `issuer` (nazwę dostawcy tożsamości), taki jak facebook.com i `issuerUserId`, który jest unikatowym identyfikatorem użytkownika dla wystawcy. Atrybut `userIdentities` zawiera co najmniej jeden rekord tożsamość użytkownika, który określa typ konta społecznościowego i unikatowy identyfikator użytkownika od dostawcy tożsamości społecznościowej.

* **Połącz konto lokalne z tożsamością społecznościową**. Jak wspomniano, nazwy logowania do konta lokalnego i tożsamości konta społecznościowego są przechowywane w różnych atrybutach. `signInNames` jest używany dla konta lokalnego, podczas gdy `userIdentities` jest używany w przypadku kont społecznościowych. Pojedyncze konto Azure AD B2C może dotyczyć tylko konta lokalnego, tylko konta społecznościowego lub łączenia konta lokalnego z co najmniej jedną tożsamością społecznościową w jednym rekordzie użytkownika. To zachowanie umożliwia zarządzanie pojedynczym kontem, podczas gdy użytkownik może zalogować się przy użyciu poświadczeń konta lokalnego lub tożsamości społecznościowych.

* Typ `UserIdentity` — zawiera informacje o tożsamości użytkownika konta społecznościowego w dzierżawie Azure AD B2C:
  * `issuer` reprezentację ciągu dostawcy tożsamości, który wystawił identyfikator użytkownika, na przykład facebook.com.
  * `issuerUserId` unikatowy identyfikator użytkownika używany przez dostawcę tożsamości społecznościowej w formacie zakodowanym algorytmem Base64.

    ```JSON
    "userIdentities": [{
          "issuer": "Facebook.com",
          "issuerUserId": "MTIzNDU2Nzg5MA=="
      }
    ]
    ```

* W zależności od dostawcy tożsamości **Identyfikator użytkownika wystawcy** jest unikatową wartością dla danego użytkownika dla aplikacji lub konta deweloperskiego. Skonfiguruj zasady Azure AD B2C przy użyciu tego samego identyfikatora aplikacji, który został wcześniej przypisany przez dostawcę usług społecznościowych lub inną aplikację w ramach tego samego konta deweloperskiego.

## <a name="use-graph-api-to-migrate-users"></a>Użyj interfejs API programu Graph do migracji użytkowników
Utwórz konto użytkownika Azure AD B2C za pośrednictwem [interfejs API programu Graph](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet).
Aby móc komunikować się z interfejs API programu Graph, musisz najpierw mieć konto usługi z uprawnieniami administracyjnymi. W usłudze Azure AD rejestrujesz aplikację i uwierzytelnianie w usłudze Azure AD. Poświadczenia aplikacji to identyfikator aplikacji i klucz tajny aplikacji. Aplikacja działa jako sama, a nie jako użytkownik, aby wywołać interfejs API programu Graph. Postępuj zgodnie z instrukcjami podanymi w kroku 1 w artykule dotyczącym [migracji użytkowników](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-user-migration) .

## <a name="required-properties"></a>Wymagane właściwości
Poniższa lista zawiera właściwości, które są wymagane podczas tworzenia użytkownika.
* **accountEnabled** — prawda
* **DisplayName** — nazwa wyświetlana w książce adresowej dla użytkownika.
* **passwordProfile** — profil hasła użytkownika.

> [!NOTE]
> W przypadku kont społecznościowych (bez poświadczeń konta lokalnego) nadal należy określić hasło. Azure AD B2C ignoruje hasło określone dla kont społecznościowych.

* **userPrincipalName** — główna nazwa użytkownika (someuser@contoso.com). Główna nazwa użytkownika musi zawierać jedną z zweryfikowanych domen dla dzierżawy. Aby określić nazwę UPN, wygeneruj nową wartość identyfikatora GUID, Połącz z `@` i swoją nazwą dzierżawy.
* **mailNickname** — alias poczty dla użytkownika. Ta wartość może być tym samym IDENTYFIKATORem, który jest używany dla elementu userPrincipalName.
* **signInNames** — co najmniej jeden rekord SignInName, który określa nazwy logowania dla użytkownika. Każda nazwa logowania musi być unikatowa w całej firmie/dzierżawie. Tylko dla konta społecznościowego ta właściwość może pozostać pusta.
* **userIdentities** — co najmniej jeden rekord tożsamość użytkownika, który określa typ konta społecznościowego i unikatowy identyfikator użytkownika od dostawcy tożsamości społecznościowej.
* obowiązkowe **otherMails** — tylko w przypadku konta społecznościowego adresy e-mail użytkownika

Aby uzyskać więcej informacji, zobacz: [Informacje o interfejs API programu Graph](/previous-versions/azure/ad/graph/api/users-operations#CreateLocalAccountUser)

## <a name="migrate-social-account-only"></a>Migrowanie konta społecznościowego (tylko)
Aby utworzyć tylko konto społecznościowe, bez poświadczeń konta lokalnego Wyślij żądanie HTTPS POST do interfejs API programu Graph. Treść żądania zawiera właściwości użytkownika konta społecznościowego, który ma zostać utworzony. Należy określić co najmniej wymagane właściwości.


**POST**  https://graph.windows.net/tenant-name.onmicrosoft.com/users

Prześlij następujący formularz — dane:

```JSON
{
    "objectId": null,
    "accountEnabled": true,
    "mailNickname": "c8c3d3b8-60cf-4c76-9aa7-eb3235b190c8",
    "signInNames": [],
    "creationType": null,
    "displayName": "Sara Bell",
    "givenName": "Sara",
    "surname": "Bell",
    "passwordProfile": {
        "password": "Test1234",
        "forceChangePasswordNextLogin": false
    },
    "passwordPolicies": null,
    "userIdentities": [{
            "issuer": "Facebook.com",
            "issuerUserId": "MTIzNDU2Nzg5MA=="
        }
    ],
    "otherMails": ["sara@live.com"],
    "userPrincipalName": "c8c3d3b8-60cf-4c76-9aa7-eb3235b190c8@tenant-name.onmicrosoft.com"
}
```
## <a name="migrate-social-account-with-local-account"></a>Migrowanie konta społecznościowego przy użyciu konta lokalnego
Aby utworzyć połączone konto lokalne z tożsamościami społecznościowymi, Wyślij żądanie HTTPS POST do interfejs API programu Graph. Treść żądania zawiera właściwości użytkownika konta społecznościowego do utworzenia, w tym nazwę logowania dla konta lokalnego. Należy określić co najmniej wymagane właściwości.

**POST**  https://graph.windows.net/tenant-name.onmicrosoft.com/users

Prześlij następujące dane formularza:

```JSON
{
    "objectId": null,
    "accountEnabled": true,
    "mailNickname": "5164db16-3eee-4629-bfda-dcc3326790e9",
    "signInNames": [{
            "type": "emailAddress",
            "value": "david@contoso.com"
        }
    ],
    "creationType": "LocalAccount",
    "displayName": "David Hor",
    "givenName": "David",
    "surname": "Hor",
    "passwordProfile": {
        "password": "1234567",
        "forceChangePasswordNextLogin": false
    },
    "passwordPolicies": "DisablePasswordExpiration,DisableStrongPassword",
    "userIdentities": [{
            "issuer": "contoso.com",
            "issuerUserId": "ZGF2aWRAY29udG9zby5jb20="
        }
    ],
    "otherMails": [],
    "userPrincipalName": "5164db16-3eee-4629-bfda-dcc3326790e9@tenant-name.onmicrosoft.com"
}
```

## <a name="frequently-asked-questions"></a>Często zadawane pytania
### <a name="how-can-i-know-the-issuer-name"></a>Jak mogę znać nazwę wystawcy?
Nazwa wystawcy lub nazwa dostawcy tożsamości jest skonfigurowana w ramach zasad. Jeśli nie znasz wartości do określenia w `issuer`, wykonaj następującą procedurę:
1. Zaloguj się przy użyciu jednego z kont społecznościowych
2. Z tokenu JWT skopiuj wartość `sub`. `sub` zwykle zawiera identyfikator obiektu użytkownika w Azure AD B2C. Lub z Azure Portal Otwórz właściwości użytkownika i skopiuj identyfikator obiektu.
3. Otwórz [Eksploratora grafów usługi Azure AD](https://graphexplorer.azurewebsites.net)
4. Zaloguj się przy użyciu administratora.
5. Uruchom następujące żądanie GET. Zastąp userObjectId IDENTYFIKATORem użytkownika, który został skopiowany. **Pobierz** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId
6. Znajdź element `userIdentities` w elemencie Return JSON z Azure AD B2C.
7. Obowiązkowe Możesz również zakodować wartość `issuerUserId`.

> [!NOTE]
> Użyj konta administratora dzierżawy B2C, które jest lokalne dla dzierżawy B2C. Składnia nazwy konta jest admin@tenant-name.onmicrosoft.com.

### <a name="is-it-possible-to-add-a-social-identity-to-an-existing-user"></a>Czy można dodać tożsamość społecznościową do istniejącego użytkownika?
Tak. Tożsamość społecznościową można dodać po utworzeniu konta Azure AD B2C (niezależnie od tego, czy jest ono kontem lokalnym czy społecznościowym lub ich kombinacją). Uruchom żądanie poprawki HTTPS. Zastąp userObjectId IDENTYFIKATORem użytkownika, który chcesz zaktualizować.

**Poprawka** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId

Prześlij następujące dane formularza:

```JSON
{
    "userIdentities": [
        {
            "issuer": "Facebook.com",
            "issuerUserId": "MTIzNDU2Nzg5MA=="
        }
    ]
}
```

### <a name="is-it-possible-to-add-multiple-social-identities"></a>Czy jest możliwe dodanie wielu tożsamości społecznościowych?
Tak. Można dodać wiele tożsamości społecznościowych dla jednego konta Azure AD B2C. Uruchom żądanie poprawki HTTPS. Zastąp userObjectId IDENTYFIKATORem użytkownika.

**Poprawka** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId

Prześlij następujące dane formularza:

```JSON
{
    "userIdentities": [
        {
            "issuer": "google.com",
            "issuerUserId": "MjQzMjE2NTc4NTQ="
        },
        {
            "issuer": "facebook.com",
            "issuerUserId": "MTIzNDU2Nzg5MA=="
        }
    ]
}
```

## <a name="optional-user-migration-application-sample"></a>Obowiązkowe Przykład aplikacji do migracji użytkowników
[Pobierz i uruchom przykładową aplikację w wersji 2](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-user-migration). Przykładowa aplikacja v2 używa pliku JSON, który zawiera fikcyjne dane użytkownika, w tym: konto lokalne, konto społecznościowe i lokalne & tożsamości społecznościowe na pojedynczym koncie.  Aby edytować plik JSON, Otwórz rozwiązanie `AADB2C.UserMigration.sln` Visual Studio. W projekcie `AADB2C.UserMigration` Otwórz plik `UsersData.json`. Plik zawiera listę jednostek użytkownika. Każda jednostka użytkownika ma następujące właściwości:
* **signInName** — w przypadku konta lokalnego adres e-mail do zalogowania
* **DisplayName** — nazwa wyświetlana użytkownika
* **FirstName** — Imię użytkownika
* **nazwisko** -nazwisko użytkownika
* **hasło** W przypadku konta lokalnego hasło użytkownika (może być puste)
* **wystawca** — konto społecznościowe, nazwa dostawcy tożsamości
* **issuerUserId** — dla konta społecznościowego unikatowy identyfikator użytkownika używany przez dostawcę tożsamości społecznościowej. Wartość powinna być w postaci zwykłego tekstu. Przykładowa aplikacja koduje tę wartość na Base64.
* **adres e-mail** Tylko dla konta społecznościowego (niepołączone), adres e-mail użytkownika

```JSON
{
  "userType": "emailAddress",
  "Users": [
    {
      // Local account only
      "signInName": "James@contoso.com",
      "displayName": "James Martin",
      "firstName": "James",
      "lastName": "Martin",
      "password": "Pass!w0rd"
    },
    {
      // Social account only
      "issuer": "Facebook.com",
      "issuerUserId": "1234567890",
      "email": "sara@contoso.com",
      "displayName": "Sara Bell",
      "firstName": "Sara",
      "lastName": "Bell"
    },
    {
      // Combine local account with social identity
      "signInName": "david@contoso.com",
      "issuer": "Facebook.com",
      "issuerUserId": "0987654321",
      "displayName": "David Hor",
      "firstName": "David",
      "lastName": "Hor",
      "password": "Pass!w0rd"
    }
  ]
}
```

> [!NOTE]
> Jeśli plik UsersData. JSON nie zostanie zaktualizowany w przykładzie z danymi, możesz zalogować się przy użyciu poświadczeń konta lokalnego, ale nie z przykładami konta społecznościowego. Aby przeprowadzić migrację kont społecznościowych, podaj prawdziwe dane.

Aby uzyskać więcej informacji, jak korzystać z przykładowej aplikacji, zobacz [Azure Active Directory B2C: migracja użytkownika](user-migration.md)
