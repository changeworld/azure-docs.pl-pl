---
title: Zarządzanie agentem usługi Azure Log Analytics
description: W tym artykule opisano różne zadania zarządzania, które zazwyczaj będą wykonywane podczas cyklu życia usługi Log Analytics systemu Windows lub agenta systemu Linux wdrożonego na komputerze.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/14/2019
ms.openlocfilehash: 4d0ceacd37748e9761903d02fd7e052d70b10e15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275102"
---
# <a name="managing-and-maintaining-the-log-analytics-agent-for-windows-and-linux"></a>Managing and maintaining the Log Analytics agent for Windows and Linux (Konserwacja agenta usługi Log Analytics dla systemów Windows i Linux oraz zarządzanie nim)

Po początkowym wdrożeniu agenta systemu Windows lub Linux usługi Log Analytics w usłudze Azure Monitor może być konieczne ponowne skonfigurowanie agenta, uaktualnienie go lub usunięcie go z komputera, jeśli osiągnął etap przejścia na emeryturę w cyklu życia. Te rutynowe zadania konserwacyjne można łatwo zarządzać ręcznie lub za pomocą automatyzacji, co zmniejsza zarówno błędy operacyjne, jak i wydatki.

## <a name="upgrading-agent"></a>Agent uaktualniania

Agent analizy dzienników dla systemów Windows i Linux można uaktualnić do najnowszej wersji ręcznie lub automatycznie w zależności od scenariusza wdrażania i środowiska, w które jest uruchomiona maszyna wirtualna. Następujące metody mogą służyć do uaktualnienia agenta.

| Środowisko | Metoda instalacji | Metoda uaktualniania |
|--------|----------|-------------|
| Maszyna wirtualna platformy Azure | Rozszerzenie maszyny Wirtualnej agenta usługi Log Analytics dla systemu Windows/Linux | Agent jest automatycznie uaktualniany domyślnie, chyba że szablon usługi Azure Resource Manager został skonfigurowany do rezygnacji, ustawiając właściwość *autoUpgradeMinorVersion* na **false**. |
| Niestandardowe obrazy maszyn wirtualnych platformy Azure | Ręczna instalacja agenta usługi Log Analytics dla systemu Windows/Linux | Aktualizowanie maszyn wirtualnych do najnowszej wersji agenta musi być wykonywane z wiersza polecenia z uruchomionym pakietem instalatora Windows lub pakietem skryptów powłoki samorozłącznej i instalowalnej systemu Linux.|
| Maszyny wirtualne spoza platformy Azure | Ręczna instalacja agenta usługi Log Analytics dla systemu Windows/Linux | Aktualizowanie maszyn wirtualnych do najnowszej wersji agenta musi być wykonywane z wiersza polecenia z uruchomionym pakietem instalatora Windows lub pakietem skryptów powłoki samorozłącznej i instalowalnej systemu Linux. |

### <a name="upgrade-windows-agent"></a>Uaktualnienie agenta systemu Windows 

Aby zaktualizować agenta na maszynie Wirtualnej systemu Windows do najnowszej wersji, która nie została zainstalowana przy użyciu rozszerzenia maszyny Wirtualnej\<analizy\>dzienników, należy uruchomić go z wiersza polecenia, skryptu lub innego rozwiązania automatyzacji lub przy użyciu Kreatora instalacji platformy MMASetup .msi.  

Najnowszą wersję agenta systemu Windows można pobrać z obszaru roboczego usługi Log Analytics, wykonując następujące kroki.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).

2. W witrynie Azure portal kliknij pozycję **Wszystkie usługi**. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz **obszary robocze usługi Log Analytics**.

3. Na liście obszarów roboczych usługi Log Analytics wybierz obszar roboczy.

4. W obszarze roboczym Analiza dzienników wybierz pozycję **Ustawienia zaawansowane,** a następnie wybierz pozycję **Połączone źródła**i na koniec **pozycję Serwery systemu Windows**.

5. Na stronie **Serwery systemu Windows** wybierz odpowiednią wersję **programu Pobierz agenta Windows** do pobrania w zależności od architektury procesora systemu operacyjnego Windows.

>[!NOTE]
>Podczas uaktualniania agenta usługi Log Analytics dla systemu Windows nie obsługuje konfigurowania ani ponownego konfigurowania obszaru roboczego do raportowania. Aby skonfigurować agenta, należy postępować zgodnie z jedną z obsługiwanych metod wymienionych w obszarze [Dodawanie lub usuwanie obszaru roboczego](#adding-or-removing-a-workspace).
>

#### <a name="to-upgrade-using-the-setup-wizard"></a>Aby uaktualnić za pomocą Kreatora instalacji

1. Zaloguj się do komputera przy użyciu konta z uprawnieniami administracyjnymi.

2. Uruchom **program MMASetup-\<platformę\>.exe,** aby uruchomić Kreatora instalacji.

3. Na pierwszej stronie Kreatora instalacji kliknij przycisk **Dalej**.

4. W oknie dialogowym **Ustawienia agenta monitorowania firmy Microsoft** kliknij pozycję Zgadzam **się** na zaakceptowanie umowy licencyjnej.

5. W oknie dialogowym **Instalator programu Microsoft Monitoring Agent** kliknij przycisk **Uaktualnij**. Na stronie stanu wyświetlany będzie postęp uaktualnienia.

6. Po **pomyślnym zakończeniu konfiguracji programu Microsoft Monitoring Agent.** zostanie wyświetlona strona, kliknij przycisk **Zakończ**.

#### <a name="to-upgrade-from-the-command-line"></a>Aby uaktualnić z wiersza polecenia

1. Zaloguj się do komputera przy użyciu konta z uprawnieniami administracyjnymi.

2. Aby wyodrębnić pliki instalacyjne agenta, `MMASetup-<platform>.exe /c` z wiersza polecenia z podwyższonym poziomem uprawnień zostanie uruchomiony monit o ścieżkę wyodrębniania plików. Alternatywnie można określić ścieżkę, `MMASetup-<platform>.exe /c /t:<Full Path>`przekazując argumenty .

3. Uruchom następujące polecenie (w którym D:\ określa lokalizację pliku dziennika uaktualnienia).

    ```dos
    setup.exe /qn /l*v D:\logs\AgentUpgrade.log AcceptEndUserLicenseAgreement=1
    ```

### <a name="upgrade-linux-agent"></a>Uaktualnienie agenta Linuksa 

Uaktualnienie z wcześniejszych wersji (>1.0.0-47) jest obsługiwane. Wykonanie instalacji `--upgrade` za pomocą polecenia spowoduje uaktualnienie wszystkich składników agenta do najnowszej wersji.

Uruchom następujące polecenie, aby uaktualnić agenta.

`sudo sh ./omsagent-*.universal.x64.sh --upgrade`

## <a name="adding-or-removing-a-workspace"></a>Dodawanie lub usuwanie obszaru roboczego

### <a name="windows-agent"></a>Agent systemu Windows
Kroki opisane w tej sekcji są konieczne, gdy chcesz nie tylko ponownie skonfigurować agenta systemu Windows do raportowania do innego obszaru roboczego lub usunięcia obszaru roboczego z jego konfiguracji, ale także wtedy, gdy chcesz skonfigurować agenta do raportowania do więcej niż jednego obszaru roboczego (zwykle zwana multi-homing). Konfigurowanie agenta systemu Windows do raportowania do wielu obszarów roboczych można wykonać tylko po wstępnej konfiguracji agenta i przy użyciu metod opisanych poniżej.    

#### <a name="update-settings-from-control-panel"></a>Aktualizowanie ustawień w Panelu sterowania

1. Zaloguj się do komputera przy użyciu konta z uprawnieniami administracyjnymi.

2. Otwórz **Panel sterowania**.

3. Wybierz pozycję **Microsoft Monitoring Agent,** a następnie kliknij kartę **Usługi Azure Log Analytics.**

4. Jeśli usunięcie obszaru roboczego zostanie zaznaczone, a następnie kliknij przycisk **Usuń**. Powtórz ten krok dla każdego innego obszaru roboczego, do którego agent ma przestać raportować.

5. W przypadku dodawania obszaru roboczego kliknij przycisk **Dodaj** i w oknie **dialogowym Dodawanie obszaru roboczego usługi Log Analytics** wklej identyfikator obszaru roboczego i klucz obszaru roboczego (klucz podstawowy). Jeśli komputer powinien wysyłać raporty do obszaru roboczego usługi Log Analytics w chmurze Azure dla instytucji rządowych, wybierz Wersja platformy Azure dla administracji USA z listy rozwijanej Azure Cloud.

6. Kliknij przycisk **OK**, aby zapisać zmiany.

#### <a name="remove-a-workspace-using-powershell"></a>Usuwanie obszaru roboczego przy użyciu programu PowerShell

```powershell
$workspaceId = "<Your workspace Id>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.RemoveCloudWorkspace($workspaceId)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-commercial-using-powershell"></a>Dodawanie obszaru roboczego w komercyjnej platformie Azure przy użyciu programu PowerShell

```powershell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-for-us-government-using-powershell"></a>Dodawanie obszaru roboczego na platformie Azure dla instytucji rządowych stanów Zjednoczonych przy użyciu programu PowerShell

```powershell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey, 1)
$mma.ReloadConfiguration()
```

>[!NOTE]
>Jeśli wiersz polecenia lub skrypt był wcześniej używany do instalowania `EnableAzureOperationalInsights` lub `AddCloudWorkspace` konfigurowania agenta, został zastąpiony przez i `RemoveCloudWorkspace`.
>

### <a name="linux-agent"></a>Agent Linuksa
W poniższych krokach pokazano, jak ponownie skonfigurować agenta systemu Linux, jeśli zdecydujesz się zarejestrować go w innym obszarze roboczym lub usunąć obszar roboczy z jego konfiguracji.

1. Aby sprawdzić, czy jest zarejestrowany w obszarze roboczym, uruchom następujące polecenie:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l`

    Powinien zwrócić stan podobny do następującego przykładu:

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

    Ważne jest, że stan pokazuje również agent jest uruchomiony, w przeciwnym razie następujące kroki, aby ponownie skonfigurować agenta nie zakończy się pomyślnie.

2. Jeśli jest już zarejestrowany w obszarze roboczym, usuń zarejestrowany obszar roboczy, uruchamiając następujące polecenie. W przeciwnym razie, jeśli nie jest zarejestrowany, przejdź do następnego kroku.

    `/opt/microsoft/omsagent/bin/omsadmin.sh -X`

3. Aby zarejestrować się w innym obszarze roboczym, uruchom następujące polecenie:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <shared key> [-d <top level domain>]`
    
4. Aby sprawdzić, czy zmiany weszły w życie, uruchom następujące polecenie:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l`

    Powinien zwrócić stan podobny do następującego przykładu:

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

Usługa agenta nie musi być ponownie uruchomiona, aby zmiany zostały wprowadzone.

## <a name="update-proxy-settings"></a>Aktualizowanie ustawień serwera proxy
Aby skonfigurować agenta do komunikowania się z usługą za pośrednictwem serwera proxy lub [bramy usługi Log Analytics](gateway.md) po wdrożeniu, użyj jednej z następujących metod, aby wykonać to zadanie.

### <a name="windows-agent"></a>Agent systemu Windows

#### <a name="update-settings-using-control-panel"></a>Aktualizowanie ustawień za pomocą Panelu sterowania

1. Zaloguj się do komputera przy użyciu konta z uprawnieniami administracyjnymi.

2. Otwórz **Panel sterowania**.

3. Wybierz pozycję **Microsoft Monitoring Agent,** a następnie kliknij kartę **Ustawienia serwera proxy.**

4. Kliknij pozycję **Użyj serwera proxy** i podaj adres URL oraz numer portu serwera proxy lub bramy. Jeśli Twój serwer proxy lub brama usługi Log Analytics wymaga uwierzytelniania, wpisz nazwę użytkownika i hasło, aby się uwierzytelnić, a następnie kliknij przycisk **OK**.

#### <a name="update-settings-using-powershell"></a>Aktualizowanie ustawień przy użyciu programu PowerShell

Skopiuj poniższy przykładowy kod programu PowerShell, zaktualizuj go o informacje specyficzne dla danego środowiska i zapisz go z rozszerzeniem nazwy pliku PS1. Uruchom skrypt na każdym komputerze, który łączy się bezpośrednio z obszarem roboczym usługi Log Analytics w usłudze Azure Monitor.

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

### <a name="linux-agent"></a>Agent Linuksa
Wykonaj następujące kroki, jeśli komputery z systemem Linux muszą komunikować się za pośrednictwem serwera proxy lub bramy usługi Log Analytics. Wartość konfiguracji serwera proxy ma następującą składnię `[protocol://][user:password@]proxyhost[:port]`. Właściwość *proxyhost* akceptuje w pełni kwalifikowaną nazwę domeny lub adres IP serwera proxy.

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
Aby odinstalować agenta systemu Windows lub Linux za pomocą wiersza polecenia lub kreatora konfiguracji, należy użyć jednej z poniższych procedur.

### <a name="windows-agent"></a>Agent systemu Windows

#### <a name="uninstall-from-control-panel"></a>Odinstalowywanie z Panelu sterowania
1. Zaloguj się do komputera przy użyciu konta z uprawnieniami administracyjnymi.

2. W **Panelu sterowania**kliknij pozycję Programy i **funkcje**.

3. W **programie Programy i funkcje**kliknij pozycję Microsoft Monitoring **Agent**, kliknij pozycję **Odinstaluj**, a następnie kliknij przycisk **Tak**.

>[!NOTE]
>Kreator instalacji agenta można również uruchomić, klikając dwukrotnie **platformę\<\>MMASetup .exe**, która jest dostępna do pobrania z obszaru roboczego w witrynie Azure portal.

#### <a name="uninstall-from-the-command-line"></a>Odinstalowywanie z wiersza polecenia
Pobrany plik dla agenta jest samodzielnym pakietem instalacyjnym utworzonym za pomocą iExpress. Program instalacyjny dla agenta i pliki pomocnicze są zawarte w pakiecie i muszą być wyodrębnione w celu prawidłowego odinstalowania za pomocą wiersza polecenia pokazano w poniższym przykładzie.

1. Zaloguj się do komputera przy użyciu konta z uprawnieniami administracyjnymi.

2. Aby wyodrębnić pliki instalacyjne agenta, `extract MMASetup-<platform>.exe` z wiersza polecenia z podwyższonym poziomem uprawnień zostanie uruchomiony monit o ścieżkę wyodrębniania plików. Alternatywnie można określić ścieżkę, `extract MMASetup-<platform>.exe /c:<Path> /t:<Path>`przekazując argumenty . Aby uzyskać więcej informacji na temat przełączników wiersza polecenia obsługiwanych przez iExpress, zobacz [Przełączniki wiersza polecenia dla IExpress,](https://support.microsoft.com/help/197147/command-line-switches-for-iexpress-software-update-packages) a następnie zaktualizuj przykład zgodnie z potrzebami.

3. W wierszu `%WinDir%\System32\msiexec.exe /x <Path>:\MOMAgent.msi /qb`polecenia wpisz .

### <a name="linux-agent"></a>Agent Linuksa
Aby usunąć agenta, uruchom poniższe polecenie na komputerze z systemem Linux. Argument *--purge* powoduje całkowite usunięcie agenta i jego konfiguracji.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

## <a name="configure-agent-to-report-to-an-operations-manager-management-group"></a>Konfigurowanie agenta do raportowania do grupy zarządzania programu Operations Manager

### <a name="windows-agent"></a>Agent systemu Windows
Wykonaj następujące kroki, aby skonfigurować agenta usługi Log Analytics dla systemu Windows do raportowania do grupy zarządzania programu Operations Manager w programie System Center.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

1. Zaloguj się do komputera przy użyciu konta z uprawnieniami administracyjnymi.

2. Otwórz **Panel sterowania**.

3. Kliknij pozycję **Agent monitorowania firmy Microsoft,** a następnie kliknij kartę **Program Operations Manager.**

4. Jeśli serwery programu Operations Manager są integrowane z usługą Active Directory, kliknij pozycję **Automatycznie aktualizuj przypisania grup zarządzania z usług AD DS**.

5. Kliknij **przycisk Dodaj,** aby otworzyć okno **dialogowe Dodawanie grupy zarządzania.**

6. W polu **Nazwa grupy zarządzania** wpisz nazwę grupy zarządzania.

7. W polu **Podstawowy serwer zarządzania** wpisz nazwę komputera podstawowego serwera zarządzania.

8. W polu **Port serwera zarządzania** wpisz numer portu TCP.

9. W obszarze **Konto akcji agenta**wybierz konto System lokalny lub konto domeny lokalnej.

10. Kliknij **przycisk OK,** aby zamknąć okno dialogowe **Dodawanie grupy zarządzania,** a następnie kliknij przycisk **OK,** aby zamknąć okno dialogowe **Właściwości agenta monitorowania firmy Microsoft.**

### <a name="linux-agent"></a>Agent Linuksa
Wykonaj następujące kroki, aby skonfigurować agenta usługi Log Analytics dla systemu Linux do raportowania do grupy zarządzania programu Operations Manager w programie System Center.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

1. Edytowanie pliku`/etc/opt/omi/conf/omiserver.conf`

2. Upewnij się, że `httpsport=` linia rozpoczynająca się od definiuje port 1270. Takie jak:`httpsport=1270`

3. Uruchom ponownie serwer OMI:`sudo /opt/omi/bin/service_control restart`

## <a name="next-steps"></a>Następne kroki

- Przejrzyj [rozwiązywanie problemów z agentem systemu Linux,](agent-linux-troubleshoot.md) jeśli wystąpią problemy podczas instalowania agenta systemu Linux lub zarządzania nim.

- Przejrzyj [rozwiązywanie problemów z agentem systemu Windows,](agent-windows-troubleshoot.md) jeśli wystąpią problemy podczas instalowania agenta systemu Windows lub zarządzania nim.
