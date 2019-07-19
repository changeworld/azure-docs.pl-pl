---
title: Aplikacja demona wywołująca interfejsy API sieci Web (Konfiguracja aplikacji) — platforma tożsamości firmy Microsoft
description: Dowiedz się, jak utworzyć aplikację demona, która wywołuje interfejsy API sieci Web (Konfiguracja aplikacji)
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
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 705545fd5167087be1a001c45f58907d6ff225e8
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68277822"
---
# <a name="daemon-app-that-calls-web-apis---code-configuration"></a>Aplikacja demona, która wywołuje interfejsy API sieci Web — konfiguracja kodu

Dowiedz się, jak skonfigurować kod dla aplikacji demona, która wywołuje interfejsy API sieci Web.

## <a name="msal-libraries-supporting-daemon-apps"></a>Biblioteki MSAL obsługujące aplikacje demona

Biblioteki firmy Microsoft obsługujące aplikacje demona są następujące:

  Biblioteka MSAL | Opis
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Obsługiwane platformy do kompilowania aplikacji demona są .NET Framework i platformy .NET Core (nie platformy UWP, Xamarin. iOS i Xamarin. Android, ponieważ te platformy są używane do tworzenia publicznych aplikacji klienckich).
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL. Python | Opracowywanie w toku — w publicznej wersji zapoznawczej
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL.Java | Opracowywanie w toku — w publicznej wersji zapoznawczej

## <a name="configuration-of-the-authority"></a>Konfiguracja urzędu

Mając na uwadze, że aplikacje demona nie korzystają z uprawnień delegowanych, ale uprawnienia aplikacji, ich *obsługiwany typ konta* nie może być *kontami w żadnym katalogu organizacyjnym i osobistymi kontami Microsoft (na przykład Skype, Xbox, Outlook.com)* . W rzeczywistości nie istnieje Administrator dzierżawy, który może udzielić zgody na aplikację demona dla kont osobistych firmy Microsoft. Musisz wybrać *konta w mojej organizacji* lub *kontach w dowolnej organizacji*.

W związku z tym Urząd określony w konfiguracji aplikacji powinien mieć wartość dzierżawca (określając identyfikator dzierżawy lub nazwę domeny skojarzoną z Twoją organizacją).

Jeśli jesteś niezależnym dostawcą oprogramowania i chcesz udostępnić narzędzie z wieloma dzierżawcami, możesz użyć `organizations`programu. Należy jednak pamiętać, że należy również wyjaśnić swoim klientom sposób udzielania zgody administratora. Aby uzyskać szczegółowe informacje, zobacz prośba o [zgodę na całą dzierżawę](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant) . Ponadto jest obecnie ograniczenie w MSAL, które `organizations` jest dozwolone tylko wtedy, gdy poświadczenia klienta są wpisem tajnym aplikacji (nie certyfikatem). Zobacz [MSAL.NET usterek #891](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/891)

## <a name="application-configuration-and-instantiation"></a>Konfiguracja i tworzenie aplikacji

W bibliotekach MSAL poświadczenia klienta (klucz tajny lub certyfikat) są przesyłane jako parametr konstrukcji poufnej aplikacji klienckiej.

> [!IMPORTANT]
> Nawet jeśli aplikacja jest aplikacją konsolową działającą jako usługa, to aplikacja demona musi być poufną aplikacją kliencką.

### <a name="msalnet"></a>MSAL.NET

Dodaj pakiet NuGet [Microsoft. IdentityClient](https://www.nuget.org/packages/Microsoft.Identity.Client) do swojej aplikacji.

Użyj przestrzeni nazw MSAL.NET

```CSharp
using Microsoft.Identity.Client;
```

Aplikacja demona będzie wyświetlana przez`IConfidentialClientApplication`

```CSharp
IConfidentialClientApplication app;
```

Oto kod do kompilowania aplikacji przy użyciu klucza tajnego aplikacji:

```CSharp
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .WithAuthority(new Uri(config.Authority))
           .Build();
```

Oto kod służący do kompilowania aplikacji z certyfikatem:

```CSharp
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .WithAuthority(new Uri(config.Authority))
    .Build();
```

Na koniec zamiast klucza tajnego klienta lub certyfikatu poufna aplikacja kliencka może również udowodnić swoją tożsamość za pomocą potwierdzeń klientów. Ten zaawansowany scenariusz jest szczegółowo opisany w potwierdzeniach [klienta](msal-net-client-assertions.md)


### <a name="msalpython"></a>MSAL. Python

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

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Aplikacja demona — uzyskiwanie tokenów dla aplikacji](./scenario-daemon-acquire-token.md)
