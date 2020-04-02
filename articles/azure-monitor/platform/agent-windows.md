---
title: Łączenie komputerów z systemem Windows z monitorem platformy Azure | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób łączenia komputerów z systemem Windows hostowanych w innych chmurach lub lokalnie z usługą Azure Monitor za pomocą agenta usługi Log Analytics dla systemu Windows.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/07/2019
ms.openlocfilehash: 65a6f51d0eef28ea33adcc755d3d51f1e06a5341
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80528336"
---
# <a name="connect-windows-computers-to-azure-monitor"></a>Łączenie komputerów z systemem Windows z monitorem platformy Azure

Aby monitorować maszyny wirtualne lub komputery fizyczne w lokalnym centrum danych lub innym środowisku chmury i zarządzać nimi za pomocą usługi Azure Monitor, należy wdrożyć agenta usługi Log Analytics (nazywanego również agentem monitorowania firmy Microsoft (MMA)) i skonfigurować go do raportowania do co najmniej jednego obszaru roboczego usługi Log Analytics. Agent obsługuje również rolę proces roboczy hybrydowego dla usługi Azure Automation.  

Na monitorowanym komputerze z systemem Windows agent jest wymieniony jako usługa Microsoft Monitoring Agent. Usługa Microsoft Monitoring Agent zbiera zdarzenia z plików dziennika i dziennika zdarzeń systemu Windows, danych wydajności i innych danych telemetrycznych. Nawet wtedy, gdy agent nie może komunikować się z usługą Azure Monitor, do którym raportuje, agent kontynuuje uruchamianie i kolejkuje zebrane dane na dysku monitorowanego komputera. Po przywróceniu połączenia usługa Microsoft Monitoring Agent wysyła zebrane dane do usługi.

Agent może być zainstalowany przy użyciu jednej z następujących metod. W większości instalacji jest używana kombinacja tych metod w celu zainstalowania różnych zestawów komputerów, zgodnie z potrzebami danej konfiguracji.  Szczegółowe informacje na temat korzystania z każdej metody są podane w dalszej części artykułu.

* Instalacja ręczna. Instalator jest uruchamiany ręcznie na komputerze za pomocą kreatora konfiguracji, z wiersza polecenia lub wdrażany przy użyciu istniejącego narzędzia do dystrybucji oprogramowania.
* Konfiguracja żądanego stanu automatyzacji usługi Azure (DSC). Korzystanie z dsc w usłudze Azure Automation ze skryptem dla komputerów z systemem Windows już wdrożonych w twoim środowisku.  
* Skrypt programu PowerShell.
* Szablon Menedżera zasobów dla maszyn wirtualnych z systemem Windows lokalnie w usłudze Azure Stack. 

>[!NOTE]
>Usługa Azure Security Center (ASC) zależy od agenta monitorowania firmy Microsoft (nazywanego również agentem systemu Windows usługi Log Analytics) i zainstaluje go i skonfiguruje do raportowania do obszaru roboczego usługi Log Analytics w ramach jego wdrażania. Asc zawiera opcję automatycznego inicjowania obsługi administracyjnej, która umożliwia automatyczną instalację agenta systemu Windows usługi Log Analytics na wszystkich maszynach wirtualnych w ramach subskrypcji i konfiguruje go do raportowania do określonego obszaru roboczego. Aby uzyskać więcej informacji na temat tej opcji, zobacz [Włączanie automatycznego inicjowania obsługi administracyjnej agenta usługi Log Analytics](../../security-center/security-center-enable-data-collection.md#auto-provision-mma).
>

Jeśli trzeba skonfigurować agenta do raportowania do więcej niż jednego obszaru roboczego, nie można tego wykonać podczas wstępnej konfiguracji, dopiero później, [aktualizując](agent-manage.md#adding-or-removing-a-workspace)ustawienia z Panelu sterowania lub programu PowerShell zgodnie z opisem w temacie Dodawanie lub usuwanie obszaru roboczego .  

Aby poznać obsługiwaną konfigurację, przejrzyj tematy dotyczące [obsługiwanych systemów operacyjnych Windows](log-analytics-agent.md#supported-windows-operating-systems) oraz [konfiguracji zapory sieciowej](log-analytics-agent.md#firewall-requirements).

## <a name="obtain-workspace-id-and-key"></a>Uzyskiwanie identyfikatora i klucza obszaru roboczego
Przed zainstalowaniem agenta usługi Log Analytics dla systemu Windows potrzebny jest identyfikator obszaru roboczego i klucz obszaru roboczego usługi Log Analytics.  Te informacje są wymagane podczas instalacji z każdej metody instalacji, aby poprawnie skonfigurować agenta i upewnić się, że może pomyślnie komunikować się z usługą Azure Monitor w chmurze komercyjnej platformy Azure i us government. 

1. W witrynie Azure portal wyszukaj i wybierz **obszary robocze usługi Log Analytics**.
2. Na liście obszarów roboczych usługi Log Analytics wybierz obszar roboczy, do którego zamierzasz skonfigurować agenta, do którego ma zostać zgłoszenia.
3. Wybierz pozycję **Ustawienia zaawansowane**.<br><br> ![Ustawienia zaawansowane usługi Log Analytics](media/agent-windows/log-analytics-advanced-settings-01.png)<br><br>  
4. Wybierz **Połączone źródła**, a następnie **Serwery Windows**.   
5. Skopiuj i wklej do ulubionego **edytora, identyfikatora obszaru roboczego** i **klucza podstawowego.**    
   
## <a name="configure-agent-to-use-tls-12"></a>Konfigurowanie agenta do używania protokołu TLS 1.2
Aby skonfigurować użycie protokołu [TLS 1.2](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12) do komunikacji między agentem systemu Windows a usługą analizy dzienników, można wykonać poniższe czynności, aby włączyć go przed zainstalowaniem agenta na maszynie wirtualnej lub później.

>[!NOTE]
>Jeśli konfigurujesz maszynę wirtualną z systemem Windows Server 2008 SP2 x64 do używania protokołu TLS 1.2, przed wykonaniem poniższych czynności należy najpierw zainstalować [następującą aktualizację pomocy technicznej dotycząca podpisywania kodu SHA-2.](https://support.microsoft.com/help/4474419/sha-2-code-signing-support-update) 
>

1. Znajdź następujący podklucz rejestru: **HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols**
2. Tworzenie podklucza w obszarze **Protokoły** dla protokołu TLS 1.2 **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2**
3. Utwórz podklucz **klienta** w podkluczietu wersji protokołu TLS 1.2, który został utworzony wcześniej. Na przykład **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client**.
4. Tworzenie następujących wartości DWORD w obszarze **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client**:

    * **Włączono** [Wartość = 1]
    * **DisabledByDefault** [Wartość = 0]  

Skonfiguruj program .NET Framework 4.6 lub nowszy, aby obsługiwać bezpieczną kryptografię, ponieważ domyślnie jest wyłączona. [Silna kryptografia](https://docs.microsoft.com/dotnet/framework/network-programming/tls#schusestrongcrypto) używa bezpieczniejszych protokołów sieciowych, takich jak TLS 1.2, i blokuje protokoły, które nie są bezpieczne. 

1. Znajdź następujący podklucz rejestru: **\\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft . NETFramework\v4.0.30319**.  
2. Utwórz wartość DWORD **SchUseStrongCrypto** pod tym podkluczem o wartości **1**.  
3. Znajdź następujący podklucz rejestru: **HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\\. NETFramework\v4.0.30319**.  
4. Utwórz wartość DWORD **SchUseStrongCrypto** pod tym podkluczem o wartości **1**. 
5. Uruchom ponownie system, aby ustawienia zostały zastosowane. 

## <a name="install-the-agent-using-setup-wizard"></a>Instalowanie agenta za pomocą kreatora konfiguracji
Poniższe kroki zainstalować i skonfigurować agenta usługi Log Analytics w chmurze Azure i azure dla instytucji rządowych przy użyciu kreatora konfiguracji dla agenta na komputerze. Jeśli chcesz dowiedzieć się, jak skonfigurować agenta do raportowania do grupy zarządzania programu System Center Operations Manager, zobacz [wdrażanie agenta programu Operations Manager za pomocą Kreatora instalacji agenta](https://docs.microsoft.com/system-center/scom/manage-deploy-windows-agent-manually#to-deploy-the-operations-manager-agent-with-the-agent-setup-wizard).

1. W obszarze roboczym analiza dzienników ze strony **Serwery systemu Windows,** do której nawładniesz wcześniej, wybierz odpowiednią wersję **programu Pobierz agenta Windows** do pobrania w zależności od architektury procesora systemu operacyjnego Windows.   
2. Uruchom Instalatora, aby zainstalować agenta na komputerze.
2. Na **stronie powitalnej** kliknij przycisk **Dalej**.
3. Na stronie **Postanowienia licencyjne** zapoznaj się z postanowieniami licencyjnymi, a następnie kliknij przycisk **Zgadzam się**.
4. Na stronie **Folder docelowy** zmień lub pozostaw domyślny folder instalacji, a następnie kliknij przycisk **Dalej**.
5. Na stronie **Opcje instalacji agenta** wybierz połączenie agenta z usługą Azure Log Analytics, a następnie kliknij przycisk **Dalej**.   
6. Na stronie **Azure Log Analytics** wykonaj następujące czynności:
   1. Wklej skopiowany wcześniej **identyfikator obszaru roboczego** i **klucz obszaru roboczego (klucz podstawowy)**.  Jeśli komputer powinien wysyłać raporty do obszaru roboczego usługi Log Analytics w chmurze Azure dla instytucji rządowych, wybierz **Wersja platformy Azure dla administracji USA** z listy rozwijanej **Azure Cloud**.  
   2. Jeśli komputer musi komunikować się z usługą Log Analytics za pośrednictwem serwera proxy, kliknij pozycję **Zaawansowane** i podaj adres URL i numer portu serwera proxy.  Jeśli Twój serwer proxy wymaga uwierzytelniania, wpisz nazwę użytkownika i hasło, aby uwierzytelnić się na serwerze proxy, a następnie kliknij przycisk **Dalej**.  
7. Po zakończeniu podawania niezbędnych ustawień konfiguracji kliknij przycisk **Dalej**.<br><br> ![Wklejanie klucza podstawowego i identyfikatora obszaru roboczego](media/agent-windows/log-analytics-mma-setup-laworkspace.png)<br><br>
8. Na stronie **Gotowe do zainstalowania** przejrzyj wybrane opcje, a następnie kliknij pozycję **Zainstaluj**.
9. Na stronie **Konfiguracja została zakończona pomyślnie** kliknij przycisk **Zakończ**.

Po ukończeniu instalacji program **Microsoft Monitoring Agent** będzie wyświetlany w **Panelu sterowania**. Aby potwierdzić, że zgłasza się do usługi Log Analytics, [przejrzyj weryfikację łączności agenta z usługi Log Analytics](#verify-agent-connectivity-to-log-analytics). 

## <a name="install-the-agent-using-the-command-line"></a>Instalowanie agenta przy użyciu wiersza polecenia
Pobrany plik dla agenta jest samodzielnym pakietem instalacyjnym.  Program instalacyjny dla agenta i pliki pomocnicze są zawarte w pakiecie i muszą być wyodrębnione w celu prawidłowego zainstalowania za pomocą wiersza polecenia pokazano w poniższych przykładach.    

>[!NOTE]
>Jeśli chcesz uaktualnić agenta, musisz użyć interfejsu API skryptów usługi Log Analytics. Zobacz temat [Zarządzanie i obsługa agenta usługi Log Analytics dla systemów Windows i Linux,](agent-manage.md) aby uzyskać więcej informacji.

W poniższej tabeli przedstawiono określone parametry obsługiwane przez konfigurację dla agenta, w tym po wdrożeniu przy użyciu usługi Automation DSC.

|Opcje specyficzne dla MMA                   |Uwagi         |
|---------------------------------------|--------------|
| NOAPM=1                               | Parametr opcjonalny. Instaluje agenta bez monitorowania wydajności aplikacji .NET.|   
|ADD_OPINSIGHTS_WORKSPACE               | 1 = Konfigurowanie agenta do raportowania do obszaru roboczego                |
|OPINSIGHTS_WORKSPACE_ID                | Identyfikator obszaru roboczego (guid) dla obszaru roboczego do dodania                    |
|OPINSIGHTS_WORKSPACE_KEY               | Klucz obszaru roboczego używany do wstępnego uwierzytelniania za pomocą obszaru roboczego |
|OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE  | Określanie środowiska chmury, w którym znajduje się obszar roboczy <br> 0 = Chmura komercyjna platformy Azure (domyślnie) <br> 1 = Azure Government |
|OPINSIGHTS_PROXY_URL               | Identyfikator URI dla serwera proxy do użycia |
|OPINSIGHTS_PROXY_USERNAME               | Nazwa użytkownika, aby uzyskać dostęp do uwierzytelnionego serwera proxy |
|OPINSIGHTS_PROXY_PASSWORD               | Hasło dostępu do uwierzytelnionego serwera proxy |

1. Aby wyodrębnić pliki instalacyjne agenta, `MMASetup-<platform>.exe /c` z wiersza polecenia z podwyższonym poziomem uprawnień zostanie uruchomiony monit o ścieżkę wyodrębniania plików.  Alternatywnie można określić ścieżkę, `MMASetup-<platform>.exe /c /t:<Full Path>`przekazując argumenty .  
2. Aby dyskretnie zainstalować agenta i skonfigurować go do raportowania do obszaru roboczego w komercyjnej chmurze platformy Azure, z folderu wyodrębnione pliki instalacyjne do typu: 
   
     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=0 OPINSIGHTS_WORKSPACE_ID="<your workspace ID>" OPINSIGHTS_WORKSPACE_KEY="<your workspace key>" AcceptEndUserLicenseAgreement=1
    ```

   lub aby skonfigurować agenta do raportowania do chmury instytucji administracji amerykańskiej platformy Azure, wpisz: 

     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=1 OPINSIGHTS_WORKSPACE_ID="<your workspace ID>" OPINSIGHTS_WORKSPACE_KEY="<your workspace key>" AcceptEndUserLicenseAgreement=1
    ```
    >[!NOTE]
    >Wartości ciągu parametrów *OPINSIGHTS_WORKSPACE_ID* i *OPINSIGHTS_WORKSPACE_KEY* muszą być hermetyzowane w cudzysłowach, aby poinstruować Instalatora Windows, aby interprit jako prawidłowe opcje dla pakietu. 

## <a name="install-the-agent-using-dsc-in-azure-automation"></a>Instalowanie agenta przy użyciu dsc w usłudze Azure Automation

Poniższy przykład skryptu służy do instalowania agenta przy użyciu usługi Azure Automation DSC.   Jeśli nie masz konta automatyzacji, zobacz Wprowadzenie do [usługi Azure Automation,](/azure/automation/) aby zrozumieć wymagania i kroki dotyczące tworzenia konta automatyzacji wymagane przed użyciem usługi Automation DSC.  Jeśli nie znasz rozwiązania Automation DSC, zapoznaj się z recenzją [Wprowadzenie do systemu Automation DSC](../../automation/automation-dsc-getting-started.md).

Poniższy przykład instaluje agenta 64-bitowego, identyfikowanego przez `URI` wartość. Można również użyć wersji 32-bitowej, zastępując wartość URI. Identyfikatory URI dla obu wersji to:

- 64-bitowy agent systemu Windows -https://go.microsoft.com/fwlink/?LinkId=828603
- 32-bitowy agent systemu Windows -https://go.microsoft.com/fwlink/?LinkId=828604


>[!NOTE]
>Ta procedura i przykład skryptu nie obsługuje uaktualniania agenta już wdrożonego na komputerze z systemem Windows.

32-bitowe i 64-bitowe wersje pakietu agenta mają różne kody produktów, a nowe wersje również mają unikatową wartość.  Kod produktu jest identyfikatorem GUID, który jest główną identyfikacją aplikacji lub produktu i jest reprezentowany przez właściwość **ProductCode** Instalatora Windows.  Wartość `ProductId` w skrypcie **MMAgent.ps1** musi być zgodna z kodem produktu z 32-bitowego lub 64-bitowego pakietu instalatora agenta.

Aby pobrać kod produktu bezpośrednio z pakietu instalacji agenta, można użyć programu Orca.exe ze [składników zestawu Windows SDK dla deweloperów instalatora windows,](https://msdn.microsoft.com/library/windows/desktop/aa370834%28v=vs.85%29.aspx) który jest składnikiem zestawu Windows Software Development Kit lub przy użyciu programu PowerShell zgodnie z [przykładowym skryptem](https://www.scconfigmgr.com/2014/08/22/how-to-get-msi-file-information-with-powershell/) napisanym przez microsoft valuable professional (MVP).  Dla obu podejść należy najpierw wyodrębnić plik **MOMagent.msi** z pakietu instalacyjnego MMASetup.  Jest to pokazane wcześniej w pierwszym kroku w sekcji [Zainstaluj agenta za pomocą wiersza polecenia](#install-the-agent-using-the-command-line).  

1. Zaimportuj moduł xPSDesiredStateConfiguration DSC z [https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration](https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) usługi Azure Automation.  
1. Tworzenie zasobów zmiennych usługi Azure Automation dla *OPSINSIGHTS_WS_ID* i *OPSINSIGHTS_WS_KEY*. Ustaw *OPSINSIGHTS_WS_ID* identyfikator obszaru roboczego usługi Log Analytics i ustaw *OPSINSIGHTS_WS_KEY* klucza podstawowego obszaru roboczego.
1. Skopiuj skrypt i zapisz go jako MMAgent.ps1.

   ```powershell
   Configuration MMAgent
   {
       $OIPackageLocalPath = "C:\Deploy\MMASetup-AMD64.exe"
       $OPSINSIGHTS_WS_ID = Get-AutomationVariable -Name "OPSINSIGHTS_WS_ID"
       $OPSINSIGHTS_WS_KEY = Get-AutomationVariable -Name "OPSINSIGHTS_WS_KEY"

       Import-DscResource -ModuleName xPSDesiredStateConfiguration
       Import-DscResource -ModuleName PSDesiredStateConfiguration

       Node OMSnode {
           Service OIService
           {
               Name = "HealthService"
               State = "Running"
               DependsOn = "[Package]OI"
           }

           xRemoteFile OIPackage {
               Uri = "https://go.microsoft.com/fwlink/?LinkId=828603"
               DestinationPath = $OIPackageLocalPath
           }

           Package OI {
               Ensure = "Present"
               Path  = $OIPackageLocalPath
               Name = "Microsoft Monitoring Agent"
               ProductId = "8A7F2C51-4C7D-4BFD-9014-91D11F24AAE2"
               Arguments = '/C:"setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=' + $OPSINSIGHTS_WS_ID + '      OPINSIGHTS_WORKSPACE_KEY=' + $OPSINSIGHTS_WS_KEY + ' AcceptEndUserLicenseAgreement=1"'
               DependsOn = "[xRemoteFile]OIPackage"
           }
       }
   }

   ```

4. Zaktualizuj `ProductId` wartość w skrypcie za pomocą kodu produktu wyodrębnionego z najnowszej wersji pakietu instalacji agenta przy użyciu metod zalecanych wcześniej. 
5. [Zaimportuj skrypt konfiguracyjny MMAgent.ps1](../../automation/automation-dsc-getting-started.md#importing-a-configuration-into-azure-automation) do swojego konta automatyzacji. 
5. [Przypisz komputer lub węzeł systemu Windows](../../automation/automation-dsc-getting-started.md#onboarding-an-azure-vm-for-management-with-azure-automation-state-configuration) do konfiguracji. W ciągu 15 minut węzeł sprawdza jego konfigurację i agent jest wypychany do węzła.

## <a name="verify-agent-connectivity-to-log-analytics"></a>Weryfikowanie łączności agenta z analizą dzienników

Po zakończeniu instalacji agenta sprawdzenie, czy jest on pomyślnie połączony, a raportowanie można wykonać na dwa sposoby.  

W **Panelu sterowania** na komputerze znajdź element **Microsoft Monitoring Agent**.  Wybierz go i na karcie **Usługi Azure Log Analytics** agent powinien wyświetlić komunikat z informacją: Agent monitorowania firmy Microsoft pomyślnie połączył się z **usługą Microsoft Operations Management Suite.**<br><br> ![Stan połączenia programu MMA z usługą Log Analytics](media/agent-windows/log-analytics-mma-laworkspace-status.png)

Można również wykonać proste zapytanie dziennika w witrynie Azure portal.  

1. W witrynie Azure portal wyszukaj i wybierz **pozycję Monitor**.
1. Wybierz **pozycję Dzienniki** w menu.
1. W okienku **Dzienniki** w typie pola kwerendy:  

    ```
    Heartbeat 
    | where Category == "Direct Agent" 
    | where TimeGenerated > ago(30m)  
    ```

W zwróconych wynikach wyszukiwania powinny być widoczne rekordy pulsu dla komputera wskazujące, że jest połączony i raportowanie do usługi.   

## <a name="next-steps"></a>Następne kroki

- Przejrzyj [zarządzanie i utrzymywanie agenta usługi Log Analytics dla systemów Windows i Linux,](agent-manage.md) aby dowiedzieć się, jak ponownie skonfigurować, uaktualnić lub usunąć agenta z maszyny wirtualnej.

- Przejrzyj [rozwiązywanie problemów z agentem systemu Windows,](agent-windows-troubleshoot.md) jeśli wystąpią problemy podczas instalowania agenta lub zarządzania nim.
