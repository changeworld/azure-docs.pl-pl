---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
origin.date: 11/30/2018
ms.date: 04/04/2019
ms.author: v-junlch
ms.openlocfilehash: 78abb190dccd27c5bf70dfe12f978e1118601815
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60456945"
---
Aby włączyć precyzyjne resetowanie haseł w aplikacji, należy użyć **resetowania hasła** przepływu użytkownika. Należy pamiętać, że osobą odpowiedzialną za opcja resetowania haseł dla dzierżawy jest określona [tutaj](../articles/active-directory-b2c/active-directory-b2c-reference-sspr.md). Ten przepływ użytkownika opisują procesy, które klienci uzyskują podczas resetowania hasła oraz zawartość tokenów, które aplikacja otrzyma po pomyślnym ukończeniu.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

W obszarze **Zarządzaj**, wybierz opcję **przepływy użytkownika** i kliknij przycisk +**nowy przepływ użytkownika**.

![Wybierz nowy przepływ użytkownika](./media/active-directory-b2c-create-password-reset-policy/add-b2c-new-user-flow.png)

Na **zalecane** zaznacz **resetowania hasła**.

Wprowadź przepływ użytkownika **nazwa** odwołać się do aplikacji. Na przykład wprowadź wartość `SSPR`.

W obszarze **dostawców tożsamości**, sprawdź **Resetuj hasło przy użyciu adresu e-mail**.

![Wprowadź nazwę i wybierz opcję Resetuj hasło przy użyciu adresu e-mail jako dostawcy tożsamości](./media/active-directory-b2c-create-password-reset-policy/add-b2c-password-reset-identity-providers.png)

W obszarze **oświadczeń aplikacji**, kliknij przycisk **Pokaż więcej** i wybierz oświadczenia, które mają być zwracane w tokenach autoryzacji wysyłanych z powrotem do aplikacji po pomyślnym zresetowaniu hasła środowisko. Na przykład wybierz pozycję **Identyfikator obiektu użytkownika**.

Kliknij przycisk **OK**.

![Wybierz część oświadczeń aplikacji i kliknij przycisk OK](./media/active-directory-b2c-create-password-reset-policy/add-b2c-password-reset-application-claims.png)

Kliknij przycisk **Utwórz** dodać przepływ użytkownika. Przepływ użytkownika znajduje się w **B2C_1_SSPR**. Do nazwy jest dołączany prefiks **B2C_1_**.

Kliknij przycisk **uruchomić przepływ użytkownika**. Sprawdź ustawienia określone w tabeli, a następnie kliknij przycisk **uruchomić przepływ użytkownika**.

![Wybierz przepływ użytkownika i uruchom go](./media/active-directory-b2c-create-password-reset-policy/add-b2c-sspr-run-user-flow.png)

| Ustawienie      | Wartość  |
| ------------ | ------ |
| **Aplikacja** | Aplikacja Contoso B2C |
| **Wybierz adres URL odpowiedzi** | `https://localhost:44316/` |

Zostanie otwarta nowa karta przeglądarki i możesz sprawdzić działanie resetowania hasła użytkownika w Twojej aplikacji.

> [!NOTE]
> Utworzenie zasad i zastosowanie aktualizacji zajmuje do minuty.
>

