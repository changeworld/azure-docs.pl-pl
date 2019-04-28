---
title: Zabezpieczanie aplikacji jednostronicowej przy użyciu niejawny przepływ platforma tożsamości firmy Microsoft | Azure
description: Tworzenie aplikacji sieci web przy użyciu implementacji platformy tożsamości Microsoft niejawny przepływ dla aplikacji jednej strony.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 3605931f-dc24-4910-bb50-5375defec6a8
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d517828b30629cd9dfba5459b1d90913d8bc4f77
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62112152"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Platforma tożsamości firmy Microsoft i niejawne udzielić przepływu

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Z punktem końcowym platforma tożsamości firmy Microsoft możesz zarejestrować użytkowników do aplikacji jednostronicowej przy użyciu zarówno osobistych i kont służbowych od firmy Microsoft. Jednej strony i inne aplikacje JavaScript Uruchom przede wszystkim jest w fazie przeglądarki kilka interesujących wyzwań, jeśli chodzi o uwierzytelniania:

* Właściwości zabezpieczeń te aplikacje są znacznie różnią się od tradycyjnych oparte na serwerze sieci web aplikacji.
* Wiele serwerów autoryzacji i dostawców tożsamości nie obsługują żądania CORS.
* Przekierowuje przeglądarki na pełnej stronie od aplikacji stają się szczególnie inwazyjne do środowiska użytkownika.

W przypadku tych aplikacji (AngularJS, Ember.js, React.js i tak dalej) Platforma tożsamości usługi Microsoft obsługuje przepływ przyznawanie niejawne protokołu OAuth 2.0. Niejawny przepływ jest opisana w [OAuth 2.0 specyfikacji](https://tools.ietf.org/html/rfc6749#section-4.2). Jego główną Korzyścią płynącą jest możliwość aplikację, aby uzyskać tokenów z platformą Microsoft identity bez przeprowadzania serwera wewnętrznej bazy danych programu exchange poświadczeń. Umożliwia to aplikacji, loguje użytkownika, zachować sesję i uzyskiwanie tokenów do innych interfejsów API sieci web w klienta kodu JavaScript. Istnieje kilka ważne zagadnienia dotyczące zabezpieczeń można wziąć pod uwagę podczas korzystania z niejawnym przepływem specjalnie około [klienta](https://tools.ietf.org/html/rfc6749#section-10.3) i [personifikacji użytkownika](https://tools.ietf.org/html/rfc6749#section-10.3).

Jeśli chcesz dodać uwierzytelnianie do aplikacji JavaScript za pomocą niejawny przepływ i platformą Microsoft identity, zalecamy użycie biblioteki JavaScript typu open source, [msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js).

Jednak jeśli nie chcesz używać biblioteki w aplikacji jednostronicowej i wysyłać wiadomości protokołu samodzielnie, należy wykonać poniższe ogólne kroki.

> [!NOTE]
> Nie wszystkie scenariusze usługi Azure Active Directory (Azure AD) i funkcje są obsługiwane przez punkt końcowy platforma tożsamości firmy Microsoft. Aby ustalić, należy użyć programu Microsoft platformy tożsamości z punktu końcowego, przeczytaj temat [ograniczenia dotyczące programu Microsoft identity platformy](active-directory-v2-limitations.md).

## <a name="protocol-diagram"></a>Diagram protokołu

Na poniższym diagramie przedstawiono, jak wygląda całego niejawny przepływ logowania, a w kolejnych sekcjach opisano każdy krok bardziej szczegółowo.

![OpenID Connect torów](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>Wyślij żądanie logowania

Początkowo utworzyć użytkownika w swojej aplikacji, możesz wysłać [OpenID Connect](v2-protocols-oidc.md) żądań uwierzytelnienia i zyskaj `id_token` z punktu końcowego platformy tożsamości firmy Microsoft.

> [!IMPORTANT]
> Aby pomyślnie zażądać identyfikator tokenu rejestracji aplikacji w [witryna Azure portal — rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) strony musi mieć włączone poprawnie, wybierając przepływ przyznawanie niejawne **tokeny dostępu** i **Tokeny Identyfikatora** w obszarze **przyznawanie niejawne** sekcji. Jeśli nie jest włączona, `unsupported_response` zostanie zwrócony błąd: **Podana wartość parametru wejściowego "response_type" jest niedozwolone dla tego klienta. Oczekiwana wartość to "code"**

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=openid
&response_mode=fragment
&state=12345
&nonce=678910
```

> [!TIP]
> Aby przetestować logowanie się przy użyciu niejawnego przepływu, kliknij pozycję <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank"> https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a> Po zarejestrowaniu się w przeglądarce powinno zostać przekierowane do `https://localhost/myapp/` z `id_token` na pasku adresu.
>

| Parametr |  | Opis |
| --- | --- | --- |
| `tenant` | wymagane |`{tenant}` Wartość w polu Ścieżka żądania może służyć do kontrolowania, kto może zalogować się do aplikacji. Dozwolone wartości to `common`, `organizations`, `consumers`i identyfikatorów dzierżawy. Aby uzyskać więcej informacji, zobacz [protokołu podstawy](active-directory-v2-protocols.md#endpoints). |
| `client_id` | wymagane | Identyfikator aplikacji (klienta) [witryna Azure portal — rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) strony przypisany do aplikacji. |
| `response_type` | wymagane |Musi zawierać `id_token` dla logowania OpenID Connect. Może również obejmować response_type `token`. Za pomocą `token` w tym miejscu pozwoli aplikację do odbierania token dostępu bezpośrednio z punktu końcowego autoryzacji bez konieczności wykonywania drugie żądanie do punktu końcowego autoryzacji. Jeśli używasz `token` elementu response_type, `scope` parametr musi zawierać wskazujący zasobu, który można wystawić tokenu dla zakresu. |
| `redirect_uri` | Zalecane |Redirect_uri aplikacji, gdzie odpowiedzi uwierzytelniania mogą być wysyłane i odbierane przez aplikację. Dokładnie musi odpowiadać jednej z redirect_uris, zarejestrowanych w portalu, z wyjątkiem musi być zakodowane w adresie url. |
| `scope` | wymagane |Listę rozdzielonych spacjami [zakresy](v2-permissions-and-consent.md). Dla protokołu OpenID Connect, musi on zawierać zakres `openid`, co przekłada się na uprawnienia "Logowanie się w" w zgody interfejsu użytkownika. Opcjonalnie możesz również chcieć dołączyć `email` lub `profile` zakresy do uzyskania dostępu do danych dodatkowych użytkowników. W tym żądaniu do żądania zgody z różnymi zasobami, może również obejmować inne zakresy. |
| `response_mode` | opcjonalne |Określa metodę, które mają być używane do wysyłania wynikowy token wstecz do swojej aplikacji. Wartość domyślna to zapytania dla tokenu dostępu, ale fragmentu, jeśli żądanie zawiera element id_token. |
| `state` | Zalecane |Wartość uwzględnione w żądaniu, które również zostaną zwrócone w odpowiedzi tokenu. Może być ciągiem żadnej zawartości, który chcesz. Losowo generowany unikatową wartość jest zwykle używany podczas [zapobieganie atakom na fałszerstwo żądania międzywitrynowego](https://tools.ietf.org/html/rfc6749#section-10.12). Stan również jest używany do kodowania informacje o stanie użytkownika w aplikacji, zanim żądanie uwierzytelniania wystąpił, takich jak strony lub widoku, które znajdowały się w. |
| `nonce` | wymagane |Wartości zawarte w żądaniu wygenerowane przez aplikację, która zostanie uwzględniona w wynikowej id_token jako oświadczenia. Aplikacja może zweryfikować tę wartość, aby uniknąć powtarzania tokenu ataków. Wartość jest zazwyczaj losowego, unikatowy ciąg, który może służyć do identyfikowania pochodzenia żądania. Wymagane tylko w przypadku elementu id_token jest wymagane. |
| `prompt` | opcjonalne |Wskazuje typ interakcji z użytkownikiem, który jest wymagany. Jedyne prawidłowe wartości w tym momencie są "login", "none", "select_account" i "". `prompt=login` Spowoduje to wymuszenie użytkownika o wprowadzenie poświadczeń w tym żądaniu Negacja logowania jednokrotnego. `prompt=none` jest przeciwieństwem - zapewni, że użytkownik nie jest wyświetlone wszystkie interaktywne monity w inny sposób. Jeśli nie można ukończyć żądania dyskretnie za pomocą logowania jednokrotnego, Microsoft platformy tożsamości z punktu końcowego zwróci błąd. `prompt=select_account` wysyła użytkownika do selektora konta, w którym zostaną wyświetlone wszystkie konta zapamiętanych w sesji. `prompt=consent` wywołuje okno dialogowe ze zgodą OAuth po użytkownik się zaloguje, monitem o nadanie uprawnień do aplikacji. |
| `login_hint`  |opcjonalne |Można wstępnie wypełnić pola Adres e-mail/nazwa użytkownika logowania do strony użytkownika, jeśli znasz swoją nazwę użytkownika, wcześniej. Aplikacje często użyje tego parametru podczas ponownego uwierzytelniania już o wyodrębnić nazwy użytkownika z poprzedniego logowania za pomocą `preferred_username` oświadczenia.|
| `domain_hint` | opcjonalne |Może być jednym z `consumers` lub `organizations`. Jeśli uwzględniony, pominie proces odnajdywania bazujące na poczcie e-mail tego użytkownika przechodzi na stronie rejestracji, co prowadzi do nieco bardziej usprawnione środowisko użytkownika. Aplikacje często będzie tego parametru należy użyć podczas ponownego uwierzytelniania, wyodrębniając `tid` oświadczeń z id_token. Jeśli `tid` oświadczenia, wartość jest `9188040d-6c67-4c5b-b112-36a304b66dad` (Account Microsoft dzierżawcy konsumenta), należy użyć `domain_hint=consumers`. W przeciwnym razie można użyć `domain_hint=organizations` podczas ponownego uwierzytelniania. |

W tym momencie użytkownik będzie monitowany wprowadzić swoje poświadczenia i wykonania uwierzytelnienia. Microsoft platformy tożsamości z punktu końcowego będą upewnij się również, że użytkownik wyraził zgodę na uprawnienia czcionką `scope` parametr zapytania. Jeśli użytkownik zgodził się **Brak** tych uprawnień, zostanie wyświetlony monit użytkownika o zgodę na wymagane uprawnienia. Aby uzyskać więcej informacji, zobacz [uprawnienia, wyrażania zgody i aplikacje z wieloma dzierżawami](v2-permissions-and-consent.md).

Po użytkownik jest uwierzytelniany i przyznaje zgody, Microsoft platformy tożsamości z punktu końcowego zwróci odpowiedź do aplikacji, na wskazany `redirect_uri`, przy użyciu metody podanej w `response_mode` parametru.

#### <a name="successful-response"></a>Pomyślna odpowiedź

Odpowiedź oznaczająca Powodzenie przy użyciu `response_mode=fragment` i `response_type=id_token+token` wygląda podobnie do następujących (z podziałów wierszy dla czytelności):

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope=https%3a%2f%2fgraph.microsoft.com%2fuser.read 
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Parametr | Opis |
| --- | --- |
| `access_token` |Jeśli uwzględniony `response_type` obejmuje `token`. Token dostępu, którego aplikacja żąda w tym przypadku dla programu Microsoft Graph. Token dostępu nie powinien zdekodowany lub w przeciwnym razie inspekcji, powinny być traktowane jako nieprzezroczysty ciąg. |
| `token_type` |Jeśli uwzględniony `response_type` obejmuje `token`. Zawsze będzie `Bearer`. |
| `expires_in`|Jeśli uwzględniony `response_type` obejmuje `token`. Określa liczbę sekund, przez które token jest prawidłowy, na potrzeby buforowania. |
| `scope` |Jeśli uwzględniony `response_type` obejmuje `token`. Wskazuje zakresy, dla którego access_token będzie nieprawidłowa. Może nie obejmować wszystkich zakresów, wymagane, jeśli nie ma zastosowania do użytkownika (w przypadku platformy Azure zakresy tylko usługi AD żądanego podczas osobiste konto jest używane podczas logowania się). |
| `id_token` | Podpisany JSON Web Token (JWT). Aplikację można dekodować segmentów tego tokenu na żądanie informacji o użytkowniku, który jest zalogowany. Wartości w pamięci podręcznej i ich wyświetlenie aplikacji, ale go nie należy traktować je autoryzacji lub granice zabezpieczeń. Aby uzyskać więcej informacji na temat id_tokens zobacz [ `id_token reference` ](id-tokens.md). <br> **Uwaga:** Tylko wtedy, jeśli podana `openid` zażądano zakresu. |
| `state` |Jeśli parametr Stan jest uwzględniony w żądaniu, tę samą wartość powinna pojawić się w odpowiedzi. Aplikację należy sprawdzić, czy wartości stanu żądania i odpowiedzi są identyczne. |

#### <a name="error-response"></a>Odpowiedzi na błąd

Odpowiedzi na błędy mogą być również wysyłane do `redirect_uri` , dzięki czemu aplikacja może je odpowiednio obsługiwać:

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parametr | Opis |
| --- | --- |
| `error` |Ciągu kodu błędu, który może służyć do klasyfikowania typy błędów, które występują i może służyć do reagowania na błędy. |
| `error_description` |Określony komunikat o błędzie ułatwiający Deweloper Identyfikuj główne przyczyny błędu uwierzytelniania. |

## <a name="validate-the-idtoken"></a>Sprawdź poprawność id_token

Po prostu odbiera id_token jest niewystarczająca do uwierzytelnienia użytkownika; należy również sprawdzić poprawności podpisu id_token i weryfikować oświadczenia w tokenie, w zależności od wymagań aplikacji. Punkt końcowy z platformy Microsoft identity korzysta [tokenów sieci Web JSON (tokenów Jwt)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) i kryptografii klucza publicznego do podpisywania tokenów i sprawdzić, czy są prawidłowe.

Można wybrać sprawdzić poprawność `id_token` w kliencie kod, ale powszechną praktyką jest wysłanie `id_token` do serwera wewnętrznej bazy danych i zweryfikować istnieje. Po zweryfikowaniu podpis id_token, istnieje kilka oświadczenia, które będzie trzeba zweryfikować. Zobacz [ `id_token` odwołania](id-tokens.md) uzyskać więcej informacji, w tym [sprawdzanie poprawności tokenów](id-tokens.md#validating-an-id_token) i [ważne informacje na temat Przerzucanie klucza podpisywania](active-directory-signing-key-rollover.md). Firma Microsoft zaleca korzystające z biblioteki do analizowania i sprawdzanie poprawności tokenów — Brak co najmniej jeden dostępny dla większości platform i języków.

Możesz również sprawdzić dodatkowe oświadczenia w zależności od scenariusza. Niektórych typowych operacji sprawdzania poprawności obejmują:

* Zapewnienie użytkownika/organizacja po zarejestrowaniu w aplikacji.
* Zapewnienie użytkownika ma właściwe autoryzacji/uprawnienia.
* Zapewnienie siły uwierzytelniania wystąpił, takie jak uwierzytelnianie wieloskładnikowe.

Po sprawdzeniu poprawności id_token, można rozpocząć sesji z użytkownikiem i korzystanie z oświadczeń id_token, aby uzyskać informacje o użytkowniku w swojej aplikacji. Te informacje mogą służyć ekranu, rekordy, personalizacji i.

## <a name="get-access-tokens"></a>Uzyskiwanie tokenów dostępu

Teraz, że logujesz się użytkownika do aplikacji jednostronicowej, możesz też uzyskać tokeny dostępu web wywoływania interfejsów API zabezpieczony przez platforma tożsamości firmy Microsoft, takich jak [programu Microsoft Graph](https://developer.microsoft.com/graph). Nawet jeśli masz już tokenu przy użyciu `token` elementu response_type, można użyć tej metody można uzyskać tokenów do dodatkowych zasobów bez konieczności przekieruje użytkownika, aby zalogować się ponownie.

W normalnym przepływie uwierzytelniania OpenID Connect/OAuth, czy w tym celu zgłaszającą żądanie do platformą Microsoft identity `/token` punktu końcowego. Punkt końcowy platforma tożsamości firmy Microsoft nie obsługuje jednak żądań CORPS, więc wykonywanie wywołań AJAX, pobieranie i tokenów odświeżania jest poza pytanie. Zamiast tego służy niejawny przepływ w ukrytym elemencie iframe w celu uzyskania nowych tokenów innych interfejsów API sieci web: 

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read 
&response_mode=fragment
&state=12345&nonce=678910
&prompt=none
&domain_hint=organizations
&login_hint=myuser@mycompany.com
```

Szczegółowe informacje na temat parametrów zapytania w adresie URL, [Wyślij znak w żądaniu](#send-the-sign-in-request).

> [!TIP]
> Spróbuj wykonać kopiowanie i wklejanie poniżej żądania na karcie przeglądarki! (Nie zapomnij zastąpić `login_hint` wartości z poprawną wartość dla użytkownika)
>
>`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2user.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&login_hint=your-username`
>

Dzięki `prompt=none` parametr, to żądanie będzie albo powiedzie się lub od razu zakończyć się niepowodzeniem i powrócić do aplikacji. Odpowiedź oznaczająca Powodzenie zostaną wysłane do Twojej aplikacji, na wskazany `redirect_uri`, przy użyciu metody podanej w `response_mode` parametru.

#### <a name="successful-response"></a>Pomyślna odpowiedź

Odpowiedź oznaczająca Powodzenie przy użyciu `response_mode=fragment` wyglądają następująco:

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read
```

| Parametr | Opis |
| --- | --- |
| `access_token` |Jeśli uwzględniony `response_type` obejmuje `token`. Token dostępu, którego aplikacja żąda w tym przypadku dla programu Microsoft Graph. Token dostępu nie powinien zdekodowany lub w przeciwnym razie inspekcji, powinny być traktowane jako nieprzezroczysty ciąg. |
| `token_type` | Zawsze będzie `Bearer`. |
| `expires_in` | Określa liczbę sekund, przez które token jest prawidłowy, na potrzeby buforowania. |
| `scope` | Wskazuje zakresy, dla którego access_token będzie nieprawidłowa. Może nie obejmować wszystkich zakresów, wymagane, jeśli nie ma zastosowania do użytkownika (w przypadku platformy Azure zakresy tylko usługi AD żądanego podczas osobiste konto jest używane podczas logowania się). |
| `id_token` | Podpisany JSON Web Token (JWT). Jeśli uwzględniony `response_type` obejmuje `id_token`. Aplikację można dekodować segmentów tego tokenu na żądanie informacji o użytkowniku, który jest zalogowany. Wartości w pamięci podręcznej i ich wyświetlenie aplikacji, ale go nie należy traktować je autoryzacji lub granice zabezpieczeń. Aby uzyskać więcej informacji na temat id_tokens zobacz [ `id_token` odwołania](id-tokens.md). <br> **Uwaga:** Tylko wtedy, jeśli podana `openid` zażądano zakresu. |
| `state` |Jeśli parametr Stan jest uwzględniony w żądaniu, tę samą wartość powinna pojawić się w odpowiedzi. Aplikację należy sprawdzić, czy wartości stanu żądania i odpowiedzi są identyczne. |

#### <a name="error-response"></a>Odpowiedzi na błąd

Odpowiedzi na błędy mogą być również wysyłane do `redirect_uri` , dzięki czemu aplikacja może je odpowiednio obsługiwać. W przypadku właściwości `prompt=none`, będzie oczekiwanym błędem:

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parametr | Opis |
| --- | --- |
| `error` |Ciągu kodu błędu, który może służyć do klasyfikowania typy błędów, które występują i może służyć do reagowania na błędy. |
| `error_description` |Określony komunikat o błędzie ułatwiający Deweloper Identyfikuj główne przyczyny błędu uwierzytelniania. |

Jeśli ten błąd jest wyświetlany w żądaniu iframe, użytkownik interakcyjnie zalogować się ponownie do pobrania nowego tokenu. Istnieje możliwość obsługi tego przypadku w sposób, który ma sens dla swojej aplikacji.

## <a name="validating-access-tokens"></a>Sprawdzanie poprawności tokenów dostępu

Po otrzymaniu ' access_token ', upewnij się, można zweryfikować podpisu tokenu, a także następujące oświadczeń. Można również sprawdzić dodatkowe oświadczenia, w oparciu o Twojego scenariusza.

* **odbiorcy** oświadczenia, aby upewnić się, że token jest przeznaczona do aplikacji
* **Wystawca** oświadczenia, aby sprawdzić, czy token został wystawiony do aplikacji przez punkt końcowy platforma tożsamości firmy Microsoft
* **nie wcześniej niż** i **czas wygaśnięcia** oświadczeń, aby sprawdzić, czy token nie wygasł

Aby uzyskać więcej informacji na temat oświadczenia obecne w tokenie dostępu, zobacz [odwołania do tokenu dostępu](access-tokens.md)

## <a name="refreshing-tokens"></a>Odświeżanie tokenów

Przyznawanie niejawne nie zapewnia tokenów odświeżania. Zarówno `id_token`s i `access_token`s wygaśnie po krótkim czasie, więc aplikacja musi być przygotowana do odświeżenia tych tokenów okresowo. Aby odświeżyć albo typ tokenu, można wykonać tego samego żądania ukrytego elementu iframe w powyższej przy użyciu `prompt=none` parametru, aby kontrolować zachowanie platforma tożsamości. Jeśli chcesz otrzymać nowy `id_token`, należy użyć `response_type=id_token` i `scope=openid`, a także `nonce` parametru.

## <a name="send-a-sign-out-request"></a>Wyślij Wyloguj żądania

OpenID Connect `end_session_endpoint` umożliwia aplikacji wysyłanie żądań do punktu końcowego platformy tożsamości firmy Microsoft do zakończenia sesji użytkownika i wyczyszczenie plików cookie, ustaw przez punkt końcowy platforma tożsamości firmy Microsoft. W pełni Podpisz użytkownika z aplikacji sieci web, aplikację należy zakończyć sesję użytkownika (zwykle przez wyczyszczenie pamięci podręcznej tokenu lub usunięcie plików cookie), a następnie przekierować przeglądarkę w celu:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| Parametr |  | Opis |
| --- | --- | --- |
| `tenant` |wymagane |`{tenant}` Wartość w polu Ścieżka żądania może służyć do kontrolowania, kto może zalogować się do aplikacji. Dozwolone wartości to `common`, `organizations`, `consumers`i identyfikatorów dzierżawy. Aby uzyskać więcej informacji, zobacz [protokołu podstawy](active-directory-v2-protocols.md#endpoints). |
| `post_logout_redirect_uri` | Zalecane | Adres URL, który użytkownik powinien nastąpić powrót do po ukończeniu Wyloguj. Ta wartość musi odpowiadać przekierowania URI zarejestrowanej aplikacji. Jeśli nie zostanie uwzględniony, zostanie wyświetlony komunikat ogólny przez punkt końcowy platforma tożsamości firmy Microsoft. |

## <a name="next-steps"></a>Kolejne kroki

* Przejdź przez [przykłady MSAL JS](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples) można rozpocząć kodowanie.
