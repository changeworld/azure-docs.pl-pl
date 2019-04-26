---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
origin.date: 11/30/2018
ms.date: 04/04/2019
ms.author: v-junlch
ms.openlocfilehash: 17c0213d63879687e9c6d5f8dca06b9113c44af8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60456904"
---
Jeśli chcesz włączyć tylko rejestracji w swojej aplikacji, możesz użyć **rejestracji** przepływu użytkownika. Ten przepływ użytkownika opisują procesy, które klienci przechodzą przez podczas tworzenia konta i zawartość tokenów, które aplikacja otrzyma po pomyślnej rejestracji.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

W obszarze **Zarządzaj**, wybierz opcję **przepływy użytkownika**.

Kliknij pozycję +**nowy przepływ użytkownika** w górnej części bloku.

W obszarze **wybierz typ przepływu użytkownika**, wybierz opcję **wszystkich**, a następnie wybierz wersję **Zarejestruj** chcesz użyć.

**Nazwa** Określa nazwę przepływu Utwórz konto użytkownika używane przez aplikację. Na przykład wprowadź wartość **SiUp**.

W obszarze **dostawców tożsamości**, wybierz opcję **rejestracja E-mail**. Opcjonalnie możesz również wybrać dostawców tożsamości społecznościowych, jeśli są już skonfigurowani.

W obszarze **atrybutach i oświadczeniach użytkowników**, kliknij przycisk **Pokaż więcej**.

W **zbieranie atrybut** kolumnę, wybierz atrybuty, które mają być zbierane od użytkownika podczas rejestracji. Na przykład wybierz pozycje **Kraj/region**, **Nazwa wyświetlana** i **Kod pocztowy**.

W **zwracany oświadczenia** kolumnę, wybierz oświadczenia, które mają być zwracane w tokenach wysyłanych z powrotem do aplikacji po pomyślnej rejestracji. Na przykład wybierz pozycje **Nazwa wyświetlana**, **Dostawca tożsamości**, **Kod pocztowy**, **Użytkownik jest nowy** i **Identyfikator obiektu użytkownika**.

Kliknij przycisk **OK**.

Kliknij pozycję **Utwórz**. Utworzony przepływ użytkownika jest wyświetlany jako **B2C_1_SiUp** ( **B2C\_1\_**  fragment jest automatycznie dodawany).

Kliknij przycisk **uruchomić przepływ użytkownika**.

Wybierz **aplikacja Contoso B2C** w **aplikacji** listy rozwijanej i `https://localhost:44321/` w **adres URL odpowiedzi** listy rozwijanej.

Kliknij przycisk **uruchomić przepływ użytkownika**. Zostanie otwarta nowa karta przeglądarki i możesz zapoznać się z procesem rejestracji w Twojej aplikacji przez użytkownika.

> [!NOTE]
> Zajmuje do minuty Tworzenie przepływu użytkownika i aktualizacje zostały wprowadzone.
>

