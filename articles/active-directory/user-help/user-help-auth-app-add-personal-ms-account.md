---
title: Dodawanie osobistego konta Microsoft do aplikacji Microsoft Authenticator — Azure AD
description: Dodaj osobiste konta Microsoft, takie jak Outlook.com lub Xbox LIVE, do aplikacji Microsoft Authenticator, aby zweryfikować twoją tożsamość podczas korzystania z weryfikacji dwuskładnikowej.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: cef4f83881aed67c46477110de530bbf191ee39f
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984765"
---
# <a name="add-personal-microsoft-accounts-to-the-microsoft-authenticator-app"></a>Dodawanie osobistych kont Microsoft do aplikacji Microsoft Authenticator

Dodaj osobiste konta Microsoft, takie jak konta Outlook.com i Xbox LIVE, do aplikacji Microsoft Authenticator, zarówno w przypadku standardowego procesu weryfikacji dwuskładnikowej, jak i logowania się do telefonu bez hasła.

- **Standardowa metoda weryfikacji dwuskładnikowej.** Wpisz nazwę użytkownika i hasło na urządzeniu, na które się logujesz, a następnie wybierz, czy aplikacja Microsoft Authenticator wysyła powiadomienie, czy wolisz skopiować skojarzony kod weryfikacyjny z ekranu **Konta** aplikacji Microsoft Authenticator.

- **Metoda logowania bez hasła.** Wpisz swoją nazwę użytkownika na urządzeniu, na które się logujesz, używając osobistego konta Microsoft, a następnie użyj urządzenia przenośnego, aby sprawdzić, czy używasz odcisku palca, twarzy lub numeru PIN. W przypadku tej metody nie musisz wprowadzać hasła.

>[!Important]
>Aby można było dodać konto, należy pobrać i zainstalować aplikację Microsoft Authenticator. Jeśli jeszcze tego nie zrobiono, wykonaj czynności opisane w artykule [Pobierz i zainstaluj aplikację.](user-help-auth-app-download-install.md)

Możesz dodać osobiste konto Microsoft, najpierw włączając weryfikację dwuskładnikową, a następnie dodając konto do aplikacji. Nie musisz włączać weryfikacji dwuskładnikowej, aby używać tylko logowania się na telefon bez hasła dla swojego konta, ale zdecydowanie zalecamy włączenie weryfikacji dwuskładnikowej w celu uzyskania dodatkowego zabezpieczenia konta.

## <a name="turn-on-two-factor-verification"></a>Włączanie weryfikacji dwuskładnikowej

1. Na komputerze przejdź do strony [Podstawy zabezpieczeń](https://account.microsoft.com/security) i zaloguj się przy użyciu osobistego konta Microsoft. Na przykład alain@outlook.com.

2. U dołu strony **Podstawy zabezpieczeń** wybierz łącze opcji **więcej zabezpieczeń.**

    ![Strona Podstawy zabezpieczeń z wyróżnionym linkiem "więcej opcji zabezpieczeń"](./media/user-help-auth-app-add-personal-ms-account/more-security-options-link.png)

3. Przejdź do sekcji **Weryfikacja dwuetapowa** i wybierz opcję włączenia **funkcji**. Możesz również wyłączyć go tutaj, jeśli nie chcesz już używać go z kontem osobistym.

## <a name="add-your-microsoft-account-to-the-app"></a>Dodawanie konta Microsoft do aplikacji

1. Otwórz aplikację Microsoft Authenticator na urządzeniu przenośnym.

1. W systemie Android wybierz pozycję **Dodaj konto** z **ikony Dostosuj i Kontroluj** w prawym górnym rogu.

    ![Strony wyboru konta na Androida](media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon.png)

    W aplikacji iOS wybierz ikonę plus w prawym górnym rogu.

    ![iOS wersja środowiska wyboru konta](media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon-ios.png)

1. Na stronie **Dodaj konto** wybierz pozycję **Konto osobiste**.

1. Wybierz **pozycję Zaloguj się za pomocą firmy Microsoft,** aby dodać swoje konto. Kod QR może być używany, gdy jest dostępny, ale zawsze możesz dodać swoje konto, logując się przy użyciu nazwy użytkownika i hasła.

    ![Wybierz konto Microsoft lub zeskanuj kod QR, jeśli jest dostępny](media/user-help-auth-app-add-personal-ms-account/add-account-android.png)

1. Zaloguj się na swoje konto osobiste, używając alain@outlook.comodpowiedniego adresu e-mail (np. ), a następnie wybierz pozycję **Dalej**.

    >[!Note]
    >Jeśli nie masz osobistego konta Microsoft, [możesz je utworzyć](https://account.microsoft.com/account/Account?refd=www.bing.com&ru=https%3A%2F%2Faccount.microsoft.com%2F%3Frefd%3Dwww.bing.com&destrt=home-index).

1. Wprowadź hasło, a następnie wybierz pozycję **Zaloguj**się . Twoje konto osobiste zostanie dodane do aplikacji Microsoft Authenticator.

## <a name="next-steps"></a>Następne kroki

- Po dodaniu kont do aplikacji możesz zalogować się za pomocą aplikacji Authenticator na urządzeniu. Aby uzyskać więcej informacji, zobacz [Logowanie się przy użyciu aplikacji](user-help-auth-app-sign-in.md).

- Jeśli masz problemy z uzyskaniem kodu weryfikacyjnego dla osobistego konta Microsoft, zobacz sekcję **Rozwiązywanie problemów z kodem weryfikacyjnym** w artykule [& kody weryfikacyjne dotyczące zabezpieczeń konta Microsoft.](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes)

- W przypadku urządzeń z systemem iOS można również w chmurze uzyskać zapas y poświadczeń konta i powiązanych ustawień aplikacji, takich jak kolejność kont. Aby uzyskać więcej informacji, zobacz [Tworzenie kopii zapasowych i odzyskiwanie za pomocą aplikacji Microsoft Authenticator](user-help-auth-app-backup-recovery.md).
