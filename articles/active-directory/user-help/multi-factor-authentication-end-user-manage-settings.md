---
title: Zmienianie metody i ustawień weryfikacji dwuskładnikowej — usługa Azure Active Directory
description: Dowiedz się, jak zmienić metodę weryfikacji zabezpieczeń i ustawienia konta służbowego na stronie Dodatkowa weryfikacja zabezpieczeń.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: richagi
ms.assetid: d3372d9a-9ad1-4609-bdcf-2c4ca9679a3b
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: curtand
ms.openlocfilehash: 5949f04ecc28a88e340a9c2de530031793f193a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253249"
---
# <a name="change-your-two-factor-verification-method-and-settings"></a>Zmienianie metody i ustawień weryfikacji dwuskładnikowej

Po skonfigurowaniu metod weryfikacji zabezpieczeń dla konta służbowego można zaktualizować dowolne z powiązanych informacji, w tym:

- Domyślna metoda weryfikacji zabezpieczeń

- Szczegóły metody weryfikacji zabezpieczeń, takie jak numer telefonu

- Konfiguracja aplikacji uwierzytelniająca lub usuwanie urządzenia z aplikacji uwierzytelniającej

## <a name="using-the-additional-security-verification-page"></a>Korzystanie ze strony Dodatkowa weryfikacja zabezpieczeń

Jeśli organizacja dostarczyła ci określone kroki dotyczące włączania weryfikacji dwuskładnikowej i zarządzania nią, należy najpierw wykonać te instrukcje. W przeciwnym razie możesz przejść do ustawień metody weryfikacji zabezpieczeń na stronie [Dodatkowa weryfikacja zabezpieczeń.](https://docs.microsoft.com/azure/active-directory/user-help/multi-factor-authentication-end-user-first-time)

>[!Note]
>Jeśli to, co widzisz na ekranie, nie odpowiada temu, co jest opisane w tym artykule, oznacza to, że administrator włączył **środowisko informacje o zabezpieczeniach (wersja zapoznawcza)** lub że twoja organizacja dostarczyła własny portal niestandardowy. Aby uzyskać więcej informacji na temat nowych informacji zabezpieczających, zobacz [Omówienie informacji o zabezpieczeniach (wersja zapoznawcza).](user-help-security-info-overview.md) Aby uzyskać więcej informacji o portalu niestandardowym organizacji, należy skontaktować się z działem pomocy technicznej organizacji.

### <a name="to-get-to-the-additional-security-verification-page"></a>Aby przejść do strony Dodatkowa weryfikacja zabezpieczeń

Możesz kierować ten link na [stronę Dodatkowa weryfikacja zabezpieczeń](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1).

![Dodatkowa strona weryfikacji zabezpieczeń z dostępnymi szczegółami metody weryfikacji zabezpieczeń](./media/multi-factor-authentication-end-user-manage-settings/mfa-security-verification-page.png)

Możesz również przejść do strony **Dodatkowa weryfikacja zabezpieczeń,** wykonując następujące kroki:

1. Zaloguj się [https://myapps.microsoft.com](https://myapps.microsoft.com)do .

1. Wybierz nazwę konta w prawym górnym rogu, a następnie wybierz **profil**.

1. Wybierz **opcję Dodatkowa weryfikacja zabezpieczeń**.  

    ![Łącze Moje aplikacje do strony Dodatkowej weryfikacji zabezpieczeń](./media/multi-factor-authentication-end-user-manage-settings/mfa-myapps-link.png)

>[!Note]
>Aby uzyskać informacje dotyczące korzystania z sekcji **Hasła aplikacji** na stronie **Dodatkowa weryfikacja zabezpieczeń,** zobacz [Zarządzanie hasłami aplikacji w celu weryfikacji dwuskładnikowej](multi-factor-authentication-end-user-app-passwords.md). Hasła aplikacji powinny być używane tylko w przypadku aplikacji, które nie obsługują weryfikacji dwuskładnikowej.

## <a name="change-your-default-security-verification-method"></a>Zmienianie domyślnej metody weryfikacji zabezpieczeń

Po zalogowaniu się na konto służbowe przy przyuśle i nazwie użytkownika zostanie automatycznie wyświetlona wybrana metoda weryfikacji zabezpieczeń. W zależności od wymagań organizacji może to być powiadomienie lub kod weryfikacyjny za pośrednictwem aplikacji uwierzytelniającego, wiadomości tekstowej lub połączenia telefonicznego.

Jeśli zdecydujesz, że chcesz zmienić używana domyślna metoda weryfikacji zabezpieczeń, możesz to zrobić w tym miejscu.

### <a name="to-change-your-default-security-verification-method"></a>Aby zmienić domyślną metodę weryfikacji zabezpieczeń

1. Na stronie **Dodatkowa weryfikacja zabezpieczeń** wybierz metodę, której chcesz użyć, z listy **Preferowane opcji.** Zobaczysz wszystkie opcje, ale możesz wybrać tylko te, które są udostępniane przez twoją organizację.

    - **Powiadom mnie za pośrednictwem aplikacji:** Otrzymasz powiadomienie za pośrednictwem aplikacji uwierzytelniającego, że masz monit weryfikacji oczekiwania.

    - **Zadzwoń na mój telefon uwierzytelniający:** otrzymasz telefon na urządzeniu mobilnym z prośbą o zweryfikowanie informacji.

    - **Kod tekstowy na mój telefon uwierzytelniający:** otrzymasz kod weryfikacyjny w ramach wiadomości tekstowej na urządzeniu mobilnym. Musisz wprowadzić ten kod w wierszu weryfikacji konta służbowego.

    - **Zadzwoń do mojego telefonu biurowego:** otrzymasz telefon na telefon biurowy z prośbą o zweryfikowanie informacji.

    - **Użyj kodu weryfikacyjnego z aplikacji:** użyjesz aplikacji uwierzytelniającego, aby uzyskać kod weryfikacyjny, który wpiszesz w wierszu z konta służbowego.

2. Wybierz **pozycję Zapisz**.

## <a name="add-or-change-your-phone-number"></a>Dodawanie lub zmienianie numeru telefonu

Możesz dodać nowe numery telefonów lub zaktualizować istniejące numery na stronie **Dodatkowa weryfikacja zabezpieczeń.**

>[!Important]
>Zdecydowanie zalecamy dodanie dodatkowego numeru telefonu, aby zapobiec zablokowaniu konta w przypadku zgubienia lub kradzieży telefonu podstawowego lub jeśli masz nowy telefon i nie masz już oryginalnego, podstawowego numeru telefonu.

### <a name="to-change-your-phone-numbers"></a>Aby zmienić numery telefonów

1. W sekcji **Jak chcesz zareagować?** na stronie **Dodatkowa weryfikacja zabezpieczeń** zaktualizuj informacje o numerze telefonu **uwierzytelniania** (podstawowym urządzeniu przenośnym) i **telefonie pakietu Office**.

1. Zaznacz pole obok opcji **Telefon uwierzytelniania alternatywnego,** a następnie wpisz dodatkowy numer telefonu, pod którym możesz odbierać wiadomości tekstowe lub połączenia telefoniczne, jeśli nie możesz uzyskać dostępu do urządzenia podstawowego.

1. Wybierz **pozycję Zapisz**.

## <a name="add-a-new-account-to-the-microsoft-authenticator-app"></a>Dodawanie nowego konta do aplikacji uwierzytelniającego microsoft

Konto służbowe można skonfigurować w aplikacji Microsoft Authenticator na [androida](https://play.google.com/store/apps/details?id=com.azure.authenticator) lub [iOS](https://apps.apple.com/app/microsoft-authenticator/id983156458).

Jeśli konto służbowe jest już skonfigurowane w aplikacji Microsoft Authenticator, nie musisz tego robić ponownie.

1. W sekcji **Jak chcesz zareagować?** na stronie **Dodatkowa weryfikacja zabezpieczeń** wybierz pozycję **Konfigurowanie aplikacji Authenticator**.

    ![Konfigurowanie konta służbowego w aplikacji Microsoft Authenticator](./media/multi-factor-authentication-end-user-manage-settings/mfa-security-verification-page-auth-app.png)

1. Postępuj zgodnie z instrukcjami wyświetlanymi na ekranie, w tym za pomocą urządzenia przenośnego do skanowania kodu QR, a następnie wybierz przycisk **Dalej**.

    Zostaniesz poproszony o zatwierdzenie powiadomienia za pośrednictwem aplikacji Microsoft Authenticator, aby zweryfikować informacje.

1. Wybierz **pozycję Zapisz**.

## <a name="delete-your-account-or-device-from-the-microsoft-authenticator-app"></a>Usuwanie konta lub urządzenia z aplikacji Microsoft Authenticator

Możesz usunąć swoje konto z aplikacji Microsoft Authenticator i usunąć urządzenie z konta służbowego. Zazwyczaj usuwasz urządzenie, aby trwale usunąć utracone, skradzione lub stare urządzenie z konta, a następnie usuwasz konto, aby spróbować rozwiązać niektóre problemy z połączeniem lub rozwiązać zmianę konta, na przykład nową nazwę użytkownika.

### <a name="to-delete-your-device-from-your-work-or-school-account"></a>Aby usunąć urządzenie z konta służbowego

1. W sekcji **Jak chcesz zareagować?** na stronie **Dodatkowa weryfikacja zabezpieczeń** wybierz przycisk **Konfigurowanie aplikacji Authenticator.**

1. Wybierz **pozycję Zapisz**.

### <a name="to-delete-your-account-from-the-microsoft-authenticator-app"></a>Aby usunąć konto z aplikacji Microsoft Authenticator

W aplikacji Microsoft Authenticator wybierz przycisk **Usuń** obok urządzenia, które chcesz usunąć.

## <a name="turn-on-two-factor-verification-prompts-on-a-trusted-device"></a>Włączanie dwuskładnikowych monitów weryfikacyjnych na zaufanym urządzeniu

W zależności od ustawień organizacji może zostać wyświetlone pole wyboru z napisem **Nie pytaj ponownie przez X dni** podczas weryfikacji dwuskładnikowej w przeglądarce. Jeśli ta opcja jest zaznaczona jako zatrzymanie monitów weryfikacji dwuskładnikowej, a następnie utracisz urządzenie lub urządzenie zostanie potencjalnie naruszone, włącz monity weryfikacji dwuskładnikowej, aby chronić swoje konto. Należy włączyć monity dla wszystkich urządzeń w tym samym czasie. Niestety, nie można włączyć monitów tylko dla określonego urządzenia.

### <a name="to-turn-two-factor-verification-prompts-back-on-for-your-devices"></a>Aby włączyć ponowne włączenie monitów weryfikacji dwuskładnikowej dla urządzeń

Na [stronie **Dodatkowa weryfikacja zabezpieczeń** ](#to-get-to-the-additional-security-verification-page)wybierz pozycję **Przywróć uwierzytelnianie wieloskładnikowe na wcześniej zaufanych urządzeniach**. Przy następnym logowanie na dowolnym urządzeniu zostanie wyświetlony monit o przeprowadzenie weryfikacji dwuskładnikowej.

## <a name="next-steps"></a>Następne kroki

Po dodaniu lub zaktualizowaniu ustawień weryfikacji dwuskładnikowej możesz zarządzać hasłami aplikacji, zalogować się lub uzyskać pomoc dotyczącą typowych problemów związanych z weryfikacją dwuskładnikową.

- [Zarządzaj hasłami aplikacji w celu weryfikacji dwuskładnikowej](multi-factor-authentication-end-user-app-passwords.md) dla wszystkich aplikacji, które nie obsługują weryfikacji dwuskładnikowej.

- [Jak zalogować się przy użyciu weryfikacji dwuskładnikowej](multi-factor-authentication-end-user-signin.md)

- [Rozwiązywanie typowych problemów za pomocą weryfikacji dwuskładnikowej](multi-factor-authentication-end-user-troubleshoot.md)
