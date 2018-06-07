---
title: Łączenia komputerów z systemem Windows Azure Log Analytics | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób podłączania komputerów z systemem Windows w innych chmur lub lokalnie z analizą dzienników z programu Microsoft Monitoring Agent (MMA).
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: magoedte
ms.openlocfilehash: 342387edb3793358d041702eaa6d76b462aef527
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34637416"
---
# <a name="connect-windows-computers-to-the-log-analytics-service-in-azure"></a>Łączenie komputerów z systemem Windows z usługą analizy dzienników na platformie Azure

Aby monitorować i zarządzać nimi maszyn wirtualnych lub fizycznych komputerów w lokalnym centrum danych lub w innym środowisku chmury z analizy dzienników, należy wdrożyć program Microsoft Monitoring Agent (MMA) i skonfigurować go do zgłoszenia do jednego lub więcej analizy dzienników obszarów roboczych.  Agent obsługuje również rolę hybrydowy proces roboczy elementu Runbook automatyzacji Azure.  

Na monitorowanym komputerze z systemem Windows agent będzie wyświetlany jako usługa Microsoft Monitoring Agent. Usługa Microsoft Monitoring Agent zbiera dane zdarzeń z plików dziennika i dziennik zdarzeń systemu Windows, dane dotyczące wydajności i innych danych telemetrycznych. Nawet wtedy, gdy agent nie może skomunikować się z usługą analizy dzienników, któremu ma podlegać, agent będzie kontynuował działanie, a kolejki zebranych danych na dysku monitorowanego komputera. Po przywróceniu połączenia usługa Microsoft Monitoring Agent wysyła zebrane dane do usługi.

Agent może być zainstalowana za pomocą jednej z poniższych metod. Większości instalacji użyj kombinacji tych metod, aby zainstalować różne zestawy komputerów, zależnie od potrzeb.  Szczegółowe informacje o wykorzystaniu poszczególnych metod znajdują się w dalszej części tego artykułu.

* Instalacja ręczna. Instalator uruchomić ręcznie na komputerze przy użyciu Kreatora instalacji, w wierszu polecenia lub wdrożyć przy użyciu istniejących narzędzie do dystrybucji oprogramowania.
* Automatyzacja Azure Konfiguracja żądanego stanu (DSC). Przy użyciu usługi Konfiguracja DSC automatyzacji Azure za pomocą skryptu już wdrożony w środowisku komputerów z systemem Windows.  
* Skrypt programu PowerShell.
* Szablon Menedżera zasobów dla maszyn wirtualnych z systemem Windows lokalnymi w stosie Azure.  

Aby poznać obsługiwanej konfiguracji, przejrzyj [obsługiwanych systemów operacyjnych Windows](log-analytics-concept-hybrid.md#supported-windows-operating-systems) i [konfigurację zapory sieciowej](log-analytics-concept-hybrid.md#network-firewall-requirements).

## <a name="obtain-workspace-id-and-key"></a>Uzyskiwanie identyfikatora i klucza obszaru roboczego
Przed zainstalowaniem programu Microsoft Monitoring Agent dla systemu Windows potrzebne są identyfikator i klucz obszaru roboczego usługi Log Analytics.  Te informacje są wymagane podczas instalacji z każdej metody instalacji, aby poprawnie skonfigurować agenta i upewnij się, że mogą się komunikować z analizy dzienników na platformie Azure handlowych i w chmurze instytucji rządowych Stanów Zjednoczonych.  

1. W witrynie Azure Portal kliknij pozycję **Wszystkie usługi**. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz pozycję **Log Analytics**.
2. Na liście obszarów roboczych usługi Analiza dzienników wybierz obszar roboczy, które mają na skonfigurowanie agenta zgłoszenia do.
3. Wybierz pozycję **Ustawienia zaawansowane**.<br><br> ![Ustawienia zaawansowane usługi Log Analytics](media/log-analytics-quick-collect-azurevm/log-analytics-advanced-settings-01.png)<br><br>  
4. Wybierz **Połączone źródła**, a następnie **Serwery Windows**.   
5. Skopiować i wkleić do edytora ulubionych **identyfikator obszaru roboczego** i **klucz podstawowy**.    
   
## <a name="install-the-agent-using-setup-wizard"></a>Zainstaluj agenta przy użyciu Kreatora instalacji
Następujące kroki instalowania i konfigurowania agenta dla analizy dzienników w chmurze Azure oraz Azure dla instytucji rządowych za przy użyciu Kreatora instalacji programu Microsoft Monitoring Agent na tym komputerze.  

1. W obszarze roboczym Analyics dziennika z **serwerów z systemem Windows** strony przejście wcześniej, wybierz odpowiednie **Pobierz agenta systemu Windows** wersji do pobrania w zależności od architektury procesora system operacyjny Windows.   
2. Uruchom Instalatora, aby zainstalować agenta na komputerze.
2. Na **stronie powitalnej** kliknij przycisk **Dalej**.
3. Na stronie **Postanowienia licencyjne** zapoznaj się z postanowieniami licencyjnymi, a następnie kliknij przycisk **Zgadzam się**.
4. Na stronie **Folder docelowy** zmień lub pozostaw domyślny folder instalacji, a następnie kliknij przycisk **Dalej**.
5. Na stronie **Opcje instalacji agenta** wybierz połączenie agenta z usługą Azure Log Analytics (OMS), a następnie kliknij przycisk **Dalej**.   
6. Na stronie **Azure Log Analytics** wykonaj następujące czynności:
   1. Wklej skopiowany wcześniej **identyfikator obszaru roboczego** i **klucz obszaru roboczego (klucz podstawowy)**.  Jeśli komputer powinien wysyłać raporty do obszaru roboczego usługi Log Analytics w chmurze Azure dla instytucji rządowych, wybierz **Wersja platformy Azure dla administracji USA** z listy rozwijanej **Azure Cloud**.  
   2. Jeśli komputer musi komunikować się z usługą Log Analytics za pośrednictwem serwera proxy, kliknij pozycję **Zaawansowane** i podaj adres URL i numer portu serwera proxy.  Jeśli Twój serwer proxy wymaga uwierzytelniania, wpisz nazwę użytkownika i hasło, aby uwierzytelnić się na serwerze proxy, a następnie kliknij przycisk **Dalej**.  
7. Po zakończeniu podawania niezbędnych ustawień konfiguracji kliknij przycisk **Dalej**.<br><br> ![Wklejanie klucza podstawowego i identyfikatora obszaru roboczego](media/log-analytics-quick-collect-windows-computer/log-analytics-mma-setup-laworkspace.png)<br><br>
8. Na stronie **Gotowe do zainstalowania** przejrzyj wybrane opcje, a następnie kliknij pozycję **Zainstaluj**.
9. Na stronie **Konfiguracja została zakończona pomyślnie** kliknij przycisk **Zakończ**.

Po ukończeniu instalacji program **Microsoft Monitoring Agent** będzie wyświetlany w **Panelu sterowania**. Aby upewnić się, to jest raportowanie do analizy dzienników, przejrzyj [zweryfikować łączności agenta analizy dzienników](#verify-agent-connectivity-to-log-analytics). 

## <a name="install-the-agent-using-the-command-line"></a>Zainstaluj agenta przy użyciu wiersza polecenia
Pobrany plik dla agenta jest pakietem instalacyjnym niezależne.  Program instalacyjny programu agent i pliki pomocnicze są zawarte w pakiecie i wymagają do wyodrębnienia, aby można było poprawnie zainstalować przy użyciu wiersza polecenia pokazano w poniższych przykładach.    

>[!NOTE]
>Jeśli chcesz uaktualnić agenta, należy użyć analizy dzienników interfejs API obsługi skryptów. Zobacz temat [zarządzanie i obsługę agenta analizy dzienników systemu Windows i Linux](log-analytics-agent-manage.md) Aby uzyskać więcej informacji.

W poniższej tabeli wymieniono obsługiwane przez Instalatora dla agenta, w tym przypadku wdrożenia przy użyciu usługi Konfiguracja DSC automatyzacji konkretne parametry analizy dzienników.

|Opcje MMA                   |Uwagi         |
|---------------------------------------|--------------|
| NOAPM=1                               | Parametr opcjonalny. Instaluje agenta bez .NET Application Performance Monitoring.|   
|ADD_OPINSIGHTS_WORKSPACE               | 1 = Konfigurowanie agenta z obszaru roboczego                |
|OPINSIGHTS_WORKSPACE_ID                | Identyfikator obszaru roboczego (guid) dla obszaru roboczego do dodania                    |
|OPINSIGHTS_WORKSPACE_KEY               | Klucz obszaru roboczego używany do uwierzytelniania początkowo z obszaru roboczego |
|OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE  | Określ środowiska chmury, w którym znajduje się obszar roboczy <br> 0 = komercyjnych chmury azure (ustawienie domyślne) <br> 1 = azure dla instytucji rządowych |
|OPINSIGHTS_PROXY_URL               | Identyfikator URI serwera proxy do użycia |
|OPINSIGHTS_PROXY_USERNAME               | Nazwa użytkownika do uzyskania dostępu uwierzytelnionego serwera proxy |
|OPINSIGHTS_PROXY_PASSWORD               | Hasło dostępu uwierzytelnionego serwera proxy |

1. Aby wyodrębnić pliki instalacyjne agenta, w wierszu polecenia z podwyższonym poziomem uprawnień uruchom `MMASetup-<platform>.exe /c` i zostanie wyświetlony monit dla ścieżki do wyodrębnienia plików.  Alternatywnie można określić ścieżkę przez przekazanie argumentów `MMASetup-<platform>.exe /c /t:<Path>`.  
2. Aby dyskretnie zainstalować agenta i skonfigurować go do raportu do obszaru roboczego w chmurze komercyjnych Azure, z folderu zostały wyodrębnione pliki instalacyjne do typu: 
   
     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=0 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1
    ```

   lub aby skonfigurować agenta do zgłaszania do chmury Azure instytucji rządowych Stanów Zjednoczonych, wpisz: 

     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1
    ```

## <a name="install-the-agent-using-dsc-in-azure-automation"></a>Zainstaluj agenta przy użyciu usługi Konfiguracja DSC automatyzacji Azure

Poniższy przykładowy skrypt służy do instalowania agenta przy użyciu usługi Konfiguracja DSC automatyzacji Azure.   Jeśli nie masz konta automatyzacji, zobacz [wprowadzenie do usługi Automatyzacja Azure](../automation/automation-offering-get-started.md) zrozumieć wymagania i kroki tworzenia konta automatyzacji wymagana przed rozpoczęciem korzystania z usługi Konfiguracja DSC automatyzacji.  Jeśli nie masz doświadczenia w usłudze Konfiguracja DSC automatyzacji, przejrzyj [wprowadzenie do korzystania z usługi Konfiguracja DSC automatyzacji](../automation/automation-dsc-getting-started.md).

W poniższym przykładzie instalowana agent 64-bitowe, zidentyfikowane przez `URI` wartość. Umożliwia także 32-bitowej wersji zastępując wartość identyfikatora URI. Identyfikatory URI dla obu wersji są:

- Agent 64-bitowego systemu Windows — https://go.microsoft.com/fwlink/?LinkId=828603
- Agent 32-bitowego systemu Windows — https://go.microsoft.com/fwlink/?LinkId=828604


>[!NOTE]
>W tym przykładzie procedury i skrypt nie obsługuje uaktualniania agenta już wdrożony na komputerze z systemem Windows.

32-bitowe i 64-bitowe wersje pakietu agent mają kody innego produktu i nowych wersji wydanych również mieć unikatową wartość.  Kod produktu jest identyfikator GUID, który jest główny identyfikator aplikacji lub produktu i jest reprezentowana przez Instalatora Windows **ProductCode** właściwości.  `ProductId value` w **MMAgent.ps1** skryptu musi odpowiadać kodu produktu z 32-bitowy lub 64-bitowy agent pakiet Instalatora.

Aby pobrać kod produktu pakietu instalacji agenta bezpośrednio, można użyć Orca.exe z [Windows SDK składników dla deweloperów systemu Windows Instalator](https://msdn.microsoft.com/library/windows/desktop/aa370834%28v=vs.85%29.aspx) oznacza to składnik systemu Windows Software Development Kit lub przy użyciu Następujące PowerShell [przykładowy skrypt](http://www.scconfigmgr.com/2014/08/22/how-to-get-msi-file-information-with-powershell/) napisane przez Microsoft cenne Professional (MVP).  Dla obu podejście, należy najpierw wyodrębnić **MOMagent.msi** plików z pakietu instalacyjnego MMASetup.  Przedstawiono to w pierwszym krokiem w sekcji [zainstalować agenta przy użyciu wiersza polecenia](#install-the-agent-using-the-command-line).  

1. Importowanie konfiguracji DSC xPSDesiredStateConfiguration modułu na podstawie [ http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration ](http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) w automatyzacji Azure.  
2.  Tworzenie zasobów zmiennej usługi Automatyzacja Azure *OPSINSIGHTS_WS_ID* i *OPSINSIGHTS_WS_KEY*. Ustaw *OPSINSIGHTS_WS_ID* do zestawu i identyfikator obszaru roboczego analizy dzienników *OPSINSIGHTS_WS_KEY* w kluczu podstawowym obszaru roboczego.
3.  Skopiuj skrypt i zapisz go jako MMAgent.ps1

    ```PowerShell
    Configuration MMAgent
    {
        $OIPackageLocalPath = "C:\Deploy\MMASetup-AMD64.exe"
        $OPSINSIGHTS_WS_ID = Get-AutomationVariable -Name "OPSINSIGHTS_WS_ID"
        $OPSINSIGHTS_WS_KEY = Get-AutomationVariable -Name "OPSINSIGHTS_WS_KEY"

        Import-DscResource -ModuleName xPSDesiredStateConfiguration
        Import-DscResource –ModuleName PSDesiredStateConfiguration

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

4. [Importuj skryptu konfiguracji MMAgent.ps1](../automation/automation-dsc-getting-started.md#importing-a-configuration-into-azure-automation) na koncie automatyzacji. 
5. [Przypisz komputer z systemem Windows lub węzeł](../automation/automation-dsc-getting-started.md#onboarding-an-azure-vm-for-management-with-azure-automation-dsc) konfiguracji. W ciągu 15 minut węzeł sprawdza konfigurację i agent zostanie przypisany do węzła.

## <a name="verify-agent-connectivity-to-log-analytics"></a>Sprawdź łączność agenta do analizy dzienników

Po zakończeniu instalacji agenta jego weryfikowania został pomyślnie połączony i raportowania można przeprowadzić na dwa sposoby.  

Z komputera w **Panelu sterowania**, Znajdź element **programu Microsoft Monitoring Agent**.  Zaznacz go i na **Analiza dzienników Azure (OMS)** kartę, powinien być wyświetlany agenta komunikat z informacją: **Microsoft Monitoring Agent pomyślnie połączył się z usługą Microsoft Operations Management Suite.**<br><br> ![Stan połączenia programu MMA z usługą Log Analytics](media/log-analytics-quick-collect-windows-computer/log-analytics-mma-laworkspace-status.png)

Można również wykonywać proste dziennik wyszukiwania w portalu Azure.  

1. W witrynie Azure Portal kliknij pozycję **Wszystkie usługi**. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz pozycję **Log Analytics**.  
2. Na stronie obszaru roboczego analizy dzienników, wybierz docelowy obszar roboczy, a następnie wybierz **wyszukiwania dziennika** kafelka. 
2. W okienku wyszukiwania dziennika, w polu wpisz kwerendę:  

    ```
    search * 
    | where Type == "Heartbeat" 
    | where Category == "Direct Agent" 
    | where TimeGenerated > ago(30m)  
    ```

W wynikach wyszukiwania zwróciła powinny zostać wyświetlone rekordy pulsu dla komputera, wskazujący, że jest połączony i raportowania do usługi.   

## <a name="next-steps"></a>Kolejne kroki

Przegląd [zarządzanie i obsługę agenta analizy dzienników systemu Windows i Linux](log-analytics-agent-manage.md) Aby dowiedzieć się więcej o sposobie zarządzania agentem podczas jego cykl życia wdrożenia na maszynach.  