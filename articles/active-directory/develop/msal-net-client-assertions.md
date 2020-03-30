---
title: Potwierdzenia klientów (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się więcej o obsłudze potwierdzeń podpisanych klientów dla poufnych aplikacji klienckich w Bibliotece uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/18/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 8c97387bfd2a362d3bf5a6b8a3252242f061da31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050290"
---
# <a name="confidential-client-assertions"></a>Poufne twierdzenia klientów

Aby udowodnić swoją tożsamość, poufne aplikacje klienckie wymieniają klucz tajny z usługą Azure AD. Tajemnicą może być:
- Klucz tajny klienta (hasło aplikacji).
- Certyfikat, który jest używany do tworzenia podpisanego potwierdzenia zawierającego standardowe oświadczenia.

Ten klucz tajny może być również podpisany potwierdzenia bezpośrednio.

MSAL.NET ma cztery metody dostarczania poświadczeń lub potwierdzeń do poufnej aplikacji klienckiej:
- `.WithClientSecret()`
- `.WithCertificate()`
- `.WithClientAssertion()`
- `.WithClientClaims()`

> [!NOTE]
> Chociaż jest możliwe użycie `WithClientAssertion()` interfejsu API do uzyskiwania tokenów dla poufnego klienta, nie zaleca się używania go domyślnie, ponieważ jest bardziej zaawansowany i jest przeznaczony do obsługi bardzo konkretnych scenariuszy, które nie są powszechne. Za `.WithCertificate()` pomocą interfejsu API pozwoli MSAL.NET do obsługi tego dla Ciebie. Ten interfejs API oferuje możliwość dostosowania żądania uwierzytelniania w razie `.WithCertificate()` potrzeby, ale domyślne potwierdzenie utworzone przez wystarczy dla większości scenariuszy uwierzytelniania. Ten interfejs API może być również używany jako obejście w niektórych scenariuszach, w których MSAL.NET nie może wykonać operacji podpisywania wewnętrznie.

### <a name="signed-assertions"></a>Podpisane potwierdzenia

Potwierdzenie podpisanego klienta ma postać podpisanego narzędzia JWT z ładunkiem zawierającym wymagane oświadczenia o uwierzytelnianiu wymagane przez usługę Azure AD, zakodowaną w bazie Base64. Aby go użyć:

```csharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

Oświadczenia oczekiwane przez usługę Azure AD to:

Typ oświadczenia | Wartość | Opis
---------- | ---------- | ----------
AUD | `https://login.microsoftonline.com/{tenantId}/v2.0` | Oświadczenie "aud" (odbiorców) identyfikuje odbiorców, dla których jest przeznaczony narzędzie JWT (tutaj usługa Azure AD) Zobacz [RFC 7519, Sekcja 4.1.3]
exp | Czw 27 2019 15:04:17 GMT+0200 (Romance Daylight Time) | Oświadczenie "exp" (czas wygaśnięcia) określa czas wygaśnięcia lub po którym JWT NIE MOŻE być akceptowany do przetworzenia. Patrz [RFC 7519, sekcja 4.1.4]
Iss | {Identyfikator klienta} | Oświadczenie "iss" (emitent) identyfikuje zleceniodawcę, który wydał JWT. Przetwarzanie tego oświadczenia jest specyficzne dla aplikacji. Wartość "iss" jest ciągiem z uwzględnieniem wielkości liter zawierającego wartość StringOrURI. [RFC 7519, sekcja 4.1.1]
jti | (a Guid) | Oświadczenie "jti" (JWT ID) zawiera unikatowy identyfikator JWT. Wartość identyfikatora MUSI być przypisana w sposób, który zapewnia, że istnieje znikome prawdopodobieństwo, że ta sama wartość zostanie przypadkowo przypisana do innego obiektu danych; jeśli aplikacja używa wielu wystawców, kolizje muszą być zapobiegane między wartościami produkowanymi przez różnych emitentów, jak również. Twierdzenie "jti" może być użyte, aby zapobiec odtwarzaniu JWT. Wartość "jti" jest ciągiem z uwzględnieniem wielkości liter. [RFC 7519, sekcja 4.1.7]
Nbf | Czw cze 27 2019 14:54:17 GMT+0200 (Romance Daylight Time) | Oświadczenie "nbf" (nie wcześniej) określa czas, przed którym JWT NIE MOŻE być dopuszczony do przetwarzania. [RFC 7519, sekcja 4.1.5]
Sub | {Identyfikator klienta} | Oświadczenie "sub" (podmiot) identyfikuje temat JWT. Roszczenia w JWT są zwykle oświadczenia na ten temat. Wartość tematu MUSI być albo zakres jest unikatowy lokalnie w kontekście wystawcy lub być globalnie unikatowy. Patrz [RFC 7519, sekcja 4.1.2]

Oto przykład tworzenia tych roszczeń:

```csharp
private static IDictionary<string, string> GetClaims()
{
      //aud = https://login.microsoftonline.com/ + Tenant ID + /v2.0
      string aud = $"https://login.microsoftonline.com/{tenantId}/v2.0";

      string ConfidentialClientID = "00000000-0000-0000-0000-000000000000" //client id
      const uint JwtToAadLifetimeInSeconds = 60 * 10; // Ten minutes
      DateTime validFrom = DateTime.UtcNow;
      var nbf = ConvertToTimeT(validFrom);
      var exp = ConvertToTimeT(validFrom + TimeSpan.FromSeconds(JwtToAadLifetimeInSeconds));

      return new Dictionary<string, string>()
           {
                { "aud", aud },
                { "exp", exp.ToString() },
                { "iss", ConfidentialClientID },
                { "jti", Guid.NewGuid().ToString() },
                { "nbf", nbf.ToString() },
                { "sub", ConfidentialClientID }
            };
}
```

Oto jak spreparować podpisane potwierdzenie klienta:

```csharp
string Encode(byte[] arg)
{
    char Base64PadCharacter = '=';
    char Base64Character62 = '+';
    char Base64Character63 = '/';
    char Base64UrlCharacter62 = '-';
    char Base64UrlCharacter63 = '_';

    string s = Convert.ToBase64String(arg);
    s = s.Split(Base64PadCharacter)[0]; // RemoveAccount any trailing padding
    s = s.Replace(Base64Character62, Base64UrlCharacter62); // 62nd char of encoding
    s = s.Replace(Base64Character63, Base64UrlCharacter63); // 63rd char of encoding

    return s;
}

string GetSignedClientAssertion()
{
    //Signing with SHA-256
    string rsaSha256Signature = "http://www.w3.org/2001/04/xmldsig-more#rsa-sha256";
     X509Certificate2 certificate = new X509Certificate2("Certificate.pfx", "Password", X509KeyStorageFlags.EphemeralKeySet);

    //Create RSACryptoServiceProvider
    var x509Key = new X509AsymmetricSecurityKey(certificate);
    var privateKeyXmlParams = certificate.PrivateKey.ToXmlString(true);
    var rsa = new RSACryptoServiceProvider();
    rsa.FromXmlString(privateKeyXmlParams);

    //alg represents the desired signing algorithm, which is SHA-256 in this case
    //kid represents the certificate thumbprint
    var header = new Dictionary<string, string>()
         {
              { "alg", "RS256"},
              { "kid", Encode(Certificate.GetCertHash()) }
         };

    //Please see the previous code snippet on how to craft claims for the GetClaims() method
    string token = Encode(Encoding.UTF8.GetBytes(JObject.FromObject(header).ToString())) + "." + Encode(Encoding.UTF8.GetBytes(JObject.FromObject(GetClaims())));

    string signature = Encode(rsa.SignData(Encoding.UTF8.GetBytes(token), new SHA256Cng()));
    string signedClientAssertion = string.Concat(token, ".", signature);
    return signedClientAssertion;
}
```

### <a name="alternative-method"></a>Metoda alternatywna

Istnieje również możliwość korzystania z [microsoft.IdentityModel.JsonWebTokens](https://www.nuget.org/packages/Microsoft.IdentityModel.JsonWebTokens/) do tworzenia potwierdzenia dla Ciebie. Kod będzie bardziej elegancki, jak pokazano w poniższym przykładzie:

```csharp
        string GetSignedClientAssertion()
        {
            var cert = new X509Certificate2("Certificate.pfx", "Password", X509KeyStorageFlags.EphemeralKeySet);

            //aud = https://login.microsoftonline.com/ + Tenant ID + /v2.0
            string aud = $"https://login.microsoftonline.com/{tenantID}/v2.0";

            // client_id
            string confidentialClientID = "00000000-0000-0000-0000-000000000000";

            // no need to add exp, nbf as JsonWebTokenHandler will add them by default.
            var claims = new Dictionary<string, object>()
            {
                { "aud", aud },
                { "iss", confidentialClientID },
                { "jti", Guid.NewGuid().ToString() },
                { "sub", confidentialClientID }
            };

            var securityTokenDescriptor = new SecurityTokenDescriptor
            {
                Claims = claims,
                SigningCredentials = new X509SigningCredentials(cert)
            };

            var handler = new JsonWebTokenHandler();
            var signedClientAssertion = handler.CreateToken(securityTokenDescriptor);
        }
```

Po podpisaniu potwierdzenia klienta, można go używać z MSAL apis, jak pokazano poniżej.

```csharp
            string signedClientAssertion = GetSignedClientAssertion();

            var confidentialApp = ConfidentialClientApplicationBuilder
                .Create(ConfidentialClientID)
                .WithClientAssertion(signedClientAssertion)
                .Build();
```

### <a name="withclientclaims"></a>ZClientClaims

`WithClientClaims(X509Certificate2 certificate, IDictionary<string, string> claimsToSign, bool mergeWithDefaultClaims = true)`domyślnie spowoduje powstanie podpisane twierdzenie zawierające oświadczenia oczekiwane przez usługę Azure AD oraz dodatkowe oświadczenia klientów, które chcesz wysłać. Oto fragment kodu, jak to zrobić.

```csharp
string ipAddress = "192.168.1.2";
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, 
                                                                      new Dictionary<string, string> { { "client_ip", ipAddress } })
                                          .Build();

```

Jeśli jedno z oświadczeń w słowniku, który przekazujesz, jest takie samo jak jedno z obowiązkowych oświadczeń, zostanie uwzględnieni wartość dodatkowego oświadczenia. Zastąpi oświadczenia obliczone przez MSAL.NET.

Jeśli chcesz podać własne oświadczenia, w tym obowiązkowe oświadczenia oczekiwane `false` przez `mergeWithDefaultClaims` usługę Azure AD, przekaż dla parametru.
