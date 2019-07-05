---
title: Azure uwierzytelniania usługi Active Directory service-to-service, używającej OAuth 2.0 specyfikacji wersji roboczej w imieniu z | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak używać wiadomości HTTP do zaimplementowania usługa Usługa uwierzytelniania za pomocą OAuth 2.0 w imieniu użytkownika z usługi flow.
services: active-directory
documentationcenter: .net
author: navyasric
manager: CelesteDG
editor: ''
ms.assetid: 09f6f318-e88b-4024-9ee1-e7f09fb19a82
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2019
ms.author: ryanwi
ms.reviewer: hirsin, nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51fd5c8f406ea54c7fc8e81c674e41b30d7ad406
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67482413"
---
# <a name="service-to-service-calls-that-use-delegated-user-identity-in-the-on-behalf-of-flow"></a>Service to service wywołania tej tożsamości użytkownika użycia delegowanego przepływu w imieniu z

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

Przepływ OAuth 2.0 "w imieniu" (OBO) umożliwia aplikacji, która wywołuje usługę lub interfejsu API do przekazania do uwierzytelniania użytkowników do innej usługi sieci web lub interfejsu API sieci web. Przepływu OBO propaguje delegowany użytkownik tożsamości i uprawnień w łańcuchu żądanie. W przypadku usługi warstwy środkowej na wysyłanie żądań uwierzytelnionych usługi transmisji ją zabezpieczyć token dostępu z usługi Azure Active Directory (Azure AD) w imieniu użytkownika.

> [!IMPORTANT]
> Od maja 2018 r. `id_token` nie można używać w imieniu z usługi flow.  Aplikacje jednej strony (źródła) musi pomyślnie przejść token dostępu do warstwy środkowej poufne klienta do wykonania przepływu OBO. Aby uzyskać więcej szczegółów na temat klientów, którzy mogą wykonywać wywołania w imieniu z, zobacz [ograniczenia](#client-limitations).

## <a name="on-behalf-of-flow-diagram"></a>W imieniu z diagramu przepływu

Po użytkownik został uwierzytelniony w aplikacji, która używa można uruchomić przepływu OBO [kod autoryzacji OAuth 2.0 udzielić przepływ](v1-protocols-oauth-code.md). W tym momencie aplikacji wysyła token dostępu (token A) w sieci web warstwy środkowej interfejsu API (interfejs API A) zawierający oświadczenia użytkownika i zgody na dostęp do interfejsu API A. Następnie element API sprawia, że uwierzytelnionego żądania podrzędnego sieci Web interfejsu API (interfejs API B).

Te kroki tworzą przepływu w imieniu z: ![Przedstawiono kroki OAuth 2.0 w imieniu użytkownika z usługi flow](./media/v1-oauth2-on-behalf-of-flow/active-directory-protocols-oauth-on-behalf-of-flow.png)

1. Aplikacja kliencka wysyła żądanie do interfejsu API, A za pomocą tokenu A.
1. A interfejs API jest uwierzytelniany punktu końcowego wystawiania tokenu usługi Azure AD i żądania tokenu służącego do dostępu do interfejsu API B.
1. Punkt końcowy wystawiania tokenu usługi Azure AD przeprowadza walidację poświadczeń A interfejsu API, a token i wystawia token dostępu dla aplikacji interfejsu API B (token B).
1. Żądanie do interfejsu API B zawiera token B w nagłówku autoryzacji.
1. B interfejsu API zwraca dane z zabezpieczono zasób.

>[!NOTE]
>Oświadczenia odbiorców w wyniku token dostępu używany do wysłania żądania tokenu usługi podrzędny musi być Identyfikatorem usługi wysyłającego żądanie OBO. Token również muszą być podpisane przy użyciu klucza podpisywania globalnej usługi Azure Active Directory (co jest ustawieniem domyślnym dla zarejestrowanych aplikacji za pośrednictwem **rejestracje aplikacji** w portalu).

## <a name="register-the-application-and-service-in-azure-ad"></a>Rejestrowanie aplikacji i usług w usłudze Azure AD

Rejestrowanie aplikacji klienckiej i usługi warstwy środkowej w usłudze Azure AD.

### <a name="register-the-middle-tier-service"></a>Rejestrowanie usługi warstwy środkowej

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Na górnym pasku wybierz swoje konto i sprawdź w obszarze **katalogu** listę, aby wybrać dzierżawy usługi Active Directory dla aplikacji.
1. Wybierz **więcej usług** w okienku po lewej stronie i wybierz polecenie **usługi Azure Active Directory**.
1. Wybierz **rejestracje aplikacji** i następnie **nowej rejestracji**.
1. Wprowadź przyjazną nazwę dla aplikacji, a następnie wybierz typ aplikacji.
1. W obszarze **Obsługiwane typy kont** wybierz pozycję **Konta w dowolnym katalogu organizacyjnym i konta osobiste Microsoft**.
1. Ustaw identyfikator URI przekierowania do podstawowego adresu URL.
1. Wybierz pozycję **Zarejestruj**, aby utworzyć aplikację.
1. Wygeneruj klucz tajny klienta przed zakończeniem pracy w witrynie Azure portal.
1. W witrynie Azure portal, wybierz aplikację, a następnie wybierz pozycję **certyfikaty i klucze tajne**.
1. Wybierz **nowy wpis tajny klienta** i dodać wpis tajny na okres jednego roku lub dwa lata.
1. Podczas zapisywania na tej stronie witryny Azure portal Wyświetla wartość wpisu tajnego. Skopiuj i Zapisz wartość wpisu tajnego w bezpiecznym miejscu.

> [!IMPORTANT]
> Potrzebny jest klucz tajny, aby skonfigurować ustawienia aplikacji w danej implementacji. Ta wartość wpisu tajnego jest on wyświetlany ponownie i nie jest możliwe do pobierania w inny sposób. Zapisz go, jak jest ona widoczna w witrynie Azure portal.

### <a name="register-the-client-application"></a>Rejestrowanie aplikacji klienta

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Na górnym pasku wybierz swoje konto i sprawdź w obszarze **katalogu** listę, aby wybrać dzierżawy usługi Active Directory dla aplikacji.
1. Wybierz **więcej usług** w okienku po lewej stronie i wybierz polecenie **usługi Azure Active Directory**.
1. Wybierz **rejestracje aplikacji** i następnie **nowej rejestracji**.
1. Wprowadź przyjazną nazwę dla aplikacji, a następnie wybierz typ aplikacji.
1. W obszarze **Obsługiwane typy kont** wybierz pozycję **Konta w dowolnym katalogu organizacyjnym i konta osobiste Microsoft**.
1. Ustaw identyfikator URI przekierowania do podstawowego adresu URL.
1. Wybierz pozycję **Zarejestruj**, aby utworzyć aplikację.
1. Skonfiguruj uprawnienia dla aplikacji. W **uprawnienia do interfejsu API**, wybierz opcję **Dodaj uprawnienia** i następnie **Moje interfejsy API**.
1. W polu tekstowym, wpisz nazwę usługi warstwy środkowej.
1. Wybierz **wybierz uprawnienia** , a następnie wybierz **dostępu <service name>** .

### <a name="configure-known-client-applications"></a>Konfiguruj aplikacje klienckie znane

W tym scenariuszu usługi warstwy środkowej musi uzyskać zgodę użytkownika na dostęp do interfejsu API niższego rzędu bez interakcji z użytkownikiem. Możliwość udzielania dostępu do interfejsu API niższego rzędu przedstawia się frontonu w ramach kroku zgodę podczas uwierzytelniania.

Wykonaj poniższe kroki, aby jawnie powiązać rejestracji aplikacji klienckiej w usłudze Azure AD za pomocą rejestracji usługi warstwy środkowej. Ta operacja scala zgody wymagane przez klienta i warstwy środkowej w jednym oknie dialogowym.

1. Przejdź do rejestracji usługi warstwy środkowej i wybierz **manifestu** celu otwórz Edytor manifestów.
1. Znajdź `knownClientApplications` macierz właściwości, a następnie Dodaj identyfikator klienta aplikacji klienckiej jako element.
1. Zapisanie manifestu, wybierając **Zapisz**.

## <a name="service-to-service-access-token-request"></a>Żądanie tokenu dostępu do usługi

Do żądania tokenu dostępu, metody HTTP POST do konkretnej dzierżawy punktu końcowego usługi Azure AD z następującymi parametrami:

```
https://login.microsoftonline.com/<tenant>/oauth2/token
```

Aplikacja kliencka jest zabezpieczony przez Wspólny klucz tajny lub przy użyciu certyfikatu.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Pierwszy przypadek: Żądanie tokenu dostępu za pomocą wspólny klucz tajny

Korzystając z wspólny klucz tajny, żądania tokenu dostępu do usługi zawiera następujące informacje:

| Parametr |  | Opis |
| --- | --- | --- |
| grant_type |Wymagane | Typ żądania tokenu. Żądanie OBO używa formatu JSON tokenu sieci Web (JWT), dlatego wartość musi być **urn: ietf:params:oauth:grant — typ: jwt-elementu nośnego**. |
| assertion |Wymagane | Wartość tokenu dostępu, użyty w żądaniu. |
| client_id |Wymagane | Identyfikator aplikacji przypisany do wywoływania usługi podczas rejestracji w usłudze Azure AD. Aby znaleźć identyfikator aplikacji w witrynie Azure portal, wybierz **usługi Active Directory**, wybierz katalog, a następnie wybierz nazwę aplikacji. |
| client_secret |Wymagane | Klucz jest zarejestrowany dla wywołania usługi w usłudze Azure AD. Ta wartość powinna zauważyć w chwili rejestracji. |
| resource |Wymagane | Aplikacja identyfikator URI usługi odbieranie (zabezpieczono zasób). Aby znaleźć identyfikator URI w witrynie Azure portal, wybierz **usługi Active Directory** i wybierz katalog. Wybierz nazwę aplikacji, wybierz pozycję **wszystkie ustawienia**, a następnie wybierz pozycję **właściwości**. |
| requested_token_use |Wymagane | Określa, jak można przetworzyć żądania. W przepływie w imieniu z, wartość musi być **on_behalf_of**. |
| scope |Wymagane | Spacjami listy zakresów dla żądania tokenu. Dla protokołu OpenID Connect, zakres **openid** musi być określona.|

#### <a name="example"></a>Przykład

W następującym WPISIE HTTP żądania tokenu dostępu dla https://graph.windows.net interfejs API sieci web. `client_id` Określa usługę, która żąda tokenu dostępu.

```
// line breaks for legibility only

POST /oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_secret=0Y1W%2BY3yYb3d9N8vSjvm8WrGzVZaAaHbHHcGbcgG%2BoI%3D
&resource=https%3A%2F%2Fgraph.windows.net
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=openid
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Drugi przypadek: Żądanie tokenu dostępu przy użyciu certyfikatu

Żądanie tokenu dostępu do usługi, za pomocą certyfikatu zawiera następujące parametry:

| Parametr |  | Opis |
| --- | --- | --- |
| grant_type |Wymagane | Typ żądania tokenu. Żądanie OBO używa tokenu dostępu JWT, dlatego wartość musi być **urn: ietf:params:oauth:grant — typ: jwt-elementu nośnego**. |
| assertion |Wymagane | Wartość tokenu użytego w żądaniu. |
| client_id |Wymagane | Identyfikator aplikacji przypisany do wywoływania usługi podczas rejestracji w usłudze Azure AD. Aby znaleźć identyfikator aplikacji w witrynie Azure portal, wybierz **usługi Active Directory**, wybierz katalog, a następnie wybierz nazwę aplikacji. |
| client_assertion_type |Wymagane |Wartość musi być `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |Wymagane | JSON Web Token, tworzenie i zalogować się przy użyciu certyfikatu rejestracji w charakterze poświadczenia dla aplikacji. Zobacz [certyfikatu poświadczeń](active-directory-certificate-credentials.md) Aby dowiedzieć się więcej o formacie potwierdzenia i o tym jak zarejestrować certyfikat.|
| resource |Wymagane | Aplikacja identyfikator URI usługi odbieranie (zabezpieczono zasób). Aby znaleźć identyfikator URI w witrynie Azure portal, wybierz **usługi Active Directory** i wybierz katalog. Wybierz nazwę aplikacji, wybierz pozycję **wszystkie ustawienia**, a następnie wybierz pozycję **właściwości**. |
| requested_token_use |Wymagane | Określa, jak można przetworzyć żądania. W przepływie w imieniu z, wartość musi być **on_behalf_of**. |
| scope |Wymagane | Spacjami listy zakresów dla żądania tokenu. Dla protokołu OpenID Connect, zakres **openid** musi być określona.|

Te parametry są prawie takie same jak w przypadku żądania przez Wspólny klucz tajny, chyba że `client_secret parameter` zostaje zastąpiona przez dwa parametry: `client_assertion_type` i `client_assertion`.

#### <a name="example"></a>Przykład

W następującym WPISIE HTTP żądania tokenu dostępu dla https://graph.windows.net interfejs API sieci web przy użyciu certyfikatu. `client_id` Określa usługę, która żąda tokenu dostępu.

```
// line breaks for legibility only

POST /oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&resource=https%3A%2F%2Fgraph.windows.net
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=openid
```

## <a name="service-to-service-access-token-response"></a>Odpowiedzi tokenu dostępu do usługi

Odpowiedź sukcesu jest odpowiedź JSON OAuth 2.0, z następującymi parametrami:

| Parametr | Opis |
| --- | --- |
| token_type |Wskazuje typ tokenu. Jedynym typem, który obsługuje usługi Azure AD jest **elementu nośnego**. Aby uzyskać więcej informacji na temat tokenów elementu nośnego, zobacz [OAuth 2.0 autoryzacji Framework: Użycie tokenu elementu nośnego (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| scope |Zakres dostępu przyznane w tokenie. |
| expires_in |Długość czasu, przez który token dostępu jest prawidłowy (w sekundach). |
| expires_on |Czas wygaśnięcia tokenu dostępu. Data jest reprezentowana jako liczbę sekund od 1970-01-01T0:0:0Z UTC do czasu wygaśnięcia. Ta wartość jest używana do określenia okres istnienia tokenów buforowanych. |
| resource |Aplikacja identyfikator URI usługi odbieranie (zabezpieczono zasób). |
| access_token |Token żądanego dostępu. Wywoływania usługi można użyć tego tokenu do uwierzytelnienia w usłudze odbierania. |
| id_token |Żądany identyfikator tokenu. Wywoływania usługi można użyć tego tokenu do zweryfikowania tożsamości użytkownika i rozpocząć sesję z użytkownikiem. |
| refresh_token |Token odświeżania dla tokenu żądanego dostępu. Wywoływania usługi można użyć tego tokenu do żądania inny token dostępu po wygaśnięciu bieżącego tokenu dostępu. |

### <a name="success-response-example"></a>Przykład odpowiedzi sukces

Poniższy przykład przedstawia odpowiedź sukcesu na żądanie, aby uzyskać dostęp do tokenu do https://graph.windows.net interfejs API sieci web.

```
{
    "token_type":"Bearer",
    "scope":"User.Read",
    "expires_in":"43482",
    "ext_expires_in":"302683",
    "expires_on":"1493466951",
    "not_before":"1493423168",
    "resource":"https://graph.windows.net",
    "access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ",
    "refresh_token":"AQABAAAAAABnfiG-mA6NTae7CdWW7QfdjKGu9-t1scy_TDEmLi4eLQMjJGt_nAoVu6A4oSu1KsRiz8XyQIPKQxSGfbf2FoSK-hm2K8TYzbJuswYusQpJaHUQnSqEvdaCeFuqXHBv84wjFhuanzF9dQZB_Ng5za9xKlUENrNtlq9XuLNVKzxEyeUM7JyxzdY7JiEphWImwgOYf6II316d0Z6-H3oYsFezf4Xsjz-MOBYEov0P64UaB5nJMvDyApV-NWpgklLASfNoSPGb67Bc02aFRZrm4kLk-xTl6eKE6hSo0XU2z2t70stFJDxvNQobnvNHrAmBaHWPAcC3FGwFnBOojpZB2tzG1gLEbmdROVDp8kHEYAwnRK947Py12fJNKExUdN0njmXrKxNZ_fEM33LHW1Tf4kMX_GvNmbWHtBnIyG0w5emb-b54ef5AwV5_tGUeivTCCysgucEc-S7G8Cz0xNJ_BOiM_4bAv9iFmrm9STkltpz0-Tftg8WKmaJiC0xXj6uTf4ZkX79mJJIuuM7XP4ARIcLpkktyg2Iym9jcZqymRkGH2Rm9sxBwC4eeZXM7M5a7TJ-5CqOdfuE3sBPq40RdEWMFLcrAzFvP0VDR8NKHIrPR1AcUruat9DETmTNJukdlJN3O41nWdZOVoJM-uKN3uz2wQ2Ld1z0Mb9_6YfMox9KTJNzRzcL52r4V_y3kB6ekaOZ9wQ3HxGBQ4zFt-2U0mSszIAA",
    "id_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC8yNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IvIiwiaWF0IjoxNDkzNDIzMTY4LCJuYmYiOjE0OTM0MjMxNjgsImV4cCI6MTQ5MzQ2Njk1MSwiYW1yIjpbInB3ZCJdLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXRpIjoieEN3ZnpoYS1QMFdKUU9MeENHZ0tBQSIsInZlciI6IjEuMCJ9."
}
```

### <a name="error-response-example"></a>Przykład odpowiedzi błędu

Punkt końcowy tokenu usługi Azure AD zwraca odpowiedź na błąd podczas próby uzyskania tokenu dostępu dla podrzędnego interfejsu API, która została ustawiona za pomocą zasad dostępu warunkowego (na przykład uwierzytelnianie wieloskładnikowe). Usługi warstwy środkowej należy przedstawić ten błąd do aplikacji klienckiej, tak, aby aplikacja kliencka może zapewnić interakcję użytkownika, aby spełnić wymagania zasad dostępu warunkowego.

```
{
    "error":"interaction_required",
    "error_description":"AADSTS50079: Due to a configuration change made by your administrator, or because you moved to a new location, you must enroll in multi-factor authentication to access 'bf8d80f9-9098-4972-b203-500f535113b1'.\r\nTrace ID: b72a68c3-0926-4b8e-bc35-3150069c2800\r\nCorrelation ID: 73d656cf-54b1-4eb2-b429-26d8165a52d7\r\nTimestamp: 2017-05-01 22:43:20Z",
    "error_codes":[50079],
    "timestamp":"2017-05-01 22:43:20Z",
    "trace_id":"b72a68c3-0926-4b8e-bc35-3150069c2800",
    "correlation_id":"73d656cf-54b1-4eb2-b429-26d8165a52d7",
    "claims":"{\"access_token\":{\"polids\":{\"essential\":true,\"values\":[\"9ab03e19-ed42-4168-b6b7-7001fb3e933a\"]}}}"
}
```

## <a name="use-the-access-token-to-access-the-secured-resource"></a>Dostęp do zabezpieczonych zasobów przy użyciu tokenu dostępu

Usługi warstwy środkowej, można użyć tokenu dostępu uzyskano dokonać uwierzytelnionego żądania podrzędnego internetowy interfejs API, ustawiając token w `Authorization` nagłówka.

### <a name="example"></a>Przykład

```
GET /me?api-version=2013-11-08 HTTP/1.1
Host: graph.windows.net
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ
```

## <a name="saml-assertions-obtained-with-an-oauth20-obo-flow"></a>Twierdzenia SAML otrzymany wraz z przepływu OBO OAuth 2.0

Niektóre usługi sieci web opartych na OAuth konieczne dostęp inne usługi sieci web, interfejsów API, które akceptują twierdzenia SAML-interactive przepływów. Usługa Azure Active Directory umożliwia potwierdzenie SAML w odpowiedzi w imieniu z przepływu, który używa usługi sieci web opartej na SAML jako zasób docelowy.

>[!NOTE]
>To jest rozszerzeniem niestandardowych do przepływu OAuth 2.0 "w imieniu", który umożliwia dostęp do sieci web usługi punktów końcowych interfejsu API, które korzystają z tokenów SAML aplikacji na podstawie protokołu OAuth2.

> [!TIP]
> Podczas wywoływania usługi sieci web chroniony SAML z poziomu aplikacji frontonu sieci web, możesz po prostu wywołania interfejsu API i zainicjować przepływu normalnego przeprowadzić uwierzytelnianie interakcyjne przy użyciu istniejącej sesji użytkownika. Musisz korzystać z przepływu OBO, gdy wywołanie service to service wymaga tokenu SAML, aby zapewnić kontekst użytkownika.

### <a name="obtain-a-saml-token-by-using-an-obo-request-with-a-shared-secret"></a>Uzyskiwanie tokenu SAML przy użyciu żądania OBO wspólny klucz tajny

Żądanie service to service dla asercji SAML zawiera następujące parametry:

| Parametr |  | Opis |
| --- | --- | --- |
| grant_type |Wymagane | Typ żądania tokenu. Dla żądania, który używa token JWT, wartość musi być **urn: ietf:params:oauth:grant — typ: jwt-elementu nośnego**. |
| assertion |Wymagane | Wartość tokenu dostępu, użyty w żądaniu.|
| client_id |Wymagane | Identyfikator aplikacji przypisany do wywoływania usługi podczas rejestracji w usłudze Azure AD. Aby znaleźć identyfikator aplikacji w witrynie Azure portal, wybierz **usługi Active Directory**, wybierz katalog, a następnie wybierz nazwę aplikacji. |
| client_secret |Wymagane | Klucz jest zarejestrowany dla wywołania usługi w usłudze Azure AD. Ta wartość powinna zauważyć w chwili rejestracji. |
| resource |Wymagane | Aplikacja identyfikator URI usługi odbieranie (zabezpieczono zasób). Jest to zasób, który ma być odbiorców tokenu SAML. Aby znaleźć identyfikator URI w witrynie Azure portal, wybierz **usługi Active Directory** i wybierz katalog. Wybierz nazwę aplikacji, wybierz pozycję **wszystkie ustawienia**, a następnie wybierz pozycję **właściwości**. |
| requested_token_use |Wymagane | Określa, jak można przetworzyć żądania. W przepływie w imieniu z, wartość musi być **on_behalf_of**. |
| requested_token_type | Wymagane | Określa typ żądanego tokenu. Wartość może być **urn: ietf:params:oauth:token — typ: saml2** lub **urn: ietf:params:oauth:token — typ: saml1** w zależności od wymagań dostęp do zasobów. |

Odpowiedź zawiera token SAML zakodowane w UTF8 i Base64url.

- **Źródło SubjectConfirmationData dla asercji SAML wywołanie OBO**: Jeśli aplikacja docelowa wymaga odbiorcy wartości w **SubjectConfirmationData**, a następnie wartość musi być adres URL odpowiedzi bez symboli wieloznacznych w konfiguracji aplikacji zasobu.
- **Węzeł SubjectConfirmationData**: Węzeł nie może zawierać **InResponseTo** atrybutu, ponieważ nie jest częścią odpowiedzi SAML. Aplikacja odbiera SAML token musi być w stanie zaakceptować potwierdzenie SAML bez **InResponseTo** atrybutu.

- **Zgoda**: Zgoda należy przyznać otrzymujących token SAML zawierającego dane użytkowników na przepływ OAuth. Aby uzyskać informacje na temat uprawnień i uzyskania zgody administratora, zobacz [uprawnienia i zgody w punkcie końcowym usługi Azure Active Directory w wersji 1.0](https://docs.microsoft.com/azure/active-directory/develop/v1-permissions-and-consent).

### <a name="response-with-saml-assertion"></a>Odpowiedź i potwierdzenie SAML

| Parametr | Opis |
| --- | --- |
| token_type |Wskazuje typ tokenu. Jedynym typem, który obsługuje usługi Azure AD jest **elementu nośnego**. Aby uzyskać więcej informacji na temat tokenów elementu nośnego, zobacz [OAuth 2.0 autoryzacji Framework: Użycie tokenu elementu nośnego (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| scope |Zakres dostępu przyznane w tokenie. |
| expires_in |Długość czasu, przez który token dostępu jest prawidłowy (w sekundach). |
| expires_on |Czas wygaśnięcia tokenu dostępu. Data jest reprezentowana jako liczbę sekund od 1970-01-01T0:0:0Z UTC do czasu wygaśnięcia. Ta wartość jest używana do określenia okres istnienia tokenów buforowanych. |
| resource |Aplikacja identyfikator URI usługi odbieranie (zabezpieczono zasób). |
| access_token |Parametr, który zwraca potwierdzenie SAML. |
| refresh_token |Token odświeżania. Wywoływania usługi można użyć tego tokenu do żądania inny token dostępu po wygaśnięciu bieżącego potwierdzenie SAML. |

- token_type: Elementu nośnego
- expires_in: 3296
- ext_expires_in: 0
- expires_on: 1529627844
- Zasób: `https://api.contoso.com`
- access_token: \<Potwierdzenie SAML\>
- issued_token_type: urn: ietf:params:oauth:token — typ: saml2
- refresh_token: \<Token odświeżania\>

## <a name="client-limitations"></a>Ograniczenia klienta

Publiczne klientów przy użyciu adresów URL odpowiedzi symboli wieloznacznych nie można użyć `id_token` OBO przepływów. Jednak nadal można zrealizować klientowi poufnemu **dostępu** tokenów, które zakupione w ramach przepływu przyznawanie niejawne, nawet wtedy, gdy publicznych klienta zawiera symbol wieloznaczny przekierowania zarejestrowany identyfikator URI.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat protokołu OAuth 2.0 i innym sposobem uwierzytelniania usługa Usługa poświadczeń klienta:

* [Usługa uwierzytelnianie usługi przy użyciu przyznanie poświadczenia klienta OAuth 2.0 w usłudze Azure AD](v1-oauth2-client-creds-grant-flow.md)
* [Uwierzytelnianie OAuth 2.0 w usłudze Azure AD](v1-protocols-oauth-code.md)
