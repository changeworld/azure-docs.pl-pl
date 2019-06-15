---
title: Certyfikat poświadczeń w usłudze Azure AD | Dokumentacja firmy Microsoft
description: W tym artykule omówiono rejestrowanie i używanie poświadczeń certyfikatu do uwierzytelniania aplikacji
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 88f0c64a-25f7-4974-aca2-2acadc9acbd8
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2019
ms.author: ryanwi
ms.reviewer: nacanuma, jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ed4e7559ff6c3b76bbdf49b538ffebf3ad09cc58
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66001230"
---
# <a name="certificate-credentials-for-application-authentication"></a>Certyfikat poświadczeń do uwierzytelniania aplikacji

Azure Active Directory (Azure AD) umożliwia aplikacji używanie jej własnych poświadczeń do uwierzytelniania, na przykład w usłudze flow przyznanie poświadczenia klienta OAuth w wersji 2.0 ([v1.0](v1-oauth2-client-creds-grant-flow.md), [v2.0](v2-oauth2-client-creds-grant-flow.md)) i w imieniu z przepływ ([v1.0](v1-oauth2-on-behalf-of-flow.md), [v2.0](v2-oauth2-on-behalf-of-flow.md)).

Jeden formularz poświadczenie, które aplikacja może użyć do uwierzytelniania jest potwierdzenie Token(JWT) sieci Web JSON, podpisane za pomocą certyfikatu, który jest właścicielem aplikacji.

## <a name="assertion-format"></a>Format asercji
Do obliczenia potwierdzenie, można użyć jednego z wielu [JSON Web Token](https://jwt.ms/) bibliotek w wybranym języku. Informacje przekazane przez token są następujące:

### <a name="header"></a>nagłówek

| Parametr |  Uwagi |
| --- | --- |
| `alg` | Powinien być **RS256** |
| `typ` | Powinien być **JWT** |
| `x5t` | Powinien być odcisk palca certyfikatu X.509 SHA-1 |

### <a name="claims-payload"></a>Oświadczenia (ładunku)

| Parametr |  Uwagi |
| --- | --- |
| `aud` | Odbiorcy: Powinien być  **https://login.microsoftonline.com/ *tenant_Id*  /oauth2/token.** |
| `exp` | Data wygaśnięcia: Data wygaśnięcia tokenu. Czas jest reprezentowany jako sekundach od 1 stycznia 1970 r. (1970-01-01T0:0:0Z) UTC do czasu wygaśnięcia ważności tokenu.|
| `iss` | Wystawca: powinien być client_id (identyfikator aplikacji usługi klienta) |
| `jti` | Identyfikator GUID: identyfikator JWT |
| `nbf` | Nie wcześniej niż: Data przed którym nie można używać tokenu. Czas jest reprezentowany jako sekundach od 1 stycznia 1970 r. (1970-01-01T0:0:0Z) UTC do czasu jego token został wystawiony. |
| `sub` | Temat: Jak w przypadku `iss`, powinny być client_id (identyfikator aplikacji usługi klienta) |

### <a name="signature"></a>Podpis

Podpis jest kolumną obliczaną, stosując certyfikatu zgodnie z opisem w [specyfikacji RFC7519 tokenu Web JSON](https://tools.ietf.org/html/rfc7519)

## <a name="example-of-a-decoded-jwt-assertion"></a>Przykład zdekodowany potwierdzenie tokenu JWT

```
{
  "alg": "RS256",
  "typ": "JWT",
  "x5t": "gx8tGysyjcRqKjFPnd7RFwvwZI0"
}
.
{
  "aud": "https: //login.microsoftonline.com/contoso.onmicrosoft.com/oauth2/token",
  "exp": 1484593341,
  "iss": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05",
  "jti": "22b3bb26-e046-42df-9c96-65dbd72c1c81",
  "nbf": 1484592741,  
  "sub": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05"
}
.
"Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"

```

## <a name="example-of-an-encoded-jwt-assertion"></a>Przykład zakodowany potwierdzenie tokenu JWT

Następujący ciąg jest przykładem zakodowany potwierdzenia. Zwróć uwagę, można zauważyć trzy sekcje rozdzielone kropkami (.):
* Pierwsza sekcja koduje nagłówka
* Druga sekcja koduje ładunku
* Ostatnia sekcja jest podpis obliczane przy użyciu certyfikatów z zawartości pierwsze dwie sekcje

```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="register-your-certificate-with-azure-ad"></a>Rejestrowanie certyfikatu za pomocą usługi Azure AD

Poświadczenie certyfikatu można skojarzyć z aplikacją klienta w usłudze Azure AD w witrynie Azure portal przy użyciu dowolnej z następujących metod:

### <a name="uploading-the-certificate-file"></a>Przekazywanie pliku certyfikatu

Podczas rejestracji aplikacji platformy Azure dla aplikacji klienckiej:
1. Wybierz **certyfikaty i klucze tajne**. 
2. Kliknij pozycję **Przekaż certyfikat** i wybierz plik certyfikatu do przekazania.
3. Kliknij pozycję **Add** (Dodaj).
  Po przekazaniu certyfikatu odciskiem palca, daty rozpoczęcia i wygaśnięcia wartości są wyświetlane. 

### <a name="updating-the-application-manifest"></a>Aktualizowanie manifestu aplikacji

Problemy Wstrzymanie certyfikatu, należy do obliczenia:

- `$base64Thumbprint`, która jest base64 kodowanie skrót certyfikatu
- `$base64Value`, która jest base64 kodowanie dane pierwotne certyfikatu

Należy także podać identyfikator GUID w celu identyfikacji klucza w manifeście aplikacji (`$keyId`).

Podczas rejestracji aplikacji platformy Azure dla aplikacji klienckiej:
1. Wybierz **manifestu** można otworzyć w manifeście aplikacji.
2. Zastąp *keyCredentials* właściwości z informacjami certyfikatu przy użyciu następującego schematu.

   ```
   "keyCredentials": [
       {
           "customKeyIdentifier": "$base64Thumbprint",
           "keyId": "$keyid",
           "type": "AsymmetricX509Cert",
           "usage": "Verify",
           "value":  "$base64Value"
       }
   ]
   ```
3. Zapisać zmiany w manifeście aplikacji, a następnie przekaż manifest z usługą Azure AD. 

   `keyCredentials` Właściwość jest wielokrotne, dzięki czemu możesz przekazać wiele certyfikatów dla bardziej zaawansowane zarządzanie kluczami.
   
## <a name="code-sample"></a>Przykład kodu

Przykładowy kod na [uwierzytelniania w usłudze Azure AD w narzędziu aplikacje demona z certyfikatami](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential) pokazuje, jak aplikacja używa jej własnych poświadczeń do uwierzytelniania. Pokazuje także sposób [Utwórz certyfikat z podpisem własnym](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential#create-a-self-signed-certificate) przy użyciu `New-SelfSignedCertificate` polecenia programu Powershell. Można również wykorzystać i używać [skryptów tworzenia aplikacji](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/AppCreationScripts/AppCreationScripts.md) tworzenia certyfikatów, obliczenia odcisk palca i tak dalej.
