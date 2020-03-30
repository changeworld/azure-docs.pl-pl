---
title: Dołączanie urządzenia służbowego do sieci organizacji — AD
description: Dowiedz się, jak dołączyć urządzenie do pracy do sieci organizacji.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: curtand
ms.reviewer: jairoc
ms.openlocfilehash: 0ff8b85a15d94ded2d702e0df247f9ebc4d3f923
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266314"
---
# <a name="join-your-work-device-to-your-organizations-network"></a>Dołączanie urządzenia służbowego do sieci organizacji
Dołącz do należącego do pracy urządzenia z systemem Windows 10 do sieci organizacji, aby uzyskać dostęp do potencjalnie ograniczonych zasobów.

## <a name="what-happens-when-you-join-your-device"></a>Co się stanie po dołączeniu do urządzenia
Podczas łączenia urządzenia z systemem Windows 10 z siecią organizacji zostaną podjęte następujące czynności:

- System Windows rejestruje urządzenie w sieci organizacji, umożliwiając dostęp do zasobów przy użyciu konta osobistego. Po zarejestrowaniu urządzenia system Windows dołącza do urządzenia do sieci, dzięki czemu można używać nazwy użytkownika i hasła organizacji do logowania się i uzyskiwania dostępu do ograniczonych zasobów.

- Opcjonalnie, w zależności od wyborów w organizacji, może zostać poproszony o skonfigurowanie weryfikacji dwuetapowej za pomocą [uwierzytelniania wieloskładnikowego](multi-factor-authentication-end-user-first-time.md) lub [informacji zabezpieczających.](user-help-security-info-overview.md)

- Opcjonalnie, na podstawie wyborów w organizacji, może być automatycznie zarejestrowany w zarządzaniu urządzeniami przenośnymi, takich jak Microsoft Intune. Aby uzyskać więcej informacji na temat rejestrowania się w usłudze Microsoft Intune, zobacz [Rejestrowanie urządzenia w usłudze Intune](https://docs.microsoft.com/intune-user-help/enroll-your-device-in-intune-all).

- Przejdziesz przez proces logowania, używając automatycznego logowania za pomocą konta instytucji.

## <a name="to-join-a-brand-new-windows-10-device"></a>Aby dołączyć do zupełnie nowego urządzenia z systemem Windows 10
Jeśli urządzenie jest zupełnie nowe i nie zostało jeszcze skonfigurowane, możesz przejść przez proces OOBE (Windows Out of Box Experience), aby dołączyć do urządzenia do sieci.

1. Uruchom nowe urządzenie i rozpocznij proces OOBE.

2. Na ekranie **Zaloguj się za pomocą firmy Microsoft** wpisz służbowy adres e-mail.

    ![Ekran logowania przy za pomocą adresu e-mail](./media/user-help-join-device-on-network/join-device-oobe-signin.png)

3. Na ekranie **Wprowadź hasło** wpisz hasło.

    ![Wprowadź ekran hasła](./media/user-help-join-device-on-network/join-device-oobe-password.png)

4. Na urządzeniu mobilnym zatwierdź urządzenie, aby mógł uzyskać dostęp do twojego konta. 

    ![Ekran powiadomień mobilnych](./media/user-help-join-device-on-network/join-device-oobe-mobile.png)

5. Ukończ proces OOBE, w tym ustawienie ustawień prywatności i skonfigurowanie funkcji Windows Hello (jeśli to konieczne).

    Urządzenie jest teraz połączone z siecią organizacji.

## <a name="to-make-sure-youre-joined"></a>Aby upewnić się, że dołączają do Ciebie
Możesz się upewnić, że dołączasz, patrząc na ustawienia.

1. Otwórz **pozycję Ustawienia**, a następnie wybierz pozycję **Konta**.

    ![Konta na ekranie Ustawienia](./media/user-help-join-device-on-network/join-device-settings-accounts.png)

2. Wybierz **pozycję Dostęp do pracy lub szkoły**i upewnij się, że widzisz tekst, który mówi coś takiego, Połączony ** * \<z your_organization>* usługi Azure AD**.

    ![Dostęp do ekranu pracy lub szkoły za pomocą połączonego konta contoso](./media/user-help-join-device-on-network/join-device-oobe-verify.png)


## <a name="to-join-an-already-configured-windows-10-device"></a>Aby dołączyć do już skonfigurowanego urządzenia z systemem Windows 10
Jeśli urządzenie jest już skonfigurowane i zostało już skonfigurowane, możesz wykonać następujące czynności, aby dołączyć do urządzenia do sieci.

1. Otwórz **pozycję Ustawienia**, a następnie wybierz pozycję **Konta**.

2. Wybierz **pozycję Dostęp do pracy lub szkoły**, a następnie wybierz pozycję **Połącz**.

    ![Dostęp do łączy pracy lub szkoły i połącz](./media/user-help-join-device-on-network/join-device-access-work-school-connect.png)

3. Na ekranie **Konfigurowanie konta służbowego** wybierz pozycję **Dołącz to urządzenie do usługi Azure Active Directory**.

    ![Konfigurowanie ekranu konta służbowego](./media/user-help-join-device-on-network/join-device-setup-join-aad.png)

4. Na ekranie **Zalogujmy się,** wpisz swój adres e-mail (na alain@contoso.comprzykład), a następnie wybierz pozycję **Dalej**.

    ![Zalogujmy się na ekranie](./media/user-help-join-device-on-network/join-device-setup-get-signed-in.png)

5. Na ekranie **Wprowadź hasło** wpisz hasło, a następnie wybierz pozycję **Zaloguj**się .

    ![Wprowadź hasło](./media/user-help-join-device-on-network/join-device-setup-password.png)

6. Na urządzeniu mobilnym zatwierdź urządzenie, aby mógł uzyskać dostęp do twojego konta. 

    ![Ekran powiadomień mobilnych](./media/user-help-join-device-on-network/join-device-setup-mobile.png)

7. Na ekranie **Upewnij się, że jest to twoja organizacja,** przejrzyj informacje, aby upewnić się, że są słuszne, a następnie wybierz pozycję **Dołącz**.

    ![Upewnij się, że jest to ekran weryfikacji organizacji](./media/user-help-join-device-on-network/join-device-setup-confirm.png)

8. Na ekranie **Masz wszystko ustawione,** kliknij przycisk **Gotowe**.

    ![Masz ustawiony ekran](./media/user-help-join-device-on-network/join-device-setup-finish.png)

## <a name="to-make-sure-youre-joined"></a>Aby upewnić się, że dołączają do Ciebie
Możesz się upewnić, że dołączasz, patrząc na ustawienia.

1. Otwórz **pozycję Ustawienia**, a następnie wybierz pozycję **Konta**.

    ![Konta na ekranie Ustawienia](./media/user-help-join-device-on-network/join-device-settings-accounts.png)

2. Wybierz **pozycję Dostęp do pracy lub szkoły**i upewnij się, że widzisz tekst, który mówi coś takiego, Połączony ** * \<z your_organization>* usługi Azure AD**.

    ![Dostęp do ekranu pracy lub szkoły za pomocą połączonego konta contoso](./media/user-help-join-device-on-network/join-device-setup-verify.png)

## <a name="next-steps"></a>Następne kroki
Po dołączeniu urządzenia do sieci organizacji powinieneś mieć dostęp do wszystkich zasobów przy użyciu informacji o koncie służbowym.

- Jeśli organizacja chce zarejestrować urządzenie osobiste, takie jak telefon, zobacz [Rejestrowanie urządzenia osobistego w sieci organizacji.](user-help-register-device-on-network.md)

- Jeśli twoja organizacja jest zarządzana przy użyciu usługi Microsoft Intune i masz pytania dotyczące rejestracji, logowania lub innego problemu związanego z usługą Intune, zobacz [zawartość pomocy użytkownika usługi Intune](https://docs.microsoft.com/intune-user-help/use-managed-devices-to-get-work-done).