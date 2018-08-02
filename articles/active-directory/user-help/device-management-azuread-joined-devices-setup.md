---
title: Konfigurowanie usługi Azure Active Directory urządzeń przyłączonych do | Dokumentacja firmy Microsoft
description: Dowiedz się, jak konfigurowanie urządzeń przyłączonych do usługi Azure Active Directory.
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
ms.openlocfilehash: 83eea565cc775a0569015c04d79d627d6ba120e6
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39411713"
---
# <a name="set-up-azure-active-directory-joined-devices"></a>Konfigurowanie usługi Azure Active Directory urządzeń przyłączonych do

Za pomocą zarządzania urządzeniami w usłudze Azure Active Directory (Azure AD) można zagwarantować, że użytkownicy uzyskują dostęp do zasobów z urządzeń, które spełniają Twoje standardy dotyczące bezpieczeństwa i zgodności. Aby uzyskać więcej informacji, zobacz [wprowadzenie do zarządzania urządzeniami w usłudze Azure Active Directory](../device-management-introduction.md).

Jeśli chcesz wyświetlić urządzenia należące do pracy systemu Windows 10 pod kontrolą programu Azure AD, można to zrobić, konfigurowanie urządzeń przyłączonych do usługi Azure AD. W tym temacie przedstawiono kroki powiązane. 


## <a name="prerequisites"></a>Wymagania wstępne

Aby przyłączyć urządzenie z systemem Windows 10, usługi rejestracji urządzeń, musi być skonfigurowany umożliwiające rejestrowanie urządzeń. Oprócz mając uprawnienia do dołączania urządzeń w Twojej dzierżawie usługi Azure AD, musi mieć mniej urządzeń zarejestrowanych niż skonfigurowane maksimum. Aby uzyskać więcej informacji, zobacz [Konfigurowanie ustawień urządzenia](../devices/device-management-azure-portal.md#configure-device-settings).



## <a name="what-you-should-know"></a>Co należy wiedzieć


- Windows nie przyłączy urządzenia w katalogu organizacji w usłudze Azure AD.

- Może być konieczne przechodzą przez wezwanie do uwierzytelnienia Multi-Factor Authentication. To żądanie jest konfigurowane przez administratora IT.

- Usługa Azure AD umożliwia sprawdzenie, czy urządzenie wymaga rejestracji zarządzania urządzeniami przenośnymi i rejestruje go, jeśli ma to zastosowanie.

- Jeśli jesteś użytkownikiem zarządzanym, Windows spowoduje przejście do pulpitu za pomocą automatycznego logowania.

- Jeśli jesteś użytkownikiem federacyjnego, trzeba Zaloguj się przy użyciu poświadczeń.

- Jeśli użytkownik są Sfederowane, dostawcy tożsamości musi obsługiwać WS-Fed i WS-Trust punkt końcowy nazwy użytkownika i hasła. Może to być w wersji 1.3 lub 2005. Ta obsługa protokołu jest wymagany do przyłączenia urządzenia do usługi Azure AD i zaloguj się do urządzenia przy użyciu hasła. 




## <a name="joining-a-device"></a>Łączenie urządzenia

Ta sekcja zawiera proste kroki, aby dołączyć urządzenie z systemem Windows 10 do usługi Azure AD. Pomyślnie dołączono urządzenie jest wyświetlane jako **połączone \<usługi Azure AD\>**.

![Połączono](./media/device-management-azuread-joined-devices-setup/13.png)


**Aby dołączyć urządzenie z systemem Windows 10:**

1. W **Start** menu, kliknij przycisk **ustawienia**.

    ![Ustawienia](./media/device-management-azuread-joined-devices-setup/01.png)

2. Kliknij przycisk **kont**.

    ![Konta](./media/device-management-azuread-joined-devices-setup/02.png)


3. Kliknij przycisk **dostęp do zasobów służbowych**.

    ![Dostęp do zasobów służbowych](./media/device-management-azuread-joined-devices-setup/03.png)

4. Na **dostęp do zasobów służbowych** okno dialogowe, kliknij przycisk **Connect**.

    ![Połączenie](./media/device-management-azuread-joined-devices-setup/04.png)


5. Na **Konfigurowanie konta służbowego lub szkolnego** okno dialogowe, kliknij przycisk **Dołącz to urządzenie do usługi Azure Active Directory**.

    ![Połączenie](./media/device-management-azuread-joined-devices-setup/08.png)


6. Na **Zaloguj się** okno dialogowe, wprowadź nazwę konta (na przykład someone@example.com), a następnie kliknij przycisk **dalej**.

    ![Zaloguj się](./media/device-management-azuread-joined-devices-setup/10.png)


6. Na **wprowadź hasło** okno dialogowe, wprowadź hasło, a następnie kliknij przycisk **Zaloguj**.

    ![Wprowadź hasło](./media/device-management-azuread-joined-devices-setup/05.png)


7. Na **upewnij się, czy Twoja organizacja jest to** okno dialogowe, kliknij przycisk **Dołącz**.

    ![Upewnij się, że jest to Twoja organizacja](./media/device-management-azuread-joined-devices-setup/11.png)


8. Na **wszystko jest gotowe** okno dialogowe, kliknij przycisk **gotowe**.

    ![Wszystko jest gotowe](./media/device-management-azuread-joined-devices-setup/12.png)

## <a name="verification"></a>Weryfikacja

Aby sprawdzić, czy urządzenie jest przyłączone do usługi Azure AD, możesz przejrzeć **dostęp do zasobów służbowych** okna dialogowego na urządzeniu.

![Połączono](./media/device-management-azuread-joined-devices-setup/13.png)

Można również uruchomić następujące polecenie: `dsregcmd /status`  
Na urządzeniu pomyślnie dołączono **AzureAdJoined** jest **tak**.

![Połączono](./media/device-management-azuread-joined-devices-setup/14.png)

Możesz również przejrzeć ustawienia urządzenia w portalu usługi Azure AD.

![Połączono](./media/device-management-azuread-joined-devices-setup/15.png)

Aby uzyskać więcej informacji, zobacz [lokalizowanie urządzeń](../devices/device-management-azure-portal.md#locate-devices).


## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji, zobacz: 

- [Wprowadzenie do zarządzania urządzeniami w usłudze Azure Active Directory](../devices/overview.md)
- [Zarządzanie urządzeniami za pomocą witryny Azure portal](../devices/device-management-azure-portal.md)


