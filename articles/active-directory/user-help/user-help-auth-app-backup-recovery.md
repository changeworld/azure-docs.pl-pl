---
title: Tworzenie kopii zapasowych i odzyskiwanie przy użyciu aplikacji Microsoft Authenticator Azure Active Directory | Microsoft Docs
description: Dowiedz się, jak tworzyć kopie zapasowe poświadczeń konta i odzyskiwać je przy użyciu aplikacji Microsoft Authenticator.
services: active-directory
author: eross-msft
manager: daveba
ms.subservice: user-help
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: lizross
ms.reviewer: olhaun
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba99001159277d9d221910cafa4f5165ae2bd812
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68880770"
---
# <a name="backup-and-recover-account-credentials-with-the-microsoft-authenticator-app"></a>Tworzenie kopii zapasowej i odzyskiwanie poświadczeń konta za pomocą aplikacji Microsoft Authenticator

**Dotyczy:**

- urządzenia z systemem iOS

Aplikacja Microsoft Authenticator tworzy kopię zapasową poświadczeń konta i powiązanych ustawień aplikacji, takich jak kolejność kont, w chmurze. Po utworzeniu kopii zapasowej można również użyć aplikacji do odzyskania informacji na nowym urządzeniu, co może uniknąć zablokowania lub konieczności ponownego tworzenia kont.

> [!IMPORTANT]
> Dla każdej lokalizacji magazynu kopii zapasowych jest potrzebne jedno osobiste konto Microsoft i jedno konto usługi iCloud. Jednak w tej lokalizacji magazynu można utworzyć kopię zapasową kilku kont. Można na przykład korzystać z konta osobistego, konta szkoły i konta innej firmy, takiego jak Facebook, Google i tak dalej.
>
> Przechowywane są tylko poświadczenia konta osobistego i innych firm, w tym nazwa użytkownika i kod weryfikacyjny konta, który jest wymagany do potwierdzenia tożsamości. Nie przechowujemy żadnych innych informacji skojarzonych z kontami, w tym wiadomości e-mail i plików. Nie kojarzą również ani nie udostępniamy kont w żaden sposób ani z żadnym innym produktem lub usługą. Ponadto administrator IT nie uzyska żadnych informacji o żadnym z tych kont.

## <a name="back-up-your-account-credentials"></a>Tworzenie kopii zapasowej poświadczeń konta

Aby można było utworzyć kopię zapasową poświadczeń, muszą one mieć następujące wartości:

- Osobista [konto Microsoft](https://account.microsoft.com/account) do działania jako konto odzyskiwania.

- [Konto usługi iCloud](https://www.icloud.com/) dla rzeczywistej lokalizacji magazynu.

Wymaganie logowania do obu kont jednocześnie zapewnia lepsze zabezpieczenia informacji o kopii zapasowej.

### <a name="to-turn-on-cloud-backup"></a>Aby włączyć usługę kopia zapasowa w chmurze

- Na urządzeniu z systemem iOS wybierz pozycję **Ustawienia**, wybierz pozycję **kopia zapasowa**, a następnie włącz opcję **kopia zapasowa iCloud**.

    Kopie zapasowe poświadczeń konta są tworzone na koncie usługi iCloud.

    ![ekran ustawień systemu iOS, przedstawiający lokalizację ustawień kopii zapasowych w usłudze iCloud](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on.png)

## <a name="recover-your-account-credentials-on-your-new-device"></a>Odzyskaj poświadczenia konta na nowym urządzeniu

Poświadczenia konta można odzyskać z konta usługi iCloud przy użyciu tego samego konta usługi Microsoft Recovery skonfigurowanego podczas tworzenia kopii zapasowej informacji.

### <a name="to-recover-your-information"></a>Aby odzyskać informacje

1. Na urządzeniu z systemem iOS otwórz aplikację Microsoft Authenticator i wybierz pozycję **Rozpocznij odzyskiwanie** w dolnej części ekranu.

    ![Aplikacja Microsoft Authenticator, pokazująca, gdzie kliknąć przycisk Rozpocznij odzyskiwanie](./media/user-help-auth-app-backup-recovery/backup-and-recovery-begin-recovery.png)

2. Zaloguj się do konta odzyskiwania przy użyciu tego samego osobistego konto Microsoft użytego podczas procesu tworzenia kopii zapasowej.

    Poświadczenia konta zostaną odzyskane do nowego urządzenia.

Po zakończeniu odzyskiwania możesz zauważyć, że własne kody weryfikacyjne konto Microsoft w aplikacji Microsoft Authenticator są różne dla starych i nowych telefonów. Kody są różne, ponieważ każde urządzenie ma własne unikatowe poświadczenia, ale oba są prawidłowe i pracują podczas logowania przy użyciu skojarzonego telefonu.

## <a name="recover-additional-accounts-requiring-more-verification"></a>Odzyskaj dodatkowe konta wymagające większej weryfikacji

Jeśli używasz powiadomień wypychanych z kontami osobistymi, służbowymi lub szkolnymi, otrzymasz alert na ekranie z informacją, że musisz podać dodatkową weryfikację, aby móc odzyskać informacje. Ponieważ powiadomienia wypychane wymagają użycia poświadczeń powiązanych z określonym urządzeniem i nigdy nie są wysyłane przez sieć, przed utworzeniem poświadczeń na urządzeniu należy potwierdzić swoją tożsamość.

W przypadku osobistych kont Microsoft można potwierdzić swoją tożsamość, wprowadzając hasło i alternatywny adres e-mail lub numer telefonu. W przypadku kont służbowych należy zeskanować kod QR przyznany przez dostawcę konta.

### <a name="to-provide-additional-verification-for-personal-accounts"></a>Aby zapewnić dodatkową weryfikację kont osobistych

1. Na ekranie **konta** aplikacji Microsoft Authenticator wybierz strzałkę listy rozwijanej obok konta, które chcesz odzyskać.

    ![Aplikacja Microsoft Authenticator, pokazująca dostępne konta z powiązanymi strzałkami listy rozwijanej](./media/user-help-auth-app-backup-recovery/backup-and-recovery-arrow.png)

2. Wybierz pozycję **Zaloguj, aby odzyskać**, wpisz hasło, a następnie potwierdź swój adres e-mail lub numer telefonu jako dodatkową weryfikację.

    ![Aplikacja Microsoft Authenticator, która umożliwia wprowadzanie informacji dotyczących logowania](./media/user-help-auth-app-backup-recovery/backup-and-recovery-sign-in.png)

### <a name="to-provide-additional-verification-for-work-or-school-accounts"></a>Aby zapewnić dodatkową weryfikację dla kont służbowych

1. Na ekranie **konta** aplikacji Microsoft Authenticator wybierz strzałkę listy rozwijanej obok konta, które chcesz odzyskać.

    ![Aplikacja Microsoft Authenticator, pokazująca dostępne konta z powiązanymi strzałkami listy rozwijanej](./media/user-help-auth-app-backup-recovery/backup-and-recovery-additional-accts.png)

2. Wybierz opcję **Skanuj kod QR do**odzyskania, a następnie Zeskanuj kod QR.

    ![Aplikacja Microsoft Authenticator, dzięki której można skanować kod QR](./media/user-help-auth-app-backup-recovery/backup-and-recovery-scan-qr-code.png)

    >[!NOTE]
    >Jeśli konfigurujesz aplikację Microsoft Authenticator po raz pierwszy, może zostać wyświetlony monit z pytaniem, czy zezwolić aplikacji na dostęp do aparatu (iOS), czy zezwolić aplikacji na wykonywanie zdjęć i nagrywanie wideo (Android). Musisz wybrać opcję **Zezwól** , aby aplikacja Authenticator mogła uzyskać dostęp do Twojego aparatu, aby w następnym kroku uzyskać zdjęcie kodu QR. Jeśli nie zezwolisz na korzystanie z aparatu, nadal możesz skonfigurować aplikację uwierzytelniającej, ale musisz ręcznie dodać informacje o kodzie. Aby dowiedzieć się, jak ręcznie dodać kod, zobacz temat [Ręczne dodawanie konta do aplikacji](user-help-auth-app-add-account-manual.md).
    >
    >Aby uzyskać więcej informacji na temat sposobu uzyskiwania kodu QR, zobacz [Rozpoczynanie pracy z aplikacją Microsoft Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install) lub [Konfigurowanie informacji zabezpieczających do korzystania z aplikacji Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-auth-app)w zależności od tego, czy administrator włączył informacje zabezpieczające.

## <a name="troubleshooting-backup-and-recovery-problems"></a>Rozwiązywanie problemów z tworzeniem kopii zapasowej i odzyskiwaniem

Istnieje kilka powodów, dla których kopia zapasowa może być niedostępna:

- **Zmiana systemów operacyjnych.** Kopia zapasowa jest przechowywana w opcji magazynu w chmurze udostępnionej przez system operacyjny telefonu, co oznacza, że kopia zapasowa jest niedostępna w przypadku przełączania między systemami Android i iOS. W takiej sytuacji należy ręcznie utworzyć ponownie konto w ramach aplikacji.

- **Problemy z siecią lub hasłem.** Upewnij się, że nawiązano połączenie z siecią i zarejestrowano Cię na koncie usługi iCloud przy użyciu tego samego AppleID użytego na ostatnim telefonie iPhone.

- **Przypadkowe usunięcie.** Istnieje możliwość usunięcia konta kopii zapasowej z poprzedniego urządzenia lub zarządzania kontem magazynu w chmurze. W takiej sytuacji należy ręcznie utworzyć ponownie konto w ramach aplikacji.

- **Istniejące konta Microsoft Authenticator.** Jeśli skonfigurowano już konta w aplikacji Microsoft Authenticator, aplikacja nie będzie mogła odzyskać kont z kopią zapasową. Uniemożliwianie odzyskiwania pomaga upewnić się, że szczegóły konta nie są zastępowane nieaktualnymi informacjami. W takiej sytuacji należy usunąć wszelkie istniejące informacje o koncie z istniejących kont skonfigurowanych w aplikacji uwierzytelniania, zanim będzie możliwe odzyskanie kopii zapasowej.

## <a name="next-steps"></a>Następne kroki

Teraz, po wykonaniu kopii zapasowej i odzyskaniu poświadczeń konta na nowym urządzeniu, możesz nadal używać aplikacji Microsoft Authenticator, aby zweryfikować swoją tożsamość. Aby uzyskać więcej informacji, zobacz [Logowanie do kont przy użyciu aplikacji Microsoft Authenticator](user-help-sign-in.md).

## <a name="related-topics"></a>Tematy pokrewne

- [Co to jest aplikacja Microsoft Authenticator?](user-help-auth-app-overview.md)

- [Microsoft Authenticator app FAQ (Aplikacja Microsoft Authenticator — często zadawane pytania)](user-help-auth-app-faq.md)

- [Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/)
