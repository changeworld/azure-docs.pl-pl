---
title: Żądanie tokenu dostępu — usługa Azure Active Directory B2C | Dokumenty firmy Microsoft
description: Dowiedz się, jak zażądać tokenu dostępu z usługi Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 8358d3378ea892ebeef653bcb51243c9f1aa0b8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259775"
---
# <a name="request-an-access-token-in-azure-active-directory-b2c"></a>Żądanie tokenu dostępu w usłudze Azure Active Directory B2C

*Token dostępu* zawiera oświadczenia, których można użyć w usłudze Azure Active Directory B2C (Azure AD B2C) w celu zidentyfikowania przyznanych uprawnień do interfejsów API. Podczas wywoływania serwera zasobów token dostępu musi znajdować się w żądaniu HTTP. Token dostępu jest oznaczony jako **access_token** w odpowiedziach z usługi Azure AD B2C.

W tym artykule pokazano, jak zażądać tokenu dostępu dla aplikacji sieci web i interfejsu API sieci Web. Aby uzyskać więcej informacji na temat tokenów w usłudze Azure AD B2C, zobacz [omówienie tokenów w usłudze Azure Active Directory B2C.](tokens-overview.md)

> [!NOTE]
> **Łańcuchy interfejsu API sieci Web (w imieniu) nie jest obsługiwany przez usługę Azure AD B2C.** - Wiele architektur zawiera internetowy interfejs API, który musi wywołać inny podrzędny interfejs API sieci web, oba zabezpieczone przez usługę Azure AD B2C. Ten scenariusz jest typowy dla klientów, którzy mają zaplecza interfejsu API sieci web, który z kolei wywołuje inną usługę. Ten scenariusz interfejsu API sieci web w łańcuchu mogą być obsługiwane przy użyciu OAuth 2.0 JWT poświadczeń poświadczeń, inaczej znany jako w imieniu przepływu. Jednak przepływ w imieniu nie jest obecnie implementowany w usłudze Azure AD B2C.

## <a name="prerequisites"></a>Wymagania wstępne

- [Utwórz przepływ użytkownika,](tutorial-create-user-flows.md) aby umożliwić użytkownikom rejestrację i zalogowanie się do aplikacji.
- Jeśli jeszcze tego nie zrobiono, [dodaj aplikację internetowego interfejsu API do dzierżawy usługi Azure Active Directory B2C.](add-web-application.md)

## <a name="scopes"></a>Zakresy

Zakresy umożliwiają zarządzanie uprawnieniami do chronionych zasobów. Gdy żądany jest token dostępu, aplikacja kliencka musi określić żądane uprawnienia w parametrze **zakresu** żądania. Na przykład, aby określić `read` wartość **zakresu** dla interfejsu API, `https://contoso.onmicrosoft.com/api`który ma identyfikator `https://contoso.onmicrosoft.com/api/read` **URI identyfikatora aplikacji** , zakres będzie .

Zakresy są używane przez internetowy interfejs API w celu implementowania kontroli dostępu opartej na zakresach. Na przykład użytkownicy internetowego interfejsu API mogą mieć dostęp zarówno do odczytu, jak i zapisu lub tylko dostęp do odczytu. Aby uzyskać wiele uprawnień w tym samym żądaniu, można dodać wiele wpisów w parametrze pojedynczego **zakresu** żądania, oddzielone spacjami.

W poniższym przykładzie przedstawiono zakresy dekodowane w adresie URL:

```
scope=https://contoso.onmicrosoft.com/api/read openid offline_access
```

W poniższym przykładzie przedstawiono zakresy zakodowane w adresie URL:

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fapi%2Fread%20openid%20offline_access
```

Jeśli zażądasz więcej zakresów niż to, co jest przyznawane dla aplikacji klienckiej, wywołanie powiedzie się, jeśli udzielono co najmniej jedno uprawnienie. Oświadczenie **scp** w tokenie dostępu wynikowego jest wypełniane tylko uprawnienia, które zostały pomyślnie przyznane. Standard OpenID Connect określa kilka specjalnych wartości zakresu. Następujące zakresy reprezentują uprawnienia dostępu do profilu użytkownika:

- **openid** — żąda tokenu identyfikatora.
- **offline_access** — żąda tokenu odświeżania przy użyciu [przepływów kodu Auth](authorization-code-flow.md).

Jeśli **parametr response_type** w `/authorize` żądaniu `token`zawiera parametr **zakresu** musi zawierać co najmniej `openid` `offline_access` jeden zakres zasobu inny niż i który zostanie przyznany. W przeciwnym `/authorize` razie żądanie kończy się niepowodzeniem.

## <a name="request-a-token"></a>Żądanie tokenu

Aby zażądać tokenu dostępu, potrzebny jest kod autoryzacji. Poniżej znajduje się przykład `/authorize` żądania do punktu końcowego dla kodu autoryzacji. Domeny niestandardowe nie są obsługiwane do użytku z tokenami dostępu. Użyj domeny tenant-name.onmicrosoft.com w adresie URL żądania.

W poniższym przykładzie można zastąpić następujące wartości:

- `<tenant-name>`- Nazwa dzierżawy usługi Azure AD B2C.
- `<policy-name>`- Nazwa niestandardowych zasad lub przepływu użytkownika.
- `<application-ID>`- Identyfikator aplikacji aplikacji sieci web, który został zarejestrowany do obsługi przepływu użytkownika.
- `<redirect-uri>`- **Identyfikator URI przekierowania** wprowadzony podczas rejestracji aplikacji klienckiej.

```HTTP
GET https://<tenant-name>.b2clogin.com/tfp/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/authorize?
client_id=<application-ID>
&nonce=anyRandomValue
&redirect_uri=https://jwt.ms
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&response_type=code
```

Odpowiedź z kodem autoryzacji powinna być podobna do tego przykładu:

```
https://jwt.ms/?code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
```

Po pomyślnym otrzymaniu kodu autoryzacji można go użyć do żądania tokenu dostępu:

```HTTP
POST <tenant-name>.onmicrosoft.com/oauth2/v2.0/token?p=<policy-name> HTTP/1.1
Host: <tenant-name>.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code
&client_id=<application-ID>
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
&redirect_uri=https://jwt.ms
&client_secret=2hMG2-_:y12n10vwH...
```

Powinieneś zobaczyć coś podobnego do następującej odpowiedzi:

```JSON
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

Podczas https://jwt.ms korzystania z badania tokenu dostępu, który został zwrócony, powinien zostać wyświetlony coś podobnego do następującego przykładu:

```JSON
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

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [skonfigurować tokeny w usłudze Azure AD B2C](configure-tokens.md)
