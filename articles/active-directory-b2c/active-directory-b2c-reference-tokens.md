---
title: Token odwołanie — usługi Azure AD B2C | Dokumentacja firmy Microsoft
description: Typy tokenów wystawionych w usłudze Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 08/16/2017
ms.author: davidmu
ms.openlocfilehash: 09d776b54941e33979d7969b25c35e67a53cf8f0
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="azure-ad-b2c-token-reference"></a>Usługi Azure AD B2C: Odwołanie Token

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Usługa Azure Active Directory B2C (Azure AD B2C) emituje kilka typów tokenów zabezpieczających w czasie przetwarzania każdego [przepływ uwierzytelniania](active-directory-b2c-apps.md). Ten dokument zawiera opis format właściwości zabezpieczeń i zawartości każdego typu tokenu.

## <a name="types-of-tokens"></a>Typy tokenów
Usługa Azure AD B2C obsługuje [protokół OAuth 2.0](active-directory-b2c-reference-protocols.md), które korzysta z obu tokenów dostępu i tokenów odświeżania. Obsługuje ona również uwierzytelniania i logowania za pomocą [OpenID Connect](active-directory-b2c-reference-protocols.md), który został wprowadzony trzeci typ tokenu: token Identyfikatora. Każdy z tych tokenów jest reprezentowany jako tokenu elementu nośnego.

Token elementu nośnego jest tokenem zabezpieczającym lekkie, która udziela dostępu "bearer" do chronionego zasobu. Przenoszącej jest każda strona, która może ona powodować tokenu. Usługa Azure AD B2C muszą najpierw zostać uwierzytelnione strona przed może odbierać tokenu elementu nośnego. Ale jeśli wymagane kroki nie są brane do zabezpieczania token w transmisji i przechowywania, może być przechwycony i używane przez niezamierzone strony. Niektóre tokeny zabezpieczające mają wbudowany mechanizm nieupoważnione uniemożliwia korzystania z nich, ale tokenów elementu nośnego nie mają ten mechanizm. Muszą one być transportowane w bezpiecznego kanału, takie jak zabezpieczeń warstwy transportu (HTTPS).

Token elementu nośnego, są przesyłane poza bezpiecznego kanału, strony złośliwych służy atak typu man-in--middle uzyskać token i używać go w celu uzyskania nieautoryzowanego dostępu do chronionego zasobu. Te same zasady zabezpieczeń mają zastosowanie, gdy tokenów elementu nośnego, są przechowywane lub w pamięci podręcznej do późniejszego użycia. Zawsze upewnij się, że aplikacja przesyła i przechowuje tokenów elementu nośnego w bezpieczny sposób.

Uwagi dodatkowe zabezpieczenia na tokenów elementu nośnego można znaleźć [RFC 6750 sekcji 5](http://tools.ietf.org/html/rfc6750).

Wiele na usługi Azure AD B2C wystawia tokeny są zaimplementowane jako tokenów sieci web JSON (Jwt). Token JWT jest compact, adres URL bezpiecznego sposób przekazywania informacji między dwiema stronami. Jwt zawierają informacje znane jako oświadczenia. Są potwierdzeniami informacji na temat elementów nośnych i podmiotu tokenu. Oświadczeń z tokenów Jwt są obiektami JSON, które są zakodowane i serializację do transmisji. Ponieważ Jwt wystawione przez usługi Azure AD B2C podpisany, ale nie są szyfrowane, można łatwo sprawdzić zawartość JWT do jego debugowania. Dostępnych jest kilka narzędzi który można to zrobić, w tym [jwt.ms](https://jwt.ms). Aby uzyskać więcej informacji na temat Jwt dotyczą [specyfikacje JWT](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

### <a name="id-tokens"></a>Tokeny Identyfikatora

Identyfikator tokenu jest formą token zabezpieczający, który odbiera aplikacji z usługi Azure AD B2C `/authorize` i `/token` punktów końcowych. Identyfikator tokeny są reprezentowane jako [Jwt](#types-of-tokens), i zawierają oświadczenia, które służą do identyfikowania użytkowników w aplikacji. Gdy tokeny Identyfikatora drogą kupna z `/authorize` punktu końcowego, są realizowane za pomocą [niejawnego przepływu](active-directory-b2c-reference-spa.md), która jest często używany do rejestrowania się w javascript użytkowników na podstawie aplikacji sieci web. Gdy tokeny Identyfikatora drogą kupna z `/token` punktu końcowego, są realizowane za pomocą [przepływu kodu poufne](active-directory-b2c-reference-oidc.md), co pozwala token ukryte w przeglądarce. Dzięki temu token do bezpiecznego wysłanie żądania HTTP do komunikacji między dwa składniki o tej samej aplikacji lub usługi. Można użyć oświadczenia w tokenie identyfikator, zgodnie z własnymi potrzebami. Są często używane do wyświetlania informacji o koncie, lub aby wprowadzić decyzje dotyczące kontroli dostępu w aplikacji.  

Zalogowano tokeny Identyfikatora, ale obecnie nie są zaszyfrowane. Gdy aplikację lub interfejsu API odbiera token Identyfikatora, należy [weryfikuje podpisu](#token-validation) potwierdzenie, czy token jest autentyczny. Aplikację lub interfejsu API należy zweryfikować kilka oświadczenia w tokenie udowodnić, że jest prawidłowy. W zależności od wymagań scenariusza oświadczeń zweryfikowany przez aplikację może się różnić, ale aplikacji należy wykonać niektóre [typowych operacji sprawdzania poprawności oświadczeń](#token-validation) w każdym scenariuszu.

#### <a name="sample-id-token"></a>Przykładowy identyfikator token
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

Token dostępu jest również formularza token zabezpieczający, który odbiera aplikacji z usługi Azure AD B2C `/authorize` i `/token` punktów końcowych. Tokeny dostępu są także przedstawione jako [Jwt](#types-of-tokens), i zawierają oświadczeń, których można użyć, aby określić uprawnienia nadanego swoje interfejsy API. Tokeny dostępu są podpisane, ale obecnie nie są zaszyfrowane. Tokeny dostępu mają służyć do zapewnienia dostępu do serwerów z interfejsów API i zasobów. Dowiedz się więcej o sposobie [używaj tokenów dostępu](active-directory-b2c-access-tokens.md). 

Interfejs API odebrania tokenu dostępu, należy [weryfikuje podpisu](#token-validation) potwierdzenie, czy token jest autentyczny. Interfejs API należy zweryfikować kilka oświadczenia w tokenie udowodnić, że jest prawidłowy. W zależności od wymagań scenariusza oświadczeń zweryfikowany przez aplikację może się różnić, ale aplikacji należy wykonać niektóre [typowych operacji sprawdzania poprawności oświadczeń](#token-validation) w każdym scenariuszu.

### <a name="claims-in-id-and-access-tokens"></a>Oświadczenia w tokenach dostępu i identyfikator

Gdy używasz usługi Azure AD B2C, masz precyzyjną kontrolę nad zawartością tokenów. Można skonfigurować [zasady](active-directory-b2c-reference-policies.md) wysłać niektóre zestawy danych użytkownika w oświadczeniach wymagane przez aplikację w operacjach. Oświadczenia te mogą obejmować właściwości standardowych, takich jak użytkownika `displayName` i `emailAddress`. Mogą również obejmować [atrybuty użytkownika niestandardowego](active-directory-b2c-reference-custom-attr.md) zdefiniowanej w katalogu usługi B2C. Każdy identyfikator i dostęp do tokenu, który pojawi się zawiera zestaw oświadczeń związanych z zabezpieczeniami. Aplikacje można użyć tych oświadczeń do bezpiecznego uwierzytelniania użytkowników i żądań.

Należy pamiętać, że oświadczenia w tokenach identyfikator nie są zwracane w określonej kolejności. Ponadto oświadczeń nowy może zostać wprowadzony w tokeny Identyfikatora, w dowolnym momencie. Aplikacji nie powinna zostać podzielona na miarę wprowadzania nowych oświadczeń. Poniżej przedstawiono oświadczenia, które powinny być objęte tokenów dostępu i identyfikator wystawione przez usługi Azure AD B2C. Wszelkie dodatkowe oświadczenia są określone przez zasady. Praktyki, spróbuj sprawdzanie oświadczenia w tokenie identyfikator próbki przez wklejenie go do [jwt.ms](https://jwt.ms). Dalsze szczegóły można znaleźć w [OpenID Connect specyfikacji](http://openid.net/specs/openid-connect-core-1_0.html).

| Name (Nazwa) | Claim | Przykładowa wartość | Opis |
| --- | --- | --- | --- |
| Grupy odbiorców |`aud` |`90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` |Oświadczenie odbiorców identyfikuje adresata tokenu. Dla usługi Azure AD B2C odbiorców jest identyfikator aplikacji dla danej aplikacji, przypisany do aplikacji w portalu rejestracji aplikacji. Aplikację należy sprawdzić tę wartość i odrzucenie tokenu, jeśli nie jest zgodny. |
| Wystawca |`iss` |`https://login.microsoftonline.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` |To oświadczenie identyfikuje usługę tokenu zabezpieczającego (STS), które tworzy i zwraca token. Identyfikuje katalog usługi Azure AD, w którym użytkownik został uwierzytelniony. Aplikację należy zweryfikować oświadczenia wystawcy, aby upewnić się, że token pochodzi od punktu końcowego v2.0 usługi Azure Active Directory. |
| wystawiony w |`iat` |`1438535543` |Oświadczenie to czas, jaką token został wystawiony, epoki czas. |
| Czas wygaśnięcia |`exp` |`1438539443` |Czas wygaśnięcia oświadczeń to czas, w którym token staje się nieprawidłowy, reprezentowane w czasie epoki. Aby sprawdzić ważność okres istnienia tokenu aplikacji należy użyć tego oświadczenia. |
| nie wcześniej niż |`nbf` |`1438535543` |Oświadczenie to godzina, o której token prawidłową, w czasie epoki. Zazwyczaj jest taka sama jak czas, który został wystawiony token. Aby sprawdzić ważność okres istnienia tokenu aplikacji należy użyć tego oświadczenia. |
| Wersja |`ver` |`1.0` |To jest wersja token Identyfikatora, zgodnie z definicją w usłudze Azure AD. |
| Kod skrótu |`c_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |Skrót kodu jest uwzględnione w tokenie identyfikator, tylko wtedy, gdy token wystawiony wraz z kodu autoryzacji protokołu OAuth 2.0. Skrót kodu może służyć do zweryfikowania autentyczności kod autoryzacji. Aby uzyskać więcej informacji na temat sposobu tę weryfikację należy przeprowadzić, zobacz [OpenID Connect specyfikacji](http://openid.net/specs/openid-connect-core-1_0.html).  |
| Skrót token dostępu |`at_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |Skrót token dostępu jest uwzględnione w tokenie identyfikator, tylko wtedy, gdy token wystawiony wraz z tokenu dostępu protokołu OAuth 2.0. Skrót tokenu dostępu może służyć do zweryfikowania autentyczności tokenu dostępu. Aby uzyskać więcej informacji na temat sposobu tę weryfikację należy przeprowadzić, zobacz [specyfikacji OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html)  |
| Identyfikator jednorazowy |`nonce` |`12345` |Identyfikator jednorazowy jest strategia umożliwia ograniczenie liczby powtórzeń tokenów ataków. Aplikację można określić identyfikatora jednorazowego w żądaniu autoryzacji przy użyciu `nonce` parametr zapytania. Wartości podane w żądaniu będzie obliczanie nie mają być modyfikowane w `nonce` oświadczeń tylko identyfikator tokenu. Dzięki temu można zweryfikować wartości z wartością określone na żądanie, co umożliwia skojarzenie sesji aplikacji z danego tokenu identyfikator aplikacji. Aplikację należy wykonać tej weryfikacji w procesie weryfikacji tokenu identyfikator. |
| Temat |`sub` |`884408e1-2918-4cz0-b12d-3aa027d7563b` |Jest to główna o tym, które token deklaracji rozkazujących informacje, takie jak użytkownika aplikacji. Ta wartość jest niezmienne i nie można ponownie przypisać lub ponownie. Może służyć do wykonywania sprawdzeń autoryzacji bezpiecznie, np. gdy jest używany do uzyskania dostępu do zasobu. Domyślnie oświadczeń podmiotu jest wypełniana o identyfikatorze obiektu użytkownika w katalogu. Aby dowiedzieć się więcej, zobacz [usługi Azure Active Directory B2C: tokenu sesji i konfiguracji rejestracji jednokrotnej](active-directory-b2c-token-session-sso.md). |
| Klasy kontekstu uwierzytelniania |`acr` |Nie dotyczy |Nie używane obecnie, z wyjątkiem starsze zasad. Aby dowiedzieć się więcej, zobacz [usługi Azure Active Directory B2C: tokenu sesji i konfiguracji rejestracji jednokrotnej](active-directory-b2c-token-session-sso.md). |
| Framework zasady zaufania |`tfp` |`b2c_1_sign_in` |Jest to nazwa zasady, które zostało użyte do uzyskania Identyfikatora tokenu. |
| Czas uwierzytelniania |`auth_time` |`1438535543` |Oświadczenie to czas, jaką ostatniego poświadczenia wprowadzonego użytkownika, reprezentowane w czasie epoki. |

### <a name="refresh-tokens"></a>Tokenów odświeżania
Odśwież tokeny to tokeny zabezpieczające, używanych przez aplikację do uzyskania nowe tokeny Identyfikatora i dostęp do tokenów w przepływu OAuth 2.0. Udostępniają one aplikacji długoterminowej dostęp do zasobów w imieniu użytkowników, bez konieczności interakcji z tymi użytkownikami.

Do odbierania odświeżenia tokenu w odpowiedzi tokenu aplikacji należy zażądać `offline_acesss` zakresu. Aby dowiedzieć się więcej o `offline_access` zakres, zapoznaj się [odwołanie do usługi Azure AD B2C protokołu](active-directory-b2c-reference-protocols.md).

Tokenów odświeżania są i będzie zawsze równa, całkowicie nieprzezroczysta dla aplikacji. Są wystawiane przez usługę Azure AD i mogą być kontrolowane i interpretowane tylko przez usługę Azure AD. Są one długotrwałe, ale aplikacja nie powinna być zapisana przy założeniu, że trwa token odświeżania w określonym okresie czasu. Tokeny odświeżania nieważne można w dowolnym momencie z różnych przyczyn. Jedynym sposobem na aplikację, aby sprawdzić, czy token odświeżania jest prawidłowy jest próba Zrealizuj go, wykonując żądania tokenu do usługi Azure AD.

Gdy zrealizować token odświeżania, aby uzyskać nowy token (i jeśli aplikacja ma przyznane `offline_access` zakresu), zostanie wyświetlony nowy token odświeżania w odpowiedzi tokenu. Należy zapisać token odświeżania nowo wystawione. Należy go zastąpić token odświeżania, który wcześniej użyty w żądaniu. Pozwala to zagwarantować, że tokenów odświeżania ważność tak długo, jak to możliwe.

## <a name="token-validation"></a>Token weryfikacji.
Można sprawdzić poprawności tokenu, aplikacji, należy sprawdzić podpis i oświadczeń tokenu.

Wiele bibliotek typu open source dostępnych sprawdzania poprawności tokenów Jwt, w zależności od preferowany język. Zaleca się, że można eksplorować te opcje, a nie implementacji logiki weryfikacji. Informacje przedstawione w tym przewodniku może pomóc prawidłowo używanie tych bibliotek.

### <a name="validate-the-signature"></a>Sprawdzanie poprawności podpisu
Token JWT zawiera trzy segmenty oddzielone `.` znaków. Pierwszy segment jest *nagłówka*, drugi jest *treści*, a trzeci *podpisu*. Segment podpisu umożliwia sprawdzenie oryginalności tokenu, dzięki czemu mogą być zaufane przez aplikację.

Azure AD B2C tokeny są podpisane za pomocą standardowych szyfrowanie asymetryczne algorytmy, takie jak RSA 256. Nagłówek tokenu zawiera informacje o metodzie kluczy i szyfrowania używany do podpisywania tokenu:

```
{
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

`alg` Oświadczeń wskazuje algorytm używany do podpisywania tokenu. `kid` Oświadczeń wskazuje określonego klucz publiczny, który został użyty do podpisania tokenu.

W dowolnym momencie usługi Azure AD można zarejestrować tokenu przy użyciu dowolny zestaw par kluczy publicznych i prywatnych. Usługi Azure AD obraca możliwe zestaw kluczy okresowo, więc aplikacja powinna być zapisana automatycznie obsługiwać te zmiany klucza. Uzasadnione częstotliwości, aby wyszukać aktualizacje do kluczy publicznych używane przez usługę Azure AD jest co 24 godziny.

Usługa Azure AD B2C ma punkt końcowy metadanych OpenID Connect. Umożliwia to aplikacjom można pobrać informacji na temat usługi Azure AD B2C w czasie wykonywania. Informacje te obejmują punkty końcowe, zawartość tokenu i klucze podpisywania tokenu. Katalogu usługi B2C zawiera dokument metadanych JSON dla każdej zasady. Na przykład dokument metadanych dla `b2c_1_sign_in` zasad w `fabrikamb2c.onmicrosoft.com` znajduje się pod adresem:

```
https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in
```

`fabrikamb2c.onmicrosoft.com` to jest katalog B2C używany do uwierzytelniania użytkownika, i `b2c_1_sign_in` zasady używany do uzyskiwania tokenu. Aby określić zasady, które zostało użyte do podpisywania tokenu i gdzie można pobrać metadanych, dostępne są dwie opcje. Po pierwsze, nazwa zasady jest dołączona do `acr` oświadczenia w tokenie. Przez base-64 dekodowania treści i deserializacji ciągu JSON, który daje może przeanalizować składni oświadczeń poza treść tokenu JWT. `acr` Oświadczenia będą nazwę zasady, którego użyto do wystawiania tokenu.  Drugą opcją jest do kodowania zasad wartości `state` parametr podczas wystawiania żądania, a następnie dekodowania go, aby określić zasady, które zostało użyte. Każda metoda jest prawidłowa.

Dokument metadanych jest obiekt JSON, który zawiera przydatne w kilku informacji. Obejmują one lokalizację punkty końcowe wymagane do przeprowadzenia uwierzytelniania OpenID Connect. Obejmuje to też `jwks_uri`, co daje lokalizacji zestawu kluczy publicznych które są używane do podpisywania tokenów. Tutaj podano lokalizacji, że najlepiej pobrać lokalizację dynamicznie za pomocą dokument metadanych i analizowania limit `jwks_uri`:

```
https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in
```

Dokument JSON znajdujący się pod tym adresem URL zawiera wszystkie informacje klucza publicznego używany w danym momencie. Może używać aplikacja `kid` oświadczeń w nagłówku JWT, aby wybrać klucz publiczny w dokumencie JSON, który jest używany do podpisywania tokenu określonego. Następnie można przeprowadzić sprawdzania poprawności podpisu za pomocą prawidłowy klucz publiczny i algorytm wskazane.

Opis sposobu wykonywania weryfikacji podpisu wykracza poza zakres tego dokumentu. Wiele bibliotek typu open source są dostępne dla pomocy z tym, jeśli zajdzie taka potrzeba.

### <a name="validate-the-claims"></a>Sprawdzanie poprawności oświadczenia
Gdy aplikację lub interfejsu API odbiera token Identyfikatora, jego również wykonywać kilka kontroli względem oświadczenia w tokenie identyfikator. Te obejmują, ale nie są ograniczone do:

* **Odbiorców** oświadczeń: sprawdza, czy identyfikator tokenu jest przeznaczona do aplikacji.
* **Nie wcześniej niż** i **czas wygaśnięcia** oświadczenia: te Sprawdź, czy identyfikator token nie wygasł.
* **Wystawcy** oświadczeń: sprawdza, czy token został wystawiony dla aplikacji przez usługę Azure AD.
* **Identyfikator jednorazowy**: jest strategię ograniczenie ataków powtórzeń tokenów.

Aby uzyskać pełną listę aplikacji należy wykonać sprawdzanie poprawności, zapoznaj się [OpenID Connect specyfikacji](https://openid.net). Szczegóły oczekiwanych wartości tych oświadczeń są zawarte w poprzednim [token sekcji](#types-of-tokens).  

## <a name="token-lifetimes"></a>Czasy życia tokenu
Następujące okresy tokenu są przekazywane do dalszego wiedzy użytkownika. One może pomóc podczas opracowywania i debugowania aplikacji. Należy pamiętać, że aplikacji nie powinna być zapisana oczekiwać żadnego z tych okresy istnienia pozostaje stała. Mogą i ulegnie zmianie. Przeczytaj więcej na temat [dostosowywania okresy istnienia tokenu](active-directory-b2c-token-session-sso.md) w usłudze Azure AD B2C.

| Token | Cykl życia | Opis |
| --- | --- | --- |
| Tokeny Identyfikatora |Jedna godzina |Identyfikator tokeny są zwykle prawidłowe godziny. Aplikacja sieci web umożliwia tego okresu istnienia Obsługa własnej sesji z użytkownikami (zalecane). Możesz również istnienia innej sesji. Jeśli Twoja aplikacja powinna Uzyskaj nowy identyfikator tokenu, należy po prostu Utwórz nowe żądanie logowania do usługi Azure AD. Jeśli użytkownik ma prawidłowy sesję z usługą Azure AD, ten użytkownik nie może być konieczne do ponowne wprowadzenie poświadczeń. |
| Tokenów odświeżania |Do 14 dni |Token odświeżania pojedynczego jest prawidłowa dla maksymalnie 14 dni. Jednak token odświeżania może być nieprawidłowy, w dowolnej chwili z kilku powodów. Aplikacji powinno być kontynuowane może podjąć próbę użycia token odświeżania, dopóki żądanie zakończy się niepowodzeniem lub aplikacja zastępuje nowy token odświeżania. Token odświeżania również może być nieprawidłowy, jeśli upłynie 90 dni od czasu ostatniego wprowadzone poświadczenia użytkownika. |
| Kodach autoryzacji |Pięć minut |Kody autoryzacji są celowo krótkim okresie. One powinny być zrealizowane natychmiast tokenów dostępu, tokeny Identyfikatora lub tokenów odświeżania po odebraniu. |

