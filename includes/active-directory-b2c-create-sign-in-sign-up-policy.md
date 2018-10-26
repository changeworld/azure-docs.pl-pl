---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/03/2016
ms.author: patricka
ms.openlocfilehash: 3485cc96ad1d9ab8f89facf33687a7ab2be43b1e
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2018
ms.locfileid: "50132841"
---
[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

W sekcji ustawień dotyczącej zasad wybierz pozycję **Zasady logowania lub tworzenia konta** i kliknij pozycję **+ Dodaj**.

![Wybierz zasady rejestracji lub logowania i kliknij przycisk Dodaj](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-policy.png)

Wprowadź **Nazwę** zasad, do której będzie odwoływać się Twoja aplikacja. Na przykład wprowadź wartość `SiUpIn`.

Wybierz pozycję **Dostawcy tożsamości** i zaznacz pozycję **Tworzenie konta za pomocą adresu e-mail**. Opcjonalnie możesz również wybrać dostawców tożsamości społecznościowych, jeśli są już skonfigurowani. Kliknij przycisk **OK**.

![Wybierz rejestrację za pomocą adresu e-mail, a następnie kliknij przycisk OK](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-identity-providers.png)

Wybierz pozycję **Atrybuty tworzenia konta**. Wybierz atrybuty, które mają być zbierane od użytkownika podczas rejestracji. Na przykład zaznacz pozycje **Kraj/Region**, **Nazwa wyświetlana** i **Kod pocztowy**. Kliknij przycisk **OK**.

![Wybierz część atrybutów, a następnie kliknij przycisk OK](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-sign-up-attributes.png)

Wybierz pozycję **Oświadczenia aplikacji**. Wybierz oświadczenia, które mają być zwracane w tokenach autoryzacji wysyłanych z powrotem do Twojej aplikacji po pomyślnej rejestracji lub zalogowaniu. Na przykład wybierz pozycje **Nazwa wyświetlana**, **Dostawca tożsamości**, **Kod pocztowy**, **Użytkownik jest nowy** i **Identyfikator obiektu użytkownika**.

![Wybierz część oświadczeń aplikacji i kliknij przycisk OK](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-application-claims.png)

Kliknij przycisk **Utwórz**, aby dodać zasady. Zasada jest wyświetlana jako **B2C_1_SiUpIn**. Do nazwy jest dołączany prefiks **B2C_1_**.

Otwórz zasady, wybierając pozycję **B2C_1_SiUpIn**. Sprawdź ustawienia określone w tabeli, a następnie kliknij pozycję **Uruchom teraz**.

![Wybierz zasady, a następnie uruchom je](media/active-directory-b2c-create-sign-in-sign-up-policy/run-b2c-signup-signin-policy.png)

| Ustawienie      | Wartość  |
| ------------ | ------ |
| **Aplikacje** | Aplikacja Contoso B2C |
| **Wybierz adres URL odpowiedzi** | `https://localhost:44316/` |

Zostanie otwarta nowa karta przeglądarki, na której możesz sprawdzić, czy rejestrowanie lub logowanie użytkownika przebiega tak, jak zostało skonfigurowane.

> [!NOTE]
> Utworzenie zasad i zastosowanie aktualizacji zajmuje do minuty.
>