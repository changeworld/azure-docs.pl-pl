---
title: Token odwołania w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Typy tokeny wystawione w usłudze Azure Active Directory B2C
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: d1c9101f10342f98803a4ace420abbed5d49ba23
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52880118"
---
# <a name="azure-ad-b2c-token-reference"></a>Usługi Azure AD B2C: Odwołanie tokenu

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Usługa Azure Active Directory B2C (Azure AD B2C) emituje kilka rodzajów tokenów zabezpieczających, ponieważ przetwarza każdy [przepływ uwierzytelniania](active-directory-b2c-apps.md). W tym dokumencie opisano format, zabezpieczenia właściwości i zawartość każdego typu tokenu.

## <a name="types-of-tokens"></a>Typy tokenów
Usługa Azure AD B2C obsługuje [Protokół autoryzacji OAuth 2.0](active-directory-b2c-reference-protocols.md), co sprawia, że oba tokeny dostępu i tokenów odświeżania. Obsługuje również uwierzytelnianie i logowanie za pomocą [OpenID Connect](active-directory-b2c-reference-protocols.md), która wprowadza trzeci typ tokenu: identyfikator tokenu. Każdy z tych tokenów jest przedstawiana jako token elementu nośnego.

Token elementu nośnego jest tokenem zabezpieczającym uproszczone, która udziela dostępu "bearer" do chronionego zasobu. Elementu nośnego jest każda strona, która może powodować tokenu. Usługa Azure AD B2C muszą najpierw zostać uwierzytelnione stroną przed może odbierać tokenu elementu nośnego. Ale jeśli nie podejmuje kroki wymagane do zabezpieczania token w transmisji i przechowywania, można go przechwycony i używane przez niezamierzone innych firm. Niektóre tokeny zabezpieczające mają wbudowany mechanizm nieupoważnione uniemożliwia korzystanie z nich, ale tokenów elementu nośnego nie mają ten mechanizm. Ich musi być transportowane zabezpieczony kanał, takie jak transport layer security (HTTPS).

Jeśli token elementu nośnego, są przesyłane poza bezpiecznego kanału, złośliwa strona użyć ataków typu man-in--middle do uzyskania tokenu i używać go w celu uzyskania nieautoryzowanego dostępu do chronionego zasobu. Te same zasady zabezpieczeń stosowane, gdy tokenów elementu nośnego, są przechowywane lub pamięci podręcznej do późniejszego użycia. Zawsze upewnij się, że Twoja aplikacja przesyła i przechowuje tokenów elementu nośnego w bezpieczny sposób.

Uwagi dodatkowe zabezpieczenie na tokenów elementu nośnego można znaleźć [RFC 6750 sekcji 5](https://tools.ietf.org/html/rfc6750).

Wiele tokenów wystawianych przez urząd usługi Azure AD B2C są implementowane jako tokenów sieci web JSON (tokenów Jwt). Token JWT jest compact, bezpieczny adres URL sposób przekazywania informacji między dwiema stronami. Tokenów Jwt zawierają informacje znane jako oświadczenia. Są to potwierdzenia informacji na temat elementu nośnego oraz temat tokenu. Oświadczenia w tokenów Jwt są obiektami JSON, które kodowania i serializacji do przesłania. Ponieważ tokenów Jwt wystawione przez usługę Azure AD B2C jest podpisany, ale nie są szyfrowane, można łatwo sprawdzić zawartość token JWT do debugowania. Dostępnych jest kilka narzędzi, można to zrobić, w tym [jwt.ms](https://jwt.ms). Aby uzyskać więcej informacji na temat tokenów Jwt, zobacz [specyfikacje JWT](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

### <a name="id-tokens"></a>Tokeny identyfikatorów

Identyfikator tokenu jest formą token zabezpieczający, który aplikacja otrzymuje z usługi Azure AD B2C `/authorize` i `/token` punktów końcowych. Identyfikator tokeny są reprezentowane jako [tokenów Jwt](#types-of-tokens), i mogą zawierać oświadczenia, które służą do identyfikowania użytkowników w swojej aplikacji. Gdy tokeny Identyfikatora są uzyskanych z `/authorize` punktu końcowego, są realizowane za pomocą [niejawny przepływ](active-directory-b2c-reference-spa.md), która jest często używana dla użytkowników, logując się do języka javascript na podstawie aplikacji sieci web. Gdy tokeny Identyfikatora są uzyskanych z `/token` punktu końcowego, są realizowane za pomocą [przepływu poufnych kod](active-directory-b2c-reference-oidc.md), co pozwala token ukryty za pomocą przeglądarki. Dzięki temu token do bezpiecznego wysłanie żądania HTTP do komunikacji między dwa składniki z tej samej aplikacji lub usługi. Można użyć oświadczenia w tokenie identyfikator, zgodnie z potrzebami. Są często używane do wyświetlania informacji o koncie lub podejmowania decyzji dotyczących kontroli dostępu w aplikacji.  

Zalogowano się tokeny Identyfikatora, ale obecnie nie są zaszyfrowane. Gdy swojej aplikacji lub interfejsu API odbiera token Identyfikatora, musi ona [zweryfikować podpisu](#token-validation) udowodnić, że token jest autentyczny. Aplikacji sieci Web lub interfejsu API należy zweryfikować kilka oświadczenia w tokenie, aby potwierdzić, że jest prawidłowy. W zależności od wymagań scenariusza oświadczeń zweryfikowany przez aplikację może się różnić, ale aplikacja musi wykonać niektóre [typowych roszczenie walidacji](#token-validation) w każdym scenariuszu.

#### <a name="sample-id-token"></a>Przykładowy identyfikator tokenu
```
// Line breaks for display purposes only

eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IklkVG9rZW5TaWduaW5nS2V5Q29udGFpbmVyIn0.
eyJleHAiOjE0NDIzNjAwMzQsIm5iZiI6MTQ0MjM1NjQzNCwidmVyIjoiMS4wIiwiaXNzIjoiaHR0cHM6Ly9s
b2dpbi5taWNyb3NvZnRvbmxpbmUuY29tLzc3NTUyN2ZmLTlhMzctNDMwNy04YjNkLWNjMzExZjU4ZDkyNS92
Mi4wLyIsImFjciI6ImIyY18xX3NpZ25faW5fc3RvY2siLCJzdWIiOiJOb3Qgc3VwcG9ydGVkIGN1cnJlbnRs
eS4gVXNlIG9pZCBjbGFpbS4iLCJhdWQiOiI5MGMwZmU2My1iY2YyLTQ0ZDUtOGZiNy1iOGJiYzBiMjlkYzYi
LCJpYXQiOjE0NDIzNTY0MzQsImF1dGhfdGltZSI6MTQ0MjM1NjQzNCwiaWRwIjoiZmFjZWJvb2suY29tIn0.
h-uiKcrT882pSKUtWCpj-_3b3vPs3bOWsESAhPMrL-iIIacKc6_uZrWxaWvIYkLra5czBcGKWrYwrAC8ZvQe
DJWZ50WXQrZYODEW1OUwzaD_I1f1HE0c2uvaWdGXBpDEVdsD3ExKaFlKGjFR2V7F-fPThkVDdKmkUDQX3bVc
yyj2V2nlCQ9jd7aGnokTPfLfpOjuIrTsAdPcGpe5hfSEuwYDmqOJjGs9Jp1f-eSNEiCDQOaTBSvr479L5ptP
XWeQZyX2SypN05Rjr05bjZh3j70ZUimiocfJzjibeoDCaQTz907yAg91WYuFOrQxb-5BaUoR7K-O7vxr2M-_
CQhoFA

```

### <a name="access-tokens"></a>Tokeny dostępu

Token dostępu jest również formularza token zabezpieczający, który aplikacja otrzymuje z usługi Azure AD B2C `/authorize` i `/token` punktów końcowych. Tokeny dostępu są także przedstawione jako [tokenów Jwt](#types-of-tokens), i mogą zawierać oświadczenia, które służą do identyfikowania udzielone uprawnienia do interfejsu API. Tokeny dostępu są podpisane, ale obecnie nie są zaszyfrowane. Tokeny dostępu powinny służyć do zapewnienia dostępu do interfejsów API i zasobów serwerów. Dowiedz się więcej na temat [używanie tokenów dostępu](active-directory-b2c-access-tokens.md). 

Gdy interfejs API odbiera token dostępu, musi ona [zweryfikować podpisu](#token-validation) udowodnić, że token jest autentyczny. Interfejs API należy zweryfikować kilka oświadczenia w tokenie, aby potwierdzić, że jest prawidłowy. W zależności od wymagań scenariusza oświadczeń zweryfikowany przez aplikację może się różnić, ale aplikacja musi wykonać niektóre [typowych roszczenie walidacji](#token-validation) w każdym scenariuszu.

### <a name="claims-in-id-and-access-tokens"></a>Oświadczenia w tokenach dostępu i identyfikator

Gdy używasz usługi Azure AD B2C, masz szczegółową kontrolę nad zawartością tokenów. Można skonfigurować [przepływy użytkownika](active-directory-b2c-reference-policies.md) i niestandardowych zasad do wysyłania oświadczeń, które wymaganych przez aplikację w operacjach niektórych zestawów danych użytkownika. Te oświadczenia mogą zawierać właściwości standardowych, takich jak użytkownika `displayName` i `emailAddress`. Mogą również obejmować [atrybutów niestandardowych użytkownika](active-directory-b2c-reference-custom-attr.md) zdefiniowanemu w katalogu usługi B2C. Każdy identyfikator i dostęp do tokenu, który pojawi się zawiera zestaw oświadczeń związanych z zabezpieczeniami. Twoje aplikacje mogą używać tych oświadczeń do bezpiecznego uwierzytelniania użytkowników i żądania.

Należy pamiętać, że oświadczenia w tokeny Identyfikatora nie są zwracane w określonej kolejności. Ponadto nowe oświadczenia mogą zostać wprowadzone w tokeny Identyfikatora, w dowolnym momencie. Aplikacja nie powinna zostać podzielona na miarę wprowadzania nowych oświadczeń. Poniżej przedstawiono oświadczeń, których oczekujesz, że istnieje w tokenach dostępu i identyfikator, które są wystawione przez usługę Azure AD B2C. Dodatkowe oświadczenia są określane przez zasady. Praktyki, spróbuj wykonać sprawdzanie oświadczenia w tokenie identyfikator przykładowy przez wklejenie go do [jwt.ms](https://jwt.ms). Więcej szczegółowych informacji można znaleźć w [specyfikacją z OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html).

| Name (Nazwa) | Claim | Przykładowa wartość | Opis |
| --- | --- | --- | --- |
| Grupy odbiorców |`aud` |`90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` |Oświadczenia odbiorców identyfikuje zamierzonym odbiorcą tokenu. Dla usługi Azure AD B2C odbiorców jest identyfikator aplikacji dla swojej aplikacji, przypisany do aplikacji w portalu rejestracji aplikacji. Aplikację należy sprawdzić tę wartość i odrzucenie tokenu, jeśli nie jest zgodny. Grupy odbiorców jest równoznaczny z zasobów. |
| Wystawca |`iss` |`https://{tenantname}.b2clogin.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` |To oświadczenie identyfikuje usługę tokenu zabezpieczającego (STS), który tworzy i zwraca token. Identyfikuje katalog usługi Azure AD, w którym użytkownik został uwierzytelniony. Aplikację należy zweryfikować Oświadczenie wystawcy, aby upewnić się, że token pochodzi od punktu końcowego v2.0 usługi Azure Active Directory. |
| Wydane w |`iat` |`1438535543` |To oświadczenie jest czas, w którym token został wystawiony, reprezentowany w czasie uniksowym. |
| Czas wygaśnięcia |`exp` |`1438539443` |Czas wygaśnięcia, oświadczenia to czas, w której token staje się nieprawidłowy, reprezentowany w czasie uniksowym. Twoja aplikacja powinna używać tego oświadczenia próba sprawdzania prawidłowości okres istnienia tokenu. |
| Nie wcześniej niż |`nbf` |`1438535543` |To oświadczenie jest czasu, jaką token staje się nieprawidłowy, jest reprezentowana w czasie uniksowym. Zazwyczaj jest taka sama jak czas, który został wystawiony token. Twoja aplikacja powinna używać tego oświadczenia próba sprawdzania prawidłowości okres istnienia tokenu. |
| Wersja |`ver` |`1.0` |To jest wersja tokenu Identyfikacyjnego, zgodnie z definicją w usłudze Azure AD. |
| Kod skrótu |`c_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |Skrót kod znajduje się w tokenie identyfikator tylko wtedy, gdy token wystawiony wraz z kodu autoryzacji OAuth 2.0. Skrót kod może służyć do weryfikowania autentyczności kodu autoryzacji. Aby uzyskać więcej informacji na temat sposobu tę weryfikację należy przeprowadzić, zobacz [specyfikacją z OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html).  |
| Skrót tokenu dostępu |`at_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |Skrót tokenu dostępu znajduje się w tokenie identyfikator tylko wtedy, gdy token wystawiony wraz z tokenu dostępu OAuth 2.0. Skrót tokenu dostępu może służyć do weryfikowania autentyczności tokenu dostępu. Aby uzyskać więcej informacji na temat sposobu tę weryfikację należy przeprowadzić, zobacz [specyfikacją z OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html)  |
| Identyfikator jednorazowy |`nonce` |`12345` |Identyfikator jednorazowy jest strategii umożliwia ograniczenie liczby ataków powtarzania tokenu. Aplikację można określić identyfikatora jednorazowego w żądaniu autoryzacji przy użyciu `nonce` parametr zapytania. Wartości podane w żądaniu będzie obliczanie w niezmienionej postaci w `nonce` oświadczenia tylko tokenu Identyfikatora. Umożliwia to aplikacji, aby sprawdzić wartość względem wartości, które on określony w żądaniu, co umożliwi skojarzenie sesji aplikacji przy użyciu podanego tokenu Identyfikatora. Aplikację należy wykonywać tej weryfikacji w procesie weryfikacji tokenu Identyfikatora. |
| Podmiot |`sub` |`884408e1-2918-4cz0-b12d-3aa027d7563b` |To jest jednostka o tym, które token określa informacje, takie jak użytkownik aplikacji. Ta wartość jest niezmienny i nie może być ponownie przypisywany ani ponownie. Może służyć do sprawdzania autoryzacji bezpiecznie, np. gdy token jest używany do uzyskania dostępu do zasobu. Domyślnie roszczenie podmiotu jest wypełniana identyfikator obiektu użytkownika w katalogu. Aby dowiedzieć się więcej, zobacz [usługi Azure Active Directory B2C: tokenów, sesji i konfiguracji rejestracji jednokrotnej](active-directory-b2c-token-session-sso.md). |
| Odwołania do klasy kontekstu uwierzytelniania |`acr` |Nie dotyczy |Nie używane obecnie, z wyjątkiem w przypadku starszych zasad. Aby dowiedzieć się więcej, zobacz [usługi Azure Active Directory B2C: tokenów, sesji i konfiguracji rejestracji jednokrotnej](active-directory-b2c-token-session-sso.md). |
| Framework zasady zaufania |`tfp` |`b2c_1_sign_in` |Jest to nazwa zasad, które zostało użyte do uzyskania tokenu Identyfikatora. |
| Czas uwierzytelniania |`auth_time` |`1438535543` |To oświadczenie jest czasu, jaką ostatniego wprowadzić poświadczenia użytkownika, są reprezentowane w czasie uniksowym. |

### <a name="refresh-tokens"></a>Tokenów odświeżania
Odśwież tokeny są tokeny zabezpieczające, które aplikacji można użyć do uzyskania nowych tokenów Identyfikatora i dostępu do tokenów w przepływ OAuth 2.0. Zapewniają one aplikację długoterminowe dostęp do zasobów w imieniu użytkowników, bez konieczności interakcji z tymi użytkownikami.

Do odbierania odświeżenia tokenu w odpowiedzi tokenu, aplikacja musi żądać `offline_access` zakresu. Aby dowiedzieć się więcej na temat `offline_access` zakresu, zapoznaj się [odwołanie do usługi Azure AD B2C protokołu](active-directory-b2c-reference-protocols.md).

Tokenów odświeżania są i będą zawsze, całkowicie nieprzezroczysty do swojej aplikacji. Wystawione przez usługi Azure AD i mogą być kontrolowane i interpretowane tylko przez usługę Azure AD. Są one długotrwałe, ale aplikacja nie powinna być zapisana przy założeniu, że token odświeżania jest ważny w określonym okresie czasu. Tokeny odświeżania można unieważniany w każdej chwili różnych powodów. Jedynym sposobem dla swojej aplikacji dowiedzieć się, czy token odświeżania jest prawidłowy jest próba Zrealizuj go, wprowadzając żądania tokenu do usługi Azure AD.

Po zrealizowaniu token odświeżania, aby uzyskać nowy token (i jeśli aplikacja ma udzielonego `offline_access` zakres), zostanie wyświetlony nowy token odświeżania w odpowiedzi tokenu. Należy zapisać token odświeżania nowo wydane. Należy go zastąpić token odświeżania, który wcześniej użyty w żądaniu. Pozwala to zagwarantować tokenów odświeżania i ważność tak długo, jak to możliwe.

## <a name="token-validation"></a>Weryfikacja tokenu
Aby zweryfikować token, aplikację należy sprawdzić podpis i oświadczeń tokenu.

Wiele bibliotek typu open source są dostępne do weryfikacji tokenów Jwt, w zależności od preferowanego języka. Zaleca się, że możesz zapoznaj się z tymi opcjami zamiast implementować logikę weryfikacji. Informacje przedstawione w tym przewodniku może pomóc dowiedzieć się, jak prawidłowo używać tych bibliotek.

### <a name="validate-the-signature"></a>Weryfikowanie podpisu
Token JWT zawiera trzy segmenty oddzielone `.` znaków. To pierwszy segment *nagłówka*, druga jest *treści*, a trzecia będzie *podpisu*. Segment podpisu może służyć do sprawdzania autentyczności tokenu, dzięki czemu mogą być zaufane przez aplikację.

Usługa Azure AD B2C tokeny są podpisane za pomocą standardowych asymetrycznych algorytmów, takich jak RSA 256. Nagłówek token zawiera informacje o metodzie klucza i szyfrowania używany do podpisywania tokenu:

```
{
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

`alg` Oświadczeń Określa algorytm, który był używany do podpisywania tokenu. `kid` Oświadczenia wskazuje określonego klucza publicznego, który był używany do podpisywania tokenu.

W dowolnym momencie usługi Azure AD można podpisać tokenu przy użyciu dowolnej z zestawu pary kluczy publiczny prywatny. Usługa Azure AD obraca się możliwe zestawu kluczy okresowo, więc aplikacji mają być zapisywane automatycznie obsługiwać zmiany klucza. Uzasadnione częstotliwość, aby sprawdzić, czy są aktualizacje kluczy publicznych używane przez usługę Azure AD jest co 24 godziny.

Usługa Azure AD B2C ma punkt końcowy metadanych OpenID Connect. Dzięki temu aplikacje, które można pobrać informacji na temat usługi Azure AD B2C w środowisku uruchomieniowym. Informacje te obejmują punkty końcowe, zawartość tokenów i tokenów kluczy podpisywania. Katalogu usługi B2C zawiera dokumentu metadanych JSON dla każdej zasady. Na przykład dokument metadanych dla `b2c_1_sign_in` zasady `fabrikamb2c.onmicrosoft.com` znajduje się w folderze:

```
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in
```

`fabrikamb2c.onmicrosoft.com` jest to katalog B2C, używany do uwierzytelniania użytkownika, i `b2c_1_sign_in` jest zasady stosowane w celu uzyskania tokenu. Aby określić zasady, które zostało użyte do podpisywania tokenu i gdzie można pobrać metadanych, masz dwie opcje. Po pierwsze, nazwa zasad jest dołączona do `acr` oświadczenia w tokenie. Przez base-64 dekodowanie treści i deserializacji ciągu JSON, która wynika, można przeanalizować oświadczeń z treści tokenu JWT. `acr` Oświadczenia będą nazwę zasad, który został użyty do wystawiania tokenu.  Drugą opcją jest do zakodowania zasad w wartości `state` parametru podczas wystawiania żądania, a następnie dekodowania go, aby określić zasady, które zostało użyte. Każda z tych metod jest prawidłowy.

Dokument metadanych jest obiekt JSON, który zawiera przydatne w kilku informacji. Obejmują one lokalizacji punktów końcowych wymagane w celu przeprowadzenia uwierzytelniania OpenID Connect. Te aktualizacje obejmują również `jwks_uri`, co daje lokalizacji zestawu kluczy publicznych, są używane do podpisywania tokenów. Lokalizacja znajduje się w tym miejscu, że najlepiej pobrać lokalizacji dynamicznie, używając dokumentu metadanych i analizowania się `jwks_uri`:

```
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in
```

Dokument JSON, znajdujący się pod tym adresem URL zawiera wszystkie informacje o kluczu publicznym używany w danym momencie. Twoja aplikacja może używać `kid` oświadczenia w nagłówku JWT, aby wybrać klucz publiczny w dokumencie JSON, który jest używany do podpisywania dany token. Następnie można wykonywać Weryfikacja podpisu przy użyciu prawidłowy klucz publiczny i wskazany algorytmu.

Opis sposobu wykonywania weryfikacji podpisu znajduje się poza zakres tego dokumentu. Wiele bibliotek typu open source są dostępne do udzielenia odpowiedzi na to, jeśli jest potrzebna.

### <a name="validate-the-claims"></a>Sprawdzanie poprawności oświadczenia
Gdy swojej aplikacji lub interfejsu API odbiera token Identyfikatora, on również wykonać kilka kontroli z roszczeń w tokenie identyfikator. Te obejmują, ale nie są ograniczone do:

* **Odbiorców** oświadczenia: sprawdza, czy identyfikator tokenu jest przeznaczona do swojej aplikacji.
* **Nie wcześniej niż** i **czas wygaśnięcia** oświadczenia: te Sprawdź, czy nie wygasł token Identyfikatora.
* **Wystawcy** oświadczenia: sprawdza, czy token został wystawiony przez usługę Azure AD w aplikacji.
* **Jednorazowego**: jest to strategię ograniczania ryzyka ataków powtarzania tokenu.

Aby uzyskać pełną listę aplikacji, należy wykonać sprawdzanie poprawności, zobacz [specyfikacją z OpenID Connect](https://openid.net). Szczegółowe informacje o oczekiwanej wartości te oświadczenia są uwzględnione w poprzednim [token sekcji](#types-of-tokens).  

## <a name="token-lifetimes"></a>Okresy istnienia tokenu
Aby jeszcze bardziej wykorzystać wiedzę na temat znajdują się następujące okresów istnienia tokenu. Mogą one pomóc podczas tworzenia i debugowania aplikacji. Pamiętaj, że aplikacje nie powinien być zapisywany można oczekiwać, żadnego z tych okresy istnienia, aby pozostał bez zmian. Mogą i ulegną zmianie. Przeczytaj więcej na temat [dostosowywania okresów istnienia tokenu](active-directory-b2c-token-session-sso.md) w usłudze Azure AD B2C.

| Token | Okres istnienia | Opis |
| --- | --- | --- |
| Tokeny identyfikatorów |Jedna godzina |Identyfikator tokeny są zazwyczaj prawidłowe na godzinę. Twoja aplikacja sieci web umożliwia ten okres istnienia utrzymują własne sesje użytkowników (zalecane). Możesz również okres istnienia innej sesji. Jeśli aplikacja wymaga uzyskać token nowego Identyfikatora, należy po prostu utworzyć nowe żądanie logowania do usługi Azure AD. Jeśli użytkownik ma prawidłową sesję z usługą Azure AD, ten użytkownik może nie być konieczne ponowne wprowadzenie poświadczeń. |
| Tokenów odświeżania |Maksymalnie 14 dni |Token odświeżania pojedynczego nadaje się do maksymalnie 14 dni. Jednak token odświeżania mogą stać się nieprawidłowe w dowolnym momencie z kilku powodów. Aplikacja powinna w dalszym ciągu spróbuj użyć tokenu odświeżania, dopóki żądanie zakończy się niepowodzeniem lub dopóki aplikacji zamienia nowego tokenu odświeżania. Token odświeżania może również stać się nieprawidłowy, jeśli upłynęło 90 dni, ponieważ ostatnio wprowadzone poświadczenia użytkownika. |
| Kody autoryzacji |Pięć minut |Kody autoryzacji są celowo krótkotrwałe. One powinien można wymienić natychmiast tokenów dostępu, tokeny Identyfikatora lub tokeny odświeżania po ich odebraniu. |

