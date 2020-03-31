---
title: Twórz zapasy i odzyskuj konta za pomocą aplikacji Microsoft Authenticator — Azure AD
description: Dowiedz się, jak zrobić kopię zapasową i odzyskać poświadczenia kopii zapasowej konta za pomocą aplikacji Microsoft Authenticator.
services: active-directory
author: curtand
manager: daveba
ms.subservice: user-help
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 2ac761355fc528fe3e4b4b5f05ee0a6e317ff99a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78298000"
---
# <a name="back-up-and-recover-account-credentials-using-the-microsoft-authenticator-app"></a>Tworzenie kopii zapasowych i odzyskiwanie poświadczeń konta przy użyciu aplikacji Microsoft Authenticator

**Dotyczy:**

- urządzenia z systemem iOS z systemem 5.7.0 lub nowszym

- Urządzenia z Androidem z systemem 6.6.0 lub nowszym

Aplikacja Microsoft Authenticator łączy w sobie poświadczenia konta i powiązane ustawienia aplikacji, takie jak kolejność kont, w chmurze. Po wykonaniu kopii zapasowej można również użyć aplikacji do odzyskania informacji na nowym urządzeniu, potencjalnie unikając zablokowania lub konieczności ponownego tworzenia kont.

Każda lokalizacja magazynu kopii zapasowej wymaga jednego osobistego konta Microsoft, podczas gdy system iOS wymaga również konta iCloud. W tej jednej lokalizacji może być przechowywanych wiele kont. Możesz na przykład mieć konto osobiste, konto służbowe lub szkolne oraz osobiste konto inne niż Microsoft, takie jak facebook, Google itd.

> [!IMPORTANT]
> Przechowywane są tylko dane osobowe i dane uwierzytelniające do konta innej firmy, które obejmują nazwę użytkownika i kod weryfikacyjny konta, który jest wymagany do udowodnienia Twojej tożsamości. Nie przechowujemy żadnych innych informacji powiązanych z Twoimi kontami, w tym wiadomości e-mail ani plików. Nie kojarzymy również ani nie udostępniamy Twoich kont w żaden sposób ani z żadnym innym produktem lub usługą. I wreszcie, twój administrator IT nie otrzyma żadnych informacji o żadnym z tych kont.

## <a name="back-up-your-account-credentials"></a>Czeliczenie kopii zapasowych danych logowania do konta

Aby można było mieć możliwość utworzenia kopii zapasowej poświadczeń, musisz mieć:

- Osobiste [konto Microsoft](https://account.microsoft.com/account) działa jako konto odzyskiwania.

- **Tylko w przypadku systemu iOS** musisz mieć [konto iCloud](https://www.icloud.com/) dla rzeczywistej lokalizacji magazynu.

### <a name="to-turn-on-cloud-backup-for-ios-devices"></a>Aby włączyć tworzenie kopii zapasowych w chmurze dla urządzeń z systemem iOS

- Na urządzeniu z systemem iOS wybierz pozycję **Ustawienia**, wybierz **pozycję Kopia zapasowa**, a następnie włącz **kopię zapasową iCloud**.

    Dane uwierzytelniające konta są archiwizowane na koncie iCloud.

    ![Ekran ustawień systemu iOS z wyświetlaniem ustawień kopii zapasowej iCloud](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on.png)

### <a name="to-turn-on-cloud-backup-for-android-devices"></a>Aby włączyć tworzenie kopii zapasowych w chmurze dla urządzeń z systemem Android

- Na urządzeniu z Androidem wybierz **pozycję Ustawienia**, wybierz pozycję **Kopia zapasowa**, a następnie włącz **kopię zapasową w chmurze**.

    Poświadczenia konta są archiwizowane na koncie w chmurze.

    ![Ekran ustawień systemu Android z wyświetlaniem lokalizacji ustawień kopii zapasowej](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on-android.png)

## <a name="recover-your-account-credentials-on-your-new-device"></a>Odzyskiwanie danych logowania do konta na nowym urządzeniu

Poświadczenia konta można odzyskać z konta w chmurze, ale najpierw upewnij się, że odzyskane konto nie istnieje w aplikacji Microsoft Authenticator. Jeśli na przykład odzyskiwasz osobiste konto Microsoft, upewnij się, że nie masz już osobistego konta Microsoft skonfigurowanego w aplikacji uwierzytelniającego. Ta kontrola jest ważna, więc możemy mieć pewność, że nie nadpisujemy ani nie wymazyjemy istniejącego konta przez pomyłkę.

### <a name="to-recover-your-information"></a>Aby odzyskać informacje

1. Na urządzeniu przenośnym otwórz aplikację Microsoft Authenticator i wybierz pozycję **Rozpocznij odzyskiwanie** od dołu ekranu.

    ![Aplikacja Microsoft Authenticator, pokazująca, gdzie kliknąć przycisk Rozpocznij odzyskiwanie](./media/user-help-auth-app-backup-recovery/backup-and-recovery-begin-recovery.png)

2. Zaloguj się do konta odzyskiwania przy użyciu tego samego osobistego konta Microsoft, którego użyto podczas procesu tworzenia kopii zapasowej.

    Poświadczenia konta zostaną odzyskane na nowym urządzeniu.

Po zakończeniu odzyskiwania można zauważyć, że osobiste kody weryfikacyjne konta Microsoft w aplikacji Microsoft Authenticator różnią się między starymi i nowymi telefonami. Kody są różne, ponieważ każde urządzenie ma własne unikatowe poświadczenia, ale oba są prawidłowe i działają podczas logowania się przy użyciu skojarzonego telefonu.

## <a name="recover-additional-accounts-requiring-more-verification"></a>Odzyskiwanie dodatkowych kont wymagających większej weryfikacji

Jeśli korzystasz z powiadomień push na kontach osobistych lub służbowych, otrzymasz alert na ekranie z informacją, że musisz podać dodatkową weryfikację, zanim odzyskasz swoje informacje. Ponieważ powiadomienia wypychane wymagają użycia poświadczeń, które są powiązane z określonym urządzeniem i nigdy nie są wysyłane za pośrednictwem sieci, należy udowodnić swoją tożsamość przed utworzeniem poświadczeń na urządzeniu.

W przypadku osobistych kont Microsoft możesz udowodnić swoją tożsamość, wprowadzając hasło wraz z alternatywnym numerem e-mail lub numerem telefonu. W przypadku kont służbowych należy zeskanować kod QR podany przez dostawcę konta.

### <a name="to-provide-additional-verification-for-personal-accounts"></a>Zapewnienie dodatkowej weryfikacji kont osobistych

1. Na ekranie **Konta** aplikacji Microsoft Authenticator wybierz konto, które chcesz odzyskać. Na urządzeniu z Androidem wybierz strzałkę obok konta, które chcesz odzyskać.

    ![Aplikacja Microsoft Authenticator, pokazująca dostępne konta ze skojarzonymi strzałkami rozwijanym](./media/user-help-auth-app-backup-recovery/backup-and-recovery-arrow.png)

    Na urządzeniu z systemem iOS naciśnij konto, które chcesz odzyskać, aby otworzyć widok pełnoekranowy konta.

    ![Aplikacja Microsoft Authenticator, pokazująca dostępne konta ze skojarzonymi strzałkami rozwijanym](./media/user-help-auth-app-backup-recovery/ios-select-account-to-recover.png)

2. Zaloguj się, aby odzyskać konto. Na urządzeniu z Androidem wybierz pozycję **Zaloguj się, aby odzyskać**.

    ![Aplikacja Microsoft Authenticator do wprowadzania informacji logowania w systemie Android](./media/user-help-auth-app-backup-recovery/backup-and-recovery-sign-in.png)

    Na urządzeniu z systemem iOS naciśnij kafelek konta, który chcesz odzyskać, a następnie naciśnij opcję logowania i odzyskiwania. Następnie wpisz hasło, a następnie potwierdź swój adres e-mail lub numer telefonu jako dodatkowy plik verification.unt.

    ![Aplikacja Microsoft Authenticator do wprowadzania informacji logowania w systemie iOS](./media/user-help-auth-app-backup-recovery/ios-sign-in-to-recover.png)

### <a name="to-provide-additional-verification-for-work-or-school-accounts"></a>Zapewnienie dodatkowej weryfikacji kont służbowych lub szkolnych

1. Zaloguj się, aby odzyskać konto. Na urządzeniu z Androidem wybierz pozycję **Zaloguj się, aby odzyskać**.

    ![Aplikacja Microsoft Authenticator odzyskuje konto służbowe w systemie Android](./media/user-help-auth-app-backup-recovery/backup-and-recovery-additional-accts.png)

    Na urządzeniu z systemem iOS stuknij konto, które chcesz odzyskać, aby otworzyć widok całego ekranu konta.

    ![Aplikacja Microsoft Authenticator odzyskuje konto służbowe w systemie iOS](./media/user-help-auth-app-backup-recovery/ios-recover-work-or-school-account.png)

2. Możesz użyć kodu QR, aby odzyskać konto. Wybierz **opcję Skanuj kod QR, aby odzyskać,** a następnie zeskanuj kod QR.

    W systemie Android:

    ![Aplikacja Microsoft Authenticator na Androida, umożliwiająca skanowanie kodu QR](./media/user-help-auth-app-backup-recovery/backup-and-recovery-scan-qr-code.png)

    W uos:

    ![Aplikacja Microsoft Authenticator w systemie iOS, umożliwiająca skanowanie kodu QR](./media/user-help-auth-app-backup-recovery/ios-scan-qr-code.png)

    >[!NOTE]
    >Aby uzyskać więcej informacji o kodach QR i jak je uzyskać, zobacz Wprowadzenie do [aplikacji Microsoft Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install) lub [Konfigurowanie informacji zabezpieczających do korzystania z aplikacji uwierzytelniającego](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-auth-app)na podstawie tego, czy administrator włączył informacje zabezpieczające.
    >
    >Jeśli po raz pierwszy konfigurujesz aplikację Microsoft Authenticator, może pojawić się monit z pytaniem, czy zezwolić aplikacji na dostęp do aparatu (iOS), czy zezwolić aplikacji na robienia zdjęć i nagrywania wideo (Android). Musisz wybrać **opcję Zezwalaj,** aby aplikacja uwierzytelniającego mogła uzyskać dostęp do aparatu, aby zrobić zdjęcie kodu QR w następnym kroku. Jeśli nie zezwolisz na aparat, nadal możesz skonfigurować aplikację uwierzytelniacza, ale musisz ręcznie dodać informacje o kodzie. Aby uzyskać informacje dotyczące ręcznego dodawania kodu, zobacz [Ręczne dodawanie konta do aplikacji](user-help-auth-app-add-account-manual.md).

## <a name="troubleshoot-backup-and-recovery-problems"></a>Rozwiązywanie problemów z tworzeniem kopii zapasowych i odzyskiwaniem danych

Istnieje kilka powodów, dla których kopia zapasowa może być niedostępna

- **Zmiana systemów operacyjnych**: Kopia zapasowa jest przechowywana w usłudze iCloud dla systemu iOS oraz w dostawcy pamięci masowej w chmurze firmy Microsoft dla systemu Android. Oznacza to, że kopia zapasowa jest niedostępna, jeśli przełączasz się między urządzeniami z systemem Android i iOS. Jeśli dokonasz zmiany, musisz ręcznie odtworzyć swoje konta w aplikacji Microsoft Authenticator.

- **Problemy z siecią:** jeśli występują problemy związane z siecią, upewnij się, że masz połączenie z siecią i poprawnie zalogowano się na swoje konto.

- **Problemy z kontem:** jeśli występują problemy związane z kontem, upewnij się, że zalogowano się poprawnie na swoje konto. W przypadku systemu iOS oznacza to, że musisz zalogować się do usługi iCloud przy użyciu tego samego konta AppleID co iPhone.

- **Przypadkowe usunięcie:** możliwe, że konto kopii zapasowej zostało usunięte z poprzedniego urządzenia lub podczas zarządzania kontem magazynu w chmurze. W tej sytuacji należy ręcznie odtworzyć konto w aplikacji.

- **Istniejące konta microsoft authenticator:** jeśli konta zostały już skonfigurowane w aplikacji Microsoft Authenticator, aplikacja nie będzie mogła odzyskać kopii zapasowych kont. Zapobieganie odzyskiwania pomaga upewnić się, że dane konta nie są zastępowane nieaktuanych informacji. W tej sytuacji należy usunąć wszystkie istniejące informacje o koncie z istniejących kont skonfigurowane w aplikacji Authenticator, zanim będzie można odzyskać kopię zapasową.

- **Kopia zapasowa jest nieaktualna:** Jeśli informacje o kopii zapasowej są nieaktualne, może zostać poproszony o odświeżenie informacji przez ponowne zalogowanie się na konto Microsoft Recovery. Twoje konto odzyskiwania jest osobistym kontem Microsoft używanym początkowo do przechowywania kopii zapasowej. Jeśli wymagane jest zalogowanie się, na menu lub pasku akcji pojawi się czerwona kropka lub wykrzyknik z monitem o zalogowanie się w celu zakończenia przywracania kopii zapasowej. Po wybraniu ikony zostanie wyświetlony monit o ponowne zalogowanie się w celu zaktualizowania informacji.

## <a name="next-steps"></a>Następne kroki

Po utworzeniu kopii zapasowej i odzyskaniu poświadczeń konta na nowym urządzeniu możesz nadal używać aplikacji Microsoft Authenticator do weryfikacji tożsamości. Aby uzyskać więcej informacji, zobacz [Logowanie się do kont przy użyciu aplikacji Microsoft Authenticator](user-help-sign-in.md).

## <a name="related-articles"></a>Pokrewne artykuły:

- [Czym jest aplikacja Microsoft Authenticator?](user-help-auth-app-overview.md)

- [Microsoft Authenticator app FAQ (Aplikacja Microsoft Authenticator — często zadawane pytania)](user-help-auth-app-faq.md)

- [Uwierzytelnianie wieloskładnikowe](https://docs.microsoft.com/azure/multi-factor-authentication/)
