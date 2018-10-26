---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/03/2016
ms.author: patricka
ms.openlocfilehash: bff2543ec48c66c10db697650def0077e3de28be
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133540"
---
Aby włączyć precyzyjne resetowanie haseł w aplikacji, należy użyć **resetowania hasła** zasad. Pamiętaj, że opcja resetowania haseł na poziomie całej dzierżawy została określona [tutaj](../articles/active-directory-b2c/active-directory-b2c-reference-sspr.md). Te zasady opisują procesy, które klienci uzyskują podczas resetowania hasła oraz zawartość tokenów, które aplikacja otrzyma po pomyślnym ukończeniu.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

W sekcji ustawień dotyczącej zasad wybierz pozycję **Zasady resetowania hasła** i kliknij pozycję **+ Dodaj**.

![Wybierz zasady rejestracji lub logowania i kliknij przycisk Dodaj](media/active-directory-b2c-create-password-reset-policy/add-b2c-password-reset-policy.png)

Wprowadź **Nazwę** zasad, do której będzie odwoływać się Twoja aplikacja. Na przykład wprowadź wartość `SSPR`.

Wybierz pozycję **Dostawcy tożsamości** i zaznacz pozycję **Resetuj hasło przy użyciu adresu e-mail**. Kliknij przycisk **OK**.

![Wybierz resetowanie hasła przy użyciu adresu e-mail jako dostawcy tożsamości, a następnie kliknij przycisk OK](media/active-directory-b2c-create-password-reset-policy/add-b2c-password-reset-identity-providers.png)

Wybierz pozycję **Oświadczenia aplikacji**. Wybierz oświadczenia, które mają być zwracane w tokenach autoryzacji odsyłanych do Twojej aplikacji po pomyślnym zresetowaniu hasła. Na przykład wybierz pozycję **Identyfikator obiektu użytkownika**.

![Wybierz część oświadczeń aplikacji i kliknij przycisk OK](media/active-directory-b2c-create-password-reset-policy/add-b2c-password-reset-application-claims.png)

Kliknij przycisk **Utwórz**, aby dodać zasady. Zasady są wyświetlane jako **B2C_1_SSPR**. Do nazwy jest dołączany prefiks **B2C_1_**.

Otwórz zasady, wybierając pozycję **B2C_1_SSPR**. Sprawdź ustawienia określone w tabeli, a następnie kliknij pozycję **Uruchom teraz**.

![Wybierz zasady, a następnie uruchom je](media/active-directory-b2c-create-password-reset-policy/run-b2c-password-reset-policy.png)

| Ustawienie      | Wartość  |
| ------------ | ------ |
| **Aplikacje** | Aplikacja Contoso B2C |
| **Wybierz adres URL odpowiedzi** | `https://localhost:44316/` |

Zostanie otwarta nowa karta przeglądarki i możesz sprawdzić działanie resetowania hasła użytkownika w Twojej aplikacji.

> [!NOTE]
> Utworzenie zasad i zastosowanie aktualizacji zajmuje do minuty.
>
