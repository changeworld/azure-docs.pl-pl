---
title: Omówienie włączania usługi Azure Monitor dla maszyn wirtualnych
description: Dowiedz się, jak wdrożyć i skonfigurować usługę Azure Monitor dla maszyn wirtualnych. Dowiedz się więcej o wymaganiach systemowych.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/08/2020
ms.openlocfilehash: 5bb5d5dd5110f176b59a99f6a3aa223184158da5
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982314"
---
# <a name="enable-azure-monitor-for-vms-overview"></a>Omówienie włączania usługi Azure Monitor dla maszyn wirtualnych

Ten artykuł zawiera omówienie dostępnych opcji umożliwiających platformę Azure Monitor dla maszyn wirtualnych na maszynach wirtualnych monitorowanie kondycji i wydajności. Odnajduj zależności aplikacji uruchamiane na maszynach wirtualnych platformy Azure (maszyny wirtualne) i zestawach skalowania maszyn wirtualnych, lokalnych maszynach wirtualnych lub maszynach wirtualnych hostowanych w innym środowisku chmury.  

Aby skonfigurować usługę Azure Monitor dla maszyn wirtualnych:

* Włącz zestaw pojedynczej skali maszyny wirtualnej platformy Azure lub maszyny wirtualnej, wybierając pozycję **Insights** bezpośrednio z zestawu skalowania maszyny wirtualnej lub maszyny wirtualnej.
* Włącz co najmniej dwie maszyny wirtualne platformy Azure i zestawy skalowania maszyny wirtualnej przy użyciu zasad platformy Azure. Ta metoda zapewnia, że na istniejących i nowych maszyn wirtualnych i zestawów skalowania wymagane zależności są instalowane i poprawnie skonfigurowane. Niezgodne maszyny wirtualne i zestawy skalowania są zgłaszane, dzięki czemu można zdecydować, czy włączyć je i je naprawić.
* Włącz co najmniej dwie maszyny wirtualne platformy Azure lub zestawy skalowania maszyny wirtualnej w określonej subskrypcji lub grupie zasobów przy użyciu programu PowerShell.
* Włącz usługę Azure Monitor dla maszyn wirtualnych do monitorowania maszyn wirtualnych lub komputerów fizycznych hostowanych w sieci firmowej lub innym środowisku chmury.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że rozumiesz informacje w poniższych sekcjach. 

>[!NOTE]
>Poniższe informacje opisane w tej sekcji mają również zastosowanie do [rozwiązania mapy usług.](service-map.md)  

### <a name="log-analytics"></a>Log Analytics

Usługa Azure Monitor dla maszyn wirtualnych obsługuje obszar roboczy usługi Log Analytics w następujących regionach:

- Zachodnio-środkowe stany USA
- Zachodnie stany USA
- Zachodnie stany USA 2
- Południowo-środkowe stany USA
- Wschodnie stany USA
- Wschodnie stany USA 2
- Środkowe stany USA
- Północno-środkowe stany USA
- Kanada Środkowa
- Południowe Zjednoczone Królestwo
- Europa Północna
- Europa Zachodnia
- Azja Wschodnia
- Azja Południowo-Wschodnia
- Indie Środkowe
- Japonia Wschodnia
- Australia Wschodnia
- Australia Południowo-Wschodnia

>[!NOTE]
>Maszyny wirtualne platformy Azure można monitorować w dowolnym regionie. Same maszyny wirtualne nie są ograniczone do regionów obsługiwanych przez obszar roboczy usługi Log Analytics.
>

Jeśli nie masz obszaru roboczego usługi Log Analytics, możesz go utworzyć przy użyciu jednego z zasobów:
* [Interfejs wiersza polecenia platformy Azure](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)
* [Portal platformy Azure](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)

Można również utworzyć obszar roboczy podczas włączania monitorowania dla pojedynczej maszyny wirtualnej platformy Azure lub skalowania maszyny wirtualnej ustawionej w witrynie Azure portal.

Aby skonfigurować scenariusz na dużą skalę, który używa szablonów usługi Azure Policy, Azure PowerShell lub Azure Resource Manager, w obszarze roboczym usługi Log Analytics:

* Zainstaluj rozwiązania *ServiceMap* i *InfrastructureInsights.* Tę instalację można ukończyć przy użyciu dostarczonego szablonu usługi Azure Resource Manager. Na karcie **Wprowadzenie** w portalu Azure wybierz pozycję **Konfiguruj obszar roboczy**.
* Skonfiguruj obszar roboczy usługi Log Analytics do zbierania liczników wydajności.

Aby skonfigurować obszar roboczy dla scenariusza w skali, należy użyć jednej z następujących metod:

* Użyj [programu Azure PowerShell](vminsights-enable-at-scale-powershell.md#set-up-a-log-analytics-workspace).
* Na stronie [**Zasady**](vminsights-enable-at-scale-policy.md#manage-policy-coverage-feature-overview) usługi Azure Monitor dla maszyn wirtualnych wybierz pozycję **Konfiguruj obszar roboczy**. 

### <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

W poniższej tabeli wymieniono systemy operacyjne Windows i Linux, które obsługuje usługa Azure Monitor dla maszyn wirtualnych. W dalszej części tej sekcji znajdziesz pełną listę szczegółów głównych i mniejszych wersji systemu operacyjnego Linux i obsługiwanych wersji jądra.

|Wersja systemu operacyjnego |Wydajność |Maps |
|-----------|------------|-----|
|Windows Server 2019 | X | X |
|Windows Server 2016 1803 | X | X |
|Windows Server 2016 | X | X |
|Windows Server 2012 R2 | X | X |
|Windows Server 2012 | X | X |
|Windows Server 2008 R2 | X | X|
|Windows 10 1803 | X | X |
|Windows 8.1 | X | X |
|Windows 8 | X | X |
|Windows 7 z dodatkiem SP1 | X | X |
|Red Hat Enterprise Linux (RHEL) 6, 7| X | X| 
|Ubuntu 18.04, 16.04 | X | X |
|CentOS Linux 7, 6 | X | X |
|SUSE Linux Enterprise Server (SLES) 12 | X | X |
|Debian 9.4, 8 | X<sup>1</sup> | |

<sup>1</sup> Funkcja wydajności usługi Azure Monitor dla maszyn wirtualnych jest dostępna tylko w usłudze Azure Monitor. Nie jest dostępna bezpośrednio z lewego okienka maszyny Wirtualnej platformy Azure.

>[!NOTE]
>W systemie operacyjnym Linux:
> - Obsługiwane są tylko wersje domyślne i wersje SMP jądra systemu Linux.
> - Niestandardowe wersje jądra, takie jak rozszerzenie adresu fizycznego (PAE) i Xen, nie są obsługiwane dla żadnej dystrybucji linuksa. Na przykład system z ciągiem zwalniania *2.6.16.21-0.8-xen* nie jest obsługiwany.
> - Niestandardowe jądra, w tym ponowne kompilacje standardowych jąder, nie są obsługiwane.
> - Obsługiwane jest jądro CentOSPlus.
> - Jądro Linuksa musi zostać poprawione w celu wyeliminowania luk w zabezpieczeniach Spectre. Aby uzyskać więcej informacji, skontaktuj się z dostawcą dystrybucji linuksu.

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Wersja systemu operacyjnego | Wersja jądra |
|:--|:--|
| 7.6 | 3.10.0-957 |
| 7,5 | 3.10.0-862 |
| 7.4 | 3.10.0-693 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Wersja systemu operacyjnego | Wersja jądra |
|:--|:--|
| 6.10 | 2.6.32-754 |
| 6.9 | 2.6.32-696 |

#### <a name="centosplus"></a>CentOSPlus ( CentOSPlus )

| Wersja systemu operacyjnego | Wersja jądra |
|:--|:--|
| 6.10 | 2.6.32-754.3.5<br>2.6.32-696.30.1 |
| 6.9 | 2.6.32-696.30.1<br>2.6.32-696.18.7 |

#### <a name="ubuntu-server"></a>Ubuntu Server

| Wersja systemu operacyjnego | Wersja jądra |
|:--|:--|
| 18.04 | 5.0 (obejmuje jądro dostrojone na platformie Azure)<br>4.18*<br>4.15* |
| 16.04.3 | 4.15.* |
| 16.04 | 4.13.\*<br>4.11.\*<br>4.10.\*<br>4.8.\*<br>4.4.\* |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Serwer dla przedsiębiorstw

| Wersja systemu operacyjnego | Wersja jądra |
|:--|:--|
|12 SP4 | 4.12.* (obejmuje jądro dostrojone na platformie Azure) |
|12 SP3 | 4.4.* |
|12 SP2 | 4.4.* |

#### <a name="debian"></a>Debian 

| Wersja systemu operacyjnego | Wersja jądra |
|:--|:--|
| 9 | 4.9 | 

### <a name="the-microsoft-dependency-agent"></a>Agent zależności firmy Microsoft

Funkcja Mapa w usłudze Azure Monitor dla maszyn wirtualnych pobiera swoje dane od agenta zależności firmy Microsoft. Agent zależności opiera się na agenta usługi Log Analytics dla jego połączenia z usługą Log Analytics. Dlatego system musi mieć agenta usługi Log Analytics zainstalowany i skonfigurowany z agentem zależności.

Niezależnie od tego, czy włączysz usługę Azure Monitor dla maszyn wirtualnych dla pojedynczej maszyny wirtualnej platformy Azure, czy używasz metody wdrażania na dużą skalę, użyj rozszerzenia agenta zależności maszyny Wirtualnej platformy Azure dla [systemu Windows](../../virtual-machines/extensions/agent-dependency-windows.md) lub [Linux,](../../virtual-machines/extensions/agent-dependency-linux.md) aby zainstalować agenta w ramach środowiska.

>[!NOTE]
>Poniższe informacje opisane w tej sekcji mają również zastosowanie do [rozwiązania mapy usług.](service-map.md)  

W środowisku hybrydowym można pobrać i zainstalować agenta zależności ręcznie lub przy użyciu metody zautomatyzowanej.

W poniższej tabeli opisano połączone źródła, które funkcja Mapa obsługuje w środowisku hybrydowym.

| Połączone źródło | Obsługiwane | Opis |
|:--|:--|:--|
| Agenci dla systemu Windows | Tak | Wraz z [agentem analizy dzienników dla systemu Windows](../../azure-monitor/platform/log-analytics-agent.md)agenci systemu Windows potrzebują agenta zależności. Aby uzyskać więcej informacji, zobacz [obsługiwane systemy operacyjne](#supported-operating-systems). |
| Agenci dla systemu Linux | Tak | Wraz z [agentem analizy dziennika dla systemu Linux,](../../azure-monitor/platform/log-analytics-agent.md)agenci Systemu Linux potrzebują agenta zależności. Aby uzyskać więcej informacji, zobacz [obsługiwane systemy operacyjne](#supported-operating-systems). |
| Grupa zarządzania programu System Center Operations Manager | Nie | |

Agent zależności można pobrać z następujących lokalizacji:

| Plik | System operacyjny | Wersja | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.10.3.9380 | 40763BD0A5B60707DF3F9E7BCC17D917F5CE995F2F5A4633D8B733F3BE143921  |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.10.3.9380 | BB41BB59BDD293968F02A9EF821F9639406AA1BDF1F67925DB9EE00D54AA7F0B |

## <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach

Aby włączyć i uzyskać dostęp do funkcji w usłudze Azure Monitor dla maszyn wirtualnych, musisz mieć rolę *współautora usługi Log Analytics.* Aby wyświetlić dane dotyczące wydajności, kondycji i mapy, musisz mieć rolę *czytnika monitorowania* dla maszyny Wirtualnej platformy Azure. Obszar roboczy usługi Log Analytics musi być skonfigurowany dla usługi Azure Monitor dla maszyn wirtualnych.

Aby uzyskać więcej informacji na temat kontrolowania dostępu do obszaru roboczego usługi Log Analytics, zobacz [Zarządzanie obszarami roboczymi](../../azure-monitor/platform/manage-access.md).

## <a name="how-to-enable-azure-monitor-for-vms"></a>Jak włączyć usługę Azure Monitor dla maszyn wirtualnych

Włącz usługę Azure Monitor dla maszyn wirtualnych przy użyciu jednej z metod opisanych w tej tabeli:

| Stan wdrożenia | Metoda | Opis |
|------------------|--------|-------------|
| Zestaw skalowania pojedynczej maszyny wirtualnej platformy Azure lub maszyny wirtualnej | [Włącz z maszyny Wirtualnej](vminsights-enable-single-vm.md) | Można włączyć jedną maszynę wirtualną platformy Azure, wybierając **usługę Insights** bezpośrednio z zestawu skalowania maszyny wirtualnej lub maszyny wirtualnej. |
| Wiele maszyn wirtualnych platformy Azure lub zestawów skalowania maszyn wirtualnych | [Włącz za pomocą zasad platformy Azure](vminsights-enable-at-scale-policy.md) | Można włączyć wiele maszyn wirtualnych platformy Azure przy użyciu zasad platformy Azure i dostępnych definicji zasad. |
| Wiele maszyn wirtualnych platformy Azure lub zestawów skalowania maszyn wirtualnych | [Włączanie za pomocą szablonów usługi Azure PowerShell lub usługi Azure Resource Manager](vminsights-enable-at-scale-powershell.md) | Można włączyć wiele maszyn wirtualnych platformy Azure lub zestawów skalowania maszyny wirtualnej w określonej subskrypcji lub grupie zasobów przy użyciu szablonów programu Azure PowerShell lub usługi Azure Resource Manager. |
| Chmura hybrydowa | [Włącz środowisko hybrydowe](vminsights-enable-hybrid-cloud.md) | Można wdrożyć na maszynach wirtualnych lub komputerach fizycznych, które są hostowane w centrum danych lub innych środowiskach w chmurze. |

## <a name="management-packs"></a>Pakiety administracyjne

Gdy usługa Azure Monitor dla maszyn wirtualnych jest włączona i skonfigurowana za pomocą obszaru roboczego usługi Log Analytics, pakiet administracyjny jest przekazywał dalej do wszystkich komputerów z systemem Windows raportujących do tego obszaru roboczego. Jeśli [grupa zarządzania programu System Center Operations Manager](../../azure-monitor/platform/om-agents.md) została zintegrowana z obszarem roboczym usługi Log Analytics, pakiet administracyjny mapy usług jest wdrażany z grupy zarządzania na komputerach z systemem Windows, które zgłaszają się do grupy zarządzania.  

Pakiet administracyjny nosi nazwę *Microsoft.IntelligencePacks.ApplicationDependencyMonitor*. Jego napisane `%Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs\` do folderu. Źródłem danych używanym przez `%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll`pakiet administracyjny jest .

## <a name="diagnostic-and-usage-data"></a>Dane diagnostyczne i dotyczące użytkowania

Firma Microsoft automatycznie zbiera dane dotyczące użycia i wydajności za pośrednictwem usługi Azure Monitor. Firma Microsoft używa tych danych w celu poprawy jakości, zabezpieczeń i integralności usługi. 

Aby zapewnić dokładne i wydajne możliwości rozwiązywania problemów, funkcja Mapa zawiera dane dotyczące konfiguracji oprogramowania. Dane zawierają informacje, takie jak system operacyjny i wersja, adres IP, nazwa DNS i nazwa stacji roboczej. Firma Microsoft nie gromadzi nazw, adresów ani innych informacji kontaktowych.

Aby uzyskać więcej informacji na temat gromadzenia i używania danych, zobacz [Zasady zachowania poufności informacji dotyczące usług online firmy Microsoft](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

Teraz, gdy włączono monitorowanie maszyny Wirtualnej, informacje o monitorowaniu są dostępne do analizy w usłudze Azure Monitor dla maszyn wirtualnych.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak korzystać z funkcji monitorowania wydajności, zobacz [Wyświetlanie usługi Azure Monitor dla wydajności maszyn wirtualnych](vminsights-performance.md). Aby wyświetlić odnalezione zależności aplikacji, zobacz [Wyświetlanie usługi Azure Monitor dla map maszyn wirtualnych.](vminsights-maps.md)
