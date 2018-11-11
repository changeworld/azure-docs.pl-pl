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
ms.devlang: na
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: a16230b6f51f0ce93f4a9bf53591abbcd6b4bd3b
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2018
ms.locfileid: "51283686"
---
# <a name="connect-windows-computers-to-the-log-analytics-service-in-azure"></a>Łączenie komputerów Windows do usługi Log Analytics na platformie Azure

Aby monitorować i zarządzać maszyn wirtualnych lub fizycznych komputerów w lokalnym centrum danych lub w innym środowisku chmury, za pomocą usługi Log Analytics, należy wdrożyć program Microsoft Monitoring Agent (MMA) i skonfigurować go do raportowania do co najmniej jeden obszaru roboczego usługi Log Analytics.  Agent obsługuje również roli hybrydowego procesu roboczego Runbook usługi Azure Automation.  

Na monitorowanym komputerze Windows agent jest wyświetlany jako usługa Microsoft Monitoring Agent. Usługa Microsoft Monitoring Agent zbiera dane zdarzeń z plików dziennika i Windows dziennika zdarzeń, dane dotyczące wydajności i inną telemetrią. Nawet wtedy, gdy agent nie może komunikować się z usługą Log Analytics, którego wysyła raporty, agent będzie nadal działać i umieszcza w kolejce zebranych danych na dysku monitorowanego komputera. Po przywróceniu połączenia usługa Microsoft Monitoring Agent wysyła zebrane dane do usługi.

Agenta można zainstalować przy użyciu jednej z następujących metod. Większości instalacji jest używana kombinacja tych metod w celu zainstalowania różnych zestawów komputerów, zgodnie z potrzebami.  Szczegółowe informacje na temat korzystania z poszczególnych metod znajdują się w dalszej części tego artykułu.

* Instalacja ręczna. Instalator jest ręcznie uruchomić na komputerze, korzystając z Kreatora instalacji z wiersza polecenia lub wdrażany za pomocą istniejącego narzędzia do dystrybucji oprogramowania.
* Usługa Azure Automation Desired State Configuration (DSC). Korzystanie z DSC w usłudze Azure Automation za pomocą skryptu programu Windows komputerów już wdrożony w środowisku.  
* Skrypt programu PowerShell.
* Szablon usługi Resource Manager dla maszyn wirtualnych z systemem Windows w środowisku lokalnym w usłudze Azure Stack.  

Aby poznać obsługiwaną konfigurację, przejrzyj tematy dotyczące [obsługiwanych systemów operacyjnych Windows](log-analytics-concept-hybrid.md#supported-windows-operating-systems) oraz [konfiguracji zapory sieciowej](log-analytics-concept-hybrid.md#network-firewall-requirements).

## <a name="obtain-workspace-id-and-key"></a>Uzyskiwanie identyfikatora i klucza obszaru roboczego
Przed zainstalowaniem programu Microsoft Monitoring Agent dla systemu Windows potrzebne są identyfikator i klucz obszaru roboczego usługi Log Analytics.  Te informacje są wymagane podczas instalacji z poszczególnych metod instalacji w celu poprawnego skonfigurowania agenta i upewnij się, że może się skutecznie komunikować z usługi Log Analytics na platformie Azure komercyjnych i w chmurze dla instytucji rządowych USA.  

1. W witrynie Azure Portal kliknij pozycję **Wszystkie usługi**. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz pozycję **Log Analytics**.
2. Na liście obszarów roboczych usługi Log Analytics wybierz obszar roboczy, którego zamierzasz na skonfigurowanie agenta raportowania do.
3. Wybierz pozycję **Ustawienia zaawansowane**.<br><br> ![Ustawienia zaawansowane usługi Log Analytics](media/log-analytics-agent-windows/log-analytics-advanced-settings-01.png)<br><br>  
4. Wybierz **Połączone źródła**, a następnie **Serwery Windows**.   
5. Skopiuj i Wklej w swoim ulubionym edytorze **identyfikator obszaru roboczego** i **klucz podstawowy**.    
   
## <a name="install-the-agent-using-setup-wizard"></a>Instalowanie agenta za pomocą Kreatora instalacji
Poniższe kroki instalowania i konfigurowania agenta usługi Log Analytics w chmurze platformy Azure i Azure dla instytucji rządowych za pomocą Kreatora instalacji programu Microsoft Monitoring Agent na komputerze.  

1. W obszarze roboczym usługi Log Analyics z **serwerów Windows** strony przejście wcześniej, wybierz odpowiedni **Pobierz agenta Windows** wersję do pobrania w zależności od architektury procesora system operacyjny Windows.   
2. Uruchom Instalatora, aby zainstalować agenta na komputerze.
2. Na **stronie powitalnej** kliknij przycisk **Dalej**.
3. Na stronie **Postanowienia licencyjne** zapoznaj się z postanowieniami licencyjnymi, a następnie kliknij przycisk **Zgadzam się**.
4. Na stronie **Folder docelowy** zmień lub pozostaw domyślny folder instalacji, a następnie kliknij przycisk **Dalej**.
5. Na stronie **Opcje instalacji agenta** wybierz połączenie agenta z usługą Azure Log Analytics, a następnie kliknij przycisk **Dalej**.   
6. Na stronie **Azure Log Analytics** wykonaj następujące czynności:
   1. Wklej skopiowany wcześniej **identyfikator obszaru roboczego** i **klucz obszaru roboczego (klucz podstawowy)**.  Jeśli komputer powinien wysyłać raporty do obszaru roboczego usługi Log Analytics w chmurze Azure dla instytucji rządowych, wybierz **Wersja platformy Azure dla administracji USA** z listy rozwijanej **Azure Cloud**.  
   2. Jeśli komputer musi komunikować się z usługą Log Analytics za pośrednictwem serwera proxy, kliknij pozycję **Zaawansowane** i podaj adres URL i numer portu serwera proxy.  Jeśli Twój serwer proxy wymaga uwierzytelniania, wpisz nazwę użytkownika i hasło, aby uwierzytelnić się na serwerze proxy, a następnie kliknij przycisk **Dalej**.  
7. Po zakończeniu podawania niezbędnych ustawień konfiguracji kliknij przycisk **Dalej**.<br><br> ![Wklejanie klucza podstawowego i identyfikatora obszaru roboczego](media/log-analytics-agent-windows/log-analytics-mma-setup-laworkspace.png)<br><br>
8. Na stronie **Gotowe do zainstalowania** przejrzyj wybrane opcje, a następnie kliknij pozycję **Zainstaluj**.
9. Na stronie **Konfiguracja została zakończona pomyślnie** kliknij przycisk **Zakończ**.

Po ukończeniu instalacji program **Microsoft Monitoring Agent** będzie wyświetlany w **Panelu sterowania**. Aby upewnić się, jest raportowanie do usługi Log Analytics, zapoznaj się z [sprawdzić połączenie agenta z usługą Log Analytics](#verify-agent-connectivity-to-log-analytics). 

## <a name="install-the-agent-using-the-command-line"></a>Instalacja agenta przy użyciu wiersza polecenia
Pobrany plik agenta jest samodzielny pakiet instalacyjny.  Program instalacyjny programu agent i pliki pomocnicze są zawarte w pakiecie i muszą zostać wyodrębnione, aby można było poprawnie zainstalować przy użyciu wiersza polecenia, pokazano w poniższych przykładach.    

>[!NOTE]
>Jeśli chcesz uaktualnić agenta, należy użyć usługi Log Analytics, interfejs API obsługi skryptów. Zobacz temat [zachowaniu agenta usługi Log Analytics dla Windows i Linux i zarządzanie nimi](log-analytics-agent-manage.md) Aby uzyskać więcej informacji.

W poniższej tabeli wymieniono określone parametry usługi Log Analytics, obsługiwane przez Instalatora dla agenta, w tym podczas wdrażania za pomocą DSC usługi Automation.

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

1. Aby wyodrębnić pliki instalacyjne agenta z wiersza polecenia z podwyższonym poziomem uprawnień uruchom `MMASetup-<platform>.exe /c` i zostanie wyświetlony monit dla ścieżki wyodrębnić pliki do.  Alternatywnie, można określić ścieżkę przez przekazanie argumentów `MMASetup-<platform>.exe /c /t:<Path>`.  
2. Aby dyskretnie zainstalować agenta i skonfigurowanie jej do raportu do obszaru roboczego w komercyjnej chmury Azure z folderu zostały wyodrębnione pliki instalacyjne na typ: 
   
     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=0 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1
    ```

   lub aby skonfigurować agenta w celu raportowania do chmury Azure instytucji rządowych USA, wpisz: 

     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1
    ```

## <a name="install-the-agent-using-dsc-in-azure-automation"></a>Instalowanie agenta za pomocą DSC w usłudze Azure Automation

Poniższy przykładowy skrypt służy do instalowania agenta przy użyciu usługi Azure Automation DSC.   Jeśli nie masz konta usługi Automation, zobacz [Rozpoczynanie pracy z usługą Azure Automation](/azure/automation/) , aby zrozumieć wymagania i procedury służące do tworzenia konta usługi Automation, wymagana przed rozpoczęciem korzystania z usługi Automation DSC.  Jeśli nie znasz dobrze usługi Automation DSC, zapoznaj się z [wprowadzenie do usługi Automation DSC](../automation/automation-dsc-getting-started.md).

W poniższym przykładzie instalowana agent 64-bitowy, identyfikowane przez `URI` wartość. Można również użyć 32-bitowej wersji, zastępując wartość identyfikatora URI. Identyfikatory URI, dla obu wersji są następujące:

- Agent 64-bitowy Windows- https://go.microsoft.com/fwlink/?LinkId=828603
- Agent 32-bitowa Windows — https://go.microsoft.com/fwlink/?LinkId=828604


>[!NOTE]
>W tym przykładzie procedury i skrypt nie obsługuje uaktualniania agenta już wdrożonych na komputerze Windows.

32-bitowych i 64-bitowe wersje pakietu agenta mają kodów innego produktu i nowe wersje wydane również mieć unikatową wartość.  Kod produktu jest identyfikator GUID jest identyfikator podmiotu zabezpieczeń aplikacji lub produktu, która jest reprezentowana przez Instalatora Windows **ProductCode** właściwości.  `ProductId` Wartość w **MMAgent.ps1** skryptu musi odpowiadać kod produktu za pomocą pakietu Instalatora 32-bitową lub 64-bitowych agentów.

Aby bezpośrednio pobrać kod produktu za pomocą pakietu instalacji agenta, należy użyć Orca.exe z [Windows SDK składników dla Windows Installer deweloperów](https://msdn.microsoft.com/library/windows/desktop/aa370834%28v=vs.85%29.aspx) oznacza to składnik systemu Windows Software Development Kit lub za pomocą Następujący program PowerShell [przykładowy skrypt](http://www.scconfigmgr.com/2014/08/22/how-to-get-msi-file-information-with-powershell/) napisane przez Microsoft Valuable Professional (MVP).  Dla obu podejście, najpierw należy wyodrębnić **MOMagent.msi** plik z pakietu instalacyjnego MMASetup.  Zostało to przedstawione wcześniej w pierwszym krokiem w sekcji [instalowania agenta przy użyciu wiersza polecenia](#install-the-agent-using-the-command-line).  

1. Importuj DSC xPSDesiredStateConfiguration modułu na podstawie [ http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration ](http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) do usługi Azure Automation.  
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
5. [Zaimportuj skrypt konfiguracji MMAgent.ps1](../automation/automation-dsc-getting-started.md#importing-a-configuration-into-azure-automation) na koncie usługi Automation. 
5. [Przypisywanie komputerów Windows lub węzeł](../automation/automation-dsc-getting-started.md#onboarding-an-azure-vm-for-management-with-azure-automation-state-configuration) do konfiguracji. W ciągu 15 minut węzeł sprawdza, czy jego konfiguracja, a agent zostanie przypisany do węzła.

## <a name="verify-agent-connectivity-to-log-analytics"></a>Sprawdź połączenie agenta z usługą Log Analytics

Po zakończeniu instalacji agenta zweryfikowaniem jej został pomyślnie połączony, i raportowania można zrobić na dwa sposoby.  

Z komputera w **Panelu sterowania**, Znajdź element **Microsoft Monitoring Agent**.  Zaznacz go i **usługi Azure Log Analytics** karcie agent powinien być wyświetlany komunikat z informacją: **program Microsoft Monitoring Agent pomyślnie połączył się z usługą Microsoft Operations Management Suite.**<br><br> ![Stan połączenia programu MMA z usługą Log Analytics](media/log-analytics-agent-windows/log-analytics-mma-laworkspace-status.png)

Wyszukiwanie w dzienniku proste można również wykonać w witrynie Azure portal.  

1. W witrynie Azure Portal kliknij pozycję **Wszystkie usługi**. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz pozycję **Log Analytics**.  
2. Na stronie obszaru roboczego usługi Log Analytics wybierz docelowy obszar roboczy, a następnie wybierz **wyszukiwanie w dzienniku** kafelka. 
2. W okienku przeszukiwanie dzienników w polu zapytania wpisz:  

    ```
    search * 
    | where Type == "Heartbeat" 
    | where Category == "Direct Agent" 
    | where TimeGenerated > ago(30m)  
    ```

W wynikach wyszukiwania, zwrócone powinien zostać wyświetlony rekordy pulsu dla komputera, wskazujący, że jest ona połączona i raportowanie do usługi.   

## <a name="next-steps"></a>Kolejne kroki

Przegląd [zachowaniu agenta usługi Log Analytics dla Windows i Linux i zarządzanie nimi](log-analytics-agent-manage.md) Aby dowiedzieć się więcej o sposobie zarządzania agenta podczas jego cykl życia wdrożenia na maszynach.  
