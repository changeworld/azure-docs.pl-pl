---
title: Odwołanie do tokenu identyfikatora platformy tożsamości firmy Microsoft | Dokumenty firmy Microsoft
description: Dowiedz się, jak używać id_tokens emitowanych przez punkty końcowe platformy Azure AD w wersji 1.0 i platformy tożsamości firmy Microsoft (w wersji 2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms:custom: fasttrack-edit
ms.openlocfilehash: 1efd027edb85cabcfdc2a170771ef19182b5c9f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77160954"
---
# <a name="microsoft-identity-platform-id-tokens"></a>Tokeny identyfikatorów platformy tożsamości firmy Microsoft

`id_tokens`są wysyłane do aplikacji klienckiej w ramach przepływu [OpenID Connect.](v2-protocols-oidc.md) Mogą być wysyłane razem lub zamiast tokenu dostępu i są używane przez klienta do uwierzytelniania użytkownika.

## <a name="using-the-id_token"></a>Korzystanie z id_token

Tokeny identyfikatorów powinny być używane do sprawdzania, czy użytkownik jest tym, kim twierdzi, że są i uzyskać dodatkowe przydatne informacje na ich temat - nie powinny być używane do autoryzacji zamiast [tokenu dostępu.](access-tokens.md) Oświadczenia, które zapewnia może służyć do środowiska użytkownika wewnątrz aplikacji, jako klucze w bazie danych i zapewnienie dostępu do aplikacji klienckiej.  Podczas tworzenia kluczy dla `idp` bazy danych, nie powinny być używane, ponieważ bałagany scenariuszy gościa.  Kluczowanie powinno odbywać się samodzielnie `sub` (co `tid` jest zawsze unikatowe), z używanym do routingu, jeśli zajdzie taka potrzeba.  Jeśli chcesz udostępniać dane `oid` + `sub` + `tid` w usługach, będzie działać, `oid`ponieważ wiele usług wszystkie otrzymują takie same .

## <a name="claims-in-an-id_token"></a>Roszczenia w id_token

`id_tokens`dla tożsamości firmy Microsoft są [JWTs](https://tools.ietf.org/html/rfc7519), co oznacza, że składają się one z nagłówka, ładunku i części podpisu. Można użyć nagłówka i podpisu, aby zweryfikować autentyczność tokenu, podczas gdy ładunek zawiera informacje o użytkowniku żądanym przez klienta. Z wyjątkiem przypadków, w których wspomniano, wszystkie oświadczenia wymienione w tym miejscu pojawiają się zarówno w wersji 1.0 i v2.0 tokenów.

### <a name="v10"></a>Wersja 1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q
```

Wyświetl ten przykładowy token w wersji 1.0 w [jwt.ms](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q).

### <a name="v20"></a>Wersja 2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw
```

Wyświetl ten przykładowy token w wersji 2.0 w [jwt.ms](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw).

### <a name="header-claims"></a>Oświadczenia nagłówka

|Claim | Format | Opis |
|-----|--------|-------------|
|`typ` | Ciąg - zawsze "JWT" | Wskazuje, że token jest JWT.|
|`alg` | Ciąg | Wskazuje algorytm, który został użyty do podpisania tokenu. Przykład: "RS256" |
|`kid` | Ciąg | Odcisk palca dla klucza publicznego używanego do podpisywania tego tokenu. Emitowane zarówno w wersji 1.0, `id_tokens`jak i v2.0 . |
|`x5t` | Ciąg | To samo (w użyciu `kid`i wartości) jak . Jednak jest to starsze oświadczenie emitowane tylko w `id_tokens` wersji 1.0 dla celów zgodności. |

### <a name="payload-claims"></a>Oświadczenia o ładunku

Ta lista zawiera oświadczenia, które są w większości id_tokens domyślnie (z wyjątkiem przypadków, gdy zaznaczono).  Jednak aplikacja może używać [opcjonalnych oświadczeń,](active-directory-optional-claims.md) aby zażądać dodatkowych oświadczeń w id_token.  Mogą one wahać się `groups` od oświadczenia do informacji o nazwie użytkownika.

|Claim | Format | Opis |
|-----|--------|-------------|
|`aud` |  Ciąg, identyfikator URI identyfikatora aplikacji | Identyfikuje zamierzonego odbiorcę tokenu. W `id_tokens`obszarze odbiorcy jest identyfikator aplikacji przypisany do aplikacji w witrynie Azure portal. Aplikacja powinna zweryfikować tę wartość i odrzucić token, jeśli wartość nie jest zgodna. |
|`iss` |  Ciąg, identyfikator URI sts | Identyfikuje usługę tokenu zabezpieczającego (STS), która konstruuje i zwraca token, oraz dzierżawę usługi Azure AD, w której użytkownik został uwierzytelniony. Jeśli token został wystawiony przez punkt końcowy w wersji 2.0, identyfikator URI zakończy się na `/v2.0`.  Identyfikator GUID wskazujący, że użytkownik jest użytkownikiem `9188040d-6c67-4c5b-b112-36a304b66dad`konsumenckim z konta Microsoft, to . Aplikacja powinna używać części guid oświadczenia, aby ograniczyć zestaw dzierżaw, którzy mogą zalogować się do aplikacji, jeśli ma to zastosowanie. |
|`iat` |  int, sygnatura czasowa UNIX | "Wystawione w" wskazuje, kiedy wystąpił uwierzytelnianie dla tego tokenu.  |
|`idp`|Ciąg, zwykle identyfikator URI STS | Rejestruje dostawcę tożsamości, który uwierzytelnił podmiot tokenu. Wartość ta jest identyczna z wartością roszczenia Emitenta, chyba że konto użytkownika nie znajduje się w tym samym najemcy co emitent - na przykład goście. Jeśli oświadczenie nie jest obecny, oznacza to, że wartość `iss` może być używana zamiast.  W przypadku kont osobistych używanych w kontekście organizacyjnym (na przykład konta `idp` osobistego zaproszonego do dzierżawy usługi Azure AD) oświadczenie może `9188040d-6c67-4c5b-b112-36a304b66dad`być "live.com" lub identyfikator URI STS zawierający dzierżawę konta Microsoft. |
|`nbf` |  int, sygnatura czasowa UNIX | Oświadczenie "nbf" (nie wcześniej) określa czas, przed którym JWT NIE MOŻE być dopuszczony do przetwarzania.|
|`exp` |  int, sygnatura czasowa UNIX | Oświadczenie "exp" (czas wygaśnięcia) określa czas wygaśnięcia lub po którym JWT NIE MOŻE być akceptowany do przetworzenia.  Należy pamiętać, że zasób może również odrzucić token przed tym czasem — jeśli na przykład wymagana jest zmiana uwierzytelniania lub wykryto odwołanie tokenu. |
| `c_hash`| Ciąg |Skrót kodu jest zawarty w tokenach identyfikatorów tylko wtedy, gdy token identyfikatora jest wydawany z kodem autoryzacji OAuth 2.0. Może służyć do sprawdzania poprawności autentyczności kodu autoryzacji. Aby uzyskać szczegółowe informacje na temat wykonywania tej weryfikacji, zobacz [specyfikację OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html). |
|`at_hash`| Ciąg |Skrót tokenu dostępu jest uwzględniony w tokenach identyfikatorów tylko wtedy, gdy token identyfikatora jest wystawiany z tokenem dostępu OAuth 2.0. Może służyć do sprawdzania poprawności autentyczności tokenu dostępu. Aby uzyskać szczegółowe informacje na temat wykonywania tej weryfikacji, zobacz [specyfikację OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html). |
|`aio` | Nieprzezroczysty ciąg | Oświadczenie wewnętrzne używane przez usługę Azure AD do rejestrowania danych do ponownego użycia tokenu. Powinny być ignorowane.|
|`preferred_username` | Ciąg | Podstawowa nazwa użytkownika, która reprezentuje użytkownika. Może to być adres e-mail, numer telefonu lub ogólna nazwa użytkownika bez określonego formatu. Jego wartość jest zmienna i może się zmieniać w czasie. Ponieważ jest modyfikowalna, wartość ta nie może być używana do podejmowania decyzji dotyczących autoryzacji. Zakres `profile` jest wymagany do otrzymania tego roszczenia.|
|`email` | Ciąg | Oświadczenie `email` jest domyślnie obecne dla kont gości, które mają adres e-mail.  Aplikacja może zażądać oświadczenia e-mail dla użytkowników zarządzanych (tych `email` z tej samej dzierżawy co zasób) przy użyciu [opcjonalnego oświadczenia.](active-directory-optional-claims.md)  W punkcie końcowym w wersji 2.0 `email` aplikacja może również zażądać zakresu OpenID Connect — nie trzeba żądać zarówno opcjonalnego oświadczenia, jak i zakresu, aby uzyskać oświadczenie.  Oświadczenie e-mail obsługuje tylko adresowalną pocztę z informacji o profilu użytkownika. |
|`name` | Ciąg | Oświadczenie `name` zapewnia wartość czytelną dla człowieka, która identyfikuje temat tokenu. Wartość nie jest gwarantowana jako unikatowa, jest zmienna i jest przeznaczona do użytku tylko do celów wyświetlania. Zakres `profile` jest wymagany do otrzymania tego roszczenia. |
|`nonce`| Ciąg | Nonce pasuje do parametru zawartego w oryginalnym /authorize żądania do IDP. Jeśli nie jest zgodny, aplikacja powinna odrzucić token. |
|`oid` | Ciąg, identyfikator GUID | Niezmienny identyfikator obiektu w systemie tożsamości firmy Microsoft, w tym przypadku konta użytkownika. Ten identyfikator jednoznacznie identyfikuje użytkownika w różnych aplikacjach — dwie różne aplikacje `oid` logujące się w tym samym użytkowniku otrzymają tę samą wartość w reklamacji. Program Microsoft Graph zwróci ten `id` identyfikator jako właściwość dla danego konta użytkownika. Ponieważ `oid` umożliwia wielu aplikacjom skorelowanie użytkowników, `profile` zakres jest wymagany do odbierania tego oświadczenia. Należy zauważyć, że jeśli jeden użytkownik istnieje w wielu dzierżaw, użytkownik będzie zawierać inny identyfikator obiektu w każdej dzierżawie — są one uważane za różne konta, nawet jeśli użytkownik loguje się do każdego konta z tymi samymi poświadczeniami. Oświadczenie `oid` jest identyfikatorem GUID i nie można go ponownie używać. |
|`roles`| Tablica ciągów | Zestaw ról, które zostały przypisane do użytkownika, który jest zalogowany. |
|`rh` | Nieprzezroczysty ciąg |Oświadczenie wewnętrzne używane przez platformę Azure do ponownego potwierdzania tokenów. Powinny być ignorowane. |
|`sub` | Ciąg, identyfikator GUID | Podmiot zabezpieczeń, o którym token potwierdza informacje, takie jak użytkownik aplikacji. Ta wartość jest niezmienna i nie można jej ponownie przypisać ani ponownie. Temat jest identyfikatorem pairwise - jest unikatowy dla określonego identyfikatora aplikacji. Jeśli pojedynczy użytkownik zaloguje się do dwóch różnych aplikacji przy użyciu dwóch różnych identyfikatorów klienta, te aplikacje otrzymają dwie różne wartości dla oświadczenia podmiotu. To może lub nie może być pożądane w zależności od architektury i wymagań dotyczących prywatności. |
|`tid` | Ciąg, identyfikator GUID | Identyfikator GUID reprezentujący dzierżawę usługi Azure AD, z których pochodzi użytkownik. W przypadku kont służbowych identyfikator GUID jest niezmiennym identyfikatorem dzierżawy organizacji, do której należy użytkownik. W przypadku kont osobistych wartość to `9188040d-6c67-4c5b-b112-36a304b66dad`. Zakres `profile` jest wymagany do otrzymania tego roszczenia. |
|`unique_name` | Ciąg | Udostępnia zrozumiałą wartość identyfikującą podmiot tokenu. Ta wartość jest unikatowa w danym momencie, ale ponieważ wiadomości e-mail i inne identyfikatory mogą być ponownie użyte, ta wartość może pojawić się ponownie na innych kontach i dlatego powinna być używana tylko do celów wyświetlania. Wydawane tylko w wersji 1.0 `id_tokens`. |
|`uti` | Nieprzezroczysty ciąg | Oświadczenie wewnętrzne używane przez platformę Azure do ponownego potwierdzania tokenów. Powinny być ignorowane. |
|`ver` | Struna, 1,0 lub 2,0 | Wskazuje wersję id_token. |


> [!NOTE]
> V1 i v2 id_token mają różnice w ilości informacji, które będą nosić, jak widać z powyższych przykładów. Wersja zasadniczo określa punkt końcowy platformy Azure AD, z którego został wydany. [Implementacja usługi Azure AD Oauth ewoluowała](https://docs.microsoft.com/azure/active-directory/develop/about-microsoft-identity-platform) przez lata. Obecnie mamy dwa różne punkty końcowe oAuth dla aplikacji AzureAD. Można użyć dowolnego z nowych punktów końcowych, które są klasyfikowane jako v2 lub stary, który jest uważany za v1. Punkty końcowe Oauth dla obu z nich są różne. Punkt końcowy V2 jest nowszym punktem, w którym próbujemy przeprowadzić migrację wszystkich funkcji punktu końcowego w wersji 1 i zalecamy nowym deweloperom korzystanie z punktu końcowego w wersji 2. 
> - Wersja 1: punkty końcowe usługi Azure Active Directory:`https://login.microsoftonline.com/common/oauth2/authorize`
> - Wersja 2: Punkty końcowe platformy microsoft identity:`https://login.microsoftonline.com/common/oauth2/v2.0/authorize`

## <a name="validating-an-id_token"></a>Sprawdzanie poprawności id_token

Sprawdzanie poprawności `id_token` jest podobny do pierwszego kroku [sprawdzania poprawności tokenu dostępu](access-tokens.md#validating-tokens) — klient powinien sprawdzić, czy poprawny wystawca odesłał token i że nie został zmodyfikowany. Ponieważ `id_tokens` są zawsze JWT, istnieje wiele bibliotek do sprawdzania poprawności tych tokenów — zaleca się użycie jednego z nich, a nie robi to samodzielnie.

Aby ręcznie sprawdzić poprawność tokenu, zobacz szczegóły kroków [podczas sprawdzania poprawności tokenu dostępu](access-tokens.md#validating-tokens). Po sprawdzeniu poprawności podpisu na tokenie następujące oświadczenia powinny zostać zweryfikowane w id_token (mogą one również zostać wykonane przez bibliotekę sprawdzania poprawności tokenu):

* Sygnatury `iat`czasowe: , `nbf`, i `exp` sygnatury czasowe powinny spaść przed lub po bieżącym czasie, odpowiednio. 
* Grupa odbiorców: `aud` roszczenie powinno być zgodne z identyfikatorem aplikacji dla aplikacji.
* Nonce: `nonce` oświadczenie w ładunku musi odpowiadać parametr nonce przekazywane do /authorize punktu końcowego podczas początkowego żądania.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [tokenach dostępu](access-tokens.md)
* Dostosuj oświadczenia w id_token za pomocą [opcjonalnych oświadczeń](active-directory-optional-claims.md).
