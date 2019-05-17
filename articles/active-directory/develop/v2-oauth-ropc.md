---
title: Użyj platforma tożsamości usługi Microsoft do logowania się użytkowników przy użyciu przyznania poświadczeń (ROPC) hasła właściciela zasobu | Azure
description: Obsługa przeglądarki bez uwierzytelniania są przekazywane przy użyciu przyznania poświadczeń hasła właściciela zasobu.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/20/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 04d2be76072866da2b21718f60fd0c9a5923b15b
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2019
ms.locfileid: "65545110"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-resource-owner-password-credential"></a>Platforma tożsamości firmy Microsoft i poświadczeń hasła właściciela zasobów OAuth 2.0

Obsługuje platformy tożsamości firmy Microsoft [przyznania poświadczeń hasła właściciela zasobu (ROPC)](https://tools.ietf.org/html/rfc6749#section-4.3), co umożliwia aplikacji do logowania użytkownika dzięki obsłudze bezpośrednio swoje hasło. Przepływ ROPC wymaga wysokiego stopnia narażenia zaufania i użytkownika, a ten przepływ należy używać tylko, gdy nie można użyć innych, bardziej bezpiecznymi przepływów.

> [!IMPORTANT]
>
> * Punkt końcowy platforma tożsamości firmy Microsoft obsługuje tylko ROPC dla dzierżaw usługi Azure AD, nie osobistych kont. Oznacza to, że trzeba korzystać z punktem końcowym specyficznym dla dzierżawy (`https://login.microsoftonline.com/{TenantId_or_Name}`) lub `organizations` punktu końcowego.
> * Konta osobiste, które są zaproszeni do dzierżawy usługi Azure AD nie można użyć ROPC.
> * Konta, które nie mają hasła nie może zalogować się za pomocą ROPC. W tym scenariuszu zaleca się używać inny przepływ dla aplikacji zamiast tego.
> * Jeśli użytkownicy muszą użyć uwierzytelniania wieloskładnikowego (MFA) do logowania do aplikacji, będą blokowane zamiast tego.

## <a name="protocol-diagram"></a>Diagram protokołu

Na poniższym diagramie przedstawiono przepływ ROPC.

![ROPC przepływu](./media/v2-oauth2-ropc/v2-oauth-ropc.svg)

## <a name="authorization-request"></a>Żądanie autoryzacji

Przepływ ROPC jest pojedyncze żądanie&mdash;wysyła do klienta w identyfikacji i poświadczenia użytkownika do dostawcy tożsamości, a następnie otrzymuje w odpowiedzi tokeny. Klient musi żądać adres e-mail użytkownika (UPN) i hasło przed jej wprowadzeniem. Natychmiast po żądania zakończonego powodzeniem klient powinien gwarantuje bezpieczne wydawanie poświadczenia użytkownika z pamięci. Nigdy nie należy zapisać je.

> [!TIP]
> Spróbuj wykonać tego żądania w narzędziu Postman!
> [![Uruchamianie w narzędziu Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)


```
// Line breaks and spaces are for legibility only.

POST {tenant}/oauth2/v2.0/token
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=user.read%20openid%20profile%20offline_access
&username=MyUsername@myTenant.com
&password=SuperS3cret
&grant_type=password
```

| Parametr | Warunek | Opis |
| --- | --- | --- |
| `tenant` | Wymagane | Chcesz się zalogować użytkownika do dzierżawy katalogu. Może to być w formacie przyjaznej nazwy lub identyfikatora GUID. Nie można ustawić ten parametr `common` lub `consumers`, ale może być ustawiona na `organizations`. |
| `grant_type` | Wymagane | Musi być równa `password`. |
| `username` | Wymagane | Adres e-mail użytkownika. |
| `password` | Wymagane | Hasło użytkownika. |
| `scope` | Zalecane | Listę rozdzielonych spacjami [zakresy](v2-permissions-and-consent.md), lub uprawnienia, których wymaga aplikacja. W przepływie interaktywne administrator lub użytkownik musi wyrazić zgodę te zakresy wcześniej. |

### <a name="successful-authentication-response"></a>Pomyślne uwierzytelnienie odpowiedzi

Poniższy przykład przedstawia odpowiedź oznaczająca Powodzenie tokenu:

```json
{
    "token_type": "Bearer",
    "scope": "User.Read profile openid email",
    "expires_in": 3599,
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD..."
}
```

| Parametr | Format | Opis |
| --------- | ------ | ----------- |
| `token_type` | String | Zawsze ustawiony na wartość `Bearer`. |
| `scope` | Ciągi oddzielone spacjami | Jeśli został zwrócony token dostępu, a ten parametr zawiera listę zakresów, który token dostępu jest prawidłowy dla. |
| `expires_in`| int | Liczba sekund, które token dostępu dołączony jest nieprawidłowa dla. |
| `access_token`| Nieprzezroczysty ciąg | Wystawiony dla [zakresy](v2-permissions-and-consent.md) którego zażądano. |
| `id_token` | JWT | Jeśli wystawionych oryginalny `scope` parametru `openid` zakresu. |
| `refresh_token` | Nieprzezroczysty ciąg | Jeśli wystawionych oryginalny `scope` parametru `offline_access`. |

Można użyć tokenu odświeżania do uzyskania nowych tokenów dostępu i Odśwież tokeny przy użyciu tego samego przepływu, opisane w [dokumentacji przepływu OAuth kodów](v2-oauth2-auth-code-flow.md#refresh-the-access-token).

### <a name="error-response"></a>Odpowiedzi na błąd

Jeśli użytkownik udzielona poprawna nazwa użytkownika lub hasło lub nie odebrał żądanego zgody, uwierzytelnianie nie powiedzie się.

| Błąd | Opis | Akcja klienta |
|------ | ----------- | -------------|
| `invalid_grant` | Uwierzytelnianie nie powiodło się | Poświadczenia są niepoprawne lub klient nie ma wyrażania zgody dla żądanych zakresów. Jeśli zakresy nie są przyznawane, `consent_required` zostanie zwrócony błąd. W takim przypadku klient powinien wysłać użytkownika do monitu interakcyjnego przy użyciu widoku sieci Web lub w przeglądarce. |
| `invalid_request` | Żądanie zostało nieprawidłowo skonstruowany. | Typ udzielania nie jest obsługiwana na `/common` lub `/consumers` kontekst uwierzytelniania.  Zamiast nich należy używać słów kluczowych `/organizations`. |
| `invalid_client` | Aplikacja jest nieprawidłowo skonfigurowana | Może się to zdarzyć, jeśli `allowPublicClient` właściwość nie jest ustawiona na wartość true w [manifest aplikacji](reference-app-manifest.md). `allowPublicClient` Właściwość jest niezbędne, ponieważ przydział ROPC nie ma identyfikatora URI przekierowania. Usługa Azure AD nie może określić, jeśli aplikacja jest aplikacji publicznych klienta lub poufne klienta, chyba że właściwość jest ustawiona. ROPC jest obsługiwana tylko w przypadku aplikacji publicznych klienta. |

## <a name="learn-more"></a>Dowiedz się więcej

* ROPC osobiście wypróbować przy użyciu [Przykładowa aplikacja konsoli](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2).
* Aby ustalić, czy należy używać punktu końcowego v2.0, przeczytaj temat [ograniczenia dotyczące programu Microsoft identity platformy](active-directory-v2-limitations.md).
