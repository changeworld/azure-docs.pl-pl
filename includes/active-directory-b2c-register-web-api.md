---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/03/2016
ms.author: patricka
ms.openlocfilehash: 9d5af21fb3b329623b14cb8742d9ec9c5d1bad46
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133022"
---
[!INCLUDE [active-directory-b2c-portal-add-application](active-directory-b2c-portal-add-application.md)]

Aby zarejestrować swój internetowy interfejs API, użyj ustawień określonych w tabeli.

![Przykład ustawień rejestracji dla nowego internetowego interfejsu API](./media/active-directory-b2c-register-web-api/b2c-new-web-api-settings.png)

| Ustawienie      | Wartość przykładowa  | Opis                                        |
| ------------ | ------- | -------------------------------------------------- |
| **Nazwa** | Interfejs API Contoso B2C | Wprowadź wartość **Nazwa** dla aplikacji, która opisuje Twój interfejs API na potrzeby użytkowników. | 
| **Uwzględnij aplikację internetową/internetowy interfejs API** | Yes | Wybierz pozycję **Tak** dla internetowego interfejsu API. |
| **Zezwalaj na niejawny przepływ** | Yes | Wybierz pozycję **Tak**, jeśli Twoja aplikacja używa [logowania za pomocą uwierzytelniania OpenID Connect](../articles/active-directory-b2c/active-directory-b2c-reference-oidc.md) |
| **Adres URL odpowiedzi** | `https://localhost:44316/` | Adresy URL odpowiedzi to punkty końcowe, w których usługa Azure AD B2C będzie zwracać wszystkie tokeny żądane przez Twoją aplikację. Wprowadź [odpowiedni](../articles/active-directory-b2c/active-directory-b2c-app-registration.md#choosing-a-web-app-or-api-reply-url) **adres URL odpowiedzi**. W tym przykładzie Twój internetowy interfejs API jest lokalny i nasłuchuje na porcie 44316. |
| **Identyfikator URI identyfikatora aplikacji** | api | Identyfikator URI identyfikatora aplikacji to identyfikator używany na potrzeby internetowego interfejsu API. Zostanie wygenerowany pełny identyfikator URI łącznie z domeną. |

Kliknij pozycję **Utwórz**, aby zarejestrować aplikację.

Nowo zarejestrowana aplikacja jest wyświetlana na liście aplikacji dla dzierżawy usługi B2C. Wybierz swój internetowy interfejs API z listy. Zostanie wyświetlone okienko właściwości interfejsu API.

![Właściwości internetowego interfejsu API](./media/active-directory-b2c-register-web-api/b2c-web-api-properties.png)

Zanotuj globalnie unikatowy **Identyfikator klienta aplikacji**. Użyjesz tego identyfikatora w kodzie Twojej aplikacji.