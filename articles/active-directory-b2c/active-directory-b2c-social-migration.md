---
title: Migrowanie użytkowników wraz z tożsamościami społecznościowymi w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Omówiono w nim podstawowe pojęcia dotyczące migracji użytkowników wraz z tożsamościami społecznościowymi do usługi Azure AD B2C przy użyciu interfejsu API programu Graph.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/03/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 12b464d9b6bd09acb9c93ab1de0ba178f28a778a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60316107"
---
# <a name="azure-active-directory-b2c-migrate-users-with-social-identities"></a>Azure Active Directory B2C: Migrowanie użytkowników z tożsamościami społecznościowymi
Planując migrację dostawcy tożsamości do usługi Azure AD B2C, konieczne może być migrowanie użytkowników wraz z tożsamościami społecznościowymi. W tym artykule wyjaśniono, jak przeprowadzić migrację istniejących kont tożsamości społecznościowych, takich jak: Kont usługi Facebook, LinkedIn i firmy Microsoft oraz Google do usługi Azure AD B2C. Ten artykuł dotyczy również do tożsamości federacyjnej, jednak te migracje są mniej typowe.

## <a name="prerequisites"></a>Wymagania wstępne
Ten artykuł jest kontynuacją artykuł dotyczący migracji użytkowników i koncentruje się na migracji tożsamości dla sieci społecznościowej. Przed rozpoczęciem przeczytaj [migracji użytkowników](active-directory-b2c-user-migration.md).

## <a name="social-identities-migration-introduction"></a>Wprowadzenie do migracji tożsamości społecznościowych

* W usłudze Azure AD B2C **kont lokalnych** nazwy logowania (adres nazwy lub adresu e-mail użytkownika) są przechowywane w `signInNames` kolekcji w rekordzie użytkownika. `signInNames` Zawiera jeden lub więcej `signInName` rekordy, które określają nazwy logowania dla użytkownika. Nazwy logowania muszą być unikatowe w dzierżawie.

* **Kont społecznościowych** tożsamości są przechowywane w `userIdentities` kolekcji. Określa wpis `issuer` (nazwa dostawcy tożsamości) takich jak facebook.com i `issuerUserId`, czyli identyfikator unikatowy użytkownika dla wystawcy. `userIdentities` Atrybut zawiera co najmniej jeden rekord tożsamości użytkownika, które określają typ konta w sieci społecznościowej i identyfikator unikatowy użytkownika z dostawcy tożsamości społecznościowych.

* **Łączenie konta lokalnego za pomocą tożsamości dla sieci społecznościowej**. Jak wspomniano wcześniej, nazwy logowania w lokalnych kont i tożsamości konta w sieci społecznościowej są przechowywane w różnych atrybutów. `signInNames` jest używana do konta lokalnego, podczas gdy `userIdentities` dla konta w sieci społecznościowej. Jedno konto usługi Azure AD B2C, może być tylko konta lokalnego, tylko konta społecznościowego lub Połącz konto lokalne z tożsamości społecznościowych w rekordzie użytkownika. To zachowanie umożliwia zarządzanie jednego konta, gdy użytkownik może zalogować się przy użyciu credential(s) konta lokalnego lub wraz z tożsamościami społecznościowymi.

* `UserIdentity` Typ — zawiera informacje o tożsamości użytkownika konta w sieci społecznościowej w dzierżawie usługi Azure AD B2C:
  * `issuer` Ciąg reprezentujący dostawcę tożsamości, która wydała identyfikator użytkownika, takie jak facebook.com.
  * `issuerUserId` Identyfikator unikatowy użytkownika używanej przez dostawcę tożsamości dla sieci społecznościowej w formacie base64.

    ```JSON
    "userIdentities": [{
          "issuer": "Facebook.com",
          "issuerUserId": "MTIzNDU2Nzg5MA=="
      }
    ]
    ```

* W zależności od dostawcy tożsamości **identyfikator użytkownika społecznościowych** jest unikatową wartość dla danego użytkownika dla aplikacji lub tworzenia konta. Konfigurowanie zasad usługi Azure AD B2C, mających taki sam identyfikator aplikacji, który wcześniej został przypisany przez dostawcę społecznościowych. Lub innej aplikacji w ramach tego samego konta rozwoju.

## <a name="use-graph-api-to-migrate-users"></a>Migrowanie użytkowników za pomocą interfejsu API programu Graph
Tworzenie konta użytkownika usługi Azure AD B2C za pośrednictwem [interfejsu API programu Graph](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet). Aby komunikować się za pomocą interfejsu API programu Graph, należy najpierw musi mieć konto usługi z uprawnieniami administracyjnymi. W usłudze Azure AD należy zarejestrować aplikację i uwierzytelniania usługi Azure AD. Poświadczenia aplikacji to identyfikator aplikacji i klucz tajny aplikacji. Aplikacja działa w swoim imieniu, nie jako użytkownik, do wywołania interfejsu API programu Graph. Postępuj zgodnie z instrukcjami w kroku 1 w [migracji użytkowników](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-user-migration) artykułu.

## <a name="required-properties"></a>Wymagane właściwości
Na poniższej liście przedstawiono właściwości, które są wymagane w przypadku tworzenia użytkownika.
* **accountEnabled** — jest to wartość true
* **displayName** — Nazwa do wyświetlenia w książce adresowej użytkownika.
* **passwordProfile** — profil hasła użytkownika. 

> [!NOTE]
> Dla konta społecznościowego tylko (bez poświadczeń kont lokalnych) nadal należy określić hasło. Usługa Azure AD B2C ignoruje hasło, które określisz dla kont społecznościowych.

* **userPrincipalName** -główna nazwa użytkownika (someuser@contoso.com). Główna nazwa użytkownika, musi zawierać jeden zweryfikowanych domen dzierżawcy. Aby określić nazwę UPN, należy wygenerować nową wartość identyfikatora GUID ZŁĄCZ.teksty z `@` i nazwa dzierżawy.
* **mailNickname** -alias poczty użytkownika. Ta wartość może być taki sam identyfikator, którego używasz dla właściwości userPrincipalName. 
* **signInNames** — co najmniej jeden rekord SignInName, które określają nazwy logowania dla użytkownika. Każda nazwa logowania musi być unikatowa we firmy/dzierżawców. Dla konta tylko w sieci społecznościowej ta właściwość może być puste.
* **userIdentities** — co najmniej jeden rekord tożsamości użytkownika, które określają ubezpieczenia konta, typ i identyfikator unikatowy użytkownika z dostawcy tożsamości społecznościowych.
* [opcjonalnie] **otherMails** — w przypadku konta w sieci społecznościowej tylko adresy e-mail użytkowników 

Aby uzyskać więcej informacji, zobacz: [Dokumentacja interfejsu API programu Graph](/previous-versions/azure/ad/graph/api/users-operations#CreateLocalAccountUser)

## <a name="migrate-social-account-only"></a>Migrowanie konta (tylko) w sieci społecznościowej
Można utworzyć tylko konta społecznościowego bez poświadczeń konta lokalnego. Wysłanie żądania POST protokołu HTTPS do interfejsu API programu Graph. Treść żądania zawiera właściwości użytkownika konta społecznościowego do utworzenia. Jako minimum należy określić wymagane właściwości. 


**POST**  https://graph.windows.net/tenant-name.onmicrosoft.com/users

Prześlij następujące dane formularza: 

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
## <a name="migrate-social-account-with-local-account"></a>Migrowanie konta społecznościowego za pomocą konta lokalnego
Aby utworzyć konto lokalne połączone wraz z tożsamościami społecznościowymi. Wysłanie żądania POST protokołu HTTPS do interfejsu API programu Graph. Treść żądania zawiera właściwości użytkownika konta społecznościowego do utworzenia. Jako minimum należy określić wymagane właściwości. 

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
### <a name="how-can-i-know-the-issuer-name"></a>Jak sprawdzić Nazwa wystawcy
Nazwa wystawcy lub nazwę dostawcy tożsamości jest skonfigurowany w zasadach. Jeśli nie znasz wartością do określenia w `issuer`, wykonaj poniższą procedurę:
1. Zaloguj się przy użyciu jednego z kont społecznościowych
2. Skopiuj z tokenu JWT `sub` wartość. `sub` Zwykle zawiera identyfikator obiektu użytkownika w usłudze Azure AD B2C. Lub w witrynie Azure portal, otwórz właściwości użytkownika i skopiuj identyfikatora obiektu.
3. Otwórz [funkcji Azure AD Graph Explorer](https://graphexplorer.azurewebsites.net)
4. Zaloguj się z administratorem.
5. Uruchom poniższe żądanie GET. Zastąp userObjectId skopiowanego Identyfikatora użytkownika. **POBIERZ** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId
6. Znajdź `userIdentities` element wewnątrz zwrot JSON z usługi Azure AD B2C.
7. [Opcjonalnie] Możesz również chcieć dekodowania `issuerUserId` wartość.

> [!NOTE]
> Użyj konta administratora dzierżawy B2C, lokalne dla dzierżawy usługi B2C. Składnia nazwy konta jest admin@tenant-name.onmicrosoft.com.

### <a name="is-it-possible-to-add-social-identity-to-an-existing-local-account"></a>Czy jest możliwe dodawanie tożsamości dla sieci społecznościowej do istniejącego konta lokalnego
Tak. Po utworzeniu konta lokalnego, można dodać tożsamości społecznościowych. Uruchom żądanie PATCH protokołu HTTPS. Zastąp userObjectId identyfikator użytkownika, które chcesz zaktualizować. 

**POPRAWKI** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId

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

### <a name="is-it-possible-to-add-multiple-social-identities"></a>Czy jest możliwe dodać wiele tożsamości społecznościowych?
Tak. Można dodać wiele tożsamości społecznościowych dla jednego konta usługi Azure AD B2C. Uruchom żądanie PATCH protokołu HTTPS. Zamień userObjectId identyfikatora użytkownika. 

**POPRAWKI** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId

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

## <a name="optional-user-migration-application-sample"></a>[Opcjonalnie] Przykład aplikacji migracji użytkowników
[Pobieranie i uruchamianie przykładowej aplikacji w wersji 2](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-user-migration). Przykładowa aplikacja w wersji 2 używa pliku JSON, który zawiera dane użytkownika fikcyjnego z rolą, w tym: konto lokalne, konta w sieci społecznościowej i tożsamości lokalne i społecznościowych w ramach jednego konta.  Aby edytować plik JSON, otwórz `AADB2C.UserMigration.sln` rozwiązania Visual Studio. W `AADB2C.UserMigration` otwarty projekt `UsersData.json` pliku. Plik zawiera listę jednostek użytkownika. Każda jednostka użytkownika ma następujące właściwości:
* **signInName** — w przypadku lokalnego konta, adres e-mail logowania
* **displayName** — Nazwa wyświetlana użytkownika
* **Imię** — imię użytkownika
* **Nazwisko** -nazwisko użytkownika
* **hasło** dla lokalnego konta i hasło użytkownika (może być puste)
* **Wystawca** — w przypadku konta w sieci społecznościowej, nazwa dostawcy tożsamości
* **issuerUserId** — w przypadku konta w sieci społecznościowej, identyfikator unikatowy użytkownika używanej przez dostawcę tożsamości dla sieci społecznościowej. Wartość powinna być w postaci zwykłego tekstu. Przykładowa aplikacja koduje tę wartość w formacie Base64.
* **adres e-mail** dla konta społecznościowego tylko (nie połączone) użytkownika adresu e-mail

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
> Jeśli nie zaktualizujesz plik UsersData.json w próbce ze swoimi danymi, możesz się zalogować przy użyciu poświadczeń konta lokalnego próbki, ale nie z przykładami konta społecznościowego. Aby przeprowadzić migrację kont społecznościowych, podaj rzeczywiste dane.

Aby uzyskać więcej informacji jak korzystać z przykładowej aplikacji, zobacz [usługi Azure Active Directory B2C: Migracja użytkowników](active-directory-b2c-user-migration.md)
