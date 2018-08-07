---
title: Usługami uwierzytelnianie usługi Azure AD za pomocą OAuth 2.0 specyfikacji wersji roboczej w imieniu z | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak używać wiadomości HTTP do zaimplementowania uwierzytelniania usług za pomocą OAuth 2.0 w imieniu użytkownika z usługi flow.
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
ms.openlocfilehash: ce29c6a9df49721ca23f84da3f1c97bcc83ab4a7
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2018
ms.locfileid: "39581464"
---
# <a name="service-to-service-calls-using-delegated-user-identity-in-the-on-behalf-of-flow"></a>Wywołania Usługa do usługi za pomocą delegowanego tożsamości użytkownika w usłudze flow w imieniu z
OAuth 2.0 "w imieniu" (OBO) usługi flow obsługuje przypadek użycia, w którym aplikacja wywołuje usługi/interfejsu web API, która z kolei musi wywołać inną usługę/internetowy interfejs API. Chodzi o to propagację delegowany użytkownik tożsamości i uprawnień w łańcuchu żądanie. Usługi warstwy środkowej na wysyłanie żądań uwierzytelnionych usługi transmisji musi ona secure token dostępu z usługi Azure Active Directory (Azure AD) w imieniu użytkownika.

> [!IMPORTANT]
> Publiczne klientów, którzy używają [przyznawanie niejawne protokołu OAuth 2.0](v1-oauth2-implicit-grant-flow.md) nie może korzystać z tego przepływu OBO. Ci klienci przekazać ich token dostępu do warstwy środkowej poufne klienta do wykonania przepływu OBO. Aby uzyskać więcej informacji o tym, które klienci będą mogli wykonywać wywołania OBO, zobacz [ograniczenia klienta](#client-limitations).

## <a name="on-behalf-of-flow-diagram"></a>W imieniu z diagramu przepływu
Przyjęto założenie, że użytkownik został uwierzytelniony w aplikacji za pomocą [kod autoryzacji OAuth 2.0 udzielić przepływ](v1-protocols-oauth-code.md). W tym momencie aplikacja ma tokenu dostępu (token A) przy użyciu oświadczeń użytkowników i zgody na dostęp do interfejsu API (interfejs API A) sieci web warstwy środkowej. Teraz A interfejs API musi dokonać uwierzytelnionego żądania podrzędnego internetowego interfejsu API (interfejs API B).

Poniższe kroki stanowią przepływu w imieniu z i zostały wyjaśnione przy pomocy poniższym diagramie.

![OAuth 2.0 w imieniu użytkownika z usługi Flow](./media/v1-oauth2-on-behalf-of-flow/active-directory-protocols-oauth-on-behalf-of-flow.png)


1. Aplikacja kliencka wysyła żądanie do interfejsu API, A za pomocą tokenu A.
2. A interfejs API jest uwierzytelniany punktu końcowego wystawiania tokenu usługi Azure AD i żądania tokenu służącego do dostępu do interfejsu API B.
3. Punkt końcowy wystawiania tokenu usługi Azure AD przeprowadza walidację poświadczeń A interfejsu API, a token i wystawia token dostępu dla aplikacji interfejsu API B (token B).
4. Token B ustawiono w nagłówku autoryzacji żądania do interfejsu API B.
5. Dane z zabezpieczono zasób jest zwracany przez interfejs API B.

## <a name="register-the-application-and-service-in-azure-ad"></a>Rejestrowanie aplikacji i usług w usłudze Azure AD
Rejestrowanie aplikacji klienckiej i usługi warstwy środkowej w usłudze Azure AD.
### <a name="register-the-middle-tier-service"></a>Rejestrowanie usługi warstwy środkowej
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Na górnym pasku kliknij na Twoim koncie, a w obszarze **katalogu** wybierz dzierżawę usługi Active Directory, w którym chcesz zarejestrować aplikację.
3. Kliknij pozycję **więcej usług** w okienku nawigacji po lewej stronie ekranu i wybierz polecenie **usługi Azure Active Directory**.
4. Kliknij pozycję **rejestracje aplikacji** i wybierz polecenie **rejestrowanie nowej aplikacji**.
5. Wprowadź przyjazną nazwę dla aplikacji, a następnie wybierz typ aplikacji. Na podstawie aplikacji typu zestawu adres URL lub przekierowania adres URL logowania do podstawowego adresu URL. Kliknij pozycję **Utwórz** do tworzenia aplikacji.
6. Mając nadal w witrynie Azure portal, wybierz aplikację, a następnie kliknij przycisk na **ustawienia**. Wybierz z menu ustawienia **klucze** i dodać klucz — wybierz czas trwania klucza roczny lub 2 lata. Po zapisaniu tej strony, będzie wyświetlana wartość klucza, skopiuj i Zapisz wartość w bezpiecznym miejscu — będzie będzie on potrzebny później, aby skonfigurować ustawienia aplikacji w danej implementacji — wartość tego klucza nie będzie wyświetlany ponownie ani pobieranie w inny sposób , dzięki czemu Zarejestruj go jak najszybciej jest widoczny w witrynie Azure Portal.

### <a name="register-the-client-application"></a>Rejestrowanie aplikacji klienta
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Na górnym pasku kliknij na Twoim koncie, a w obszarze **katalogu** wybierz dzierżawę usługi Active Directory, w którym chcesz zarejestrować aplikację.
3. Kliknij pozycję **więcej usług** w okienku nawigacji po lewej stronie ekranu i wybierz polecenie **usługi Azure Active Directory**.
4. Kliknij pozycję **rejestracje aplikacji** i wybierz polecenie **rejestrowanie nowej aplikacji**.
5. Wprowadź przyjazną nazwę dla aplikacji, a następnie wybierz typ aplikacji. Na podstawie aplikacji typu zestawu adres URL lub przekierowania adres URL logowania do podstawowego adresu URL. Kliknij pozycję **Utwórz** do tworzenia aplikacji.
6. Konfigurowanie uprawnień dla aplikacji — w menu Ustawienia, wybierz polecenie **wymagane uprawnienia** sekcji, kliknij pozycję **Dodaj**, następnie **wybierz interfejs API**, a następnie wpisz nazwę Usługa warstwy środkowej, w polu tekstowym. Następnie kliknij **wybierz uprawnienia** i wybierz pozycję "dostęp *nazwa usługi*".

### <a name="configure-known-client-applications"></a>Konfiguruj aplikacje klienckie znane
W tym scenariuszu usługa warstwy środkowej ma bez interakcji użytkownika w celu uzyskania zgody użytkownika dostępu do podrzędnego interfejsu API. W związku z tym opcję, aby udzielić dostępu do interfejsu API niższego rzędu muszą być przedstawione z wyprzedzeniem, jako część zgody kroku podczas uwierzytelniania.
Aby to osiągnąć, wykonaj poniższe kroki, aby jawnie powiązać rejestracji aplikacji klienckiej w usłudze Azure AD za pomocą rejestracji usługi warstwy środkowej, która scala zgody wymagane przez klienta i warstwy środkowej w jednym oknie dialogowym.
1. Przejdź do rejestracji usługi warstwy środkowej, a następnie kliknij **manifestu** celu otwórz Edytor manifestów.
2. W manifeście, zlokalizuj `knownClientApplications` tablicy właściwości i dodać identyfikator klienta aplikacji klienckiej jako element.
3. Zapisanie manifestu, klikając pozycję Zapisz przycisku.

## <a name="service-to-service-access-token-request"></a>Usługa na żądanie tokenu dostępu usługi
Do żądania tokenu dostępu, metody HTTP POST do konkretnej dzierżawy punktu końcowego usługi Azure AD z następującymi parametrami.

```
https://login.microsoftonline.com/<tenant>/oauth2/token
```
Istnieją dwa przypadki, w zależności od tego, czy aplikacja kliencka zdecyduje się na chronione przez wspólne hasło lub certyfikat.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Pierwszy przypadek: żądanie tokenu dostępu za pomocą wspólny klucz tajny
Korzystając z wspólny klucz tajny, żądania tokenu dostępu do usługi zawiera następujące informacje:

| Parametr |  | Opis |
| --- | --- | --- |
| grant_type |wymagane | Typ żądania tokenu. Na żądanie przy użyciu token JWT, wartość musi być **urn: ietf:params:oauth:grant — typ: jwt-elementu nośnego**. |
| potwierdzenie |wymagane | Wartość tokenu użytego w żądaniu. |
| client_id |wymagane | Identyfikator aplikacji przypisany do wywoływania usługi podczas rejestracji w usłudze Azure AD. Aby znaleźć identyfikator aplikacji w portalu zarządzania systemu Azure, kliknij przycisk **usługi Active Directory**, kliknij katalog, a następnie kliknij nazwę aplikacji. |
| client_secret |wymagane | Klucz jest zarejestrowany dla wywołania usługi w usłudze Azure AD. Ta wartość powinna zauważyć w chwili rejestracji. |
| zasób |wymagane | Identyfikator URI Identyfikatora aplikacji odbierającej usługi (zabezpieczono zasób). Aby znaleźć identyfikator URI aplikacji w portalu zarządzania systemu Azure, kliknij przycisk **usługi Active Directory**kliknij katalog, kliknij nazwę aplikacji, kliknij przycisk **wszystkie ustawienia** a następnie kliknij przycisk **właściwości**. |
| requested_token_use |wymagane | Określa, jak można przetworzyć żądania. W przepływie w imieniu z, wartość musi być **on_behalf_of**. |
| scope |wymagane | Spacjami listy zakresów dla żądania tokenu. Dla protokołu OpenID Connect, zakres **openid** musi być określona.|

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

### <a name="second-case-access-token-request-with-a-certificate"></a>Drugi przypadek: żądanie tokenu dostępu przy użyciu certyfikatu
Żądanie tokenu dostępu do usługi, za pomocą certyfikatu zawiera następujące parametry:

| Parametr |  | Opis |
| --- | --- | --- |
| grant_type |wymagane | Typ żądania tokenu. Na żądanie przy użyciu token JWT, wartość musi być **urn: ietf:params:oauth:grant — typ: jwt-elementu nośnego**. |
| potwierdzenie |wymagane | Wartość tokenu użytego w żądaniu. |
| client_id |wymagane | Identyfikator aplikacji przypisany do wywoływania usługi podczas rejestracji w usłudze Azure AD. Aby znaleźć identyfikator aplikacji w portalu zarządzania systemu Azure, kliknij przycisk **usługi Active Directory**, kliknij katalog, a następnie kliknij nazwę aplikacji. |
| client_assertion_type |wymagane |Wartość musi być `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |wymagane | Potwierdzenie (JSON Web Token), musisz utworzyć i podpisać za pomocą certyfikatu rejestracji w charakterze poświadczenia dla aplikacji. Przeczytaj o [certyfikatu poświadczeń](active-directory-certificate-credentials.md) informacje na temat rejestracji certyfikatu i format potwierdzenia.|
| zasób |wymagane | Identyfikator URI Identyfikatora aplikacji odbierającej usługi (zabezpieczono zasób). Aby znaleźć identyfikator URI aplikacji w portalu zarządzania systemu Azure, kliknij przycisk **usługi Active Directory**kliknij katalog, kliknij nazwę aplikacji, kliknij przycisk **wszystkie ustawienia** a następnie kliknij przycisk **właściwości**. |
| requested_token_use |wymagane | Określa, jak można przetworzyć żądania. W przepływie w imieniu z, wartość musi być **on_behalf_of**. |
| scope |wymagane | Spacjami listy zakresów dla żądania tokenu. Dla protokołu OpenID Connect, zakres **openid** musi być określona.|

Należy zauważyć, że parametry są prawie takie same jak w przypadku żądania przez Wspólny klucz tajny, z tą różnicą, że parametr client_secret zostaje zastąpiona przez dwa parametry: client_assertion_type i client_assertion.

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

## <a name="service-to-service-access-token-response"></a>Usługa odpowiedzi tokenu dostępu usługi
Odpowiedź sukcesu jest odpowiedź JSON OAuth 2.0, z następującymi parametrami.

| Parametr | Opis |
| --- | --- |
| token_type |Wskazuje typ tokenu. Jedynym typem, który obsługuje usługi Azure AD jest **elementu nośnego**. Aby uzyskać więcej informacji na temat tokenów elementu nośnego, zobacz [OAuth 2.0 autoryzacji Framework: użycie tokenu elementu nośnego (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt). |
| scope |Zakres dostępu przyznane w tokenie. |
| expires_in |Długość czasu, przez który token dostępu jest prawidłowy (w sekundach). |
| expires_on |Czas wygaśnięcia tokenu dostępu. Data jest reprezentowana jako liczbę sekund od 1970-01-01T0:0:0Z UTC do czasu wygaśnięcia. Ta wartość jest używana do określenia okres istnienia tokenów buforowanych. |
| zasób |Identyfikator URI Identyfikatora aplikacji odbierającej usługi (zabezpieczono zasób). |
| access_token |Token żądanego dostępu. Wywoływania usługi można użyć tego tokenu do uwierzytelnienia w usłudze odbierania. |
| id_token |Token żądanym identyfikatorem. Wywoływania usługi umożliwia to zweryfikowanie tożsamości użytkownika i rozpocząć sesję z użytkownikiem. |
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
Odpowiedź na błąd jest zwracany przez punkt końcowy tokenu usługi Azure AD, podczas próby uzyskania tokenu dostępu dla interfejsu API niższego rzędu, jeśli zasady dostępu warunkowego, takie jak uwierzytelnianie wieloskładnikowe, ustaw na nim podrzędnego interfejsu API. Usługi warstwy środkowej należy przedstawić ten błąd do aplikacji klienckiej, tak, aby aplikacja kliencka może zapewnić interakcję użytkownika, aby spełnić wymagania zasad dostępu warunkowego.

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
Teraz usługi warstwy środkowej używać tokenu nabyte powyżej dokonać uwierzytelnionego żądania podrzędnego internetowego interfejsu API, ustawiając token w `Authorization` nagłówka.

### <a name="example"></a>Przykład
```
GET /me?api-version=2013-11-08 HTTP/1.1
Host: graph.windows.net
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ
```
## <a name="client-limitations"></a>Ograniczenia klienta
Publiczne klientów przy użyciu adresów URL odpowiedzi symboli wieloznacznych nie można użyć `id_token` OBO przepływów. Jednak poufne klienta, nadal można zrealizować tokenów dostępu, które zakupione w ramach przepływu przyznawanie niejawne, nawet wtedy, gdy publicznych klienta został zarejestrowany identyfikator URI przekierowania symboli wieloznacznych.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej na temat protokołu OAuth 2.0 i innym sposobem uwierzytelniania usług przy użyciu poświadczeń klienta.
* [Usługa uwierzytelnianie usługi przy użyciu przyznanie poświadczenia klienta OAuth 2.0 w usłudze Azure AD](v1-oauth2-client-creds-grant-flow.md)
* [Uwierzytelnianie OAuth 2.0 w usłudze Azure AD](v1-protocols-oauth-code.md)
