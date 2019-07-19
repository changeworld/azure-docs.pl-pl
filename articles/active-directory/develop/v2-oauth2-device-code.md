---
title: Korzystanie z platformy tożsamości firmy Microsoft do logowania użytkowników na urządzeniach bez przeglądarki | Azure
description: Utwórz osadzone i niezwiązane z przeglądarką przepływy uwierzytelniania za pomocą przydzielenia kodu urządzenia.
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
ms.date: 06/12/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 274c4e89ff3f996cc71cdacdfb7b5b72e813ae4b
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297661"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-device-code-flow"></a>Microsoft Identity platform i przepływ kodu urządzenia OAuth 2,0

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Platforma tożsamości firmy Microsoft obsługuje [przyznawanie kodu urządzenia](https://tools.ietf.org/html/draft-ietf-oauth-device-flow-12), dzięki czemu użytkownicy mogą logować się do urządzeń z ograniczeniami, takich jak inteligentna telewizja, urządzenie IoT lub drukarka.  Aby włączyć ten przepływ, na urządzeniu zostanie wyświetlona strona sieci Web w przeglądarce na innym urządzeniu w celu zalogowania się.  Po zalogowaniu się użytkownika urządzenie będzie w stanie uzyskać tokeny dostępu i odświeżać tokeny zgodnie z wymaganiami.  

> [!IMPORTANT]
> W tej chwili punkt końcowy platformy tożsamości firmy Microsoft obsługuje tylko przepływ urządzeń dla dzierżawców usługi Azure AD, ale nie do kont osobistych.  Oznacza to, że należy użyć punktu końcowego skonfigurowanego jako dzierżawca lub `organizations` punktu końcowego.  Ta pomoc techniczna zostanie włączona wkrótce. 
>
> Konta osobiste, które są zapraszane do dzierżawy usługi Azure AD, będą mogły korzystać z dotacji do przepływu urządzeń, ale tylko w kontekście dzierżawy.
>
> W tej chwili pole odpowiedziniejestuwzględnianeaniobsługiwane.`verification_uri_complete`  

> [!NOTE]
> Punkt końcowy platformy tożsamości firmy Microsoft nie obsługuje wszystkich Azure Active Directoryych scenariuszy i funkcji. Aby określić, czy należy używać punktu końcowego platformy tożsamości firmy Microsoft, przeczytaj artykuł [ograniczenia dotyczące platformy tożsamości firmy Microsoft](active-directory-v2-limitations.md).

## <a name="protocol-diagram"></a>Diagram protokołu

Cały przepływ kodu urządzenia wygląda podobnie do następnego diagramu. Opisujemy każdy z kroków opisanych w dalszej części tego artykułu.

![Przepływ kodu urządzenia](./media/v2-oauth2-device-code/v2-oauth-device-flow.svg)

## <a name="device-authorization-request"></a>Żądanie autoryzacji urządzenia

Klient musi najpierw sprawdzić, czy na serwerze uwierzytelniania znajduje się kod urządzenia i użytkownika używany do inicjowania uwierzytelniania. Klient zbiera żądanie z `/devicecode` punktu końcowego. W tym żądaniu klient powinien również zawierać uprawnienia wymagane do uzyskania od użytkownika. Od momentu wysłania tego żądania użytkownik ma tylko 15 minut na zalogowanie (zazwyczaj wartość dla `expires_in`), więc to żądanie należy wykonać tylko wtedy, gdy użytkownik wykazał, że jest gotowy do zalogowania się.

> [!TIP]
> Spróbuj wykonać to żądanie w programie Poster!
> [![Spróbuj uruchomić to żądanie w programie Poster](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| Parametr | Warunek | Opis |
| --- | --- | --- |
| `tenant` | Wymagane |Dzierżawa katalogu, z której chcesz zażądać uprawnień. Może to być w formacie identyfikatora GUID lub przyjaznej nazwy.  |
| `client_id` | Wymagane | **Identyfikator aplikacji (klienta)** , który [Azure Portal — rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) środowisko przypisane do aplikacji. |
| `scope` | Zalecane | Rozdzielana spacjami lista [zakresów](v2-permissions-and-consent.md) , do których użytkownik ma wyrazić zgodę.  |

### <a name="device-authorization-response"></a>Odpowiedź na autoryzację urządzenia

Pomyślna odpowiedź będzie obiektem JSON zawierającym wymagane informacje umożliwiające użytkownikowi zalogowanie się.  

| Parametr | Format | Opis |
| ---              | --- | --- |
|`device_code`     | String | Długi ciąg używany do weryfikowania sesji między klientem a serwerem autoryzacji. Klient używa tego parametru, aby zażądać tokenu dostępu z serwera autoryzacji. |
|`user_code`       | Ciąg | Krótki ciąg pokazywany użytkownikowi, który służy do identyfikowania sesji na urządzeniu pomocniczym.|
|`verification_uri`| Identyfikator URI | Identyfikator URI, do którego użytkownik powinien przejść, `user_code` aby móc się zalogować. |
|`expires_in`      | int | Liczba sekund przed `device_code` wygaśnięciem i `user_code` . |
|`interval`        | int | Liczba sekund, przez jaką klient powinien czekać między żądaniami sondowania. |
| `message`        | String | Czytelny dla człowieka ciąg zawierający instrukcje dla użytkownika. Może to być lokalizowane przez dołączenie **parametru zapytania** w żądaniu formularza `?mkt=xx-XX`, wypełniając odpowiedni kod kultury języka. |

## <a name="authenticating-the-user"></a>Uwierzytelnianie użytkownika

Po odebraniu `user_code` usługi `verification_uri`i klient wyświetli te elementy użytkownikowi, co powoduje ich zalogowanie się przy użyciu telefonu komórkowego lub przeglądarki komputerowej.  Ponadto Klient może użyć kodu QR lub podobnego mechanizmu `verfication_uri_complete`, aby wyświetlić, co spowoduje `user_code` przejście do użytkownika.

Podczas uwierzytelniania użytkownika w `verification_uri`usłudze należy wykonać sondowanie `/token` punktu końcowego dla żądanego tokenu przy użyciu `device_code`.

``` 
POST https://login.microsoftonline.com/tenant/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type: urn:ietf:params:oauth:grant-type:device_code
client_id: 6731de76-14a6-49ae-97bc-6eba6914391e
device_code: GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8
```

| Parametr | Wymagane | Opis|
| -------- | -------- | ---------- |
| `grant_type` | Wymagane | Musi być`urn:ietf:params:oauth:grant-type:device_code`|
| `client_id`  | Wymagane | Musi być zgodna `client_id` z użytym w początkowym żądaniu. |
| `device_code`| Wymagane | `device_code` Zwrócone w żądaniu autoryzacji urządzenia.  |

### <a name="expected-errors"></a>Oczekiwane błędy

Przepływ kodu urządzenia to protokół sondowania, aby klient oczekiwał na otrzymywanie błędów, zanim użytkownik zakończy uwierzytelnianie.  

| Błąd | Opis | Akcja klienta |
| ------ | ----------- | -------------|
| `authorization_pending` | Użytkownik nie zakończył uwierzytelniania, ale nie anulował przepływu. | Powtórz żądanie po co najmniej `interval` sekundach. |
| `authorization_declined` | Użytkownik końcowy odrzucił żądanie autoryzacji.| Zatrzymaj sondowanie i Przywróć stan nieuwierzytelniony.  |
| `bad_verification_code`| Nie rozpoznano `/token` wysłanego do punktu końcowego. `device_code` | Sprawdź, czy klient wysyła poprawny `device_code` w żądaniu. |
| `expired_token` | Co najmniej `expires_in` kilka sekund zostało zakończone, a uwierzytelnianie nie jest już możliwe `device_code`. | Zatrzymaj sondowanie i Przywróć stan nieuwierzytelniony. |

### <a name="successful-authentication-response"></a>Pomyślna odpowiedź uwierzytelniania

Pomyślna odpowiedź dotycząca tokenu będzie wyglądać następująco:

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
| `token_type` | String| Zawsze "Bearer". |
| `scope` | Ciągi rozdzielone spacją | Jeśli został zwrócony token dostępu, spowoduje to wyświetlenie listy zakresów, dla których token dostępu jest prawidłowy. |
| `expires_in`| int | Liczba sekund, po upływie których uwzględniony token dostępu jest prawidłowy dla elementu. |
| `access_token`| Ciąg nieprzezroczysty | Wystawiony dla żądanych [zakresów](v2-permissions-and-consent.md) .  |
| `id_token`   | JWT | Wystawiony, `scope` Jeśli oryginalny parametr `openid` zawiera zakres.  |
| `refresh_token` | Ciąg nieprzezroczysty | Wystawiony, `scope` Jeśli zostanie `offline_access`uwzględniony oryginalny parametr.  |

Możesz użyć tokenu odświeżania, aby uzyskać nowe tokeny dostępu i odświeżać tokeny przy użyciu tego samego przepływu udokumentowanego w [dokumentacji przepływu kodu OAuth](v2-oauth2-auth-code-flow.md#refresh-the-access-token).  
