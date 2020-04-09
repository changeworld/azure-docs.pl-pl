---
title: Funkcja SSO między aplikacjami ADAL & MSAL (iOS/macOS) — platforma tożsamości firmy Microsoft | Azure
description: ''
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: 7a8a1667ba1ca2a99c053c6941e3ba778299fd53
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80880754"
---
# <a name="how-to-sso-between-adal-and-msal-apps-on-macos-and-ios"></a>Jak: SSO między aplikacjami ADAL i MSAL w systemach macOS i iOS

Biblioteka uwierzytelniania firmy Microsoft (MSAL) dla systemu iOS może współużytkować stan SSO z [ADAL Objective-C](https://github.com/AzureAD/azure-activedirectory-library-for-objc) między aplikacjami. Możesz migrować aplikacje do msal we własnym tempie, zapewniając, że użytkownicy nadal będą korzystać z wielo aplikacjami SSO - nawet z mieszanką aplikacji opartych na ADAL i MSAL.

Jeśli szukasz wskazówek dotyczących konfigurowania jednokrotnego ustawienia między aplikacjami przy użyciu sdk MSAL, zobacz [Ciche jedno przystawka SSO między wieloma aplikacjami](single-sign-on-macos-ios.md#silent-sso-between-apps). Ten artykuł koncentruje się na eso między ADAL i MSAL.

Specyfika implementacji usługi SSO zależy od używanej wersji ADAL.

## <a name="adal-27x"></a>ADAL 2.7.x

Ta sekcja obejmuje różnice sso między MSAL i ADAL 2.7.x

### <a name="cache-format"></a>Format pamięci podręcznej

ADAL 2.7.x może odczytać format pamięci podręcznej MSAL. Nie musisz robić nic specjalnego dla cross-app SSO z wersją ADAL 2.7.x. Należy jednak pamiętać o różnicach w identyfikatorach kont obsługiwanych przez te dwie biblioteki.

### <a name="account-identifier-differences"></a>Różnice identyfikatorów kont

MSAL i ADAL używają różnych identyfikatorów kont. ADAL używa głównej liczby numerów UPN jako podstawowego identyfikatora konta. Usługa MSAL używa nieobserwowalny identyfikator konta, który jest oparty na identyfikatorze obiektu `sub` i identyfikatora dzierżawy dla kont AAD i oświadczenia dla innych typów kont.

Po otrzymaniu `MSALAccount` obiektu w wyniku MSAL, zawiera identyfikator konta `identifier` we właściwości. Aplikacja powinna używać tego identyfikatora dla kolejnych żądań niemych.

Oprócz `identifier`obiektu `MSALAccount` obiekt zawiera wyświetlany identyfikator `username`o nazwie . To przekłada `userId` się na w ADAL. `username`nie jest uważany za unikatowy identyfikator i można zmienić w dowolnym momencie, więc powinien być używany tylko dla scenariuszy zgodności z powrotem z ADAL. MSAL obsługuje kwerendy pamięci `username` `identifier`podręcznej przy `identifier` użyciu albo lub , gdzie zaleca się wykonywanie zapytań przez.

W poniższej tabeli podsumowano różnice identyfikatorów kont między ADAL i MSAL:

| Identyfikator konta                | BIBLIOTEKA MSAL                                                         | ADAL 2.7.x      | Starsze ADAL (przed ADAL 2.7.x) |
| --------------------------------- | ------------------------------------------------------------ | --------------- | ------------------------------ |
| wyświetlany identyfikator            | `username`                                                   | `userId`        | `userId`                       |
| unikatowy identyfikator nieekslidalny | `identifier`                                                 | `homeAccountId` | Nie dotyczy                            |
| Nie jest znany identyfikator konta               | Zapytanie do `allAccounts:` wszystkich kont za pośrednictwem interfejsu API w`MSALPublicClientApplication` | Nie dotyczy             | Nie dotyczy                            |

Jest to `MSALAccount` interfejs zawierający te identyfikatory:

```objc
@protocol MSALAccount <NSObject>

/*!
 Displayable user identifier. Can be used for UI and backward compatibility with ADAL.
 */
@property (readonly, nullable) NSString *username;

/*!
 Unique identifier for the account.
 Save this for account lookups from cache at a later point.
 */
@property (readonly, nullable) NSString *identifier;

/*!
 Host part of the authority string used for authentication based on the issuer identifier.
 */
@property (readonly, nonnull) NSString *environment;

/*!
 ID token claims for the account.
 Can be used to read additional information about the account, e.g. name
 Will only be returned if there has been an id token issued for the client Id for the account's source tenant.
 */
@property (readonly, nullable) NSDictionary<NSString *, NSString *> *accountClaims;

@end
```

### <a name="sso-from-msal-to-adal"></a>SSO z MSAL do ADAL

Jeśli masz aplikację MSAL i aplikację ADAL, a użytkownik najpierw loguje się do aplikacji opartej na msal, `username` możesz `MSALAccount` uzyskać logowanie sygowe w `userId`aplikacji ADAL, zapisując go z obiektu i przekazując go do aplikacji opartej na ADAL jako . ADAL można następnie znaleźć informacje o `acquireTokenSilentWithResource:clientId:redirectUri:userId:completionBlock:` koncie po cichu z interfejsem API.

### <a name="sso-from-adal-to-msal"></a>SSO z ADAL do MSAL

Jeśli masz aplikację MSAL i aplikację ADAL, a użytkownik najpierw loguje się do aplikacji opartej na adal, możesz użyć identyfikatorów użytkowników ADAL do wyszukiwania kont w msal. Dotyczy to również podczas migracji z usługi ADAL do msal.

#### <a name="adals-homeaccountid"></a>ADAL's homeAccountId

ADAL 2.7.x `homeAccountId` zwraca `ADUserInformation` w obiekcie w wyniku za pośrednictwem tej właściwości:

```objc
/*! Unique AAD account identifier across tenants based on user's home OID/home tenantId. */
@property (readonly) NSString *homeAccountId;
```

`homeAccountId`w ADAL jest odpowiednikiem `identifier` w MSAL. Można zapisać ten identyfikator do użycia w msal dla `accountForIdentifier:error:` wyszukiwania kont z interfejsem API.

#### <a name="adals-userid"></a>ADAL's`userId`

Jeśli `homeAccountId` nie jest dostępny lub masz tylko wyświetlany identyfikator, możesz użyć `userId` usługi ADAL do wyszukiwania konta w msal.

W msal, najpierw wyszukać konto przez `username` lub `identifier`. Zawsze `identifier` używaj do wykonywania zapytań, `username` jeśli go masz i używaj tylko jako rezerwowego. Jeśli konto zostanie znalezione, użyj `acquireTokenSilent` konta w połączeniach.

Cel C:

```objc
NSString *msalIdentifier = @"previously.saved.msal.account.id";
MSALAccount *account = nil;
    
if (msalIdentifier)
{
    // If you have MSAL account id returned either from MSAL as identifier or ADAL as homeAccountId, use it
    account = [application accountForIdentifier:@"my.account.id.here" error:nil];
}
else
{
    // Fallback to ADAL userId for migration
    account = [application accountForUsername:@"adal.user.id" error:nil];
}
    
if (!account)
{
  // Account not found.
  return;
}

MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:account];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Swift:

```swift
        
let msalIdentifier: String?
var account: MSALAccount
        
do {
  if let msalIdentifier = msalIdentifier {
    account = try application.account(forIdentifier: msalIdentifier)
  }
  else {
    account = try application.account(forUsername: "adal.user.id") 
  }
             
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: account)          
  application.acquireTokenSilent(with: silentParameters) {
    (result: MSALResult?, error: Error?) in
    // handle result
  }  
} catch let error as NSError {
  // handle error or account not found
}
```



Interfejsy API wyszukiwania kont obsługiwane przez firmę MSAL:

```objc
/*!
 Returns account for the given account identifier (received from an account object returned in a previous acquireToken call)
 
 @param  error      The error that occurred trying to get the accounts, if any, if you're
                    not interested in the specific error pass in nil.
 */
- (nullable MSALAccount *)accountForIdentifier:(nonnull NSString *)identifier
                                         error:(NSError * _Nullable __autoreleasing * _Nullable)error;
    
/*!
Returns account for for the given username (received from an account object returned in a previous acquireToken call or ADAL)
    
@param  username    The displayable value in UserPrincipleName(UPN) format
@param  error       The error that occurred trying to get the accounts, if any, if you're
                    not interested in the specific error pass in nil.
*/
- (MSALAccount *)accountForUsername:(NSString *)username
                              error:(NSError * __autoreleasing *)error;
```

## <a name="adal-2x-266"></a>ADAL 2.x-2.6.6

Ta sekcja obejmuje różnice sso między MSAL i ADAL 2.x-2.6.6.

Starsze wersje ADAL nie są natywnie obsługiwane przez format pamięci podręcznej MSAL. Jednak aby zapewnić płynną migrację z usługi ADAL do msal, msal może odczytać starszy format pamięci podręcznej ADAL bez monitowania o poświadczenia użytkownika ponownie.

Ponieważ `homeAccountId` nie jest dostępny w starszych wersjach ADAL, należy `username`wyszukać konta za pomocą :

```objc
/*!
 Returns account for for the given username (received from an account object returned in a previous acquireToken call or ADAL)

 @param  username    The displayable value in UserPrincipleName(UPN) format
 @param  error       The error that occurred trying to get the accounts, if any.  If you're not interested in the specific error pass in nil.
 */
- (MSALAccount *)accountForUsername:(NSString *)username
                              error:(NSError * __autoreleasing *)error;
```

Przykład:

Cel C:


```objc
MSALAccount *account = [application accountForUsername:@"adal.user.id" error:nil];;
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:account];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Swift:

```swift
do {
  let account = try application.account(forUsername: "adal.user.id")          
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: account)
  application.acquireTokenSilent(with: silentParameters) { 
    (result: MSALResult?, error: Error?) in
    // handle result
  }   
} catch let error as NSError { 
  // handle error or account not found
}
```



Alternatywnie, można przeczytać wszystkie konta, które będą również odczytywać informacje o koncie z ADAL:

Cel C:

```objc
NSArray *accounts = [application allAccounts:nil];
    
if ([accounts count] == 0)
{
  // No account found.
  return; 
}
if ([accounts count] > 1)
{
  // You might want to display an account picker to user in actual application
  // For this sample we assume there's only ever one account in cache
  return;
}
    ``
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:accounts[0]];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Swift:

```swift
      
do {
  let accounts = try application.allAccounts()
  if accounts.count == 0 {
    // No account found.
    return
  }
  if accounts.count > 1 {
    // You might want to display an account picker to user in actual application
    // For this sample we assume there's only ever one account in cache
    return
  }
  
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: accounts[0])
  application.acquireTokenSilent(with: silentParameters) {
    (result: MSALResult?, error: Error?) in
    // handle result or error  
  }  
} catch let error as NSError { 
  // handle error
}
```



## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [przepływach uwierzytelniania i scenariuszach aplikacji](authentication-flows-app-scenarios.md)
