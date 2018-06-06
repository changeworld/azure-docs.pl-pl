---
title: Przy użyciu AppAuth w aplikacji systemu iOS w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: W tym artykule przedstawiono sposób tworzenia aplikacji systemu iOS, która używa AppAuth z usługi Azure Active Directory B2C do zarządzania tożsamościami użytkowników i uwierzytelniania użytkowników.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 03/07/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 1da0187fc4ffa139eb7b245dd20d708eb98e0cf5
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34710311"
---
# <a name="azure-ad-b2c-sign-in-using-an-ios-application"></a>Usługa Azure AD B2C: Zaloguj się przy użyciu aplikacji systemu iOS

Platforma Microsoft Identity korzysta z otwartych standardów, takich jak OAuth2 i OpenID Connect. Przy użyciu standardowego protokołu zapewniającego Otwórz oferuje większy wybór developer, wybierając biblioteki do integracji z naszych usług. Podobnie jak jego pomóc deweloperom pisanie aplikacji łączących się z platformą Microsoft Identity przygotowaliśmy tego przewodnika oraz innym osobom. Większość bibliotek, które implementują [specyfikację RFC6749 OAuth2](https://tools.ietf.org/html/rfc6749) są w stanie nawiązać połączenia z platformą Microsoft Identity.

> [!WARNING]
> Firma Microsoft zapewnia poprawki dla bibliotek innych firm, a nie przeprowadził Przegląd tych bibliotek. Ten przykład jest przy użyciu biblioteki innej firmy o nazwie AppAuth, które zostały przetestowane na zgodność w podstawowe scenariusze w usłudze Azure AD B2C. Problemy i żądania funkcji powinny być kierowane do biblioteki projektu open source. Więcej informacji znajduje się w [tym artykule](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries).
>
>

Jeśli jesteś nowym użytkownikiem OAuth2 lub OpenID Connect, znacznie to przykładowa konfiguracja może nie mieć sensu dużo do Ciebie. Zalecamy zapoznanie się z [krótkim omówieniem protokołu w tej dokumentacji](active-directory-b2c-reference-protocols.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Tworzenie katalogu usługi Azure AD B2C
Przed rozpoczęciem korzystania z usługi Azure AD B2C należy utworzyć katalog lub dzierżawę. Katalog jest kontenerem dla wszystkich użytkowników, aplikacji, grup i inne. Jeśli jeszcze go nie masz, [utwórz katalog usługi B2C](active-directory-b2c-get-started.md), zanim przejdziesz dalej.

## <a name="create-an-application"></a>Tworzenie aplikacji
Następnie musisz utworzyć aplikację w katalogu usługi B2C. Rejestracja aplikacji zapewnia informacje wymagane do bezpiecznego komunikowania się z aplikacją usługi Azure AD. Aby utworzyć aplikacji mobilnej, wykonaj [tych instrukcji](active-directory-b2c-app-registration.md). Należy pamiętać o wykonaniu następujących czynności:

* Obejmują **Native client** w aplikacji.
* Skopiuj **Identyfikator aplikacji** przypisany do aplikacji. Ten identyfikator GUID jest potrzebny później.
* Konfigurowanie **identyfikator URI przekierowania** z schematu niestandardowego (na przykład com.onmicrosoft.fabrikamb2c.exampleapp://oauth/redirect). Ten identyfikator URI potrzebne później.

## <a name="create-your-policies"></a>Tworzenie zasad
W usłudze Azure AD B2C każde działanie użytkownika jest definiowane przy użyciu [zasad](active-directory-b2c-reference-policies.md). Ta aplikacja zawiera jeden obsługi tożsamości: połączone logowania i rejestrowania. Utwórz te zasady, zgodnie z opisem w [artykule o zasadach](active-directory-b2c-reference-policies.md#create-a-sign-up-policy). Podczas tworzenia zasad należy:

* W obszarze **atrybuty rejestracji**, wybierz atrybut **Nazwa wyświetlana**.  Możesz wybrać także inne atrybuty.
* W obszarze **oświadczenia aplikacji**, wybierz oświadczenia **Nazwa wyświetlana** i **Identyfikatora obiektu użytkownika**. Możesz wybrać także inne oświadczenia.
* Skopiować każdą utworzoną wartość **Nazwa** zasad. Nazwę zasad jest prefiksem `b2c_1_` podczas zapisywania zasad.  Nazwa zasady jest potrzebny później.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Po utworzeniu zasad będzie można rozpocząć tworzenie aplikacji.

## <a name="download-the-sample-code"></a>Pobierz przykładowy kod
Firma Microsoft umieściła próbki pracy, który korzysta z usługi Azure AD B2C AppAuth [w serwisie GitHub](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c). Można go pobrać i uruchom go. Aby używać dzierżawy usługi Azure AD B2C, postępuj zgodnie z instrukcjami [README.md](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md).

W tym przykładzie został utworzony zgodnie z instrukcjami README przez [iOS AppAuth projektu w witrynie GitHub](https://github.com/openid/AppAuth-iOS). Aby uzyskać więcej informacji na temat działania próbki i bibliotece odwoływać README AppAuth w witrynie GitHub.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Modyfikowanie aplikacji do użycia usługi Azure AD B2C z AppAuth

> [!NOTE]
> AppAuth obsługuje system iOS 7 lub nowszym.  Jednak do obsługi logowania społecznościowych Google, SFSafariViewController jest potrzebny co wymaga systemu iOS 9 lub nowszą.
>

### <a name="configuration"></a>Konfigurowanie

Komunikację można skonfigurować z usługi Azure AD B2C, określając punktu końcowego autoryzacji i punktu końcowego tokena identyfikatorów URI.  Aby wygenerować te identyfikatory URI, potrzebne są następujące informacje:
* Identyfikator dzierżawcy (np. contoso.onmicrosoft.com)
* Nazwa zasady (na przykład B2C\_1\_SignUpIn)

Zastępując dzierżawcy można wygenerować identyfikatora URI punktu końcowego tokena\_identyfikator i zasady\_nazwy w następujący adres URL:

```objc
static NSString *const tokenEndpoint = @"https://login.microsoftonline.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/token";
```

Zastępując dzierżawcy można wygenerować identyfikatora URI punktu końcowego autoryzacji\_identyfikator i zasady\_nazwy w następujący adres URL:

```objc
static NSString *const authorizationEndpoint = @"https://login.microsoftonline.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/authorize";
```

Uruchom następujący kod, aby utworzyć obiekt AuthorizationServiceConfiguration:

```objc
OIDServiceConfiguration *configuration = 
    [[OIDServiceConfiguration alloc] initWithAuthorizationEndpoint:authorizationEndpoint tokenEndpoint:tokenEndpoint];
// now we are ready to perform the auth request...
```

### <a name="authorizing"></a>Autoryzowanie

Po Konfigurowanie lub pobieranie konfiguracji usługi autoryzacji, można utworzyć żądania autoryzacji. Aby utworzyć żądanie, potrzebne są następujące informacje:  
* Identyfikator klienta (na przykład 00000000-0000-0000-0000-000000000000)
* Identyfikator URI przekierowania z schematu niestandardowego (na przykład com.onmicrosoft.fabrikamb2c.exampleapp://oauth/redirect)

Oba elementy powinny zostały zapisane, gdy użytkownik [rejestrowanie aplikacji](#create-an-application).

```objc
OIDAuthorizationRequest *request = 
    [[OIDAuthorizationRequest alloc] initWithConfiguration:configuration
                                                  clientId:kClientId
                                                    scopes:@[OIDScopeOpenID, OIDScopeProfile]
                                               redirectURL:[NSURL URLWithString:kRedirectUri]
                                              responseType:OIDResponseTypeCode
                                      additionalParameters:nil];

AppDelegate *appDelegate = (AppDelegate *)[UIApplication sharedApplication].delegate;
appDelegate.currentAuthorizationFlow = 
    [OIDAuthState authStateByPresentingAuthorizationRequest:request
                                   presentingViewController:self
                                                   callback:^(OIDAuthState *_Nullable authState, NSError *_Nullable error) {
        if (authState) {
            NSLog(@"Got authorization tokens. Access token: %@", authState.lastTokenResponse.accessToken);
            [self setAuthState:authState];
        } else {
            NSLog(@"Authorization error: %@", [error localizedDescription]);
            [self setAuthState:nil];
        }
    }];
```

Aby skonfigurować aplikację do obsługi przekierowania do identyfikatora URI ze schematem niestandardowe, należy zaktualizować listę "Schematy adresów URL" w Twojej Info.pList:
* Otwórz Info.pList.
* Umieść kursor nad wiersza, takie jak "Kod typu systemu operacyjnego pakietu", a następnie kliknij przycisk \+ symbolu.
* Zmień nazwę nowego wiersza "Adres URL typy".
* Kliknij strzałkę w lewo "Adres URL typy" Aby otworzyć drzewa.
* Kliknij strzałkę po lewej "elementu 0", aby otworzyć drzewa.
* Zmień nazwę pierwszego elementu poniżej elementu 0 "Schematy adresów URL".
* Kliknij strzałkę w lewo "Schematy adresów URL", aby otworzyć drzewa.
* W kolumnie 'Value' jest puste pole na lewo od elementu 0 poniżej "Schematy adresów URL".  Wartość schemat unikatowy aplikacji.  Wartość musi być zgodna używany w redirectURL podczas tworzenia obiektu OIDAuthorizationRequest schemat.  W przykładzie jest używany schemat "com.onmicrosoft.fabrikamb2c.exampleapp".

Zapoznaj się [przewodnik AppAuth](https://openid.github.io/AppAuth-iOS/) na temat sposobu ukończenia resztę procesu. Jeśli potrzebujesz szybko rozpocząć pracę z aplikacją pracy, zapoznaj się [próbki](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c). Postępuj zgodnie z instrukcjami [README.md](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md) wprowadzić konfigurację usługi Azure AD B2C.

Firma Microsoft zawsze są otwarte na opinie i sugestie! Jeśli masz trudności w tym artykule, lub mają zalecenia dotyczące poprawy tej zawartości, prosimy o wyrażenie opinii na dole strony. Funkcja żądań, dodaj je do [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).
