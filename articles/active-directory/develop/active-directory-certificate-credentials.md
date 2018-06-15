---
title: Certyfikat poświadczeń w usłudze Azure AD | Dokumentacja firmy Microsoft
description: W tym artykule omówiono rejestracji i stosowania certyfikatu poświadczeń dla uwierzytelniania aplikacji
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 88f0c64a-25f7-4974-aca2-2acadc9acbd8
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2018
ms.author: celested
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: c782429ac2d8ee030ca8b589b4241242c7b101d6
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
ms.locfileid: "34156504"
---
# <a name="certificate-credentials-for-application-authentication"></a>Certyfikat poświadczeń do uwierzytelniania aplikacji

Azure Active Directory umożliwia aplikacji korzystanie z własnych poświadczeń do uwierzytelniania. Na przykład w przepływ udzielania poświadczeń klienta OAuth w 2.0 ([v1](active-directory-protocols-oauth-service-to-service.md), [v2](active-directory-v2-protocols-oauth-client-creds.md)), a przepływ w imieniu-z ([v1](active-directory-protocols-oauth-on-behalf-of.md), [v2](active-directory-v2-protocols-oauth-on-behalf-of.md)).
Jeden formularz poświadczeniami, które mogą być używane jest potwierdzenie Token(JWT) sieci Web JSON, podpisanego przy użyciu certyfikatu, który jest właścicielem aplikacji.

## <a name="format-of-the-assertion"></a>Format potwierdzenia
Do obliczenia potwierdzenia, prawdopodobnie chcesz skorzystać z jednej z wielu [JSON Web Token](https://jwt.ms/) bibliotek w wybranym języku. Informacje przez token jest:

#### <a name="header"></a>Nagłówek

| Parametr |  Uwagi |
| --- | --- |
| `alg` | Powinien być **RS256** |
| `typ` | Powinien być **JWT** |
| `x5t` | Powinien być odcisk palca certyfikatu X.509 SHA-1. |

#### <a name="claims-payload"></a>Oświadczenia (ładunku)

| Parametr |  Uwagi |
| --- | --- |
| `aud` | Grupy odbiorców: Powinien być  **https://login.microsoftonline.com/ *tenant_Id*  /oauth2/token.** |
| `exp` | Data wygaśnięcia: Data wygaśnięcia tokenu. Czas jest reprezentowany jako liczba sekund od 1 stycznia 1970 (1970-01-01T0:0:0Z) UTC czasu wygaśnięcia ważności tokenu.|
| `iss` | Wystawca: powinien być client_id (identyfikator aplikacji usługi klienta) |
| `jti` | Identyfikator GUID: identyfikator JWT |
| `nbf` | Nie wcześniej niż: Data przed którym nie można użyć tokenu. Czas jest reprezentowany jako liczba sekund od 1 stycznia 1970 (1970-01-01T0:0:0Z) UTC czasu token został wystawiony. |
| `sub` | Podmiot: jak w przypadku `iss`, powinny być client_id (identyfikator aplikacji usługi klienta) |

#### <a name="signature"></a>Podpis

Podpis jest obliczana stosowania certyfikatu zgodnie z opisem w [specyfikacji RFC7519 tokenu Web JSON](https://tools.ietf.org/html/rfc7519)

### <a name="example-of-a-decoded-jwt-assertion"></a>Przykład dekodowane potwierdzenia JWT

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

### <a name="example-of-an-encoded-jwt-assertion"></a>Przykład zakodowanego potwierdzenia JWT

Następujący ciąg jest przykładem zakodowanego potwierdzenia. Zwróć uwagę, można zauważyć trzy części oddzielone kropkami (.).
Pierwsza sekcja koduje nagłówka, drugi ładunku oraz za ostatni jest z podpisem obliczonym z certyfikatami od zawartości najpierw dwie sekcje.
```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

### <a name="register-your-certificate-with-azure-ad"></a>Zarejestruj certyfikat z usługą Azure AD

Certyfikat poświadczeń można skojarzyć z aplikacji klienckiej w usłudze Azure AD za pomocą portalu Azure przy użyciu dowolnej z następujących metod:

**Przekazywanie pliku certyfikatu**

W aplikacji Azure rejestracji aplikacji klienckiej, kliknij polecenie **ustawienia**, kliknij przycisk **klucze** , a następnie kliknij przycisk **przekazać klucza publicznego**. Wybierz plik certyfikatu, aby przekazać i kliknij przycisk **zapisać**. Po zapisaniu, certyfikat zostanie przesłany i odcisku palca, Data rozpoczęcia i wygaśnięcia wartości są wyświetlane. 

**Aktualizowanie manifestu aplikacji**

O wstrzymywania certyfikatu, należy obliczyć:

- `$base64Thumbprint`, która jest base64 kodowanie skrót certyfikatu
- `$base64Value`, która jest base64 kodowanie dane pierwotne certyfikatu

Należy również podać identyfikator GUID, aby zidentyfikować klucza w manifeście aplikacji (`$keyId`).

W aplikacji Azure rejestracji aplikacji klienckiej, otwórz plik manifestu aplikacji i Zastąp *keyCredentials* właściwości z informacjami certyfikatu przy użyciu następującego schematu:

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

Zapisać zmiany w manifeście aplikacji, a następnie przekazać do usługi Azure AD. Właściwość keyCredentials jest wielowartościowe, więc może przekazać wiele certyfikatów bardziej zaawansowane funkcje zarządzania kluczami.
