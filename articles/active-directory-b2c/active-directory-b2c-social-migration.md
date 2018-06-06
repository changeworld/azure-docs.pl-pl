---
title: Migracja użytkowników z społecznościowych tożsamości w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Omówiono w nim podstawowe koncepcje migracji użytkowników z społecznościowych tożsamości w usłudze Azure AD B2C, za pomocą interfejsu API programu Graph.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 03/03/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 7c83afba1f027771b3407aecf94fefffdc951664
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34710563"
---
# <a name="azure-active-directory-b2c-migrate-users-with-social-identities"></a>Usługa Azure Active Directory B2C: Migracja użytkowników z tożsamościami społecznościowych
Podczas planowania migracji do usługi Azure AD B2C dostawcy tożsamości, konieczne może być migracja użytkowników z tożsamościami społecznościowych. W tym artykule opisano sposób migracji istniejących kont społecznościowych tożsamości, takich jak: usługi Facebook, LinkedIn firmy Microsoft i Google kont do usługi Azure AD B2C. Ten artykuł dotyczy również tożsamości federacyjnych, jednak te migracji są mniej typowe.

## <a name="prerequisites"></a>Wymagania wstępne
Ten artykuł stanowi kontynuację artykułu migracji użytkowników i koncentruje się na tożsamości społecznościowych migracji. Przed rozpoczęciem przeczytaj [migracji użytkownika](active-directory-b2c-user-migration.md).

## <a name="social-identities-migration-introduction"></a>Wprowadzenie migracji społecznościowych tożsamości

* W usłudze Azure AD B2C **kont lokalnych** nazwy logowania (użytkownika nazwy lub adresu e-mail) są przechowywane w `signInNames` kolekcji w rekordzie użytkownika. `signInNames` Zawiera jeden lub więcej `signInName` rekordów, które określają nazwy logowania dla użytkownika. Każda nazwa logowania musi być unikatowa we dzierżawcy.

* **Kont społecznościowych** tożsamości są przechowywane w `userIdentities` kolekcji. Określa wpis `issuer` (nazwa dostawcy tożsamości) takie jak facebook.com i `issuerUserId`, czyli identyfikatora unikatowego użytkownika wystawcy. `userIdentities` Atrybut zawiera co najmniej jeden rekord tożsamości użytkownika, które określają typ konta społecznościowych i identyfikatora unikatowego użytkownika przez dostawcę tożsamości społecznościowych.

* **Połączyć konto lokalne z tożsamością społecznościowych**. Jak wspomniano, nazwy logowania w lokalnych kont i tożsamości społecznościowych konta są przechowywane w różnych atrybutów. `signInNames` jest używana dla konta lokalnego, podczas gdy `userIdentities` dla konta społecznościowych. Jednego konta usługi Azure AD B2C, może być tylko konta lokalnego, tylko konta społecznościowych lub połączyć konto lokalne z społecznościowych tożsamości w rekordzie jednego użytkownika. To zachowanie umożliwia zarządzanie jednego konta, gdy użytkownik może zalogować się za pomocą konta lokalnego credential(s) lub za pomocą tożsamości społecznościowych.

* `UserIdentity` Wpisz — zawiera informacje o tożsamości użytkownika społecznościowych konta dzierżawy usługi Azure AD B2C:
    * `issuer` Reprezentacja ciągu dostawcy tożsamości, który wystawił identyfikator użytkownika, takie jak facebook.com.
    * `issuerUserId` Identyfikator unikatowy użytkownika używane przez dostawcę tożsamości społecznościowych w formacie base64.

    ```JSON
    "userIdentities": [{
            "issuer": "Facebook.com",
            "issuerUserId": "MTIzNDU2Nzg5MA=="
        }
    ]
    ```

* W zależności od dostawcy tożsamości **identyfikator użytkownika społecznościowych** jest unikatową wartość dla danego użytkownika dla aplikacji lub tworzenia konta. Konfigurowanie zasad usługi Azure AD B2C mających taki sam identyfikator aplikacji, który został już wcześniej przypisany przez dostawcę społecznościowych. Lub innej aplikacji w ramach tego samego konta programowanie.

## <a name="use-graph-api-to-migrate-users"></a>Migracja użytkowników za pomocą interfejsu API Graph
Tworzenie konta użytkownika usługi Azure AD B2C za pośrednictwem [interfejsu API programu Graph](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet). Do komunikacji z interfejsu API programu Graph, należy najpierw musi mieć konto usługi z uprawnieniami administracyjnymi. W usłudze Azure AD należy zarejestrować aplikację i uwierzytelniania do usługi Azure AD. Poświadczenia aplikacji są aplikacji identyfikator i klucz tajny aplikacji. Aplikacja działa jako sam nie jako użytkownik, do wywołania interfejsu API programu Graph. Postępuj zgodnie z instrukcjami w kroku 1 w [migracji użytkowników](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-user-migration#step-1-use-graph-api-to-migrate-users) artykułu.

## <a name="required-properties"></a>Wymagane właściwości
Na poniższej liście przedstawiono właściwości, które są wymagane w przypadku tworzenia użytkownika.
* **accountEnabled** — PRAWDA
* **Nazwa wyświetlana** — Nazwa do wyświetlenia w książce adresowej użytkownika.
* **passwordProfile** -profil hasła dla użytkownika. 

> [!NOTE]
> Dla konta społecznościowych tylko (bez poświadczeń kont lokalnych) nadal należy określić hasło. Usługa Azure AD B2C ignoruje hasło określone dla kont społecznościowych.

* **userPrincipalName** — nazwa główna użytkownika (someuser@contoso.com). Główna nazwa użytkownika musi zawierać jeden z zweryfikowanych domen dzierżawy. Aby określić nazwę UPN, generowanie nowych wartości identyfikatora GUID ZŁĄCZ.teksty z `@` i nazwa dzierżawy.
* **mailNickname** -alias poczty dla użytkownika. Ta wartość może być taki sam identyfikator, której użyjesz dla właściwości userPrincipalName. 
* **signInNames** -rekordy SignInName, które określają nazwy logowania dla użytkownika. Każda nazwa logowania musi być unikatowa we firmy/dzierżawców. Tylko konta społecznościowych ta właściwość może być puste.
* **userIdentities** — rejestruje tożsamości użytkownika, określające społecznego konta, typ i identyfikator unikatowy użytkownika od dostawcy tożsamości społecznościowych.
* [opcjonalnie] **otherMails** — społecznościowych przeznaczone tylko dla konta, adresy e-mail użytkownika 

Aby uzyskać więcej informacji, zobacz: [dokumentacja interfejsu API programu Graph](https://msdn.microsoft.com/library/azure/ad/graph/api/users-operations#CreateLocalAccountUser)

## <a name="migrate-social-account-only"></a>Migrowanie kont społecznościowych (tylko)
Aby utworzyć konta społecznościowych, bez poświadczeń lokalnego konta. Wysłanie żądania POST protokołu HTTPS do interfejsu API programu Graph. Treść żądania zawiera właściwości społecznościowych konta użytkownika do tworzenia. Co najmniej należy określić wymagane właściwości. 


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
## <a name="migrate-social-account-with-local-account"></a>Migrowanie społecznościowych konta z konta lokalnego
Aby utworzyć połączonego konta lokalnego z tożsamościami społecznościowych. Wysłanie żądania POST protokołu HTTPS do interfejsu API programu Graph. Treść żądania zawiera właściwości społecznościowych konta użytkownika do tworzenia. Co najmniej należy określić wymagane właściwości. 

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
### <a name="how-can-i-know-the-issuer-name"></a>Jak sprawdzić, nazwa wystawcy?
Nazwa wystawcy lub nazwa dostawcy tożsamości, jest skonfigurowany w zasadach. Jeśli nie znasz wartość, aby określić w `issuer`, wykonaj poniższą procedurę:
1. Zaloguj się przy użyciu jednego z kont społecznościowych
2. JWT token, skopiuj `sub` wartość. `sub` Zwykle zawiera identyfikator obiektu użytkownika w usłudze Azure AD B2C. Lub z portalu Azure, otwórz właściwości użytkownika i skopiuj identyfikatora obiektu.
3. Otwórz [Eksploratora usługi Azure AD Graph](https://graphexplorer.azurewebsites.net)
4. Zaloguj się z administratorem. Nie
5. Uruchom poniższe żądanie GET. Zastąp userObjectId identyfikator użytkownika został skopiowany. **POBIERZ** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId
6. Zlokalizuj `userIdentities` element wewnątrz powrót JSON z usługi Azure AD B2C.
7. [Opcjonalnie] Można również dekodowania `issuerUserId` wartość.

> [!NOTE]
> Użyj konta administratora dzierżawy B2C, lokalne dla dzierżawy usługi B2C. Składnia nazwy konta jest admin@tenant-name.onmicrosoft.com.

### <a name="is-it-possible-to-add-social-identity-to-an-existing-local-account"></a>Czy jest możliwie dodanie społecznościowych tożsamości do istniejącego konta lokalnego?
Tak. Po utworzeniu konta lokalnego, można dodać tożsamości społecznościowych. Uruchom żądania PATCH protokołu HTTPS. Zastąp userObjectId przy użyciu Identyfikatora użytkownika, które chcesz zaktualizować. 

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

### <a name="is-it-possible-to-add-multiple-social-identities"></a>Jest możliwe dodać wiele tożsamości społecznościowych?
Tak. Można dodać wiele tożsamości społecznościowych dla jednego konta usługi Azure AD B2C. Uruchom żądania PATCH protokołu HTTPS. Zamień userObjectId identyfikator użytkownika. 

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

## <a name="optional-user-migration-application-sample"></a>[Opcjonalnie] Użytkownik migracji aplikacji przykładowych
[Pobieranie i uruchamianie aplikacji przykładowej V2](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-user-migration). Przykładowa aplikacja V2 używa pliku JSON, który zawiera dane fikcyjny użytkownika, w tym: konta lokalnego, konto społecznościowych i tożsamości lokalnych & społecznościowych w jednego konta.  Aby edytować plik JSON, otwórz `AADB2C.UserMigration.sln` rozwiązania Visual Studio. W `AADB2C.UserMigration` otwarciu projektu `UsersData.json` pliku. Plik zawiera listę jednostkami użytkownika. Każdy obiekt użytkownika ma następujące właściwości:
* **signInName** — dla konta lokalnego, adres e-mail w celu logowania
* **Nazwa wyświetlana** — Nazwa wyświetlana użytkownika
* **Imię** -imię użytkownika
* **Nazwisko** -nazwisko użytkownika
* **hasło** dla lokalnego konta i hasło użytkownika (może być pusta)
* **Wystawca** — dla konta społecznościowych, nazwa dostawcy tożsamości
* **issuerUserId** — w przypadku kont społecznościowych, identyfikatora unikatowego użytkownika używane przez dostawcę tożsamości społecznościowych. Wartość powinna być w postaci zwykłego tekstu. Przykładowa aplikacja koduje tę wartość w formacie base64.
* **wiadomości e-mail** dla kont społecznościowych tylko (nie połączonych) użytkownika adresu e-mail

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
> Jeśli nie zaktualizujesz pliku UsersData.json w przykładzie z danymi należy można Zaloguj się przy użyciu poświadczeń lokalnego konta próbki, ale nie wraz z przykładami społecznościowych konta. Aby dokonać migracji kont społecznościowych, podaj rzeczywiste dane.

Aby uzyskać więcej informacji sposobu używania przykładowej aplikacji, zobacz [Azure Active Directory B2C: Migracja użytkowników](active-directory-b2c-user-migration.md)
