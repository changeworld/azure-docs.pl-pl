---
title: Dostęp warunkowy — blokowanie dostępu według lokalizacji — usługa Azure Active Directory
description: Tworzenie niestandardowej zasady dostępu warunkowego w celu blokowania dostępu do zasobów według lokalizacji IP
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34b29ceadaaf85e69d1214039fa1b563ed21a77d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295182"
---
# <a name="conditional-access-block-access-by-location"></a>Dostęp warunkowy: blokowanie dostępu według lokalizacji

Dzięki warunkowi lokalizacji w usłudze Dostęp warunkowy można kontrolować dostęp do aplikacji w chmurze na podstawie lokalizacji sieciowej użytkownika. Warunek lokalizacji jest powszechnie używany do blokowania dostępu z krajów, z których organizacja wie, że ruch nie powinien pochodzić.

## <a name="define-locations"></a>Definiowanie lokalizacji

1. Zaloguj się do **witryny Azure portal** jako administrator globalny, administrator zabezpieczeń lub administrator dostępu warunkowego.
1. Przejdź do > **lokalizacji Nazwanych nazwanych****dostępu warunkowego****usługi** >  **Azure Active Directory** > .
1. Wybierz **pozycję Nowa lokalizacja**.
1. Nadaj swojej lokalizacji nazwę.
1. Wybierz **zakresy adresów IP,** jeśli znasz określone zasięgi adresów IPv4 dostępne z zewnątrz, które składają się na tę lokalizację lub **kraje/regiony.**
   1. Podaj **zakresy adresów IP** lub wybierz **kraje/regiony** dla określonej lokalizacji.
      * Jeśli wybierzesz kraje/regiony, możesz opcjonalnie uwzględnić nieznane obszary.
1. Wybierz **pozycję Zapisz**

Więcej informacji na temat stanu lokalizacji w programie Dostęp warunkowy można znaleźć w artykule: [Jaki jest warunek lokalizacji w usłudze Azure Active Directory Dostęp warunkowy](location-condition.md)

## <a name="create-a-conditional-access-policy"></a>Tworzenie zasad dostępu warunkowego

1. Zaloguj się do **witryny Azure portal** jako administrator globalny, administrator zabezpieczeń lub administrator dostępu warunkowego.
1. Przejdź do **usługi Azure Active Directory** > **Security** > **Conditional Access**.
1. Wybierz **pozycję Nowa zasada**.
1. Nadaj polityce nazwę. Zaleca się, aby organizacje tworzyły znaczący standard nazw swoich zasad.
1. W obszarze **Przydziały**wybierz **pozycję Użytkownicy i grupy**
   1. W **obszarze Uwzględnij**wybierz pozycję **Wszyscy użytkownicy**.
   1. Wybierz pozycję **Done** (Gotowe).
1. W obszarze **Aplikacje lub akcje** > w chmurze**Uwzględnij**wybierz pozycję **Wszystkie aplikacje w chmurze**i wybierz pozycję **Gotowe**.
1. W **warunkach** > **Lokalizacja**.
   1. Ustaw **pozycję Konfiguruj** na **Tak**
   1. **Uwzględnij** wybrane **lokalizacje**
   1. Wybierz zablokowaną lokalizację utworzoną dla organizacji.
   1. Kliknij **pozycję Wybierz** > **gotowe** > **gotowe**.
1. W **obszarze Warunki** > **aplikacje klienckie (Wersja zapoznawcza)** ustaw **pozycję Konfiguruj** na **Tak**i wybierz opcję **Gotowe**.
1. W obszarze **Formanty** > dostępu**blokuj**i wybierz pozycję **Wybierz**.
1. Potwierdź ustawienia i ustaw **włącz zasadę** **na Włącz**.
1. Wybierz **pozycję Utwórz,** aby utworzyć, aby włączyć zasady.

## <a name="next-steps"></a>Następne kroki

[Wspólne zasady dostępu warunkowego](concept-conditional-access-policy-common.md)

[Określanie wpływu przy użyciu trybu tylko dla dostępu warunkowego](howto-conditional-access-report-only.md)

[Symulowanie zachowania logowania za pomocą narzędzia Co jeśli dostęp warunkowy](troubleshoot-conditional-access-what-if.md)
