---
title: Wdrażanie ochrony hasłem w usłudze Azure AD — Azure Active Directory
description: Wdróż ochronę hasłem usługi Azure AD, aby zablokować złe hasła lokalnie
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
ms.openlocfilehash: 4a745648f1b7abac7267d51cac9e1fe642ae13d8
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68853687"
---
# <a name="deploy-azure-ad-password-protection"></a>Wdrażanie ochrony haseł w usłudze Azure AD

Teraz, gdy zrozumiesz, [Jak wymusić ochronę hasłem w usłudze Azure AD dla systemu Windows Server Active Directory](concept-password-ban-bad-on-premises.md), następnym krokiem jest zaplanowanie i wykonanie wdrożenia.

## <a name="deployment-strategy"></a>Strategia wdrażania

Zalecamy rozpoczęcie wdrożeń w trybie inspekcji. Tryb inspekcji jest domyślnym ustawieniem początkowym, w którym można kontynuować ustawianie haseł. Hasła, które byłyby blokowane, są rejestrowane w dzienniku zdarzeń. Po wdrożeniu serwerów proxy i agentów kontrolera domeny w trybie inspekcji należy monitorować wpływ zasad haseł na użytkowników i środowisko, gdy zasady zostaną wymuszone.

Na etapie inspekcji w wielu organizacjach jest dowiedzieć się, że:

* Muszą oni ulepszyć istniejące procesy operacyjne, aby używać bardziej bezpiecznych haseł.
* Użytkownicy często używają niezabezpieczonych haseł.
* Muszą oni informować użytkowników o nadchodzącej zmianie wymuszania zabezpieczeń, możliwym wpływie na nie i sposobach wybierania bezpiecznych haseł.

Istnieje również możliwość, że sprawdzanie poprawności hasła ma wpływ na istniejące Active Directory Automatyzacja wdrożenia kontrolera domeny. Zaleca się, aby podczas oceny okresu inspekcji zakończyło się co najmniej jedną promocję kontrolera domeny i jedno obniżenie kontrolera domeny w celu ułatwienia odzyskania takich problemów.  Aby uzyskać więcej informacji, zobacz:

* [Narzędzie Ntdsutil. exe nie może ustawić słabego hasła trybu naprawy usług katalogowych](howto-password-ban-bad-on-premises-troubleshoot.md##ntdsutilexe-fails-to-set-a-weak-dsrm-password)
* [Podwyższanie poziomu repliki kontrolera domeny kończy się niepowodzeniem z powodu słabego hasła trybu naprawy usług katalogowych](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password)
* [Obniżanie poziomu kontrolera domeny nie powiodło się z powodu słabego hasła administratora lokalnego](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-demotion-fails-due-to-a-weak-local-administrator-password)

Po uruchomieniu funkcji w trybie inspekcji przez rozsądny okres można zmienić konfigurację z *inspekcji* w celu wymuszenia, aby wymagać bardziej bezpiecznych haseł. Ukierunkowane monitorowanie w tym czasie jest dobrym pomysłem.

## <a name="deployment-requirements"></a>Wymagania dotyczące wdrażania

* Wymagania dotyczące licencjonowania usługi Azure AD Password Protection można znaleźć w artykule eliminowanie nieprawidłowych [haseł w organizacji](concept-password-ban-bad.md#license-requirements).
* Wszystkie kontrolery domeny, na których zainstalowano usługę agenta DC dla ochrony haseł usługi Azure AD, muszą mieć uruchomiony system Windows Server 2012 lub nowszy. Ten wymóg nie oznacza, że domena lub las Active Directory muszą być również na poziomie funkcjonalności domeny lub lasu systemu Windows Server 2012. Jak wspomniano w [zasadach projektowania](concept-password-ban-bad-on-premises.md#design-principles), nie ma minimalnych DFL lub FFL wymaganych do uruchomienia agenta lub oprogramowania serwera proxy.
* Na wszystkich maszynach, na których zainstalowano usługę agenta kontrolera domeny, musi być zainstalowany program .NET 4,5.
* Na wszystkich maszynach, na których zainstalowano usługę proxy dla ochrony haseł usługi Azure AD, musi działać system Windows Server 2012 R2 lub nowszy.
   > [!NOTE]
   > Wdrożenie usługi serwera proxy to obowiązkowe wymaganie do wdrożenia ochrony hasłem usługi Azure AD, mimo że kontroler domeny może mieć bezpośrednią łączność z Internetem. 
   >
* Na wszystkich maszynach, na których zostanie zainstalowana usługa serwera proxy ochrony hasłem usługi Azure AD, musi być zainstalowany program .NET 4,7.
  Środowisko .NET 4,7 powinno być już zainstalowane w w pełni zaktualizowanym systemie Windows Server. Jeśli tak nie jest, Pobierz i uruchom Instalatora, który znajduje się w [instalatorze offline .NET Framework 4,7 dla systemu Windows](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows).
* Wszystkie maszyny, w tym kontrolery domeny, które mają zainstalowane składniki ochrony haseł usługi Azure AD, muszą mieć zainstalowane środowisko uruchomieniowe języka uniwersalnego C. Środowisko uruchomieniowe można uzyskać, upewniając się, że masz wszystkie aktualizacje Windows Update. Lub można ją pobrać w pakiecie aktualizacji specyficznym dla systemu operacyjnego. Aby uzyskać więcej informacji, zobacz [Aktualizacja dla środowiska uruchomieniowego uniwersalnego języka C w systemie Windows](https://support.microsoft.com/help/2999226/update-for-uniersal-c-runtime-in-windows).
* Połączenie sieciowe musi istnieć między co najmniej jednym kontrolerem domeny w każdej domenie i co najmniej jednym serwerem, który obsługuje usługę proxy na potrzeby ochrony hasłem. Ta łączność musi zezwalać kontrolerowi domeny na dostęp do portu mapowania punktów końcowych wywołań RPC 135 i portu serwera RPC w usłudze serwera proxy. Domyślnie port serwera RPC to dynamiczny port RPC, ale można go skonfigurować do [korzystania z portu statycznego](#static).
* Wszystkie maszyny, które obsługują usługę proxy, muszą mieć dostęp sieciowy do następujących punktów końcowych:

    |**Punkt końcowy**|**Cel**|
    | --- | --- |
    |`https://login.microsoftonline.com`|Żądania uwierzytelniania|
    |`https://enterpriseregistration.windows.net`|Funkcja ochrony hasłem w usłudze Azure AD|

* Wszystkie maszyny, na których jest hostowana usługa serwera proxy dla ochrony haseł, muszą być skonfigurowane tak, aby zezwalały na ruch HTTP 1,2 ruchu przychodzącego TLS.
* Konto administratora globalnego do zarejestrowania usługi proxy na potrzeby ochrony hasłem i lasu w usłudze Azure AD.
* Konto, które ma Active Directory uprawnienia administratora domeny w domenie głównej lasu do zarejestrowania lasu systemu Windows Server Active Directory z usługą Azure AD.
* Każda domena Active Directory, na której działa oprogramowanie usługi agenta kontrolera domeny, musi używać replikacji rozproszony system plików (DFSR) do replikacji folderu SYSVOL.
* Usługa dystrybucji kluczy musi być włączona na wszystkich kontrolerach domeny w domenie z systemem Windows Server 2012. Domyślnie ta usługa jest włączona za pośrednictwem uruchomienia wyzwalacza ręcznego.

## <a name="single-forest-deployment"></a>Wdrożenie z jednego lasu

Na poniższym diagramie przedstawiono sposób, w jaki podstawowe składniki ochrony hasłem usługi Azure AD współpracują ze sobą w środowisku lokalnym Active Directory.

![Jak współpracują ze składnikami ochrony haseł usługi Azure AD](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

Dobrym pomysłem jest sprawdzenie, jak działa oprogramowanie przed jego wdrożeniem. Zobacz [Omówienie pojęć związanych z ochroną hasłem usługi Azure AD](concept-password-ban-bad-on-premises.md).

### <a name="download-the-software"></a>Pobierz oprogramowanie

Istnieją dwa wymagane Instalatory dla ochrony hasłem usługi Azure AD. Są one dostępne w [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=57071).

### <a name="install-and-configure-the-proxy-service-for-password-protection"></a>Instalowanie i Konfigurowanie usługi proxy na potrzeby ochrony hasłem

1. Wybierz co najmniej jeden serwer, na którym ma być Hostowana usługa serwera proxy w celu ochrony haseł.
   * Każda taka usługa może udostępniać tylko zasady haseł dla jednego lasu. Komputer hosta musi być przyłączony do domeny w tym lesie. Domeny główne i podrzędne są obsługiwane. Wymagana jest łączność sieciowa między co najmniej jednym KONTROLERem domeny w każdej domenie lasu i komputerem ochrony hasłem.
   * Usługę serwera proxy można uruchomić na kontrolerze domeny w celu przetestowania. Jednak kontroler domeny wymaga łączności z Internetem, co może stanowić zagrożenie dla bezpieczeństwa. Zalecamy tę konfigurację tylko do celów testowych.
   * Zalecamy co najmniej dwa serwery proxy w celu zapewnienia nadmiarowości. Zobacz [wysoką dostępność](howto-password-ban-bad-on-premises-deploy.md#high-availability).

1. Zainstaluj usługę proxy ochrony hasłem usługi Azure AD przy użyciu `AzureADPasswordProtectionProxySetup.exe` instalatora oprogramowania.
   * Instalacja oprogramowania nie wymaga ponownego uruchomienia komputera. Instalację oprogramowania można zautomatyzować za pomocą standardowych procedur MSI, na przykład:

      `AzureADPasswordProtectionProxySetup.exe /quiet`

      > [!NOTE]
      > Przed zainstalowaniem pakietu AzureADPasswordProtectionProxySetup. msi w celu uniknięcia błędu instalacji musi być uruchomiona usługa Zapora systemu Windows. Jeśli Zapora systemu Windows jest skonfigurowana do uruchamiania, obejście to tymczasowe włączenie i uruchomienie usługi zapory podczas instalacji. Oprogramowanie serwera proxy nie ma określonej zależności od zapory systemu Windows po zakończeniu instalacji. Jeśli używasz zapory innej firmy, musi ona być skonfigurowana w celu spełnienia wymagań dotyczących wdrożenia. Obejmują one między innymi Zezwalanie na dostęp przychodzący do portu 135 i portu serwera proxy RPC. Zobacz [wymagania dotyczące wdrażania](howto-password-ban-bad-on-premises-deploy.md#deployment-requirements).

1. Otwórz okno programu PowerShell jako administrator.
   * Oprogramowanie proxy ochrony hasłem zawiera nowy moduł programu PowerShell, *AzureADPasswordProtection*. Poniższe kroki uruchamiają różne polecenia cmdlet z tego modułu programu PowerShell. Zaimportuj nowy moduł w następujący sposób:

      ```powershell
      Import-Module AzureADPasswordProtection
      ```

   * Aby sprawdzić, czy usługa jest uruchomiona, użyj następującego polecenia programu PowerShell:

      `Get-Service AzureADPasswordProtectionProxy | fl`.

     Wynik powinien zawierać **stan** "uruchomiona".

1. Zarejestruj serwer proxy.
   * Po wykonaniu kroku 3 usługa serwera proxy jest uruchomiona na komputerze. Jednak usługa nie ma jeszcze wymaganych poświadczeń do komunikowania się z usługą Azure AD. Wymagana jest rejestracja w usłudze Azure AD:

     `Register-AzureADPasswordProtectionProxy`

     To polecenie cmdlet wymaga poświadczeń administratora globalnego dla dzierżawy platformy Azure. Wymagane są również lokalne Active Directory uprawnienia administratora domeny w domenie głównej lasu. Po jednokrotnym pomyślnym wykonaniu tego polecenia dla usługi serwera proxy dodatkowe wywołania będą się kończyć powodzeniem, ale nie są potrzebne.

      `Register-AzureADPasswordProtectionProxy` Polecenie cmdlet obsługuje następujące trzy tryby uwierzytelniania.

     * Tryb uwierzytelniania interaktywnego:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Ten tryb nie działa w systemach operacyjnych Server Core. Zamiast tego należy użyć jednego z następujących trybów uwierzytelniania. Ponadto ten tryb może zakończyć się niepowodzeniem, jeśli konfiguracja zwiększonych zabezpieczeń programu Internet Explorer jest włączona. Obejście polega na wyłączeniu tej konfiguracji, zarejestrowaniu serwera proxy, a następnie jego ponownym włączeniu.

     * Tryb uwierzytelniania kodu urządzenia:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
        ```

        Następnie można wykonać uwierzytelnianie, postępując zgodnie z instrukcjami wyświetlanymi na innym urządzeniu.

     * Tryb uwierzytelniania dyskretnego (opartego na hasłach):

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionProxy -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Ten tryb kończy się niepowodzeniem, jeśli dla Twojego konta jest wymagane uwierzytelnianie wieloskładnikowe systemu Azure. W takim przypadku należy użyć jednego z dwóch poprzednich trybów uwierzytelniania lub zamiast tego użyć innego konta, które nie wymaga uwierzytelniania MFA.
        >
        > Możesz również sprawdzić, czy uwierzytelnianie wieloskładnikowe jest wymagane, jeśli usługa Azure Device Registration (która jest używana w ramach okładki przez usługę Azure AD Password Protection) została skonfigurowana w taki sposób, aby globalnie wymagała uwierzytelniania wieloskładnikowego. Aby obejść ten sposób, możesz użyć innego konta, które obsługuje usługę MFA z jednym z poprzednich dwóch trybów uwierzytelniania, lub można także tymczasowo osłabić wymaganie usługi Azure Device Registration MFA. W tym celu przejdź do portalu zarządzania systemu Azure, a następnie przejdź do pozycji Azure Active Directory, następnie kliknij pozycję urządzenia, następnie pozycję Ustawienia urządzenia, a następnie ustaw opcję Wymagaj uwierzytelniania wieloskładnikowego w celu dołączenia do urządzeń. Należy koniecznie zmienić konfigurację tego ustawienia z powrotem na wartość tak po zakończeniu rejestracji.
        >
        > Zalecamy, aby wymagania usługi MFA były pomijane wyłącznie w celach testowych.

       Obecnie nie musisz określać parametru *-ForestCredential* , który jest zarezerwowany dla przyszłych funkcji.

   Rejestracja usługi proxy na potrzeby ochrony hasłem jest niezbędna tylko raz w okresie istnienia usługi. Po wykonaniu tej czynności usługa serwera proxy automatycznie przeprowadzi wszelkie inne niezbędne konserwacje.

   > [!TIP]
   > Podczas pierwszego uruchomienia tego polecenia cmdlet dla określonej dzierżawy platformy Azure może wystąpić zauważalne opóźnienie. O ile nie zgłoszono błędu, nie martw się o to opóźnienie.

1. Zarejestruj Las.
   * Aby komunikować się z platformą Azure przy użyciu `Register-AzureADPasswordProtectionForest` polecenia cmdlet programu PowerShell, należy zainicjować Las Active Directory lokalnego z poświadczeniami niezbędnymi. Polecenie cmdlet wymaga poświadczeń administratora globalnego dla dzierżawy platformy Azure. Wymaga również lokalnego Active Directory uprawnień administratora przedsiębiorstwa. Ten krok jest uruchamiany raz na las.

      `Register-AzureADPasswordProtectionForest` Polecenie cmdlet obsługuje następujące trzy tryby uwierzytelniania.

     * Tryb uwierzytelniania interaktywnego:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Ten tryb nie będzie działać w systemach operacyjnych Server Core. Zamiast tego należy użyć jednego z następujących dwóch trybów uwierzytelniania. Ponadto ten tryb może zakończyć się niepowodzeniem, jeśli konfiguracja zwiększonych zabezpieczeń programu Internet Explorer jest włączona. Obejście polega na wyłączeniu tej konfiguracji, zarejestrowaniu lasu, a następnie jego ponownym włączeniu.  

     * Tryb uwierzytelniania kodu urządzenia:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
        ```

        Następnie można wykonać uwierzytelnianie, postępując zgodnie z instrukcjami wyświetlanymi na innym urządzeniu.

     * Tryb uwierzytelniania dyskretnego (opartego na hasłach):

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Ten tryb kończy się niepowodzeniem, jeśli dla Twojego konta jest wymagane uwierzytelnianie wieloskładnikowe systemu Azure. W takim przypadku należy użyć jednego z dwóch poprzednich trybów uwierzytelniania lub zamiast tego użyć innego konta, które nie wymaga uwierzytelniania MFA.
        >
        > Możesz również sprawdzić, czy uwierzytelnianie wieloskładnikowe jest wymagane, jeśli usługa Azure Device Registration (która jest używana w ramach okładki przez usługę Azure AD Password Protection) została skonfigurowana w taki sposób, aby globalnie wymagała uwierzytelniania wieloskładnikowego. Aby obejść ten sposób, możesz użyć innego konta, które obsługuje usługę MFA z jednym z poprzednich dwóch trybów uwierzytelniania, lub można także tymczasowo osłabić wymaganie usługi Azure Device Registration MFA. W tym celu przejdź do portalu zarządzania systemu Azure, a następnie przejdź do pozycji Azure Active Directory, następnie kliknij pozycję urządzenia, następnie pozycję Ustawienia urządzenia, a następnie ustaw opcję Wymagaj uwierzytelniania wieloskładnikowego w celu dołączenia do urządzeń. Należy koniecznie zmienić konfigurację tego ustawienia z powrotem na wartość tak po zakończeniu rejestracji.
        >
        > Zalecamy, aby wymagania usługi MFA były pomijane wyłącznie w celach testowych.

       Te przykłady zakończyły się powodzeniem tylko wtedy, gdy obecnie zalogowany użytkownik jest również Active Directory administratorem domeny dla domeny katalogu głównego. Jeśli tak nie jest, możesz podać alternatywne poświadczenia domeny za pomocą parametru *-ForestCredential* .

   > [!NOTE]
   > Jeśli w środowisku zainstalowano wiele serwerów proxy, nie ma znaczenia serwera proxy używanego do zarejestrowania lasu.
   >
   > [!TIP]
   > Podczas pierwszego uruchomienia tego polecenia cmdlet dla określonej dzierżawy platformy Azure może wystąpić zauważalne opóźnienie. O ile nie zgłoszono błędu, nie martw się o to opóźnienie.

   Rejestracja lasu Active Directory jest konieczna tylko raz w okresie istnienia lasu. Po upływie tego czasu agenci kontrolera domeny w lesie będą automatycznie wykonywać wszelkie inne niezbędne czynności konserwacyjne. Po `Register-AzureADPasswordProtectionForest` pomyślnym uruchomieniu dla lasu, dodatkowe wywołania polecenia cmdlet powiodą się, ale nie są potrzebne.

   `Register-AzureADPasswordProtectionForest` Aby pomyślnie, w domenie serwera proxy musi być dostępny co najmniej jeden kontroler domeny z systemem Windows Server 2012 lub nowszym. Jednak przed wykonaniem tego kroku nie trzeba instalować oprogramowania agenta DC na żadnym z kontrolerów domeny.

1. Skonfiguruj usługę proxy do ochrony hasłem, aby komunikować się za pośrednictwem serwera proxy HTTP.

   Jeśli środowisko wymaga użycia określonego serwera proxy HTTP do komunikowania się z platformą Azure, Użyj tej metody: Utwórz plik *AzureADPasswordProtectionProxy. exe. config* w folderze%PROGRAMFILES%\Azure usługi AD Password Protection Proxy\Service. Uwzględnij następującą zawartość:

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

   Jeśli serwer proxy HTTP wymaga uwierzytelniania, Dodaj tag *UseDefaultCredentials* :

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

   W obu przypadkach Zastąp `http://yourhttpproxy.com:8080` ciąg adresem i portem określonego serwera proxy HTTP.

   Jeśli serwer proxy HTTP jest skonfigurowany do korzystania z zasad autoryzacji, należy udzielić dostępu do konta komputera Active Directory maszyny, która obsługuje usługę serwera proxy na potrzeby ochrony hasłem.

   Zalecamy zatrzymanie i ponowne uruchomienie usługi proxy po utworzeniu lub zaktualizowaniu pliku *AzureADPasswordProtectionProxy. exe. config* .

   Usługa serwera proxy nie obsługuje korzystania z określonych poświadczeń w celu nawiązania połączenia z serwerem proxy HTTP.

1. Opcjonalnie: Skonfiguruj usługę proxy do ochrony hasłem, aby nasłuchiwać na określonym porcie.
   * Oprogramowanie agenta DC do ochrony hasłem na kontrolerach domeny używa protokołu RPC przez TCP do komunikowania się z usługą proxy. Domyślnie usługa serwera proxy nasłuchuje na dowolnym dostępnym dynamicznym punkcie końcowym RPC. Można jednak skonfigurować usługę do nasłuchiwania na konkretnym porcie TCP, jeśli jest to konieczne ze względu na topologię sieci lub wymagania dotyczące zapory w danym środowisku.
      * <a id="static" /></a>Aby skonfigurować usługę do uruchamiania w ramach portu statycznego, należy użyć `Set-AzureADPasswordProtectionProxyConfiguration` polecenia cmdlet.

         ```powershell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
         ```

         > [!WARNING]
         > Aby te zmiany zaczęły obowiązywać, należy zatrzymać i ponownie uruchomić usługę.

      * Aby skonfigurować usługę do uruchamiania w ramach portu dynamicznego, Użyj tej samej procedury, ale ustaw *StaticPort* z powrotem na zero:

         ```powershell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
         ```

         > [!WARNING]
         > Aby te zmiany zaczęły obowiązywać, należy zatrzymać i ponownie uruchomić usługę.

   > [!NOTE]
   > Usługa serwera proxy dla ochrony hasłem wymaga ręcznego ponownego uruchomienia po zmianie konfiguracji portów. Nie trzeba jednak ponownie uruchamiać oprogramowania usługi agenta DC na kontrolerach domeny po wprowadzeniu tych zmian konfiguracji.

   * Aby wykonać zapytanie o bieżącą konfigurację usługi, należy użyć `Get-AzureADPasswordProtectionProxyConfiguration` polecenia cmdlet:

      ```powershell
      Get-AzureADPasswordProtectionProxyConfiguration | fl

      ServiceName : AzureADPasswordProtectionProxy
      DisplayName : Azure AD password protection Proxy
      StaticPort  : 0
      ```

### <a name="install-the-dc-agent-service"></a>Instalowanie usługi agenta DC

   Zainstaluj usługę agenta DC do ochrony hasłem przy użyciu `AzureADPasswordProtectionDCAgentSetup.msi` pakietu.

   Instalacja oprogramowania lub Dezinstalacja wymaga ponownego uruchomienia komputera. Wynika to z faktu, że biblioteki DLL filtru haseł są ładowane lub zwalniane tylko po ponownym uruchomieniu.

   Usługę agenta DC można zainstalować na komputerze, który nie jest jeszcze kontrolerem domeny. W takim przypadku usługa zostanie uruchomiona i uruchomiona, ale pozostanie nieaktywna, dopóki komputer nie zostanie podwyższony do poziomu kontrolera domeny.

   Instalację oprogramowania można zautomatyzować za pomocą standardowych procedur MSI. Na przykład:

   `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart`

   Możesz pominąć `/norestart` flagę, jeśli wolisz, aby Instalator automatycznie ponownie uruchomił maszynę.

Instalacja zostanie zakończona po zainstalowaniu na kontrolerze domeny oprogramowania agenta DC i ponownym uruchomieniu komputera. Nie jest wymagana żadna inna konfiguracja.

## <a name="upgrading-the-proxy-agent"></a>Uaktualnianie agenta proxy

Gdy jest dostępna nowsza wersja oprogramowania proxy ochrony hasłem usługi Azure AD, uaktualnienie jest wykonywane przez uruchomienie najnowszej wersji `AzureADPasswordProtectionProxySetup.exe` instalatora oprogramowania. Najnowsza wersja oprogramowania jest dostępna w [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=57071).

Odinstalowywanie bieżącej wersji oprogramowania serwera proxy nie jest wymagane — Instalator wykona uaktualnienie w miejscu. Podczas uaktualniania oprogramowania serwera proxy nie powinno być wymagane ponowne uruchomienie komputera. Uaktualnienie oprogramowania może być zautomatyzowane przy użyciu standardowych procedur MSI, na przykład: `AzureADPasswordProtectionProxySetup.exe /quiet`.

Agent proxy obsługuje automatyczne uaktualnianie. Automatyczne uaktualnianie Microsoft Azure AD używa usługi Aktualizator Connect Agent, która jest zainstalowana równolegle z usługą proxy. Automatyczne uaktualnianie jest domyślnie włączone i można je włączyć lub wyłączyć za pomocą polecenia cmdlet Set-AzureADPasswordProtectionProxyConfiguration. Bieżące ustawienie można wykonać przy użyciu polecenia cmdlet Get-AzureADPasswordProtectionProxyConfiguration. Firma Microsoft zaleca, aby funkcja automatycznego uaktualniania była włączona.

`Get-AzureADPasswordProtectionProxy` Polecenie cmdlet może służyć do wykonywania zapytań dotyczących wersji oprogramowania wszystkich aktualnie zainstalowanych agentów proxy w lesie.

## <a name="upgrading-the-dc-agent"></a>Uaktualnianie agenta DC

Gdy jest dostępna nowsza wersja oprogramowania agenta DC ochrony hasłem w usłudze Azure AD, uaktualnienie jest wykonywane przez uruchomienie najnowszej wersji `AzureADPasswordProtectionDCAgentSetup.msi` pakietu oprogramowania. Najnowsza wersja oprogramowania jest dostępna w [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=57071).

Odinstalowywanie bieżącej wersji oprogramowania agenta kontrolera domeny nie jest wymagane — Instalator wykona uaktualnienie w miejscu. Podczas uaktualniania oprogramowania agenta kontrolera domeny jest zawsze wymagane ponowne uruchomienie komputera — jest to spowodowane przez podstawowe zachowanie systemu Windows. 

Uaktualnienie oprogramowania może być zautomatyzowane przy użyciu standardowych procedur MSI, na przykład: `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart`.

Możesz pominąć `/norestart` flagę, jeśli wolisz, aby Instalator automatycznie ponownie uruchomił maszynę.

`Get-AzureADPasswordProtectionDCAgent` Polecenie cmdlet może służyć do wykonywania zapytań dotyczących wersji oprogramowania wszystkich aktualnie zainstalowanych agentów kontrolera domeny w lesie.

## <a name="multiple-forest-deployments"></a>Wdrożenia wielu lasów

Nie ma dodatkowych wymagań dotyczących wdrażania ochrony hasłem usługi Azure AD w wielu lasach. Każdy Las jest niezależnie skonfigurowany zgodnie z opisem w sekcji "wdrożenie jednego lasu". Każdy serwer proxy ochrony hasłem może obsługiwać tylko kontrolery domeny z lasu, do którego jest przyłączony. Oprogramowanie ochrony hasłem w dowolnym lesie nie jest nieświadome oprogramowania ochrony hasła, które zostało wdrożone w innych lasach, niezależnie od konfiguracji zaufania Active Directory.

## <a name="read-only-domain-controllers"></a>Kontrolery domeny tylko do odczytu

Zmiany/zestawy haseł nie są przetwarzane i utrwalane na kontrolerach domeny tylko do odczytu (RODC). Są one przekazywane do zapisywalnych kontrolerów domeny. W związku z tym nie trzeba instalować oprogramowania agenta kontrolera domeny na kontrolerach RODC.

## <a name="high-availability"></a>Wysoka dostępność

Główną kwestią dostępności ochrony hasłem jest dostępność serwerów proxy, gdy kontrolery domeny w lesie próbują pobrać nowe zasady lub inne dane z platformy Azure. Każdy agent kontrolera domeny używa prostego algorytmu okrężnej w stylu podczas wybierania serwera proxy do wywołania. Agent pomija serwery proxy, które nie odpowiadają. W przypadku większości w pełni połączonych Active Directory wdrożeń, które mają dobrą replikację stanu katalogu i folderu SYSVOL, dwa serwery proxy są wystarczające do zapewnienia dostępności. Powoduje to okresowe pobieranie nowych zasad i innych danych. Można jednak wdrożyć dodatkowe serwery proxy.

Projekt oprogramowania agenta kontrolera domeny ogranicza typowe problemy, które są związane z wysoką dostępnością. Agent DC obsługuje lokalną pamięć podręczną ostatnio pobranych zasad haseł. Nawet jeśli wszystkie zarejestrowane serwery proxy staną się niedostępne, agenci kontrolera domeny kontynuują wymuszanie zasad haseł w pamięci podręcznej. Rozsądna częstotliwość aktualizacji zasad haseł w dużych wdrożeniach to zwykle dni, nie więcej niż godz. W związku z tym krótkie przestoje serwerów proxy nie wpływają znacząco na ochronę hasłem usługi Azure AD.

## <a name="next-steps"></a>Następne kroki

Po zainstalowaniu usług potrzebnych do ochrony hasłem usługi Azure AD na serwerach lokalnych należy [przeprowadzić konfigurację po instalacji i zebrać informacje o raportowaniu](howto-password-ban-bad-on-premises-operations.md) w celu ukończenia wdrożenia.

[Omówienie pojęć związanych z ochroną hasłem w usłudze Azure AD](concept-password-ban-bad-on-premises.md)
