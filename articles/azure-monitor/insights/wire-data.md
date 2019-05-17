---
title: Połączenie rozwiązań danych w usłudze Azure Monitor | Dokumentacja firmy Microsoft
description: Dane o komunikacji sieciowej jest skonsolidowane dane sieci i wydajności z komputerów przy użyciu agentów usługi Log Analytics. Dane sieciowe są połączone z danymi Twojego dziennika, aby ułatwić korelowanie danych.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: fc3d7127-0baa-4772-858a-5ba995d1519b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/03/2018
ms.author: magoedte
ms.openlocfilehash: b7968de8ad4357241eb8d86d51e991f4bbab959d
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2019
ms.locfileid: "65601570"
---
# <a name="wire-data-20-preview-solution-in-azure-monitor"></a>Połączenie rozwiązań danych w wersji 2.0 (wersja zapoznawcza) w usłudze Azure Monitor

![Symbol rozwiązania Dane o komunikacji sieciowej](media/wire-data/wire-data2-symbol.png)

Dane o komunikacji sieciowej jest skonsolidowane dane sieci i wydajności zbierane z komputerów połączonych Windows i połączone z systemem Linux za pomocą agenta usługi Log Analytics, łącznie z tymi monitorowane przez program Operations Manager w środowisku. Dane sieciowe są łączone z innymi danymi Twojego dziennika, aby ułatwić korelowanie danych.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Oprócz agenta usługi Log Analytics rozwiązanie dane o komunikacji sieciowej używa agenci zależności firmy Microsoft, które zainstalujesz na komputerach w infrastrukturze IT. Agenci Dependency Agent monitorują dane sieciowe wysyłane do i z Twoich komputerów dla poziomów sieci 2 i 3 w [modelu OSI](https://en.wikipedia.org/wiki/OSI_model), włącznie z różnymi używanymi protokołami i portami. Dane są następnie wysyłane do usługi Azure Monitor przy użyciu agentów.  

>[!NOTE]
>Jeśli masz już wdrożone rozwiązania Service Map lub rozważa mapy usługi lub [usługi Azure Monitor dla maszyn wirtualnych](../../azure-monitor/insights/vminsights-overview.md), ma nowego połączenia metryki zestawu danych mogą zbierać i przechowywać w usłudze Azure Monitor, który zawiera informacje porównywalne z danymi o komunikacji sieciowej.

Domyślnie usługa Azure Monitor rejestruje dane dla procesora CPU, pamięci, dysku i sieci danych dotyczących wydajności z liczników wbudowane w Windows i Linux, a także inne liczniki wydajności, które można określić. Gromadzenie danych sieciowych i innych odbywa się w czasie rzeczywistym dla każdego agenta, łącznie z podsieciami i protokołami poziomu aplikacji używanymi przez komputer.  Rozwiązanie Dane o komunikacji sieciowej bierze pod uwagę dane sieciowe na poziomie aplikacji, a nie niżej, w warstwie transportu TCP.  Rozwiązanie nie bierze pod uwagę pojedynczych komunikatów potwierdzeń i synchronizacji.  Po zakończeniu uzgadniania połączenie jest uznawane za aktywne i oznaczane jako Połączono. To połączenie pozostaje aktywne, dopóki obie strony zgadzają się, że gniazdo jest otwarte i dane można przekazywać tam i z powrotem.  Po obu stronach zamyka połączenie, jest oznaczana Rozłączono.  W związku z tym zliczana jest tylko przepustowość pomyślnie zakończonych pakietów. Pakiety wysłane ponownie lub zakończone niepowodzeniem nie są raportowane.

Jeśli zdarzało Ci się korzystać z programu [sFlow](http://www.sflow.org/) albo innego oprogramowania z [protokołem NetFlow firmy Cisco](https://www.cisco.com/c/en/us/products/collateral/ios-nx-os-software/ios-netflow/prod_white_paper0900aecd80406232.html), statystyki i dane wyświetlane w danych o komunikacji sieciowej będą wyglądać znajomo.

Do niektórych typów wbudowanych zapytań przeszukiwania dzienników należą:

- Agenci udostępniający dane o komunikacji sieciowej
- Adres IP agentów udostępniających dane o komunikacji sieciowej
- Komunikacja wychodząca według adresów IP
- Liczba wysłanych bajtów według protokołów aplikacji
- Liczba bajtów wysłanych przez usługę aplikacji
- Bajty odebrane przez różne protokoły
- Całkowita liczba bajtów wysłanych i odebranych według wersji protokołu IP
- Średnie opóźnienie dla połączeń, które zostały wiarygodnie zmierzone
- Procesy komputera, które zainicjowały lub odebrały ruch sieciowy
- Ilość ruchu sieciowego dla procesu

Podczas wyszukiwania przy użyciu danych o komunikacji sieciowej możesz filtrować i grupować dane, aby wyświetlić informacje o najczęściej używanych agentach i protokołach. Możesz też sprawdzić, kiedy niektóre komputery (adresy IP/adresy MAC) komunikowały się ze sobą, ile to trwało i ile danych wysłano. Zasadniczo możesz przeglądać metadane dotyczące ruchu sieciowego, przeszukując je.

Warto jednak podkreślić, że prezentowane są metadane, a więc niekoniecznie będzie to przydatne przy rozwiązywaniu bardziej złożonych problemów. Dane o komunikacji sieciowej w usłudze Azure Monitor nie jest pełną przechwytywania danych sieciowych.  Nie są one przeznaczone do rozwiązywania problemów na poziomie pakietów. Zaletą używania agenta, w porównaniu do innych metod zbierania jest, że nie trzeba zainstalować urządzeń, ponownie skonfiguruj przełączników sieciowych lub wykonywania skomplikowanych konfiguracji. Dane o komunikacji sieciowej są po prostu oparte na agencie — instalujesz agenta na komputerze i będzie on monitorować własny ruch sieciowy. Inną zaletą jest możliwość monitorowania obciążeń działających u dostawców chmury lub dostawców usług hostingowych albo na platformie Microsoft Azure, gdy użytkownik nie jest właścicielem warstwy sieci szkieletowej.

## <a name="connected-sources"></a>Połączone źródła

Rozwiązanie Dane o komunikacji sieciowej pobiera swoje dane z agenta Microsoft Dependency Agent. Agent zależności zależy od agenta usługi Log Analytics dla jego połączenia z usługi Azure Monitor. Oznacza to, że serwer musi mieć agenta usługi Log Analytics, zainstalować i skonfigurować za pomocą agenta zależności. W poniższej tabeli opisano połączone źródła obsługiwane przez rozwiązanie Dane o komunikacji sieciowej.

| **Połączone źródło** | **Obsługiwane** | **Opis** |
| --- | --- | --- |
| Agenci dla systemu Windows | Yes | Rozwiązanie Dane o komunikacji sieciowej analizuje i gromadzi dane z komputerów z agentami systemu Windows. <br><br> Oprócz [agenta usługi Log Analytics dla Windows](../../azure-monitor/platform/agent-windows.md), agenci Windows wymagają Microsoft Dependency Agent. Zobacz [obsługiwane systemy operacyjne](../../azure-monitor/insights/service-map-configure.md#supported-windows-operating-systems), gdzie znajdziesz pełną listę wersji systemu operacyjnego. |
| Agenci dla systemu Linux | Yes | Rozwiązanie Dane o komunikacji sieciowej analizuje i gromadzi dane z komputerów z agentami systemu Linux.<br><br> Oprócz [agenta usługi Log Analytics dla systemu Linux](../../azure-monitor/learn/quick-collect-linux-computer.md), agenci dla systemu Linux wymaga Microsoft Dependency Agent. Zobacz [obsługiwane systemy operacyjne](../../azure-monitor/insights/service-map-configure.md#supported-linux-operating-systems), gdzie znajdziesz pełną listę wersji systemu operacyjnego. |
| Grupa zarządzania programu System Center Operations Manager | Yes | Rozwiązanie Dane o komunikacji sieciowej analizuje i gromadzi dane z agentów systemu Windows i Linux w połączonej [grupie zarządzania programu System Center Operations Manager](../../azure-monitor/platform/om-agents.md). <br><br> Bezpośrednie połączenie z komputera z programem System Center Operations Manager agent usługi Azure Monitor jest wymagana. |
| Konto magazynu Azure | Nie | Rozwiązanie Dane o komunikacji sieciowej gromadzi dane z komputerów agenta, więc nie ma od niego żadnych danych do gromadzenia z usługi Azure Storage. |

W Windows Microsoft Monitoring Agent (MMA) jest używany zarówno przez System Center Operations Manager i usługi Azure Monitor do zbierania i wysyłania danych. W zależności od kontekstu agent nosi nazwę agenta programu System Center Operations Manager, agenta usługi Log Analytics, Agent MMA lub Agent bezpośredni. System Center Operations Manager i usługi Azure Monitor zapewnia nieco inne wersje programu MMA. Te wersje strony każdy raport do programu System Center Operations Manager, usługi Azure Monitor lub oba.

W systemie Linux agent usługi Log Analytics dla systemu Linux zbiera i przesyła dane do usługi Azure Monitor. Na serwerach z agentami podłączone bezpośrednio do usługi Azure Monitor lub na serwerach, łączących się z monitora platformy Azure za pośrednictwem grupy zarządzania programu System Center Operations Manager, można użyć dane o komunikacji sieciowej.

Agent Dependency Agent nie przesyła żadnych danych samodzielnie i nie wymaga żadnych zmian zapory lub portów. Dane w dane o komunikacji sieciowej zawsze są przesyłane przez agenta usługi Log Analytics do usługi Azure Monitor, bezpośrednio lub za pośrednictwem bramy usługi Log Analytics.

![diagram agenta](./media/wire-data/agents.png)

Jeśli jesteś użytkownikiem programu System Center Operations Manager z grupy zarządzania podłączone do usługi Azure Monitor:

- Dodatkowa konfiguracja nie jest wymagana, gdy agentów programu System Center Operations Manager ma dostęp do Internetu, aby nawiązać połączenie z usługi Azure Monitor.
- Należy skonfigurować bramę usługi Log Analytics do pracy z programu System Center Operations Manager, gdy Twoi agenci programu System Center Operations Manager nie może uzyskać dostępu usługi Azure Monitor za pośrednictwem Internetu.

Jeśli komputery Windows lub Linux bezpośrednio nie może połączyć się z usługą, należy skonfigurować agenta usługi Log Analytics, aby nawiązać połączenie przy użyciu bramy usługi Log Analytics w usłudze Azure Monitor. Można pobrać bramę usługi Log Analytics z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=52666).

## <a name="prerequisites"></a>Wymagania wstępne

- Wymaga oferty rozwiązania [Insight and Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite-pricing).
- Jeśli używasz poprzedniej wersji rozwiązania danych o komunikacji sieciowej, musisz ją najpierw usunąć. Jednak wszystkie dane przechwycone poprzez oryginalne rozwiązanie Dane o komunikacji sieciowej są nadal dostępne za pośrednictwem rozwiązania Dane o komunikacji sieciowej 2.0 i przeszukiwania dzienników.
- Aby zainstalować lub odinstalować agenta Dependency Agent, są wymagane uprawnienia administratora.
- Agent Dependency Agent musi być zainstalowany na komputerze z 64-bitowym systemem operacyjnym.

### <a name="operating-systems"></a>Systemy operacyjne

Poniższe sekcje zawierają listę obsługiwanych systemów operacyjnych dla agenta Dependency Agent. Rozwiązanie Dane o komunikacji sieciowej nie obsługuje 32-bitowych architektur dla żadnego systemu operacyjnego.

#### <a name="windows-server"></a>Windows Server

- Windows Server 2019
- System Windows Server 2016 1803
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

#### <a name="windows-desktop"></a>Pulpit systemu Windows

- Windows 10 w wersji 1803
- Windows 10
- Windows 8.1
- Windows 8
- Windows 7

#### <a name="supported-linux-operating-systems"></a>Obsługiwane systemy operacyjne Linux
W poniższych sekcjach wymieniono obsługiwane systemy operacyjne dla agenta zależności w systemie Linux.  

- Obsługiwane są tylko wersje domyślne i wersje SMP jądra systemu Linux.
- Niestandardowe wydania jądra, takie jak PAE i Xen, nie są obsługiwane dla żadnej dystrybucji systemu Linux. Na przykład systemu z wersji ciąg "2.6.16.21-0.8-xen" nie jest obsługiwane.
- Niestandardowe jądra, łącznie z ponownymi kompilacjami standardowych jąder, nie są obsługiwane.

##### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Wersja systemu operacyjnego | Wersja jądra |
|:--|:--|
| 7.4 | 3.10.0-693 |
| 7.5 | 3.10.0-862 |
| 7.6 | 3.10.0-957 |

##### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Wersja systemu operacyjnego | Wersja jądra |
|:--|:--|
| 6.9 | 2.6.32-696 |
| 6.10 | 2.6.32-754 |

##### <a name="centosplus"></a>CentOSPlus
| Wersja systemu operacyjnego | Wersja jądra |
|:--|:--|
| 6.9 | 2.6.32-696.18.7<br>2.6.32-696.30.1 |
| 6.10 | 2.6.32-696.30.1<br>2.6.32-754.3.5 |

##### <a name="ubuntu-server"></a>Ubuntu Server

| Wersja systemu operacyjnego | Wersja jądra |
|:--|:--|
| Ubuntu 18.04 | kernel 4.15.\*<br>4.18* |
| Systemu Ubuntu 16.04.3 | jądra 4.15. * |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

##### <a name="suse-linux-11-enterprise-server"></a>SUSE Linux 11 Enterprise Server

| Wersja systemu operacyjnego | Wersja jądra
|:--|:--|
| 11 SP4 | 3.0.* |

##### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| Wersja systemu operacyjnego | Wersja jądra
|:--|:--|
| 12 Z DODATKIEM SP2 | 4.4. * |
| 12 Z DODATKIEM SP3 | 4.4. * |

### <a name="dependency-agent-downloads"></a>Pobieranie agenta zależności

| Plik | System operacyjny | Wersja | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.7.4 | A111B92AB6CF28EB68B696C60FE51F980BFDFF78C36A900575E17083972989E0 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.7.4 | AB58F3DB8B1C3DEE7512690E5A65F1DFC41B43831543B5C040FCCE8390F2282C |



## <a name="configuration"></a>Konfigurowanie

Wykonaj poniższe kroki, aby skonfigurować rozwiązanie Dane o komunikacji sieciowej dla Twoich obszarów roboczych.

1. Włącz rozwiązanie Activity Log Analytics z [portalu Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WireData2OMS?tab=Overview) lub przy użyciu procesu opisanego w [Dodaj rozwiązania z galerii rozwiązań do monitorowania](../../azure-monitor/insights/solutions.md).
2. Na każdym komputerze, z którego chcesz uzyskiwać dane, zainstaluj agenta Dependency Agent. Agent Dependency Agent może monitorować połączenia do najbliższych sąsiadów, więc nie jest konieczny na każdym komputerze.

> [!NOTE]
> Do nowych obszarów roboczych nie można dodać poprzedniej wersji rozwiązania Dane o komunikacji sieciowej. Jeśli masz włączone oryginalne rozwiązanie Dane o komunikacji sieciowej, możesz nadal z niego korzystać. Jednak aby używać rozwiązania Dane o komunikacji sieciowej 2.0, należy najpierw usunąć oryginalną wersję.
> 
 
### <a name="install-the-dependency-agent-on-windows"></a>Instalowanie agenta Dependency Agent w systemie Windows

Aby zainstalować lub odinstalować agenta, wymagane są uprawnienia administratora.

Agent Dependency Agent jest instalowany na komputerach z systemem Windows za pomocą pliku InstallDependencyAgent-Windows.exe. Uruchomienie tego pliku wykonywalnego bez żadnych opcji powoduje uruchomienie kreatora, którego polecenia należy wykonywać w celu przeprowadzenia interaktywnej instalacji.

Aby zainstalować agenta Dependency Agent na każdym komputerze z systemem Windows, wykonaj następujące kroki:

1. Zainstaluj agenta usługi Log Analytics, wykonaj czynności w [zbieranie danych z komputerów Windows hostowanych w danym środowisku](../../azure-monitor/platform/agent-windows.md).
2. Pobierz program Windows Dependency Agent przy użyciu linku w poprzedniej sekcji, a następnie uruchom go za pomocą następującego polecenia: `InstallDependencyAgent-Windows.exe`
3. Użyj kreatora, aby zainstalować agenta.
4. Jeśli agent Dependency Agent się nie uruchomi, sprawdź dzienniki, aby uzyskać szczegółowe informacje o błędzie. W przypadku agentów systemu Windows katalog dziennika to %Programfiles%\Microsoft Dependency Agent\logs.

#### <a name="windows-command-line"></a>Wiersz polecenia systemu Windows

Użyj opcji z poniższej tabeli, aby przeprowadzić instalację z poziomu wiersza polecenia. Aby wyświetlić listę flag instalacji, uruchom instalatora, używając flagi /? w następujący sposób.

InstallDependencyAgent-Windows.exe /?

| **Flaga** | **Opis** |
| --- | --- |
| <code>/?</code> | Pobierz listę opcji wiersza polecenia. |
| <code>/S</code> | Przeprowadź instalację cichą bez monitowania użytkownika. |

Pliki programu Windows Dependency Agent są domyślnie umieszczane w folderze C:\Program Files\Microsoft Dependency Agent.

### <a name="install-the-dependency-agent-on-linux"></a>Instalowanie agenta Dependency Agent w systemie Linux

Aby zainstalować lub skonfigurować agenta, wymagany jest dostęp na poziomie administratora.

Agent Dependency Agent jest instalowany na komputerach z systemem Linux za pomocą pliku InstallDependencyAgent-Linux64.bin, czyli skryptu powłoki z samowyodrębniającym plikiem binarnym. Plik ten można uruchomić przy użyciu polecenia _sh_ lub dodać uprawnienia do wykonywania do samego pliku.

Aby zainstalować agenta Dependency Agent na każdym komputerze z systemem Linux, wykonaj następujące kroki:

1. Zainstaluj agenta usługi Log Analytics, wykonaj czynności w [zbieranie danych z komputerów z systemem Linux hostowanych w danym środowisku](../../azure-monitor/learn/quick-collect-linux-computer.md#obtain-workspace-id-and-key).
2. Pobierz program Linux Dependency Agent przy użyciu linku w poprzedniej sekcji, a następnie zainstaluj go jako użytkownik root za pomocą następującego polecenia: sh InstallDependencyAgent-Linux64.bin
3. Jeśli agent Dependency Agent się nie uruchomi, sprawdź dzienniki, aby uzyskać szczegółowe informacje o błędzie. Dla agentów systemu Linux katalogiem dziennika jest: /var/opt/microsoft/dependency-agent/log.

Aby wyświetlić listę flag instalacji, uruchom program instalacyjny z flagą `-help` w następujący sposób.

```
InstallDependencyAgent-Linux64.bin -help
```

| **Flaga** | **Opis** |
| --- | --- |
| <code>-help</code> | Pobierz listę opcji wiersza polecenia. |
| <code>-s</code> | Przeprowadź instalację cichą bez monitowania użytkownika. |
| <code>--check</code> | Sprawdź uprawnienia i system operacyjny, ale nie instaluj agenta. |

Pliki agenta Dependency Agent są umieszczane w następujących katalogach:

| **Pliki** | **Lokalizacja** |
| --- | --- |
| Pliki jądra | /opt/microsoft/dependency-agent |
| Pliki dziennika | /var/opt/microsoft/dependency-agent/log |
| Plik konfiguracji | /etc/opt/microsoft/dependency-agent/config |
| Pliki wykonywalne usługi | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br><br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Pliki binarne magazynu | /var/opt/microsoft/dependency-agent/storage |

### <a name="installation-script-examples"></a>Przykłady skryptów instalacji

Aby łatwo wdrożyć agenta Dependency Agent na wielu serwerach naraz, można użyć skryptu. Poniższe przykładowe skrypty pozwalają pobrać i zainstalować agenta zależności w systemu Windows lub Linux.

#### <a name="powershell-script-for-windows"></a>Skrypt programu PowerShell dla systemu Windows

```powershell

Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S

```

#### <a name="shell-script-for-linux"></a>Skrypt powłoki dla systemu Linux

```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
```

```
sh InstallDependencyAgent-Linux64.bin -s
```

### <a name="desired-state-configuration"></a>Konfiguracja żądanego stanu

Aby wdrożyć agenta Dependency Agent za pośrednictwem usługi Desired State Configuration, możesz użyć modułu xPSDesiredStateConfiguration i fragmentu kodu podobnego do poniższego:

```powershell
Import-DscResource -ModuleName xPSDesiredStateConfiguration

$DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"



Node $NodeName

{

    # Download and install the Dependency Agent

    xRemoteFile DAPackage

    {

        Uri = "https://aka.ms/dependencyagentwindows"

        DestinationPath = $DAPackageLocalPath

        DependsOn = "[Package]OI"

    }

    xPackage DA

    {

        Ensure = "Present"

        Name = "Dependency Agent"

        Path = $DAPackageLocalPath

        Arguments = '/S'

        ProductId = ""

        InstalledCheckRegKey = "HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent"

        InstalledCheckRegValueName = "DisplayName"

        InstalledCheckRegValueData = "Dependency Agent"

    }

}

```

### <a name="uninstall-the-dependency-agent"></a>Odinstalowywanie agenta Dependency Agent

W poniższych sekcjach opisano, jak usunąć agenta Dependency Agent.

#### <a name="uninstall-the-dependency-agent-on-windows"></a>Odinstalowywanie agenta Dependency Agent w systemie Windows

Administrator może odinstalować agenta Dependency Agent w systemie Windows za pomocą Panelu sterowania.

Administrator może również uruchomić plik %Programfiles%\Microsoft Dependency Agent\Uninstall.exe, aby odinstalować agenta Dependency Agent.

#### <a name="uninstall-the-dependency-agent-on-linux"></a>Odinstalowywanie agenta Dependency Agent w systemie Linux

Aby całkowicie odinstalować agenta Dependency Agent z systemu Linux, musisz usunąć samego agenta i łącznik, który jest instalowany automatycznie wraz z agentem. Oba te elementy można usunąć przy użyciu następującego pojedynczego polecenia:

```
rpm -e dependency-agent dependency-agent-connector
```

## <a name="management-packs"></a>Pakiety administracyjne

Gdy rozwiązanie Dane o komunikacji sieciowej zostanie uaktywnione w obszarze roboczym usługi Log Analytics, pakiet administracyjny o wielkości 300 KB jest wysyłany do wszystkich serwerów z systemem Windows w tym obszarze roboczym. Jeśli używasz agentów programu System Center Operations Manager w [połączonej grupie zarządzania](../platform/om-agents.md), pakiet administracyjny monitora zależności zostanie wdrożony z programu System Center Operations Manager. Jeśli agenci są połączone bezpośrednio usługi Azure Monitor zapewnia pakietu administracyjnego.

Pakiet administracyjny nosi nazwę Microsoft.IntelligencePacks.ApplicationDependencyMonitor. Jest on zapisywany w folderze: %Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs. Źródło danych, którego używa pakiet administracyjny, to: % Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources&lt;AutoGeneratedID&gt;\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll.

## <a name="using-the-solution"></a>Użycie rozwiązania

Skorzystaj z poniższych informacji, aby zainstalować i skonfigurować rozwiązanie.

- Rozwiązanie Dane o komunikacji sieciowej pozyskuje dane z komputerów z systemem Windows Server 2012 R2, Windows 8.1 i z nowszymi systemami operacyjnymi.
- Na komputerach, z których chcesz pozyskiwać dane o komunikacji sieciowej, wymagana jest platforma Microsoft .NET Framework 4.0 lub nowsza.
- Dodaj rozwiązanie dane o komunikacji sieciowej do obszaru roboczego usługi Log Analytics przy użyciu procesu opisanego w [Dodaj rozwiązania z galerii rozwiązań do monitorowania](solutions.md). Nie są wymagane żadne dalsze czynności konfiguracyjne.
- Jeśli chcesz wyświetlić dane o komunikacji sieciowej dla konkretnego rozwiązania, to rozwiązanie musi być już dodane do Twojego obszaru roboczego.

Po zainstalowaniu agentów i rozwiązania w Twoim obszarze roboczym pojawi się kafelek Dane o komunikacji sieciowej 2.0.

![Kafelek Dane o komunikacji sieciowej](./media/wire-data/wire-data-tile.png)

## <a name="using-the-wire-data-20-solution"></a>Używanie rozwiązania Dane o komunikacji sieciowej 2.0

Na stronie **Omówienie** Twojego obszaru roboczego usługi Log Analytics w witrynie Azure Portal kliknij kafelek **Dane o komunikacji sieciowej 2.0**, aby otworzyć pulpit nawigacyjny rozwiązania Dane o komunikacji sieciowej. Na pulpicie nawigacyjnym znajdują się bloki wymienione w poniższej tabeli. Każdy blok zawiera do 10 elementów spełniających kryteria tego bloku dla określonego zakresu i czasu. Możesz uruchomić przeszukiwanie dzienników, które zwróci wszystkie rekordy. W tym celu kliknij przycisk **Zobacz wszystko** na dole bloku lub kliknij nagłówek bloku.

| **Blok** | **Opis** |
| --- | --- |
| Agenci przechwytujący ruch sieciowy | Pokazuje liczbę agentów, którzy przechwytują ruch sieciowy, i listę 10 najważniejszych komputerów przechwytujących ruch. Kliknij liczbę, aby uruchomić przeszukiwanie dzienników dla <code>WireData \| summarize sum(TotalBytes) by Computer \| take 500000</code>. Kliknij komputer na liście, aby uruchomić przeszukiwanie dzienników zwracające całkowitą liczbę przechwyconych bajtów. |
| Lokalne podsieci | Pokazuje liczbę lokalnych podsieci, które zostały odnalezione przez agentów.  Kliknij liczbę, aby uruchomić przeszukiwanie dzienników dla <code>WireData \| summarize sum(TotalBytes) by LocalSubnet</code>, które wyświetli listę wszystkich podsieci wraz z liczbą bajtów przesłanych w ramach każdej z nich. Kliknij podsieć na liście, aby uruchomić przeszukiwanie dzienników zwracające całkowitą liczbę bajtów przesłanych w tej podsieci. |
| Protokoły poziomu aplikacji | Pokazuje liczbę używanych protokołów poziomu aplikacji wykrytych przez agentów. Kliknij liczbę, aby uruchomić przeszukiwanie dzienników dla <code>WireData \| summarize sum(TotalBytes) by ApplicationProtocol</code>. Kliknij protokół, aby uruchomić przeszukiwanie dzienników zwracające całkowitą liczbę bajtów wysłanych przy użyciu tego protokołu. |

![Pulpit nawigacyjny rozwiązania Dane o komunikacji sieciowej](./media/wire-data/wire-data-dash.png)

Możesz użyć bloku **Agenci przechwytujący ruch sieciowy**, aby ustalić, jaka część przepustowości sieci jest zużywana przez komputery. Ten blok może pomóc łatwo odnaleźć _najbardziej rozgadany_ komputer w Twoim środowisku. Takie komputery mogą być przeciążone, działać inaczej niż zwykle lub zużywać więcej zasobów sieciowych niż zwykle.

![przykład przeszukiwania dzienników](./media/wire-data/log-search-example01.png)

Analogicznie możesz użyć bloku **Podsieci lokalne**, aby określić, ile ruchu sieciowego przechodzi przez Twoje podsieci. Użytkownicy często definiują podsieci wokół najważniejszych obszarów dla swoich aplikacji. Ten blok oferuje wgląd w te obszary.

![przykład przeszukiwania dzienników](./media/wire-data/log-search-example02.png)

Blok **Protokoły poziomu aplikacji** przydaje się, ponieważ pomaga dowiedzieć się, jakie protokoły są używane. Na przykład możesz spodziewać się, że protokół SSH nie będzie używany w Twoim środowisku sieciowym. Wyświetlanie informacji dostępnych w bloku może szybko potwierdzić lub obalić Twoje oczekiwania.

![przykład przeszukiwania dzienników](./media/wire-data/log-search-example03.png)

Warto również wiedzieć, czy ruch w ramach protokołu zwiększa się, czy też zmniejsza wraz z upływem czasu. Jeśli na przykład wzrasta ilość danych przesyłanych przez aplikację, być może warto zwrócić uwagę.

## <a name="input-data"></a>Dane wejściowe

Dane o komunikacji sieciowej gromadzą metadane dotyczące ruchu sieciowego przy użyciu agentów, którzy zostali włączeni. Każdy agent wysyła dane co około 15 s.

## <a name="output-data"></a>Dane wyjściowe

Rekord o typie _WireData_ jest tworzony dla każdego typu danych wejściowych. Rekordy rozwiązania Dane o komunikacji sieciowej mają właściwości podane w poniższej tabeli:

| Właściwość | Opis |
|---|---|
| Computer (Komputer) | Nazwa komputera, gdzie są gromadzone dane |
| TimeGenerated | Czas rekordu |
| LocalIP | Adres IP komputera lokalnego |
| SessionState | Połączone lub rozłączone |
| ReceivedBytes | Liczba odebranych bajtów |
| ProtocolName | Nazwa używanego protokołu sieciowego |
| IPVersion | Wersja protokołu IP |
| Direction | Ruch przychodzący lub wychodzący |
| MaliciousIP | Adres IP znanego złośliwego źródła |
| Severity | Ważność podejrzanego złośliwego oprogramowania |
| RemoteIPCountry | Kraj/region dla zdalnego adresu IP |
| ManagementGroupName | Nazwa grupy zarządzania programu Operations Manager |
| SourceSystem | Źródło, gdzie zostały zebrane dane |
| SessionStartTime | Godzina rozpoczęcia sesji |
| SessionEndTime | Godzina zakończenia sesji |
| LocalSubnet | Podsieć, gdzie zostały zebrane dane |
| LocalPortNumber | Numer portu lokalnego |
| RemoteIP | Zdalny adres IP używany przez komputer zdalny |
| RemotePortNumber | Numer portu używany przez zdalny adres IP |
| SessionID | Unikatowa wartość, która identyfikuje sesję komunikacji między dwoma adresami IP |
| SentBytes | Liczba wysłanych bajtów |
| TotalBytes | Całkowita liczba bajtów wysłanych podczas sesji |
| ApplicationProtocol | Typ użytego protokołu sieciowego   |
| ProcessId | Identyfikator procesu systemu Windows |
| ProcessName | Ścieżka i nazwa pliku procesu |
| RemoteIPLongitude | Wartość długości geograficznej IP |
| RemoteIPLatitude | Wartość szerokości geograficznej IP |


## <a name="next-steps"></a>Kolejne kroki

- [Wyszukaj dzienniki](../../azure-monitor/log-query/log-query-overview.md), aby wyświetlić szczegółowe rekordy wyszukiwania rozwiązania Dane o komunikacji sieciowej.