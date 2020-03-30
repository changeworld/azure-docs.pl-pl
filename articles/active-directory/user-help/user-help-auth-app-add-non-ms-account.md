---
title: Dodawanie kont innych firm do aplikacji Microsoft Authenticator — Azure AD
description: Dodaj konta inne niż Microsoft, na przykład dla Google lub Facebooka, do aplikacji Microsoft Authenticator, aby zweryfikować twoją tożsamość podczas korzystania z weryfikacji dwuskładnikowej.
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
ms.openlocfilehash: 8650d0170e8ff910140e2b432dd1c998d19e72d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063955"
---
# <a name="add-non-microsoft-accounts-to-the-microsoft-authenticator-app"></a>Dodawanie kont innych firm do aplikacji Microsoft Authenticator

Dodaj swoje konta inne niż Microsoft, takie jak Google, Facebook lub GitHub, do aplikacji Microsoft Authenticator w celu weryfikacji dwuskładnikowej. Aplikacja Microsoft Authenticator współdziała z dowolną aplikacją używającą weryfikacji dwuetapowej i dowolnym kontem obsługującym standardy TOTP (Time-based One-time Password).

>[!Important]
>Aby można było dodać konto, należy pobrać i zainstalować aplikację Microsoft Authenticator. Jeśli jeszcze tego nie zrobiono, wykonaj czynności opisane w artykule [Pobierz i zainstaluj aplikację.](user-help-auth-app-download-install.md)

## <a name="add-personal-accounts"></a>Dodawanie kont osobistych

Ogólnie rzecz biorąc, dla wszystkich kont osobistych, musisz:

1. Zaloguj się na swoje konto, a następnie włącz weryfikację dwuskładnikową przy użyciu urządzenia lub komputera.

2. Dodaj konto do aplikacji Microsoft Authenticator. W ramach tego procesu może zostać poproszony o zeskanowanie kodu QR.

    >[!Note]
    >Jeśli po raz pierwszy konfigurujesz aplikację Microsoft Authenticator, może pojawić się monit z pytaniem, czy zezwolić aplikacji na dostęp do aparatu (iOS), czy zezwolić aplikacji na robienia zdjęć i nagrywania wideo (Android). Musisz wybrać **opcję Zezwalaj,** aby aplikacja uwierzytelniającego mogła uzyskać dostęp do aparatu, aby zrobić zdjęcie kodu QR w następnym kroku. Jeśli nie zezwolisz na aparat, nadal możesz skonfigurować aplikację uwierzytelniacza, ale musisz ręcznie dodać informacje o kodzie. Aby uzyskać informacje dotyczące ręcznego dodawania kodu, zobacz [Ręczne dodawanie konta do aplikacji](user-help-auth-app-add-account-manual.md).

Udostępniamy tutaj proces dla twoich kont Na Facebooku, Google, GitHub i Amazon, ale ten proces jest taki sam dla każdej innej aplikacji, takiej jak Instagram, Netflix lub Adobe.

## <a name="add-your-google-account"></a>Dodawanie konta Google

Dodaj swoje konto Google, włączając weryfikację dwuskładnikową, a następnie dodając konto do aplikacji.

### <a name="turn-on-two-factor-verification"></a>Włączanie weryfikacji dwuskładnikowej

1. Na komputerze przejdź https://myaccount.google.com/signinoptions/two-step-verification/enroll-welcomedo , wybierz pozycję **Wprowadzenie**, a następnie zweryfikuj swoją tożsamość.

2. Wykonaj czynności na stronie, aby włączyć weryfikację dwuetapową dla osobistego konta Google.

### <a name="add-your-google-account-to-the-app"></a>Dodawanie konta Google do aplikacji

1. Na stronie Google na komputerze przejdź do sekcji **Konfigurowanie alternatywnego drugiego kroku,** wybierz pozycję **Konfigurowanie** w sekcji **Aplikacja Authenticator.**

2. Na stronie **Pobierz kody z aplikacji Authenticator** wybierz **pozycję Android** lub **iPhone** na podstawie typu telefonu, a następnie wybierz pozycję **Dalej**.

    Otrzymujesz kod QR, którego możesz użyć do automatycznego skojarzenia konta z aplikacją Microsoft Authenticator. Nie zamykaj tego okna.

3. Otwórz aplikację Microsoft Authenticator, wybierz pozycję **Dodaj konto** z **ikony Dostosuj i kontroluj** w prawym górnym rogu, a następnie wybierz **pozycję Inne konto (Google, Facebook itp.).**

4. Użyj aparatu urządzenia, aby zeskanować kod QR ze strony **Konfigurowanie authenticatora** na komputerze.

    >[!Note]
    >Jeśli aparat nie działa prawidłowo, możesz ręcznie wprowadzić kod QR i adres URL.

5. Przejrzyj stronę **Konta** w aplikacji Microsoft Authenticator na urządzeniu, aby upewnić się, że informacje o koncie są odpowiednie i że istnieje skojarzony sześciocyfrowy kod weryfikacyjny.

    Aby uzyskać dodatkowe bezpieczeństwo, kod weryfikacyjny zmienia się co 30 sekund, uniemożliwiając innej osobie wielokrotne używanie kodu.

6. **Wybierz dalej** na stronie **Konfigurowanie wystawcy uwierzytelniania** na komputerze wpisz sześciocyfrowy kod weryfikacyjny podany w aplikacji dla swojego konta Google, a następnie wybierz pozycję **Zweryfikuj**.

7. Twoje konto zostało zweryfikowane i możesz wybrać **opcję Gotowe,** aby zamknąć stronę **Konfigurowanie wystawcy uwierzytelnienia.**

    >[!NOTE]
    >Aby uzyskać więcej informacji na temat weryfikacji dwuskładnikowej i konta Google, zobacz [Włączanie weryfikacji dwuetapowej](https://support.google.com/accounts/answer/185839) i [dowiedz się więcej o weryfikacji dwuetapowej.](https://www.google.com/landing/2step/help.html)

## <a name="add-your-facebook-account"></a>Dodawanie konta na Facebooku

Dodaj swoje konto na Facebooku, włączając weryfikację dwuskładnikową, a następnie dodając konto do aplikacji.

### <a name="turn-on-two-factor-verification"></a>Włączanie weryfikacji dwuskładnikowej

1. Na komputerze otwórz facebooka, wybierz menu rozwijane w prawym górnym rogu, a następnie przejdź do **pozycji Ustawienia** > **zabezpieczeń i logowania**.

    Zostanie wyświetlona strona **Zabezpieczenia i logowanie.**

2. Przejdź do opcji **Użyj uwierzytelniania dwuskładnikowego** w sekcji **Uwierzytelnianie dwuskładnikowe,** a następnie wybierz pozycję **Edytuj**.

    Zostanie wyświetlona strona **Uwierzytelnianie dwuskładnikowe.**

3. Wybierz **włącz**.

### <a name="add-your-facebook-account-to-the-app"></a>Dodawanie konta na Facebooku do aplikacji

1. Na stronie Facebook na komputerze przejdź do sekcji **Dodawanie kopii zapasowej,** a następnie wybierz pozycję **Instalator** z obszaru **aplikacji Uwierzytelnianie.**

    Otrzymujesz kod QR, którego możesz użyć do automatycznego skojarzenia konta z aplikacją Microsoft Authenticator. Nie zamykaj tego okna.

2. Otwórz aplikację Microsoft Authenticator, wybierz pozycję **Dodaj konto** z **ikony Dostosuj i kontroluj** w prawym górnym rogu, a następnie wybierz **pozycję Inne konto (Google, Facebook itp.).**

3. Użyj aparatu urządzenia, aby zeskanować kod QR ze strony **Uwierzytelnianie dwuskładnikowe** na komputerze.

    >[!Note]
    >Jeśli aparat nie działa prawidłowo, możesz ręcznie wprowadzić kod QR i adres URL.

4. Przejrzyj stronę **Konta** w aplikacji Microsoft Authenticator na urządzeniu, aby upewnić się, że informacje o koncie są odpowiednie i że istnieje skojarzony sześciocyfrowy kod weryfikacyjny.

    Aby uzyskać dodatkowe bezpieczeństwo, kod weryfikacyjny zmienia się co 30 sekund, uniemożliwiając innej osobie wielokrotne używanie kodu.

5. Wybierz **pozycję Dalej** na stronie **Uwierzytelnianie dwuskładnikowe** na komputerze, a następnie wpisz sześciocyfrowy kod weryfikacyjny podany w aplikacji dla swojego konta na Facebooku.

    Twoje konto zostało zweryfikowane i możesz teraz użyć aplikacji do weryfikacji konta.

    >[!NOTE]
    >Aby uzyskać więcej informacji na temat weryfikacji dwuskładnikowej i konta na Facebooku, zobacz [Co to jest uwierzytelnianie dwuskładnikowe i jak działa?](https://www.facebook.com/help/148233965247823).

## <a name="add-your-github-account"></a>Dodawanie konta GitHub

Dodaj swoje konto GitHub, włączając weryfikację dwuskładnikową, a następnie dodając konto do aplikacji.

### <a name="turn-on-two-factor-verification"></a>Włączanie weryfikacji dwuskładnikowej

1. Na komputerze otwórz gitHub, wybierz obraz z prawego górnego rogu, a następnie wybierz pozycję **Ustawienia**.

    Zostanie wyświetlona strona **Uwierzytelnianie dwuskładnikowe.**

2. Wybierz **pozycję Zabezpieczenia** na pasku bocznym Ustawień **osobistych,** a następnie wybierz pozycję Włącz **uwierzytelnianie dwuskładnikowe** z obszaru **uwierzytelniania dwuskładnikowego.**

### <a name="add-your-github-account-to-the-app"></a>Dodawanie konta GitHub do aplikacji

1. Na stronie **Uwierzytelnianie dwuskładnikowe** na komputerze wybierz pozycję **Konfigurowanie przy użyciu aplikacji**.

2. Zapisz kody odzyskiwania, aby można było wrócić na swoje konto w przypadku utraty dostępu, a następnie wybrać **pozycję Dalej**. 

    Kody można zapisać, pobierając je na urządzenie, drukując wydruk lub kopiując je do narzędzia do menedżera hasła.

3. Na stronie **Uwierzytelnianie dwuskładnikowe** wybierz pozycję **Skonfiguruj przy użyciu aplikacji**.

    Strona zmieni się, aby wyświetlić kod QR. Nie zamykaj tej strony.

4. Otwórz aplikację Microsoft Authenticator, wybierz pozycję **Dodaj konto** z **ikony Dostosuj i kontroluj** w prawym górnym rogu, wybierz **pozycję Inne konto (Google, Facebook itp.),** a następnie wybierz pozycję **Wprowadź ten kod tekstowy** z tekstu u góry strony.

    Aplikacja Microsoft Authenticator nie może zeskanować kodu QR, dlatego należy ręcznie wprowadzić kod.

5. Wprowadź **nazwę konta** (na przykład GitHub) i wpisz **klucz tajny** z kroku 4, a następnie wybierz pozycję **Zakończ**.

6. Na stronie **Dwuskładnikowy wystawca uwierzytelniania** na komputerze wpisz sześciocyfrowy kod weryfikacyjny podany w aplikacji dla swojego konta GitHub, a następnie wybierz pozycję **Włącz**.

    Na stronie **Konta** w aplikacji jest wyświetlana nazwa konta i sześciocyfrowy kod weryfikacyjny. Aby uzyskać dodatkowe bezpieczeństwo, kod weryfikacyjny zmienia się co 30 sekund, uniemożliwiając innej osobie wielokrotne używanie kodu.

    >[!NOTE]
    >Aby uzyskać więcej informacji na temat weryfikacji dwuskładnikowej i konta GitHub, zobacz [Informacje o uwierzytelnianiu dwuskładnikowym](https://help.github.com/articles/about-two-factor-authentication/).

## <a name="add-your-amazon-account"></a>Dodaj swoje konto Amazon

Dodaj swoje konto Amazon, włączając weryfikację dwuskładnikową, a następnie dodając konto do aplikacji.

### <a name="turn-on-two-factor-verification"></a>Włączanie weryfikacji dwuskładnikowej

1. Na komputerze otwórz program Amazon, wybierz menu rozwijane **Konto & Listy,** a następnie wybierz **pozycję Twoje konto**.

2. Wybierz **pozycję Zaloguj & zabezpieczenia**, zaloguj się na swoje konto Amazon, a następnie wybierz pozycję **Edytuj** w obszarze Zaawansowane **ustawienia zabezpieczeń.**

    Zostanie wyświetlona strona **Zaawansowane ustawienia zabezpieczeń.**

3. Wybierz **pozycję Wprowadzenie**.

4. Wybierz **aplikację Authenticator** ze strony **Wybierz sposób otrzymywania kodów.**

    Strona zmieni się, aby wyświetlić kod QR. Nie zamykaj tej strony.

5. Otwórz aplikację Microsoft Authenticator, wybierz pozycję **Dodaj konto** z **ikony Dostosuj i kontroluj** w prawym górnym rogu, a następnie wybierz **pozycję Inne konto (Google, Facebook itp.).**

6. Użyj aparatu urządzenia, aby zeskanować kod QR ze strony **Wybierz sposób otrzymywania kodów** na komputerze.

    >[!Note]
    >Jeśli aparat nie działa prawidłowo, możesz ręcznie wprowadzić kod QR i adres URL.

7. Przejrzyj stronę **Konta** w aplikacji Microsoft Authenticator na urządzeniu, aby upewnić się, że informacje o koncie są odpowiednie i że istnieje skojarzony sześciocyfrowy kod weryfikacyjny.

    Aby uzyskać dodatkowe bezpieczeństwo, kod weryfikacyjny zmienia się co 30 sekund, uniemożliwiając innej osobie wielokrotne używanie kodu.

8. Na stronie **Wybieranie sposobu otrzymywania kodów** na komputerze wpisz sześciocyfrowy kod weryfikacyjny podany w aplikacji dla swojego konta Amazon, a następnie wybierz pozycję **Zweryfikuj kod i kontynuuj**.

9. Ukończ pozostałą część procesu rejestracji, w tym dodanie metody weryfikacji kopii zapasowej, takiej jak wiadomość tekstowa, a następnie wybierz pozycję **Wyślij kod**.

10. Na stronie **Dodaj metodę weryfikacji kopii zapasowej** na komputerze wpisz sześciocyfrowy kod weryfikacyjny dostarczony przez metodę weryfikacji kopii zapasowej dla swojego konta Amazon, a następnie wybierz pozycję **Zweryfikuj kod i kontynuuj**.

11. Na stronie **Prawie gotowe** zdecyduj, czy komputer ma być zaufanym urządzeniem, a następnie wybierz pozycję **Mam. Włącz weryfikację dwuetapową**.

    Zostanie **wyświetlona** strona Zaawansowane ustawienia zabezpieczeń z zaktualizowanymi danymi weryfikacji dwuskładnikowej.

    >[!NOTE]
    >Aby uzyskać więcej informacji na temat weryfikacji dwuskładnikowej i konta Amazon, zobacz [Informacje o weryfikacji dwuetapowej](https://www.amazon.com/gp/help/customer/display.html?nodeId=201596330) i [logowaniu się za pomocą weryfikacji dwuetapowej.](https://www.amazon.com/gp/help/customer/display.html?nodeId=201962440)

## <a name="next-steps"></a>Następne kroki

- Po dodaniu kont do aplikacji możesz zalogować się za pomocą aplikacji Authenticator na urządzeniu. Aby uzyskać więcej informacji, zobacz [Logowanie się przy użyciu aplikacji](user-help-auth-app-sign-in.md).

- W przypadku urządzeń z systemem iOS można również w chmurze uzyskać zapas y poświadczeń konta i powiązanych ustawień aplikacji, takich jak kolejność kont. Aby uzyskać więcej informacji, zobacz [Tworzenie kopii zapasowych i odzyskiwanie za pomocą aplikacji Microsoft Authenticator](user-help-auth-app-backup-recovery.md).
