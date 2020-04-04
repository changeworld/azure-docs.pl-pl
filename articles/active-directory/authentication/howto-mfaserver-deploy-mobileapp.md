---
title: Usługa sieci Web aplikacji mobilnej usługi azure mfa server — usługa Azure Active Directory
description: Konfigurowanie serwera MFA do wysyłania powiadomień push do użytkowników za pomocą aplikacji Microsoft Authenticator.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 785c4ccf591ce6a7a50a3d144fb1740121c73ce6
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80653190"
---
# <a name="enable-mobile-app-authentication-with-azure-multi-factor-authentication-server"></a>Włączanie uwierzytelniania aplikacji mobilnych za pomocą serwera usługi Azure Multi-Factor Authentication

Aplikacja Microsoft Authenticator oferuje dodatkową opcję weryfikacji poza pasmem. Zamiast umieszczać automatyczne połączenie telefoniczne lub SMS do użytkownika podczas logowania, uwierzytelnianie wieloskładnikowe platformy Azure wypycha powiadomienie do aplikacji Microsoft Authenticator na smartfonie lub tablecie użytkownika. Użytkownik po prostu naciska **Sprawdź** (lub wprowadza kod PIN i naciska "Uwierzytelnij") w aplikacji, aby zakończyć logowanie.

Korzystanie z aplikacji mobilnej w celu weryfikacji dwuetapowej jest preferowane, jeśli zasięg telefonu jest niestabilny. Jeśli używasz aplikacji jako generatora tokenów OATH, nie wymaga ona żadnego połączenia z siecią ani Internetem.

> [!IMPORTANT]
> Od 1 lipca 2019 r. firma Microsoft nie będzie już oferować serwera usługi MFA dla nowych wdrożeń. Nowi klienci, którzy chcieliby wymagać uwierzytelniania wieloskładnikowego od swoich użytkowników, powinni korzystać z uwierzytelniania wieloskładnikowego platformy Azure w chmurze. Obecni klienci, którzy aktywowali serwer usługi MFA przed 1 lipca, będą mogli pobrać najnowszą wersję, przyszłe aktualizacje i wygenerować poświadczenia aktywacji w zwykły sposób.

> [!IMPORTANT]
> Jeśli masz zainstalowany serwer usługi Azure Multi-Factor Authentication Server 8.x lub nowszy, większość poniższych kroków nie jest wymagana. Uwierzytelnianie aplikacji mobilnej można skonfigurować, wykonując czynności opisane w sekcji [Konfigurowanie aplikacji mobilnej](#configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server).

## <a name="requirements"></a>Wymagania

Aby korzystać z aplikacji Microsoft Authenticator, musisz mieć uruchomiony serwer usługi Azure Multi-Factor Authentication Server 8.x lub nowszy

## <a name="configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server"></a>Skonfigurowanie ustawień aplikacji mobilnej na serwerze usługi Azure Multi-Factor Authentication

1. W konsoli usługi Azure Multi-Factor Authentication kliknij ikonę Portal użytkowników. Jeśli użytkownicy mogą kontrolować metody ich uwierzytelniania, zaznacz opcję **Aplikacja mobilna** na karcie Ustawienia w obszarze **Zezwalaj użytkownikom na wybór metody**. W przeciwnym razie użytkownicy końcowi będą kontaktować się z działem pomocy technicznej w celu ukończenia procesu aktywacji aplikacji mobilnej.
2. Zaznacz pole **Zezwalaj użytkownikom na uaktywnienie aplikacji mobilnej**.
3. Zaznacz pole **Zezwalaj na rejestrację użytkownika**.
4. Kliknij ikonę **Aplikacji mobilnej.**
5. W polu **nazwy konta** wprowadź nazwę firmy lub organizacji, aby wyświetlić ją w aplikacji mobilnej dla tego konta.
   ![Ustawienia aplikacji mobilnej konfiguracji serwera usługi MFA](./media/howto-mfaserver-deploy-mobileapp/mobile.png)

## <a name="next-steps"></a>Następne kroki

- [Zaawansowane scenariusze z uwierzytelnianiem wieloskładnikowym platformy Azure i sieciami VPN innych firm.](howto-mfaserver-nps-vpn.md)
