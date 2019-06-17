---
title: Program Microsoft access platformy tożsamości tokenów odwołania | Azure
description: Więcej informacji na temat tokenów dostępu wyemitowane przez usługę Azure AD w wersji 1.0 i punktów końcowych platformy (w wersji 2.0) tożsamości firmy Microsoft.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/13/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 355e61fdfd9847e54a4bd13ac3b0f2d416c05812
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111954"
---
# <a name="microsoft-identity-platform-access-tokens"></a>Tokeny dostępu platforma tożsamości firmy Microsoft

Tokeny dostępu umożliwia klientom bezpiecznie wywoływać interfejsy API chronionej przez platformę Azure. Tokeny dostępu platforma tożsamości firmy Microsoft są [tokenów Jwt](https://tools.ietf.org/html/rfc7519), obiekty JSON, podpisanego przez platformę Azure kodowany w formacie Base64. Klienci powinien traktować dostępu tokenów jako nieprzezroczysty ciągi, zawartość, token są przeznaczone tylko do tego zasobu. Do sprawdzania poprawności i debugowania, deweloperzy mogą odszyfrowywać tokenów Jwt, przy użyciu witryny, takie jak [jwt.ms](https://jwt.ms). Klient może uzyskiwanie tokenu dostępu z punktu końcowego w wersji 1.0 lub punktu końcowego v2.0, przy użyciu różnych protokołów.

Gdy klient żąda tokenu dostępu, usługi Azure AD zwraca także niektóre metadane dotyczące tokenu dostępu do użycia w Twojej aplikacji. Informacje te obejmują czas wygaśnięcia tokenu dostępu i zakresy, dla których jest on prawidłowy. Te dane umożliwia aplikacji, aby wykonać inteligentne buforowanie tokenów dostępu, bez konieczności przeanalizować tokenu dostępu, sama.

Jeśli aplikacja jest klienci mogą żądać dostępu do zasobów (interfejsu API sieci web), tokeny dostępu zapewniają pomocne informacje na potrzeby uwierzytelniania i autoryzacji, takich jak użytkownik, klient, wystawcy i uprawnienia.

Zobacz następujące sekcje, aby dowiedzieć się, jak zasób można sprawdzać poprawność i korzystanie z oświadczeń w tokenie dostępu.

> [!IMPORTANT]
> Tokeny dostępu tworzone są na podstawie *odbiorców* tokenu, co oznacza, aplikacji, która jest właścicielem zakresów w tokenie.  Jest to sposób ustawienia zasobów `accessTokenAcceptedVersion` w [manifest aplikacji](reference-app-manifest.md#manifest-reference) do `2` umożliwia klientowi wywołanie punktu końcowego w wersji 1.0 do odbierania token dostępu w wersji 2.0.  Podobnie, to dlaczego zmiany token dostępu [opcjonalnych oświadczeń](active-directory-optional-claims.md) dla Twojego klienta nie zmieniać Odebrano tokenu dostępu, gdy token jest wymagany dla `user.read`, który jest własnością zasobów MS Graph.  
> Z tego samego powodu podczas testowania aplikacji klienckiej przy użyciu osobistego konta (np. hotmail.com lub outlook.com), może się okazać, że token dostępu odebranych przez klienta to nieprzezroczysty ciąg. Jest to spowodowane zasobu, do którego wysłano żądanie starszych biletów (konto Microsoft) MSA, są szyfrowane, które nie może być rozumiany przez klienta.

## <a name="sample-tokens"></a>Przykładowe tokenów

tokeny w wersji 1.0 i 2.0 wyglądać mniej więcej i zawierać wiele z tych samych oświadczeń. Przykład każdego z nich znajduje się w tym miejscu.

### <a name="v10"></a>Wersja 1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0=.D3H6pMUtQnoJAGq6AHd
```

Wyświetl ten token w wersji 1.0 w [JWT.ms](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0=.D3H6pMUtQnoJAGq6AHd).

### <a name="v20"></a>Wersja 2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt
```

Wyświetl ten token w wersji 2.0 w [JWT.ms](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt).

## <a name="claims-in-access-tokens"></a>Oświadczenia w tokeny dostępu

Tokenów Jwt są podzielone na trzy części:

* **Nagłówek** — zawiera informacje o sposobie [zweryfikować token](#validating-tokens) wraz z informacjami dotyczącymi typu tokenu i jak został on podpisany.
* **Ładunek** — zawiera wszystkie ważne dane dotyczące użytkownika lub aplikacji, która próbuje wywołać usługi.
* **Podpis** — jest to istotne raw, używany do sprawdzania poprawności tokenu.

Każdy element jest oddzielony kropką (`.`) i oddzielnie zakodowane w formacie Base64.

Oświadczenia są obecne, tylko wtedy, gdy istnieje wartość, aby wypełnić go. Tak aplikacja nie powinna zależni oświadczenia, jest on nieobecny. Przykłady obejmują `pwd_exp` (nie każdego dzierżawcę wymaga hasła wygasną) lub `family_name` ([poświadczeń klienta](v1-oauth2-client-creds-grant-flow.md) przepływy są w imieniu aplikacji, które nie mają nazwy). Oświadczenia używane do weryfikacji tokenów dostępu, zawsze będzie istnieć.

> [!NOTE]
> Niektóre oświadczenia są używane usługi Azure AD, zabezpieczenia tokenów w przypadku ponownego użycia. Te zostaną oznaczone jako nie jest do użytku publicznego w opisie jako "Nieprzezroczyste". Te oświadczenia mogą lub nie może występować w tokenie i mogą być dodawane nowe bez powiadomienia.

### <a name="header-claims"></a>Nagłówek oświadczeń

|Claim | Format | Opis |
|--------|--------|-------------|
| `typ` | Ciąg — zawsze "JWT" | Wskazuje, czy token jest token JWT.|
| `nonce` | String | Unikatowy identyfikator używany do ochrony przed atakami powtarzania tokenu. Zasób może zapisać tę wartość, aby zapewnić ochronę przed odtworzenie. |
| `alg` | String | Określa algorytm, który był używany do podpisywania tokenu, na przykład "RS256" |
| `kid` | String | Określa odcisk palca klucza publicznego, który jest używany do podpisywania tego tokenu. Emitowane zarówno w wersji 1.0, jak i w wersji 2.0 tokenów dostępu. |
| `x5t` | String | Działa tak samo, (w użyciu i wartości) jako `kid`. `x5t` starsze oświadczenia znacznikowej tylko w tokeny dostępu w wersji 1.0 do celów zgodności. |

### <a name="payload-claims"></a>Ładunek oświadczeń

| Claim | Format | Opis |
|-----|--------|-------------|
| `aud` | Ciąg, identyfikator URI Identyfikatora aplikacji | Identyfikuje zamierzonym odbiorcą tokenu. W tokenach dostępu odbiorców jest identyfikator aplikacji, przypisany do aplikacji w witrynie Azure portal. Aplikację należy sprawdzić tę wartość i odrzucenie tokenu, jeśli wartość nie jest zgodny. |
| `iss` | Ciąg identyfikatora URI usługi STS | Określa usługę tokenu zabezpieczającego (STS), który tworzy i zwraca token i dzierżawy usługi Azure AD, w którym użytkownik został uwierzytelniony. Jeśli token wystawiony token w wersji 2.0 (zobacz `ver` oświadczeń), identyfikator URI będzie kończyć się `/v2.0`. Identyfikator GUID, który wskazuje, czy użytkownik jest użytkownikiem odbiorcy z konta Microsoft jest `9188040d-6c67-4c5b-b112-36a304b66dad`. Aplikację należy użyć część stanowiącą identyfikator GUID oświadczenia, aby ograniczyć zestaw dzierżawców, którzy mogą zalogować do aplikacji, jeśli ma to zastosowanie. |
|`idp`| Ciąg, zazwyczaj identyfikator URI usługi STS | Rejestruje dostawcę tożsamości, który uwierzytelnił podmiot tokenu. Ta wartość jest taka sama jak wartość oświadczenia wystawcy, chyba że konto użytkownika nie znajduje się w tej samej dzierżawie, jako wystawca - goście, na przykład. Jeśli oświadczenia nie jest obecny, oznacza to, że wartość `iss` mogą być używane zamiast tego.  Dla osobistych kont, które są używane w kontekście organizacji (na przykład konta osobistego zaproszeni do dzierżawy usługi Azure AD) `idp` oświadczeń może być "live.com" lub identyfikator URI usługi STS dzierżawy konta Microsoft zawierającego `9188040d-6c67-4c5b-b112-36a304b66dad`. |  
| `iat` | int, sygnatura czasowa systemu UNIX | "Wydany w" wskazuje, kiedy wystąpił uwierzytelniania dla tego tokenu. |
| `nbf` | int, sygnatura czasowa systemu UNIX | Oświadczenie "nbf" (nie przed) identyfikuje czas, przed którym tokenu JWT nie muszą zostać zaakceptowane do przetworzenia. |
| `exp` | int, sygnatura czasowa systemu UNIX | Oświadczenie "exp" (czas wygaśnięcia) identyfikuje czasu wygaśnięcia na lub po którym tokenu JWT nie może zostać przyjęty do przetworzenia. Należy zauważyć, że zasób może odrzucić token przed upływem wskazanego terminu, takie jak kiedy konieczne jest wprowadzenie zmiany w uwierzytelnianiu lub token odwołania został wykryty. |
| `aio` | Nieprzezroczysty ciąg | Oświadczenie wewnętrzny używany przez usługę Azure AD do rejestrowania danych do ponownego wykorzystania tokenu. Zasobów nie należy używać tego oświadczenia. |
| `acr` | Ciąg, "0" lub "1" | Wyświetlane tylko w wersji 1.0 tokenów. Oświadczenie "Class kontekstu uwierzytelniania". Wartość "0" oznacza, że uwierzytelnianie użytkowników końcowych nie spełniał wymagań 29115 ISO/IEC. |
| `amr` | Tablica JSON ciągów | Wyświetlane tylko w wersji 1.0 tokenów. Określa, jak został uwierzytelniony podmiot tokenu. Zobacz [amr oświadczenia sekcji](#the-amr-claim) Aby uzyskać więcej informacji. |
| `appid` | Ciąg identyfikatora GUID | Wyświetlane tylko w wersji 1.0 tokenów. Identyfikator aplikacji klienta przy użyciu tokenu. Aplikacja może działać jako sama lub w imieniu użytkownika. Identyfikator aplikacji jest zazwyczaj reprezentuje obiekt aplikacji, ale może również reprezentować obiektu jednostki usługi w usłudze Azure AD. |
| `appidacr` | "0", "1" lub "2" | Wyświetlane tylko w wersji 1.0 tokenów. Wskazuje, jak klient został uwierzytelniony. Publicznych klienta wartość to "0". Jeśli identyfikator klienta oraz klucz tajny klienta są używane, wartość jest "1". Jeśli certyfikat klienta był używany do uwierzytelniania, wartość to "2". |
| `azp` | Ciąg identyfikatora GUID | Tokeny się tylko w wersji 2.0, zastępuje `appid`. Identyfikator aplikacji klienta przy użyciu tokenu. Aplikacja może działać jako sama lub w imieniu użytkownika. Identyfikator aplikacji jest zazwyczaj reprezentuje obiekt aplikacji, ale może również reprezentować obiektu jednostki usługi w usłudze Azure AD. |
| `azpacr` | "0", "1" lub "2" | Tokeny się tylko w wersji 2.0, zastępuje `appidacr`. Wskazuje, jak klient został uwierzytelniony. Publicznych klienta wartość to "0". Jeśli identyfikator klienta oraz klucz tajny klienta są używane, wartość jest "1". Jeśli certyfikat klienta był używany do uwierzytelniania, wartość to "2". |
| `preferred_username` | String | Nazwa głównej reprezentuje użytkownika. Może on być adres e-mail, numer telefonu lub ogólny nazwy użytkownika bez określonego formatu. Jego wartość jest modyfikowalna i mogą ulec zmianie wraz z upływem czasu. Ponieważ jest ona modyfikowalna, ta wartość nie należy do podejmowania decyzji dotyczących autoryzacji.  Może służyć do wskazówek dotyczących nazwy użytkownika do. `profile` Zakres jest wymagany w celu odbierania tego oświadczenia. |
| `name` | String | Zawiera czytelne dla człowieka wartość, która identyfikuje podmiotu tokenu. Wartość nie musi być unikatowy, jest ona modyfikowalna i został zaprojektowany tak, ma być używany tylko w celach wyświetlania. `profile` Zakres jest wymagany w celu odbierania tego oświadczenia. |
| `scp` | Ciąg, spacjami listy zakresów | Zestaw zakresów udostępniany przez aplikację, dla którego aplikacja kliencka zażądała (i odebrano) zgody. Aplikację należy sprawdzić, czy te zakresy są prawidłowe te udostępniane przez aplikację i podejmowania decyzji dotyczących autoryzacji na podstawie wartości tych zakresów. Tylko włączone dla [tokeny użytkowników](#user-and-application-tokens). |
| `roles` | Tablica ciągów, listy uprawnień | Zestaw uprawnień udostępniany przez aplikację, że żądanie aplikacji lub użytkownika ma uprawnienia do wywołania. Dla [tokenów aplikacji](#user-and-application-tokens), jest on używany podczas [poświadczeń klienta](v1-oauth2-client-creds-grant-flow.md) przepływ zamiast zakresy użytkownika.  Aby uzyskać [tokeny użytkowników](#user-and-application-tokens) wypełniony ról użytkownika została przypisana do aplikacji docelowej. |
| `wids` | Tablica [RoleTemplateID](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#role-template-ids) identyfikatorów GUID | Wskazuje role obowiązujące w dzierżawie, przypisane do tego użytkownika w sekcji role w [strony Role administratora](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#role-template-ids).  To oświadczenie jest skonfigurowana na podstawie poszczególnych aplikacji, za pośrednictwem `groupMembershipClaims` właściwość [manifest aplikacji](reference-app-manifest.md).  Ustawienie "Wszystkie" lub "DirectoryRole" jest wymagana.  Nie może znajdować się w tokeny uzyskanymi za pośrednictwem niejawny przepływ ze względu na wątpliwości długość tokenu. |
| `groups` | Tablica JSON identyfikatorów GUID | Zawiera identyfikatory obiektów, reprezentujących podmiotu członkostw w grupie. Te wartości są unikatowe (patrz obiektu o identyfikatorze) i może być bezpiecznie stosowany w celu zarządzania dostępem, takie jak wymuszanie autoryzacji dostępu do zasobu. Grupy uwzględniane w oświadczenie grupy są skonfigurowane na podstawie poszczególnych aplikacji, za pośrednictwem `groupMembershipClaims` właściwość [manifest aplikacji](reference-app-manifest.md). Wartość null powoduje wyłączenie wszystkich grup, wartość "SecurityGroup" będzie zawierać tylko członkostwa grupy zabezpieczeń usługi Active Directory, a wartość "All" będzie zawierać grupy zabezpieczeń i listy dystrybucyjne usługi Office 365. <br><br>Zobacz `hasgroups` oświadczenia poniżej, aby uzyskać szczegółowe informacje na temat korzystania z `groups` oświadczenia przyznawania niejawnego. <br>Dla innych przepływów Jeśli liczba grup, których użytkownik ma przekroczą limit (150 dla protokołu SAML, 200 dla tokenów JWT), następnie oświadczenie nadwyżkowe zostanie dodany do źródła oświadczeń, wskazujący na punkt końcowy usługi AAD Graph zawierający listę grup dla użytkownika. |
| `hasgroups` | Boolean | Jeśli jest obecne, zawsze `true`, oznaczające użytkownika znajduje się w co najmniej jedną grupę. Użyta zamiast `groups` oświadczenia dla elementów Jwt w przepływach przyznawanie niejawne, jeśli pełna grupy oświadczenia będzie rozszerzać składnik fragment identyfikatora URI poza granicami długość adresu URL (obecnie 6- lub więcej grup). Wskazuje, czy którego powinien używać klient programu Graph można określić grupy użytkowników (`https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects`). |
| `groups:src1` | Obiekt JSON | Dla żądań tokenów, które nie są ograniczone długość (zobacz `hasgroups` powyżej), ale nadal zbyt duży dla tokenu, łącze do listy grup pełny dla użytkownika zostaną dołączone. Dla elementów Jwt jako roszczenie rozproszonej, SAML jako nowe oświadczenie zamiast `groups` oświadczenia. <br><br>**Przykładowa wartość tokenu JWT**: <br> `"groups":"src1"` <br> `"_claim_sources`: `"src1" : { "endpoint" : "https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects" }` |
| `sub` | Ciąg identyfikatora GUID | Podmiot zabezpieczeń o tym, które token określa informacje, takie jak użytkownika aplikacji. Ta wartość jest niezmienny i nie może być ponownie przypisywany ani ponownie. Ona może służyć do sprawdzania autoryzacji bezpiecznie, np. gdy token jest używany do uzyskania dostępu do zasobu i może być używany jako klucz w tabelach bazy danych. Ponieważ podmiot jest zawsze obecne w tokenach problemy dotyczące usługi Azure AD, zalecamy używanie tej wartości w systemie autoryzacji ogólnego przeznaczenia. Temat jest jednak identyfikator pairwise — Unikatowy identyfikator określonej aplikacji. W związku z tym jeśli jeden użytkownik zaloguje się do dwóch różnych aplikacji przy użyciu dwóch identyfikatorów innego klienta, aplikacji, które otrzyma dwóch różnych wartości oświadczenia podmiotu. To może być lub może nie być wskazane w zależności od wymagań dotyczących architektury i ochrony prywatności. Zobacz też `oid` roszczenia, (które pozostają bez zmian w aplikacjach w ramach dzierżawy). |
| `oid` | Ciąg identyfikatora GUID | Niemodyfikowalny identyfikator obiektu na platformie Microsoft tożsamości, w tym przypadku konta użytkownika. Może również służyć do sprawdzania autoryzacji i bezpiecznie jako klucz w tabelach bazy danych. Ten identyfikator unikatowo identyfikuje użytkownika w aplikacjach — dwóch różnych aplikacji, rejestrowanie w ten sam użytkownik otrzyma taką samą wartość w `oid` oświadczenia. W efekcie `oid` mogą być używane podczas tworzenia kwerend do usług Microsoft online services, takich jak program Microsoft Graph. Program Microsoft Graph zwróci ten identyfikator jako `id` właściwość dla danego konta użytkownika. Ponieważ `oid` wielu aplikacjom do skorelowania użytkowników, `profile` zakres jest wymagany w celu odbierania tego oświadczenia. Należy pamiętać, że jeden użytkownik istnieje w wielu dzierżawach, użytkownik będzie zawierać identyfikator inny obiekt, w każdej dzierżawy — są traktowane jako różne konta, nawet jeśli użytkownik loguje się do każdego konta przy użyciu tych samych poświadczeń. |
| `tid` | Ciąg identyfikatora GUID | Reprezentuje dzierżawy usługi Azure AD, do której należy użytkownik. Dla kont służbowych identyfikator GUID jest identyfikator dzierżawy niezmienne organizacji, do której należy użytkownik. Dla osobistych kont, wartość jest `9188040d-6c67-4c5b-b112-36a304b66dad`. `profile` Zakres jest wymagany w celu odbierania tego oświadczenia. |
| `unique_name` | String | Wyświetlane tylko w wersji 1.0 tokenów. Udostępnia zrozumiałą wartość identyfikującą podmiot tokenu. Ta wartość nie musi być unikatowa w ramach dzierżawy i należy używać tylko w celach wyświetlania. |
| `uti` | Nieprzezroczysty ciąg | Oświadczenie wewnętrzne używane przez platformę Azure w celu ponownego zweryfikowania tokenów. Zasoby, nie używaj tego oświadczenia. |
| `rh` | Nieprzezroczysty ciąg | Oświadczenie wewnętrzne używane przez platformę Azure w celu ponownego zweryfikowania tokenów. Zasobów nie należy używać tego oświadczenia. |
| `ver` | Ciąg znaków, albo `1.0` lub `2.0` | Wskazuje wersję tokenu dostępu. |

#### <a name="v10-basic-claims"></a>podstawowe oświadczeń w wersji 1.0

Poniższe oświadczenia zostaną uwzględnione w tokenów w wersji 1.0, jeśli ma to zastosowanie, ale nie są domyślnie włączone w tokenach w wersji 2.0. Jeśli używasz wersji 2.0 i konieczności te oświadczenia żądania ich przy użyciu [opcjonalnych oświadczeń](active-directory-optional-claims.md).

| Claim | Format | Opis |
|-----|--------|-------------|
| `ipaddr`| String | Adres IP użytkownika dokonało uwierzytelnienia z komputera. |
| `onprem_sid`| Ciąg w [format identyfikatora SID](https://docs.microsoft.com/windows/desktop/SecAuthZ/sid-components) | W przypadkach, w której użytkownik ma uwierzytelniania lokalnego to oświadczenie udostępnia identyfikatora SID. Możesz użyć `onprem_sid` do autoryzacji w starszych aplikacji.|
| `pwd_exp`| int, sygnatura czasowa systemu UNIX | Wskazuje, kiedy wygasa hasło użytkownika. |
| `pwd_url`| String | Adres URL, których użytkownicy mogą być wysyłane do zresetowania swojego hasła. |
| `in_corp`| wartość logiczna | Sygnały, jeśli klient jest logowania się z siecią firmową. Jeśli nie, oświadczenia nie jest uwzględniona. |
| `nickname`| String | Dodatkową nazwę użytkownika, niezależnie od imię lub nazwisko.|
| `family_name` | String | Zawiera ostatni nazwę, nazwisko lub nazwę rodziny użytkownika, zgodnie z definicją w obiekcie użytkownika. |
| `given_name` | String | Zawiera pierwszy lub imię użytkownika, według stawki ustalonej obiektu user. |
| `upn` | String | Nazwa użytkownika. Może to być numer telefonu, adres e-mail lub niesformatowany ciąg. Można stosować tylko do wyświetlania i udostępnienie wskazówek nazwy użytkownika w scenariuszach ponowne uwierzytelnienie. |

#### <a name="the-amr-claim"></a>`amr` Oświadczeń

Tożsamości firmy Microsoft mogą uwierzytelniać się na różne sposoby, które mogą być istotne do aplikacji. `amr` Oświadczeń jest tablicą, który może zawierać wiele elementów, takich jak `["mfa", "rsa", "pwd"]`, do uwierzytelniania używane hasła i aplikacji wystawcy uwierzytelnienia.

| Wartość | Opis |
|-----|-------------|
| `pwd` | Uwierzytelnianie hasłem, użytkownika Microsoft hasła lub klucza tajnego klienta aplikacji. |
| `rsa` | Uwierzytelnianie zależała od dowód klucz RSA, na przykład za pomocą [aplikacji Microsoft Authenticator](https://aka.ms/AA2kvvu). W tym przypadku uwierzytelnianie zostało wykonanej przez JWT podpisanych przy użyciu usługi należące do X509 certyfikatu. |
| `otp` | Jednorazowy kod dostępu za pomocą wiadomości e-mail lub wiadomości SMS. |
| `fed` | Użyto asercję uwierzytelnianie federacyjne (na przykład JWT lub SAML). |
| `wia` | Zintegrowane uwierzytelnianie Windows |
| `mfa` | Użyto usługi Multi-Factor authentication. Jeśli jest obecny innych metod uwierzytelniania, również zostaną dołączone. |
| `ngcmfa` | Odpowiednikiem `mfa`, który jest używany do inicjowania obsługi niektórych typów zaawansowanych poświadczenia. |
| `wiaormfa`| Użytkownik użył Windows lub poświadczenia usługi MFA do uwierzytelniania. |
| `none` | Uwierzytelnianie nie zostało zrobione. |

## <a name="validating-tokens"></a>Sprawdzanie poprawności tokenów

Aby sprawdzić poprawność id_token ' access_token ', aplikację należy zweryfikować podpisu tokenu i oświadczenia. Aby sprawdzić poprawność tokenów dostępu, aplikacja powinna również umożliwić weryfikację w wystawcy, odbiorców i podpisywania tokenów. Muszą one być weryfikowany pod kątem wartości w dokumencie odnajdywania protokołu OpenID. Na przykład niezależny od dzierżawcy wersję dokumentu znajduje się w [ https://login.microsoftonline.com/common/.well-known/openid-configuration ](https://login.microsoftonline.com/common/.well-known/openid-configuration).

Oprogramowanie pośredniczące usługi Azure AD ma wbudowane możliwości sprawdzania poprawności tokenów dostępu i możesz przeglądać naszych [przykłady](https://docs.microsoft.com/azure/active-directory/active-directory-code-samples) można znaleźć w wybranym języku. Aby uzyskać więcej informacji na temat sposobu jawnie Weryfikacja tokenu JWT, zobacz [ręczne przykładowe sprawdzania poprawności tokenu JWT](https://github.com/Azure-Samples/active-directory-dotnet-webapi-manual-jwt-validation).

Firma Microsoft zapewnia biblioteki i przykłady kodu, które pokazują, jak i w łatwy sposób spełniaj walidacji tokenów. Podano następujące informacje dla tych, którzy chcą poznać bazowego procesu. Dostępne są również kilka bibliotek typu open-source innych firm dla walidacji tokenów JWT — istnieje co najmniej jedną z opcji prawie każdej platformy i języka tam. Aby uzyskać więcej informacji dotyczących bibliotek uwierzytelniania usługi Azure AD oraz przykładów kodu, zobacz [bibliotek uwierzytelniania v1.0](active-directory-authentication-libraries.md) i [bibliotek uwierzytelniania v2.0](reference-v2-libraries.md).

### <a name="validating-the-signature"></a>Weryfikowanie podpisu

Token JWT zawiera trzy segmenty, które są oddzielone `.` znaków. Pierwszy segment jest znany jako **nagłówka**, drugi jako **treści**, a trzeci jako **podpisu**. Segment podpisu może służyć do sprawdzania autentyczności tokenu, dzięki czemu mogą być zaufane przez aplikację.

Tokeny wystawione przez usługę Azure AD są podpisywane przy użyciu branżowych standardowa asymetrycznych algorytmów, takich jak RSA 256. Nagłówek tokenu JWT zawiera informacje o metodzie klucza i szyfrowania używany do podpisywania tokenu:

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk",
  "kid": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk"
}
```

`alg` Oświadczenia wskazuje algorytm, który został użyty do podpisywania tokenu, while `kid` oświadczenia wskazuje określonego klucza publicznego, który był używany do podpisywania tokenu.

W dowolnym danym momencie w czasie usługi Azure AD może podpisać id_token przy użyciu jednego zestawu pary kluczy publiczny prywatny. Usługa Azure AD obraca się możliwe zestaw kluczy w regularnych odstępach czasu, więc aplikacji mają być zapisywane automatycznie obsługiwać zmiany klucza. Uzasadnione częstotliwość, aby sprawdzić, czy są aktualizacje kluczy publicznych używane przez usługę Azure AD jest co 24 godziny.

Mogą nabyć podpisywania danych klucza, który należy zweryfikować podpisu, używając [OpenID Connect dokument metadanych](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document) znajdujących się na:

```
https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration
```

> [!TIP]
> Skorzystaj z tej [adresu URL](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) w przeglądarce!

Ten dokument metadanych:

* Jest obiektem JSON zawierającym kilka rodzajów przydatne informacje, takie jak lokalizacja różnych punktów końcowych wymaganych do wykonywania uwierzytelniania OpenID Connect.
* Obejmuje `jwks_uri`, co daje lokalizacji zestawu kluczy publicznych, używany do podpisywania tokenów. Dokument JSON znajdujący się w `jwks_uri` zawiera wszystkie informacje o kluczu publicznym używany w tej chwili określonego czasu. Twoja aplikacja może używać `kid` oświadczenia w nagłówku JWT, aby wybrać, które klucza publicznego, w tym dokumencie został użyty do podpisania dany token. Następnie można to zrobić Weryfikacja podpisu przy użyciu prawidłowy klucz publiczny i wskazany algorytmu.

> [!NOTE]
> Zwraca punkt końcowy w wersji 1.0, zarówno `x5t` i `kid` oświadczeń, natomiast punktu końcowego v2.0 odpowiada za pomocą tylko `kid` oświadczenia. Idąc dalej, firma Microsoft zaleca używanie `kid` oświadczeń można zweryfikować tokenu.

Weryfikacja podpisu wykracza poza zakres tego dokumentu — wiele bibliotek typu open source są dostępne, aby pomóc Ci w tym celu w razie potrzeby.  Jednak platformą Microsoft Identity ma jeden token podpisywania rozszerzenia ze standardami - niestandardowych kluczy podpisywania.  

Jeśli aplikacja ma niestandardowe klucze podpisywania w wyniku użycia [Mapowanie oświadczeń](active-directory-claims-mapping.md) funkcji, należy dołączyć `appid` zapytania parametr zawierający identyfikator aplikacji, aby uzyskać `jwks_uri` wskazujący aplikacji podpisywania kluczowe informacje, które powinno być używane do sprawdzania poprawności. Na przykład: `https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` zawiera `jwks_uri` z `https://login.microsoftonline.com/{tenant}/discovery/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e`.

### <a name="claims-based-authorization"></a>Autoryzacja oparta na oświadczeniach

Logika biznesowa aplikacji określają ten krok, niektóre typowe metody autoryzacji zostały przedstawione poniżej.

* Sprawdź `scp` lub `roles` oświadczenia sprawdzić, czy wszystkie zakresy obecne odpowiadają udostępnianych przez interfejs API i zezwolić na klientów, które można wykonać żądanej akcji.
* Upewnij się, może wywołać za pomocą interfejsu API klienta wywołującego `appid` oświadczenia.
* Sprawdzanie poprawności stanu uwierzytelniania przy użyciu klienta wywołującego `appidacr` — nie powinien być 0, jeśli klienci nie są dozwolone do wywołania interfejsu API.
* Zapoznaj się z listą przeszłości `nonce` oświadczeń zweryfikować token nie jest odtwarzany.
* Sprawdź, czy `tid` dopasowuje dzierżawy, który można wywoływać interfejs API.
* Użyj `acr` oświadczenia sprawdzić, użytkownik wykonał usługi MFA. Powinno to być wymuszane za pomocą [dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/conditional-access/overview).
* Jeśli żądanej `roles` lub `groups` oświadczenia w tokenie dostępu, sprawdź, czy użytkownik znajduje się w grupie może wykonać tej akcji.
  * Tokeny pobrany przy użyciu niejawnego przepływu, prawdopodobnie należy zbadać [programu Microsoft Graph](https://developer.microsoft.com/graph/) dla tych danych, ponieważ jest często zbyt duży, aby zmieścić ją w tokenie.

## <a name="user-and-application-tokens"></a>Tokeny użytkownika i aplikacji

Aplikacja może zostać wyświetlony tokenów w imieniu użytkownika (zwykle przepływ) lub bezpośrednio z aplikacji (za pośrednictwem [przepływ poświadczeń klienta](v1-oauth2-client-creds-grant-flow.md)). Tokeny te tylko do aplikacji wskazują, że to wywołanie pochodzi z aplikacji i nie ma użytkownika, jego kopię. Tokeny te są obsługiwane w większości takie same, z pewne różnice:

* Tokeny tylko do aplikacji nie będzie miał `scp` oświadczeń i zamiast tego może być `roles` oświadczenia. Jest to, gdzie mają być rejestrowane uprawnień aplikacji (w przeciwieństwie do delegowane uprawnienia). Aby uzyskać więcej informacji na temat uprawnień delegowanych i aplikacji, zobacz uprawnienia i zgody w [v1.0](v1-permissions-and-consent.md) i [v2.0](v2-permissions-and-consent.md).
* Wiele oświadczeń specyficzne dla człowieka, będą niedostępne, takich jak `name` lub `upn`.
* `sub` i `oid` oświadczenia będą takie same. 

## <a name="token-revocation"></a>Odwołanie tokenu

Odśwież tokeny mogą zostać unieważnione lub odwołać w dowolnym momencie i z różnych powodów. Te można podzielić na dwie główne kategorie: limity czasu i odwołania.

### <a name="token-timeouts"></a>Token przekroczeń limitu czasu

* MaxInactiveTime: Jeśli token odświeżania nie został użyty w czasie przez MaxInactiveTime, Token odświeżenia nie będzie już prawidłowy.
* MaxSessionAge: Jeśli ustawiono MaxAgeSessionMultiFactor lub MaxAgeSessionSingleFactor na coś innego niż domyślne (aż do odwołane) to ponowne uwierzytelnianie będą wymagane po upływie tego czasu w MaxAgeSession *.
* Przykłady:
  * Dzierżawa ma MaxInactiveTime przez pięć dni i użytkownik przeszedł na urlopie na tydzień, a więc w usłudze Azure AD nie widoczne nowe żądanie tokenu użytkownika w ciągu 7 dni. Następnym razem użytkownik zażąda nowego tokenu, znajdą one ich odświeżyć Token został odwołany, a ich musi ponownie wprowadzić swoje poświadczenia.
  * Poufnej aplikacji ma MaxAgeSessionSingleFactor jeden dzień. Jeśli użytkownik loguje się w poniedziałek i wtorek (po upływie 25 godzin), wymagane będzie ponowne uwierzytelnianie.

### <a name="revocation"></a>Odwołania

|   | Cookie opartego na hasłach | Token opartego na hasłach | Opartego na hasłach inne niż plik cookie | Token opartego na hasłach inne niż | Token poufne klienta |
|---|-----------------------|----------------------|---------------------------|--------------------------|---------------------------|
| Hasło wygasło | Pozostanie aktywny | Pozostanie aktywny | Pozostanie aktywny | Pozostanie aktywny | Pozostanie aktywny |
| Hasło zostało zmienione przez użytkownika | Odwołane | Odwołane | Pozostanie aktywny | Pozostanie aktywny | Pozostanie aktywny |
| Użytkownik wykona samoobsługowego resetowania HASEŁ | Odwołane | Odwołane | Pozostanie aktywny | Pozostanie aktywny | Pozostanie aktywny |
| Administrator operacji resetowania hasła | Odwołane | Odwołane | Pozostanie aktywny | Pozostanie aktywny | Pozostanie aktywny |
| Użytkownik odwołuje tokeny odświeżania [za pośrednictwem programu PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureadsignedinuserallrefreshtoken) | Odwołane | Odwołane | Odwołane | Odwołane | Odwołane |
| Administrator odwołuje wszystkie tokeny odświeżania dla dzierżawy [za pośrednictwem programu PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken) | Odwołane | Odwołane |Odwołane | Odwołane | Odwołane |
| [Wylogowania jednokrotnego](v1-protocols-openid-connect-code.md#single-sign-out) w sieci web | Odwołane | Pozostanie aktywny | Odwołane | Pozostanie aktywny | Pozostanie aktywny |

> [!NOTE]
> "Non-password na podstawie" nazwy logowania jest w jednym gdzie użytkownik nie wpisz hasło, aby pobrać go. Na przykład za pomocą usługi rozpoznawania twarzy Windows Hello, klucz FIDO lub numeru PIN.
>
> Istnieje znany problem za pomocą Windows głównej odświeżanie tokenu. Jeśli PRT uzyskuje się za pomocą hasła, a następnie użytkownik loguje się za pośrednictwem Witaj, nie ma to wpływu na inicjowanie PRT i zostanie odwołana, jeśli użytkownik zmieni swoje hasło.
>
> Tokeny odświeżania nie są unieważniane lub odwoływane, gdy jest używana do pobrania nowego tokenu dostępu i token odświeżania.  

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [ `id_tokens` w usłudze Azure AD](id-tokens.md).
* Dowiedz się więcej o uprawnienia i zgody w [v1.0](v1-permissions-and-consent.md) i [v2.0](v2-permissions-and-consent.md).
