---
title: Autoryzowanie dostępu do aplikacji sieci Web za pomocą OpenID Connect Connect & usługi Azure AD | Microsoft Docs
description: W tym artykule opisano sposób korzystania z komunikatów HTTP w celu autoryzowania dostępu do aplikacji sieci Web i interfejsów API sieci Web w dzierżawie przy użyciu Azure Active Directory i OpenID Connect Connect.
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 29142f7e-d862-4076-9a1a-ecae5bcd9d9b
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 6eb71bb4fdaa602dcbce5d351c261493647f3caa
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76700808"
---
# <a name="authorize-access-to-web-applications-using-openid-connect-and-azure-active-directory"></a>Autoryzuj dostęp do aplikacji sieci Web za pomocą OpenID Connect Connect i Azure Active Directory

[OpenID Connect Connect](https://openid.net/specs/openid-connect-core-1_0.html) to prosta warstwa tożsamości oparta na protokole OAuth 2,0. Uwierzytelnianie OAuth 2,0 definiuje mechanizmy uzyskiwania i używania [**tokenów dostępu**](access-tokens.md) w celu uzyskiwania dostępu do chronionych zasobów, ale nie definiują standardowych metod zapewniających informacje o tożsamości. OpenID Connect Connect implementuje uwierzytelnianie jako rozszerzenie procesu autoryzacji OAuth 2,0. Zawiera informacje o użytkowniku końcowym w formie [`id_token`](id-tokens.md) , która weryfikuje tożsamość użytkownika i zawiera podstawowe informacje o profilu użytkownika.

OpenID Connect Connect to nasze zalecenie, jeśli tworzysz aplikację sieci Web, która jest hostowana na serwerze i uzyskuje dostęp za pośrednictwem przeglądarki.

[!INCLUDE [active-directory-protocols-getting-started](../../../includes/active-directory-protocols-getting-started.md)] 

## <a name="authentication-flow-using-openid-connect"></a>Przepływ uwierzytelniania w przypadku protokołu OpenID Connect

Najbardziej podstawowy przepływ logowania zawiera następujące kroki — każda z nich jest szczegółowo opisana poniżej.

![Przepływ uwierzytelniania OpenID Connect Connect](./media/v1-protocols-openid-connect-code/active-directory-oauth-code-flow-web-app.png)

## <a name="openid-connect-metadata-document"></a>Dokument metadanych OpenID Connect Connect

OpenID Connect Connect opisuje dokument metadanych zawierający większość informacji wymaganych do przeprowadzenia logowania przez aplikację. Obejmuje to takie informacje, jak adresy URL, które mają być używane, oraz lokalizację publicznych kluczy podpisywania usługi. Dokument metadanych OpenID Connect Connect można znaleźć pod adresem:

```
https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration
```
Metadane są prostym dokumentem JavaScript Object Notation (JSON). Zapoznaj się z poniższym fragmentem kodu. Zawartość tego fragmentu kodu jest w pełni opisana w [specyfikacji OpenID Connect Connect](https://openid.net). Należy pamiętać, że podanie identyfikatora dzierżawy zamiast `common` zamiast elementu {dzierżawca} spowoduje, że identyfikatory URI specyficzne dla dzierżawy w zwracanym obiekcie JSON.

```
{
    "authorization_endpoint": "https://login.microsoftonline.com/{tenant}/oauth2/authorize",
    "token_endpoint": "https://login.microsoftonline.com/{tenant}/oauth2/token",
    "token_endpoint_auth_methods_supported":
    [
        "client_secret_post",
        "private_key_jwt",
        "client_secret_basic"
    ],
    "jwks_uri": "https://login.microsoftonline.com/common/discovery/keys"
    "userinfo_endpoint":"https://login.microsoftonline.com/{tenant}/openid/userinfo",
    ...
}
```

Jeśli aplikacja ma niestandardowe klucze podpisywania w wyniku użycia funkcji [mapowania oświadczeń](active-directory-claims-mapping.md) , należy dołączyć parametr zapytania `appid` zawierający identyfikator aplikacji w celu uzyskania `jwks_uri` wskazującego informacje o kluczu podpisywania aplikacji. Na przykład: `https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` zawiera `jwks_uri` `https://login.microsoftonline.com/{tenant}/discovery/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e`.

## <a name="send-the-sign-in-request"></a>Wyślij żądanie logowania

Gdy aplikacja sieci Web wymaga uwierzytelnienia użytkownika, musi skierować użytkownika do punktu końcowego `/authorize`. To żądanie jest podobne do pierwszego etapu [przepływu kodu autoryzacji OAuth 2,0](v1-protocols-oauth-code.md)z kilkoma ważnymi rozróżnieniem:

* Żądanie musi zawierać zakres `openid` w parametrze `scope`.
* Parametr `response_type` musi zawierać `id_token`.
* Żądanie musi zawierać parametr `nonce`.

Dlatego przykładowe żądanie będzie wyglądać następująco:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%3a12345
&response_mode=form_post
&scope=openid
&state=12345
&nonce=7362CAEA-9CA5-4B43-9BA3-34D7C303EBA7
```

| Parametr |  | Opis |
| --- | --- | --- |
| tenant |{1&gt;wymagane&lt;1} |Wartość `{tenant}` w ścieżce żądania może służyć do kontrolowania, kto może zalogować się do aplikacji. Dozwolone wartości to identyfikatory dzierżawców, na przykład `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` lub `contoso.onmicrosoft.com` lub `common` dla tokenów niezależnych od dzierżawy |
| client_id |{1&gt;wymagane&lt;1} |Identyfikator aplikacji przypisany do aplikacji po jej zarejestrowaniu w usłudze Azure AD. Można to znaleźć w Azure Portal. Kliknij **Azure Active Directory**, kliknij pozycję **rejestracje aplikacji**, wybierz aplikację i Znajdź identyfikator aplikacji na stronie aplikacji. |
| response_type |{1&gt;wymagane&lt;1} |Musi zawierać `id_token` do logowania za OpenID Connect Connect. Może również zawierać inne response_types, takie jak `code` lub `token`. |
| scope | zalecane | Specyfikacja OpenID Connect Connect wymaga zakresu `openid`, który tłumaczy na uprawnienie "Logowanie użytkownika" w interfejsie użytkownika wyrażania zgody. Te i inne zakresy OIDC są ignorowane w punkcie końcowym v 1.0, ale nadal są najlepszym rozwiązaniem dla klientów zgodnych ze standardami. |
| nonce |{1&gt;wymagane&lt;1} |Wartość uwzględniona w żądaniu wygenerowanym przez aplikację, która jest uwzględniona w `id_token` w wyniku żądania. Następnie aplikacja może zweryfikować tę wartość, aby zmniejszyć ataki metodą powtórzeń tokenu. Wartość jest zazwyczaj losowym, unikatowym ciągiem lub identyfikatorem GUID, który może służyć do identyfikowania pochodzenia żądania. |
| redirect_uri | zalecane |Redirect_uri aplikacji, w której odpowiedzi uwierzytelniania mogą być wysyłane i odbierane przez aplikację. Musi dokładnie pasować do jednego z redirect_uris zarejestrowanego w portalu, z wyjątkiem tego, że musi on być zakodowany w adresie URL. Jeśli go nie ma, agent użytkownika zostanie wysłany z powrotem do jednego z identyfikatorów URI przekierowania zarejestrowanych dla aplikacji. Maksymalna długość to 255 bajtów |
| response_mode |opcjonalnie |Określa metodę, która ma być używana do wysyłania wyników authorization_code z powrotem do aplikacji. Obsługiwane wartości to `form_post` w przypadku *wpisów w formacie http* i `fragment` dla *fragmentu adresu URL*. W przypadku aplikacji sieci Web zalecamy używanie `response_mode=form_post` w celu zapewnienia najbezpieczniejszego transferu tokenów do aplikacji. Wartość domyślna dla każdego przepływu, w tym id_token, jest `fragment`.|
| state |zalecane |Wartość zawarta w żądaniu, która jest zwracana w odpowiedzi tokenu. Może to być ciąg dowolnej zawartości. Losowo wygenerowana unikatowa wartość jest zwykle używana w celu [zapobiegania atakom na fałszerstwo żądań między witrynami](https://tools.ietf.org/html/rfc6749#section-10.12). Ten stan jest również używany do kodowania informacji o stanie użytkownika w aplikacji przed wystąpieniem żądania uwierzytelnienia, takiego jak strona lub widok. |
| pytać |opcjonalnie |Wskazuje typ interakcji użytkownika, która jest wymagana. Obecnie jedynymi prawidłowymi wartościami są "login'", "none" i "zgody". `prompt=login` wymusza, aby użytkownik wprowadził swoje poświadczenia na tym żądaniu, negację logowania jednokrotnego. `prompt=none` jest przeciwieństwem-gwarantuje, że użytkownik nie jest wyświetlany z żadnym interaktywnym monitem. Jeśli żądanie nie może zostać zakończone dyskretnie przy użyciu logowania jednokrotnego, punkt końcowy zwraca błąd. `prompt=consent` wyzwala okno dialogowe zgody OAuth po zalogowaniu się użytkownika, z prośbą o udzielenie uprawnień do aplikacji. |
| login_hint |opcjonalnie |Może służyć do wstępnego wypełniania pola Nazwa użytkownika/adres e-mail strony logowania dla użytkownika, jeśli znana jest jego nazwa użytkownika przed czasem. Często aplikacje używają tego parametru podczas ponownego uwierzytelniania, ponieważ już wyodrębnili nazwę użytkownika z poprzedniego logowania przy użyciu `preferred_username`. |

W tym momencie użytkownik zostanie poproszony o wprowadzenie poświadczeń i zakończenie uwierzytelniania.

### <a name="sample-response"></a>Przykładowa odpowiedź

Przykładowa odpowiedź wysłana do `redirect_uri` określonej w żądaniu logowania po uwierzytelnieniu użytkownika może wyglądać następująco:

```
POST / HTTP/1.1
Host: localhost:12345
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parametr | Opis |
| --- | --- |
| id_token |`id_token` żądana przez aplikację. Możesz użyć `id_token` do zweryfikowania tożsamości użytkownika i rozpoczęcia sesji z użytkownikiem. |
| state |Wartość zawarta w żądaniu, która jest również zwracana w odpowiedzi tokenu. Losowo wygenerowana unikatowa wartość jest zwykle używana w celu [zapobiegania atakom na fałszerstwo żądań między witrynami](https://tools.ietf.org/html/rfc6749#section-10.12). Ten stan jest również używany do kodowania informacji o stanie użytkownika w aplikacji przed wystąpieniem żądania uwierzytelnienia, takiego jak strona lub widok. |

### <a name="error-response"></a>Odpowiedź na błąd

Odpowiedzi na błędy mogą być również wysyłane do `redirect_uri`, aby aplikacja mogła je odpowiednio obsłużyć:

```
POST / HTTP/1.1
Host: localhost:12345
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parametr | Opis |
| --- | --- |
| error |Ciąg kodu błędu, który może służyć do klasyfikowania typów błędów, które występują i mogą być używane do reagowania na błędy. |
| error_description |Konkretny komunikat o błędzie, który może ułatwić deweloperom zidentyfikowanie głównej przyczyny błędu uwierzytelniania. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Kody błędów dla błędów punktu końcowego autoryzacji

W poniższej tabeli opisano różne kody błędów, które mogą być zwracane w `error` parametru odpowiedzi na błąd.

| Kod błędu | Opis | Akcja klienta |
| --- | --- | --- |
| invalid_request |Błąd protokołu, taki jak brak wymaganego parametru. |Napraw i ponownie prześlij żądanie. Jest to błąd programistyczny, który jest zazwyczaj przechwytywany podczas wstępnego testowania. |
| unauthorized_client |Aplikacja kliencka nie może zażądać kodu autoryzacji. |Zwykle dzieje się tak, gdy aplikacja kliencka nie jest zarejestrowana w usłudze Azure AD lub nie została dodana do dzierżawy usługi Azure AD użytkownika. Aplikacja może monitować użytkownika z instrukcją dotyczącą instalowania aplikacji i dodawania jej do usługi Azure AD. |
| access_denied |Niedozwolona zgoda właściciela zasobu |Aplikacja kliencka może powiadomić użytkownika o tym, że nie może wykonać tej czynności, chyba że użytkownik wyraził zgodę. |
| unsupported_response_type |Serwer autoryzacji nie obsługuje typu odpowiedzi w żądaniu. |Napraw i ponownie prześlij żądanie. Jest to błąd programistyczny, który jest zazwyczaj przechwytywany podczas wstępnego testowania. |
| server_error |Serwer napotkał nieoczekiwany błąd. |Ponów żądanie. Te błędy mogą wynikać z warunków tymczasowych. Aplikacja kliencka może wyjaśnić użytkownikowi, że jego odpowiedź jest opóźniona z powodu tymczasowego błędu. |
| temporarily_unavailable |Serwer jest tymczasowo zbyt zajęty, aby obsłużyć żądanie. |Ponów żądanie. Aplikacja kliencka może wyjaśnić użytkownikowi, że jego odpowiedź jest opóźniona ze względu na tymczasowy warunek. |
| invalid_resource |Zasób docelowy jest nieprawidłowy, ponieważ nie istnieje, usługa Azure AD nie może go odnaleźć lub nie jest poprawnie skonfigurowana. |Wskazuje to, że zasób, jeśli istnieje, nie został skonfigurowany w dzierżawie. Aplikacja może monitować użytkownika z instrukcją dotyczącą instalowania aplikacji i dodawania jej do usługi Azure AD. |

## <a name="validate-the-id_token"></a>Weryfikowanie id_token

Tylko otrzymanie `id_token` nie wystarcza do uwierzytelnienia użytkownika; należy sprawdzić poprawność podpisu i zweryfikować oświadczenia w `id_token` zgodnie z wymaganiami aplikacji. Punkt końcowy usługi Azure AD używa tokenów sieci Web JSON (JWTs) i kryptografii klucza publicznego do podpisywania tokenów i sprawdzania, czy są one prawidłowe.

Można sprawdzić poprawność `id_token` w kodzie klienta, ale typowe rozwiązanie polega na wysłaniu `id_token` do serwera wewnętrznej bazy danych i przeprowadzenia weryfikacji w tym miejscu. 

Możesz również chcieć sprawdzić poprawność dodatkowych oświadczeń w zależności od danego scenariusza. Niektóre typowe walidacje obejmują:

* Upewnienie się, że użytkownik/organizacja zarejestrowali się w aplikacji.
* Upewnienie się, że użytkownik ma odpowiednie uprawnienia/autoryzacja przy użyciu `wids` lub `roles` oświadczeń. 
* Upewnienie się, że wystąpiło pewne silne uwierzytelnianie, takie jak uwierzytelnianie wieloskładnikowe.

Po sprawdzeniu `id_token`można rozpocząć sesję z użytkownikiem i użyć oświadczeń w `id_token`, aby uzyskać informacje o użytkowniku w aplikacji. Te informacje mogą służyć do wyświetlania, rekordów, personalizacji itp. Aby uzyskać więcej informacji na temat `id_tokens` i oświadczeń, Odczytaj [id_tokens usługi AAD](id-tokens.md).

## <a name="send-a-sign-out-request"></a>Wyślij żądanie wylogowania

Jeśli chcesz podpisać użytkownika poza aplikacją, nie jest wystarczające czyszczenie plików cookie aplikacji lub zakończenie sesji z użytkownikiem. Należy również przekierować użytkownika do `end_session_endpoint` w celu wylogowania. Jeśli to zrobisz, użytkownik będzie mógł ponownie uwierzytelnić się w aplikacji bez konieczności ponownego wprowadzania poświadczeń, ponieważ będzie mieć prawidłową sesję logowania jednokrotnego w punkcie końcowym usługi Azure AD.

Można po prostu przekierować użytkownika do `end_session_endpoint` wymienionego w dokumencie metadanych OpenID Connect Connect:

```
GET https://login.microsoftonline.com/common/oauth2/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F

```

| Parametr |  | Opis |
| --- | --- | --- |
| post_logout_redirect_uri |zalecane |Adres URL, do którego użytkownik powinien zostać przekierowany po pomyślnym wylogowaniu.  Ten adres URL musi być zgodny z jednym z identyfikatorów URI przekierowania zarejestrowanych dla aplikacji w portalu rejestracji aplikacji.  Jeśli *post_logout_redirect_uri* nie jest uwzględniony, zostanie wyświetlony komunikat generyczny. |

## <a name="single-sign-out"></a>Wylogowanie jednokrotne

Po przekierowaniu użytkownika do `end_session_endpoint`usługa Azure AD czyści sesję użytkownika z przeglądarki. Jednak użytkownik może nadal być zalogowany do innych aplikacji korzystających z usługi Azure AD do uwierzytelniania. Aby umożliwić tym aplikacjom jednoczesne podpisywanie użytkownika, usługa Azure AD wysyła żądanie HTTP GET do zarejestrowanej `LogoutUrl` wszystkich aplikacji, do których użytkownik jest aktualnie zalogowany. Aplikacje muszą odpowiedzieć na to żądanie przez wyczyszczenie każdej sesji, która identyfikuje użytkownika i zwróci odpowiedź `200`. Jeśli chcesz obsługiwać Logowanie jednokrotne w aplikacji, musisz zaimplementować takie `LogoutUrl` w kodzie aplikacji. Możesz ustawić `LogoutUrl` z Azure Portal:

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
2. Wybierz Active Directory, klikając swoje konto w prawym górnym rogu strony.
3. W panelu nawigacyjnym po lewej stronie wybierz pozycję **Azure Active Directory**, a następnie wybierz pozycję **rejestracje aplikacji** i wybierz aplikację.
4. Kliknij pozycję **Ustawienia**, a następnie **Właściwości** i Znajdź pole tekstowe **adres URL wylogowywania** . 

## <a name="token-acquisition"></a>Pozyskiwanie tokenu
Wiele aplikacji sieci Web musi nie tylko podpisać użytkownika w programie, ale również uzyskiwać dostęp do usługi sieci Web w imieniu tego użytkownika przy użyciu protokołu OAuth. Ten scenariusz łączy OpenID Connect Connect for User Authentication podczas jednoczesnego pozyskiwania `authorization_code`, których można użyć do uzyskania `access_tokens` przy użyciu [przepływu kodu autoryzacji OAuth](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token).

## <a name="get-access-tokens"></a>Uzyskaj tokeny dostępu
Aby uzyskać tokeny dostępu, musisz zmodyfikować żądanie logowania z powyższych:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application ID
&response_type=id_token+code
&redirect_uri=http%3A%2F%2Flocalhost%3a12345          // Your registered Redirect Uri, url encoded
&response_mode=form_post                              // `form_post' or 'fragment'
&scope=openid
&resource=https%3A%2F%2Fservice.contoso.com%2F        // The identifier of the protected resource (web API) that your application needs access to
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

Dołączając zakresy uprawnień w żądaniu i korzystając `response_type=code+id_token`, punkt końcowy `authorize` zapewnia, że użytkownik wyraził zgodę na uprawnienia wskazane w `scope` parametr zapytania i zwróci aplikację kod autoryzacji do wymiany dla tokenu dostępu.

### <a name="successful-response"></a>Pomyślna odpowiedź

Pomyślna odpowiedź wysłana do `redirect_uri` przy użyciu `response_mode=form_post`wygląda następująco:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Parametr | Opis |
| --- | --- |
| id_token |`id_token` żądana przez aplikację. Możesz użyć `id_token` do zweryfikowania tożsamości użytkownika i rozpoczęcia sesji z użytkownikiem. |
| kod |Authorization_code żądana przez aplikację. Aplikacja może użyć kodu autoryzacji, aby zażądać tokenu dostępu dla zasobu docelowego. Authorization_codes są krótkotrwałe i zazwyczaj wygasa po około 10 minutach. |
| state |Jeśli w żądaniu zostanie uwzględniony parametr stanu, ta sama wartość powinna pojawić się w odpowiedzi. Aplikacja powinna sprawdzić, czy wartości stanu w żądaniu i odpowiedzi są identyczne. |

### <a name="error-response"></a>Odpowiedź na błąd

Odpowiedzi na błędy mogą być również wysyłane do `redirect_uri`, aby aplikacja mogła je odpowiednio obsłużyć:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parametr | Opis |
| --- | --- |
| error |Ciąg kodu błędu, który może służyć do klasyfikowania typów błędów, które występują i mogą być używane do reagowania na błędy. |
| error_description |Konkretny komunikat o błędzie, który może ułatwić deweloperom zidentyfikowanie głównej przyczyny błędu uwierzytelniania. |

Aby uzyskać opis możliwych kodów błędów i ich zalecaną akcję klienta, zobacz [kody błędów dla błędów punktu końcowego autoryzacji](#error-codes-for-authorization-endpoint-errors).

Po uzyskaniu `code` autoryzacji i `id_token`można podpisać użytkownika w usłudze i uzyskać [tokeny dostępu](access-tokens.md) w ich imieniu. Aby podpisać użytkownika w programie, należy sprawdzić poprawność `id_token` dokładnie zgodnie z powyższym opisem. Aby uzyskać tokeny dostępu, można wykonać kroki opisane w sekcji "Używanie kodu autoryzacji do żądania tokenu dostępu" w [dokumentacji przepływu kodu OAuth](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [tokenach dostępu](access-tokens.md).
* Dowiedz się więcej na temat [`id_token` i oświadczeń](id-tokens.md).
