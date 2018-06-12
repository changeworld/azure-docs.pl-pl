---
title: Konfigurowanie usługi Azure Active Directory urządzeniach przyłączonych do | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować urządzenia przyłączone do usługi Azure Active Directory.
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
ms.openlocfilehash: 791181f11af38e4b78743ddb372207bd21893a6b
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35260933"
---
# <a name="set-up-azure-active-directory-joined-devices"></a>Urządzeniach przyłączonych do konfigurowania usługi Azure Active Directory

Z zarządzania urządzeniami w usłudze Azure Active Directory (Azure AD) można zapewnić, że użytkownicy uzyskują dostęp do zasobów z urządzeń, które spełniają standardy zabezpieczeń i zgodności. Aby uzyskać więcej informacji, zobacz [wprowadzenie do zarządzania urządzeniami w usłudze Azure Active Directory](device-management-introduction.md).

Jeśli chcesz przełączyć urządzenia należące do pracy z systemem Windows 10 pod kontrolą programu Azure AD, można to zrobić przez skonfigurowanie usługi Azure AD przyłączone do urządzeń. W tym temacie przedstawiono kroki pokrewne. 


## <a name="prerequisites"></a>Wymagania wstępne

Aby przyłączyć urządzenie z systemem Windows 10, usługi rejestracji urządzeń muszą być skonfigurowane do włączenia rejestracji urządzeń. Oprócz mając uprawnienia do przyłączania urządzeń w Twojej dzierżawie usługi Azure AD, musi mieć mniej urządzeń zarejestrowanych niż skonfigurowana wartość maksymalna. Aby uzyskać więcej informacji, zobacz [skonfigurować ustawienia urządzenia](device-management-azure-portal.md#configure-device-settings).



## <a name="what-you-should-know"></a>Co należy wiedzieć


- System Windows nie przyłączy urządzenia w katalogu organizacji w usłudze Azure AD.

- Może być konieczne podąża żądania uwierzytelniania wieloskładnikowego. To żądanie jest konfigurowane przez administratora IT.

- Usługi Azure AD sprawdza, czy urządzenie wymaga rejestracji funkcji zarządzania urządzeniami przenośnymi i rejestruje go, jeśli to konieczne.

- Jeśli jesteś użytkownikiem zarządzanych systemu Windows umożliwia przejście do pulpitu za pośrednictwem automatycznego logowania.

- W przypadku użytkowników federacyjnych, musisz zalogować się przy użyciu poświadczeń.

- Jeśli użytkownik są Sfederowane, dostawcy tożsamości musi obsługiwać WS-Fed i WS-Trust punktu końcowego nazwy użytkownika i hasła. Może to być w wersji 1.3 lub 2005. Ta obsługa protokołu jest wymagany do dołączenie urządzenia do usługi Azure AD i zaloguj się do urządzenia za pomocą hasła. 




## <a name="joining-a-device"></a>Dołączenie urządzenia

Ta sekcja zawiera czynności, aby dołączyć urządzenie z systemem Windows 10 do usługi Azure AD. Pomyślnie dołączono urządzenia jest wyświetlany jako **połączony \<usługi Azure AD\>**.

![Połączono](./media/device-management-azuread-joined-devices-setup/13.png)


**Aby dołączyć urządzenie z systemem Windows 10:**

1. W **Start** menu, kliknij przycisk **ustawienia**.

    ![Ustawienia](./media/device-management-azuread-joined-devices-setup/01.png)

2. Kliknij przycisk **kont**.

    ![Konta](./media/device-management-azuread-joined-devices-setup/02.png)


3. Kliknij przycisk **dostępu służbowego**.

    ![Dostęp do służbowych.](./media/device-management-azuread-joined-devices-setup/03.png)

4. Na **dostępu służbowego** okna dialogowego, kliknij przycisk **Connect**.

    ![Połączenie](./media/device-management-azuread-joined-devices-setup/04.png)


5. Na **ustawienia konta firmowego lub szkolnego** okna dialogowego, kliknij przycisk **Dołącz to urządzenie w usłudze Azure Active Directory**.

    ![Połączenie](./media/device-management-azuread-joined-devices-setup/08.png)


6. Na **Zaloguj się** okna dialogowego, wprowadź nazwę konta (na przykład someone@example.com), a następnie kliknij przycisk **dalej**.

    ![Zaloguj się](./media/device-management-azuread-joined-devices-setup/10.png)


6. Na **wprowadź hasło** okna dialogowego, wprowadź hasło, a następnie kliknij przycisk **Zaloguj**.

    ![Wprowadź hasło](./media/device-management-azuread-joined-devices-setup/05.png)


7. Na **upewnij się, że jest to organizacja** okna dialogowego, kliknij przycisk **Join**.

    ![Upewnij się, że jest to organizacji](./media/device-management-azuread-joined-devices-setup/11.png)


8. Na **wszystko jest gotowe** okna dialogowego, kliknij przycisk **gotowe**.

    ![Wszystko jest gotowe](./media/device-management-azuread-joined-devices-setup/12.png)

## <a name="verification"></a>Weryfikacja

Aby sprawdzić, czy urządzenie jest dołączane do usługi Azure AD, możesz przejrzeć **dostępu służbowego** okna dialogowego na urządzeniu.

![Połączono](./media/device-management-azuread-joined-devices-setup/13.png)

Możesz też uruchomić następujące polecenie: `dsregcmd /status`  
Na urządzeniu pomyślnie dołączono **AzureAdJoined** jest **tak**.

![Połączono](./media/device-management-azuread-joined-devices-setup/14.png)

Można również przejrzeć ustawienia urządzenia w portalu usługi Azure AD.

![Połączono](./media/device-management-azuread-joined-devices-setup/15.png)

Aby uzyskać więcej informacji, zobacz [znaleźć urządzenia](device-management-azure-portal.md#locate-devices).


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz: 

- [Wprowadzenie do zarządzania urządzeniami w usłudze Azure Active Directory](device-management-introduction.md)
- [Zarządzanie urządzeniami przy użyciu portalu Azure](device-management-azure-portal.md)
- 



