---
title: Włączanie dostępu zdalnego do programu SharePoint — serwer proxy aplikacji usługi Azure AD
description: Obejmuje podstawy dotyczące integrowania lokalnego serwera programu SharePoint z serwerem proxy aplikacji usługi Azure AD.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 983470994c103cb25d0d2aff96ae8544080e6288
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481300"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>Włączanie dostępu zdalnego do programu SharePoint przy użyciu serwera proxy aplikacji usługi Azure AD

W tym przewodniku krok po kroku wyjaśniono, jak zintegrować lokalną farmę programu SharePoint z serwerem proxy aplikacji usługi Azure Active Directory (Azure AD).

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania konfiguracji potrzebne są następujące zasoby:
- Farma programu SharePoint 2013 lub nowsza.
- Dzierżawa usługi Azure AD z planem obejmującym serwer proxy aplikacji. Dowiedz się więcej o [planach i cenach usługi Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).
- [Niestandardowa, zweryfikowana domena](../fundamentals/add-custom-domain.md) w dzierżawie usługi Azure AD.
- Lokalna usługa Active Directory zsynchronizowana z usługą Azure AD Connect, za pośrednictwem której użytkownicy mogą [logować się na platformie Azure.](../hybrid/plan-connect-user-signin.md)
- Łącznik serwera proxy aplikacji zainstalowany i uruchomiony na komputerze w domenie firmowej.

Konfigurowanie programu SharePoint za pomocą serwera proxy aplikacji wymaga dwóch adresów URL:
- Zewnętrzny adres URL, widoczny dla użytkowników końcowych i określony w usłudze Azure AD. Ten adres URL może używać domeny niestandardowej. Dowiedz się więcej o [pracy z domenami niestandardowymi w usłudze Azure AD Application Proxy](application-proxy-configure-custom-domain.md).
- Wewnętrzny adres URL, znany tylko w domenie firmowej i nigdy nie używany bezpośrednio.

> [!IMPORTANT]
> Aby upewnić się, że łącza są mapowane poprawnie, postępuj zgodnie z poniższymi zaleceniami dotyczącymi wewnętrznego adresu URL:
> - Użyj protokołu HTTPS.
> - Nie używaj portów niestandardowych.
> - W firmowym systemie nazw domen (DNS) utwórz hosta (A), aby wskazać usługę SharePoint WFE (lub moduł równoważenia obciążenia), a nie alias (CName).

W tym artykule użyto następujących wartości:
- Wewnętrzny adres URL:`https://sharepoint`
- Zewnętrzny adres URL:`https://spsites-demo1984.msappproxy.net/`
- Konto puli aplikacji dla aplikacji sieci Web programu SharePoint:`Contoso\spapppool`

## <a name="step-1-configure-an-application-in-azure-ad-that-uses-application-proxy"></a>Krok 1: Konfigurowanie aplikacji w usłudze Azure AD korzystającej z serwera proxy aplikacji

W tym kroku utworzysz aplikację w dzierżawie usługi Azure Active Directory, która używa serwera proxy aplikacji. Ustaw zewnętrzny adres URL i określ wewnętrzny adres URL, który jest używany później w programie SharePoint.

1. Utwórz aplikację zgodnie z poniższymi ustawieniami. Aby uzyskać instrukcje krok po kroku, zobacz [Publikowanie aplikacji przy użyciu serwera proxy aplikacji usługi Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).
   * **Wewnętrzny adres URL:** wewnętrzny adres URL programu SharePoint, `https://sharepoint`który zostanie ustawiony później w programie SharePoint, na przykład .
   * **Uwierzytelnianie wstępne:** Usługa Azure Active Directory
   * **Tłumaczenie adresów URL w nagłówkach**: Nie
   * **Tłumaczenie adresów URL w treści aplikacji**: Nie

   ![Publikowanie programu SharePoint jako aplikacji](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

1. Po opublikowaniu aplikacji wykonaj następujące czynności, aby skonfigurować ustawienia logowania jednokrotnego:

   1. Na stronie aplikacji w portalu wybierz pozycję **Logowanie jednokrotne**.
   1. W **trybie logowania jednokrotnego**wybierz pozycję **Zintegrowane uwierzytelnianie systemu Windows**.
   1. Ustaw **nazwę SPN aplikacji wewnętrznej** na wartość ustawioną wcześniej. W tym przykładzie `HTTP/sharepoint`wartość to .
   1. W obszarze **Delegowana tożsamość logowania**wybierz najbardziej odpowiednią opcję dla konfiguracji lasu usługi Active Directory. Jeśli na przykład w lesie jest jedna domena usługi Active Directory, wybierz **lokalną nazwę konta SAM** (jak pokazano na poniższym zrzucie ekranu). Jeśli jednak użytkownicy nie są w tej samej domenie co serwery programu SharePoint i łącznika serwera proxy aplikacji, wybierz pozycję **Lokalna nazwa użytkownika** (nie pokazano na zrzucie ekranu).

   ![Konfigurowanie zintegrowanego uwierzytelniania systemu Windows dla usługi SSO](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

1. Aby zakończyć konfigurowanie aplikacji, przejdź do sekcji **Użytkownicy i grupy** i przypisz użytkowników, aby uzyskać dostęp do tej aplikacji. 

## <a name="step-2-configure-the-sharepoint-web-application"></a>Krok 2: Konfigurowanie aplikacji sieci Web programu SharePoint

Aplikacja sieci Web programu SharePoint musi być skonfigurowana za pomocą protokołu Kerberos i odpowiednich mapowań dostępu alternatywnego, aby działały poprawnie z serwerem proxy aplikacji usługi Azure AD. Dostępne są dwie możliwe opcje:

- Utwórz nową aplikację sieci web i użyj tylko strefy domyślnej. Jest to preferowana opcja, ponieważ oferuje najlepsze środowisko z programem SharePoint (na przykład łącza w alertach e-mail generowanych przez program SharePoint zawsze wskazują strefę domyślną).
- Rozszerz istniejącą aplikację sieci web, aby skonfigurować protokół Kerberos w strefie nieaskładnikowej.

> [!IMPORTANT]
> Niezależnie od używanej strefy konto puli aplikacji sieci Web programu SharePoint musi być kontem domeny dla protokołu Kerberos, aby działało poprawnie.

### <a name="provision-the-sharepoint-web-application"></a>Aprowizuj aplikację sieci Web programu SharePoint

- Jeśli tworzysz nową aplikację sieci Web i używasz tylko strefy domyślnej (preferowana opcja):

    1. Uruchom **powłokę zarządzania programem SharePoint** i uruchom następujący skrypt:

       ```powershell
       # This script creates a web application and configures the Default zone with the internal/external URL needed to work with Azure AD Application Proxy
       # Edit variables below to fit your environment. Note that the managed account must exist and it must be a domain account
       $internalUrl = "https://sharepoint"
       $externalUrl = "https://spsites-demo1984.msappproxy.net/"
       $applicationPoolManagedAccount = "Contoso\spapppool"
            
       $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
       $wa = New-SPWebApplication -Name "SharePoint - AAD Proxy" -Port 443 -SecureSocketsLayer -URL $externalUrl -ApplicationPool "SharePoint - AAD Proxy" -ApplicationPoolAccount (Get-SPManagedAccount $applicationPoolManagedAccount) -AuthenticationProvider $winAp
       New-SPAlternateURL -Url $internalUrl -WebApplication $wa -Zone Default -Internal
       ```

    2. Otwórz witrynę **administracji centralnej programu SharePoint.**
    1. W obszarze **Ustawienia systemowe**wybierz pozycję **Konfiguruj mapowania dostępu alternatywnego**. Zostanie otwarte okno **Kolekcja mapowania dostępu alternatywnego.**
    1. Przefiltruj wyświetlacz za pomocą nowej aplikacji internetowej i potwierdź, że widzisz coś takiego:

       ![Mapowania dostępu alternatywnego aplikacji sieci web](./media/application-proxy-integrate-with-sharepoint-server/new-webapp-aam.png)

- Jeśli rozszerzysz istniejącą aplikację sieci web na nową strefę (w przypadku, gdy nie możesz użyć strefy domyślnej):

    1. Uruchom powłokę zarządzania programem SharePoint i uruchom następujący skrypt:

       ```powershell
       # This script extends an existing web application to Internet zone with the internal/external URL needed to work with Azure AD Application Proxy
       # Edit variables below to fit your environment
       $webAppUrl = "http://spsites/"
       $internalUrl = "https://sharepoint"
       $externalUrl = "https://spsites-demo1984.msappproxy.net/"
       
       $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
       $wa = Get-SPWebApplication $webAppUrl
       New-SPWebApplicationExtension -Name "SharePoint - AAD Proxy" -Identity $wa -SecureSocketsLayer -Zone Extranet -Url $externalUrl -AuthenticationProvider $winAp
       New-SPAlternateURL -Url $internalUrl -WebApplication $wa -Zone Extranet -Internal
       ```

    2. Otwórz witrynę **administracji centralnej programu SharePoint.**
    1. W obszarze **Ustawienia systemowe**wybierz pozycję **Konfiguruj mapowania dostępu alternatywnego**. Zostanie otwarte okno **Kolekcja mapowania dostępu alternatywnego.**
    1. Przefiltruj wyświetlacz za pomocą rozszerzonej aplikacji internetowej i potwierdź, że widzisz coś takiego:

        ![Mapowania dostępu alternatywnego aplikacji rozszerzonej](./media/application-proxy-integrate-with-sharepoint-server/extend-webapp-aam.png)

### <a name="make-sure-the-sharepoint-web-application-is-running-under-a-domain-account"></a>Upewnij się, że aplikacja sieci Web programu SharePoint działa na koncie domeny

Aby zidentyfikować konto z uruchomieniem puli aplikacji sieci Web programu SharePoint i upewnić się, że jest to konto domeny, wykonaj następujące kroki:

1. Otwórz witrynę **administracji centralnej programu SharePoint.**
1. Przejdź do pozycję **Zabezpieczenia** i wybierz pozycję **Konfiguruj konta usług**.
1. Wybierz **pulę aplikacji sieci Web — YourWebApplicationName**.

   ![Opcje konfigurowania konta usługi](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

1. Upewnij się, że **wybierz konto dla tego składnika** zwraca konto domeny i zapamiętaj je, ponieważ będzie potrzebne w następnym kroku.

### <a name="make-sure-that-an-https-certificate-is-configured-for-the-iis-site-of-the-extranet-zone"></a>Upewnij się, że certyfikat HTTPS jest skonfigurowany dla witryny IIS strefy Extranet

Ponieważ wewnętrzny adres URL używa`https://SharePoint/`protokołu HTTPS ( ), certyfikat musi być ustawiony w witrynie internetowych usług informacyjnych (IIS).

1. Otwórz konsolę programu Windows PowerShell.
1. Uruchom następujący skrypt, aby wygenerować certyfikat z podpisem własnym i dodać go do magazynu MY komputera:

   ```powershell
   # Replace "SharePoint" with the actual hostname of the Internal URL of your Azure AD proxy application
   New-SelfSignedCertificate -DnsName "SharePoint" -CertStoreLocation "cert:\LocalMachine\My"
   ```

   > [!IMPORTANT]
   > Certyfikaty z podpisem własnym nadają się tylko do celów testowych. W środowiskach produkcyjnych zdecydowanie zaleca się używanie certyfikatów wystawionych przez urząd certyfikacji.

1. Otwórz konsolę Menedżer internetowych usług informacyjnych.
1. Rozwiń serwer w widoku drzewa, rozwiń pozycję **Witryny**, wybierz witrynę **programu SharePoint — AAD Proxy** i wybierz pozycję **Powiązania**.
1. Wybierz **powiązanie https,** a następnie wybierz pozycję **Edytuj**.
1. W polu Certyfikat TLS/SSL wybierz pozycję Certyfikat **programu SharePoint,** a następnie wybierz przycisk **OK**.

Teraz można uzyskać dostęp do witryny programu SharePoint zewnętrznie za pośrednictwem serwera proxy aplikacji usługi Azure AD.

## <a name="step-3-configure-kerberos-constrained-delegation"></a>Krok 3: Konfigurowanie delegowania ograniczonego protokołu Kerberos

Użytkownicy będą początkowo uwierzytelniać się w usłudze Azure AD, a następnie do programu SharePoint przy użyciu protokołu Kerberos za pośrednictwem łącznika serwera proxy usługi Azure AD. Aby zezwolić łącznikowi na uzyskanie tokenu Protokołu Kerberos w imieniu użytkownika usługi Azure AD, należy skonfigurować delegowanie ograniczone kerberos (KCD) z przejściem protokołu. Aby dowiedzieć się więcej o KCD, zobacz [Omówienie ograniczonego delegowania protokołu Kerberos](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj553400(v=ws.11)).

### <a name="set-the-spn-for-the-sharepoint-service-account"></a>Ustawianie nazwy SPN dla konta usługi programu SharePoint

W tym artykule wewnętrzny `https://sharepoint`adres URL to , a więc `HTTP/sharepoint`nazwa główna usługi (SPN) jest . Należy zastąpić te wartości wartości, które odpowiadają środowiska.
Aby zarejestrować `HTTP/sharepoint` nazwę SPN dla `Contoso\spapppool`konta puli aplikacji programu SharePoint, uruchom następujące polecenie z wiersza polecenia jako administrator domeny:

`setspn -S HTTP/sharepoint Contoso\spapppool`

Polecenie `Setspn` wyszukuje nazwę SPN przed jej dołączeniem. Jeśli nazwa SPN już istnieje, zostanie wyświetlony błąd **zduplikowanej wartości nazwy SPN.** W takim przypadku należy rozważyć usunięcie istniejącej nazwy SPN, jeśli nie jest ustawiona w ramach konta puli poprawnej aplikacji. Można sprawdzić, czy nazwa SPN została `Setspn` pomyślnie dodana, uruchamiając polecenie z opcją -L. Aby dowiedzieć się więcej o tym poleceniu, zobacz [Setspn](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc731241(v=ws.11)).

### <a name="make-sure-the-connector-is-trusted-for-delegation-to-the-spn-that-was-added-to-the-sharepoint-application-pool-account"></a>Upewnij się, że łącznik jest zaufany dla delegowania do nazwy SPN, który został dodany do konta puli aplikacji programu SharePoint

Skonfiguruj kcd, aby usługa serwera proxy aplikacji usługi Azure AD mogła delegować tożsamości użytkowników do konta puli aplikacji programu SharePoint. Skonfiguruj KCD, włączając łącznik serwera proxy aplikacji do pobierania biletów protokołu Kerberos dla użytkowników, którzy zostali uwierzytelnieni w usłudze Azure AD. Następnie ten serwer przekazuje kontekst do aplikacji docelowej (SharePoint w tym przypadku).

Aby skonfigurować KCD, wykonaj następujące kroki dla każdego urządzenia łącznika:

1. Zaloguj się do kontrolera domeny jako administrator domeny, a następnie otwórz pozycję Użytkownicy i komputery usługi Active Directory.
1. Znajdź komputer z łącznikiem serwera proxy usługi Azure AD. W tym przykładzie jest to sam serwer programu SharePoint.
1. Kliknij dwukrotnie komputer, a następnie wybierz kartę **Delegowanie.**
1. Upewnij się, że opcje delegowania są ustawione **na Ufaj temu komputerowi tylko do delegowania do określonych usług**. Następnie wybierz pozycję **Użyj dowolnego protokołu uwierzytelniania**.
1. Wybierz przycisk **Dodaj,** wybierz pozycję **Użytkownicy lub Komputery**i znajdź konto puli aplikacji programu SharePoint. Na przykład: `Contoso\spapppool`.
1. Na liście SPN wybierz ten, który został utworzony wcześniej dla konta usługi.
1. Wybierz **przycisk OK,** a następnie ponownie wybierz przycisk **OK,** aby zapisać zmiany.
  
   ![Ustawienia delegowania](./media/application-proxy-integrate-with-sharepoint-server/delegation-box2.png)

Teraz możesz zalogować się do programu SharePoint przy użyciu zewnętrznego adresu URL i uwierzytelnić się za pomocą platformy Azure.

## <a name="troubleshoot-sign-in-errors"></a>Rozwiązywanie problemów z błędami logowania

Jeśli logowanie do witryny nie działa, można uzyskać więcej informacji na temat problemu w dziennikach łącznika: Na komputerze z uruchomionym łącznikiem otwórz przeglądarkę zdarzeń, przejdź do **aplikacji i dzienników** > usług**Microsoft** > **AadApplicationProxy** > **Connector**i sprawdź dziennik **administratora.**

## <a name="next-steps"></a>Następne kroki

* [Praca z domenami niestandardowymi w serwerze proxy aplikacji usługi Azure AD](application-proxy-configure-custom-domain.md)
* [Opis łączników serwera proxy aplikacji usługi Azure AD](application-proxy-connectors.md)
