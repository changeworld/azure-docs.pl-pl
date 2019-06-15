---
title: Utwórz wystąpienie aplikacji poufne klienta, za pomocą opcji (Biblioteka Microsoft Authentication Library dla platformy .NET) | Azure
description: Dowiedz się, jak tworzyć wystąpienia aplikacji poufne klienta przy użyciu opcji konfiguracji przy użyciu Biblioteka Microsoft Authentication Library for .NET (platformy MSAL.NET).
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
ms.date: 04/30/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f935b1b2815501710444e3f921a157ba02e3215
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65544089"
---
# <a name="instantiate-a-confidential-client-application-with-configuration-options-using-msalnet"></a>Utwórz wystąpienie aplikacji zawierających poufne dane klienta z opcjami konfiguracji przy użyciu platformy MSAL.NET

W tym artykule opisano sposób tworzenia wystąpienia [poufne klienta aplikacji](msal-client-applications.md) przy użyciu Biblioteka Microsoft Authentication Library for .NET (platformy MSAL.NET).  Aplikacja jest utworzone za pomocą opcji konfiguracji zdefiniowanej w pliku ustawień.

Przed inicjowania aplikacji, musisz najpierw [zarejestrować](quickstart-register-app.md) go tak, aby aplikację można zintegrować z platformą Microsoft identity. Po zarejestrowaniu może być konieczne następujące informacje (które można znaleźć w witrynie Azure portal):

- Identyfikator klienta (ciąg reprezentujący identyfikator GUID)
- Adres URL dostawcy tożsamości (nazwanego wystąpienia) i grupy odbiorców logowania dla swojej aplikacji. Te dwa parametry są nazywane zbiorczo urzędu.
- Identyfikator dzierżawy, jeśli piszesz linię aplikacji biznesowej wyłącznie na potrzeby Twojej organizacji (również o nazwie aplikacja jednej dzierżawy).
- Klucz tajny aplikacji (ciąg klucza tajnego klienta) lub certyfikatu (typ X509Certificate2), jeśli jest to aplikacja poufne klienta.
- Dla aplikacji sieci web, a czasami dla aplikacji publicznych klienta (w szczególności, gdy Twoja aplikacja wymaga pod kątem wykorzystania brokera) będzie również ustawiono element redirectUri gdzie dostawcy tożsamości skontaktuje się z powrotem aplikacji przy użyciu tokenów zabezpieczających.

## <a name="configure-the-application-from-the-config-file"></a>Konfigurowanie aplikacji z pliku konfiguracji
Nazwa właściwości opcje platformy MSAL.NET odpowiadać nazwa właściwości `AzureADOptions` w programie ASP.NET Core, więc nie trzeba pisać kodu pośredniczącego.

Konfiguracja aplikacji ASP.NET Core jest opisana w *appsettings.json* pliku:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "[Enter the domain of your tenant, e.g. contoso.onmicrosoft.com]",
    "TenantId": "[Enter 'common', or 'organizations' or the Tenant Id (Obtained from the Azure portal. Select 'Endpoints' from the 'App registrations' blade and use the GUID in any of the URLs), e.g. da41245a5-11b3-996c-00a8-4d99re19f292]",
    "ClientId": "[Enter the Client Id (Application ID obtained from the Azure portal), e.g. ba74781c2-53c2-442a-97c2-3d60re42f403]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc",

    "ClientSecret": "[Copy the client secret added to the app from the Azure portal]"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

Począwszy od platformy MSAL.NET v3.x, można skonfigurować aplikacji zawierających poufne dane klienta z pliku konfiguracji. Klasy związane z konfiguracją aplikacji znajdują się w `Microsoft.Identity.Client.AppConfig` przestrzeni nazw.

W klasie, w której chcesz skonfigurować i utworzyć wystąpienie aplikacji, trzeba zadeklarować `ConfidentialClientApplicationOptions` obiektu.  Konfiguracja odczytu ze źródła (w tym pliku appconfig.json) należy powiązać wystąpienia Opcje aplikacji:

```csharp
using Microsoft.Identity.Client.AppConfig;

private ConfidentialClientApplicationOptions _applicationOptions;
_applicationOptions = new ConfidentialClientApplicationOptions();
configuration.Bind("AzureAD", _applicationOptions);
```

Dzięki temu zawartość w sekcji "AzureAD" *appsettings.json* pliku może być powiązane z odpowiedniej właściwości `ConfidentialClientApplicationOptions` obiektu.  Następnie kompilacji `ConfidentialClientApplication` obiektu:

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
        .Build();
```

## <a name="add-runtime-configuration"></a>Dodaj konfigurację środowiska uruchomieniowego
W przypadku aplikacji poufnych klienta zazwyczaj mają pamięci podręcznej na użytkownika. W związku z tym należy uzyskać pamięci podręcznej skojarzonych z użytkownikiem i informować Konstruktor aplikacji mają z niego korzystać. W ten sam sposób może być dynamicznie obliczaną identyfikator URI przekierowania. W takim przypadku ten kod jest następująca:

```csharp
IConfidentialClientApplication app;
var request = httpContext.Request;
var currentUri = UriHelper.BuildAbsolute(request.Scheme, request.Host, request.PathBase, _azureAdOptions.CallbackPath ?? string.Empty);
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
       .WithRedirectUri(currentUri)
       .Build();
TokenCache userTokenCache = _tokenCacheProvider.SerializeCache(app.UserTokenCache,httpContext, claimsPrincipal);
```

