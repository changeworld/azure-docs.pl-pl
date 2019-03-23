---
title: Łączenie komputerów Windows z usługą Azure Log Analytics | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak połączyć komputery Windows hostowanych w innych chmurach lub środowiska lokalnego do usługi Log Analytics za pomocą programu Microsoft Monitoring Agent (MMA).
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: magoedte
ms.openlocfilehash: f9df65d143fbb0eaf6276a0f38971e19c0741786
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370962"
---
# <a name="connect-windows-computers-to-azure-monitor"></a>Łączenie komputerów Windows z usługą Azure Monitor

Aby monitorować i zarządzać maszyn wirtualnych lub fizycznych komputerów w lokalnym centrum danych lub w innym środowisku chmury dzięki usłudze Azure Monitor, należy wdrożyć agenta Log Analytics (nazywane także jako Microsoft Monitoring Agent (MMA)) i skonfigurować go do raporty do jednego lub więcej obszarów roboczych usługi Log Analytics. Agent obsługuje również roli hybrydowego procesu roboczego Runbook usługi Azure Automation.  

Na monitorowanym komputerze Windows agent jest wyświetlany jako usługa Microsoft Monitoring Agent. Usługa Microsoft Monitoring Agent zbiera dane zdarzeń z plików dziennika i Windows dziennika zdarzeń, dane dotyczące wydajności i inną telemetrią. Nawet wtedy, gdy agent nie może komunikować się z usługą Azure Monitor wysyła raporty, agent będzie nadal działać i umieszcza w kolejce zebranych danych na dysku monitorowanego komputera. Po przywróceniu połączenia usługa Microsoft Monitoring Agent wysyła zebrane dane do usługi.

Agenta można zainstalować przy użyciu jednej z następujących metod. Większości instalacji jest używana kombinacja tych metod w celu zainstalowania różnych zestawów komputerów, zgodnie z potrzebami.  Szczegółowe informacje na temat korzystania z poszczególnych metod znajdują się w dalszej części tego artykułu.

* Instalacja ręczna. Instalator jest ręcznie uruchomić na komputerze, korzystając z Kreatora instalacji z wiersza polecenia lub wdrażany za pomocą istniejącego narzędzia do dystrybucji oprogramowania.
* Usługa Azure Automation Desired State Configuration (DSC). Korzystanie z DSC w usłudze Azure Automation za pomocą skryptu programu Windows komputerów już wdrożony w środowisku.  
* Skrypt programu PowerShell.
* Szablon usługi Resource Manager dla maszyn wirtualnych z systemem Windows w środowisku lokalnym w usłudze Azure Stack.  

>[!NOTE]
>Usługa Azure Security Center (ASC) jest zależna od programu Microsoft Monitoring Agent (nazywane również agenta Log Analytics Windows) i instalowanych i konfigurowanych go do raportu do obszaru roboczego usługi Log Analytics podczas jego wdrażania. ASC obejmuje opcji automatycznego inicjowania obsługi administracyjnej, co umożliwia automatyczną instalację agenta Log Analytics Windows na wszystkich maszynach wirtualnych w ramach subskrypcji i konfiguruje go w celu raportowania do określonego obszaru roboczego. Aby uzyskać więcej informacji na temat tej opcji, zobacz [Włączanie automatycznej aprowizacji agenta usługi Log Analytics](../../security-center/security-center-enable-data-collection.md#enable-automatic-provisioning-of-microsoft-monitoring-agent-).
>

Aby poznać obsługiwaną konfigurację, przejrzyj tematy dotyczące [obsługiwanych systemów operacyjnych Windows](log-analytics-agent.md#supported-windows-operating-systems) oraz [konfiguracji zapory sieciowej](log-analytics-agent.md#network-firewall-requirements).

## <a name="obtain-workspace-id-and-key"></a>Uzyskiwanie identyfikatora i klucza obszaru roboczego
Przed zainstalowaniem agenta usługi Log Analytics dla Windows, potrzebne są identyfikator obszaru roboczego i klucz obszaru roboczego usługi Log Analytics.  Te informacje są wymagane podczas instalacji z poszczególnych metod instalacji w celu poprawnego skonfigurowania agenta i upewnij się, że może się skutecznie komunikować z usługi Azure Monitor na platformie Azure komercyjnych i w chmurze dla instytucji rządowych USA.  

1. W witrynie Azure Portal kliknij pozycję **Wszystkie usługi**. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz pozycję **Log Analytics**.
2. Na liście obszarów roboczych usługi Log Analytics wybierz obszar roboczy, którego zamierzasz na skonfigurowanie agenta raportowania do.
3. Wybierz pozycję **Ustawienia zaawansowane**.<br><br> ![Ustawienia zaawansowane usługi Log Analytics](media/agent-windows/log-analytics-advanced-settings-01.png)<br><br>  
4. Wybierz **Połączone źródła**, a następnie **Serwery Windows**.   
5. Skopiuj i Wklej w swoim ulubionym edytorze **identyfikator obszaru roboczego** i **klucz podstawowy**.    
   
## <a name="configure-agent-to-use-tls-12"></a>Skonfiguruj agenta do użycia protokołu TLS 1.2
Aby skonfigurować używanie [protokołu TLS 1.2](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12) protokołu komunikacji między agentem Windows i usługi Log Analytics, możesz wykonać poniższe kroki, aby włączyć, zanim agent jest zainstalowany na maszynie wirtualnej lub później.   

1. Znajdź następujący podklucz rejestru: **HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols**
2. Utwórz podklucz w obszarze **protokołów** protokół TLS 1.2 **HKLM\System\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2**
3. Tworzenie **klienta** podkluczy w podkluczu wersji protokołu TLS 1.2 została utworzona wcześniej. Na przykład **HKLM\System\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client**.
4. Utwórz następujące wartości DWORD pod **HKLM\System\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client**:

    * **Włączone** [wartość = 1]
    * **DisabledByDefault** [wartość = 0]  

Konfigurowanie programu .NET Framework 4.6 lub później do obsługi bezpiecznego szyfrowania, ponieważ domyślnie go jest wyłączona. [Silnej kryptografii](https://docs.microsoft.com/dotnet/framework/network-programming/tls#schusestrongcrypto) bezpieczniejsze protokołów sieciowych, takich jak protokół TLS 1.2 i blokuje protokołów, które nie są bezpieczne. 

1. Znajdź następujący podklucz rejestru: **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\\.NETFramework\v4.0.30319**.  
2. Utwórz wartość DWORD **SchUseStrongCrypto** tego podklucza o wartości **1**.  
3. Znajdź następujący podklucz rejestru: **HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\\.NETFramework\v4.0.30319**.  
4. Utwórz wartość DWORD **SchUseStrongCrypto** tego podklucza o wartości **1**. 
5. Ponowne uruchomienie systemu, aby ustawienia zaczęły obowiązywać. 

## <a name="install-the-agent-using-setup-wizard"></a>Instalowanie agenta za pomocą Kreatora instalacji
Poniższe kroki instalowania i konfigurowania agenta usługi Log Analytics w chmurze platformy Azure i Azure dla instytucji rządowych za pomocą Kreatora instalacji agenta na komputerze. Jeśli chcesz dowiedzieć się, jak skonfigurować agenta Aby również raport z grupą zarządzania programu System Center Operations Manager, zobacz [wdrożyć agenta programu Operations Manager przy użyciu Kreatora instalacji agenta](https://docs.microsoft.com/system-center/scom/manage-deploy-windows-agent-manually#to-deploy-the-operations-manager-agent-with-the-agent-setup-wizard).

1. W obszarze roboczym usługi Log Analytics z **serwerów Windows** strony przejście wcześniej, wybierz odpowiedni **Pobierz agenta Windows** wersję do pobrania w zależności od architektury procesora system operacyjny Windows.   
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

Po ukończeniu instalacji program **Microsoft Monitoring Agent** będzie wyświetlany w **Panelu sterowania**. Aby upewnić się, jest raportowanie do usługi Log Analytics, zapoznaj się z [sprawdzić połączenie agenta z usługą Log Analytics](#verify-agent-connectivity-to-log-analytics). 

## <a name="install-the-agent-using-the-command-line"></a>Instalacja agenta przy użyciu wiersza polecenia
Pobrany plik agenta jest samodzielny pakiet instalacyjny.  Program instalacyjny programu agent i pliki pomocnicze są zawarte w pakiecie i muszą zostać wyodrębnione, aby można było poprawnie zainstalować przy użyciu wiersza polecenia, pokazano w poniższych przykładach.    

>[!NOTE]
>Jeśli chcesz uaktualnić agenta, należy użyć usługi Log Analytics, interfejs API obsługi skryptów. Zobacz temat [zachowaniu agenta usługi Log Analytics dla Windows i Linux i zarządzanie nimi](agent-manage.md) Aby uzyskać więcej informacji.

W poniższej tabeli wymieniono określone parametry, które są obsługiwane przez Instalatora dla agenta, w tym podczas wdrażania za pomocą DSC usługi Automation.

|Opcje programu MMA                   |Uwagi         |
|---------------------------------------|--------------|
| NOAPM=1                               | Parametr opcjonalny. Instaluje agenta bez .NET Application Performance Monitoring.|   
|ADD_OPINSIGHTS_WORKSPACE               | 1 = Konfigurowanie agenta w celu przekazywania informacji do obszaru roboczego                |
|OPINSIGHTS_WORKSPACE_ID                | Identyfikator obszaru roboczego (guid) dla obszaru roboczego można dodać                    |
|OPINSIGHTS_WORKSPACE_KEY               | Klucz obszaru roboczego, używane do początkowego uwierzytelniania z obszarem roboczym |
|OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE  | Określ w środowisku chmury, w którym znajduje się obszar roboczy <br> 0 = komercyjnej chmury azure (ustawienie domyślne) <br> 1 = platformy azure dla instytucji rządowych |
|OPINSIGHTS_PROXY_URL               | Identyfikator URI serwera proxy do użycia |
|OPINSIGHTS_PROXY_USERNAME               | Nazwa użytkownika używana do uzyskania dostępu uwierzytelnionego serwera proxy |
|OPINSIGHTS_PROXY_PASSWORD               | Hasło dostępu uwierzytelnionego serwera proxy |

1. Aby wyodrębnić pliki instalacyjne agenta z wiersza polecenia z podwyższonym poziomem uprawnień uruchom `MMASetup-<platform>.exe /c` i zostanie wyświetlony monit dla ścieżki wyodrębnić pliki do.  Alternatywnie, można określić ścieżkę przez przekazanie argumentów `MMASetup-<platform>.exe /c /t:<Full Path>`.  
2. Aby dyskretnie zainstalować agenta i skonfigurowanie jej do raportu do obszaru roboczego w komercyjnej chmury Azure z folderu zostały wyodrębnione pliki instalacyjne na typ: 
   
     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=0 OPINSIGHTS_WORKSPACE_ID=<your workspace ID> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1
    ```

   lub aby skonfigurować agenta w celu raportowania do chmury Azure instytucji rządowych USA, wpisz: 

     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace ID> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1
    ```

## <a name="install-the-agent-using-dsc-in-azure-automation"></a>Instalowanie agenta za pomocą DSC w usłudze Azure Automation

Poniższy przykładowy skrypt służy do instalowania agenta przy użyciu usługi Azure Automation DSC.   Jeśli nie masz konta usługi Automation, zobacz [Rozpoczynanie pracy z usługą Azure Automation](/azure/automation/) , aby zrozumieć wymagania i procedury służące do tworzenia konta usługi Automation, wymagana przed rozpoczęciem korzystania z usługi Automation DSC.  Jeśli nie znasz dobrze usługi Automation DSC, zapoznaj się z [wprowadzenie do usługi Automation DSC](../../automation/automation-dsc-getting-started.md).

W poniższym przykładzie instalowana agent 64-bitowy, identyfikowane przez `URI` wartość. Można również użyć 32-bitowej wersji, zastępując wartość identyfikatora URI. Identyfikatory URI, dla obu wersji są następujące:

- Agent 64-bitowy Windows- https://go.microsoft.com/fwlink/?LinkId=828603
- Agent 32-bitowa Windows — https://go.microsoft.com/fwlink/?LinkId=828604


>[!NOTE]
>W tym przykładzie procedury i skrypt nie obsługuje uaktualniania agenta już wdrożonych na komputerze Windows.

32-bitowych i 64-bitowe wersje pakietu agenta mają kodów innego produktu i nowe wersje wydane również mieć unikatową wartość.  Kod produktu jest identyfikator GUID jest identyfikator podmiotu zabezpieczeń aplikacji lub produktu, która jest reprezentowana przez Instalatora Windows **ProductCode** właściwości.  `ProductId` Wartość w **MMAgent.ps1** skryptu musi odpowiadać kod produktu za pomocą pakietu Instalatora 32-bitową lub 64-bitowych agentów.

Aby bezpośrednio pobrać kod produktu za pomocą pakietu instalacji agenta, należy użyć Orca.exe z [Windows SDK składników dla Windows Installer deweloperów](https://msdn.microsoft.com/library/windows/desktop/aa370834%28v=vs.85%29.aspx) oznacza to składnik systemu Windows Software Development Kit lub za pomocą Następujący program PowerShell [przykładowy skrypt](https://www.scconfigmgr.com/2014/08/22/how-to-get-msi-file-information-with-powershell/) napisane przez Microsoft Valuable Professional (MVP).  Dla obu podejście, najpierw należy wyodrębnić **MOMagent.msi** plik z pakietu instalacyjnego MMASetup.  Zostało to przedstawione wcześniej w pierwszym krokiem w sekcji [instalowania agenta przy użyciu wiersza polecenia](#install-the-agent-using-the-command-line).  

1. Importuj DSC xPSDesiredStateConfiguration modułu na podstawie [ https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration ](https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) do usługi Azure Automation.  
2.  Tworzenie zmiennych elementów zawartości usługi Azure Automation do *OPSINSIGHTS_WS_ID* i *OPSINSIGHTS_WS_KEY*. Ustaw *OPSINSIGHTS_WS_ID* identyfikator obszaru roboczego usługi Log Analytics i zestaw *OPSINSIGHTS_WS_KEY* do klucza podstawowego obszaru roboczego.
3.  Skopiuj skrypt i zapisz go jako MMAgent.ps1.

    ```PowerShell
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
                Arguments = '/C:"setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=' + $OPSINSIGHTS_WS_ID + ' OPINSIGHTS_WORKSPACE_KEY=' + $OPSINSIGHTS_WS_KEY + ' AcceptEndUserLicenseAgreement=1"'
                DependsOn = "[xRemoteFile]OIPackage"
            }
        }
    }

    ```

4. Aktualizacja `ProductId` wartość w skrypcie kodem produktu wyodrębnione z najnowszą wersję agenta należy zainstalować pakiet przy użyciu metod, zaleca się wcześniej. 
5. [Zaimportuj skrypt konfiguracji MMAgent.ps1](../../automation/automation-dsc-getting-started.md#importing-a-configuration-into-azure-automation) na koncie usługi Automation. 
5. [Przypisywanie komputerów Windows lub węzeł](../../automation/automation-dsc-getting-started.md#onboarding-an-azure-vm-for-management-with-azure-automation-state-configuration) do konfiguracji. W ciągu 15 minut węzeł sprawdza, czy jego konfiguracja, a agent zostanie przypisany do węzła.

## <a name="verify-agent-connectivity-to-log-analytics"></a>Sprawdź połączenie agenta z usługą Log Analytics

Po zakończeniu instalacji agenta zweryfikowaniem jej został pomyślnie połączony, i raportowania można zrobić na dwa sposoby.  

Z komputera w **Panelu sterowania**, Znajdź element **Microsoft Monitoring Agent**.  Zaznacz go i **usługi Azure Log Analytics** karcie agent powinien być wyświetlany komunikat z informacją: **Program Microsoft Monitoring Agent pomyślnie połączył się z usługą Microsoft Operations Management Suite.**<br><br> ![Stan połączenia programu MMA z usługą Log Analytics](media/agent-windows/log-analytics-mma-laworkspace-status.png)

Można również wykonać zapytanie dziennika proste w witrynie Azure portal.  

1. W witrynie Azure Portal kliknij pozycję **Wszystkie usługi**. Na liście zasobów wpisz **usługi Azure Monitor**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz **usługa Azure Monitor**.  
2. Wybierz **dzienniki** w menu. 
2. W okienku dzienników w polu zapytania wpisz:  

    ```
    Heartbeat 
    | where Category == "Direct Agent" 
    | where TimeGenerated > ago(30m)  
    ```

W wynikach wyszukiwania, zwrócone powinien zostać wyświetlony rekordy pulsu dla komputera, wskazujący, że jest ona połączona i raportowanie do usługi.   

## <a name="next-steps"></a>Kolejne kroki

Przegląd [zachowaniu agenta usługi Log Analytics dla Windows i Linux i zarządzanie nimi](agent-manage.md) Aby dowiedzieć się więcej o sposobie zarządzania agenta podczas jego cykl życia wdrożenia na maszynach.  
