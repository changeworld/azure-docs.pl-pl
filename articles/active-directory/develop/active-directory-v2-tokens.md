---
title: Azure Active Directory w wersji 2.0 tokenów odwołania | Dokumentacja firmy Microsoft
description: Typy tokenów i oświadczeń wyemitowane przez punktu końcowego v2.0 usługi Azure AD
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
ms.date: 06/22/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 1b0db9ad6d72268ff6074ab2b21026cc04758fbd
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2018
ms.locfileid: "39504196"
---
# <a name="azure-active-directory-v20-tokens-reference"></a>Dokumentacja tokenów w wersji 2.0 w usłudze Azure Active Directory
Punktu końcowego v2.0 usługi Azure Active Directory (Azure AD) emituje kilka rodzajów tokenów zabezpieczających w każdym [przepływ uwierzytelniania](active-directory-v2-flows.md). Ta dokumentacja w tym artykule opisano format, zabezpieczenia właściwości i zawartość każdego typu tokenu.

> [!NOTE]
> Nie obsługuje punktu końcowego v2.0, wszystkie scenariusze usługi Azure Active Directory i funkcje. Aby ustalić, czy należy używać punktu końcowego v2.0, przeczytaj temat [ograniczenia v2.0](active-directory-v2-limitations.md).
>
>

## <a name="types-of-tokens"></a>Typy tokenów
Punkt końcowy v2.0 obsługuje [Protokół autoryzacji OAuth 2.0](active-directory-v2-protocols.md), który wykorzystuje tokeny dostępu i tokenów odświeżania. Punkt końcowy v2.0 obsługuje również uwierzytelnianie i logowanie za pomocą [OpenID Connect](active-directory-v2-protocols.md). OpenID Connect wprowadza trzeci typ tokenu, identyfikator tokenu. Każdy z tych tokenów jest reprezentowany jako *elementu nośnego* tokenu.

Token elementu nośnego jest tokenem zabezpieczającym uproszczone, która udziela dostępu elementu nośnego do chronionego zasobu. Elementu nośnego jest każda strona, która może powodować tokenu. Mimo że strona musi uwierzytelniać się z usługą Azure AD, aby otrzymać token elementu nośnego, jeśli kroki nie zostaną podjęte w celu zabezpieczenia tokenu podczas transmisji i przechowywania, można przechwycony i używane przez niezamierzone innych firm. Niektóre tokeny zabezpieczające mają wbudowany mechanizm, aby uniemożliwić ich użycie przez osoby nieupoważnione, ale tokenów elementu nośnego nie obsługują. Musi być transportowane tokenów elementu nośnego bezpiecznego kanału, takie jak transport layer security (HTTPS). Jeśli token elementu nośnego, są przesyłane bez zabezpieczeń tego typu, złośliwa strona wystarczą "atak typu man-in--middle" do uzyskania tokenu i użyć jej do nieautoryzowanego dostępu do chronionego zasobu. Te same zasady zabezpieczeń stosowane, gdy przechowywania lub buforowanie tokenów elementu nośnego do późniejszego użycia. Zawsze upewnij się, że aplikacja bezpiecznie przesyła i przechowuje tokenów elementu nośnego. Aby uzyskać więcej zagadnienia dotyczące zabezpieczeń tokenów elementu nośnego, zobacz [RFC 6750 sekcji 5](http://tools.ietf.org/html/rfc6750).

Wiele tokeny wystawione przez punkt końcowy v2.0 są implementowane jako tokenów sieci Web JSON (tokenów Jwt). Token JWT jest sposób compact, bezpieczny adres URL do przekazywania informacji między dwiema stronami. Informacje przedstawione w token JWT jest nazywany *oświadczenia*. Jest potwierdzenie informacji dotyczących elementu nośnego i podmiotu tokenu. Oświadczenia w token JWT są obiektami JavaScript Object Notation (JSON), które kodowania i serializacji do przesłania. Ponieważ tokenów Jwt wystawione przez punkt końcowy v2.0 jest podpisany, ale nie są szyfrowane, można łatwo sprawdzić zawartość token JWT na potrzeby debugowania. Aby uzyskać więcej informacji na temat tokenów Jwt, zobacz [specyfikacji JWT](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

### <a name="id-tokens"></a>Tokeny Identyfikatora
Identyfikator tokenu jest formą logowania w tokenie zabezpieczającym aplikacja odbiera podczas przeprowadzania uwierzytelniania za pomocą [OpenID Connect](active-directory-v2-protocols.md). Identyfikator tokeny są reprezentowane jako [tokenów Jwt](#types-of-tokens), i mogą zawierać oświadczenia, które można użyć do logowania użytkownika do aplikacji. Można użyć oświadczenia w tokenie identyfikator na różne sposoby. Zazwyczaj Administratorzy korzystają tokeny Identyfikatora, aby wyświetlić informacje o koncie lub podejmowania decyzji dotyczących kontroli dostępu w aplikacji. Punkt końcowy v2.0 wystawia tylko jeden typ tokenu Identyfikacyjnego, która ma spójny zestaw oświadczeń, bez względu na typ użytkownika, który jest zalogowany. Format i zawartość tokenów, identyfikator są takie same dla osobistych użytkowników kont Microsoft i kont służbowych.

Identyfikator tokeny są obecnie podpisana, ale nie jest szyfrowana. Gdy aplikacja otrzymuje identyfikator tokenu, musi ona [zweryfikować podpisu](#validating-tokens) potwierdzenia autentyczności tokenu i weryfikować kilka oświadczenia w tokenie, aby udowodnić, że jego ważności. Oświadczenia zweryfikowany przez aplikację różnią się w zależności od wymagań scenariusza, ale aplikacja musi wykonać niektóre [typowych roszczenie walidacji](#validating-tokens) w każdym scenariuszu.

Udostępniamy pełne szczegóły na temat oświadczenia w tokeny Identyfikatora w następujących sekcjach, oprócz tokenu Identyfikacyjnego próbki. Należy pamiętać, że oświadczenia w tokeny Identyfikatora nie są zwracane w określonej kolejności. Ponadto nowe oświadczenia mogą zostać wprowadzone do tokeny Identyfikatora, w dowolnym momencie. Aplikacja nie powinna Przerwij, gdy wprowadzono nowe oświadczenia. Poniższa lista zawiera oświadczenia, które aplikacja aktualnie niezawodne stanie zinterpretować. Można znaleźć więcej szczegółów w [specyfikacją z OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html).

#### <a name="sample-id-token"></a>Przykładowy identyfikator tokenu
```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiI2NzMxZGU3Ni0xNGE2LTQ5YWUtOTdiYy02ZWJhNjkxNDM5MWUiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vYjk0MTk4MTgtMDlhZi00OWMyLWIwYzMtNjUzYWRjMWYzNzZlL3YyLjAiLCJpYXQiOjE0NTIyODUzMzEsIm5iZiI6MTQ1MjI4NTMzMSwiZXhwIjoxNDUyMjg5MjMxLCJuYW1lIjoiQmFiZSBSdXRoIiwibm9uY2UiOiIxMjM0NSIsIm9pZCI6ImExZGJkZGU4LWU0ZjktNDU3MS1hZDkzLTMwNTllMzc1MGQyMyIsInByZWZlcnJlZF91c2VybmFtZSI6InRoZWdyZWF0YmFtYmlub0BueXkub25taWNyb3NvZnQuY29tIiwic3ViIjoiTUY0Zi1nZ1dNRWppMTJLeW5KVU5RWnBoYVVUdkxjUXVnNWpkRjJubDAxUSIsInRpZCI6ImI5NDE5ODE4LTA5YWYtNDljMi1iMGMzLTY1M2FkYzFmMzc2ZSIsInZlciI6IjIuMCJ9.p_rYdrtJ1oCmgDBggNHB9O38KTnLCMGbMDODdirdmZbmJcTHiZDdtTc-hguu3krhbtOsoYM2HJeZM3Wsbp_YcfSKDY--X_NobMNsxbT7bqZHxDnA2jTMyrmt5v2EKUnEeVtSiJXyO3JWUq9R0dO-m4o9_8jGP6zHtR62zLaotTBYHmgeKpZgTFB9WtUq8DVdyMn_HSvQEfz-LWqckbcTwM_9RNKoGRVk38KChVJo4z5LkksYRarDo8QgQ7xEKmYmPvRr_I7gvM2bmlZQds2OeqWLB1NSNbFZqyFOCgYn3bAQ-nEQSKwBaA36jYGPOVG2r2Qv1uKcpSOxzxaQybzYpQ
```

> [!TIP]
> Praktyki, aby przeprowadzić inspekcję oświadczenia w tokenie identyfikator próbki, wklej przykładowy identyfikator tokenu do [jwt.ms](http://jwt.ms/).
>
>

#### <a name="claims-in-id-tokens"></a>Oświadczenia w tokeny Identyfikatora
| Name (Nazwa) | Claim | Przykładowa wartość | Opis |
| --- | --- | --- | --- |
| Grupy odbiorców |`aud` |`6731de76-14a6-49ae-97bc-6eba6914391e` |Identyfikuje zamierzonym odbiorcą tokenu. W tokenach identyfikator odbiorców jest identyfikator aplikacji, przypisany do aplikacji w portalu rejestracji aplikacji firmy Microsoft. Aplikację należy sprawdzić tę wartość i odrzucenie tokenu, jeśli wartość nie jest zgodny. |
| Wystawcy |`iss` |`https://login.microsoftonline.com/b9419818-09af-49c2-b0c3-653adc1f376e/v2.0 ` |Określa usługę tokenu zabezpieczającego (STS), który tworzy i zwraca token i dzierżawy usługi Azure AD, w którym użytkownik został uwierzytelniony. Aplikację należy zweryfikować Oświadczenie wystawcy, aby upewnić się, że token pochodzi od punktu końcowego v2.0. Ponadto powinna korzystać część stanowiącą identyfikator GUID oświadczenia ograniczyć zestaw dzierżawców, którzy mogą zalogować się do aplikacji. Identyfikator GUID, który wskazuje, czy użytkownik jest użytkownikiem odbiorcy z konta Microsoft jest `9188040d-6c67-4c5b-b112-36a304b66dad`. |
| Wydane w |`iat` |`1452285331` |Czas, w którym token został wystawiony, jest reprezentowana w czasie uniksowym. |
| Czas wygaśnięcia |`exp` |`1452289231` |Czas, w której token staje się nieprawidłowy, jest reprezentowana w czasie uniksowym. Twoja aplikacja powinna używać tego oświadczenia próba sprawdzania prawidłowości okres istnienia tokenu. |
| Nie wcześniej niż |`nbf` |`1452285331` |Czas, w którym token staje się nieprawidłowy, jest reprezentowana w czasie uniksowym. Zazwyczaj przyczyną jest taka sama jak podczas wystawiania. Twoja aplikacja powinna używać tego oświadczenia próba sprawdzania prawidłowości okres istnienia tokenu. |
| wersja |`ver` |`2.0` |Wersja tokenu Identyfikacyjnego, zgodnie z definicją w usłudze Azure AD. W przypadku punktu końcowego v2.0 wartością jest `2.0`. |
| Identyfikator dzierżawy |`tid` |`b9419818-09af-49c2-b0c3-653adc1f376e` |Identyfikator GUID, który reprezentuje dzierżawy usługi Azure AD, do której należy użytkownik. Dla kont służbowych identyfikator GUID jest identyfikator dzierżawy niezmienne organizacji, do której należy użytkownik. Dla osobistych kont, wartość jest `9188040d-6c67-4c5b-b112-36a304b66dad`. `profile` Zakres jest wymagany w celu odbierania tego oświadczenia. |
| Kod skrótu |`c_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |Skrót kod znajduje się w tokeny Identyfikatora, tylko wtedy, gdy wystawiono tokenu Identyfikacyjnego z kodu autoryzacji OAuth 2.0. Może służyć do weryfikowania autentyczności kodu autoryzacji. Aby uzyskać szczegółowe informacje dotyczące przeprowadzania tej weryfikacji, zobacz [specyfikacją z OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html). |
| Skrót tokenu dostępu |`at_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |Skrót tokenu znajduje się w identyfikatorze dostęp tokenów, tylko, gdy tokenu Identyfikacyjnego wystawione przy użyciu tokenu dostępu OAuth 2.0. Może służyć do weryfikowania autentyczności tokenu dostępu. Aby uzyskać szczegółowe informacje dotyczące przeprowadzania tej weryfikacji, zobacz [specyfikacją z OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html). |
| Identyfikator jednorazowy |`nonce` |`12345` |Identyfikator jednorazowy jest strategię złagodzić skutki ewentualnych ataków powtarzania tokenu. Aplikację można określić identyfikatora jednorazowego w żądaniu autoryzacji przy użyciu `nonce` parametr zapytania. Wartości podane w żądaniu jest emitowane w tokenu Identyfikacyjnego `nonce` oświadczenia w niezmienionej postaci. Aplikację można sprawdzić wartość względem wartości, które on określony w żądaniu, co umożliwi skojarzenie sesji aplikacji przy użyciu określonego tokenu Identyfikatora. Aplikację należy wykonywać tej weryfikacji w procesie weryfikacji tokenu Identyfikatora. |
| name |`name` |`Babe Ruth` |Oświadczenie nazwy zawiera zrozumiałą wartość, która identyfikuje podmiotu tokenu. Wartość nie musi być unikatowy, jest ona modyfikowalna i został zaprojektowany tak, ma być używany tylko w celach wyświetlania. `profile` Zakres jest wymagany w celu odbierania tego oświadczenia. |
| e-mail |`email` |`thegreatbambino@nyy.onmicrosoft.com` |Adres podstawowy adres e-mail skojarzony z konta użytkownika, jeśli taka istnieje. Jego wartość jest modyfikowalna i mogą ulec zmianie wraz z upływem czasu. `email` Zakres jest wymagany w celu odbierania tego oświadczenia. |
| Preferowany nazwy użytkownika |`preferred_username` |`thegreatbambino@nyy.onmicrosoft.com` |Nazwa głównej reprezentuje użytkownika w punkcie końcowym v2.0. Może on być adres e-mail, numer telefonu lub ogólny nazwy użytkownika bez określonego formatu. Jego wartość jest modyfikowalna i mogą ulec zmianie wraz z upływem czasu. Ponieważ jest ona modyfikowalna, ta wartość nie należy do podejmowania decyzji dotyczących autoryzacji. `profile` Zakres jest wymagany w celu odbierania tego oświadczenia. |
| Temat |`sub` |`MF4f-ggWMEji12KynJUNQZphaUTvLcQug5jdF2nl01Q` | Podmiot zabezpieczeń o tym, które token określa informacje, takie jak użytkownika aplikacji. Ta wartość jest niezmienny i nie może być ponownie przypisywany ani ponownie. Ona może służyć do sprawdzania autoryzacji bezpiecznie, np. gdy token jest używany do uzyskania dostępu do zasobu i może być używany jako klucz w tabelach bazy danych. Ponieważ podmiot jest zawsze obecne w tokenach problemy dotyczące usługi Azure AD, zalecamy używanie tej wartości w systemie autoryzacji ogólnego przeznaczenia. Temat jest jednak identyfikator pairwise — Unikatowy identyfikator określonej aplikacji. W związku z tym jeśli jeden użytkownik zaloguje się do dwóch różnych aplikacji przy użyciu dwóch identyfikatorów innego klienta, aplikacji, które otrzyma dwóch różnych wartości oświadczenia podmiotu. To może być lub może nie być wskazane w zależności od wymagań dotyczących architektury i ochrony prywatności. |
| Identyfikator obiektu: |`oid` |`a1dbdde8-e4f9-4571-ad93-3059e3750d23` | Niemodyfikowalny identyfikator obiektu w Microsoft systemu tożsamości, w tym przypadku konta użytkownika. Może również służyć do sprawdzania autoryzacji i bezpiecznie jako klucz w tabelach bazy danych. Ten identyfikator unikatowo identyfikuje użytkownika w aplikacjach — dwóch różnych aplikacji, rejestrowanie w ten sam użytkownik otrzyma taką samą wartość w `oid` oświadczenia. Oznacza to, że mogą być używane podczas tworzenia kwerend do usług Microsoft online services, takich jak program Microsoft Graph. Program Microsoft Graph zwróci ten identyfikator jako `id` właściwość dla danego konta użytkownika. Ponieważ `oid` wielu aplikacjom do skorelowania użytkowników, `profile` zakres jest wymagany w celu odbierania tego oświadczenia. Należy pamiętać, że jeden użytkownik istnieje w wielu dzierżawach, użytkownik będzie zawierać identyfikator inny obiekt, w każdej dzierżawy — są traktowane jako różne konta, nawet jeśli użytkownik loguje się do każdego konta przy użyciu tych samych poświadczeń. |

### <a name="access-tokens"></a>Tokeny dostępu

Punktu końcowego v2.0 aplikacjom innych firm, które są zarejestrowane w usłudze Azure AD do wystawiania tokenów dostępu do zabezpieczonych zasobów, takich jak interfejsy API sieci Web. Aby uzyskać więcej informacji na temat konfigurowania aplikacji do wystawiania tokenów dostępu, zobacz [jak zarejestrować aplikację za pośrednictwem punktu końcowego v2.0](quickstart-v2-register-an-app.md). Podczas rejestracji aplikacji z punktem końcowym v2.0, deweloper może określić poziom dostępu, o nazwie **zakresy**, na których dostęp mogą zostać wystawione tokeny. Na przykład **calendars.read** zakresem zdefiniowanym w interfejsie API programu Graph Microsoft przyznaje uprawnienia do odczytu użytkownika kalendarza. Gdy aplikacja odbiera token dostępu z punktu końcowego v2.0, należy sprawdzić podpis tokenu, wystawcy, odbiorców, czas wygaśnięcia i innych oświadczeń, zależnie od scenariusza. 

W przypadku żądania tokenu dostępu z punktu końcowego v2.0, punktu końcowego v2.0 również zwraca metadane dotyczące tokenu dostępu dla aplikacji do użycia. Informacje te obejmują czas wygaśnięcia tokenu dostępu i zakresy, dla których jest on prawidłowy. Aplikacja wykorzystuje te metadane, aby wykonać inteligentne buforowanie tokenów dostępu bez konieczności przeanalizować Otwórz tokenu dostępu, sam.

### <a name="refresh-tokens"></a>Tokenów odświeżania
Odśwież tokeny są tokeny zabezpieczające, które Twoja aplikacja może użyć w celu uzyskania nowych tokenów dostępu w przepływ OAuth 2.0. Aplikację można użyć tokenów odświeżania do osiągnięcia długoterminowe dostęp do zasobów w imieniu użytkownika bez konieczności interakcji z użytkownikiem.

Tokeny odświeżania są wielu zasobów. Token odświeżania Odebrane żądania tokenu dla jednego zasobu można zrealizować dla tokenów dostępu do zupełnie innego zasobu.

Aby otrzymywać odświeżenia tokenu odpowiedzi, aplikacja musi żądać i otrzymać `offline_access` zakresu. Aby dowiedzieć się więcej na temat `offline_access` zakresu, zobacz [wyrażania zgody i zakresy](active-directory-v2-scopes.md) artykułu.

Tokenów odświeżania są i zawsze będą, całkowicie nieprzezroczysty do swojej aplikacji. One są wydawane przez punktu końcowego v2.0 usługi Azure AD można tylko inspekcji i interpretowane przez punktu końcowego v2.0. Są one długotrwałe, ale aplikacja nie powinna być zapisana można oczekiwać, że token odświeżania jest ważny dla dowolnego okresu czasu. Tokeny odświeżania może zostać unieważnione w każdej chwili z różnych przyczyn — Aby uzyskać szczegółowe informacje, zobacz [token odwołania](active-directory-token-and-claims.md#token-revocation). Jedynym sposobem dla swojej aplikacji dowiedzieć się, czy token odświeżania jest prawidłowa jest próba Zrealizuj go, wprowadzając żądania tokenu do punktu końcowego v2.0.

Po zrealizowaniu token odświeżania, aby uzyskać nowy token dostępu (i jeśli aplikacja została udzielona `offline_access` zakres), masz dostęp do nowego tokena odświeżania na odpowiedzi tokenu. Zapisz token odświeżania nowo wystawione, aby zastąpić ten, który zostanie użyty w żądaniu. Jest to gwarancją tokenów odświeżania i ważność tak długo, jak to możliwe.

## <a name="validating-tokens"></a>Sprawdzanie poprawności tokenów
Obecnie tylko tokenu weryfikacji, należy potrzebnych do wykonania dla aplikacji jest sprawdzanie poprawności tokenów Identyfikatora. Można zweryfikować tokenu Identyfikacyjnego, aplikację należy zweryfikować podpisu tokenu Identyfikacyjnego i oświadczenia w tokenie identyfikator.

<!-- TODO: Link --> Firma Microsoft udostępnia biblioteki i przykłady kodu, które pokazują, jak i w łatwy sposób spełniaj walidacji tokenów. W następnych sekcjach opisano bazowego procesu. Kilka bibliotek typu open-source innych firm również są dostępne do weryfikacji tokenów JWT. Istnieje co najmniej jedna biblioteka dotycząca prawie każdej platformy i języka.

### <a name="validate-the-signature"></a>Weryfikowanie podpisu
Token JWT zawiera trzy segmenty, które są oddzielone `.` znaków. Pierwszy segment jest znany jako *nagłówka*, drugi segment jest *treści*, a trzeci segmentu *podpisu*. Segment podpisu może służyć do weryfikowania autentyczności tokenu Identyfikacyjnego, dzięki czemu mogą być zaufane przez aplikację.

Identyfikator tokeny są podpisane za pomocą standardowych asymetrycznych algorytmów, takich jak RSA 256. Nagłówek tokenu Identyfikacyjnego zawiera informacje o metodzie klucza i szyfrowania używany do podpisywania tokenu. Na przykład:

```
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "MnC_VZcATfM5pOYiJHMba9goEKY"
}
```

`alg` Oświadczeń Określa algorytm, który był używany do podpisywania tokenu. `kid` Oświadczenia wskazuje klucz publiczny, który był używany do podpisywania tokenu.

Punktu końcowego v2.0 podpisuje tokenach dostępu i identyfikator przy użyciu jednej z określonego zestawu pary kluczy publiczny prywatny. Punktu końcowego v2.0 okresowo obraca możliwe zestawu kluczy, więc aplikacji mają być zapisywane automatycznie obsługiwać zmiany klucza. Uzasadnione częstotliwość, aby sprawdzić, czy są aktualizacje kluczy publicznych używane przez punktem końcowym v2.0 jest co 24 godziny.

Możesz uzyskać podpisywania danych klucza, który chcesz zweryfikować podpisu, za pomocą protokołu OpenID Connect dokumentów metadanych znajdujący się w:

```
https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration
```

> [!TIP]
> Spróbuj użyć adresu URL w przeglądarce!

Ten dokument metadanych jest obiekt JSON, który ma kilka fragmentów przydatne informacje, takie jak lokalizacja różnych punktów końcowych wymaganych do uwierzytelniania OpenID Connect. Dokument obejmuje również *jwks_uri*, co daje lokalizacji zestawu kluczy publicznych, używany do podpisywania tokenów. Dokument JSON, znajdujący się w jwks_uri ma wszystkie dane klucza publicznego, który jest obecnie w użyciu. Twoja aplikacja może używać `kid` oświadczenia w nagłówku JWT, aby wybrać, które klucza publicznego, w tym dokumencie został użyty do podpisywania tokenu. Następnie wykonuje sprawdzenie podpisu przy użyciu prawidłowy klucz publiczny i wskazany algorytmu.

> [!NOTE]
> `x5t` Oświadczeń jest przestarzała w punkcie końcowym v2.0. Firma Microsoft zaleca używanie `kid` oświadczeń można zweryfikować tokenu.

Wykonywanie weryfikacji podpisu wykracza poza zakres tego dokumentu. Wiele bibliotek typu open source są dostępne do udzielenia odpowiedzi na to.

### <a name="validate-the-claims"></a>Sprawdzanie poprawności oświadczenia
Gdy aplikacja otrzymuje tokenu Identyfikacyjnego podczas logowania użytkownika, go również wykonać kilka kontroli względem oświadczenia w tokenie identyfikator. Obejmują one nie są ograniczone do:

* **odbiorcy** oświadczenia, aby sprawdzić, czy identyfikator tokenu jest przeznaczona do aplikacji
* **nie wcześniej niż** i **czas wygaśnięcia** oświadczeń, aby sprawdzić, czy nie wygasł token Identyfikatora
* **Wystawca** oświadczenia, aby sprawdzić, czy token został wystawiony do aplikacji przez punkt końcowy v2.0
* **Identyfikator jednorazowy**, jako token oparte na metodzie powtórzeń ograniczaniu ryzyka ataków

Aby uzyskać pełną listę sprawdzanie poprawności oświadczenia, które należy wykonać aplikację, zobacz [specyfikacją z OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html#IDTokenValidation).

Szczegółowe informacje o oczekiwanej wartości te oświadczenia są objęte [tokeny Identyfikatora](# ID tokens) sekcji.

## <a name="token-lifetimes"></a>Okresy istnienia tokenu
Firma Microsoft oferuje następujące okresów istnienia tokenu wyłącznie w celach informacyjnych. Informacje mogą pomóc podczas tworzenia i debugowania aplikacji. Aplikacje nie powinien być zapisywany można oczekiwać, żadnego z tych okresy istnienia, aby pozostał bez zmian. Token może okresy istnienia i zmieni się w dowolnym momencie.

| Token | Okres istnienia | Opis |
| --- | --- | --- |
| Tokeny Identyfikatora (konta służbowego) |1 godzina |Tokeny Identyfikatora zwykle są ważne przez 1 godzinę. Aplikacji sieci web można użyć tego samego okres istnienia do obsługi własnej sesji użytkownika (zalecane) lub możesz wybrać okres istnienia całkowicie innej sesji. Jeśli aplikacja wymaga uzyskać token nowego Identyfikatora, musi wprowadzić nowe żądanie logowania do wersji 2.0 punktu końcowego autoryzacji. Jeśli użytkownik ma prawidłową sesję z punktem końcowym v2.0, użytkownik może nie być konieczne ponownie wprowadzić swoje poświadczenia. |
| Tokeny Identyfikatora (konta osobistego) |24 godziny |Tokeny Identyfikatora dla osobistych kont zwykle są ważne przez 24 godziny. Aplikacji sieci web można użyć tego samego okres istnienia do obsługi własnej sesji użytkownika (zalecane) lub możesz wybrać okres istnienia całkowicie innej sesji. Jeśli aplikacja wymaga uzyskać token nowego Identyfikatora, musi wprowadzić nowe żądanie logowania do wersji 2.0 punktu końcowego autoryzacji. Jeśli użytkownik ma prawidłową sesję z punktem końcowym v2.0, użytkownik może nie być konieczne ponownie wprowadzić swoje poświadczenia. |
| Tokeny dostępu (konta służbowego) |1 godzina |Wskazane w odpowiedzi z tokenem, jako część tokenu metadanych. |
| Tokeny dostępu (konta osobistego) |1 godzina |Wskazane w odpowiedzi z tokenem, jako część tokenu metadanych. Tokeny dostępu, które są wydawane w imieniu kont osobistych można skonfigurować inny okres istnienia, ale typowe jest 1 godzinę. |
| Odświeżanie tokenów (konto służbowe) |Maksymalnie 14 dni |Token odświeżania pojedynczego nadaje się do maksymalnie 14 dni. Jednak token odświeżania mogą być nieprawidłowe w dowolnym momencie z różnych powodów, dzięki czemu aplikacja powinna w dalszym ciągu spróbuj użyć tokenu odświeżania, dopóki nie zakończy się niepowodzeniem lub aplikacja zastępuje go znakiem nowego tokena odświeżania. Token odświeżania również staje się nieprawidłowy, jeśli upłynęło 90 dni od użytkownik wprowadził swoje poświadczenia. |
| Odświeżanie tokenów (konta osobistego) |Do 1 rok |Token odświeżania pojedynczego nadaje się do więcej niż 1 rok. Jednak token odświeżania mogą być nieprawidłowe w dowolnym momencie z różnych powodów, dzięki czemu aplikacja powinna w dalszym ciągu spróbuj użyć tokenu odświeżania, dopóki nie zakończy się niepowodzeniem. |
| Kody autoryzacji (konta służbowego) |10 minut |Kody autoryzacji są celowo krótkotrwałe i należy natychmiast skorzystać tokenów dostępu i tokeny odświeżania po odebraniu tokenów. |
| Kody autoryzacji (konta osobistego) |5 minut |Kody autoryzacji są celowo krótkotrwałe i należy natychmiast skorzystać tokenów dostępu i tokeny odświeżania po odebraniu tokenów. Kody autoryzacji, które są wydawane w imieniu konta osobiste są przeznaczony do jednorazowego użytku. |
