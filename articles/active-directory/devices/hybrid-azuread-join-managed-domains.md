---
title: Konfigurowanie hybrydowego dołączania do usługi Azure Active Directory dla domen zarządzanych | Microsoft Docs
description: Dowiedz się, jak skonfigurować hybrydowe dołączanie do usługi Azure Active Directory dla domen zarządzanych.
services: active-directory
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: joflore
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 94fbcc1c29c1860642dda0afab6a1f10e719a05a
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962190"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-managed-domains"></a>Samouczek: Konfigurowanie hybrydowego dołączania do usługi Azure Active Directory dla domen zarządzanych

Podobnie jak w przypadku użytkownika, urządzenie staje się kolejną tożsamością, którą należy chronić oraz używać do zabezpieczania zasobów w dowolnym czasie i miejscu. W tym celu można przenieść tożsamości urządzeń do usługi Azure AD przy użyciu jednej z następujących metod:

- Dołączenie do usługi Azure AD
- Dołączenie hybrydowe do usługi Azure AD
- Rejestracja w usłudze Azure AD

Przenosząc urządzenia do usługi Azure AD, można zmaksymalizować wydajność użytkowników dzięki zastosowaniu logowania jednokrotnego (SSO) w zasobach chmury i zasobach lokalnych. Jednocześnie można zapewnić bezpieczny dostęp do zasobów chmury i zasobów lokalnych przy użyciu [dostępu warunkowego](../active-directory-conditional-access-azure-portal.md).

W tym samouczku dowiesz się, jak skonfigurować dołączenie hybrydowe do usługi Azure AD dla urządzeń w domenach zarządzanych.

> [!div class="checklist"]
> * Konfigurowanie dołączenia hybrydowego do usługi Azure AD
> * Włączanie urządzeń z systemem Windows niższego poziomu
> * Weryfikowanie dołączonych urządzeń 
> * Rozwiązywanie problemów 


## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku założono, że znasz następujące informacje:
    
-  [Wprowadzenie do zarządzania urządzeniami w usłudze Azure Active Directory](../device-management-introduction.md)
    
-  [Jak planować implementację z hybrydowym dołączaniem do usługi Azure Active Directory](hybrid-azuread-join-plan.md)

-  [Jak kontrolować hybrydowe dołączanie Twoich urządzeń do usługi Azure AD](hybrid-azuread-join-control.md)

> [!NOTE]
> Usługa Azure AD nie obsługuje w domenach zarządzanych kart inteligentnych lub certyfikatów.


Aby skonfigurować scenariusz zaprezentowany w tym artykule, potrzebujesz następujących elementów:

- [Najnowsza wersja programu Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) (1.1.819.0 lub nowsza) do zainstalowania. 

Upewnij się, że program Azure AD Connect został zsynchronizowany z obiektami komputera urządzeń, które chcesz dołączyć hybrydowo do usługi Azure AD. Jeśli obiekty komputera należą do określonych jednostek organizacyjnych, to te jednostki muszą również być skonfigurowane na potrzeby synchronizacji w programie Azure AD Connect.

Począwszy od wersji 1.1.819.0, program Azure AD Connect zapewnia kreator umożliwiający konfigurowanie dołączania hybrydowego do usługi Azure AD. Kreator pozwala znacznie uprościć proces konfiguracji. Powiązany kreator konfiguruje punkty połączenia usługi (SCP, service connection point) na potrzeby rejestracji urządzenia.

Kroki konfiguracji w tym artykule zostały oparte na tym kreatorze. 

Dołączenie hybrydowe do usługi Azure AD wymaga urządzeń z dostępem do następujących zasobów Microsoft z wewnątrz sieci organizacji:  

- https://enterpriseregistration.windows.net
- https://login.microsoftonline.com
- https://device.login.microsoftonline.com
- https://autologon.microsoftazuread-sso.com (jeśli używasz lub planujesz użycie bezproblemowego logowania jednokrotnego)

Jeśli organizacja wymaga dostępu do Internetu za pośrednictwem serwera proxy ruchu wychodzącego, począwszy od systemu Windows 10 w wersji 1709 możesz [skonfigurować ustawienia serwera proxy na komputerze przy użyciu obiektu zasad grupy (GPO)](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/). Jeśli na komputerze masz system Windows 10 w wersji starszej niż 1709, musisz wdrożyć usługę autowykrywania internetowego serwera proxy (WPAD), aby umożliwić komputerom z systemem Windows 10 przeprowadzenie rejestracji urządzenia w usłudze Azure AD. 

Jeśli organizacja wymaga dostępu do Internetu za pośrednictwem uwierzytelnionego serwera proxy ruchu wychodzącego, musisz upewnić się, że komputery z systemem Windows 10 mogą pomyślnie uwierzytelnić się na serwerze proxy ruchu wychodzącego. Ponieważ komputery z systemem Windows 10 uruchamiają rejestrację urządzenia przy użyciu kontekstu maszyny, konfigurowanie uwierzytelniania serwera proxy ruchu wychodzącego należy wykonać właśnie przy użyciu kontekstu maszyny. Skontaktuj się z dostawcą serwera proxy ruchu wychodzącego, aby uzyskać informacje na temat wymagań dotyczących konfiguracji. 



## <a name="configure-hybrid-azure-ad-join"></a>Konfigurowanie dołączenia hybrydowego do usługi Azure AD

Aby skonfigurować dołączanie hybrydowe do usługi Azure AD przy użyciu programu Azure AD Connect, potrzebujesz następujących elementów:

- Poświadczenia administratora globalnego dzierżawy usługi Azure AD.  

- Poświadczenia administratora przedsiębiorstwa dla każdego lasu.


**Aby skonfigurować dołączanie hybrydowe do usługi Azure AD przy użyciu programu Azure AD Connect:**

1. Uruchom program Azure AD Connect, a następnie kliknij pozycję **Konfiguruj**.

    ![Powitanie](./media/hybrid-azuread-join-managed-domains/11.png)

2. Na stronie **Dodatkowe zadania** wybierz pozycję **Skonfiguruj opcje urządzenia**, a następnie kliknij pozycję **Dalej**. 

    ![Dodatkowe zadania](./media/hybrid-azuread-join-managed-domains/12.png)

3. Na stronie **Przegląd** kliknij pozycję **Dalej**. 

    ![Omówienie](./media/hybrid-azuread-join-managed-domains/13.png)

4. Na stronie **Łączenie z usługą Azure AD** wprowadź poświadczenia administratora globalnego dzierżawy usługi Azure AD.  

    ![Łączenie z usługą Azure AD](./media/hybrid-azuread-join-managed-domains/14.png)

5. Na stronie **Opcje urządzenia** wybierz pozycję **Skonfiguruj dołączenie hybrydowe do usługi Azure AD**, a następnie kliknij pozycję **Dalej**. 

    ![Opcje urządzenia](./media/hybrid-azuread-join-managed-domains/15.png)

6. Na stronie **SCP** dla każdego lasu, dla którego program Azure AD Connect powinien skonfigurować punkt połączenia usługi, wykonaj następujące kroki, a następnie kliknij przycisk **Dalej**: 

    ![Punkt połączenia z usługą](./media/hybrid-azuread-join-managed-domains/16.png)

    a. Wybierz las.

    b. Wybierz usługę uwierzytelniania.

    d. Kliknij pozycję **Dodaj**, aby wprowadzić poświadczenia administratora przedsiębiorstwa.


7. Na stronie **Systemy operacyjne urządzeń** wybierz systemy operacyjne używane przez urządzenia w środowisku usługi Active Directory, a następnie kliknij pozycję **Dalej**. 

    ![System operacyjny urządzenia](./media/hybrid-azuread-join-managed-domains/17.png)


8. Na stronie **Wszystko gotowe do skonfigurowania** kliknij pozycję **Konfiguruj**. 

    ![Wszystko gotowe do skonfigurowania](./media/hybrid-azuread-join-managed-domains/19.png)

9. Na stronie **Ukończono konfigurację** kliknij przycisk **Wyjdź**. 

    ![Ukończono konfigurację](./media/hybrid-azuread-join-managed-domains/20.png)




## <a name="enable-windows-down-level-devices"></a>Włączanie urządzeń z systemem Windows niższego poziomu

Jeśli część urządzeń dołączonych do domeny to urządzenia z systemem Windows niższego poziomu, musisz spełnić następujące warunki:

- Aktualizacja ustawień urządzenia
 
- Konfigurowanie ustawień lokalnego intranetu na potrzeby rejestracji urządzeń

- Konfigurowanie bezproblemowego logowania jednokrotnego

- Kontrolowanie urządzeń z systemem Windows niższego poziomu 


### <a name="update-device-settings"></a>Aktualizacja ustawień urządzenia 

Aby zarejestrować urządzenia z systemem Windows niższego poziomu, należy upewnić się, że skonfigurowano ustawienia urządzeń umożliwiające użytkownikom rejestrowanie urządzeń w usłudze Azure AD. W witrynie Azure Portal możesz znaleźć te ustawienia w obszarze:

`Home > [Name of your tenant] > Devices - Device settings`  


    
Dla następujących zasad należy wybrać ustawienie **Wszystkie**: **Użytkownicy mogą rejestrować swoje urządzenia w usłudze Azure AD**

![Rejestrowanie urządzeń](media/hybrid-azuread-join-managed-domains/23.png)



### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Konfigurowanie ustawień lokalnego intranetu na potrzeby rejestracji urządzeń

Aby pomyślnie przeprowadzić dołączenie hybrydowe do usługi Azure AD dla urządzeń z systemem Windows niższego poziomu oraz uniknąć monitów o certyfikat podczas uwierzytelniania urządzeń w usłudze Azure AD, możesz wypchnąć zasady do urządzeń dołączonych do domeny, aby dodać następujące adresy URL do lokalnej strefy intranetu w programie Internet Explorer:

- `https://device.login.microsoftonline.com`

- `https://autologon.microsoftazuread-sso.com`.

Ponadto musisz włączyć opcję **Zezwalaj na aktualizacje na pasku stanu za pomocą skryptu** w lokalnej strefie intranetu użytkownika.


### <a name="configure-seamless-sso"></a>Konfigurowanie bezproblemowego logowania jednokrotnego

Aby pomyślnie przeprowadzić dołączenie hybrydowe do usługi Azure AD urządzeń niższego poziomu z systemem Windows w domenie zarządzanej, w której jako metoda uwierzytelniania w chmurze używane jest uwierzytelnianie przekazywane lub synchronizacja skrótów haseł, należy również [skonfigurować bezproblemowe logowanie jednokrotne](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso-quick-start#step-2-enable-the-feature). Jeśli masz problem ze skonfigurowaniem bezproblemowe logowanie Jednokrotne, zobacz artykuł [Rozwiązywanie problemów z usługi Azure Active Directory bezproblemowe logowanie jednokrotne](https://docs.microsoft.com/en-us/azure/active-directory/hybrid/tshoot-connect-sso).


### <a name="control-windows-down-level-devices"></a>Kontrolowanie urządzeń z systemem Windows niższego poziomu 

Aby zarejestrować urządzenia z systemem Windows niższego poziomu, musisz pobrać i zainstalować pakiet Instalatora Windows (msi) z Centrum pobierania. Aby uzyskać więcej informacji, zobacz artykuł [kontrolować dołączenie do hybrydowej usługi Azure AD urządzeń](hybrid-azuread-join-control.md#control-windows-down-level-devices). 


## <a name="verify-the-registration"></a>Weryfikacja rejestracji

Aby zweryfikować stan rejestracji urządzenia w dzierżawie platformy Azure, możesz użyć polecenia cmdlet **[Get-MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice)** w **[module Azure Active Directory programu PowerShell](/powershell/azure/install-msonlinev1?view=azureadps-2.0)**.

W przypadku użycia polecenia cmdlet **Get-MSolDevice** w celu sprawdzenia szczegółów usługi:

- Musi istnieć obiekt z **identyfikatorem urządzenia**, który pasuje do identyfikatora w kliencie systemu Windows.
- Wartością atrybutu **DeviceTrustType** musi być **Dołączone do domeny**. Jest to równoważne ze stanem **Dołączone hybrydowo do usługi Azure AD** na stronie Urządzenia w portalu usługi Azure AD.
- Ustawienie **Włączone** musi mieć wartość **True**, a ustawienie **DeviceTrustLevel** musi mieć wartość **Zarządzane** dla urządzeń używanych w dostępie warunkowym. 


**Aby sprawdzić szczegóły usługi:**

1. Otwórz program **Windows PowerShell** jako administrator.

2. Wpisz polecenie `Connect-MsolService`, aby nawiązać połączenie z dzierżawą platformy Azure.  

3. Wpisz polecenie `get-msoldevice -deviceId <deviceId>`.

6. Upewnij się, że opcja **Włączone** ma wartość **Prawda**.





## <a name="troubleshoot-your-implementation"></a>Rozwiązywanie problemów z implementacją

Jeśli występują problemy z ukończeniem dołączania hybrydowego do usługi Azure AD dla urządzeń z systemem Windows dołączonych do domeny, zobacz:

- [Rozwiązywanie problemów z dołączeniem hybrydowym do usługi Azure AD urządzeń z bieżącym systemem Windows](troubleshoot-hybrid-join-windows-current.md)
- [Rozwiązywanie problemów z dołączeniem hybrydowym do usługi Azure AD urządzeń z systemem Windows niższego poziomu](troubleshoot-hybrid-join-windows-legacy.md)


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Konfigurowanie dołączania hybrydowego do usługi Azure Active Directory dla domen federacyjnych](hybrid-azuread-join-federated-domains.md)
> [Ręczne konfigurowanie hybrydowego dołączania do usługi Azure Active Directory](hybrid-azuread-join-manual.md)

