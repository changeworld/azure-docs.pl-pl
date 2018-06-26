---
title: Konfigurowanie mapy usługi na platformie Azure | Dokumentacja firmy Microsoft
description: Usługa Service Map jest rozwiązaniem platformy Azure, które automatycznie odnajduje składniki aplikacji w systemach Windows i Linux oraz mapuje komunikację między usługami. Ten artykuł zawiera szczegółowe informacje dotyczące wdrażania mapy usługi w danym środowisku i korzystania z niego w różnych scenariuszach.
services: monitoring
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: d3d66b45-9874-4aad-9c00-124734944b2e
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/22/2018
ms.author: daseidma;bwren
ms.openlocfilehash: 872d5f05e4d607c9445d1af5cc9b9cb984c19e11
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36752578"
---
# <a name="configure-service-map-in-azure"></a>Konfigurowanie mapy usługi na platformie Azure
Mapa usługi automatycznie odnajduje składniki aplikacji w systemach Windows i Linux oraz mapuje komunikację między usługami. Służy on do wyświetlania serwerów jako traktować ich — jako połączonych systemy, które dostarczają usług krytycznych. Mapy usług zawiera połączeń między serwerami, procesów i portów w dowolnej architekturze połączenia TCP z konfiguracja nie jest wymagane, innego niż instalacji agenta.

W tym artykule opisano konfigurowanie agentów mapy usługi i przechodzenia do szczegółów. Uzyskać przy użyciu mapy usługi, zobacz [używać rozwiązania mapy usługi na platformie Azure]( monitoring-service-map.md).

## <a name="dependency-agent-downloads"></a>Pobieranie agenta Dependency Agent
| Plik | System operacyjny | Wersja | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.5.0 | 8B8FE0F6B0A9F589C4B7B52945C2C25DF008058EB4D4866DC45EE2485062C9D7 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.5.1 | 09D56EF43703A350FF586B774900E1F48E72FE3671144B5C99BB1A494C201E9E |


## <a name="connected-sources"></a>Połączone źródła
Mapa usług dane są pobierane z Microsoft Dependency Agent. Agent Dependency Agent zależy od agenta pakietu OMS w zakresie połączeń z usługą Log Analytics. Oznacza to, że serwer musi mieć zainstalowany i skonfigurowany najpierw Agent pakietu OMS, a następnie może być zainstalowany Agent zależności. W poniższej tabeli opisano połączonych źródeł, które obsługuje rozwiązania mapy usługi.

| Połączone źródło | Obsługiwane | Opis |
|:--|:--|:--|
| Agenci dla systemu Windows | Yes | Mapa usług analizuje i zbiera dane z komputerów z systemem Windows agenta. <br><br>Oprócz [agenta pakietu OMS](../log-analytics/log-analytics-windows-agent.md), agenci systemu Windows wymagają agenta Microsoft Dependency Agent. Zobacz [obsługiwane systemy operacyjne](#supported-operating-systems), gdzie znajdziesz pełną listę wersji systemu operacyjnego. |
| Agenci dla systemu Linux | Yes | Mapa usług analizuje i zbiera dane z komputerów z systemem Linux agenta. <br><br>Oprócz [agenta pakietu OMS](../log-analytics/log-analytics-linux-agents.md), agenci systemu Linux wymagają agenta Microsoft Dependency Agent. Zobacz [obsługiwane systemy operacyjne](#supported-operating-systems), gdzie znajdziesz pełną listę wersji systemu operacyjnego. |
| Grupa zarządzania programu System Center Operations Manager | Yes | Mapa usług analizuje i zbiera dane z agentów systemu Windows i Linux w połączonych [grupy zarządzania programu System Center Operations Manager](../log-analytics/log-analytics-om-agents.md). <br><br>Wymagane jest bezpośrednie połączenie z komputera agenta programu System Center Operations Manager do usługi Log Analytics. Dane są przesyłane dalej z grupy zarządzania do obszaru roboczego analizy dzienników.|
| Konto magazynu Azure | Nie | Mapy usługi zbiera dane z komputerami agenta, więc nie ma żadnych danych z niego do zbierania z usługi Azure Storage. |

Mapy usługi obsługuje tylko 64-bitowych platform.

W systemie Windows, Microsoft Monitoring Agent (MMA) jest używany zarówno przez System Center Operations Manager i analizy dzienników do zbierania i wysyłania danych monitorowania. (Ten agent jest nazywane agenta programu System Center Operations Manager, Agent pakietu OMS, Agent analizy dziennika, MMA lub bezpośredniego agenta, w zależności od kontekstu.) System Center Operations Manager i analizy dzienników zawiera pole poza z różnych wersji MMA. Każda z tych wersji może raportować do programu System Center Operations Manager, do usługi Log Analytics lub do obu miejsc.  

W systemie Linux, Agent pakietu OMS gromadzi systemu Linux i wysyła dane do analizy dzienników monitorowania. Na serwerach z agentami bezpośredniego OMS lub na serwerach, które są dołączone do analizy dzienników za pośrednictwem grup zarządzania programu System Center Operations Manager, możesz użyć mapy usługi.  

W tym artykule, firma Microsoft będzie odwoływać się do wszystkich agentów — czy Linux lub Windows, czy połączony z grupą zarządzania programu System Center Operations Manager lub bezpośrednio do analizy dzienników — jako *Agent pakietu OMS*. Konkretna nazwa wdrożenia agenta jest używana tylko wtedy, gdy jest wymagana dla kontekstu.

Agent mapy usług nie przesyła wszystkie dane, a nie wymaga zmian zapory lub porty. Agent pakietu OMS z analizą dzienników przesyłania danych na mapie usługi zawsze, bezpośrednio lub za pośrednictwem bramy OMS.

![Agenci mapy usług](media/monitoring-service-map/agents.png)

Jeśli jesteś klientem programu System Center Operations Manager z grupą zarządzania podłączone do analizy dzienników:

- Jeśli agentów programu System Center Operations Manager można uzyskać dostęp do Internetu do nawiązania połączenia analizy dzienników, nie jest wymagana żadna konfiguracja dodatkowych.  
- Agentów programu System Center Operations Manager nie może uzyskać dostęp do analizy dzienników za pośrednictwem Internetu, należy skonfigurować bramę OMS do pracy z programem System Center Operations Manager.
  
Jeśli używasz bezpośredniej Agent pakietu OMS, należy skonfigurować agenta pakietu OMS do połączenia analizy dzienników lub bramy OMS. Brama OMS można pobrać z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=52666). Aby uzyskać więcej informacji na temat sposobu wdrażania i konfigurowania bramy OMS, zobacz [łączenia komputerów bez dostępu do Internetu za pomocą bramy OMS](../log-analytics/log-analytics-oms-gateway.md).  

### <a name="management-packs"></a>Pakiety administracyjne
Po aktywowaniu usługi mapy w obszarze roboczym analizy dzienników pakietu administracyjnego 300 KB są wysyłane do wszystkich serwerów z systemem Windows w tym obszarze roboczym. Jeśli używasz programu System Center Operations Manager agentów w [podłączonej grupy zarządzania](../log-analytics/log-analytics-om-agents.md), mapy usługi pakietu administracyjnego wdrażania programu System Center Operations Manager. Jeżeli agenci są połączeni bezpośrednio, usługa Log Analytics zapewni pakiet administracyjny.

Pakiet administracyjny nosi nazwę Microsoft.IntelligencePacks.ApplicationDependencyMonitor. Jest ona zapisywana w %Programfiles%\Microsoft Packs\ State\Management usługi Agent\Agent\Health monitorowanie. Źródło danych, które korzysta z pakietu administracyjnego jest % Program files%\Microsoft monitorowanie Agent\Agent\Health usługi State\Resources\<AutoGeneratedID > \ Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll.

## <a name="installation"></a>Instalacja
### <a name="install-the-dependency-agent-on-microsoft-windows"></a>Zainstaluj agenta zależności w systemie Microsoft Windows
Aby zainstalować lub odinstalować agenta, wymagane są uprawnienia administratora.

Dependency Agent jest zainstalowany na komputerach z systemem Windows za pośrednictwem InstallDependencyAgent Windows.exe. Uruchomienie tego pliku wykonywalnego bez żadnych opcji powoduje uruchomienie kreatora, którego polecenia należy wykonywać w celu przeprowadzenia interaktywnej instalacji.  

Aby zainstalować agenta zależności na każdym komputerze z systemem Windows, wykonaj następujące kroki:

1.  Zainstaluj agenta pakietu OMS po jednej z metod opisanych w [zbierania danych z komputerów w środowisku z analizy dzienników](../log-analytics/log-analytics-concept-hybrid.md).
2.  Pobierz agenta systemu Windows i uruchom go za pomocą następującego polecenia: <br>`InstallDependencyAgent-Windows.exe`
3.  Użyj kreatora, aby zainstalować agenta.
4.  Jeśli agent Dependency Agent się nie uruchomi, sprawdź dzienniki, aby uzyskać szczegółowe informacje o błędzie. Agentów systemu Windows katalog dziennika jest %Programfiles%\Microsoft Agent\logs zależności. 

#### <a name="windows-command-line"></a>Wiersz polecenia systemu Windows
Użyj opcji z poniższej tabeli, aby przeprowadzić instalację z poziomu wiersza polecenia. Aby wyświetlić listę flag instalacji, uruchom instalatora, używając flagi /? w następujący sposób.

    InstallDependencyAgent-Windows.exe /?

| Flaga | Opis |
|:--|:--|
| /? | Pobierz listę opcji wiersza polecenia. |
| /S | Przeprowadź instalację cichą bez monitowania użytkownika. |

Pliki programu Windows Dependency Agent są domyślnie umieszczane w folderze C:\Program Files\Microsoft Dependency Agent.

### <a name="install-the-dependency-agent-on-linux"></a>Instalowanie agenta Dependency Agent w systemie Linux
Aby zainstalować lub skonfigurować agenta, wymagany jest dostęp na poziomie administratora.

Agent Dependency Agent jest instalowany na komputerach z systemem Linux za pomocą pliku InstallDependencyAgent-Linux64.bin, czyli skryptu powłoki z samowyodrębniającym plikiem binarnym. Możesz uruchomić plik przy użyciu sh lub Dodaj uprawnienia w samym pliku do wykonywania.
 
Aby zainstalować agenta Dependency Agent na każdym komputerze z systemem Linux, wykonaj następujące kroki:

1.  Zainstaluj agenta pakietu OMS po jednej z metod opisanych w [zbierania danych z komputerów w środowisku z analizy dzienników](../log-analytics/log-analytics-concept-hybrid.md).
2.  Zainstaluj agenta systemu Linux zależności jako główny przy użyciu następującego polecenia:<br>`sh InstallDependencyAgent-Linux64.bin`
3.  Jeśli agent Dependency Agent się nie uruchomi, sprawdź dzienniki, aby uzyskać szczegółowe informacje o błędzie. W agencie Linux katalog dziennika jest /var/opt/microsoft/dependency-agent/log.

Aby wyświetlić listę flagi instalacji, uruchom instalację programu pomocy flagi w następujący sposób.

    InstallDependencyAgent-Linux64.bin -help

| Flaga | Opis |
|:--|:--|
| -help | Pobierz listę opcji wiersza polecenia. |
| -s | Przeprowadź instalację cichą bez monitowania użytkownika. |
| — Sprawdź | Sprawdź uprawnienia i system operacyjny, ale nie instaluj agenta. |

Pliki agenta Dependency Agent są umieszczane w następujących katalogach:

| Pliki | Lokalizacja |
|:--|:--|
| Pliki jądra | /opt/microsoft/dependency-agent |
| Pliki dziennika | /var/opt/microsoft/dependency-agent/log |
| Plik konfiguracji | /etc/opt/microsoft/dependency-agent/config |
| Pliki wykonywalne usługi | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Pliki binarne magazynu | /var/opt/microsoft/dependency-agent/storage |

## <a name="installation-script-examples"></a>Przykłady skryptów instalacji
Aby łatwo wdrożyć agenta Dependency Agent na wielu serwerach naraz, można użyć skryptu. Poniższe przykładowe skrypty pozwalają pobrać i zainstalować agenta zależności w systemu Windows lub Linux.

### <a name="powershell-script-for-windows"></a>Skrypt programu PowerShell dla systemu Windows
```PowerShell
Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S
```

### <a name="shell-script-for-linux"></a>Skrypt powłoki dla systemu Linux
```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
sudo sh InstallDependencyAgent-Linux64.bin -s
```

## <a name="azure-vm-extension"></a>Rozszerzenia maszyny Wirtualnej platformy Azure
Możesz z łatwością wdrożyć agenta zależności przy użyciu maszyn wirtualnych platformy Azure [rozszerzenia maszyny Wirtualnej Azure](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-features).  Rozszerzenie maszyny Wirtualnej Azure można wdrożyć agenta zależności maszyn wirtualnych za pomocą skryptu programu PowerShell lub bezpośrednio w szablonie usługi Azure Resource Manager maszyny Wirtualnej.  Rozszerzenie jest dostępna dla systemów Windows (DependencyAgentWindows), jak i Linux (DependencyAgentLinux).  W przypadku wdrożenia za pośrednictwem rozszerzenia maszyny Wirtualnej Azure, agentów może automatycznie zaktualizowana do najnowszej wersji.

Aby wdrożyć rozszerzenie maszyny Wirtualnej Azure za pomocą programu PowerShell, można skorzystaj z następującego przykładu:

```PowerShell
#
# Deploy the Dependency Agent to every VM in a Resource Group
#

$version = "9.4"
$ExtPublisher = "Microsoft.Azure.Monitoring.DependencyAgent"
$OsExtensionMap = @{ "Windows" = "DependencyAgentWindows"; "Linux" = "DependencyAgentLinux" }
$rmgroup = "<Your Resource Group Here>"

Get-AzureRmVM -ResourceGroupName $rmgroup |
ForEach-Object {
    ""
    $name = $_.Name
    $os = $_.StorageProfile.OsDisk.OsType
    $location = $_.Location
    $vmRmGroup = $_.ResourceGroupName
    "${name}: ${os} (${location})"
    Date -Format o
    $ext = $OsExtensionMap.($os.ToString())
    $result = Set-AzureRmVMExtension -ResourceGroupName $vmRmGroup -VMName $name -Location $location `
    -Publisher $ExtPublisher -ExtensionType $ext -Name "DependencyAgent" -TypeHandlerVersion $version
    $result.IsSuccessStatusCode
}
```

Łatwiejsze sposobem upewnij się, że Agent zależności dla wszystkich maszyn wirtualnych jest Dołącz agenta do szablonu usługi Azure Resource Manager.  Należy pamiętać, że Dependency Agent nadal zależy od agenta pakietu OMS, więc [rozszerzenia maszyny Wirtualnej agenta pakietu OMS](../virtual-machines/extensions/oms-linux.md) należy najpierw wdrożyć.  Poniższy fragment JSON można dodać do *zasobów* części szablonu.

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


## <a name="desired-state-configuration"></a>Konfiguracja żądanego stanu
Aby wdrożyć agenta Dependency Agent za pośrednictwem usługi Desired State Configuration, możesz użyć modułu xPSDesiredStateConfiguration i fragmentu kodu podobnego do poniższego:

```
configuration ServiceMap {

Import-DscResource -ModuleName xPSDesiredStateConfiguration

$DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"

Node localhost
{ 
    # Download and install the Dependency Agent
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

## <a name="uninstallation"></a>Dezinstalacja
### <a name="uninstall-the-dependency-agent-on-windows"></a>Odinstalowywanie agenta Dependency Agent w systemie Windows
Administrator może odinstalować agenta Dependency Agent w systemie Windows za pomocą Panelu sterowania.

Administrator może również uruchomić plik %Programfiles%\Microsoft Dependency Agent\Uninstall.exe, aby odinstalować agenta Dependency Agent.

### <a name="uninstall-the-dependency-agent-on-linux"></a>Odinstalowywanie agenta Dependency Agent w systemie Linux
Zależności agenta można odinstalować z systemem Linux przy użyciu następującego polecenia.
<br>RHEL, CentOs lub Oracle:

```
sudo rpm -e dependency-agent
```

Ubuntu:

```
sudo apt -y purge dependency-agent
```
## <a name="troubleshooting"></a>Rozwiązywanie problemów
Jeśli masz problemy z Instalowanie i uruchamianie mapy usług w tej sekcji mogą pomóc. Jeśli nadal nie możesz rozwiązać problemu, skontaktuj się z Microsoft Support.

### <a name="dependency-agent-installation-problems"></a>Problemy z instalacją agenta zależności
#### <a name="installer-prompts-for-a-reboot"></a>Instalator wyświetli monit o ponowne uruchomienie komputera
Zależności agenta *zazwyczaj* nie wymaga ponownego uruchomienia komputera po instalacji i dezinstalacji. Jednak w niektórych rzadkich przypadkach, Windows Server wymaga ponownego uruchomienia, aby kontynuować instalację. Dzieje się tak, gdy zależności, zwykle Microsoft Visual C++ Redistributable, wymaga ponownego uruchomienia ze względu na zablokowany plik.

#### <a name="message-unable-to-install-dependency-agent-visual-studio-runtime-libraries-failed-to-install-code--codenumber-appears"></a>Komunikat "nie można zainstalować agenta zależności: bibliotek środowiska uruchomieniowego Visual Studio nie może zainstalować (kod = [Numer_kodu])" pojawia się

Program Microsoft Dependency Agent jest oparty na bibliotek środowiska uruchomieniowego programu Microsoft Visual Studio. Zostanie wyświetlony komunikat, jeśli występuje problem podczas instalacji bibliotek. 

Instalatorzy biblioteki środowiska uruchomieniowego Utwórz dzienniki w folderze %LOCALAPPDATA%\temp. Plik jest dd_vcredist_arch_yyyymmddhhmmss.log, gdzie *arch* "x86" lub "amd64" i *rrrrmmddggmmss* jest data i godzina (24-godzinnym) podczas tworzenia dziennika. Dziennik zawiera szczegółowe informacje o problemie, który blokuje instalację.

Może być przydatne do zainstalowania [najnowsze bibliotek środowiska uruchomieniowego](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) samodzielnie pierwszy.

W poniższej tabeli wymieniono kody i sugerowanymi metodami rozwiązania.

| Kod | Opis | Rozwiązanie |
|:--|:--|:--|
| 0x17 | Biblioteka Instalator wymaga aktualizacji systemu Windows, który nie został zainstalowany. | Poszukaj w dzienniku ostatniego Instalator biblioteki.<br><br>Jeśli występuje odwołanie do "Windows8.1-KB2999226-x64.msu" wiersza "Błąd 0x80240017: nie można wykonać pakietu MSU" nie ma wymagań wstępnych instalacji KB2999226. Postępuj zgodnie z instrukcjami w sekcji wymagania wstępne [uniwersalnego C środowiska uruchomieniowego w systemie Windows](https://support.microsoft.com/kb/2999226). Może być konieczne, uruchom usługę Windows Update, a następnie ponownie uruchom wiele razy, aby można było zainstalować wymagania wstępne.<br><br>Ponownie uruchom Instalatora programu Microsoft Dependency Agent. |

### <a name="post-installation-issues"></a>Problemy po instalacji
#### <a name="server-doesnt-appear-in-service-map"></a>Serwer nie ma mapy usług
Jeśli instalacji agenta zależności zakończyło się pomyślnie, ale nie widzisz serwera w rozwiązaniu mapy usługi:
* Dependency Agent jest zainstalowany pomyślnie? Możesz to sprawdzić przez sprawdzanie, czy usługa jest zainstalowana i uruchomiona.<br><br>
**Windows**: Wyszukaj usługi o nazwie "Microsoft Dependency Agent."<br>
**Linux**: Wyszukaj uruchomione przetworzyć "microsoft zależności agenta."

* Czy na [wolnego cenowym Operations Management Suite/Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions#offers-and-pricing-tiers)? Umożliwia planu Free do pięciu unikatowych serwerów mapy usługi. Kolejne serwery będą serwerami nie będą wyświetlane w mapy usług, nawet jeśli poprzednie pięć już wysyłania danych.

* Analiza dzienników jest danych wysyłania dziennika serwera i wydajności? Przejdź do wyszukiwania dziennika i uruchom następujące zapytanie dla danego komputera: 

        * Computer="<your computer name here>" | measure count() by Type
        
  Czy został wyświetlony w wynikach różnych zdarzeń? To jest ostatnie dane? Jeśli tak, agenta pakietu OMS jest prawidłowego działania i komunikacji z analizy dzienników. Jeśli nie, sprawdź, czy Agent pakietu OMS na serwerze: [Rozwiązywanie problemów z usługą OMS agenta dla systemu Windows](https://support.microsoft.com/help/3126513/how-to-troubleshoot-monitoring-onboarding-issues) lub [Agent pakietu OMS dla systemu Linux Rozwiązywanie problemów z](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md).

#### <a name="server-appears-in-service-map-but-has-no-processes"></a>Serwer jest wyświetlany w mapy usług, ale ma żadne procesy
Jeśli zostanie wyświetlony w mapie usługi serwera, ale go nie ma procesu lub połączenia danych, który wskazuje Dependency Agent jest zainstalowany i działa, że sterownik jądra nie został załadowany. 

Sprawdź plik C:\Program Files\Microsoft zależności Agent\logs\wrapper.log (system Windows) lub plik /var/opt/microsoft/dependency-agent/log/service.log (Linux). Ostatnich wierszy w pliku powinny wskazywać, dlaczego jądra nie został załadowany. Na przykład jądra może nie być obsługiwany w systemie Linux, jeśli zaktualizowane z jądra.

## <a name="data-collection"></a>Zbieranie danych
Można oczekiwać, że każdy agent ma zostać przesłany około 25 MB na dzień, w zależności od sposobu złożonych zależności systemu. Każdy agent wysyła dane zależności mapy usług co 15 s.  

Zależności agenta zwykle zużywa 0,1 procent pamięci systemowej i procent 0,1 systemu Procesora.

## <a name="supported-azure-regions"></a>Obsługiwane regiony platformy Azure
Mapa usługi jest obecnie dostępna w następujących regionach platformy Azure:
- Wschodnie stany USA
- Europa Zachodnia
- Środkowo-zachodnie stany USA
- Azja Południowo-Wschodnia


## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne
Poniższe sekcje zawierają listę obsługiwanych systemów operacyjnych dla agenta Dependency Agent. Mapy usług nie obsługuje architektury 32-bitowego dla dowolnego systemu operacyjnego.

### <a name="windows-server"></a>Windows Server
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

### <a name="windows-desktop"></a>Pulpit systemu Windows
- Windows 10
- Windows 8.1
- Windows 8
- Windows 7

### <a name="red-hat-enterprise-linux-centos-linux-and-oracle-linux-with-rhel-kernel"></a>Red Hat Enterprise Linux, CentOS Linux i Oracle Linux (z jądrem RHEL)
- Obsługiwane są tylko wersje domyślne i wersje SMP jądra systemu Linux.
- Niestandardowe wydania jądra, takie jak PAE i Xen, nie są obsługiwane dla żadnej dystrybucji systemu Linux. Na przykład system z wersji ciąg "2.6.16.21-0.8-xen" nie jest obsługiwane.
- Niestandardowe jądra, łącznie z ponownymi kompilacjami standardowych jąder, nie są obsługiwane.
- Jądro CentOSPlus nie jest obsługiwane.
- Jądro Oracle Unbreakable Enterprise Kernel (UEK) zostało opisane w dalszej części tego artykułu.


#### <a name="red-hat-linux-7"></a>Red Hat Linux 7
| Wersja systemu operacyjnego | Wersja jądra |
|:--|:--|
| 7.0 | 3.10.0-123 |
| 7.1 | 3.10.0-229 |
| 7.2 | 3.10.0-327 |
| 7.3 | 3.10.0-514 |
| 7.4 | 3.10.0-693 |
| 7.5 | 3.10.0-862 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6
| Wersja systemu operacyjnego | Wersja jądra |
|:--|:--|
| 6.0 | 2.6.32-71 |
| 6.1 | 2.6.32-131 |
| 6.2 | 2.6.32-220 |
| 6.3 | 2.6.32-279 |
| 6.4 | 2.6.32-358 |
| 6.5 | 2.6.32-431 |
| 6.6 | 2.6.32-504 |
| 6.7 | 2.6.32-573 |
| 6.8 | 2.6.32-642 |
| 6.9 | 2.6.32-696 |

#### <a name="red-hat-linux-5"></a>Red Hat Linux 5
| Wersja systemu operacyjnego | Wersja jądra |
|:--|:--|
| 5.8 | 2.6.18-308 |
| 5.9 | 2.6.18-348 |
| 5.10 | 2.6.18-371 |
| 5.11 | 2.6.18-398<br>2.6.18-400<br>2.6.18-402<br>2.6.18-404<br>2.6.18-406<br>2.6.18-407<br>2.6.18-408<br>2.6.18-409<br>2.6.18-410<br>2.6.18-411<br>2.6.18-412<br>2.6.18-416<br>2.6.18-417<br>2.6.18-419<br>2.6.18-420 |

### <a name="ubuntu-server"></a>Ubuntu Server
- Niestandardowe jądra, łącznie z ponownymi kompilacjami standardowych jąder, nie są obsługiwane.

| Wersja systemu operacyjnego | Wersja jądra |
|:--|:--|
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

### <a name="oracle-enterprise-linux-with-unbreakable-enterprise-kernel"></a>Oracle Enterprise Linux z jądrem Unbreakable Enterprise Kernel
#### <a name="oracle-linux-6"></a>Oracle Linux 6
| Wersja systemu operacyjnego | Wersja jądra
|:--|:--|
| 6.2 | Oracle 2.6.32-300 (UEK R1) |
| 6.3 | Oracle 2.6.39-200 (UEK R2) |
| 6.4 | Oracle 2.6.39-400 (UEK R2) |
| 6.5 | Oracle 2.6.39-400 (UEK R2 i386) |
| 6.6 | Oracle 2.6.39-400 (UEK R2 i386) |

#### <a name="oracle-linux-5"></a>Oracle Linux 5

| Wersja systemu operacyjnego | Wersja jądra
|:--|:--|
| 5.10 | Oracle 2.6.39-400 (UEK R2) |
| 5.11 | Oracle 2.6.39-400 (UEK R2) |

#### <a name="suse-linux-enterprise-server"></a>SUSE Linux Enterprise Server

#### <a name="suse-linux-11"></a>SUSE Linux 11
| Wersja systemu operacyjnego | Wersja jądra
|:--|:--|
| 11 SP2 | 3.0.101-0.7 |
| 11 SP3 | 3.0.101-0.47 |
| 11 SP4 | 3.0.101-65 |


## <a name="diagnostic-and-usage-data"></a>Dane diagnostyczne i dane dotyczące użycia
Firma Microsoft automatycznie zbiera dane użycia i wydajności przez korzystanie z usługi mapy usługi. Firma Microsoft używa tych danych do udostępniania i ulepszania jakości, bezpieczeństwa i integralności usługi mapy usługi. Dane obejmują informacje o konfiguracji oprogramowania, takie jak wersja systemu operacyjnego i. Zawiera także adres IP, nazwę DNS i nazwę stacji roboczej zapewnić dokładne i skuteczne funkcje do rozwiązywania problemów. Nie gromadzimy nazwisk, adresów ani innych informacji kontaktowych.

Aby uzyskać więcej informacji dotyczących zbierania i użycia danych, zobacz [Microsoft Online Services Privacy Statement](https://go.microsoft.com/fwlink/?LinkId=512132).



## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się, jak [użyć mapy usługi]( monitoring-service-map.md) po zostały wdrożone i skonfigurowane.
