---
title: Wykonywanie kopii zapasowych i odzyskiwanie przy użyciu aplikacji Microsoft Authenticator — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Informacje o sposobie tworzenia kopii zapasowych i odzyskiwanie poświadczeń konta usługi, za pomocą aplikacji Microsoft Authenticator.
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
ms.openlocfilehash: 9634e2578ea256d1dec71389f676ee53627e6272
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60474233"
---
# <a name="backup-and-recover-account-credentials-with-the-microsoft-authenticator-app"></a>Kopia zapasowa i odzyskiwanie poświadczeń konta, za pomocą aplikacji Microsoft Authenticator

**Dotyczy:**

- urządzenia z systemem iOS

Aplikacja Microsoft Authenticator tworzy kopie zapasowe Twoje poświadczenia konta i ustawień aplikacji, takich jak kolejność kont do chmury. Po utworzeniu kopii zapasowej, można użyć jej do odzyskania danych na nowym urządzeniu potencjalnie unikanie wprowadzenie zablokowane ze specyfikatorem out lub konieczności ponownego tworzenia konta.

> [!IMPORTANT]
> Dla każdej lokalizacji magazynu kopii zapasowych należy jeden osobistego konta Microsoft i jednym koncie usługi iCloud. Ale w tej lokalizacji magazynu można utworzyć kopię zapasową kilku kont. Na przykład można mieć konto osobiste, konto służbowe i konto innych firm, takich jak Facebook, Google i tak dalej.
> 
> Tylko poświadczenia konta osobiste i firm 3 są przechowywane w tym nazwę użytkownika i kod weryfikacyjny konta, które są wymagane, aby potwierdzić swoją tożsamość. Nie przechowujemy inne informacje związane z kontami, w tym wiadomości e-mail lub plików. Możemy również nie skojarzyć lub Udostępnij swoje konta w dowolny sposób lub przy użyciu dowolnego produktu lub usługi. A na koniec administrator IT nie będą otrzymywać żadnych informacji na temat tych kont.

## <a name="back-up-your-account-credentials"></a>Wykonaj kopię zapasową poświadczeń konta
Przed utworzeniem kopii zapasowej swoje poświadczenia muszą istnieć metody:

- Osobiste [konta Microsoft](https://account.microsoft.com/account) jako swojego konta odzyskiwania.

- [Konta usługi iCloud](https://www.icloud.com/) dla lokalizacji rzeczywisty magazyn. 

Wymaganie, należy zalogować się do obu kont, które są razem zapewnia lepsze zabezpieczenia, aby uzyskać informacje o kopii zapasowej.

**Aby włączyć w chmurze, kopii zapasowej**
-   Na urządzeniu z systemem iOS, wybierz **ustawienia**, wybierz opcję **kopii zapasowej**, a następnie Włącz **kopii zapasowej usługi iCloud**.

    Poświadczenia konta kopię zapasową na Twoim koncie usługi iCloud.

    ![iOS ustawień przedstawiający ekran lokalizacji usługi iCloud ustawienia kopii zapasowej](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on.png)

## <a name="recover-your-account-credentials-on-your-new-device"></a>Odzyskiwanie poświadczeń konta nowe urządzenie
Poświadczenia konta można odzyskać z Twojego konta usługi iCloud, przy użyciu tego samego konta Microsoft odzyskiwania, które można skonfigurować podczas tworzenia kopii zapasowych informacji.

### <a name="to-recover-your-information"></a>Aby odzyskać informacje
1.  Na urządzeniu z systemem iOS Otwórz aplikację Microsoft Authenticator, a następnie wybierz **rozpocząć odzyskiwanie** w dolnej części ekranu.

    ![Aplikacja Microsoft Authenticator, przedstawiający miejsce, kliknij przycisk Rozpocznij odzyskiwanie](./media/user-help-auth-app-backup-recovery/backup-and-recovery-begin-recovery.png)

2.  Zaloguj się do swojego konta odzyskiwania, za pomocą tego samego osobistego konta Microsoft, używane podczas procesu tworzenia kopii zapasowej.

    Poświadczenia konta są odzyskiwane do nowego urządzenia.

Po zakończeniu odzyskiwania, można zauważyć, że Twoje osobiste Microsoft konta kody weryfikacyjne w aplikacji Microsoft Authenticator różnią się między swoje stare i nowe telefony. Kody są różne, ponieważ każde urządzenie ma własny unikatowy poświadczeń, ale są prawidłowe i działają podczas logowania przy użyciu telefonów skojarzone.

## <a name="recover-additional-accounts-requiring-more-verification"></a>Odzyskaj dodatkowych kont wymagające dodatkowej weryfikacji
Użycie powiadomień wypychanych przy użyciu osobistej, kont służbowych lub szkolnych, zostanie wyświetlony na ekranie alertu, mówi, przed odzyskaniem danych, należy podać dodatkowej weryfikacji. Ponieważ powiadomienia wypychane wymagają przy użyciu poświadczeń, powiązany z konkretnego urządzenia i nigdy nie są wysyłane za pośrednictwem sieci, muszą potwierdzić swoją tożsamość, zanim poświadczenie jest tworzony na urządzeniu z systemem.

Dla osobistych kont Microsoft można potwierdzić swoją tożsamość, wprowadzając swoje hasło, wraz z alternatywny adres e-mail lub numer telefonu. Dla konta służbowego lub szkolnego musi skanowanie kodu QR dostarczone przez Twój dostawca kont.

### <a name="to-provide-additional-verification-for-personal-accounts"></a>Zapewnienie dodatkowej weryfikacji dla osobistych kont
1.  W **kont** ekranu aplikacji Microsoft Authenticator, wybierz listę rozwijaną strzałkę obok pozycji konto, którego chcesz odzyskać.

    ![Aplikacja Microsoft Authenticator, pokazujący dostępne konta za pomocą ich skojarzone strzałki listy rozwijanej](./media/user-help-auth-app-backup-recovery/backup-and-recovery-arrow.png)

2.  Wybierz **Zaloguj się do odzyskania**, wpisz hasło, a następnie potwierdź swoje wiadomości e-mail adres lub numer telefonu jako dodatkowej weryfikacji.

    ![Aplikacja Microsoft Authenticator, dzięki czemu możesz wprowadzić informacje logowania](./media/user-help-auth-app-backup-recovery/backup-and-recovery-sign-in.png)

### <a name="to-provide-additional-verification-for-work-or-school-accounts"></a>Aby zapewnić dodatkową weryfikację konta służbowego lub szkolnego
1.  W **kont** ekranu aplikacji Microsoft Authenticator, wybierz listę rozwijaną strzałkę obok pozycji konto, którego chcesz odzyskać.

    ![Aplikacja Microsoft Authenticator, pokazujący dostępne konta za pomocą ich skojarzone strzałki listy rozwijanej](./media/user-help-auth-app-backup-recovery/backup-and-recovery-additional-accts.png)

2.  Wybierz **kod QR skanowanie, aby odzyskać**, a następnie Zeskanuj kod QR.

    ![Aplikacja Microsoft Authenticator, co umożliwia skanowanie kodu QR](./media/user-help-auth-app-backup-recovery/backup-and-recovery-scan-qr-code.png)

    >[!NOTE]
    >Aby uzyskać więcej informacji na temat sposobu uzyskania kodu QR, zobacz [Rozpoczynanie pracy z aplikacją Microsoft Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install) lub [ustawić informacje zabezpieczające, aby użyć aplikacji authenticator](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-auth-app), zależnie od tego, czy Twój administrator wyłączył informacji zabezpieczających.

## <a name="troubleshooting-backup-and-recovery-problems"></a>Rozwiązywanie problemów z kopii zapasowej i odzyskiwania
Istnieje kilka powodów dlaczego kopii zapasowej może nie być dostępne:

-   **Zmiana systemu operacyjnego.** Kopia zapasowa jest przechowywana w chmurze opcję magazynu, dostarczone przez system operacyjny na swoim telefonie, co oznacza, że kopia zapasowa jest niedostępna, Jeśli przełączasz się między systemami Android i iOS. W takiej sytuacji należy ręcznie ponownie utworzyć konta w aplikacji.

-   **Problemy z siecią lub hasło.** Upewnij się, że jesteś podłączony do sieci i zalogowali się do Twojego konta usługi iCloud, przy użyciu tego samego AppleID, używane na telefonie iPhone ostatni.

-   **Przypadkowego usunięcia.** Użytkownik może usunąć kopii zapasowej konta, z poprzedniego urządzenia lub konta magazynu w chmurze i zarządzania nimi. W takiej sytuacji należy ręcznie ponownie utworzyć konta w aplikacji.

-   **Istniejących kont Microsoft Authenticator.** Jeśli zostały już skonfigurowane w aplikacji Microsoft Authenticator, aplikacja nie będzie możliwe odzyskanie kont usługi kopii zapasowej. Zapobieganie recovery pomaga, upewnij się, że szczegółów konta nie są zastąpione przy użyciu nieaktualnych informacji. W takiej sytuacji należy usunąć wszystkie istniejące informacje o koncie z istniejących kont, skonfiguruj w aplikacji wystawcy uwierzytelnienia, zanim będzie można odzyskać z kopii zapasowej.

## <a name="next-steps"></a>Kolejne kroki
Teraz, że wykonano kopię zapasową, a następnie odzyskać Twoje poświadczenia konta na nowe urządzenie, możesz zweryfikować swoją tożsamość za pomocą aplikacji Microsoft Authenticator. Aby uzyskać więcej informacji, zobacz [Zaloguj się do konta przy użyciu aplikacji Microsoft Authenticator](user-help-sign-in.md).

## <a name="related-topics"></a>Powiązane tematy

- [Co to jest aplikacja Microsoft Authenticator?](user-help-auth-app-overview.md)

- [Microsoft Authenticator app FAQ (Aplikacja Microsoft Authenticator — często zadawane pytania)](user-help-auth-app-faq.md)

- [Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/)
