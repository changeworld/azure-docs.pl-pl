---
title: Zarządzanie agenta usługi Analiza dzienników Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano zadania różnych zarządzania, które zwykle będą wykonywane podczas cyklu życia programu Microsoft Monitoring Agent (MMA) wdrożone na maszynie.
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
ms.date: 03/30/2018
ms.author: magoedte
ms.openlocfilehash: 5ff4f79a607143683b37726f1c02a6057dc6b9b0
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="managing-and-maintaining-the-log-analytics-agent-for-windows-and-linux"></a>Zarządzanie i obsługę agenta analizy dzienników systemu Windows i Linux

Po początkowym wdrożeniu agentów systemu Windows lub Linux do analizy dzienników może być konieczne ponowne konfigurowanie agenta lub usunąć z komputera, jeśli został osiągnięty wycofania etapem cykl życia.  Te zadania Konserwacja umożliwia łatwe zarządzanie ręcznie lub za pomocą automatyzacji, co zmniejsza zarówno operacyjne błędu, jak i koszty.

## <a name="adding-or-removing-a-workspace"></a>Dodawanie lub usuwanie obszaru roboczego 

### <a name="windows-agent"></a>Agent systemu Windows

#### <a name="update-settings-from-control-panel"></a>Aktualizowanie ustawień z poziomu Panelu sterowania

1. Zaloguj się do komputera przy użyciu konta, które ma uprawnienia administracyjne.
2. Otwórz **Panel sterowania**.
3. Wybierz **programu Microsoft Monitoring Agent** , a następnie kliknij przycisk **Analiza dzienników Azure (OMS)** kartę.
4. Jeśli usuwanie obszaru roboczego, zaznacz go, a następnie kliknij przycisk **Usuń**. Powtórz ten krok dla innych obszaru roboczego Agent ma przestają raportować do.
5. Dodanie obszaru roboczego, kliknij przycisk **Dodaj** i na **Dodaj obszar roboczy analizy dzienników** okno dialogowe, wklej identyfikator obszaru roboczego i klucz obszaru roboczego (klucz podstawowy). Jeśli komputer należy zgłosić obszaru roboczego analizy dzienników w chmurze Azure dla instytucji rządowych, wybierz Azure instytucji rządowych Stanów Zjednoczonych, z listy rozwijanej w chmurze Azure. 
6. Kliknij przycisk **OK**, aby zapisać zmiany.

#### <a name="remove-a-workspace-using-powershell"></a>Usuwanie obszaru roboczego przy użyciu programu PowerShell 

```PowerShell
$workspaceId = "<Your workspace Id>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.RemoveCloudWorkspace($workspaceId)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-commercial-using-powershell"></a>Dodaj obszar roboczy na platformie Azure komercyjnych przy użyciu programu PowerShell 

```PowerShell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>”
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-for-us-government-using-powershell"></a>Dodaj obszar roboczy na platformie Azure dla instytucji rządowych Stanów Zjednoczonych przy użyciu programu PowerShell 

```PowerShell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>”
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey, 1)
$mma.ReloadConfiguration()
```

>[!NOTE]
>Jeśli używano wiersza polecenia lub skryptu wcześniej do zainstalowania i skonfigurowania agenta, `EnableAzureOperationalInsights` została zastąpiona `AddCloudWorkspace` i `RemoveCloudWorkspace`.
>

### <a name="linux-agent"></a>Agent systemu Linux
Poniższe kroki pokazują, jak zmienić konfigurację agenta systemu Linux, jeśli zdecydujesz się zarejestrowanie go za pomocą innego obszaru roboczego lub chcesz usunąć obszar roboczy z jej konfiguracji.  

1.  Aby sprawdzić, czy jest zarejestrowany do obszaru roboczego, uruchom następujące polecenie.

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l` 

    Powinien zostać zwrócony stan podobny do poniższego przykładu- 

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

    Należy pamiętać, że stan przedstawiono również agent nie działa, w przeciwnym razie następujące kroki, aby ponownie skonfigurować agenta programu nie zostanie ukończona pomyślnie.  

2. Jeśli został on już zarejestrowany z obszaru roboczego, Usuń zarejestrowanych obszaru roboczego, uruchamiając następujące polecenie.  W przeciwnym razie, jeśli nie jest zarejestrowany, przejdź do następnego kroku.

    `/opt/microsoft/omsagent/bin/omsadmin.sh -X`  
    
3. Aby zarejestrować z innego obszaru roboczego, uruchom polecenie `/opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <shared key> [-d <top level domain>]` 
4. Aby sprawdzić, czy zmiany miały wpływ, uruchom polecenie.

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l` 

    Powinien zostać zwrócony stan podobny do poniższego przykładu- 

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

Usługa agenta nie musi zostać uruchomiony ponownie, aby zmiany zaczęły obowiązywać.

## <a name="update-proxy-settings"></a>Zaktualizuj ustawienia serwera proxy 
Aby skonfigurować agenta do komunikowania się z usługą za pośrednictwem serwera proxy lub [bramy OMS](log-analytics-oms-gateway.md) po wdrożeniu, użyj jednej z następujących metod do zakończenia tego zadania.

### <a name="windows-agent"></a>Agent systemu Windows

#### <a name="update-settings-using-control-panel"></a>Ustawienia aktualizacji za pomocą Panelu sterowania

1. Zaloguj się do komputera przy użyciu konta, które ma uprawnienia administracyjne.
2. Otwórz **Panel sterowania**.
3. Wybierz **programu Microsoft Monitoring Agent** , a następnie kliknij przycisk **ustawienia serwera Proxy** kartę.
4. Kliknij przycisk **Użyj serwera proxy** i podaj adres URL i port numer serwera proxy lub bramy. Jeśli serwer proxy lub bramy OMS wymaga uwierzytelniania, wpisz nazwę użytkownika i hasło uwierzytelniania, a następnie kliknij przycisk **OK**. 

#### <a name="update-settings-using-powershell"></a>Ustawienia aktualizacji przy użyciu programu PowerShell 

Skopiuj następujący przykładowy kod programu PowerShell, go zaktualizować z użyciem informacji specyficznych dla danego środowiska, a następnie zapisz plik z rozszerzeniem nazwy pliku PS1.  Uruchom skrypt na każdym komputerze, który łączy się bezpośrednio z usługą analizy dzienników.

```PowerShell
param($ProxyDomainName="https://proxy.contoso.com:30443", $cred=(Get-Credential))

# First we get the Health Service configuration object.  We need to determine if we
#have the right update rollup with the API we need.  If not, no need to run the rest of the script.
$healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'

$proxyMethod = $healthServiceSettings | Get-Member -Name 'SetProxyInfo'

if (!$proxyMethod)
{
     Write-Output 'Health Service proxy API not present, will not update settings.'
     return
}

Write-Output "Clearing proxy settings."
$healthServiceSettings.SetProxyInfo('', '', '')

$ProxyUserName = $cred.username

Write-Output "Setting proxy to $ProxyDomainName with proxy username $ProxyUserName."
$healthServiceSettings.SetProxyInfo($ProxyDomainName, $ProxyUserName, $cred.GetNetworkCredential().password)
```  

### <a name="linux-agent"></a>Agent systemu Linux
Jeśli komputery Linux muszą komunikować się za pośrednictwem serwera proxy lub bramy OMS do analizy dzienników, należy wykonać następujące czynności.  Wartość konfiguracji serwera proxy ma następującą składnię `[protocol://][user:password@]proxyhost[:port]`.  Właściwość *proxyhost* akceptuje w pełni kwalifikowaną nazwę domeny lub adres IP serwera proxy.

1. Edytuj plik `/etc/opt/microsoft/omsagent/proxy.conf`, uruchamiając następujące polecenia, i zmień wartości na odpowiednie dla siebie.

    ```
    proxyconf="https://proxyuser:proxypassword@proxyserver01:30443"
    sudo echo $proxyconf >>/etc/opt/microsoft/omsagent/proxy.conf
    sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf 
    ```

2. Uruchom ponownie agenta, uruchamiając następujące polecenie: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="uninstall-agent"></a>Odinstaluj agenta
Użyj jednej z poniższych procedur do odinstalowania agenta systemu Windows lub Linux przy użyciu Kreatora instalacji lub wiersza polecenia.

### <a name="windows-agent"></a>Agent systemu Windows

#### <a name="uninstall-from-control-panel"></a>Odinstaluj z Panelu sterowania
1. Zaloguj się do komputera przy użyciu konta, które ma uprawnienia administracyjne.  
2. W **Panelu sterowania**, kliknij przycisk **programy i funkcje**.
3. W **programy i funkcje**, kliknij przycisk **programu Microsoft Monitoring Agent**, kliknij przycisk **Odinstaluj**, a następnie kliknij przycisk **tak**.

>[!NOTE]
>Kreator instalacji agenta można również uruchomić przez dwukrotne kliknięcie **MMASetup -<platform>.exe**, który jest dostępny do pobrania z obszaru roboczego w portalu Azure.

#### <a name="uninstall-from-the-command-line"></a>Odinstaluj z wiersza polecenia
Pobrany plik dla agenta jest pakietem instalacyjnym niezależne utworzone za pomocą IExpress.  Program instalacyjny programu agent i pliki pomocnicze są zawarte w pakiecie i trzeba wyodrębnienia Aby poprawnie ją odinstalować przy użyciu wiersza polecenia pokazano w poniższym przykładzie. 

1. Zaloguj się do komputera przy użyciu konta, które ma uprawnienia administracyjne.  
2. Aby wyodrębnić pliki instalacyjne agenta, w wierszu polecenia z podwyższonym poziomem uprawnień uruchom `extract MMASetup-<platform>.exe` i zostanie wyświetlony monit dla ścieżki do wyodrębnienia plików.  Alternatywnie można określić ścieżkę przez przekazanie argumentów `extract MMASetup-<platform>.exe /c:<Path> /t:<Path>`.  Aby uzyskać więcej informacji na swtiches wiersza polecenia, obsługiwane przez IExpress, zobacz [przełączniki wiersza polecenia dla IExpress](https://support.microsoft.com/help/197147/command-line-switches-for-iexpress-software-update-packages) , a następnie zaktualizować przykładu do własnych potrzeb.
3. W wierszu polecenia wpisz `%WinDir%\System32\msiexec.exe /x <Path>:\MOMAgent.msi /qb`.  

### <a name="linux-agent"></a>Agent systemu Linux
Aby usunąć agenta, uruchom poniższe polecenie na komputerze z systemem Linux.  Argument *--purge* powoduje całkowite usunięcie agenta i jego konfiguracji.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

## <a name="configure-agent-to-report-to-an-operations-manager-management-group"></a>Konfigurowanie agenta z grupy zarządzania programu Operations Manager

### <a name="windows-agent"></a>Agent systemu Windows
Wykonaj poniższe kroki, aby skonfigurować OMS agenta dla systemu Windows do raportu do grupy zarządzania programu System Center Operations Manager. 

1. Zaloguj się do komputera przy użyciu konta, które ma uprawnienia administracyjne.
2. Otwórz **Panel sterowania**. 
3. Kliknij przycisk **programu Microsoft Monitoring Agent** , a następnie kliknij przycisk **programu Operations Manager** kartę.
4. Jeśli serwery programu Operations Manager są zintegrowane z usługą Active Directory, kliknij przycisk **automatycznie Aktualizuj przypisania grupy zarządzania z usług AD DS**.
5. Kliknij przycisk **Dodaj** otworzyć **Dodaj grupę zarządzania** okno dialogowe.
6. W **Nazwa grupy zarządzania** wpisz nazwę grupy zarządzania.
7. W **podstawowego serwera zarządzania** wpisz nazwę komputera w podstawowy serwer zarządzania.
8. W **port serwera zarządzania** wpisz numer portu TCP.
9. W obszarze **konto akcji agenta**, wybierz konto systemu lokalnego lub konta domeny lokalnej.
10. Kliknij przycisk **OK** zamknąć **Dodaj grupę zarządzania** okno dialogowe, a następnie kliknij przycisk **OK** zamknąć **właściwości agenta monitorowania firmy Microsoft** okno dialogowe.

### <a name="linux-agent"></a>Agent systemu Linux
Wykonaj poniższe kroki, aby skonfigurować agenta pakietu OMS dla systemu Linux z grupy zarządzania programu System Center Operations Manager. 

1. Przeprowadź edycję pliku `/etc/opt/omi/conf/omiserver.conf`
2. Upewnij się, że zaczyna się od wiersza `httpsport=` definiuje portu 1270. Takie jak: `httpsport=1270`
3. Uruchom ponownie serwer OMI: `sudo /opt/omi/bin/service_control restart`

## <a name="next-steps"></a>Następne kroki

Przegląd [Rozwiązywanie problemów z agentem systemu Linux](log-analytics-agent-linux-support.md) napotkania problemów podczas instalowania lub zarządzania agentem.  