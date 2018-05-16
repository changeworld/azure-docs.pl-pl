---
title: Zrozumienie przepływu kodu autoryzacji protokołu OAuth 2.0 w usłudze Azure AD
description: W tym artykule opisano sposób użycia wiadomości HTTP do autoryzowania dostępu do aplikacji sieci web i interfejsów API sieci web w dzierżawie przy użyciu usługi Azure Active Directory i OAuth 2.0.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 93de62a21ca1d3b8c88715fc9207a583920ac33e
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="authorize-access-to-azure-active-directory-web-applications-using-the-oauth-20-code-grant-flow"></a>Autoryzacja dostępu do aplikacji sieci web usługi Azure Active Directory przy użyciu przepływu grant kodu OAuth 2.0
Azure Active Directory (Azure AD) używa protokołu OAuth 2.0 do autoryzowania dostępu do aplikacji sieci web i interfejsów API sieci web w dzierżawie usługi Azure AD. Ten przewodnik jest niezależny od języka i opisuje sposób wysyłania i odbierania wiadomości HTTP bez przy użyciu dowolnej z naszych [bibliotekach open source](active-directory-authentication-libraries.md).

Przepływu kodu autoryzacji protokołu OAuth 2.0 jest opisany w [sekcji 4.1 specyfikacji protokołu OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.1). Służy do wykonywania uwierzytelniania i autoryzacji w większości typów aplikacji, w tym aplikacje sieci web, a natywnie zainstalowane aplikacje.

[!INCLUDE [active-directory-protocols-getting-started](../../../includes/active-directory-protocols-getting-started.md)]

## <a name="oauth-20-authorization-flow"></a>Przepływ autoryzacji OAuth 2.0
Na wysokim poziomie przepływ całego autoryzacji dla aplikacji wygląda nieco następująco:

![Przepływu kodu autoryzacji OAuth](media/active-directory-protocols-oauth-code/active-directory-oauth-code-flow-native-app.png)

## <a name="request-an-authorization-code"></a>Kod autoryzacji żądania
Przepływu kodu autoryzacji rozpoczyna się od klienta kierowanie użytkownikowi `/authorize` punktu końcowego. W tym żądaniu klient wskazuje uprawnień wymaganych do uzyskania dostępu przez użytkownika. Zaznaczając można uzyskać punktu końcowego autoryzacji OAuth 2.0 dla dzierżawy **rejestracji aplikacji > punkty końcowe** w portalu Azure.

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%3A%12345
&response_mode=query
&resource=https%3A%2F%2Fservice.contoso.com%2F
&state=12345
```

| Parametr |  | Opis |
| --- | --- | --- |
| dzierżawa |wymagane |`{tenant}` Wartość w ścieżce żądania może być używana w celu kontrolowania, kto może zalogować się do aplikacji. Dozwolone wartości to identyfikatory dzierżawy, na przykład `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` lub `contoso.onmicrosoft.com` lub `common` tokenów niezależny od dzierżawcy |
| client_id |wymagane |Identyfikator aplikacji przypisany do aplikacji podczas rejestrowania z usługą Azure AD. To można znaleźć w portalu Azure. Kliknij przycisk **usługi Azure Active Directory** na pasku bocznym usług kliknij **rejestracji aplikacji**i wybierz aplikację. |
| response_type |wymagane |Musi zawierać `code` dla przepływu kodu autoryzacji. |
| redirect_uri |Zalecane |Redirect_uri aplikacji, w którym można wysłanych i odebranych przez aplikację odpowiedzi uwierzytelniania. Go musi dokładnie pasować redirect_uris, który został zarejestrowany w portalu, z wyjątkiem musi być zakodowane w adresie url. W przypadku aplikacji natywnej & przenośnych powinny używać wartość domyślną `urn:ietf:wg:oauth:2.0:oob`. |
| response_mode |Zalecane |Określa metodę, które mają być używane do wysyłania wynikowy token wstecz do aplikacji. Możliwe wartości to `query` i `form_post`. `query` zawiera kod jako parametr ciągu zapytania w Twojej przekierowania URI, podczas gdy `form_post` wykonuje POST, zawierającego kod identyfikator URI przekierowania. |
| state |Zalecane |Wartość zawarte w żądaniu, który jest także zwracany w odpowiedzi tokenu. Losowo generowany unikatową wartość jest zazwyczaj używana w przypadku [zapobieganie fałszerstwie żądania międzywitrynowego](http://tools.ietf.org/html/rfc6749#section-10.12). Stan służy także do kodowania informacje o stanie użytkownika w aplikacji przed wystąpieniem żądania uwierzytelniania, takich jak strony lub widok, które były na. |
| zasób | Zalecane |Identyfikator URI aplikacji sieci Web docelowego interfejsu API (zabezpieczonych zasobów). Aby znaleźć identyfikator URI aplikacji w portalu Azure, kliknij przycisk **usługi Azure Active Directory**, kliknij przycisk **rejestracji aplikacji**, Otwórz aplikację **ustawienia** strony, a następnie kliknij przycisk  **Właściwości**. Może to być również zasób zewnętrzny, takich jak `https://graph.microsoft.com`. Jest to wymagane w jednym autoryzacji lub żądania tokenu. Do zapewnienia uwierzytelniania mniejszą liczbę monitów umieścić w żądaniu autoryzacji, aby upewnić się, że odebraniu zgody użytkownika. |
| scope | **ignorowane** | W przypadku aplikacji usługi Azure AD v1, zakresy muszą być skonfigurowane statycznie w portalu Azure w obszarze aplikacje **ustawienia**, **wymagane uprawnienia**. |
| wiersz |opcjonalne |Wskazuje typ interakcji z użytkownikiem, który jest wymagany.<p> Prawidłowe wartości to: <p> *logowania*: użytkownik powinien być monitowany o ponownego uwierzytelnienia. <p> *zgoda*: zgody użytkownika przyznano, ale musi zostać zaktualizowany. Użytkownik powinien monit o zgodę. <p> *admin_consent*: administrator powinien być monitowany o zgodę imieniu wszyscy użytkownicy w organizacji |
| login_hint |opcjonalne |Można wstępnie wypełnić pole adresu e-mail/nazwa użytkownika strony logowania dla użytkownika, jeśli znasz swoją nazwę użytkownika wcześniejsze. Aplikacje często tego parametru należy użyć podczas ponownego uwierzytelniania, już o wyodrębnić nazwy użytkownika z poprzedniej logowania przy użyciu `preferred_username` oświadczeń. |
| domain_hint |opcjonalne |Zawiera wskazówki dotyczące dzierżawy lub domeny, która powinna być używana do logowania użytkownika. Wartość domain_hint jest domeną zarejestrowanych dla dzierżawcy. Jeśli dzierżawa jest Sfederowane do katalogu lokalnego, usługi AAD przekierowuje do serwera federacyjnego w określonym dzierżawcy. |
| code_challenge_method | opcjonalne    | Metody używane do kodowania `code_verifier` dla `code_challenge` parametru. Może być jednym z `plain` lub `S256`. Jeśli wykluczone, `code_challenge` zostanie potraktowany jako zwykłego tekstu, jeśli `code_challenge` jest dołączony. Azure 1.0 AAD obsługuje zarówno `plain` i `S256`. Aby uzyskać więcej informacji, zobacz [PKCE RFC](https://tools.ietf.org/html/rfc7636). |
| code_challenge        | opcjonalne    | Używany do zabezpieczania przyznaje kod autoryzacji przy użyciu klucza dowód kodu programu Exchange (PKCE) z klienta natywnego lub publiczny. Jeśli wymagane `code_challenge_method` jest dołączony. Aby uzyskać więcej informacji, zobacz [PKCE RFC](https://tools.ietf.org/html/rfc7636). |

> [!NOTE]
> Jeśli użytkownik należy do organizacji, administrator w organizacji można wyrazić zgodę lub odrzucić w imieniu użytkownika lub zezwolenie na użytkownika o zgodę. Użytkownik otrzymuje opcję, aby wyrazić zgodę tylko wtedy, gdy administrator pozwala.
>
>

W tym momencie użytkownik jest proszony o wprowadzenie poświadczeń i wyrażenia zgody na uprawnienia wymagane przez aplikację w portalu Azure. Po uwierzytelnia i udziela zgody użytkownika usługi Azure AD wysyła odpowiedź do aplikacji w `redirect_uri` adresu w żądaniu z kodem.

### <a name="successful-response"></a>Odpowiedź oznaczająca Powodzenie
Odpowiedź oznaczająca Powodzenie może wyglądać następująco:

```
GET  HTTP/1.1 302 Found
Location: http://localhost:12345/?code= AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF&state=D79E5777-702E-4260-9A62-37F75FF22CCE
```

| Parametr | Opis |
| --- | --- |
| admin_consent |Ma wartość True, jeśli administrator zgodę na wiersz żądania zgody. |
| Kod |Aplikacja zażądała kod autoryzacji. Aplikacji można użyć kodu autoryzacji do żądania tokenu dostępu dla zasobu docelowego. |
| session_state |Unikatowa wartość, która identyfikuje bieżącą sesję użytkownika. Ta wartość jest identyfikatorem GUID, ale powinien być traktowany jako wartość przezroczystości, która została przekazana bez badania. |
| state |Jeśli parametr Stan jest uwzględniony w żądaniu, tę samą wartość powinna być widoczna w odpowiedzi. Jest dobrym rozwiązaniem dla aplikacji, aby zweryfikować, że wartości stan żądania i odpowiedzi są identyczne, przed użyciem odpowiedzi. Ułatwia to wykrywanie [atakami opartymi na fałszowanie żądań między witrynami (CSRF)](https://tools.ietf.org/html/rfc6749#section-10.12) względem klienta. |

### <a name="error-response"></a>Odpowiedzi na błąd
Odpowiedzi na błędy mogą być również wysyłane do `redirect_uri` , dzięki czemu aplikacja może je odpowiednią obsługę.

```
GET http://localhost:12345/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parametr | Opis |
| --- | --- |
| error |Wartość kodu błędu, zdefiniowane w sekcji 5.2 [OAuth 2.0 autoryzacji Framework](http://tools.ietf.org/html/rfc6749). W następnej tabeli opisano kody błędów, które zwraca usługi Azure AD. |
| error_description |Bardziej szczegółowy opis błędu. Ten komunikat nie ma być przyjazny dla użytkownika końcowego. |
| state |Wartość stanu jest losowo generowany-ponownie wartość, która jest wysłanych w żądaniu i zwracany w odpowiedzi, aby zapobiec fałszerstwie żądania międzywitrynowego (CSRF). |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Kody błędów dla błędów punktu końcowego autoryzacji
W poniższej tabeli opisano różne kody błędów, które mogą być zwracane w `error` parametr odpowiedzi na błąd.

| Kod błędu | Opis | Akcja klienta |
| --- | --- | --- |
| invalid_request |Błąd protokołu, takie jak brak wymaganego parametru. |Usuń i ponownie prześlij żądanie. Jest to błąd programowanie i zwykle zostanie przechwycony podczas testowania początkowej. |
| unauthorized_client |Aplikacja kliencka nie jest dozwolone przy kod autoryzacji żądania. |Dzieje się tak zazwyczaj, gdy aplikacja kliencka nie jest zarejestrowany w usłudze Azure AD lub nie została dodana do dzierżawy usługi Azure AD przez użytkownika. Aplikację można monitować użytkownika z instrukcji dotyczących instalowania aplikacji i dodanie go do usługi Azure AD. |
| access_denied |Odmowa zgody właściciel zasobu |Aplikacja kliencka powiadamiać użytkownika, którego nie można kontynuować, chyba że użytkownik zgadza. |
| unsupported_response_type |Serwer autoryzacji w żądaniu nie obsługuje typ odpowiedzi. |Usuń i ponownie prześlij żądanie. Jest to błąd programowanie i zwykle zostanie przechwycony podczas testowania początkowej. |
| server_error |Serwer napotkał nieoczekiwany błąd. |Ponów żądanie. Te błędy może wynikać z tymczasowego warunków. Aplikacja kliencka może wyjaśnić użytkownikowi, że odpowiedzi jest opóźnione z powodu tymczasowy błąd. |
| temporarily_unavailable |Serwer jest tymczasowo zbyt zajęty, aby obsłużyć żądania. |Ponów żądanie. Aplikacja kliencka może wyjaśnić użytkownikowi, że odpowiedzi jest opóźniony ze względu na tymczasowy warunek. |
| invalid_resource |Zasób docelowy jest nieprawidłowy, ponieważ nie istnieje, nie można znaleźć usługi Azure AD lub nie została poprawnie skonfigurowana. |Oznacza to, że zasób, jeśli istnieje, nie został skonfigurowany w dzierżawie. Aplikację można monitować użytkownika z instrukcji dotyczących instalowania aplikacji i dodanie go do usługi Azure AD. |

## <a name="use-the-authorization-code-to-request-an-access-token"></a>Użyj kodu autoryzacji do żądania tokenu dostępu
Teraz, gdy zostały nabyte kod autoryzacji i mieć odpowiednie uprawnienia udzielone przez użytkownika, możesz zrealizować kod tokenu dostępu do żądanego zasobu, poprzez wysłanie żądania POST do `/token` punktu końcowego:

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
| dzierżawa |wymagane |`{tenant}` Wartość w ścieżce żądania może być używana w celu kontrolowania, kto może zalogować się do aplikacji. Dozwolone wartości to identyfikatory dzierżawy, na przykład `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` lub `contoso.onmicrosoft.com` lub `common` tokenów niezależny od dzierżawcy |
| client_id |wymagane |Identyfikator aplikacji przypisany do aplikacji podczas rejestrowania z usługą Azure AD. To można znaleźć w portalu Azure. Identyfikator aplikacji jest wyświetlany w ustawieniach rejestracji aplikacji. |
| Typ grant_type |wymagane |Musi być `authorization_code` dla przepływu kodu autoryzacji. |
| Kod |wymagane |`authorization_code` Uzyskanego w poprzedniej sekcji |
| redirect_uri |wymagane |Taki sam `redirect_uri` wartości, które zostało użyte do uzyskania `authorization_code`. |
| client_secret |wymagane dla aplikacji sieci web, nie jest dozwolone dla publicznych klientów |Klucz tajny aplikacji utworzonej w portalu Azure dla aplikacji w obszarze **klucze**. Nie można użyć w aplikacji natywnej (publicznego klienta), ponieważ client_secrets nie może być niezawodnie przechowywanych na urządzeniach. Jest to wymagane dla aplikacji sieci web i interfejsów API (wszystkie poufne klientów), która ma możliwość przechowywania sieci web `client_secret` bezpiecznie po stronie serwera. |
| zasób | Zalecane |Identyfikator URI aplikacji sieci Web docelowego interfejsu API (zabezpieczonych zasobów). Aby znaleźć identyfikator URI aplikacji w portalu Azure, kliknij przycisk **usługi Azure Active Directory**, kliknij przycisk **rejestracji aplikacji**, Otwórz aplikację **ustawienia** strony, a następnie kliknij przycisk  **Właściwości**. Może to być również zasób zewnętrzny, takich jak `https://graph.microsoft.com`. Jest to wymagane w jednym autoryzacji lub żądania tokenu. Do zapewnienia uwierzytelniania mniejszą liczbę monitów umieścić w żądaniu autoryzacji, aby upewnić się, że odebraniu zgody użytkownika. Jeśli zarówno żądania autoryzacji i żądania tokenu zasobu "Parametry muszą być zgodne. | 
| code_verifier | opcjonalne | Tym samym code_verifier, który został użyty do uzyskania authorization_code. Wymagane, jeśli PKCE został użyty w żądaniu grant kod autoryzacji. Aby uzyskać więcej informacji, zobacz [PKCE RFC](https://tools.ietf.org/html/rfc7636)   |

Aby znaleźć identyfikator URI aplikacji w portalu Azure, kliknij przycisk **usługi Azure Active Directory**, kliknij przycisk **rejestracji aplikacji**, Otwórz aplikację **ustawienia** strony, a następnie kliknij przycisk  **Właściwości**.

### <a name="successful-response"></a>Odpowiedź oznaczająca Powodzenie
Usługi Azure AD zwraca token dostępu po pomyślnej odpowiedzi. Aby zminimalizować wywołania sieci z aplikacji klienckiej i ich skojarzonych opóźnienia, aplikacja kliencka powinna buforowane tokenów dostępu przez okres istnienia tokenu, który określono w odpowiedzi protokołu OAuth 2.0. Aby ustalić okres istnienia tokenu, użyj `expires_in` lub `expires_on` wartości parametrów.

Jeśli zasobu interfejsu API sieci web zwraca `invalid_token` kodu błędu, może to oznaczać, że zasób stwierdził, że token utracił ważność. Jeśli czas zegara klienta i zasobów są różne (znane jako "czas pochylenia"), zasobu warto rozważyć token wygaśnie, zanim token jest wyczyszczone z pamięci podręcznej klienta. W takim przypadku należy wyczyścić tokenu z pamięci podręcznej, nawet jeśli jest nadal w jego obliczeniowej okres istnienia.

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
| access_token |Żądany dostęp token jako podpisem JSON Web Token (JWT). Aplikacja może używać tego tokenu do uwierzytelniania zabezpieczonych zasobów, takich jak interfejsu API sieci web. |
| token_type |Wskazuje wartość typ tokenu. Jedynym typem, który obsługuje usługę Azure AD jest elementu nośnego. Aby uzyskać więcej informacji dotyczących tokenów elementu nośnego, zobacz [Framework autoryzacji OAuth2.0: użycie tokenu elementu nośnego (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt) |
| expires_in |Jak długo token dostępu jest nieprawidłowy (w sekundach). |
| expires_on |Czas wygaśnięcia tokenu dostępu. Data jest reprezentowana jako liczbę sekund z rokiem 1970-01-01T0:0:0Z UTC czasu wygaśnięcia. Ta wartość jest używana do określenia okres istnienia pamięci podręcznej tokenów. |
| zasób |Identyfikator URI aplikacji sieci Web interfejsu API (zabezpieczonych zasobów). |
| scope |Personifikacja uprawnienia do aplikacji klienckiej. Domyślne uprawnienia `user_impersonation`. Właściciel zasobu zabezpieczonych rejestrować dodatkowych wartości w usłudze Azure AD. |
| refresh_token |Token odświeżania OAuth 2.0. Aplikacja może używać tego tokenu uzyskanie tokenów dostępu dodatkowe po wygaśnięciu bieżącego tokenu dostępu. Odśwież tokeny są to długotrwałe i pozwala zachować dostęp do zasobów przez dłuższy czas. |
| żądaniu |Niepodpisane JSON Web Token (JWT). Aplikacji base64Url może zdekodować segmentów tego tokenu do żądania informacji dotyczących użytkownika, który jest zalogowany. Można je wyświetlić i pamięci podręcznej wartości aplikacji, ale nie należy polegać na nich autoryzacji lub granic zabezpieczeń. |

### <a name="jwt-token-claims"></a>Oświadczenia tokenu JWT
Token JWT wartości `id_token` parametr może zostać odczytany na następujących oświadczenia:

```
{
 "typ": "JWT",
 "alg": "none"
}.
{
 "aud": "2d4d11a2-f814-46a7-890a-274a72a7309e",
 "iss": "https://sts.windows.net/7fe81447-da57-4385-becb-6de57f21477e/",
 "iat": 1388440863,
 "nbf": 1388440863,
 "exp": 1388444763,
 "ver": "1.0",
 "tid": "7fe81447-da57-4385-becb-6de57f21477e",
 "oid": "68389ae2-62fa-4b18-91fe-53dd109d74f5",
 "upn": "frank@contoso.com",
 "unique_name": "frank@contoso.com",
 "sub": "JWvYdCWPhhlpS1Zsf7yYUxShUwtUm5yzPmw_-jX3fHY",
 "family_name": "Miller",
 "given_name": "Frank"
}.
```

Aby uzyskać więcej informacji dotyczących tokenów sieci web JSON, zobacz [Specyfikacja wersji roboczej JWT IETF](http://go.microsoft.com/fwlink/?LinkId=392344). Aby uzyskać więcej informacji o typach tokenów i oświadczeń, przeczytaj [obsługiwany Token i typy oświadczeń](active-directory-token-and-claims.md)

`id_token` Parametr zawiera następujące typy oświadczeń:

| Typ oświadczenia | Opis |
| --- | --- |
| lub |Odbiorcy tokenu. Token jest wystawiony dla aplikacji klienckiej, odbiorców jest `client_id` klienta. |
| EXP |Czas wygaśnięcia. Czas wygaśnięcia tokenu. Token był prawidłowy, bieżącej daty/godziny musi być mniejsza lub równa `exp` wartość. Czas jest reprezentowany jako liczba sekund od 1 stycznia 1970 (1970-01-01T0:0:0Z) UTC czasu wygaśnięcia ważności tokenu.|
| family_name |Użytkownika imienia lub nazwiska. Aplikację można wyświetlić tę wartość. |
| given_name |Imię użytkownika. Aplikację można wyświetlić tę wartość. |
| IAT |Wygenerowane w czasie. Czas, kiedy wydano tokenu JWT. Czas jest reprezentowany jako liczba sekund od 1 stycznia 1970 (1970-01-01T0:0:0Z) UTC czasu token został wystawiony. |
| iss |Identyfikuje wystawcy tokenów |
| nbf |Nie wcześniej niż czas. Czas, gdy token rozpoczęcia obowiązywania. Aby token był prawidłowy bieżącej daty/godziny musi być większa lub równa wartości Nbf. Czas jest reprezentowany jako liczba sekund od 1 stycznia 1970 (1970-01-01T0:0:0Z) UTC czasu token został wystawiony. |
| Identyfikator OID |Identyfikator obiektu (ID) obiektu użytkownika w usłudze Azure AD. |
| Sub |Identyfikator podmiotu tokenu. To jest trwałe i modyfikować identyfikator dla użytkownika, który opisuje tokenu. Użyj tej wartości w ramach buforowania logiki. |
| TID |Dzierżawy identyfikator dzierżawy usługi Azure AD, która wystawiła token. |
| unique_name |Unikatowy identyfikator, które mogą być wyświetlane dla użytkownika. Jest to zwykle główna nazwa użytkownika (UPN). |
| nazwy UPN |Główna nazwa użytkownika użytkownika. |
| VER |Wersja. Wersja tokenu JWT, zwykle 1.0. |

### <a name="error-response"></a>Odpowiedzi na błąd
Błędy programu endpoint wystawiania tokenu są kody błędów HTTP, ponieważ klient wywołuje punkt końcowy wydawania tokenów bezpośrednio. Oprócz kod stanu HTTP punktu końcowego usługi Azure AD wydawania tokenów zwraca dokument JSON z obiektami, które opisują błędu.

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
| error |Ciąg kodu błędu, który może służyć do klasyfikowania typy błędów występujących i może służyć do reagowania na błędy. |
| error_description |Komunikat o błędzie, które mogą ułatwić dewelopera Określ przyczynę błędu uwierzytelniania. |
| error_codes |Lista specyficzne dla usługi STS kody błędów, które mogą pomóc w diagnostyce. |
| sygnatura czasowa |Czas, w którym wystąpił błąd. |
| trace_id |Unikatowy identyfikator dla żądania, które mogą pomóc w diagnostyce. |
| correlation_id |Unikatowy identyfikator dla żądania, które mogą pomóc w diagnostyce między składnikami. |

#### <a name="http-status-codes"></a>Kody stanu HTTP
Poniższa tabela zawiera listę kodów stanu HTTP, które zwraca punkt końcowy wydawania tokenów. W niektórych przypadkach kod błędu jest wystarczająca do opisywania odpowiedzi, ale jeśli występują błędy, należy przeanalizować towarzyszący dokument JSON i sprawdź, czy jego kod błędu.

| Kod HTTP | Opis |
| --- | --- |
| 400 |Domyślny kod HTTP. Używane w większości przypadków i jest zwykle z powodu źle sformułowane żądanie. Usuń i ponownie prześlij żądanie. |
| 401 |Uwierzytelnianie nie powiodło się. Na przykład w żądaniu brakuje parametru client_secret. |
| 403 |Autoryzacja nie powiodła się. Na przykład użytkownik nie ma uprawnień dostępu do zasobu. |
| 500 |Wystąpił błąd wewnętrzny w usłudze. Ponów żądanie. |

#### <a name="error-codes-for-token-endpoint-errors"></a>Kody błędów dla punktu końcowego tokena błędów
| Kod błędu | Opis | Akcja klienta |
| --- | --- | --- |
| invalid_request |Błąd protokołu, takie jak brak wymaganego parametru. |Usuń i ponownie prześlij żądanie |
| invalid_grant |Kod autoryzacji jest nieprawidłowa lub wygasła. |Ponów żądanie nowej `/authorize` punktu końcowego |
| unauthorized_client |Uwierzytelniany klient nie ma uprawnień do używania tego typu przydziału autoryzacji. |Dzieje się tak zazwyczaj, gdy aplikacja kliencka nie jest zarejestrowany w usłudze Azure AD lub nie została dodana do dzierżawy usługi Azure AD przez użytkownika. Aplikację można monitować użytkownika z instrukcji dotyczących instalowania aplikacji i dodanie go do usługi Azure AD. |
| invalid_client |Uwierzytelnianie klienta nie powiodło się. |Poświadczenia klienta są nieprawidłowe. Aby rozwiązać problem, administrator aplikacji aktualizuje poświadczenia. |
| unsupported_grant_type |Serwer autoryzacji nie obsługuje typ przydziału autoryzacji. |Zmień typ grant w żądaniu. Tego typu błędu powinien wystąpić tylko podczas programowania i być wykryte podczas testowania początkowej. |
| invalid_resource |Zasób docelowy jest nieprawidłowy, ponieważ nie istnieje, nie można znaleźć usługi Azure AD lub nie została poprawnie skonfigurowana. |Oznacza to, że zasób, jeśli istnieje, nie został skonfigurowany w dzierżawie. Aplikację można monitować użytkownika z instrukcji dotyczących instalowania aplikacji i dodanie go do usługi Azure AD. |
| interaction_required |Żądanie wymaga interakcji z użytkownikiem. Na przykład krok dodatkowego uwierzytelniania jest wymagany. | Zamiast żądanie nieinterakcyjnym spróbuj ponownie z żądaniem interakcyjne autoryzacji dla tego samego zasobu. |
| temporarily_unavailable |Serwer jest tymczasowo zbyt zajęty, aby obsłużyć żądania. |Ponów żądanie. Aplikacja kliencka może wyjaśnić użytkownikowi, że odpowiedzi jest opóźniony ze względu na tymczasowy warunek. |

## <a name="use-the-access-token-to-access-the-resource"></a>Użyj tokenu dostępu, dostęp do zasobu
Teraz, zostały pomyślnie uzyskano `access_token`, użyć tokenu w żądaniach wysyłanych do interfejsów API sieci Web, w tym `Authorization` nagłówka. [RFC 6750](http://www.rfc-editor.org/rfc/rfc6750.txt) specyfikacji wyjaśniono, jak uzyskać dostęp do chronionych zasobów za pomocą tokenów bearer w żądaniach HTTP.

### <a name="sample-request"></a>Przykładowe żądanie
```
GET /data HTTP/1.1
Host: service.contoso.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ
```

### <a name="error-response"></a>Odpowiedzi na błąd
Zabezpieczonych zasobów, które implementuje kody stanu HTTP problem RFC 6750. Jeśli nie ma poświadczeń uwierzytelniania lub brak tokenu żądania odpowiedź zawiera `WWW-Authenticate` nagłówka. Gdy żądanie nie powiodło się, serwer zasobów odpowie kod stanu HTTP i kod błędu.

Poniżej przedstawiono przykładowy odpowiedzi nie powiodło się, gdy żądanie klienta nie obejmuje tokenu elementu nośnego:

```
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer authorization_uri="https://login.microsoftonline.com/contoso.com/oauth2/authorize",  error="invalid_token",  error_description="The access token is missing.",
```

#### <a name="error-parameters"></a>Błąd parametrów
| Parametr | Opis |
| --- | --- |
| authorization_uri |Identyfikator URI (fizycznych punktu końcowego) serwera autoryzacji. Ta wartość jest także używana jako klucz wyszukiwania, aby uzyskać więcej informacji o serwerze z punktu końcowego odnajdywania. <p><p> Klient musi sprawdzić, czy serwer autoryzacji jest zaufany. Jeśli zasób jest chroniony przez usługę Azure AD, jest wystarczająca sprawdzić, czy adres URL rozpoczyna się od https://login.microsoftonline.com lub innej nazwy hosta, który obsługuje usługę Azure AD. Zasób specyficznego dla dzierżawy zawsze powinna zwrócić identyfikatora URI autoryzacji specyficznego dla dzierżawy. |
| error |Wartość kodu błędu, zdefiniowane w sekcji 5.2 [OAuth 2.0 autoryzacji Framework](http://tools.ietf.org/html/rfc6749). |
| error_description |Bardziej szczegółowy opis błędu. Ten komunikat nie ma być przyjazny dla użytkownika końcowego. |
| resource_id |Zwraca unikatowy identyfikator zasobu. Aplikacja kliencka może użyć tego identyfikatora jako wartość `resource` parametr podczas żądania tokenu dla zasobu. <p><p> Ważne jest, aby aplikacja klienta sprawdzić tę wartość, w przeciwnym razie złośliwe usługi będzie mógł wywołać **"podniesienia uprawnień"** ataków <p><p> Jest zalecana strategia zapobiegania atak do sprawdzenia, czy `resource_id` podstawowy adres URL interfejsu API sieci Web odpowiada, do której uzyskuje dostęp. Na przykład jeśli https://service.contoso.com/data uzyskiwany jest `resource_id` może być htttps://service.contoso.com/. Aplikacja kliencka musi odrzucania `resource_id` nie zaczynające się od podstawowego adresu URL, chyba że jest to niezawodny sposób alternatywnych można zweryfikować identyfikatora. |

#### <a name="bearer-scheme-error-codes"></a>Kody błędów schematu elementu nośnego
Specyfikacja RFC 6750 definiuje następujące błędy zasobów korzystających z nagłówka WWW-Authenticate i schematu elementu nośnego w odpowiedzi.

| Kod stanu HTTP | Kod błędu | Opis | Akcja klienta |
| --- | --- | --- | --- |
| 400 |invalid_request |Żądanie nie jest poprawnie sformułowany. Na przykład może być brak parametru lub przy użyciu tego samego parametru dwa razy. |Usuń błąd i ponów żądanie. Tego typu błędu powinien wystąpić tylko podczas programowania i wykryty w początkowej testowania. |
| 401 |invalid_token |Token dostępu jest brakujące, nieprawidłowe lub został odwołany. Wartość parametru error_description zawiera dodatkowe szczegóły. |Żądania nowy token od serwera autoryzacji. Jeśli nowy token nie powiedzie się, wystąpił nieoczekiwany błąd. Wyślij komunikat o błędzie do użytkownika i spróbuj ponownie po losowego opóźnienia. |
| 403 |insufficient_scope |Token dostępu zawiera personifikacji wymaganych uprawnień do dostępu do zasobu. |Wysłanie nowego żądania autoryzacji do punktu końcowego autoryzacji. Jeśli odpowiedź zawiera parametr zakresu, należy użyć wartości zakresu żądania do zasobu. |
| 403 |insufficient_access |Podmiot tokenu nie ma uprawnień, które są wymagane do uzyskania dostępu do zasobu. |Monituj użytkownika, aby korzystała z innego konta lub poproś o uprawnienia do określonego zasobu. |

## <a name="refreshing-the-access-tokens"></a>Odświeżanie tokeny dostępu
Tokeny dostępu są krótkim okresie i musi zostać odświeżona po wygaśnięciu, aby kontynuować, uzyskiwanie dostępu do zasobów. Można odświeżać `access_token` poprzez przesłanie innego `POST` żądanie `/token` punktu końcowego, ale ten czas, zapewniając `refresh_token` zamiast `code`.

Odśwież tokenów nie ma określonego okresy istnienia. Zwykle okresy istnienia tokenów odświeżania są stosunkowo długo. Jednak w niektórych przypadkach tokenów odświeżania wygaśnie, były odwołane lub Brak wystarczających uprawnień dla żądanej akcji. Aplikacja musi oczekiwać i błędy zwrócone przez punkt końcowy wydawania tokenów poprawnie obsłużyć.

Po otrzymaniu odpowiedzi z powodu błędu tokenu odświeżania odrzucić bieżący token odświeżania i poproś o nowy kod autoryzacji lub tokenu dostępu. W szczególności, gdy przy użyciu odświeżenia tokenu w przepływ udzielania kodu autoryzacji, jeśli otrzymasz odpowiedź z `interaction_required` lub `invalid_grant` kody błędów, Odrzuć token odświeżania i poproś o nowy kod autoryzacji.

Przykładowe żądanie do **specyficznego dla dzierżawy** punktu końcowego (można również użyć **wspólnej** punktu końcowego) do uzyskania dostępu nowego tokenu przy użyciu tokenu odświeżania wygląda następująco:

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

### <a name="successful-response"></a>Odpowiedź oznaczająca Powodzenie
Odpowiedź oznaczająca Powodzenie tokenu będą wyglądać jak:

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
| token_type |Typ tokenu. Jest to jedyna obsługiwana wartość **elementu nośnego**. |
| expires_in |Pozostały okres istnienia tokenu, w sekundach. Typowe wartości to 3600 (jedna godzina). |
| expires_on |Data i godzina wygaśnięcia tokenu. Data jest reprezentowana jako liczbę sekund z rokiem 1970-01-01T0:0:0Z UTC czasu wygaśnięcia. |
| zasób |Identyfikuje zabezpieczonym zasobem, który token dostępu mogą być używane do dostępu. |
| scope |Personifikacja uprawnienia przyznane aplikację native client. Domyślne uprawnienia **user_impersonation**. Właściciel zasobu docelowego można zarejestrować alternatywne wartości w usłudze Azure AD. |
| access_token |Nowy token dostępu, którego zażądano. |
| refresh_token |Nowe refresh_token OAuth 2.0, który może służyć do żądania nowe tokeny dostępu po wygaśnięciu w tej odpowiedzi. |

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
| error |Ciąg kodu błędu, który może służyć do klasyfikowania typy błędów występujących i może służyć do reagowania na błędy. |
| error_description |Komunikat o błędzie, które mogą ułatwić dewelopera Określ przyczynę błędu uwierzytelniania. |
| error_codes |Lista specyficzne dla usługi STS kody błędów, które mogą pomóc w diagnostyce. |
| sygnatura czasowa |Czas, w którym wystąpił błąd. |
| trace_id |Unikatowy identyfikator dla żądania, które mogą pomóc w diagnostyce. |
| correlation_id |Unikatowy identyfikator dla żądania, które mogą pomóc w diagnostyce między składnikami. |

Opis akcji zalecane klienta i kody błędów, zobacz [kody błędów dla błędów punktu końcowego tokena](#error-codes-for-token-endpoint-errors).
