---
title: Rozumienie przepływu kodu autoryzacji OAuth 2.0 w usłudze Azure AD
description: W tym artykule opisano, jak używać wiadomości HTTP do autoryzacji dostępu do aplikacji internetowych i internetowych interfejsów API w dzierżawie za pomocą usługi Azure Active Directory i OAuth 2.0.
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e05d79773cfd2ebae8047e75d41684de9101787a
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962182"
---
# <a name="authorize-access-to-azure-active-directory-web-applications-using-the-oauth-20-code-grant-flow"></a>Autoryzowanie dostępu do aplikacji sieci web usługi Azure Active Directory przy użyciu przepływie przyznawania kodu OAuth 2.0

Azure Active Directory (Azure AD) używa protokołu OAuth 2.0 umożliwia autoryzowanie dostępu do aplikacji internetowych i internetowych interfejsów API w dzierżawie usługi Azure AD. Ten przewodnik jest niezależny od języka i opisano, jak wysyłać i odbierać komunikaty HTTP bez użycia jakichkolwiek naszych [bibliotek typu open-source](active-directory-authentication-libraries.md).

Przepływ kodu autoryzacji OAuth 2.0 opisano w [sekcji 4.1 specyfikacji protokołu OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.1). Jest on używany do wykonywania uwierzytelniania i autoryzacji w większości typów aplikacji, w tym aplikacje sieci web i natywnie zainstalowanych aplikacji.

[!INCLUDE [active-directory-protocols-getting-started](../../../includes/active-directory-protocols-getting-started.md)]

## <a name="oauth-20-authorization-flow"></a>Przepływ autoryzacji OAuth 2.0

Na wysokim poziomie przepływu autoryzacji całej aplikacji wygląda nieco następująco:

![Przepływ kodu autoryzacji OAuth](./media/v1-protocols-oauth-code/active-directory-oauth-code-flow-native-app.png)

## <a name="request-an-authorization-code"></a>Żądanie zwróciło kod autoryzacji

Przepływ kodu autoryzacji zaczyna się od klienta kierowanie użytkowników do `/authorize` punktu końcowego. W tym żądaniu klient wskazuje uprawnienia niezbędne do uzyskania przez użytkownika. Punkt końcowy autoryzacji OAuth 2.0 można uzyskać dzierżawy, wybierając **rejestracji aplikacji > punkty końcowe** w witrynie Azure portal.

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
| tenant |wymagane |`{tenant}` Wartość w polu Ścieżka żądania może służyć do kontrolowania, kto może zalogować się do aplikacji. Dozwolone są wartości identyfikatorów dzierżawy, na przykład `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` lub `contoso.onmicrosoft.com` lub `common` tokenów niezależne od dzierżawcy |
| client_id |wymagane |Identyfikator aplikacji przypisany do aplikacji podczas rejestrowania za pomocą usługi Azure AD. To można znaleźć w witrynie Azure Portal. Kliknij przycisk **usługi Azure Active Directory** na pasku bocznym usługi kliknij **rejestracje aplikacji**i wybierz aplikację. |
| response_type |wymagane |Musi zawierać `code` dla przepływ kodu autoryzacji. |
| redirect_uri |Zalecane |Redirect_uri aplikacji, gdzie odpowiedzi uwierzytelniania mogą być wysyłane i odbierane przez aplikację. Dokładnie musi odpowiadać jednej z redirect_uris, zarejestrowanych w portalu, z wyjątkiem musi być zakodowane w adresie url. W przypadku aplikacji natywnych i mobilne, należy używać wartość domyślną `urn:ietf:wg:oauth:2.0:oob`. |
| response_mode |opcjonalne |Określa metodę, które mają być używane do wysyłania wynikowy token wstecz do swojej aplikacji. Może być `query`, `fragment`, lub `form_post`. `query` zawiera kod jako parametr ciągu zapytania identyfikatora URI przekierowania. W przypadku żądania tokenu Identyfikatora, przy użyciu niejawnego przepływu, nie można użyć `query` określonej [Specyfikacja OpenID](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations). Jeśli masz żądania tylko kod, możesz użyć `query`, `fragment`, lub `form_post`. `form_post` wykonuje WPIS zawierający kod, aby identyfikator URI przekierowania. Wartość domyślna to `query` przepływu kodu.  |
| stan |Zalecane |Wartość uwzględnione w żądaniu, która jest także zwracany w odpowiedzi tokenu. Losowo generowany unikatową wartość jest zwykle używany podczas [zapobieganie atakom na fałszerstwo żądania międzywitrynowego](https://tools.ietf.org/html/rfc6749#section-10.12). Stan również jest używany do kodowania informacje o stanie użytkownika w aplikacji, zanim żądanie uwierzytelniania wystąpił, takich jak strony lub widoku, które znajdowały się w. |
| zasób | Zalecane |Identyfikator URI Identyfikatora aplikacji docelowej internetowego interfejsu API (zabezpieczono zasób). Aby znaleźć identyfikator URI aplikacji w witrynie Azure Portal, kliknij **usługi Azure Active Directory**, kliknij przycisk **rejestracje aplikacji**, Otwórz aplikację **ustawienia** stronie, a następnie kliknij przycisk  **Właściwości**. Może to być również zasób zewnętrzny, takie jak `https://graph.microsoft.com`. Jest to wymagane w jedno autoryzacji lub żądania tokenu. Do zapewnienia uwierzytelniania mniejszą liczbę monitów umieść go w żądaniu autoryzacji, aby upewnić się, że otrzymaniu zgody przez użytkownika. |
| zakres | **ignorowane** | W przypadku aplikacji usługi Azure AD w wersji 1, zakresy muszą być skonfigurowane statycznie w witrynie Azure Portal w obszarze aplikacje **ustawienia**, **wymagane uprawnienia**. |
| wiersz |opcjonalne |Wskazuje typ interakcji z użytkownikiem, który jest wymagany.<p> Prawidłowe wartości to: <p> *Zaloguj się*: Użytkownik powinien monitowany ponownego uwierzytelnienia. <p> *select_account*: Użytkownik jest monitowany o wybranie konta, przerywania logowania jednokrotnego na. Użytkownik może wybrać istniejące konto zalogowanego, wprowadź swoje poświadczenia dla konta zapamiętanych lub chce użyć całkowicie innego konta. <p> *Zgoda*: Zgoda użytkownika przyznano, ale musi zostać zaktualizowany. Użytkownik powinien monit o zgodę. <p> *admin_consent*: Administrator powinien być monitowany o zgody w imieniu wszystkich użytkowników w organizacji |
| login_hint |opcjonalne |Można wstępnie wypełnić pola Adres e-mail/nazwy użytkownika strony logowania dla użytkownika, jeśli znasz swoją nazwę użytkownika, wcześniej. Aplikacje często tego parametru należy użyć podczas ponownego uwierzytelniania, mających już wyodrębnione nazwy użytkownika z poprzedniego logowania za pomocą `preferred_username` oświadczenia. |
| Element domain_hint |opcjonalne |Zawiera wskazówki dotyczące dzierżawy lub domeny, które użytkownik powinien używać do logowania. Element domain_hint wartość zarejestrowanej domeny dla dzierżawy. Jeśli dzierżawa jest sfederowana do katalogu lokalnego, AAD przekierowuje do określonej dzierżawy serwera federacyjnego. |
| code_challenge_method | Zalecane    | Metoda użyta do zakodowania `code_verifier` dla `code_challenge` parametru. Może być jednym z `plain` lub `S256`. Jeśli wykluczone, `code_challenge` przyjęto, że będzie mieć postać zwykłego tekstu, jeśli `code_challenge` jest dołączony. Obsługuje platformy Azure 1.0 w usłudze AAD, zarówno `plain` i `S256`. Aby uzyskać więcej informacji, zobacz [PKCE RFC](https://tools.ietf.org/html/rfc7636). |
| code_challenge        | Zalecane    | Wykorzystywany do zabezpieczenia przydziałów kod autoryzacji za pomocą klucza dowód kod programem Exchange (PKCE) od klienta macierzystego lub publicznej. Jeśli wymagane `code_challenge_method` jest dołączony. Aby uzyskać więcej informacji, zobacz [PKCE RFC](https://tools.ietf.org/html/rfc7636). |

> [!NOTE]
> Jeśli użytkownik jest częścią organizacji, administrator organizacji może wyrazić zgodę odrzucić w imieniu użytkownika lub zezwolić na użytkownika o zgodę. Użytkownik może wskazać opcja zgody tylko wtedy, gdy administrator to umożliwia.
>
>

W tym momencie użytkownik jest proszony o wprowadzenie poświadczeń i wyrazić zgodę na uprawnienia wymagane przez aplikację w witrynie Azure Portal. Po użytkownik jest uwierzytelniany i przyznaje zgody, usługa Azure AD wysyła odpowiedź do aplikacji w `redirect_uri` adres w żądaniu z kodem.

### <a name="successful-response"></a>Pomyślna odpowiedź
Odpowiedź oznaczająca Powodzenie może wyglądać następująco:

```
GET  HTTP/1.1 302 Found
Location: http://localhost:12345/?code= AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF&state=D79E5777-702E-4260-9A62-37F75FF22CCE
```

| Parametr | Opis |
| --- | --- |
| admin_consent |Ma wartość True, jeśli administrator wyraził zgodę na monit o wyrażenie zgody żądania. |
| kod |Kod autoryzacji, który zażądał aplikacji. Aplikacja może używać kod autoryzacji do żądania tokenu dostępu dla zasobu docelowego. |
| session_state |Unikatowa wartość, która identyfikuje bieżącą sesję użytkownika. Ta wartość jest identyfikatorem GUID, ale powinien być traktowany jako nieprzezroczysta wartość przekazywaną bez badania. |
| stan |Jeśli parametr Stan jest uwzględniony w żądaniu, tę samą wartość powinna pojawić się w odpowiedzi. Jest dobrym rozwiązaniem dla aplikacji, aby zweryfikować, że wartości stanu żądania i odpowiedzi są identyczne, przed rozpoczęciem korzystania z odpowiedzi. Ułatwia to wykrywanie [ataki fałszerstwo żądania Międzywitrynowego Międzywitrynowych](https://tools.ietf.org/html/rfc6749#section-10.12) względem klienta. |

### <a name="error-response"></a>Odpowiedzi na błąd
Odpowiedzi na błędy mogą być również wysyłane do `redirect_uri` tak, aby aplikacja może je odpowiednio obsługiwać.

```
GET http://localhost:12345/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parametr | Opis |
| --- | --- |
| błąd |Wartość kodu błędu, zdefiniowane w sekcji 5.2 [OAuth 2.0 autoryzacji Framework](https://tools.ietf.org/html/rfc6749). W następnej tabeli opisano kody błędów, które zwraca usługi Azure AD. |
| error_description |Bardziej szczegółowy opis błędu. Ten komunikat nie ma być przyjazne dla użytkownika końcowego. |
| stan |Wartość stanu jest generowany losowo-ponownie wartość, która jest wysyłane w żądaniu i zwracany w odpowiedzi na fałszerstwo żądania międzywitrynowego (CSRF) atakami. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Kody błędów dla błędów punktu końcowego autoryzacji
W poniższej tabeli opisano różne kody błędów, które mogą być zwracane w `error` parametru odpowiedzi na błąd.

| Kod błędu | Opis | Akcja klienta |
| --- | --- | --- |
| invalid_request |Błąd protokołu, np. Brak wymaganego parametru. |Usuń i ponownie prześlij żądanie. Jest to błąd programowania i zwykle jest przechwytywane w początkowej fazie testowania. |
| unauthorized_client |Aplikacja kliencka nie ma zezwolenia na żądanie kod autoryzacji. |Dzieje się tak zazwyczaj, gdy aplikacja kliencka nie jest zarejestrowany w usłudze Azure AD lub nie została dodana do dzierżawy usługi Azure AD przez użytkownika. Aplikacja może monitować użytkownika przy użyciu instrukcji dotyczących instalowania aplikacji i dodanie go do usługi Azure AD. |
| access_denied |Właściciel zasobu odmowa zgody |Aplikacja kliencka może powiadomić użytkownika, którego nie można kontynuować, chyba że użytkownik wyrazi na to zgody. |
| unsupported_response_type |Serwer autoryzacji nie obsługuje typu odpowiedzi w żądaniu. |Usuń i ponownie prześlij żądanie. Jest to błąd programowania i zwykle jest przechwytywane w początkowej fazie testowania. |
| server_error |Serwer napotkał nieoczekiwany błąd. |Ponów żądanie. Te błędy mogą być wynikiem tymczasowe warunki. Aplikacja kliencka może wyjaśnić użytkownikowi, że odpowiedzi przez punkt końcowy jest opóźnione ze względu na tymczasowy błąd. |
| temporarily_unavailable |Serwer jest tymczasowo zbyt zajęty, aby obsłużyć żądanie. |Ponów żądanie. Aplikacja kliencka może wyjaśnić użytkownikowi, że odpowiedzi przez punkt końcowy jest opóźnione ze względu na tymczasowy warunek. |
| invalid_resource |Zasób docelowy jest nieprawidłowy, ponieważ nie istnieje, usługa Azure AD nie może okazać się lub nie jest poprawnie skonfigurowana. |Oznacza to, że zasób, jeśli istnieje, nie został skonfigurowany w ramach dzierżawy. Aplikacja może monitować użytkownika przy użyciu instrukcji dotyczących instalowania aplikacji i dodanie go do usługi Azure AD. |

## <a name="use-the-authorization-code-to-request-an-access-token"></a>Użyj kodu autoryzacji do wysłania żądania tokenu dostępu
Po nabyciu kod autoryzacji i ma odpowiednie uprawnienia przyznane przez użytkownika można zrealizować kodu dla tokenu dostępu do żądanego zasobu przez wysłanie żądania POST na `/token` punktu końcowego:

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
| tenant |wymagane |`{tenant}` Wartość w polu Ścieżka żądania może służyć do kontrolowania, kto może zalogować się do aplikacji. Dozwolone są wartości identyfikatorów dzierżawy, na przykład `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` lub `contoso.onmicrosoft.com` lub `common` tokenów niezależne od dzierżawcy |
| client_id |wymagane |Identyfikator aplikacji przypisany do aplikacji podczas rejestrowania za pomocą usługi Azure AD. To można znaleźć w witrynie Azure portal. Identyfikator aplikacji jest wyświetlany w ustawieniach rejestracji aplikacji. |
| grant_type |wymagane |Musi być `authorization_code` dla przepływ kodu autoryzacji. |
| kod |wymagane |`authorization_code` Uzyskany w poprzedniej sekcji |
| redirect_uri |wymagane | A `redirect_uri`zarejestrowanych aplikacji klienta. |
| client_secret |wymagane dla aplikacji sieci web, nie jest dozwolona dla klientów publicznych |Klucz tajny aplikacji, utworzony w witrynie Azure Portal na potrzeby aplikacji w środowisku **klucze**. Nie można użyć w aplikacji macierzystej (publicznych klienta), ponieważ client_secrets nie mogą być w niezawodny sposób będą przechowywane na urządzeniach. Jest to wymagane dla aplikacji internetowych i internetowych interfejsów API (wszystkich poufnych klientów), która ma możliwość przechowywania `client_secret` bezpiecznie po stronie serwera. Wartość client_secret powinna być zakodowane w adresie URL przed wysłaniem. |
| zasób | Zalecane |Identyfikator URI Identyfikatora aplikacji docelowej internetowego interfejsu API (zabezpieczono zasób). Aby znaleźć identyfikator URI aplikacji w witrynie Azure Portal, kliknij **usługi Azure Active Directory**, kliknij przycisk **rejestracje aplikacji**, Otwórz aplikację **ustawienia** stronie, a następnie kliknij przycisk  **Właściwości**. Może to być również zasób zewnętrzny, takie jak `https://graph.microsoft.com`. Jest to wymagane w jedno autoryzacji lub żądania tokenu. Do zapewnienia uwierzytelniania mniejszą liczbę monitów umieść go w żądaniu autoryzacji, aby upewnić się, że otrzymaniu zgody przez użytkownika. Jeśli w żądaniu autoryzacji i żądania tokenu zasobu "Parametry muszą być zgodne. | 
| code_verifier | opcjonalne | Tym samym wartość parametru code_verifier użytego do uzyskania authorization_code. Wymagane, jeśli PKCE został użyty w żądaniu grant kod autoryzacji. Aby uzyskać więcej informacji, zobacz [PKCE RFC](https://tools.ietf.org/html/rfc7636)   |

Aby znaleźć identyfikator URI aplikacji w witrynie Azure Portal, kliknij **usługi Azure Active Directory**, kliknij przycisk **rejestracje aplikacji**, Otwórz aplikację **ustawienia** stronie, a następnie kliknij przycisk  **Właściwości**.

### <a name="successful-response"></a>Pomyślna odpowiedź
Usługa Azure AD zwraca [token dostępu](access-tokens.md) po pomyślnej odpowiedzi. Aby zminimalizować wywołań sieci z aplikacji klienckiej i ich skojarzone opóźnienia, aplikacja kliencka powinna tokeny dostępu pamięci podręcznej na okres istnienia tokenu, który jest określony w odpowiedzi OAuth 2.0. Aby określić czas życia tokenu, należy użyć `expires_in` lub `expires_on` wartości parametrów.

Jeśli zasobu internetowego interfejsu API zwraca `invalid_token` kodu błędu, może to oznaczać, że zasób stwierdził wygasł token. Jeśli czas zegara klienta i zasobów są różne (nazywane "niesymetryczność czasu"), zasób, warto rozważyć token wygasł, zanim token jest usuwane z pamięci podręcznej klienta. Jeśli ten problem wystąpi, wyczyść tokenu z pamięci podręcznej, nawet jeśli jest nadal w obliczeniowej okresie swojego istnienia.

Odpowiedź oznaczająca Powodzenie może wyglądać następująco:

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
| access_token |Żądany [token dostępu](access-tokens.md) jako podpisem JSON Web Token (JWT). Aplikacja może używać tego tokenu do uwierzytelniania zabezpieczonych zasobów, takich jak interfejs API sieci web. |
| token_type |Wskazuje typ tokenu. Jedynym typem, który obsługuje usługi Azure AD jest elementu nośnego. Aby uzyskać więcej informacji na temat tokenów elementu nośnego, zobacz [ramy autoryzacji OAuth 2.0: Użycie tokenu elementu nośnego (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt) |
| expires_in |Jak długo token dostępu jest prawidłowy (w sekundach). |
| expires_on |Czas wygaśnięcia tokenu dostępu. Data jest reprezentowana jako liczbę sekund od 1970-01-01T0:0:0Z UTC do czasu wygaśnięcia. Ta wartość jest używana do określenia okres istnienia tokenów buforowanych. |
| zasób |Identyfikator URI Identyfikatora aplikacji internetowego interfejsu API (zabezpieczono zasób). |
| zakres |Personifikacja uprawnienia do aplikacji klienckiej. Domyślne uprawnienia `user_impersonation`. Właściciel zabezpieczono zasób można zarejestrować dodatkowych wartości w usłudze Azure AD. |
| refresh_token |Token odświeżania OAuth 2.0. Aplikacja może używać tego tokenu, można uzyskać tokenów dodatkowych dostępu po wygaśnięciu bieżącego tokenu dostępu. Odśwież tokeny są długotrwałe i pozwala zachować dostęp do zasobów przez dłuższy czas. |
| id_token |Niepodpisane reprezentujący tokenu Web JSON (JWT) [tokenu Identyfikacyjnego](id-tokens.md). Aplikacji base64Url może zdekodować segmentów tego tokenu na żądanie informacji o użytkowniku, który jest zalogowany. Wartości w pamięci podręcznej i ich wyświetlenie aplikacji, ale nie należy polegać na nich autoryzacji lub granice zabezpieczeń. |

Aby uzyskać więcej informacji na temat tokenów sieci web JSON, zobacz [Specyfikacja wersji roboczej JWT IETF](https://go.microsoft.com/fwlink/?LinkId=392344).   Aby dowiedzieć się więcej na temat `id_tokens`, zobacz [przepływ protokołu OpenID Connect 1.0](v1-protocols-openid-connect-code.md).

### <a name="error-response"></a>Odpowiedzi na błąd
Błędy punktu końcowego wystawiania tokenów są kody błędów HTTP, ponieważ klient wywołuje punkt końcowy wystawiania tokenu bezpośrednio. Oprócz kod stanu HTTP punktu końcowego wystawiania tokenu usługi Azure AD zwraca dokument JSON z obiektami, które opisuje błąd.

Przykładowa odpowiedź błędu może wyglądać następująco:

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
| błąd |Ciągu kodu błędu, który może służyć do klasyfikowania typy błędów, które występują i może służyć do reagowania na błędy. |
| error_description |Określony komunikat o błędzie ułatwiający Deweloper Identyfikuj główne przyczyny błędu uwierzytelniania. |
| error_codes |Lista kodów błędów specyficznych dla usługi STS, które mogą pomóc w diagnostyce. |
| timestamp |Czas, w którym wystąpił błąd. |
| trace_id |Unikatowy identyfikator dla żądania, które mogą pomóc w diagnostyce. |
| correlation_id |Unikatowy identyfikator dla żądania, które mogą pomóc w diagnostyce składnikami. |

#### <a name="http-status-codes"></a>Kody stanu HTTP
Poniższa tabela zawiera listę kodów stanu HTTP, które zwraca punkt końcowy wystawiania tokenu. W niektórych przypadkach kod błędu jest wystarczająca do opisania odpowiedzi, ale jeśli występują błędy, należy przeanalizować towarzyszący dokument JSON i zbadać jego kod błędu.

| Kod HTTP | Opis |
| --- | --- |
| 400 |Domyślny kod HTTP. Używane w większości przypadków i jest zazwyczaj spowodowane źle sformułowane żądanie. Usuń i ponownie prześlij żądanie. |
| 401 |Uwierzytelnianie nie powiodło się. Na przykład w żądaniu brakuje parametru wartości client_secret. |
| 403 |Autoryzacja nie powiodła się. Na przykład użytkownik nie ma uprawnień dostępu do zasobu. |
| 500 |Wystąpił błąd wewnętrzny na usługę. Ponów żądanie. |

#### <a name="error-codes-for-token-endpoint-errors"></a>Kody błędów dla błędów punkt końcowy tokenu
| Kod błędu | Opis | Akcja klienta |
| --- | --- | --- |
| invalid_request |Błąd protokołu, np. Brak wymaganego parametru. |Usuń i ponownie prześlij żądanie |
| invalid_grant |Kod autoryzacji jest nieprawidłowy lub wygasł. |Wypróbuj nowe żądanie do `/authorize` punktu końcowego |
| unauthorized_client |Uwierzytelniany klient nie ma uprawnień do użycia tego typu przydziału autoryzacji. |Dzieje się tak zazwyczaj, gdy aplikacja kliencka nie jest zarejestrowany w usłudze Azure AD lub nie została dodana do dzierżawy usługi Azure AD przez użytkownika. Aplikacja może monitować użytkownika przy użyciu instrukcji dotyczących instalowania aplikacji i dodanie go do usługi Azure AD. |
| invalid_client |Nie można uwierzytelnić klienta. |Poświadczenia klienta są nieprawidłowe. Aby rozwiązać problem, administrator aplikacji aktualizuje poświadczenia. |
| unsupported_grant_type |Serwer autoryzacji nie obsługuje typu Udziel autoryzacji. |Zmień typ przydział w żądaniu. Tego rodzaju błąd powinien wystąpić tylko podczas programowania i można wykryć w początkowej fazie testowania. |
| invalid_resource |Zasób docelowy jest nieprawidłowy, ponieważ nie istnieje, usługa Azure AD nie może okazać się lub nie jest poprawnie skonfigurowana. |Oznacza to, że zasób, jeśli istnieje, nie został skonfigurowany w ramach dzierżawy. Aplikacja może monitować użytkownika przy użyciu instrukcji dotyczących instalowania aplikacji i dodanie go do usługi Azure AD. |
| interaction_required |Żądanie wymaga interakcji użytkownika. Na przykład krok dodatkowego uwierzytelniania jest wymagany. | Zamiast nieinterakcyjnych żądania spróbuj ponownie, używając interaktywne żądanie autoryzacji dla tego samego zasobu. |
| temporarily_unavailable |Serwer jest tymczasowo zbyt zajęty, aby obsłużyć żądanie. |Ponów żądanie. Aplikacja kliencka może wyjaśnić użytkownikowi, że odpowiedzi przez punkt końcowy jest opóźnione ze względu na tymczasowy warunek. |

## <a name="use-the-access-token-to-access-the-resource"></a>Użyj tokenu dostępu, aby uzyskać dostęp do zasobu
Teraz, gdy zostały pomyślnie uzyskano `access_token`, służy token w żądaniach wysyłanych do interfejsów API sieci Web, umieszczając go w `Authorization` nagłówka. [RFC 6750](https://www.rfc-editor.org/rfc/rfc6750.txt) specyfikacji wyjaśnia, jak uzyskać dostęp do chronionych zasobów za pomocą tokenów bearer w żądaniach HTTP.

### <a name="sample-request"></a>Przykładowe żądanie
```
GET /data HTTP/1.1
Host: service.contoso.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ
```

### <a name="error-response"></a>Odpowiedzi na błąd
Zabezpieczonych zasobów, które implementują kodów stanu HTTP problem RFC 6750. Jeśli żądanie nie obejmuje poświadczenia uwierzytelniania lub brakuje tokenu, odpowiedź zawiera `WWW-Authenticate` nagłówka. Jeśli żądanie zakończy się niepowodzeniem, serwer zasobów odpowiada z kodem stanu HTTP i kod błędu.

Oto przykład niepomyślnych odpowiedzi po żądaniu klienta nie zawiera token elementu nośnego:

```
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer authorization_uri="https://login.microsoftonline.com/contoso.com/oauth2/authorize",  error="invalid_token",  error_description="The access token is missing.",
```

#### <a name="error-parameters"></a>Błąd parametrów
| Parametr | Opis |
| --- | --- |
| authorization_uri |Identyfikator URI (fizycznym punktem końcowym) serwera autoryzacji. Ta wartość jest również używana jako klucz wyszukiwania, aby uzyskać więcej informacji na temat serwera z punktu końcowego wykrywania. <p><p> Klient musi sprawdzić, czy serwer autoryzacji jest zaufany. Jeśli zasób jest chroniony przez usługę Azure AD, jest wystarczające, aby sprawdzić, czy adres URL zaczyna się od https://login.microsoftonline.com lub inną nazwę hosta, który obsługuje usługi Azure AD. Zasób specyficznym dla dzierżawy zawsze powinien zwrócić tymczasową autoryzację specyficznym dla dzierżawy, identyfikatora URI. |
| błąd |Wartość kodu błędu, zdefiniowane w sekcji 5.2 [OAuth 2.0 autoryzacji Framework](https://tools.ietf.org/html/rfc6749). |
| error_description |Bardziej szczegółowy opis błędu. Ten komunikat nie ma być przyjazne dla użytkownika końcowego. |
| resource_id |Zwraca unikatowy identyfikator zasobu. Aplikacja kliencka może użyć tego identyfikatora, jako wartość `resource` parametru podczas żądania tokenu do zasobu. <p><p> Ważne jest, aby aplikacja kliencka sprawdzić tę wartość, w przeciwnym razie złośliwe usługi mogą być możliwe do wywołania **podniesienia uprawnień dla uprawnień** ataku <p><p> Zalecaną strategią w celu zapobiegania ataku jest sprawdzenie, czy `resource_id` podstawą adresu URL interfejsu API sieci web jest zgodna z którego uzyskiwany jest dostęp. Na przykład jeśli https://service.contoso.com/data jest dostępny, `resource_id` może być htttps://service.contoso.com/. Aplikacja kliencka musi odrzucać `resource_id` , nie rozpoczyna się od podstawowego adresu URL, chyba że istnieje niezawodny sposób alternatywne, Sprawdź identyfikator. |

#### <a name="bearer-scheme-error-codes"></a>Kody błędów schematu elementu nośnego
Specyfikacja RFC 6750 definiuje następujące błędy dotyczące zasobów, korzystające z nagłówka WWW-Authenticate i schematu elementu nośnego w odpowiedzi.

| Kod stanu HTTP | Kod błędu | Opis | Akcja klienta |
| --- | --- | --- | --- |
| 400 |invalid_request |Żądanie nie jest poprawnie sformułowany. Na przykład może być brak parametru lub przy użyciu tego samego parametru dwa razy. |Usuń błąd i ponowić próbę żądania. Tego rodzaju błąd powinien wystąpić tylko podczas programowania i wykryty w początkowej fazie testowania. |
| 401 |invalid_token |Token dostępu jest brakujące, nieprawidłowe lub został odwołany. Wartość parametru error_description zawiera dodatkowe szczegóły. |Żądania nowy token z serwera autoryzacji. Jeśli nowy token nie powiedzie się, wystąpił nieoczekiwany błąd. Wyślij komunikat o błędzie do użytkownika, a następnie spróbuj ponownie po losowe opóźnienia. |
| 403 |insufficient_scope |Token dostępu nie zawiera personifikacji wymaganych uprawnień do dostępu do zasobu. |Wyślij nowe żądanie autoryzacji do punktu końcowego autoryzacji. Jeśli odpowiedź zawiera parametr zakresu, użyj wartości zakresu w żądaniu do zasobu. |
| 403 |insufficient_access |Temat ten token nie ma uprawnienia, które są wymagane do dostępu do zasobu. |Monituj użytkownika, użyj innego konta lub zażądać uprawnień do określonego zasobu. |

## <a name="refreshing-the-access-tokens"></a>Odświeżanie tokenów dostępu

Tokeny dostępu są krótkotrwałe i muszą zostać odświeżone po ich wygaśnięciu, aby nadal mieć dostęp do zasobów. Możesz odświeżyć `access_token` przesyłając innego `POST` limit czasu żądania `/token` punktu końcowego, ale ten czas, zapewniając `refresh_token` zamiast `code`.  Tokeny odświeżania są prawidłowe dla wszystkich zasobów, które klient został już podany zgody na dostęp — tak więc token odświeżania wydane w żądaniu na potrzeby `resource=https://graph.microsoft.com` może służyć do żądania nowy token dostępu dla `resource=https://contoso.com/api`. 

Odświeżanie tokenów nie mają określonego okresy istnienia. Zazwyczaj okresy istnienia tokenów odświeżania są stosunkowo długo. Jednak w niektórych przypadkach, tokeny odświeżania wygaśnie, zostaną odwołane lub braku wystarczających uprawnień do żądanej akcji. Aplikacja musi się spodziewać i obsługiwać błędy zwrócone przez punkt końcowy wystawiania tokenu poprawnie.

Po otrzymaniu odpowiedzi z powodu błędu tokenu odświeżania odrzucić bieżący token odświeżania i poproś o nowy kod autoryzacji lub tokenu dostępu. W szczególności, gdy przy użyciu odświeżenia tokenu w przepływie przyznawania kodu autoryzacji Jeśli otrzymasz odpowiedź z `interaction_required` lub `invalid_grant` kody błędów, odrzucić token odświeżania i poproś o nowy kod autoryzacji.

Przykładowe żądanie do **specyficznym dla dzierżawy** punktu końcowego (możesz również użyć **wspólnej** punktu końcowego) mogą uzyskać nowy token przy użyciu tokenu odświeżania wygląda następująco:

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

### <a name="successful-response"></a>Pomyślna odpowiedź
Odpowiedź oznaczająca Powodzenie tokenu będzie wyglądać następująco:

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
| token_type |Typ tokenu. Jedyna obsługiwana wartość to **elementu nośnego**. |
| expires_in |Pozostały czas istnienia tokenu w ciągu kilku sekund. Typowa wartość to 3600 (jedna godzina). |
| expires_on |Data i godzina wygaśnięcia tokenu. Data jest reprezentowana jako liczbę sekund od 1970-01-01T0:0:0Z UTC do czasu wygaśnięcia. |
| zasób |Identyfikuje zabezpieczono zasób, który token dostępu mogą być używane do dostępu. |
| zakres |Uprawnień personifikacji aplikacja kliencka macierzystego. Domyślne uprawnienia **user_impersonation**. Właściciel zasobu docelowego można zarejestrować alternatywne wartości w usłudze Azure AD. |
| access_token |Nowy token dostępu, którego zażądano. |
| refresh_token |Nowe refresh_token OAuth 2.0, który może służyć do żądania nowych tokenów dostępu, gdy wygaśnie stary w tej odpowiedzi. |

### <a name="error-response"></a>Odpowiedzi na błąd
Przykładowa odpowiedź błędu może wyglądać następująco:

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
| błąd |Ciągu kodu błędu, który może służyć do klasyfikowania typy błędów, które występują i może służyć do reagowania na błędy. |
| error_description |Określony komunikat o błędzie ułatwiający Deweloper Identyfikuj główne przyczyny błędu uwierzytelniania. |
| error_codes |Lista kodów błędów specyficznych dla usługi STS, które mogą pomóc w diagnostyce. |
| timestamp |Czas, w którym wystąpił błąd. |
| trace_id |Unikatowy identyfikator dla żądania, które mogą pomóc w diagnostyce. |
| correlation_id |Unikatowy identyfikator dla żądania, które mogą pomóc w diagnostyce składnikami. |

Opis kodów błędów i działań opartej na zalecanym kliencie, zobacz [kody błędów dla błędów punktu końcowego tokenu](#error-codes-for-token-endpoint-errors).
