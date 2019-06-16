---
title: Demon aplikacji wywoływania interfejsów API sieci web (Konfiguracja aplikacji) — Platforma tożsamości firmy Microsoft
description: Dowiedz się, jak utworzyć aplikację demona wywołuje interfejsy API (Konfiguracja aplikacji) sieci web
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
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
ms.openlocfilehash: fd2da6baecdce3ab85a45347f27f573bf814445d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67055754"
---
# <a name="daemon-app-that-calls-web-apis---code-configuration"></a>Demon aplikacji, że wywołania sieci web interfejsy API — Konfiguracja kodu

Dowiedz się, jak skonfigurować kod aplikacji demona wywołuje interfejsy API sieci web.

## <a name="msal-libraries-supporting-daemon-apps"></a>Aplikacje demona z pomocniczych biblioteki MSAL

Biblioteki Microsoft Obsługa demonem aplikacje są:

  Biblioteka MSAL | Opis
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Obsługiwane platformy do tworzenia aplikacji demona to platformy .NET Framework i .NET Core (nie platformy uniwersalnej systemu Windows, platformy Xamarin.iOS i Xamarin.Android jako tych platform są używane do tworzenia aplikacji klienckich publiczny)
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL.Python | Programowanie w toku — w publicznej wersji zapoznawczej
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL.Java | Programowanie w toku — w publicznej wersji zapoznawczej

## <a name="configuration-of-the-authority"></a>Konfiguracja urzędu

Biorąc pod uwagę, że aplikacje demona nie należy używać uprawnień delegowanych, ale uprawnienia aplikacji ich *obsługiwany typ konta* nie może być *kont w dowolnym katalogu organizacji i Microsoft osobistych kont () na przykład, Skype, Xbox, Outlook.com)* . W rzeczywistości jest Brak administratora dzierżawy do udzielania zgody aplikacji demona dla osobistych kont Microsoft. Musisz wybrać *kont w mojej organizacji* lub *kont w dowolnej organizacji*.

W związku z tym urząd określony w konfiguracji aplikacji powinna być dzierżawy ed (określenia Identyfikatora dzierżawy lub nazwy domeny skojarzonego z Twoją organizacją).

Jeśli jesteś niezależnym dostawcą oprogramowania i chcesz zapewniają narzędzie wielodostępnych, możesz użyć `organizations`. Jednak należy pamiętać o tym, należy również do wyjaśnienia dla klientów, jak udzielić zgody administratora. Zobacz [żądanie zgody na całej dzierżawie](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant) Aby uzyskać szczegółowe informacje. Również ma ograniczenia w MSAL, `organizations` jest dozwolona tylko w przypadku poświadczeń klienta klucz tajny aplikacji (nie certyfikat). Zobacz [usterkę platformy MSAL.NET #891](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/891)

## <a name="application-configuration-and-instantiation"></a>Konfiguracja aplikacji i wystąpienia

W bibliotekach MSAL poświadczeń klienta (hasło lub certyfikat) są przekazywane jako parametr do budowy aplikacji poufne klienta.

> [!IMPORTANT]
> Nawet jeśli Twoja aplikacja jest aplikacją konsoli uruchamianie jako usługi, jeśli jest to aplikacja demona musi to być aplikacja poufne klienta.

### <a name="msalnet"></a>MSAL.NET

Dodaj [Microsoft.IdentityClient](https://www.nuget.org/packages/Microsoft.Identity.Client) pakiet NuGet do aplikacji.

Użyj platformy MSAL.NET przestrzeni nazw

```CSharp
using Microsoft.Identity.Client;
```

Demon aplikacji zostanie wyświetlony przez `IConfidentialClientApplication`

```CSharp
IConfidentialClientApplication app;
```

Poniżej przedstawiono kod, aby skompilować aplikację przy użyciu klucza tajnego aplikacji:

```CSharp
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .WithAuthority(new Uri(config.Authority))
           .Build();
```

Poniżej przedstawiono kod, aby skompilować aplikację przy użyciu certyfikatu:

```CSharp
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .WithAuthority(new Uri(config.Authority))
    .Build();
```

### <a name="msalpython"></a>MSAL.Python

```Python
# Create a preferably long-lived app instance which maintains a token cache.

app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential=config["secret"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache

    )
```

### <a name="msaljava"></a>MSAL.Java

```Java
PrivateKey key = getPrivateKey();
X509Certificate publicCertificate = getPublicCertificate();

// create clientCredential with public and private key
IClientCredential credential = ClientCredentialFactory.create(key, publicCertificate);

ConfidentialClientApplication cca = ConfidentialClientApplication
  .builder(CLIENT_ID, credential)
  .authority(AUTHORITY_MICROSOFT)
  .build();
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Demon aplikacji — uzyskiwania tokenów dla aplikacji](./scenario-daemon-acquire-token.md)