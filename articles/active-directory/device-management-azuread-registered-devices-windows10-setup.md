---
title: Konfigurowanie usługi Azure Active Directory zarejestrowanych urządzeń | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować usługi Azure Active Directory zarejestrowane urządzenia.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 541f37bb11c604567b356716db282d0e4e2f629b
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34713528"
---
# <a name="set-up-azure-active-directory-registered-windows-10-devices"></a>Konfigurowanie usługi Azure Active Directory zarejestrowanych urządzeń z systemem Windows 10

Z zarządzania urządzeniami w usłudze Azure Active Directory (Azure AD) można zapewnić, że użytkownicy uzyskują dostęp do zasobów z urządzeń, które spełniają standardy zabezpieczeń i zgodności. Aby uzyskać więcej informacji, zobacz [wprowadzenie do zarządzania urządzeniami w usłudze Azure Active Directory](device-management-introduction.md).

Jeśli chcesz włączyć **urządzenia (PRZYNIEŚ własne)** scenariusz, można to zrobić przez skonfigurowanie usługi Azure AD zarejestrowanych urządzeń. W usłudze Azure AD możesz skonfigurować urządzenia usługi Azure AD w zarejestrowany dla systemu Windows 10, iOS, Android i macOS. Ten artykuł zawiera kroki pokrewne dla urządzeń z systemem Windows 10. 


## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby zarejestrować urządzenia z systemem Windows 10, usługi rejestracji urządzeń muszą być skonfigurowane do włączenia rejestracji urządzeń. Ponadto musi mieć mniej urządzeń zarejestrowanych niż skonfigurowana wartość maksymalna. Aby uzyskać więcej informacji, zobacz [skonfigurować ustawienia urządzenia](device-management-azure-portal.md#configure-device-settings).

## <a name="what-you-should-know"></a>Co należy wiedzieć

Podczas rejestrowania urządzenia, należy mieć następujące na uwadze:

- Windows zarejestrowanie urządzenia w katalogu organizacji w usłudze Azure AD

- Może być konieczne podąża żądania uwierzytelniania wieloskładnikowego. To żądanie jest konfigurowane przez administratora IT.

- Usługi Azure AD sprawdza, czy urządzenie wymaga rejestracji funkcji zarządzania urządzeniami przenośnymi i rejestruje go, jeśli to konieczne.

- Jeśli jesteś użytkownikiem zarządzanych systemu Windows umożliwia przejście do pulpitu za pośrednictwem automatycznego logowania.

- W przypadku użytkowników federacyjnych, nastąpi przekierowanie do ekranu logowania systemu Windows o podanie poświadczeń.


## <a name="registering-a-device"></a>Rejestrowanie urządzenia

Ta sekcja zawiera proste kroki, aby zarejestrować urządzenie z systemem Windows 10 do usługi Azure AD. Pomyślnie zarejestrowano urządzenia zostaną wyświetlone z **pracy konto służbowe** wpisu.

![Zarejestruj subskrypcję](./media/device-management-azuread-registered-devices-windows10-setup/08.png)


**Aby zarejestrować urządzenia z systemem Windows 10:**

1. W **Start** menu, kliknij przycisk **ustawienia**.

    ![Ustawienia](./media/device-management-azuread-registered-devices-windows10-setup/01.png)

2. Kliknij przycisk **kont**.

    ![Konta](./media/device-management-azuread-registered-devices-windows10-setup/02.png)


3. Kliknij przycisk **dostępu służbowego**.

    ![Dostęp do służbowych.](./media/device-management-azuread-registered-devices-windows10-setup/03.png)

4. Na **dostępu służbowego** okna dialogowego, kliknij przycisk **Connect**.

    ![Połączenie](./media/device-management-azuread-registered-devices-windows10-setup/04.png)


5. Na **Konfigurowanie konta służbowego** okna dialogowego, wprowadź nazwę konta (na przykład someone@example.com), a następnie kliknij przycisk **dalej**.

    ![Połączenie](./media/device-management-azuread-registered-devices-windows10-setup/06.png)


6. Na **wprowadź hasło** okna dialogowego, wprowadź hasło, a następnie kliknij przycisk **dalej**.

    ![Połączenie](./media/device-management-azuread-registered-devices-windows10-setup/05.png)


7. Na **wszystko jest gotowe** okna dialogowego, kliknij przycisk **gotowe**.

    ![Połączenie](./media/device-management-azuread-registered-devices-windows10-setup/07.png)

## <a name="verification"></a>Weryfikacja

Aby sprawdzić, czy urządzenie jest dołączane do usługi Azure AD, możesz przejrzeć **dostępu służbowego** okna dialogowego na urządzeniu.

![Zarejestruj subskrypcję](./media/device-management-azuread-registered-devices-windows10-setup/08.png)

Alternatywnie możesz przejrzeć ustawienia urządzenia w portalu usługi Azure AD.

![Zarejestruj subskrypcję](./media/device-management-azuread-registered-devices-windows10-setup/09.png)





## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać więcej informacji, zobacz [wprowadzenie do zarządzania urządzeniami w usłudze Azure Active Directory](device-management-introduction.md)

- Aby uzyskać więcej informacji na temat zarządzania urządzeniami w portalu usługi Azure AD, zobacz [zarządzanie urządzeniami przy użyciu portalu Azure ](device-management-azure-portal.md).




