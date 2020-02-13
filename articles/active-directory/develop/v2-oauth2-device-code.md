---
title: Przepływ kodu urządzenia OAuth 2,0 | Azure
titleSuffix: Microsoft identity platform
description: Użytkownicy logują się bez przeglądarki. Tworzenie wbudowanych i niezwiązanych z przeglądarką przepływów uwierzytelniania przy użyciu uprawnień do autoryzacji urządzeń.
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
ms.date: 11/19/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: b45ba0c0b417be9cf308fedbb7fad2f6ad5fceaf
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77159735"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-device-authorization-grant-flow"></a>Microsoft Identity platform i autoryzacja autoryzacji urządzeń z uwierzytelnianiem OAuth 2,0

Platforma tożsamości firmy Microsoft obsługuje [przyznawanie autoryzacji urządzeń](https://tools.ietf.org/html/rfc8628), co pozwala użytkownikom na logowanie się na urządzeniach z ograniczeniami, takich jak inteligentna telewizja, urządzenie IoT lub drukarka.  Aby włączyć ten przepływ, na urządzeniu zostanie wyświetlona strona sieci Web w przeglądarce na innym urządzeniu w celu zalogowania się.  Po zalogowaniu się użytkownika urządzenie będzie w stanie uzyskać tokeny dostępu i odświeżać tokeny zgodnie z wymaganiami.  

W tym artykule opisano, jak programować bezpośrednio w odniesieniu do protokołu w aplikacji.  Jeśli to możliwe, zalecamy korzystanie z obsługiwanych bibliotek uwierzytelniania firmy Microsoft (MSAL) zamiast [uzyskiwać tokeny i wywoływać zabezpieczone interfejsy API sieci Web](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Zapoznaj się również z [przykładowymi aplikacjami korzystającymi z MSAL](sample-v2-code.md).

> [!NOTE]
> Punkt końcowy platformy tożsamości firmy Microsoft nie obsługuje wszystkich Azure Active Directoryych scenariuszy i funkcji. Aby określić, czy należy używać punktu końcowego platformy tożsamości firmy Microsoft, przeczytaj artykuł [ograniczenia dotyczące platformy tożsamości firmy Microsoft](active-directory-v2-limitations.md).

## <a name="protocol-diagram"></a>Diagram protokołu

Cały przepływ kodu urządzenia wygląda podobnie do następnego diagramu. Opisujemy każdy z kroków opisanych w dalszej części tego artykułu.

![Przepływ kodu urządzenia](./media/v2-oauth2-device-code/v2-oauth-device-flow.svg)

## <a name="device-authorization-request"></a>Żądanie autoryzacji urządzenia

Klient musi najpierw sprawdzić, czy na serwerze uwierzytelniania znajduje się kod urządzenia i użytkownika używany do inicjowania uwierzytelniania. Klient zbiera dane żądanie z punktu końcowego `/devicecode`. W tym żądaniu klient powinien również zawierać uprawnienia wymagane do uzyskania od użytkownika. Od momentu wysłania tego żądania użytkownik ma tylko 15 minut na zalogowanie (zazwyczaj wartość dla `expires_in`), więc to żądanie należy wykonać tylko wtedy, gdy użytkownik wskaże, że jest gotowy do zalogowania się.

> [!TIP]
> Spróbuj wykonać to żądanie w programie Poster!
> [![spróbuj uruchomić to żądanie w programie Poster](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| Parametr | Warunek | Opis |
| --- | --- | --- |
| `tenant` | Wymagany | Może być/typowe,/consumers lub/Organizations.  Może to być również dzierżawca katalogu, do którego chcesz zażądać uprawnień w formacie GUID lub przyjaznych nazw.  |
| `client_id` | Wymagany | **Identyfikator aplikacji (klienta)** , który [Azure Portal — rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) środowisko przypisane do aplikacji. |
| `scope` | Zalecane | Rozdzielana spacjami lista [zakresów](v2-permissions-and-consent.md) , do których użytkownik ma wyrazić zgodę.  |

### <a name="device-authorization-response"></a>Odpowiedź na autoryzację urządzenia

Pomyślna odpowiedź będzie obiektem JSON zawierającym wymagane informacje umożliwiające użytkownikowi zalogowanie się.  

| Parametr | Format | Opis |
| ---              | --- | --- |
|`device_code`     | Ciąg | Długi ciąg używany do weryfikowania sesji między klientem a serwerem autoryzacji. Klient używa tego parametru, aby zażądać tokenu dostępu z serwera autoryzacji. |
|`user_code`       | Ciąg | Krótki ciąg pokazywany użytkownikowi, który służy do identyfikowania sesji na urządzeniu pomocniczym.|
|`verification_uri`| Identyfikator URI | Identyfikator URI, do którego użytkownik powinien przejść przy użyciu `user_code` w celu zalogowania się. |
|`expires_in`      | int | Liczba sekund przed wygaśnięciem `device_code` i `user_code`. |
|`interval`        | int | Liczba sekund, przez jaką klient powinien czekać między żądaniami sondowania. |
| `message`        | Ciąg | Czytelny dla człowieka ciąg zawierający instrukcje dla użytkownika. Może to być lokalizowane przez dołączenie **parametru zapytania** w żądaniu formularza `?mkt=xx-XX`, wypełniając odpowiedni kod kultury języka. |

> [!NOTE]
> Pole odpowiedzi `verification_uri_complete` nie jest w tej chwili uwzględnione ani obsługiwane.  Wspominamy, że jeśli odczytasz [Standard](https://tools.ietf.org/html/rfc8628) , zobaczysz, że `verification_uri_complete` jest wyświetlana jako opcjonalna część standardowego przepływu kodu urządzenia.

## <a name="authenticating-the-user"></a>Uwierzytelnianie użytkownika

Po odebraniu `user_code` i `verification_uri`klient wyświetli te elementy użytkownikowi, co nakazuje ich zalogowanie się przy użyciu telefonu komórkowego lub przeglądarki KOMPUTERowej.

Jeśli użytkownik jest uwierzytelniany przy użyciu konta osobistego (na/typowe lub/consumers), zostanie poproszony o ponowne zalogowanie się w celu przetransferowania stanu uwierzytelniania na urządzenie.  Zostaną również poproszeni o udzielenie zgody, aby upewnić się, że są świadome udzielanych uprawnień.  Nie dotyczy to kont służbowych używanych do uwierzytelniania. 

Gdy użytkownik jest uwierzytelniany na `verification_uri`, klient powinien sondować punkt końcowy `/token` dla żądanego tokenu przy użyciu `device_code`.

``` 
POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type: urn:ietf:params:oauth:grant-type:device_code
client_id: 6731de76-14a6-49ae-97bc-6eba6914391e
device_code: GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8...
```

| Parametr | Wymagany | Opis|
| -------- | -------- | ---------- |
| `tenant`  | Wymagany | Ten sam alias dzierżawcy lub dzierżawy używany w żądaniu początkowym. | 
| `grant_type` | Wymagany | Musi być `urn:ietf:params:oauth:grant-type:device_code`|
| `client_id`  | Wymagany | Musi pasować do `client_id` użytego w żądaniu początkowym. |
| `device_code`| Wymagany | `device_code` zwrócona w żądaniu autoryzacji urządzenia.  |

### <a name="expected-errors"></a>Oczekiwane błędy

Przepływ kodu urządzenia to protokół sondowania, aby klient oczekiwał na otrzymywanie błędów, zanim użytkownik zakończy uwierzytelnianie.  

| Błąd | Opis | Akcja klienta |
| ------ | ----------- | -------------|
| `authorization_pending` | Użytkownik nie zakończył uwierzytelniania, ale nie anulował przepływu. | Powtórz żądanie po upływie co najmniej `interval` sekund. |
| `authorization_declined` | Użytkownik końcowy odrzucił żądanie autoryzacji.| Zatrzymaj sondowanie i Przywróć stan nieuwierzytelniony.  |
| `bad_verification_code`| `device_code` wysłane do punktu końcowego `/token` nie został rozpoznany. | Sprawdź, czy klient wysyła poprawne `device_code` w żądaniu. |
| `expired_token` | Zakończono co najmniej `expires_in` sekund, a uwierzytelnianie nie jest już możliwe z tym `device_code`. | Zatrzymaj sondowanie i Przywróć stan nieuwierzytelniony. |   

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
| `token_type` | Ciąg| Zawsze "Bearer". |
| `scope` | Ciągi rozdzielone spacją | Jeśli został zwrócony token dostępu, spowoduje to wyświetlenie listy zakresów, dla których token dostępu jest prawidłowy. |
| `expires_in`| int | Liczba sekund, po upływie których uwzględniony token dostępu jest prawidłowy dla elementu. |
| `access_token`| Ciąg nieprzezroczysty | Wystawiony dla żądanych [zakresów](v2-permissions-and-consent.md) .  |
| `id_token`   | JWT | Wystawiony, jeśli oryginalny parametr `scope` uwzględniony w zakresie `openid`.  |
| `refresh_token` | Ciąg nieprzezroczysty | Wystawiony, jeśli oryginalny parametr `scope` uwzględniony `offline_access`.  |

Możesz użyć tokenu odświeżania, aby uzyskać nowe tokeny dostępu i odświeżać tokeny przy użyciu tego samego przepływu udokumentowanego w [dokumentacji przepływu kodu OAuth](v2-oauth2-auth-code-flow.md#refresh-the-access-token).  
