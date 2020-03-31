---
title: Opis przepływu kodu autoryzacji OAuth 2.0 w usłudze Azure AD
description: W tym artykule opisano sposób używania wiadomości HTTP do autoryzowania dostępu do aplikacji sieci web i interfejsów API sieci Web w dzierżawie przy użyciu usługi Azure Active Directory i OAuth 2.0.
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 3ec7cf5a45ce31cde923dce521636589cfcda786
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154461"
---
# <a name="authorize-access-to-azure-active-directory-web-applications-using-the-oauth-20-code-grant-flow"></a>Autoryzowanie dostępu do aplikacji internetowych usługi Azure Active Directory przy użyciu przepływu udzielania kodu OAuth 2.0

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

> [!NOTE]
>  Jeśli nie powiesz serwerowi, jaki zasób zamierzasz wywołać, serwer nie wyzwoli zasad dostępu warunkowego dla tego zasobu. Tak więc, aby mieć wyzwalacz usługi MFA, należy dołączyć zasób w adresie URL. 
>

Przy użyciu protokołu OAuth 2.0 usługa Azure Active Directory (Azure AD) umożliwia autoryzację dostępu do aplikacji internetowych i internetowych interfejsów API w dzierżawie usługi Azure AD. Ten przewodnik jest niezależny od języka i opisuje sposób wysyłania i odbierania wiadomości HTTP bez korzystania z naszych [bibliotek open source.](active-directory-authentication-libraries.md)

Przepływ kodu autoryzacji OAuth 2.0 jest opisany w [sekcji 4.1 specyfikacji OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.1). Służy do uwierzytelniania i autoryzacji w większości typów aplikacji, w tym aplikacji sieci web i natywnie zainstalowanych aplikacji.

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

## <a name="oauth-20-authorization-flow"></a>Przepływ autoryzacji OAuth 2.0

Na wysokim poziomie cały przepływ autoryzacji dla aplikacji wygląda nieco tak:

![Przepływ kodu OAuth Auth](./media/v1-protocols-oauth-code/active-directory-oauth-code-flow-native-app.png)

## <a name="request-an-authorization-code"></a>Poproś o kod autoryzacji

Przepływ kodu autoryzacji rozpoczyna się od klienta `/authorize` kierującego użytkownika do punktu końcowego. W tym żądaniu klient wskazuje uprawnienia, które musi uzyskać od użytkownika. Możesz uzyskać punkt końcowy autoryzacji OAuth 2.0 dla dzierżawy, wybierając **rejestracje aplikacji > punktami końcowymi** w witrynie Azure portal.

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%3A12345
&response_mode=query
&resource=https%3A%2F%2Fservice.contoso.com%2F
&state=12345
```

| Parametr |  | Opis |
| --- | --- | --- |
| Dzierżawy |wymagany |Wartość `{tenant}` w ścieżce żądania może służyć do kontrolowania, kto może zalogować się do aplikacji. Dozwolone wartości to identyfikatory dzierżawy, `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` `contoso.onmicrosoft.com` na `common` przykład lub tokeny niezależne od dzierżawy |
| client_id |wymagany |Identyfikator aplikacji przypisany do aplikacji po zarejestrowaniu go w usłudze Azure AD. Można to znaleźć w witrynie Azure Portal. Kliknij **pozycję Azure Active Directory** na pasku bocznym usług, kliknij pozycję **Rejestracje aplikacji**i wybierz aplikację. |
| response_type |wymagany |Musi `code` zawierać dla przepływu kodu autoryzacji. |
| redirect_uri |zalecane |Redirect_uri aplikacji, w której aplikacja może wysyłać i odbierać odpowiedzi na uwierzytelnianie. Musi dokładnie odpowiadać jednej z redirect_uris zarejestrowanej w portalu, z tą różnicą, że musi być zakodowany adres URL. W przypadku aplikacji mobilnych & natywnych `https://login.microsoftonline.com/common/oauth2/nativeclient`należy użyć domyślnej wartości programu . |
| response_mode |optional |Określa metodę, która powinna służyć do wysyłania tokenu wynikowego z powrotem do aplikacji. Może `query`być `fragment`, `form_post`lub . `query`udostępnia kod jako parametr ciągu zapytania w identyfikatorze URI przekierowania. Jeśli żądasz tokenu identyfikatora przy użyciu przepływu niejawnego, nie można użyć `query` zgodnie z specyfikacją [OpenID](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations). Jeśli żądasz tylko kodu, możesz użyć `query` `fragment`, `form_post`, lub . `form_post`wykonuje post zawierający kod do identyfikatora URI przekierowania. Wartość domyślna dotyczy `query` przepływu kodu.  |
| state |zalecane |Wartość uwzględniona w żądaniu, który jest również zwracany w odpowiedzi tokenu. Losowo generowana unikatowa wartość jest zwykle używana do [zapobiegania atakom fałszerskości żądań między witrynami.](https://tools.ietf.org/html/rfc6749#section-10.12) Stan jest również używany do kodowania informacji o stanie użytkownika w aplikacji przed wystąpieniem żądania uwierzytelnienia, takich jak strona lub widok, na których się znajdowały. |
| zasób | zalecane |Identyfikator URI identyfikatora aplikacji docelowego interfejsu API sieci web (zasobu zabezpieczonego). Aby znaleźć identyfikator URI identyfikatora aplikacji, w portalu Azure portal kliknij pozycję **Azure Active Directory**, kliknij pozycję **Rejestracje aplikacji**, otwórz stronę **Ustawienia** aplikacji, a następnie kliknij pozycję **Właściwości**. Może to być również `https://graph.microsoft.com`zasób zewnętrzny, taki jak . Jest to wymagane w jednym z żądań autoryzacji lub tokenu. Aby upewnić się, że mniej monitów o uwierzytelnienie umieść go w żądaniu autoryzacji, aby upewnić się, że zgoda jest odbierana od użytkownika. |
| scope | **Ignorowane** | W przypadku aplikacji usługi Azure AD w wersji 1 zakresy muszą być statycznie skonfigurowane w witrynie Azure Portal w obszarze **Ustawienia**aplikacji — **wymagane uprawnienia**. |
| Wierszu |optional |Wskazać typ interakcji użytkownika, który jest wymagany.<p> Prawidłowe wartości: <p> *login*: Użytkownik powinien zostać poproszony o ponowne uwierzytelnienie. <p> *select_account*: Użytkownik jest monitowany o wybranie konta, przerywając logowanie jednokrotne. Użytkownik może wybrać istniejące konto logowania, wprowadzić swoje poświadczenia dla zapamiętanego konta lub wybrać opcję użycia innego konta. <p> *zgoda*: Zgoda użytkownika została udzielona, ale musi zostać zaktualizowana. Użytkownik powinien zostać poproszony o wyrażenie zgody. <p> *admin_consent*: Administrator powinien zostać poproszony o wyrażenie zgody w imieniu wszystkich użytkowników w organizacji |
| login_hint |optional |Może służyć do wstępnego wypełnienia pola nazwy użytkownika/adresu e-mail strony logowania dla użytkownika, jeśli znasz jego nazwę użytkownika z wyprzedzeniem. Często aplikacje używają tego parametru podczas ponownego uwierzytelniania, po wyodrębnieniu już `preferred_username` nazwy użytkownika z poprzedniego logowania przy użyciu oświadczenia. |
| domain_hint |optional |Zawiera wskazówkę dotyczącą dzierżawy lub domeny, której użytkownik powinien używać do logowania. Wartość domain_hint jest domeną zarejestrowaną dla dzierżawcy. Jeśli dzierżawca jest sfederowany do katalogu lokalnego, usługa AAD przekierowuje do określonego serwera federacyjnego dzierżawy. |
| code_challenge_method | zalecane    | Metoda używana do kodowania `code_verifier` `code_challenge` dla parametru. Może być `plain` jednym `S256`z lub . Jeśli jest `code_challenge` wykluczona, przyjmuje `code_challenge` się, że jest to zwykły tekst, jeśli jest dołączona. Usługa Azure AAD w wersji `plain` `S256`1.0 obsługuje zarówno i . Aby uzyskać więcej informacji, zobacz [PKCE RFC](https://tools.ietf.org/html/rfc7636). |
| code_challenge        | zalecane    | Służy do zabezpieczania dotacji kodu autoryzacji za pośrednictwem klucza dowodu wymiany kodu (PKCE) od klienta macierzystego lub publicznego. Wymagane, `code_challenge_method` jeśli jest wliczone w cenę. Aby uzyskać więcej informacji, zobacz [PKCE RFC](https://tools.ietf.org/html/rfc7636). |

> [!NOTE]
> Jeśli użytkownik jest częścią organizacji, administrator organizacji może wyrazić zgodę lub odrzucić w imieniu użytkownika lub zezwolić użytkownikowi na wyrażenie zgody. Użytkownik ma możliwość wyrażenia zgody tylko wtedy, gdy administrator na to zezwala.
>
>

W tym momencie użytkownik jest proszony o wprowadzenie swoich poświadczeń i zgody na uprawnienia wymagane przez aplikację w witrynie Azure Portal. Gdy użytkownik uwierzytelnia i udziela zgody, usługa Azure AD `redirect_uri` wysyła odpowiedź do aplikacji na adres w żądaniu z kodem.

### <a name="successful-response"></a>Skuteczna reakcja
Pomyślna odpowiedź może wyglądać następująco:

```
GET  HTTP/1.1 302 Found
Location: http://localhost:12345/?code= AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF&state=D79E5777-702E-4260-9A62-37F75FF22CCE
```

| Parametr | Opis |
| --- | --- |
| admin_consent |Wartość true jest true, jeśli administrator wyraził zgodę na żądanie zgody monit. |
| kod |Kod autoryzacji, o który prosiła aplikacja. Aplikacja może użyć kodu autoryzacji, aby zażądać tokenu dostępu dla zasobu docelowego. |
| session_state |Unikatowa wartość identyfikująca bieżącą sesję użytkownika. Ta wartość jest identyfikatorem GUID, ale powinny być traktowane jako nieprzezroczyste wartości, która jest przekazywana bez badania. |
| state |Jeśli parametr stanu znajduje się w żądaniu, ta sama wartość powinna pojawić się w odpowiedzi. Jest dobrą praktyką dla aplikacji, aby sprawdzić, czy wartości stanu w żądaniu i odpowiedzi są identyczne przed użyciem odpowiedzi. Pomaga to wykryć [ataki csrf (Cross-Site Request Forgery)](https://tools.ietf.org/html/rfc6749#section-10.12) na klienta. |

### <a name="error-response"></a>Odpowiedź na błąd
Odpowiedzi na błędy mogą być `redirect_uri` również wysyłane do tak, że aplikacja może obsługiwać je odpowiednio.

```
GET http://localhost:12345/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parametr | Opis |
| --- | --- |
| error |Wartość kodu błędu zdefiniowana w sekcji 5.2 [programu OAuth 2.0 Authorization Framework](https://tools.ietf.org/html/rfc6749). W następnej tabeli opisano kody błędów zwracane przez usługę Azure AD. |
| error_description |Bardziej szczegółowy opis błędu. Ten komunikat nie jest przeznaczony do przyjaznego dla użytkownika końcowego. |
| state |Wartość stanu jest losowo wygenerowaną nieużywaną wartością, która jest wysyłana w żądaniu i zwracana w odpowiedzi, aby zapobiec atakom fałszerskości żądań między lokacjami (CSRF). |

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

## <a name="use-the-authorization-code-to-request-an-access-token"></a>Użyj kodu autoryzacji, aby zażądać tokenu dostępu
Teraz, po nabyciu kodu autoryzacji i uzyskałeś uprawnienia przez użytkownika, można zrealizować kod tokenu dostępu do `/token` żądanego zasobu, wysyłając żądanie POST do punktu końcowego:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded
grant_type=authorization_code
&client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA
&redirect_uri=https%3A%2F%2Flocalhost%3A12345
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=p@ssw0rd

//NOTE: client_secret only required for web apps
```

| Parametr |  | Opis |
| --- | --- | --- |
| Dzierżawy |wymagany |Wartość `{tenant}` w ścieżce żądania może służyć do kontrolowania, kto może zalogować się do aplikacji. Dozwolone wartości to identyfikatory dzierżawy, `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` `contoso.onmicrosoft.com` na `common` przykład lub tokeny niezależne od dzierżawy |
| client_id |wymagany |Identyfikator aplikacji przypisany do aplikacji po zarejestrowaniu go w usłudze Azure AD. Można to znaleźć w witrynie Azure portal. Identyfikator aplikacji jest wyświetlany w ustawieniach rejestracji aplikacji. |
| grant_type |wymagany |Musi `authorization_code` być dla przepływu kodu autoryzacji. |
| kod |wymagany |Nabyte `authorization_code` w poprzedniej sekcji |
| redirect_uri |wymagany | Zarejestrowany `redirect_uri`w aplikacji klienckiej. |
| client_secret |wymagane dla aplikacji internetowych, niedozwolone dla klientów publicznych |Klucze tajne aplikacji utworzone w witrynie Azure Portal dla aplikacji w obszarze **Klucze**. Nie można go używać w aplikacji natywnej (klient publiczny), ponieważ nie można niezawodnie przechowywać client_secrets na urządzeniach. Jest to wymagane dla aplikacji sieci web i interfejsów API sieci Web `client_secret` (wszystkich klientów poufnych), które mają możliwość bezpiecznego przechowywania po stronie serwera. Client_secret powinny być zakodowane w adresie URL przed wysłaniem. |
| zasób | zalecane |Identyfikator URI identyfikatora aplikacji docelowego interfejsu API sieci web (zasobu zabezpieczonego). Aby znaleźć identyfikator URI identyfikatora aplikacji, w portalu Azure portal kliknij pozycję **Azure Active Directory**, kliknij pozycję **Rejestracje aplikacji**, otwórz stronę **Ustawienia** aplikacji, a następnie kliknij pozycję **Właściwości**. Może to być również `https://graph.microsoft.com`zasób zewnętrzny, taki jak . Jest to wymagane w jednym z żądań autoryzacji lub tokenu. Aby upewnić się, że mniej monitów o uwierzytelnienie umieść go w żądaniu autoryzacji, aby upewnić się, że zgoda jest odbierana od użytkownika. Jeśli zarówno w żądaniu autoryzacji, jak i żądaniu tokenu parametry zasobu muszą być zgodne. | 
| code_verifier | optional | Ten sam code_verifier, który został użyty do uzyskania authorization_code. Wymagane, jeśli PKCE był używany w żądaniu udzielenia kodu autoryzacji. Aby uzyskać więcej informacji, zobacz [PKCE RFC](https://tools.ietf.org/html/rfc7636)   |

Aby znaleźć identyfikator URI identyfikatora aplikacji, w portalu Azure portal kliknij pozycję **Azure Active Directory**, kliknij pozycję **Rejestracje aplikacji**, otwórz stronę **Ustawienia** aplikacji, a następnie kliknij pozycję **Właściwości**.

### <a name="successful-response"></a>Skuteczna reakcja
Usługa Azure AD zwraca [token dostępu](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) po pomyślnej odpowiedzi. Aby zminimalizować wywołania sieciowe z aplikacji klienckiej i ich skojarzone opóźnienie, aplikacja kliencka powinna buforować tokeny dostępu dla okresu istnienia tokenu określonego w odpowiedzi OAuth 2.0. Aby określić okres istnienia tokenu, należy użyć wartości lub `expires_in` `expires_on` parametrów.

Jeśli zasób interfejsu `invalid_token` API sieci web zwraca kod błędu, może to oznaczać, że zasób ustalił, że token wygasł. Jeśli czasy zegara klienta i zasobu są różne (znany jako "pochylenie czasu"), zasób może uznać token, który ma zostać wygasł, zanim token zostanie wyczyszczony z pamięci podręcznej klienta. W takim przypadku wyczyść token z pamięci podręcznej, nawet jeśli nadal znajduje się w jego obliczonym okresie istnienia.

Pomyślna odpowiedź może wyglądać następująco:

```
{
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1388444763",
  "resource": "https://service.contoso.com/",
  "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA",
  "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
  "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ."
}

```

| Parametr | Opis |
| --- | --- |
| access_token |Żądany token dostępu.  Jest to nieprzezroczysty ciąg — zależy od tego, co zasób oczekuje, aby otrzymać i nie jest przeznaczony dla klienta, aby spojrzeć na. Aplikacja może używać tego tokenu do uwierzytelniania do zabezpieczonego zasobu, takiego jak internetowy interfejs API. |
| token_type |Wskazuje wartość typu tokenu. Jedynym typem, który obsługuje usługi Azure AD jest Bearer. Aby uzyskać więcej informacji na temat tokenów na okaziciela, zobacz [Framework autoryzacji OAuth2.0: Użycie tokenu nośnika (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt) |
| expires_in |Jak długo token dostępu jest prawidłowy (w sekundach). |
| expires_on |Czas wygaśnięcia tokenu dostępu. Data jest reprezentowana jako liczba sekund od 1970-01-01T0:0:0Z UTC do czasu wygaśnięcia. Ta wartość jest używana do określenia okresu istnienia tokenów buforowanych. |
| zasób |Identyfikator URI identyfikatora aplikacji internetowego interfejsu API (zasobu zabezpieczonego). |
| scope |Uprawnienia personifikacji przyznane aplikacji klienckiej. Domyślnym uprawnieniem jest `user_impersonation`. Właściciel zabezpieczonego zasobu może rejestrować dodatkowe wartości w usłudze Azure AD. |
| refresh_token |Token odświeżania OAuth 2.0. Aplikacja może użyć tego tokenu, aby uzyskać dodatkowe tokeny dostępu po wygaśnięciu bieżącego tokenu dostępu. Tokeny odświeżania są długotrwałe i mogą służyć do zachowania dostępu do zasobów przez dłuższy czas. |
| id_token |Niepodpisany token JSON Web Token (JWT) reprezentujący [token identyfikatora](../develop/id-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json). Aplikacja może base64Url dekodować segmenty tego tokenu, aby zażądać informacji o użytkowniku, który się zalogował. Aplikacja może buforować wartości i wyświetlać je, ale nie należy polegać na nich dla żadnych granic autoryzacji lub zabezpieczeń. |

Aby uzyskać więcej informacji na temat tokenów internetowych JSON, zobacz [specyfikację roboczą JWT IETF](https://go.microsoft.com/fwlink/?LinkId=392344).   Aby dowiedzieć `id_tokens`się więcej o , zobacz [przepływ OpenID Connect w wersji 1.0](v1-protocols-openid-connect-code.md).

### <a name="error-response"></a>Odpowiedź na błąd
Błędy punktu końcowego wystawienia tokenu są kodami błędów HTTP, ponieważ klient wywołuje punkt końcowy wystawienia tokenu bezpośrednio. Oprócz kodu stanu HTTP punkt końcowy wystawienia tokenu usługi Azure AD zwraca również dokument JSON z obiektami opisującymi błąd.

Przykładowa odpowiedź na błąd może wyglądać następująco:

```
{
  "error": "invalid_grant",
  "error_description": "AADSTS70002: Error validating credentials. AADSTS70008: The provided authorization code or refresh token is expired. Send a new interactive authorization request for this user and resource.\r\nTrace ID: 3939d04c-d7ba-42bf-9cb7-1e5854cdce9e\r\nCorrelation ID: a8125194-2dc8-4078-90ba-7b6592a7f231\r\nTimestamp: 2016-04-11 18:00:12Z",
  "error_codes": [
    70002,
    70008
  ],
  "timestamp": "2016-04-11 18:00:12Z",
  "trace_id": "3939d04c-d7ba-42bf-9cb7-1e5854cdce9e",
  "correlation_id": "a8125194-2dc8-4078-90ba-7b6592a7f231"
}
```
| Parametr | Opis |
| --- | --- |
| error |Ciąg kodu błędu, który może służyć do klasyfikowania typów błędów, które występują i może służyć do reagowania na błędy. |
| error_description |Określony komunikat o błędzie, który może pomóc deweloperowi zidentyfikować główną przyczynę błędu uwierzytelniania. |
| error_codes |Lista kodów błędów specyficznych dla STS, które mogą pomóc w diagnostyce. |
| sygnatura czasowa |Czas, w którym wystąpił błąd. |
| Trace_id |Unikatowy identyfikator żądania, który może pomóc w diagnostyce. |
| correlation_id |Unikatowy identyfikator żądania, który może pomóc w diagnostyce między składnikami. |

#### <a name="http-status-codes"></a>Kody stanu HTTP
W poniższej tabeli wymieniono kody stanu HTTP, które zwraca punkt końcowy wystawiania tokenu. W niektórych przypadkach kod błędu jest wystarczający do opisania odpowiedzi, ale jeśli występują błędy, należy przeanalizować towarzyszący dokument JSON i sprawdzić jego kod błędu.

| Kod HTTP | Opis |
| --- | --- |
| 400 |Domyślny kod HTTP. Używany w większości przypadków i jest zazwyczaj ze względu na nieprawidłowo sformułowane żądanie. Napraw i ponownie prześlij żądanie. |
| 401 |Uwierzytelnianie nie powiodło się. Na przykład żądanie brakuje parametru client_secret. |
| 403 |Autoryzacja nie powiodła się. Na przykład użytkownik nie ma uprawnień dostępu do zasobu. |
| 500 |Wystąpił błąd wewnętrzny w usłudze. Ponów próbę żądania. |

#### <a name="error-codes-for-token-endpoint-errors"></a>Kody błędów dla błędów tokenu końcowego
| Kod błędu | Opis | Akcja klienta |
| --- | --- | --- |
| invalid_request |Błąd protokołu, taki jak brak wymaganego parametru. |Napraw i ponownie prześlij żądanie |
| invalid_grant |Kod autoryzacji jest nieprawidłowy lub wygasł. |Wypróbuj nowe żądanie `/authorize` do punktu końcowego |
| unauthorized_client |Uwierzytelniony klient nie jest upoważniony do używania tego typu udzielenia autoryzacji. |Zwykle dzieje się tak, gdy aplikacja kliencka nie jest zarejestrowana w usłudze Azure AD lub nie jest dodawana do dzierżawy usługi Azure AD użytkownika. Aplikacja może monitować użytkownika o instrukcje dotyczące instalowania aplikacji i dodawania jej do usługi Azure AD. |
| invalid_client |Uwierzytelnianie klienta nie powiodło się. |Poświadczenia klienta są nieprawidłowe. Aby naprawić, administrator aplikacji aktualizuje poświadczenia. |
| unsupported_grant_type |Serwer autoryzacji nie obsługuje typu udzielania autoryzacji. |Zmień typ dotacji w żądaniu. Ten typ błędu powinien występować tylko podczas opracowywania i być wykryty podczas wstępnego testowania. |
| invalid_resource |Zasób docelowy jest nieprawidłowy, ponieważ nie istnieje, usługa Azure AD nie może go znaleźć lub nie jest poprawnie skonfigurowany. |Oznacza to, że zasób, jeśli istnieje, nie został skonfigurowany w dzierżawie. Aplikacja może monitować użytkownika o instrukcje dotyczące instalowania aplikacji i dodawania jej do usługi Azure AD. |
| interaction_required |Żądanie wymaga interakcji z użytkownikiem. Na przykład wymagany jest dodatkowy krok uwierzytelniania. | Zamiast żądania nieinterakcyjnego ponów próbę użycia żądania autoryzacji interaktywnej dla tego samego zasobu. |
| temporarily_unavailable |Serwer jest tymczasowo zbyt zajęty, aby obsłużyć żądanie. |Ponów próbę żądania. Aplikacja kliencka może wyjaśnić użytkownikowi, że jego odpowiedź jest opóźniona z powodu stanu tymczasowego. |

## <a name="use-the-access-token-to-access-the-resource"></a>Użyj tokenu dostępu, aby uzyskać dostęp do zasobu
Teraz, po pomyślnym `access_token`nabyciu programu , można użyć tokenu w żądaniach `Authorization` do interfejsów API sieci Web, dołączając go do nagłówka. Specyfikacja [RFC 6750](https://www.rfc-editor.org/rfc/rfc6750.txt) wyjaśnia, jak używać tokenów nośnych w żądaniach HTTP w celu uzyskania dostępu do chronionych zasobów.

### <a name="sample-request"></a>Przykładowe żądanie
```
GET /data HTTP/1.1
Host: service.contoso.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ
```

### <a name="error-response"></a>Odpowiedź na błąd
Zabezpieczone zasoby implementujące kody stanu HTTP programu RFC 6750. Jeśli żądanie nie zawiera poświadczeń uwierzytelniania lub `WWW-Authenticate` brakuje tokenu, odpowiedź zawiera nagłówek. Gdy żądanie nie powiedzie się, serwer zasobów odpowiada kodem stanu HTTP i kodem błędu.

Poniżej przedstawiono przykład nieudanej odpowiedzi, gdy żądanie klienta nie zawiera tokenu nośnika:

```
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer authorization_uri="https://login.microsoftonline.com/contoso.com/oauth2/authorize",  error="invalid_token",  error_description="The access token is missing.",
```

#### <a name="error-parameters"></a>Parametry błędu
| Parametr | Opis |
| --- | --- |
| authorization_uri |Identyfikator URI (fizyczny punkt końcowy) serwera autoryzacji. Ta wartość jest również używana jako klucz odnośnika, aby uzyskać więcej informacji o serwerze z punktu końcowego odnajdywania. <p><p> Klient musi sprawdzić, czy serwer autoryzacji jest zaufany. Gdy zasób jest chroniony przez usługę Azure AD, wystarczy `https://login.microsoftonline.com` sprawdzić, czy adres URL zaczyna się od lub innej nazwy hosta, która obsługuje usługę Azure AD. Zasób specyficzny dla dzierżawy powinien zawsze zwracać identyfikator URI autoryzacji specyficznej dla dzierżawy. |
| error |Wartość kodu błędu zdefiniowana w sekcji 5.2 [programu OAuth 2.0 Authorization Framework](https://tools.ietf.org/html/rfc6749). |
| error_description |Bardziej szczegółowy opis błędu. Ten komunikat nie jest przeznaczony do przyjaznego dla użytkownika końcowego. |
| resource_id |Zwraca unikatowy identyfikator zasobu. Aplikacja kliencka może użyć tego identyfikatora jako wartości parametru, `resource` gdy żąda tokenu dla zasobu. <p><p> Ważne jest, aby aplikacja kliencka zweryfikowała tę wartość, w przeciwnym razie złośliwa usługa może wywołać atak **z podniesieniem uprawnień** <p><p> Zalecaną strategią zapobiegania atakowi jest `resource_id` sprawdzenie, czy jest to zgodne z bazą adresu URL interfejsu API sieci web, do której dostęp jest dostępny. Na przykład, `https://service.contoso.com/data` jeśli jest dostępny, `resource_id` `https://service.contoso.com/`może być . Aplikacja kliencka `resource_id` musi odrzucić, który nie zaczyna się od podstawowego adresu URL, chyba że istnieje niezawodny alternatywny sposób weryfikacji identyfikatora. |

#### <a name="bearer-scheme-error-codes"></a>Kody błędów schematu na okaziciela
Specyfikacja RFC 6750 definiuje następujące błędy dla zasobów, które używają nagłówka WWW-Authenticate i schematu nadźwignia w odpowiedzi.

| Kod stanu HTTP | Kod błędu | Opis | Akcja klienta |
| --- | --- | --- | --- |
| 400 |invalid_request |Żądanie nie jest dobrze sformułowane. Na przykład może brakować parametru lub dwa razy przy użyciu tego samego parametru. |Napraw błąd i ponów próbę żądania. Ten typ błędu powinien występować tylko podczas opracowywania i być wykryty podczas wstępnego testowania. |
| 401 |invalid_token |Token dostępu jest brak, nieprawidłowy lub jest odwołany. Wartość parametru error_description zawiera dodatkowe szczegóły. |Zażądaj nowego tokenu z serwera autoryzacji. Jeśli nowy token nie powiedzie się, wystąpił nieoczekiwany błąd. Wyślij komunikat o błędzie do użytkownika i ponów próbę po losowych opóźnieniach. |
| 403 |insufficient_scope |Token dostępu nie zawiera uprawnień personifikacji wymaganych do uzyskania dostępu do zasobu. |Wyślij nowe żądanie autoryzacji do punktu końcowego autoryzacji. Jeśli odpowiedź zawiera parametr zakresu, należy użyć wartości zakresu w żądaniu do zasobu. |
| 403 |insufficient_access |Temat tokenu nie ma uprawnień, które są wymagane do uzyskania dostępu do zasobu. |Monituj użytkownika o użycie innego konta lub żądanie uprawnień do określonego zasobu. |

## <a name="refreshing-the-access-tokens"></a>Odświeżanie tokenów dostępu

Tokeny dostępu są krótkotrwałe i muszą zostać odświeżone po wygaśnięciu, aby kontynuować dostęp do zasobów. Można odświeżyć, `access_token` `POST` przesyłając kolejne `/token` żądanie do punktu końcowego, ale tym razem podając `refresh_token` zamiast `code`.  Tokeny odświeżania są prawidłowe dla wszystkich zasobów, do których klient otrzymał już zgodę `resource=https://graph.microsoft.com` na dostęp — w związku `resource=https://contoso.com/api`z tym token odświeżania wystawiony na żądanie może zostać użyty do żądania nowego tokenu dostępu dla . 

Tokeny odświeżania nie mają określonych okresów istnienia. Zazwyczaj okresy istnienia tokenów odświeżania są stosunkowo długie. Jednak w niektórych przypadkach tokeny odświeżania wygasają, są odwoływane lub nie mają wystarczających uprawnień do żądanej akcji. Aplikacja musi oczekiwać i obsługiwać błędy zwracane przez punkt końcowy wystawienia tokenu poprawnie.

Po otrzymaniu odpowiedzi z błędem tokenu odświeżania, odrzucić bieżący token odświeżania i zażądać nowego kodu autoryzacji lub tokenu dostępu. W szczególności podczas korzystania z tokenu odświeżania w przepływie grantu kodu autoryzacji, jeśli otrzymasz odpowiedź z kodami `interaction_required` lub `invalid_grant` błędami, odrzuć token odświeżania i zażądaj nowego kodu autoryzacji.

Przykładowe żądanie do punktu końcowego **specyficzne dla dzierżawy** (można również użyć **wspólnego** punktu końcowego), aby uzyskać nowy token dostępu przy użyciu tokenu odświeżania wygląda następująco:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```

### <a name="successful-response"></a>Skuteczna reakcja
Pomyślna odpowiedź tokenu będzie wyglądać następująco:

```
{
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1460404526",
  "resource": "https://service.contoso.com/",
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "refresh_token": "AwABAAAAv YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl PM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfmVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA"
}
```
| Parametr | Opis |
| --- | --- |
| token_type |Typ tokenu. Jedyną obsługiwaną wartością jest **okaziciela**. |
| expires_in |Pozostały okres istnienia tokenu w sekundach. Typowa wartość to 3600 (jedna godzina). |
| expires_on |Data i godzina wygaśnięcia tokenu. Data jest reprezentowana jako liczba sekund od 1970-01-01T0:0:0Z UTC do czasu wygaśnięcia. |
| zasób |Identyfikuje zabezpieczony zasób, do którego można uzyskać dostęp token dostępu. |
| scope |Uprawnienia personifikacji przyznane natywnej aplikacji klienckiej. Uprawnienie domyślne to **user_impersonation**. Właściciel zasobu docelowego można zarejestrować alternatywne wartości w usłudze Azure AD. |
| access_token |Nowy token dostępu, który został poproszony. |
| refresh_token |Nowy refresh_token OAuth 2.0, który może służyć do żądania nowych tokenów dostępu, gdy ten w tej odpowiedzi wygasa. |

### <a name="error-response"></a>Odpowiedź na błąd
Przykładowa odpowiedź na błąd może wyglądać następująco:

```
{
  "error": "invalid_resource",
  "error_description": "AADSTS50001: The application named https://foo.microsoft.com/mail.read was not found in the tenant named 295e01fc-0c56-4ac3-ac57-5d0ed568f872. This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant. You might have sent your authentication request to the wrong tenant.\r\nTrace ID: ef1f89f6-a14f-49de-9868-61bd4072f0a9\r\nCorrelation ID: b6908274-2c58-4e91-aea9-1f6b9c99347c\r\nTimestamp: 2016-04-11 18:59:01Z",
  "error_codes": [
    50001
  ],
  "timestamp": "2016-04-11 18:59:01Z",
  "trace_id": "ef1f89f6-a14f-49de-9868-61bd4072f0a9",
  "correlation_id": "b6908274-2c58-4e91-aea9-1f6b9c99347c"
}
```

| Parametr | Opis |
| --- | --- |
| error |Ciąg kodu błędu, który może służyć do klasyfikowania typów błędów, które występują i może służyć do reagowania na błędy. |
| error_description |Określony komunikat o błędzie, który może pomóc deweloperowi zidentyfikować główną przyczynę błędu uwierzytelniania. |
| error_codes |Lista kodów błędów specyficznych dla STS, które mogą pomóc w diagnostyce. |
| sygnatura czasowa |Czas, w którym wystąpił błąd. |
| Trace_id |Unikatowy identyfikator żądania, który może pomóc w diagnostyce. |
| correlation_id |Unikatowy identyfikator żądania, który może pomóc w diagnostyce między składnikami. |

Aby uzyskać opis kodów błędów i zalecanej akcji klienta, zobacz [Kody błędów dla błędów tokenu końcowego](#error-codes-for-token-endpoint-errors).

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o punkcie końcowym usługi Azure AD w wersji 1.0 oraz o dodaniu uwierzytelniania i autoryzacji do aplikacji sieci Web i interfejsów API sieci Web, zobacz [przykładowe aplikacje](sample-v1-code.md).
