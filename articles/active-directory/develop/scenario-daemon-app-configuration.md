---
title: Skonfiguruj aplikacje demona, które wywołują interfejsy API sieci Web — Microsoft Identity platform | Azure
description: Dowiedz się, jak skonfigurować kod dla aplikacji demona, która wywołuje interfejsy API sieci Web (Konfiguracja aplikacji)
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
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7afad7bdc0cd0fb957104e4963eaade96fa2d840
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423931"
---
# <a name="daemon-app-that-calls-web-apis---code-configuration"></a>Aplikacja demona, która wywołuje interfejsy API sieci Web — konfiguracja kodu

Dowiedz się, jak skonfigurować kod dla aplikacji demona, która wywołuje interfejsy API sieci Web.

## <a name="msal-libraries-supporting-daemon-apps"></a>Biblioteki MSAL obsługujące aplikacje demona

Biblioteki firmy Microsoft obsługujące aplikacje demona są następujące:

  Biblioteka MSAL | Opis
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Obsługiwane platformy do kompilowania aplikacji demona są .NET Framework i platformy .NET Core (nie platformy UWP, Xamarin. iOS i Xamarin. Android, ponieważ te platformy są używane do tworzenia publicznych aplikacji klienckich).
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL Python | Obsługa aplikacji demona w języku Python
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL Java | Obsługa aplikacji demona w języku Java

## <a name="configuration-of-the-authority"></a>Konfiguracja urzędu

Ponieważ aplikacje demona nie korzystają z uprawnień delegowanych, ale uprawnienia aplikacji, ich *obsługiwany typ konta* nie mogą być *kontami w żadnym katalogu organizacyjnym i osobistymi kontami Microsoft (na przykład Skype, Xbox, Outlook.com)* . W rzeczywistości nie istnieje Administrator dzierżawy, który może udzielić zgody na aplikację demona dla kont osobistych firmy Microsoft. Musisz wybrać *konta w mojej organizacji* lub *kontach w dowolnej organizacji*.

W związku z tym Urząd określony w konfiguracji aplikacji powinien mieć wartość dzierżawca (określając identyfikator dzierżawy lub nazwę domeny skojarzoną z Twoją organizacją).

Jeśli jesteś niezależnym dostawcą oprogramowania i chcesz udostępnić narzędzie z wieloma dzierżawcami, możesz użyć `organizations`. Należy jednak pamiętać, że należy również wyjaśnić swoim klientom sposób udzielania zgody administratora. Aby uzyskać szczegółowe informacje [, zobacz prośba o zgodę na całą dzierżawę](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant) . Istnieje również ograniczenie MSAL: wartość `organizations` jest dozwolona tylko wtedy, gdy poświadczenia klienta to klucz tajny aplikacji (a nie certyfikat).

## <a name="application-configuration-and-instantiation"></a>Konfiguracja i tworzenie aplikacji

W bibliotekach MSAL poświadczenia klienta (klucz tajny lub certyfikat) są przesyłane jako parametr konstrukcji poufnej aplikacji klienckiej.

> [!IMPORTANT]
> Nawet jeśli aplikacja jest aplikacją konsolową działającą jako usługa, to aplikacja demona musi być poufną aplikacją kliencką.

### <a name="configuration-file"></a>Plik konfiguracji

Plik konfiguracji definiuje:

- Urząd lub wystąpienie w chmurze i tenantId
- ClientID, który pochodzi z rejestracji aplikacji
- wpis tajny klienta lub certyfikat

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

plik [appSettings. JSON](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/daemon-console/appsettings.json) z przykładu [demona konsoli .NET Core](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) .

```JSon
{
  "Instance": "https://login.microsoftonline.com/{0}",
  "Tenant": "[Enter here the tenantID or domain name for your Azure AD tenant]",
  "ClientId": "[Enter here the ClientId for your application]",
  "ClientSecret": "[Enter here a client secret for your application]",
  "CertificateName": "[Or instead of client secret: Enter here the name of a certificate (from the user cert store) as registered with your application]"
}
```

Podaj wartość clientSecret lub certificateName. Oba ustawienia są na wyłączność.

# <a name="pythontabpython"></a>[Python](#tab/python)

Podczas budowania klienta poufnego z kluczami tajnymi klienta plik config [. JSON](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/1-Call-MsGraph-WithSecret/parameters.json) w przykładzie [demona języka Python](https://github.com/Azure-Samples/ms-identity-python-daemon) jest następujący.

```Json
{
  "authority": "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here",
  "client_id": "your_client_id",
  "scope": [ "https://graph.microsoft.com/.default" ],
  "secret": "The secret generated by AAD during your confidential app registration",
  "endpoint": "https://graph.microsoft.com/v1.0/users"
}
```

Podczas budowania klienta poufnego z certyfikatami plik konfiguracyjny [Parameters. JSON](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/2-Call-MsGraph-WithCertificate/parameters.json) w przykładzie [demona języka Python](https://github.com/Azure-Samples/ms-identity-python-daemon) jest następujący.

```Json
{
  "authority": "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here",
  "client_id": "your_client_id",
  "scope": [ "https://graph.microsoft.com/.default" ],
  "thumbprint": "790E... The thumbprint generated by AAD when you upload your public cert",
  "private_key_file": "server.pem",
  "endpoint": "https://graph.microsoft.com/v1.0/users"
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

Oto Klasa używana w przykładach deweloperskich MSAL Java do konfigurowania przykładów: [TestData](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/TestData.java).

```Java
public class TestData {

    final static String TENANT_SPECIFIC_AUTHORITY = "https://login.microsoftonline.com/<TenantId>/";
    final static String GRAPH_DEFAULT_SCOPE = "https://graph.microsoft.com/.default";
    final static String CONFIDENTIAL_CLIENT_ID = "";
    final static String CONFIDENTIAL_CLIENT_SECRET = "";
}
```

---

### <a name="instantiation-of-the-msal-application"></a>Tworzenie wystąpienia aplikacji MSAL

Aby utworzyć wystąpienie aplikacji MSAL, należy wykonać następujące:

- Dodawanie, odwoływanie lub Importowanie pakietu MSAL (w zależności od języka)
- Następnie konstrukcja jest różna w zależności od tego, czy używane są klucze tajne lub certyfikaty klienta (lub, jako zaawansowany scenariusz, podpisane potwierdzenia)

#### <a name="reference-the-package"></a>Odwoływanie się do pakietu

Odwołuje się do pakietu MSAL w kodzie aplikacji.

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

Dodaj pakiet NuGet [Microsoft. IdentityClient](https://www.nuget.org/packages/Microsoft.Identity.Client) do swojej aplikacji.
W MSAL.NET, poufna aplikacja kliencka jest reprezentowana przez interfejs `IConfidentialClientApplication`.
Użyj przestrzeni nazw MSAL.NET w kodzie źródłowym

```csharp
using Microsoft.Identity.Client;
IConfidentialClientApplication app;
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```python
import msal
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
import com.microsoft.aad.msal4j.ClientCredentialFactory;
import com.microsoft.aad.msal4j.ClientCredentialParameters;
import com.microsoft.aad.msal4j.ConfidentialClientApplication;
import com.microsoft.aad.msal4j.IAuthenticationResult;
```

---

#### <a name="instantiate-the-confidential-client-application-with-client-secrets"></a>Tworzenie wystąpienia poufnej aplikacji klienckiej przy użyciu kluczy tajnych klienta

Oto kod służący do tworzenia wystąpienia poufnej aplikacji klienckiej przy użyciu klucza tajnego klienta:

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

```csharp
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .WithAuthority(new Uri(config.Authority))
           .Build();
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance which maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential=config["secret"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="javatabjava"></a>[Java](#tab/java)

```Java
ConfidentialClientApplication app = ConfidentialClientApplication.builder(
        TestData.CONFIDENTIAL_CLIENT_ID,
        ClientCredentialFactory.create(TestData.CONFIDENTIAL_CLIENT_SECRET))
        .authority(TestData.TENANT_SPECIFIC_AUTHORITY)
        .build();
```

---

#### <a name="instantiate-the-confidential-client-application-with-client-certificate"></a>Tworzenie wystąpienia poufnej aplikacji klienckiej przy użyciu certyfikatu klienta

Oto kod służący do kompilowania aplikacji z certyfikatem:

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

```csharp
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .WithAuthority(new Uri(config.Authority))
    .Build();
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance which maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential={"thumbprint": config["thumbprint"], "private_key": open(config['private_key_file']).read()},
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="javatabjava"></a>[Java](#tab/java)

W MSAL. Java istnieją dwóch konstruktorów, które umożliwiają utworzenie wystąpienia poufnej aplikacji klienckiej przy użyciu certyfikatów:

```Java

InputStream pkcs12Certificate = ... ; /* containing PCKS12 formatted certificate*/
string certificatePassword = ... ;    /* contains the password to access the certificate */

ConfidentialClientApplication app = ConfidentialClientApplication.builder(
        TestData.CONFIDENTIAL_CLIENT_ID,
        ClientCredentialFactory.create(pkcs12Certificate, certificatePassword))
        .authority(TestData.TENANT_SPECIFIC_AUTHORITY)
        .build();
```

lub

```Java
PrivateKey key = getPrivateKey(); /* RSA private key to sign the assertion */
X509Certificate publicCertificate = getPublicCertificate(); /* x509 public certificate used as a thumbprint */

ConfidentialClientApplication app = ConfidentialClientApplication.builder(
        TestData.CONFIDENTIAL_CLIENT_ID,
        ClientCredentialFactory.create(rsaPrivateKey, publicKeyCertificate))
        .authority(TestData.TENANT_SPECIFIC_AUTHORITY)
        .build();
```

---

#### <a name="advanced-scenario---instantiate-the-confidential-client-application-with-client-assertions"></a>Scenariusz zaawansowany — Tworzenie wystąpienia poufnej aplikacji klienckiej z potwierdzeniami klientów

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

Zamiast klucza tajnego klienta lub certyfikatu poufna aplikacja kliencka może również udowodnić swoją tożsamość za pomocą potwierdzeń klientów.

MSAL.NET ma dwie metody, aby zapewnić podpisane potwierdzenia do poufnej aplikacji klienckiej:

- `.WithClientAssertion()`
- `.WithClientClaims()`

W przypadku korzystania z `WithClientAssertion`należy zapewnić podpisany token JWT. Ten zaawansowany scenariusz jest szczegółowo opisany w [potwierdzeniach klienta](msal-net-client-assertions.md)

```csharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

W przypadku korzystania z `WithClientClaims`, MSAL.NET będzie same podpisane potwierdzenie zawierające oświadczenia oczekiwane przez usługę Azure AD i dodatkowe oświadczenia klienta, które chcesz wysłać.
Oto fragment kodu dotyczący sposobu wykonywania tych czynności:

```csharp
string ipAddress = "192.168.1.2";
var claims = new Dictionary<string, string> { { "client_ip", ipAddress } };
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, claims)
                                          .Build();```
```

Aby uzyskać szczegółowe informacje, zobacz [potwierdzenia klientów](msal-net-client-assertions.md).

# <a name="pythontabpython"></a>[Python](#tab/python)

W MSAL Python można dostarczyć oświadczenia klienta przy użyciu oświadczeń, które będą podpisane przez klucz prywatny tego `ConfidentialClientApplication`.

```Python
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance which maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential={"thumbprint": config["thumbprint"], "private_key": open(config['private_key_file']).read()},
    client_claims = {"client_ip": "x.x.x.x"}
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

Aby uzyskać szczegółowe informacje, zobacz dokumentację referencyjną MSAL języka Python dla [ConfidentialClientApplication](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__).

# <a name="javatabjava"></a>[Java](#tab/java)

MSAL Java jest w publicznej wersji zapoznawczej. Podpisane potwierdzenia nie są jeszcze obsługiwane.

---

## <a name="next-steps"></a>Następne kroki

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [Aplikacja demona — uzyskiwanie tokenów dla aplikacji](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-acquire-token?tabs=dotnet)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Aplikacja demona — uzyskiwanie tokenów dla aplikacji](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-acquire-token?tabs=python)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Aplikacja demona — uzyskiwanie tokenów dla aplikacji](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-acquire-token?tabs=java)

---
