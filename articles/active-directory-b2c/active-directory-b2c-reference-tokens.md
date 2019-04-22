---
title: Przegląd tokenów — Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Więcej informacji na temat tokeny używane w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 11361bc6ab75e873e1b4081dcfc6492abc093b54
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2019
ms.locfileid: "59680268"
---
# <a name="overview-of-tokens-in-azure-active-directory-b2c"></a>Przegląd tokenów w usłudze Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Usługa Azure Active Directory B2C (Azure AD) B2C emituje kilka rodzajów tokenów zabezpieczających, ponieważ przetwarza każdy [przepływ uwierzytelniania](active-directory-b2c-apps.md). W tym dokumencie opisano format, zabezpieczenia właściwości i zawartość każdego typu tokenu.

## <a name="token-types"></a>Typy tokenów

Usługa Azure AD B2C obsługuje [protokoły OAuth 2.0 i OpenID Connect](active-directory-b2c-reference-protocols.md), co sprawia, że na użytek tokenów uwierzytelniania i bezpieczny dostęp do zasobów. Wszystkie tokeny używane w usłudze Azure AD B2C są [tokenów sieci web JSON (tokenów Jwt)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) zawierających potwierdzenia informacji na temat elementu nośnego oraz temat tokenu.

W komunikacji z usługą Azure AD B2C, używane są następujące generatory kodów:

- *Identyfikator tokenu* -JWT, który zawiera oświadczenia, że służy do identyfikowania użytkowników w aplikacji. Ten token jest bezpiecznie wysyłane w żądaniach HTTP do komunikacji między dwa składniki z tej samej aplikacji lub usługi. Można użyć oświadczenia w tokenie identyfikator, zgodnie z potrzebami. Są często używane do wyświetlania informacji o koncie lub podejmowania decyzji dotyczących kontroli dostępu w aplikacji. Zalogowano się tokeny Identyfikatora, ale nie są szyfrowane. Gdy Twoja aplikacja lub interfejsu API odbiera token Identyfikatora, musi ona zweryfikować podpisu, aby potwierdzić, że token jest autentyczny. Twoja aplikacja lub interfejsu API należy zweryfikować kilka oświadczenia w tokenie, aby potwierdzić, że jest prawidłowy. W zależności od wymagań scenariusza oświadczeń zweryfikowany przez aplikację może się różnić, ale Twoja aplikacja musi wykonać niektórych typowych operacji sprawdzania poprawności oświadczenia w każdym scenariuszu.
- *Token dostępu* -JWT, który zawiera oświadczenia, że służy do identyfikowania udzielone uprawnienia do interfejsu API. Tokeny dostępu są podpisane, ale ich nie są szyfrowane. Tokeny dostępu są używane do zapewnienia dostępu do interfejsów API i zasobów serwera.  Gdy interfejs API odbiera token dostępu, musi ona zweryfikować podpisu, aby potwierdzić, że token jest autentyczny. Interfejs API należy zweryfikować kilka oświadczenia w tokenie, aby potwierdzić, że jest prawidłowy. W zależności od wymagań scenariusza oświadczeń zweryfikowany przez aplikację może się różnić, ale Twoja aplikacja musi wykonać niektórych typowych operacji sprawdzania poprawności oświadczenia w każdym scenariuszu.
- *Token odświeżania* — tokeny odświeżania są używane do uzyskania nowych tokenów Identyfikatora i dostęp do tokenów w przepływ OAuth 2.0. Zapewniają one aplikacji za pomocą długoterminowe dostępu do zasobów w imieniu użytkowników, bez konieczności interakcji z tymi użytkownikami. Tokeny odświeżania jest nieprzezroczysta dla aplikacji. Wystawione przez usługę Azure AD B2C i mogą być kontrolowane i interpretowane tylko przez usługę Azure AD B2C. Są one długotrwałe, ale aplikacji nie powinien być zapisywany z założeniu, że token odświeżania jest ważny w określonym okresie czasu. Tokeny odświeżania można unieważniany w każdej chwili różnych powodów. Jedynym sposobem dla swojej aplikacji dowiedzieć się, czy token odświeżania jest prawidłowa jest próba Zrealizuj go, wprowadzając żądania tokenu do usługi Azure AD B2C. Po wprowadzeniu token odświeżania, aby uzyskać nowy token, pojawi się nowy token odświeżania w odpowiedzi tokenu. Zapisz nowy token odświeżania. Zastępuje token odświeżania, który wcześniej używany w żądaniu. Ta akcja ułatwia gwarantuje tokenów odświeżania i ważność tak długo, jak to możliwe. 

## <a name="endpoints"></a>Punkty końcowe

A [zarejestrowana aplikacja](tutorial-register-applications.md) odbiera tokenów i komunikuje się z usługą Azure AD B2C, wysyłając żądania do tych punktów końcowych:

- `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/authorize`
- `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/token`

Tokeny zabezpieczające, które Twoja aplikacja otrzymuje z usługi Azure AD B2C mogą pochodzić z `/authorize` lub `/token` punktów końcowych. Gdy tokeny Identyfikatora są uzyskanych z `/authorize` punktu końcowego, są realizowane za pomocą [niejawny przepływ](active-directory-b2c-reference-spa.md), która jest często używana dla użytkowników, logując się do aplikacji sieci web opartych na język javascript. Gdy tokeny Identyfikatora są uzyskanych z `/token` punktu końcowego, są realizowane za pomocą [przepływu poufnych kod](active-directory-b2c-reference-oidc.md), co pozwala token ukryty za pomocą przeglądarki.

## <a name="claims"></a>Oświadczenia

Gdy używasz usługi Azure AD B2C, masz szczegółową kontrolę nad zawartością tokenów. Można skonfigurować [przepływy użytkownika](active-directory-b2c-reference-policies.md) i [zasady niestandardowe](active-directory-b2c-overview-custom.md) do wysyłania oświadczeń, które są wymagane dla badanej aplikacji niektórych zestawów danych użytkownika. Te oświadczenia mogą zawierać właściwości standardowe takie jak **displayName** i **emailAddress**. Twoje aplikacje mogą używać tych oświadczeń do bezpiecznego uwierzytelniania użytkowników i żądania. 

Oświadczenia w tokeny Identyfikatora nie są zwracane w określonej kolejności. Nowych oświadczeń mogą zostać wprowadzone w tokeny Identyfikatora, w dowolnym momencie. Aplikacja nie należy przerwać wprowadzoną nowych oświadczeń. Możesz również uwzględnić [atrybutów niestandardowych użytkownika](active-directory-b2c-reference-custom-attr.md) w swoje oświadczenia.

Poniższa tabela zawiera listę oświadczeń można oczekiwać w tokenach identyfikator i dostęp do tokenów wystawionych przez usługę Azure AD B2C.

| Name (Nazwa) | Claim | Przykładowa wartość | Opis |
| ---- | ----- | ------------- | ----------- |
| Grupy odbiorców | `aud` | `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` | Identyfikuje zamierzonym odbiorcą tokenu. Dla usługi Azure AD B2C odbiorców jest identyfikator aplikacji. Aplikację należy sprawdzić tę wartość i odrzucenie tokenu, jeśli nie jest zgodny. Grupy odbiorców jest równoznaczny z zasobów. |
| Wystawca | `iss` |`https://{tenant}.b2clogin.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` | Określa usługę tokenu zabezpieczającego (STS), który tworzy i zwraca token. Identyfikuje katalogu, w którym użytkownik został uwierzytelniony. Aplikację należy zweryfikować Oświadczenie wystawcy, aby upewnić się, że token pochodzi od odpowiednich punktów końcowych. |
| Wydane w | `iat` | `1438535543` | Czas, w którym token został wystawiony, jest reprezentowana w czasie uniksowym. |
| Czas wygaśnięcia | `exp` | `1438539443` | Czas, w której token staje się nieprawidłowy, jest reprezentowana w czasie uniksowym. Twoja aplikacja powinna używać tego oświadczenia próba sprawdzania prawidłowości okres istnienia tokenu. |
| Nie wcześniej niż | `nbf` | `1438535543` | Czas, w którym token staje się nieprawidłowy, jest reprezentowana w czasie uniksowym. Tym razem jest zwykle taka sama jak czas, który został wystawiony token. Twoja aplikacja powinna używać tego oświadczenia próba sprawdzania prawidłowości okres istnienia tokenu. |
| Wersja | `ver` | `1.0` | Wersja tokenu Identyfikacyjnego, zgodnie z definicją w usłudze Azure AD B2C. |
| Kod skrótu | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Skrót kodu zawarte w tokenie identyfikator tylko wtedy, gdy token wystawiony wraz z kodu autoryzacji OAuth 2.0. Skrót kod może służyć do weryfikowania autentyczności kodu autoryzacji. Aby uzyskać więcej informacji na temat przeprowadzania tej weryfikacji, zobacz [specyfikacją z OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html).  |
| Skrót tokenu dostępu | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Dostęp do tokenu skrót zawarte w tokenie identyfikator tylko wtedy, gdy token wystawiony wraz z tokenu dostępu OAuth 2.0. Skrót tokenu dostępu może służyć do weryfikowania autentyczności tokenu dostępu. Aby uzyskać więcej informacji na temat przeprowadzania tej weryfikacji, zobacz [specyfikacją z OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html)  |
| Identyfikator jednorazowy | `nonce` | `12345` | Identyfikator jednorazowy jest strategii umożliwia ograniczenie liczby ataków powtarzania tokenu. Aplikację można określić identyfikatora jednorazowego w żądaniu autoryzacji przy użyciu `nonce` parametr zapytania. Wartość Podaj w żądaniu jest emitowane w niezmienionej postaci w `nonce` oświadczenia tylko tokenu Identyfikatora. To oświadczenie umożliwia aplikacji można zweryfikować wartości względem wartości określonej w żądaniu. Aplikacja powinna przeprowadzić tej weryfikacji w procesie weryfikacji tokenu Identyfikatora. |
| Podmiot | `sub` | `884408e1-2918-4cz0-b12d-3aa027d7563b` | Podmiot zabezpieczeń o tym, które token określa informacje, takie jak użytkownik aplikacji. Ta wartość jest niezmienny i nie może być ponownie przypisywany ani ponownie. Może służyć do sprawdzania autoryzacji bezpiecznie, np. gdy token jest używany do uzyskania dostępu do zasobu. Domyślnie roszczenie podmiotu jest wypełniana identyfikator obiektu użytkownika w katalogu. |
| Odwołania do klasy kontekstu uwierzytelniania | `acr` | Nie dotyczy | Używany tylko w przypadku starszych zasad. |
| Framework zasady zaufania | `tfp` | `b2c_1_signupsignin1` | Nazwa zasad, które zostało użyte do uzyskania tokenu Identyfikatora. |
| Czas uwierzytelniania | `auth_time` | `1438535543` | Czas, jaką wprowadzone przez użytkownika ostatniej poświadczeń, reprezentowany w czasie uniksowym. |
| Zakres | `scp` | `Read`| Uprawnienia przyznane zasobu dla tokenu dostępu. Wiele udzielone uprawnienia są oddzielone spacją. |
| Autoryzowane innych firm | `azp` | `975251ed-e4f5-4efd-abcb-5f1a8f566ab7` | **Identyfikator aplikacji** aplikacji klienta, który zainicjował żądanie. |

## <a name="configuration"></a>Konfigurowanie

Następujące właściwości są używane do [Zarządzanie okresy istnienia tokenów zabezpieczających](configure-tokens.md) emitowane przez usługę Azure AD B2C:

- **Dostęp czas życia tokenu Identyfikatora (w minutach)** — okres istnienia tokenu elementu nośnego OAuth 2.0, które są używane do uzyskiwania dostępu do chronionego zasobu. Wartość domyślna to 60 minut. Minimum (włącznie) wynosi 5 minut. Maksymalna (włącznie) to 1440 minut.

- **Czas życia tokenu odświeżania (dni)** — maksymalny okres, przed którym token odświeżania może służyć do uzyskania nowego dostępu lub identyfikator tokenu. Okres czasu obejmuje również uzyskiwanie nowego tokena odświeżania, jeśli aplikacja ma udzielonego `offline_access` zakresu. Wartość domyślna to 14 dni. Minimum (włącznie) to jeden dzień. (Włącznie) maksymalna wartość to 90 dni.

- **Czas życia okna przewijania tokenu odświeżania (dni)** — po tym czasie upłynięciu tego okresu użytkownik jest zmuszony do ponownego uwierzytelnienia, niezależnie od ostatniego okresu ważności tokenu odświeżania uzyskanego przez aplikację. Może być udostępniony tylko, jeśli przełącznik jest równa **powiązana**. Musi być większa lub równa **czas życia tokenu odświeżania (dni)** wartość. Jeśli przełącznik jest równa **niepowiązane**, nie może dostarczyć określonej wartości. Wartość domyślna to 90 dni. Minimum (włącznie) to jeden dzień. Maksymalna (włącznie) to 365 dni.

Następujące przypadki użycia są włączone, przy użyciu tych właściwości:

- Umożliwia użytkownikowi pozostanie w stanie zalogowania do aplikacji mobilnej na czas nieokreślony, tak długo, jak użytkownik jest ciągle aktywne w aplikacji. Możesz ustawić **przesuwanego okna czas życia tokenu odświeżania (dni)** do **niepowiązane** w przepływie logowania użytkownika.
- Spełnia wymagania dotyczące zgodności w branży zabezpieczeń i ustawiając okresów istnienia tokenu odpowiedni dostęp.

Te ustawienia nie są dostępne w przypadku resetowania haseł przepływy użytkownika. 

## <a name="compatibility"></a>Zgodność

Następujące właściwości są używane do [Zarządzanie zgodności tokenów](configure-tokens.md):

- **Oświadczenie wystawcy (iss)** — ta właściwość identyfikuje dzierżawy usługi Azure AD B2C, który wystawił token. Wartość domyślna to `https://<domain>/{B2C tenant GUID}/v2.0/`. Wartość `https://<domain>/tfp/{B2C tenant GUID}/{Policy ID}/v2.0/` zawiera identyfikatory dla dzierżawy usługi Azure AD B2C i przepływ użytkownika, który był używany w żądania tokenu. Jeśli aplikacji lub biblioteki musi być zgodne z usługi Azure AD B2C [specyfikacji protokołu OpenID Connect 1.0 odnajdywania](https://openid.net/specs/openid-connect-discovery-1_0.html), użyj tej wartości.

- **Oświadczenia podmiotu (pod)** — ta właściwość identyfikuje jednostkę, dla której token określa informacje. Wartość domyślna to **ObjectID**, która wypełnia `sub` oświadczenia w tokenie o identyfikatorze obiektu użytkownika. Wartość **nieobsługiwane** jest świadczona wyłącznie dla zgodności z poprzednimi wersjami. Zaleca się przejście na **ObjectID** jak tylko można.

- **Oświadczenie reprezentujące identyfikator zasad** — ta właściwość identyfikuje typ oświadczenia, do którego została wpisana nazwa zasad używanych w żądania tokenu. Wartość domyślna to `tfp`. Wartość `acr` jest świadczona wyłącznie dla zgodności z poprzednimi wersjami.

## <a name="pass-through"></a>Przekazywanie

Po uruchomieniu podróży użytkownika usługi Azure AD B2C odbiera token dostępu z dostawcy tożsamości. Usługa Azure AD B2C używa tego tokenu, można pobrać informacji o użytkowniku. Możesz [włączenie oświadczenia w przepływie użytkownika](idp-pass-through-user-flow.md) lub [zdefiniować oświadczenia w zasadach niestandardowych](idp-pass-through-custom.md) do przekazania tokenu za pośrednictwem aplikacji, które należy zarejestrować w usłudze Azure AD B2C. Aplikacja musi używać [przepływ użytkownika v2](user-flow-versions.md) z zalet przekazywanie tokenu jako oświadczenia.

Usługa Azure AD B2C aktualnie obsługuje tylko przekazywanie tokenu dostępu OAuth 2.0 dostawców tożsamości, które obejmują usługi Facebook i Google. W przypadku innych dostawców tożsamości oświadczenie zwracany jest pusty. 

## <a name="validation"></a>Walidacja

Aby zweryfikować token, aplikację należy sprawdzić podpis i oświadczeń tokenu. Wiele bibliotek typu open source są dostępne do weryfikacji tokenów Jwt, w zależności od preferowanego języka. Zalecane jest, że możesz zapoznaj się z tymi opcjami zamiast implementować logikę weryfikacji.

### <a name="validate-signature"></a>Weryfikowanie podpisu

Token JWT zawiera trzy segmenty *nagłówka*, *treści*, a *podpisu*. Segment podpisu może służyć do sprawdzania autentyczności tokenu, dzięki czemu mogą być zaufane przez aplikację. Usługa Azure AD B2C tokeny są podpisane za pomocą standardowych asymetrycznych algorytmów, takich jak RSA 256. 

Nagłówek token zawiera informacje o metodzie klucza i szyfrowania używany do podpisywania tokenu:

```
{
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

Wartość **algorytmu podpisu** oświadczeń jest algorytm, który był używany do podpisywania tokenu. Wartość **Kącik** oświadczeń jest klucz publiczny, który był używany do podpisywania tokenu. W dowolnym momencie usługi Azure AD B2C można podpisać tokenu przy użyciu dowolnej zestaw pary kluczy publiczny prywatny. Usługa Azure AD B2C, które okresowo obraca możliwe zestawu kluczy. Aplikacji mają być zapisywane automatycznie obsługiwać zmiany klucza. Uzasadnione częstotliwość, aby sprawdzić, czy są aktualizacje kluczy publicznych używane przez usługę Azure AD B2C jest co 24 godziny.

Usługa Azure AD B2C ma punkt końcowy metadanych OpenID Connect. Korzystając z tego punktu końcowego, aplikacje mogą żądać informacji na temat usługi Azure AD B2C w środowisku uruchomieniowym. Informacje te obejmują punkty końcowe, zawartość tokenów i tokenów kluczy podpisywania. Dzierżawy usługi Azure AD B2C zawiera dokumentu metadanych JSON dla każdej zasady. Dokument metadanych jest obiekt JSON, który zawiera przydatne w kilku informacji. Zawiera metadane **jwks_uri**, co daje lokalizacji zestawu kluczy publicznych, są używane do podpisywania tokenów. W tym miejscu podać lokalizację jednak użytkownika najlepiej pobrać lokalizacji dynamicznie, używając dokumentu metadanych i analizowania **jwks_uri**:

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_signupsignin1
```
Dokument JSON, znajdujący się pod tym adresem URL zawiera wszystkie informacje o kluczu publicznym używany w danym momencie. Twoja aplikacja może używać `kid` oświadczenia w nagłówku JWT, aby wybrać klucz publiczny w dokumencie JSON, który jest używany do podpisywania dany token. Następnie można wykonywać Weryfikacja podpisu przy użyciu prawidłowy klucz publiczny i wskazany algorytmu.

Dokument metadanych dla `B2C_1_signupsignin1` zasady `contoso.onmicrosoft.com` dzierżawa znajduje się w lokalizacji:

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_signupsignin1
```

Aby określić zasady, które zostało użyte do podpisywania tokenu i gdzie można zażądać metadanych, masz dwie opcje. Po pierwsze, nazwa zasad jest dołączona do `acr` oświadczenia w tokenie. Przez base-64 dekodowanie treści i deserializacji ciągu JSON, która wynika, można przeanalizować oświadczeń z treści tokenu JWT. `acr` Oświadczenie ma nazwę zasad, który został użyty do wystawiania tokenu. Druga opcja polega na zakodowaniu zasad w wartości `state` parametru podczas wystawiania żądania, a następnie dekodowania go, aby określić zasady, które zostało użyte. Każda z tych metod jest prawidłowy.

Opis sposobu wykonywania weryfikacji podpisu znajduje się poza zakres tego dokumentu. Wiele bibliotek typu open source dostępnych może pomóc podczas weryfikowania tokenu.

### <a name="validate-claims"></a>Sprawdź poprawność oświadczeń

Po otrzymaniu tokenu Identyfikacyjnego, interfejsu API lub aplikacje, go również wykonać kilka kontroli względem oświadczenia w tokenie identyfikator. Należy sprawdzić następujące oświadczeń:

- **odbiorcy** — sprawdza, czy identyfikator tokenu jest przeznaczona do aplikacji.
- **nie wcześniej niż** i **czas wygaśnięcia** — sprawdza, czy identyfikator tokenu nie wygasło.
- **Wystawca** — sprawdza, czy token został wystawiony do swojej aplikacji za pomocą usługi Azure AD B2C.
- **Identyfikator jednorazowy** -strategię ograniczania ryzyka ataków powtarzania tokenu.

Aby uzyskać pełną listę operacji sprawdzania poprawności, aplikacja powinna przeprowadzić dotyczą [specyfikacją z OpenID Connect](https://openid.net).  

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat [używanie tokenów dostępu](active-directory-b2c-access-tokens.md).

