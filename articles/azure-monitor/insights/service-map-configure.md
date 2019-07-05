---
title: Konfigurowanie rozwiązania Service Map na platformie Azure | Dokumentacja firmy Microsoft
description: Usługa Service Map jest rozwiązaniem platformy Azure, które automatycznie odnajduje składniki aplikacji w systemach Windows i Linux oraz mapuje komunikację między usługami. Ten artykuł zawiera szczegółowe informacje dotyczące wdrażania rozwiązania Service Map w danym środowisku i korzystania z niego w różnych scenariuszach.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: d3d66b45-9874-4aad-9c00-124734944b2e
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/28/2019
ms.author: magoedte
ms.openlocfilehash: 8f2b74c4c091aed0a1b5889b0a07d44d450d1922
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2019
ms.locfileid: "67477083"
---
# <a name="configure-service-map-in-azure"></a>Konfigurowanie rozwiązania Service Map na platformie Azure

Mapa usługi automatycznie odnajduje składniki aplikacji w systemach Windows i Linux oraz mapuje komunikację między usługami. Służy on do wyświetlenia serwerów, prawdopodobnie z nich--wzajemnie połączonych systemów dostarczających krytycznych usług. Usługa Service Map Pokazuje połączenia między serwerami, procesami i portami w dowolnej architekturze połączenia TCP bez konieczności konfiguracji, innej niż Instalacja agenta.

W tym artykule opisano konfigurowanie agentów rozwiązania Service Map i dołączania. Aby uzyskać informacje na temat korzystania z rozwiązania Service Map, zobacz [używać rozwiązania mapy usługi na platformie Azure]( service-map.md).

## <a name="supported-azure-regions"></a>Obsługiwane regiony platformy Azure

Mapa usługi jest obecnie dostępna w następujących regionach platformy Azure:
- East US
- Środkowo-zachodnie stany USA
- Kanada Środkowa
- Południowe Zjednoczone Królestwo
- Europa Zachodnia
- Azja Południowo-Wschodnia

## <a name="supported-windows-operating-systems"></a>Obsługiwane systemy operacyjne Windows

Poniższa sekcja Lista obsługiwanych systemów operacyjnych dla agenta zależności na Windows. 

>[!NOTE]
>Usługa Service Map obsługuje tylko 64-bitowych platform.
>

### <a name="windows-server"></a>Windows Server

- Windows Server 2019
- System Windows Server 2016 1803
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

### <a name="windows-desktop"></a>Pulpit systemu Windows

- Windows 10 w wersji 1803
- Windows 10
- Windows 8.1
- Windows 8
- Windows 7

## <a name="supported-linux-operating-systems"></a>Obsługiwane systemy operacyjne Linux

Poniższa sekcja Lista obsługiwanych systemów operacyjnych dla agenta zależności w systemie Linux.  

- Obsługiwane są tylko wersje domyślne i wersje SMP jądra systemu Linux.
- Niestandardowe wydania jądra, takie jak PAE i Xen, nie są obsługiwane dla żadnej dystrybucji systemu Linux. Na przykład systemu z wersji ciąg "2.6.16.21-0.8-xen" nie jest obsługiwane.
- Niestandardowe jądra, łącznie z ponownymi kompilacjami standardowych jąder, nie są obsługiwane.

### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Wersja systemu operacyjnego | Wersja jądra |
|:--|:--|
| 7.4 | 3.10.0-693 |
| 7.5 | 3.10.0-862 |
| 7.6 | 3.10.0-957 |

### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Wersja systemu operacyjnego | Wersja jądra |
|:--|:--|
| 6.9 | 2.6.32-696 |
| 6.10 | 2.6.32-754 |

### <a name="centosplus"></a>CentOSPlus

| Wersja systemu operacyjnego | Wersja jądra |
|:--|:--|
| 6.9 | 2.6.32-696.18.7<br>2.6.32-696.30.1 |
| 6.10 | 2.6.32-696.30.1<br>2.6.32-754.3.5 |

### <a name="ubuntu-server"></a>Ubuntu Server

| Wersja systemu operacyjnego | Wersja jądra |
|:--|:--|
| Ubuntu 18.04 | kernel 4.15.\*<br>4.18* |
| Systemu Ubuntu 16.04.3 | jądra 4.15. * |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| Wersja systemu operacyjnego | Wersja jądra
|:--|:--|
|12 Z DODATKIEM SP2 | 4.4. * |
|12 Z DODATKIEM SP3 | 4.4. * |
|12 SP4 | 4.4. * |
|12 SP4 | Dopasowane Azure jądra |

## <a name="dependency-agent-downloads"></a>Pobieranie agenta zależności

| Plik | System operacyjny | Wersja | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.8.1 | 622C99924385CBF539988D759BCFDC9146BB157E7D577C997CDD2674E27E08DD |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.8.1 | 3037934A5D3FB7911D5840A9744AE9F980F87F620A7F7B407F05E276FE7AE4A8 |

## <a name="connected-sources"></a>Połączone źródła

Usługa Service Map, dane są pobierane z Microsoft Dependency agent. Agent zależności zależy od agenta usługi Log Analytics dla jego połączenia z usługą Log Analytics. Oznacza to, że serwer musi mieć agenta usługi Log Analytics, zainstalować i skonfigurować za pomocą agenta zależności.  W poniższej tabeli opisano połączone źródła, które obsługuje rozwiązania mapy usługi.

| Połączone źródło | Obsługiwane | Opis |
|:--|:--|:--|
| Agenci dla systemu Windows | Yes | Usługa Service Map, analizuje i zbiera dane z komputerów Windows. <br><br>Oprócz [agenta usługi Log Analytics dla Windows](../../azure-monitor/platform/log-analytics-agent.md), agenci Windows wymagają Microsoft Dependency agent. Zobacz obsługiwane systemy operacyjne, aby uzyskać pełną listę wersji systemów operacyjnych. |
| Agenci dla systemu Linux | Yes | Usługa Service Map, analizuje i zbiera dane z komputerów z systemem Linux. <br><br>Oprócz [agenta usługi Log Analytics dla systemu Linux](../../azure-monitor/platform/log-analytics-agent.md), agenci dla systemu Linux wymaga program Microsoft Dependency agent. Zobacz obsługiwane systemy operacyjne, aby uzyskać pełną listę wersji systemów operacyjnych. |
| Grupa zarządzania programu System Center Operations Manager | Yes | Usługa Service Map, analizuje i zbiera dane z agentów systemu Windows i Linux w połączonej [grupy zarządzania programu System Center Operations Manager](../../azure-monitor/platform/om-agents.md). <br><br>Wymagane jest bezpośrednie połączenie z komputera agenta programu System Center Operations Manager do usługi Log Analytics. |
| Konto magazynu Azure | Nie | Mapa usługi zbiera dane z komputerów agentów, dzięki czemu nie ma żadnych danych od niego mają być zbierane z usługi Azure Storage. |

Na Windows, Microsoft Monitoring Agent (MMA) jest używane zarówno przez System Center Operations Manager i usługi Log Analytics zbieranie i wysyłanie danych monitorowania. (Ten agent jest nazywane agenta programu System Center Operations Manager, agenta usługi Log Analytics, Agent MMA lub Agent bezpośredni, w zależności od kontekstu). System Center Operations Manager i usługi Log Analytics zawiera różne out-gotową do wersji programu MMA. Każda z tych wersji może raportować do programu System Center Operations Manager, do usługi Log Analytics lub do obu miejsc.  

W systemie Linux agent usługi Log Analytics dla systemu Linux zbiera informacje i wysyła dane do usługi Log Analytics monitorowania. Za pomocą rozwiązania Service Map na serwerach agentów usługi Log Analytics, bezpośrednio z usługą jest połączonych lub raporty z grupą zarządzania programu Operations Manager zintegrowany z usługą Log Analytics.  

W tym artykule będziemy odnosić się do wszystkich agentów, czy Linux lub Windows połączonych z grupą zarządzania programu System Center Operations Manager lub bezpośrednio do usługi Log Analytics, jako *agenta usługi Log Analytics*. 

Agent rozwiązania Service Map nie przesyła wszystkie dane, a nie wymaga wprowadzania zmian w zapory i porty. Agenta usługi Log Analytics w usłudze Log Analytics, przesyłania danych na mapie usługi zawsze, bezpośrednio lub za pośrednictwem bramy usługi Log Analytics.

![Agenci rozwiązania Service Map](media/service-map-configure/agents.png)

Jeśli jesteś klientem programu System Center Operations Manager z grupy zarządzania podłączone do usługi Log Analytics:

- Jeśli agentów programu System Center Operations Manager ma dostęp do Internetu, aby nawiązać połączenie z usługi Log Analytics, żadna dodatkowa konfiguracja jest wymagana.  
- Agentów programu System Center Operations Manager nie może uzyskać dostęp do usługi Log Analytics, za pośrednictwem Internetu, należy skonfigurować bramę usługi Log Analytics do pracy z programu System Center Operations Manager.
  
Jeśli komputery Windows lub Linux bezpośrednio nie może połączyć się z usługą, należy skonfigurować agenta usługi Log Analytics, aby nawiązać połączenie z obszaru roboczego usługi Log Analytics, za pomocą bramy. Aby uzyskać więcej informacji na temat sposobu wdrażania i konfigurowania bramy usługi Log Analytics, zobacz [połączyć komputery bez dostępu do Internetu za pomocą bramy usługi Log Analytics](../../azure-monitor/platform/gateway.md).  

### <a name="management-packs"></a>Pakiety administracyjne

Po aktywowaniu mapy usługi w obszarze roboczym usługi Log Analytics pakietu administracyjnego 300 KB są przekazywane do wszystkich serwerów Windows, w tym obszarze roboczym. Jeśli używasz agentów programu System Center Operations Manager w [podłączonej grupy zarządzania](../../azure-monitor/platform/om-agents.md), pakiet administracyjny rozwiązania Service Map jest wdrażany z programu System Center Operations Manager. 

Pakiet administracyjny nosi nazwę Microsoft.IntelligencePacks.ApplicationDependencyMonitor. Jest ona zapisywana w folderze %Programfiles%\Microsoft Monitoring Agent\Agent\Health usługi State\Management Packs\. Źródła danych, które korzysta z pakietu administracyjnego jest % Program files%\Microsoft monitorowanie Agent\Agent\Health usługi State\Resources\<AutoGeneratedID > \ Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll.

## <a name="data-collection"></a>Zbieranie danych

Można oczekiwać, że każdego agenta w celu przesłania około 25 MB na dzień, w zależności od tego, jak złożone są zależności systemu. Każdy agent wysyła dane zależności mapy usług co 15 sekund.  

Agenta zależności zwykle zużywa 0,1 procent pamięci systemowej i procent 0,1 systemu procesora CPU.

## <a name="diagnostic-and-usage-data"></a>Dane diagnostyczne i dane dotyczące użycia

Firma Microsoft automatycznie zbiera dane dotyczące użycia i wydajności przez korzystanie z usługi mapy usługi. Firma Microsoft używa tych danych do zapewniania i poprawiania jakości, bezpieczeństwa i integralności usługi mapy usługi. Dane obejmują informacje o konfiguracji oprogramowania, takie jak wersja i system operacyjny. Zawiera także adres IP, nazwę DNS i nazwę stacji roboczej zapewnić dokładne i wydajne funkcje do rozwiązywania problemów. Nie zbieramy nazwisk, adresów ani innych informacji kontaktowych.

Aby uzyskać więcej informacji na temat zbierania i wykorzystywania danych, zobacz [Microsoft Online Services Privacy Statement](https://go.microsoft.com/fwlink/?LinkId=512132).

## <a name="installation"></a>Instalacja

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="azure-vm-extension"></a>Rozszerzenie maszyny Wirtualnej platformy Azure

Rozszerzenie jest dostępne zarówno dla Windows (DependencyAgentWindows) i systemu Linux (DependencyAgentLinux) i agenta zależności można łatwo wdrożyć na maszynach wirtualnych platformy Azure, używając [rozszerzenie maszyny Wirtualnej Azure](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-features).  Rozszerzenie maszyny Wirtualnej platformy Azure Windows i maszyn wirtualnych systemu Linux przy użyciu skryptu programu PowerShell lub bezpośrednio na maszynie Wirtualnej przy użyciu szablonu usługi Azure Resource Manager można wdrożyć agenta zależności.  W przypadku wdrożenia agenta za pomocą rozszerzenia maszyny Wirtualnej platformy Azure, Twoi agenci są automatycznie aktualizowane do najnowszej wersji.

Aby wdrożyć rozszerzenie maszyny Wirtualnej platformy Azure przy użyciu programu PowerShell, służy poniższy przykład:

```powershell
#
# Deploy the Dependency agent to every VM in a Resource Group
#

$version = "9.4"
$ExtPublisher = "Microsoft.Azure.Monitoring.DependencyAgent"
$OsExtensionMap = @{ "Windows" = "DependencyAgentWindows"; "Linux" = "DependencyAgentLinux" }
$rmgroup = "<Your Resource Group Here>"

Get-AzVM -ResourceGroupName $rmgroup |
ForEach-Object {
    ""
    $name = $_.Name
    $os = $_.StorageProfile.OsDisk.OsType
    $location = $_.Location
    $vmRmGroup = $_.ResourceGroupName
    "${name}: ${os} (${location})"
    Date -Format o
    $ext = $OsExtensionMap.($os.ToString())
    $result = Set-AzVMExtension -ResourceGroupName $vmRmGroup -VMName $name -Location $location `
    -Publisher $ExtPublisher -ExtensionType $ext -Name "DependencyAgent" -TypeHandlerVersion $version
    $result.IsSuccessStatusCode
}
```

Jest jeszcze łatwiejsze sposób, aby upewnić się, że zainstalowano agenta zależności na maszynach wirtualnych do uwzględnienia agenta w szablonie usługi Azure Resource Manager.  Poniższy przykład kodu JSON można dodać do *zasobów* części szablonu.

```JSON
"type": "Microsoft.Compute/virtualMachines/extensions",
"name": "[concat(parameters('vmName'), '/DependencyAgent')]",
"apiVersion": "2017-03-30",
"location": "[resourceGroup().location]",
"dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]"
],
"properties": {
    "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
    "type": "DependencyAgentWindows",
    "typeHandlerVersion": "9.4",
    "autoUpgradeMinorVersion": true
}

```

### <a name="install-the-dependency-agent-on-microsoft-windows"></a>Instalowanie agenta zależności na Microsoft Windows

Agent zależności można zainstalować ręcznie na komputerach Windows przez uruchomienie `InstallDependencyAgent-Windows.exe`. Po uruchomieniu tego pliku wykonywalnego bez żadnych opcji, uruchamia Kreatora instalacji, które można wykonać w celu interaktywnego instalowania.  

>[!NOTE]
>Aby zainstalować lub odinstalować agenta, wymagane są uprawnienia administratora.

Aby zainstalować agenta zależności na każdym komputerze, Windows, wykonaj następujące kroki:

1.  Instalacja agenta usługi Log Analytics dla Windows zgodnie z jedną z metod opisanych w [omówienie agenta usługi Log Analytics](../../azure-monitor/platform/log-analytics-agent.md).
2.  Pobierz agenta Windows i uruchom go przy użyciu następującego polecenia: 
    
    `InstallDependencyAgent-Windows.exe`

3.  Postępuj zgodnie z Kreatora instalacji, aby zainstalować agenta.
4.  Jeśli agenta zależności nie powiedzie się, sprawdź dzienniki Aby uzyskać szczegółowe informacje o błędzie. Na agentach Windows katalog dziennika jest %Programfiles%\Microsoft Agent\logs zależności. 

#### <a name="windows-command-line"></a>Wiersz polecenia systemu Windows

Użyj opcji z poniższej tabeli, aby przeprowadzić instalację z poziomu wiersza polecenia. Aby wyświetlić listę flag instalacji, uruchom instalatora, używając flagi /? w następujący sposób.

    InstallDependencyAgent-Windows.exe /?

| Flaga | Opis |
|:--|:--|
| /? | Pobierz listę opcji wiersza polecenia. |
| /S | Przeprowadź instalację cichą bez monitowania użytkownika. |

Domyślnie pliki agenta zależności Windows znajdują się w C:\Program Files\Microsoft Dependency Agent.

### <a name="install-the-dependency-agent-on-linux"></a>Instalowanie agenta zależności w systemie Linux

Agent zależności jest zainstalowany na komputerach z systemem Linux z `InstallDependencyAgent-Linux64.bin`, skrypt powłoki, za pomocą samorozpakowujący się plik binarny. Można uruchomić go za pomocą `sh` lub Dodaj uprawnienia do wykonywania do pliku sam.

>[!NOTE]
> Aby zainstalować lub skonfigurować agenta, wymagany jest dostęp na poziomie administratora.

Aby zainstalować agenta zależności na każdy komputer z systemem Linux, wykonaj następujące kroki:

1.  Zainstaluj agenta usługi Log Analytics, zgodnie z jedną z metod opisanych w [omówienie agenta usługi Log Analytics](../../azure-monitor/platform/log-analytics-agent.md).
2.  Zainstaluj agenta zależności systemu Linux jako użytkownik główny, uruchamiając następujące polecenie:
    
    `sh InstallDependencyAgent-Linux64.bin`

3.  Jeśli agenta zależności nie powiedzie się, sprawdź dzienniki Aby uzyskać szczegółowe informacje o błędzie. Na agentach systemu Linux katalog dziennika jest /var/opt/microsoft/dependency-agent/log.

Aby wyświetlić listę flag instalacji, uruchom instalację programu za pomocą — flagi w następujący sposób.

    InstallDependencyAgent-Linux64.bin -help

| Flaga | Opis |
|:--|:--|
| -help | Pobierz listę opcji wiersza polecenia. |
| -s | Przeprowadź instalację cichą bez monitowania użytkownika. |
| — Sprawdź | Sprawdź uprawnienia i system operacyjny, ale nie instaluj agenta. |

Pliki agenta zależności są umieszczone w następujących katalogach:

| Pliki | Lokalizacja |
|:--|:--|
| Pliki jądra | /opt/microsoft/dependency-agent |
| Pliki dziennika | /var/opt/microsoft/dependency-agent/log |
| Plik konfiguracji | /etc/opt/microsoft/dependency-agent/config |
| Pliki wykonywalne usługi | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Pliki binarne magazynu | /var/opt/microsoft/dependency-agent/storage |

## <a name="installation-script-examples"></a>Przykłady skryptów instalacji

Łatwe wdrażanie agenta zależności na wiele serwerów na raz, w poniższym przykładzie skrypt znajduje się pobrać i zainstalować agenta zależności w systemie Windows lub Linux.

### <a name="powershell-script-for-windows"></a>Skrypt programu PowerShell dla systemu Windows

```powershell
Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S
```

### <a name="shell-script-for-linux"></a>Skrypt powłoki dla systemu Linux

```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
sudo sh InstallDependencyAgent-Linux64.bin -s
```

## <a name="desired-state-configuration"></a>Konfiguracja żądanego stanu

Aby wdrożyć agenta zależności za pomocą Desired State Configuration (DSC), możesz użyć modułu xPSDesiredStateConfiguration z poniższy przykład kodu:

```powershell
configuration ServiceMap {

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    $DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"

    Node localhost
    {
        # Download and install the Dependency agent
        xRemoteFile DAPackage 
        {
            Uri = "https://aka.ms/dependencyagentwindows"
            DestinationPath = $DAPackageLocalPath
        }

        xPackage DA
        {
            Ensure="Present"
            Name = "Dependency Agent"
            Path = $DAPackageLocalPath
            Arguments = '/S'
            ProductId = ""
            InstalledCheckRegKey = "HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent"
            InstalledCheckRegValueName = "DisplayName"
            InstalledCheckRegValueData = "Dependency Agent"
            DependsOn = "[xRemoteFile]DAPackage"
        }
    }
}
```

## <a name="remove-the-dependency-agent"></a>Usuń agenta zależności

### <a name="uninstall-agent-on-windows"></a>Odinstalowywanie agenta na Windows

Administrator można odinstalować agenta zależności dla Windows za pomocą Panelu sterowania.

Administrator może także uruchomić %Programfiles%\Microsoft Agent\Uninstall.exe zależności do odinstalowania agenta zależności.

### <a name="uninstall-agent-on-linux"></a>Odinstaluj agenta w systemie Linux

Za pomocą następującego polecenia, można odinstalować agenta zależności z systemem Linux.

RHEL, CentOs lub Oracle:

```
sudo rpm -e dependency-agent
```

Ubuntu:

```
sudo apt -y purge dependency-agent
```

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli masz problemy z Instalowanie i uruchamianie rozwiązania Service Map, w tej sekcji mogą pomóc. Jeśli nadal nie możesz rozwiązać problemu, skontaktuj się z Microsoft Support.

### <a name="dependency-agent-installation-problems"></a>Problemy z instalacją agenta zależności

#### <a name="installer-prompts-for-a-reboot"></a>Instalator monituje o ponowne uruchomienie komputera

Agent zależności *ogólnie* nie jest wymagane ponowne uruchomienie komputera po instalacji lub odinstalowania. Jednak w niektórych przypadkach rzadkich systemu Windows Server wymaga ponownego uruchomienia, aby kontynuować instalację. Dzieje się tak, gdy zależność, zwykle Microsoft Visual C++ Redistributable, wymaga ponownego uruchomienia ze względu na zablokowany plik.

#### <a name="message-unable-to-install-dependency-agent-visual-studio-runtime-libraries-failed-to-install-code--codenumber-appears"></a>Komunikat "nie można zainstalować agenta zależności: Visual Studio środowiska uruchomieniowego bibliotek instalacja nie powiodła się (kod = [Numer_kodu]) "pojawi się

Program Microsoft Dependency agent jest oparta na bibliotekach środowiska uruchomieniowego programu Microsoft Visual Studio. Jeśli występuje problem podczas instalacji bibliotek, zostanie wyświetlony komunikat. 

Instalatory biblioteki środowiska uruchomieniowego twórz dzienniki w folderze %LOCALAPPDATA%\temp. Plik jest dd_vcredist_arch_yyyymmddhhmmss.log, gdzie *arch* jest "x86" lub "amd64" i *rrrrmmddggmmss* jest data i godzina (zegar 24-godzinny), utworzenia dziennika. Dziennik zawiera szczegółowe informacje o problemie, który blokuje instalację.

Może być przydatne do zainstalowania [najnowsze biblioteki środowiska uruchomieniowego](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) samodzielnie pierwszy.

W poniższej tabeli wymieniono numery kodu i sugerowanymi metodami rozwiązania.

| Kod | Opis | Rozwiązanie |
|:--|:--|:--|
| 0x17 | Instalator biblioteki wymaga aktualizacji Windows, która nie została ona zainstalowana. | Poszukaj w dzienniku Instalatora usługi najnowszej biblioteki.<br><br>Jeśli odwołanie do "Windows8.1-KB2999226-x64.msu" następuje wiersza "Błąd 0x80240017: Nie można wykonać MSU pakietu,"nie ma wymagań wstępnych, można zainstalować aktualizacji KB2999226. Postępuj zgodnie z instrukcjami w sekcji wymagania wstępne [uniwersalnego środowiska uruchomieniowego c. w Windows](https://support.microsoft.com/kb/2999226). Może być konieczne, uruchom usługę Windows Update i ponownie uruchomić wiele razy, aby można było zainstalować wymagania wstępne.<br><br>Ponownie uruchom Instalatora agenta Dependency firmy Microsoft. |

### <a name="post-installation-issues"></a>Problemy z instalacją po

#### <a name="server-doesnt-appear-in-service-map"></a>Serwer nie jest wyświetlane w mapy usługi

Jeśli instalację agenta zależności zakończyła się pomyślnie, ale nie widać na serwerze usługi w ramach rozwiązania mapy usługi:
* Agent zależności zainstalowano pomyślnie? Aby to sprawdzić przez sprawdzanie, czy usługa jest zainstalowana i uruchomiona.<br><br>
**Windows**: Wyszukaj usługę o nazwie "Agent Dependency firmy Microsoft."<br>
**Linux**: Zwróć uwagę na uruchomionego procesu "microsoft--agenta zależności."

* Czy na [bezpłatnej warstwy Operations Management Suite/Log Analytics cenowej](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions)? Bezpłatny plan zezwala na maksymalnie pięciu unikatowych serwerów mapy usługi. Kolejne serwery będą serwerami nie będą widoczne mapy usługi, nawet wtedy, gdy wcześniejsze pięciu już nie wysyłają dane.

* To Twoje wysyłania dziennika serwera i danych o wydajności do usługi Log Analytics? Przejdź do wyszukiwania w dziennikach i uruchom następujące zapytanie dla danego komputera: 

    Sposób użycia | gdy komputer == "Nazwa komputera" | Podsumowanie sum(Quantity), any(QuantityUnit) przez typ danych

Czy został wyświetlony w wynikach różnych zdarzeń? To najnowsze dane? Jeśli tak, usługi Log Analytics Agent jest prawidłowo i komunikacji z usługą Log Analytics. W przeciwnym razie sprawdź agenta na serwerze: [Agent analizy dziennika do rozwiązywania problemów Windows](https://support.microsoft.com/help/3126513/how-to-troubleshoot-monitoring-onboarding-issues) lub [agenta usługi Log Analytics na potrzeby rozwiązywania problemów w systemie Linux](../../azure-monitor/platform/agent-linux-troubleshoot.md).

#### <a name="server-appears-in-service-map-but-has-no-processes"></a>Serwer jest wyświetlany w rozwiązania Service Map, ale żadne procesy nie ma

Jeśli zostanie wyświetlony na mapie usługi serwera, ale nie zawiera on procesu lub połączenia danych, który wskazuje agenta zależności jest zainstalowana i uruchomiona, że sterownik jądra nie została załadowana. 

Sprawdź plik C:\Program Files\Microsoft zależności Agent\logs\wrapper.log (Windows) lub pliku /var/opt/microsoft/dependency-agent/log/service.log (Linux). Ostatnie wiersze pliku powinno wskazywać, dlaczego jądra nie została załadowana. Na przykład jądra mogą nie być obsługiwane w systemie Linux, jeśli zaktualizowane swoje jądra.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [użycia rozwiązania Service Map]( service-map.md) po jej wdrożeniu i skonfigurowaniu.