---
title: Zarządzanie zagrożeniami dla zasobów i danych
titleSuffix: Azure AD B2C
description: Dowiedz się więcej o technikach wykrywania i ograniczania ryzyka ataków typu "odmowa usługi" i ataków na hasła w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a438363b054361420222804dffac7973470e82e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183605"
---
# <a name="manage-threats-to-resources-and-data-in-azure-active-directory-b2c"></a>Zarządzanie zagrożeniami dla zasobów i danych w usłudze Azure Active Directory B2C

Usługa Azure Active Directory B2C (Azure AD B2C) ma wbudowane funkcje, które mogą pomóc w ochronie przed zagrożeniami dla zasobów i danych. Zagrożenia te obejmują ataki typu "odmowa usługi" i ataki hasłem. Ataki typu "odmowa usługi" mogą sprawić, że zasoby będą niedostępne dla zamierzonych użytkowników. Ataki hasła prowadzą do nieautoryzowanego dostępu do zasobów.

## <a name="denial-of-service-attacks"></a>Ataki typu "odmowa usługi"

Usługa Azure AD B2C broni się przed atakami powodziowymi SYN przy użyciu pliku cookie SYN. Usługa Azure AD B2C chroni również przed atakami typu "odmowa usługi" przy użyciu limitów stawek i połączeń.

## <a name="password-attacks"></a>Ataki hasłami

Hasła, które są ustawiane przez użytkowników muszą być dość skomplikowane. Usługa Azure AD B2C ma techniki ograniczania ryzyka w miejscu ataków hasła. Łagodzenie obejmuje wykrywanie ataków na hasła brutalnej siły i ataków haseł słownikowych. Przy użyciu różnych sygnałów usługi Azure AD B2C analizuje integralność żądań. Usługa Azure AD B2C została zaprojektowana w celu inteligentnego odróżnienia zamierzonych użytkowników od hakerów i botnetów.

Usługa Azure AD B2C używa zaawansowanej strategii blokowania kont. Konta są zablokowane na podstawie adresu IP żądania i wprowadzonych haseł. Czas trwania blokady zwiększa się również w zależności od prawdopodobieństwa, że jest to atak. Po próbie hasła 10 razy bezpowodzenia (domyślny próg próby), występuje jednominutowa blokada. Następnym razem, gdy logowanie nie powiedzie się po odblokowaniu konta (to znaczy po automatycznym odblokowaniu konta przez usługę po upływie okresu blokady), następuje kolejna jednominutowa blokada i kontynuowana dla każdego nieudanego logowania. Wielokrotne wprowadzanie tego samego hasła nie jest liczone jako wielokrotne nieudane logowania.

Pierwsze 10 okresów blokady trwa minutę. Kolejne 10 okresów blokady jest nieco dłuższe i wydłuża się po każdym 10 okresach blokady. Licznik blokady resetuje się do zera po pomyślnym zalogowaniu się, gdy konto nie jest zablokowane. Okresy blokady mogą trwać do pięciu godzin.

## <a name="manage-password-protection-settings"></a>Zarządzanie ustawieniami ochrony hasłem

Aby zarządzać ustawieniami ochrony hasłem, w tym progiem blokady:

1. Logowanie się do [witryny Azure portal](https://portal.azure.com)
1. Użyj filtru **subskrypcja Katalog +** w górnym menu, aby wybrać katalog zawierający dzierżawę usługi Azure AD B2C.
1. W menu po lewej stronie wybierz pozycję **Azure AD B2C**. Możesz też wybrać **wszystkie usługi** i wyszukać i wybrać pozycję Azure **AD B2C**.
1. W obszarze **Zabezpieczenia**wybierz pozycję **Metody uwierzytelniania (Podgląd)**( wybierz pozycję **Ochrona hasłem**.
1. Wprowadź żądane ustawienia ochrony hasłem, a następnie wybierz pozycję **Zapisz**.

    ![Strona ochrony hasłem portalu Azure w ustawieniach usługi Azure AD](./media/threat-management/portal-02-password-protection.png)
    <br />*Ustawienie progu blokady na 5 w **ustawieniach ochrony hasłem** *.

## <a name="view-locked-out-accounts"></a>Wyświetlanie zablokowanych kont

Aby uzyskać informacje o zablokowanych kontach, można sprawdzić [raport aktywności logowania](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md)usługi Active Directory . W obszarze **Stan**wybierz pozycję **Błąd**. Nieudane próby logowania z **kodem błędu** `50053` logowania wskazują zablokowane konto:

![Sekcja raportu logowania usługi Azure AD z zablokowanym kontem](./media/threat-management/portal-01-locked-account.png)

Aby dowiedzieć się więcej o wyświetlaniu raportu aktywności logowania w usłudze Azure Active Directory, zobacz [Kody błędów raportu aktywności logowania](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md).
