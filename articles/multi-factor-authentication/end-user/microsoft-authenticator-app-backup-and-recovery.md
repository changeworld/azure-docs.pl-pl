---
title: Wykonywanie kopii zapasowych i odzyskiwania informacji o Microsoft Authenticator - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzyć i odzyskiwać poświadczenia konta, za pomocą aplikacji Authenticator firmy Microsoft.
services: multi-factor-authentication
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2018
ms.author: lizross
ms.reviewer: olhaun
ms.custom: end-user
ms.openlocfilehash: 8fa9a24b5b402ebebb69bc69f7cda47092436642
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="backup-and-recover-account-credentials-with-the-microsoft-authenticator-app"></a>Kopia zapasowa i odzyskiwanie poświadczenia konta z aplikacji Microsoft Authenticator
**Dotyczy:**

- urządzenia z systemem iOS

Aplikacji Microsoft Authenticator kopię zapasową poświadczenia konta i ustawień powiązanych aplikacji, takich jak kolejność kont, do chmury. Po utworzeniu kopii zapasowej, można użyć aplikacji do odzyskania danych na nowe urządzenie, potencjalnie unikanie pobierania zablokowany out lub konieczności ponownego tworzenia kont.

>[!IMPORTANT]
> Dla każdej lokalizacji magazynu kopii zapasowej należy jeden osobistego konta Microsoft i kontami usługi iCloud. Jednak w tej lokalizacji magazynu, można tworzyć kopie zapasowe kilku kont. Na przykład można mieć konto osobiste konto służbowe i konta innych firm, takich jak Facebook, Google itd.<br><br>Tylko poświadczenia konta są przechowywane w tym nazwę użytkownika i kod weryfikacyjny konta 8 cyfr, która jest wymagana do potwierdzenia tożsamości z aplikacji Authenticator firmy Microsoft. Inne informacje związane z kontami, w tym wiadomości e-mail lub pliki nie są przechowywane. Możemy również nie skojarzyć lub udostępniać swoje konta, w dowolny sposób, lub z innego produktu lub usługi. I na koniec administrator IT nie będzie żadnych informacji o tych kont.

## <a name="back-up-your-account-credentials"></a>Wykonaj kopię zapasową poświadczeń konta
Przed utworzeniem kopii zapasowej poświadczeń, muszą mieć system:

- Osobistego [konta Microsoft](https://account.microsoft.com/account) do działania jako konto odzyskiwania.

- [Konta iCloud](https://www.icloud.com/) lokalizacji przechowywania. 

Trzeba zalogować się do obu razem kont zapewnia lepsze zabezpieczenia, aby uzyskać informacje o kopii zapasowej.

**Aby włączyć usługę w chmurze kopii zapasowej**
-   Na urządzeniu z systemem iOS, wybierz **ustawienia**, wybierz pozycję **kopii zapasowej**, a następnie Włącz **automatyczne kopie zapasowe**.

    Poświadczenia konta kopię zapasową na koncie usługi iCloud.

    ![Ekran ustawień systemu iOS, przedstawiający lokalizację automatycznie ustawienia kopii zapasowej](./media/authenticator-app-backup-and-recovery/backup-and-recovery-turn-on.png)

## <a name="recover-your-account-credentials-on-your-new-device"></a>Odzyskaj poświadczeń konta na nowe urządzenie
Poświadczenia konta można odzyskać z konta usługi iCloud przy użyciu tego samego konta odzyskiwania firmy Microsoft, którego można skonfigurować, gdy kopie zapasowe informacji.

**Aby odzyskać informacje**
1.  Na urządzeniu z systemem iOS Otwórz aplikację Microsoft Authenticator i wybierz **rozpocząć odzyskiwanie** w dolnej części ekranu.

    ![Aplikacja Microsoft Authenticator, przedstawiający miejsce kliknij przycisk Rozpocznij odzyskiwania](./media/authenticator-app-backup-and-recovery/backup-and-recovery-begin-recovery.png)

2.  Zaloguj się do konta odzyskiwania przy użyciu tego samego osobiste konto Microsoft, używane podczas procesu tworzenia kopii zapasowej.

    Poświadczenia konta zostaną odzyskane do nowego urządzenia.

Po zakończeniu odzyskiwania, można zauważyć, że Twoje osobiste Microsoft konta kodów weryfikacyjnych w aplikacji Microsoft Authenticator różnią się między z telefonów stary i nowy. Kody są różne, ponieważ każde urządzenie ma własny unikatowy poświadczeń, ale są prawidłowe i służbowe podczas logowania przy użyciu telefonu skojarzony.

## <a name="recover-additional-accounts-requiring-more-verification"></a>Odzyskaj dodatkowych kont wymaganie weryfikacji więcej
Użycie powiadomień wypychanych przy użyciu osobistej, kont służbowych lub szkolnych, zostanie wyświetlony na ekranie alertu który mówi było można odzyskać dane, musisz podać dodatkowej weryfikacji. Ponieważ powiadomienia wypychane wymagają przy użyciu poświadczeń, które zostały powiązane z określonym urządzeniem nigdy nie są wysyłane za pośrednictwem sieci, należy potwierdzenia tożsamości przed utworzeniem poświadczenia na urządzeniu.

Do osobistego konta Microsoft można potwierdzić Twoją tożsamość przez wprowadzenie hasła oraz alternatywny adres e-mail lub numer telefonu. Dla konta służbowego musisz skanowania kod QR dostarczone przez dostawcę konta.

**Aby zapewnić dodatkową weryfikację konta osobiste**
1.  W **kont** ekranu aplikacji Microsoft Authenticator strzałkę listy rozwijanej wybierz obok konta chcesz odzyskać.

    ![Aplikacji Authenticator firmy Microsoft, pokazujący dostępne konta z ich skojarzone strzałki rozwijanej](./media/authenticator-app-backup-and-recovery/backup-and-recovery-arrow.png)

2.  Wybierz **Zaloguj się do odzyskania**, wpisz hasło, a następnie potwierdź z wiadomości e-mail adres lub numer telefonu jako dodatkowej weryfikacji.

    ![Aplikacji Authenticator firmy Microsoft, umożliwiając wprowadź informacje logowania](./media/authenticator-app-backup-and-recovery/backup-and-recovery-sign-in.png)

**Aby zapewnić dodatkową weryfikację konta służbowe**
1.  W **kont** ekranu aplikacji Microsoft Authenticator strzałkę listy rozwijanej wybierz obok konta chcesz odzyskać.

    ![Aplikacji Authenticator firmy Microsoft, pokazujący dostępne konta z ich skojarzone strzałki rozwijanej](./media/authenticator-app-backup-and-recovery/backup-and-recovery-additonal-accts.png)

2.  Wybierz **kod QR skanowania, aby odzyskać**, a następnie Zeskanuj kod QR podany przez administratora.

    ![Aplikacji Authenticator firmy Microsoft, co umożliwia skanowanie Twój kod QR](./media/authenticator-app-backup-and-recovery/backup-and-recovery-scan-qr-code.png)

## <a name="troubleshooting-backup-and-recovery-problems"></a>Rozwiązywanie problemów z kopii zapasowej i odzyskiwania
Istnieje kilka przyczyn, dlaczego kopii zapasowej mogą nie być dostępne:

-   **Zmiana systemu operacyjnego.** Kopia zapasowa jest przechowywana w chmurze opcji magazynu dostarczane przez system operacyjny Twój telefon, co oznacza, że kopia zapasowa jest niedostępna, jeśli przełączania się między systemami Android i iOS. W takiej sytuacji należy ręcznie ponownie utworzyć konta w aplikacji.

-   **Problemy z siecią lub hasła.** Upewnij się, jest połączony z siecią i zalogowaniem się do konta usługi iCloud za pomocą tego samego AppleID, używane na telefonie iPhone ostatni.

-   **Przypadkowego usunięcia.** Użytkownik może usunąć konta kopii zapasowej, z poprzedniej urządzenia lub konta magazynu w chmurze i zarządzania nimi. W takiej sytuacji należy ręcznie ponownie utworzyć konta w aplikacji.

-   **Istniejące konta Authenticator firmy Microsoft.** Jeśli zostały już skonfigurowane konto w aplikacji Microsoft Authenticator, aplikacja nie będzie możliwe odzyskanie konta kopii zapasowej. Zapobieganie recovery pomaga, upewnij się, że szczegółowe informacje o koncie nie są zastąpione nieaktualnych informacji. W takiej sytuacji należy usunąć wszystkie istniejące informacje o koncie z istniejących kont skonfigurowane w aplikację uwierzytelniania, zanim będzie można odzyskać z kopii zapasowej.

## <a name="next-steps"></a>Kolejne kroki
Teraz, że wykonano kopię zapasową, a następnie odzyskać poświadczeń konta do nowe urządzenie, możesz zweryfikować Twoją tożsamość przy użyciu aplikacji Authenticator firmy Microsoft.

## <a name="related-topics"></a>Powiązane tematy
- [Wprowadzenie do aplikacji Microsoft Authenticator](microsoft-authenticator-app-how-to.md)  

- [Logowanie przy użyciu telefonu](microsoft-authenticator-app-phone-signin-faq.md)

- [Aplikacja uwierzytelniania firmy Microsoft — często zadawane pytania](microsoft-authenticator-app-faq.md)

- [Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/)
