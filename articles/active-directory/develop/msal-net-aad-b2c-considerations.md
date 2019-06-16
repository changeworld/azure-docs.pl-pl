---
title: Usługa Azure AD B2C (Microsoft Authentication Library dla platformy .NET) | Azure
description: Dowiedz się więcej o tu konkretne uwagi przy użyciu usługi Azure AD B2C Biblioteka Microsoft Authentication Library for .NET (platformy MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8240a487bdb01cdbe9017ddc7cb95ce4fc0e1503
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67052365"
---
# <a name="use-msalnet-to-sign-in-users-with-social-identities"></a>Umożliwia logowanie użytkowników mających tożsamości społecznościowych platformy MSAL.NET

Można logować użytkowników za pomocą tożsamości społecznościowych, przy użyciu platformy MSAL.NET [usługi Azure Active Directory B2C (Azure AD B2C)](https://aka.ms/aadb2c). Usługa Azure AD B2C opiera się na koncepcji zasad. W platformy MSAL.NET określać zasady przekłada się na zapewnieniu urzędu.

- Podczas tworzenia wystąpienia aplikacji publicznych klienta, należy określić zasad urzędu certyfikacji.
- Jeśli chcesz zastosować zasady, należy wywołać zastąpieniu obiektu `AcquireTokenInteractive` zawierający `authority` parametru.

Ta strona dotyczy MSAL 3.x. Jeśli interesują Cię MSAL 2.x, zobacz [charakterystykę usługi Azure AD B2C w MSAL 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-Specifics-MSAL-2.x).

## <a name="authority-for-a-azure-ad-b2c-tenant-and-policy"></a>Urząd dla dzierżawy usługi Azure AD B2C i zasad

Urząd używany jest `https://login.microsoftonline.com/tfp/{tenant}/{policyName}` gdzie:

- `tenant` jest nazwą dzierżawy Azure AD B2C 
- `policyName` Nazwa zasady do zastosowania (na przykład "b2c_1_susi" dla logowania — w/rejestracją).

Bieżące wskazówki z usługi Azure AD B2C jest użycie `b2clogin.com` jako źródło. Na przykład `$"https://{your-tenant-name}.b2clogin.com/tfp/{your-tenant-ID}/{policyname}"`. Aby uzyskać więcej informacji, zobacz ten [dokumentacji](/azure/active-directory-b2c/b2clogin).

## <a name="instantiating-the-application"></a>Utworzenie wystąpienia aplikacji

Podczas kompilowania aplikacji, musisz podać uprawnienia.

```csharp
// Azure AD B2C Coordinates
public static string Tenant = "fabrikamb2c.onmicrosoft.com";
public static string ClientID = "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6";
public static string PolicySignUpSignIn = "b2c_1_susi";
public static string PolicyEditProfile = "b2c_1_edit_profile";
public static string PolicyResetPassword = "b2c_1_reset";

public static string AuthorityBase = $"https://fabrikamb2c.b2clogin.com/tfp/{Tenant}/";
public static string Authority = $"{AuthorityBase}{PolicySignUpSignIn}";
public static string AuthorityEditProfile = $"{AuthorityBase}{PolicyEditProfile}";
public static string AuthorityPasswordReset = $"{AuthorityBase}{PolicyResetPassword}";

application = PublicClientApplicationBuilder.Create(ClientID)
               .WithB2CAuthority(Authority)
               .Build();
```

## <a name="acquire-a-token-to-apply-a-policy"></a>Uzyskanie tokenu, aby zastosować zasady

Uzyskiwanie tokenu dla usługi Azure AD B2C chronionego interfejsu API w aplikacji klienckiej publicznych wymaga używania zastąpień z urzędem:

```csharp
IEnumerable<IAccount> accounts = await application.GetAccountsAsync();
AuthenticationResult ar = await application .AcquireToken(scopes, parentWindow)
                                            .WithAccount(GetAccountByPolicy(accounts, policy))
                                            .ExecuteAsync();
```

tym:

- `policy` jest jednym z poprzednich ciągów (na przykład `PolicySignUpSignIn`).
- `GetAccountByPolicy(IEnumerable<IAccount>, string)` jest metodą, która umożliwia znalezienie konta usługi dla danej zasady. Na przykład:

  ```csharp
  private IAccount GetAccountByPolicy(IEnumerable<IAccount> accounts, string policy)
  {
   foreach (var account in accounts)
   {
    string userIdentifier = account.HomeAccountId.ObjectId.Split('.')[0];
    if (userIdentifier.EndsWith(policy.ToLower()))
     return account;
   }
   return null;
  }
  ```

Stosowanie zasady (na przykład przez użytkownika końcowego, o których edytować swój profil lub zresetowania swojego hasła) obecnie odbywa się przez wywołanie metody `AcquireTokenInteractive`. W przypadku tych dwóch zasad, nie używaj zwrócony token / wynik uwierzytelniania.

## <a name="special-case-of-editprofile-and-resetpassword-policies"></a>Przypadek specjalny EditProfile i ResetPassword zasad

Jeśli chcesz zapewnić środowisko, w którym użytkownicy końcowi Zaloguj się przy użyciu tożsamości społecznościowych, a następnie edytować swój profil chcesz zastosować zasady usługi Azure AD B2C EditProfile. Sposób, aby to zrobić, to przez wywołanie metody `AcquireTokenInteractive` z urzędem określone dla tej zasady i monit równa `Prompt.NoPrompt` w celu uniknięcia okna dialogowego wyboru konta mają być wyświetlane (jak użytkownik jest już zalogowany)

```csharp
private async void EditProfileButton_Click(object sender, RoutedEventArgs e)
{
 IEnumerable<IAccount> accounts = await app.GetAccountsAsync();
 try
 {
  var authResult = await app.AcquireToken(scopes:App.ApiScopes)
                               .WithAccount(GetUserByPolicy(accounts, App.PolicyEditProfile)),
                               .WithPrompt(Prompt.NoPrompt),
                               .WithB2CAuthority(App.AuthorityEditProfile)
                               .ExecuteAsync();
  DisplayBasicTokenInfo(authResult);
 }
 catch
 {
  . . .
 }
}
```
## <a name="resource-owner-password-credentials-ropc-with-azure-ad-b2c"></a>Poświadczenia hasła właściciela zasobu (ROPC) za pomocą usługi Azure AD B2C
Aby uzyskać szczegółowe informacje na temat przepływu ROPC, przeczytaj ten [dokumentacji](v2-oauth-ropc.md).

Ten przepływ jest **niezalecane** ponieważ monitowania użytkownika o ich hasła aplikacji nie jest bezpieczny. Aby uzyskać więcej informacji na temat tego problemu, zobacz [w tym artykule](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). 

Za pomocą nazwy użytkownika/hasła, możesz są zapewniając telefoniczny kilka rzeczy:
- podstawowe dzierżaw nowoczesnych tożsamości: hasło pobiera połowy, odtworzyć. Ponieważ mamy tę koncepcję udziału klucz tajny, który może zostać przechwycona. Jest to niezgodne z bez hasła.
- Użytkownicy, którzy muszą przeprowadzić uwierzytelnianie wieloskładnikowe nie będzie mógł zalogować (zgodnie z nie zachodzi żadna interakcja).
- Użytkownicy nie będą mogli logowanie jednokrotne.

### <a name="configure-the-ropc-flow-in-azure-ad-b2c"></a>Konfiguruj przepływ ROPC w usłudze Azure AD B2C
W swojej dzierżawie usługi Azure AD B2C, utworzyć nowy przepływ użytkownika, a następnie wybierz **Zaloguj się przy użyciu ROPC**. Spowoduje to włączenie zasad ROPC dla Twojej dzierżawy. Zobacz [skonfiguruj przepływ poświadczeń hasła właściciela zasobu](/azure/active-directory-b2c/configure-ropc) Aby uzyskać więcej informacji.

`IPublicClientApplication` zawiera metodę:
```csharp
AcquireTokenByUsernamePassword(
            IEnumerable<string> scopes,
            string username,
            SecureString password)
```

Ta metoda przyjmuje jako parametry:
- *Zakresy* token dostępu dla żądania.
- A *username*.
- Obiektu SecureString *hasło* dla użytkownika.

Pamiętaj, aby użyć urząd certyfikacji który zawiera zasady ROPC.

### <a name="limitations-of-the-ropc-flow"></a>Ograniczenia przepływu ROPC
 - Przepływ ROPC **działa tylko dla kont lokalnych** (gdzie zarejestrować za pomocą usługi Azure AD B2C przy użyciu poczty e-mail lub nazwa użytkownika). Ten przepływ nie działa, jeśli federowania do dowolnego dostawcy tożsamości, obsługiwane przez usługę Azure AD B2C (Facebook, Google, itd.).
 - Obecnie ma **id_token nie zwrócone z usługi Azure AD B2C** podczas wykonywania przepływu ROPC z biblioteki MSAL. Oznacza to, że nie można utworzyć obiekt konta, więc w pamięci podręcznej, będzie żadnego konta i użytkownik nie. Przepływ AcquireTokenSilent nie będzie działać w tym scenariuszu. Jednak ROPC nie są wyświetlane w interfejsie użytkownika, więc nie będzie żadnego wpływu na środowisko pracy użytkownika.

## <a name="google-auth-and-embedded-webview"></a>Uwierzytelniania Google i osadzone Webview

Jeśli jesteś deweloperem usługi Azure AD B2C przy użyciu Google jako dostawcy tożsamości możemy recommand korzystać z przeglądarki systemu, jak Google nie zezwala na [uwierzytelnianie z osadzonych elementów Webview](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html). Obecnie `login.microsoftonline.com` jest zaufany urząd, za pomocą usługi Google. Przy użyciu tego urzędu będzie działać z osadzonych webview. Jednak przy użyciu `b2clogin.com` nie jest zaufany urząd, za pomocą usługi Google, dzięki czemu użytkownicy nie będą w stanie uwierzytelnić.

Firma Microsoft udostępni aktualizację wiki i to [problem](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/688) w przypadku zmiany rzeczy.

## <a name="caching-with-azure-ad-b2c-in-msalnet"></a>Buforowanie w usłudze Azure AD B2C w platformy MSAL.Net 

### <a name="known-issue-with-azure-ad-b2c"></a>Znany problem w usłudze Azure AD B2C

Obsługuje platformy MSAL.Net [tokenu z pamięci podręcznej](/dotnet/api/microsoft.identity.client.tokencache?view=azure-dotnet). Token buforowania klucz opiera się na oświadczeniach, zwrócona przez dostawcę tożsamości. Obecnie platformy MSAL.Net musi mieć dwa oświadczenia do tworzenia klucza pamięci podręcznej tokenu:  
- `tid` co jest identyfikator dzierżawy usługi Azure AD i 
- `preferred_username` 

Oba te oświadczenia brakuje w wielu scenariuszach usługi Azure AD B2C. 

Wpływ klienta jest jako wartość, podczas próby wyświetlenia pole nazwy użytkownika, czy uzyskujesz "Brak z tokenu odpowiedzi"? Jeśli tak, jest to spowodowane usługi Azure AD B2C nie zwraca wartości w IdToken dla preferred_username ze względu na ograniczenia przy użyciu kont społecznościowych i zewnętrzny dostawca tożsamości (IdPs). Usługa Azure AD zwraca wartość preferred_username, ponieważ wie, kim jest użytkownik, ale dla usługi Azure AD B2C, ponieważ użytkownik może zalogować się przy użyciu lokalnego konta Facebook, Google, GitHub, itp. jest spójne wartość dla usługi Azure AD B2C na potrzeby preferred_username. Aby odblokować MSAL z wprowadza zgodności pamięci podręcznej biblioteki adal, firma zdecydowała się podczas pracy z kontami usługi Azure AD B2C, gdy IdToken zwraca nic preferred_username przy użyciu "Brak z odpowiedzi tokenu" po naszej stronie. Biblioteka MSAL musi zwracać wartość preferred_username zachować zgodność z pamięci podręcznej w całej biblioteki.

### <a name="workarounds"></a>Rozwiązania

#### <a name="mitigation-for-the-missing-tenant-id"></a>Środki zaradcze dla Identyfikatora dzierżawy

Sugerowane obejście polega na użyciu [w pamięci podręcznej przez zasady](#acquire-a-token-to-apply-a-policy)

Alternatywnie, można użyć `tid` oświadczenia, jeśli używasz [zasady niestandardowe B2C](https://aka.ms/ief), ponieważ zapewnia funkcje, aby zwrócić dodatkowe oświadczenia do aplikacji. Aby dowiedzieć się więcej na temat [przekształcania oświadczeń](/azure/active-directory-b2c/claims-transformation-technical-profile)

#### <a name="mitigation-for-missing-from-the-token-response"></a>Środki zaradcze dla "Brak z tokenu odpowiedzi"
Jedną z opcji jest używany oświadczenia "name" jako preferowaną nazwy użytkownika. Ten proces jest wymieniony w tym [B2C doc](../../active-directory-b2c/active-directory-b2c-reference-policies.md) -> "w kolumnie zwracany oświadczenia wybierz oświadczenia, o których mają być zwracane w tokenach autoryzacji wysyłanych z powrotem do aplikacji po pomyślnym edytowaniu profilu. Na przykład wybierz nazwę wyświetlaną, kod pocztowy."

## <a name="next-steps"></a>Kolejne kroki 

Więcej informacji na temat uzyskiwania tokenów interaktywnie przy użyciu platformy MSAL.NET dla aplikacji usługi Azure AD B2C znajdują się w następującym przykładzie.

| Sample | Platforma | Opis|
|------ | -------- | -----------|
|[active-directory-b2c-xamarin-native](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Xamarin dla systemu iOS, Xamarin dla systemu Android, platformy uniwersalnej systemu Windows | Prosta aplikacja Xamarin Forms przedstawiająca sposób użycia platformy MSAL.NET uwierzytelniania użytkowników za pośrednictwem usługi Azure AD B2C w celu uzyskania dostępu do internetowego interfejsu API z tokenami wynikowymi.|
