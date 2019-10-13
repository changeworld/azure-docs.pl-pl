---
title: Włączanie dostępu zdalnego do programu SharePoint przy użyciu usługi Azure serwer proxy aplikacji usługi Azure AD | Microsoft Docs
description: Obejmuje podstawowe informacje na temat integrowania lokalnego programu SharePoint z usługą Azure serwer proxy aplikacji usługi Azure AD.
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
ms.openlocfilehash: a8f9cba1072866a3efdeb7b99981d0bfda22ab00
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286086"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>Włączanie dostępu zdalnego do programu SharePoint przy użyciu serwera proxy aplikacji usługi Azure AD

W tym przewodniku krok po kroku opisano integrację lokalnej farmy programu SharePoint z serwerem proxy aplikacji Azure Active Directory (Azure AD).

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było przeprowadzić konfigurację, potrzebne są następujące zasoby:
- Farma programu SharePoint 2013 lub nowsza.
- Dzierżawa Azure Active Directory z planem zawierającym serwer proxy aplikacji. Dowiedz się więcej o [planach Azure Active Directory i cenach](https://azure.microsoft.com/pricing/details/active-directory/).
- [Niestandardowa zweryfikowana domena](../fundamentals/add-custom-domain.md) w dzierżawie usługi Azure AD.
- Active Directory lokalne zsynchronizowane z Azure AD Connect z użytkownikami, którzy mogą [zalogować się do platformy Azure](../hybrid/plan-connect-user-signin.md).
- Łącznik serwera proxy aplikacji został zainstalowany i uruchomiony na komputerze w domenie firmowej.

Skonfigurowanie programu SharePoint z serwerem proxy aplikacji wymaga dwóch adresów URL:
- Zewnętrzny adres URL, widoczny dla użytkowników końcowych i określony w Azure Active Directory. Ten adres URL może korzystać z domeny niestandardowej. Dowiedz się więcej [na temat pracy z domenami niestandardowymi w usłudze Azure serwer proxy aplikacji usługi Azure AD](application-proxy-configure-custom-domain.md).
- Wewnętrzny adres URL, znany tylko w domenie firmowej i nigdy nie jest bezpośrednio używany.

> [!IMPORTANT]
> Aby upewnić się, że linki są mapowane prawidłowo, postępuj zgodnie z następującymi zaleceniami dotyczącymi wewnętrznego adresu URL:
> - Użyj protokołu HTTPS
> - Nie używaj portów niestandardowych
> - W firmowej usłudze DNS Utwórz hosta (A), aby wskazać WFE SharePoint (lub moduł równoważenia obciążenia), a nie alias (CName).

W tym artykule zostaną użyte następujące wartości:
- Wewnętrzny adres URL: `https://sharepoint`
- Zewnętrzny adres URL: `https://spsites-demo1984.msappproxy.net/`
- Konto puli aplikacji dla aplikacji sieci Web programu SharePoint: `Contoso\spapppool`

## <a name="step-1-configure-an-application-in-azure-ad-that-uses-application-proxy"></a>Krok 1. Konfigurowanie aplikacji w usłudze Azure AD, która używa serwera proxy aplikacji

W tym kroku utworzysz aplikację w dzierżawie Azure Active Directory, która będzie korzystać z serwera proxy aplikacji. Zostanie ustawiony zewnętrzny adres URL i zostanie określony wewnętrzny adres URL używany później w programie SharePoint.

1. Utwórz aplikację zgodnie z opisem w następujących ustawieniach. Aby uzyskać instrukcje krok po kroku, zobacz [publikowanie aplikacji przy użyciu usługi Azure serwer proxy aplikacji usługi Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).
   * **Wewnętrzny adres URL**: wewnętrzny adres URL programu SharePoint, który zostanie ustawiony w dalszej części programu SharePoint, na przykład `https://sharepoint`.
   * **Metoda wstępnego uwierzytelniania**: Azure Active Directory
   * **Tłumaczenie adresu URL w nagłówkach**: nie
   * **Tłumaczenie adresu URL w treści aplikacji**: nie

   ![Publikowanie programu SharePoint jako aplikacji](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

1. Po opublikowaniu aplikacji Skonfiguruj ustawienia logowania jednokrotnego, wykonując następujące czynności:

   1. Na stronie aplikacji w portalu wybierz pozycję **Logowanie jednokrotne**.
   1. W przypadku **trybu logowania**jednokrotnego wybierz pozycję **zintegrowane uwierzytelnianie systemu Windows**.
   1. Ustaw **nazwę SPN aplikacji wewnętrznej** na ustawioną wcześniej wartość. Na potrzeby tego przykładu wartość będzie `HTTP/sharepoint`.
   1. W obszarze **delegowana tożsamość logowania**wybierz opcję najbardziej odpowiednią dla konfiguracji lasu Active Directory. Jeśli na przykład masz pojedynczą domenę usługi AD w lesie, wybierz pozycję **lokalna nazwa konta sam** (jak pokazano poniżej), ale jeśli użytkownicy nie znajdują się w tej samej domenie co program SharePoint i serwery łącznika serwera proxy aplikacji, wybierz pozycję **lokalna nazwa użytkownika Nazwa** (niepokazywana).

   ![Konfigurowanie zintegrowanego uwierzytelniania systemu Windows na potrzeby logowania jednokrotnego](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

1. Aby zakończyć konfigurowanie aplikacji, przejdź do sekcji **Użytkownicy i grupy** i przypisz użytkownikom dostęp do tej aplikacji. 

## <a name="step-2-configure-the-sharepoint-web-application"></a>Krok 2. Konfigurowanie aplikacji sieci Web programu SharePoint

Aplikacja sieci Web programu SharePoint musi być skonfigurowana przy użyciu protokołu Kerberos, a odpowiednie mapowania dostępu alternatywnego mogą działać poprawnie z usługą Azure serwer proxy aplikacji usługi Azure AD. Dostępne są dwie możliwe opcje:

1. Utwórz nową aplikację sieci Web i Użyj tylko strefy domyślnej. Jest to preferowana opcja, aby zapewnić najlepsze środowisko pracy z programem SharePoint (na przykład linki w alertach e-mail generowanych przez program SharePoint zawsze wskazują strefę domyślną).
1. Rozwiń istniejącą aplikację sieci Web, aby skonfigurować protokół Kerberos w strefie innej niż domyślna.

> [!IMPORTANT]
> Niezależnie od strefy, która będzie używana, konto puli aplikacji w aplikacji sieci Web programu SharePoint musi być kontem domeny, aby protokół Kerberos działał poprawnie.

### <a name="provision-the-sharepoint-web-application"></a>Inicjowanie obsługi administracyjnej aplikacji sieci Web programu SharePoint

- Jeśli tworzysz nową aplikację sieci Web i używasz tylko strefy domyślnej (preferowana opcja):

    1. Uruchom **powłokę zarządzania programu SharePoint** i uruchom następujący skrypt:

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

    1. Otwórz witrynę **administracji centralnej programu SharePoint** .
    1. W obszarze **Ustawienia systemu**wybierz pozycję **Konfiguruj alternatywne mapowania dostępu**. Zostanie otwarte okno mapowania dostępu alternatywnego.
    1. Przefiltruj wyświetlanie przy użyciu nowej aplikacji sieci Web i Potwierdź, że zobaczysz coś podobnego do tego:

       ![Alternatywne mapowania dostępu aplikacji sieci Web](./media/application-proxy-integrate-with-sharepoint-server/new-webapp-aam.png)

- Jeśli w nowej strefie zostanie rozszerzona istniejąca aplikacja sieci Web (w przypadku, gdy nie można użyć strefy domyślnej):

    1. Uruchom **powłokę zarządzania programu SharePoint** i uruchom następujący skrypt:

       ```powershell
       # This scripts extends an existing web application to Internet zone with the internal/external URL needed to work with Azure AD Application Proxy
       # Edit variables below to fit your environment
       $webAppUrl = "http://spsites/"
       $internalUrl = "https://sharepoint"
       $externalUrl = "https://spsites-demo1984.msappproxy.net/"
       
       $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
       $wa = Get-SPWebApplication $webAppUrl
       New-SPWebApplicationExtension -Name "SharePoint - AAD Proxy" -Identity $wa -SecureSocketsLayer -Zone Extranet -Url $externalUrl -AuthenticationProvider $winAp
       New-SPAlternateURL -Url $internalUrl -WebApplication $wa -Zone Extranet -Internal
       ```

    1. Otwórz witrynę **administracji centralnej programu SharePoint** .
    1. W obszarze **Ustawienia systemu**wybierz pozycję **Konfiguruj alternatywne mapowania dostępu**. Zostanie otwarte okno mapowania dostępu alternatywnego.
    1. Przefiltruj Wyświetlanie z aplikacją sieci Web, która została rozszerzona, i Potwierdź, że zobaczysz coś w następujący sposób:

        ![Alternatywne mapowania dostępu rozszerzonej aplikacji](./media/application-proxy-integrate-with-sharepoint-server/extend-webapp-aam.png)

### <a name="ensure-that-the-sharepoint-web-application-is-running-under-a-domain-account"></a>Upewnij się, że aplikacja sieci Web programu SharePoint jest uruchomiona na koncie domeny

Wykonaj następujące kroki, aby zidentyfikować konto z uruchomioną pulą aplikacji aplikacji sieci Web programu SharePoint i upewnić się, że jest to konto domeny:

1. Otwórz witrynę **administracji centralnej programu SharePoint** .
1. Przejdź do pozycji **zabezpieczenia** i wybierz pozycję **Konfiguruj konta usług**.
1. Wybierz pozycję **Pula aplikacji sieci Web — YourWebApplicationName**.

   ![Opcje konfigurowania konta usługi](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

1. Upewnij się, że **Wybierz konto dla tego składnika** zwraca konto domeny i Zapamiętaj, że będzie ono niezbędne w następnym kroku.

### <a name="ensure-that-an-https-certificate-is-configured-for-the-iis-site-of-the-extranet-zone"></a>Upewnij się, że certyfikat HTTPS jest skonfigurowany dla witryny usług IIS strefy ekstranetu

Ponieważ wewnętrzny adres URL używa protokołu HTTPS (`https://SharePoint/`), należy ustawić certyfikat w witrynie usług IIS.

1. Otwórz konsolę programu Windows PowerShell.
1. Uruchom następujący skrypt, aby wygenerować certyfikat z podpisem własnym i dodać go do komputera mój magazyn:

   ```powershell
   # Replace "SharePoint" with the actual hostname of the Internal URL of your Azure AD proxy application
   New-SelfSignedCertificate -DnsName "SharePoint" -CertStoreLocation "cert:\LocalMachine\My"
   ```

   > [!IMPORTANT]
   > Certyfikaty z podpisem własnym są odpowiednie tylko do celów testowych. W środowiskach produkcyjnych zdecydowanie zaleca się używanie certyfikatów wystawionych przez urząd certyfikacji.

1. Otwórz konsolę "Internet Information Services Manager".
1. Rozwiń serwer w widoku drzewa, rozwiń węzeł "Lokacje", wybierz lokację "SharePoint-AAD proxy" i kliknij pozycję **powiązania**.
1. Wybierz pozycję powiązanie https, a następnie kliknij pozycję **Edytuj.** ...
1. W polu certyfikat protokołu SSL wybierz pozycję certyfikat **programu SharePoint** , a następnie kliknij przycisk OK.

Teraz możesz uzyskiwać dostęp do witryny programu SharePoint zewnętrznie za pośrednictwem usługi Azure serwer proxy aplikacji usługi Azure AD.

## <a name="step-3-configure-kerberos-constrained-delegation-kcd"></a>Krok 3. Konfigurowanie ograniczonego delegowania protokołu Kerberos (KCD)

Użytkownicy będą początkowo uwierzytelniani w Azure Active Directory, a następnie do programu SharePoint przy użyciu protokołu Kerberos za pośrednictwem łącznika serwera proxy usługi Azure AD. Aby umożliwić łącznikowi uzyskanie tokenu Kerberos w imieniu użytkownika Azure Active Directory, wymagane jest skonfigurowanie ograniczonego delegowania protokołu Kerberos przy użyciu przejścia protokołu. Aby dowiedzieć się więcej na temat KCD, zobacz [Omówienie ograniczonego delegowania protokołu Kerberos](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj553400(v=ws.11)).

### <a name="set-the-service-principal-name-for-the-sharepoint-service-account"></a>Ustawianie nazwy głównej usługi dla konta usługi programu SharePoint

W tym artykule wewnętrzny adres URL to `https://sharepoint`, więc nazwa SPN to `HTTP/sharepoint`. Należy zamienić te wartości na odpowiednie dla danego środowiska.
Aby zarejestrować nazwę SPN `HTTP/sharepoint` dla konta puli aplikacji programu SharePoint `Contoso\spapppool`, uruchom następujące polecenie w wierszu polecenia jako administrator domeny:

`setspn -S HTTP/sharepoint Contoso\spapppool`

Polecenie Setspn wyszukuje nazwę SPN przed dodaniem jej. Jeśli już istnieje, zostanie wyświetlony **zduplikowany błąd wartości SPN** . W takim przypadku należy rozważyć usunięcie istniejącej nazwy SPN, jeśli nie jest ona ustawiona na poprawnym koncie puli aplikacji.  
Można sprawdzić, czy nazwa SPN została dodana pomyślnie, uruchamiając polecenie Setspn z opcją-L. Aby dowiedzieć się więcej na temat tego polecenia, zobacz [Setspn](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc731241(v=ws.11)).

### <a name="ensure-that-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-sharepoint-application-pool-account"></a>Upewnij się, że łącznik jest zaufany do delegowania do nazwy SPN dodanej do konta puli aplikacji SharePoint

Skonfiguruj KCD tak, aby usługa Azure serwer proxy aplikacji usługi Azure AD mogła delegować tożsamości użytkowników do konta puli aplikacji programu SharePoint. Skonfiguruj KCD przez włączenie łącznika serwera proxy aplikacji w celu pobrania biletów Kerberos dla użytkowników uwierzytelnionych w usłudze Azure AD. Następnie serwer przekazuje kontekst do aplikacji docelowej lub programu SharePoint w tym przypadku.

Aby skonfigurować KCD, powtórz następujące kroki dla każdej maszyny łącznika:

1. Zaloguj się do kontrolera domeny jako administrator domeny, a następnie otwórz **Active Directory Użytkownicy i komputery**.
1. Znajdź komputer z uruchomionym łącznikiem usługi Azure AD proxy. W tym przykładzie jest to serwer programu SharePoint.
1. Kliknij dwukrotnie komputer, a następnie kliknij kartę **delegowanie** .
1. Upewnij się, że ustawienia delegowania mają ustawioną wartość **Ufaj temu komputerowi w delegowaniu tylko do określonych usług**. Następnie wybierz opcję **Użyj dowolnego protokołu uwierzytelniania**.
1. Kliknij przycisk **Dodaj** , kliknij pozycję **Użytkownicy lub komputery**, a następnie Znajdź konto puli aplikacji SharePoint, na przykład `Contoso\spapppool`.
1. Z listy nazw SPN wybierz ten, który został utworzony wcześniej dla konta usługi.
1. Kliknij przycisk **OK**. Kliknij ponownie przycisk **OK** , aby zapisać zmiany.
  
   ![Ustawienia delegowania](./media/application-proxy-integrate-with-sharepoint-server/delegation-box2.png)

Teraz można przystąpić do logowania się do programu SharePoint przy użyciu zewnętrznego adresu URL i uwierzytelniania za pomocą platformy Azure.

## <a name="troubleshoot-sign-in-errors"></a>Rozwiązywanie problemów z błędami logowania

Jeśli logowanie do lokacji nie działa, można uzyskać więcej informacji o problemie w dziennikach łączników: na maszynie z uruchomionym łącznikiem Otwórz Podgląd zdarzeń, przejdź do pozycji **Dzienniki aplikacji i usług** > **Microsoft** >  **AadApplicationProxy**@no__t 5**łącznika**i sprawdź dziennik **administratora** .

## <a name="next-steps"></a>Następne kroki

* [Praca z domenami niestandardowymi w usłudze Azure serwer proxy aplikacji usługi Azure AD](application-proxy-configure-custom-domain.md)
* [Omówienie łączników serwer proxy aplikacji usługi Azure AD platformy Azure](application-proxy-connectors.md)
