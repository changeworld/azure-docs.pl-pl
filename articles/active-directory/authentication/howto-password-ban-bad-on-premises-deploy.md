---
title: Wdrażanie usługi Azure AD hasła ochrona — Azure Active Directory
description: Wdrażanie ochrony hasłem usługi Azure AD, aby zablokować błędnego hasła lokalnie
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8487f82b123b42f9d6a6f0fbd6d6cbb240bf9fdc
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67785526"
---
# <a name="deploy-azure-ad-password-protection"></a>Wdrażanie ochrony haseł w usłudze Azure AD

Teraz, gdy już rozumiesz [jak wymusić ochrona za pomocą hasła usługi Azure AD dla usługi Active Directory systemu Windows Server](concept-password-ban-bad-on-premises.md), następnym krokiem jest planowania i wykonywania wdrożenia.

## <a name="deployment-strategy"></a>Strategia wdrażania

Zaleca się rozpocząć wdrożenia w trybie inspekcji. Tryb inspekcji jest początkowa ustawienie domyślne, których hasła mogą w dalszym można ustawić. Hasła, które zostałby zablokowany są rejestrowane w dzienniku zdarzeń. Po wdrożeniu serwery proxy i agentów kontrolera domeny w trybie inspekcji, należy monitorować wpływ zasad haseł będzie mieć na użytkowników i środowiska, gdy zasady są wymuszane.

Na etapie inspekcji w wielu organizacjach dowiedzieć się który:

* Należy poprawić istniejące procesy operacyjne z bardziej bezpiecznych haseł.
* Użytkownicy często używają niezabezpieczonych hasła.
* Muszą oni informować użytkowników o nadchodzących zmianę wymuszania zabezpieczeń, może mieć wpływ na ich oraz sposobu wybierania bardziej bezpiecznych haseł.

Po ta funkcja działa w trybie inspekcji odpowiednim czasie, można przełączać konfigurację z *inspekcji* do *Wymuś* wymagające wprowadzenia hasła bardziej bezpieczne. Dobrym pomysłem jest monitorowanie wąsko zdefiniowany w tym czasie.

## <a name="deployment-requirements"></a>Wymagania dotyczące wdrażania

* Wymagania dotyczące licencjonowania dla ochrona za pomocą hasła usługi Azure AD można znaleźć w artykule [eliminowanie nieprawidłowych haseł w organizacji](concept-password-ban-bad.md#license-requirements).
* Wszystkie kontrolery domeny, które Agent kontrolera domeny usługi, aby zainstalować ochrona za pomocą hasła usługi Azure AD musi działać system Windows Server 2012 lub nowszym. To wymaganie nie oznacza, że domeny usługi Active Directory lub lasu również musi występować w systemie Windows Server 2012 domeny lub lasu poziom funkcjonalności. Jak wspomniano w [zasady projektowania](concept-password-ban-bad-on-premises.md#design-principles), nie ma żadnych minimalna DFL ani FFL wymagane dla dowolnego kontrolera domeny agenta lub serwera proxy oprogramowania do uruchomienia.
* Wszystkie maszyny, które zainstalowana Usługa agenta kontrolera domeny musi mieć zainstalowane w .NET 4.5.
* Wszystkie maszyny, które serwer proxy usługi, aby zainstalować ochrona za pomocą hasła usługi Azure AD musi działać system Windows Server 2012 R2 lub nowszym.
   > [!NOTE]
   > Wdrożenie usługi serwera proxy jest wymagane do wdrażania ochrona za pomocą hasła usługi Azure AD, nawet jeśli kontroler domeny może mieć wychodzących bezpośrednie połączenie z Internetem. 
   >
* Wszystkie komputery z zainstalowaną usługę serwera Proxy ochrony haseł usługi Azure AD musi mieć zainstalowane programu .NET 4.7.
  .NET 4.7 powinno być już zainstalowane na serwerze systemu Windows w pełni zaktualizowany. Jeśli nie jest tak, Pobierz i uruchom Instalator znalazł w [Instalator w trybie offline programu .NET Framework 4.7 dla Windows](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows).
* Wszystkie maszyny, tym przez kontrolery domen, które zawierają zainstalowania składników ochrony haseł usługi Azure AD musi mieć zainstalowane uniwersalnego środowiska uruchomieniowego C. Środowisko uruchomieniowe można uzyskać, upewniając się, że masz wszystkie aktualizacje z witryny Windows Update. Lub możesz pobrać go w pakiecie aktualizacji specyficznych dla systemu operacyjnego. Aby uzyskać więcej informacji, zobacz [aktualizacji dla uniwersalnego środowiska uruchomieniowego c. w Windows](https://support.microsoft.com/help/2999226/update-for-uniersal-c-runtime-in-windows).
* Sieci musi istnieć łączność między co najmniej jeden kontroler domeny w każdej domenie i co najmniej jeden serwer hostujący usługę serwera proxy ochrony hasłem. To połączenie musi zezwalać na kontrolerze domeny, aby uzyskiwać dostęp do punktu końcowego usługi RPC, mapowania portu 135 protokołów i port serwera RPC na usługę serwera proxy. Domyślnie przez port serwera RPC jest dynamicznego portu RPC, ale mogą być konfigurowane do [korzystanie z portu statycznego](#static).
* Wszystkie komputery obsługujące usługę serwera proxy musi mieć dostęp do następujących punktów końcowych sieci:

    |**Punkt końcowy**|**Cel**|
    | --- | --- |
    |`https://login.microsoftonline.com`|Żądania uwierzytelniania|
    |`https://enterpriseregistration.windows.net`|Funkcjonalność ochrony haseł w usłudze Azure AD|

* Wszystkie komputery obsługujące usługę serwera proxy dla ochrony hasłem muszą zostać skonfigurowane tak, aby zezwolić na ruch wychodzący protokołu TLS 1.2 HTTP.
* Konto administratora globalnego, aby zarejestrować usługę serwera proxy dla ochrony hasłem i lasu za pomocą usługi Azure AD.
* Konto, które ma uprawnienia administratora domeny usługi Active Directory w domenie głównej lasu, aby zarejestrować lasu usługi Active Directory systemu Windows Server z usługą Azure AD.
* Dowolnej domeny usługi Active Directory, która działa oprogramowanie service agenta kontrolera domeny, należy użyć rozproszonego replikacji systemu plików (DFSR) do replikacji folderu sysvol.
* Usługi dystrybucji kluczy musi być włączona na wszystkich kontrolerach domeny w domenie z systemem Windows Server 2012. Domyślnie ta usługa jest włączone za pomocą wyzwalacza ręcznego rozpoczęcia.

## <a name="single-forest-deployment"></a>Wdrożenie pojedynczego lasu

Na poniższym diagramie przedstawiono, jak podstawowe składniki ochrona za pomocą hasła usługi Azure AD współpracują ze sobą w środowisku usługi Active Directory w środowisku lokalnym.

![W jaki sposób składniki ochrony hasła usługi Azure AD współpracują ze sobą](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

To dobry pomysł, aby przejrzeć działania oprogramowania przed ich wdrożeniem. Zobacz [ochrona za pomocą hasła usługi Azure AD z koncepcyjnym omówieniem](concept-password-ban-bad-on-premises.md).

### <a name="download-the-software"></a>Pobierz oprogramowanie

Istnieją dwa wymagane pliki instalacyjne dla programów ochrona za pomocą hasła usługi Azure AD. Są one dostępne z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57071).

### <a name="install-and-configure-the-proxy-service-for-password-protection"></a>Instalowanie i konfigurowanie usługi Serwer proxy ochrony hasłem

1. Wybierz co najmniej jeden serwer do obsługi usługi serwera proxy ochrony hasłem.
   * Każdej z tych usług można zapewniają tylko zasady dotyczące haseł dla pojedynczego lasu. Komputer hosta muszą być przyłączone do domeny w lesie. Domeny katalogu głównego i podrzędnego są obsługiwane. Konieczne jest połączenie sieciowe między co najmniej jeden kontroler domeny w każdej domenie w lesie i na komputerze ochrony haseł.
   * Na kontrolerze domeny, które testy można uruchomić usługę serwera proxy. Jednak ten kontroler domeny następnie wymaga łączności z Internetem, który może być kwestią zabezpieczeń. Firma Microsoft zaleca tej konfiguracji tylko do celów testowych.
   * Zalecamy co najmniej dwóch serwerów proxy dla zapewnienia nadmiarowości. Zobacz [wysokiej dostępności](howto-password-ban-bad-on-premises-deploy.md#high-availability).

1. Instalowanie programu Azure AD hasło ochrony z serwera Proxy usługi przy użyciu `AzureADPasswordProtectionProxySetup.exe` Instalatora oprogramowania.
   * Instalacja oprogramowania nie jest wymagane ponowne uruchomienie komputera. Instalacja oprogramowania mogą zostać zautomatyzowane przy użyciu standardowych procedur MSI, na przykład:

      `AzureADPasswordProtectionProxySetup.exe /quiet`

      > [!NOTE]
      > Usługa Zapora Windows musi działać przed ponownym zainstalowaniem pakietu AzureADPasswordProtectionProxySetup.msi, aby uniknąć błąd instalacji. Jeśli Zapora Windows jest skonfigurowany do uruchamiania nie, obejście polega na tymczasowo włączyć i uruchomiona usługa Zapora podczas instalacji. Oprogramowanie serwera proxy nie jest określone zależny w Zaporze Windows po instalacji. Jeśli używasz zapory innych firm, nadal musi być skonfigurowany w celu spełnienia wymagań związanych z wdrażaniem. Obejmują one, zezwalając na dostęp dla ruchu przychodzącego do portu 135 i serwera proxy, port serwera RPC. Zobacz [wymagania w zakresie wdrażania](howto-password-ban-bad-on-premises-deploy.md#deployment-requirements).

1. Otwórz okno programu PowerShell jako administrator.
   * Oprogramowanie serwera proxy ochrony haseł obejmuje nowy moduł programu PowerShell, *AzureADPasswordProtection*. Poniższe kroki uruchamiania różnych poleceń cmdlet z tego modułu programu PowerShell. Zaimportuj nowy moduł w następujący sposób:

      ```powershell
      Import-Module AzureADPasswordProtection
      ```

   * Aby sprawdzić, czy usługa jest uruchomiona, użyj następującego polecenia programu PowerShell:

      `Get-Service AzureADPasswordProtectionProxy | fl`.

     Wynik powinien być wyświetlony **stan** z "Działa".

1. Zarejestruj serwer proxy.
   * Po wykonaniu kroku 3, usługa serwera proxy jest uruchomiona na maszynie. Jednak usługa nie ma jeszcze poświadczenia niezbędne do komunikacji z usługą Azure AD. Wymagana jest rejestracja w usłudze Azure AD:

     `Register-AzureADPasswordProtectionProxy`

     To polecenie cmdlet wymaga poświadczeń administratora globalnego dla dzierżawy usługi Azure. Należy również lokalne uprawnienia administratora domeny usługi Active Directory w domenie głównej lasu. Po to polecenie powiedzie się jeden raz dla usługi serwera proxy, dodatkowe wywołania jego zakończy się powodzeniem, ale nie są konieczne.

      `Register-AzureADPasswordProtectionProxy` Polecenie cmdlet obsługuje następujące tryby uwierzytelniania trzy.

     * Tryb interaktywny uwierzytelniania:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Ten tryb nie działa w systemach operacyjnych Server Core. Zamiast tego należy użyć jednej z następujących trybów uwierzytelniania. Ponadto ten tryb może zakończyć się niepowodzeniem, jeśli konfiguracja zwiększonych zabezpieczeń programu Internet Explorer jest włączona. Obejście polega na wyłączyć tę konfigurację, Zarejestruj serwer proxy, a następnie włączyć ją ponownie.

     * Tryb uwierzytelniania kodu urządzenia:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
        ```

        Możesz następnie ukończenia uwierzytelniania, wykonując instrukcje wyświetlone na innym urządzeniu.

     * Tryb cichy uwierzytelniania (opartego na hasłach):

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionProxy -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > W tym trybie kończy się niepowodzeniem, jeśli wymagana jest usługa Azure Multi-Factor Authentication. W takim przypadku użyj jednego z poprzednich Tryby uwierzytelniania dwa.

       Obecnie nie trzeba określać *- ForestCredential* parametr, który jest zarezerwowane dla przyszłych funkcji.

   Rejestracja usługi serwera proxy w celu ochrony hasłem jest niezbędne tylko raz w okresie istnienia usługi. Po tym usługa serwera proxy automatycznie wykonać wszelkie niezbędne konserwacji.

   > [!TIP]
   > Może to być zauważalnego opóźnienia przed ukończeniem, to polecenie cmdlet jest uruchamiane dla określonej dzierżawy platformy Azure po raz pierwszy. Chyba że błąd jest zgłaszany, nie martw się o to opóźnienie.

1. Zarejestruj lasu.
   * Należy zainicjować lasu usługi Active Directory w środowisku lokalnym, niezbędne poświadczenia do komunikowania się z platformą Azure za pomocą `Register-AzureADPasswordProtectionForest` polecenia cmdlet programu PowerShell. Polecenie cmdlet wymaga poświadczeń administratora globalnego dla dzierżawy usługi Azure. Wymaga uprawnień administratora przedsiębiorstwa katalogu Active w środowisku lokalnym. Ten krok zostanie uruchomiony jeden raz w każdym lesie.

      `Register-AzureADPasswordProtectionForest` Polecenie cmdlet obsługuje następujące tryby uwierzytelniania trzy.

     * Tryb interaktywny uwierzytelniania:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > W tym trybie nie będzie działać w systemach operacyjnych Server Core. Zamiast tego użyj jednej z następujących trybów uwierzytelniania dwa. Ponadto ten tryb może zakończyć się niepowodzeniem, jeśli konfiguracja zwiększonych zabezpieczeń programu Internet Explorer jest włączona. Obejście polega na wyłączyć tę konfigurację, Zarejestruj serwer proxy, a następnie włączyć ją ponownie.  

     * Tryb uwierzytelniania kodu urządzenia:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
        ```

        Możesz następnie ukończenia uwierzytelniania, wykonując instrukcje wyświetlone na innym urządzeniu.

     * Tryb cichy uwierzytelniania (opartego na hasłach):

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > W tym trybie kończy się niepowodzeniem, jeśli wymagana jest usługa Azure Multi-Factor Authentication. W takim przypadku użyj jednego z poprzednich Tryby uwierzytelniania dwa.

       Te przykłady powiedzie się tylko, jeśli aktualnie zalogowanego użytkownika jest również administratorem domeny usługi Active Directory dla domeny katalogu głównego. Jeśli to nie jest tak, możesz podać poświadczenia alternatywne domeny za pośrednictwem *- ForestCredential* parametru.

   > [!NOTE]
   > Jeśli wielu serwerów proxy są zainstalowane w danym środowisku, nie ma znaczenia serwera proxy, który służy do rejestrowania w lesie.
   >
   > [!TIP]
   > Może to być zauważalnego opóźnienia przed ukończeniem, to polecenie cmdlet jest uruchamiane dla określonej dzierżawy platformy Azure po raz pierwszy. Chyba że błąd jest zgłaszany, nie martw się o to opóźnienie.

   Rejestracja lasu usługi Active Directory jest niezbędne tylko raz w okresie istnienia w lesie. Po tym agentów kontrolera domeny w lesie automatycznie będzie wykonywać inne potrzeby konserwacji. Po `Register-AzureADPasswordProtectionForest` działa pomyślnie dla lasu, dodatkowe wywołania polecenia cmdlet wykonane pomyślnie, ale nie są konieczne.

   Aby uzyskać `Register-AzureADPasswordProtectionForest` zakończyło się sukcesem, co najmniej jeden kontroler domeny systemem Windows Server 2012 lub nowszy musi się znajdować w domenie serwera proxy. Jednak oprogramowanie agenta kontrolera domeny nie musi być zainstalowany we wszystkich kontrolerach domeny, przed wykonaniem tego kroku.

1. Konfigurowanie usługi Serwer proxy ochrony hasłem komunikować się za pośrednictwem serwera proxy HTTP.

   Jeśli dane środowisko wymaga użycia określonego serwera proxy HTTP do komunikowania się z platformą Azure, należy użyć tej metody: Tworzenie *AzureADPasswordProtectionProxy.exe.config* pliku w folderze %ProgramFiles%\Azure AD Proxy\Service ochrony haseł. Obejmują następującej zawartości:

      ```xml
      <configuration>
        <system.net>
          <defaultProxy enabled="true">
           <proxy bypassonlocal="true"
               proxyaddress="http://yourhttpproxy.com:8080" />
          </defaultProxy>
        </system.net>
      </configuration>
      ```

   Jeśli Twój serwer proxy HTTP wymaga uwierzytelniania, należy dodać *useDefaultCredentials* tag:

      ```xml
      <configuration>
        <system.net>
          <defaultProxy enabled="true" useDefaultCredentials="true">
           <proxy bypassonlocal="true"
               proxyaddress="http://yourhttpproxy.com:8080" />
          </defaultProxy>
        </system.net>
      </configuration>
      ```

   W obu przypadkach należy zastąpić `http://yourhttpproxy.com:8080` adres i port serwera proxy HTTP określone.

   Jeśli Twój serwer proxy HTTP jest skonfigurowany przy użyciu zasad autoryzacji, należy udzielić dostępu do konta komputera usługi Active Directory, komputera, który obsługuje usługę serwera proxy ochrony hasłem.

   Zaleca się zatrzymać i ponownie uruchomić usługę serwera proxy, po Tworzenie lub aktualizowanie *AzureADPasswordProtectionProxy.exe.config* pliku.

   Usługa serwera proxy nie obsługuje określonych poświadczeń do łączenia się z serwera proxy HTTP.

1. Opcjonalnie: Konfigurowanie usługi Serwer proxy ochrony hasłem do nasłuchiwania na konkretnym porcie.
   * Oprogramowanie agenta kontrolera domeny, do ochrony haseł na kontrolerach domeny używa RPC za pośrednictwem protokołu TCP do komunikowania się z usługą serwera proxy. Domyślnie usługa serwera proxy nasłuchuje na wszystkie dostępne dynamiczne końcowych wywołań RPC. Można jednak skonfigurować usługi do nasłuchiwania na konkretnym porcie TCP, jeśli jest to konieczne ze względu na topologii sieci i wymagania dotyczące zapory w danym środowisku.
      * <a id="static" /></a>Aby skonfigurować usługę, aby działała z portu statycznego, należy użyć `Set-AzureADPasswordProtectionProxyConfiguration` polecenia cmdlet.

         ```powershell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
         ```

         > [!WARNING]
         > Należy zatrzymać i ponownie uruchomić usługę, aby zmiany zaczęły obowiązywać.

      * Aby skonfigurować usługę, aby była uruchamiana z portów dynamicznych, użyj tej samej procedury, ale ustawiona *StaticPort* powrotem do zera:

         ```powershell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
         ```

         > [!WARNING]
         > Należy zatrzymać i ponownie uruchomić usługę, aby zmiany zaczęły obowiązywać.

   > [!NOTE]
   > Usługa serwera proxy ochrony hasłem wymaga ręcznego ponownego uruchomienia po każdej zmianie konfiguracji portu. Ale nie trzeba ponownie uruchomić usługę oprogramowani kontrolera domeny na kontrolerach domeny, po wprowadzeniu tych zmian konfiguracji.

   * Aby wysłać zapytanie w bieżącej konfiguracji usługi, użyj `Get-AzureADPasswordProtectionProxyConfiguration` polecenia cmdlet:

      ```powershell
      Get-AzureADPasswordProtectionProxyConfiguration | fl

      ServiceName : AzureADPasswordProtectionProxy
      DisplayName : Azure AD password protection Proxy
      StaticPort  : 0
      ```

### <a name="install-the-dc-agent-service"></a>Instalacja usługi agenta kontrolera domeny

   Zainstaluj usługę agenta kontrolera domeny do hasła ochrony za pomocą `AzureADPasswordProtectionDCAgentSetup.msi` pakietu.

   Instalacja oprogramowania lub dezinstalacji, wymaga ponownego uruchomienia. Jest to spowodowane filtru haseł biblioteki DLL są tylko załadowane lub zwolnione przez ponowne uruchomienie komputera.

   Usługa agenta kontrolera domeny można zainstalować na komputerze, który nie jest jeszcze kontrolera domeny. W tym przypadku usługa będzie uruchomić i uruchamiania, ale pozostawać aktywne, dopóki komputer zostanie podwyższony do kontrolera domeny.

   Instalacja oprogramowania można zautomatyzować przy użyciu standardowych procedur MSI. Na przykład:

   `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn`

   > [!WARNING]
   > W tym miejscu polecenia msiexec przykład powoduje natychmiastowego ponownego uruchomienia. Aby tego uniknąć, należy użyć `/norestart` flagi.

Instalacja jest zakończona, po oprogramowanie agenta kontrolera domeny jest zainstalowany na kontrolerze domeny, a ten komputer jest uruchamiany ponownie. Konfiguracja nie jest wymagane, czy możliwa.

## <a name="multiple-forest-deployments"></a>Wielu wdrożeń będących częścią lasu

Nie ma żadnych dodatkowych wymagań, aby wdrożyć ochrona za pomocą hasła usługi Azure AD w wielu lasach. Każdy las jest skonfigurowany niezależnie, zgodnie z opisem w sekcji "obejmujących jeden las deployment". Każdy serwer proxy ochrony haseł może obsługiwać tylko kontrolery domeny z lasu, który jest dołączony do. Oprogramowanie ochrony haseł w dowolnym lesie nie rozpoznaje oprogramowanie do ochrony przed hasło, które zostało wdrożone w innych lasach, niezależnie od konfiguracji relacji zaufania usługi Active Directory.

## <a name="read-only-domain-controllers"></a>Kontrolery domeny tylko do odczytu

Hasło zmiany/zestawy są przetwarzane i nie utrwalony na kontrolerach domeny tylko do odczytu (RODC). Są one przekazywane do kontrolerów domeny z możliwością zapisu. Dlatego nie trzeba zainstalować oprogramowanie agenta kontrolera domeny na kontrolery RODC.

## <a name="high-availability"></a>Wysoka dostępność

Kwestią dostępność głównego ochrony hasłem jest dostępność serwerów proxy, gdy kontrolery domeny w lesie próbuje pobrać nowe zasady lub inne dane z platformy Azure. Każdy Agent kontrolera domeny używa prosty algorytm działanie okrężne stylu przy podejmowaniu decyzji, którego serwera proxy do wywołania. Agent pomija serwerów proxy, które nie odpowiadają. Dla najbardziej pełni połączone wdrożeniach usługi Active Directory, które mają dobrej kondycji replikacji katalogu i folderu sysvol stanu folderu dwa serwery proxy jest wystarczający, aby zapewnić dostępność. Powoduje to szybkie pobieranie nowych zasad oraz inne dane. Jednak można wdrożyć serwery proxy dodatkowe.

Projekt oprogramowania agenta DC zmniejsza zwykle problemy, które są skojarzone z wysokiej dostępności. Agent kontroler domeny przechowuje lokalnej pamięci podręcznej najbardziej ostatnio pobrane zasady haseł. Nawet wtedy, gdy wszystkie zarejestrowane serwery proxy, które staną się niedostępne, agentów DC w dalszym ciągu wymuszać swoje zasady haseł w pamięci podręcznej. Częstotliwość aktualizacji uzasadnione dla zasad haseł w dużych wdrożeniach, jest zazwyczaj *dni*, nie godziny lub mniej. Dlatego krótki awarii serwery proxy nie poważny wpływ na ochrona za pomocą hasła usługi Azure AD.

## <a name="next-steps"></a>Kolejne kroki

Teraz, po zainstalowaniu usługi, które wymagają ochrony hasłem w usłudze Azure AD na serwerach lokalnych, [wykonania po instalacji, konfiguracji i zbieranie raportowanie informacji o](howto-password-ban-bad-on-premises-operations.md) do ukończenia danego wdrożenia.

[Omówienie pojęć dotyczących ochrona za pomocą hasła usługi Azure AD](concept-password-ban-bad-on-premises.md)
