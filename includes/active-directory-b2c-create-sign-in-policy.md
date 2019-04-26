---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
origin.date: 11/30/2018
ms.date: 04/04/2019
ms.author: v-junlch
ms.openlocfilehash: 0ab34d6234db9c13ffe82ccd0e8580217085f631
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60456033"
---
Jeśli chcesz tylko Włącz logowanie w swojej aplikacji, możesz użyć **logowania** przepływu użytkownika. Ten przepływ użytkownika opisują procesy, które klienci uzyskują podczas logowania i zawartość tokenów, które aplikacja otrzyma w pomyślnych logowaniach.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]
W obszarze **Zarządzaj**, wybierz opcję **przepływy użytkownika**.

Kliknij pozycję +**nowy przepływ użytkownika** w górnej części bloku.

W obszarze **wybierz typ przepływu użytkownika**, wybierz opcję **wszystkich**, a następnie wybierz wersję **Zaloguj się** chcesz użyć.

**Nazwa** Określa nazwę przepływu logowania użytkownika, które są używane przez aplikację. Na przykład wprowadź wartość **SiIn**.

W obszarze **dostawców tożsamości**, wybierz opcję. Możesz wybrać dostawców tożsamości społecznościowej, również, jeśli już skonfigurowany. Kliknij przycisk **OK**.

W obszarze **oświadczeń aplikacji**, kliknij przycisk **Pokaż więcej**.

W **zwracany oświadczenia** kolumnę, wybierz oświadczenia, które mają być zwracane w tokenach wysyłanych z powrotem do aplikacji po pomyślnym środowiska logowania. Na przykład wybierz pozycje **Nazwa wyświetlana**, **Dostawca tożsamości**, **Kod pocztowy** i **Identyfikator obiektu użytkownika**. Kliknij przycisk **OK**.

Kliknij pozycję **Utwórz**. Należy zauważyć, że właśnie utworzony przepływ użytkownika jest wyświetlany jako **B2C_1_SiIn** ( **B2C\_1\_**  fragment jest automatycznie dodawany).

Kliknij przycisk **uruchomić przepływ użytkownika**.

Wybierz **aplikacja Contoso B2C** w **aplikacji** listy rozwijanej i `https://localhost:44321/` w **adres URL odpowiedzi** listy rozwijanej.

Kliknij przycisk **uruchomić przepływ użytkownika**. Zostanie otwarta nowa karta przeglądarki i możesz zapoznać się z procesem logowania się użytkownika do Twojej aplikacji.

> [!NOTE]
> Zajmuje do minuty Tworzenie przepływu użytkownika i aktualizacje zostały wprowadzone.
>

