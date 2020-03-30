---
title: Rejestrowanie urządzeń osobistych w sieci organizacji — Usługa Azure AD
description: Dowiedz się, jak zarejestrować urządzenie osobiste w sieci organizacji, aby uzyskać dostęp do chronionych zasobów organizacji.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: curtand
ms.reviewer: jairoc
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: e2e6585bac100a09f3f98037e90b24738e22816f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063870"
---
# <a name="register-your-personal-device-on-your-organizations-network"></a>Rejestrowanie urządzenia osobistego w sieci organizacji
Zarejestruj swoje urządzenie osobiste (zazwyczaj telefon lub tablet) w sieci organizacji. Po zarejestrowaniu urządzenia będzie ono mogło uzyskać dostęp do ograniczonych zasobów organizacji.

>[!Note]
>W tym artykule użyto urządzenia z systemem Windows do celów demonstracyjnych, ale można również zarejestrować urządzenia z systemem iOS, Android lub macOS.

## <a name="what-happens-when-you-register-your-device"></a>Co się stanie podczas rejestracji urządzenia
Podczas rejestrowania urządzenia w sieci organizacji zostaną podjęte następujące czynności:

- System Windows rejestruje urządzenie w sieci organizacji.

- Opcjonalnie, w zależności od wyborów w organizacji, może zostać poproszony o skonfigurowanie weryfikacji dwuetapowej za pomocą [uwierzytelniania wieloskładnikowego](multi-factor-authentication-end-user-first-time.md) lub [informacji zabezpieczających.](user-help-security-info-overview.md)

- Opcjonalnie, na podstawie wyborów w organizacji, może być automatycznie zarejestrowany w zarządzaniu urządzeniami przenośnymi, takich jak Microsoft Intune. Aby uzyskać więcej informacji na temat rejestrowania się w usłudze Microsoft Intune, zobacz [Rejestrowanie urządzenia w usłudze Intune](https://docs.microsoft.com/intune-user-help/enroll-your-device-in-intune-all).

- Przejdziesz przez proces logowania, używając nazwy użytkownika i hasła do konta służbowego.

## <a name="to-register-your-windows-device"></a>Aby zarejestrować urządzenie z systemem Windows

Wykonaj następujące kroki, aby zarejestrować urządzenie osobiste w sieci.

1. Otwórz **pozycję Ustawienia**, a następnie wybierz pozycję **Konta**.

    ![Konta na ekranie Ustawienia](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. Wybierz **pozycję Dostęp do pracy lub szkoły**, a następnie wybierz pozycję **Połącz** z ekranu **Programu Access (Praca lub szkoła** programu Access).

    ![Dostęp do ekranu pracy lub szkoły z wyróżnioną opcją Połącz](./media/user-help-register-device-on-network/register-device-access-work-school-connect.png)

3. Na ekranie **Dodawanie konta służbowego** wpisz swój adres e-mail dla konta służbowego, a następnie wybierz pozycję **Dalej**. Na przykład alain@contoso.com.

4. Zaloguj się na swoje konto służbowe, a następnie wybierz pozycję **Zaloguj**się.

5. Ukończ pozostałą część procesu rejestracji, w tym zatwierdzenie żądania weryfikacji tożsamości (jeśli używasz weryfikacji dwuetapowej) i skonfigurowanie funkcji Windows Hello (jeśli to konieczne).

## <a name="to-verify-that-youre-registered"></a>Aby sprawdzić, czy jesteś zarejestrowany
Możesz się upewnić, że jesteś zarejestrowany, patrząc na ustawienia.

1. Otwórz **pozycję Ustawienia**, a następnie wybierz pozycję **Konta**.

    ![Konta na ekranie Ustawienia](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. Wybierz **pozycję Dostęp do pracy lub szkoły**i upewnij się, że widzisz swoje konto służbowe.

    ![Dostęp do ekranu pracy lub szkoły za pomocą połączonego konta contoso](./media/user-help-register-device-on-network/register-device-setup-verify.png)

## <a name="next-steps"></a>Następne kroki
Po zarejestrowaniu urządzenia osobistego w sieci organizacji powinieneś mieć dostęp do większości zasobów.

- Jeśli twoja organizacja chce dołączyć do urządzenia służbowego, zobacz [Dołączanie urządzenia służbowego do sieci organizacji](user-help-join-device-on-network.md).



