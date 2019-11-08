---
title: Dodawanie konto Microsoft osobistych do aplikacji uwierzytelniania — Azure AD
description: Jak dodać osobiste konta Microsoft, takie jak Outlook.com lub Xbox LIVE do aplikacji Microsoft Authenticator na potrzeby weryfikacji dwuskładnikowej.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: lizross
ms.reviewer: olhaun
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ca01aad76bfbeba0cf56be8ee74287a79f6346e
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820238"
---
# <a name="add-your-personal-microsoft-accounts"></a>Dodawanie osobistych kont Microsoft

Dodaj osobiste konta Microsoft, takie jak Outlook.com i Xbox LIVE do aplikacji Microsoft Authenticator dla standardowego procesu weryfikacji dwuskładnikowej i metody logowania za pomocą telefonu bezhasło.

- **Standardowa metoda weryfikacji dwuskładnikowej.** Wpisz nazwę użytkownika i hasło do urządzenia, którego używasz do logowania, a następnie wybierz, czy aplikacja Microsoft Authenticator będzie wysyłać powiadomienie, czy wolisz skopiować skojarzony kod weryfikacyjny z ekranu **konta** w firmie Microsoft. Aplikacja uwierzytelniania.

- **Metoda logowania bezhasłem.** Wpisz nazwę użytkownika na urządzeniu, na którym logujesz się do konto Microsoft osobistego, a następnie użyj urządzenia przenośnego, aby sprawdzić, czy używasz odcisku palca, kroju lub numeru PIN. W przypadku tej metody nie musisz wprowadzać hasła.

>[!Important]
>Aby można było dodać konto, należy pobrać i zainstalować aplikację Microsoft Authenticator. Jeśli jeszcze tego nie zrobiono, wykonaj kroki opisane w artykule [pobieranie i instalowanie aplikacji](user-help-auth-app-download-install.md) .

## <a name="add-your-personal-microsoft-account"></a>Dodaj osobistą konto Microsoft

Możesz dodać własne konto Microsoft, najpierw włączając weryfikację dwuetapową, a następnie dodając konto do aplikacji.

>[!Note]
>Jeśli zamierzasz korzystać tylko z logowania jednokrotnego dla osobistego konto Microsoft, nie musisz włączać weryfikacji dwuskładnikowej. Jednak w przypadku dodatkowych zabezpieczeń konta zalecamy włączenie weryfikacji dwuetapowej.

### <a name="turn-on-two-factor-verification"></a>Włącz weryfikację dwuskładnikową

1. Na komputerze przejdź do strony [podstawy zabezpieczeń](https://account.microsoft.com/security) i zaloguj się przy użyciu konto Microsoft osobistych. Na przykład alain@outlook.com.

2. W dolnej części strony **podstawowe informacje o zabezpieczeniach** wybierz łącze **więcej opcji zabezpieczeń** .

    ![Strona podstawy zabezpieczeń z wyróżnionym linkiem "więcej opcji zabezpieczeń"](./media/user-help-auth-app-add-personal-ms-account/more-security-options-link.png)

3. Przejdź do sekcji **weryfikacja dwuetapowa** i **Wybierz Włączenie tej funkcji.** Możesz również ją wyłączyć, jeśli nie chcesz już używać jej z kontem osobistym.

### <a name="add-your-microsoft-account-to-the-app"></a>Dodawanie konto Microsoft do aplikacji

1. Otwórz aplikację Microsoft Authenticator na urządzeniu przenośnym.

2. Wybierz pozycję **Dodaj konto** z ikony **Dostosowywanie i kontrola** w prawym górnym rogu.

    ![Strona kont z wyróżnioną ikoną Dostosowywanie i kontrolkę](./media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon.png)

3. Na stronie **Dodawanie konta** wybierz pozycję **konto osobiste**.

4. Zaloguj się do swojego konta osobistego przy użyciu odpowiedniego adresu e-mail (takiego jak alain@outlook.com), a następnie wybierz przycisk **dalej**.

    >[!Note]
    >Jeśli nie masz konto Microsoft osobistych, możesz utworzyć je tutaj.

5. Wprowadź hasło, a następnie wybierz pozycję **Zaloguj się**.

    Twoje konto osobiste zostanie dodane do aplikacji Microsoft Authenticator.

## <a name="next-steps"></a>Następne kroki

- Po dodaniu kont do aplikacji możesz zalogować się przy użyciu aplikacji uwierzytelniania na urządzeniu. Aby uzyskać więcej informacji, zobacz [Logowanie przy użyciu aplikacji](user-help-auth-app-sign-in.md).

- Jeśli masz problemy z uzyskaniem kodu weryfikacyjnego dla konto Microsoft osobistych, zobacz sekcję **Rozwiązywanie problemów z kodem weryfikacyjnym** w artykule [konto Microsoft informacje zabezpieczające & kody weryfikacyjne](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes) .

- W przypadku urządzeń z systemem iOS można również utworzyć kopię zapasową poświadczeń konta i powiązanych ustawień aplikacji, takich jak kolejność kont, w chmurze. Aby uzyskać więcej informacji, zobacz [Tworzenie kopii zapasowych i odzyskiwanie danych za pomocą aplikacji Microsoft Authenticator](user-help-auth-app-backup-recovery.md).
