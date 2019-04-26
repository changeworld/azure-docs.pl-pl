---
title: Platforma tożsamości firmy Microsoft i przepływ kodu autoryzacji w OAuth | Azure
description: Tworzenie aplikacji sieci web przy użyciu implementacji platformy tożsamości firmy Microsoft protokołu uwierzytelniania OAuth 2.0.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: ae1d7d86-7098-468c-aa32-20df0a10ee3d
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
ms.openlocfilehash: 79e0ebce5704e7b61956568f5ebbce6ea6cbc3af
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60299263"
---
# <a name="microsoft-identity-platform-and-oauth-20-authorization-code-flow"></a>Platforma tożsamości firmy Microsoft i przepływ kodu autoryzacji OAuth 2.0

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Przyznawanie kodu autoryzacji OAuth 2.0 może służyć w aplikacjach, które są zainstalowane na urządzeniu w celu uzyskania dostępu do chronionych zasobów, takich jak interfejsy API sieci web. Przy użyciu implementacji platformy tożsamości firmy Microsoft, OAuth 2.0, możesz dodać Zaloguj się i interfejs API dostępu do aplikacji mobilnych i klasycznych. Ten przewodnik jest niezależny od języka i opisano, jak wysyłać i odbierać komunikaty HTTP bez użycia jakichkolwiek [biblioteki uwierzytelniania usługi platformy Azure typu open-source](active-directory-authentication-libraries.md).

> [!NOTE]
> Nie wszystkie scenariusze usługi Azure Active Directory i funkcje są obsługiwane przez punkt końcowy platforma tożsamości firmy Microsoft. Aby ustalić, należy użyć programu Microsoft platformy tożsamości z punktu końcowego, przeczytaj temat [ograniczenia dotyczące programu Microsoft identity platformy](active-directory-v2-limitations.md).

Przepływ kodu autoryzacji OAuth 2.0 opisano w [sekcji 4.1 specyfikacji protokołu OAuth 2.0](https://tools.ietf.org/html/rfc6749). Jest używana do wykonywania uwierzytelniania i autoryzacji w większości typów aplikacji, w tym [aplikacje sieci web](v2-app-types.md#web-apps) i [natywnie zainstalowanych aplikacji](v2-app-types.md#mobile-and-native-apps). Przepływ umożliwia aplikacjom, które można bezpiecznie uzyskać access_tokens, którego można uzyskać dostęp do zasobów zabezpieczonych przez punkt końcowy platforma tożsamości firmy Microsoft.

## <a name="protocol-diagram"></a>Diagram protokołu

Na wysokim poziomie przepływ cały proces uwierzytelniania dla aplikacji natywnych/mobile nieco wyglądają następująco:

![Przepływ kodu autoryzacji OAuth](./media/v2-oauth2-auth-code-flow/convergence-scenarios-native.svg)

## <a name="request-an-authorization-code"></a>Żądanie zwróciło kod autoryzacji

Przepływ kodu autoryzacji zaczyna się od klienta kierowanie użytkowników do `/authorize` punktu końcowego. W tym żądaniu klient wskazuje uprawnienia niezbędne do uzyskania przez użytkownika:

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&state=12345
```

> [!TIP]
> Kliknij poniższy link, aby wykonać tego żądania. Po zarejestrowaniu się w przeglądarce powinno zostać przekierowane do `https://localhost/myapp/` z `code` na pasku adresu.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&state=12345" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| Parametr    | Wymagane/opcjonalne | Opis |
|--------------|-------------|--------------|
| `tenant`    | wymagane    | `{tenant}` Wartość w polu Ścieżka żądania może służyć do kontrolowania, kto może zalogować się do aplikacji. Dozwolone wartości to `common`, `organizations`, `consumers`i identyfikatorów dzierżawy. Aby uzyskać więcej informacji, zobacz [protokołu podstawy](active-directory-v2-protocols.md#endpoints).  |
| `client_id`   | wymagane    | **Identyfikator aplikacji (klienta)** , [rejestracje aplikacji z witryny Azure portal](https://go.microsoft.com/fwlink/?linkid=2083908) środowisko przypisany do aplikacji.  |
| `response_type` | wymagane    | Musi zawierać `code` dla przepływ kodu autoryzacji.       |
| `redirect_uri`  | wymagane | Redirect_uri aplikacji, gdzie odpowiedzi uwierzytelniania mogą być wysyłane i odbierane przez aplikację. Dokładnie musi odpowiadać jednej z redirect_uris, zarejestrowanych w portalu, z wyjątkiem musi być zakodowane w adresie url. W przypadku aplikacji natywnych i mobilne, należy używać wartość domyślną `https://login.microsoftonline.com/common/oauth2/nativeclient`.   |
| `scope`  | wymagane    | Listę rozdzielonych spacjami [zakresy](v2-permissions-and-consent.md) ma użytkownika o zgodę na. |
| `response_mode`   | Zalecane | Określa metodę, które mają być używane do wysyłania wynikowy token wstecz do swojej aplikacji. Może to być jeden z następujących modyfikatorów dostępu:<br/><br/>- `query`<br/>- `fragment`<br/>- `form_post`<br/><br/>`query` zawiera kod jako parametr ciągu zapytania identyfikatora URI przekierowania. W przypadku żądania tokenu Identyfikatora, przy użyciu niejawnego przepływu, nie można użyć `query` określonej [Specyfikacja OpenID](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations). Jeśli masz żądania tylko kod, możesz użyć `query`, `fragment`, lub `form_post`. `form_post` wykonuje WPIS zawierający kod, aby identyfikator URI przekierowania. Aby uzyskać więcej informacji, zobacz [protokołu OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code).  |
| `state`                 | Zalecane | Wartość uwzględnione w żądaniu, które również zostaną zwrócone w odpowiedzi tokenu. Może być ciągiem żadnej zawartości, który chcesz. Losowo generowany unikatową wartość jest zwykle używany podczas [zapobieganie atakom na fałszerstwo żądania międzywitrynowego](https://tools.ietf.org/html/rfc6749#section-10.12). Przed wystąpieniem żądania uwierzytelniania, takich jak strony lub widoku, które znajdowały się w wartości również zakodować informacje o stanie użytkownika w aplikacji. |
| `prompt`  | opcjonalne    | Wskazuje typ interakcji z użytkownikiem, który jest wymagany. Jedyne prawidłowe wartości w tym momencie są `login`, `none`, i `consent`.<br/><br/>- `prompt=login` Spowoduje to wymuszenie użytkownika o wprowadzenie poświadczeń w tym żądaniu Negacja logowania jednokrotnego.<br/>- `prompt=none` jest przeciwieństwem - zapewni, że użytkownik nie jest wyświetlone wszystkie interaktywne monity w inny sposób. Jeśli nie można ukończyć żądania dyskretnie za pomocą logowania jednokrotnego, zwróci Microsoft platformy tożsamości z punktu końcowego `interaction_required` błędu.<br/>- `prompt=consent` wywołuje okno dialogowe ze zgodą OAuth po użytkownik się zaloguje, monitem o nadanie uprawnień do aplikacji. |
| `login_hint`  | opcjonalne    | Można wstępnie wypełnić pola Adres e-mail/nazwy użytkownika strony logowania dla użytkownika, jeśli znasz swoją nazwę użytkownika, wcześniej. Aplikacje często użyje tego parametru podczas ponownego uwierzytelniania już o wyodrębnić nazwy użytkownika z poprzedniego logowania za pomocą `preferred_username` oświadczenia.   |
| `domain_hint`  | opcjonalne    | Może być jednym z `consumers` lub `organizations`.<br/><br/>Jeśli uwzględniony, pominie proces odnajdywania bazujące na poczcie e-mail tego użytkownika przechodzi przez na stronie logowania, co prowadzi do nieco bardziej usprawnione środowisko użytkownika. Aplikacje często będzie tego parametru należy użyć podczas ponownego uwierzytelniania, wyodrębniając `tid` z poprzedniego logowania. Jeśli `tid` oświadczenia, wartość jest `9188040d-6c67-4c5b-b112-36a304b66dad`, należy użyć `domain_hint=consumers`. W przeciwnym razie użyj `domain_hint=organizations`.  |
| `code_challenge_method` | opcjonalne    | Metoda użyta do zakodowania `code_verifier` dla `code_challenge` parametru. Może być jednym z następujących wartości:<br/><br/>- `plain` <br/>- `S256`<br/><br/>Jeśli wykluczone, `code_challenge` przyjęto, że będzie mieć postać zwykłego tekstu, jeśli `code_challenge` jest dołączony. Platforma tożsamości firmy Microsoft obsługuje zarówno `plain` i `S256`. Aby uzyskać więcej informacji, zobacz [PKCE RFC](https://tools.ietf.org/html/rfc7636). |
| `code_challenge`  | opcjonalne | Wykorzystywany do zabezpieczenia przydziałów kod autoryzacji za pomocą klucza dowód kod programem Exchange (PKCE) z klientami. Jeśli wymagane `code_challenge_method` jest dołączony. Aby uzyskać więcej informacji, zobacz [PKCE RFC](https://tools.ietf.org/html/rfc7636). |

W tym momencie użytkownik będzie monitowany wprowadzić swoje poświadczenia i wykonania uwierzytelnienia. Microsoft platformy tożsamości z punktu końcowego będą upewnij się również, że użytkownik wyraził zgodę na uprawnienia czcionką `scope` parametr zapytania. Jeśli użytkownik nie wyraził zgody do dowolnego z tych uprawnień, zostanie wyświetlony monit użytkownika o zgodę na wymagane uprawnienia. Szczegółowe informacje o [uprawnienia, wyrażania zgody i aplikacje z wieloma dzierżawami znajdują się w tym miejscu](v2-permissions-and-consent.md).

Po użytkownik jest uwierzytelniany i przyznaje zgody, Microsoft platformy tożsamości z punktu końcowego zwróci odpowiedź do aplikacji, na wskazany `redirect_uri`, przy użyciu metody podanej w `response_mode` parametru.

#### <a name="successful-response"></a>Pomyślna odpowiedź

Odpowiedź oznaczająca Powodzenie przy użyciu `response_mode=query` wyglądają następująco:

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| Parametr | Opis  |
|-----------|--------------|
| `code` | Authorization_code, który zażądał aplikacji. Aplikacja może używać kodu autoryzacji do żądania tokenu dostępu dla zasobu docelowego. Authorization_codes są krótkotrwałe, zwykle wygasają po upływie około 10 minut. |
| `state` | Jeśli parametr Stan jest uwzględniony w żądaniu, tę samą wartość powinna pojawić się w odpowiedzi. Aplikację należy sprawdzić, czy wartości stanu żądania i odpowiedzi są identyczne. |

#### <a name="error-response"></a>Odpowiedzi na błąd

Odpowiedzi na błędy mogą być również wysyłane do `redirect_uri` , dzięki czemu aplikacja może je odpowiednio obsługiwać:

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parametr | Opis  |
|----------|------------------|
| `error`  | Ciągu kodu błędu, który może służyć do klasyfikowania typy błędów, które występują i może służyć do reagowania na błędy. |
| `error_description` | Określony komunikat o błędzie ułatwiający Deweloper Identyfikuj główne przyczyny błędu uwierzytelniania. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Kody błędów dla błędów punktu końcowego autoryzacji

W poniższej tabeli opisano różne kody błędów, które mogą być zwracane w `error` parametru odpowiedzi na błąd.

| Kod błędu  | Opis    | Akcja klienta   |
|-------------|----------------|-----------------|
| `invalid_request` | Błąd protokołu, np. Brak wymaganego parametru. | Usuń i ponownie prześlij żądanie. Jest to błąd programistyczny, zazwyczaj przechwytywane w początkowej fazie testowania. |
| `unauthorized_client` | Aplikacja kliencka nie ma zezwolenia na żądanie kod autoryzacji. | Ten błąd występuje zazwyczaj, gdy aplikacja kliencka nie jest zarejestrowany w usłudze Azure AD lub nie została dodana do dzierżawy usługi Azure AD przez użytkownika. Aplikacja może monitować użytkownika przy użyciu instrukcji dotyczących instalowania aplikacji i dodanie go do usługi Azure AD. |
| `access_denied`  | Właściciel zasobu odmowa zgody  | Aplikacja kliencka może powiadomić użytkownika, którego nie można kontynuować, chyba że użytkownik wyrazi na to zgody. |
| `unsupported_response_type` | Serwer autoryzacji nie obsługuje typu odpowiedzi w żądaniu. | Usuń i ponownie prześlij żądanie. Jest to błąd programistyczny, zazwyczaj przechwytywane w początkowej fazie testowania.  |
| `server_error`  | Serwer napotkał nieoczekiwany błąd.| Ponów żądanie. Te błędy mogą być wynikiem tymczasowe warunki. Aplikacja kliencka może wyjaśnić użytkownikowi, że odpowiedzi przez punkt końcowy zostanie opóźnione do tymczasowego błędu. |
| `temporarily_unavailable`   | Serwer jest tymczasowo zbyt zajęty, aby obsłużyć żądanie. | Ponów żądanie. Aplikacja kliencka może wyjaśnić użytkownikowi, że odpowiedzi przez punkt końcowy jest opóźnione ze względu na tymczasowy warunek. |
| `invalid_resource`  | Zasób docelowy jest nieprawidłowy, ponieważ nie istnieje, usługa Azure AD nie może okazać się lub nie jest prawidłowo skonfigurowany. | Ten błąd wskazuje, że zasób, jeśli istnieje, nie został skonfigurowany w ramach dzierżawy. Aplikacja może monitować użytkownika przy użyciu instrukcji dotyczących instalowania aplikacji i dodanie go do usługi Azure AD. |
| `login_required` | Zbyt wiele lub nie znaleziono żadnych użytkowników | Klient zażądał dyskretne uwierzytelnianie (`prompt=none`), ale nie można odnaleźć jednego użytkownika. Może to oznaczać, że w sesji lub żaden użytkownik nie są aktywne wielu użytkowników. Uwzględnia to dzierżawy wybrać (na przykład, jeśli istnieją dwa aktywne konta usługi Azure AD i jednego konta Microsoft i `consumers` wybrano dyskretne uwierzytelnianie będzie działać). |
| `interaction_required` | Żądanie wymaga interakcji użytkownika. | Krok dodatkowego uwierzytelniania lub zgoda jest wymagana. Ponów próbę wykonania żądania bez `prompt=none`. |

## <a name="request-an-access-token"></a>Żądanie tokenu dostępu

Po nabyciu authorization_code i ma odpowiednie uprawnienia przyznane przez użytkownika można zrealizować `code` dla `access_token` do żądanego zasobu. W tym celu wysyłanie `POST` limit czasu żądania `/token` punktu końcowego:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```

> [!TIP]
> Spróbuj wykonać tego żądania w narzędziu Postman! (Nie zapomnij zastąpić `code`) [ ![uruchamianie w narzędziu Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

| Parametr  | Wymagane/opcjonalne | Opis     |
|------------|-------------------|----------------|
| `tenant`   | wymagane   | `{tenant}` Wartość w polu Ścieżka żądania może służyć do kontrolowania, kto może zalogować się do aplikacji. Dozwolone wartości to `common`, `organizations`, `consumers`i identyfikatorów dzierżawy. Aby uzyskać więcej informacji, zobacz [protokołu podstawy](active-directory-v2-protocols.md#endpoints).  |
| `client_id` | wymagane  | Identyfikator aplikacji (klienta) [rejestracje aplikacji z witryny Azure portal](https://go.microsoft.com/fwlink/?linkid=2083908) strony przypisany do aplikacji. |
| `grant_type` | wymagane   | Musi być `authorization_code` dla przepływ kodu autoryzacji.   |
| `scope`      | wymagane   | Rozdzielonej spacjami listy zakresów. Zakresy w tej gałęzi musi być równoważna lub być podzbiorem wartości zakresów w pierwszy gałęzi. Jeśli określono w tym żądaniu zakresów obejmują wiele zasobów serwerów, Microsoft platformy tożsamości z punktu końcowego zwróci tokenu do zasobu, określony w zakresie pierwszy. Aby uzyskać bardziej szczegółowy opis zakresów, zobacz [uprawnienia, wyrażania zgody i zakresy](v2-permissions-and-consent.md). |
| `code`          | wymagane  | Authorization_code, uzyskanego w pierwszej gałęzi przepływu. |
| `redirect_uri`  | wymagane  | Taką samą wartość redirect_uri, który został użyty do uzyskania authorization_code. |
| `client_secret` | wymagane dla aplikacji sieci web | Klucz tajny aplikacji, utworzonego w portalu rejestracji aplikacji dla aplikacji. Nie można używać klucza tajnego aplikacji w aplikacji macierzystej, ponieważ client_secrets nie mogą być w niezawodny sposób będą przechowywane na urządzeniach. Jest to wymagane dla aplikacji sieci web i interfejsów API, które mają możliwość bezpiecznie przechowywać wartość client_secret po stronie serwera sieci web.  Klucz tajny klienta musi być zakodowane w adresie URL przed wysłaniem.  |
| `code_verifier` | opcjonalne  | Tym samym wartość parametru code_verifier użytego do uzyskania authorization_code. Wymagane, jeśli PKCE został użyty w żądaniu grant kod autoryzacji. Aby uzyskać więcej informacji, zobacz [PKCE RFC](https://tools.ietf.org/html/rfc7636). |

### <a name="successful-response"></a>Pomyślna odpowiedź

Odpowiedź oznaczająca Powodzenie tokenu będzie wyglądać następująco:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fuser.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```

| Parametr     | Opis   |
|---------------|------------------------------|
| `access_token`  | Token żądanego dostępu. Aplikacja może używać tego tokenu do uwierzytelniania zabezpieczonych zasobów, takich jak interfejs API sieci web.  |
| `token_type`    | Wskazuje typ tokenu. Jedynym typem, który obsługuje usługi Azure AD jest elementu nośnego |
| `expires_in`    | Jak długo token dostępu jest prawidłowy (w sekundach). |
| `scope`         | Access_token nadaje się do zakresów. |
| `refresh_token` | Token odświeżania OAuth 2.0. Aplikacja może używać tego tokenu uzyskiwanie tokenów dostępu dodatkowe, po upływie bieżącego tokenu dostępu. Refresh_tokens są długotrwałe i pozwala zachować dostęp do zasobów przez dłuższy czas. Aby uzyskać więcej szczegółów na odświeżenie tokenu dostępu, zapoznaj się [poniżej](#refresh-the-access-token). <br> **Uwaga:** Tylko wtedy, jeśli podana `offline_access` zażądano zakresu. |
| `id_token`      | Tokenu Web JSON (JWT). Aplikację można dekodować segmentów tego tokenu na żądanie informacji o użytkowniku, który jest zalogowany. Wartości w pamięci podręcznej i ich wyświetlenie aplikacji, ale nie należy polegać na nich autoryzacji lub granice zabezpieczeń. Aby uzyskać więcej informacji na temat id_tokens zobacz [ `id_token reference` ](id-tokens.md). <br> **Uwaga:** Tylko wtedy, jeśli podana `openid` zażądano zakresu. |

### <a name="error-response"></a>Odpowiedzi na błąd

Odpowiedzi na błędy będzie wyglądać następująco:

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parametr         | Opis    |
|-------------------|----------------|
| `error`       | Ciągu kodu błędu, który może służyć do klasyfikowania typy błędów, które występują i może służyć do reagowania na błędy. |
| `error_description` | Określony komunikat o błędzie ułatwiający Deweloper Identyfikuj główne przyczyny błędu uwierzytelniania. |
| `error_codes` | Lista kodów błędów specyficznych dla usługi STS, które mogą pomóc w diagnostyce.  |
| `timestamp`   | Czas, w którym wystąpił błąd. |
| `trace_id`    | Unikatowy identyfikator dla żądania, które mogą pomóc w diagnostyce. |
| `correlation_id` | Unikatowy identyfikator dla żądania, które mogą pomóc w diagnostyce składnikami. |

### <a name="error-codes-for-token-endpoint-errors"></a>Kody błędów dla błędów punkt końcowy tokenu

| Kod błędu         | Opis        | Akcja klienta    |
|--------------------|--------------------|------------------|
| `invalid_request`  | Błąd protokołu, np. Brak wymaganego parametru. | Usuń i ponownie prześlij żądanie   |
| `invalid_grant`    | Kod autoryzacji lub weryfikatora kodu PKCE jest nieprawidłowy lub wygasł. | Wypróbuj nowe żądanie do `/authorize` punktu końcowego i sprawdź, czy parametr wartość parametru code_verifier jest poprawna.  |
| `unauthorized_client` | Uwierzytelniany klient nie jest autoryzowane do używania tego typu przydziału autoryzacji. | Dzieje się tak zazwyczaj, gdy aplikacja kliencka nie jest zarejestrowany w usłudze Azure AD lub nie została dodana do dzierżawy usługi Azure AD przez użytkownika. Aplikacja może monitować użytkownika przy użyciu instrukcji dotyczących instalowania aplikacji i dodanie go do usługi Azure AD. |
| `invalid_client` | Nie można uwierzytelnić klienta.  | Poświadczenia klienta nie są prawidłowe. Aby rozwiązać problem, administrator aplikacji aktualizuje poświadczenia.   |
| `unsupported_grant_type` | Serwer autoryzacji nie obsługuje typu Udziel autoryzacji. | Zmień typ przydział w żądaniu. Tego rodzaju błąd powinien wystąpić tylko podczas programowania i można wykryć w początkowej fazie testowania. |
| `invalid_resource` | Zasób docelowy jest nieprawidłowy, ponieważ nie istnieje, usługa Azure AD nie może okazać się lub nie jest prawidłowo skonfigurowany. | Oznacza to, że zasób, jeśli istnieje, nie został skonfigurowany w ramach dzierżawy. Aplikacja może monitować użytkownika przy użyciu instrukcji dotyczących instalowania aplikacji i dodanie go do usługi Azure AD.  |
| `interaction_required` | Żądanie wymaga interakcji użytkownika. Na przykład krok dodatkowego uwierzytelniania jest wymagany. | Ponów próbę wykonania żądania z tego samego zasobu.  |
| `temporarily_unavailable` | Serwer jest tymczasowo zbyt zajęty, aby obsłużyć żądanie. | Ponów żądanie. Aplikacja kliencka może wyjaśnić użytkownikowi, że odpowiedzi przez punkt końcowy jest opóźnione ze względu na tymczasowy warunek. |

## <a name="use-the-access-token"></a>Użyj tokenu dostępu

Teraz, gdy zostały pomyślnie uzyskano `access_token`, umieszczając go w służy token w żądaniach wysyłanych do interfejsów API sieci Web `Authorization` nagłówka:

> [!TIP]
> Wykonanie tego żądania w narzędziu Postman! (Zastąp `Authorization` nagłówek pierwszego) [ ![uruchamianie w narzędziu Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-access-token"></a>Odświeżenia tokenu dostępu

Access_tokens są krótkie krótkotrwałe i należy je odświeżyć, po ich wygaśnięciu, aby nadal mieć dostęp do zasobów. Możesz to zrobić, przesyłając innego `POST` limit czasu żądania `/token` punktu końcowego, tym razem realizacji `refresh_token` zamiast `code`.  Tokeny odświeżania są prawidłowe dla wszystkich uprawnień, które klient otrzymał już zgoda na — w związku z tym, token odświeżania wydane w żądaniu na potrzeby `scope=mail.read` może służyć do żądania nowy token dostępu dla `scope=api://contoso.com/api/UseResource`.  

Odświeżanie tokenów nie mają określonego okresy istnienia. Zazwyczaj okresy istnienia tokenów odświeżania są stosunkowo długo. Jednak w niektórych przypadkach, tokeny odświeżania wygaśnie, zostaną odwołane lub braku wystarczających uprawnień do żądanej akcji. Twoja aplikacja potrzebuje do oczekują i obsługiwać [błędy zwrócone przez punkt końcowy wystawiania tokenu](#error-codes-for-token-endpoint-errors) poprawnie. 

Mimo że tokeny odświeżania nie są odwoływane, gdy jest używana w celu uzyskania nowych tokenów dostępu, mają być Odrzuć stare token odświeżania. [Specyfikacji protokołu OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-6) jest wyświetlany komunikat: "Serwer autoryzacji może wystawić nowy token odświeżania, w którym należy odrzucić przypadku klient stary token odświeżania i zastąp go nowego tokena odświeżania. Serwer autoryzacji może cofnąć stary token odświeżania po wydaniu nowego tokena odświeżania do klienta."  

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&client_secret=JqQX2PNo9bpM0uEihUPzyrh      // NOTE: Only required for web apps
```

> [!TIP]
> Spróbuj wykonać tego żądania w narzędziu Postman! (Nie zapomnij zastąpić `refresh_token`) [ ![uruchamianie w narzędziu Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)
> 

| Parametr     |                | Opis        |
|---------------|----------------|--------------------|
| `tenant`        | wymagane     | `{tenant}` Wartość w polu Ścieżka żądania może służyć do kontrolowania, kto może zalogować się do aplikacji. Dozwolone wartości to `common`, `organizations`, `consumers`i identyfikatorów dzierżawy. Aby uzyskać więcej informacji, zobacz [protokołu podstawy](active-directory-v2-protocols.md#endpoints).   |
| `client_id`     | wymagane    | **Identyfikator aplikacji (klienta)** , [rejestracje aplikacji z witryny Azure portal](https://go.microsoft.com/fwlink/?linkid=2083908) środowisko przypisany do aplikacji. |
| `grant_type`    | wymagane    | Musi być `refresh_token` dla tej gałęzi przepływ kodu autoryzacji. |
| `scope`         | wymagane    | Rozdzielonej spacjami listy zakresów. Zakresy w tej gałęzi musi być równoważna lub być podzbiorem wartości zakresów w oryginalnym gałęzi żądania authorization_code wymagane. Jeśli określono w tym żądaniu zakresów obejmują wiele zasobów serwerów, Microsoft platformy tożsamości z punktu końcowego zwróci tokenu do zasobu, określony w zakresie pierwszy. Aby uzyskać bardziej szczegółowy opis zakresów, zobacz [uprawnienia, wyrażania zgody i zakresy](v2-permissions-and-consent.md). |
| `refresh_token` | wymagane    | Refresh_token, uzyskanego w drugim nogi przepływ. |
| `client_secret` | wymagane dla aplikacji sieci web | Klucz tajny aplikacji, utworzonego w portalu rejestracji aplikacji dla aplikacji. Nie należy można użyć w aplikacji macierzystej, ponieważ client_secrets nie mogą być w niezawodny sposób będą przechowywane na urządzeniach. Jest to wymagane dla aplikacji sieci web i interfejsów API, które mają możliwość bezpiecznie przechowywać wartość client_secret po stronie serwera sieci web. |

#### <a name="successful-response"></a>Pomyślna odpowiedź

Odpowiedź oznaczająca Powodzenie tokenu będzie wyglądać następująco:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fuser.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| Parametr     | Opis         |
|---------------|-------------------------------------------------------------|
| `access_token`  | Token żądanego dostępu. Aplikacja może używać tego tokenu do uwierzytelniania zabezpieczonych zasobów, takich jak interfejs API sieci web. |
| `token_type`    | Wskazuje typ tokenu. Jedynym typem, który obsługuje usługi Azure AD jest elementu nośnego |
| `expires_in`    | Jak długo token dostępu jest prawidłowy (w sekundach).   |
| `scope`         | Access_token nadaje się do zakresów.    |
| `refresh_token` | Nowy token odświeżania protokołu OAuth 2.0. Z tym tokenem nowo pobranych odświeżania, aby upewnić się, że tokenów odświeżania ważność tak długo, jak to możliwe, należy zastąpić stary token odświeżania. <br> **Uwaga:** Tylko wtedy, jeśli podana `offline_access` zażądano zakresu.|
| `id_token`      | Niepodpisane JSON Web Token (JWT). Aplikację można dekodować segmentów tego tokenu na żądanie informacji o użytkowniku, który jest zalogowany. Wartości w pamięci podręcznej i ich wyświetlenie aplikacji, ale nie należy polegać na nich autoryzacji lub granice zabezpieczeń. Aby uzyskać więcej informacji na temat id_tokens zobacz [ `id_token reference` ](id-tokens.md). <br> **Uwaga:** Tylko wtedy, jeśli podana `openid` zażądano zakresu. |

#### <a name="error-response"></a>Odpowiedzi na błąd

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parametr         | Opis                                                                                        |
|-------------------|----------------------------------------------------------------------------------------------------|
| `error`           | Ciągu kodu błędu, który może służyć do klasyfikowania typy błędów, które występują i może służyć do reagowania na błędy. |
| `error_description` | Określony komunikat o błędzie ułatwiający Deweloper Identyfikuj główne przyczyny błędu uwierzytelniania.           |
| `error_codes` |Lista kodów błędów specyficznych dla usługi STS, które mogą pomóc w diagnostyce. |
| `timestamp` | Czas, w którym wystąpił błąd. |
| `trace_id` | Unikatowy identyfikator dla żądania, które mogą pomóc w diagnostyce. |
| `correlation_id` | Unikatowy identyfikator dla żądania, które mogą pomóc w diagnostyce składnikami. |

Opis kodów błędów i działań opartej na zalecanym kliencie, zobacz [kody błędów dla błędów punktu końcowego tokenu](#error-codes-for-token-endpoint-errors).
