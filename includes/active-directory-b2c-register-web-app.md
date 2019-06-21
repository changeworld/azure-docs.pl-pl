---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/03/2016
ms.author: patricka
ms.openlocfilehash: 4d5c8373f2a8f02ccfbe51cb970ab08744950e7e
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203840"
---
[!INCLUDE [active-directory-b2c-portal-add-application](active-directory-b2c-portal-add-application.md)]

Aby zarejestrować swoją aplikację internetową, użyj ustawień określonych w tabeli.

![Przykład ustawień rejestracji dla nowej aplikacji internetowej](./media/active-directory-b2c-register-web-app/b2c-new-app-settings.png)

| Ustawienie      | Wartość przykładowa  | Opis                                        |
| ------------ | ------- | -------------------------------------------------- |
| **Nazwa** | Aplikacja Contoso B2C | Wprowadź **Nazwę**, która będzie opisywać aplikację użytkownikom. | 
| **Uwzględnij aplikację sieci web / interfejs API sieci web** | Tak | Wybierz pozycję **Tak** dla aplikacji internetowej. |
| **Zezwalaj na niejawny przepływ** | Tak | Wybierz pozycję **Tak**, jeśli Twoja aplikacja używa [logowania za pomocą uwierzytelniania OpenID Connect](../articles/active-directory-b2c/active-directory-b2c-reference-oidc.md) |
| **Adres URL odpowiedzi** | `https://localhost:44316` | Adresy URL odpowiedzi to punkty końcowe, w których usługa Azure AD B2C będzie zwracać wszystkie tokeny żądane przez Twoją aplikację. Wprowadź [odpowiedni](../articles/active-directory-b2c/active-directory-b2c-app-registration.md#choosing-a-web-app-or-api-reply-url) **adres URL odpowiedzi**. W tym przykładzie Twoja aplikacja jest lokalna i nasłuchuje na porcie 44316. |

Kliknij pozycję **Utwórz**, aby zarejestrować aplikację.

Nowo zarejestrowana aplikacja jest wyświetlana na liście aplikacji dla dzierżawy usługi B2C. Wybierz swoją aplikację internetową z listy. Zostanie wyświetlone okienko właściwości aplikacji internetowej.

![Właściwości aplikacji internetowej](./media/active-directory-b2c-register-web-app/b2c-web-app-properties.png)

Zanotuj globalnie unikatowy **Identyfikator klienta aplikacji**. Użyjesz tego identyfikatora w kodzie Twojej aplikacji.