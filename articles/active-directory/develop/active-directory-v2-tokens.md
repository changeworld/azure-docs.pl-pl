---
title: Azure Active Directory v2.0 tokeny odwołania | Dokumentacja firmy Microsoft
description: Typy tokenów i oświadczeń emitowane przez punktu końcowego v2.0 usługi Azure AD
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: dc58c282-9684-4b38-b151-f3e079f034fd
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/22/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: d7b9ad5c76b0e20a3c58bddcc4947482b237fb8f
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
ms.locfileid: "34164462"
---
# <a name="azure-active-directory-v20-tokens-reference"></a>Azure Active Directory w wersji 2.0 tokeny odwołania
Punktu końcowego v2.0 usługi Azure Active Directory (Azure AD) emituje kilka typów tokenów zabezpieczających w każdym [przepływ uwierzytelniania](active-directory-v2-flows.md). To odwołanie opisuje format właściwości zabezpieczeń i zawartości każdego typu tokenu.

> [!NOTE]
> Punktu końcowego v2.0 nie obsługuje wszystkich scenariuszy Azure Active Directory i funkcji. Aby ustalić, czy należy używać punktu końcowego v2.0, przeczytaj o [ograniczenia v2.0](active-directory-v2-limitations.md).
>
>

## <a name="types-of-tokens"></a>Typy tokenów
Obsługuje punktu końcowego v2.0 [protokół OAuth 2.0](active-directory-v2-protocols.md), który korzysta z tokenów dostępu i tokenów odświeżania. Punktu końcowego v2.0 obsługuje również uwierzytelniania i logowania za pomocą [OpenID Connect](active-directory-v2-protocols.md). OpenID Connect wprowadza trzeci typ tokenu, token Identyfikatora. Każdy z tych tokenów jest reprezentowany jako *elementu nośnego* tokenu.

Token elementu nośnego jest tokenem zabezpieczającym lekkie, która udziela dostępu elementu nośnego do chronionego zasobu. Przenoszącej jest każda strona, która może ona powodować tokenu. Mimo że strona musi uwierzytelniać się z usługą Azure AD do odbierania tokenu elementu nośnego, jeśli kroki nie zostaną podjęte w celu bezpiecznego tokenu podczas transmisji i przechowywania, można przechwycony i używane przez niezamierzone strony. Niektóre tokeny zabezpieczające mają wbudowany mechanizm uniemożliwić nieupoważnione ich użycie, ale nie tokenów elementu nośnego. Musi być transportowane tokenów elementu nośnego bezpiecznego kanału, takie jak zabezpieczeń warstwy transportu (HTTPS). Jeśli token elementu nośnego, są przesyłane bez zabezpieczeń tego typu, za pomocą strony złośliwych atak"man-in--middle" Aby uzyskać token i użyć jej do nieautoryzowanego dostępu do chronionego zasobu. Te same zasady zabezpieczeń mają zastosowanie po zapisaniu lub buforowanie tokenów elementu nośnego do późniejszego użycia. Zawsze upewnij się, że aplikacji bezpiecznego przesyła i przechowuje tokenów elementu nośnego. Aby uzyskać więcej uwagi dotyczące zabezpieczeń tokenów elementu nośnego, zobacz [RFC 6750 sekcji 5](http://tools.ietf.org/html/rfc6750).

Wiele tokenów wystawionych przez punktu końcowego v2.0 są zaimplementowane jako tokenów sieci Web JSON (Jwt). Token JWT jest compact, adres URL bezpiecznego sposób przekazywania informacji między dwiema stronami. Informacje zawarte w token JWT jest nazywany *oświadczeń*. Jest potwierdzenie informacji na temat elementów nośnych i temat tokenu. Oświadczenia w token JWT są obiektów JavaScript Object Notation (JSON), które są zakodowane i serializację do transmisji. Ponieważ Jwt wystawione przez punktu końcowego v2.0 podpisany, ale nie są szyfrowane, możesz łatwo sprawdzić zawartość token JWT na potrzeby debugowania. Aby uzyskać więcej informacji dotyczących tokenów Jwt, zobacz [specyfikacji JWT](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

### <a name="id-tokens"></a>Tokeny Identyfikatora
Identyfikator tokenu jest formą logowania w tokenie zabezpieczającym aplikacja odbiera podczas uwierzytelniania przy użyciu [OpenID Connect](active-directory-v2-protocols.md). Identyfikator tokeny są reprezentowane jako [Jwt](#types-of-tokens), i zawierają oświadczeń, których używasz do logowania użytkownika do aplikacji. Można użyć oświadczenia w tokenie identyfikator na różne sposoby. Zazwyczaj Administratorzy korzystają tokeny Identyfikatora, aby wyświetlić informacje o koncie, lub aby wprowadzić decyzje dotyczące kontroli dostępu w aplikacji. Problemy z punktem końcowym v2.0 tylko jeden typ tokenu identyfikator, który ma spójny zestaw oświadczeń, bez względu na typ użytkownika, który jest zalogowany. Format i zawartość tokeny Identyfikatora są takie same dla osobistych użytkowników kont Microsoft i konta firmowego lub szkolnego.

Tokeny Identyfikatora są obecnie podpisany, ale nie jest zaszyfrowany. Gdy aplikacja odbiera token Identyfikatora, należy [weryfikuje podpisu](#validating-tokens) potwierdzenia autentyczności tokenu i sprawdzić poprawności kilku oświadczenia w tokenie potwierdzenie jego ważności. Oświadczenia zweryfikowany przez aplikację różny w zależności od wymagań scenariusza, ale aplikacji należy wykonać niektóre [typowych operacji sprawdzania poprawności oświadczeń](#validating-tokens) w każdym scenariuszu.

Firma Microsoft zapewniają wszystkich szczegółów dotyczących oświadczenia w tokenach identyfikator w poniższych sekcjach, oprócz Identyfikatora przykładowy token. Należy pamiętać, że oświadczenia w tokenach identyfikator nie są zwracane w określonej kolejności. Ponadto oświadczeń nowy mogą zostać wprowadzone do tokeny Identyfikatora, w dowolnym momencie. Aplikacji nie powinna Przerwij, gdy wprowadzono nowych oświadczeń. Poniższa lista zawiera oświadczenia, które aplikacji obecnie można niezawodnie zinterpretować. Szczegółowe informacje można znaleźć [OpenID Connect specyfikacji](http://openid.net/specs/openid-connect-core-1_0.html).

#### <a name="sample-id-token"></a>Przykładowy identyfikator token
```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiI2NzMxZGU3Ni0xNGE2LTQ5YWUtOTdiYy02ZWJhNjkxNDM5MWUiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vYjk0MTk4MTgtMDlhZi00OWMyLWIwYzMtNjUzYWRjMWYzNzZlL3YyLjAiLCJpYXQiOjE0NTIyODUzMzEsIm5iZiI6MTQ1MjI4NTMzMSwiZXhwIjoxNDUyMjg5MjMxLCJuYW1lIjoiQmFiZSBSdXRoIiwibm9uY2UiOiIxMjM0NSIsIm9pZCI6ImExZGJkZGU4LWU0ZjktNDU3MS1hZDkzLTMwNTllMzc1MGQyMyIsInByZWZlcnJlZF91c2VybmFtZSI6InRoZWdyZWF0YmFtYmlub0BueXkub25taWNyb3NvZnQuY29tIiwic3ViIjoiTUY0Zi1nZ1dNRWppMTJLeW5KVU5RWnBoYVVUdkxjUXVnNWpkRjJubDAxUSIsInRpZCI6ImI5NDE5ODE4LTA5YWYtNDljMi1iMGMzLTY1M2FkYzFmMzc2ZSIsInZlciI6IjIuMCJ9.p_rYdrtJ1oCmgDBggNHB9O38KTnLCMGbMDODdirdmZbmJcTHiZDdtTc-hguu3krhbtOsoYM2HJeZM3Wsbp_YcfSKDY--X_NobMNsxbT7bqZHxDnA2jTMyrmt5v2EKUnEeVtSiJXyO3JWUq9R0dO-m4o9_8jGP6zHtR62zLaotTBYHmgeKpZgTFB9WtUq8DVdyMn_HSvQEfz-LWqckbcTwM_9RNKoGRVk38KChVJo4z5LkksYRarDo8QgQ7xEKmYmPvRr_I7gvM2bmlZQds2OeqWLB1NSNbFZqyFOCgYn3bAQ-nEQSKwBaA36jYGPOVG2r2Qv1uKcpSOxzxaQybzYpQ
```

> [!TIP]
> Praktyki, aby sprawdzić oświadczenia w tokenie identyfikator próbki, wklej przykładowy token identyfikator do [jwt.ms](http://jwt.ms/).
>
>

#### <a name="claims-in-id-tokens"></a>Oświadczenia w tokenach identyfikator
| Name (Nazwa) | Claim | Przykładowa wartość | Opis |
| --- | --- | --- | --- |
| grupy odbiorców |`aud` |`6731de76-14a6-49ae-97bc-6eba6914391e` |Identyfikuje adresata tokenu. W tokenach identyfikator odbiorców jest identyfikator aplikacji aplikacji, przypisany do aplikacji w portalu rejestracji aplikacji firmy Microsoft. Aplikację należy sprawdzić tę wartość i odrzucenie tokenu, jeśli wartość nie jest zgodna. |
| Wystawcy |`iss` |`https://login.microsoftonline.com/b9419818-09af-49c2-b0c3-653adc1f376e/v2.0 ` |Identyfikuje usługę tokenu zabezpieczającego (STS), które tworzy i zwraca token i dzierżawy usługi Azure AD, w którym użytkownik został uwierzytelniony. Aplikację należy zweryfikować oświadczenia wystawcy, aby upewnić się, że token pochodzą z punktem końcowym v2.0. On również należy używać części identyfikatora GUID oświadczenie tak, aby ograniczyć zestaw dzierżawcami, które można zalogować się do aplikacji. Identyfikator GUID, który wskazuje, czy użytkownik jest użytkownikiem użytkownika z konta Microsoft jest `9188040d-6c67-4c5b-b112-36a304b66dad`. |
| wystawiony w |`iat` |`1452285331` |Czas, w którym token został wystawiony, reprezentowane w czasie epoki. |
| czas wygaśnięcia |`exp` |`1452289231` |Czas, w którym token staje się nieprawidłowy, reprezentowane w czasie epoki. Aby sprawdzić ważność okres istnienia tokenu aplikacji należy użyć tego oświadczenia. |
| nie wcześniej niż |`nbf` |`1452285331` |Czas, w którym token staje się nieprawidłowy, reprezentowane w czasie epoki. Zazwyczaj jest taka sama jak godzina wystawienia. Aby sprawdzić ważność okres istnienia tokenu aplikacji należy użyć tego oświadczenia. |
| wersja |`ver` |`2.0` |Wersja token Identyfikatora, zgodnie z definicją w usłudze Azure AD. Wartość dla punktu końcowego v2.0 jest `2.0`. |
| Identyfikator dzierżawy |`tid` |`b9419818-09af-49c2-b0c3-653adc1f376e` |Identyfikator GUID reprezentujący dzierżawy usługi Azure AD, do której należy użytkownik z. Dla kont służbowych identyfikator GUID jest Identyfikatorem niezmienne dzierżawy organizacji, do której należy użytkownik. Dla konta osobiste, wartość jest `9188040d-6c67-4c5b-b112-36a304b66dad`. `profile` Zakres jest wymagany w celu odbierania tego oświadczenia. |
| Kod skrótu |`c_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |Skrót kod znajduje się w tokeny Identyfikatora tylko wtedy, gdy wystawiony token Identyfikatora z kodu autoryzacji protokołu OAuth 2.0. Może służyć do zweryfikowania autentyczności kod autoryzacji. Aby uzyskać szczegółowe informacje o wykonywaniu tej weryfikacji, zobacz [OpenID Connect specyfikacji](http://openid.net/specs/openid-connect-core-1_0.html). |
| Skrót token dostępu |`at_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |Tokeny dostępu, których token wyznaczania wartości skrótu jest uwzględniona w identyfikatorze, tylko gdy wydano tokenu identyfikator z tokenem dostępu protokołu OAuth 2.0. Może służyć do zweryfikowania autentyczności tokenu dostępu. Aby uzyskać szczegółowe informacje o wykonywaniu tej weryfikacji, zobacz [OpenID Connect specyfikacji](http://openid.net/specs/openid-connect-core-1_0.html). |
| Identyfikator jednorazowy |`nonce` |`12345` |Identyfikator jednorazowy jest strategii łagodzenia ataków powtórzeń tokenów. Aplikację można określić identyfikatora jednorazowego w żądaniu autoryzacji przy użyciu `nonce` parametr zapytania. Wartości podane w żądaniu jest emitowany w tokenie identyfikator `nonce` oświadczenia, nie mają być modyfikowane. Aplikację można sprawdzić wartość względem wartości określone dla żądania, który kojarzy z określonym tokenem identyfikator sesji aplikacji. Aplikację należy wykonać tej weryfikacji w procesie weryfikacji tokenu identyfikator. |
| name |`name` |`Babe Ruth` |Oświadczenia nazwy zawiera wartość zrozumiałą dla użytkownika, która identyfikuje podmiotu tokenu. Wartość nie musi być unikatowy, jest modyfikowalna, i został zaprojektowany tak, aby można używać tylko do wyświetlania. `profile` Zakres jest wymagany w celu odbierania tego oświadczenia. |
| wyślij wiadomość e-mail |`email` |`thegreatbambino@nyy.onmicrosoft.com` |Podstawowego adresu e-mail skojarzonego z konta użytkownika, jeśli taka istnieje. Jego wartość jest modyfikowalna i może ulec zmianie. `email` Zakres jest wymagany w celu odbierania tego oświadczenia. |
| Preferowany nazwy użytkownika |`preferred_username` |`thegreatbambino@nyy.onmicrosoft.com` |Nazwa głównej reprezentuje użytkownika w punkcie końcowym v2.0. Może to być adres e-mail, numer telefonu lub ogólny nazwy użytkownika bez określonego formatu. Jego wartość jest modyfikowalna i może ulec zmianie. Ponieważ jest modyfikowalna, ta wartość nie należy używana do podejmowania decyzji dotyczących autoryzacji. `profile` Zakres jest wymagany w celu odbierania tego oświadczenia. |
| Temat |`sub` |`MF4f-ggWMEji12KynJUNQZphaUTvLcQug5jdF2nl01Q` | Podmiot zabezpieczeń o tym, które token deklaracji rozkazujących informacje, takie jak użytkownika aplikacji. Ta wartość jest niezmienne i nie można ponownie przypisać lub ponownie. Może służyć do wykonywania sprawdzeń autoryzacji bezpiecznie, np. gdy jest używany do uzyskania dostępu do zasobu, a może być używany jako klucz w tabelach bazy danych. Ponieważ podmiot jest zawsze znajdujących się w tokeny problemów z usługą Azure AD, zaleca się korzystanie z tej wartości w systemie autoryzacji ogólnego przeznaczenia. Podmiot jest jednak parowania identyfikator — unikatowy identyfikator aplikacji. W związku z tym jeśli jeden użytkownik zaloguje się do dwóch różnych aplikacji przy użyciu dwóch identyfikatorów innego klienta, tych aplikacji zostanie wyświetlony dwóch różnych wartości oświadczeń podmiotu. To może lub nie może być wskazane w zależności od wymagań architektury i ochrony prywatności. |
| Identyfikator obiektu: |`oid` |`a1dbdde8-e4f9-4571-ad93-3059e3750d23` | Niezmienne identyfikator obiektu programu Microsoft identity system, w tym przypadku konta użytkownika. Można go również używane do wykonywania sprawdzeń autoryzacji i bezpiecznie jako klucz w tabelach bazy danych. Ten identyfikator unikatowo identyfikuje użytkownika w aplikacjach — dwóch różnych aplikacji podpisywania w ten sam użytkownik otrzyma tę samą wartość w `oid` oświadczeń. Oznacza to, że mogą być używane podczas wykonywania kwerend do usług online firmy Microsoft, takich jak Microsoft Graph. Program Microsoft Graph, którą będzie zwracać ten identyfikator jako `id` właściwości dla danego konta użytkownika. Ponieważ `oid` umożliwia wielu aplikacjom do skorelowania użytkowników, `profile` zakres jest wymagany w celu odbierania tego oświadczenia. Należy pamiętać, że jeden użytkownik istnieje w wielu dzierżawców, użytkownik będzie zawierać identyfikator inny obiekt, w każdej dzierżawy — są traktowane jako różne konta, nawet jeśli użytkownik loguje się do wszystkich kont z tymi samymi poświadczeniami. |

### <a name="access-tokens"></a>Tokeny dostępu

Punktu końcowego v2.0 umożliwia aplikacji innych firm, które są zarejestrowane w usłudze Azure AD do wystawiania tokenów dostępu do zabezpieczonych zasobów, takich jak interfejsów API sieci Web. Aby uzyskać więcej informacji o konfigurowaniu aplikacji do wystawiania tokenów dostępu, zobacz [jak zarejestrować aplikację z punktem końcowym v2.0](active-directory-v2-app-registration.md). Podczas rejestracji aplikacji z punktem końcowym v2.0, deweloper można określić poziom dostępu o nazwie **zakresy**, do którego dostęp może być wystawiony tokenów. Na przykład **calendars.read** zakres zdefiniowany w interfejsu API programu Microsoft Graph przyznaje uprawnienia do odczytu kalendarza użytkownika. Gdy aplikacja odbiera token dostępu z punktem końcowym v2.0, należy sprawdzić podpisu tokenu, wystawcy, odbiorców, czas wygaśnięcia i inne roszczenia, w zależności od danego scenariusza. 

W przypadku żądania tokenu dostępu z punktem końcowym v2.0, punktu końcowego v2.0 również zwraca metadane dotyczące tokenu dostępu dla aplikacji do użycia. Informacje te obejmują czas wygaśnięcia tokenu dostępu i zakresów, dla których jest prawidłowy. Aplikacja wykorzystuje te metadane, aby wykonać inteligentne buforowanie tokenów dostępu bez konieczności przeanalizować otwórz sam token dostępu.

### <a name="refresh-tokens"></a>Tokenów odświeżania
Odśwież tokeny to tokeny zabezpieczające, które można pobrać nowe tokeny dostępu w przepływu OAuth 2.0 może używać aplikacja. Tokeny odświeżania może używać aplikacja do osiągnięcia długoterminowej dostęp do zasobów w imieniu użytkownika bez konieczności interakcji z użytkownikiem.

Tokeny odświeżania są wielu zasobów. Token odświeżania otrzymał podczas żądania tokenu dla jednego zasobu można wykorzystać tokenów dostępu na zupełnie innego zasobu.

Aby otrzymywać odświeżania w odpowiedzi tokenu, aplikacji należy zażądać i otrzymać `offline_access` zakresu. Aby dowiedzieć się więcej o `offline_access` zakres, zobacz [zgody i zakresy](active-directory-v2-scopes.md) artykułu.

Tokenów odświeżania są i zawsze będą, całkowicie nieprzezroczysta dla aplikacji. One są wystawiane przez punktu końcowego v2.0 usługi Azure AD i można tylko inspekcji, a interpretowane przez punktu końcowego v2.0. Są one długotrwałe, ale aplikacji nie powinna być zapisana można oczekiwać, że token odświeżania będą trwać przez dowolnego okresu. Tokeny odświeżania może być nieważne w dowolnym momencie z różnych przyczyn — Aby uzyskać więcej informacji, zobacz [tokenu odwołania](active-directory-token-and-claims.md#token-revocation). Jedynym sposobem na aplikację, aby sprawdzić, czy token odświeżania jest prawidłowy jest próba Zrealizuj go, wykonując żądania tokenu do punktu końcowego v2.0.

Gdy zrealizować token odświeżania, aby uzyskać nowy token dostępu (i jeśli przyznano aplikacji `offline_access` zakresu), zostanie wyświetlony nowy token odświeżania w odpowiedzi tokenu. Zapisz token odświeżania nowo wystawione, aby zastąpić ten, który zostanie użyty w żądaniu. Gwarantuje to, że tokenów odświeżania ważność tak długo, jak to możliwe.

## <a name="validating-tokens"></a>Sprawdzanie poprawności tokenów
Obecnie tylko token weryfikacji, należy potrzebnych do wykonania dla aplikacji jest sprawdzanie poprawności tokenów identyfikator. Aby sprawdzić poprawność Identyfikatora tokenu, aplikacji powinni sprawdzać zarówno token Identyfikatora podpisu i oświadczenia w tokenie identyfikator.

<!-- TODO: Link -->
Firma Microsoft udostępnia biblioteki i przykłady kodu, których opisano, jak łatwo obsługi tokenu weryfikacji. W kolejnych sekcjach opisano proces podstawowej. Kilka bibliotek open source innych firm również są dostępne do weryfikacji tokenu JWT. Ma co najmniej jedną bibliotekę opcji dla prawie wszystkich platform i języków.

### <a name="validate-the-signature"></a>Sprawdzanie poprawności podpisu
Token JWT zawiera trzy segmentów, które są rozdzielone `.` znaków. Pierwszy segment nosi nazwę *nagłówka*, drugi segment jest *treści*, a trzeci segment jest *podpisu*. Segment podpisu umożliwia sprawdzenie oryginalności token Identyfikatora, dzięki czemu mogą być zaufane przez aplikację.

Tokeny Identyfikatora zalogowano się za pomocą standardowych szyfrowanie asymetryczne algorytmy, takie jak RSA 256. Nagłówek token identyfikator ma informacje o metodzie kluczy i szyfrowania używany do podpisywania tokenu. Na przykład:

```
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "MnC_VZcATfM5pOYiJHMba9goEKY"
}
```

`alg` Oświadczeń wskazuje algorytm używany do podpisywania tokenu. `kid` Oświadczeń wskazuje klucz publiczny, który został użyty do podpisania tokenu.

W dowolnym momencie punktu końcowego v2.0 może podpisać identyfikator tokenu przy użyciu jednej z określonych par kluczy publicznych i prywatnych. Punktu końcowego v2.0 okresowo obraca możliwe zestaw kluczy, więc aplikacja powinna być zapisana automatycznie obsługiwać te zmiany klucza. Uzasadnione częstotliwość, aby wyszukać aktualizacje do kluczy publicznych używane przez punktu końcowego v2.0 jest co 24 godziny.

Można uzyskać dane klucza podpisywania należy dokonać weryfikacji podpisu za pomocą dokumentów metadanych OpenID Connect w lokalizacji:

```
https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration
```

> [!TIP]
> Spróbuj użyć adresu URL w przeglądarce!
>
>

Ten dokument metadanych jest obiekt JSON, który ma kilka części przydatne informacje, takie jak lokalizacja różne punkty końcowe wymagane do uwierzytelniania OpenID Connect. Dokument zawiera również *jwks_uri*, co daje lokalizacji zestawu kluczy publicznych używane do podpisywania tokenów. Dokument JSON, znajdujący się w jwks_uri ma wszystkie informacje klucza publicznego, który jest aktualnie używany. Może używać aplikacja `kid` oświadczeń w nagłówku JWT, aby wybrać, które klucz publiczny w tym dokumencie został użyty do podpisywania tokenu. Następnie wykonuje sprawdzanie poprawności podpisu za pomocą prawidłowy klucz publiczny i algorytm wskazane.

Zostanie wykonana Walidacja podpisu wykracza poza zakres tego dokumentu. Wiele open source biblioteki są dostępne dla pomocy w związku z tym.

### <a name="validate-the-claims"></a>Sprawdzanie poprawności oświadczenia
Gdy aplikacja odbiera token identyfikator podczas logowania użytkownika, jego również wykonywać kilka kontroli względem oświadczenia w tokenie identyfikator. Te obejmują, ale nie są ograniczone do:

* **grupy odbiorców** oświadczeń, aby sprawdzić, czy identyfikator tokenu jest przeznaczona do aplikacji
* **nie wcześniej niż** i **czas wygaśnięcia** oświadczeń, aby sprawdzić, czy nie wygasł token Identyfikatora
* **Wystawca** oświadczeń, aby sprawdzić, czy token został wystawiony do aplikacji przez punktu końcowego v2.0
* **Identyfikator jednorazowy**, jako token powtarzania ograniczenie ataków

Aby uzyskać pełną listę operacji sprawdzania poprawności oświadczenia, które należy wykonać w aplikacji, zobacz [OpenID Connect specyfikacji](http://openid.net/specs/openid-connect-core-1_0.html#IDTokenValidation).

Szczegóły oczekiwanych wartości oświadczenia te są objęte [tokeny Identyfikatora](# ID tokens) sekcji.

## <a name="token-lifetimes"></a>Czasy życia tokenu
Firma Microsoft udostępnia następujące okresy tokenu tylko charakter informacyjny. Informacje mogą ułatwić opracowanie i debugowania aplikacji. Aplikacji nie powinna być zapisana oczekiwać żadnego z tych okresy istnienia pozostaje stała. Token może okresy istnienia i zmieni się w dowolnym momencie.

| Token | Cykl życia | Opis |
| --- | --- | --- |
| Identyfikator tokeny (konta firmowego lub szkolnego) |1 godzina |Tokeny Identyfikatora zwykle są prawidłowe dla 1 godzina. Aplikacja sieci web można użyć ten sam okres istnienia do obsługi własnej sesji użytkownika (zalecane), lub można wybrać okres istnienia całkowicie innej sesji. Jeśli Twoja aplikacja powinna Uzyskaj nowy identyfikator tokenu, musi utworzyć nowego logowania żądanie v2.0 punktu końcowego autoryzacji. Jeśli użytkownik ma prawidłowy sesję z punktem końcowym v2.0, użytkownik nie może być konieczne, aby ponownie wprowadzić swoje poświadczenia. |
| Identyfikator tokeny (konta osobiste) |24 godziny |Identyfikator tokeny dla kont osobistych zwykle są ważne przez 24 godziny. Aplikacja sieci web można użyć ten sam okres istnienia do obsługi własnej sesji użytkownika (zalecane), lub można wybrać okres istnienia całkowicie innej sesji. Jeśli Twoja aplikacja powinna Uzyskaj nowy identyfikator tokenu, musi utworzyć nowego logowania żądanie v2.0 punktu końcowego autoryzacji. Jeśli użytkownik ma prawidłowy sesję z punktem końcowym v2.0, użytkownik nie może być konieczne, aby ponownie wprowadzić swoje poświadczenia. |
| Tokeny dostępu (konta firmowego lub szkolnego) |1 godzina |Wskazane jako część tokenu metadanych odpowiedzi tokenu. |
| Tokeny dostępu (konta osobiste) |1 godzina |Wskazane jako część tokenu metadanych odpowiedzi tokenu. Tokenów dostępu, które są wydawane w imieniu kont osobistych można skonfigurować dla różnych okres istnienia, ale jest typowy 1 godzina. |
| Odśwież tokeny (konta firmowego lub szkolnego) |Do 14 dni |Token odświeżania pojedynczego jest prawidłowa dla maksymalnie 14 dni. Jednak token odświeżania mogą być nieprawidłowe w dowolnym momencie z różnych powodów, więc aplikacji będą nadal próbować użyć token odświeżania, do momentu jej nie powiedzie się lub aplikacja zastępuje go nowy token odświeżania. Token odświeżania również staje się nieprawidłowy, jeśli został 90 dni od momentu użytkownik wprowadził swoje poświadczenia. |
| Odśwież tokeny (konta osobiste) |Maksymalnie 1 rok |Token odświeżania pojedynczego jest prawidłowa dla maksymalnie 1 rok. Jednak token odświeżania mogą być nieprawidłowe w dowolnym momencie z różnych powodów, więc aplikacji będą nadal próbować używać token odświeżania, dopóki nie jest on. |
| Kodach autoryzacji (konta firmowego lub szkolnego) |10 minut |Kodach autoryzacji są celowo krótkim okresie i powinny być natychmiast zrealizowane tokenów dostępu i tokenów odświeżania, po odebraniu tokenów. |
| Kodach autoryzacji (konta osobiste) |5 minut |Kodach autoryzacji są celowo krótkim okresie i powinny być natychmiast zrealizowane tokenów dostępu i tokenów odświeżania, po odebraniu tokenów. Kodach autoryzacji, które są wydawane w imieniu kont osobistych jest przeznaczony do jednorazowego użytku. |
