---
title: Use MSAL in a national cloud app | Azure
titleSuffix: Microsoft identity platform
description: Microsoft Authentication Library (MSAL) enables application developers to acquire tokens in order to call secured web APIs. These web APIs can be Microsoft Graph, other Microsoft APIs, partner web APIs, or your own web API. MSAL supports multiple application architectures and platforms.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: dfca2b1311f1b55f19d5709f7c9ca7c3e366769c
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76695742"
---
# <a name="use-msal-in-a-national-cloud-environment"></a>Use MSAL in a national cloud environment

[National clouds](authentication-national-cloud.md), also known as Sovereign clouds, are physically isolated instances of Azure. These regions of Azure help make sure that data residency, sovereignty, and compliance requirements are honored within geographical boundaries.

In addition to the Microsoft worldwide cloud, the Microsoft Authentication Library (MSAL) enables application developers in national clouds to acquire tokens in order to authenticate and call secured web APIs. These web APIs can be Microsoft Graph or other Microsoft APIs.

Including the global cloud, Azure Active Directory (Azure AD) is deployed in the following national clouds:  

- Platforma Azure dla instytucji rządowych
- Azure w Chinach — 21Vianet
- Azure (Niemcy)

This guide demonstrates how to sign in to work and school accounts, get an access token, and call the Microsoft Graph API in the [Azure Government cloud](https://azure.microsoft.com/global-infrastructure/government/) environment.

## <a name="prerequisites"></a>Wymagania wstępne

Before you start, make sure that you meet these prerequisites.

### <a name="choose-the-appropriate-identities"></a>Choose the appropriate identities

[Azure Government](https://docs.microsoft.com/azure/azure-government/) applications can use Azure AD Government identities and Azure AD Public identities to authenticate users. Because you can use any of these identities, you need to decide which authority endpoint you should choose for your scenario:

- Azure AD Public: Commonly used if your organization already has an Azure AD Public tenant to support Office 365 (Public or GCC) or another application.
- Azure AD Government: Commonly used if your organization already has an Azure AD Government tenant to support Office 365 (GCC High or DoD) or is creating a new tenant in Azure AD Government.

After you decide, a special consideration is where you perform your app registration. If you choose Azure AD Public identities for your Azure Government application, you must register the application in your Azure AD Public tenant.

### <a name="get-an-azure-government-subscription"></a>Get an Azure Government subscription

To get an Azure Government subscription, see [Managing and connecting to your subscription in Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-subscriptions).

If you don't have an Azure Government subscription, create a [free account](https://azure.microsoft.com/global-infrastructure/government/request/) before you begin.

For details about using a national cloud with a particular programming language, choose the tab matching your language:

## <a name="nettabdonet"></a>[.NET](#tab/donet)

You can use MSAL.NET to sign in users, acquire tokens, and call the Microsoft Graph API in national clouds.

The following tutorials demonstrate how to build a .NET Core 2.2 MVC Web app. The app uses OpenID Connect to sign in users with a work and school account in an organization that belongs to a national cloud.

- To sign in users and acquire tokens, follow this tutorial: [Build an ASP.NET Core Web app signing-in users in sovereign clouds with the Microsoft identity platform](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-4-Sovereign#build-an-aspnet-core-web-app-signing-in-users-in-sovereign-clouds-with-the-microsoft-identity-platform).
- To call the Microsoft Graph API, follow this tutorial: [Using the Microsoft identity platform to call the Microsoft Graph API from an An ASP.NET Core 2.x Web App, on behalf of a user signing-in using their work and school account in Microsoft National Cloud](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-4-Sovereign-Call-MSGraph#using-the-microsoft-identity-platform-to-call-the-microsoft-graph-api-from-an-an-aspnet-core-2x-web-app-on-behalf-of-a-user-signing-in-using-their-work-and-school-account-in-microsoft-national-cloud).

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

To enable your MSAL.js application for sovereign clouds:

### <a name="step-1-register-your-application"></a>Krok 1. Rejestrowanie aplikacji

1. Zaloguj się do [portalu Azure](https://portal.azure.us/).
    
   To find Azure portal endpoints for other national clouds, see [App registration endpoints](authentication-national-cloud.md#app-registration-endpoints).

1. If your account gives you access to more than one tenant, select your account in the upper-right corner, and set your portal session to the desired Azure AD tenant.
1. Go to the [App registrations](https://aka.ms/ra/ff) page on the Microsoft identity platform for developers.
1. Po wyświetleniu strony **Rejestrowanie aplikacji** wprowadź nazwę aplikacji.
1. Under **Supported account types**, select **Accounts in any organizational directory**.
1. In the **Redirect URI** section, select the **Web** platform and set the value to the application's URL based on your web server. See the next sections for instructions on how to set and obtain the redirect URL in Visual Studio and Node.
1. Wybierz pozycję **Zarejestruj**.
1. Na stronie **Przegląd** aplikacji zanotuj wartość **Identyfikator aplikacji (klienta)** .
1. This tutorial requires you to enable the [implicit grant flow](v2-oauth2-implicit-grant-flow.md). In the left pane of the registered application, select **Authentication**.
1. In **Advanced settings**, under **Implicit grant**, select the **ID tokens** and **Access tokens** check boxes. ID tokens and access tokens are required because this app needs to sign in users and call an API.
1. Wybierz pozycję **Zapisz**.

### <a name="step-2--set-up-your-web-server-or-project"></a>Step 2:  Set up your web server or project

- [Download the project files](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip) for a local web server, such as Node.

  lub

- [Download the Visual Studio project](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).

Then skip to [Configure your JavaScript SPA](#step-4-configure-your-javascript-spa) to configure the code sample before running it.

### <a name="step-3-use-the-microsoft-authentication-library-to-sign-in-the-user"></a>Krok 3. zalogowanie użytkownika przy użyciu biblioteki uwierzytelniania firmy Microsoft

Wykonaj kroki opisane w [samouczku języka JavaScript](tutorial-v2-javascript-spa.md#create-your-project) , aby utworzyć projekt i zintegrować go z MSAL, aby zalogować użytkownika.

### <a name="step-4-configure-your-javascript-spa"></a>Krok 4. Konfigurowanie protokołu JavaScript SPA

W pliku `index.html` utworzonym podczas instalacji projektu Dodaj informacje o rejestracji aplikacji. Dodaj następujący kod w górnej części tagów `<script></script>` w treści pliku `index.html`:

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

- `Enter_the_Application_Id_here` to wartość **identyfikatora aplikacji (klienta)** dla zarejestrowanej aplikacji.
- `Enter_the_Tenant_Info_Here` jest ustawiona na jedną z następujących opcji:
    - Jeśli aplikacja obsługuje **konta w tym katalogu organizacyjnym**, Zastąp tę wartość identyfikatorem dzierżawy lub nazwą dzierżawy (na przykład contoso.Microsoft.com).
    - Jeśli aplikacja obsługuje **konta w dowolnym katalogu organizacyjnym**, Zastąp tę wartość `organizations`.
    
    Aby znaleźć punkty końcowe uwierzytelniania dla wszystkich chmur narodowych, zobacz [punkty końcowe uwierzytelniania usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints).

    > [!NOTE]
    > Osobiste konta Microsoft nie są obsługiwane w chmurach krajowych.
  
- `graphEndpoint` to Microsoft Graph punkt końcowy dla chmury firmy Microsoft dla instytucji rządowych USA.

   Aby znaleźć Microsoft Graph punkty końcowe dla wszystkich chmur narodowych, zobacz [Microsoft Graph punktów końcowych w chmurach krajowych](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

## <a name="pythontabpython"></a>[Python](#tab/python)

Aby włączyć aplikację MSAL języka Python dla suwerennych chmur:

- Zarejestruj swoją aplikację w określonym portalu, w zależności od chmury. Aby uzyskać więcej informacji na temat wybierania portalu odwołań do [punktów końcowych rejestracji aplikacji](authentication-national-cloud.md#app-registration-endpoints)
- Użyj dowolnych [przykładów](https://github.com/AzureAD/microsoft-authentication-library-for-python/tree/dev/sample) z repozytorium z kilkoma zmianami w konfiguracji, w zależności od chmury, która jest wspomniana dalej.
- Użyj określonego urzędu w zależności od chmury, w której zarejestrowano aplikację. Aby uzyskać więcej informacji na temat urzędów dla różnych chmur, odwołaj się do [punktów końcowych uwierzytelniania usługi Azure AD](authentication-national-cloud.md#azure-ad-authentication-endpoints).

    Oto przykładowa władza:

    ```json
    "authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
    ```
    
- Aby wywołać program Microsoft Graph, wymagany jest konkretny adres URL punktu końcowego grafu, który zależy od używanej chmury. Aby znaleźć Microsoft Graph punkty końcowe dla wszystkich chmur narodowych, zapoznaj się z [głównymi punktami końcowymi usługi Microsoft Graph i Eksplorator grafów](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

    Oto przykład punktu końcowego grafu z zakresem:
    
    ```json
    "endpoint" : "https://graph.microsoft.us/v1.0/me"
    "scope": "User.Read"
    ```
    
## <a name="javatabjava"></a>[Java](#tab/java)

Aby włączyć MSAL dla aplikacji Java dla suwerennych chmur:

- Zarejestruj swoją aplikację w określonym portalu, w zależności od chmury. Aby uzyskać więcej informacji na temat wybierania portalu odwołań do [punktów końcowych rejestracji aplikacji](authentication-national-cloud.md#app-registration-endpoints)
- Użyj dowolnych [przykładów](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples) z repozytorium z kilkoma zmianami w konfiguracji, w zależności od chmury, które są wymienione dalej.
- Użyj określonego urzędu w zależności od chmury, w której zarejestrowano aplikację. Aby uzyskać więcej informacji na temat urzędów dla różnych chmur, odwołaj się do [punktów końcowych uwierzytelniania usługi Azure AD](authentication-national-cloud.md#azure-ad-authentication-endpoints).

Oto przykładowa władza:

```json
"authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
```

- Aby wywołać program Microsoft Graph, wymagany jest konkretny adres URL punktu końcowego grafu, który zależy od używanej chmury. Aby znaleźć Microsoft Graph punkty końcowe dla wszystkich chmur narodowych, zapoznaj się z [głównymi punktami końcowymi usługi Microsoft Graph i Eksplorator grafów](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

Oto przykład punktu końcowego grafu z zakresem:

```json
"endpoint" : "https://graph.microsoft.us/v1.0/me"
"scope": "User.Read"
```

## <a name="objective-ctabobjc"></a>[Objective-C](#tab/objc)

MSAL dla systemów iOS i macOS mogą być używane do uzyskiwania tokenów w chmurach narodowych, ale wymaga dodatkowej konfiguracji podczas tworzenia `MSALPublicClientApplication`.

Na przykład jeśli chcesz, aby aplikacja była aplikacją wielodostępną w chmurze krajowej (w tym przypadku dla instytucji rządowych USA), możesz napisać:

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

## <a name="swifttabswift"></a>[Swift](#tab/swift)

MSAL dla systemów iOS i macOS mogą być używane do uzyskiwania tokenów w chmurach narodowych, ale wymaga dodatkowej konfiguracji podczas tworzenia `MSALPublicClientApplication`.

Na przykład jeśli chcesz, aby aplikacja była aplikacją wielodostępną w chmurze krajowej (w tym przypadku dla instytucji rządowych USA), możesz napisać:

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)
        
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```

---

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o usługach:

- [Uwierzytelnianie w chmurach narodowych](authentication-national-cloud.md)
- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure — Chiny](https://docs.microsoft.com/azure/china/)
- [Azure (Niemcy)](https://docs.microsoft.com/azure/germany/)
