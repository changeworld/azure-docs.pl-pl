---
title: Utwórz wystąpienie aplikacji publicznych klienta, za pomocą opcji (Biblioteka Microsoft Authentication Library dla platformy .NET) | Azure
description: Dowiedz się, jak utworzyć aplikację kliencką publicznej za pomocą opcji konfiguracji przy użyciu Biblioteka Microsoft Authentication Library for .NET (platformy MSAL.NET).
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
ms.openlocfilehash: 125bbf9aed54fb00f039aeffddd5cc1aad3360a6
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544398"
---
# <a name="instantiate-a-public-client-application-with-configuration-options-using-msalnet"></a>Utwórz wystąpienie aplikacji publicznych klienta za pomocą opcji konfiguracji przy użyciu platformy MSAL.NET

W tym artykule opisano sposób tworzenia wystąpienia [aplikacja kliencka publicznych](msal-client-applications.md) przy użyciu Biblioteka Microsoft Authentication Library for .NET (platformy MSAL.NET).  Aplikacja jest utworzone za pomocą opcji konfiguracji zdefiniowanej w pliku ustawień.

Przed inicjowania aplikacji, musisz najpierw [zarejestrować](quickstart-register-app.md) go tak, aby aplikację można zintegrować z platformą Microsoft identity. Po zarejestrowaniu może być konieczne następujące informacje (które można znaleźć w witrynie Azure portal):

- Identyfikator klienta (ciąg reprezentujący identyfikator GUID)
- Adres URL dostawcy tożsamości (nazwanego wystąpienia) i grupy odbiorców logowania dla swojej aplikacji. Te dwa parametry są nazywane zbiorczo urzędu.
- Identyfikator dzierżawy, jeśli piszesz linię aplikacji biznesowej wyłącznie na potrzeby Twojej organizacji (również o nazwie aplikacja jednej dzierżawy).
- Dla aplikacji sieci web, a czasami dla aplikacji publicznych klienta (w szczególności, gdy Twoja aplikacja wymaga pod kątem wykorzystania brokera) będzie również ustawiono element redirectUri gdzie dostawcy tożsamości skontaktuje się z powrotem aplikacji przy użyciu tokenów zabezpieczających.


Aplikację konsolową .NET Core może mieć następujące *appsettings.json* pliku konfiguracji:

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

Poniższy kod odczytuje ten plik za pomocą programu .NET framework configuration:

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

Poniższy kod tworzy aplikację za pomocą konfiguracji z pliku ustawień:

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .Build();
```

