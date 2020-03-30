---
title: Używanie aplikacji AppAuth w aplikacji dla systemu iOS
titleSuffix: Azure AD B2C
description: Jak utworzyć aplikację dla systemu iOS, która używa appauth z usługi Azure Active Directory B2C do zarządzania tożsamościami użytkowników i uwierzytelniania użytkowników.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c659280ebc8c91b53cbc3a176c84397edd942c23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186832"
---
# <a name="azure-ad-b2c-sign-in-using-an-ios-application"></a>Usługa Azure AD B2C: logowanie się przy użyciu aplikacji na iOS

Platforma Microsoft Identity korzysta z otwartych standardów, takich jak OAuth2 i OpenID Connect. Korzystanie z otwartego standardowego protokołu oferuje większy wybór programistów przy wyborze biblioteki do integracji z naszymi usługami. Udostępniliśmy ten instruktaż i inne podobne, aby ułatwić deweloperom pisanie aplikacji łączących się z platformą Microsoft Identity. Większość bibliotek, które implementują [specyfikację OAuth2 RFC6749,](https://tools.ietf.org/html/rfc6749) może łączyć się z platformą Microsoft Identity.

> [!WARNING]
> Firma Microsoft nie udostępnia poprawek dla bibliotek innych firm i nie dokonała przeglądu tych bibliotek. W tym przykładzie jest przy użyciu biblioteki innej firmy o nazwie AppAuth, który został przetestowany pod kątem zgodności w podstawowych scenariuszach z usługi Azure AD B2C. Problemy i żądania funkcji powinny być kierowane do projektu open source biblioteki. Aby uzyskać więcej informacji, zobacz [ten artykuł](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries).
>
>

Jeśli jesteś nowy w OAuth2 lub OpenID Connect, wiele z tej przykładowej konfiguracji może nie mieć większego sensu dla Ciebie. Zalecamy zapoznanie się z [krótkim omówieniem protokołu w tej dokumentacji](protocols-overview.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Tworzenie katalogu usługi Azure AD B2C
Przed rozpoczęciem korzystania z usługi Azure AD B2C należy utworzyć katalog lub dzierżawę. Katalog jest kontenerem dla wszystkich użytkowników, aplikacji, grup i innych. Jeśli jeszcze go nie masz, [utwórz katalog usługi B2C](tutorial-create-tenant.md), zanim przejdziesz dalej.

## <a name="create-an-application"></a>Tworzenie aplikacji

Następnie zarejestruj aplikację w dzierżawie usługi Azure AD B2C. Dzięki temu usługa Azure AD informacje potrzebne do bezpiecznej komunikacji z aplikacją.

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

Zarejestruj **identyfikator aplikacji (klienta)** do użycia w późniejszym kroku.

Również nagrać niestandardowy identyfikator URI przekierowania do użycia w późniejszym kroku. Na przykład `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

## <a name="create-your-user-flows"></a>Tworzenie przepływów użytkownika
W usłudze Azure AD B2C każde środowisko użytkownika jest definiowane przez [przepływ użytkownika.](user-flow-overview.md) Ta aplikacja zawiera jedno środowisko tożsamości: połączone logowanie i rejestrację. Podczas tworzenia przepływu użytkownika należy pamiętać o:

* W obszarze **Atrybuty rejestracji**wybierz atrybut **Nazwa wyświetlana**.  Można również wybrać inne atrybuty.
* W obszarze **Oświadczenia aplikacji**wybierz **reklamę Wyświetlaną i identyfikator** obiektu **użytkownika**. Można również wybrać inne oświadczenia.
* Skopiuj **nazwę** każdego przepływu użytkownika po jego utworzeniu. Nazwa przepływu użytkownika jest poprzedzony `b2c_1_` podczas zapisywania przepływu użytkownika.  Później potrzebna jest nazwa przepływu użytkownika.

Po utworzeniu przepływów użytkownika możesz przystąpić do tworzenia aplikacji.

## <a name="download-the-sample-code"></a>Pobierz przykładowy kod
Udostępniliśmy próbkę roboczą, która używa appauth z usługą Azure AD B2C [w usłudze GitHub.](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c) Możesz pobrać kod i uruchomić go. Aby użyć własnej dzierżawy usługi Azure AD B2C, postępuj zgodnie z instrukcjami w [README.md](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md).

Ten przykład został utworzony przez następujące instrukcje README przez [projekt iOS AppAuth na GitHub](https://github.com/openid/AppAuth-iOS). Aby uzyskać więcej informacji na temat pracy próbki i biblioteki, odwołaj się do AppAuth README w usłudze GitHub.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Modyfikowanie aplikacji w celu używania usługi Azure AD B2C z aplikacją AppAuth

> [!NOTE]
> AppAuth obsługuje system iOS 7 lub wyższy.  Jednak do obsługi loginów społecznościowych w Google potrzebny jest SFSafariViewController, który wymaga systemu iOS 9 lub nowszego.
>

### <a name="configuration"></a>Konfigurowanie

Można skonfigurować komunikację z usługą Azure AD B2C, określając zarówno identyfikatory identyfikatorów URI punktu końcowego autoryzacji, jak i tokenu końcowego.  Aby wygenerować te identyfikatory URI, potrzebne są następujące informacje:
* Identyfikator dzierżawy (na przykład contoso.onmicrosoft.com)
* Nazwa przepływu użytkownika (na przykład\_B2C 1\_SignUpIn)

Identyfikator URI punktu końcowego tokenu\_można wygenerować, zastępując identyfikator dzierżawy i nazwę zasad\_w następującym adresie URL:

```objc
static NSString *const tokenEndpoint = @"https://<Tenant_name>.b2clogin.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/token";
```

Identyfikator URI punktu końcowego autoryzacji\_można wygenerować, zastępując identyfikator dzierżawy i nazwę zasad\_w następującym adresie URL:

```objc
static NSString *const authorizationEndpoint = @"https://<Tenant_name>.b2clogin.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/authorize";
```

Uruchom następujący kod, aby utworzyć obiekt autoryzacji Usługi konfiguracyjne:

```objc
OIDServiceConfiguration *configuration =
    [[OIDServiceConfiguration alloc] initWithAuthorizationEndpoint:authorizationEndpoint tokenEndpoint:tokenEndpoint];
// now we are ready to perform the auth request...
```

### <a name="authorizing"></a>Autoryzowania

Po skonfigurowaniu lub pobraniu konfiguracji usługi autoryzacji można utworzyć żądanie autoryzacji. Aby utworzyć żądanie, potrzebne są następujące informacje:

* Identyfikator klienta (application id), który został nagrany wcześniej. Na przykład `00000000-0000-0000-0000-000000000000`.
* Niestandardowy identyfikator URI przekierowania, który został nagrany wcześniej. Na przykład `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

Oba elementy powinny zostać zapisane podczas [rejestracji aplikacji.](#create-an-application)

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

Aby skonfigurować aplikację do obsługi przekierowania do identyfikatora URI za pomocą schematu niestandardowego, należy zaktualizować listę "Schematy adresów URL" na liście Info.pList:
* Otwórz listę Info.pList.
* Umieść wskaźnik myszy na wierszu takim jak \+ "Kod typu pakietu OS" i kliknij symbol.
* Zmień nazwę nowego wiersza "Typy adresów URL".
* Kliknij strzałkę po lewej stronie "Typy adresów URL", aby otworzyć drzewo.
* Kliknij strzałkę po lewej stronie "Element 0", aby otworzyć drzewo.
* Zmień nazwę pierwszego elementu pod elementem 0 na "Schematy adresów URL".
* Kliknij strzałkę po lewej stronie "Schematy adresów URL", aby otworzyć drzewo.
* W kolumnie "Wartość" po lewej stronie pola "Element 0" znajduje się pole puste pod "Schematy adresów URL".  Ustaw wartość unikatowego schematu aplikacji.  Wartość musi być zgodna ze schematem używanym w redirectURL podczas tworzenia obiektu OIDAuthorizationRequest.  W przykładzie jest używany schemat "com.onmicrosoft.fabrikamb2c.exampleapp".

Zapoznaj się z [przewodnikiem AppAuth,](https://openid.github.io/AppAuth-iOS/) jak ukończyć resztę procesu. Jeśli chcesz szybko rozpocząć pracę z działającą aplikacją, zapoznaj się [z próbką](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c). Wykonaj kroki opisane w [README.md,](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md) aby wprowadzić własną konfigurację usługi Azure AD B2C.

Jesteśmy zawsze otwarci na opinie i sugestie! Jeśli masz jakiekolwiek trudności z tym artykułem lub masz zalecenia dotyczące ulepszania tej zawartości, będziemy wdzięczni za twoją opinię na dole strony. W przypadku żądań funkcji dodaj je do [uservoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).
