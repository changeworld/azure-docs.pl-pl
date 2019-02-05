---
title: Wdrażanie ochrony haseł usługi Azure AD (wersja zapoznawcza)
description: Wdrażanie Podgląd ochrony haseł usługi Azure AD, aby zablokować błędnego hasła lokalnie
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.openlocfilehash: 824bedf782d6d227f2fa3adcf52492bb5a3eb478
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2019
ms.locfileid: "55696867"
---
# <a name="preview-deploy-azure-ad-password-protection"></a>Wersja zapoznawcza: Wdrażanie ochrony haseł usługi Azure AD

|     |
| --- |
| Ochrony hasłem w usłudze Azure AD jest funkcją publicznej wersji zapoznawczej usługi Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [dodatkowym warunkom użytkowania wersji zapoznawczych usług Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Skoro mamy już zrozumienia [jak wymusić hasło ochrony dla systemu Windows Server Active katalog usługi Azure AD](concept-password-ban-bad-on-premises.md), następnym krokiem jest planowania i wykonywania wdrożenia.

## <a name="deployment-strategy"></a>Strategia wdrażania

Firma Microsoft sugeruje, że wszystkie wdrożenia rozruchu w trybie inspekcji. Tryb inspekcji jest ustawienie początkowej domyślne, których hasła mogą w dalszym ciągu można ustawić i dowolny, które zostałby zablokowany utworzyć wpisy w dzienniku zdarzeń. Gdy serwery proxy i agentów DC zostały w pełni wdrożone w trybie inspekcji, regularne monitorowanie ma się odbywać w celu ustalenia, jakie zasady haseł wpływu wymuszania miałby on użytkowników i środowiska, jeśli zasada była wymuszana.

Na etapie inspekcji znaleźć wiele organizacji:

* Należy poprawić istniejące procesy operacyjne z bardziej bezpiecznych haseł.
* Użytkownicy są przyzwyczajeni do regularnie wybierając niezabezpieczonych hasła
* Muszą oni o tym poinformować użytkowników o nadchodzących zmianę wymuszania zabezpieczeń, wpływ może mieć na nich i pomagają lepiej zrozumieć, jak można wybrać bardziej bezpiecznych haseł.

Gdy ta funkcja działa w trybie inspekcji rozsądnym czasie, konfigurację wymuszania można odwrócić z **inspekcji** do **Wymuś** dzięki czemu wymagają bardziej bezpiecznych haseł. Dobrym pomysłem jest monitorowanie wąsko zdefiniowany w tym czasie.

## <a name="deployment-requirements"></a>Wymagania dotyczące wdrażania

* Wszystkie kontrolery domeny, w którym zostanie zainstalowana usługa hasło ochrony kontrolera domeny agenta programu Azure AD musi działać system Windows Server 2012 lub nowszy.
* Wszystkie komputery z zainstalowaną usługę serwera Proxy ochrony haseł usługi Azure AD musi działać system Windows Server 2012 R2 lub nowszym.
* Wszystkie maszyny, których ochrony haseł usługi Azure AD składniki są zainstalowane w tym kontrolery domeny muszą mieć zainstalowanego środowiska uruchomieniowego języka Universal C.
Najlepiej jest to realizowane poprzez wdrażanie poprawek w pełni maszyny za pośrednictwem usługi Windows Update. W przeciwnym razie odpowiedni pakiet aktualizacji specyficznych dla systemu operacyjnego może być zainstalowana — zobacz [aktualizacji dla uniwersalnego środowiska uruchomieniowego c. w Windows](https://support.microsoft.com/help/2999226/update-for-universal-c-runtime-in-windows)
* Łączność sieciowa musi istnieć między co najmniej jeden kontroler domeny w każdej domenie i co najmniej jeden serwer obsługujący usługę Proxy ochrony haseł usługi Azure AD. To połączenie musi zezwalać na kontrolerze domeny, aby uzyskiwać dostęp do portu mapowania punktu końcowego RPC (135) i port serwera RPC na usługę serwera proxy. Port serwera RPC jest domyślnie dynamicznego portu RPC, ale można go skonfigurować (zobacz poniżej), aby korzystanie z portu statycznego.
* Wszystkie maszyny, obsługujący usługę Proxy ochrony haseł usługi Azure AD, musi mieć dostęp do następujących punktów końcowych sieci:

    |Endpoint |Przeznaczenie|
    | --- | --- |
    |`https://login.microsoftonline.com`|Żądania uwierzytelniania|
    |`https://enterpriseregistration.windows.net`|Funkcjonalność ochrony hasłem usługi AD platformy Azure|

* Konta administratora globalnego, aby zarejestrować usługę serwera Proxy ochrony haseł usługi Azure AD i lasu w usłudze Azure AD.
* Konta z uprawnieniami administratora domeny usługi Active Directory w domenie głównej lasu, aby zarejestrować lasu usługi Active Directory systemu Windows Server z usługą Azure AD.
* Dowolnej domeny usługi Active Directory, uruchamianie kontrolera domeny oprogramowanie usługi agenta należy użyć DFSR do replikacji folderu sysvol.

## <a name="single-forest-deployment"></a>Wdrożenie pojedynczego lasu

Na poniższym diagramie przedstawiono, jak podstawowe składniki ochrony haseł usługi Azure AD współpracują ze sobą w środowisku usługi Active Directory w środowisku lokalnym.  

![Sposób ochrony haseł usługi Azure AD składniki współpracują ze sobą](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

### <a name="download-the-software"></a>Pobierz oprogramowanie

Istnieją dwa wymagane pliki instalacyjne dla ochrony haseł usługi Azure AD, który można pobrać z [Centrum pobierania firmy Microsoft](https://www.microsoft.com/download/details.aspx?id=57071)

### <a name="install-and-configure-the-azure-ad-password-protection-proxy-service"></a>Instalowanie i konfigurowanie usługi Serwer proxy ochrony haseł usługi Azure AD

1. Wybierz co najmniej jeden serwer do obsługi usługi Proxy ochrony haseł usługi Azure AD.
   * Każdej z tych usług można tylko zasady dotyczące haseł dla pojedynczego lasu, i maszynie hosta muszą być przyłączone do domeny (domeny katalogu głównego i podrzędnego są obsługiwane) w tym lesie. Aby usługa serwera Proxy ochrony haseł usługi Azure AD do spełnienia swoją misję musi istnieć łączność sieciową między co najmniej jeden kontroler domeny w każdej domenie w lesie i na komputerze serwera Proxy ochrony haseł usługi Azure AD.
   * Jest ona obsługiwana do zainstalowania i uruchomienia usługi serwera Proxy ochrony haseł usługi Azure AD na kontrolerze domeny, do testowania; minusem jest to, że kontroler domeny, a następnie wymaga łączności z Internetem, który może być kwestią zabezpieczeń. Firma Microsoft zaleca się, że ta konfiguracja używane tylko do celów testowych.
   * Co najmniej dwóch serwerów proxy jest zalecane do celów nadmiarowości. [Zobacz wysokiej dostępności](howto-password-ban-bad-on-premises-deploy.md#high-availability)

2. Zainstaluj usługę serwera Proxy ochrony haseł usługi Azure AD za pomocą pakietu AzureADPasswordProtectionProxySetup.msi MSI.
   * Instalacja oprogramowania nie jest wymagane ponowne uruchomienie komputera. Instalacja oprogramowania mogą zostać zautomatyzowane przy użyciu standardowych procedur MSI, na przykład: `msiexec.exe /i AzureADPasswordProtectionProxySetup.msi /quiet /qn`

      > [!NOTE]
      > Musi być uruchomiona usługa Zapora Windows przed zainstalowaniem pakietu AzureADPasswordProtectionProxySetup.msi MSI — w przeciwnym razie wystąpi błąd instalacji. Jeśli Zapora Windows jest skonfigurowany do uruchamiania nie, obejście jest tymczasowo włączyć i uruchom usługę Windows zapory podczas procesu instalacji. Oprogramowanie serwera Proxy nie jest określone zależny na oprogramowanie zapory Windows po instalacji. Jeśli używasz zapory innych firm, nadal musi być skonfigurowany do spełnienia wymagań związanych z wdrażaniem (zezwala dostępu przychodzącego do portu 135 protokołów i serwer proxy RPC port serwera czy dynamiczny lub statyczny). [Zobacz wymagania dotyczące wdrażania](howto-password-ban-bad-on-premises-deploy.md#deployment-requirements)

3. Otwórz okno programu PowerShell jako Administrator.
   * Oprogramowanie serwera Proxy ochrony haseł usługi Azure AD obejmuje nowy moduł programu PowerShell o nazwie AzureADPasswordProtection. Poniższe kroki opierają się na uruchamianie różnych poleceń cmdlet z tego modułu programu PowerShell i założono, że otwarto nowe okno programu PowerShell i zaimportowano nowy moduł w następujący sposób:

      ```PowerShell
      Import-Module AzureADPasswordProtection
      ```

   * Upewnij się, że usługa jest uruchomiona przy użyciu następującego polecenia programu PowerShell: `Get-Service AzureADPasswordProtectionProxy | fl`.
     Wynik powinno to dawać wynik z parametrem **stan** zwróceniem wyników "Uruchomiona".

4. Zarejestruj serwer proxy.
   * Po ukończeniu kroku 3 usługę serwera Proxy ochrony haseł usługi Azure AD jest uruchomiona na komputerze, ale nie ma jeszcze poświadczenia niezbędne do komunikacji z usługą Azure AD. Rejestracja w usłudze Azure AD jest wymagany do włączenia, że używanie możliwości `Register-AzureADPasswordProtectionProxy` polecenia cmdlet programu PowerShell. Polecenie cmdlet wymaga administratora globalnego poświadczeń dla Twojej dzierżawy platformy Azure, a także uprawnienia administratora domeny usługi Active Directory w domenie głównej lasu lokalnego. Gdy zakończyła się pomyślnie dla danego serwera proxy usługi dodatkowe wywołań `Register-AzureADPasswordProtectionProxy` nadal pomyślnie tworzone, ale nie są konieczne.

      Polecenie cmdlet Register-AzureADPasswordProtectionProxy obsługuje trzy tryby uwierzytelniania różnych w następujący sposób.

      * Tryb interaktywny uwierzytelniania:

         ```PowerShell
         Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
         ```
         > [!NOTE]
         > W tym trybie nie będzie działać w systemach operacyjnych Server Core. Zamiast tego użyj jednego z trybów uwierzytelniania alternatywnych zgodnie z opisem poniżej.

         > [!NOTE]
         > Ten tryb może zakończyć się niepowodzeniem, jeśli konfiguracja zwiększonych zabezpieczeń programu Internet Explorer jest włączona. Obejście polega na wyłączyć IESC, Zarejestruj serwer proxy, a następnie ponownie włącz IESC.

      * Tryb uwierzytelniania kodu urządzenia:

         ```PowerShell
         Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceMode
         To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
         ```

         Następnie można zakończyć uwierzytelnianie, wykonując instrukcje wyświetlone na innym urządzeniu.

      * Tryb cichy uwierzytelniania (opartego na hasłach):

         ```PowerShell
         $globalAdminCredentials = Get-Credential
         Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
         ```

         > [!NOTE]
         > W tym trybie zakończy się niepowodzeniem, jeśli uwierzytelnianie MFA wymaga jakiegokolwiek powodu. Jeśli jest to możliwe, użyj jednego z poprzednich dwóch trybów do wykonywania uwierzytelniania MFA.

      Obecnie nie jest wymagane do określenia parametru - ForestCredential, który jest zarezerwowane dla przyszłych funkcji.

   > [!NOTE]
   > Rejestracja usługi serwera proxy ochrony haseł usługi Azure AD jest oczekiwaną jednorazowy krok w okresie istnienia usługi. Usługa serwera proxy automatycznie wykona niezbędnych działań od tej pory i nowszych wersjach. Po pomyślnym dla danego serwera proxy dodatkowe wywołań "Register-AzureADPasswordProtectionProxy" nadal pomyślnie tworzone, ale nie są konieczne.

   > [!TIP]
   > Może istnieć znaczne opóźnienie (w sekundach wielu) przy pierwszym uruchomieniu tego polecenia cmdlet jest uruchamiane dla danej dzierżawy usługi Azure, przed polecenie cmdlet kończy wykonywanie. Chyba że błąd jest zgłaszany to opóźnienie nie powinny być uznawane za monitów.

5. Zarejestruj lasu.
   * W środowisku lokalnym lesie usługi Active Directory musi zostać zainicjowany z poświadczenia niezbędne do komunikacji z platformy Azure za pomocą `Register-AzureADPasswordProtectionForest` polecenia cmdlet programu PowerShell. Polecenie cmdlet wymaga administratora globalnego poświadczeń dla Twojej dzierżawy platformy Azure, a także uprawnienia administratora domeny usługi Active Directory w domenie głównej lasu lokalnego. Ten krok zostanie uruchomiony jeden raz w każdym lesie.

      Polecenie cmdlet Register-AzureADPasswordProtectionForest obsługuje trzy tryby uwierzytelniania różnych w następujący sposób.

      * Tryb interaktywny uwierzytelniania:

         ```PowerShell
         Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
         ```
         > [!NOTE]
         > W tym trybie nie będzie działać w systemach operacyjnych Server Core. Zamiast tego użyj jednego z trybów uwierzytelniania alternatywnych zgodnie z opisem poniżej.

         > [!NOTE]
         > Ten tryb może zakończyć się niepowodzeniem, jeśli konfiguracja zwiększonych zabezpieczeń programu Internet Explorer jest włączona. Obejście polega na wyłączyć IESC, Zarejestruj serwer proxy, a następnie ponownie włącz IESC.  

      * Tryb uwierzytelniania kodu urządzenia:

         ```PowerShell
         Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceMode
         To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
         ```

         Następnie można zakończyć uwierzytelnianie, wykonując instrukcje wyświetlone na innym urządzeniu.

      * Tryb cichy uwierzytelniania (opartego na hasłach):
         ```PowerShell
         $globalAdminCredentials = Get-Credential
         Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
         ```

         > [!NOTE]
         > W tym trybie zakończy się niepowodzeniem, jeśli są wymagane uwierzytelnianie MFA. Jeśli jest to możliwe, użyj jednego z poprzednich dwóch trybów do wykonywania uwierzytelniania MFA.

      Powyższe przykłady powiedzie się tylko, jeśli aktualnie zalogowanego użytkownika jest również administratorem domeny usługi Active Directory dla domeny głównej. Jeśli nie jest to możliwe, może podać poświadczenia alternatywne domeny za pomocą parametru - ForestCredential.

   > [!NOTE]
   > Jeśli wielu serwerów proxy są zainstalowane w danym środowisku, nie ma znaczenia, który serwer proxy jest używane do rejestrowania w lesie.

   > [!TIP]
   > Może istnieć znaczne opóźnienie (w sekundach wielu) przy pierwszym uruchomieniu tego polecenia cmdlet jest uruchamiane dla danej dzierżawy usługi Azure, przed polecenie cmdlet kończy wykonywanie. Chyba że błąd jest zgłaszany to opóźnienie nie powinny być uznawane za monitów.

   > [!NOTE]
   > Rejestracja lasu usługi Active Directory oczekuje jednorazowy krok w okresie istnienia w lesie. Agentów kontrolera domeny w lesie automatycznie będzie wykonywać wszystkie niezbędne maintainenance od tej pory i nowszych wersjach. Gdy powiodła się dla podanego lasu, dodatkowe wywołań `Register-AzureADPasswordProtectionForest` nadal pomyślnie tworzone, ale nie są konieczne.

   > [!NOTE]
   > Aby `Register-AzureADPasswordProtectionForest` została wykonana pomyślnie co najmniej jeden system Windows Server 2012 lub nowszym domeny musi być dostępny kontroler domeny serwera proxy. Jednak nie jest wymagane zainstalowanie oprogramowania agenta kontrolera domeny kontrolerów domeny przed ten krok.

6. Konfigurowanie usługi Serwer Proxy ochrony haseł usługi Azure AD do komunikowania się za pośrednictwem serwera proxy HTTP

   Jeśli dane środowisko wymaga użycia określonego serwera proxy HTTP do komunikowania się z platformą Azure, można to zrobić w następujący sposób.

   Utwórz plik o nazwie `proxyservice.exe.config` w pliku `%ProgramFiles%\Azure AD Password Protection Proxy\Service` folder o następującej zawartości:

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

   Jeśli Twój serwer proxy HTTP wymaga uwierzytelnienia, Dodaj znacznik useDefaultCredentials w następujący sposób:

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

   W obu przypadkach należy zamienić `http://yourhttpproxy.com:8080` adres i port serwera proxy HTTP określone.

   Jeśli Twój serwer proxy HTTP jest skonfigurowany przy użyciu zasad autoryzacji, można udzielić dostępu, aby konto komputera usługi Active Directory z komputerem obsługującym usługę serwera Proxy ochrony haseł usługi Azure AD.

   Należy zatrzymać i ponownie uruchom usługę serwera Proxy ochrony haseł usługi Azure AD po tworzenia lub aktualizowania `proxyservice.exe.config` pliku.

   Usługę serwera Proxy ochrony haseł usługi Azure AD nie obsługuje użycia określonego poświadczenia do łączenia się z serwera proxy HTTP.

7. Opcjonalnie: Konfigurowanie usługi Serwer Proxy ochrony haseł usługi Azure AD do nasłuchiwania na konkretnym porcie.
   * RPC za pośrednictwem protokołu TCP jest używany przez oprogramowanie agenta ochrony kontrolera domeny haseł usługi Azure AD na kontrolerach domeny, do komunikacji z usługą serwera Proxy ochrony haseł usługi Azure AD. Domyślnie usługa serwera Proxy ochrony haseł usługi Azure AD nasłuchuje na wszystkie dostępne dynamiczne końcowych wywołań RPC. Jeśli to konieczne ze względu na topologii sieci i wymagania dotyczące zapory, usługi zamiast tego można skonfigurować do nasłuchiwania na konkretnym porcie TCP.
      * Aby skonfigurować usługę, aby działała z portu statycznego, należy użyć `Set-AzureADPasswordProtectionProxyConfiguration` polecenia cmdlet.
         ```PowerShell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
         ```

         > [!WARNING]
         > Należy zatrzymać i ponownie uruchomić usługę, aby zmiany zaczęły obowiązywać.

      * Aby skonfigurować usługę, aby była uruchamiana z portów dynamicznych, użyj tej samej procedury ale StaticPort ponownie ustawiony na wartość zero, w następujący sposób:
         ```PowerShell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
         ```

         > [!WARNING]
         > Należy zatrzymać i ponownie uruchomić usługę, aby zmiany zaczęły obowiązywać.

   > [!NOTE]
   > Usługa Proxy ochrony haseł usługi Azure AD wymaga ręcznego ponownego uruchomienia po każdej zmianie konfiguracji portu. Nie jest konieczne ponowne uruchomienie kontrolera domeny oprogramowani usługi uruchomione na kontrolerach domeny, po wprowadzeniu zmian w konfiguracji tego rodzaju.

   * Bieżąca konfiguracja usługi może być odpytywany za pomocą `Get-AzureADPasswordProtectionProxyConfiguration` jak w poniższym przykładzie przedstawiono polecenie cmdlet:

      ```PowerShell
      Get-AzureADPasswordProtectionProxyConfiguration | fl

      ServiceName : AzureADPasswordProtectionProxy
      DisplayName : Azure AD password protection Proxy
      StaticPort  : 0
      ```

### <a name="install-the-azure-ad-password-protection-dc-agent-service"></a>Instalacja usługi agenta ochrony haseł usługi Azure AD kontrolera domeny

   Instalacja kontrolera domeny ochrony haseł usługi Azure AD agenta usługi oprogramowania przy użyciu `AzureADPasswordProtectionDCAgent.msi` pakietu MSI

   Instalacja oprogramowania wymaga ponownego uruchomienia po instalacji i odinstalować ze względu na wymagania systemu operacyjnego tylko załadowane lub zwolnione po ponownym rozruchu biblioteki DLL filtru haseł.

   Możliwe jest, aby zainstalować usługę agenta kontrolera domeny na komputerze, który nie jest jeszcze kontrolera domeny. W tym przypadku usługa zostanie uruchomiona i wykonywania, ale będą, w przeciwnym razie być nieaktywne do momentu po komputer zostanie podwyższony do kontrolera domeny.

   Instalacja oprogramowania mogą zostać zautomatyzowane przy użyciu standardowych procedur MSI, na przykład:

   `msiexec.exe /i AzureADPasswordProtectionDCAgent.msi /quiet /qn`

   > [!WARNING]
   > Powyższe przykładowe msiexec polecenie spowoduje natychmiastowe ponowne uruchomienie; można tego uniknąć, określając `/norestart` flagi.

Po zainstalowaniu na kontrolerze domeny i ponownie uruchomiony, instalacja oprogramowania agenta ochrony kontrolera domeny haseł usługi Azure AD została zakończona. Konfiguracja nie jest wymagane, czy możliwa.

## <a name="multiple-forest-deployments"></a>Wielu wdrożeń będących częścią lasu

Nie ma żadnych dodatkowych wymagań, aby wdrożyć ochrony haseł usługi Azure AD w wielu lasach. Zgodnie z opisem w sekcji Wdrażanie pojedynczego lasu, każdy las jest skonfigurowany niezależnie. Każdy serwer Proxy ochrony haseł usługi Azure AD może obsługiwać tylko kontrolery domeny z lasu, który jest dołączony do. Oprogramowanie ochrony haseł usługi Azure AD w danym lesie nie rozpoznaje oprogramowania ochrony haseł usługi Azure AD, wdrożonego w innym lesie, niezależnie od konfiguracji relacji zaufania usługi Active Directory.

## <a name="read-only-domain-controllers"></a>Kontrolery domeny tylko do odczytu

Changes\sets hasło nigdy nie są przetwarzane i utrwalane na kontrolerach domeny tylko do odczytu (RODC); Zamiast tego są one przekazywane do zapisywalne kontrolery domeny. W związku z tym nie ma potrzeby zainstalować oprogramowanie agenta kontrolera domeny na kontrolery RODC.

## <a name="high-availability"></a>Wysoka dostępność

Głównym problemem przy zapewnieniu wysokiej dostępności ochrony haseł usługi Azure AD jest dostępność serwerów proxy, podczas próby pobrania nowych zasad lub innych danych z platformy Azure kontrolerów domeny w lesie. Każdy agent kontrolera domeny używa algorytmu prostym stylem działanie okrężne, podejmując decyzję o który serwer proxy do wywołania i pomija za pośrednictwem serwerów proxy, które nie odpowiadają. Większości pełni połączone wdrożeniach usługi Active Directory z dobrej kondycji replikacji (z katalogu i folderu sysvol stanu) serwery proxy dwóch (2) powinna być wystarczająca do zapewnienia dostępności i w związku z tym w odpowiednim czasie pliki do pobrania nowych zasad i innych danych. Żądana dodatkowych serwerów, może być wdrożony jako serwera proxy.

Zwykle problemy związane z wysoką dostępność zostaną skorygowane przez projekt oprogramowania agenta kontrolera domeny. Agent kontroler domeny przechowuje lokalnej pamięci podręcznej najbardziej ostatnio pobrane zasady haseł. Nawet wtedy, gdy wszystkie zarejestrowane serwery proxy stać się niedostępne z jakiegokolwiek powodu, agentach DC w dalszym ciągu wymuszać swoje zasady haseł w pamięci podręcznej. Częstotliwość aktualizacji uzasadnione dla zasad haseł w dużych wdrożeniach jest zwykle od kolejności, dni, a nie godzin lub mniej. W związku z tym krótki awarii serwery proxy nie powodują znaczącego wpływu na działanie funkcji ochrony haseł usługi Azure AD lub jego korzyści w zakresie zabezpieczeń.

## <a name="next-steps"></a>Kolejne kroki

Po zainstalowaniu usług wymaganych do ochrony haseł usługi Azure AD na serwerach lokalnych wykonać czynności opisane [po instalacji, konfiguracji i zbieranie raportowanie informacji o](howto-password-ban-bad-on-premises-operations.md) do ukończenia danego wdrożenia.

[Omówienie pojęć dotyczących ochrony haseł usługi Azure AD](concept-password-ban-bad-on-premises.md)
