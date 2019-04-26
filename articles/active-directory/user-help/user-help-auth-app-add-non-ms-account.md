---
title: Dodawanie kont innych niż Microsoft do aplikacji Microsoft Authenticator — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Jak dodawać konta firmy Microsoft, takich jak Google, Facebook lub GitHub do aplikacji Microsoft Authenticator, aby Weryfikacja two-Factor Authentication.
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
ms.openlocfilehash: e6f94ba30c06fc6975ab212c895cecefe5d383fa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60473910"
---
# <a name="add-your-non-microsoft-accounts"></a>Dodawanie kont innych niż Microsoft
Dodaj konta firmy Microsoft, takich jak Google, Facebook lub GitHub do aplikacji Microsoft Authenticator, aby Weryfikacja two-Factor Authentication. Aplikacja Microsoft Authenticator współdziała z dowolną aplikacją używającą weryfikacji dwuetapowej i dowolnym kontem obsługującym standardy TOTP (Time-based One-time Password).

>[!Important]
>Przed dodaniem konta należy pobrać i zainstalować aplikację Microsoft Authenticator. Jeśli użytkownik jeszcze nie, wykonaj kroki opisane w [pobrać i zainstalować aplikację](user-help-auth-app-download-install.md) artykułu.

## <a name="add-personal-accounts"></a>Dodawanie kont osobistych
Ogólnie rzecz biorąc dla wszystkich kont osobistych, musisz mieć:

1. Zaloguj się do swojego konta, a następnie włączyć weryfikację two-Factor Authentication przy użyciu komputera lub urządzenia.

2. Dodaj konto do aplikacji Microsoft Authenticator. Użytkownik, może zostać poproszona o zeskanować kod QR w ramach tego procesu.

Udostępniamy poniżej proces dla kont usługi Facebook, Google, GitHub i Amazon, ale ten proces jest taki sam dla żadnych innych aplikacji, takich jak usługi Instagram, Netflix lub Adobe.

## <a name="add-your-google-account"></a>Dodaj swoje konto Google
Dodaj swoje konto Google przez włączenie weryfikacji two-Factor Authentication, a następnie dodanie konta do aplikacji.

### <a name="turn-on-two-factor-verification"></a>Włącz weryfikację two-Factor Authentication

1. Na komputerze, przejdź do https://myaccount.google.com/signinoptions/two-step-verification/enroll-welcome, wybierz opcję **wprowadzenie**, a następnie zweryfikować Twoją tożsamość.

2. Wykonaj kroki na stronie, aby włączyć weryfikację dwuetapową dla Twojego osobistego konta Google.

### <a name="add-your-google-account-to-the-app"></a>Dodaj swoje konto Google do aplikacji

1. Na stronie usługi Google na komputerze, przejdź do **Konfigurowanie alternatywnych drugi etap** wybierz pozycję **Konfigurowanie** z **aplikacji Authenticator** sekcji.

2. Na **uzyskiwać kody z aplikacji wystawcy uwierzytelnienia** stronie, wybierz opcję **Android** lub **iPhone** zależnie od typu telefonu, a następnie wybierz **dalej**.

    Otrzymuje kod QR, który służy do automatycznego skojarzyć swoje konto z aplikacji Microsoft Authenticator. Nie zamykaj tego okna.

3. Otwórz aplikację Microsoft Authenticator, wybierz opcję **Dodaj konto** z **dostosowywanie i sterowania** ikonę w prawym górnym, prawo, a następnie wybierz **inne konto (Google, Facebook itd.)** .

4. Umożliwia skanowanie kodu QR za pomocą aparatu urządzenia **Konfigurowanie uwierzytelniania** strony na tym komputerze.

    >[!Note]
    >Jeśli aparat fotograficzny nie działa prawidłowo, można wprowadzić kod QR i adres URL ręcznie.

5. Przegląd **kont** strony aplikacji Microsoft Authenticator na urządzeniu, aby upewnić się, że informacje o Twoim koncie jest po prawej stronie i że jest skojarzony 6 cyfrowy kod weryfikacyjny.

    Dla dodatkowego bezpieczeństwa kod weryfikacyjny zmienia co 30 sekund, uniemożliwiając ktoś przy użyciu kodu wiele razy.

6. Wybierz **dalej** na **Konfigurowanie uwierzytelniania** strony na komputerze, wpisz 6 cyfrowy kod weryfikacyjny w aplikacji przewidziane swoje konto Google, a następnie wybierz **Sprawdź**.

7. Twoje konto zostało zweryfikowane i można wybrać **gotowe** zamknąć **Konfigurowanie uwierzytelniania** strony.

    >[!NOTE]
    >Aby uzyskać więcej informacji na temat weryfikacji two-Factor Authentication i konto Google zobacz [włączyć 2-etapową](https://support.google.com/accounts/answer/185839) i [Dowiedz się więcej o 2-etapową](https://www.google.com/landing/2step/help.html).

## <a name="add-your-facebook-account"></a>Dodaj swoje konto usługi Facebook
Przez włączenie weryfikacji two-Factor Authentication, a następnie dodanie konta do aplikacji, należy dodać konta w serwisie Facebook.

### <a name="turn-on-two-factor-verification"></a>Włącz weryfikację two-Factor Authentication

1. Na komputerze, należy otworzyć usługi Facebook, wybierz menu rozwijane w prawym górnym rogu, a następnie przejdź do **ustawienia** > **zabezpieczeń i zaloguj się**.

    **Zabezpieczeń i zaloguj się** zostanie wyświetlona strona.

2. Przejdź w dół do **uwierzytelnianie dwuskładnikowe** opcji **uwierzytelniania dwuskładnikowego** sekcji, a następnie wybierz **Edytuj**.

    **Uwierzytelniania dwuskładnikowego** zostanie wyświetlona strona.

3. Wybierz **włączyć**.

### <a name="add-your-facebook-account-to-the-app"></a>Dodawanie konta w serwisie Facebook do aplikacji

1. Na stronie usługi Facebook na komputerze przejdź do **Dodaj kopii zapasowej** sekcji, a następnie wybierz **instalacji** z **aplikacji Authentication** obszaru.

    Otrzymuje kod QR, który służy do automatycznego skojarzyć swoje konto z aplikacji Microsoft Authenticator. Nie zamykaj tego okna.

2. Otwórz aplikację Microsoft Authenticator, wybierz opcję **Dodaj konto** z **dostosowywanie i sterowania** ikonę w prawym górnym, prawo, a następnie wybierz **inne konto (Google, Facebook itd.)** .

3. Umożliwia skanowanie kodu QR za pomocą aparatu urządzenia **uwierzytelniania dwuskładnikowego** strony na tym komputerze.

    >[!Note]
    >Jeśli aparat fotograficzny nie działa prawidłowo, można wprowadzić kod QR i adres URL ręcznie.

4. Przegląd **kont** strony aplikacji Microsoft Authenticator na urządzeniu, aby upewnić się, że informacje o Twoim koncie jest po prawej stronie i że jest skojarzony 6 cyfrowy kod weryfikacyjny.

    Dla dodatkowego bezpieczeństwa kod weryfikacyjny zmienia co 30 sekund, uniemożliwiając ktoś przy użyciu kodu wiele razy.

5. Wybierz **dalej** na **uwierzytelniania dwuskładnikowego** strony na komputerze, a następnie wpisz 6 cyfrowy kod weryfikacyjny w aplikacji przewidziane Twojego konta serwisu Facebook.

    Twoje konto zostało zweryfikowane, a aplikacji mogą teraz używać, aby zweryfikować swoje konto.

    >[!NOTE]
    >Aby uzyskać więcej informacji na temat weryfikacji two-Factor Authentication i Twojego konta serwisu Facebook, zobacz [co to jest uwierzytelnianie dwuskładnikowe i jak to działa?](https://www.facebook.com/help/148233965247823).

## <a name="add-your-github-account"></a>Dodawanie konta usługi GitHub
Dodaj swoje konto usługi GitHub przez włączenie weryfikacji two-Factor Authentication, a następnie dodanie konta do aplikacji.

### <a name="turn-on-two-factor-verification"></a>Włącz weryfikację two-Factor Authentication

1. Na komputerze, należy otworzyć usługi GitHub, wybierz obraz w prawym górnym rogu, a następnie wybierz **ustawienia**.

    **Uwierzytelniania dwuskładnikowego** zostanie wyświetlona strona.

2. Wybierz **zabezpieczeń** z **ustawienia osobiste** pasku bocznym, a następnie wybierz **Włączanie uwierzytelniania dwuskładnikowego** z **uwierzytelniania dwuskładnikowego**  obszaru.

### <a name="add-your-github-account-to-the-app"></a>Dodawanie konta usługi GitHub do aplikacji

1. Na **uwierzytelniania dwuskładnikowego** strony na komputerze, wybierz opcję **przy użyciu aplikacji**.

2. Zapisz swoje kody odzyskiwania, dzięki czemu możesz wrócić do swojego konta w przypadku utraty dostępu, a następnie wybierz **dalej**. 

    Można zapisać Twoje kody, pobierając je do urządzenia, wydrukowanie lub kopiując je do narzędzia Menedżer haseł.

3. Na **uwierzytelniania dwuskładnikowego** wybierz opcję **przy użyciu aplikacji**.

    Zmiany strony dowiesz się, kod QR. Nie zamykaj tej strony.

4. Otwórz aplikację Microsoft Authenticator, wybierz opcję **Dodaj konto** z **dostosowywanie i sterowania** ikonę w prawym, wybierz opcję **inne konto (Google, Facebook itd.)** , a następnie wybierz pozycję **wprowadź ten kod tekstu** z tekstu w górnej części strony.

    Nie można zeskanować kod QR, dlatego należy ręcznie wprowadzić kod aplikacji Microsoft Authenticator.

5. Wprowadź **nazwa konta** (na przykład GitHub) i wpisz **klucz tajny** z kroku 4, a następnie wybierz **Zakończ**.

4. Na **uwierzytelniania dwuskładnikowego** strony na komputerze, wpisz 6 cyfrowy kod weryfikacyjny w aplikacji przewidziane Twojego konta usługi GitHub, a następnie wybierz pozycję **Włącz**.

    **Kont** strona aplikacji zawiera nazwę konta i 6 cyfrowy kod weryfikacyjny. Dla dodatkowego bezpieczeństwa kod weryfikacyjny zmienia co 30 sekund, uniemożliwiając ktoś przy użyciu kodu wiele razy.

    >[!NOTE]
    >Aby uzyskać więcej informacji na temat weryfikacji two-Factor Authentication i konta usługi GitHub, zobacz [o uwierzytelnianie dwuskładnikowe](https://help.github.com/articles/about-two-factor-authentication/).

## <a name="add-your-amazon-account"></a>Dodaj swoje konto Amazon
Dodaj swoje konto Amazon przez włączenie weryfikacji two-Factor Authentication, a następnie dodanie konta do aplikacji.

### <a name="turn-on-two-factor-verification"></a>Włącz weryfikację two-Factor Authentication

1. Na komputerze otwórz Amazon, wybierz **konta & listy** menu rozwijanego, a następnie wybierz **konta**.

2. Wybierz **logowania i bezpieczeństwo**, zaloguj się do swojego konta Amazon, a następnie wybierz **Edytuj** w **Zaawansowane ustawienia zabezpieczeń** obszaru.

    **Zaawansowane ustawienia zabezpieczeń** zostanie wyświetlona strona.

3. Wybierz **wprowadzenie**.

4. Wybierz **aplikacji Authenticator** z **wybierz, jak otrzymasz kodów** strony.

    Zmiany strony dowiesz się, kod QR. Nie zamykaj tej strony.

5. Otwórz aplikację Microsoft Authenticator, wybierz opcję **Dodaj konto** z **dostosowywanie i sterowania** ikonę w prawym górnym, prawo, a następnie wybierz **inne konto (Google, Facebook itd.)** .

6. Umożliwia skanowanie kodu QR za pomocą aparatu urządzenia **wybierz, jak otrzymasz kodów** strony na tym komputerze.

    >[!Note]
    >Jeśli aparat fotograficzny nie działa prawidłowo, można wprowadzić kod QR i adres URL ręcznie.

5. Przegląd **kont** strony aplikacji Microsoft Authenticator na urządzeniu, aby upewnić się, że informacje o Twoim koncie jest po prawej stronie i że jest skojarzony 6 cyfrowy kod weryfikacyjny.

    Dla dodatkowego bezpieczeństwa kod weryfikacyjny zmienia co 30 sekund, uniemożliwiając ktoś przy użyciu kodu wiele razy.

6. Na **wybierz, jak otrzymasz kodów** strony na komputerze, wpisz 6 cyfrowy kod weryfikacyjny podane w aplikacji konta Amazon, a następnie wybierz **Sprawdź kod i Kontynuuj**.

7. Wykonaj pozostałe procesu rejestracji, takie jak dodawanie metody weryfikacji kopii zapasowej, takie jak wiadomości SMS, a następnie wybierz pozycję **Wyślij kod**.

8. Na **Dodaj metodę weryfikacji kopii zapasowej** strony na komputerze, wpisz 6 cyfrowy kod weryfikacyjny dostarczone przez metodę weryfikacji kopii zapasowej dla swojego konta Amazon, a następnie wybierz **Sprawdź kod i Kontynuuj**.

9. Almost strony, zdecyduj, czy komputer stał się zaufanego urządzenia, a następnie wybierz **rozumiem. Włącz weryfikację dwuetapową**.

    **Zaawansowane ustawienia zabezpieczeń** zostanie wyświetlona strona, wyświetlanie szczegółów dotyczących zaktualizowane weryfikacji two-Factor Authentication.

    >[!NOTE]
    >Aby uzyskać więcej informacji na temat weryfikacji two-Factor Authentication i konto Amazon, zobacz [o weryfikację dwuetapową](https://www.amazon.com/gp/help/customer/display.html?nodeId=201596330) i [logujesz się przy użyciu weryfikacji dwuetapowej](https://www.amazon.com/gp/help/customer/display.html?nodeId=201962440).


## <a name="next-steps"></a>Kolejne kroki

- Po dodaniu konta do aplikacji, można zalogować się przy użyciu aplikacji Authenticator na urządzeniu. Aby uzyskać więcej informacji, zobacz [Zaloguj się przy użyciu aplikacji](user-help-auth-app-sign-in.md).

- Dla urządzeń z systemem iOS można również wykonać kopię zapasową poświadczeń konta usługi i powiązane ustawienia aplikacji, takie jak kolejność Twoje konta z chmurą. Aby uzyskać więcej informacji, zobacz [tworzenia kopii zapasowych i odzyskiwanie przy użyciu aplikacji Microsoft Authenticator](user-help-auth-app-backup-recovery.md).
