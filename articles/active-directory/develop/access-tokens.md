---
title: Odwołanie do tokenów dostępu do platformy tożsamości firmy Microsoft | Azure
description: Dowiedz się więcej o tokenach dostępu emitowanych przez punkty końcowe platformy Azure AD w wersji 1.0 i platformy tożsamości firmy Microsoft (w wersji 2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 3/27/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40, fasttrack-edit
ms.openlocfilehash: ed583abc8f60f3d367bf75254807e3f28cd0f1c9
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309708"
---
# <a name="microsoft-identity-platform-access-tokens"></a>Tokeny dostępu do platformy tożsamości firmy Microsoft

Tokeny dostępu umożliwiają klientom bezpieczne wywoływanie interfejsów API chronionych przez platformę Azure. Tokeny dostępu do platformy tożsamości firmy Microsoft są [JWTs](https://tools.ietf.org/html/rfc7519), Base64 zakodowane obiekty JSON podpisane przez platformę Azure. Klienci powinni traktować tokeny dostępu jako nieprzezroczyste ciągi, ponieważ zawartość tokenu jest przeznaczona tylko dla zasobu. Do celów sprawdzania poprawności i debugowania deweloperzy mogą dekodować JWTs przy użyciu witryny, takiej jak [jwt.ms](https://jwt.ms). Klient może uzyskać token dostępu z punktu końcowego w wersji 1.0 lub punktu końcowego w wersji 2.0 przy użyciu różnych protokołów.

Gdy klient żąda tokenu dostępu, usługa Azure AD zwraca również niektóre metadane dotyczące tokenu dostępu dla użycia aplikacji. Informacje te obejmują czas wygaśnięcia tokenu dostępu i zakresy, dla których jest prawidłowy. Te dane umożliwia aplikacji do inteligentnego buforowania tokenów dostępu bez konieczności analizowania tokenu dostępu.

Jeśli aplikacja jest zasobem (internetowy interfejs API), do którego klienci mogą żądać dostępu, tokeny dostępu zawierają przydatne informacje do użycia w uwierzytelnianiu i autoryzacji, takie jak użytkownik, klient, wystawca, uprawnienia i inne.

Zobacz poniższe sekcje, aby dowiedzieć się, jak zasób może sprawdzać poprawność i używać oświadczeń wewnątrz tokenu dostępu.

> [!IMPORTANT]
> Tokeny dostępu są tworzone na podstawie *odbiorców* tokenu, co oznacza, że aplikacja, która jest właścicielem zakresów w tokenie.  Jest to jak `accessTokenAcceptedVersion` ustawienie zasobu `2` w [manifeście aplikacji,](reference-app-manifest.md#manifest-reference) aby klient wywołujący punkt końcowy w wersji 1.0 odbierał token dostępu w wersji 2.0.  Podobnie dlatego zmiana tokenu dostępu [opcjonalne oświadczenia](active-directory-optional-claims.md) dla klienta nie należy do zmiany tokenu dostępu odebrane, gdy token jest żądana dla `user.read`, który jest własnością zasobu.
> Z tego samego powodu podczas testowania aplikacji klienckiej przy za pomocą konta osobistego (takiego jak hotmail.com lub outlook.com), może się okazać, że token dostępu odebrany przez klienta jest nieprzezroczystym ciągiem. Dzieje się tak, ponieważ zasób, do który uzyskuje się dostęp, zażądał starszych biletów msa (konta Microsoft), które są szyfrowane i nie mogą być zrozumiałe dla klienta.

## <a name="sample-tokens"></a>Przykładowe tokeny

Tokeny v1.0 i v2.0 wyglądają podobnie i zawierają wiele tych samych oświadczeń. Przykład każdego z nich znajduje się tutaj.

### <a name="v10"></a>Wersja 1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0.D3H6pMUtQnoJAGq6AHd
```

Wyświetl ten token w wersji 1.0 w [JWT.ms](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0.D3H6pMUtQnoJAGq6AHd).

### <a name="v20"></a>Wersja 2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt
```

Wyświetl ten token w wersji 2.0 w [JWT.ms](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt).

## <a name="claims-in-access-tokens"></a>Oświadczenia w tokenach dostępu

JWTs są podzielone na trzy części:

* **Nagłówek** — zawiera informacje dotyczące sprawdzania [poprawności tokenu,](#validating-tokens) w tym informacje o typie tokenu i sposobie jego podpisania.
* **Ładunek** — zawiera wszystkie ważne dane dotyczące użytkownika lub aplikacji, która próbuje wywołać usługę.
* **Podpis** — jest surowcem używanym do sprawdzania poprawności tokenu.

Każdy kawałek jest oddzielony`.`kropką ( ) i oddzielnie zakodowany Base64.

Oświadczenia są obecne tylko wtedy, gdy istnieje wartość, aby ją wypełnić. Dlatego aplikacja nie powinna uwzględniać obecności oświadczenia. Przykłady `pwd_exp` obejmują (nie każda dzierżawa wymaga `family_name` haseł do wygaśnięcia) lub (poświadczenia klienta[(wersja 1.0](../azuread-dev/v1-oauth2-client-creds-grant-flow.md), [wersja 2.0](v2-oauth2-client-creds-grant-flow.md)) przepływy są w imieniu aplikacji, które nie mają nazw). Oświadczenia używane do sprawdzania poprawności tokenu dostępu będą zawsze obecne.

> [!NOTE]
> Niektóre oświadczenia są używane do pomocy azure ad bezpiecznych tokenów w przypadku ponownego użycia. Są one oznaczone jako nie do spożycia publicznego w opisie jako "Nieprzezroczyste". Roszczenia te mogą lub nie mogą pojawić się w tokenie, a nowe mogą być dodawane bez powiadomienia.

### <a name="header-claims"></a>Oświadczenia nagłówka

|Claim | Format | Opis |
|--------|--------|-------------|
| `typ` | Ciąg - zawsze "JWT" | Wskazuje, że token jest JWT.|
| `nonce` | Ciąg | Unikatowy identyfikator używany do ochrony przed atakami powtarzania tokenów. Zasób może rejestrować tę wartość, aby chronić przed powtórkami. |
| `alg` | Ciąg | Wskazuje algorytm, który został użyty do podpisania tokenu, na przykład "RS256" |
| `kid` | Ciąg | Określa odcisk palca dla klucza publicznego, który jest używany do podpisywania tego tokenu. Emitowane w tokenach dostępu w wersji 1.0 i v2.0. |
| `x5t` | Ciąg | Funkcje takie same (w `kid`użyciu i wartości) jak . `x5t`jest starszą oświadczeniem emitowanym tylko w tokenach dostępu w wersji 1.0 dla celów zgodności. |

### <a name="payload-claims"></a>Oświadczenia o ładunku

| Claim | Format | Opis |
|-----|--------|-------------|
| `aud` | Ciąg, identyfikator URI identyfikatora aplikacji | Identyfikuje zamierzonego odbiorcę tokenu. W tokenach identyfikatorów odbiorcy jest identyfikator aplikacji, przypisany do aplikacji w witrynie Azure portal. Aplikacja powinna zweryfikować tę wartość i odrzucić token, jeśli wartość nie jest zgodna. |
| `iss` | Ciąg, identyfikator URI sts | Identyfikuje usługę tokenu zabezpieczającego (STS), która konstruuje i zwraca token, oraz dzierżawę usługi Azure AD, w której użytkownik został uwierzytelniony. Jeśli wystawiony token jest tokenem w wersji `ver` 2.0 (zobacz `/v2.0`oświadczenie), identyfikator URI zakończy się na . Identyfikator GUID wskazujący, że użytkownik jest użytkownikiem `9188040d-6c67-4c5b-b112-36a304b66dad`konsumenckim z konta Microsoft, to . Aplikacja powinna używać części guid oświadczenia, aby ograniczyć zestaw dzierżaw, którzy mogą zalogować się do aplikacji, jeśli ma to zastosowanie. |
|`idp`| Ciąg, zwykle identyfikator URI STS | Rejestruje dostawcę tożsamości, który uwierzytelnił podmiot tokenu. Wartość ta jest identyczna z wartością roszczenia Emitenta, chyba że konto użytkownika nie znajduje się w tym samym najemcy co emitent - na przykład goście. Jeśli oświadczenie nie jest obecny, oznacza to, że wartość `iss` może być używana zamiast.  W przypadku kont osobistych używanych w kontekście organizacyjnym (na przykład konta `idp` osobistego zaproszonego do dzierżawy usługi Azure AD) oświadczenie może `9188040d-6c67-4c5b-b112-36a304b66dad`być "live.com" lub identyfikator URI STS zawierający dzierżawę konta Microsoft. |
| `iat` | int, sygnatura czasowa UNIX | "Wystawione w" wskazuje, kiedy wystąpił uwierzytelnianie dla tego tokenu. |
| `nbf` | int, sygnatura czasowa UNIX | Oświadczenie "nbf" (nie wcześniej) określa czas, przed którym JWT nie może być przyjęte do przetworzenia. |
| `exp` | int, sygnatura czasowa UNIX | Oświadczenie "exp" (czas wygaśnięcia) określa czas wygaśnięcia lub po którym JWT nie może być przyjęte do przetworzenia. Należy pamiętać, że zasób może odrzucić token przed tym razem, jak również, na przykład, gdy zmiana uwierzytelniania jest wymagana lub wykryto odwołanie tokenu. |
| `aio` | Nieprzezroczysty ciąg | Oświadczenie wewnętrzne używane przez usługę Azure AD do rejestrowania danych do ponownego użycia tokenu. Zasoby nie powinny używać tego oświadczenia. |
| `acr` | Ciąg, "0" lub "1" | Obecny tylko w tokenach w wersji 1.0. Oświadczenie "Klasa kontekstu uwierzytelniania". Wartość "0" oznacza, że uwierzytelnianie użytkownika końcowego nie spełniało wymagań normy ISO/IEC 29115. |
| `amr` | Tablica ciągów JSON | Obecny tylko w tokenach w wersji 1.0. Identyfikuje sposób uwierzytelniania obiektu tokenu. Zobacz [sekcję roszczenia amr, aby](#the-amr-claim) uzyskać więcej informacji. |
| `appid` | Ciąg, identyfikator GUID | Obecny tylko w tokenach w wersji 1.0. Identyfikator aplikacji klienta przy użyciu tokenu. Aplikacja może działać samodzielnie lub w imieniu użytkownika. Identyfikator aplikacji zazwyczaj reprezentuje obiekt aplikacji, ale może również reprezentować obiekt jednostki usługi w usłudze Azure AD. |
| `appidacr` | "0", "1" lub "2" | Obecny tylko w tokenach w wersji 1.0. Wskazuje sposób uwierzytelnionego klienta. Dla klienta publicznego wartość jest "0". Jeśli używany jest identyfikator klienta i klucz tajny klienta, wartość to "1". Jeśli certyfikat klienta został użyty do uwierzytelniania, wartość to "2". |
| `azp` | Ciąg, identyfikator GUID | Występuje tylko w tokenach v2.0, zastępując `appid`. Identyfikator aplikacji klienta przy użyciu tokenu. Aplikacja może działać samodzielnie lub w imieniu użytkownika. Identyfikator aplikacji zazwyczaj reprezentuje obiekt aplikacji, ale może również reprezentować obiekt jednostki usługi w usłudze Azure AD. |
| `azpacr` | "0", "1" lub "2" | Występuje tylko w tokenach v2.0, zastępując `appidacr`. Wskazuje sposób uwierzytelnionego klienta. Dla klienta publicznego wartość jest "0". Jeśli używany jest identyfikator klienta i klucz tajny klienta, wartość to "1". Jeśli certyfikat klienta został użyty do uwierzytelniania, wartość to "2". |
| `preferred_username` | Ciąg | Podstawowa nazwa użytkownika, która reprezentuje użytkownika. Może to być adres e-mail, numer telefonu lub ogólna nazwa użytkownika bez określonego formatu. Jego wartość jest zmienna i może się zmieniać w czasie. Ponieważ jest modyfikowalna, wartość ta nie może być używana do podejmowania decyzji dotyczących autoryzacji.  Może być jednak używany do podpowiedzi dotyczących nazwy użytkownika. Zakres `profile` jest wymagany do otrzymania tego roszczenia. |
| `name` | Ciąg | Zapewnia wartość czytelną dla człowieka, która identyfikuje temat tokenu. Wartość nie jest gwarantowana jako unikatowa, jest zmienna i jest przeznaczona do użytku tylko do celów wyświetlania. Zakres `profile` jest wymagany do otrzymania tego roszczenia. |
| `scp` | Ciąg, oddzielona spacja lista zakresów | Zestaw zakresów ujawnionych przez aplikację, dla której aplikacja kliencka zażądała (i otrzymała) zgodę. Aplikacja powinna sprawdzić, czy te zakresy są prawidłowe są udostępniane przez aplikację i podejmować decyzje dotyczące autoryzacji na podstawie wartości tych zakresów. Uwzględnione tylko dla [tokenów użytkownika](#user-and-application-tokens). |
| `roles` | Tablica ciągów, lista uprawnień | Zestaw uprawnień udostępnianych przez aplikację, że aplikacja żądająca lub użytkownik otrzymał uprawnienia do wywołania. W przypadku [tokenów aplikacji](#user-and-application-tokens)jest to używane podczas przepływu poświadczeń klienta ([wersja 1.0](../azuread-dev/v1-oauth2-client-creds-grant-flow.md), [wersja 2.0](v2-oauth2-client-creds-grant-flow.md)) zamiast zakresów użytkownika.  W przypadku [tokenów użytkownika](#user-and-application-tokens) jest to wypełniane rolami, do które użytkownik został przypisany w aplikacji docelowej. |
| `wids` | Tablica identyfikatorów GUID [roletemplateID](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#role-template-ids) | Oznacza role dla całej dzierżawy przypisane do tego użytkownika, z sekcji ról obecnych [na stronie role administratora](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#role-template-ids).  To oświadczenie jest skonfigurowane na podstawie aplikacji, za pośrednictwem `groupMembershipClaims` właściwości [manifestu aplikacji](reference-app-manifest.md).  Ustawienie go na "Wszystkie" lub "DirectoryRole" jest wymagane.  Może nie być obecny w tokenach uzyskanych za pośrednictwem przepływu niejawnego ze względu na obawy dotyczące długości tokenu. |
| `groups` | Tablica identyfikatorów GUID JSON | Udostępnia identyfikatory obiektów, które reprezentują członkostwo podmiotu w grupie. Te wartości są unikatowe (zobacz identyfikator obiektu) i mogą być bezpiecznie używane do zarządzania dostępem, takie jak wymuszanie autoryzacji dostępu do zasobu. Grupy uwzględnione w żądaniu grup są konfigurowane na podstawie `groupMembershipClaims` aplikacji, za pośrednictwem właściwości [manifestu aplikacji](reference-app-manifest.md). Wartość null wyklucza wszystkie grupy, wartość "SecurityGroup" będzie zawierać tylko członkostwo w grupie zabezpieczeń usługi Active Directory, a wartość "Wszystkie" będzie zawierać zarówno grupy zabezpieczeń, jak i listy dystrybucyjne usługi Office 365. <br><br>Szczegółowe `hasgroups` informacje na temat wykorzystania `groups` roszczenia z dorozumianą dotacją można znaleźć w poniższym wniosku. <br>W przypadku innych przepływów, jeśli liczba grup, w których znajduje się użytkownik, przekracza limit (150 dla SAML, 200 dla JWT), do źródeł oświadczeń wskazujących punkt końcowy programu Microsoft Graph zawierający listę grup dla użytkownika zostanie dodane oświadczenie dotyczące nadania reklamacji. |
| `hasgroups` | Wartość logiczna | Jeśli jest `true`obecny, zawsze oznaczający użytkownika znajduje się w co najmniej jednej grupie. Używane zamiast `groups` oświadczenia dla JWTs w niejawnych przepływów dotacji, jeśli pełne grupy oświadczenia rozszerzyłby fragment identyfikatora URI poza limity długości adresu URL (obecnie 6 lub więcej grup). Wskazuje, że klient powinien używać interfejsu API programu Microsoft Graph do określania grup użytkownika (`https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects`). |
| `groups:src1` | Obiekt JSON | W przypadku żądań tokenu, `hasgroups` które nie są ograniczone (patrz wyżej), ale nadal zbyt duże dla tokenu, zostanie uwzględnione łącze do pełnej listy grup dla użytkownika. Dla JWTs jako roszczenia rozproszonego, dla SAML jako `groups` nowego roszczenia zamiast roszczenia. <br><br>**Przykładowa wartość JWT:** <br> `"groups":"src1"` <br> `"_claim_sources`: `"src1" : { "endpoint" : "https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects" }` |
| `sub` | Ciąg, identyfikator GUID | Podmiot zabezpieczeń, o którym token potwierdza informacje, takie jak użytkownik aplikacji. Ta wartość jest niezmienna i nie można jej ponownie przypisać ani ponownie. Może służyć do bezpiecznego sprawdzania autoryzacji, na przykład gdy token jest używany do uzyskiwania dostępu do zasobu i może służyć jako klucz w tabelach bazy danych. Ponieważ temat jest zawsze obecny w tokenach, które wystawiają usługę Azure AD, zaleca się używanie tej wartości w systemie autoryzacji ogólnego przeznaczenia. Temat jest jednak identyfikatorem pairwise - jest unikatowy dla określonego identyfikatora aplikacji. W związku z tym jeśli pojedynczy użytkownik zaloguje się do dwóch różnych aplikacji przy użyciu dwóch różnych identyfikatorów klienta, te aplikacje otrzymają dwie różne wartości dla oświadczenia podmiotu. Może to być lub nie może być pożądane w zależności od architektury i wymagań dotyczących prywatności. Zobacz też `oid` oświadczenie (które pozostaje takie samo w aplikacjach w dzierżawie). |
| `oid` | Ciąg, identyfikator GUID | Niezmienny identyfikator obiektu na platformie tożsamości firmy Microsoft, w tym przypadku konta użytkownika. Może również służyć do przeprowadzania kontroli autoryzacji bezpiecznie i jako klucz w tabelach bazy danych. Ten identyfikator jednoznacznie identyfikuje użytkownika w różnych aplikacjach — dwie różne aplikacje `oid` logujące się w tym samym użytkowniku otrzymają tę samą wartość w reklamacji. W `oid` związku z tym może służyć podczas wykonywania zapytań do usług online firmy Microsoft, takich jak Microsoft Graph. Program Microsoft Graph zwróci ten `id` identyfikator jako właściwość dla danego [konta użytkownika.](/graph/api/resources/user) Ponieważ `oid` umożliwia wielu aplikacjom skorelowanie użytkowników, `profile` zakres jest wymagany do odbierania tego oświadczenia. Należy zauważyć, że jeśli jeden użytkownik istnieje w wielu dzierżaw, użytkownik będzie zawierać inny identyfikator obiektu w każdej dzierżawie — są one uważane za różne konta, nawet jeśli użytkownik loguje się do każdego konta z tymi samymi poświadczeniami. |
| `tid` | Ciąg, identyfikator GUID | Reprezentuje dzierżawę usługi Azure AD, z których pochodzi użytkownik. W przypadku kont służbowych identyfikator GUID jest niezmiennym identyfikatorem dzierżawy organizacji, do której należy użytkownik. W przypadku kont osobistych wartość to `9188040d-6c67-4c5b-b112-36a304b66dad`. Zakres `profile` jest wymagany do otrzymania tego roszczenia. |
| `unique_name` | Ciąg | Obecny tylko w tokenach w wersji 1.0. Udostępnia zrozumiałą wartość identyfikującą podmiot tokenu. Ta wartość nie jest gwarantowana jest unikatowa w obrębie dzierżawy i powinny być używane tylko do celów wyświetlania. |
| `uti` | Nieprzezroczysty ciąg | Oświadczenie wewnętrzne używane przez platformę Azure do ponownego potwierdzania tokenów. Zasoby nie powinny używać tego oświadczenia. |
| `rh` | Nieprzezroczysty ciąg | Oświadczenie wewnętrzne używane przez platformę Azure do ponownego potwierdzania tokenów. Zasoby nie powinny używać tego oświadczenia. |
| `ver` | Ciąg, albo `1.0``2.0` | Wskazuje wersję tokenu dostępu. |

**Grupy roszczenia dotyczące nadaboju**

Aby upewnić się, że rozmiar tokenu nie przekracza limitów rozmiaru nagłówka HTTP, usługa Azure AD ogranicza liczbę identyfikatorów obiektów, które zawiera w żądaniu grup. Jeśli użytkownik jest członkiem więcej grup niż limit przekrojowe (150 dla tokenów SAML, 200 dla tokenów JWT), a następnie usługi Azure AD nie emituje oświadczenia grup w tokenie. Zamiast tego zawiera oświadczenie nadajność w tokenie, który wskazuje do aplikacji do kwerendy interfejsu API programu Microsoft Graph, aby pobrać członkostwo użytkownika w grupie.

```JSON
{
  ...
  "_claim_names": {
   "groups": "src1"
    },
    {
  "_claim_sources": {
    "src1": {
        "endpoint":"[Url to get this user's group membership from]"
        }
       }
     }
  ...
 }
 ```

Można użyć `BulkCreateGroups.ps1` podanych w folderze [Skrypty tworzenia aplikacji,](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-2-Groups/AppCreationScripts) aby przetestować scenariusze przecewiania.

#### <a name="v10-basic-claims"></a>v1.0 podstawowe roszczenia

Następujące oświadczenia zostaną uwzględnione w tokenach w wersji 1.0, jeśli ma to zastosowanie, ale domyślnie nie są uwzględniane w tokenach w wersji 2.0. Jeśli używasz wersji 2.0 i potrzebujesz jednego z tych oświadczeń, poproś o ich użycie za pomocą [opcjonalnych oświadczeń.](active-directory-optional-claims.md)

| Claim | Format | Opis |
|-----|--------|-------------|
| `ipaddr`| Ciąg | Adres IP, z na podstawie który użytkownik uwierzytelnił. |
| `onprem_sid`| Ciąg w [formacie SID](https://docs.microsoft.com/windows/desktop/SecAuthZ/sid-components) | W przypadkach, gdy użytkownik ma uwierzytelnianie lokalne, to oświadczenie zapewnia ich identyfikator SID. Można użyć `onprem_sid` do autoryzacji w starszych aplikacjach.|
| `pwd_exp`| int, sygnatura czasowa UNIX | Wskazuje, kiedy hasło użytkownika wygaśnie. |
| `pwd_url`| Ciąg | Adres URL, pod którym użytkownicy mogą wysyłać hasła, aby zresetować swoje hasło. |
| `in_corp`| wartość logiczna | Sygnalizuje, że klient loguje się z sieci firmowej. Jeśli tak nie jest, roszczenie nie jest uwzględniane. |
| `nickname`| Ciąg | Dodatkowa nazwa użytkownika, oddzielona od imienia lub nazwiska.|
| `family_name` | Ciąg | Zawiera nazwisko, nazwisko lub nazwisko użytkownika zgodnie z definicją w obiekcie użytkownika. |
| `given_name` | Ciąg | Zawiera pierwszą lub podana nazwa użytkownika, zgodnie z ustawą w obiekcie użytkownika. |
| `upn` | Ciąg | Nazwa użytkownika. Może to być numer telefonu, adres e-mail lub niesformatowany ciąg znaków. Powinien być używany tylko do celów wyświetlania i dostarczania wskazówek dotyczących nazwy użytkownika w scenariuszach ponownego uwierzytelniania. |

#### <a name="the-amr-claim"></a>Roszczenie `amr`

Tożsamości firmy Microsoft można uwierzytelnić na różne sposoby, które mogą być istotne dla aplikacji. Oświadczenie `amr` jest tablicą, która może zawierać `["mfa", "rsa", "pwd"]`wiele elementów, takich jak , dla uwierzytelniania, które używało zarówno hasła, jak i aplikacji Authenticator.

| Wartość | Opis |
|-----|-------------|
| `pwd` | Uwierzytelnianie hasłem, hasło użytkownika firmy Microsoft lub klucz tajny klienta aplikacji. |
| `rsa` | Uwierzytelnianie opierało się na dowodzie klucza RSA, na przykład w [aplikacji Microsoft Authenticator](https://aka.ms/AA2kvvu). Dotyczy to również, jeśli uwierzytelnianie zostało wykonane przez własny podpisany JWT z certyfikatem X509 należącym do usługi. |
| `otp` | Jednorazowy kod dostępu za pomocą wiadomości e-mail lub wiadomości tekstowej. |
| `fed` | Użyto potwierdzenia uwierzytelniania federacyjnego (takiego jak JWT lub SAML). |
| `wia` | Zintegrowane uwierzytelnianie systemu Windows |
| `mfa` | Użyto uwierzytelniania wieloskładnikowego. Gdy jest to obecne, zostaną również uwzględnione inne metody uwierzytelniania. |
| `ngcmfa` | Odpowiednik `mfa`, używane do inicjowania obsługi administracyjnej niektórych zaawansowanych typów poświadczeń. |
| `wiaormfa`| Do uwierzytelnienia użytkownik użył systemu Windows lub poświadczenia usługi MFA. |
| `none` | Uwierzytelnianie nie zostało wykonane. |

## <a name="validating-tokens"></a>Sprawdzanie poprawności tokenów

Aby sprawdzić poprawność id_token lub access_token, aplikacja powinna sprawdzić poprawność zarówno podpisu tokenu, jak i oświadczeń. Aby sprawdzić poprawność tokenów dostępu, aplikacja powinna również sprawdzić poprawność wystawcy, odbiorców i tokenów podpisywania. Muszą one zostać zweryfikowane względem wartości w dokumencie odnajdywania OpenID. Na przykład niezależna od dzierżawy wersja [https://login.microsoftonline.com/common/.well-known/openid-configuration](https://login.microsoftonline.com/common/.well-known/openid-configuration)dokumentu znajduje się w pliku .

Oprogramowanie pośredniczące usługi Azure AD ma wbudowane funkcje sprawdzania poprawności tokenów dostępu i można przeglądać nasze [przykłady,](https://docs.microsoft.com/azure/active-directory/active-directory-code-samples) aby znaleźć je w wybranym języku.

Udostępniamy biblioteki i przykłady kodu, które pokazują, jak obsługiwać sprawdzanie poprawności tokenu. Poniższe informacje są dostarczane dla tych, którzy chcą zrozumieć podstawowy proces. Istnieje również kilka bibliotek open source innych firm dostępnych do sprawdzania poprawności JWT - istnieje co najmniej jedna opcja dla prawie każdej platformy i języka. Aby uzyskać więcej informacji na temat bibliotek uwierzytelniania usługi Azure AD i przykładów kodu, zobacz [biblioteki uwierzytelniania w wersji 1.0](../azuread-dev/active-directory-authentication-libraries.md) i [biblioteki uwierzytelniania w wersji 2.0](reference-v2-libraries.md).

### <a name="validating-the-signature"></a>Sprawdzanie poprawności podpisu

JWT zawiera trzy segmenty, które są `.` oddzielone znakiem. Pierwszy segment jest znany jako **nagłówek,** drugi jako **treść,** a trzeci jako **podpis.** Segment podpisu może służyć do sprawdzania autentyczności tokenu, dzięki czemu można mu zaufać przez aplikację.

Tokeny wystawione przez usługę Azure AD są podpisywane przy użyciu standardowych algorytmów szyfrowania asymetrycznego standardu branżowego, takich jak RS256. Nagłówek JWT zawiera informacje o kluczu i metodzie szyfrowania używanej do podpisywania tokenu:

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk",
  "kid": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk"
}
```

Oświadczenie `alg` wskazuje algorytm, który został użyty do `kid` podpisania tokenu, podczas gdy oświadczenie wskazuje określony klucz publiczny, który został użyty do sprawdzania poprawności tokenu.

W dowolnym momencie usługa Azure AD może podpisać id_token przy użyciu jednego z określonego zestawu par kluczy publiczno-prywatnych. Usługa Azure AD obraca możliwy zestaw kluczy okresowo, więc aplikacja powinna być zapisywana do obsługi tych zmian klucza automatycznie. Rozsądna częstotliwość sprawdzania dostępności aktualizacji kluczy publicznych używanych przez usługę Azure AD jest co 24 godziny.

Można uzyskać dane klucza podpisywania niezbędne do sprawdzania poprawności podpisu przy użyciu [dokumentu metadanych OpenID Connect](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document) znajdującego się pod adresem:

```
https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration
```

> [!TIP]
> Wypróbuj ten [adres URL](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) w przeglądarce!

Ten dokument metadanych:

* Jest obiektem JSON zawierającym kilka przydatnych informacji, takich jak lokalizacja różnych punktów końcowych wymaganych do uwierzytelniania OpenID Connect.
* Zawiera `jwks_uri`program , który podaje lokalizację zestawu kluczy publicznych używanych do podpisywania tokenów. JSON Web Key (JWK) `jwks_uri` znajduje się w zawiera wszystkie informacje o kluczu publicznym w użyciu w danym momencie w czasie.  Format JWK jest opisany w [RFC 7517](https://tools.ietf.org/html/rfc7517).  Aplikacja może użyć `kid` oświadczenia w nagłówku JWT, aby wybrać klucz publiczny w tym dokumencie został użyty do podpisania określonego tokenu. Następnie można wykonać sprawdzanie poprawności podpisu przy użyciu poprawnego klucza publicznego i wskazanego algorytmu.

> [!NOTE]
> Punkt końcowy w wersji 1.0 zwraca zarówno `x5t` i `kid` oświadczeń, podczas gdy punkt `kid` końcowy w wersji 2.0 odpowiada tylko oświadczenia. W przyszłości zalecamy `kid` użycie oświadczenia do sprawdzania poprawności tokenu.

Sprawdzanie poprawności podpisu wykracza poza zakres tego dokumentu — istnieje wiele bibliotek open source dostępnych do pomocy w tym zakresie, jeśli to konieczne.  Jednak platforma Microsoft Identity ma jedno rozszerzenie podpisywania tokenów do standardów — niestandardowe klucze podpisywania.

Jeśli aplikacja ma niestandardowe klucze podpisywania w wyniku korzystania z `appid` funkcji [mapowania oświadczeń,](active-directory-claims-mapping.md) należy dołączyć parametr kwerendy zawierający identyfikator aplikacji, aby uzyskać `jwks_uri` wskazujące informacje klucza podpisywania aplikacji, które powinny być używane do sprawdzania poprawności. Na przykład: `https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` `jwks_uri` zawiera `https://login.microsoftonline.com/{tenant}/discovery/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e`a z .

### <a name="claims-based-authorization"></a>Autoryzacja oparta na zgłoszeniach

Logika biznesowa aplikacji będzie dyktować ten krok, niektóre typowe metody autoryzacji są określone poniżej.

* Sprawdź `scp` lub `roles` oświadczenie, aby sprawdzić, czy wszystkie obecne zakresy są zgodne z zakresami udostępnianych przez interfejs API i zezwalaj klientowi na wykonać żądaną akcję.
* Upewnij się, że klient wywołujący może `appid` wywołać interfejs API przy użyciu oświadczenia.
* Sprawdź poprawność stanu uwierzytelniania `appidacr` klienta wywołującego przy użyciu — nie powinno być 0, jeśli klienci publiczni nie mogą wywoływać interfejsu API.
* Sprawdź listę wcześniejszych `nonce` oświadczeń, aby sprawdzić, czy token nie jest odtwarzany.
* Sprawdź, `tid` czy pasuje do dzierżawy, która może wywołać interfejs API.
* Użyj `acr` oświadczenia, aby sprawdzić, czy użytkownik wykonał uwierzytelnianie wieloskładnikowe. Należy to wymuszać przy użyciu [dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/conditional-access/overview).
* Jeśli zażądano `roles` lub `groups` oświadczeń w tokenie dostępu, sprawdź, czy użytkownik jest w grupie może wykonać tę akcję.
  * W przypadku tokenów pobranych przy użyciu przepływu niejawnego prawdopodobnie należy zbadać [program Microsoft Graph](https://developer.microsoft.com/graph/) dla tych danych, ponieważ często są zbyt duże, aby zmieścić się w tokenie.

## <a name="user-and-application-tokens"></a>Tokeny użytkowników i aplikacji

Aplikacja może odbierać tokeny w imieniu użytkownika (zwykły przepływ) lub bezpośrednio z aplikacji (za pośrednictwem przepływu poświadczeń klienta ([wersja 1.0](../azuread-dev/v1-oauth2-client-creds-grant-flow.md), [v2.0](v2-oauth2-client-creds-grant-flow.md)). Te tokeny tylko do aplikacji wskazują, że to wywołanie pochodzi z aplikacji i nie ma użytkownika, który je obsługuje. Te tokeny są obsługiwane w dużej mierze takie same, z pewnymi różnicami:

* Tokeny tylko do aplikacji `scp` nie będą miały roszczenia `roles` i mogą mieć roszczenie. W tym miejscu zostaną zarejestrowane uprawnienia aplikacji (w przeciwieństwie do uprawnień delegowanych). Aby uzyskać więcej informacji na temat uprawnień delegowanych i aplikacji, zobacz uprawnienia i zgoda ([wersja 1.0](../azuread-dev/v1-permissions-consent.md), [wersja 2.0](v2-permissions-and-consent.md)).
* Brakuje wielu roszczeń specyficznych dla `name` człowieka, takich jak lub `upn`.
* Roszczenia `sub` `oid` i roszczenia będą takie same.

## <a name="token-revocation"></a>Odwołanie tokenu

Tokeny odświeżania mogą zostać unieważnione lub odwołane w dowolnym momencie z różnych powodów. Dzielą się one na dwie główne kategorie: limity czasu i cofnięcia.

### <a name="token-timeouts"></a>Limity czasu tokenu

Przy użyciu [konfiguracji okresu istnienia tokenu](active-directory-configurable-token-lifetimes.md)można zmienić okres istnienia tokenów odświeżania.  Jest to normalne i oczekuje się, że niektóre tokeny przejść bez użycia (np. użytkownik nie otwiera aplikację przez 3 miesiące) i dlatego wygasa.  Aplikacje napotkają scenariusze, w których serwer logowania odrzuca token odświeżania ze względu na jego wiek. 

* MaxInactiveTime: Jeśli token odświeżania nie został użyty w czasie podyktowanym przez MaxInactiveTime, token odświeżania nie będzie już prawidłowy.
* MaxSessionAge: Jeśli MaxAgeSessionMultiFactor lub MaxAgeSessionSingleFactor zostały ustawione na coś innego niż ich domyślne (Until-revoked), a następnie ponowneuthentication będą wymagane po upływie czasu ustawionego w MaxAgeSession * upływa.
* Przykłady:
  * Dzierżawca ma MaxInactiveTime pięciu dni, a użytkownik udał się na wakacje na tydzień, a więc usługa Azure AD nie widział nowego żądania tokenu od użytkownika w ciągu 7 dni. Następnym razem, gdy użytkownik zażąda nowego tokenu, znajdzie ich token odświeżania został odwołany i muszą wprowadzić swoje poświadczenia ponownie.
  * Aplikacja wrażliwa ma MaxAgeSessionSingleFactor jednego dnia. Jeśli użytkownik zaloguje się w poniedziałek i we wtorek (po upływie 25 godzin), będzie musiał ponownie uwierzytelnić.

### <a name="revocation"></a>Odwołania

Tokeny odświeżania mogą zostać odwołane przez serwer z powodu zmiany poświadczeń lub z powodu użycia lub działania administratora.  Tokeny odświeżania dzielą się na dwie klasy — te wystawione klientom poufnym (kolumna po prawej stronie) i tokeny wystawione klientom publicznym (wszystkie inne kolumny).   

|   | Plik cookie oparty na hasłach | Token oparty na hasłach | Plik cookie nieoparty na hasłach | Token nieoparty na hasłach | Poufny token klienta |
|---|-----------------------|----------------------|---------------------------|--------------------------|---------------------------|
| Hasło wygasa | Pozostaje przy życiu | Pozostaje przy życiu | Pozostaje przy życiu | Pozostaje przy życiu | Pozostaje przy życiu |
| Hasło zmienione przez użytkownika | Revoked | Revoked | Pozostaje przy życiu | Pozostaje przy życiu | Pozostaje przy życiu |
| Użytkownik wykonuje wiele łat. | Revoked | Revoked | Pozostaje przy życiu | Pozostaje przy życiu | Pozostaje przy życiu |
| Administrator resetuje hasło | Revoked | Revoked | Pozostaje przy życiu | Pozostaje przy życiu | Pozostaje przy życiu |
| Użytkownik odwołuje tokeny odświeżania [za pośrednictwem programu PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureadsignedinuserallrefreshtoken) | Revoked | Revoked | Revoked | Revoked | Revoked |
| Administrator odwołuje wszystkie tokeny odświeżania dla użytkownika [za pośrednictwem programu PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken) | Revoked | Revoked |Revoked | Revoked | Revoked |
| Wylogowywanie jednokrotne ([v1.0](../azuread-dev/v1-protocols-openid-connect-code.md#single-sign-out), [v2.0](v2-protocols-oidc.md#single-sign-out) ) w internecie | Revoked | Pozostaje przy życiu | Revoked | Pozostaje przy życiu | Pozostaje przy życiu |

> [!NOTE]
> Login "Bez hasła" to login, w którym użytkownik nie wpisywać hasła, aby go uzyskać. Na przykład za pomocą twarzy z windows hello, kluczEM FIDO2 lub kodem PIN.
>
> Podstawowe tokeny odświeżania (PRT) w systemie Windows 10 są segregowane na podstawie poświadczeń. Na przykład Windows Hello i hasło mają swoje odpowiednie PRT, odizolowane od siebie. Gdy użytkownik zaloguje się przy użyciu poświadczeń Hello (PIN lub biometrii), a następnie zmieni hasło, hasło oparte NA PRT uzyskane wcześniej zostanie odwołany. Zalogowanie się z powrotem przy za pomocą hasła unieważnia stary PRT i żąda nowego.
>
> Tokeny odświeżania nie są unieważniane ani odwoływane, gdy są używane do pobierania nowego tokenu dostępu i odświeżania tokenu.  Jednak aplikacja powinna odrzucić stary, jak tylko jest używany i zastąpić go nowym, ponieważ nowy token ma nowy czas wygaśnięcia w nim. 

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej [ `id_tokens` na](id-tokens.md)temat usługi Azure AD .
* Dowiedz się więcej o zezwolenie i zgoda ( [v1.0](../azuread-dev/v1-permissions-consent.md), [v2.0](v2-permissions-and-consent.md)).
