---
title: Tworzenie wystąpienia publicznej aplikacji klienckiej (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak utworzyć wystąpienia publicznej aplikacji klienckiej z opcjami konfiguracji przy użyciu biblioteki uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 1dd06e139f931bbf8554f05f05c5d9b9ccf200e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083593"
---
# <a name="instantiate-a-public-client-application-with-configuration-options-using-msalnet"></a>Tworzenie wystąpienia publicznej aplikacji klienckiej z opcjami konfiguracji przy użyciu MSAL.NET

W tym artykule opisano sposób tworzenia wystąpienia [publicznej aplikacji klienckiej](msal-client-applications.md) przy użyciu biblioteki uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET).  Tworzenie wystąpienia aplikacji z opcjami konfiguracji zdefiniowanymi w pliku ustawień.

Przed zainicjowaniem aplikacji należy najpierw [ją zarejestrować,](quickstart-register-app.md) aby aplikacja mogła być zintegrowana z platformą tożsamości firmy Microsoft. Po rejestracji mogą być potrzebne następujące informacje (które można znaleźć w witrynie Azure portal):

- Identyfikator klienta (ciąg reprezentujący identyfikator GUID)
- Adres URL dostawcy tożsamości (o nazwie wystąpienie) i grupa odbiorców logowania dla aplikacji. Te dwa parametry są łącznie znane jako organ.
- Identyfikator dzierżawy, jeśli piszesz wiersz aplikacji biznesowej wyłącznie dla twojej organizacji (również o nazwie aplikacja z jedną dzierżawą).
- W przypadku aplikacji sieci web, a czasami dla publicznych aplikacji klienckich (w szczególności gdy aplikacja musi używać brokera), należy również ustawić redirectUri, gdzie dostawca tożsamości skontaktuje się z powrotem aplikacji za pomocą tokenów zabezpieczających.


Aplikacja konsoli .NET Core może mieć następujący plik konfiguracji *appsettings.json:*

```json
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

Następujący kod odczytuje ten plik przy użyciu struktury konfiguracji platformy .NET:

```csharp
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
        config.MicrosoftGraphBaseEndpoint = Configuration.GetValue<string>("WebAPI:MicrosoftGraphBaseEndpoint");
        return config;
    }
}
```

Następujący kod tworzy aplikację przy użyciu konfiguracji z pliku ustawień:

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .Build();
```

