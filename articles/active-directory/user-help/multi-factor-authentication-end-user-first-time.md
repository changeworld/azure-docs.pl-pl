---
title: Konfigurowanie metod weryfikacji dwuskładnikowej — Omówienie Azure Active Directory | Microsoft Docs
description: Omówienie sposobu konfigurowania metod weryfikacji dwuskładnikowej w celu weryfikacji dwuskładnikowej.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: overview
ms.date: 08/12/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83bba343ac73fd0df575ae1e8a83c589a0bc15ac
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69616166"
---
# <a name="set-up-your-two-factor-verification-methods-overview"></a>Konfigurowanie metod weryfikacji dwuskładnikowej — Omówienie

Twoja organizacja włączyła weryfikację dwuskładnikową, co oznacza, że logowanie za pomocą konta służbowego wymaga podania kombinacji nazwy użytkownika, hasła oraz urządzenia przenośnego lub telefonu. Twoja organizacja włączyła tę dodatkową weryfikację, ponieważ jest bardziej bezpieczna niż tylko hasło oparte na dwóch formach uwierzytelniania: coś, czego znasz Weryfikacja dwuetapowa może pomóc zapobiec zastępowaniu złośliwych hakerów przez użytkownika, ponieważ nawet jeśli mają swoje hasło, szanse się, że nie mają one jeszcze urządzenia.

>[!Important]
>Ta zawartość jest przeznaczona dla użytkowników. Jeśli jesteś administratorem, więcej informacji na temat konfigurowania i zarządzania środowiskiem usługi Azure Active Directory (Azure AD) możesz znaleźć w [dokumentacji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory).

## <a name="who-decides-if-you-use-this-feature"></a>Kto decyduje o możliwości użycia tej funkcji?

W zależności od typu konta o konieczności korzystania z weryfikacji dwuskładnikowej może zdecydować Twoja organizacja lub ta decyzja może zostać przeniesiona na użytkownika.

- **Konto służbowe.** Jeśli używasz konta służbowego (na przykład alain@contoso.com), to Twoja organizacja decyduje, czy musisz korzystać z weryfikacji dwuskładnikowej i konkretnych metod weryfikacji. Ponieważ organizacja zdecydowała się na korzystanie z tej funkcji, nie ma możliwości jej samoistnienia.

- **Osobiste konto Microsoft.** Weryfikację dwuskładnikowa można skonfigurować dla osobistych kont Microsoft (na przykład alain@outlook.com). Jeśli masz problemy z weryfikacją dwuetapową i osobistą konto Microsoft, zobacz [Włączanie lub wyłączanie weryfikacji dwuskładnikowej dla konto Microsoft](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off). Ze względu na to, czy należy używać tej funkcji, możesz ją włączać i wyłączać w dowolnym momencie.

## <a name="access-the-additional-security-verification-page"></a>Dostęp do strony dodatkowej weryfikacji zabezpieczeń

Po włączeniu i skonfigurowaniu weryfikacji dwuskładnikowej zostanie wyświetlony monit z prośbą o podanie dodatkowych informacji w celu zabezpieczenia konta.

![Monit o podanie większej ilości informacji](media/multi-factor-authentication-verification-methods/multi-factor-authentication-initial-prompt.png)

### <a name="to-access-the-additional-security-verification-page"></a>Aby uzyskać dostęp do strony dodatkowej weryfikacji zabezpieczeń

1. Wybierz pozycję **dalej** z monitu **więcej informacji** .

    Zostanie wyświetlona strona **dodatkowa Weryfikacja zabezpieczeń** .

2. Na stronie **dodatkowej weryfikacji zabezpieczeń** należy zdecydować, która metoda weryfikacji dwuskładnikowej ma zostać użyta w celu zweryfikowania, czy po zalogowaniu się do konta służbowego lub szkolnego. Możesz wybrać:

    | Metoda kontaktu | Opis |
    | --- | --- |
    | Aplikacja mobilna | <ul><li>**Otrzymuj powiadomienia o weryfikacji.** Ta opcja powoduje wypchnięcie powiadomienia do aplikacji Authenticator na telefonie Smartphone lub tablecie. Wyświetl powiadomienie i, jeśli jest ono wiarygodne, wybierz pozycję **Uwierzytelnij** w aplikacji. Przed uwierzytelnieniem może być wymagane wprowadzenie numeru PIN przez użytkownika lub szkołę.</li><li>**Użyj kodu weryfikacyjnego.** W tym trybie aplikacja Authenticator generuje kod weryfikacyjny, który aktualizuje co 30 sekund. Wprowadź najbardziej aktualny kod weryfikacyjny na ekranie logowania.<br>Aplikacja Microsoft Authenticator jest dostępna dla systemów [Android](https://go.microsoft.com/fwlink/?linkid=866594) i [iOS](https://go.microsoft.com/fwlink/?linkid=866594).</li></ul> |
    | Numer telefonu uwierzytelniania | <ul><li>**Połączenie telefoniczne** umieszcza automatyczne połączenie głosowe z podanym numerem telefonu. Odpowiedz na wywołanie i naciśnij klawisz krzyżyka (#) na klawiaturze telefonu w celu uwierzytelnienia.</li><li>**Wiadomość SMS** zawiera wiadomość tekstową zawierającą kod weryfikacyjny. Po wyświetleniu monitu w tekście należy odpowiedzieć na wiadomość tekstową lub wprowadzić kod weryfikacyjny podany w interfejsie logowania.</li></ul> |
    | Telefon służbowy | Umieszcza automatyczne połączenie głosowe z podanym numerem telefonu. Odpowiedz na wywołanie i naciśnij klawisz krzyżyka (#) na klawiaturze telefonu w celu uwierzytelnienia. |

## <a name="next-steps"></a>Następne kroki

Po uzyskaniu dostępu do strony **dodatkowej weryfikacji zabezpieczeń** należy wybrać i skonfigurować metodę weryfikacji dwuskładnikowej:

- [Konfigurowanie urządzenia przenośnego jako metody weryfikacji](multi-factor-authentication-setup-phone-number.md)

- [Konfigurowanie telefonu biurowego jako metody weryfikacji](multi-factor-authentication-setup-office-phone.md)

- [Konfigurowanie aplikacji Microsoft Authenticator jako metody weryfikacji](multi-factor-authentication-setup-auth-app.md)

## <a name="related-resources"></a>Powiązane zasoby

- [Zarządzanie ustawieniami metody weryfikacji dwuskładnikowej](multi-factor-authentication-end-user-manage-settings.md)

- [Zarządzanie hasłami aplikacji](multi-factor-authentication-end-user-app-passwords.md)

- [Logowanie przy użyciu weryfikacji dwuskładnikowej](multi-factor-authentication-end-user-signin.md)

- [Uzyskaj pomoc dotyczącą weryfikacji dwuskładnikowej](multi-factor-authentication-end-user-troubleshoot.md) 
