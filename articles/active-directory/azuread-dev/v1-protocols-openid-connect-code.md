---
title: Autoryzowanie dostępu do aplikacji sieci Web za pomocą openid connect & usługi Azure AD | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób używania wiadomości HTTP do autoryzowania dostępu do aplikacji sieci web i interfejsów API sieci Web w dzierżawie przy użyciu usługi Azure Active Directory i OpenID Connect.
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: eef8174056be7e6be35cea56788c0a519d02944e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154444"
---
# <a name="authorize-access-to-web-applications-using-openid-connect-and-azure-active-directory"></a>Autoryzowanie dostępu do aplikacji internetowych przy użyciu warstwy OpenID Connect i usługi Azure Active Directory

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) to prosta warstwa tożsamości zbudowana na szczycie protokołu OAuth 2.0. OAuth 2.0 definiuje mechanizmy uzyskiwania i używania [**tokenów dostępu**](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) w celu uzyskania dostępu do chronionych zasobów, ale nie definiują standardowych metod dostarczania informacji o tożsamości. OpenID Connect implementuje uwierzytelnianie jako rozszerzenie procesu autoryzacji OAuth 2.0. Zawiera informacje o użytkowniku końcowym w [`id_token`](../develop/id-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) postaci, która weryfikuje tożsamość użytkownika i zawiera podstawowe informacje o profilu użytkownika.

OpenID Connect jest naszym zaleceniem, jeśli budujesz aplikację internetową, która jest hostowana na serwerze i dostępna za pośrednictwem przeglądarki.

## <a name="register-your-application-with-your-ad-tenant"></a>Rejestrowanie aplikacji w dzierżawie usługi AD
Najpierw zarejestruj aplikację w dzierżawie usługi Azure Active Directory (Azure AD). Spowoduje to nadanie aplikacji identyfikatora oraz umożliwi jej otrzymywanie tokenów.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
   
1. Wybierz dzierżawę usługi Azure AD, wybierając swoje konto w prawym górnym rogu strony, a następnie wybierając nawigację **w katalogu przełączania,** a następnie wybierając odpowiednią dzierżawę. 
   - Pomiń ten krok, jeśli masz tylko jedną dzierżawę usługi Azure AD na swoim koncie lub jeśli masz już wybraną odpowiednią dzierżawę usługi Azure AD.
   
1. W witrynie Azure portal wyszukaj i wybierz pozycję **Azure Active Directory**.
   
1. W menu Po lewej **stronie usługi Azure Active Directory** wybierz pozycję **Rejestracje aplikacji**, a następnie wybierz pozycję Nowa **rejestracja**.
   
1. Postępuj zgodnie z monitami i utwórz nową aplikację. Nie ma znaczenia, czy jest to aplikacja internetowa lub klienta publicznego (mobile & desktop) aplikacji dla tego samouczka, ale jeśli chcesz konkretnych przykładów dla aplikacji internetowych lub publicznych aplikacji klienckich, sprawdź nasze [przewodniki Szybki start](v1-overview.md).
   
   - **Nazwa** to nazwa aplikacji; opisuje aplikację innym użytkownikom.
   - W obszarze **Obsługiwane typy kont** wybierz pozycję **Konta w dowolnym katalogu organizacyjnym i konta osobiste Microsoft**.
   - Podaj **identyfikator URI przekierowania**. W przypadku aplikacji sieci web jest to podstawowy adres URL aplikacji, w którym użytkownicy mogą się logować.  Na przykład `http://localhost:12345`. Dla klienta publicznego (mobile & desktop), usługa Azure AD używa go do zwracania odpowiedzi tokenu. Wprowadź wartość specyficzną dla swojej aplikacji.  Na przykład `http://MyFirstAADApp`.
   <!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->  
   
1. Po zakończeniu rejestracji usługa Azure AD przypisze aplikacji unikatowy identyfikator klienta **(identyfikator aplikacji).** Ta wartość jest potrzebna w następnych sekcjach, więc skopiuj ją ze strony aplikacji.
   
1. Aby znaleźć aplikację w witrynie Azure portal, wybierz pozycję **Rejestracje aplikacji**, a następnie wybierz pozycję **Wyświetl wszystkie aplikacje**.

## <a name="authentication-flow-using-openid-connect"></a>Przepływ uwierzytelniania w przypadku protokołu OpenID Connect

Najbardziej podstawowy przepływ logowania zawiera następujące kroki — każdy z nich jest szczegółowo opisany poniżej.

![Przepływ uwierzytelniania Połączenia OpenId](./media/v1-protocols-openid-connect-code/active-directory-oauth-code-flow-web-app.png)

## <a name="openid-connect-metadata-document"></a>Dokument metadanych OpenID Connect

OpenID Connect opisuje dokument metadanych, który zawiera większość informacji wymaganych dla aplikacji do wykonania logowania. Obejmuje to informacje, takie jak adresy URL do użycia i lokalizacja publicznych kluczy podpisywania usługi. Dokument metadanych OpenID Connect można znaleźć pod adresem:

```
https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration
```
Metadane to prosty dokument notacji obiektu JavaScript (JSON). Zobacz poniższy fragment kodu na przykład. Zawartość fragmentu kodu jest w pełni opisana w [specyfikacji OpenID Connect](https://openid.net). Należy zauważyć, że podanie `common` identyfikatora dzierżawy, a nie zamiast {tenant} powyżej spowoduje URI specyficzne dla dzierżawy w obiekcie JSON zwrócone.

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

Jeśli aplikacja ma niestandardowe klucze podpisywania w wyniku korzystania z `appid` funkcji [mapowania oświadczeń,](../develop/active-directory-claims-mapping.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) należy dołączyć parametr kwerendy zawierający identyfikator aplikacji, aby uzyskać `jwks_uri` wskazanie informacji o kluczu podpisywania aplikacji. Na przykład: `https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` `jwks_uri` zawiera `https://login.microsoftonline.com/{tenant}/discovery/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e`a z .

## <a name="send-the-sign-in-request"></a>Wyślij żądanie logowania

Gdy aplikacja sieci web musi uwierzytelnić użytkownika, musi `/authorize` kierować użytkownika do punktu końcowego. To żądanie jest podobne do pierwszego etapu [przepływu kodu autoryzacji OAuth 2.0,](v1-protocols-oauth-code.md)z kilkoma ważnymi różnicami:

* Żądanie musi zawierać `openid` zakres `scope` w parametrze.
* Parametr `response_type` musi `id_token`zawierać .
* Żądanie musi zawierać `nonce` parametr.

Więc przykładowe żądanie będzie wyglądać następująco:

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
| Dzierżawy |wymagany |Wartość `{tenant}` w ścieżce żądania może służyć do kontrolowania, kto może zalogować się do aplikacji. Dozwolone wartości to identyfikatory dzierżawy, `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` `contoso.onmicrosoft.com` na `common` przykład lub tokeny niezależne od dzierżawy |
| client_id |wymagany |Identyfikator aplikacji przypisany do aplikacji po zarejestrowaniu go w usłudze Azure AD. Można to znaleźć w witrynie Azure portal. Kliknij **pozycję Usługa Azure Active Directory**, kliknij pozycję **Rejestracje aplikacji**, wybierz aplikację i znajdź identyfikator aplikacji na stronie aplikacji. |
| response_type |wymagany |Musi `id_token` zawierać dla openid connect logowania. Może również obejmować inne response_types, takie `code` `token`jak lub . |
| scope | zalecane | Specyfikacja OpenID Connect `openid`wymaga zakresu , co przekłada się na uprawnienie "Zaloguj się" w interfejsie użytkownika zgody. To i inne zakresy OIDC są ignorowane w punkcie końcowym w wersji 1.0, ale nadal jest najlepszym rozwiązaniem dla klientów zgodnych ze standardami. |
| nonce |wymagany |Wartość uwzględniona w żądaniu, wygenerowana przez aplikację, która `id_token` jest uwzględniona w wyniku jako oświadczenie. Aplikacja może następnie zweryfikować tę wartość, aby ograniczyć ataki powtarzania tokenów. Wartość jest zazwyczaj losowo, unikatowy ciąg lub identyfikator GUID, który może służyć do identyfikowania pochodzenia żądania. |
| redirect_uri | zalecane |Redirect_uri aplikacji, w której aplikacja może wysyłać i odbierać odpowiedzi na uwierzytelnianie. Musi dokładnie odpowiadać jednej z redirect_uris zarejestrowanej w portalu, z tą różnicą, że musi być zakodowany adres URL. Jeśli brakuje, agent użytkownika zostanie wysłany z powrotem do jednego z przekierowania identyfikatorów URI zarejestrowanych dla aplikacji, losowo. Maksymalna długość to 255 bajtów |
| response_mode |optional |Określa metodę, która powinna służyć do wysyłania wynikowych authorization_code z powrotem do aplikacji. Obsługiwane wartości `form_post` są dla *wpisu formularza HTTP* i `fragment` *fragmentu adresu URL*. W przypadku aplikacji sieci `response_mode=form_post` web zaleca się używanie w celu zapewnienia najbezpieczniejszego transferu tokenów do aplikacji. Domyślnie dla każdego przepływu, w `fragment`tym id_token, jest .|
| state |zalecane |Wartość uwzględniona w żądaniu, który jest zwracany w odpowiedzi tokenu. Może to być ciąg dowolnej zawartości, którą chcesz. Losowo generowana unikatowa wartość jest zwykle używana do [zapobiegania atakom fałszerskości żądań między witrynami.](https://tools.ietf.org/html/rfc6749#section-10.12) Stan jest również używany do kodowania informacji o stanie użytkownika w aplikacji przed wystąpieniem żądania uwierzytelnienia, takich jak strona lub widok, na których się znajdowały. |
| Wierszu |optional |Wskazuje typ interakcji użytkownika, który jest wymagany. Obecnie jedynymi prawidłowymi wartościami są "login", "none" i "consent". `prompt=login`wymusza wprowadzenie przez użytkownika swoich poświadczeń w tym żądaniu, negując jednokrotne zalogowanie. `prompt=none`jest odwrotnie - zapewnia, że użytkownik nie jest prezentowany z żadnym interaktywnym monitem. Jeśli żądanie nie może być wykonane w trybie dyskretnym za pomocą logowania jednokrotnego, punkt końcowy zwraca błąd. `prompt=consent`wyzwala okno dialogowe zgody OAuth po zalogowaniu się użytkownika, prosząc użytkownika o przyznanie uprawnień do aplikacji. |
| login_hint |optional |Może służyć do wstępnego wypełnienia pola nazwy użytkownika/adresu e-mail strony logowania dla użytkownika, jeśli znasz jego nazwę użytkownika z wyprzedzeniem. Często aplikacje używają tego parametru podczas ponownego uwierzytelniania, po wyodrębnieniu już `preferred_username` nazwy użytkownika z poprzedniego logowania przy użyciu oświadczenia. |

W tym momencie użytkownik jest proszony o wprowadzenie swoich poświadczeń i zakończenie uwierzytelniania.

### <a name="sample-response"></a>Przykładowa odpowiedź

Przykładowa odpowiedź, wysłana `redirect_uri` do określonego w żądaniu logowania po uwierzytelnieniu użytkownika, może wyglądać następująco:

```
POST / HTTP/1.1
Host: localhost:12345
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parametr | Opis |
| --- | --- |
| id_token |To, `id_token` o co prosiła aplikacja. Można użyć, `id_token` aby zweryfikować tożsamość użytkownika i rozpocząć sesję z użytkownikiem. |
| state |Wartość uwzględniona w żądaniu, który jest również zwracany w odpowiedzi tokenu. Losowo generowana unikatowa wartość jest zwykle używana do [zapobiegania atakom fałszerskości żądań między witrynami.](https://tools.ietf.org/html/rfc6749#section-10.12) Stan jest również używany do kodowania informacji o stanie użytkownika w aplikacji przed wystąpieniem żądania uwierzytelnienia, takich jak strona lub widok, na których się znajdowały. |

### <a name="error-response"></a>Odpowiedź na błąd

Odpowiedzi na błędy mogą być `redirect_uri` również wysyłane do aplikacji, dzięki czemu aplikacja może obsługiwać je odpowiednio:

```
POST / HTTP/1.1
Host: localhost:12345
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parametr | Opis |
| --- | --- |
| error |Ciąg kodu błędu, który może służyć do klasyfikowania typów błędów, które występują i może służyć do reagowania na błędy. |
| error_description |Określony komunikat o błędzie, który może pomóc deweloperowi zidentyfikować główną przyczynę błędu uwierzytelniania. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Kody błędów dla błędów punktu końcowego autoryzacji

W poniższej tabeli opisano różne kody `error` błędów, które mogą być zwracane w parametrze odpowiedzi na błąd.

| Kod błędu | Opis | Akcja klienta |
| --- | --- | --- |
| invalid_request |Błąd protokołu, taki jak brak wymaganego parametru. |Napraw i ponownie prześlij żądanie. Jest to błąd rozwoju i zazwyczaj jest przechwytyny podczas wstępnego testowania. |
| unauthorized_client |Aplikacja kliencka nie może żądać kodu autoryzacji. |Zwykle dzieje się tak, gdy aplikacja kliencka nie jest zarejestrowana w usłudze Azure AD lub nie jest dodawana do dzierżawy usługi Azure AD użytkownika. Aplikacja może monitować użytkownika o instrukcje dotyczące instalowania aplikacji i dodawania jej do usługi Azure AD. |
| Access_denied |Właściciel zasobu, który odmówił zgody |Aplikacja kliencka może powiadomić użytkownika, że nie może kontynuować, chyba że użytkownik wyrazi na to zgodę. |
| unsupported_response_type |Serwer autoryzacji nie obsługuje typu odpowiedzi w żądaniu. |Napraw i ponownie prześlij żądanie. Jest to błąd rozwoju i zazwyczaj jest przechwytyny podczas wstępnego testowania. |
| server_error |Serwer napotkał nieoczekiwany błąd. |Ponów próbę żądania. Błędy te mogą wynikać z warunków tymczasowych. Aplikacja kliencka może wyjaśnić użytkownikowi, że jego odpowiedź jest opóźniona z powodu błędu tymczasowego. |
| temporarily_unavailable |Serwer jest tymczasowo zbyt zajęty, aby obsłużyć żądanie. |Ponów próbę żądania. Aplikacja kliencka może wyjaśnić użytkownikowi, że jego odpowiedź jest opóźniona z powodu stanu tymczasowego. |
| invalid_resource |Zasób docelowy jest nieprawidłowy, ponieważ nie istnieje, usługa Azure AD nie może go znaleźć lub nie jest poprawnie skonfigurowany. |Oznacza to, że zasób, jeśli istnieje, nie został skonfigurowany w dzierżawie. Aplikacja może monitować użytkownika o instrukcje dotyczące instalowania aplikacji i dodawania jej do usługi Azure AD. |

## <a name="validate-the-id_token"></a>Sprawdzanie poprawności id_token

Samo odebranie `id_token` nie jest wystarczające do uwierzytelnienia użytkownika; należy zweryfikować podpis i zweryfikować `id_token` oświadczenia zgodnie z wymaganiami aplikacji. Punkt końcowy usługi Azure AD używa tokenów JSON Web Tokens (JWTs) i kryptografii klucza publicznego do podpisywania tokenów i sprawdzania, czy są one prawidłowe.

Można wybrać, aby `id_token` sprawdzić poprawność w kodzie klienta, `id_token` ale powszechną praktyką jest wysłanie do serwera wewnętrznej bazy danych i wykonać sprawdzanie poprawności tam. 

Można również sprawdzić poprawność dodatkowych oświadczeń w zależności od scenariusza. Niektóre typowe weryfikacje obejmują:

* Upewnienie się, że użytkownik/organizacja zarejestrowała się w aplikacji.
* Zapewnienie użytkownikowi odpowiedniej autoryzacji/uprawnień `wids` przy `roles` użyciu lub oświadczeń. 
* Zapewnienie pewnej siły uwierzytelniania, takich jak uwierzytelnianie wieloskładnikowe.

Po sprawdzeniu `id_token`poprawności programu , można rozpocząć sesję z użytkownikiem i użyć oświadczeń w `id_token` celu uzyskania informacji o użytkowniku w aplikacji. Informacje te mogą być używane do wyświetlania, zapisów, personalizacji itp. Aby uzyskać `id_tokens` więcej informacji na temat i oświadczeń, zobacz [id_tokens AAD](../develop/id-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

## <a name="send-a-sign-out-request"></a>Wysyłanie żądania wylogowywania

Jeśli chcesz wylogować użytkownika z aplikacji, nie wystarczy wyczyścić pliki cookie aplikacji lub w inny sposób zakończyć sesję z użytkownikiem. Należy również przekierować użytkownika `end_session_endpoint` do wylogowywania się. Jeśli to nie zrobisz, użytkownik będzie mógł ponownie uwierzytelnić do aplikacji bez wprowadzania ich poświadczeń ponownie, ponieważ będą mieli prawidłową sesję logowania jednokrotnego z punktem końcowym usługi Azure AD.

Możesz po prostu przekierować `end_session_endpoint` użytkownika do wymienionych w dokumencie metadanych OpenID Connect:

```
GET https://login.microsoftonline.com/common/oauth2/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F

```

| Parametr |  | Opis |
| --- | --- | --- |
| post_logout_redirect_uri |zalecane |Adres URL, do który użytkownik powinien zostać przekierowany po pomyślnym wylogowywaniu się.  Ten adres URL musi być zgodny z jednym z identyfikatorów URI przekierowania zarejestrowanych dla aplikacji w portalu rejestracji aplikacji.  Jeśli *post_logout_redirect_uri* nie jest uwzględniony, użytkownik otrzymuje komunikat ogólny. |

## <a name="single-sign-out"></a>Wylogowanie jednokrotne

Po przekierowaniu użytkownika `end_session_endpoint`do usługi Azure AD czyści sesji użytkownika z przeglądarki. Jednak użytkownik może nadal być zalogowany do innych aplikacji, które używają usługi Azure AD do uwierzytelniania. Aby umożliwić tym aplikacjom jednoczesne wylogowywanie użytkownika, usługa `LogoutUrl` Azure AD wysyła żądanie HTTP GET do zarejestrowanych wszystkich aplikacji, do których użytkownik jest aktualnie zalogowany. Aplikacje muszą odpowiedzieć na to żądanie, czyszcząc dowolną `200` sesję, która identyfikuje użytkownika i zwracając odpowiedź. Jeśli chcesz obsługiwać pojedyncze wylogowania w `LogoutUrl` aplikacji, należy zaimplementować takie w kodzie aplikacji. Można ustawić `LogoutUrl` z witryny Azure portal:

1. Przejdź do [witryny Azure portal](https://portal.azure.com).
2. Wybierz usługa Active Directory, klikając konto w prawym górnym rogu strony.
3. W lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**, a następnie wybierz pozycję **Rejestracje aplikacji** i wybierz aplikację.
4. Kliknij **ustawienia**, a następnie właściwości i znajdź pole tekstowe **adresu URL wylogowania.** **Properties** 

## <a name="token-acquisition"></a>Pozyskiwanie tokenów
Wiele aplikacji sieci web musi nie tylko zalogować użytkownika, ale także uzyskać dostęp do usługi sieci web w imieniu tego użytkownika przy użyciu usługi OAuth. W tym scenariuszu łączy OpenID Connect do `authorization_code` uwierzytelniania użytkownika, `access_tokens` jednocześnie uzyskując, że może służyć do uzyskania przy użyciu [przepływu kodu autoryzacji OAuth](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token).

## <a name="get-access-tokens"></a>Uzyskaj tokeny dostępu
Aby uzyskać tokeny dostępu, należy zmodyfikować żądanie logowania z góry:

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

Dołączając zakresy uprawnień w `response_type=code+id_token`żądaniu i używając , punkt `authorize` końcowy zapewnia, że użytkownik `scope` wyraził zgodę na uprawnienia wskazane w parametrze zapytania i zwraca aplikacji kod autoryzacji do wymiany tokenu dostępu.

### <a name="successful-response"></a>Skuteczna reakcja

Pomyślna odpowiedź, wysłana `redirect_uri` `response_mode=form_post`do using , wygląda następująco:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Parametr | Opis |
| --- | --- |
| id_token |To, `id_token` o co prosiła aplikacja. Można użyć, `id_token` aby zweryfikować tożsamość użytkownika i rozpocząć sesję z użytkownikiem. |
| kod |Authorization_code, o które prosiła aplikacja. Aplikacja może użyć kodu autoryzacji, aby zażądać tokenu dostępu dla zasobu docelowego. Authorization_codes są krótkotrwałe i zazwyczaj wygasają po około 10 minutach. |
| state |Jeśli parametr stanu znajduje się w żądaniu, ta sama wartość powinna pojawić się w odpowiedzi. Aplikacja powinna sprawdzić, czy wartości stanu w żądaniu i odpowiedzi są identyczne. |

### <a name="error-response"></a>Odpowiedź na błąd

Odpowiedzi na błędy mogą być `redirect_uri` również wysyłane do aplikacji, dzięki czemu aplikacja może obsługiwać je odpowiednio:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parametr | Opis |
| --- | --- |
| error |Ciąg kodu błędu, który może służyć do klasyfikowania typów błędów, które występują i może służyć do reagowania na błędy. |
| error_description |Określony komunikat o błędzie, który może pomóc deweloperowi zidentyfikować główną przyczynę błędu uwierzytelniania. |

Aby uzyskać opis możliwych kodów błędów i ich zalecanej akcji klienta, zobacz [Kody błędów dla błędów punktów końcowych autoryzacji](#error-codes-for-authorization-endpoint-errors).

Po uzyskaniu autoryzacji `code` i `id_token`, można zalogować użytkownika i uzyskać [tokeny dostępu](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) w ich imieniu. Aby zalogować się do użytkownika, `id_token` należy sprawdzić poprawność dokładnie tak, jak opisano powyżej. Aby uzyskać tokeny dostępu, można wykonać kroki opisane w sekcji "Użyj kodu autoryzacji, aby zażądać tokenu dostępu" w naszej [dokumentacji przepływu kodu OAuth.](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [tokenach dostępu](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).
* Dowiedz się więcej o [ `id_token` roszczeniach i roszczeniach](../develop/id-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).
