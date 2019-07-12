---
title: Zarządzanie zagrożeniami do zasobów i danych w usłudze Azure Active Directory B2C
description: Poznaj techniki wykrywania i łagodzenia skutków ataków typu "odmowa usługi" i złamania hasła w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 7232917df6018c9c8afc7e7edd3730a277b193f4
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798259"
---
# <a name="manage-threats-to-resources-and-data-in-azure-active-directory-b2c"></a>Zarządzanie zagrożeniami do zasobów i danych w usłudze Azure Active Directory B2C

Usługa Azure Active Directory (Azure AD) B2C ma wbudowane funkcje, które mogą pomóc w ochronie przed zagrożeniami dla zasobów i danych. Zagrożenia te obejmują ataków typu "odmowa usługi" i złamania hasła. Ataki typu "odmowa usługi" wprowadzać zasobów niedostępny oprogramowanie wyświetla odpowiednich użytkowników. Złamania hasła prowadzić do nieautoryzowanego dostępu do zasobów.

## <a name="denial-of-service-attacks"></a>Ataki typu "odmowa usługi"

Usługa Azure AD B2C systemów przed atakami powódź SYN SYN pliki cookie. Usługa Azure AD B2C chroni także przed atakami typu "odmowa usługi" przy użyciu limitów szybkości i połączeń.

## <a name="password-attacks"></a>Złamania hasła

Hasła, które są ustawiane przez użytkowników muszą być zrealizowane złożone. Usługa Azure AD B2C ma ograniczenie technik w celu złamania hasła. Środki zaradcze obejmuje wykrywanie ataków siłowych hasła i złamania hasła słownika. Za pomocą różnych sygnały, usługi Azure AD B2C analizuje integralność żądań. Usługa Azure AD B2C jest przeznaczony do inteligentnie odróżnić oprogramowanie wyświetla odpowiednich użytkowników przed hakerami i botnetami.

Usługa Azure AD B2C używa zaawansowanej strategii blokowania kont. Konta są zablokowane oparte na adresie IP żądania i wprowadzonym. Czas trwania blokady zwiększa także zależności od stopnia prawdopodobieństwa ataku. Po hasła podjęto 10 prób (domyślny próg prób), co minutę zablokowaniem. Podczas następnego logowania zakończy się niepowodzeniem po konto odblokowane (po konta zostały automatycznie odblokowana przez usługę po wygaśnięciu czasu blokady), innego blokady jednominutowy występuje i jest powtarzany przez każde logowanie powiodło się. Wielokrotnego wprowadzania tego samego hasła nie są liczone jako wiele nieudanych logowań.

Pierwszy okresy 10 blokady są długie minuty. Okresy 10 następnych blokady są nieco dłużej i zwiększenie czasu trwania po okresach co 10 blokady. Resetuje licznik blokady do zera po pomyślnym zalogowaniu, jeśli konto nie jest zablokowane. Okresy blokady może trwać do pięciu godzin.

## <a name="manage-password-protection-settings"></a>Zarządzanie ustawieniami ochrony hasłem

Do zarządzania ustawieniami ochrony haseł, w tym próg blokady:

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
1. Wybierz **katalog i Subskrypcja** filtru w menu w prawym górnym rogu portalu, a następnie wybierz dzierżawę usługi Azure AD B2C.
1. Wybierz **usługi Azure Active Directory** w menu po lewej stronie (lub wybierz **wszystkich usług** w lewej górnej części portalu następnie wyszukaj i wybierz pozycję *usługi Azure Active Directory*).
1. W obszarze **zabezpieczeń**, wybierz opcję **metod uwierzytelniania**, a następnie wybierz **ochrony hasłem**.
1. Wprowadź odpowiednie hasło ustawienia ochrony, a następnie wybierz **Zapisz**.

    ![Hasło ochrony stronę witryny Azure portal w obszarze Ustawienia usługi Azure AD](media/active-directory-b2c-reference-threat-management/portal-02-password-protection.png)
    <br />*Ustawienie wartości progowej blokady do 5 w **ochrony hasłem** ustawienia*.

## <a name="view-locked-out-accounts"></a>Wyświetl zablokowanego konta

Aby uzyskać informacje o kontach zablokowanego, można sprawdzić usługi Active Directory [raport aktywności logowania](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md). W obszarze **stan**, wybierz opcję **błąd**. Nieudane logowania za pomocą **logowania kod błędu:** z `50053` wskazania zablokowanych konta:

![Części usługi Azure AD w raporcie logowania przedstawiający zablokowanego konta](media/active-directory-b2c-reference-threat-management/portal-01-locked-account.png)

Aby dowiedzieć się więcej o wyświetlaniu raportów aktywności logowania w usłudze Azure Active Directory, zobacz [logowania w raporcie kody błędów działań związanych](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md).
