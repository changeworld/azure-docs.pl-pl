---
title: Jak skonfigurować hybrydowych urządzeń w przyłączonych do usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak konfigurowanie urządzeń przyłączonych do usługi Azure Active Directory hybrydowych.
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
ms.date: 07/31/2018
ms.author: markvi
ms.reviewer: sandeo
ms.openlocfilehash: f61f0649900c27a6aa3a873f60dc60b4985f5d4f
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39424191"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-managed-domains"></a>Samouczek: Konfigurowanie hybrydowych usługi Azure Active Directory join dla domeny zarządzanej

W podobny sposób jak użytkownik urządzenie staje się innej tożsamości, które chcesz chronić, a także użyć do ochrony zasobów w dowolnym czasie i miejscu. W tym celu można wykonać, przenosząc tożsamości swoje urządzenia do usługi Azure AD przy użyciu jednej z następujących metod:

- Funkcja Azure AD join
- Dołączenie do hybrydowej usługi Azure AD
- Rejestracja w usłudze Azure AD

Między zasobami w chmurze i lokalnych, przenosząc urządzeń do usługi Azure AD, zmaksymalizuj produktywność użytkowników za pomocą logowania jednokrotnego (SSO). W tym samym czasie można zabezpieczyć dostęp do zasobów w chmurze i lokalnych przy użyciu [dostępu warunkowego](../active-directory-conditional-access-azure-portal.md).

W tym samouczku dowiesz się, jak skonfigurować hybrydowych usługi Azure AD join dla urządzeń w domenach zarządzanych.

> [!div class="checklist"]
> * Konfiguruj dołączenie do hybrydowej usługi Azure AD
> * Włączanie na urządzeniach Windows niższego poziomu
> * Sprawdź urządzenia przyłączone 
> * Rozwiązywanie problemów 


## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku przyjęto założenie, że znasz:
    
-  [Wprowadzenie do zarządzania urządzeniami w usłudze Azure Active Directory](../device-management-introduction.md)
    
-  [Jak planować implementację z hybrydowym dołączaniem do usługi Azure Active Directory](hybrid-azuread-join-plan.md)

Aby skonfigurować scenariusz, w tym artykule, musisz mieć [najnowszą wersję programu Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) (1.1.819.0 lub nowszej) do zainstalowania. 
 

Począwszy od wersji 1.1.819.0, program Azure AD Connect zapewnia za pomocą kreatora, aby skonfigurować dołączenie do hybrydowej usługi Azure AD. Kreator umożliwia znacznie upraszcza proces konfigurowania. Powiązane Kreator konfiguruje punktów połączenia usługi (SCP) dla rejestracji urządzeń.

Kroki konfiguracji, w tym artykule są oparte na tego kreatora. 

Dołączenie do hybrydowej usługi Azure AD wymaga, aby urządzenia mają mieć dostęp do następujących zasobów firmy Microsoft z wewnątrz sieci organizacji:  

- https://enterpriseregistration.windows.net
- https://login.microsoftonline.com
- https://device.login.microsoftonline.com
- https://autologon.microsoftazuread-sso.com (Jeśli są przy użyciu lub planowane jest używanie bezproblemowe logowanie Jednokrotne)

Jeśli Twoja organizacja wymaga dostępu do Internetu za pośrednictwem serwera proxy ruchu wychodzącego, począwszy od systemu Windows 10 1709 można skonfigurować ustawienia serwera proxy na komputerze przy użyciu obiektu zasad grupy (GPO). Jeśli na komputerze jest uruchomiony niczego, które są starsze niż Windows 10 1709, należy zaimplementować autowykrywania serwera Proxy sieci Web (WPAD) umożliwia komputerom systemu Windows 10 w celu rejestracji urządzeń w usłudze Azure AD. 

Jeśli Twoja organizacja wymaga dostępu do Internetu za pośrednictwem uwierzytelnionego serwera proxy ruchu wychodzącego, upewnij się, że komputery z systemem Windows 10 może pomyślnie uwierzytelnić się serwera proxy ruchu wychodzącego. Ponieważ komputery z systemem Windows 10 są uruchamiane rejestracji urządzenia przy użyciu kontekstu komputera, należy skonfigurować uwierzytelnianie serwera proxy ruchu wychodzącego przy użyciu kontekstu maszyny. Śledzenie przy użyciu dostawcy serwera proxy ruchu wychodzącego wymagania dotyczące konfiguracji. 



## <a name="configure-hybrid-azure-ad-join"></a>Konfiguruj dołączenie do hybrydowej usługi Azure AD

Aby skonfigurować dołączenie do hybrydowej usługi Azure AD za pomocą usługi Azure AD Connect, potrzebne są:

- Poświadczenia administratora globalnego dla dzierżawy usługi Azure AD.  

- Poświadczenia administratora przedsiębiorstwa dla każdego lasu.


**Aby skonfigurować dołączenie do hybrydowej usługi Azure AD, za pomocą usługi Azure AD Connect:**

1. Uruchom program Azure AD Connect, a następnie kliknij przycisk **Konfiguruj**.

    ![Zapraszamy!](./media/hybrid-azuread-join-managed-domains/11.png)

2. Na **dodatkowe zadania** wybierz opcję **Konfiguruj opcje urządzenia**, a następnie kliknij przycisk **dalej**. 

    ![Dodatkowe zadania](./media/hybrid-azuread-join-managed-domains/12.png)

3. Na **Przegląd** kliknij **dalej**. 

    ![Przegląd](./media/hybrid-azuread-join-managed-domains/13.png)

4. Na **nawiązywanie połączenia z usługi Azure AD** strony, wprowadź poświadczenia administratora globalnego dla dzierżawy usługi Azure AD.  

    ![Łączenie z usługą Azure AD](./media/hybrid-azuread-join-managed-domains/14.png)

5. Na **opcje urządzenia** wybierz opcję **Konfigurowanie hybrydowej usługi Azure AD join**, a następnie kliknij przycisk **dalej**. 

    ![Opcje urządzenia](./media/hybrid-azuread-join-managed-domains/15.png)

6. Na **SCP** strony dla każdego lasu, ma punkt połączenia usługi Azure AD Connect, wykonaj następujące czynności, a następnie kliknij **dalej**: 

    ![Punkt połączenia z usługą](./media/hybrid-azuread-join-managed-domains/16.png)

    a. Wybierz las.

    b. Wybierz usługę uwierzytelniania.

    c. Kliknij przycisk **Dodaj** wprowadzić poświadczenia administratora przedsiębiorstwa.


7. Na **systemów operacyjnych urządzeń** strony, wybierz systemy operacyjne używane przez urządzenia w środowisku usługi Active Directory, a następnie kliknij przycisk **dalej**. 

    ![System operacyjny urządzenia](./media/hybrid-azuread-join-managed-domains/17.png)


8. Na **wszystko gotowe do skonfigurowania** kliknij **Konfiguruj**. 

    ![Wszystko gotowe do skonfigurowania](./media/hybrid-azuread-join-managed-domains/19.png)

9. Na **ukończyć konfiguracji** kliknij **zakończenia**. 

    ![Ukończono konfigurację](./media/hybrid-azuread-join-managed-domains/20.png)




## <a name="enable-windows-down-level-devices"></a>Włączanie na urządzeniach Windows niższego poziomu

W przypadku niektórych urządzeń przyłączonych do domeny systemu Windows niższego poziomu urządzeń, należy:

- Aktualizuj ustawienia urządzenia
 
- Skonfiguruj ustawienia lokalny intranet dla rejestracji urządzeń

### <a name="update-device-settings"></a>Aktualizuj ustawienia urządzenia 

Aby zarejestrować urządzenia niskiego poziomu Windows, należy się upewnić, że ustawienia urządzenia, aby umożliwić użytkownikom rejestrowanie urządzeń w usłudze Azure AD są ustawione. W witrynie Azure portal możesz znaleźć tego ustawienia:

`Home > [Name of your tenant] > Devices - Device settings`  


    
Następujące zasady musi być równa **wszystkich**: **użytkownicy mogą rejestrować swoje urządzenia w usłudze Azure AD**

![Rejestrowanie urządzeń](media/hybrid-azuread-join-managed-domains/23.png)



### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Skonfiguruj ustawienia lokalny intranet dla rejestracji urządzeń

Hybrydowym zostało ukończone pomyślnie usługi Azure AD join urządzeń Windows niższego poziomu, a w celu uniknięcia monity certyfikatu podczas uwierzytelniania urządzeń uwierzytelniania usługi Azure AD, możesz wypchnąć zasad na urządzeniach przyłączonych do domeny można dodać następujące adresy URL do lokalnego intranetu Strefa w programie Internet Explorer:

- `https://device.login.microsoftonline.com`

- `https://device.login.microsoftonline.com`

- `https://autologon.microsoftazuread-sso.com`.

Ponadto należy włączyć **zezwala na aktualizacje na pasku stanu za pomocą skryptu** w strefie Lokalny intranet użytkownika.

## <a name="verify-the-registration"></a>Weryfikuj rejestrację

Aby sprawdzić stanu rejestracji urządzenia w dzierżawie platformy Azure, możesz użyć **[Get MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice)** polecenia cmdlet w  **[modułu programu PowerShell usługi Azure Active Directory](/powershell/azure/install-msonlinev1?view=azureadps-2.0)**.

Korzystając z **Get MSolDevice** polecenia cmdlet, aby sprawdzić szczegóły usługi:

- Obiekt o **identyfikator urządzenia** odpowiadającej Identyfikatora w Windows, klient musi istnieć.
- Wartość **DeviceTrustType** musi być **przyłączone do domeny**. Jest to równoważne **przyłączone do hybrydowej usługi Azure AD** stanu na stronie urządzenia w portalu usługi Azure AD.
- Wartość **włączone** musi być **True** dla urządzeń, które są używane w funkcji dostępu warunkowego. 


**Aby sprawdzić szczegóły usługi:**

1. Otwórz **programu Windows PowerShell** jako administrator.

2. Typ `Connect-MsolService` połączyć z dzierżawą platformy Azure.  

3. Wpisz polecenie `get-msoldevice -deviceId <deviceId>`.

6. Upewnij się, że **włączone** ustawiono **True**.





## <a name="troubleshoot-your-implementation"></a>Rozwiązywanie problemów z implementacji

Jeśli występują problemy z ukończeniem poszczególnych hybrydowych urządzeń Windows w przyłączonych do usługi Azure AD join dla domeny, zobacz:

- [Rozwiązywanie problemów z dołączenie do hybrydowej usługi Azure AD dla Windows bieżące urządzenia](troubleshoot-hybrid-join-windows-current.md)
- [Rozwiązywanie problemów z dołączenie do hybrydowej usługi Azure AD dla Windows niższego poziomu urządzeń](troubleshoot-hybrid-join-windows-legacy.md)


## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Konfiguruj dołączenie do usługi Azure Active Directory hybrydowej przez domeny federacyjne](hybrid-azuread-join-federated-domains.md)
> [ręcznie skonfigurować dołączenie do hybrydowej usługi Azure Active Directory](hybrid-azuread-join-manual-steps.md)

