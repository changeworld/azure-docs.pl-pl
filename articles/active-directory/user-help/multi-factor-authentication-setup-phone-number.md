---
title: Konfigurowanie urządzenia przenośnego jako metody weryfikacji dwuskładnikowej — Usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Dowiedz się, jak skonfigurować urządzenie przenośne jako metodę weryfikacji dwuskładnikowej.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: overview
ms.date: 08/12/2019
ms.author: curtand
ms.openlocfilehash: b0bfaa87c77ba9fff9f6605c1989e48ffbc3fb35
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77062510"
---
# <a name="set-up-a-mobile-device-as-your-two-factor-verification-method"></a>Konfigurowanie urządzenia przenośnego jako metody weryfikacji dwuskładnikowej

Możesz skonfigurować urządzenie mobilne tak, aby działało jako metoda weryfikacji dwuskładnikowej. Twój telefon komórkowy może odbierać sms-y z kodem weryfikacyjnym lub telefonem.

>[!Note]
> Jeśli opcja telefonu uwierzytelniania jest wyszarzona, istnieje możliwość, że organizacja nie zezwala na używanie numeru telefonu lub wiadomości tekstowej do weryfikacji. W takim przypadku musisz wybrać inną metodę lub skontaktować się z administratorem, aby uzyskać więcej pomocy.

## <a name="set-up-your-mobile-device-to-use-a-text-message-as-your-verification-method"></a>Skonfiguruj urządzenie mobilne tak, aby używało wiadomości tekstowej jako metody weryfikacji

1. Na stronie **Dodatkowa weryfikacja zabezpieczeń** wybierz **pozycję Telefon uwierzytelniający** z **kroku 1: Jak skontaktować się z Tobą,** wybrać kraj lub region z listy rozwijanej, a następnie wpisać numer telefonu urządzenia mobilnego.

2. Wybierz **pozycję Wyślij mi kod za pomocą wiadomości tekstowej** z obszaru **Metoda,** a następnie wybierz pozycję **Dalej**.

    ![Dodatkowa strona weryfikacji zabezpieczeń z telefonem uwierzytelnianiem i wiadomością tekstową](media/multi-factor-authentication-verification-methods/multi-factor-authentication-text-message.png)

3. Wpisz kod weryfikacyjny z wiadomości tekstowej wysłanej przez firmę Microsoft w **kroku 2: Wysłaliśmy wiadomość tekstową do** obszaru telefonu, a następnie wybierz pozycję **Zweryfikuj**.

    ![Dodatkowa strona weryfikacji zabezpieczeń z telefonem uwierzytelnianiem i wiadomością tekstową](media/multi-factor-authentication-verification-methods/multi-factor-authentication-text-message-test.png)

4. W **kroku 3: Kontynuuj korzystanie z istniejącego** obszaru aplikacji, skopiuj podane hasło aplikacji i wklej go w bezpiecznym miejscu.

    ![Obszar Hasła aplikacji na stronie Dodatkowa weryfikacja zabezpieczeń](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >Aby uzyskać informacje dotyczące używania hasła aplikacji ze starszymi [aplikacjami,](multi-factor-authentication-end-user-app-passwords.md)zobacz Zarządzanie hasłami aplikacji . Hasła aplikacji muszą być używane tylko wtedy, gdy nadal korzystasz ze starszych aplikacji, które nie obsługują weryfikacji dwuskładnikowej.

5. Wybierz pozycję **Done** (Gotowe).

## <a name="set-up-your-mobile-device-to-receive-a-phone-call"></a>Konfigurowanie urządzenia mobilnego do odbierania połączeń telefonicznych

1. Na stronie **Dodatkowa weryfikacja zabezpieczeń** wybierz **pozycję Telefon uwierzytelniający** z **kroku 1: Jak skontaktować się z Tobą,** wybrać kraj lub region z listy rozwijanej, a następnie wpisać numer telefonu urządzenia mobilnego.

2. Wybierz **pozycję Zadzwoń do mnie** z obszaru **Metoda,** a następnie wybierz pozycję **Dalej**.

    ![Dodatkowa strona weryfikacji zabezpieczeń z telefonem uwierzytelnianiem i rozmową telefoniczną](media/multi-factor-authentication-verification-methods/multi-factor-authentication-phone-call.png)

3. Otrzymasz telefon od firmy Microsoft z prośbą o naciśnięcie znaku funt (#) na urządzeniu przenośnym, aby zweryfikować twoją tożsamość.

    ![Testowanie określonego numeru telefonu](media/multi-factor-authentication-verification-methods/multi-factor-authentication-phone-call-test.png)

4. W **kroku 3: Kontynuuj korzystanie z istniejącego** obszaru aplikacji, skopiuj podane hasło aplikacji i wklej go w bezpiecznym miejscu.

    ![Obszar Hasła aplikacji na stronie Dodatkowa weryfikacja zabezpieczeń](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >Aby uzyskać informacje dotyczące używania hasła aplikacji ze starszymi [aplikacjami,](multi-factor-authentication-end-user-app-passwords.md)zobacz Zarządzanie hasłami aplikacji . Hasła aplikacji muszą być używane tylko wtedy, gdy nadal korzystasz ze starszych aplikacji, które nie obsługują weryfikacji dwuskładnikowej.

5. Wybierz pozycję **Done** (Gotowe).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu metody weryfikacji dwuskładnikowej możesz dodać dodatkowe metody, zarządzać ustawieniami i hasłami aplikacji, zalogować się lub uzyskać pomoc dotyczącą typowych problemów związanych z weryfikacją dwuskładnikową.

- [Zarządzanie ustawieniami metody weryfikacji dwuskładnikowej](multi-factor-authentication-end-user-manage-settings.md)

- [Zarządzanie hasłami aplikacji](multi-factor-authentication-end-user-app-passwords.md)

- [Logowanie przy użyciu weryfikacji dwuskładnikowej](multi-factor-authentication-end-user-signin.md)

- [Uzyskaj pomoc dotyczącą weryfikacji dwuskładnikowej](multi-factor-authentication-end-user-troubleshoot.md)
