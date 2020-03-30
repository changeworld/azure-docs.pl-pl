---
title: Ręczne dodawanie konta do aplikacji — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Jak ręcznie dodać konta do aplikacji Microsoft Authenticator w celu weryfikacji dwuskładnikowej.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: b237f710866ad3960dd65902c0789228d8c391bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062323"
---
# <a name="manually-add-an-account-to-the-app"></a>Ręczne dodawanie konta do aplikacji

Jeśli aparat nie może przechwycić kodu QR, możesz ręcznie dodać informacje o koncie do aplikacji Microsoft Authenticator w celu weryfikacji dwuskładnikowej. Działa to w przypadku kont służbowych i kont innych firm.

W kodach podanych dla kont nie jest rozróżniana wielkość liter i nie wymagają spacji po dodaniu do aplikacji Microsoft Authenticator.

>[!Important]
>Aby można było dodać konto, należy pobrać i zainstalować aplikację Microsoft Authenticator. Jeśli jeszcze tego nie zrobiono, wykonaj czynności opisane w artykule [Pobierz i zainstaluj aplikację.](user-help-auth-app-download-install.md)

## <a name="add-your-work-or-school-account"></a>Dodawanie konta służbowego

1. Na komputerze zanotuj informacje o **kodzie** i **adresie URL** na stronie **Konfigurowanie aplikacji mobilnej.** Zachowaj otwartą stronę, aby zobaczyć kod i adres URL.

    ![Ekran, który zapewnia kod QR](./media/user-help-auth-app-add-account-manual/auth-app-barcode.png)

2. Otwórz aplikację Microsoft Authenticator, wybierz pozycję **Dodaj konto** z **ikony Dostosuj i kontroluj** w prawym górnym rogu, a następnie wybierz **pozycję Konto służbowe.**

3. Wybierz **lub wprowadź kod ręcznie**.

    ![Ekran do skanowania kodu QR](./media/user-help-auth-app-add-account-manual/auth-app-manual-code.png)

4. Wprowadź **kod** i **adres URL** z kroku 1, a następnie wybierz pozycję **Zakończ**.

    ![Ekran wprowadzania kodu i adresu URL](./media/user-help-auth-app-add-account-manual/auth-app-code-url.png)

    Na ekranie **Konta** w aplikacji wyświetlana jest nazwa konta i sześciocyfrowy kod weryfikacyjny. Aby uzyskać dodatkowe bezpieczeństwo, kod weryfikacyjny zmienia się co 30 sekund, uniemożliwiając innej osobie wielokrotne używanie kodu.

## <a name="add-your-google-account"></a>Dodawanie konta Google

1. Na komputerze wybierz pozycję **Nie można skanować it** ze strony **Konfigurowanie wystawcy uwierzytelniacza** z kodem QR.

    Zostanie wyświetlona strona **Nie można zeskanować kodów kreskowych** z tajnym kodem. Zachowaj tę stronę otwartą, aby można było zobaczyć tajny kod.

2. Otwórz aplikację Microsoft Authenticator, wybierz pozycję **Dodaj konto** z **ikony Dostosuj i kontroluj** w prawym górnym rogu, wybierz **pozycję Inne konto (Google, Facebook itp.),** a następnie wybierz lub **wprowadź kod ręcznie**.

3. Wprowadź **nazwę konta** (na przykład Google) i wpisz **klucz tajny** z kroku 1, a następnie wybierz pozycję **Zakończ**.

4. Na stronie **Konfigurowanie wystawcy uwierzytelniacza** na komputerze wpisz sześciocyfrowy kod weryfikacyjny podany w aplikacji dla swojego konta Google, a następnie wybierz pozycję **Zweryfikuj**.

    Na ekranie **Konta** w aplikacji wyświetlana jest nazwa konta i sześciocyfrowy kod weryfikacyjny. Aby uzyskać dodatkowe bezpieczeństwo, kod weryfikacyjny zmienia się co 30 sekund, uniemożliwiając innej osobie wielokrotne używanie kodu.

    >[!NOTE]
    >Aby uzyskać więcej informacji na temat weryfikacji dwuskładnikowej i konta Google, zobacz [Włączanie weryfikacji dwuetapowej](https://support.google.com/accounts/answer/185839) i [dowiedz się więcej o weryfikacji dwuetapowej.](https://www.google.com/landing/2step/help.html)

## <a name="add-your-facebook-account"></a>Dodawanie konta na Facebooku

1. Na stronie **Konfigurowanie za pośrednictwem funkcji uwierzytelniającego strony innej** firmy, która zawiera kod QR i kod zapisany do wprowadzenia do aplikacji. Zachowaj otwartą stronę, aby zobaczyć kod.

2. Otwórz aplikację Microsoft Authenticator, wybierz pozycję **Dodaj konto** z **ikony Dostosuj i kontroluj** w prawym górnym rogu, wybierz **pozycję Inne konto (Google, Facebook itp.),** a następnie wybierz lub **wprowadź kod ręcznie**.

3. Wprowadź **nazwę konta** (na przykład Facebook) i wpisz **klucz tajny** z kroku 1, a następnie wybierz pozycję **Zakończ**.

4. Na stronie **Uwierzytelniacz dwuskładnikowy** na komputerze wpisz sześciocyfrowy kod weryfikacyjny podany w aplikacji dla swojego konta na Facebooku, a następnie wybierz pozycję **Zweryfikuj**.

    Na ekranie **Konta** w aplikacji wyświetlana jest nazwa konta i sześciocyfrowy kod weryfikacyjny. Aby uzyskać dodatkowe bezpieczeństwo, kod weryfikacyjny zmienia się co 30 sekund, uniemożliwiając innej osobie wielokrotne używanie kodu.

    >[!NOTE]
    >Aby uzyskać więcej informacji na temat weryfikacji dwuskładnikowej i konta na Facebooku, zobacz [Co to jest uwierzytelnianie dwuskładnikowe i jak działa?](https://www.facebook.com/help/148233965247823).

## <a name="add-your-amazon-account"></a>Dodaj swoje konto Amazon

Możesz dodać swoje konto Amazon, włączając weryfikację dwuskładnikową, a następnie dodając konto do aplikacji.

1. Na komputerze wybierz pozycję **Nie można zeskanować kodu kreskowego** na stronie **Wybierz sposób otrzymywania kodów** z kodem QR.

    Nie **można zeskanować** komunikat kod kreskowy pojawi się z tajnym kodem. Zachowaj tę wiadomość otwartą, aby można było zobaczyć tajny kod.

2. Otwórz aplikację Microsoft Authenticator, wybierz pozycję **Dodaj konto** z **ikony Dostosuj i kontroluj** w prawym górnym rogu, wybierz **pozycję Inne konto (Google, Facebook itp.),** a następnie wybierz lub **wprowadź kod ręcznie**.

3. Wprowadź **nazwę konta** (na przykład Amazon) i wpisz **klucz tajny** z kroku 1, a następnie wybierz pozycję **Zakończ**.

4. Ukończ pozostałą część procesu rejestracji, w tym dodanie metody weryfikacji kopii zapasowej, takiej jak wiadomość tekstowa, a następnie wybierz pozycję **Wyślij kod**.

5. Na stronie **Dodaj metodę weryfikacji kopii zapasowej** na komputerze wpisz sześciocyfrowy kod weryfikacyjny dostarczony przez metodę weryfikacji kopii zapasowej dla swojego konta Amazon, a następnie wybierz pozycję **Zweryfikuj kod i kontynuuj**.

6. Na stronie Prawie gotowe zdecyduj, czy komputer ma być zaufanym urządzeniem, a następnie wybierz pozycję **Mam. Włącz weryfikację dwuetapową**.

    Zostanie **wyświetlona** strona Zaawansowane ustawienia zabezpieczeń z zaktualizowanymi danymi weryfikacji dwuskładnikowej.

    >[!NOTE]
    >Aby uzyskać więcej informacji na temat weryfikacji dwuskładnikowej i konta Amazon, zobacz [Informacje o weryfikacji dwuetapowej](https://www.amazon.com/gp/help/customer/display.html?nodeId=201596330) i [logowaniu się za pomocą weryfikacji dwuetapowej.](https://www.amazon.com/gp/help/customer/display.html?nodeId=201962440)    

## <a name="next-steps"></a>Następne kroki

- Po dodaniu kont do aplikacji możesz zalogować się za pomocą aplikacji Microsoft Authenticator na urządzeniu. Aby uzyskać więcej informacji, zobacz [Logowanie się przy użyciu aplikacji](user-help-auth-app-sign-in.md).

- Jeśli masz problemy z uzyskaniem kodu weryfikacyjnego dla osobistego konta Microsoft, zobacz sekcję **Rozwiązywanie problemów z kodem weryfikacyjnym** w artykule [& kody weryfikacyjne dotyczące zabezpieczeń konta Microsoft.](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes)

- W przypadku urządzeń z systemem iOS można również w chmurze uzyskać zapas y poświadczeń konta i powiązanych ustawień aplikacji, takich jak kolejność kont. Aby uzyskać więcej informacji, zobacz [Tworzenie kopii zapasowych i odzyskiwanie za pomocą aplikacji Microsoft Authenticator](user-help-auth-app-backup-recovery.md).
