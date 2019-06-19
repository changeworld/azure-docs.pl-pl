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
ms.openlocfilehash: 738b4f47054081f0fb1b1a530bdf21cbf07a7726
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204696"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-federated-domains"></a>Samouczek: Konfigurowanie hybrydowego dołączania do usługi Azure Active Directory dla domen federacyjnych

Podobnie jak użytkownik w organizacji urządzenie jest tożsamości podstawowej, który chcesz chronić. Tożsamość urządzenia można użyć do ochrony zasobów, w dowolnym momencie i z dowolnego miejsca. Ten cel można osiągnąć przez wprowadzenie tożsamości urządzeń i zarządzania nimi w usłudze Azure Active Directory (Azure AD) przy użyciu jednej z następujących metod:

- Dołączenie do usługi Azure AD
- Dołączenie hybrydowe do usługi Azure AD
- Rejestracja w usłudze Azure AD

Urządzenia do usługi Azure AD pozwala zwiększyć produktywność użytkowników za pomocą logowania jednokrotnego (SSO) w chmurze i zasobów lokalnych. Można zabezpieczyć dostęp do zasobów w chmurze i lokalnych przy użyciu [dostępu warunkowego](../active-directory-conditional-access-azure-portal.md) w tym samym czasie.

W tym samouczku dowiesz się, jak skonfigurować hybrydowych usługi Azure AD join dla urządzeń z komputerów przyłączonych do domeny usługi Active Directory w środowisku federacyjnym przy użyciu usługi Active Directory Federation Services (AD FS).

> [!NOTE]
> Jeśli w środowisku federacyjnym używa dostawcy tożsamości innych niż usługi AD FS, upewnij się, że dostawca tożsamości obsługuje protokół WS-Trust. WS-Trust jest wymagany do uwierzytelniania bieżącego hybrydowe usługi Azure AD Windows przyłączone do urządzenia z usługą Azure AD. Jeśli masz urządzenia niskiego poziomu Windows, które mają być dołączenie do hybrydowej usługi Azure AD, dostawcy tożsamości musi obsługiwać oświadczenia WIAORMULTIAUTHN. 

Omawiane kwestie:

> [!div class="checklist"]
> * Konfigurowanie dołączenia hybrydowego do usługi Azure AD
> * Włączanie na urządzeniach Windows niższego poziomu
> * Weryfikacja rejestracji
> * Rozwiązywanie problemów

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku przyjęto założenie, że you'e zapoznać się z tych artykułów:

- [Co to jest tożsamość urządzenia?](overview.md)
- [Jak planowanie implementacji hybrydowej usługi Azure AD join](hybrid-azuread-join-plan.md)
- [Jak przeprowadzić kontrolowana Walidacja dołączenie do hybrydowej usługi Azure AD](hybrid-azuread-join-control.md)

Aby skonfigurować scenariusz zaprezentowany w tym samouczku, potrzebujesz:

- Windows Server 2012 R2 z usługami AD FS
- [Program Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) wersji 1.1.819.0 lub nowszej

Począwszy od wersji 1.1.819.0, program Azure AD Connect zawiera kreatora, który służy do konfigurowania dołączenie do hybrydowej usługi Azure AD. Kreator znacznie upraszcza proces konfiguracji. Powiązany kreator:

- Konfiguruje punktów połączenia usługi (SCP) dla rejestracji urządzeń
- Tworzy kopię zapasową istniejącej relacji zaufania dla jednostki zależnej usługi Azure AD.
- Aktualizuje reguły dotyczące oświadczeń w relacji zaufania usługi Azure AD.

Kroki konfiguracji, w tym artykule są oparte na temat korzystania z Kreatora programu Azure AD Connect. Jeśli masz wcześniejszej wersji programu Azure AD Connect, należy uaktualnić 1.1.819 lub później za pomocą kreatora. Jeśli zainstalowanie najnowszej wersji programu Azure AD Connect nie jest dostępną opcją w, zobacz [jak ręcznie skonfigurować dołączenie do hybrydowej usługi Azure AD](hybrid-azuread-join-manual.md).

Dołączenie do hybrydowej usługi Azure AD wymaga, aby urządzenia mają mieć dostęp do następujących zasobów firmy Microsoft z wewnątrz sieci organizacji:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- Twoja organizacja Usługa tokenu zabezpieczającego (STS) (w przypadku domen federacyjnych)
- `https://autologon.microsoftazuread-sso.com` (Jeśli jest używany, lub planowane jest użycie bezproblemowe logowanie Jednokrotne)

Począwszy od z systemem Windows 10 1803, jeśli natychmiastowe hybrydowego sprzężenia usługi Azure AD w środowisku federacyjnym za pomocą usług AD FS nie powiedzie się, możemy polegać na program Azure AD Connect do synchronizowania obiektu komputera w usłudze Azure AD, która jest następnie używane do ukończenia rejestracji urządzenia do hybrydowej usługi Azure Dołączania do usługi AD. Sprawdź, czy program Azure AD Connect ma zsynchronizowane obiekty komputerów, urządzeń, które mają być hybrydowe do usługi Azure AD do usługi Azure AD. Jeśli obiekty komputerów należy do określonej jednostki organizacyjne (OU), należy także skonfigurować jednostek organizacyjnych do synchronizacji w programie Azure AD Connect. Aby dowiedzieć się więcej na temat synchronizowania obiektów komputerów przy użyciu usługi Azure AD Connect, zobacz [skonfiguruj filtrowanie przy użyciu usługi Azure AD Connect](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering).

Jeśli Twoja organizacja wymaga dostępu do Internetu za pośrednictwem serwera proxy ruchu wychodzącego, firma Microsoft zaleca [Implementowanie autowykrywania serwera Proxy sieci Web (WPAD)](https://docs.microsoft.com/previous-versions/tn-archive/cc995261(v%3dtechnet.10)) aby umożliwić komputerom z systemem Windows 10 rejestracji urządzeń w usłudze Azure AD. Jeśli wystąpią problemy, konfigurowanie i zarządzanie nimi WPAD, zobacz [Rozwiązywanie problemów z automatycznego wykrywania](https://docs.microsoft.com/previous-versions/tn-archive/cc302643(v=technet.10)). 

Jeśli nie używasz WPAD i chcesz skonfigurować ustawienia serwera proxy na komputerze, należy więc począwszy od systemu Windows 10 1709. Aby uzyskać więcej informacji, zobacz [WinHTTP skonfigurować ustawienia za pomocą obiektu zasad grupy (GPO)](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/).

> [!NOTE]
> Jeśli konfigurujesz ustawienia serwera proxy na komputerze przy użyciu ustawień WinHTTP wszystkie komputery, których nie można nawiązać połączenia z skonfigurowany serwer proxy zakończy się niepowodzeniem nawiązać połączenie z Internetem.

Jeśli Twoja organizacja wymaga dostępu do Internetu za pośrednictwem uwierzytelnionego serwera proxy ruchu wychodzącego, upewnij się, że komputery z systemem Windows 10 może pomyślnie uwierzytelnić się serwera proxy ruchu wychodzącego. Ponieważ komputery z systemem Windows 10 są uruchamiane rejestracji urządzenia przy użyciu kontekstu komputera, należy skonfigurować uwierzytelnianie serwera proxy ruchu wychodzącego, za pomocą kontekstu maszyny. Skontaktuj się z dostawcą serwera proxy ruchu wychodzącego, aby uzyskać informacje na temat wymagań dotyczących konfiguracji.

## <a name="configure-hybrid-azure-ad-join"></a>Konfigurowanie dołączenia hybrydowego do usługi Azure AD

Aby skonfigurować dołączenie do hybrydowej usługi Azure AD przy użyciu usługi Azure AD Connect, należy:

- Poświadczenia administratora globalnego dla dzierżawy usługi Azure AD  
- Poświadczenia administratora przedsiębiorstwa dla każdego lasu
- Poświadczenia administratora usług AD FS

**Aby skonfigurować dołączenie do hybrydowej usługi Azure AD przy użyciu usługi Azure AD Connect**:

1. Uruchom program Azure AD Connect, a następnie wybierz pozycję **Konfiguruj**.

   ![Witaj](./media/hybrid-azuread-join-federated-domains/11.png)

1. Na **dodatkowe zadania** wybierz opcję **Konfiguruj opcje urządzenia**, a następnie wybierz pozycję **dalej**.

   ![Dodatkowe zadania](./media/hybrid-azuread-join-federated-domains/12.png)

1. Na **Przegląd** wybierz opcję **dalej**.

   ![Omówienie](./media/hybrid-azuread-join-federated-domains/13.png)

1. Na **nawiązywanie połączenia z usługi Azure AD** strony, wprowadź poświadczenia administratora globalnego dla dzierżawy usługi Azure AD, a następnie wybierz pozycję **dalej**.

   ![Łączenie z usługą Azure AD](./media/hybrid-azuread-join-federated-domains/14.png)

1. Na **opcje urządzenia** wybierz opcję **Konfigurowanie hybrydowej usługi Azure AD join**, a następnie wybierz pozycję **dalej**.

   ![Opcje urządzenia](./media/hybrid-azuread-join-federated-domains/15.png)

1. Na **SCP** strony, wykonaj następujące czynności, a następnie wybierz pozycję **dalej**:

   ![Punkt połączenia z usługą](./media/hybrid-azuread-join-federated-domains/16.png)

   1. Wybierz las.
   1. Wybierz usługę uwierzytelniania. Musisz wybrać **serwera usług AD FS** chyba, że Twoja organizacja ma wyłącznie klientów z systemem Windows 10 i skonfigurowaniu synchronizacji komputera/urządzenia lub Twoja organizacja korzysta z bezproblemowe logowanie Jednokrotne.
   1. Wybierz **Dodaj** wprowadzić poświadczenia administratora przedsiębiorstwa.

1. Na **systemów operacyjnych urządzeń** wybierz systemy operacyjne, korzystających z urządzeń w środowisku usługi Active Directory, a następnie wybierz pozycję **dalej**.

   ![System operacyjny urządzenia](./media/hybrid-azuread-join-federated-domains/17.png)

1. Na **Konfiguracja Federacji** strony, wprowadź poświadczenia administratora usług AD FS, a następnie wybierz pozycję **dalej**.

   ![Konfiguracja federacji](./media/hybrid-azuread-join-federated-domains/18.png)

1. Na **wszystko gotowe do skonfigurowania** wybierz opcję **Konfiguruj**.

   ![Wszystko gotowe do skonfigurowania](./media/hybrid-azuread-join-federated-domains/19.png)

1. Na **ukończyć konfiguracji** wybierz opcję **zakończenia**.

   ![Ukończono konfigurację](./media/hybrid-azuread-join-federated-domains/20.png)

## <a name="enable-windows-downlevel-devices"></a>Włączanie na urządzeniach Windows niższego poziomu

W przypadku niektórych urządzeń przyłączonych do domeny urządzenia niskiego poziomu Windows, musisz mieć:

- Konfigurowanie ustawień lokalnego intranetu na potrzeby rejestracji urządzeń
- Zainstaluj Microsoft pracy Join dla Windows niższego poziomu komputerów

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Konfigurowanie ustawień lokalnego intranetu na potrzeby rejestracji urządzeń

Aby pomyślnie zakończyć hybrydowych usługi Azure AD join urządzeń Windows niższego poziomu, a aby monity certyfikatu podczas uwierzytelniania urządzeń do usługi Azure AD, możesz wypchnąć zasad na urządzeniach przyłączonych do domeny można dodać następujące adresy URL do strefy Lokalny intranet w programie Internet Explorer:

- `https://device.login.microsoftonline.com`
- Twoja organizacja STS (w przypadku domen federacyjnych)
- `https://autologon.microsoftazuread-sso.com` (W przypadku bezproblemowe logowanie Jednokrotne)

Należy również włączyć **zezwala na aktualizacje na pasku stanu za pomocą skryptu** w strefie Lokalny intranet użytkownika.

### <a name="install-microsoft-workplace-join-for-windows-downlevel-computers"></a>Zainstaluj Microsoft pracy Join dla Windows niższego poziomu komputerów

Aby zarejestrować urządzenia niskiego poziomu Windows, należy zainstalować organizacje [Microsoft dołączania komputerów do systemu Windows 10](https://www.microsoft.com/download/details.aspx?id=53554). Microsoft Workplace Join na komputerach bez systemu Windows 10 jest dostępna w programie Microsoft Download Center.

Pakiet można wdrożyć za pomocą to system dystrybucji oprogramowania, takie jak [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager). Pakiet obsługuje opcje standardowej instalacji dyskretnej przy użyciu `quiet` parametru. Current branch programu Configuration Manager zapewnia korzyści w porównaniu ze starszymi wersjami, takich jak możliwość śledzenia rejestracji zakończonych.

Instalator jest utworzenie zaplanowanego zadania w systemie, który jest uruchamiany w kontekście użytkownika. Zadanie jest wyzwalane, gdy użytkownik loguje się do Windows. Zadanie dyskretnie łączy urządzenie z usługą Azure AD przy użyciu poświadczeń użytkownika, po uwierzytelnia się za pomocą usługi Azure AD.

## <a name="verify-the-registration"></a>Weryfikacja rejestracji

Aby sprawdzić stanu rejestracji urządzenia w dzierżawie platformy Azure, możesz użyć **[Get MsolDevice](/powershell/msonline/v1/get-msoldevice)** polecenia cmdlet w [modułu programu PowerShell usługi Azure Active Directory](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

Kiedy używasz **Get MSolDevice** polecenia cmdlet, aby sprawdzić szczegóły usługi:

- Obiekt o **identyfikator urządzenia** odpowiadającej Identyfikatora w Windows, klient musi istnieć.
- Wartością atrybutu **DeviceTrustType** musi być **Dołączone do domeny**. To ustawienie jest odpowiednikiem **przyłączone do hybrydowej usługi Azure AD** stanu w obszarze **urządzeń** w portalu usługi Azure AD.
- Dla urządzeń, które są używane w funkcji dostępu warunkowego, wartość **włączone** musi być **True** i **DeviceTrustLevel** musi być **zarządzane**.

**Aby sprawdzić szczegóły usługi**:

1. Otwórz program Windows PowerShell jako administrator.
1. Wprowadź `Connect-MsolService` połączyć z dzierżawą platformy Azure.  
1. Wprowadź polecenie `get-msoldevice -deviceId <deviceId>`.
1. Upewnij się, że opcja **Włączone** ma wartość **Prawda**.

## <a name="troubleshoot-your-implementation"></a>Rozwiązywanie problemów z implementacją

Jeśli występują problemy z wykonaniem dołączenie do hybrydowej usługi Azure AD dla urządzeń Windows przyłączone do domeny, zobacz:

- [Rozwiązywanie problemów z hybrydowych usługi Azure AD join dla bieżącego urządzeń Windows](troubleshoot-hybrid-join-windows-current.md)
- [Rozwiązywanie problemów z hybrydowych usługi Azure AD join dla systemu Windows niższego poziomu urządzeń](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak [Zarządzanie tożsamościami urządzeń przy użyciu witryny Azure portal](device-management-azure-portal.md).

<!--Image references-->
[1]: ./media/active-directory-conditional-access-automatic-device-registration-setup/12.png
