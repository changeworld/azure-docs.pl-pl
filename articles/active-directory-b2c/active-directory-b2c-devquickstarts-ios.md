---
title: Z zastosowaniem AppAuth w aplikacji systemu iOS w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: W tym artykule pokazano, jak utworzyć aplikację dla systemu iOS, która używa AppAuth z usługi Azure Active Directory B2C, aby zarządzać tożsamościami użytkowników i ich uwierzytelniać.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 3c7eace7c643286575e2625cb81f84d858bbaa8f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60317925"
---
# <a name="azure-ad-b2c-sign-in-using-an-ios-application"></a>Azure AD B2C: Zaloguj się przy użyciu aplikacji systemu iOS

Platforma Microsoft Identity korzysta z otwartych standardów, takich jak OAuth2 i OpenID Connect. Za pomocą otwartego protokołu oferuje większy wybór dla deweloperów, podczas wybierania biblioteki można zintegrować z naszymi usługami. Podoba Ci się, aby pomóc deweloperom w pisaniu aplikacji łączących się z platformą Microsoft Identity przygotowaliśmy ten przewodnik i inne. Większość bibliotek implementujących [specyfikację RFC6749 OAuth2](https://tools.ietf.org/html/rfc6749) są w stanie połączyć się z platformą Microsoft Identity.

> [!WARNING]
> Firma Microsoft udostępnia poprawki dotyczące bibliotek innych firm, a nie zrobił przeglądu tych bibliotek. W tym przykładzie jest przy użyciu biblioteki innej firmy o nazwie AppAuth, który został przetestowany na zgodność w podstawowych scenariuszy z usługą Azure AD B2C. Problemy i sugestie funkcji powinny być kierowane do biblioteki typu open-source projektu. Więcej informacji znajduje się w [tym artykule](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries).
>
>

Jeśli jesteś nowym użytkownikiem OAuth2 lub OpenID Connect, znaczną część tej przykładowej konfiguracji może niezrozumiała dla Ciebie. Zalecamy zapoznanie się z [krótkim omówieniem protokołu w tej dokumentacji](active-directory-b2c-reference-protocols.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Tworzenie katalogu usługi Azure AD B2C
Przed rozpoczęciem korzystania z usługi Azure AD B2C należy utworzyć katalog lub dzierżawę. Katalog jest kontenerem dla wszystkich użytkowników, aplikacji, grup i elementów. Jeśli jeszcze go nie masz, [utwórz katalog usługi B2C](tutorial-create-tenant.md), zanim przejdziesz dalej.

## <a name="create-an-application"></a>Tworzenie aplikacji
Następnie musisz utworzyć aplikację w katalogu usługi B2C. Rejestracja aplikacji zapewnia usługi Azure AD informacje wymagane do bezpiecznego komunikowania się z aplikacją. Aby utworzyć aplikację mobilną, postępuj zgodnie z [w instrukcjach](active-directory-b2c-app-registration.md). Należy pamiętać o wykonaniu następujących czynności:

* Obejmują **Native client** w aplikacji.
* Skopiuj **Identyfikator aplikacji** przypisany do aplikacji. Ten identyfikator GUID będą potrzebne później.
* Konfigurowanie **identyfikator URI przekierowania** ze schematem niestandardowym (na przykład com.onmicrosoft.fabrikamb2c.exampleapp://oauth/redirect). Ten identyfikator URI będzie potrzebna później.

## <a name="create-your-user-flows"></a>Tworzyć przepływy użytkownika
W usłudze Azure AD B2C każde działanie użytkownika jest definiowany przez [przepływ użytkownika](active-directory-b2c-reference-policies.md). Ta aplikacja zawiera jedno rozwiązanie tożsamości: połączonego logowania i rejestracji. Po utworzeniu przepływu użytkownika, należy koniecznie:

* W obszarze **atrybuty tworzenia konta**, wybierz atrybut **nazwę wyświetlaną**.  Możesz wybrać, jak również inne atrybuty.
* W obszarze **oświadczeń aplikacji**, wybierz oświadczenia **nazwę wyświetlaną** i **identyfikator obiektu użytkownika**. Możesz wybrać inne oświadczenia, jak również.
* Kopiuj **nazwa** każdego przepływu użytkownika po jego utworzeniu. Nazwa przepływu użytkownika jest poprzedzony znakiem `b2c_1_` po zapisaniu przepływu użytkownika.  Przepływ użytkownika będą potrzebne później.

Po utworzeniu przepływów użytkownika możesz przystąpić do kompilowania aplikacji.

## <a name="download-the-sample-code"></a>Pobierz przykładowy kod
Udostępniliśmy próbkę pracy z zastosowaniem AppAuth za pomocą usługi Azure AD B2C [w serwisie GitHub](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c). Możesz pobrać kod i uruchom go. Aby korzystać z własnej dzierżawy usługi Azure AD B2C, postępuj zgodnie z instrukcjami [README.md](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md).

W tym przykładzie został utworzony zgodnie z instrukcjami w pliku README [iOS AppAuth projektu w witrynie GitHub](https://github.com/openid/AppAuth-iOS). Aby uzyskać więcej informacji na temat sposobu działania przykładu i biblioteki należy odwoływać się AppAuth pliku README w serwisie GitHub.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Modyfikowanie aplikacji za pomocą usług Azure AD B2C AppAuth

> [!NOTE]
> AppAuth obsługuje system iOS 7 i nowsze wersje.  Jednak, aby zapewnić obsługę społecznościowych nazw logowania w usłudze Google, SFSafariViewController jest potrzebny co wymaga systemu iOS 9 lub nowszą.
>

### <a name="configuration"></a>Konfigurowanie

Komunikację można skonfigurować za pomocą usługi Azure AD B2C, określając punkt końcowy autoryzacji i tokena identyfikatory URI punktów końcowych.  Aby wygenerować te identyfikatory URI, potrzebne są następujące informacje:
* Identyfikator dzierżawy (np. contoso.onmicrosoft.com)
* Przepływ użytkownika (na przykład B2C\_1\_SignUpIn)

Punkt końcowy tokenu identyfikatora URI mogą być generowane przez zastąpienie dzierżawy\_identyfikator i zasady\_nazwy w następującym adresem URL:

```objc
static NSString *const tokenEndpoint = @"https://<Tenant_name>.b2clogin.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/token";
```

Punkt końcowy autoryzacji, które można wygenerować identyfikatora URI, zastępując dzierżawy\_identyfikator i zasady\_nazwy w następującym adresem URL:

```objc
static NSString *const authorizationEndpoint = @"https://<Tenant_name>.b2clogin.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/authorize";
```

Uruchom następujący kod, aby utworzyć obiekt AuthorizationServiceConfiguration:

```objc
OIDServiceConfiguration *configuration = 
    [[OIDServiceConfiguration alloc] initWithAuthorizationEndpoint:authorizationEndpoint tokenEndpoint:tokenEndpoint];
// now we are ready to perform the auth request...
```

### <a name="authorizing"></a>Autoryzowanie

Po Konfigurowanie lub pobieranie konfiguracji usługi autoryzacji, można konstruować żądanie autoryzacji. Aby utworzyć żądanie, potrzebne są następujące informacje:  
* Identyfikator klienta (na przykład 00000000-0000-0000-0000-000000000000)
* Identyfikator URI przekierowania ze schematem niestandardowym (na przykład com.onmicrosoft.fabrikamb2c.exampleapp://oauth/redirect)

Oba elementy powinny zostały zapisane po użytkownik zostały [rejestrując aplikację](#create-an-application).

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

Aby skonfigurować aplikację w celu obsługi przekierowania do identyfikatora URI ze schematem niestandardowym, należy zaktualizować listę "Schematy adresów URL" w pliku Info.pList:
* Otwórz plik Info.pList.
* Umieść kursor nad wiersz, takie jak "Kod typu systemu operacyjnego pakietu", a następnie kliknij przycisk \+ symboli.
* Zmień nazwę nowego wiersza "Typy adresów URL".
* Kliknij strzałkę po lewej stronie "Typy adresów URL" Aby otworzyć drzewa.
* Kliknij strzałkę po lewej stronie "elementu 0", aby otworzyć drzewa.
* Zmień nazwę pierwszego elementu poniżej elementu 0 do "Schematy adresów URL".
* Kliknij strzałkę po lewej stronie "Schematy adresów URL", aby otworzyć drzewa.
* W kolumnie 'Value' jest puste pole na lewo od elementu 0 poniżej "Schematy adresów URL".  Ustaw wartość na schemat unikatowy Twojej aplikacji.  Wartość musi odpowiadać używany w redirectURL podczas tworzenia obiektu OIDAuthorizationRequest schemat.  W tym przykładzie używany jest schemat "com.onmicrosoft.fabrikamb2c.exampleapp".

Zapoznaj się [przewodnik AppAuth](https://openid.github.io/AppAuth-iOS/) na temat sposobu ukończenia pozostałej części procesu. Jeśli potrzebujesz szybko rozpocząć pracę z działającą aplikację, zapoznaj się z [próbki](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c). Postępuj zgodnie z instrukcjami w [README.md](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md) wprowadzić konfigurację usługi Azure AD B2C.

Firma Microsoft zawsze są otwarte opinie i sugestie! Jeśli mają trudności z tym artykułem lub poprawić tę zawartość, prosimy o wyrażenie opinii na dole strony. Żądania funkcji, dodaj je do [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).
