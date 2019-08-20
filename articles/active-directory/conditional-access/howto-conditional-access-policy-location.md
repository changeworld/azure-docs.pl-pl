---
title: Dostęp warunkowy — Blokuj dostęp według lokalizacji — Azure Active Directory
description: Tworzenie niestandardowych zasad dostępu warunkowego w celu blokowania dostępu do zasobów według lokalizacji IP
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: b31fc3aed0d412646d9924e87170dffcd5145409
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69576604"
---
# <a name="conditional-access-block-access-by-location"></a>Dostęp warunkowy: Blokuj dostęp według lokalizacji

Z warunkiem lokalizacji w polu dostęp warunkowy można kontrolować dostęp do aplikacji w chmurze na podstawie lokalizacji sieciowej użytkownika. Warunek lokalizacji jest często używany do blokowania dostępu z krajów, w których organizacja wie ruch, nie powinien pochodzić z.

## <a name="define-locations"></a>Definiuj lokalizacje

1. Zaloguj się do **Azure Portal** jako Administrator globalny, administrator zabezpieczeń lub administrator dostępu warunkowego.
1. Przejdź do **Azure Active Directory** > **dostęp warunkowy**.
1. Wybierz pozycję **Nowa lokalizacja**.
1. Nadaj lokalizacji nazwę.
1. Wybierz pozycję **zakresy adresów IP** , jeśli znasz określone zewnętrznie zakresy adresów IPv4, które składają się na tę lokalizację lub **kraje/regiony**.
   1. Podaj **zakresy adresów IP** lub wybierz **kraje/regiony** dla określonej lokalizacji.
      * W przypadku wybrania opcji kraje/regiony można opcjonalnie dodać obszary nieznane.
1. Wybierz pozycję **Zapisz**

Więcej informacji o warunku lokalizacji w dostępie warunkowym można znaleźć w artykule [co to jest warunek lokalizacji w Azure Active Directory dostęp warunkowy](location-condition.md)

## <a name="create-a-conditional-access-policy"></a>Tworzenie zasad dostępu warunkowego

1. Zaloguj się do **Azure Portal** jako Administrator globalny, administrator zabezpieczeń lub administrator dostępu warunkowego.
1. Przejdź do **Azure Active Directory** > **dostęp warunkowy**.
1. Wybierz pozycję **nowe zasady**.
1. Nadaj zasadom nazwę. Firma Microsoft zaleca, aby organizacje utworzyły znaczący Standard nazw swoich zasad.
1. W obszarze **przypisania**wybierz pozycję **Użytkownicy i grupy**
   1. Wobszarze Dołączanie wybierz pozycję **Wszyscy użytkownicy**.
   1. Wybierz pozycję **Done** (Gotowe).
1. W obszarze **aplikacje lub akcje** > w chmurze wybierz pozycję **wszystkie aplikacje w chmurze**, a następnie wybierz pozycję **gotowe**.
1. W obszarze**Lokalizacja** **warunków** > .
   1. Ustaw wartość **tak**
   1. **Dołącz** **wybrane lokalizacje**
   1. Wybierz zablokowaną lokalizację utworzoną dla swojej organizacji.
   1. Kliknij pozycjęgotowe > gotowe. > 
1. W obszarze**blok** **kontroli** > dostępu, a następnie wybierz pozycję **Wybierz**.
1. Potwierdź ustawienia i ustaw opcję **Włącz zasady** na **włączone**.
1. Wybierz pozycję **Utwórz** , aby utworzyć zasady.

## <a name="next-steps"></a>Następne kroki

[Wspólne zasady dostępu warunkowego](concept-conditional-access-policy-common.md)

[Symulowanie zachowania logowania za pomocą narzędzia What If dostępu warunkowego](troubleshoot-conditional-access-what-if.md)
