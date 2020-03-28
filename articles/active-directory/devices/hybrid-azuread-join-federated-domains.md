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
ms.openlocfilehash: 1a61c89199c89f09b5cc0e553dbbf48655ad1b6a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239098"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-federated-domains"></a>Samouczek: konfigurowanie dołączania hybrydowego do usługi Azure Active Directory dla domen federacyjnych

Podobnie jak użytkownik w organizacji, urządzenie jest podstawową tożsamością, którą chcesz chronić. Możesz użyć tożsamości urządzenia, aby chronić swoje zasoby w dowolnym czasie i z dowolnego miejsca. Ten cel można osiągnąć, wprowadzając tożsamości urządzeń i zarządzając nimi w usłudze Azure Active Directory (Azure AD) przy użyciu jednej z następujących metod:

- Dołączenie do usługi Azure AD
- Dołączenie hybrydowe do usługi Azure AD
- Rejestracja w usłudze Azure AD

Przeniesienie urządzeń do usługi Azure AD maksymalizuje produktywność użytkowników za pomocą logowania jednokrotnego (Logowanie jednokrotne) w chmurze i w zasobach lokalnych. Jednocześnie można zabezpieczyć dostęp do zasobów w chmurze i lokalnie za pomocą [dostępu warunkowego.](../active-directory-conditional-access-azure-portal.md)

Środowisko federacyjne powinno mieć dostawcę tożsamości, który obsługuje następujące wymagania. Jeśli masz środowisko federacyjne korzystające z usług federacyjnych Active Directory (AD FS), poniższe wymagania są już obsługiwane.

- **WIAORMULTIAUTHN twierdzenie:** To oświadczenie jest wymagane do hybrydowego sprzężenia usługi Azure AD dla urządzeń w dół systemu Windows.
- **Protokół WS-Trust:** Ten protokół jest wymagany do uwierzytelniania obecnych hybrydowych urządzeń platformy Azure AD z usługą Azure AD.
  Podczas korzystania z usług AD FS należy włączyć następujące punkty końcowe WS-Trust:`/adfs/services/trust/2005/windowstransport`
   `/adfs/services/trust/13/windowstransport`
   `/adfs/services/trust/2005/usernamemixed`
   `/adfs/services/trust/13/usernamemixed`
   `/adfs/services/trust/2005/certificatemixed`
   `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> Zarówno **adfs/services/trust/2005/windowstransport** i **adfs/services/trust/13/windowstransport** powinny być włączone tylko jako punkty końcowe skierowane do intranetu i NIE mogą być udostępniane jako punkty końcowe skierowane do ekstranetu za pośrednictwem serwera proxy aplikacji sieci Web. Aby dowiedzieć się więcej na temat wyłączania punktów końcowych systemu Windows w systemie WS-Trust, zobacz [Wyłączanie punktów końcowych systemu Windows w systemie WS-Trust w serwerze proxy](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet). Punkty końcowe są włączone za pośrednictwem konsoli zarządzania usługami AD FS w obszarze**Punkty końcowe** **usługi** > .

W tym samouczku dowiesz się, jak skonfigurować hybrydowe przyłączenie usługi Azure AD dla urządzeń komputerów przyłączonych do domeny usługi Active Directory w środowisku federatywnym przy użyciu usług AD FS.

Omawiane kwestie:

> [!div class="checklist"]
> * Konfigurowanie dołączenia hybrydowego do usługi Azure AD
> * Włączanie urządzeń niższego poziomu systemu Windows
> * Weryfikacja rejestracji
> * Rozwiązywanie problemów

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku założono, że znasz te artykuły:

- [Co to jest tożsamość urządzenia?](overview.md)
- [Jak zaplanować implementację dołączania do usługi Azure AD](hybrid-azuread-join-plan.md)
- [Jak wykonać kontrolowane sprawdzanie poprawności hybrydowego sprzężenia usługi Azure AD](hybrid-azuread-join-control.md)

Aby skonfigurować scenariusz zaprezentowany w tym samouczku, potrzebujesz:

- Windows Server 2012 R2 z usługami AD FS
- [Usługa Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) w wersji 1.1.819.0 lub nowszej

Począwszy od wersji 1.1.819.0, usługa Azure AD Connect zawiera kreatora, którego można użyć do skonfigurowania hybrydowego sprzężenia usługi Azure AD. Kreator znacznie upraszcza proces konfiguracji. Powiązany kreator:

- Konfiguruje punkty połączenia usługi (SCP) do rejestracji urządzenia
- Tworzy kopię zapasową istniejącej relacji zaufania dla jednostki zależnej usługi Azure AD.
- Aktualizuje reguły dotyczące oświadczeń w relacji zaufania usługi Azure AD.

Kroki konfiguracji opisane w tym artykule są oparte na użyciu kreatora usługi Azure AD Connect. Jeśli masz zainstalowaną wcześniejszą wersję usługi Azure AD Connect, należy uaktualnić ją do wersji 1.1.819 lub nowszej, aby korzystać z kreatora. Jeśli zainstalowanie najnowszej wersji usługi Azure AD Connect nie jest opcją, zobacz, [jak ręcznie skonfigurować hybrydowe sprzężenie usługi Azure AD](hybrid-azuread-join-manual.md).

Hybrydowe sprzężenie usługi AD wymaga, aby urządzenia miały dostęp do następujących zasobów firmy Microsoft z sieci organizacji:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- Usługa tokenu zabezpieczającego (STS) organizacji (dla domen federacyjnej)
- `https://autologon.microsoftazuread-sso.com`(Jeśli używasz lub planujesz używać bezszwowego logowania jednokrotnego)

Począwszy od systemu Windows 10 1803, jeśli chwilowe hybrydowe sprzężenie usługi Azure AD dla środowiska federacyjnego przy użyciu usług AD FS zakończy się niepowodzeniem, polegamy na usłudze Azure AD Connect w celu zsynchronizowania obiektu komputera w usłudze Azure AD, który jest następnie używany do ukończenia rejestracji urządzenia dla hybrydowej platformy Azure Sprzężenie usługi AD. Sprawdź, czy usługa Azure AD Connect zsynchronizowała obiekty komputerów urządzeń, które mają być hybrydowe usługi Azure AD przyłączone do usługi Azure AD. Jeśli obiekty komputera należą do określonych jednostek organizacyjnych, należy również skonfigurować jednostki organizacyjne do synchronizacji w usłudze Azure AD Connect. Aby dowiedzieć się więcej o synchronizowaniu obiektów komputera przy użyciu usługi Azure AD Connect, zobacz [Konfigurowanie filtrowania przy użyciu usługi Azure AD Connect](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering).

Jeśli organizacja wymaga dostępu do Internetu za pośrednictwem wychodzącego serwera proxy, firma Microsoft zaleca [wdrożenie automatycznego odnajdowania serwera proxy sieci Web (WPAD),](/previous-versions/tn-archive/cc995261(v%3dtechnet.10)) aby włączyć komputery z systemem Windows 10 do rejestracji urządzeń w usłudze Azure AD. Jeśli wystąpią problemy z konfiguracją wpad i zarządzaniem nim, zobacz [Rozwiązywanie problemów z automatycznym wykrywaniem](/previous-versions/tn-archive/cc302643(v=technet.10)). 

Jeśli nie używasz WPAD i chcesz skonfigurować ustawienia serwera proxy na komputerze, możesz to zrobić począwszy od systemu Windows 10 1709. Aby uzyskać więcej informacji, zobacz [Konfigurowanie ustawień winhttp przy użyciu obiektu zasad grupy ..](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/)

> [!NOTE]
> Jeśli ustawienia serwera proxy na komputerze zostaną skonfigurowane przy użyciu ustawień winhttp, wszystkie komputery, które nie mogą połączyć się ze skonfigurowanym serwerem proxy, nie powiodą się z Internetem.

Jeśli organizacja wymaga dostępu do Internetu za pośrednictwem uwierzytelnionego wychodzącego serwera proxy, należy upewnić się, że komputery z systemem Windows 10 mogą pomyślnie uwierzytelnić się na wychodzącym serwerze proxy. Ponieważ komputery z systemem Windows 10 uruchamiają rejestrację urządzeń przy użyciu kontekstu komputera, należy skonfigurować uwierzytelnianie wychodzącego serwera proxy przy użyciu kontekstu komputera. Skontaktuj się z dostawcą serwera proxy ruchu wychodzącego, aby uzyskać informacje na temat wymagań dotyczących konfiguracji.

Aby sprawdzić, czy urządzenie jest w stanie uzyskać dostęp do powyższych zasobów firmy Microsoft w ramach konta systemowego, można użyć skryptu [łączności rejestracji urządzeń testowych.](https://gallery.technet.microsoft.com/Test-Device-Registration-3dc944c0)

## <a name="configure-hybrid-azure-ad-join"></a>Konfigurowanie dołączenia hybrydowego do usługi Azure AD

Aby skonfigurować hybrydowe sprzężenie usługi Azure AD przy użyciu usługi Azure AD Connect, potrzebujesz:

- Poświadczenia administratora globalnego dla dzierżawy usługi Azure AD  
- Poświadczenia administratora przedsiębiorstwa dla każdego z lasów
- Poświadczenia administratora usług AD FS

**Aby skonfigurować hybrydowe sprzężenie usługi Azure AD przy użyciu usługi Azure AD Connect:**

1. Uruchom usługę Azure AD Connect, a następnie wybierz pozycję **Konfiguruj**.

   ![Powitanie](./media/hybrid-azuread-join-federated-domains/11.png)

1. Na stronie **Zadania dodatkowe** wybierz pozycję **Konfiguruj opcje urządzenia**, a następnie wybierz pozycję **Dalej**.

   ![Dodatkowe zadania](./media/hybrid-azuread-join-federated-domains/12.png)

1. Na stronie **Przegląd** wybierz pozycję **Dalej**.

   ![Omówienie](./media/hybrid-azuread-join-federated-domains/13.png)

1. Na stronie **Połącz z usługą Azure AD** wprowadź poświadczenia administratora globalnego dla dzierżawy usługi Azure AD, a następnie wybierz pozycję **Dalej**.

   ![Łączenie z usługą Azure AD](./media/hybrid-azuread-join-federated-domains/14.png)

1. Na stronie **Opcje urządzenia** wybierz pozycję **Konfiguruj hybrydowe sprzężenie usługi Azure AD,** a następnie wybierz pozycję **Dalej**.

   ![Opcje urządzenia](./media/hybrid-azuread-join-federated-domains/15.png)

1. Na stronie **SCP** wykonaj następujące czynności, a następnie wybierz pozycję **Dalej:**

   ![SCP](./media/hybrid-azuread-join-federated-domains/16.png)

   1. Wybierz las.
   1. Wybierz usługę uwierzytelniania. Należy wybrać **serwer usług AD FS,** chyba że organizacja ma wyłącznie klientów systemu Windows 10 i skonfigurowano synchronizację komputera/urządzenia lub organizacja używa bezproblemowego logowania jednokrotnego.
   1. Wybierz **pozycję Dodaj,** aby wprowadzić poświadczenia administratora przedsiębiorstwa.

1. Na stronie **Systemy operacyjne Urządzenia** wybierz systemy operacyjne używane przez urządzenia w środowisku usługi Active Directory, a następnie wybierz pozycję **Dalej**.

   ![System operacyjny urządzenia](./media/hybrid-azuread-join-federated-domains/17.png)

1. Na stronie **Konfiguracja federacji** wprowadź poświadczenia administratora usług AD FS, a następnie wybierz pozycję **Dalej**.

   ![Konfiguracja federacji](./media/hybrid-azuread-join-federated-domains/18.png)

1. Na stronie **Gotowe do skonfigurowania** wybierz pozycję **Konfiguruj**.

   ![Wszystko gotowe do skonfigurowania](./media/hybrid-azuread-join-federated-domains/19.png)

1. Na stronie **Konfiguracja zakończona** wybierz pozycję **Zakończ**.

   ![Ukończono konfigurację](./media/hybrid-azuread-join-federated-domains/20.png)

## <a name="enable-windows-downlevel-devices"></a>Włączanie urządzeń niższego poziomu systemu Windows

Jeśli niektóre urządzenia przyłączone do domeny są urządzeniami niższego poziomu systemu Windows, należy:

- Konfigurowanie ustawień lokalnego intranetu na potrzeby rejestracji urządzeń
- Instalowanie programu Microsoft Workplace Join dla komputerów niższego poziomu systemu Windows

> [!NOTE]
> Obsługa systemu Windows 7 zakończyła się 14 stycznia 2020 roku. Aby uzyskać więcej informacji, [pomoc techniczna dla systemu Windows 7 została zakończona](https://support.microsoft.com/en-us/help/4057281/windows-7-support-ended-on-january-14-2020).

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Konfigurowanie ustawień lokalnego intranetu na potrzeby rejestracji urządzeń

Aby pomyślnie ukończyć hybrydowe sprzężenie usługi Azure AD urządzeń niższego poziomu systemu Windows i uniknąć monitów o certyfikaty podczas uwierzytelniania urządzeń w usłudze Azure AD, można wypchnąć zasadę do urządzeń przyłączonych do domeny, aby dodać następujące adresy URL do lokalnej strefy intranetu w Internecie Explorer:

- `https://device.login.microsoftonline.com`
- STS organizacji (dla domen federacyjnej)
- `https://autologon.microsoftazuread-sso.com`(Dla bezproblemowego logowania jednokrotnego)

Należy również włączyć **zezwalaj na aktualizacje paska stanu za pomocą skryptu** w lokalnej strefie intranetu użytkownika.

### <a name="install-microsoft-workplace-join-for-windows-downlevel-computers"></a>Instalowanie programu Microsoft Workplace Join dla komputerów niższego poziomu systemu Windows

Aby zarejestrować urządzenia niższego poziomu systemu Windows, organizacje muszą zainstalować [usługę Microsoft Workplace Join dla komputerów z systemem innych niż Windows 10](https://www.microsoft.com/download/details.aspx?id=53554). Aplikacja Microsoft Workplace Join dla komputerów z systemem windows 10 jest dostępna w Centrum pobierania Microsoft.

Pakiet można wdrożyć za pomocą systemu dystrybucji oprogramowania, takiego jak [program Microsoft Endpoint Configuration Manager](/configmgr/). Pakiet obsługuje standardowe opcje instalacji `quiet` dyskretnej z parametrem. Bieżąca gałąź programu Configuration Manager oferuje korzyści w stosunku do wcześniejszych wersji, takich jak możliwość śledzenia zakończonych rejestracji.

Instalator tworzy zaplanowane zadanie w systemie, który działa w kontekście użytkownika. Zadanie jest wyzwalane, gdy użytkownik loguje się do systemu Windows. Zadanie dyskretnie łączy urządzenie z usługą Azure AD przy użyciu poświadczeń użytkownika po uwierzytelnii się za pomocą usługi Azure AD.

## <a name="verify-the-registration"></a>Weryfikacja rejestracji

Aby zweryfikować stan rejestracji urządzenia w dzierżawie platformy Azure, możesz użyć polecenia cmdlet **[Get-MsolDevice](/powershell/msonline/v1/get-msoldevice)** w [module Azure Active Directory programu PowerShell](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

Podczas korzystania z polecenia cmdlet **Get-MSolDevice,** aby sprawdzić szczegóły usługi:

- Obiekt o **identyfikatorze urządzenia,** który pasuje do identyfikatora na kliencie systemu Windows musi istnieć.
- Wartością atrybutu **DeviceTrustType** musi być **Dołączone do domeny**. To ustawienie jest równoważne **hybrydowej usługi Azure AD przyłączony** stan w obszarze **Urządzenia** w portalu usługi Azure AD.
- W przypadku urządzeń używanych w układzie dostępu warunkowego wartość **włączonej** musi być **true,** a **DeviceTrustLevel** **.**

**Aby sprawdzić szczegóły usługi:**

1. Uruchom program Windows PowerShell jako administrator.
1. Wprowadź, `Connect-MsolService` aby połączyć się z dzierżawą platformy Azure.  
1. Wprowadź polecenie `get-msoldevice -deviceId <deviceId>`.
1. Upewnij się, że opcja **Włączone** ma wartość **Prawda**.

## <a name="troubleshoot-your-implementation"></a>Rozwiązywanie problemów z implementacją

Jeśli występują problemy z ukończeniem hybrydowego sprzężenia usługi Azure AD dla urządzeń z systemem Windows przyłączonych do domeny, zobacz:

- [Rozwiązywanie problemów z hybrydowym sprzężeniem usługi Azure AD dla bieżących urządzeń z systemem Windows](troubleshoot-hybrid-join-windows-current.md)
- [Rozwiązywanie problemów z hybrydowym sprzężeniem usługi Azure AD dla urządzeń niższego poziomu systemu Windows](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [zarządzać tożsamościami urządzeń przy użyciu witryny Azure portal](device-management-azure-portal.md).

<!--Image references-->
[1]: ./media/active-directory-conditional-access-automatic-device-registration-setup/12.png
