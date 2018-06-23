---
title: Azure AD usług uwierzytelniania przy użyciu OAuth2.0 specyfikacji w imieniu — z projektu | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób użycia wiadomości HTTP do zaimplementowania usług uwierzytelniania za pomocą OAuth2.0 imieniu-przepływ "w".
services: active-directory
documentationcenter: .net
author: navyasric
manager: mtillman
editor: ''
ms.assetid: 09f6f318-e88b-4024-9ee1-e7f09fb19a82
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2017
ms.author: celested
ms.reviewer: hirsin, nacanuma
ms.custom: aaddev
ms.openlocfilehash: e8957db002dd6fdeaf6da03b02e518e3e423539d
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2018
ms.locfileid: "36317735"
---
# <a name="service-to-service-calls-using-delegated-user-identity-in-the-on-behalf-of-flow"></a>Usługi w celu wywołania usługi za pomocą delegowanego tożsamość użytkownika w imieniu — z przepływu
OAuth 2.0 On-Behalf-Of (OBO) przepływ służy przypadek użycia, w którym aplikacja wywołuje usługi/składnika web API, który z kolei musi wywołać inny usługi/interfejs API sieci web. Będzie propagację uprawnień za pomocą łańcucha żądań i tożsamości użytkowników delegowanego. Dla usługi warstwy środkowej na wysyłanie żądań uwierzytelnionych usługi podrzędne należy go secure token dostępu z usługi Azure Active Directory (Azure AD), w imieniu użytkownika.

> [!IMPORTANT]
> Publiczny klientów, którzy używają [OAuth 2.0 niejawne Przyznaj](active-directory-dev-understanding-oauth2-implicit-grant.md) nie można użyć przepływu OBO. Ci klienci musi przejść pomyślnie ich tokenu dostępu do warstwy środkowej poufne klienta w celu wykonania OBO przepływów. Aby uzyskać więcej informacji o tym, które klienci mogli wykonywać wywołania OBO, zobacz [ograniczenia klienta](#client-limitations).

## <a name="on-behalf-of-flow-diagram"></a>W imieniu — z diagram przepływu
Załóżmy, że użytkownik został uwierzytelniony w aplikacji przy użyciu [kodu autoryzacji protokołu OAuth 2.0 przyznać przepływu](active-directory-protocols-oauth-code.md). W tym momencie aplikacja ma token dostępu (token A) z oświadczeń użytkowników i zgody na dostęp do sieci web warstwy środkowej interfejsu API (interfejs API A). Teraz A interfejsu API musi wprowadzić żądania uwierzytelnionego podrzędne sieci web interfejsu API (interfejs API B).

Czynności, które wykonują stanowią przepływu w imieniu-z i zostały wyjaśnione przy użyciu poniższym diagramie.

![OAuth2.0 imieniu-przepływ "w"](media/active-directory-protocols-oauth-on-behalf-of/active-directory-protocols-oauth-on-behalf-of-flow.png)


1. Aplikacja kliencka zgłasza żądanie do interfejsu API, A z tokenem A.
2. A interfejsu API uwierzytelnia do punktu końcowego wystawiania tokenu usługi Azure AD i żądania tokenu na potrzeby dostępu do interfejsu API B.
3. Punkt końcowy usługi Azure AD wydawania tokenów sprawdza poprawność poświadczeń A interfejsu API z tokenów w przypadku i wystawia token dostępu do interfejsu API B (token B).
4. Token B znajduje się w nagłówku autoryzacji żądania interfejsu API B.
5. Dane zabezpieczonych zasobów jest zwracane przez interfejs API B.

## <a name="register-the-application-and-service-in-azure-ad"></a>Rejestrowanie aplikacji i usług w usłudze Azure AD
Zarejestruj zarówno aplikacja klienta, jak i usługi warstwy środkowej w usłudze Azure AD.
### <a name="register-the-middle-tier-service"></a>Zarejestruj usługę warstwy środkowej
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Na górnym pasku, kliknij na Twoim koncie i w obszarze **katalogu** wybierz dzierżawy usługi Active Directory, w którym chcesz zarejestrować aplikację.
3. Polecenie **więcej usług** w nawigacji po lewej stronie i wybierz polecenie **usługi Azure Active Directory**.
4. Polecenie **rejestracji aplikacji** i wybierz polecenie **nowej rejestracji aplikacji**.
5. Wprowadź przyjazną nazwę dla aplikacji, a następnie wybierz typ aplikacji. Oparte na zestawie typu aplikacji adres URL lub przekierowania adres URL logowania do podstawowego adresu URL. Polecenie **Utwórz** do tworzenia aplikacji.
6. Podczas nadal w portalu Azure, wybierz aplikację, a następnie kliknij przycisk na **ustawienia**. Z menu Ustawienia, wybierz **klucze** i Dodaj klucz — wybierz czas trwania klucza roku 1 lub 2 lata. Po zapisaniu tej strony, wartość klucza będzie wyświetlana, skopiuj i Zapisz wartość w bezpiecznym miejscu — należy ten klucz później, aby skonfigurować ustawienia aplikacji w implementacji — wartość tego klucza nie będą wyświetlane w ponownie ani pobieranie w inny sposób , dlatego należy zarejestrować się, gdy jest on widoczny w portalu Azure.

### <a name="register-the-client-application"></a>Rejestrowanie aplikacji klienta
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Na górnym pasku, kliknij na Twoim koncie i w obszarze **katalogu** wybierz dzierżawy usługi Active Directory, w którym chcesz zarejestrować aplikację.
3. Polecenie **więcej usług** w nawigacji po lewej stronie i wybierz polecenie **usługi Azure Active Directory**.
4. Polecenie **rejestracji aplikacji** i wybierz polecenie **nowej rejestracji aplikacji**.
5. Wprowadź przyjazną nazwę dla aplikacji, a następnie wybierz typ aplikacji. Oparte na zestawie typu aplikacji adres URL lub przekierowania adres URL logowania do podstawowego adresu URL. Polecenie **Utwórz** do tworzenia aplikacji.
6. Konfigurowanie uprawnień dla aplikacji - w menu Ustawienia, wybierz **wymagane uprawnienia** sekcji, kliknij pozycję **Dodaj**, następnie **wybierz interfejs API**i wpisz nazwę Usługa warstwy środkowej w polu tekstowym. Następnie kliknij **wybierz uprawnienia** i wybierz pozycję "dostępu *nazwa usługi*".

### <a name="configure-known-client-applications"></a>Skonfiguruj aplikacje klienckie znane
W tym scenariuszu usługa warstwy środkowej ma bez interakcji użytkownika, aby uzyskać zgodę użytkownika na dostęp do interfejsu API niższego rzędu. W związku z tym opcję, aby udzielić dostępu do interfejsu API niższego rzędu przedstawia wyprzedzeniem jako część zgody kroku podczas uwierzytelniania.
Aby to osiągnąć, wykonaj poniższe kroki, aby jawnie powiązać rejestracji aplikacji klienckiej w usłudze Azure AD z rejestracją usługi warstwy środkowej, która scala zgody wymagane przez klienta i warstwy środkowej w jednym oknie dialogowym.
1. Przejdź do rejestracji usługi warstwy środkowej, a następnie kliknij polecenie **manifestu** aby otworzyć Edytor manifestu.
2. W manifeście, zlokalizuj `knownClientApplications` tablicy właściwości, a następnie Dodaj identyfikator klienta aplikacji klienckiej jako elementu.
3. Zapisz plik manifestu, klikając pozycję Zapisz przycisku.

## <a name="service-to-service-access-token-request"></a>Usługi w celu żądania tokenu dostępu usługi
Żądania tokenu dostępu, aby HTTP POST do specyficznego dla dzierżawy punktu końcowego usługi Azure AD z następującymi parametrami.

```
https://login.microsoftonline.com/<tenant>/oauth2/token
```
Istnieją dwa przypadki, w zależności od tego, czy aplikacja kliencka zdecydował się były zabezpieczone przez Wspólny klucz tajny lub certyfikatu.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Najpierw przypadek: żądanie tokenu dostępu z wspólny klucz tajny
Korzystając z wspólny klucz tajny, żądania tokenu dostępu do usługi zawiera następujące parametry:

| Parametr |  | Opis |
| --- | --- | --- |
| Typ grant_type |wymagane | Typ żądania tokenu. Dla żądania przy użyciu token JWT, wartość musi być **urn: ietf:params:oauth:grant — typ: jwt-elementu nośnego**. |
| potwierdzenia |wymagane | Wartość tokenu użytego w żądaniu. |
| client_id |wymagane | Identyfikator aplikacji przypisany do wywoływania usługi podczas rejestracji w usłudze Azure AD. Aby znaleźć identyfikator aplikacji w portalu zarządzania Azure, kliknij przycisk **usługi Active Directory**, kliknij katalog, a następnie kliknij nazwę aplikacji. |
| client_secret |wymagane | Klucz jest zarejestrowany dla wywołania usługi w usłudze Azure AD. Ta wartość powinna zauważyć w chwili rejestracji. |
| zasób |wymagane | Identyfikator URI aplikacji odbierającej usługi (zabezpieczonych zasobów). Aby znaleźć identyfikator URI aplikacji w portalu zarządzania Azure, kliknij przycisk **usługi Active Directory**kliknij katalog, kliknij nazwę aplikacji, kliknij przycisk **wszystkie ustawienia** , a następnie kliknij przycisk **właściwości**. |
| requested_token_use |wymagane | Określa sposób przetwarzania żądania. W strumieniu w imieniu-z, wartość musi być **on_behalf_of**. |
| scope |wymagane | Lista zakresów dla żądania tokenu rozdzielonych spacją. Dla protokołu OpenID Connect, zakres **openid** musi być określona.|

#### <a name="example"></a>Przykład
Token dostępu dla żądań następujące HTTP POST https://graph.windows.net interfejs API sieci web. `client_id` Identyfikuje usługę żądania tokenu dostępu.

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

### <a name="second-case-access-token-request-with-a-certificate"></a>W drugim przypadku: żądanie tokenu dostępu przy użyciu certyfikatu
Żądanie tokenu dostępu do usługi przy użyciu certyfikatu zawiera następujące parametry:

| Parametr |  | Opis |
| --- | --- | --- |
| Typ grant_type |wymagane | Typ żądania tokenu. Dla żądania przy użyciu token JWT, wartość musi być **urn: ietf:params:oauth:grant — typ: jwt-elementu nośnego**. |
| potwierdzenia |wymagane | Wartość tokenu użytego w żądaniu. |
| client_id |wymagane | Identyfikator aplikacji przypisany do wywoływania usługi podczas rejestracji w usłudze Azure AD. Aby znaleźć identyfikator aplikacji w portalu zarządzania Azure, kliknij przycisk **usługi Active Directory**, kliknij katalog, a następnie kliknij nazwę aplikacji. |
| client_assertion_type |wymagane |Wartość musi być `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |wymagane | (JSON Web Token) potwierdzenia, że musisz utworzyć i podpisać przy użyciu certyfikatu został zarejestrowany jako poświadczeń dla aplikacji. Przeczytaj informacje o [certyfikatu poświadczeń](active-directory-certificate-credentials.md) informacje na temat rejestracji certyfikatu i format potwierdzenia.|
| zasób |wymagane | Identyfikator URI aplikacji odbierającej usługi (zabezpieczonych zasobów). Aby znaleźć identyfikator URI aplikacji w portalu zarządzania Azure, kliknij przycisk **usługi Active Directory**kliknij katalog, kliknij nazwę aplikacji, kliknij przycisk **wszystkie ustawienia** , a następnie kliknij przycisk **właściwości**. |
| requested_token_use |wymagane | Określa sposób przetwarzania żądania. W strumieniu w imieniu-z, wartość musi być **on_behalf_of**. |
| scope |wymagane | Lista zakresów dla żądania tokenu rozdzielonych spacją. Dla protokołu OpenID Connect, zakres **openid** musi być określona.|

Należy zauważyć, że parametry są prawie takie same jak w przypadku żądania przez Wspólny klucz tajny, z wyjątkiem tego, że parametr client_secret zostało zastąpione przez dwa parametry: client_assertion_type i client_assertion.

#### <a name="example"></a>Przykład
Token dostępu dla żądań następujące HTTP POST https://graph.windows.net interfejs API sieci web przy użyciu certyfikatu. `client_id` Identyfikuje usługę żądania tokenu dostępu.

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

## <a name="service-to-service-access-token-response"></a>Usługa do odpowiedzi tokenu dostępu usługi
Odpowiedź sukcesu jest odpowiedź JSON OAuth 2.0 z następującymi parametrami.

| Parametr | Opis |
| --- | --- |
| token_type |Wskazuje wartość typ tokenu. Jedynym typem, który obsługuje usługę Azure AD jest **elementu nośnego**. Aby uzyskać więcej informacji dotyczących tokenów elementu nośnego, zobacz [OAuth 2.0 autoryzacji Framework: użycie tokenu elementu nośnego (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt). |
| scope |Zakres dostępu przyznane w tokenie. |
| expires_in |Czas token dostępu jest nieprawidłowy (w sekundach). |
| expires_on |Czas wygaśnięcia tokenu dostępu. Data jest reprezentowana jako liczbę sekund z rokiem 1970-01-01T0:0:0Z UTC czasu wygaśnięcia. Ta wartość jest używana do określenia okres istnienia pamięci podręcznej tokenów. |
| zasób |Identyfikator URI aplikacji odbierającej usługi (zabezpieczonych zasobów). |
| access_token |Żądany dostęp token. Wywołanie usługi umożliwia ten token uwierzytelniania w usłudze odbierania. |
| żądaniu |Token żądanym identyfikatorem. Wywołanie usługi służy to do weryfikacji tożsamości użytkownika i rozpocząć sesję użytkownika. |
| refresh_token |Token odświeżania dla tokenu żądany dostęp. Wywołanie usługi umożliwia żądania inny token dostępu po wygaśnięciu tokenu dostępu bieżącego ten token. |

### <a name="success-response-example"></a>Przykład odpowiedź sukcesu
W poniższym przykładzie przedstawiono Powodzenie odpowiedzi na żądanie dostępu do tokenu dla https://graph.windows.net interfejs API sieci web.

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
Odpowiedzi na błąd jest zwracany przez punkt końcowy tokenu usługi Azure AD podczas próby uzyskania tokenu dostępu dla interfejsu API podrzędne, jeśli zasady dostępu warunkowego, takich jak uwierzytelnianie wieloskładnikowe, ustaw w niej ma podrzędne interfejsu API. Usługi warstwy środkowej powinien powierzchni ten błąd do aplikacji klienckiej, aby aplikacja kliencka zapewniają interakcji użytkownika, aby spełniać zasady dostępu warunkowego.

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

## <a name="use-the-access-token-to-access-the-secured-resource"></a>Użyj tokenu dostępu, aby uzyskać dostępu do zabezpieczonych zasobów
Obecnie usługa warstwy środkowej może używać tokenu nabyte powyżej, aby uwierzytelnionego żądania podrzędnego sieci Web interfejsu API, ustawiając token w `Authorization` nagłówka.

### <a name="example"></a>Przykład
```
GET /me?api-version=2013-11-08 HTTP/1.1
Host: graph.windows.net
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ
```
## <a name="client-limitations"></a>Ograniczenia klienta
Publiczny klientów z adresami URL odpowiedzi symboli wieloznacznych nie można użyć `id_token` OBO przepływów. Jednak klientowi poufne nadal można zrealizować tokeny dostępu zakupione w ramach przepływu niejawne Przyznaj nawet w przypadku publicznych klient ma zarejestrowany identyfikator URI przekierowania symboli wieloznacznych.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej na temat protokołu OAuth 2.0 i innym sposobem wykonania uwierzytelniania usług przy użyciu poświadczeń klienta.
* [Usługi w celu uwierzytelniania usługi przy użyciu przyznania poświadczeń klienta OAuth 2.0 w usłudze Azure AD](active-directory-protocols-oauth-service-to-service.md)
* [OAuth 2.0 w usłudze Azure AD](active-directory-protocols-oauth-code.md)
