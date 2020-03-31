---
title: Co to jest strona Dodatkowa weryfikacja? - Usługa Azure AD
description: Jak uzyskać informacje na temat dodatkowej weryfikacji zabezpieczeń w celu weryfikacji dwuskładnikowej.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: overview
ms.date: 01/15/2020
ms.author: curtand
ms.openlocfilehash: 5a7f0e10b23bf1a541fe83c3112962c38f7e1331
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77062561"
---
# <a name="what-is-the-additional-verification-page"></a>Co to jest strona Dodatkowa weryfikacja?

Dotarła do Ciebie wiadomość e-mail z działu IT lub przełożonego z informacją, że organizacja dodała dodatkową weryfikację zabezpieczeń do Twojego konta. Co to oznacza? Oznacza to, że Twoja organizacja podejmuje dodatkowe kroki, aby upewnić się, że jesteś osobą, za którą podajesz się podczas logowania. Ta dodatkowa weryfikacja, znana również jako weryfikacja dwuskładniowa, odbywa się za pomocą kombinacji nazwy użytkownika, hasła oraz urządzenia mobilnego lub telefonu.

Weryfikacja dwuskładniowa jest bezpieczniejsza niż tylko hasło, ponieważ opiera się na dwóch formach uwierzytelniania: czymś, co wiesz i czymś, co masz przy sobie. Coś, co znasz, to hasło. Coś, co masz, to telefon lub urządzenie, które często masz przy sobie. Weryfikacja dwuskładniowa może pomóc powstrzymać złośliwych hakerów przed podszywaniem się pod Ciebie, ponieważ nawet jeśli mają twoje hasło, szanse są takie, że nie mają też Twojego urządzenia.

>[!Important]
>Ten artykuł jest przeznaczony dla użytkowników próbujących użyć weryfikacji dwuskładnikowej z kontem służbowym alain@contoso.com(np. ). Jeśli jesteś administratorem szukającym informacji o tym, jak włączyć weryfikację dwuskładnikową dla pracowników lub innych użytkowników, zapoznaj się z [dokumentacją uwierzytelniania usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/).

## <a name="who-decides-if-you-use-this-feature"></a>Kto decyduje o możliwości użycia tej funkcji?

W zależności od typu konta o konieczności korzystania z weryfikacji dwuskładnikowej może zdecydować Twoja organizacja lub ta decyzja może zostać przeniesiona na użytkownika.

- **Konto służbowe.** Jeśli używasz konta służbowego (na przykład alain@contoso.com), to Twoja organizacja decyduje, czy musisz korzystać z weryfikacji dwuskładnikowej i konkretnych metod weryfikacji. Ponieważ organizacja zdecydowała, że należy użyć tej funkcji, nie ma możliwości jej indywidualnego wyłączenia.

- **Osobiste konto Microsoft.** Weryfikację dwuskładnikowa można skonfigurować dla osobistych kont Microsoft (na przykład alain@outlook.com). Jeśli masz problemy z weryfikacją dwuskładnikową i osobistym kontem Microsoft, zobacz [Włączanie lub wyłączanie weryfikacji dwuskładnikowej dla konta Microsoft.](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off) Ponieważ wybierzesz, czy chcesz korzystać z tej funkcji, możesz ją włączać i wyłączać w dowolnym momencie.

    >[!Note]
    >Jeśli masz problemy z weryfikacją dwuskładnikową i jednym danielle@outlook.comz osobistych kont Microsoft (na przykład), możesz wypróbować sugestie dotyczące [sposobu korzystania z weryfikacji dwuetapowej z kontem Microsoft.](https://support.microsoft.com/help/12408/microsoft-account-how-to-use-two-step-verification)

## <a name="access-the-additional-security-verification-page"></a>Dostęp do strony Dodatkowa weryfikacja zabezpieczeń

Po włączeniu organizacji i skonfigurowaniu weryfikacji dwuskładnikowej pojawi się monit z informacją o dostarczeniu większej ilości informacji, które pomogą ci zabezpieczyć konto.

![Więcej informacji wymaganych monit](media/multi-factor-authentication-verification-methods/multi-factor-authentication-initial-prompt.png)

### <a name="to-access-the-additional-security-verification-page"></a>Aby uzyskać dostęp do strony Dodatkowa weryfikacja zabezpieczeń

1. Wybierz **przycisk Dalej** z monitu Więcej **wymaganych informacji.**

    Zostanie wyświetlona strona **Dodatkowa weryfikacja zabezpieczeń.**

2. Na stronie **Dodatkowa weryfikacja zabezpieczeń** musisz zdecydować, której dwuskładnikowej metody weryfikacji użyć, aby zweryfikować, że jesteś tym, kim się mówisz po zalogowaniu się na konto służbowe. Dostępne są następujące opcje:

    | Metoda kontaktu | Opis |
    | --- | --- |
    | Aplikacja mobilna | <ul><li>**Otrzymuj powiadomienia do weryfikacji.** Ta opcja powoduje wypuszczenie powiadomienia do aplikacji uwierzytelniającego na smartfonie lub tablecie. Wyświetl powiadomienie i, jeśli jest zgodne z prawem, wybierz pozycję **Uwierzytelnij** w aplikacji. Praca lub szkoła może wymagać wprowadzenia kodu PIN przed uwierzytelnieniem.</li><li>**Użyj kodu weryfikacyjnego.** W tym trybie aplikacja uwierzytelniającego generuje kod weryfikacyjny, który jest aktualizowany co 30 sekund. Wprowadź najbardziej aktualny kod weryfikacyjny na ekranie logowania.<br>Aplikacja Microsoft Authenticator jest dostępna dla systemów [Android](https://go.microsoft.com/fwlink/?linkid=866594) i [iOS](https://go.microsoft.com/fwlink/?linkid=866594).</li></ul> |
    | Telefon uwierzytelniający | <ul><li>**Połączenie telefoniczne** umieszcza automatyczne połączenie głosowe na podajone przez Ciebie numer telefonu. Odbierz połączenie i naciśnij klawisz funta (#) na klawiaturze telefonu, aby uwierzytelnić się.</li><li>**Wiadomość tekstowa** kończy wiadomość tekstową zawierającą kod weryfikacyjny. Po wierszu w tekście odpowiedz na wiadomość tekstową lub wprowadź kod weryfikacyjny podany w interfejsie logowania.</li></ul> |
    | Telefon służbowy | Umieszcza automatyczne połączenie głosowe na podanym numerem telefonu. Odbierz połączenie i naciśnij klawisz funta (#) na klawiaturze telefonu, aby uwierzytelnić się. |

## <a name="next-steps"></a>Następne kroki

Po przejściu dostępu do strony **Dodatkowa weryfikacja zabezpieczeń** musisz wybrać i skonfigurować metodę weryfikacji dwuskładnikowej:

- [Konfigurowanie urządzenia mobilnego jako metody weryfikacji](multi-factor-authentication-setup-phone-number.md)

- [Konfigurowanie telefonu biurowego jako metody weryfikacji](multi-factor-authentication-setup-office-phone.md)

- [Konfigurowanie aplikacji Microsoft Authenticator jako metody weryfikacji](multi-factor-authentication-setup-auth-app.md)

## <a name="related-resources"></a>Powiązane zasoby

- [Zarządzanie ustawieniami metody weryfikacji dwuskładnikowej](multi-factor-authentication-end-user-manage-settings.md)

- [Zarządzanie hasłami aplikacji](multi-factor-authentication-end-user-app-passwords.md)

- [Logowanie przy użyciu weryfikacji dwuskładnikowej](multi-factor-authentication-end-user-signin.md)

- [Uzyskaj pomoc dotyczącą weryfikacji dwuskładnikowej](multi-factor-authentication-end-user-troubleshoot.md) 
