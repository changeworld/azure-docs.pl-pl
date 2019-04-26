---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
origin.date: 11/30/2018
ms.date: 04/04/2019
ms.author: v-junlch
ms.openlocfilehash: 0d9f0a24d84bd18bdf1fac84c744cc34a7d89ab3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60456042"
---
Jeśli chcesz włączyć edytowania profilu w swojej aplikacji, możesz użyć **edytowania profilu** przepływu użytkownika. Ten przepływ użytkownika opisują procesy, które klienci uzyskują podczas edytowania profilu i zawartość tokenów, które aplikacja otrzyma po pomyślnym ukończeniu.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

W obszarze **Zarządzaj**, wybierz opcję **przepływy użytkownika** i kliknij przycisk +**nowy przepływ użytkownika**.

![Wybierz nowy przepływ użytkownika](./media/active-directory-b2c-create-profile-editing-policy/add-b2c-new-user-flow.png)

Na **zalecane** zaznacz **edytowania profilu**.

Wprowadź przepływ użytkownika **nazwa** odwołać się do aplikacji. Na przykład wprowadź wartość `SiPe`.

W obszarze **dostawców tożsamości**, sprawdź **logowanie za pomocą konta lokalnego**. Opcjonalnie możesz również wybrać dostawców tożsamości społecznościowych, jeśli są już skonfigurowani.

![Wybierz pozycję Logowanie za pomocą konta lokalnego jako dostawcę tożsamości, a następnie kliknij przycisk OK](./media/active-directory-b2c-create-profile-editing-policy/add-b2c-profile-editing-identity-providers.png)

W obszarze **atrybutów użytkownika**, kliknij przycisk **Pokaż więcej**. W **zbieranie atrybut** kolumnę, wybierz atrybuty, użytkownik może wyświetlać i edytować w swoim profilu. Na przykład zaznacz pozycje **Kraj/Region**, **Nazwa wyświetlana** i **Kod pocztowy**.

W **zwracany oświadczenia** kolumnę, wybierz oświadczenia, które mają być zwracane w tokenach autoryzacji wysyłanych z powrotem do aplikacji po pomyślnym edytowaniu profilu. Na przykład wybierz pozycje **Nazwa wyświetlana**, **Kod pocztowy**.

Kliknij przycisk **OK**.

![Wybierz część oświadczeń aplikacji i kliknij przycisk OK](./media/active-directory-b2c-create-profile-editing-policy/add-b2c-user-attributes.png)

Kliknij przycisk **Utwórz** dodać przepływ użytkownika. Przepływ użytkownika znajduje się w **B2C_1_SiPe**. Do nazwy jest dołączany prefiks **B2C_1_**.

Wybierz **uruchomić przepływ użytkownika**. Sprawdź ustawienia określone w tabeli, a następnie kliknij przycisk **uruchomić przepływ użytkownika**.

![Wybierz przepływ użytkownika i uruchom go](./media/active-directory-b2c-create-profile-editing-policy/add-b2c-profile-editing-run-user-flow.png)

| Ustawienie      | Wartość  |
| ------------ | ------ |
| **Aplikacja** | Aplikacja Contoso B2C |
| **Adres URL odpowiedzi** | `https://localhost:44316/` |

Zostanie otwarta nowa karta przeglądarki, na której możesz sprawdzić, czy edytowanie profilu użytkownika działa tak, jak zostało skonfigurowane.

> [!NOTE]
> Zajmuje do minuty Tworzenie przepływu użytkownika i aktualizacje zostały wprowadzone.
>

