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
ms.openlocfilehash: 600d6b9f1eb8d8073e1658dd5b8196a3d8137e42
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2019
ms.locfileid: "66733720"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-federated-domains"></a>Samouczek: Konfigurowanie hybrydowego dołączania do usługi Azure Active Directory dla domen federacyjnych

W podobny sposób jak użytkownik urządzenie jest innej tożsamości core, który chcesz chronić i użyć jej do ochrony zasobów, w dowolnym momencie i z dowolnego miejsca. Ten cel można osiągnąć przez wprowadzenie i Zarządzanie tożsamościami urządzeń w usłudze Azure AD przy użyciu jednej z następujących metod:

- Dołączenie do usługi Azure AD
- Dołączenie hybrydowe do usługi Azure AD
- Rejestracja w usłudze Azure AD

Przenosząc urządzenia do usługi Azure AD, można zmaksymalizować wydajność użytkowników dzięki zastosowaniu logowania jednokrotnego (SSO) w zasobach chmury i zasobach lokalnych. Jednocześnie można zapewnić bezpieczny dostęp do zasobów chmury i zasobów lokalnych przy użyciu [dostępu warunkowego](../active-directory-conditional-access-azure-portal.md).

W tym samouczku dowiesz się, jak skonfigurować dołączenie do hybrydowej usługi Azure AD dla urządzeń z komputerów przyłączonych do domeny usługi AD w środowisku federacyjnym za pomocą usług AD FS.

> [!NOTE]
> Jeśli środowisko federacyjnego używa dostawcy tożsamości innych niż usługi AD FS, należy upewnić się, że dostawca tożsamości obsługuje protokół WS-Trust. WS-Trust jest potrzebne do uwierzytelnienia użytkownika Windows urządzenia z usługą Azure AD przyłączone do bieżącego hybrydowej usługi Azure AD. Ponadto w przypadku Windows niższego poziomu urządzeń wymagających dołączenie do hybrydowej usługi Azure AD dostawcy tożsamości należy obsługiwać WIAORMULTIAUTHN oświadczenia. 


> [!div class="checklist"]
> * Konfigurowanie dołączenia hybrydowego do usługi Azure AD
> * Włączanie urządzeń z systemem Windows niższego poziomu
> * Weryfikacja rejestracji
> * Rozwiązywanie problemów

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku założono, że znasz następujące informacje:

- [Wprowadzenie do zarządzania tożsamościami urządzeń w usłudze Azure Active Directory](../device-management-introduction.md)
- [Jak planować implementację z hybrydowym dołączaniem do usługi Azure Active Directory](hybrid-azuread-join-plan.md)
- [Jak przeprowadzić kontrolowana Walidacja dołączenie do hybrydowej usługi Azure AD](hybrid-azuread-join-control.md)

Aby skonfigurować scenariusz zaprezentowany w tym samouczku, potrzebujesz:

- Windows Server 2012 R2 z usługami AD FS
- [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) w wersji 1.1.819.0 lub nowszej.

Począwszy od wersji 1.1.819.0, program Azure AD Connect zapewnia kreator umożliwiający konfigurowanie dołączania hybrydowego do usługi Azure AD. Kreator pozwala znacznie uprościć proces konfiguracji. Powiązany kreator:

- Konfiguruje punkty połączenia usługi (SCP) na potrzeby rejestracji urządzenia.
- Tworzy kopię zapasową istniejącej relacji zaufania dla jednostki zależnej usługi Azure AD.
- Aktualizuje reguły dotyczące oświadczeń w relacji zaufania usługi Azure AD.

Kroki konfiguracji w tym artykule zostały oparte na tym kreatorze. W przypadku starszej wersji programu Azure AD Connect należy uaktualnić go do wersji 1.1.819 lub nowszej. Jeśli zainstalowanie najnowszej wersji programu Azure AD Connect nie jest dostępną opcją w, zobacz [jak ręcznie skonfigurować dołączenie do hybrydowej usługi Azure AD](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-manual).

Dołączenie hybrydowe do usługi Azure AD wymaga urządzeń z dostępem do następujących zasobów Microsoft z wewnątrz sieci organizacji:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- Usługa STS (domen federacyjnych) w organizacji
- `https://autologon.microsoftazuread-sso.com` (jeśli używasz lub planujesz użycie bezproblemowego logowania jednokrotnego)

Począwszy od z systemem Windows 10 1803, jeśli natychmiastowe dołączenie do hybrydowej usługi Azure AD, w środowisku federacyjnym za pomocą usług AD FS nie powiedzie się, możemy polegać na program Azure AD Connect do synchronizowania obiektu komputera w usłudze Azure AD, które są następnie używane do ukończenia rejestracji urządzenia do hybrydowej usługi Azure AD Dołącz do. Upewnij się, że program Azure AD Connect został zsynchronizowany z obiektami komputera urządzeń, które chcesz dołączyć hybrydowo do usługi Azure AD. Jeśli obiekty komputera należą do określonych jednostek organizacyjnych, to te jednostki muszą również być skonfigurowane na potrzeby synchronizacji w programie Azure AD Connect. Aby dowiedzieć się więcej na temat sposobu synchronizacji obiektów komputerów za pomocą usługi Azure AD Connect, zobacz artykuł [Konfigurowanie filtrowania, za pomocą usługi Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-configure-filtering#organizational-unitbased-filtering).

Jeśli Twoja organizacja wymaga dostępu do Internetu za pośrednictwem serwera proxy ruchu wychodzącego, firma Microsoft zaleca [Implementowanie autowykrywania serwera Proxy sieci Web (WPAD)](https://docs.microsoft.com/previous-versions/tn-archive/cc995261(v%3dtechnet.10)) aby umożliwić komputerom systemu Windows 10 w celu rejestracji urządzeń w usłudze Azure AD. Jeśli występują problemy dotyczące konfigurowania i zarządzania nimi WPAD, przejdź do strony [Rozwiązywanie problemów z automatycznego wykrywania](https://docs.microsoft.com/previous-versions/tn-archive/cc302643(v=technet.10)). 

Jeśli nie używasz WPAD i należy skonfigurować ustawienia serwera proxy na komputerze, możesz to zrobić tak począwszy od systemu Windows 10 1709 przez [konfigurowania ustawień usług WinHTTP przy użyciu obiektu zasad grupy (GPO)](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/).

> [!NOTE]
> Jeśli konfigurujesz ustawienia serwera proxy na komputerze przy użyciu ustawienia WinHTTP wszystkie komputery, które są w stanie połączyć się z skonfigurowany serwer proxy zakończy się niepowodzeniem do łączenia się z Internetem.

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

   ![Omówienie](./media/hybrid-azuread-join-federated-domains/13.png)

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

- Konfigurowanie ustawień lokalnego intranetu na potrzeby rejestracji urządzeń
- Zainstaluj Microsoft pracy Join dla Windows niższego poziomu komputerów

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Konfigurowanie ustawień lokalnego intranetu na potrzeby rejestracji urządzeń

Aby pomyślnie przeprowadzić dołączenie hybrydowe do usługi Azure AD dla urządzeń z systemem Windows niższego poziomu oraz uniknąć monitów o certyfikat podczas uwierzytelniania urządzeń w usłudze Azure AD, możesz wypchnąć zasady do urządzeń dołączonych do domeny, aby dodać następujące adresy URL do lokalnej strefy intranetu w programie Internet Explorer:

- `https://device.login.microsoftonline.com`
- Usługa tokenu zabezpieczającego (STS — domen federacyjnych) w organizacji
- `https://autologon.microsoftazuread-sso.com` (na potrzeby bezproblemowego logowania jednokrotnego).

Ponadto musisz włączyć opcję **Zezwalaj na aktualizacje na pasku stanu za pomocą skryptu** w lokalnej strefie intranetu użytkownika.

### <a name="install-microsoft-workplace-join-for-windows-down-level-computers"></a>Zainstaluj Microsoft pracy Join dla Windows niższego poziomu komputerów

Aby zarejestrować urządzenia niskiego poziomu Windows, należy zainstalować organizacje [Microsoft dołączania komputerów do systemu Windows 10](https://www.microsoft.com/download/details.aspx?id=53554) dostępne w Microsoft Download Center.

Pakiet można wdrożyć za pomocą to system dystrybucji oprogramowania, takie jak [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager). Pakiet obsługuje opcje standardowej instalacji dyskretnej, za pomocą parametru cichy. Current branch programu Configuration Manager zapewnia korzyści w porównaniu ze starszymi wersjami, takich jak możliwość śledzenia rejestracji zakończonych.

Instalator jest utworzenie zaplanowanego zadania w systemie, który jest uruchamiany w kontekście użytkownika. Zadanie jest wyzwalane, gdy użytkownik wykona logowania celu Windows. Zadanie dyskretnie łączy urządzenie z usługą Azure AD przy użyciu poświadczeń użytkownika, po uwierzytelnieniu w usłudze Azure AD.

## <a name="verify-the-registration"></a>Weryfikacja rejestracji

Aby zweryfikować stan rejestracji urządzenia w dzierżawie platformy Azure, możesz użyć polecenia cmdlet **[Get-MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice)** w **[module Azure Active Directory programu PowerShell](/powershell/azure/install-msonlinev1?view=azureadps-2.0)** .

W przypadku użycia polecenia cmdlet **Get-MSolDevice** w celu sprawdzenia szczegółów usługi:

- Obiekt o **identyfikator urządzenia** odpowiadającej Identyfikatora w Windows, klient musi istnieć.
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

- Aby uzyskać więcej informacji o zarządzaniu tożsamościami urządzeń w portalu usługi Azure AD, zobacz [Zarządzanie tożsamościami urządzeń przy użyciu witryny Azure portal](device-management-azure-portal.md).

<!--Image references-->
[1]: ./media/active-directory-conditional-access-automatic-device-registration-setup/12.png
