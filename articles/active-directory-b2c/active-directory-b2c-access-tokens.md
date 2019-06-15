---
title: Żądanie tokenu dostępu — Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć żądanie tokenu dostępu z usługi Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1a545f1e0fd1360d9147280454fb8b75bf216152
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66507387"
---
# <a name="request-an-access-token-in-azure-active-directory-b2c"></a>Żądanie tokenu dostępu w usłudze Azure Active Directory B2C

*Token dostępu* zawiera oświadczenia, można użyć w usłudze Azure Active Directory (Azure AD) B2C, aby zidentyfikować udzielone uprawnienia do interfejsu API. Podczas wywoływania serwera zasobów, token dostępu musi być obecne w żądaniu HTTP. Token dostępu jest oznaczona jako **access_token** w odpowiedzi z usługi Azure AD B2C. 

W tym artykule dowiesz się, jak utworzyć żądanie tokenu dostępu dla aplikacji sieci web i interfejsu API sieci web. Aby uzyskać więcej informacji na temat tokenów w usłudze Azure AD B2C, zobacz [Przegląd tokenów w usłudze Azure Active Directory B2C](active-directory-b2c-reference-tokens.md).

> [!NOTE]
> **Łańcuchy interfejsu API w sieci Web (w imieniu z) nie jest obsługiwana przez usługę Azure AD B2C.** -Wiele architektur obejmuje internetowego interfejsu API, który musi wywołać inny podrzędny interfejsu API sieci web, zarówno zabezpieczone za pomocą usługi Azure AD B2C. Ten scenariusz jest typowy w klienci, którzy mają internetowy interfejs API zaplecza, która z kolei wywołuje inną usługę. Ten scenariusz łańcuchowych interfejsu API sieci web mogą być obsługiwane przy użyciu przyznania poświadczeń elementu nośnego JWT OAuth w wersji 2.0, znanych także jako przepływu w imieniu z. Jednak przepływ w imieniu z nie jest obecnie wdrażany w usłudze Azure AD B2C.

## <a name="prerequisites"></a>Wymagania wstępne

- [Utwórz przepływ użytkownika](tutorial-create-user-flows.md) aby użytkownicy mogli zarejestrować się i zaloguj się do aplikacji.
- Jeśli użytkownik jeszcze tego nie zrobiono, [Dodaj aplikacji interfejsu API do dzierżawy usługi Azure Active Directory B2C w sieci web](add-web-application.md).

## <a name="scopes"></a>Zakresy

Zakresy umożliwiają zarządzanie uprawnieniami do chronionych zasobów. Podczas żądania tokenu dostępu, aplikacja kliencka musi określić odpowiednich uprawnień w **zakres** parametr żądania. Na przykład, aby określić **wartość zakresu** z `read` dla interfejsu API, który ma **identyfikator URI Identyfikatora aplikacji** z `https://contoso.onmicrosoft.com/api`, byłoby zakres `https://contoso.onmicrosoft.com/api/read`.

Zakresy są używane przez internetowy interfejs API w celu implementowania kontroli dostępu opartej na zakresach. Na przykład użytkownicy internetowego interfejsu API mogą mieć dostęp zarówno do odczytu, jak i zapisu lub tylko dostęp do odczytu. Aby nabyć wiele uprawnień w tym samym żądaniu, można dodać wiele wpisów w jednej **zakres** parametr żądania, rozdzielone spacjami.

Poniższy przykład przedstawia zakresy zdekodowany w adresie URL:

```
scope=https://contoso.onmicrosoft.com/api/read openid offline_access
```

Poniższy przykład przedstawia zakresy zakodowane w adresie URL:

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fapi%2Fread%20openid%20offline_access
```

Jeśli zażądasz więcej zakresów niż co będzie udzielany dla aplikacji klienckiej, wywołanie zakończy się pomyślnie, jeśli co najmniej jednego uprawnienia. **Scp** oświadczenia w tokenie dostępu wynikowy jest wypełniana przy użyciu uprawnień, które zostały udzielone pomyślnie. Standardowa OpenID Connect określono kilka wartości specjalny zakres. Wymienione poniżej zakresy reprezentują uprawnienie do dostępu do profilu użytkownika:

- **openid** -żąda tokenu Identyfikatora.
- **offline_access** -żądania tokenu odświeżania przy użyciu [przepływu kodu autoryzacji](active-directory-b2c-reference-oauth-code.md).

Jeśli **response_type** parametru w `/authorize` żądanie zawiera `token`, **zakres** parametr musi zawierać co najmniej jeden zasób zakresu innego niż `openid` i `offline_access`, zostaną przyznane. W przeciwnym razie `/authorize` żądanie nie powiedzie się.

## <a name="request-a-token"></a>Żądania tokenu

Aby żądania tokenu dostępu, musisz mieć kod autoryzacji. Poniżej znajduje się przykład żądanie `/authorize` punktu końcowego dla kodu autoryzacji. Domeny niestandardowe nie są obsługiwane do użytku z tokenami dostępu. Korzystania z domeny name.onmicrosoft.com dzierżawy w adresie URL żądania.

W poniższym przykładzie Zastąp są następujące wartości:

- `<tenant-name>` — Nazwa dzierżawy usługi Azure AD B2C.
- `<policy-name>` — Nazwa przepływu niestandardowych zasad lub użytkownika.
- `<application-ID>` — Identyfikator aplikacji dla aplikacji sieci web, która została zarejestrowana do obsługi przepływu użytkownika.
- `<redirect-uri>` **Identyfikator URI przekierowania** wprowadzona podczas rejestrowania aplikacji klienckiej.

```
GET https://<tenant-name>.b2clogin.com/tfp/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/authorize?
client_id=<application-ID>
&nonce=anyRandomValue
&redirect_uri=https://jwt.ms
&scope=https://tenant-name>.onmicrosoft.com/api/read
&response_type=code 
```

Odpowiedzi z kodem autoryzacji powinny być podobne do poniższego przykładu:

```
https://jwt.ms/?code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
```

Po pomyślnym uzyskaniu kodu autoryzacji, można użyć go do wysłania żądania tokenu dostępu:

```
POST <tenant-name>.onmicrosoft.com/oauth2/v2.0/token?p=<policy-name> HTTP/1.1
Host: https://<tenant-name>.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code
&client_id=<application-ID>
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
&redirect_uri=https://jwt.ms
&client_secret=2hMG2-_:y12n10vwH...
```

Powinny zostać wyświetlone podobną do następującej:

```
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrN...",
    "token_type": "Bearer",
    "not_before": 1549647431,
    "expires_in": 3600,
    "expires_on": 1549651031,
    "resource": "f2a76e08-93f2-4350-833c-965c02483b11",
    "profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiJjNjRhNGY3ZC0zMDkxLTRjNzMtYTcyMi1hM2YwNjk0Z..."
}
```

Korzystając z https://jwt.ms zbadać tokenu dostępu, który został zwrócony, powinny zostać wyświetlone informacje podobne do poniższego przykładu:

```
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dl..."
}.{
  "iss": "https://contoso0926tenant.b2clogin.com/c64a4f7d-3091-4c73-a7.../v2.0/",
  "exp": 1549651031,
  "nbf": 1549647431,
  "aud": "f2a76e08-93f2-4350-833c-965...",
  "oid": "1558f87f-452b-4757-bcd1-883...",
  "sub": "1558f87f-452b-4757-bcd1-883...",
  "name": "David",
  "tfp": "B2C_1_signupsignin1",
  "nonce": "anyRandomValue",
  "scp": "read",
  "azp": "38307aee-303c-4fff-8087-d8d2...",
  "ver": "1.0",
  "iat": 1549647431
}.[Signature]
```

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o sposobie [Konfigurowanie tokenów w usłudze Azure AD B2C](configure-tokens.md)
