---
title: Aplikacja klasyczna wywołuje interfejsy API (kod Konfiguracja) - sieci web platforma tożsamości firmy Microsoft
description: Naucz się tworzyć aplikację klasyczną wywołuje interfejsy API (konfiguracji kodu aplikacji) sieci web
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
ms.openlocfilehash: 600b6db1eb3d3b422d62e49c5bc816a1a56370f9
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798505"
---
# <a name="desktop-app-that-calls-web-apis---code-configuration"></a>Aplikacja klasyczna, że wywołania sieci web interfejsy API — Konfiguracja kodu

Teraz, po utworzeniu aplikacji, dowiesz się, jak skonfigurować kod z aplikacji współrzędne.

## <a name="msal-libraries"></a>Biblioteki MSAL

Tylko Biblioteka MSAL obsługi aplikacji klasycznych już dziś jest platformy MSAL.NET

## <a name="public-client-application"></a>Aplikacja kliencka publiczne

Z punktu widzenia kodu, aplikacje klasyczne są aplikacje klienckie publicznych i dlatego można tworzyć i manipulowania platformy MSAL.NET `IPublicClientApplication`. Ponownie czynności będzie nieco inna, czy używać przeprowadzić uwierzytelnianie interakcyjne czy nie.

![IPublicClientApplication](media/scenarios/public-client-application.png)

### <a name="exclusively-by-code"></a>Wyłącznie przez kod

Poniższy kod tworzy wystąpienie aplikacji publicznych klienta, logowanie użytkowników w chmurze publicznej Microsoft Azure przy użyciu służbowego i konta służbowego lub osobistego konta Microsoft.

```CSharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

Jeśli zamierzasz używać przeprowadzić uwierzytelnianie interakcyjne lub urządzenie przepływu kodu, jak pokazano powyżej, chcesz użyć `.WithRedirectUri` modyfikator:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="using-configuration-files"></a>Za pomocą plików konfiguracji

Poniższy kod tworzy wystąpienie aplikacji klienckiej publicznych z obiektu konfiguracji, które mogą być wypełniane programowo lub odczytu z pliku konfiguracji

```CSharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="more-elaborated-configuration"></a>Konfiguracja bardziej uszczegółowionych

Można opracować aplikację tworzenia przez dodanie liczby modyfikatory. Na przykład jeśli chcesz, aby aplikacja ma być aplikacji z wieloma dzierżawami, w chmurze krajowej (tutaj instytucji rządowych USA), można napisać:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment,
                         AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

Platformy MSAL.NET zawiera również modyfikator 2019 usług AD FS:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Ponadto jeśli chcesz uzyskać tokeny dla dzierżawy usługi Azure AD B2C, można określić swojej dzierżawy, jak pokazano w poniższym fragmencie kodu:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

### <a name="learn-more"></a>Dowiedz się więcej

Aby dowiedzieć się więcej na temat konfigurowania aplikacji klasycznej platformy MSAL.NET:

- Aby uzyskać listę wszystkich modyfikatorów dostępne na `PublicClientApplicationBuilder`, zobacz dokumentację referencyjną [PublicClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods)
- Aby uzyskać opis wszystkich opcji, które są widoczne w `PublicClientApplicationOptions` zobacz [PublicClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions), w dokumentacji

## <a name="complete-example-with-configuration-options"></a>Pełny przykład za pomocą opcji konfiguracji

Wyobraź sobie aplikacji konsolowej .NET Core, która ma następujące `appsettings.json` pliku konfiguracji:

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

Masz niewielkiej ilości kodu do odczytu tego pliku przy użyciu platformy .NET, udostępniane w ramach konfiguracji;

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

Teraz Aby utworzyć aplikację, po prostu należy napisać następujący kod:

```CSharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .WithDefaultRedirectUri()
           .Build();
```

i przed wywołaniem do `.Build()` metody, można zastąpić konfigurację przy użyciu wywołań `.WithXXX` metody, jak pokazano wcześniej.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Uzyskiwanie tokenu dla aplikacji klasycznej](scenario-desktop-acquire-token.md)
