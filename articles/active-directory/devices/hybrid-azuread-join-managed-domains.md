---
title: Konfigurowanie hybrydowego dołączania do usługi Azure Active Directory dla domen zarządzanych | Microsoft Docs
description: Dowiedz się, jak skonfigurować hybrydowe dołączanie do usługi Azure Active Directory dla domen zarządzanych.
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
ms.openlocfilehash: 787900918035dc8b14d3a173496ab1a23b0f93bb
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68813086"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-managed-domains"></a>Samouczek: Konfigurowanie hybrydowego dołączania do usługi Azure Active Directory dla domen zarządzanych

Podobnie jak w przypadku użytkownika w organizacji, urządzenie to podstawowa tożsamość, która ma być chroniona. Możesz użyć tożsamości urządzenia do ochrony zasobów w dowolnym momencie i z dowolnej lokalizacji. Ten cel można osiągnąć przez umieszczenie tożsamości urządzeń i zarządzanie nimi w Azure Active Directory (Azure AD) za pomocą jednej z następujących metod:

- Dołączenie do usługi Azure AD
- Dołączenie hybrydowe do usługi Azure AD
- Rejestracja w usłudze Azure AD

Przełączenie urządzeń do usługi Azure AD maksymalizuje produktywność użytkowników dzięki logowaniem jednokrotnym (SSO) w chmurze i zasobach lokalnych. Jednocześnie można zabezpieczyć dostęp do zasobów w chmurze i lokalnych przy użyciu [dostępu warunkowego](../active-directory-conditional-access-azure-portal.md) .

W tym samouczku dowiesz się, jak skonfigurować sprzężenie hybrydowe usługi Azure AD dla urządzeń przyłączonych Active Directory do domeny w środowisku zarządzanym. 

Środowisko zarządzane można wdrożyć za pomocą [synchronizacji skrótów haseł (PHS)](../hybrid/whatis-phs.md) lub [uwierzytelniania przekazującego (PTA)](../hybrid/how-to-connect-pta.md) z bezproblemowym [logowaniem](../hybrid/how-to-connect-sso.md)jednokrotnym. Te scenariusze nie wymagają konfigurowania serwera federacyjnego na potrzeby uwierzytelniania.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie dołączenia hybrydowego do usługi Azure AD
> * Włączanie urządzeń z systemem Windows niższego poziomu
> * Weryfikowanie dołączonych urządzeń
> * Rozwiązywanie problemów

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku założono, że znasz następujące artykuły:

- [Co to jest tożsamość urządzenia?](overview.md)
- [Jak zaplanować implementację hybrydowej funkcji dołączania do usługi Azure AD](hybrid-azuread-join-plan.md)
- [Jak sprawdzać poprawność funkcji dołączania hybrydowego usługi Azure AD](hybrid-azuread-join-control.md)

> [!NOTE]
> Usługa Azure AD nie obsługuje kart inteligentnych ani certyfikatów w domenach zarządzanych.

Aby skonfigurować scenariusz w tym artykule, potrzebna jest [Najnowsza wersja Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) (1.1.819.0 lub nowsza).

Sprawdź, czy Azure AD Connect synchronizuje obiekty komputerów urządzeń, które mają być przyłączone do usługi Azure AD jako hybrydowe. Jeśli obiekty komputera należą do określonych jednostek organizacyjnych (OU), należy również skonfigurować jednostki organizacyjne do synchronizacji w Azure AD Connect. Aby dowiedzieć się więcej o synchronizowaniu obiektów komputerów za pomocą Azure AD Connect, zobacz [Konfigurowanie filtrowania przy użyciu Azure AD Connect](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering).

Począwszy od wersji 1.1.819.0, Azure AD Connect zawiera kreatora, którego można użyć do skonfigurowania hybrydowego sprzężenia usługi Azure AD. Kreator znacznie upraszcza proces konfiguracji. Kreator konfiguruje punkty połączenia usługi (punktu SCP) na potrzeby rejestracji urządzeń.

Kroki konfiguracji opisane w tym artykule opierają się na użyciu kreatora w Azure AD Connect.

Hybrydowe dołączenie usługi Azure AD wymaga, aby urządzenia miały dostęp do następujących zasobów firmy Microsoft z sieci organizacji:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com`(Jeśli używasz lub planujesz korzystanie z bezproblemowego logowania jednokrotnego)

Jeśli Twoja organizacja wymaga dostępu do Internetu za pośrednictwem serwera proxy wychodzącego, firma Microsoft zaleca zaimplementowanie funkcji [autowykrywania serwera proxy sieci Web (WPAD)](https://docs.microsoft.com/previous-versions/tn-archive/cc995261(v%3dtechnet.10)) w celu umożliwienia komputerom z systemem Windows 10 rejestracji urządzeń w usłudze Azure AD. Jeśli wystąpią problemy podczas konfigurowania usługi WPAD i zarządzania nią, zobacz [Rozwiązywanie problemów z automatycznym](https://docs.microsoft.com/previous-versions/tn-archive/cc302643(v=technet.10))wykryciem. 

Jeśli nie korzystasz z usługi WPAD i nie musisz konfigurować ustawień serwera proxy na komputerze, możesz to zrobić, rozpoczynając od systemu Windows 10 1709. Aby uzyskać więcej informacji, zobacz [Konfigurowanie ustawień WinHTTP przy użyciu obiektu zasad grupy (GPO)](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/).

> [!NOTE]
> W przypadku skonfigurowania ustawień serwera proxy na komputerze przy użyciu ustawień usługi WinHTTP wszystkie komputery, które nie mogą nawiązać połączenia ze skonfigurowanym serwerem proxy, nie będą mogły połączyć się z Internetem.

Jeśli Twoja organizacja wymaga dostępu do Internetu za pośrednictwem uwierzytelnionego wychodzącego serwera proxy, musisz upewnić się, że komputery z systemem Windows 10 mogą pomyślnie uwierzytelniać się na wychodzącym serwerze proxy. Ponieważ komputery z systemem Windows 10 uruchamiają rejestrację urządzeń przy użyciu kontekstu komputera, należy skonfigurować uwierzytelnianie serwera proxy wychodzące przy użyciu kontekstu komputera. Skontaktuj się z dostawcą serwera proxy ruchu wychodzącego, aby uzyskać informacje na temat wymagań dotyczących konfiguracji.

Aby sprawdzić, czy urządzenie jest w stanie uzyskać dostęp do powyższych zasobów firmy Microsoft w ramach konta systemowego, można użyć skryptu [łączności rejestracji urządzeń testowych](https://gallery.technet.microsoft.com/Test-Device-Registration-3dc944c0) .

## <a name="configure-hybrid-azure-ad-join"></a>Konfigurowanie dołączenia hybrydowego do usługi Azure AD

Aby skonfigurować dołączanie hybrydowe do usługi Azure AD przy użyciu programu Azure AD Connect, potrzebujesz następujących elementów:

- Poświadczenia administratora globalnego dla dzierżawy usługi Azure AD
- Poświadczenia administratora przedsiębiorstwa dla każdego z lasów

**Aby skonfigurować sprzężenie hybrydowe usługi Azure AD przy użyciu Azure AD Connect:**

1. Uruchom Azure AD Connect, a następnie wybierz pozycję **Konfiguruj**.

   ![Witaj](./media/hybrid-azuread-join-managed-domains/11.png)

1. Na stronie **dodatkowe zadania** wybierz pozycję **Konfiguruj opcje urządzenia**, a następnie wybierz przycisk **dalej**.

   ![Dodatkowe zadania](./media/hybrid-azuread-join-managed-domains/12.png)

1. Na stronie **Przegląd** wybierz pozycję **dalej**.

   ![Przegląd](./media/hybrid-azuread-join-managed-domains/13.png)

1. Na stronie **Łączenie z usługą Azure AD** wprowadź poświadczenia administratora globalnego dzierżawy usługi Azure AD.  

   ![Łączenie z usługą Azure AD](./media/hybrid-azuread-join-managed-domains/14.png)

1. Na stronie **Opcje urządzenia** wybierz pozycję **Konfiguruj sprzężenie hybrydowe usługi Azure AD**, a następnie wybierz przycisk **dalej**.

   ![Opcje urządzenia](./media/hybrid-azuread-join-managed-domains/15.png)

1. Na stronie **SCP** dla każdego lasu, w którym chcesz Azure AD Connect skonfigurować punkt połączenia usługi, wykonaj następujące czynności, a następnie wybierz przycisk **dalej**:

   ![Punkt połączenia z usługą](./media/hybrid-azuread-join-managed-domains/16.png)

   1. Wybierz las.
   1. Wybierz usługę uwierzytelniania.
   1. Wybierz pozycję **Dodaj** , aby wprowadzić poświadczenia administratora przedsiębiorstwa.

1. Na stronie **systemy operacyjne urządzeń** wybierz systemy operacyjne używane przez urządzenia w środowisku Active Directory, a następnie wybierz przycisk **dalej**.

   ![System operacyjny urządzenia](./media/hybrid-azuread-join-managed-domains/17.png)

1. Na stronie **gotowy do skonfigurowania** wybierz pozycję **Konfiguruj**.

   ![Wszystko gotowe do skonfigurowania](./media/hybrid-azuread-join-managed-domains/19.png)

1. Na stronie **Konfiguracja ukończona** wybierz pozycję **Zakończ**.

   ![Ukończono konfigurację](./media/hybrid-azuread-join-managed-domains/20.png)

## <a name="enable-windows-downlevel-devices"></a>Włącz urządzenia niskiego poziomu systemu Windows

Jeśli niektóre urządzenia dołączone do domeny są urządzeniami niskiego poziomu systemu Windows, musisz:

- Konfigurowanie ustawień lokalnego intranetu na potrzeby rejestracji urządzeń
- Konfigurowanie bezproblemowego logowania jednokrotnego
- Zainstaluj program Microsoft Workplace Join dla komputerów niskiego poziomu systemu Windows

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Konfigurowanie ustawień lokalnego intranetu na potrzeby rejestracji urządzeń

Aby pomyślnie zakończyć dołączanie hybrydowej usługi Azure AD do urządzeń niskiego poziomu systemu Windows i uniknąć wyświetlania wierszy przez certyfikat podczas uwierzytelniania urządzeń w usłudze Azure AD, można wypchnąć zasady do urządzeń przyłączonych do domeny w celu dodania następujących adresów URL do strefy Lokalny intranet w Internecie Programie

- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com`

Należy również włączyć opcję **Zezwalaj na aktualizacje na pasku stanu za pomocą skryptu** w strefie Lokalny intranet użytkownika.

### <a name="configure-seamless-sso"></a>Konfigurowanie bezproblemowego logowania jednokrotnego

Aby pomyślnie zakończyć dołączanie hybrydowej usługi Azure AD urządzeń niskiego poziomu systemu Windows w domenie zarządzanej używającej [PHS](../hybrid/whatis-phs.md) lub [PTA](../hybrid/how-to-connect-pta.md) jako metody uwierzytelniania w chmurze usługi Azure AD, należy również [skonfigurować bezproblemowe logowanie](../hybrid/how-to-connect-sso-quick-start.md#step-2-enable-the-feature)jednokrotne.

### <a name="install-microsoft-workplace-join-for-windows-downlevel-computers"></a>Zainstaluj program Microsoft Workplace Join dla komputerów niskiego poziomu systemu Windows

Aby zarejestrować urządzenia niskiego poziomu systemu Windows, organizacje muszą zainstalować [Workplace Join firmy Microsoft dla komputerów z systemem innym niż Windows 10](https://www.microsoft.com/download/details.aspx?id=53554). Program Microsoft Workplace Join dla komputerów z systemem innym niż Windows 10 jest dostępny w centrum pobierania Microsoft.

Pakiet można wdrożyć przy użyciu systemu dystrybucji oprogramowania, takiego jak [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager). Pakiet obsługuje standardowe opcje instalacji dyskretnej z `quiet` parametrem. Bieżąca gałąź Configuration Manager oferuje korzyści w porównaniu z wcześniejszymi wersjami, takie jak możliwość śledzenia zakończonych rejestracji.

Instalator tworzy zaplanowane zadanie w systemie, który działa w kontekście użytkownika. Zadanie jest wyzwalane, gdy użytkownik loguje się do systemu Windows. Zadanie dyskretnie dołącza urządzenie do usługi Azure AD przy użyciu poświadczeń użytkownika po uwierzytelnieniu w usłudze Azure AD.

## <a name="verify-the-registration"></a>Weryfikacja rejestracji

Aby sprawdzić stan rejestracji urządzenia w dzierżawie platformy Azure, możesz użyć polecenia cmdlet **[Get-MsolDevice](/powershell/msonline/v1/get-msoldevice)** w [module Azure Active Directory PowerShell](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

Korzystając z polecenia cmdlet **Get-MSolDevice** , można sprawdzić szczegóły usługi:

- Obiekt z IDENTYFIKATORem **urządzenia** , który jest zgodny z identyfikatorem na kliencie systemu Windows, musi istnieć.
- Wartością atrybutu **DeviceTrustType** musi być **Dołączone do domeny**. To ustawienie jest równoważne ze stanem przyłączonym do hybrydowej usługi **Azure AD** na stronie **urządzenia** w portalu usługi Azure AD.
- W przypadku urządzeń, które są używane w dostęp warunkowy, wartość **włączone** musi mieć wartość **true** , a **DeviceTrustLevel** musi być **zarządzany**.

**Aby sprawdzić szczegóły usługi**:

1. Otwórz program Windows PowerShell jako administrator.
1. Wprowadź `Connect-MsolService` , aby nawiązać połączenie z dzierżawcą platformy Azure.  
1. Wprowadź polecenie `get-msoldevice -deviceId <deviceId>`.
1. Upewnij się, że opcja **Włączone** ma wartość **Prawda**.

## <a name="troubleshoot-your-implementation"></a>Rozwiązywanie problemów z implementacją

Jeśli wystąpią problemy z ukończeniem hybrydowego przyłączania do usługi Azure AD dla przyłączonych do domeny urządzeń z systemem Windows, zobacz:

- [Rozwiązywanie problemów z funkcją hybrydowego przyłączania do usługi Azure AD](troubleshoot-hybrid-join-windows-current.md)
- [Rozwiązywanie problemów z funkcją hybrydowego przyłączania do usługi Azure AD](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się [, jak zarządzać tożsamościami urządzeń za pomocą Azure Portal](device-management-azure-portal.md).
