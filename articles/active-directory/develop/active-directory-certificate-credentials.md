---
title: Poświadczenia certyfikatu platformy tożsamości firmy Microsoft
titleSuffix: Microsoft identity platform
description: W tym artykule omówiono rejestrację i używanie poświadczeń certyfikatu do uwierzytelniania aplikacji.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: ryanwi
ms.reviewer: nacanuma, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 5587d0bf2c6cdeb2a6ad5e7dc3a078097c48c7ea
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80884566"
---
# <a name="microsoft-identity-platform-application-authentication-certificate-credentials"></a>Poświadczenia certyfikatu uwierzytelniania aplikacji platformy tożsamości firmy Microsoft

Platforma tożsamości firmy Microsoft umożliwia aplikacji używanie własnych poświadczeń do uwierzytelniania, na przykład w programie [OAuth 2.0 Client Credentials Grant flowv2.0](v2-oauth2-client-creds-grant-flow.md) i [przepływie w imieniu).](v2-oauth2-on-behalf-of-flow.md)

Jedną z form poświadczeń, których aplikacja może używać do uwierzytelniania, jest asercja JSON Web Token(JWT) podpisana certyfikatem, który jest właścicielem aplikacji.

## <a name="assertion-format"></a>Format potwierdzenia
Platforma tożsamości firmy Microsoft Aby obliczyć twierdzenie, można użyć jednej z wielu bibliotek [tokenów sieci Web JSON](https://jwt.ms/) w wybranym języku. Informacje przenoszone przez token są następujące:

### <a name="header"></a>Nagłówek

| Parametr |  Uwaga |
| --- | --- |
| `alg` | Powinien być **RS256** |
| `typ` | Powinien być **JWT** |
| `x5t` | Powinien być odcisk palca X.509 Certificate SHA-1 |

### <a name="claims-payload"></a>Roszczenia (ładunek)

| Parametr |  Uwagi |
| --- | --- |
| `aud` | Publiczność: Powinna być ** https://login.microsoftonline.com/ *tenant_Id*/oauth2/token** |
| `exp` | Data wygaśnięcia: data wygaśnięcia tokenu. Czas jest reprezentowany jako liczba sekund od 1 stycznia 1970 (1970-01-01T0:0:0Z) UTC do czasu wygaśnięcia ważności tokenu.|
| `iss` | Wystawca: powinien być client_id (Identyfikator aplikacji usługi klienta) |
| `jti` | Identyfikator GUID: identyfikator JWT |
| `nbf` | Nie wcześniej: data, przed którą nie można użyć tokenu. Czas jest reprezentowany jako liczba sekund od 1 stycznia 1970 (1970-01-01T0:0:0Z) UTC do czasu wystawienia tokenu. |
| `sub` | Temat: Co `iss`do , powinien być client_id (Identyfikator aplikacji usługi klienta) |

### <a name="signature"></a>Sygnatura

Podpis jest obliczany przy zastosowaniu certyfikatu zgodnie z opisem w [specyfikacji JSON Web Token RFC7519](https://tools.ietf.org/html/rfc7519)

## <a name="example-of-a-decoded-jwt-assertion"></a>Przykład zdekodowanego potwierdzenia JWT

```JSON
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

## <a name="example-of-an-encoded-jwt-assertion"></a>Przykład zakodowanego potwierdzenia JWT

Poniższy ciąg jest przykładem zakodowanej asercji. Jeśli przyjrzysz się uważnie, zauważysz trzy sekcje oddzielone kropkami (.):
* Pierwsza sekcja koduje nagłówek
* Druga sekcja koduje ładunek
* Ostatnia sekcja to podpis obliczony z certyfikatami z zawartości pierwszych dwóch sekcji

```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="register-your-certificate-with-microsoft-identity-platform"></a>Rejestrowanie certyfikatu na platformie tożsamości firmy Microsoft

Poświadczenia certyfikatu można skojarzyć z aplikacją kliencką na platformie tożsamości firmy Microsoft za pośrednictwem witryny Azure portal przy użyciu dowolnej z następujących metod:

### <a name="uploading-the-certificate-file"></a>Przekazywanie pliku certyfikatu

W rejestracji aplikacji platformy Azure dla aplikacji klienckiej:
1. Wybierz **pozycję Certyfikaty & wpisy tajne**.
2. Kliknij **przycisk Przekaż certyfikat** i wybierz plik certyfikatu do przekazania.
3. Kliknij przycisk **Dodaj**.
  Po przekazaniu certyfikatu są wyświetlane odcisk palca, data rozpoczęcia i wartości wygaśnięcia.

### <a name="updating-the-application-manifest"></a>Aktualizowanie manifestu aplikacji

Po posiadaniu certyfikatu należy obliczyć:

- `$base64Thumbprint`, który jest kodowaniem base64 skrótu certyfikatu
- `$base64Value`, który jest kodowaniem base64 nieprzetworzonych danych certyfikatu

Należy również podać identyfikator GUID, aby zidentyfikować klucz`$keyId`w manifeście aplikacji ( ).

W rejestracji aplikacji platformy Azure dla aplikacji klienckiej:
1. Wybierz **manifest,** aby otworzyć manifest aplikacji.
2. Zastąp właściwość *keyCredentials* nowymi informacjami o certyfikacie przy użyciu następującego schematu.

   ```JSON
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
3. Zapisz zmiany w manifeście aplikacji, a następnie przekaż manifest do platformy tożsamości firmy Microsoft.

   Właściwość `keyCredentials` jest wielowartościowa, więc można przekazać wiele certyfikatów dla bogatsze zarządzanie kluczami.

## <a name="code-sample"></a>Przykład kodu

> [!NOTE]
> Nagłówek X5T należy obliczyć, konwertując go na podstawowy ciąg 64 przy użyciu skrótu certyfikatu. Kod do wykonania tego w `System.Convert.ToBase64String(cert.GetCertHash());`języku C# jest .

Przykładowy kod [.NET Core demona aplikacji konsoli przy użyciu platformy tożsamości firmy Microsoft](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) pokazuje, jak aplikacja używa własnych poświadczeń do uwierzytelniania. Pokazano również, jak można [utworzyć certyfikat z podpisem własnym](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/1-Call-MSGraph#optional-use-the-automation-script) za pomocą polecenia `New-SelfSignedCertificate` programu Powershell. Można również skorzystać i używać [skryptów tworzenia aplikacji](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/AppCreationScripts-withCert/AppCreationScripts.md) do tworzenia certyfikatów, obliczania odcisku palca i tak dalej.
