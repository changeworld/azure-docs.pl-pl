---
title: Platforma tożsamości firmy Microsoft i OAuth 2.0 w imieniu z przepływem | Azure
description: W tym artykule opisano, jak używać wiadomości HTTP do zaimplementowania uwierzytelniania usług za pomocą OAuth 2.0 w imieniu użytkownika z usługi flow.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 09f6f318-e88b-4024-9ee1-e7f09fb19a82
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d0c7c29bf3094c3d5fc99b9906ee4469a6643317
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60296888"
---
# <a name="microsoft-identity-platform-and-oauth-20-on-behalf-of-flow"></a>Platforma tożsamości firmy Microsoft i przepływ OAuth 2.0 "w imieniu"

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Przepływ OAuth 2.0 "w imieniu" (OBO) służy przypadek użycia, gdzie aplikacja wywołuje usługę/internetowego interfejsu API, które z kolei są potrzebne do wywoływania innej usługi/interfejsu API sieci web. Chodzi o to propagację delegowany użytkownik tożsamości i uprawnień w łańcuchu żądanie. Usługi warstwy środkowej na wysyłanie żądań uwierzytelnionych usługi transmisji musi ona secure token dostępu z platformą Microsoft identity w imieniu użytkownika.

> [!NOTE]
>
> - Punkt końcowy platforma tożsamości firmy Microsoft nie obsługuje wszystkie scenariusze i funkcje. Aby ustalić, czy należy używać punktu końcowego platformy tożsamości firmy Microsoft, przeczytaj temat [ograniczenia dotyczące programu Microsoft identity platformy](active-directory-v2-limitations.md). Ściślej mówiąc aplikacje klienckie znanych nie są obsługiwane w przypadku aplikacji z kontem Microsoft (MSA) i odbiorców usługi Azure AD. W związku z tym Wspólny wzorzec OBO zgody nie będzie działać dla klientów, które logują się zarówno osobistych i kont służbowych. Aby dowiedzieć się więcej na temat sposobu obsługi przepływu w tym kroku, zobacz [uzyskaniem zgody dla aplikacji warstwy środkowej](#gaining-consent-for-the-middle-tier-application).
> - Od maja 2018 r. niektóre niejawny przepływ pochodne `id_token` nie można używać do przepływu OBO. Aplikacje jednej strony (źródła), powinna przekazać **dostępu** tokenu do warstwy środkowej poufne klienta do wykonania OBO przepływy zamiast tego. Aby uzyskać więcej informacji o tym, które klienci będą mogli wykonywać wywołania OBO, zobacz [ograniczenia](#client-limitations).

## <a name="protocol-diagram"></a>Diagram protokołu

Przyjęto założenie, że użytkownik został uwierzytelniony w aplikacji za pomocą [kod autoryzacji OAuth 2.0 udzielić przepływ](v2-oauth2-auth-code-flow.md). W tym momencie aplikacja ma token dostępu *dla interfejsu API* (token A) przy użyciu oświadczeń użytkowników i zgody na dostęp do warstwy środkowej sieci web interfejsu API (interfejs API A). Teraz A interfejs API musi dokonać uwierzytelnionego żądania podrzędnego internetowego interfejsu API (interfejs API B).

Poniższe kroki stanowią przepływu OBO i zostały wyjaśnione przy pomocy poniższym diagramie.

![W imieniu użytkownika — przepływ OAuth 2.0](./media/v2-oauth2-on-behalf-of-flow/protocols-oauth-on-behalf-of-flow.png)

1. Aplikacja kliencka wysyła żądanie do interfejsu API, A za pomocą tokenu A (przy użyciu `aud` oświadczenia a interfejsu API).
1. A interfejs API jest uwierzytelniany punktu końcowego wystawiania tokenu platforma tożsamości firmy Microsoft i żądania tokenu służącego do dostępu do interfejsu API B.
1. Endpoint wystawiania tokenu platforma tożsamości firmy Microsoft weryfikuje poświadczenia A interfejsu API za pomocą tokenu A i wystawia token dostępu dla aplikacji interfejsu API B (token B).
1. Token B ustawiono w nagłówku autoryzacji żądania do interfejsu API B.
1. Dane z zabezpieczono zasób jest zwracany przez interfejs API B.

> [!NOTE]
> W tym scenariuszu usługa warstwy środkowej ma bez interakcji użytkownika w celu uzyskania zgody użytkownika dostępu do podrzędnego interfejsu API. W związku z tym opcję, aby udzielić dostępu do interfejsu API niższego rzędu są prezentowane z wyprzedzeniem, jako część zgody kroku podczas uwierzytelniania. Aby dowiedzieć się, jak ustawienie dla tych aplikacji, zobacz [uzyskaniem zgody dla aplikacji warstwy środkowej](#gaining-consent-for-the-middle-tier-application).

## <a name="service-to-service-access-token-request"></a>Żądanie tokenu dostępu do usługi

Do żądania tokenu dostępu, metody HTTP POST do specyficznym dla dzierżawy Microsoft tożsamości platformy punktu końcowego tokenu z następującymi parametrami.

```
https://login.microsoftonline.com/<tenant>/oauth2/v2.0/token
```

Istnieją dwa przypadki, w zależności od tego, czy aplikacja kliencka zdecyduje się na chronione przez wspólne hasło lub certyfikat.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Pierwszy przypadek: Żądanie tokenu dostępu za pomocą wspólny klucz tajny

Korzystając z wspólny klucz tajny, żądania tokenu dostępu do usługi zawiera następujące informacje:

| Parametr |  | Opis |
| --- | --- | --- |
| `grant_type` | Wymagane | Typ żądania tokenu. Na żądanie przy użyciu token JWT, wartość musi być `urn:ietf:params:oauth:grant-type:jwt-bearer`. |
| `client_id` | Wymagane | Identyfikator aplikacji (klienta) [witryna Azure portal — rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) strony ma przypisany do aplikacji. |
| `client_secret` | Wymagane | Klucz tajny klienta, który został wygenerowany dla aplikacji w witrynie Azure portal — strona rejestracji aplikacji. |
| `assertion` | Wymagane | Wartość tokenu użytego w żądaniu. |
| `scope` | Wymagane | Spacjami listy zakresów dla żądania tokenu. Aby uzyskać więcej informacji, zobacz [zakresy](v2-permissions-and-consent.md). |
| `requested_token_use` | Wymagane | Określa, jak można przetworzyć żądania. Przepływu OBO musi być równa wartości `on_behalf_of`. |

#### <a name="example"></a>Przykład

W następującym WPISIE HTTP żądania token dostępu i token odświeżania za pomocą `user.read` zakresu dla https://graph.microsoft.com interfejs API sieci web.

```
//line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn:ietf:params:oauth:grant-type:jwt-bearer
&client_id=2846f71b-a7a4-4987-bab3-760035b2f389
&client_secret=BYyVnAt56JpLwUcyo47XODd
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE0OTM5MjA5MTYsIm5iZiI6MTQ5MzkyMDkxNiwiZXhwIjoxNDkzOTI0ODE2LCJhaW8iOiJBU1FBMi84REFBQUFnZm8vNk9CR0NaaFV2NjJ6MFFYSEZKR0VVYUIwRUlIV3NhcGducndMMnVrPSIsIm5hbWUiOiJOYXZ5YSBDYW51bWFsbGEiLCJvaWQiOiJkNWU5NzljNy0zZDJkLTQyYWYtOGYzMC03MjdkZDRjMmQzODMiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJuYWNhbnVtYUBtaWNyb3NvZnQuY29tIiwic3ViIjoiZ1Q5a1FMN2hXRUpUUGg1OWJlX1l5dVZNRDFOTEdiREJFWFRhbEQzU3FZYyIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInV0aSI6IjN5U3F4UHJweUVPd0ZsTWFFMU1PQUEiLCJ2ZXIiOiIyLjAifQ.TPPJSvpNCSCyUeIiKQoLMixN1-M-Y5U0QxtxVkpepjyoWNG0i49YFAJC6ADdCs5nJXr6f-ozIRuaiPzy29yRUOdSz_8KqG42luCyC1c951HyeDgqUJSz91Ku150D9kP5B9-2R-jgCerD_VVuxXUdkuPFEl3VEADC_1qkGBiIg0AyLLbz7DTMp5DvmbC09DhrQQiouHQGFSk2TPmksqHm3-b3RgeNM1rJmpLThis2ZWBEIPx662pjxL6NJDmV08cPVIcGX4KkFo54Z3rfwiYg4YssiUc4w-w3NJUBQhnzfTl4_Mtq2d7cVlul9uDzras091vFy32tWkrpa970UvdVfQ
&scope=https://graph.microsoft.com/user.read+offline_access
&requested_token_use=on_behalf_of
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Drugi przypadek: Żądanie tokenu dostępu przy użyciu certyfikatu

Żądanie tokenu dostępu do usługi, za pomocą certyfikatu zawiera następujące parametry:

| Parametr |  | Opis |
| --- | --- | --- |
| `grant_type` | Wymagane | Typ żądania tokenu. Na żądanie przy użyciu token JWT, wartość musi być `urn:ietf:params:oauth:grant-type:jwt-bearer`. |
| `client_id` | Wymagane |  Identyfikator aplikacji (klienta) [witryna Azure portal — rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) strony ma przypisany do aplikacji. |
| `client_assertion_type` | Wymagane | Wartość musi być `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`. |
| `client_assertion` | Wymagane | Potwierdzenie (JSON web token) należy utworzyć i podpisać za pomocą certyfikatu rejestracji w charakterze poświadczenia dla aplikacji. Aby dowiedzieć się, jak zarejestrować certyfikat i format potwierdzenie, zobacz [certyfikatu poświadczeń](active-directory-certificate-credentials.md). |
| `assertion` | Wymagane | Wartość tokenu użytego w żądaniu. |
| `requested_token_use` | Wymagane | Określa, jak można przetworzyć żądania. Przepływu OBO musi być równa wartości `on_behalf_of`. |
| `scope` | Wymagane | Rozdzielonej spacjami listy zakresów dla żądania tokenu. Aby uzyskać więcej informacji, zobacz [zakresy](v2-permissions-and-consent.md).|

Należy zauważyć, że parametry są prawie takie same jak w przypadku żądania przez Wspólny klucz tajny, chyba że `client_secret` parametr zostaje zastąpiona przez dwa parametry: `client_assertion_type` i `client_assertion`.

#### <a name="example"></a>Przykład

W następującym WPISIE HTTP żądania tokenu dostępu za pomocą `user.read` zakresu dla https://graph.microsoft.com interfejs API sieci web przy użyciu certyfikatu.

```
// line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=https://graph.microsoft.com/user.read+offline_access
```

## <a name="service-to-service-access-token-response"></a>Usługa odpowiedzi tokenu dostępu usługi

Odpowiedź sukcesu jest odpowiedź JSON OAuth 2.0, z następującymi parametrami.

| Parametr | Opis |
| --- | --- |
| `token_type` | Wskazuje typ tokenu. To jedyny typ przez firmę Microsoft, które obsługuje platforma tożsamości jest `Bearer`. Aby uzyskać więcej informacji na temat tokenów elementu nośnego, zobacz [OAuth 2.0 autoryzacji Framework: Użycie tokenu elementu nośnego (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| `scope` | Zakres dostępu przyznane w tokenie. |
| `expires_in` | Długość czasu w sekundach, token dostępu jest prawidłowy. |
| `access_token` | Token żądanego dostępu. Wywoływania usługi można użyć tego tokenu do uwierzytelnienia w usłudze odbierania. |
| `refresh_token` | Token odświeżania dla tokenu żądanego dostępu. Wywoływania usługi można użyć tego tokenu do żądania inny token dostępu po wygaśnięciu bieżącego tokenu dostępu. Token odświeżania jest podawana tylko wtedy, gdy `offline_access` zażądano zakresu. |

### <a name="success-response-example"></a>Przykład odpowiedzi sukces

Poniższy przykład przedstawia odpowiedź sukcesu na żądanie, aby uzyskać dostęp do tokenu do https://graph.microsoft.com interfejs API sieci web.

```
{
  "token_type": "Bearer",
  "scope": "https://graph.microsoft.com/user.read",
  "expires_in": 3269,
  "ext_expires_in": 0,
  "access_token": "eyJ0eXAiOiJKV1QiLCJub25jZSI6IkFRQUJBQUFBQUFCbmZpRy1tQTZOVGFlN0NkV1c3UWZkQ0NDYy0tY0hGa18wZE50MVEtc2loVzRMd2RwQVZISGpnTVdQZ0tQeVJIaGlDbUN2NkdyMEpmYmRfY1RmMUFxU21TcFJkVXVydVJqX3Nqd0JoN211eHlBQSIsImFsZyI6IlJTMjU2IiwieDV0IjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIiwia2lkIjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIn0.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaWF0IjoxNDkzOTMwMzA1LCJuYmYiOjE0OTM5MzAzMDUsImV4cCI6MTQ5MzkzMzg3NSwiYWNyIjoiMCIsImFpbyI6IkFTUUEyLzhEQUFBQU9KYnFFWlRNTnEyZFcxYXpKN1RZMDlYeDdOT29EMkJEUlRWMXJ3b2ZRc1k9IiwiYW1yIjpbInB3ZCJdLCJhcHBfZGlzcGxheW5hbWUiOiJUb2RvRG90bmV0T2JvIiwiYXBwaWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJhcHBpZGFjciI6IjEiLCJmYW1pbHlfbmFtZSI6IkNhbnVtYWxsYSIsImdpdmVuX25hbWUiOiJOYXZ5YSIsImlwYWRkciI6IjE2Ny4yMjAuMC4xOTkiLCJuYW1lIjoiTmF2eWEgQ2FudW1hbGxhIiwib2lkIjoiZDVlOTc5YzctM2QyZC00MmFmLThmMzAtNzI3ZGQ0YzJkMzgzIiwib25wcmVtX3NpZCI6IlMtMS01LTIxLTIxMjc1MjExODQtMTYwNDAxMjkyMC0xODg3OTI3NTI3LTI2MTE4NDg0IiwicGxhdGYiOiIxNCIsInB1aWQiOiIxMDAzM0ZGRkEwNkQxN0M5Iiwic2NwIjoiVXNlci5SZWFkIiwic3ViIjoibWtMMHBiLXlpMXQ1ckRGd2JTZ1JvTWxrZE52b3UzSjNWNm84UFE3alVCRSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInVuaXF1ZV9uYW1lIjoibmFjYW51bWFAbWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hY2FudW1hQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJWR1ItdmtEZlBFQ2M1dWFDaENRSkFBIiwidmVyIjoiMS4wIn0.cubh1L2VtruiiwF8ut1m9uNBmnUJeYx4x0G30F7CqSpzHj1Sv5DCgNZXyUz3pEiz77G8IfOF0_U5A_02k-xzwdYvtJUYGH3bFISzdqymiEGmdfCIRKl9KMeoo2llGv0ScCniIhr2U1yxTIkIpp092xcdaDt-2_2q_ql1Ha_HtjvTV1f9XR3t7_Id9bR5BqwVX5zPO7JMYDVhUZRx08eqZcC-F3wi0xd_5ND_mavMuxe2wrpF-EZviO3yg0QVRr59tE3AoWl8lSGpVc97vvRCnp4WVRk26jJhYXFPsdk4yWqOKZqzr3IFGyD08WizD_vPSrXcCPbZP3XWaoTUKZSNJg",
  "refresh_token": "OAQABAAAAAABnfiG-mA6NTae7CdWW7QfdAALzDWjw6qSn4GUDfxWzJDZ6lk9qRw4AnqPnvFqnzS3GiikHr5wBM1bV1YyjH3nUeIhKhqJWGwqJFRqs2sE_rqUfz7__3J92JDpi6gDdCZNNaXgreQsH89kLCVNYZeN6kGuFGZrjwxp1wS2JYc97E_3reXBxkHrA09K5aR-WsSKCEjf6WI23FhZMTLhk_ZKOe_nWvcvLj13FyvSrTMZV2cmzyCZDqEHtPVLJgSoASuQlD2NXrfmtcmgWfc3uJSrWLIDSn4FEmVDA63X6EikNp9cllH3Gp7Vzapjlnws1NQ1_Ff5QrmBHp_LKEIwfzVKnLLrQXN0EzP8f6AX6fdVTaeKzm7iw6nH0vkPRpUeLc3q_aNsPzqcTOnFfgng7t2CXUsMAGH5wclAyFCAwL_Cds7KnyDLL7kzOS5AVZ3Mqk2tsPlqopAiHijZaJumdTILDudwKYCFAMpUeUwEf9JmyFjl2eIWPmlbwU7cHKWNvuRCOYVqbsTTpJthwh4PvsL5ov5CawH_TaV8omG_tV6RkziHG9urk9yp2PH9gl7Cv9ATa3Vt3PJWUS8LszjRIAJmyw_EhgHBfYCvEZ8U9PYarvgqrtweLcnlO7BfnnXYEC18z_u5wemAzNBFUje2ttpGtRmRic4AzZ708tBHva2ePJWGX6pgQbiWF8esOrvWjfrrlfOvEn1h6YiBW291M022undMdXzum6t1Y1huwxHPHjCAA"
}
```

> [!NOTE]
> Powyższe token dostępu jest tokenem sformatowane w wersji 1.0. Jest to spowodowane token jest zapewniany dla zasobu, którego uzyskiwany jest dostęp. Program Microsoft Graph żądań tokenów w wersji 1.0, dzięki czemu platforma tożsamości usługi Microsoft generuje tokeny dostępu w wersji 1.0, gdy klient zażąda tokenów dla programu Microsoft Graph. Tylko aplikacje, należy rozważyć tokenów dostępu. Klienci nie powinni je sprawdzić.

### <a name="error-response-example"></a>Przykład odpowiedzi błędu

Odpowiedź na błąd jest zwracany przez punkt końcowy tokenu, podczas próby uzyskania tokenu dostępu dla interfejsu API niższego rzędu, jeśli podrzędny interfejs API ma zasady dostępu warunkowego (takie jak uwierzytelnianie wieloskładnikowe), ustaw na nim. Usługi warstwy środkowej należy przedstawić ten błąd do aplikacji klienckiej, tak, aby aplikacja kliencka może zapewnić interakcję użytkownika, aby spełnić wymagania zasad dostępu warunkowego.

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
GET /v1.0/me HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJub25jZSI6IkFRQUJBQUFBQUFCbmZpRy1tQTZOVGFlN0NkV1c3UWZkSzdNN0RyNXlvUUdLNmFEc19vdDF3cEQyZjNqRkxiNlVrcm9PcXA2cXBJclAxZVV0QktzMHEza29HN3RzXzJpSkYtQjY1UV8zVGgzSnktUHZsMjkxaFNBQSIsImFsZyI6IlJTMjU2IiwieDV0IjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIiwia2lkIjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIn0.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaWF0IjoxNDkzOTMwMDE2LCJuYmYiOjE0OTM5MzAwMTYsImV4cCI6MTQ5MzkzMzg3NSwiYWNyIjoiMCIsImFpbyI6IkFTUUEyLzhEQUFBQUlzQjN5ZUljNkZ1aEhkd1YxckoxS1dlbzJPckZOUUQwN2FENTVjUVRtems9IiwiYW1yIjpbInB3ZCJdLCJhcHBfZGlzcGxheW5hbWUiOiJUb2RvRG90bmV0T2JvIiwiYXBwaWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJhcHBpZGFjciI6IjEiLCJmYW1pbHlfbmFtZSI6IkNhbnVtYWxsYSIsImdpdmVuX25hbWUiOiJOYXZ5YSIsImlwYWRkciI6IjE2Ny4yMjAuMC4xOTkiLCJuYW1lIjoiTmF2eWEgQ2FudW1hbGxhIiwib2lkIjoiZDVlOTc5YzctM2QyZC00MmFmLThmMzAtNzI3ZGQ0YzJkMzgzIiwib25wcmVtX3NpZCI6IlMtMS01LTIxLTIxMjc1MjExODQtMTYwNDAxMjkyMC0xODg3OTI3NTI3LTI2MTE4NDg0IiwicGxhdGYiOiIxNCIsInB1aWQiOiIxMDAzM0ZGRkEwNkQxN0M5Iiwic2NwIjoiVXNlci5SZWFkIiwic3ViIjoibWtMMHBiLXlpMXQ1ckRGd2JTZ1JvTWxrZE52b3UzSjNWNm84UFE3alVCRSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInVuaXF1ZV9uYW1lIjoibmFjYW51bWFAbWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hY2FudW1hQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJzUVlVekYxdUVVS0NQS0dRTVFVRkFBIiwidmVyIjoiMS4wIn0.Hrn__RGi-HMAzYRyCqX3kBGb6OS7z7y49XPVPpwK_7rJ6nik9E4s6PNY4XkIamJYn7tphpmsHdfM9lQ1gqeeFvFGhweIACsNBWhJ9Nx4dvQnGRkqZ17KnF_wf_QLcyOrOWpUxdSD_oPKcPS-Qr5AFkjw0t7GOKLY-Xw3QLJhzeKmYuuOkmMDJDAl0eNDbH0HiCh3g189a176BfyaR0MgK8wrXI_6MTnFSVfBePqklQeLhcr50YTBfWg3Svgl6MuK_g1hOuaO-XpjUxpdv5dZ0SvI47fAuVDdpCE48igCX5VMj4KUVytDIf6T78aIXMkYHGgW3-xAmuSyYH_Fr0yVAQ
```

## <a name="gaining-consent-for-the-middle-tier-application"></a>Uzyskanie zgody dla aplikacji warstwy środkowej

W zależności od odbiorców dla aplikacji można rozważyć różne strategie za zapewnienie, że przepływu OBO zakończyła się powodzeniem. We wszystkich przypadkach ostatecznym celem jest upewnij się, że podano odpowiedniej zgody. Jak ma to miejsce, jednak jest zależna od użytkowników, którzy aplikacja obsługuje.

### <a name="consent-for-azure-ad-only-applications"></a>Zgoda na usługi Azure AD aplikacji

#### <a name="default-and-combined-consent"></a>/.default i połączone zgody

W przypadku aplikacji, którzy muszą tylko zarejestrować się w pracy lub szkołą kont tradycyjne podejście "Znane aplikacje klienckie" jest wystarczająca. Aplikacja warstwy środkowej dodaje klienta do listy znanych klienta aplikacji w manifeście, a następnie klient może wyzwolić przepływu wyrażania zgody połączone sam i aplikacji warstwy środkowej. W punkcie końcowym platforma tożsamości firmy Microsoft, odbywa się przy użyciu [ `/.default` zakres](v2-permissions-and-consent.md#the-default-scope). Podczas wyzwalania ekranie wyrażania zgody, przy użyciu aplikacji klienckich znane i `/.default`, ekran wyrażania zgody Pokaż uprawnienia dla klienta do interfejsu API warstwy środkowej, a także zażądać uprawnień wymaganych przez interfejs API warstwy środkowej. Użytkownik udostępnia wyrażania zgody dla obu aplikacji, a następnie przepływu OBO działa.

W tej chwili system osobistych kont Microsoft nie obsługuje połączonych zgody, a więc takie podejście nie działa w przypadku aplikacji, które mają się w szczególności w ramach kont osobistych. Osobistych kont Microsoft, używane jako konta gościa w dzierżawie są obsługiwane w systemie usługi Azure AD i można przejść przez połączone zgody.

#### <a name="pre-authorized-applications"></a>Wstępnie autoryzowane aplikacje

Funkcja portalu aplikacji jest "wstępnie autoryzowane aplikacje". W ten sposób zasobu może wskazywać, że zawsze danej aplikacji ma uprawnienia do odbierania określonych zakresów. Jest to głównie przydatne nawiązywanie połączeń między klientem frontonu i zaplecza zasób bezproblemowa. Zasób może zadeklarować wiele wstępnie autoryzowanych aplikacjach — takich aplikacji mogą żądać tych uprawnień w OBO przepływu i odbierać je bez użytkownika zgodę.

#### <a name="admin-consent"></a>zgoda administratora

Administrator dzierżawy może zagwarantować, że aplikacje mają uprawnienia do wywołania ich wymaganych interfejsów API, zapewniając zgody administratora dla aplikacji warstwy środkowej. Aby to zrobić, administrator może znaleźć aplikacji warstwy środkowej w ramach ich dzierżawy, otwórz stronę wymaganych uprawnień, a następnie wybierz udzielić uprawnień dla aplikacji. Aby dowiedzieć się więcej na temat zgody administratora, zobacz [dokumentacji uprawnienia i zgody](v2-permissions-and-consent.md).

### <a name="consent-for-azure-ad--microsoft-account-applications"></a>Zgoda na usługi Azure AD i aplikacji konta Microsoft

Z powodu ograniczeń w modelu uprawnień dla osobistych kont i braku zarządzania dzierżawy wymagania wyrażania zgody dla osobistych kont różnią się nieco z usługi Azure AD. Istnieje żaden z dzierżawców zgody dla dzierżawy dla nie jest istnieje możliwość robienia połączone zgody. W związku z tym inne strategie, które są obecne samodzielnie — należy pamiętać, że działają one dla aplikacji wymagających tylko obsługują także kont usługi Azure AD.

#### <a name="use-of-a-single-application"></a>Korzystanie z pojedynczą aplikacją

W niektórych scenariuszach może mieć tylko, parowanie pojedynczego klienta warstwy środkowej i frontonu. W tym scenariuszu użytkownik może ułatwić pojedynczej aplikacji, całkowicie Negacja potrzeby aplikacji warstwy środkowej. Na potrzeby uwierzytelniania między fronton i interfejs API sieci web, można użyć plików cookie, id_token lub token dostępu żądany dla samej aplikacji. Następnie zażądania zgody z tej aplikacji w taki sposób, w celu zasobów zaplecza.

## <a name="client-limitations"></a>Ograniczenia klienta

Jeśli klient używa niejawny przepływ w celu uzyskania elementu id_token, a klient ma również symboli wieloznacznych w adresie URL odpowiedzi, id_token nie można użyć przepływu OBO.  Jednak tokenów dostępu, które zakupione w ramach przepływu przyznawanie niejawne nadal można zrealizować za poufne klienta nawet wtedy, gdy zainicjować klienta został zarejestrowany adres URL odpowiedzi symboli wieloznacznych.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat protokołu OAuth 2.0 i innym sposobem uwierzytelniania usług przy użyciu poświadczeń klienta.

* [Przyznawanie poświadczeń klienta OAuth 2.0 na platformie tożsamości firmy Microsoft](v2-oauth2-client-creds-grant-flow.md)
* [Przepływ kodu OAuth 2.0 na platformie tożsamości firmy Microsoft](v2-oauth2-auth-code-flow.md)
* [Za pomocą `/.default` zakresu](v2-permissions-and-consent.md#the-default-scope)
