---
title: Omówienie tokenów — usługa Azure Active Directory B2C
description: Dowiedz się więcej o tokenach używanych w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/27/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cbbd083a6b62733d71c316af95dffaa188b28955
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186492"
---
# <a name="overview-of-tokens-in-azure-active-directory-b2c"></a>Omówienie tokenów w usłudze Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Usługa Azure Active Directory B2C (Azure AD B2C) emituje kilka typów tokenów zabezpieczających podczas przetwarzania każdego [przepływu uwierzytelniania.](application-types.md) W tym dokumencie opisano format, właściwości zabezpieczeń i zawartość każdego typu tokenu.

## <a name="token-types"></a>Typy tokenów

Usługa Azure AD B2C obsługuje [protokoły OAuth 2.0 i OpenID Connect](protocols-overview.md), które wykorzystują tokeny do uwierzytelniania i bezpiecznego dostępu do zasobów. Wszystkie tokeny używane w usłudze Azure AD B2C są [tokenami internetowymi JSON (JWTs),](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) które zawierają potwierdzenia informacji o nośniku i temacie tokenu.

Następujące tokeny są używane w komunikacji z usługą Azure AD B2C:

- *Token identyfikatora* — JWT, który zawiera oświadczenia, których można użyć do identyfikowania użytkowników w aplikacji. Ten token jest bezpiecznie wysyłany w żądaniach HTTP do komunikacji między dwoma składnikami tej samej aplikacji lub usługi. Oświadczenia można użyć w tokenie identyfikatora, jak na to zamieścisz. Są one powszechnie używane do wyświetlania informacji o koncie lub do podejmowania decyzji kontroli dostępu w aplikacji. Tokeny identyfikatorów są podpisane, ale nie są szyfrowane. Gdy aplikacja lub interfejs API odbiera token identyfikatora, musi sprawdzić poprawność podpisu, aby udowodnić, że token jest autentyczny. Aplikacja lub interfejs API musi również sprawdzić poprawność kilku oświadczeń w tokenie, aby udowodnić, że jest prawidłowy. W zależności od wymagań scenariusza oświadczenia zweryfikowane przez aplikację mogą się różnić, ale aplikacja musi wykonać niektóre typowe sprawdzanie poprawności oświadczeń w każdym scenariuszu.
- *Token dostępu* — JWT, który zawiera oświadczenia, których można użyć do zidentyfikowania przyznanych uprawnień do interfejsów API. Tokeny dostępu są podpisane, ale nie są szyfrowane. Tokeny dostępu są używane do zapewnienia dostępu do interfejsów API i serwerów zasobów.  Gdy interfejs API odbiera token dostępu, musi sprawdzić poprawność podpisu, aby udowodnić, że token jest autentyczny. Interfejs API musi również sprawdzić poprawność kilku oświadczeń w tokenie, aby udowodnić, że jest prawidłowy. W zależności od wymagań scenariusza oświadczenia zweryfikowane przez aplikację mogą się różnić, ale aplikacja musi wykonać niektóre typowe sprawdzanie poprawności oświadczeń w każdym scenariuszu.
- *Token odświeżania* — tokeny odświeżania są używane do uzyskiwania nowych tokenów identyfikatorów i uzyskiwania dostępu do tokenów w przepływie OAuth 2.0. Zapewniają one aplikacji długoterminowy dostęp do zasobów w imieniu użytkowników bez konieczności interakcji z tymi użytkownikami. Tokeny odświeżania są nieprzezroczyste dla aplikacji. Są one wydawane przez usługę Azure AD B2C i mogą być kontrolowane i interpretowane tylko przez usługę Azure AD B2C. Są one długotrwałe, ale aplikacja nie powinna być zapisywana z oczekiwaniem, że token odświeżania będzie trwać przez określony czas. Tokeny odświeżania mogą zostać unieważnione w dowolnym momencie z różnych powodów. Jedynym sposobem, aby aplikacja wiedziała, czy token odświeżania jest prawidłowy, jest próba jego zrealizowania przez złożenie żądania tokenu do usługi Azure AD B2C. Po zrealizowaniu tokenu odświeżania dla nowego tokenu otrzymasz nowy token odświeżania w odpowiedzi tokenu. Zapisz nowy token odświeżania. Zastępuje token odświeżania, który był wcześniej używany w żądaniu. Ta akcja pomaga zagwarantować, że tokeny odświeżania pozostają ważne tak długo, jak to możliwe.

## <a name="endpoints"></a>Punkty końcowe

[Zarejestrowana aplikacja](tutorial-register-applications.md) odbiera tokeny i komunikuje się z usługą Azure AD B2C, wysyłając żądania do tych punktów końcowych:

- `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/authorize`
- `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/token`

Tokeny zabezpieczające, które aplikacja otrzymuje z usługi `/authorize` Azure `/token` AD B2C może pochodzić z lub punktów końcowych. Gdy tokeny identyfikatorów są `/authorize` pobierane z punktu końcowego, odbywa się to przy użyciu [przepływu niejawnego,](implicit-flow-single-page-application.md)który jest często używany dla użytkowników logujących się do aplikacji internetowych opartych na języku JavaScript. Gdy tokeny identyfikatora są `/token` pobierane z punktu końcowego, odbywa się to przy użyciu [przepływu kodu autoryzacji,](openid-connect.md#get-a-token)który utrzymuje token ukryty w przeglądarce.

## <a name="claims"></a>Oświadczenia

Korzystając z usługi Azure AD B2C, masz szczegółową kontrolę nad zawartością tokenów. Można skonfigurować [przepływy użytkowników](user-flow-overview.md) i [zasady niestandardowe,](custom-policy-overview.md) aby wysyłać określone zestawy danych użytkownika w oświadczeń, które są wymagane dla aplikacji. Oświadczenia te mogą obejmować standardowe właściwości, takie jak **displayName** i **emailAddress**. Aplikacje mogą używać tych oświadczeń do bezpiecznego uwierzytelniania użytkowników i żądań.

Oświadczenia w tokenach identyfikatorów nie są zwracane w żadnej określonej kolejności. Nowe oświadczenia mogą być wprowadzane w tokenach identyfikatorów w dowolnym momencie. Aplikacja nie powinna się zepsuć, ponieważ zostaną wprowadzone nowe oświadczenia. W zgłoszeniach można również uwzględnić [niestandardowe atrybuty użytkownika.](user-flow-custom-attributes.md)

W poniższej tabeli wymieniono oświadczenia, których można oczekiwać w tokenach identyfikatorów i tokenach dostępu wystawionych przez usługę Azure AD B2C.

| Nazwa | Claim | Przykładowa wartość | Opis |
| ---- | ----- | ------------- | ----------- |
| Grupy odbiorców | `aud` | `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` | Identyfikuje zamierzonego odbiorcę tokenu. W przypadku usługi Azure AD B2C odbiorcą jest identyfikator aplikacji. Aplikacja powinna zweryfikować tę wartość i odrzucić token, jeśli nie jest zgodny. Publiczność jest synonimem zasobów. |
| Wystawca | `iss` |`https://{tenant}.b2clogin.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` | Identyfikuje usługę tokenu zabezpieczającego (STS), która konstruuje i zwraca token. Identyfikuje również katalog, w którym użytkownik został uwierzytelniony. Aplikacja powinna sprawdzić poprawność oświadczenia wystawcy, aby upewnić się, że token pochodzi z odpowiedniego punktu końcowego. |
| Wydane w | `iat` | `1438535543` | Czas, w którym token został wystawiony, reprezentowany w czasie epoki. |
| Czas wygaśnięcia | `exp` | `1438539443` | Czas, w którym token staje się nieprawidłowy, reprezentowany w czasie epoki. Aplikacja powinna używać tego oświadczenia, aby zweryfikować ważność okresu istnienia tokenu. |
| Nie wcześniej niż | `nbf` | `1438535543` | Czas, w którym token staje się prawidłowy, reprezentowany w czasie epoki. Ten czas jest zwykle taki sam, jak czas token został wydany. Aplikacja powinna używać tego oświadczenia, aby zweryfikować ważność okresu istnienia tokenu. |
| Wersja | `ver` | `1.0` | Wersja tokenu identyfikatora, zgodnie z definicją usługi Azure AD B2C. |
| Skrót kodu | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Skrót kodu zawarty w tokenie identyfikatora tylko wtedy, gdy token jest wystawiany wraz z kodem autoryzacji OAuth 2.0. Skrót kodu może służyć do sprawdzania poprawności autentyczności kodu autoryzacji. Aby uzyskać więcej informacji na temat sposobu sprawdzania poprawności, zobacz [specyfikację OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html).  |
| Skrót tokenu dostępu | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Skrót tokenu dostępu zawarte w tokenie identyfikatora tylko wtedy, gdy token jest wystawiany wraz z tokenem dostępu OAuth 2.0. Skrót tokenu dostępu może służyć do sprawdzania poprawności autentyczności tokenu dostępu. Aby uzyskać więcej informacji na temat sposobu sprawdzania poprawności, zobacz [specyfikację OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html)  |
| Nonce (niem. | `nonce` | `12345` | Nonce to strategia używana do łagodzenia ataków powtarzania tokenów. Aplikacja może określić wartość nonce w `nonce` żądaniu autoryzacji przy użyciu parametru kwerendy. Wartość poda, że w żądaniu jest `nonce` emitowana bez modyfikacji w żądaniu tokenu identyfikatora tylko. To oświadczenie umożliwia aplikacji zweryfikować wartość względem wartości określonej w żądaniu. Aplikacja powinna wykonać tę weryfikację podczas procesu sprawdzania poprawności tokenu identyfikatora. |
| Podmiot | `sub` | `884408e1-2918-4cz0-b12d-3aa027d7563b` | Podmiot zabezpieczeń, o którym token potwierdza informacje, takie jak użytkownik aplikacji. Ta wartość jest niezmienna i nie można jej ponownie przypisać ani ponownie. Może służyć do bezpiecznego wykonywania kontroli autoryzacji, na przykład gdy token jest używany do uzyskiwania dostępu do zasobu. Domyślnie oświadczenie podmiotu jest wypełniane identyfikatorem obiektu użytkownika w katalogu. |
| Odwołanie do klasy kontekstu uwierzytelniania | `acr` | Nie dotyczy | Używane tylko ze starszymi zasadami. |
| Polityka ramowa zaufania | `tfp` | `b2c_1_signupsignin1` | Nazwa zasad, który został użyty do uzyskania tokenu identyfikatora. |
| Czas uwierzytelniania | `auth_time` | `1438535543` | Czas, w którym użytkownik ostatnio wprowadził poświadczenia, reprezentowane w czasie epoki. Nie ma żadnej dyskryminacji między tym uwierzytelnianiem jest świeże logowanie, sesji logowania jednokrotnego lub innego typu logowania. Jest `auth_time` to ostatni raz, gdy aplikacja (lub użytkownik) zainicjowała próbę uwierzytelnienia dla usługi Azure AD B2C. Metoda używana do uwierzytelniania nie jest zróżnicowana. |
| Zakres | `scp` | `Read`| Uprawnienia przyznane zasobowi dla tokenu dostępu. Wiele przyznanych uprawnień są oddzielone spacją. |
| Strona autoryzowana | `azp` | `975251ed-e4f5-4efd-abcb-5f1a8f566ab7` | **Identyfikator aplikacji** klienckiej, która zainicjowała żądanie. |

## <a name="configuration"></a>Konfigurowanie

Następujące właściwości są używane do [zarządzania okresami istnienia tokenów zabezpieczających emitowanych](configure-tokens.md) przez usługę Azure AD B2C:

- **Dostęp & okres istnienia tokenu identyfikatora (w minutach)** — okres istnienia tokenu nośnika OAuth 2.0 używanego do uzyskania dostępu do chronionego zasobu. Wartość domyślna to 60 minut. Minimalna (włącznie) wynosi 5 minut. Maksymalna (włącznie) wynosi 1440 minut.

- **Okres istnienia tokenu odświeżania (dni)** — maksymalny okres, przed którym token odświeżania może służyć do uzyskania nowego dostępu lub tokenu identyfikatora. Okres obejmuje również pobieranie nowego tokenu odświeżania, jeśli `offline_access` aplikacja została przyznana zakres. Wartość domyślna to 14 dni. Minimalna (włącznie) wynosi jeden dzień. Maksymalna wartość (włącznie) wynosi 90 dni.

- **Okres istnienia okna przesuwnego tokenu odświeżania (dni)** — po upływie tego okresu użytkownik jest zmuszony do ponownego uwierzytelnienia, niezależnie od okresu ważności ostatniego tokenu odświeżania nabytego przez aplikację. Można go podać tylko wtedy, gdy przełącznik jest ustawiony na **Ograniczony**. Musi być większa lub równa **wartość okresu istnienia tokenu odświeżania (dni).** Jeśli przełącznik jest ustawiony na **Niezwiązany,** nie można podać określonej wartości. Wartość domyślna to 90 dni. Minimalna (włącznie) wynosi jeden dzień. Maksymalna wartość (włącznie) wynosi 365 dni.

Przy użyciu tych właściwości włączono następujące przypadki użycia:

- Zezwalaj użytkownikowi na pozostawanie zalogowanym do aplikacji mobilnej przez czas nieokreślony, o ile użytkownik jest stale aktywny w aplikacji. Okres **istnienia okna przesuwnego tokenu odświeżania (dni)** można ustawić na **Niezwiązany** w przepływie użytkownika logowania.
- Spełnij wymagania twojej branży dotyczące zabezpieczeń i zgodności, ustawiając odpowiednie okresy istnienia tokenów dostępu.

Te ustawienia nie są dostępne dla przepływów użytkownika resetowania hasła.

## <a name="compatibility"></a>Zgodność

Następujące właściwości są używane do [zarządzania zgodnością tokenów:](configure-tokens.md)

- **Oświadczenie wystawcy (iss)** — ta właściwość identyfikuje dzierżawę usługi Azure AD B2C, która wystawiła token. Wartością domyślną jest `https://<domain>/{B2C tenant GUID}/v2.0/`. Wartość `https://<domain>/tfp/{B2C tenant GUID}/{Policy ID}/v2.0/` zawiera identyfikatory dla dzierżawy usługi Azure AD B2C i przepływu użytkownika, który został użyty w żądaniu tokenu. Jeśli twoja aplikacja lub biblioteka wymaga usługi Azure AD B2C, aby być zgodne ze [specyfikacją OpenID Connect Discovery 1.0,](https://openid.net/specs/openid-connect-discovery-1_0.html)użyj tej wartości.

- **Podmiot (sub) oświadczenie** — ta właściwość identyfikuje jednostkę, dla której token potwierdza informacje. Wartością domyślną jest **ObjectID**, `sub` który wypełnia oświadczenie w tokenie identyfikatorem obiektu użytkownika. Wartość **Nie obsługiwane** jest tylko dla zgodności z powrotem. Zaleca się, aby przełączyć się do **ObjectID** tak szybko, jak to możliwe.

- **Oświadczenie reprezentujące identyfikator zasad** — ta właściwość identyfikuje typ oświadczenia, do którego wypełniana jest nazwa zasad używana w żądaniu tokenu. Wartością domyślną jest `tfp`. Wartość `acr` jest dostępna tylko dla zgodności z powrotem.

## <a name="pass-through"></a>Przekazywanie

Po uruchomieniu podróży użytkownika usługa Azure AD B2C odbiera token dostępu od dostawcy tożsamości. Usługa Azure AD B2C używa tego tokenu do pobierania informacji o użytkowniku. Włącz [oświadczenie w przepływie użytkownika](idp-pass-through-user-flow.md) lub [zdefiniować oświadczenie w zasadach niestandardowych,](idp-pass-through-custom.md) aby przekazać token do aplikacji, które można zarejestrować w usłudze Azure AD B2C. Aplikacja musi używać [przepływu użytkownika w wersji 2,](user-flow-versions.md) aby skorzystać z przekazywania tokenu jako oświadczenia.

Usługa Azure AD B2C obecnie obsługuje tylko przekazywanie tokenu dostępu dostawców tożsamości OAuth 2.0, które obejmują Facebook i Google. Dla wszystkich innych dostawców tożsamości oświadczenie jest zwracane puste.

## <a name="validation"></a>Sprawdzanie poprawności

Aby sprawdzić poprawność tokenu, aplikacja powinna sprawdzić zarówno podpis, jak i oświadczenia tokenu. Wiele bibliotek typu open source jest dostępnych do sprawdzania poprawności jwts, w zależności od preferowanego języka. Zaleca się, aby zbadać te opcje, a nie zaimplementować własną logikę sprawdzania poprawności.

### <a name="validate-signature"></a>Sprawdzanie poprawności podpisu

JWT zawiera trzy segmenty, *nagłówek,* *treść*i *podpis.* Segment podpisu może służyć do sprawdzania poprawności autentyczności tokenu, dzięki czemu można mu zaufać przez aplikację. Tokeny usługi Azure AD B2C są podpisywane przy użyciu standardowych algorytmów szyfrowania asymetrycznego, takich jak RSA 256.

Nagłówek tokenu zawiera informacje o kluczu i metodzie szyfrowania używanej do podpisywania tokenu:

```
{
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

Wartość oświadczenia **alg** jest algorytm, który został użyty do podpisania tokenu. Wartość oświadczenia **dziecka** jest kluczem publicznym, który został użyty do podpisania tokenu. W dowolnym momencie usługi Azure AD B2C można podpisać token przy użyciu jednego z zestawu par kluczy publiczno-prywatnych. Usługa Azure AD B2C okresowo obraca możliwy zestaw kluczy. Aplikacja powinna być zapisywana do obsługi tych kluczowych zmian automatycznie. Rozsądna częstotliwość sprawdzania dostępności aktualizacji kluczy publicznych używanych przez usługę Azure AD B2C jest co 24 godziny.

Usługa Azure AD B2C ma punkt końcowy metadanych OpenID Connect. Za pomocą tego punktu końcowego aplikacje mogą żądać informacji o usłudze Azure AD B2C w czasie wykonywania. Informacje te obejmują punkty końcowe, zawartość tokenu i klucze podpisywania tokenów. Dzierżawa usługi Azure AD B2C zawiera dokument metadanych JSON dla każdej zasady. Dokument metadanych jest obiektem JSON, który zawiera kilka przydatnych informacji. Metadane zawiera **jwks_uri**, który daje lokalizację zestawu kluczy publicznych, które są używane do podpisywania tokenów. Ta lokalizacja jest tutaj dostępna, ale najlepiej jest dynamicznie pobierać lokalizację przy użyciu dokumentu metadanych i analizowania **jwks_uri:**

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_signupsignin1
```
Dokument JSON znajdujący się pod tym adresem URL zawiera wszystkie informacje o kluczu publicznym używane w danym momencie. Aplikacja może użyć `kid` oświadczenia w nagłówku JWT, aby wybrać klucz publiczny w dokumencie JSON, który jest używany do podpisywania określonego tokenu. Następnie można wykonać sprawdzanie poprawności podpisu przy użyciu poprawnego klucza publicznego i wskazanego algorytmu.

Dokument metadanych `B2C_1_signupsignin1` dla zasad `contoso.onmicrosoft.com` w dzierżawie znajduje się pod adresem:

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_signupsignin1
```

Aby określić, które zasady zostały użyte do podpisania tokenu (i gdzie można poprosić o metadane), masz dwie opcje. Po pierwsze nazwa zasad jest `acr` uwzględniona w żądaniu w tokenie. Można przeanalizować oświadczenia z treści JWT przez base-64 dekodowania treści i deserializacji ciąg JSON, który powoduje. Oświadczenie `acr` jest nazwą zasad, który został użyty do wystawienia tokenu. Inną opcją jest zakodowanie zasad w `state` wartości parametru podczas wystawiania żądania, a następnie dekodowanie go w celu określenia, które zasady zostały użyte. Każda z tych metod jest prawidłowa.

Opis sposobu sprawdzania poprawności podpisu wykracza poza zakres tego dokumentu. Wiele bibliotek typu open source jest dostępnych, aby pomóc w weryfikacji tokenu.

### <a name="validate-claims"></a>Sprawdzanie poprawności oświadczeń

Gdy aplikacje lub interfejs API odbiera token identyfikatora, należy również wykonać kilka kontroli oświadczeń w tokenie identyfikatora. Należy sprawdzić następujące oświadczenia:

- **odbiorcy** — sprawdza, czy token identyfikatora miał być podany do aplikacji.
- **nie przed** i **czas wygaśnięcia** — sprawdza, czy token identyfikatora nie wygasł.
- **wystawca** — sprawdza, czy token został wystawiony do aplikacji przez usługę Azure AD B2C.
- **nonce** - strategia łagodzenia ataku powtarzania tokenów.

Aby uzyskać pełną listę weryfikacji, które aplikacja powinna wykonać, zobacz [specyfikację OpenID Connect](https://openid.net).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [używaniu tokenów dostępu](access-tokens.md).

