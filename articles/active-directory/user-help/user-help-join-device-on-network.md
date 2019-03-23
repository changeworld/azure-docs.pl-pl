---
title: Dołącz swoje urządzenie pracy do sieci w organizacji — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak sprzęgać swoje urządzenie pracy z siecią organizacji.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: lizross
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: e2ba6b2d33c3fb5d9fda6821718ac61513a958b7
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58369143"
---
# <a name="join-your-work-device-to-your-organizations-network"></a>Dołącz swoje urządzenie pracy do sieci organizacji
Dołączanie urządzenia należące do pracy systemu Windows 10 do sieci w organizacji, aby dostęp do zasobów z potencjalnie ograniczeniami.

## <a name="what-happens-when-you-join-your-device"></a>Co się stanie po dołączeniu do urządzenia
Gdy dołączasz urządzenia z systemem Windows 10 do sieci w organizacji, będzie miało miejsce następujące akcje:

- Windows wykonywana jest rejestracja urządzenia do sieci w organizacji, umożliwiając dostęp do zasobów przy użyciu konta osobistego. Po zarejestrowaniu urządzenia Windows następnie łączy urządzenie z siecią, aby można było używać nazwy użytkownika i hasła w organizacji mogą się zalogować, a dostęp do zasobów z ograniczeniami.

- Opcjonalnie, oparte na opcje Twojej organizacji, użytkownik może zostać poproszona o skonfigurowaną weryfikację dwuetapową, przy użyciu jednej [uwierzytelnianie wieloskładnikowe](multi-factor-authentication-end-user-first-time.md) lub [zabezpieczające](user-help-security-info-overview.md).

- Opcjonalnie oparte na opcje Twojej organizacji, użytkownik może automatycznej rejestracji w zarządzania urządzeniami przenośnymi, takie jak Microsoft Intune. Aby uzyskać więcej informacji na temat rejestrowania w programie Microsoft Intune, zobacz [zarejestrowania urządzenia w usłudze Intune](https://docs.microsoft.com/intune-user-help/enroll-your-device-in-intune-all).

- Będzie można przejść przez proces logowania przy użyciu automatyczne logowanie z Twoim kontem organizacyjnym.

## <a name="to-join-a-brand-new-windows-10-device"></a>Aby dołączanie zupełnie nowym urządzenia z systemem Windows 10
Jeśli urządzenie jest całkowicie nowy i nie skonfigurowano jeszcze, możesz przejść przez proces Windows poza (tryb OOBE Box Experience), aby dołączyć urządzenie z siecią.

1. Uruchom nowe urządzenie i rozpocznij proces OOBE.

2. Na **Zaloguj się przy użyciu Microsoft** ekranu, podaj swój służbowy adres e-mail.

    ![Zaloguj się na ekranie za pomocą adresu e-mail](./media/user-help-join-device-on-network/join-device-oobe-signin.png)

3. Na **wprowadź hasło** ekranu, wpisz swoje hasło.

    ![Wprowadź hasło ekranu](./media/user-help-join-device-on-network/join-device-oobe-password.png)

4. Na urządzeniu przenośnym zatwierdzić urządzenia, więc może ona uzyskać dostępu do konta. 

    ![Ekran powiadomienie na telefon komórkowy](./media/user-help-join-device-on-network/join-device-oobe-mobile.png)

5. Zakończenie procesu OOBE, włącznie z ustawieniem ustawień prywatności i konfigurowania Windows Hello (jeśli jest to konieczne).

    Urządzenie jest przyłączone do sieci organizacji.

## <a name="to-make-sure-youre-joined"></a>Aby upewnić się, są przyłączone do
Należy upewnić się, że one przyłączone, sprawdzając ustawienia.

1. Otwórz **ustawienia**, a następnie wybierz pozycję **kont**.

    ![Konta na ekranie Ustawienia](./media/user-help-join-device-on-network/join-device-settings-accounts.png)

2. Wybierz **dostęp do zasobów służbowych**i upewnij się, zostanie wyświetlony tekst, który jest wyświetlany komunikat podobny do, **połączone *< your_organization >* usługi Azure AD**.

    ![Otwórz ekran służbowego przy użyciu konta z połączonych firmy contoso](./media/user-help-join-device-on-network/join-device-oobe-verify.png)


## <a name="to-join-an-already-configured-windows-10-device"></a>Aby dołączyć już skonfigurowanego urządzenia z systemem Windows 10
Jeśli uzyskano wcześniej urządzenia pod kątem czasu i jest już został skonfigurowany, możesz wykonać następujące kroki, aby dołączyć urządzenie z siecią.

1. Otwórz **ustawienia**, a następnie wybierz pozycję **kont**.

2. Wybierz **dostęp do zasobów służbowych**, a następnie wybierz pozycję **Connect**.

    ![Dostęp do służbowego i połącz łącza](./media/user-help-join-device-on-network/join-device-access-work-school-connect.png)

3. Na **Konfigurowanie konta służbowego lub szkolnego** ekranu, wybierz opcję **Dołącz to urządzenie do usługi Azure Active Directory**.

    ![Konfigurowanie pracy lub szkołą ekranu konta](./media/user-help-join-device-on-network/join-device-setup-join-aad.png)

4. Na **Zaloguj się** ekranu, wpisz swój adres e-mail (na przykład alain@contoso.com), a następnie wybierz pozycję **dalej**.

    ![Zaloguj się na ekranie](./media/user-help-join-device-on-network/join-device-setup-get-signed-in.png)

5. Na **wprowadź hasło** ekranu, wpisz hasło, a następnie wybierz **Zaloguj**.

    ![Wprowadź hasło](./media/user-help-join-device-on-network/join-device-setup-password.png)

6. Na urządzeniu przenośnym zatwierdzić urządzenia, więc może ona uzyskać dostępu do konta. 

    ![Ekran powiadomienie na telefon komórkowy](./media/user-help-join-device-on-network/join-device-setup-mobile.png)

7. Na **upewnij się, czy Twoja organizacja jest to** ekranu, przejrzyj informacje, aby upewnić się, że jest on odpowiedni, a następnie wybierz pozycję **Dołącz**.

    ![Upewnij się, że na ekranie weryfikacji Twojej organizacji](./media/user-help-join-device-on-network/join-device-setup-confirm.png)

8. Na **wszystko jest gotowe** ekranu, kliknij przycisk **gotowe**.

    ![Teraz wszystkie ekranu zestawu](./media/user-help-join-device-on-network/join-device-setup-finish.png)

## <a name="to-make-sure-youre-joined"></a>Aby upewnić się, są przyłączone do
Należy upewnić się, że one przyłączone, sprawdzając ustawienia.

1. Otwórz **ustawienia**, a następnie wybierz pozycję **kont**.

    ![Konta na ekranie Ustawienia](./media/user-help-join-device-on-network/join-device-settings-accounts.png)

2. Wybierz **dostęp do zasobów służbowych**i upewnij się, zostanie wyświetlony tekst, który jest wyświetlany komunikat podobny do, **połączone *< your_organization >* usługi Azure AD**.

    ![Otwórz ekran służbowego przy użyciu konta z połączonych firmy contoso](./media/user-help-join-device-on-network/join-device-setup-verify.png)

## <a name="next-steps"></a>Kolejne kroki
Po dołączeniu urządzenia do sieci w organizacji należy mogła uzyskać dostęp do wszystkich zasobów przy użyciu firmowego lub szkolnego informacje o koncie.

- Jeśli Twoja organizacja chce, aby zarejestrować urządzenie osobiste, takie jak telefon, zobacz [zarejestrowania urządzenia osobistego w sieci organizacji](user-help-register-device-on-network.md).

