---
title: Uwierzytelnianie za pomocą usługi Azure AD i uzyskiwanie tokenu JWT przy użyciu protokołu OAuth 2.0
description: Przykładowy kod, przedstawiający sposób uwierzytelniania za pomocą usługi Azure Active Directory przy użyciu protokołu OAuth 2.0 dostęp do aplikacji zabezpieczonej sieci web i interfejsów API sieci web w Twojej organizacji.
services: active-directory
author: rloutlaw
manager: angerobe
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: routlaw
ms.custom: aaddev
ms.openlocfilehash: eb26101229ad60abae7a8a84f8dfa496488e84ba
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2018
ms.locfileid: "39579007"
---
# <a name="request-an-access-token-using-oauth-20-to-access-web-apis-and-applications-secured-by-azure-active-directory"></a>Żądanie tokenu dostępu, dostęp do interfejsów API sieci web i aplikacji zabezpieczonej przez usługi Azure Active Directory przy użyciu protokołu OAuth 2.0

W tym artykule pokazano, jak uzyskać JSON Web Token (JWT) dostęp do zasobów zabezpieczonych przez usługi Azure AD. Przyjęto założenie, że masz [token autoryzacji](/azure/active-directory/develop/active-directory-protocols-oauth-code#request-an-authorization-code) z uprawnienia przyznane przez użytkownika lub za pomocą [nazwy głównej usługi](/azure/active-directory/develop/active-directory-application-objects).

## <a name="build-the-request"></a>Żądanie kompilacji

Należy użyć następującego `POST` żądania HTTP, aby uzyskać token JWT dostęp do określonej aplikacji lub interfejs API zabezpieczony przez usługę Azure AD.

```http
POST https://{tenant}/oauth2/v2.0/token?client_id={client-id}
&scope={scope}
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&client_secret=JqQX2PNo9bpM0uEihUPzyrh   
```

### <a name="request-headers"></a>Nagłówki żądań

Wymagane są następujące nagłówki:

|Nagłówek żądania|Opis|  
|--------------------|-----------------|  
| *Host:* | https://login.microsoftonline.com |
| *Typ zawartości:*| application/x-www-form-urlencoded |
 

### <a name="uri-parameters"></a>Parametry identyfikatora URI

| Parametr     |                       | Opis                                                                                                                                                                                                                                                                                                                                                                                                                                |
|---------------|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| dzierżawa        | wymagane              | `{tenant}` Wartość w polu Ścieżka żądania może służyć do kontrolowania, kto może zalogować się do aplikacji. Dozwolone wartości to `common`, `organizations`, `consumers`i identyfikatorów dzierżawy. Aby uzyskać więcej informacji, zobacz [protokołu podstawy](active-directory-v2-protocols.md#endpoints).                                                                                                                                                   |
| client_id     | wymagane              | Identyfikator aplikacji portalu rejestracji ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) przypisanych aplikacji.                                                                                                                                                                                                                             |
| grant_type    | wymagane              | Musi być `authorization_code` dla przepływ kodu autoryzacji.                                                                                                                                                                                                                                                                                                                                                                            |
| scope         | wymagane              | Rozdzielonej spacjami listy zakresów. Zakresy w tej gałęzi musi być równoważna lub być podzbiorem wartości zakresów w wywołaniu `/authorize`. Jeśli określono w tym żądaniu zakresów obejmują wiele zasobów serwerów, punktu końcowego v2.0 zwróci tokenu do zasobu, określony w zakresie pierwszy. Aby uzyskać bardziej szczegółowy opis zakresów, zobacz [uprawnienia, wyrażania zgody i zakresy](v2-permissions-and-consent.md). |
| Kod          | wymagane              | Authorization_code, uzyskany w wywołaniu `/authorize`                                                                                                                                                                                                                                                                                                                                                                |
| redirect_uri  | wymagane              | Taką samą wartość redirect_uri, który został użyty do uzyskania authorization_code.                                                                                                                                                                                                                                                                                                                                                             |
| client_secret | wymagane dla aplikacji sieci web | Klucz tajny aplikacji, utworzonego w portalu rejestracji aplikacji dla aplikacji. Nie należy używać w aplikacji natywnej, ponieważ client_secrets nie mogą być w niezawodny sposób będą przechowywane na urządzeniach. Jest ona wymagana dla aplikacji sieci web i interfejsów API, które mają możliwość bezpiecznie przechowywać wartość client_secret po stronie serwera sieci web.  Wpisy tajne klienta musi być zakodowane w adresie URL przed wysłaniem.                                                                                 |
| wartość parametru code_verifier | opcjonalne              | Tym samym wartość parametru code_verifier użytego do uzyskania authorization_code. Wymagane, jeśli PKCE został użyty w żądaniu grant kod autoryzacji. Aby uzyskać więcej informacji, zobacz [PKCE RFC](https://tools.ietf.org/html/rfc7636)                                                                                                                                                                                                                                                                                             |
## <a name="handle-the-response"></a>Dojście do odpowiedzi

Odpowiedź oznaczająca Powodzenie tokenu będzie zawierać JWT token i będzie wyglądać następująco:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| Parametr     | Opis                                                                                                                                                                                                                                                                                                                                                                                                                                              |
|---------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| access_token  | Token żądanego dostępu. Aplikacja może używać tego tokenu do uwierzytelniania zabezpieczonych zasobów, takich jak interfejs API sieci web.                                                                                                                                                                                                                                                                                                                                    |
| token_type    | Wskazuje typ tokenu. Jedynym typem, który obsługuje usługi Azure AD jest elementu nośnego                                                                                                                                                                                                                                                                                                                                                                           |
| expires_in    | Jak długo token dostępu jest prawidłowy (w sekundach).                                                                                                                                                                                                                                                                                                                                                                                                       |
| scope         | Access_token nadaje się do zakresów.                                                                                                                                                                                                                                                                                                                                                                                                         |
| refresh_token | Token odświeżania OAuth 2.0. Aplikacja może używać tego tokenu uzyskiwanie tokenów dostępu dodatkowe, po upływie bieżącego tokenu dostępu. Refresh_tokens są długotrwałe i pozwala zachować dostęp do zasobów przez dłuższy czas. Aby uzyskać więcej szczegółów, zobacz [odwołania do tokenu v2.0](v2-id-and-access-tokens.md). <br> **Uwaga:** tylko wtedy, jeśli podana `offline_access` zażądano zakresu.                                               |
| id_token      | Niepodpisane JSON Web Token (JWT). Aplikację można dekodować segmentów tego tokenu na żądanie informacji o użytkowniku, który jest zalogowany. Wartości w pamięci podręcznej i ich wyświetlenie aplikacji, ale nie należy polegać na nich autoryzacji lub granice zabezpieczeń. Aby uzyskać więcej informacji na temat id_tokens zobacz [token odwołania do punktu końcowego v2.0](v2-id-and-access-tokens.md). <br> **Uwaga:** tylko wtedy, jeśli podana `openid` zażądano zakresu. |



