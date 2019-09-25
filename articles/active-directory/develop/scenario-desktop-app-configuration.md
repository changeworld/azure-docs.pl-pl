---
title: Aplikacja klasyczna, która wywołuje interfejsy API sieci Web (Konfiguracja kodu) — Microsoft Identity platform
description: Dowiedz się, jak utworzyć aplikację klasyczną wywołującą interfejsy API sieci Web (konfigurację kodu aplikacji)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0926e6800dbcd81d2e542e27afe3afb1240cff22
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268411"
---
# <a name="desktop-app-that-calls-web-apis---code-configuration"></a>Aplikacja klasyczna, która wywołuje interfejsy API sieci Web — konfiguracja kodu

Teraz, po utworzeniu aplikacji, dowiesz się, jak skonfigurować kod przy użyciu współrzędnych aplikacji.

## <a name="msal-libraries"></a>Biblioteki MSAL

Jedyną biblioteką MSAL obsługującą aplikacje klasyczne na wielu platformach jest MSAL.NET.

MSAL dla systemów iOS i macOS obsługuje aplikacje klasyczne działające tylko na macOS. 

## <a name="public-client-application-with-msalnet"></a>Publiczna aplikacja kliencka z MSAL.NET

Z punktu widzenia kodu aplikacje klasyczne są publicznymi aplikacjami klienckimi i dlatego Kompilowanie i manipulowanie MSAL.NET `IPublicClientApplication`. W przypadku korzystania z uwierzytelniania interaktywnego nie będzie to możliwe.

![IPublicClientApplication](media/scenarios/public-client-application.png)

### <a name="exclusively-by-code"></a>Wyłącznie przez kod

Poniższy kod tworzy wystąpienie publicznej aplikacji klienckiej, logowania użytkowników w Microsoft Azure chmurze publicznej przy użyciu konta służbowego lub osobistego konto Microsoft.

```CSharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

Jeśli zamierzasz używać uwierzytelniania interakcyjnego lub przepływu kodu urządzenia, jak pokazano powyżej, chcesz użyć `.WithRedirectUri` modyfikatora:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="using-configuration-files"></a>Korzystanie z plików konfiguracji

Poniższy kod tworzy wystąpienie publicznej aplikacji klienckiej z obiektu konfiguracji, który może być wypełniany programowo lub odczytywany z pliku konfiguracji

```CSharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="more-elaborated-configuration"></a>Bardziej rozbudowana konfiguracja

Kompilowanie aplikacji można rozbudować przez dodanie szeregu modyfikatorów. Na przykład jeśli chcesz, aby aplikacja była aplikacją wielodostępną w chmurze krajowej (w tym przypadku dla instytucji rządowych USA), możesz napisać:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment,
                         AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

MSAL.NET zawiera również modyfikator dla usług AD FS 2019:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Na koniec Jeśli chcesz uzyskać tokeny dla dzierżawy Azure AD B2C, możesz określić dzierżawcę, jak pokazano w poniższym fragmencie kodu:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

### <a name="learn-more"></a>Dowiedz się więcej

Aby dowiedzieć się więcej na temat konfigurowania aplikacji klasycznej MSAL.NET:

- Aby uzyskać listę wszystkich modyfikatorów dostępnych na stronie `PublicClientApplicationBuilder`, zobacz dokumentację referencyjną [PublicClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods)
- Opis wszystkich opcji dostępnych w `PublicClientApplicationOptions` temacie zobacz [PublicClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions)w dokumentacji referencyjnej

## <a name="complete-example-with-configuration-options"></a>Ukończ przykład z opcjami konfiguracji

Załóżmy, że Aplikacja konsolowa platformy .NET Core ma `appsettings.json` następujący plik konfiguracyjny:

```JSon
{
  "Authentication": {
    "AzureCloudInstance": "AzurePublic",
    "AadAuthorityAudience": "AzureAdMultipleOrgs",
    "ClientId": "ebe2ab4d-12b3-4446-8480-5c3828d04c50"
  },

  "WebAPI": {
    "MicrosoftGraphBaseEndpoint": "https://graph.microsoft.com"
  }
}
```

Masz niewielki kod, aby odczytać ten plik za pomocą platformy .NET dostarczonej przez program.

```CSharp
public class SampleConfiguration
{
 /// <summary>
 /// Authentication options
 /// </summary>
 public PublicClientApplicationOptions PublicClientApplicationOptions { get; set; }

 /// <summary>
 /// Base URL for Microsoft Graph (it varies depending on whether the application is ran
 /// in Microsoft Azure public clouds or national / sovereign clouds
 /// </summary>
 public string MicrosoftGraphBaseEndpoint { get; set; }

 /// <summary>
 /// Reads the configuration from a json file
 /// </summary>
 /// <param name="path">Path to the configuration json file</param>
 /// <returns>SampleConfiguration as read from the json file</returns>
 public static SampleConfiguration ReadFromJsonFile(string path)
 {
  // .NET configuration
  IConfigurationRoot Configuration;
  var builder = new ConfigurationBuilder()
                    .SetBasePath(Directory.GetCurrentDirectory())
                    .AddJsonFile(path);
  Configuration = builder.Build();

  // Read the auth and graph endpoint config
  SampleConfiguration config = new SampleConfiguration()
  {
   PublicClientApplicationOptions = new PublicClientApplicationOptions()
  };
  Configuration.Bind("Authentication", config.PublicClientApplicationOptions);
  config.MicrosoftGraphBaseEndpoint =
  Configuration.GetValue<string>("WebAPI:MicrosoftGraphBaseEndpoint");
  return config;
 }
}
```

Teraz, aby utworzyć aplikację, wystarczy napisać następujący kod:

```CSharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .WithDefaultRedirectUri()
           .Build();
```

i przed wywołaniem `.Build()` metody można zastąpić konfigurację z `.WithXXX` wywołaniami metod tak jak wcześniej.

## <a name="public-client-application-with-msal-for-ios-and-macos"></a>Publiczna aplikacja kliencka z MSAL dla systemów iOS i macOS

Poniższy kod tworzy wystąpienie publicznej aplikacji klienckiej, logowania użytkowników w Microsoft Azure chmurze publicznej przy użyciu konta służbowego lub osobistego konto Microsoft.

### <a name="quick-configuration"></a>Szybka konfiguracja

Cel-C:

```objc
NSError *msalError = nil;
    
MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

Adres
```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

### <a name="more-elaborated-configuration"></a>Bardziej rozbudowana konfiguracja

Kompilowanie aplikacji można rozbudować przez dodanie szeregu modyfikatorów. Na przykład jeśli chcesz, aby aplikacja była aplikacją wielodostępną w chmurze krajowej (w tym przypadku dla instytucji rządowych USA), możesz napisać:

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

> [!div class="nextstepaction"]
> [Uzyskiwanie tokenu dla aplikacji klasycznej](scenario-desktop-acquire-token.md)
