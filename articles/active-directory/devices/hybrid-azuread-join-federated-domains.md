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
ms.openlocfilehash: cc6d08de74097ba7566037664fd33d9be85ac390
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39628992"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-federated-domains"></a>Samouczek: Konfigurowanie hybrydowej usługi Azure Active Directory join przez domeny federacyjne

W podobny sposób jak użytkownik urządzenie staje się innej tożsamości, które chcesz chronić, a także użyć do ochrony zasobów w dowolnym czasie i miejscu. W tym celu można wykonać, przenosząc tożsamości swoje urządzenia do usługi Azure AD przy użyciu jednej z następujących metod:

- Funkcja Azure AD join
- Dołączenie do hybrydowej usługi Azure AD
- Rejestracja w usłudze Azure AD

Między zasobami w chmurze i lokalnych, przenosząc urządzeń do usługi Azure AD, zmaksymalizuj produktywność użytkowników za pomocą logowania jednokrotnego (SSO). W tym samym czasie można zabezpieczyć dostęp do zasobów w chmurze i lokalnych przy użyciu [dostępu warunkowego](../active-directory-conditional-access-azure-portal.md).

W tym samouczku dowiesz się, jak skonfigurować hybrydowych usługi Azure AD join dla urządzeń, które są Sfederowane za pomocą usług AD FS.

> [!div class="checklist"]
> * Konfiguruj dołączenie do hybrydowej usługi Azure AD
> * Włączanie na urządzeniach Windows niższego poziomu
> * Weryfikuj rejestrację
> * Rozwiązywanie problemów


## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku przyjęto założenie, że znasz:

-  [Wprowadzenie do zarządzania urządzeniami w usłudze Azure Active Directory](../device-management-introduction.md)

-  [Jak planować implementację z hybrydowym dołączaniem do usługi Azure Active Directory](hybrid-azuread-join-plan.md)

-  [Jak kontrolować hybrydowe dołączanie Twoich urządzeń do usługi Azure AD](hybrid-azuread-join-control.md)


Aby skonfigurować scenariusz, w ramach tego samouczka, potrzebne są:

- Windows Server 2012 R2 z usługami AD FS

- [Program Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) wersji 1.1.819.0 lub nowszej. 
 

Począwszy od wersji 1.1.819.0, program Azure AD Connect zapewnia za pomocą kreatora, aby skonfigurować dołączenie do hybrydowej usługi Azure AD. Kreator umożliwia znacznie upraszcza proces konfigurowania. Kreator pokrewne:

- Konfiguruje punktów połączenia usługi (SCP) dla rejestracji urządzeń

- Tworzy kopię zapasową istniejących Azure AD zaufanie jednostki uzależnionej

- Aktualizuje reguły oświadczeń w relacji zaufania usługi Azure AD

Kroki konfiguracji, w tym artykule są oparte na tego kreatora. W przypadku starszej wersji programu Azure AD Connect, należy uaktualnić go do 1.1.819 lub nowszej. Jeśli zainstalowanie najnowszej wersji programu Azure AD Connect nie jest dostępną opcją w, zobacz [jak ręcznie skonfigurować rejestrację urządzeń](../device-management-hybrid-azuread-joined-devices-setup.md).

Dołączenie do hybrydowej usługi Azure AD wymaga, aby urządzenia mają mieć dostęp do następujących zasobów firmy Microsoft z wewnątrz sieci organizacji:  

- https://enterpriseregistration.windows.net
- https://login.microsoftonline.com
- https://device.login.microsoftonline.com
- Twoja organizacja usługi STS (domen federacyjnych)
- https://autologon.microsoftazuread-sso.com (Jeśli są przy użyciu lub planowane jest używanie bezproblemowe logowanie Jednokrotne)

Jeśli Twoja organizacja wymaga dostępu do Internetu za pośrednictwem serwera proxy ruchu wychodzącego, począwszy od systemu Windows 10 1709 można skonfigurować ustawienia serwera proxy na komputerze przy użyciu obiektu zasad grupy (GPO). Jeśli na komputerze jest uruchomiony niczego, które są starsze niż Windows 10 1709, należy zaimplementować autowykrywania serwera Proxy sieci Web (WPAD) umożliwia komputerom systemu Windows 10 w celu rejestracji urządzeń w usłudze Azure AD. 

Jeśli Twoja organizacja wymaga dostępu do Internetu za pośrednictwem uwierzytelnionego serwera proxy ruchu wychodzącego, upewnij się, że komputery z systemem Windows 10 może pomyślnie uwierzytelnić się serwera proxy ruchu wychodzącego. Ponieważ komputery z systemem Windows 10 są uruchamiane rejestracji urządzenia przy użyciu kontekstu komputera, należy skonfigurować uwierzytelnianie serwera proxy ruchu wychodzącego przy użyciu kontekstu maszyny. Śledzenie przy użyciu dostawcy serwera proxy ruchu wychodzącego wymagania dotyczące konfiguracji. 


## <a name="configure-hybrid-azure-ad-join"></a>Konfiguruj dołączenie do hybrydowej usługi Azure AD

Aby skonfigurować dołączenie do hybrydowej usługi Azure AD za pomocą usługi Azure AD Connect, potrzebne są:

- Poświadczenia administratora globalnego dla dzierżawy usługi Azure AD.  

- Poświadczenia administratora przedsiębiorstwa dla każdego lasu.

- Poświadczenia administratora usług AD FS. 


**Aby skonfigurować dołączenie do hybrydowej usługi Azure AD, za pomocą usługi Azure AD Connect:**

1. Uruchom program Azure AD Connect, a następnie kliknij przycisk **Konfiguruj**.

    ![Zapraszamy!](./media/hybrid-azuread-join-federated-domains/11.png)

2. Na **dodatkowe zadania** wybierz opcję **Konfiguruj opcje urządzenia**, a następnie kliknij przycisk **dalej**. 

    ![Dodatkowe zadania](./media/hybrid-azuread-join-federated-domains/12.png)

3. Na **Przegląd** kliknij **dalej**. 

    ![Przegląd](./media/hybrid-azuread-join-federated-domains/13.png)

4. Na **nawiązywanie połączenia z usługi Azure AD** strony, wprowadź poświadczenia administratora globalnego dla dzierżawy usługi Azure AD, a następnie kliknij przycisk **dalej**.   

    ![Łączenie z usługą Azure AD](./media/hybrid-azuread-join-federated-domains/14.png)

5. Na **opcje urządzenia** wybierz opcję **Konfigurowanie hybrydowej usługi Azure AD join**, a następnie kliknij przycisk **dalej**. 

    ![Opcje urządzenia](./media/hybrid-azuread-join-federated-domains/15.png)

6. Na **SCP** strony, wykonaj następujące czynności, a następnie kliknij przycisk **dalej**: 

    ![Punkt połączenia z usługą](./media/hybrid-azuread-join-federated-domains/16.png)

    a. Wybierz las.

    b. Wybierz usługę uwierzytelniania.

    c. Kliknij przycisk **Dodaj** wprowadzić poświadczenia administratora przedsiębiorstwa.


7. Na **systemów operacyjnych urządzeń** strony, wybierz systemy operacyjne używane przez urządzenia w środowisku usługi Active Directory, a następnie kliknij przycisk **dalej**. 

    ![System operacyjny urządzenia](./media/hybrid-azuread-join-federated-domains/17.png)

8. Na **Konfiguracja Federacji** strony, wprowadź poświadczenia administratora usług AD FS, a następnie kliknij przycisk **dalej**. 

    ![Konfiguracja federacji](./media/hybrid-azuread-join-federated-domains/18.png)

9. Na **wszystko gotowe do skonfigurowania** kliknij **Konfiguruj**. 

    ![Wszystko gotowe do skonfigurowania](./media/hybrid-azuread-join-federated-domains/19.png)

10. Na **ukończyć konfiguracji** kliknij **zakończenia**. 

    ![Ukończono konfigurację](./media/hybrid-azuread-join-federated-domains/20.png)




## <a name="enable-windows-down-level-devices"></a>Włączanie na urządzeniach Windows niższego poziomu

W przypadku niektórych urządzeń przyłączonych do domeny systemu Windows niższego poziomu urządzeń, należy:

- Aktualizuj ustawienia urządzenia
 
- Skonfiguruj ustawienia lokalny intranet dla rejestracji urządzeń


### <a name="update-device-settings"></a>Aktualizuj ustawienia urządzenia 

Aby zarejestrować urządzenia niskiego poziomu Windows, należy się upewnić, że ustawienia urządzenia, aby umożliwić użytkownikom rejestrowanie urządzeń w usłudze Azure AD są ustawione. W witrynie Azure portal możesz znaleźć tego ustawienia:

`Home > [Name of your tenant] > Devices - Device settings`  


    
Następujące zasady musi być równa **wszystkich**: **użytkownicy mogą rejestrować swoje urządzenia w usłudze Azure AD**

![Rejestrowanie urządzeń](./media/hybrid-azuread-join-federated-domains/23.png)


### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Skonfiguruj ustawienia lokalny intranet dla rejestracji urządzeń

Hybrydowym zostało ukończone pomyślnie usługi Azure AD join urządzeń Windows niższego poziomu, a w celu uniknięcia monity certyfikatu podczas uwierzytelniania urządzeń uwierzytelniania usługi Azure AD, możesz wypchnąć zasad na urządzeniach przyłączonych do domeny można dodać następujące adresy URL do lokalnego intranetu Strefa w programie Internet Explorer:

- `https://device.login.microsoftonline.com`

- `https://device.login.microsoftonline.com`

- Usługa tokenu zabezpieczającego (STS - domen federacyjnych) w organizacji

- `https://autologon.microsoftazuread-sso.com` (w przypadku bezproblemowe logowanie Jednokrotne).

Ponadto należy włączyć **zezwala na aktualizacje na pasku stanu za pomocą skryptu** w strefie Lokalny intranet użytkownika.



## <a name="verify-the-registration"></a>Weryfikuj rejestrację

Aby sprawdzić stanu rejestracji urządzenia w dzierżawie platformy Azure, możesz użyć ** [Get MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice) ** polecenia cmdlet w ** [modułu programu PowerShell usługi Azure Active Directory](/powershell/azure/install-msonlinev1?view=azureadps-2.0)**.

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
> [Konfigurowanie hybrydowej usługi Azure Active Directory join dla domen zarządzanych](hybrid-azuread-join-managed-domains.md)
> [ręcznie skonfigurować dołączenie do hybrydowej usługi Azure Active Directory](hybrid-azuread-join-manual-steps.md)




<!--Image references-->
[1]: ./media/active-directory-conditional-access-automatic-device-registration-setup/12.png
