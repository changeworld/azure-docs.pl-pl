---
title: Uwierzytelnianie usługi do usługi za pomocą usługi OAuth2.0 w imieniu przepływu | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób używania wiadomości HTTP do implementowania uwierzytelniania usługi do usługi za pomocą przepływu W imieniu użytkownika OAuth2.0.
services: active-directory
documentationcenter: .net
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: ryanwi
ms.reviewer: hirsin, nacanuma
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: a301029f30a77f4e62ad3529aac488a81c12566e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154529"
---
# <a name="service-to-service-calls-that-use-delegated-user-identity-in-the-on-behalf-of-flow"></a>Wywołania usługi do usługi, które używają delegowanej tożsamości użytkownika w przepływie w imieniu

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Przepływ OAuth 2.0 w imieniu (OBO) umożliwia aplikacji, która wywołuje usługi lub interfejsu API sieci web do przekazywania uwierzytelniania użytkownika do innej usługi lub interfejsu API sieci web. Przepływ OBO propaguje delegowane tożsamości użytkownika i uprawnienia za pośrednictwem łańcucha żądań. Aby usługa warstwy środkowej była zabezpieczona do usługi podrzędnej, musi zabezpieczyć token dostępu z usługi Azure Active Directory (Azure AD) w imieniu użytkownika.

> [!IMPORTANT]
> Od maja 2018 `id_token` r. nie można używać przepływu w imieniu.  Aplikacje jednostronicowe (SPA) muszą przekazać token dostępu do poufnego klienta warstwy środkowej w celu wykonywania przepływów OBO. Aby uzyskać więcej informacji na temat klientów, którzy mogą wykonywać wywołania w imieniu, zobacz [ograniczenia](#client-limitations).

## <a name="on-behalf-of-flow-diagram"></a>Diagram przepływu w imieniu

Przepływ OBO rozpoczyna się po uwierzytelnieniu użytkownika w aplikacji, która używa [przepływu udzielania kodu autoryzacyjnego OAuth 2.0.](v1-protocols-oauth-code.md) W tym momencie aplikacja wysyła token dostępu (token A) do interfejsu API sieci web warstwy środkowej (API A) zawierającego oświadczenia użytkownika i zgodę na dostęp do interfejsu API A. Następnie api A sprawia, że uwierzytelnione żądanie do podrzędnego interfejsu API sieci web (API B).

Te kroki stanowią przepływ w imieniu: ![pokazuje kroki w przepływie OAuth2.0 w imieniu](./media/v1-oauth2-on-behalf-of-flow/active-directory-protocols-oauth-on-behalf-of-flow.png)

1. Aplikacja kliencka sprawia, że żądanie do interfejsu API A z tokenem A.
1. Interfejs API A uwierzytelnia się w punkcie końcowym wystawienia tokenu usługi Azure AD i żąda tokenu dostępu do interfejsu API B.
1. Punkt końcowy wystawiania tokenu usługi Azure AD sprawdza poprawność poświadczeń interfejsu API A za pomocą tokenu A i wystawia token dostępu dla interfejsu API B (token B).
1. Żądanie do interfejsu API B zawiera token B w nagłówku autoryzacji.
1. Interfejs API B zwraca dane z zabezpieczonego zasobu.

>[!NOTE]
>Oświadczenie odbiorców w tokenie dostępu używanym do żądania tokenu dla usługi podrzędnej musi być identyfikatorem usługi składającej żądanie OBO. Token również musi być podpisany za pomocą globalnego klucza podpisywania usługi Azure Active Directory (który jest domyślny dla aplikacji zarejestrowanych za pośrednictwem **rejestracji aplikacji** w portalu).

## <a name="register-the-application-and-service-in-azure-ad"></a>Rejestrowanie aplikacji i usługi w usłudze Azure AD

Zarejestruj zarówno usługę warstwy środkowej, jak i aplikację kliencką w usłudze Azure AD.

### <a name="register-the-middle-tier-service"></a>Rejestrowanie usługi warstwy środkowej

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Na górnym pasku wybierz swoje konto i zajrzyj do listy **Katalog,** aby wybrać dzierżawę usługi Active Directory dla aplikacji.
1. Wybierz **pozycję Więcej usług** w lewym okienku i wybierz pozycję Azure Active **Directory**.
1. Wybierz **pozycję Rejestracje aplikacji,** a następnie **pozycję Nowa rejestracja**.
1. Wprowadź przyjazną nazwę aplikacji i wybierz typ aplikacji.
1. W obszarze **Obsługiwane typy kont** wybierz pozycję **Konta w dowolnym katalogu organizacyjnym i konta osobiste Microsoft**.
1. Ustaw identyfikator URI przekierowania do podstawowego adresu URL.
1. Wybierz pozycję **Zarejestruj**, aby utworzyć aplikację.
1. Generowanie klucza tajnego klienta przed zamknięciem witryny Azure portal.
1. W witrynie Azure portal wybierz aplikację i wybierz **pozycję Certyfikaty & wpisy tajne**.
1. Wybierz **nowy klucz tajny klienta** i dodaj klucz tajny o czasie trwania jednego roku lub dwóch lat.
1. Po zapisaniu tej strony w witrynie Azure Portal jest wyświetlana wartość tajnego. Skopiuj i zapisz tajną wartość w bezpiecznym miejscu.

> [!IMPORTANT]
> Klucz tajny jest potrzebny do skonfigurowania ustawień aplikacji w implementacji. Ta wartość tajnego nie jest wyświetlana ponownie i nie można jej pobrać w żaden inny sposób. Nagraj go tak szybko, jak jest widoczny w witrynie Azure portal.

### <a name="register-the-client-application"></a>Zarejestruj aplikację kliencką

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Na górnym pasku wybierz swoje konto i zajrzyj do listy **Katalog,** aby wybrać dzierżawę usługi Active Directory dla aplikacji.
1. Wybierz **pozycję Więcej usług** w lewym okienku i wybierz pozycję Azure Active **Directory**.
1. Wybierz **pozycję Rejestracje aplikacji,** a następnie **pozycję Nowa rejestracja**.
1. Wprowadź przyjazną nazwę aplikacji i wybierz typ aplikacji.
1. W obszarze **Obsługiwane typy kont** wybierz pozycję **Konta w dowolnym katalogu organizacyjnym i konta osobiste Microsoft**.
1. Ustaw identyfikator URI przekierowania do podstawowego adresu URL.
1. Wybierz pozycję **Zarejestruj**, aby utworzyć aplikację.
1. Skonfiguruj uprawnienia dla aplikacji. W **obszarze Uprawnienia interfejsu API**wybierz pozycję Dodaj **uprawnienie,** a następnie pozycję Moje **interfejsy API**.
1. Wpisz nazwę usługi warstwy środkowej w polu tekstowym.
1. Wybierz **pozycję Wybierz uprawnienia,** a następnie wybierz pozycję **>nazwa usługi programu Access \< **.

### <a name="configure-known-client-applications"></a>Konfigurowanie znanych aplikacji klienckich

W tym scenariuszu usługi warstwy środkowej musi uzyskać zgodę użytkownika na dostęp do interfejsu API podrzędnego bez interakcji z użytkownikiem. Opcja udzielenia dostępu do interfejsu API podrzędnego musi być przedstawiona z góry jako część kroku zgody podczas uwierzytelniania.

Wykonaj poniższe kroki, aby jawnie powiązać rejestrację aplikacji klienckiej w usłudze Azure AD z rejestracją usługi warstwy środkowej. Ta operacja scala zgody wymagane przez klienta i warstwy środkowej w jednym oknie dialogowym.

1. Przejdź do rejestracji usługi warstwy środkowej i wybierz **manifest,** aby otworzyć edytor manifestu.
1. Znajdź `knownClientApplications` właściwość tablicy i dodaj identyfikator klienta aplikacji klienckiej jako element.
1. Zapisz manifest, wybierając pozycję **Zapisz**.

## <a name="service-to-service-access-token-request"></a>Żądanie tokenu dostępu usługi do usługi

Aby zażądać tokenu dostępu, należy wprowadzić wpis HTTP do punktu końcowego usługi Azure AD specyficzne dla dzierżawy z następującymi parametrami:

```
https://login.microsoftonline.com/<tenant>/oauth2/token
```

Aplikacja kliencka jest zabezpieczona przez wspólny klucz tajny lub certyfikat.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Pierwszy przypadek: żądanie tokenu dostępu z udostępnionym kluczem tajnym

Podczas korzystania z udostępnionego klucza tajnego żądanie tokenu dostępu usługi do usługi zawiera następujące parametry:

| Parametr |  | Opis |
| --- | --- | --- |
| grant_type |wymagany | Typ żądania tokenu. Żądanie OBO używa tokenu JSON Web Token (JWT), więc wartość musi być **urn:ietf:params:oauth:grant-type:jwt-bearer**. |
| Potwierdzenia |wymagany | Wartość tokenu dostępu używanego w żądaniu. |
| client_id |wymagany | Identyfikator aplikacji przypisany do usługi wywołującej podczas rejestracji w usłudze Azure AD. Aby znaleźć identyfikator aplikacji w witrynie Azure portal, wybierz pozycję **Active Directory**, wybierz katalog, a następnie wybierz nazwę aplikacji. |
| client_secret |wymagany | Klucz zarejestrowany dla usługi wywołującej w usłudze Azure AD. Wartość ta powinna być odnotowana w momencie rejestracji. |
| zasób |wymagany | Identyfikator identyfikatora aplikacji usługi odbierającej (zasób zabezpieczony). Aby znaleźć identyfikator URI identyfikatora aplikacji w witrynie Azure portal, wybierz pozycję **Active Directory** i wybierz katalog. Wybierz nazwę aplikacji, wybierz pozycję **Wszystkie ustawienia**, a następnie wybierz pozycję **Właściwości**. |
| requested_token_use |wymagany | Określa sposób przetwarzania żądania. W przepływie w imieniu musi być **on_behalf_of**. |
| scope |wymagany | Przestrzeń oddzielona listą zakresów dla żądania tokenu. W przypadku OpenID Connect należy określić **openid** zakresu.|

#### <a name="example"></a>Przykład

Następujący protokół HTTP POST żąda https://graph.microsoft.com tokenu dostępu dla internetowego interfejsu API. Identyfikuje `client_id` usługę, która żąda tokenu dostępu.

```
// line breaks for legibility only

POST /oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_secret=0Y1W%2BY3yYb3d9N8vSjvm8WrGzVZaAaHbHHcGbcgG%2BoI%3D
&resource=https%3A%2F%2Fgraph.microsoft.com
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.ewogICJhdWQiOiAiaHR0cHM6Ly9ncmFwaC5taWNyb3NvZnQuY29tIiwKICAiaXNzIjogImh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLAogICJpYXQiOiAxNDkzNDIzMTY4LAogICJuYmYiOiAxNDkzNDIzMTY4LAogICJleHAiOiAxNDkzNDY2OTUxLAogICJhY3IiOiAiMSIsCiAgImFpbyI6ICJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsCiAgImFtciI6IFsKICAgICJwd2QiCiAgXSwKICAiYXBwaWQiOiAiNjI1MzkxYWYtYzY3NS00M2U1LThlNDQtZWRkM2UzMGNlYjE1IiwKICAiYXBwaWRhY3IiOiAiMSIsCiAgImVfZXhwIjogMzAyNjgzLAogICJmYW1pbHlfbmFtZSI6ICJUZXN0IiwKICAiZ2l2ZW5fbmFtZSI6ICJOYXZ5YSIsCiAgImlwYWRkciI6ICIxNjcuMjIwLjEuMTc3IiwKICAibmFtZSI6ICJOYXZ5YSBUZXN0IiwKICAib2lkIjogIjFjZDRiY2FjLWI4MDgtNDIzYS05ZTJmLTgyN2ZiYjFiYjczOSIsCiAgInBsYXRmIjogIjMiLAogICJwdWlkIjogIjEwMDMzRkZGQTEyRUQ3RkUiLAogICJzY3AiOiAiVXNlci5SZWFkIiwKICAic3ViIjogIjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLAogICJ0aWQiOiAiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwKICAidW5pcXVlX25hbWUiOiAibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLAogICJ1cG4iOiAibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLAogICJ1dGkiOiAieEN3ZnpoYS1QMFdKUU9MeENHZ0tBQSIsCiAgInZlciI6ICIxLjAiCn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ
&requested_token_use=on_behalf_of
&scope=openid
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Drugi przypadek: żądanie tokenu dostępu z certyfikatem

Żądanie tokenu dostępu usługi do usługi z certyfikatem zawiera następujące parametry:

| Parametr |  | Opis |
| --- | --- | --- |
| grant_type |wymagany | Typ żądania tokenu. Żądanie OBO używa tokenu dostępu JWT, więc wartość musi być **urn:ietf:params:oauth:grant-type:jwt-bearer**. |
| Potwierdzenia |wymagany | Wartość tokenu używanego w żądaniu. |
| client_id |wymagany | Identyfikator aplikacji przypisany do usługi wywołującej podczas rejestracji w usłudze Azure AD. Aby znaleźć identyfikator aplikacji w witrynie Azure portal, wybierz pozycję **Active Directory**, wybierz katalog, a następnie wybierz nazwę aplikacji. |
| client_assertion_type |wymagany |Wartość musi być`urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |wymagany | Token sieci Web JSON, który tworzysz i podpisujesz za pomocą certyfikatu zarejestrowanego jako poświadczenia dla aplikacji. Zobacz [poświadczenia certyfikatu,](../develop/active-directory-certificate-credentials.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) aby dowiedzieć się więcej o formacie potwierdzenia i o tym, jak zarejestrować certyfikat.|
| zasób |wymagany | Identyfikator identyfikatora aplikacji usługi odbierającej (zasób zabezpieczony). Aby znaleźć identyfikator URI identyfikatora aplikacji w witrynie Azure portal, wybierz pozycję **Active Directory** i wybierz katalog. Wybierz nazwę aplikacji, wybierz pozycję **Wszystkie ustawienia**, a następnie wybierz pozycję **Właściwości**. |
| requested_token_use |wymagany | Określa sposób przetwarzania żądania. W przepływie w imieniu musi być **on_behalf_of**. |
| scope |wymagany | Przestrzeń oddzielona listą zakresów dla żądania tokenu. W przypadku OpenID Connect należy określić **openid** zakresu.|

Parametry te są prawie takie same jak w `client_secret parameter` żądaniu przez wspólny `client_assertion_type` `client_assertion`klucz tajny, z tą różnicą, że jest zastępowany przez dwa parametry: i .

#### <a name="example"></a>Przykład

Następujący protokół HTTP POST żąda https://graph.microsoft.com tokenu dostępu dla internetowego interfejsu API z certyfikatem. Identyfikuje `client_id` usługę, która żąda tokenu dostępu.

```
// line breaks for legibility only

POST /oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&resource=https%3A%2F%2Fgraph.microsoft.com
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=openid
```

## <a name="service-to-service-access-token-response"></a>Odpowiedź tokenu dostępu usługi do usługi

Odpowiedzią na sukces jest odpowiedź JSON OAuth 2.0 z następującymi parametrami:

| Parametr | Opis |
| --- | --- |
| token_type |Wskazuje wartość typu tokenu. Jedynym typem, który obsługuje usługę Azure AD jest **Bearer**. Aby uzyskać więcej informacji na temat tokenów nośnika, zobacz [OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| scope |Zakres dostępu przyznanego w tokenie. |
| expires_in |Czas, przez który token dostępu jest prawidłowy (w sekundach). |
| expires_on |Czas wygaśnięcia tokenu dostępu. Data jest reprezentowana jako liczba sekund od 1970-01-01T0:0:0Z UTC do czasu wygaśnięcia. Ta wartość jest używana do określenia okresu istnienia tokenów buforowanych. |
| zasób |Identyfikator identyfikatora aplikacji usługi odbierającej (zasób zabezpieczony). |
| access_token |Żądany token dostępu. Usługa wywołująca może używać tego tokenu do uwierzytelniania w usłudze odbierającej. |
| id_token |Żądany token identyfikatora. Usługa wywołująca może użyć tego tokenu, aby zweryfikować tożsamość użytkownika i rozpocząć sesję z użytkownikiem. |
| refresh_token |Token odświeżania żądanego tokenu dostępu. Usługa wywołująca może użyć tego tokenu, aby zażądać innego tokenu dostępu po wygaśnięciu bieżącego tokenu dostępu. |

### <a name="success-response-example"></a>Przykład odpowiedzi na sukces

W poniższym przykładzie pokazano odpowiedź na powodzenie https://graph.microsoft.com żądania tokenu dostępu dla internetowego interfejsu API.

```json
{
    "token_type":"Bearer",
    "scope":"User.Read",
    "expires_in":"43482",
    "ext_expires_in":"302683",
    "expires_on":"1493466951",
    "not_before":"1493423168",
    "resource":"https://graph.microsoft.com",
    "access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ",
    "refresh_token":"AQABAAAAAABnfiG-mA6NTae7CdWW7QfdjKGu9-t1scy_TDEmLi4eLQMjJGt_nAoVu6A4oSu1KsRiz8XyQIPKQxSGfbf2FoSK-hm2K8TYzbJuswYusQpJaHUQnSqEvdaCeFuqXHBv84wjFhuanzF9dQZB_Ng5za9xKlUENrNtlq9XuLNVKzxEyeUM7JyxzdY7JiEphWImwgOYf6II316d0Z6-H3oYsFezf4Xsjz-MOBYEov0P64UaB5nJMvDyApV-NWpgklLASfNoSPGb67Bc02aFRZrm4kLk-xTl6eKE6hSo0XU2z2t70stFJDxvNQobnvNHrAmBaHWPAcC3FGwFnBOojpZB2tzG1gLEbmdROVDp8kHEYAwnRK947Py12fJNKExUdN0njmXrKxNZ_fEM33LHW1Tf4kMX_GvNmbWHtBnIyG0w5emb-b54ef5AwV5_tGUeivTCCysgucEc-S7G8Cz0xNJ_BOiM_4bAv9iFmrm9STkltpz0-Tftg8WKmaJiC0xXj6uTf4ZkX79mJJIuuM7XP4ARIcLpkktyg2Iym9jcZqymRkGH2Rm9sxBwC4eeZXM7M5a7TJ-5CqOdfuE3sBPq40RdEWMFLcrAzFvP0VDR8NKHIrPR1AcUruat9DETmTNJukdlJN3O41nWdZOVoJM-uKN3uz2wQ2Ld1z0Mb9_6YfMox9KTJNzRzcL52r4V_y3kB6ekaOZ9wQ3HxGBQ4zFt-2U0mSszIAA",
    "id_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC8yNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IvIiwiaWF0IjoxNDkzNDIzMTY4LCJuYmYiOjE0OTM0MjMxNjgsImV4cCI6MTQ5MzQ2Njk1MSwiYW1yIjpbInB3ZCJdLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXRpIjoieEN3ZnpoYS1QMFdKUU9MeENHZ0tBQSIsInZlciI6IjEuMCJ9."
}
```

### <a name="error-response-example"></a>Przykład odpowiedzi na błąd

Punkt końcowy tokenu usługi Azure AD zwraca odpowiedź na błąd, gdy próbuje uzyskać token dostępu dla interfejsu API podrzędnego, który jest ustawiany za pomocą zasad dostępu warunkowego (na przykład uwierzytelniania wieloskładnikowego). Usługa warstwy środkowej powinna wywklić ten błąd w aplikacji klienckiej, aby aplikacja kliencka mogła zapewnić interakcję użytkownika w celu spełnienia zasad dostępu warunkowego.

```json
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

## <a name="use-the-access-token-to-access-the-secured-resource"></a>Użyj tokenu dostępu, aby uzyskać dostęp do zabezpieczonego zasobu

Usługa warstwy środkowej może używać tokenu nabytego dostępu do tworzenia uwierzytelnionych żądań `Authorization` do podrzędnego interfejsu API sieci web, ustawiając token w nagłówku.

### <a name="example"></a>Przykład

```
GET /me?api-version=2013-11-08 HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ
```

## <a name="saml-assertions-obtained-with-an-oauth20-obo-flow"></a>Twierdzenia SAML uzyskane przy przepływie OAuth2.0 OBO

Niektóre usługi sieci web oparte na OAuth muszą uzyskać dostęp do innych interfejsów API usług sieci web, które akceptują potwierdzenia SAML w przepływach nieinterakcyjnych. Usługa Azure Active Directory może dostarczyć potwierdzenia SAML w odpowiedzi na przepływ w imieniu, który używa usługi sieci web opartej na SAML jako zasobu docelowego.

>[!NOTE]
>Jest to niestandardowe rozszerzenie przepływu W imieniu OAuth 2.0, który umożliwia aplikacji opartej na UAuth2 dostęp do punktów końcowych interfejsu API usługi sieci web, które zużywają tokeny SAML.

> [!TIP]
> Po wywołaniu usługi sieci web chronionej przez SAML z aplikacji sieci web front-end można po prostu wywołać interfejs API i zainicjować normalny przepływ uwierzytelniania interaktywnego z istniejącą sesją użytkownika. Przepływ OBO należy używać tylko wtedy, gdy wywołanie usługi do usługi wymaga tokenu SAML, aby zapewnić kontekst użytkownika.

### <a name="obtain-a-saml-token-by-using-an-obo-request-with-a-shared-secret"></a>Uzyskiwanie tokenu SAML przy użyciu żądania OBO z udostępnionym kluczem tajnym

Żądanie usługi do usługi dla potwierdzenia SAML zawiera następujące parametry:

| Parametr |  | Opis |
| --- | --- | --- |
| grant_type |wymagany | Typ żądania tokenu. W przypadku żądania, które używa JWT, wartość musi być **urn:ietf:params:oauth:grant-type:jwt-bearer**. |
| Potwierdzenia |wymagany | Wartość tokenu dostępu używanego w żądaniu.|
| client_id |wymagany | Identyfikator aplikacji przypisany do usługi wywołującej podczas rejestracji w usłudze Azure AD. Aby znaleźć identyfikator aplikacji w witrynie Azure portal, wybierz pozycję **Active Directory**, wybierz katalog, a następnie wybierz nazwę aplikacji. |
| client_secret |wymagany | Klucz zarejestrowany dla usługi wywołującej w usłudze Azure AD. Wartość ta powinna być odnotowana w momencie rejestracji. |
| zasób |wymagany | Identyfikator identyfikatora aplikacji usługi odbierającej (zasób zabezpieczony). Jest to zasób, który będzie odbiorcą tokenu SAML. Aby znaleźć identyfikator URI identyfikatora aplikacji w witrynie Azure portal, wybierz pozycję **Active Directory** i wybierz katalog. Wybierz nazwę aplikacji, wybierz pozycję **Wszystkie ustawienia**, a następnie wybierz pozycję **Właściwości**. |
| requested_token_use |wymagany | Określa sposób przetwarzania żądania. W przepływie w imieniu musi być **on_behalf_of**. |
| requested_token_type | wymagany | Określa typ żądanego tokenu. Wartością może być **urn:ietf:params:oauth:token-type:saml2** lub **urn:ietf:params:oauth:token-type:saml1** w zależności od wymagań dostępnego zasobu. |

Odpowiedź zawiera token SAML zakodowany w UTF8 i Base64url.

- **SubjectConfirmationData dla potwierdzenia SAML po pochodzące z wywołania OBO:** Jeśli aplikacja docelowa wymaga wartości adresata w **SubjectConfirmationData**, wartość musi być adresem URL odpowiedzi bez symbolu wieloznacznego w konfiguracji aplikacji zasobów.
- **Węzeł SubjectConfirmationData:** Węzeł nie może zawierać atrybutu **InResponseTo,** ponieważ nie jest częścią odpowiedzi SAML. Aplikacja odbierająca token SAML musi być w stanie zaakceptować asercja SAML bez atrybutu **InResponseTo.**

- **Zgoda:** Zgoda musi zostać udzielona, aby otrzymać token SAML zawierający dane użytkownika w przepływie OAuth. Aby uzyskać informacje na temat uprawnień i uzyskiwania zgody [administratora, zobacz Uprawnienia i zgoda w punkcie końcowym usługi Azure Active Directory w wersji 1.0](https://docs.microsoft.com/azure/active-directory/azuread-dev/v1-permissions-consent).

### <a name="response-with-saml-assertion"></a>Odpowiedź z twierdzeniem SAML

| Parametr | Opis |
| --- | --- |
| token_type |Wskazuje wartość typu tokenu. Jedynym typem, który obsługuje usługę Azure AD jest **Bearer**. Aby uzyskać więcej informacji na temat tokenów [nośnika, zobacz OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| scope |Zakres dostępu przyznanego w tokenie. |
| expires_in |Czas, przez który token dostępu jest prawidłowy (w sekundach). |
| expires_on |Czas wygaśnięcia tokenu dostępu. Data jest reprezentowana jako liczba sekund od 1970-01-01T0:0:0Z UTC do czasu wygaśnięcia. Ta wartość jest używana do określenia okresu istnienia tokenów buforowanych. |
| zasób |Identyfikator identyfikatora aplikacji usługi odbierającej (zasób zabezpieczony). |
| access_token |Parametr, który zwraca asercja SAML. |
| refresh_token |Token odświeżania. Usługa wywołująca może użyć tego tokenu, aby zażądać innego tokenu dostępu po wygaśnięciu bieżącego potwierdzenia SAML. |

- token_type: Okaziciela
- expires_in: 3296
- ext_expires_in: 0
- expires_on: 1529627844
- Zasobów:`https://api.contoso.com`
- access_token: \<Twierdzenie SAML\>
- issued_token_type: urn:ietf:params:oauth:token-type:saml2
- refresh_token: \<Odśwież token\>

## <a name="client-limitations"></a>Ograniczenia klienta

Klienci publiczni z adresami URL odpowiedzi `id_token` wieloznacznych nie mogą używać przepływów OBO. Jednak klient poufny nadal można zrealizować tokeny **dostępu** nabyte za pośrednictwem niejawnego przepływu dotacji, nawet jeśli klient publiczny ma zarejestrowanych identyfikatorów URI przekierowania z symbolami wieloznacznych.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o protokole OAuth 2.0 i innym sposobie wykonywania uwierzytelniania usługi do usługi, który używa poświadczeń klienta:

* [Uwierzytelnianie usługi przy użyciu poświadczeń klienta usługi OAuth 2.0 w usłudze Azure AD](v1-oauth2-client-creds-grant-flow.md)
* [OAuth 2.0 w usłudze Azure AD](v1-protocols-oauth-code.md)
