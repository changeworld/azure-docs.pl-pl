---
title: Korzystanie z biblioteki Microsoft Authentication Library (MSAL) w chmurach narodowych — platforma tożsamości firmy Microsoft
description: Biblioteka Microsoft Authentication Library (MSAL) umożliwia deweloperom aplikacji uzyskanie tokenów w celu wywołania zabezpieczonych interfejsów API sieci Web. Te interfejsy API sieci Web mogą być Microsoft Graph, inne interfejsy API firmy Microsoft, interfejsy API sieci Web partnera lub własny internetowy interfejs API. MSAL obsługuje wiele architektur aplikacji i platform.
services: active-directory
documentationcenter: dev-center-name
author: negoe
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97855a52831a63a92a46bd0d25d23ba3fc91a07b
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268576"
---
# <a name="use-msal-in-a-national-cloud-environment"></a>Korzystanie z MSAL w środowisku chmury krajowej

[Chmury krajowe](authentication-national-cloud.md) to fizyczne izolowane wystąpienia platformy Azure. Te regiony pomocy systemu Azure zapewniają, że wymagania dotyczące miejsca zamieszkania, suwerenności i zgodności są honorowane w granicach geograficznych.

Oprócz chmury Microsoft Worldwide Library, biblioteka uwierzytelniania firmy Microsoft (MSAL) umożliwia deweloperom aplikacji w chmurach narodowych uzyskanie tokenów w celu uwierzytelniania i wywoływania zabezpieczonych interfejsów API sieci Web. Te interfejsy API sieci Web mogą być Microsoft Graph lub innych interfejsów API firmy Microsoft.

W tym chmurę globalną usługa Azure Active Directory (Azure AD) jest wdrażana w następujących chmurach narodowych:  

- Azure Government
- Azure w Chinach — 21Vianet
- Azure (Niemcy)

W tym przewodniku pokazano, jak zalogować się do kont służbowych, uzyskać token dostępu i wywołać interfejs API Microsoft Graph w środowisku [chmury Azure Government](https://azure.microsoft.com/global-infrastructure/government/) .

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że spełniono te wymagania wstępne.

### <a name="choose-the-appropriate-identities"></a>Wybierz odpowiednie tożsamości

Aplikacje [Azure Government](https://docs.microsoft.com/azure/azure-government/) mogą uwierzytelniać użytkowników przy użyciu tożsamości instytucji rządowych usługi Azure AD i publicznych tożsamości usługi Azure AD. Ponieważ można użyć dowolnej z tych tożsamości, należy zdecydować, który punkt końcowy urzędu należy wybrać dla danego scenariusza:

- Publiczna usługa Azure AD: Często używane, jeśli organizacja ma już dzierżawę publiczną usługi Azure AD do obsługi pakietu Office 365 (Public lub w zatoce) lub innej aplikacji.
- Usługa Azure AD dla instytucji rządowych: Często używane, jeśli organizacja ma już dzierżawcę dla instytucji rządowych usługi Azure AD w celu obsługi pakietu Office 365 (w serwisie lub wyższej), lub tworzy nową dzierżawę w usłudze Azure AD dla instytucji rządowych.

Po podjęciu decyzji, szczególnym zagadnieniem jest przeprowadzenie rejestracji aplikacji. Jeśli wybierzesz tożsamość publiczną usługi Azure AD dla aplikacji Azure Government, musisz zarejestrować aplikację w dzierżawie publicznej usługi Azure AD.

### <a name="get-an-azure-government-subscription"></a>Pobierz subskrypcję usługi Azure Government

Aby uzyskać subskrypcję Azure Government, zobacz [Zarządzanie subskrypcją w programie Azure Government i nawiązywanie z nią połączenia](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-subscriptions).

Jeśli nie masz subskrypcji Azure Government, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/global-infrastructure/government/request/) .

## <a name="javascript"></a>JavaScript

### <a name="step-1-register-your-application"></a>Krok 1: Zarejestruj swoją aplikację

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.us/).
    
   Aby znaleźć Azure Portal punkty końcowe dla innych chmur narodowych, zobacz [punkty końcowe rejestracji aplikacji](authentication-national-cloud.md#app-registration-endpoints).

1. Jeśli Twoje konto zapewnia dostęp do więcej niż jednej dzierżawy, wybierz swoje konto w prawym górnym rogu, a następnie ustaw sesję portalu na żądaną dzierżawę usługi Azure AD.
1. Przejdź do strony [rejestracje aplikacji](https://aka.ms/ra/ff) na platformie tożsamości firmy Microsoft dla deweloperów.
1. Po wyświetleniu strony **Rejestrowanie aplikacji** wprowadź nazwę aplikacji.
1. W obszarze **obsługiwane typy kont**wybierz pozycję **konta w dowolnym katalogu organizacyjnym**.
1. W sekcji **Identyfikator URI przekierowania** Wybierz platformę **sieci Web** i ustaw wartość na adres URL aplikacji na podstawie serwera sieci Web. Zobacz następne sekcje, aby uzyskać instrukcje dotyczące ustawiania i uzyskiwania adresu URL przekierowania w programie Visual Studio i węźle.
1. Wybierz pozycję **Zarejestruj**.
1. Na stronie **Przegląd** aplikacji zanotuj wartość **Identyfikator aplikacji (klienta)** .
1. Ten samouczek wymaga włączenia [niejawnego przepływu dotacji](v2-oauth2-implicit-grant-flow.md). W lewym okienku zarejestrowanej aplikacji wybierz pozycję **uwierzytelnianie**.
1. W obszarze **Ustawienia zaawansowane**w obszarze **niejawne przyznanie**zaznacz pola wyboru **tokeny identyfikatorów** i **tokeny dostępu** . Tokeny identyfikatorów i tokeny dostępu są wymagane, ponieważ ta aplikacja musi zalogować użytkowników i wywołać interfejs API.
1. Wybierz pozycję **Zapisz**.

### <a name="step-2--set-up-your-web-server-or-project"></a>Krok 2:  Konfigurowanie serwera lub projektu sieci Web

- [Pobierz pliki projektu](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip) dla lokalnego serwera sieci Web, na przykład węzeł.

  lub

- [Pobierz projekt programu Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).

Następnie pominięcie, aby [skonfigurować swój Spa JavaScript](#step-4-configure-your-javascript-spa) w celu skonfigurowania przykładu kodu przed jego uruchomieniem.

### <a name="step-3-use-the-microsoft-authentication-library-to-sign-in-the-user"></a>Krok 3: Logowanie użytkownika przy użyciu biblioteki uwierzytelniania firmy Microsoft

Wykonaj kroki opisane w [samouczku języka JavaScript](tutorial-v2-javascript-spa.md#create-your-project) , aby utworzyć projekt i zintegrować go z MSAL, aby zalogować użytkownika.

### <a name="step-4-configure-your-javascript-spa"></a>Krok 4: Konfigurowanie protokołu JavaScript SPA

`index.html` W pliku utworzonym podczas instalacji projektu Dodaj informacje o rejestracji aplikacji. Dodaj następujący kod w górnej części `<script></script>` tagów w treści `index.html` pliku:

```javascript
const msalConfig = {
    auth:{
        clientId: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here",
        }
}

const graphConfig = {
        graphEndpoint: "https://graph.microsoft.us",
        graphScopes: ["user.read"],
}

// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(msalConfig);
```

W tym kodzie:

- `Enter_the_Application_Id_here`jest wartością **identyfikatora aplikacji (klienta)** dla zarejestrowanej aplikacji.
- `Enter_the_Tenant_Info_Here`jest ustawiona na jedną z następujących opcji:
    - Jeśli aplikacja obsługuje **konta w tym katalogu organizacyjnym**, Zastąp tę wartość identyfikatorem dzierżawy lub nazwą dzierżawy (na przykład contoso.Microsoft.com).
    - Jeśli aplikacja obsługuje **konta w dowolnym katalogu organizacyjnym**, Zastąp tę wartość `organizations`wartością.
    
    Aby znaleźć punkty końcowe uwierzytelniania dla wszystkich chmur narodowych, zobacz [punkty końcowe uwierzytelniania usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints).

    > [!NOTE]
    > Osobiste konta Microsoft nie są obsługiwane w chmurach krajowych.
  
- `graphEndpoint`jest punktem końcowym Microsoft Graph w chmurze firmy Microsoft dla instytucji rządowych USA.

   Aby znaleźć Microsoft Graph punkty końcowe dla wszystkich chmur narodowych, zobacz [Microsoft Graph punktów końcowych w chmurach krajowych](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

## <a name="net"></a>.NET

Możesz użyć MSAL.NET, aby zalogować użytkowników, uzyskać tokeny i wywołać interfejs API Microsoft Graph w chmurach krajowych.

W poniższych samouczkach pokazano, jak utworzyć aplikację sieci Web platformy .NET Core 2,2 MVC. Aplikacja używa usługi OpenID Connect Connect do logowania użytkowników przy użyciu konta służbowego w organizacji, która należy do chmury krajowej.

- Aby zalogować użytkowników i uzyskać tokeny, postępuj zgodnie z [tym samouczkiem](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-4-Sovereign#build-an-aspnet-core-web-app-signing-in-users-in-sovereign-clouds-with-the-microsoft-identity-platform).
- Aby wywołać interfejs API Microsoft Graph, postępuj zgodnie z [tym samouczkiem](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-4-Sovereign-Call-MSGraph#using-the-microsoft-identity-platform-to-call-the-microsoft-graph-api-from-an-an-aspnet-core-2x-web-app-on-behalf-of-a-user-signing-in-using-their-work-and-school-account-in-microsoft-national-cloud).

## <a name="msal-for-ios-and-macos"></a>MSAL dla systemów iOS i macOS

MSAL dla systemów iOS i macOS mogą być używane do uzyskiwania tokenów w chmurach narodowych, ale wymaga dodatkowej `MSALPublicClientApplication`konfiguracji podczas tworzenia.

Na przykład jeśli chcesz, aby aplikacja była aplikacją wielodostępną w chmurze krajowej (w tym przypadku dla instytucji rządowych USA), możesz napisać:

Cel-C:

```objc
MSALAADAuthority *aadAuthority =
                [[MSALAADAuthority alloc] initWithCloudInstance:MSALAzureUsGovernmentCloudInstance
                                                   audienceType:MSALAzureADMultipleOrgsAudience
                                                      rawTenant:nil
                                                          error:nil];
                                                          
MSALPublicClientApplicationConfig *config =
                [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"
                                                                redirectUri:@"<your-redirect-uri-here>"
                                                                  authority:aadAuthority];
                                                                  
NSError *applicationError = nil;
MSALPublicClientApplication *application =
                [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&applicationError];
```

Adres

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)
        
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o usługach:

- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure — Chiny](https://docs.microsoft.com/azure/china/)
- [Azure (Niemcy)](https://docs.microsoft.com/azure/germany/)
