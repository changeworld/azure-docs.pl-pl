---
title: Połączenie danych rozwiązania analizy dzienników | Dokumentacja firmy Microsoft
description: Podczas transmisji danych jest skonsolidowanych danych sieci i wydajności z komputerów z agentami OMS, w tym programu Operations Manager oraz agenci połączone z systemem Windows. Dane sieciowe jest połączone z dane dziennika, aby ułatwić skorelować danych.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: fc3d7127-0baa-4772-858a-5ba995d1519b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: magoedte
ms.openlocfilehash: d824272f5b5569971eddcf0a43bd5ba97f60f506
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="wire-data-20-preview-solution-in-log-analytics"></a>Podczas transmisji danych 2.0 (wersja zapoznawcza) rozwiązania analizy dzienników

![Podczas transmisji danych symbolu](./media/log-analytics-wire-data/wire-data2-symbol.png)

Podczas transmisji danych jest skonsolidowanych danych sieci i wydajności zebrane z komputerów połączonych z systemem Windows i połączonego Linux z agentem pakietu OMS, łącznie z tymi monitorowane przez program Operations Manager w środowisku. Dane sieci są łączone z innymi danymi dziennika ułatwiają skorelować danych.

Oprócz agent pakietu OMS rozwiązania podczas transmisji danych używa Microsoft zależności Agents instalowanego na komputerach w infrastrukturze IT. Zależności agenci monitorują dane sieci wysyłane do i z komputerów dla sieci poziomy 2 – 3 [OSI model](https://en.wikipedia.org/wiki/OSI_model), włącznie z różnych protokołów i portów używanych. Dane są następnie wysyłane do analizy dzienników przy użyciu agentów.  

> [!NOTE]
> Nie można dodać poprzedniej wersji rozwiązania przesyłania danych do nowych obszarów roboczych. Jeśli masz w oryginalnym rozwiązaniu udostępniający dane włączone, można nadal z niego korzystać. Jednak aby używać podczas transmisji danych 2.0, należy najpierw usunąć oryginalną wersją.

Domyślnie analizy dzienników rejestruje dane dla Procesora, pamięci, dysku i sieci danych dotyczących wydajności z liczników wbudowane w system Windows i Linux, a także innych liczniki wydajności, które można określić. Sieci i innych zbieranie danych odbywa się w czasie rzeczywistym dla każdego agenta, włącznie z podsieci i protokoły poziomu aplikacji, używane przez komputer.  Podczas transmisji danych analizuje dane sieci na poziomie aplikacji, nie w dół w przypadku warstwy transportu TCP.  Rozwiązanie nie wygląda na poszczególnych potwierdzeń i syn.  Po ukończeniu uzgadniania jest uznawany za połączenia na żywo i oznaczone jako połączony. Połączenie pozostaje na żywo, jak długo obie strony zgadzają się gniazda jest otwarty i dane można przekazać i z powrotem.  Po obu stronach zamyka połączenie, jest oznaczony jako rozłączony.  W związku z tym tylko liczy przepustowości pomyślnie zakończono pakietów, go nie raportują na wysyła ponownie, lub nie powiodło się pakietów.

Jeśli użyto [sFlow](http://www.sflow.org/) lub innego oprogramowania z [protokołu NetFlow firmy Cisco](http://www.cisco.com/c/en/us/products/collateral/ios-nx-os-software/ios-netflow/prod_white_paper0900aecd80406232.html), statystyk i danych, zostanie wyświetlony podczas transmisji danych będzie znane.

Typy wbudowane zapytania wyszukiwania dziennika, należą:

- Agenci udostępniający dane
- Adres IP agentów udostępniających dane o komunikacji sieciowej
- Komunikacja wychodząca według adresów IP
- Liczba bajtów wysłanych przez protokoły aplikacji
- Liczba bajtów wysłanych przez usługę aplikacji
- Bajty odebrane przez różnych protokołów
- Całkowita liczba bajtów wysłanych i odebranych według wersji adresu IP
- Średni czas oczekiwania dla połączenia, które zostały wiarygodny
- Komputer przetwarza sieciowej zainicjowały lub odebrały ruch
- Ilość ruchu sieciowego dla procesu

Podczas wyszukiwania przy użyciu udostępniający dane, można filtrować i grupy danych, aby wyświetlić informacje o najwyższym agentów i protokoły najwyższego. Lub kiedy można wyświetlić niektórych komputerów (adresów IP adresy MAC) przekazywane ze sobą, jak długo i ilość danych została wysłana — zasadniczo wyświetlić metadane dotyczące ruchu sieciowego, który jest na podstawie wyszukiwania.

Jednak ponieważ wyświetlasz metadanych nie jest zawsze przydatne podczas rozwiązywania szczegółowe. Podczas transmisji danych analizy dzienników nie jest pełną przechwytywania danych w sieci.  Nie jest on przeznaczony do rozwiązywania problemów głębokość poziomie pakietów. Zaletą przy użyciu agenta, w porównaniu do innych metod kolekcji jest, że nie trzeba zainstalować urządzenia, skonfiguruj ponownie przełączników sieciowych lub przeprowadzić skomplikowane konfiguracje. Podczas transmisji danych jest po prostu agenta na podstawie — Zainstaluj agenta na komputerze i będzie monitorować ruch sieci. Inną zaletą jest to, jeśli chcesz monitorować obciążeń działających w chmurze dostawcy lub dostawcy usług hostingowych lub Microsoft Azure, gdy użytkownik nie posiada warstwy sieci szkieletowej.

## <a name="connected-sources"></a>Połączone źródła

Podczas transmisji danych dane są pobierane z Microsoft Dependency Agent. Agent zależności zależy od agenta pakietu OMS dla jego połączenia z analizy dzienników. To oznacza, że serwer musi mieć Agent pakietu OMS zainstalowany i skonfigurowany najpierw, a następnie zainstaluj agenta zależności. W poniższej tabeli opisano połączonych źródeł, które obsługuje rozwiązania przesyłania danych.

| **Źródło połączenia** | **Obsługiwane** | **Opis** |
| --- | --- | --- |
| Agenci dla systemu Windows | Yes | Podczas transmisji danych analizuje i zbiera dane z komputerów z systemem Windows agenta. <br><br> Oprócz [Agent pakietu OMS](log-analytics-windows-agent.md), agentów systemu Windows wymagają Microsoft Dependency Agent. Zobacz [obsługiwanych systemów operacyjnych](../monitoring/monitoring-service-map-configure.md#supported-operating-systems) pełną listę wersji systemu operacyjnego. |
| Agenci dla systemu Linux | Yes | Podczas transmisji danych analizuje i zbiera dane z komputerów z systemem Linux agenta.<br><br> Oprócz [Agent pakietu OMS](log-analytics-quick-collect-linux-computer.md), Microsoft Dependency Agent wymagają agentów systemu Linux. Zobacz [obsługiwanych systemów operacyjnych](../monitoring/monitoring-service-map-configure.md#supported-operating-systems) pełną listę wersji systemu operacyjnego. |
| Grupa zarządzania programu System Center Operations Manager | Yes | Podczas transmisji danych analizuje i zbiera dane z agentów systemu Windows i Linux w połączonych [grupy zarządzania programu System Center Operations Manager](log-analytics-om-agents.md). <br><br> Połączenie bezpośrednie z komputera agenta programu System Center Operations Manager do analizy dzienników jest wymagana. Dane są przesyłane dalej z grupy zarządzania do analizy dzienników. |
| Konto magazynu Azure | Nie | Podczas transmisji danych zbiera dane z komputery agenta, więc nie ma żadnych danych z niego do zbierania z usługi Azure Storage. |

W systemie Windows Microsoft Monitoring Agent (MMA) jest używany zarówno przez System Center Operations Manager i analizy dzienników do zbierania i wysyłania danych. W zależności od kontekstu agent jest nazywany agenta programu System Center Operations Manager, Agent pakietu OMS, Agent analizy dziennika, MMA lub bezpośredniego agenta. System Center Operations Manager i Log Analytics zapewnia nieco inne wersje MMA. Te wersje strony każdy raport do programu System Center Operations Manager do analizy dzienników lub obie.

W systemie Linux Agent pakietu OMS Linux zbiera i wysyłania danych do analizy dzienników. Na serwerach z agentami bezpośredniego OMS lub na serwerach, które są dołączone do analizy dzienników za pośrednictwem grup zarządzania programu System Center Operations Manager, można użyć podczas transmisji danych.

W tym artykule odwołuje się do wszystkich agentów, czy Linux lub Windows, czy połączony z grupą zarządzania programu System Center Operations Manager lub bezpośrednio do analizy dzienników są określane jako _agent pakietu OMS_. Nazwa określonego wdrożenia agenta będą używane tylko wtedy, gdy jest wymagana dla kontekstu.

Agent zależności nie przesyła wszystkie dane, a nie wymaga zmian zapory lub porty. Dane udostępniający dane są zawsze przesyłane przez agenta pakietu OMS z analizą dzienników, albo bezpośrednio lub przy użyciu bramy OMS.

![diagram agenta](./media/log-analytics-wire-data/agents.png)

Jeśli jesteś użytkownikiem programu System Center Operations Manager z grupą zarządzania podłączone do analizy dzienników:

- Dodatkowa konfiguracja nie jest wymagana, gdy agentów programu System Center Operations Manager można uzyskać dostęp do Internetu do nawiązania połączenia analizy dzienników.
- Należy skonfigurować bramę OMS do pracy z programem System Center Operations Manager, gdy agentów programu System Center Operations Manager nie może uzyskać dostęp do analizy dzienników za pośrednictwem Internetu.

Jeśli używasz bezpośredniej agenta, należy skonfigurować agenta pakietu OMS do połączenia analizy dzienników lub bramy OMS. Możesz pobrać bramy OMS z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=52666).

## <a name="prerequisites"></a>Wymagania wstępne

- Wymaga [szczegółowe dane i analiza](https://www.microsoft.com/cloud-platform/operations-management-suite-pricing) oferta rozwiązania.
- Jeśli używasz poprzedniej wersji rozwiązania podczas transmisji danych, należy najpierw usunąć go. Jednak wszystkie dane zarejestrowane przez w oryginalnym rozwiązaniu udostępniający dane są nadal dostępne w podczas transmisji danych w wersji 2.0 i wyszukiwania dziennika.
- Aby zainstalować lub odinstalować agenta zależności są wymagane uprawnienia administratora.
- Musi być zainstalowany Agent zależności na komputerze z 64-bitowym systemie operacyjnym.

### <a name="operating-systems"></a>Systemy operacyjne

Poniższe sekcje zawierają listę obsługiwanych systemów operacyjnych dla agenta zależności. Podczas transmisji danych nie obsługuje architektury 32-bitowego dla dowolnego systemu operacyjnego.

#### <a name="windows-server"></a>Windows Server

- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

#### <a name="windows-desktop"></a>Pulpit systemu Windows

- Windows 10
- Windows 8.1
- Windows 8
- Windows 7

#### <a name="red-hat-enterprise-linux-centos-linux-and-oracle-linux-with-rhel-kernel"></a>Red Hat Enterprise Linux, CentOS Linux i Oracle Linux (z RHEL jądra)

- Obsługiwane są tylko domyślnej i wersjach jądra systemu Linux SMP.
- Zwalnia jądra niestandardowe, takie jak rozszerzenia adresu fizycznego i Xen, nie są obsługiwane dla dowolnego dystrybucji systemu Linux. Na przykład system z ciąg wersji _2.6.16.21-0.8-xen_ nie jest obsługiwane.
- Niestandardowe jądra, w tym ponownych kompilacji standardowe jądra, nie są obsługiwane.
- CentOSPlus jądra nie jest obsługiwane.
- Oracle podzielenie Enterprise jądra (UEK) zostało opisane w dalszej części tego artykułu.

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| **Wersja systemu operacyjnego** | **Wersja jądra** |
| --- | --- |
| 7.0 | 3.10.0-123 |
| 7.1 | 3.10.0-229 |
| 7.2 | 3.10.0-327 |
| 7.3 | 3.10.0-514 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| **Wersja systemu operacyjnego** | **Wersja jądra** |
| --- | --- |
| 6.0 | 2.6.32-71 |
| 6.1 | 2.6.32-131 |
| 6.2 | 2.6.32-220 |
| 6.3 | 2.6.32-279 |
| 6.4 | 2.6.32-358 |
| 6.5 | 2.6.32-431 |
| 6.6 | 2.6.32-504 |
| 6.7 | 2.6.32-573 |
| 6.8 | 2.6.32-642 |

#### <a name="red-hat-linux-5"></a>Red Hat Linux 5

| **Wersja systemu operacyjnego** | **Wersja jądra** |
| --- | --- |
| 5.8 | 2.6.18-308 |
| 5.9 | 2.6.18-348 |
| 5.10 | 2.6.18-371 |
| 5.11 | 2.6.18-398 <br> 2.6.18-400 <br>2.6.18-402 <br>2.6.18-404 <br>2.6.18-406 <br> 2.6.18-407 <br> 2.6.18-408 <br> 2.6.18-409 <br> 2.6.18-410 <br> 2.6.18-411 <br> 2.6.18-412 <br> 2.6.18-416 <br> 2.6.18-417 <br> 2.6.18-419 |

#### <a name="oracle-enterprise-linux-with-unbreakable-enterprise-kernel"></a>Oracle Linux przedsiębiorstwa z jądra podzielenie Enterprise

#### <a name="oracle-linux-6"></a>Oracle Linux 6

| **Wersja systemu operacyjnego** | **Wersja jądra** |
| --- | --- |
| 6.2 | Oracle 2.6.32-300 (UEK R1) |
| 6.3 | Oracle 2.6.39-200 (UEK R2) |
| 6.4 | Oracle 2.6.39-400 (UEK R2) |
| 6.5 | Oracle 2.6.39-400 (UEK R2 i386) |
| 6.6 | Oracle 2.6.39-400 (UEK R2 i386) |

#### <a name="oracle-linux-5"></a>Oracle Linux 5

| **Wersja systemu operacyjnego** | **Wersja jądra** |
| --- | --- |
| 5.8 | Oracle 2.6.32-300 (UEK R1) |
| 5.9 | Oracle 2.6.39-300 (UEK R2) |
| 5.10 | Oracle 2.6.39-400 (UEK R2) |
| 5.11 | Oracle 2.6.39-400 (UEK R2) |

#### <a name="suse-linux-enterprise-server"></a>SUSE Linux Enterprise Server

#### <a name="suse-linux-11"></a>SUSE Linux 11

| **Wersja systemu operacyjnego** | **Wersja jądra** |
| --- | --- |
| 11 | 2.6.27 |
| 11 SP1 | 2.6.32 |
| 11 SP2 | 3.0.13 |
| 11 SP3 | 3.0.76 |
| 11 SP4 | 3.0.101 |

#### <a name="suse-linux-10"></a>SUSE Linux 10

| **Wersja systemu operacyjnego** | **Wersja jądra** |
| --- | --- |
| 10 SP4 | 2.6.16.60 |

#### <a name="dependency-agent-downloads"></a>Zależności agenta pliki do pobrania

| **Plik** | **OS** | **Wersja** | **SHA-256** |
| --- | --- | --- | --- |
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.0.5 | 73B3F6A2A76A08D58F72A550947FF839B588591C48E6EDDD6DDF73AA3FD82B43 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.0.5 | A1BAD0B36EBF79F2B69113A07FCF48C68D90BD169C722689F9C83C69FC032371 |



## <a name="configuration"></a>Konfigurowanie

Wykonaj poniższe kroki, aby skonfigurować rozwiązanie udostępniający dane dla obszarów roboczych.

1. Włącz rozwiązania analizy dzienników działania z [witrynę Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WireData2OMS?tab=Overview) lub przy użyciu procesu opisanego w [rozwiązań dodać analizy dzienników z galerii rozwiązań](log-analytics-add-solutions.md).
2. Na każdym komputerze, na którym chcesz pobrać dane, należy zainstalować agenta zależności. Dependency Agent można monitorować połączenia do natychmiastowego sąsiadów, więc nie trzeba agenta na każdym komputerze.

### <a name="install-the-dependency-agent-on-windows"></a>Zainstaluj agenta zależności w systemie Windows

Aby zainstalować lub odinstalować agenta wymagane są uprawnienia administratora.

Dependency Agent jest zainstalowany na komputerach z systemem Windows za pośrednictwem InstallDependencyAgent Windows.exe. Po uruchomieniu tego pliku wykonywalnego bez żadnych opcji uruchamia kreatora, który można wykonać w celu zainstalowania interaktywnie.

Aby zainstalować agenta zależności na każdy komputer z systemem Windows, wykonaj następujące kroki:

1. Zainstaluj agenta pakietu OMS zgodnie z krokami w [zbierania danych z komputerów z systemem Windows w środowisku](log-analytics-windows-agent.md).
2. Pobierz agenta zależności systemu Windows przy użyciu łącza w poprzedniej sekcji, a następnie uruchom go za pomocą następującego polecenia: `InstallDependencyAgent-Windows.exe`
3. Użyj kreatora, aby zainstalować agenta.
4. Jeśli Dependency Agent nie powiedzie się, sprawdź dzienniki, aby uzyskać szczegółowe informacje o błędzie. W przypadku agentów systemu Windows katalog dziennika jest %Programfiles%\Microsoft Agent\logs zależności.

#### <a name="windows-command-line"></a>Wiersz polecenia systemu Windows

Opcje z poniższej tabeli służą do instalacji z wiersza polecenia. Aby wyświetlić listę flagi instalacji, należy uruchomić Instalatora przy użyciu /? Flaga w następujący sposób.

InstallDependencyAgent-Windows.exe /?

| **Flaga** | **Opis** |
| --- | --- |
| <code>/?</code> | Pobierz listę opcji wiersza polecenia. |
| <code>/S</code> | Wykonaj instalację dyskretną bez monitowania użytkownika. |

Pliki agenta zależności systemu Windows są umieszczane w C:\Program Files\Microsoft Dependency Agent domyślnie.

### <a name="install-the-dependency-agent-on-linux"></a>Zainstaluj agenta zależności w systemie Linux

Dostęp do konta root jest wymagane do zainstalowania i skonfigurowania agenta.

Dependency Agent jest zainstalowany na komputery z systemem Linux za pomocą Linux64.bin InstallDependencyAgent, skrypt powłoki z samowyodrębniający plikiem binarnym. Plik ten można uruchomić przy użyciu _sh_ lub Dodaj uprawnienia w samym pliku do wykonywania.

Aby zainstalować agenta zależności na każdym komputerze z systemem Linux, wykonaj następujące kroki:

1. Zainstaluj agenta pakietu OMS zgodnie z krokami w [zbierania danych z komputerów z systemem Linux hostowanych w danym środowisku](log-analytics-quick-collect-linux-computer.md#obtain-workspace-id-and-key).
2. Pobierz agenta zależności systemu Linux przy użyciu łącza w poprzedniej sekcji, a następnie zainstaluj go jako głównego za pomocą następującego polecenia: sh InstallDependencyAgent Linux64.bin
3. Jeśli Dependency Agent nie powiedzie się, sprawdź dzienniki, aby uzyskać szczegółowe informacje o błędzie. Na agentów systemu Linux jest katalog dziennika: /var/opt/microsoft/dependency-agent/log.

Aby wyświetlić listę flagi instalacji, uruchom program instalacyjny z `-help` Flaga w następujący sposób.

```
InstallDependencyAgent-Linux64.bin -help
```

| **Flaga** | **Opis** |
| --- | --- |
| <code>-help</code> | Pobierz listę opcji wiersza polecenia. |
| <code>-s</code> | Wykonaj instalację dyskretną bez monitowania użytkownika. |
| <code>--check</code> | Sprawdź uprawnienia i systemu operacyjnego, ale nie należy instalować agenta. |

Pliki programu Agent zależności są umieszczane w następujących katalogów:

| **Pliki** | **Lokalizacja** |
| --- | --- |
| Podstawowe pliki | /opt/microsoft/dependency-agent |
| Pliki dziennika | /var/OPT/Microsoft/Dependency-Agent/log |
| Pliki konfiguracji | /etc/opt/microsoft/dependency-agent/config |
| Pliki wykonywalne usługi | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br><br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Pliki binarne magazynu | /var/OPT/Microsoft/Dependency-Agent/Storage |

### <a name="installation-script-examples"></a>Przykłady skryptów instalacji

Aby łatwo wdrożyć agenta zależności na wiele serwerów na raz, pomaga za pomocą skryptu. W poniższych przykładach skrypt umożliwia pobranie i zainstalowanie agenta zależności w systemu Windows lub Linux.

#### <a name="powershell-script-for-windows"></a>Skrypt programu PowerShell dla systemu Windows

```PowerShell

Invoke-WebRequest &quot;https://aka.ms/dependencyagentwindows&quot; -OutFile InstallDependencyAgent-Windows.exe

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

Aby wdrożyć Dependency Agent za pomocą konfiguracji żądanego stanu, można użyć modułu xPSDesiredStateConfiguration i kodu podobne do poniższych:

```
Import-DscResource -ModuleName xPSDesiredStateConfiguration

$DAPackageLocalPath = &quot;C:\InstallDependencyAgent-Windows.exe&quot;



Node $NodeName

{

    # Download and install the Dependency Agent

    xRemoteFile DAPackage

    {

        Uri = &quot;https://aka.ms/dependencyagentwindows&quot;

        DestinationPath = $DAPackageLocalPath

        DependsOn = &quot;[Package]OI&quot;

    }

    xPackage DA

    {

        Ensure=&quot;Present&quot;

        Name = &quot;Dependency Agent&quot;

        Path = $DAPackageLocalPath

        Arguments = '/S'

        ProductId = &quot;&quot;

        InstalledCheckRegKey = &quot;HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent&quot;

        InstalledCheckRegValueName = &quot;DisplayName&quot;

        InstalledCheckRegValueData = &quot;Dependency Agent&quot;

    }

}

```
### <a name="uninstall-the-dependency-agent"></a>Odinstaluj agenta zależności

Poniższe sekcje umożliwiają usuwania agenta zależności.

#### <a name="uninstall-the-dependency-agent-on-windows"></a>Odinstaluj agenta zależności w systemie Windows

Administrator może odinstalować zależności agenta dla systemu Windows za pomocą Panelu sterowania.

Administrator można również uruchomić %Programfiles%\Microsoft Agent\Uninstall.exe zależności można odinstalować agenta zależności.

#### <a name="uninstall-the-dependency-agent-on-linux"></a>Odinstaluj agenta zależności w systemie Linux

Aby całkowicie odinstalować agenta zależności z systemem Linux, należy usunąć sam agent i łącznika, który jest instalowany automatycznie przy użyciu agenta. Można odinstalować zarówno przy użyciu pojedynczego następujące polecenie:

```
rpm -e dependency-agent dependency-agent-connector
```

## <a name="management-packs"></a>Pakiety administracyjne

Po aktywowaniu podczas transmisji danych w obszarze roboczym analizy dzienników pakietu administracyjnego 300 KB są wysyłane do wszystkich serwerów z systemem Windows w tym obszarze roboczym. Jeśli używasz programu System Center Operations Manager agentów w [podłączonej grupy zarządzania](log-analytics-om-agents.md), Monitor zależności pakietu administracyjnego wdrażania programu System Center Operations Manager. Jeżeli agenci są połączone bezpośrednio Log Analytics zapewnia pakietu administracyjnego.

Pakiet administracyjny nosi nazwę Microsoft.IntelligencePacks.ApplicationDependencyMonitor. Jest ona zapisywana w: %Programfiles%\Microsoft monitorowania Agent\Agent\Health usługi State\Management pakietów. To źródło danych, które korzysta z pakietu administracyjnego: % Program files%\Microsoft monitorowanie Agent\Agent\Health usługi State\Resources&lt;AutoGeneratedID&gt;\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll.

## <a name="using-the-solution"></a>Użycie rozwiązania

**Instalowanie i konfigurowanie rozwiązania**

Skorzystaj z poniższych informacji, aby zainstalować i skonfigurować rozwiązania.

- Rozwiązanie udostępniający dane pobrania danych z komputerów z systemem Windows Server 2012 R2, Windows 8.1 i nowszych systemów operacyjnych.
- Microsoft .NET Framework 4.0 lub nowszy jest wymagany na komputerach, na którym chcesz uzyskać udostępniający dane z.
- Dodaj rozwiązanie przesyłania danych do obszaru roboczego analizy dzienników przy użyciu procesu opisanego w [rozwiązań dodać analizy dzienników z galerii rozwiązań](log-analytics-add-solutions.md). Nie są wymagane żadne dalsze czynności konfiguracyjne.
- Aby wyświetlić udostępniający dane dla konkretnego rozwiązania, należy mieć rozwiązanie już dodana do obszaru roboczego.

Po zostać zainstalowani agenci i zainstalować rozwiązania, w obszarze roboczym pojawi się Kafelek podczas transmisji danych 2.0.

![Podczas transmisji danych kafelków](./media/log-analytics-wire-data/wire-data-tile.png)

## <a name="using-the-wire-data-20-solution"></a>Za pomocą rozwiązania podczas transmisji danych 2.0

W portalu OMS kliknij **podczas transmisji danych 2.0** Kafelek, aby otworzyć pulpit nawigacyjny udostępniający dane. Pulpit nawigacyjny zawiera bloki w poniższej tabeli. Każdy blok zawiera maksymalnie 10 elementów spełniających kryteria tego bloku dla określonego zakresu i zakres czasu. Można uruchomić wyszukiwania dziennika, który zwraca wszystkie rekordy, klikając **zobaczyć wszystkie** w dolnej części bloku lub przez kliknięcie nagłówka bloku.

| **Blade** | **Opis** |
| --- | --- |
| Agenci przechwytujący ruch sieciowy | Zawiera liczbę agentów, które są Przechwytywanie ruchu sieciowego i listę top 10 komputerów, które są Przechwytywanie ruchu. Kliknij liczbę do uruchamiania dziennika wyszukiwanie <code>Type:WireData &#124; measure Sum(TotalBytes) by Computer &#124; top 500000</code>. Kliknij komputer, na liście do uruchomienia zwracanie całkowita liczba bajtów przechwytywane wyszukiwanie dziennika. |
| Lokalne podsieci | Pokazuje liczbę podsieci lokalne, które zostały odnalezione agentów.  Kliknij liczbę do uruchamiania dziennika wyszukiwanie <code>Type:WireData &#124; Measure Sum(TotalBytes) by LocalSubnet</code> które wyświetla listę wszystkich podsieci z liczbę bajtów przesyłanych w ramach każdej z nich. Kliknij przycisk podsieci na liście, aby uruchomić wyszukiwanie dziennika zwracanie całkowita liczba bajtów wysłanych w tej podsieci. |
| Protokoły poziomu aplikacji | Pokazuje liczbę protokoły poziomu aplikacji w użyciu, wykrywane przez agentów. Kliknij liczbę do uruchamiania dziennika wyszukiwanie <code>Type:WireData &#124; Measure Sum(TotalBytes) by ApplicationProtocol</code>. Kliknij protokół, aby uruchomić wyszukiwanie dziennika zwracanie całkowita liczba bajtów wysłanych przy użyciu protokołu. |

[!INCLUDE[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

![Podczas transmisji danych z pulpitu nawigacyjnego](./media/log-analytics-wire-data/wire-data-dash.png)

Można użyć **agenci przechwytujący ruch sieciowy** bloku, aby ustalić, jaka przepustowość sieci jest są używane przez komputery. Ten blok mogą pomóc w prosty sposób wyszukiwać _chattiest_ komputera w danym środowisku. Komputery te może być przeciążony działający nieprawidłowo lub przy użyciu więcej zasobów sieci niż zwykle.

![Przykład wyszukiwania dziennika](./media/log-analytics-wire-data/log-search-example01.png)

Analogicznie, można użyć **podsieci lokalne** bloku, aby określić, ile ruch sieciowy jest przenoszenie za pomocą podsieci. Użytkownicy często definiować podsieci wokół najważniejsze obszary, w przypadku ich aplikacji. Ten blok oferuje zapewnia wgląd w tych obszarach.

![Przykład wyszukiwania dziennika](./media/log-analytics-wire-data/log-search-example02.png)

**Protokoły poziomu aplikacji** bloku przydaje się, dlatego warto wiedzieć, jakie protokoły są używane. Na przykład może spodziewać się SSH nie będzie używana w środowisku sieciowym. Wyświetlanie informacji o dostępnych w bloku można szybko potwierdzenia lub disprove Twoje oczekiwania.

![Przykład wyszukiwania dziennika](./media/log-analytics-wire-data/log-search-example03.png)

W tym przykładzie użytkownik może Wejdź do szczegóły SSH, aby wyświetlić komputery, które korzystają z protokołu SSH i inne szczegóły komunikacji.

![Pokaż wyniki wyszukiwania](./media/log-analytics-wire-data/ssh-details.png)

Jest również warto wiedzieć, jeśli ruch w protokole jest zwiększenie lub zmniejszenie wraz z upływem czasu. Na przykład jeśli wzrasta ilość danych przesyłanych przez aplikację, która może być coś, co należy zwrócić uwagę, lub czy można znaleźć warte wymienienia.

## <a name="input-data"></a>Dane wejściowe

Podczas transmisji danych zbiera metadane dotyczące ruchu sieciowego przy użyciu agentów, które zostało włączone. Każdy agent wysyła dane dotyczące co 15 s.

## <a name="output-data"></a>Dane wyjściowe

Rekord o typie _WireData_ jest tworzony dla każdego typu danych wejściowych. Rejestruje WireData mają właściwości poniższą tabelą:

| Właściwość | Opis |
|---|---|
| Computer (Komputer) | Nazwa komputera, w których zebrano dane |
| TimeGenerated | Czas rekordu |
| LocalIP | Adres IP komputera lokalnego |
| SessionState | Połączone lub rozłączone |
| ReceivedBytes | Liczba bajtów odebranych |
| ProtocolName | Nazwa używanego protokołu sieciowego |
| Wersję adresu IP ustawioną | Wersji protokołu IP |
| Kierunek | Przychodzący lub wychodzący |
| MaliciousIP | Adres IP znanego złośliwego źródła |
| Ważność | Ważność podejrzanych złośliwego oprogramowania |
| RemoteIPCountry | Kraj zdalny adres IP |
| ManagementGroupName | Nazwa grupy zarządzania programu Operations Manager |
| SourceSystem | Źródło, gdzie zostały zebrane dane |
| SessionStartTime | Czas rozpoczęcia sesji |
| SessionEndTime | Godzina zakończenia sesji |
| LocalSubnet | Podsieć, w których zebrano dane |
| LocalPortNumber | Numer portu lokalnego |
| RemoteIP | Zdalny adres IP używany przez komputer zdalny |
| RemotePortNumber | Numer portu używany przez zdalny adres IP |
| Identyfikator sesji | Unikatowa wartość, która identyfikuje sesji komunikacji między dwoma adresami IP |
| SentBytes | Liczba bajtów wysłanych |
| TotalBytes | Całkowita liczba bajtów wysłanych podczas sesji |
| ApplicationProtocol | Typ protokołu sieciowego używane   |
| Identyfikator procesu | Identyfikator procesu systemu Windows |
| ProcessName | Ścieżka i nazwa pliku procesu |
| RemoteIPLongitude | Wartości długości geograficznej IP |
| RemoteIPLatitude | Wartość szerokości geograficznej IP |


## <a name="next-steps"></a>Kolejne kroki

- [Wyszukaj dzienniki](log-analytics-log-searches.md) Aby wyświetlić szczegółowe podczas transmisji danych wyszukiwanie rekordów.
