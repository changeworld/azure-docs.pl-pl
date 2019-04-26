---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
origin.date: 11/30/2018
ms.date: 04/04/2019
ms.author: v-junlch
ms.openlocfilehash: f23d2b02bc2a23c5333a48a50532c03f3aa6a031
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60455658"
---
[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

W obszarze **Zarządzaj**, wybierz opcję **przepływy użytkownika** i kliknij przycisk +**nowy przepływ użytkownika**.

![Wybierz nowy przepływ użytkownika](./media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-user-flow.png)

Na **zalecane** zaznacz **Zaloguj się i logowanie**.

![Wybierz opcję logowania się i zaloguj się przepływ użytkownika](./media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-user-flow-type.png)

Wprowadź przepływ użytkownika **nazwa** odwołać się do aplikacji. Na przykład wprowadź wartość `SiUpIn`.

W obszarze **dostawców tożsamości** i sprawdź **rejestracja E-mail**. Opcjonalnie możesz również wybrać dostawców tożsamości społecznościowych, jeśli są już skonfigurowani.

W obszarze **uwierzytelnianie wieloskładnikowe**, wybierają **włączone** lub **wyłączone**.

![Wprowadź nazwę i wybierz pozycję rejestracja E-mail jako dostawcy tożsamości](./media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-name-identity-providers.png)

W obszarze **atrybutach i oświadczeniach użytkowników**, wybierz opcję **Pokaż więcej** aby zobaczyć pełną listę atrybutów i oświadczenia mogą wybierać.

W **zbieranie atrybut** kolumnę, wybierz atrybuty, mają być zbierane od użytkownika podczas rejestracji. Na przykład zaznacz pozycje **Kraj/Region**, **Nazwa wyświetlana** i **Kod pocztowy**.

W **zwracany oświadczenia** kolumnę, wybierz oświadczenia, o których mają być zwracane w tokenach autoryzacji wysyłanych z powrotem do aplikacji po pomyślnej rejestracji lub logowania. Na przykład wybierz pozycje **Nazwa wyświetlana**, **Dostawca tożsamości**, **Kod pocztowy**, **Użytkownik jest nowy** i **Identyfikator obiektu użytkownika**.

Kliknij przycisk **OK**.

![Wybierz niektóre atrybutach i oświadczeniach użytkowników, a następnie kliknij przycisk OK](./media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-sign-up-all-attributes.png)

Kliknij przycisk **Utwórz** dodać przepływ użytkownika. Przepływ użytkownika znajduje się w **B2C_1_SiUpIn**. Do nazwy jest dołączany prefiks **B2C_1_**.

Wybierz **uruchomić przepływ użytkownika**. Sprawdź ustawienia określone w tabeli, a następnie kliknij przycisk **uruchomić przepływ użytkownika**.

![Wybierz przebieg przepływu użytkownika](./media/active-directory-b2c-create-sign-in-sign-up-policy/run-user-flow-b2c-signup-signin.png)

| Ustawienie      | Wartość  |
| ------------ | ------ |
| **Aplikacja** | Aplikacja Contoso B2C |
| **Adres URL odpowiedzi** | `https://localhost:44316/` |

Zostanie otwarta nowa karta przeglądarki, na której możesz sprawdzić, czy rejestrowanie lub logowanie użytkownika przebiega tak, jak zostało skonfigurowane.

> [!NOTE]
> Zajmuje do minuty Tworzenie przepływu użytkownika i aktualizacje zostały wprowadzone.
>

