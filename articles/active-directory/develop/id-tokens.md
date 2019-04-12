---
title: Azure odwołania do tokenu identyfikator Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać id_tokens wyemitowane przez punkty końcowe usługi Azure AD w wersji 1.0 i 2.0.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/08/2019
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms:custom: fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: dad1b686152101cbeaddc29974a2a1c5f13a7712
ms.sourcegitcommit: 41015688dc94593fd9662a7f0ba0e72f044915d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/11/2019
ms.locfileid: "59500549"
---
# <a name="id-tokens"></a>Tokeny identyfikatorów

`id_tokens` są wysyłane do aplikacji klienckiej jako część [OpenID Connect](v1-protocols-openid-connect-code.md) przepływu. Mogą być wysyłane wzdłuż krawędzi lub zamiast token dostępu i są używane przez klienta do uwierzytelnienia użytkownika.

## <a name="using-the-idtoken"></a>Za pomocą id_token

Tokeny identyfikator powinien być używany do weryfikacji, czy jest użytkownik, który podaje się i Uzyskaj przydatne informacje dodatkowe o nich — nie należy jej używać do autoryzacji zamiast [token dostępu](access-tokens.md). Oświadczenia, które zapewnia może służyć do środowiska użytkownika w aplikacji, obsługi kluczy bazy danych i zapewniania dostępu do aplikacji klienckiej.

## <a name="claims-in-an-idtoken"></a>Oświadczenia w id_token

`id_tokens` dla programu Microsoft identity są [tokenów Jwt](https://tools.ietf.org/html/rfc7519), co oznacza składają się z części nagłówka, ładunek i podpis. Nagłówek i podpis służy do zweryfikowania autentyczności token, a ładunek zawiera informacje o użytkowniku żądany przez klienta. Jeśli nie zaznaczono inaczej wszystkie oświadczenia wymienione w tym miejscu są wyświetlane w tokenów w wersji 1.0 i 2.0.

### <a name="v10"></a>Wersja 1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q
```

Wyświetl ten token przykładowe 1.0 w [jwt.ms](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q).

### <a name="v20"></a>Wersja 2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTE4ODA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjMzMzgwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0=.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw
```

Wyświetl ten token przykładowe v2.0 w [jwt.ms](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTE4ODA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjMzMzgwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw).

### <a name="header-claims"></a>Nagłówek oświadczeń

|Claim | Format | Opis |
|-----|--------|-------------|
|`typ` | Ciąg — zawsze "JWT" | Wskazuje, czy token jest token JWT.|
|`alg` | String | Określa algorytm, który był używany do podpisywania tokenu. Przykład: "RS256" |
|`kid` | String | Odcisk palca klucza publicznego używany do podpisywania tego tokenu. Emitowane zarówno w wersji 1.0, jak i w wersji 2.0 `id_tokens`. |
|`x5t` | String | Takie same (w użyciu i wartości) jako `kid`. Jest to jednak starsze oświadczenie emitowane tylko w wersji 1.0 `id_tokens` do celów zgodności. |

### <a name="payload-claims"></a>Ładunek oświadczeń

Ta lista zawiera oświadczenia, które są w większości id_tokens domyślnie (z wyjątkiem sytuacji, gdy podane).  Jednak Twoja aplikacja może używać [opcjonalnych oświadczeń](active-directory-optional-claims.md) dodatkowe oświadczenia w id_token żądania.  Te można dostosować w zakresie `groups` oświadczenie do informacji na temat nazw użytkowników.

|Claim | Format | Opis |
|-----|--------|-------------|
|`aud` |  Ciąg, identyfikator URI Identyfikatora aplikacji | Identyfikuje zamierzonym odbiorcą tokenu. W `id_tokens`, odbiorców jest identyfikator aplikacji, przypisany do aplikacji w witrynie Azure portal. Aplikację należy sprawdzić tę wartość i odrzucenie tokenu, jeśli wartość nie jest zgodny. |
|`iss` |  Ciąg identyfikatora URI usługi STS | Określa usługę tokenu zabezpieczającego (STS), który tworzy i zwraca token i dzierżawy usługi Azure AD, w którym użytkownik został uwierzytelniony. Jeśli token został wystawiony przez punkt końcowy v2.0, identyfikator URI zakończy się w `/v2.0`.  Identyfikator GUID, który wskazuje, czy użytkownik jest użytkownikiem odbiorcy z konta Microsoft jest `9188040d-6c67-4c5b-b112-36a304b66dad`. Aplikację należy użyć część stanowiącą identyfikator GUID oświadczenia, aby ograniczyć zestaw dzierżawców, którzy mogą zalogować do aplikacji, jeśli ma to zastosowanie. |
|`iat` |  int, sygnatura czasowa systemu UNIX | "Wydany w" wskazuje, kiedy wystąpił uwierzytelniania dla tego tokenu.  |
|`idp`|Ciąg, zazwyczaj identyfikator URI usługi STS | Rejestruje dostawcę tożsamości, który uwierzytelnił podmiot tokenu. Ta wartość jest taka sama jak wartość oświadczenia wystawcy, chyba że konto użytkownika nie znajduje się w tej samej dzierżawie, jako wystawca - goście, na przykład. Jeśli oświadczenia nie jest obecny, oznacza to, że wartość `iss` mogą być używane zamiast tego.  Dla osobistych kont, które są używane w kontekście organizacji (na przykład konta osobistego zaproszeni do dzierżawy usługi Azure AD) `idp` oświadczeń może być "live.com" lub identyfikator URI usługi STS dzierżawy konta Microsoft zawierającego `9188040d-6c67-4c5b-b112-36a304b66dad`. |
|`nbf` |  int, sygnatura czasowa systemu UNIX | Oświadczenie "nbf" (nie przed) identyfikuje czas, przed którym tokenu JWT nie muszą zostać zaakceptowane do przetworzenia.|
|`exp` |  int, sygnatura czasowa systemu UNIX | Oświadczenie "exp" (czas wygaśnięcia) identyfikuje czasu wygaśnięcia na lub po JWT nie może być który zaakceptowane do przetworzenia.  Należy zauważyć, że zasób może odrzucić token przed upływem wskazanego terminu, jak również — Jeśli na przykład konieczne jest wprowadzenie zmiany w uwierzytelnianiu lub token odwołania został wykryty. |
| `c_hash`| String |Skrót kod znajduje się w tokeny Identyfikatora, tylko wtedy, gdy wystawiono tokenu Identyfikacyjnego z kodu autoryzacji OAuth 2.0. Może służyć do weryfikowania autentyczności kodu autoryzacji. Aby uzyskać szczegółowe informacje dotyczące przeprowadzania tej weryfikacji, zobacz [specyfikacją z OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html). |
|`at_hash`| String |Skrót tokenu znajduje się w identyfikatorze dostęp tokenów, tylko, gdy tokenu Identyfikacyjnego wystawione przy użyciu tokenu dostępu OAuth 2.0. Może służyć do weryfikowania autentyczności tokenu dostępu. Aby uzyskać szczegółowe informacje dotyczące przeprowadzania tej weryfikacji, zobacz [specyfikacją z OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html). |
|`aio` | Nieprzezroczysty ciąg | Oświadczenie wewnętrzny używany przez usługę Azure AD do rejestrowania danych do ponownego wykorzystania tokenu. Mają być ignorowane.|
|`preferred_username` | String | Nazwa głównej reprezentuje użytkownika. Może on być adres e-mail, numer telefonu lub ogólny nazwy użytkownika bez określonego formatu. Jego wartość jest modyfikowalna i mogą ulec zmianie wraz z upływem czasu. Ponieważ jest ona modyfikowalna, ta wartość nie należy do podejmowania decyzji dotyczących autoryzacji. `profile` Zakres jest wymagany w celu odbierania tego oświadczenia.|
|`email` | String | `email` Oświadczenia ma domyślnie dla konta gościa, które mają adres e-mail.  Aplikacja może żądać oświadczenie adresu e-mail dla zarządzanych użytkowników (te, z tą samą dzierżawą jako zasób) przy użyciu `email` [opcjonalnego roszczenia](active-directory-optional-claims.md).  Dla punktu końcowego v2.0 aplikacji mogą również żądać `email` zakresu OpenID Connect — nie trzeba używać zarówno opcjonalnego roszczenia oraz zakres, aby uzyskać oświadczenia.  Oświadczenie adresu e-mail obsługuje tylko adresy poczty z informacji o profilu użytkownika. |
|`name` | String | `name` Oświadczeń zawiera zrozumiałą wartość, która identyfikuje podmiotu tokenu. Wartość nie musi być unikatowy, jest ona modyfikowalna i został zaprojektowany tak, ma być używany tylko w celach wyświetlania. `profile` Zakres jest wymagany w celu odbierania tego oświadczenia. |
|`nonce`| String | Identyfikator jednorazowy odpowiada parametrowi uwzględnione w oryginalnym / autoryzować żądania przez dostawcę tożsamości. Jeśli nie jest zgodny, aplikacja powinna odrzucenie tokena. |
|`oid` | Ciąg identyfikatora GUID | Niemodyfikowalny identyfikator obiektu w Microsoft systemu tożsamości, w tym przypadku konta użytkownika. Ten identyfikator unikatowo identyfikuje użytkownika w aplikacjach — dwóch różnych aplikacji, rejestrowanie w ten sam użytkownik otrzyma taką samą wartość w `oid` oświadczenia. Program Microsoft Graph zwróci ten identyfikator jako `id` właściwość dla danego konta użytkownika. Ponieważ `oid` wielu aplikacjom do skorelowania użytkowników, `profile` zakres jest wymagany w celu odbierania tego oświadczenia. Należy pamiętać, że jeden użytkownik istnieje w wielu dzierżawach, użytkownik będzie zawierać identyfikator inny obiekt, w każdej dzierżawy — są traktowane jako różne konta, nawet jeśli użytkownik loguje się do każdego konta przy użyciu tych samych poświadczeń. |
|`roles`| Tablica ciągów | Zbiór ról, które zostały przypisane do użytkownika, który jest logowania. |
|`rh` | Nieprzezroczysty ciąg |Oświadczenie wewnętrzne używane przez platformę Azure w celu ponownego zweryfikowania tokenów. Mają być ignorowane. |
|`sub` | Ciąg identyfikatora GUID | Podmiot zabezpieczeń o tym, które token określa informacje, takie jak użytkownika aplikacji. Ta wartość jest niezmienny i nie może być ponownie przypisywany ani ponownie. Temat jest identyfikatorem pairwise — jest on unikatowy dla identyfikatora dla określonej aplikacji. W związku z tym jeśli jeden użytkownik zaloguje się do dwóch różnych aplikacji przy użyciu dwóch identyfikatorów innego klienta, aplikacji, które otrzyma dwóch różnych wartości oświadczenia podmiotu. To może być lub może nie być wskazane w zależności od wymagań dotyczących architektury i ochrony prywatności. |
|`tid` | Ciąg identyfikatora GUID | Identyfikator GUID, który reprezentuje dzierżawy usługi Azure AD, do której należy użytkownik. Dla kont służbowych identyfikator GUID jest identyfikator dzierżawy niezmienne organizacji, do której należy użytkownik. Dla osobistych kont, wartość jest `9188040d-6c67-4c5b-b112-36a304b66dad`. `profile` Zakres jest wymagany w celu odbierania tego oświadczenia. |
|`unique_name` | String | Udostępnia zrozumiałą wartość identyfikującą podmiot tokenu. Ta wartość nie musi być unikatowa w ramach dzierżawy i należy używać tylko w celach wyświetlania. Pojawiły się tylko w wersji 1.0 `id_tokens`. |
|`uti` | Nieprzezroczysty ciąg | Oświadczenie wewnętrzne używane przez platformę Azure w celu ponownego zweryfikowania tokenów. Mają być ignorowane. |
|`ver` | Ciąg, w wersji 1.0 lub 2.0 | Wskazuje wersję id_token. |

## <a name="validating-an-idtoken"></a>Sprawdzanie poprawności elementu id_token

Sprawdzanie poprawności `id_token` jest bardzo podobny do pierwszego kroku [sprawdzania poprawności tokenu dostępu](access-tokens.md#validating-tokens) — klienta należy sprawdzić, czy poprawny wystawca ma odesłał tokenu i nie została zmieniona z. Ponieważ `id_tokens` są zawsze token JWT, wiele bibliotek istnieją, aby sprawdzić te tokeny — firma Microsoft zaleca, możesz użyć jednej z tych zamiast wykonywania samodzielnie.

Aby ręcznie sprawdzić poprawność tokenu, się ze szczegółami kroki [sprawdzania poprawności tokenu dostępu](access-tokens.md#validating-tokens). Po upewnieniu się, podpis w tokenie, następujące oświadczeń powinny być weryfikowane w id_token (te mogą również po weryfikacji tokenu biblioteki):

* Sygnatury czasowe: `iat`, `nbf`, i `exp` sygnatury czasowe powinny wszystkie dzielą się przed lub po bieżącej godzinie, zgodnie z potrzebami. 
* Odbiorcy: `aud` oświadczenia powinny odpowiadać identyfikator aplikacji dla aplikacji.
* Identyfikator jednorazowy: `nonce` oświadczenia w ładunku musi być zgodna z przekazanego do parametru jednorazowy punktu końcowego / authorize podczas wstępnego żądania.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [tokenów dostępu usługi Azure AD](access-tokens.md)
* Dostosowywanie oświadczeń przy użyciu id_token [opcjonalnych oświadczeń](active-directory-optional-claims.md).