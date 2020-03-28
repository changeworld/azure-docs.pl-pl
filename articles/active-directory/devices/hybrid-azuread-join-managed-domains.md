---
title: Konfigurowanie hybrydowego dołączania do usługi Azure Active Directory dla domen zarządzanych | Microsoft Docs
description: Dowiedz się, jak skonfigurować hybrydowe dołączanie do usługi Azure Active Directory dla domen zarządzanych.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 03/06/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcd00972c2da0d3d5dafe76a8619e0f0ccaedc19
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239112"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-managed-domains"></a>Samouczek: konfigurowanie dołączania hybrydowego do usługi Azure Active Directory dla domen zarządzanych

W tym samouczku dowiesz się, jak skonfigurować hybrydowe przyłączenie usługi Azure Active Directory (Azure AD) dla urządzeń przyłączonych do domeny usługi Active Directory. Ta metoda obsługuje środowisko zarządzane, które obejmuje zarówno lokalną usługę Active Directory, jak i usługę Azure AD.

Podobnie jak użytkownik w organizacji, urządzenie jest podstawową tożsamością, którą chcesz chronić. Możesz użyć tożsamości urządzenia, aby chronić swoje zasoby w dowolnym czasie i z dowolnego miejsca. Ten cel można osiągnąć, zarządzając tożsamościami urządzeń w usłudze Azure AD. Użyj jednej z następujących metod:

- Dołączenie do usługi Azure AD
- Dołączenie hybrydowe do usługi Azure AD
- Rejestracja w usłudze Azure AD

W tym artykule skupiono się na hybrydowym sprzęcie usługi Azure AD.

Przeniesienie urządzeń do usługi Azure AD maksymalizuje produktywność użytkowników za pomocą logowania jednokrotnego (Logowanie jednokrotne) w chmurze i w zasobach lokalnych. Jednocześnie można zabezpieczyć dostęp do zasobów w chmurze i lokalnie za pomocą [dostępu warunkowego.](../active-directory-conditional-access-azure-portal.md)

Środowisko zarządzane można wdrożyć za pomocą [synchronizacji skrótów haseł (PHS)](../hybrid/whatis-phs.md) lub [uwierzytelniania przekazywanego (PTA)](../hybrid/how-to-connect-pta.md) z [bezproblemowym logowaniem jednokrotnym.](../hybrid/how-to-connect-sso.md) Te scenariusze nie wymagają konfigurowania serwera federacyjnego do uwierzytelniania.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie dołączenia hybrydowego do usługi Azure AD
> * Włączanie urządzeń z systemem Windows niższego poziomu
> * Weryfikowanie dołączonych urządzeń
> * Rozwiązywanie problemów

## <a name="prerequisites"></a>Wymagania wstępne

- Usługa [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) (1.1.819.0 lub nowsza)
- Poświadczenia administratora globalnego dla dzierżawy usługi Azure AD
- Poświadczenia administratora przedsiębiorstwa dla każdego z lasów

Zapoznaj się z tymi artykułami:

- [Co to jest tożsamość urządzenia?](overview.md)
- [Jak: Planowanie implementacji dołączania do hybrydowej usługi Azure Active Directory](hybrid-azuread-join-plan.md)
- [Kontrolowana walidacja przyłączenia do hybrydowej usługi Azure AD](hybrid-azuread-join-control.md)

> [!NOTE]
> Usługa Azure AD nie obsługuje kart inteligentnych ani certyfikatów w domenach zarządzanych.

Sprawdź, czy usługa Azure AD Connect zsynchronizowała obiekty komputerów urządzeń, które mają być hybrydowe usługi Azure AD przyłączone do usługi Azure AD. Jeśli obiekty komputera należą do określonych jednostek organizacyjnych, skonfiguruj jednostki organizacyjne do synchronizacji w usłudze Azure AD Connect. Aby dowiedzieć się więcej o synchronizowaniu obiektów komputera przy użyciu usługi Azure AD Connect, zobacz [Filtrowanie oparte na jednostkach organizacyjnych](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering).

Począwszy od wersji 1.1.819.0, usługa Azure AD Connect zawiera kreatora konfigurowania hybrydowego sprzężenia usługi Azure AD. Kreator znacznie upraszcza proces konfiguracji. Kreator konfiguruje punkty połączenia usługi (SCP) do rejestracji urządzenia.

Kroki konfiguracji opisane w tym artykule są oparte na użyciu kreatora w usłudze Azure AD Connect.

Hybrydowe sprzężenie usługi AD wymaga, aby urządzenia miały dostęp do następujących zasobów firmy Microsoft z sieci organizacji:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com`(Jeśli używasz lub planujesz używać bezszwowego logowania jednokrotnego)

Jeśli organizacja wymaga dostępu do Internetu za pośrednictwem wychodzącego serwera proxy, zalecamy [wdrożenie automatycznego odnajdowania serwera proxy sieci Web (WPAD),](https://docs.microsoft.com/previous-versions/tn-archive/cc995261(v%3dtechnet.10)) aby włączyć komputery z systemem Windows 10 do rejestracji urządzeń w usłudze Azure AD. Aby rozwiązać problemy z konfiguracją systemu WPAD i zarządzaniem nim, zobacz [Rozwiązywanie problemów z automatycznym wykrywaniem](/previous-versions/tn-archive/cc302643(v=technet.10)).

Jeśli nie używasz WPAD, możesz skonfigurować ustawienia serwera proxy na komputerze, począwszy od systemu Windows 10 1709. Aby uzyskać więcej informacji, zobacz [WinHTTP Ustawienia serwera proxy wdrożone przez obiekt zasad grupy](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/).

> [!NOTE]
> Jeśli ustawienia serwera proxy na komputerze zostaną skonfigurowane przy użyciu ustawień winhttp, wszystkie komputery, które nie mogą połączyć się ze skonfigurowanym serwerem proxy, nie powiodą się z Internetem.

Jeśli organizacja wymaga dostępu do Internetu za pośrednictwem uwierzytelnionego wychodzącego serwera proxy, upewnij się, że komputery z systemem Windows 10 mogą pomyślnie uwierzytelnić się na wychodzącym serwerze proxy. Ponieważ komputery z systemem Windows 10 uruchamiają rejestrację urządzeń przy użyciu kontekstu komputera, skonfiguruj uwierzytelnianie wychodzącego serwera proxy przy użyciu kontekstu komputera. Skontaktuj się z dostawcą serwera proxy ruchu wychodzącego, aby uzyskać informacje na temat wymagań dotyczących konfiguracji.

Sprawdź, czy urządzenie może uzyskać dostęp do powyższych zasobów firmy Microsoft w ramach konta systemowego przy użyciu skryptu [łączności rejestracji urządzeń testowych.](https://gallery.technet.microsoft.com/Test-Device-Registration-3dc944c0)

## <a name="configure-hybrid-azure-ad-join"></a>Konfigurowanie dołączenia hybrydowego do usługi Azure AD

Aby skonfigurować hybrydowe sprzężenie usługi Azure AD przy użyciu usługi Azure AD Connect:

1. Uruchom usługę Azure AD Connect, a następnie wybierz pozycję **Konfiguruj**.

   ![Powitanie](./media/hybrid-azuread-join-managed-domains/welcome-azure-ad-connect.png)

1. W **obszarze Dodatkowe zadania**wybierz pozycję **Konfiguruj opcje urządzenia**, a następnie wybierz pozycję **Dalej**.

   ![Dodatkowe zadania](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-additional-tasks.png)

1. W **obszarze Przegląd**wybierz pozycję **Dalej**.

   ![Omówienie](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-overview.png)

1. W **obszarze Połącz z usługą Azure AD**wprowadź poświadczenia administratora globalnego dla dzierżawy usługi Azure AD.  

   ![Łączenie z usługą Azure AD](./media/hybrid-azuread-join-managed-domains/connect-to-azure-ad-username-password.png)

1. W **obszarze Opcje urządzenia**wybierz pozycję **Konfiguruj hybrydowe sprzężenie usługi Azure AD**, a następnie wybierz pozycję **Dalej**.

   ![Opcje urządzenia](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-device-options.png)

1. W **konfiguracji protokołu SCP**dla każdego lasu, w którym ma być skonfigurowana usługa Azure AD Connect, należy wykonać następujące kroki, a następnie wybrać **pozycję Dalej**.

   1. Wybierz **las**.
   1. Wybierz **usługę uwierzytelniania**.
   1. Wybierz **pozycję Dodaj,** aby wprowadzić poświadczenia administratora przedsiębiorstwa.

   ![SCP](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-scp-configuration.png)

1. W **systemach operacyjnych Urządzenia**wybierz systemy operacyjne używane przez urządzenia w środowisku usługi Active Directory, a następnie wybierz pozycję **Dalej**.

   ![System operacyjny urządzenia](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-device-operating-systems.png)

1. W **obszarze Gotowe do skonfigurowania**wybierz pozycję **Konfiguruj**.

   ![Wszystko gotowe do skonfigurowania](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-ready-to-configure.png)

1. W **obszarze Konfiguracja zakończona**wybierz pozycję **Zakończ**.

   ![Ukończono konfigurację](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-configuration-complete.png)

## <a name="enable-windows-down-level-devices"></a>Włączanie urządzeń z systemem Windows niższego poziomu

Jeśli niektóre urządzenia przyłączone do domeny są urządzeniami z systemem Windows na poziomie down, należy:

- Konfigurowanie ustawień lokalnego intranetu na potrzeby rejestracji urządzeń
- Konfigurowanie bezproblemowego logowania jednokrotnego
- Instalowanie programu Microsoft Workplace Join dla komputerów z systemem Windows w wersji down-level

> [!NOTE]
> Obsługa systemu Windows 7 zakończyła się 14 stycznia 2020 roku. Aby uzyskać więcej informacji, zobacz [Obsługa systemu Windows 7 zakończona](https://support.microsoft.com/help/4057281/windows-7-support-ended-on-january-14-2020).

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Konfigurowanie ustawień lokalnego intranetu na potrzeby rejestracji urządzeń

Aby ukończyć hybrydowe sprzężenie usługi Azure AD urządzeń z systemem Windows w dół i uniknąć monitów o certyfikat podczas uwierzytelniania urządzeń usługi Azure AD, można wypchnąć zasady do urządzeń przyłączonych do domeny, aby dodać następujące adresy URL do lokalnej strefy intranetu w programie Internet Explorer:

- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com`

Należy również włączyć **zezwalaj na aktualizacje paska stanu za pomocą skryptu** w lokalnej strefie intranetu użytkownika.

### <a name="configure-seamless-sso"></a>Konfigurowanie bezproblemowego logowania jednokrotnego

Aby ukończyć hybrydowe sprzężenie usługi Azure AD urządzeń w dół systemu Windows w domenie zarządzanej, która używa [synchronizacji skrótu hasła](../hybrid/whatis-phs.md) lub [uwierzytelniania przekazywanego](../hybrid/how-to-connect-pta.md) jako metody uwierzytelniania w chmurze usługi Azure AD, należy również [skonfigurować bezproblemowe logowanie jednokrotne.](../hybrid/how-to-connect-sso-quick-start.md#step-2-enable-the-feature)

### <a name="install-microsoft-workplace-join-for-windows-down-level-computers"></a>Instalowanie programu Microsoft Workplace Join dla komputerów z systemem Windows w wersji down-level

Aby zarejestrować urządzenia z systemem Windows w dół, organizacje muszą zainstalować [usługę Microsoft Workplace Join dla komputerów z systemem innych niż Windows 10](https://www.microsoft.com/download/details.aspx?id=53554). Aplikacja Microsoft Workplace Join dla komputerów z systemem windows 10 jest dostępna w Centrum pobierania Microsoft.

Pakiet można wdrożyć za pomocą systemu dystrybucji oprogramowania, takiego jak [program Microsoft Endpoint Configuration Manager](/configmgr/). Pakiet obsługuje standardowe opcje instalacji `quiet` dyskretnej z parametrem. Bieżąca wersja programu Configuration Manager oferuje korzyści w stosunku do wcześniejszych wersji, takich jak możliwość śledzenia zakończonych rejestracji.

Instalator tworzy zaplanowane zadanie w systemie, który działa w kontekście użytkownika. Zadanie jest wyzwalane, gdy użytkownik loguje się do systemu Windows. Zadanie dyskretnie łączy urządzenie z usługą Azure AD przy użyciu poświadczeń użytkownika po uwierzytelnii się za pomocą usługi Azure AD.

## <a name="verify-the-registration"></a>Weryfikacja rejestracji

Sprawdź stan rejestracji urządzenia w dzierżawie platformy Azure przy użyciu **[polecenia Get-MsolDevice](/powershell/msonline/v1/get-msoldevice)**. To polecenie cmdlet znajduje się w [module programu PowerShell usługi Azure Active Directory](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

Podczas korzystania z polecenia cmdlet **Get-MSolDevice,** aby sprawdzić szczegóły usługi:

- Obiekt o **identyfikatorze urządzenia,** który pasuje do identyfikatora na kliencie systemu Windows musi istnieć.
- Wartość **DeviceTrustType** to **Przyłączono do domeny**. To ustawienie jest równoważne hybrydowej **usługi Azure AD przyłączone** stan na **urządzeniach** strony w portalu usługi Azure AD.
- W przypadku urządzeń używanych w programie Dostępwarunkowy wartość **włączonej** wartości to **Prawda,** a **poziom zaufania urządzenia** — **zarządzanie**.

Aby sprawdzić szczegóły usługi:

1. Uruchom program Windows PowerShell jako administrator.
1. Wprowadź, `Connect-MsolService` aby połączyć się z dzierżawą platformy Azure.  
1. Wprowadź polecenie `get-msoldevice -deviceId <deviceId>`.
1. Upewnij się, że opcja **Włączone** ma wartość **Prawda**.

## <a name="troubleshoot-your-implementation"></a>Rozwiązywanie problemów z implementacją

Jeśli występują problemy z ukończeniem hybrydowego sprzężenia usługi Azure AD dla urządzeń z systemem Windows przyłączonych do domeny, zobacz:

- [Rozwiązywanie problemów z hybrydowymi urządzeniami przyłączanym do usługi Azure Active Directory](troubleshoot-hybrid-join-windows-current.md)
- [Rozwiązywanie problemów z hybrydowymi urządzeniami usługi Azure Active Directory przyłączanych do urządzeń poziomu sieciowego](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu, aby dowiedzieć się, jak zarządzać tożsamościami urządzeń przy użyciu witryny Azure portal.
> [!div class="nextstepaction"]
> [Zarządzanie tożsamościami urządzeń](device-management-azure-portal.md)
