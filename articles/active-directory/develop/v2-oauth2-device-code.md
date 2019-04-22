---
title: Użyj platforma tożsamości usługi Microsoft do logowania użytkowników na urządzeniach bez przeglądarki | Azure
description: Tworzenie osadzonego i udzielić przepływów uwierzytelniania bez przeglądarki, przy użyciu kodu urządzenia.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 780eec4d-7ee1-48b7-b29f-cd0b8cb41ed3
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 14291a6e8f9c4cde3c8777969047ebaa77e42b59
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59500451"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-device-code-flow"></a>Platforma tożsamości firmy Microsoft i przepływ kodu urządzenia OAuth 2.0

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Obsługuje platformy tożsamości firmy Microsoft [przyznawania kodu urządzenia](https://tools.ietf.org/html/draft-ietf-oauth-device-flow-12), co pozwala użytkownikom na logowanie się do ograniczonych danych wejściowych urządzeń, takich jak telewizor smart TV, urządzenia IoT lub drukarki.  Aby włączyć ten przepływ, do urządzenia przypisano użytkownika odwiedź strony sieci Web w przeglądarce na innym urządzeniu, aby zalogować się.  Gdy użytkownik się zaloguje, urządzenie jest w stanie uzyskiwanie tokenów dostępu i tokenów odświeżania, zgodnie z potrzebami.  

> [!IMPORTANT]
> W tej chwili punktu końcowego platformy tożsamości firmy Microsoft obsługuje przepływ urządzenia tylko dla dzierżaw usługi Azure AD, ale nie osobistych kont.  Oznacza to, że punkt końcowy skonfigurowany jako dzierżawca, należy użyć lub `organizations` punktu końcowego.  
>
> Konta osobiste, które są zaproszeni do dzierżawy usługi Azure AD będzie używać grant flow urządzenia, ale tylko w kontekście dzierżawy.

> [!NOTE]
> Punkt końcowy platforma tożsamości firmy Microsoft nie obsługuje wszystkich scenariuszy usługi Azure Active Directory i funkcji. Aby ustalić, czy należy używać punktu końcowego platformy tożsamości firmy Microsoft, przeczytaj temat [ograniczenia dotyczące programu Microsoft identity platformy](active-directory-v2-limitations.md).

## <a name="protocol-diagram"></a>Diagram protokołu

Przepływ kodu całego urządzenia wygląda podobnie do następny diagram. Opisano poszczególne kroki w dalszej części tego artykułu.

![Przepływ kodu urządzenia](media/v2-oauth2-device-flow/v2-oauth-device-flow.png)

## <a name="device-authorization-request"></a>Żądanie autoryzacji urządzenia

Klient musi najpierw skontaktuj się z serwera uwierzytelniania dla użytkowników i urządzeń kodu, używane do inicjowania uwierzytelniania.  Klient zbiera tego żądania z `/devicecode` punktu końcowego. W tym żądaniu klienta powinny również obejmować uprawnienia niezbędne do uzyskania przez użytkownika.  Od momentu to żądanie jest wysyłane, użytkownik ma tylko 15 minut do logowania (zwykle wartość `expires_in`), dlatego tylko przesłać to żądanie, gdy użytkownik wskazuje, będą one gotowe do logowania.

> [!TIP]
> Spróbuj wykonać tego żądania w narzędziu Postman!
> [![Uruchamianie w narzędziu Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| Parametr | Warunek | Opis |
| --- | --- | --- |
| `tenant` | Wymagane |Dzierżawy katalogu, który chcesz zażądać uprawnień z. Może to być w formacie przyjaznej nazwy lub identyfikatora GUID.  |
| `client_id` | Wymagane | **Identyfikator aplikacji (klienta)** , [rejestracje aplikacji z witryny Azure portal](https://go.microsoft.com/fwlink/?linkid=2083908) środowisko przypisany do aplikacji. |
| `scope` | Zalecane | Listę rozdzielonych spacjami [zakresy](v2-permissions-and-consent.md) ma użytkownika o zgodę na.  |

### <a name="device-authorization-response"></a>Odpowiedzi autoryzacji urządzenia

Odpowiedź oznaczająca Powodzenie będzie obiektem JSON zawierającym wymagane informacje, aby umożliwić użytkownikowi zalogowanie.  

| Parametr | Format | Opis |
| ---              | --- | --- |
|`device_code`     | String | Długi ciąg używany do sprawdzenia sesji między klientem a serwerem autoryzacji.  To jest używany przez klienta do wysłania żądania tokenu dostępu z serwera autoryzacji. |
|`user_code`       | String | Krótkiego ciągu dla użytkownika, używany do identyfikowania sesji na urządzeniach pomocniczych.|
|`verification_uri`| URI | Identyfikator URI, użytkownik powinien przejdź do widoku `user_code` Aby móc się zalogować. |
|`verification_uri_complete`|URI| Łączenie URI `user_code` i `verification_uri`, który jest używany do transmisji tekstowej dla użytkownika (na przykład za pośrednictwem połączenia Bluetooth na urządzeniu lub za pomocą kodu QR).  |
|`expires_in`      |  int| Liczba sekund przed `device_code` i `user_code` wygaśnie. |
|`interval`        | int | Liczba sekund, które klient powinien upłynąć między żądaniami sondowania. |
| `message`        | String | Ciąg czytelny dla człowieka z instrukcjami dla użytkownika.  To może być lokalizowana umieszczając **parametr zapytania** w żądaniu formularza `?mkt=xx-XX`, napełniania w kodzie kultury odpowiedni język. |

## <a name="authenticating-the-user"></a>Uwierzytelnianie użytkownika

Od momentu odebrania `user_code` i `verification_uri`, klient jest wyświetlany one użytkownikowi, poinstruowanie go o zalogowanie się za pomocą swojego telefonu komórkowego lub przeglądarki komputera.  Ponadto klient może używać kod QR lub podobny mechanizm do wyświetlenia `verfication_uri_complete`, co spowoduje przejście kroku przedstawiający wprowadzanie `user_code` dla użytkownika.

Gdy użytkownik jest uwierzytelniany na `verification_uri`, klient powinien sondowanie `/token` punktu końcowego dla żądanego tokenu za pomocą `device_code`.

``` 
POST https://login.microsoftonline.com/tenant/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type: urn:ietf:params:oauth:grant-type:device_code
client_id: 6731de76-14a6-49ae-97bc-6eba6914391e
device_code: GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8
```

| Parametr | Wymagane | Opis|
| -------- | -------- | ---------- |
| `grant_type` | Wymagane | musi być `urn:ietf:params:oauth:grant-type:device_code`|
| `client_id`  | Wymagane | Musi odpowiadać `client_id` użyty w żądaniu początkowej. |
| `device_code`| Wymagane | `device_code` Zwracane w żądaniu autoryzacji urządzeń.  |

### <a name="expected-errors"></a>Oczekiwano błędy

Przepływ kodu urządzenia jest protokołem sondowania, Twój klient musi oczekiwać wystąpią błędy, zanim użytkownik zakończył uwierzytelnianie.  

| Błąd | Opis | Akcja klienta |
| ------ | ----------- | -------------|
| `authorization_pending` | Użytkownik nie została jeszcze ukończona uwierzytelniania, ale nie anulował przepływ. | Powtórz żądania po co najmniej `interval` sekund. |
| `authorization_declined` | Użytkownik odrzucił żądanie autoryzacji.| Zatrzymywanie sondowania i przywrócić stan nieuwierzytelnionych.  |
| `bad_verification_code`|`device_code` Wysyłane do `/token` punkt końcowy nie został rozpoznany. | Sprawdź, czy klient wysyła prawidłowe `device_code` w żądaniu. |
| `expired_token` | Co najmniej `expires_in` sekund i uwierzytelniania nie jest możliwe dzięki temu `device_code`. | Zatrzymywanie sondowania i przywrócić stan nieuwierzytelnionych. |


### <a name="successful-authentication-response"></a>Pomyślne uwierzytelnienie odpowiedzi

Odpowiedź oznaczająca Powodzenie tokenu będzie wyglądać następująco:

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
| `token_type` | String| Zawsze "Bearer. |
| `scope` | Ciągi oddzielone spacjami | Jeśli token dostępu został zwrócony, ta lista zawiera token dostępu jest prawidłowy dla zakresy. |
| `expires_in`| int | Liczba sekund przed token dostępu dołączony jest prawidłowy dla. |
| `access_token`| Nieprzezroczysty ciąg | Wystawiony dla [zakresy](v2-permissions-and-consent.md) którego zażądano.  |
| `id_token`   | JWT | Jeśli wystawionych oryginalny `scope` parametru `openid` zakresu.  |
| `refresh_token` | Nieprzezroczysty ciąg | Jeśli wystawionych oryginalny `scope` parametru `offline_access`.  |

Token odświeżania może służyć do uzyskania nowych tokenów dostępu i Odśwież tokeny przy użyciu tego samego przepływu szczegółowo opisane w [dokumentacji przepływu OAuth kodów](v2-oauth2-auth-code-flow.md#refresh-the-access-token).  
