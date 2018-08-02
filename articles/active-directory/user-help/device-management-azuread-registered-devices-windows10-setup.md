---
title: Konfigurowanie usługi Azure Active Directory zarejestrowanych urządzeń | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory zarejestrowanych urządzeń.
services: active-directory
author: eross-msft
manager: mtillman
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: lizross
ms.reviewer: jairoc
ms.openlocfilehash: 0c38c0160cea51940ac5b04ee64095c6a6f25b5d
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39414678"
---
# <a name="set-up-azure-active-directory-registered-windows-10-devices"></a>Konfigurowanie usługi Azure Active Directory zarejestrowanych urządzeń z systemem Windows 10

Za pomocą zarządzania urządzeniami w usłudze Azure Active Directory (Azure AD) można zagwarantować, że użytkownicy uzyskują dostęp do zasobów z urządzeń, które spełniają Twoje standardy dotyczące bezpieczeństwa i zgodności. Aby uzyskać więcej informacji, zobacz [wprowadzenie do zarządzania urządzeniami w usłudze Azure Active Directory](../device-management-introduction.md).

Jeśli chcesz włączyć **Przenieś swój własny urządzenia (BYOD)** scenariusz, można to zrobić przez skonfigurowanie usługi Azure AD, zarejestrowanych urządzeń. W usłudze Azure AD można skonfigurować urządzenia w usłudze Azure AD zarejestrowany dla systemu Windows 10, iOS, Android i macOS. Ten artykuł zawiera kroki powiązane dla urządzeń z systemem Windows 10. 


## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby zarejestrować urządzenie z systemem Windows 10, usługi rejestracji urządzeń, musi być skonfigurowany umożliwiające rejestrowanie urządzeń. Ponadto konieczne jest posiadanie mniejszą liczbę urządzeń zarejestrowanych niż skonfigurowane maksimum. Aby uzyskać więcej informacji, zobacz [Konfigurowanie ustawień urządzenia](../devices/device-management-azure-portal.md#configure-device-settings).

## <a name="what-you-should-know"></a>Co należy wiedzieć

Podczas rejestrowania urządzenia, użytkownik powinien pamiętać następujące:

- Windows to zarejestrowanie urządzenia w katalogu organizacji w usłudze Azure AD

- Może być konieczne przechodzą przez wezwanie do uwierzytelnienia Multi-Factor Authentication. To żądanie jest konfigurowane przez administratora IT.

- Usługa Azure AD umożliwia sprawdzenie, czy urządzenie wymaga rejestracji zarządzania urządzeniami przenośnymi i rejestruje go, jeśli ma to zastosowanie.

- Jeśli jesteś użytkownikiem zarządzanym, Windows spowoduje przejście do pulpitu za pomocą automatycznego logowania.

- Jeśli użytkownik federacyjny, możesz wyświetli się na ekranie logowania Windows o podanie poświadczeń.


## <a name="registering-a-device"></a>Zarejestruj urządzenie

Ta sekcja zawiera kroki, aby zarejestrować urządzenia z systemem Windows 10 do usługi Azure AD. Pomyślnie zarejestrowano urządzenie jest wyświetlane przy użyciu **pracy konta służbowego** wpisu.

![Zarejestruj subskrypcję](./media/device-management-azuread-registered-devices-windows10-setup/08.png)


**Aby zarejestrować urządzenia z systemem Windows 10:**

1. W **Start** menu, kliknij przycisk **ustawienia**.

    ![Ustawienia](./media/device-management-azuread-registered-devices-windows10-setup/01.png)

2. Kliknij przycisk **kont**.

    ![Konta](./media/device-management-azuread-registered-devices-windows10-setup/02.png)


3. Kliknij przycisk **dostęp do zasobów służbowych**.

    ![Dostęp do zasobów służbowych](./media/device-management-azuread-registered-devices-windows10-setup/03.png)

4. Na **dostęp do zasobów służbowych** okno dialogowe, kliknij przycisk **Connect**.

    ![Połączenie](./media/device-management-azuread-registered-devices-windows10-setup/04.png)


5. Na **Konfigurowanie konta służbowego lub szkolnego** okno dialogowe, wprowadź nazwę konta (na przykład someone@example.com), a następnie kliknij przycisk **dalej**.

    ![Połączenie](./media/device-management-azuread-registered-devices-windows10-setup/06.png)


6. Na **wprowadź hasło** okno dialogowe, wprowadź hasło, a następnie kliknij przycisk **dalej**.

    ![Połączenie](./media/device-management-azuread-registered-devices-windows10-setup/05.png)


7. Na **wszystko jest gotowe** okno dialogowe, kliknij przycisk **gotowe**.

    ![Połączenie](./media/device-management-azuread-registered-devices-windows10-setup/07.png)

## <a name="verification"></a>Weryfikacja

Aby sprawdzić, czy urządzenie jest przyłączone do usługi Azure AD, możesz przejrzeć **dostęp do zasobów służbowych** okna dialogowego na urządzeniu.

![Zarejestruj subskrypcję](./media/device-management-azuread-registered-devices-windows10-setup/08.png)

Alternatywnie możesz przejrzeć ustawienia urządzenia w portalu usługi Azure AD.

![Zarejestruj subskrypcję](./media/device-management-azuread-registered-devices-windows10-setup/09.png)





## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać więcej informacji, zobacz [wprowadzenie do zarządzania urządzeniami w usłudze Azure Active Directory](../device-management-introduction.md)

- Aby uzyskać więcej informacji na temat zarządzania urządzeniami w portalu usługi Azure AD, zobacz [zarządzania urządzeniami przy użyciu witryny Azure portal ](../device-management-azure-portal.md).




