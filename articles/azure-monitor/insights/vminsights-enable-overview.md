---
title: Włączanie usługi Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza) — omówienie | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wdrożyć i skonfigurować usługi Azure Monitor dla maszyn wirtualnych. Dowiedz się z wymaganiami systemowymi.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/28/2019
ms.author: magoedte
ms.openlocfilehash: 6fb7e6b9611b28dab856209aaf03aa93c25d3968
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478046"
---
# <a name="enable-azure-monitor-for-vms-preview-overview"></a>Włączanie usługi Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza) — omówienie

W tym artykule omówiono dostępne opcje Aby skonfigurować usługi Azure Monitor dla maszyn wirtualnych. Używanie usługi Azure Monitor dla maszyn wirtualnych, monitorowanie kondycji i wydajności. Odkryj zależności aplikacji, które działają na maszynach wirtualnych platformy Azure (maszyny wirtualne) i zestawy skalowania maszyn wirtualnych, lokalnych maszyn wirtualnych lub maszyn wirtualnych hostowanych w innym środowisku chmury.  

Aby skonfigurować usługi Azure Monitor dla maszyn wirtualnych:

* Włącz pojedynczej maszyny Wirtualnej platformy Azure lub maszyny wirtualnej zestawu skalowania, wybierając **Insights (wersja zapoznawcza)** bezpośrednio od maszyny Wirtualnej lub maszyny wirtualnej zestawu skalowania.
* Włączyć dwie lub więcej maszyn wirtualnych platformy Azure i virtual machine scale sets przy użyciu usługi Azure Policy. Ta metoda zapewnia zainstalowany i poprawnie skonfigurowany na istniejących i nowych maszyn wirtualnych i zestawów skalowania, wymagane zależności. Niezgodne maszyny wirtualne i zestawy skalowania są zgłaszane, dzięki czemu możesz zdecydować, czy można je włączyć i je skorygować.
* Włączyć dwie lub więcej maszyn wirtualnych platformy Azure lub na maszynie wirtualnej skalować zestawy skalowania w określonej subskrypcji lub grupy zasobów przy użyciu programu PowerShell.
* Włączanie usługi Azure Monitor dla maszyn wirtualnych do monitorowania maszyn wirtualnych lub fizycznych komputerów hostowanych w sieci firmowej lub w innym środowisku chmury.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że rozumiesz, informacje w poniższych sekcjach.

### <a name="log-analytics"></a>Log Analytics

Usługa Azure Monitor dla maszyn wirtualnych obsługuje obszar roboczy usługi Log Analytics w następujących regionach:

- Środkowo-zachodnie stany USA
- Zachodnie stany USA 2<sup>1</sup>
- East US
- Kanada Środkowa
- Południowe Zjednoczone Królestwo
- Europa Zachodnia
- Azja Południowo-Wschodnia

<sup>1</sup> ten region nie obsługuje obecnie funkcję kondycji usługi Azure monitor maszyn wirtualnych.

>[!NOTE]
>Można wdrożyć maszyny wirtualne platformy Azure z dowolnego regionu. Te maszyny wirtualne nie są ograniczone do określonych regionów obsługiwanych przez obszar roboczy usługi Log Analytics.
>

Jeśli nie masz obszaru roboczego, można utworzyć za pomocą jednej z następujących zasobów:
* [Interfejs wiersza polecenia platformy Azure](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [Program PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)
* [Witryna Azure Portal](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)

Można również utworzyć obszar roboczy, natomiast w przypadku włączania monitorowania dla pojedynczej maszyny Wirtualnej platformy Azure lub maszyny wirtualnej zestawu skalowania w witrynie Azure portal.

Aby skonfigurować scenariusz na dużą skalę, która korzysta z szablonów usługi Azure Policy, programu Azure PowerShell lub usługi Azure Resource Manager, w obszarze roboczym usługi Log Analytics:

* Zainstaluj ServiceMap i InfrastructureInsights rozwiązań. Tę instalację można wykonać za pomocą podanego szablonu Azure Resource Manager. Lub na **wprowadzenie** zaznacz **skonfigurować obszar roboczy**.
* Konfiguruj obszar roboczy usługi Log Analytics, można zebrać liczników wydajności.

Aby skonfigurować obszar roboczy dla scenariusza na dużą skalę, użyj jednej z następujących metod:

* Użyj [programu Azure PowerShell](vminsights-enable-at-scale-powershell.md#set-up-a-log-analytics-workspace).
* W usłudze Azure Monitor dla maszyn wirtualnych [ **pokrycia zasad** ](vminsights-enable-at-scale-policy.md#manage-policy-coverage-feature-overview) wybierz opcję **skonfigurować obszar roboczy**. 

### <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

W poniższej tabeli wymieniono systemy operacyjne Windows i Linux, które obsługuje usługi Azure Monitor dla maszyn wirtualnych. Później w tej sekcji znajdziesz pełną listę głównych i pomocniczych systemu operacyjnego Linux wydania i obsługiwane wersje jądra, który wyszczególnia.

|Wersja systemu operacyjnego |Wydajność |Maps |Health |
|-----------|------------|-----|-------|
|Windows Server 2019 | X | X | X |
|System Windows Server 2016 1803 | X | X | X |
|Windows Server 2016 | X | X | X |
|Windows Server 2012 R2 | X | X | X |
|Windows Server 2012 | X | X | |
|Windows Server 2008 R2 | X | X| |
|Red Hat Enterprise Linux (RHEL) 6, 7| X | X| X |
|Ubuntu 14.04, 16.04, 18.04 | X | X | X |
|CentOS Linux 6, 7 | X | X | X |
|SUSE Linux Enterprise Server (SLES) 12 | X | X | X |
|Debian 8, 9.4 | X<sup>1</sup> | | X |

<sup>1</sup> funkcja wydajności usługi Azure Monitor dla maszyn wirtualnych jest dostępna tylko z usługi Azure Monitor. Nie jest ona dostępna bezpośrednio z poziomu okienka po lewej stronie maszyny Wirtualnej platformy Azure.

>[!NOTE]
>Funkcja kondycji monitora platformy Azure dla maszyn wirtualnych nie obsługuje [wirtualizacji zagnieżdżonej](../../virtual-machines/windows/nested-virtualization.md) w Maszynie wirtualnej platformy Azure.
>

>[!NOTE]
>W systemie operacyjnym Linux:
> - Obsługiwane są tylko wersje domyślne i wersje SMP jądra systemu Linux.
> - Zwalnia jądra niestandardowe, takie jak rozszerzenia adresu fizycznego (PAE) i Xen, nie są obsługiwane w przypadku dowolnej dystrybucji systemu Linux. Na przykład system z ciągu wersji *2.6.16.21-0.8-xen* nie jest obsługiwane.
> - Niestandardowe jądra, w tym ponownych kompilacji o standardowych jądra nie są obsługiwane.
> - CentOSPlus jądra jest obsługiwana.

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Wersja systemu operacyjnego | Wersja jądra |
|:--|:--|
| 7.4 | 3.10.0-693 |
| 7.5 | 3.10.0-862 |
| 7.6 | 3.10.0-957 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Wersja systemu operacyjnego | Wersja jądra |
|:--|:--|
| 6.9 | 2.6.32-696 |
| 6.10 | 2.6.32-754 |

#### <a name="centosplus"></a>CentOSPlus
| Wersja systemu operacyjnego | Wersja jądra |
|:--|:--|
| 6.9 | 2.6.32-696.18.7<br>2.6.32-696.30.1 |
| 6.10 | 2.6.32-696.30.1<br>2.6.32-754.3.5 |

#### <a name="ubuntu-server"></a>Ubuntu Server

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

### <a name="the-microsoft-dependency-agent"></a>Program Microsoft Dependency agent

Funkcja mapy w usłudze Azure Monitor dla maszyn wirtualnych, dane są pobierane z Microsoft Dependency agent. Agent zależności zależy od agenta usługi Log Analytics do połączenia z usługi Log Analytics. Dlatego system musi mieć agenta usługi Log Analytics, zainstalować i skonfigurować za pomocą agenta zależności.

Czy włączyć usługi Azure Monitor dla maszyn wirtualnych z jednej maszyny wirtualnej platformy Azure lub użyć metody wdrożenia na dużą skalę, należy użyć rozszerzenia agenta zależności maszyn wirtualnych platformy Azure do zainstalowania agenta w ramach środowiska.

W środowisku hybrydowym można pobrać i zainstalować agenta zależności ręcznie. Jeśli Twoje maszyny wirtualne znajdują się poza systemem Azure, należy użyć metody wdrażania automatycznego.

W poniższej tabeli opisano połączone źródła obsługiwanych przez funkcję mapy w środowisku hybrydowym.

| Połączone źródło | Obsługiwane | Opis |
|:--|:--|:--|
| Agenci dla systemu Windows | Tak | Wraz z [agenta usługi Log Analytics dla Windows](../../azure-monitor/platform/log-analytics-agent.md), agenci Windows muszą agenta zależności. Aby uzyskać więcej informacji, zobacz [obsługiwane systemy operacyjne](#supported-operating-systems). |
| Agenci dla systemu Linux | Tak | Wraz z [agenta usługi Log Analytics dla systemu Linux](../../azure-monitor/platform/log-analytics-agent.md), agenci dla systemu Linux muszą agenta zależności. Aby uzyskać więcej informacji, zobacz [obsługiwane systemy operacyjne](#supported-operating-systems). |
| Grupa zarządzania programu System Center Operations Manager | Nie | |

Z tych lokalizacji, możesz pobrać agenta zależności:

| Plik | System operacyjny | Wersja | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.8.1 | 622C99924385CBF539988D759BCFDC9146BB157E7D577C997CDD2674E27E08DD |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.8.1 | 3037934A5D3FB7911D5840A9744AE9F980F87F620A7F7B407F05E276FE7AE4A8 |

## <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach

Aby włączyć i funkcje w usłudze Azure Monitor są dostępne dla maszyn wirtualnych, konieczne jest posiadanie *Współautor usługi Log Analytics* roli. Aby wyświetlić kondycję, wydajność i mapowanie danych, konieczne jest posiadanie *Czytelnik monitorowania* roli maszyny wirtualnej platformy Azure. Obszar roboczy usługi Log Analytics, musi być skonfigurowany dla usługi Azure Monitor dla maszyn wirtualnych.

Aby uzyskać więcej informacji na temat kontrolowania dostępu do obszaru roboczego usługi Log Analytics, zobacz [możesz zarządzać obszarami roboczymi](../../azure-monitor/platform/manage-access.md).

## <a name="how-to-enable-azure-monitor-for-vms-preview"></a>Jak włączyć usługi Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza)

Włączanie usługi Azure Monitor dla maszyn wirtualnych przy użyciu jednej z metod opisanych w poniższej tabeli:

| Stan wdrożenia | Metoda | Opis |
|------------------|--------|-------------|
| Jeden zestaw skalowania maszyn wirtualnych platformy Azure lub na maszynie wirtualnej | [Korzystanie z maszyny Wirtualnej](vminsights-enable-single-vm.md) | Pojedyncza maszyna wirtualna platformy Azure można włączyć, zaznaczając **Insights (wersja zapoznawcza)** bezpośrednio od maszyny Wirtualnej lub maszyny wirtualnej zestawu skalowania. |
| Wiele maszyn wirtualnych platformy Azure lub zestawy skalowania maszyn wirtualnych | [Włączyć za pomocą usługi Azure Policy](vminsights-enable-at-scale-policy.md) | Wiele maszyn wirtualnych platformy Azure można włączyć za pomocą usługi Azure Policy i dostępne definicje zasad. |
| Wiele maszyn wirtualnych platformy Azure lub zestawy skalowania maszyn wirtualnych | [Włącz za pomocą szablonów programu Azure PowerShell lub usługi Azure Resource Manager](vminsights-enable-at-scale-powershell.md) | Wiele zestawów skalowania maszyn wirtualnych platformy Azure lub na maszynie wirtualnej w określonej subskrypcji lub grupy zasobów można włączyć za pomocą szablonów programu Azure PowerShell lub usługi Azure Resource Manager. |
| Chmura hybrydowa | [Włączanie środowiska hybrydowego](vminsights-enable-hybrid-cloud.md) | Można wdrożyć do maszyn wirtualnych lub fizycznych komputerów, które są hostowane w centrum danych lub innych środowisk w chmurze. |

## <a name="performance-counters-enabled"></a>Liczniki wydajności włączone 

Usługa Azure Monitor dla maszyn wirtualnych służy do konfigurowania obszaru roboczego usługi Log Analytics można zebrać liczników wydajności, których używa. W poniższej tabeli wymieniono obiektów i liczników, które są gromadzone co 60 sekund.

### <a name="windows-performance-counters"></a>Liczniki wydajności systemu Windows

|Nazwa obiektu |Nazwa licznika |
|------------|-------------|
|Dysk logiczny |% Wolnego miejsca |
|Dysk logiczny |Średni Czas dysku w s/Odczyt |
|Dysk logiczny |Średni Dysku w s/Transfer |
|Dysk logiczny |Średni Dysku w s/Zapis |
|Dysk logiczny |Bajty dysku/s |
|Dysk logiczny |Bajty odczytu z dysku/s |
|Dysk logiczny |Odczyty dysku/s |
|Dysk logiczny |Transfery dyskowe/s |
|Dysk logiczny |Bajty zapisu na dysku/s |
|Dysk logiczny |Zapisy dysku/s |
|Dysk logiczny |Wolne megabajty |
|Memory (Pamięć) |Dostępna pamięć (MB) |
|Karta sieciowa |Bajty odebrane/s |
|Karta sieciowa |Bajty wysłane/s |
|Procesor |Czas procesora (%) |

### <a name="linux-performance-counters"></a>Liczniki wydajności systemu Linux

|Nazwa obiektu |Nazwa licznika |
|------------|-------------|
|Dysk logiczny |Procent wykorzystania miejsca |
|Dysk logiczny |Bajty odczytu z dysku/s |
|Dysk logiczny |Odczyty dysku/s |
|Dysk logiczny |Transfery dyskowe/s |
|Dysk logiczny |Bajty zapisu na dysku/s |
|Dysk logiczny |Zapisy dysku/s |
|Dysk logiczny |Wolne megabajty |
|Dysk logiczny |Bajty dysku logicznego/s |
|Memory (Pamięć) |Dostępna pamięć (MB) |
|Sieć |Całkowita liczba odebranych bajtów |
|Sieć |Całkowita liczba przesłanych bajtów |
|Procesor |Czas procesora (%) |

## <a name="diagnostic-and-usage-data"></a>Dane diagnostyczne i dane dotyczące użycia

Firma Microsoft automatycznie zbiera dane dotyczące użycia i wydajności przez korzystanie z usługi Azure Monitor. Firma Microsoft używa tych danych do poprawy jakości, bezpieczeństwa i integralności usługi. 

Aby zapewnić dokładne i wydajne funkcje do rozwiązywania problemów, funkcja mapy zawiera dane o konfiguracji oprogramowania. Dane zawierają informacje, takie jak system operacyjny i wersję, adres IP, nazwę DNS i nazwę stacji roboczej. Firma Microsoft nie zbiera nazwisk, adresów ani innych informacji kontaktowych.

Aby uzyskać więcej informacji na temat zbierania i wykorzystywania danych, zobacz [Microsoft Online Services Privacy Statement](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

Teraz, gdy zostało włączone, monitorowanie dla maszyny Wirtualnej, informacje o monitorowaniu jest dostępna do analizy w usłudze Azure Monitor dla maszyn wirtualnych.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się, jak korzystać z funkcji Health, zobacz [widok usługi Azure Monitor kondycji maszyn wirtualnych](vminsights-health.md). Aby obejrzeć zależności odnalezionych aplikacji, zobacz [widok usługi Azure Monitor dla maszyn wirtualnych mapy](vminsights-maps.md).
