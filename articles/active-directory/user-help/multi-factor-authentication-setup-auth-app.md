---
title: Konfigurowanie aplikacji uwierzytelniającego jako metody weryfikacji dwuskładnikowej — Azure Active Directory | Dokumenty firmy Microsoft
description: Dowiedz się, jak skonfigurować aplikację Microsoft Authenticator jako metodę weryfikacji dwuskładnikowej.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: overview
ms.date: 08/12/2019
ms.author: curtand
ms.openlocfilehash: 6ab1f7c97173021cc112a5f117469abd74ac954d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77062578"
---
# <a name="set-up-an-authenticator-app-as-your-two-factor-verification-method"></a>Konfigurowanie aplikacji uwierzytelniającego jako metody weryfikacji dwuskładnikowej

Możesz skonfigurować aplikację uwierzytelniającego, aby wysyłać powiadomienia na urządzenie mobilne lub wysyłać kod weryfikacyjny jako metodę weryfikacji zabezpieczeń. Nie musisz korzystać z aplikacji Microsoft Authenticator i możesz wybrać inną aplikację podczas procesu konfiguracji. Jednak w tym artykule używa się aplikacji Microsoft Authenticator.

>[!Important]
>Aby można było dodać konto, należy pobrać i zainstalować aplikację Microsoft Authenticator. Jeśli jeszcze tego nie zrobiono, wykonaj czynności opisane w artykule [Pobierz i zainstaluj aplikację.](user-help-auth-app-download-install.md)

>[!Note]
> Jeśli opcja Aplikacja mobilna jest wyszarzona, istnieje możliwość, że Twoja organizacja nie zezwala na używanie aplikacji uwierzytelniania do weryfikacji. W takim przypadku musisz wybrać inną metodę lub skontaktować się z administratorem, aby uzyskać więcej pomocy.

## <a name="set-up-the-microsoft-authenticator-app-to-send-notifications"></a>Konfigurowanie aplikacji Microsoft Authenticator do wysyłania powiadomień

1. Na stronie **Dodatkowa weryfikacja zabezpieczeń** wybierz **aplikację mobilną** z **kroku 1: Jak powinniśmy skontaktować się z Tobą** w obszarze.

2. Wybierz **pozycję Odbierz powiadomienia do weryfikacji** w obszarze Jak chcesz **korzystać z aplikacji mobilnej,** a następnie wybierz pozycję **Skonfiguruj**.

    ![Dodatkowa strona weryfikacji zabezpieczeń z opcją aplikacji mobilnej i powiadomień](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-notification.png)

    Zostanie wyświetlona strona **Konfigurowanie aplikacji mobilnej.**

    ![Ekran, który zapewnia kod QR](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-barcode.png)

3. Otwórz aplikację Microsoft Authenticator, wybierz pozycję **Dodaj konto** z **ikony Dostosuj i kontroluj** w prawym górnym rogu, a następnie wybierz **pozycję Konto służbowe.**

    >[!Note]
    >Jeśli po raz pierwszy konfigurujesz aplikację Microsoft Authenticator, może pojawić się monit z pytaniem, czy zezwolić aplikacji na dostęp do aparatu (iOS), czy zezwolić aplikacji na robienia zdjęć i nagrywania wideo (Android). Musisz wybrać **opcję Zezwalaj,** aby aplikacja uwierzytelniającego mogła uzyskać dostęp do aparatu, aby zrobić zdjęcie kodu QR w następnym kroku. Jeśli nie zezwolisz na aparat, nadal możesz skonfigurować aplikację uwierzytelniacza, ale musisz ręcznie dodać informacje o kodzie. Aby uzyskać informacje dotyczące ręcznego dodawania kodu, zobacz [Ręczne dodawanie konta do aplikacji](user-help-auth-app-add-account-manual.md).

4. Użyj kamery urządzenia, aby zeskanować kod QR z ekranu **Konfigurowanie aplikacji mobilnej** na komputerze, a następnie wybierz pozycję **Dalej**.

5. Wróć do komputera i na stronie **Dodatkowa weryfikacja zabezpieczeń,** upewnij się, że zostanie wyświetlony komunikat informujący, że konfiguracja zakończyła się pomyślnie, a następnie wybierz pozycję **Dalej**.

    ![Dodatkowa strona weryfikacji zabezpieczeń z komunikatem o sukcesie](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-notification-confirm.png)

    Aplikacja uwierzytelniającego wyśle powiadomienie na urządzenie mobilne jako test.

6. Na urządzeniu przenośnym wybierz pozycję **Zatwierdź**.

7. Na komputerze dodaj numer telefonu urządzenia mobilnego do **kroku 3: W przypadku utraty dostępu do obszaru aplikacji mobilnej,** a następnie wybierz przycisk **Dalej**.

    Zdecydowanie zalecamy dodanie numeru telefonu urządzenia mobilnego, aby działać jako kopia zapasowa, jeśli nie możesz uzyskać dostępu do aplikacji mobilnej lub z niej korzystać z jakiegokolwiek powodu.

8. W **kroku 4: Kontynuuj korzystanie z istniejącego** obszaru aplikacji, skopiuj podane hasło aplikacji i wklej go w bezpiecznym miejscu.

    ![Obszar Hasła aplikacji na stronie Dodatkowa weryfikacja zabezpieczeń](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >Aby uzyskać informacje dotyczące używania hasła aplikacji ze starszymi [aplikacjami,](multi-factor-authentication-end-user-app-passwords.md)zobacz Zarządzanie hasłami aplikacji . Hasła aplikacji muszą być używane tylko wtedy, gdy nadal korzystasz ze starszych aplikacji, które nie obsługują weryfikacji dwuskładnikowej.

9. Wybierz pozycję **Done** (Gotowe).

## <a name="set-up-the-microsoft-authenticator-app-to-use-verification-codes"></a>Konfigurowanie aplikacji Microsoft Authenticator do używania kodów weryfikacyjnych

1. Na stronie **Dodatkowa weryfikacja zabezpieczeń** wybierz **aplikację mobilną** z **kroku 1: Jak powinniśmy skontaktować się z Tobą** w obszarze.

2. Wybierz **pozycję Użyj kodu weryfikacyjnego** w obszarze Jak chcesz **korzystać z aplikacji mobilnej,** a następnie wybierz pozycję **Skonfiguruj**.

    ![Dodatkowa strona weryfikacji zabezpieczeń z opcją aplikacji mobilnej i powiadomień](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-verification-code.png)

    Zostanie wyświetlona strona **Konfigurowanie aplikacji mobilnej.**

    ![Ekran, który zapewnia kod QR](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-barcode.png)

3. Otwórz aplikację Microsoft Authenticator, wybierz pozycję **Dodaj konto** z **ikony Dostosuj i kontroluj** w prawym górnym rogu, a następnie wybierz **pozycję Konto służbowe.**

    >[!Note]
    >Jeśli po raz pierwszy konfigurujesz aplikację Microsoft Authenticator, może pojawić się monit z pytaniem, czy zezwolić aplikacji na dostęp do aparatu (iOS), czy zezwolić aplikacji na robienia zdjęć i nagrywania wideo (Android). Musisz wybrać **opcję Zezwalaj,** aby aplikacja uwierzytelniającego mogła uzyskać dostęp do aparatu, aby zrobić zdjęcie kodu QR w następnym kroku. Jeśli nie zezwolisz na aparat, nadal możesz skonfigurować aplikację uwierzytelniacza, ale musisz ręcznie dodać informacje o kodzie. Aby uzyskać informacje dotyczące ręcznego dodawania kodu, zobacz [Ręczne dodawanie konta do aplikacji](user-help-auth-app-add-account-manual.md).

4. Użyj kamery urządzenia, aby zeskanować kod QR z ekranu **Konfigurowanie aplikacji mobilnej** na komputerze, a następnie wybierz pozycję **Dalej**.

5. Wróć do komputera i na stronie **Dodatkowa weryfikacja zabezpieczeń,** upewnij się, że zostanie wyświetlony komunikat informujący, że konfiguracja zakończyła się pomyślnie, a następnie wybierz pozycję **Dalej**.

    ![Dodatkowa strona weryfikacji zabezpieczeń z komunikatem o sukcesie](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-verification-confirm.png)

    Aplikacja wystawca uwierzytelniacza poprosi o kod weryfikacyjny jako test.

6. W aplikacji Microsoft Authenticator przewiń w dół do konta służbowego, skopiuj i wklej 6-cyfrowy kod z aplikacji do **kroku 2: Wprowadź kod weryfikacyjny z** pola aplikacji mobilnej na komputerze, a następnie wybierz pozycję **Sprawdź**.

    ![Dodatkowa strona weryfikacji zabezpieczeń z testem kodu weryfikacyjnego](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-verification-test.png)

7. Na komputerze dodaj numer telefonu urządzenia mobilnego do **kroku 3: W przypadku utraty dostępu do obszaru aplikacji mobilnej,** a następnie wybierz przycisk **Dalej**.

    Zdecydowanie zalecamy dodanie numeru telefonu urządzenia mobilnego, aby działać jako kopia zapasowa, jeśli nie możesz uzyskać dostępu do aplikacji mobilnej lub z niej korzystać z jakiegokolwiek powodu.

8. W **kroku 4: Kontynuuj korzystanie z istniejącego** obszaru aplikacji, skopiuj podane hasło aplikacji i wklej go w bezpiecznym miejscu.

    ![Obszar Hasła aplikacji na stronie Dodatkowa weryfikacja zabezpieczeń](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >Aby uzyskać informacje dotyczące używania hasła aplikacji ze starszymi [aplikacjami,](multi-factor-authentication-end-user-app-passwords.md)zobacz Zarządzanie hasłami aplikacji . Hasła aplikacji muszą być używane tylko wtedy, gdy nadal korzystasz ze starszych aplikacji, które nie obsługują weryfikacji dwuskładnikowej.

9. Wybierz pozycję **Done** (Gotowe).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu metody weryfikacji dwuskładnikowej możesz dodać dodatkowe metody, zarządzać ustawieniami i hasłami aplikacji, zalogować się lub uzyskać pomoc dotyczącą typowych problemów związanych z weryfikacją dwuskładnikową.

- [Zarządzanie ustawieniami metody weryfikacji dwuskładnikowej](multi-factor-authentication-end-user-manage-settings.md)

- [Zarządzanie hasłami aplikacji](multi-factor-authentication-end-user-app-passwords.md)

- [Logowanie przy użyciu weryfikacji dwuskładnikowej](multi-factor-authentication-end-user-signin.md)

- [Uzyskaj pomoc dotyczącą weryfikacji dwuskładnikowej](multi-factor-authentication-end-user-troubleshoot.md)
