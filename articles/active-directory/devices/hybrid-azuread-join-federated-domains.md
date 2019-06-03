---
title: Konfigurowanie hybrydowego dołączania do usługi Azure Active Directory dla domen federacyjnych | Microsoft Docs
description: Dowiedz się, jak konfigurować dołączanie hybrydowe do usługi Azure Active Directory dla domen federacyjnych.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a7896a31d3957f858de356a9cffdcb45a1fb649
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/31/2019
ms.locfileid: "66426648"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-federated-domains"></a>Samouczek: Konfigurowanie hybrydowego dołączania do usługi Azure Active Directory dla domen federacyjnych

Podobnie jak w przypadku użytkownika, urządzenie staje się kolejną tożsamością, którą należy chronić oraz używać do zabezpieczania zasobów w dowolnym czasie i miejscu. W tym celu można przenieść tożsamości urządzeń do usługi Azure AD przy użyciu jednej z następujących metod:

- Dołączenie do usługi Azure AD
- Dołączenie hybrydowe do usługi Azure AD
- Rejestracja w usłudze Azure AD

Przenosząc urządzenia do usługi Azure AD, można zmaksymalizować wydajność użytkowników dzięki zastosowaniu logowania jednokrotnego (SSO) w zasobach chmury i zasobach lokalnych. Jednocześnie można zapewnić bezpieczny dostęp do zasobów chmury i zasobów lokalnych przy użyciu [dostępu warunkowego](../active-directory-conditional-access-azure-portal.md).

Z tego samouczka dowiesz się, jak za pomocą usług ADFS skonfigurować dołączenie hybrydowe do usługi Azure AD dla urządzeń będących w federacji.

> [!div class="checklist"]
> * Konfigurowanie dołączenia hybrydowego do usługi Azure AD
> * Włączanie urządzeń z systemem Windows niższego poziomu
> * Weryfikacja rejestracji
> * Rozwiązywanie problemów

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku założono, że znasz następujące informacje:

- [Wprowadzenie do zarządzania urządzeniami w usłudze Azure Active Directory](../device-management-introduction.md)
- [Jak planować implementację z hybrydowym dołączaniem do usługi Azure Active Directory](hybrid-azuread-join-plan.md)
- [Jak kontrolować hybrydowe dołączanie Twoich urządzeń do usługi Azure AD](hybrid-azuread-join-control.md)

Aby skonfigurować scenariusz zaprezentowany w tym samouczku, potrzebujesz:

- Windows Server 2012 R2 z usługami AD FS
- [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) w wersji 1.1.819.0 lub nowszej.

Począwszy od wersji 1.1.819.0, program Azure AD Connect zapewnia kreator umożliwiający konfigurowanie dołączania hybrydowego do usługi Azure AD. Kreator pozwala znacznie uprościć proces konfiguracji. Powiązany kreator:

- Konfiguruje punkty połączenia usługi (SCP) na potrzeby rejestracji urządzenia.
- Tworzy kopię zapasową istniejącej relacji zaufania dla jednostki zależnej usługi Azure AD.
- Aktualizuje reguły dotyczące oświadczeń w relacji zaufania usługi Azure AD.

Kroki konfiguracji w tym artykule zostały oparte na tym kreatorze. W przypadku starszej wersji programu Azure AD Connect należy uaktualnić go do wersji 1.1.819 lub nowszej. Jeśli zainstalowanie najnowszej wersji programu Azure AD Connect nie jest możliwe, zobacz [jak ręcznie skonfigurować rejestrację urządzeń](../device-management-hybrid-azuread-joined-devices-setup.md).

Dołączenie hybrydowe do usługi Azure AD wymaga urządzeń z dostępem do następujących zasobów Microsoft z wewnątrz sieci organizacji:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- Usługa STS (domen federacyjnych) w organizacji
- `https://autologon.microsoftazuread-sso.com` (jeśli używasz lub planujesz użycie bezproblemowego logowania jednokrotnego)

Od wersji systemu Windows 10 1803, jeśli natychmiastowe dołączenie hybrydowe do usługi Azure AD w przypadku domeny federacyjnej, na przykład AD FS, zakończy się niepowodzeniem, usługa Azure AD Connect jest używana do synchronizacji obiektu komputera w usłudze Azure AD, który jest następnie używany do ukończenia rejestracji urządzenia na potrzeby hybrydowego dołączenia do usługi Azure AD.

Jeśli organizacja wymaga dostępu do Internetu za pośrednictwem serwera proxy ruchu wychodzącego, począwszy od systemu Windows 10 w wersji 1709 możesz [skonfigurować ustawienia serwera proxy na komputerze przy użyciu obiektu zasad grupy (GPO)](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/). Jeśli na komputerze masz system Windows 10 w wersji starszej niż 1709, musisz wdrożyć usługę autowykrywania internetowego serwera proxy (WPAD), aby umożliwić komputerom z systemem Windows 10 przeprowadzenie rejestracji urządzenia w usłudze Azure AD.

Jeśli organizacja wymaga dostępu do Internetu za pośrednictwem uwierzytelnionego serwera proxy ruchu wychodzącego, musisz upewnić się, że komputery z systemem Windows 10 mogą pomyślnie uwierzytelnić się na serwerze proxy ruchu wychodzącego. Ponieważ komputery z systemem Windows 10 uruchamiają rejestrację urządzenia przy użyciu kontekstu maszyny, konfigurowanie uwierzytelniania serwera proxy ruchu wychodzącego należy wykonać właśnie przy użyciu kontekstu maszyny. Skontaktuj się z dostawcą serwera proxy ruchu wychodzącego, aby uzyskać informacje na temat wymagań dotyczących konfiguracji.

## <a name="configure-hybrid-azure-ad-join"></a>Konfigurowanie dołączenia hybrydowego do usługi Azure AD

Aby skonfigurować dołączanie hybrydowe do usługi Azure AD przy użyciu programu Azure AD Connect, potrzebujesz następujących elementów:

- Poświadczenia administratora globalnego dzierżawy usługi Azure AD.  
- Poświadczenia administratora przedsiębiorstwa dla każdego lasu.
- Poświadczenia administratora usług AD FS.

**Aby skonfigurować dołączanie hybrydowe do usługi Azure AD przy użyciu programu Azure AD Connect:**

1. Uruchom program Azure AD Connect, a następnie kliknij pozycję **Konfiguruj**.

   ![Powitanie](./media/hybrid-azuread-join-federated-domains/11.png)

1. Na stronie **Dodatkowe zadania** wybierz pozycję **Skonfiguruj opcje urządzenia**, a następnie kliknij pozycję **Dalej**.

   ![Dodatkowe zadania](./media/hybrid-azuread-join-federated-domains/12.png)

1. Na stronie **Przegląd** kliknij pozycję **Dalej**.

   ![Przegląd](./media/hybrid-azuread-join-federated-domains/13.png)

1. Na stronie **Łączenie z usługą Azure AD** wprowadź poświadczenia administratora globalnego dzierżawy usługi Azure AD, a następnie kliknij pozycję **Dalej**.

   ![Łączenie z usługą Azure AD](./media/hybrid-azuread-join-federated-domains/14.png)

1. Na stronie **Opcje urządzenia** wybierz pozycję **Skonfiguruj dołączenie hybrydowe do usługi Azure AD**, a następnie kliknij pozycję **Dalej**.

   ![Opcje urządzenia](./media/hybrid-azuread-join-federated-domains/15.png)

1. Na stronie **Punkt połączenia z usługą** wykonaj następujące czynności, a następnie kliknij polecenie **Dalej**:

   ![Punkt połączenia z usługą](./media/hybrid-azuread-join-federated-domains/16.png)

   1. Wybierz las.
   1. Wybierz usługę uwierzytelniania. Nie musisz wybierać serwera usług AD FS w sytuacji, gdy organizacja ma klientów systemu Windows 10 na wyłączność i skonfigurowano synchronizację komputera/urządzenia lub organizacja używa bezproblemowego logowania jednokrotnego.
   1. Kliknij pozycję **Dodaj**, aby wprowadzić poświadczenia administratora przedsiębiorstwa.

1. Na stronie **Systemy operacyjne urządzeń** wybierz systemy operacyjne używane przez urządzenia w środowisku usługi Active Directory, a następnie kliknij pozycję **Dalej**.

   ![System operacyjny urządzenia](./media/hybrid-azuread-join-federated-domains/17.png)

1. Na stronie **Konfiguracja federacji** wprowadź poświadczenia administratora usług AD FS, a następnie kliknij pozycję **Dalej**.

   ![Konfiguracja federacji](./media/hybrid-azuread-join-federated-domains/18.png)

1. Na stronie **Wszystko gotowe do skonfigurowania** kliknij pozycję **Konfiguruj**.

   ![Wszystko gotowe do skonfigurowania](./media/hybrid-azuread-join-federated-domains/19.png)

1. Na stronie **Ukończono konfigurację** kliknij przycisk **Wyjdź**.

   ![Ukończono konfigurację](./media/hybrid-azuread-join-federated-domains/20.png)

## <a name="enable-windows-down-level-devices"></a>Włączanie urządzeń z systemem Windows niższego poziomu

Jeśli część urządzeń dołączonych do domeny to urządzenia z systemem Windows niższego poziomu, musisz spełnić następujące warunki:

- Aktualizacja ustawień urządzenia
- Konfigurowanie ustawień lokalnego intranetu na potrzeby rejestracji urządzeń
- Kontrolowanie urządzeń z systemem Windows niższego poziomu

### <a name="update-device-settings"></a>Aktualizacja ustawień urządzenia

Aby zarejestrować urządzenia z systemem Windows niższego poziomu, należy upewnić się, że skonfigurowano ustawienia urządzeń umożliwiające użytkownikom rejestrowanie urządzeń w usłudze Azure AD. W witrynie Azure Portal możesz znaleźć te ustawienia w obszarze:

`Home > [Name of your tenant] > Devices - Device settings`

Dla następujących zasad należy wybrać ustawienie **Wszystkie**: **Użytkownicy mogą rejestrować swoje urządzenia w usłudze Azure AD**

![Rejestrowanie urządzeń](./media/hybrid-azuread-join-federated-domains/23.png)

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Konfigurowanie ustawień lokalnego intranetu na potrzeby rejestracji urządzeń

Aby pomyślnie przeprowadzić dołączenie hybrydowe do usługi Azure AD dla urządzeń z systemem Windows niższego poziomu oraz uniknąć monitów o certyfikat podczas uwierzytelniania urządzeń w usłudze Azure AD, możesz wypchnąć zasady do urządzeń dołączonych do domeny, aby dodać następujące adresy URL do lokalnej strefy intranetu w programie Internet Explorer:

- `https://device.login.microsoftonline.com`
- Usługa tokenu zabezpieczającego (STS — domen federacyjnych) w organizacji
- `https://autologon.microsoftazuread-sso.com` (na potrzeby bezproblemowego logowania jednokrotnego).

Ponadto musisz włączyć opcję **Zezwalaj na aktualizacje na pasku stanu za pomocą skryptu** w lokalnej strefie intranetu użytkownika.

### <a name="control-windows-down-level-devices"></a>Kontrolowanie urządzeń z systemem Windows niższego poziomu

Aby zarejestrować urządzenia z systemem Windows niższego poziomu, musisz pobrać i zainstalować pakiet Instalatora Windows (msi) z Centrum pobierania. Aby uzyskać więcej informacji, zobacz sekcję [kontrolowana Walidacja dołączenie do hybrydowej usługi Azure AD na urządzeniach z Windows niższego poziomu](hybrid-azuread-join-control.md#controlled-validation-of-hybrid-azure-ad-join-on-windows-down-level-devices).

## <a name="verify-the-registration"></a>Weryfikacja rejestracji

Aby zweryfikować stan rejestracji urządzenia w dzierżawie platformy Azure, możesz użyć polecenia cmdlet **[Get-MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice)** w **[module Azure Active Directory programu PowerShell](/powershell/azure/install-msonlinev1?view=azureadps-2.0)** .

W przypadku użycia polecenia cmdlet **Get-MSolDevice** w celu sprawdzenia szczegółów usługi:

- Musi istnieć obiekt z **identyfikatorem urządzenia**, który pasuje do identyfikatora w kliencie systemu Windows.
- Wartością atrybutu **DeviceTrustType** musi być **Dołączone do domeny**. Jest to równoważne ze stanem **Dołączone hybrydowo do usługi Azure AD** na stronie Urządzenia w portalu usługi Azure AD.
- Ustawienie **Włączone** musi mieć wartość **True**, a ustawienie **DeviceTrustLevel** musi mieć wartość **Zarządzane** dla urządzeń używanych w dostępie warunkowym.

**Aby sprawdzić szczegóły usługi:**

1. Otwórz program **Windows PowerShell** jako administrator.
1. Wpisz polecenie `Connect-MsolService`, aby nawiązać połączenie z dzierżawą platformy Azure.  
1. Wpisz polecenie `get-msoldevice -deviceId <deviceId>`.
1. Upewnij się, że opcja **Włączone** ma wartość **Prawda**.

## <a name="troubleshoot-your-implementation"></a>Rozwiązywanie problemów z implementacją

Jeśli występują problemy z ukończeniem dołączania hybrydowego do usługi Azure AD dla urządzeń z systemem Windows dołączonych do domeny, zobacz:

- [Rozwiązywanie problemów z dołączeniem hybrydowym do usługi Azure AD urządzeń z bieżącym systemem Windows](troubleshoot-hybrid-join-windows-current.md)
- [Rozwiązywanie problemów z dołączeniem hybrydowym do usługi Azure AD urządzeń z systemem Windows niższego poziomu](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Konfigurowanie dołączenia hybrydowego do usługi Azure Active Directory dla domen zarządzanych](hybrid-azuread-join-managed-domains.md)
> [Ręczne konfigurowanie dołączenia hybrydowego do usługi Azure Active Directory](hybrid-azuread-join-manual.md)

<!--Image references-->
[1]: ./media/active-directory-conditional-access-automatic-device-registration-setup/12.png
