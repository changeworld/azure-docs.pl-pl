---
title: Zarządzanie agenta usługi Azure Log Analytics | Dokumentacja firmy Microsoft
description: W tym artykule opisano zadania zarządzania różnych, które zazwyczaj należy wykonać podczas cyklu życia Windows Analytics dziennika lub agenta systemu Linux wdrożonych na komputerze.
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
ms.date: 04/23/2019
ms.author: magoedte
ms.openlocfilehash: 1809cc50f3ad3c285e0b69bc6e383a2c7c398238
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65139264"
---
# <a name="managing-and-maintaining-the-log-analytics-agent-for-windows-and-linux"></a>Zarządzania i konserwacji agenta usługi Log Analytics dla Windows i Linux

Po początkowym wdrożeniu programu Windows Analytics dziennika lub agenta systemu Linux w usłudze Azure Monitor konieczne może być ponownie skonfigurować agenta, ją uaktualnić lub usunąć z komputera, jeżeli zostanie osiągnięta etapu wycofanie etapie jej cyklu życia. Te zadania rutynowej konserwacji umożliwia łatwe zarządzanie, ręcznie lub za pomocą automatyzacji, co zmniejsza zarówno błędu operacyjnego, jak i koszty.

## <a name="upgrading-agent"></a>Uaktualnianie agenta

Agenta usługi Log Analytics dla Windows i Linux można uaktualnić do najnowszej wersji ręcznie lub automatycznie w zależności od scenariusza wdrażania i środowisko, w których maszyna wirtualna jest uruchomiona w. Następujące metody może służyć do uaktualnienia tego agenta.

| Środowisko | Metody instalacji | Metoda aktualizacji |
|--------|----------|-------------|
| Maszyna wirtualna platformy Azure | Zaloguj się rozszerzenia maszyny Wirtualnej agenta Analytics dla systemu Windows/Linux | Agent jest automatycznie uaktualniany domyślnie, chyba że skonfigurowano szablon usługi Azure Resource Manager, aby zrezygnować z nowego, ustawiając właściwość *autoUpgradeMinorVersion* do **false**. |
| Niestandardowe obrazy maszyn wirtualnych platformy Azure | Ręczna instalacja agenta usługi Log Analytics dla systemu Windows/Linux | Aktualizowanie maszyn wirtualnych do najnowszej wersji agenta musi zostać wykonana w wierszu polecenia z pakietu Instalatora Windows lub Linux samowyodrębniający i możliwe do zainstalowania powłoki skrypt pakietu.|
| Maszyny wirtualne poza platformą Azure | Ręczna instalacja agenta usługi Log Analytics dla systemu Windows/Linux | Aktualizowanie maszyn wirtualnych do najnowszej wersji agenta musi zostać wykonana w wierszu polecenia z pakietu Instalatora Windows lub Linux samowyodrębniający i możliwe do zainstalowania powłoki skrypt pakietu. |

### <a name="upgrade-windows-agent"></a>Uaktualnij agenta Windows 

Aby zaktualizować agenta na maszynie Wirtualnej Windows do najnowszej wersji, nie zainstalowano za pomocą rozszerzenia Log Analytics VM, należy uruchomić z wiersza polecenia, skryptu lub innego rozwiązania automatyzacji lub za pomocą MMASetup -\<platformy\>Instalatora MSI Kreator.  

Możesz pobrać najnowszą wersję agenta Windows z obszaru roboczego usługi Log Analytics, wykonując następujące kroki.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. W witrynie Azure Portal kliknij pozycję **Wszystkie usługi**. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz **obszarów roboczych usługi Log Analytics**.

3. Na liście obszarów roboczych usługi Log Analytics wybierz obszar roboczy.

4. W obszarze roboczym usługi Log Analytics wybierz **Zaawansowane ustawienia**, a następnie wybierz **połączone źródła**, a na koniec **serwerów Windows**.

5. Z **serwerów Windows** wybierz odpowiednią **Pobierz agenta Windows** wersję do pobrania w zależności od architektury procesora systemu operacyjnego Windows.

>[!NOTE]
>Podczas uaktualniania agenta usługi Log Analytics dla Windows nie obsługuje konfigurowania ani ponownej konfiguracji obszaru roboczego, aby zgłosić. Aby skonfigurować agenta, należy wykonać jedną z obsługiwanych metod, które są wyświetlane w obszarze [Dodawanie lub usuwanie obszaru roboczego](#adding-or-removing-a-workspace).
>

#### <a name="to-upgrade-using-the-setup-wizard"></a>Aby uaktualnić, za pomocą Kreatora instalacji

1. Zaloguj się do komputera przy użyciu konta z uprawnieniami administracyjnymi.

2. Wykonaj **MMASetup -\<platformy\>.exe** Aby uruchomić Kreatora instalacji.

3. Na pierwszej stronie Kreatora instalacji, kliknij przycisk **dalej**.

4. W **Instalator programu Microsoft Monitoring Agent** okno dialogowe, kliknij przycisk **zgodę** aby zaakceptować umowę licencyjną.

5. W **Instalator programu Microsoft Monitoring Agent** okno dialogowe, kliknij przycisk **uaktualnienia**. Na stronie stanu wyświetlany postęp uaktualnienia.

6. Gdy **Konfiguracja programu Microsoft Monitoring Agent została pomyślnie ukończona.** zostanie wyświetlona strona, kliknij przycisk **Zakończ**.

#### <a name="to-upgrade-from-the-command-line"></a>Aby uaktualnić z wiersza polecenia

1. Zaloguj się do komputera przy użyciu konta z uprawnieniami administracyjnymi.

2. Aby wyodrębnić pliki instalacyjne agenta z wiersza polecenia z podwyższonym poziomem uprawnień uruchom `MMASetup-<platform>.exe /c` i zostanie wyświetlony monit dla ścieżki wyodrębnić pliki do. Alternatywnie, można określić ścieżkę przez przekazanie argumentów `MMASetup-<platform>.exe /c /t:<Full Path>`.

3. Uruchom następujące polecenie, w którym D:\ jest lokalizacją pliku dziennika uaktualnienia.

    ```dos
    setup.exe /qn /l*v D:\logs\AgentUpgrade.log AcceptEndUserLicenseAgreement=1
    ```

### <a name="upgrade-linux-agent"></a>Uaktualnienie agenta systemu Linux 

Uaktualnianie z wcześniejszych wersji (> 1.0.0-47) jest obsługiwana. Przeprowadzanie instalacji przy użyciu `--upgrade` polecenie uaktualni wszystkie składniki agenta do najnowszej wersji.

Uruchom następujące polecenie, aby uaktualnić agenta.

`sudo sh ./omsagent-*.universal.x64.sh --upgrade`

## <a name="adding-or-removing-a-workspace"></a>Dodawanie lub usuwanie obszaru roboczego

### <a name="windows-agent"></a>Windows agent
Kroki opisane w tej sekcji są niezbędne w przypadku, gdy zachodzi potrzeba nie tylko ponownie skonfigurować agenta Windows raportować do innego obszaru roboczego lub usunąć obszar roboczy z jego konfiguracji, ale także gdy zachodzi potrzeba skonfigurować agenta Aby zgłosić się do więcej niż jednego obszaru roboczego (często nazywane multihosting). Konfigurowanie agenta Windows w celu raportowania do wielu obszarów roboczych można wykonać tylko po wstępnej instalacji agenta i za pomocą metod opisanych poniżej.    

#### <a name="update-settings-from-control-panel"></a>Aktualizowanie ustawień w Panelu sterowania

1. Zaloguj się do komputera przy użyciu konta z uprawnieniami administracyjnymi.

2. Otwórz **Panel sterowania**.

3. Wybierz **Microsoft Monitoring Agent** a następnie kliknij przycisk **usługi Azure Log Analytics** kartę.

4. Jeżeli usunięcie obszaru roboczego, wybierz ją, a następnie kliknij przycisk **Usuń**. Powtórz ten krok dla inny obszar roboczy ma przestają raportować do agenta.

5. Dodawanie obszaru roboczego, kliknij przycisk **Dodaj** i **Dodaj obszar roboczy usługi Log Analytics** okno dialogowe, wklej identyfikator obszaru roboczego i klucz obszaru roboczego (klucz podstawowy). Jeśli komputer powinien wysyłać raporty do obszaru roboczego usługi Log Analytics w chmurze Azure dla instytucji rządowych, wybierz dla administracji USA, z listy rozwijanej w chmurze platformy Azure.

6. Kliknij przycisk **OK**, aby zapisać zmiany.

#### <a name="remove-a-workspace-using-powershell"></a>Usuwanie obszaru roboczego przy użyciu programu PowerShell

```powershell
$workspaceId = "<Your workspace Id>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.RemoveCloudWorkspace($workspaceId)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-commercial-using-powershell"></a>Dodaj obszar roboczy na platformie Azure komercyjnych, przy użyciu programu PowerShell

```powershell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-for-us-government-using-powershell"></a>Dodaj obszar roboczy na platformie Azure dla instytucji rządowych USA przy użyciu programu PowerShell

```powershell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey, 1)
$mma.ReloadConfiguration()
```

>[!NOTE]
>Jeśli wcześniej używano wiersza polecenia lub skryptu wcześniej zainstalować lub skonfigurować agenta, `EnableAzureOperationalInsights` została zastąpiona `AddCloudWorkspace` i `RemoveCloudWorkspace`.
>

### <a name="linux-agent"></a>Agent systemu Linux
Poniższe kroki pokazują, jak można ponownie skonfigurować agenta systemu Linux, jeśli użytkownik zdecyduje, zarejestruj je przy użyciu innego obszaru roboczego lub usunąć obszar roboczy z jego konfiguracji.

1. Aby sprawdzić, czy jest zarejestrowany do obszaru roboczego, uruchom następujące polecenie:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l`

    Powinien zostać zwrócony stan podobny do poniższego przykładu:

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

    Należy pamiętać, że jest również wyświetlany stan agenta jest uruchomiona, w przeciwnym razie poniższe kroki, aby ponownie skonfigurować agenta nie zostanie ukończone pomyślnie.

2. Jeśli został on już zarejestrowany z obszarem roboczym, należy usunąć zarejestrowane obszar roboczy, uruchamiając następujące polecenie. W przeciwnym razie, jeśli nie jest zarejestrowana, przejdź do następnego kroku.

    `/opt/microsoft/omsagent/bin/omsadmin.sh -X`

3. Aby zarejestrować się przy użyciu innego obszaru roboczego, uruchom następujące polecenie:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <shared key> [-d <top level domain>]`
    
4. Aby sprawdzić, czy zmiany weszło w życie, uruchom następujące polecenie:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l`

    Powinien zostać zwrócony stan podobny do poniższego przykładu:

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

Usługa agenta nie musi być uruchomiona ponownie, aby zmiany zaczęły obowiązywać.

## <a name="update-proxy-settings"></a>Zaktualizuj ustawienia serwera proxy
Aby skonfigurować agenta do komunikowania się z usługą za pośrednictwem serwera proxy lub [bramy usługi Log Analytics](gateway.md) po wdrożeniu, należy użyć jednej z następujących metod do wykonania tego zadania.

### <a name="windows-agent"></a>Windows agent

#### <a name="update-settings-using-control-panel"></a>Aktualizowanie ustawień za pomocą Panelu sterowania

1. Zaloguj się do komputera przy użyciu konta z uprawnieniami administracyjnymi.

2. Otwórz **Panel sterowania**.

3. Wybierz **Microsoft Monitoring Agent** a następnie kliknij przycisk **ustawienia serwera Proxy** kartę.

4. Kliknij pozycję **Użyj serwera proxy** i podaj adres URL oraz numer portu serwera proxy lub bramy. Jeśli Twój serwer proxy lub brama usługi Log Analytics wymaga uwierzytelniania, wpisz nazwę użytkownika i hasło, aby się uwierzytelnić, a następnie kliknij przycisk **OK**.

#### <a name="update-settings-using-powershell"></a>Ustawienia aktualizacji przy użyciu programu PowerShell

Skopiuj następujący przykładowy kod programu PowerShell, zaktualizuj go informacjami specyficznymi dla środowiska i zapisz go z rozszerzeniem nazwy pliku PS1. Uruchom skrypt na każdym komputerze, który łączy się bezpośrednio z obszaru roboczego usługi Log Analytics w usłudze Azure Monitor.

```powershell
param($ProxyDomainName="https://proxy.contoso.com:30443", $cred=(Get-Credential))

# First we get the Health Service configuration object. We need to determine if we
#have the right update rollup with the API we need. If not, no need to run the rest of the script.
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
Wykonaj następujące czynności w przypadku komputerów z systemem Linux muszą komunikować się za pośrednictwem serwera proxy lub bramy usługi Log Analytics. Wartość konfiguracji serwera proxy ma następującą składnię `[protocol://][user:password@]proxyhost[:port]`. Właściwość *proxyhost* akceptuje w pełni kwalifikowaną nazwę domeny lub adres IP serwera proxy.

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
Użyj jednej z poniższych procedur można odinstalować agenta Windows lub Linux, przy użyciu wiersza polecenia lub Kreatora instalacji.

### <a name="windows-agent"></a>Windows agent

#### <a name="uninstall-from-control-panel"></a>Odinstaluj z poziomu Panelu sterowania
1. Zaloguj się do komputera przy użyciu konta z uprawnieniami administracyjnymi.

2. W **Panelu sterowania**, kliknij przycisk **programy i funkcje**.

3. W **programy i funkcje**, kliknij przycisk **Microsoft Monitoring Agent**, kliknij przycisk **Odinstaluj**, a następnie kliknij przycisk **tak**.

>[!NOTE]
>Kreatora instalacji agenta można również uruchomić przez dwukrotne kliknięcie **MMASetup -\<platformy\>.exe**, który jest dostępny do pobrania z obszaru roboczego w witrynie Azure portal.

#### <a name="uninstall-from-the-command-line"></a>Odinstaluj z wiersza polecenia
Pobrany plik agenta jest pakietem instalacyjnym niezależna utworzonych za pomocą IExpress. Program instalacyjny programu agent i pliki pomocnicze są zawarte w pakiecie i muszą zostać wyodrębnione w celu poprawnego odinstalowania przy użyciu wiersza polecenia, pokazano w poniższym przykładzie.

1. Zaloguj się do komputera przy użyciu konta z uprawnieniami administracyjnymi.

2. Aby wyodrębnić pliki instalacyjne agenta z wiersza polecenia z podwyższonym poziomem uprawnień uruchom `extract MMASetup-<platform>.exe` i zostanie wyświetlony monit dla ścieżki wyodrębnić pliki do. Alternatywnie, można określić ścieżkę przez przekazanie argumentów `extract MMASetup-<platform>.exe /c:<Path> /t:<Path>`. Aby uzyskać więcej informacji na temat parametrów wiersza polecenia obsługiwane przez IExpress, zobacz [przełączniki wiersza polecenia dla IExpress](https://support.microsoft.com/help/197147/command-line-switches-for-iexpress-software-update-packages) , a następnie zaktualizuj przykład do własnych potrzeb.

3. W wierszu polecenia wpisz `%WinDir%\System32\msiexec.exe /x <Path>:\MOMAgent.msi /qb`.

### <a name="linux-agent"></a>Agent systemu Linux
Aby usunąć agenta, uruchom poniższe polecenie na komputerze z systemem Linux. Argument *--purge* powoduje całkowite usunięcie agenta i jego konfiguracji.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

## <a name="configure-agent-to-report-to-an-operations-manager-management-group"></a>Skonfiguruj agenta w celu raportowania do grupy zarządzania programu Operations Manager

### <a name="windows-agent"></a>Windows agent
Wykonaj poniższe kroki, aby skonfigurować agenta usługi Log Analytics dla Windows zgłosić do grupy zarządzania programu System Center Operations Manager.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

1. Zaloguj się do komputera przy użyciu konta z uprawnieniami administracyjnymi.

2. Otwórz **Panel sterowania**.

3. Kliknij przycisk **Microsoft Monitoring Agent** a następnie kliknij przycisk **programu Operations Manager** kartę.

4. Jeśli serwery programu Operations Manager są zintegrowane z usługą Active Directory, kliknij przycisk **automatycznie Aktualizuj przypisania grupy zarządzania z usług AD DS**.

5. Kliknij przycisk **Dodaj** otworzyć **Dodaj grupę zarządzania** okno dialogowe.

6. W **Nazwa grupy zarządzania** wpisz nazwę grupy zarządzania.

7. W **podstawowego serwera zarządzania** wpisz nazwę komputera w podstawowy serwer zarządzania.

8. W **port serwera zarządzania** wpisz numer portu TCP.

9. W obszarze **konto akcji agenta**, wybierz konto systemu lokalnego lub konta domeny lokalnej.

10. Kliknij przycisk **OK** zamknąć **Dodaj grupę zarządzania** okno dialogowe, a następnie kliknij przycisk **OK** zamknąć **właściwości programu Microsoft Monitoring Agent** okno dialogowe.

### <a name="linux-agent"></a>Agent systemu Linux
Wykonaj poniższe kroki, aby skonfigurować agenta usługi Log Analytics dla systemu Linux w celu przekazywania informacji do grupy zarządzania programu System Center Operations Manager.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

1. Edytuj plik `/etc/opt/omi/conf/omiserver.conf`

2. Upewnij się, że wiersz zaczynający się od `httpsport=` definiuje portu 1270. Przykład: `httpsport=1270`

3. Uruchom ponownie serwer OMI: `sudo /opt/omi/bin/service_control restart`

## <a name="next-steps"></a>Kolejne kroki

Przegląd [Rozwiązywanie problemów z agentem systemu Linux](agent-linux-troubleshoot.md) Jeśli napotkasz problemy podczas instalowania lub zarządzania agenta.
