---
title: Włączanie usługi Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza) — omówienie | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak wdrożyć i skonfigurować usługi Azure Monitor dla maszyn wirtualnych i wymagania systemowe wymagane.
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
ms.date: 05/22/2019
ms.author: magoedte
ms.openlocfilehash: c84c3eb74d214a5c98aabef7b2e2987dfdf67c0f
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66472607"
---
# <a name="enable-azure-monitor-for-vms-preview-overview"></a>Włączanie usługi Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza) — omówienie

Ten artykuł zawiera omówienie dostępne opcje Aby skonfigurować usługi Azure Monitor dla maszyn wirtualnych na potrzeby monitorowania kondycji, wydajności i odnajdywania zależności aplikacji działających na maszynach wirtualnych platformy Azure i zestawów skalowania maszyn wirtualnych, maszyny wirtualne w lokalnej lub maszyny wirtualne hostowane w innym środowisku chmury.  

Należy włączyć usługi Azure Monitor dla maszyn wirtualnych przy użyciu jednej z następujących metod:

* Włącz pojedynczej maszyny wirtualnej platformy Azure lub skalowania maszyn wirtualnych ustawiona przez wybranie **Insights (wersja zapoznawcza)** bezpośrednio od maszyny Wirtualnej lub maszyny wirtualnej zestawu skalowania.
* Włączyć dwie lub więcej maszyn wirtualnych platformy Azure i virtual machine scale sets przy użyciu usługi Azure Policy. Za pomocą tej metody wymagane zależności istniejących i nowych maszyn wirtualnych i zestawów skalowania są zainstalowane i poprawnie skonfigurowane. Niezgodne maszyny wirtualne i zestawy skalowania są zgłaszane, dzięki czemu możesz zdecydować, czy chcesz je włączyć, a jaki chcesz je skorygować.
* Włączyć dwie lub więcej maszyn wirtualnych platformy Azure lub na maszynie wirtualnej skalować zestawy skalowania w określonej subskrypcji lub grupy zasobów przy użyciu programu PowerShell.
* Włącz monitorowanie maszyn wirtualnych lub fizycznych komputerów hostowanych w sieci firmowej lub w innym środowisku chmury.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że rozumiesz, informacje w poniższych sekcjach.

### <a name="log-analytics"></a>Log Analytics

Usługa Azure Monitor dla maszyn wirtualnych obsługuje obszar roboczy usługi Log Analytics w następujących regionach:

- Środkowo-zachodnie stany USA
- Zachodnie stany USA 2<sup>1</sup>
- Wschodnie stany USA
- Canada Central<sup>1</sup>
- UK South<sup>1</sup>
- Europa Zachodnia
- Azja południowo-wschodnia<sup>1</sup>

<sup>1</sup> ten region nie obsługuje obecnie funkcję kondycji usługi Azure monitor maszyn wirtualnych.

>[!NOTE]
>Usługa Azure virtual machines można wdrażać z dowolnego regionu i nie są ograniczone do określonych regionów obsługiwanych dla obszaru roboczego usługi Log Analytics.
>

Jeśli nie masz obszaru roboczego, można utworzyć jeden z jedną z następujących metod:
* [Interfejs wiersza polecenia platformy Azure](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [Program PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)
* [Witryna Azure Portal](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)

Jeśli w przypadku włączenia monitorowania dla pojedynczej maszyny Wirtualnej platformy Azure lub zestawu skalowania maszyn wirtualnych w witrynie Azure portal, można utworzyć obszar roboczy, w trakcie tego procesu.

W scenariuszu na dużą skalę za pomocą usługi Azure Policy, programu Azure PowerShell lub usługi Azure Resource Manager obszaru roboczego usługi Log Analytics wymaga najpierw skonfigurowanych następujących opcji:

* Zainstaluj ServiceMap i InfrastructureInsights rozwiązań. Tę instalację można wykonać przy użyciu szablonu usługi Azure Resource Manager, który znajduje się lub za pomocą **skonfigurować obszar roboczy** opcji znalezione na **wprowadzenie** kartę.
* Konfiguruj obszar roboczy usługi Log Analytics, można zebrać liczników wydajności.

Aby skonfigurować obszar roboczy dla scenariusza na dużą skalę, można skonfigurować za pomocą jednego z następujących metod:

* [Azure PowerShell](vminsights-enable-at-scale-powershell.md#set-up-a-log-analytics-workspace)
* Z **skonfigurować obszar roboczy** dotycząca maszyn wirtualnych w usłudze Azure Monitor [pokrycia zasad](vminsights-enable-at-scale-policy.md#manage-policy-coverage-feature-overview) strony

### <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

W poniższej tabeli wymieniono systemy operacyjne Windows i Linux, które są obsługiwane z usługą Azure Monitor dla maszyn wirtualnych. Pełną listę, która szczegóły wersji systemu operacyjnego Linux głównych i pomocniczych i obsługiwane wersje jądra później znajduje się w tej sekcji.

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
|SUSE Linux Enterprise Server (SLES) 11, 12 | X | X | X |
|Debian 8, 9.4 | X<sup>1</sup> | | X |

<sup>1</sup> funkcja wydajności usługi Azure Monitor dla maszyn wirtualnych jest dostępna tylko z usługi Azure Monitor. Nie jest dostępna, gdy zostanie do niego dostęp bezpośrednio z poziomu okienka po lewej stronie maszyny Wirtualnej platformy Azure.

>[!NOTE]
>Poniższe informacje dotyczą do obsługi systemów operacyjnych Linux:
> - Obsługiwane są tylko wersje domyślne i wersje SMP jądra systemu Linux.
> - Zwalnia jądra niestandardowe, takie jak rozszerzenia adresu fizycznego (PAE) i Xen, nie są obsługiwane w przypadku dowolnej dystrybucji systemu Linux. Na przykład system z ciągu wersji *2.6.16.21-0.8-xen* nie jest obsługiwane.
> - Niestandardowe jądra, tym rekompilacji standardowa jądra nie są obsługiwane.
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

### <a name="centosplus"></a>CentOSPlus
| Wersja systemu operacyjnego | Wersja jądra |
|:--|:--|
| 6.9 | 2.6.32-696.18.7<br>2.6.32-696.30.1 |
| 6.10 | 2.6.32-696.30.1<br>2.6.32-754.3.5 |

#### <a name="ubuntu-server"></a>Ubuntu Server

| Wersja systemu operacyjnego | Wersja jądra |
|:--|:--|
| Ubuntu 18.04 | jądra 4.15. * |
| Systemu Ubuntu 16.04.3 | jądra 4.15. * |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

#### <a name="suse-linux-11-enterprise-server"></a>SUSE Linux 11 Enterprise Server

| Wersja systemu operacyjnego | Wersja jądra
|:--|:--|
|11 SP4 | 3.0.* |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| Wersja systemu operacyjnego | Wersja jądra
|:--|:--|
|12 Z DODATKIEM SP2 | 4.4. * |
|12 Z DODATKIEM SP3 | 4.4. * |

### <a name="the-microsoft-dependency-agent"></a>Program Microsoft Dependency agent

Usługi Azure Monitor dla funkcji mapy maszyny wirtualne, dane są pobierane z Microsoft Dependency agent. Agent zależności zależy od agenta usługi Log Analytics do połączenia z usługi Log Analytics. W związku z tym system musi mieć agenta usługi Log Analytics, zainstalować i skonfigurować za pomocą agenta zależności.

Czy włączyć usługi Azure Monitor dla maszyn wirtualnych z jednej maszyny wirtualnej platformy Azure lub użyć metody wdrożenia na dużą skalę, należy zainstalować agenta w ramach środowiska za pomocą rozszerzenia agenta zależności maszyn wirtualnych platformy Azure.

W środowisku hybrydowym, można pobrać i zainstalować agenta zależności na dwa sposoby: ręcznie lub za pomocą metody wdrażania automatycznego, dla maszyn wirtualnych, które są hostowane spoza platformy Azure.

W poniższej tabeli opisano połączone źródła obsługiwanych przez funkcję mapy w środowisku hybrydowym.

| Połączone źródło | Obsługiwane | Opis |
|:--|:--|:--|
| Agenci dla systemu Windows | Yes | Oprócz [agenta usługi Log Analytics dla Windows](../../azure-monitor/platform/log-analytics-agent.md), agenci Windows wymagają Microsoft Dependency agent. Aby uzyskać pełną listę wersji systemów operacyjnych, zobacz [obsługiwane systemy operacyjne](#supported-operating-systems). |
| Agenci dla systemu Linux | Yes | Oprócz [agenta usługi Log Analytics dla systemu Linux](../../azure-monitor/platform/log-analytics-agent.md), agenci dla systemu Linux wymaga program Microsoft Dependency agent. Aby uzyskać pełną listę wersji systemów operacyjnych, zobacz [obsługiwane systemy operacyjne](#supported-operating-systems). |
| Grupa zarządzania programu System Center Operations Manager | Nie | |

Agent zależności można pobrać z następujących lokalizacji:

| Plik | System operacyjny | Wersja | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.8.1 | 622C99924385CBF539988D759BCFDC9146BB157E7D577C997CDD2674E27E08DD |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.8.1 | 3037934A5D3FB7911D5840A9744AE9F980F87F620A7F7B407F05E276FE7AE4A8 |

## <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach

Aby włączyć i funkcje w usłudze Azure Monitor są dostępne dla maszyn wirtualnych, musisz mieć przypisane następujące role dostępu:

- Aby ją włączyć, konieczne jest posiadanie *Współautor usługi Log Analytics* roli.

- Aby wyświetlić kondycję, wydajność i mapowanie danych, konieczne jest posiadanie *Czytelnik monitorowania* roli maszyny wirtualnej platformy Azure. Obszar roboczy usługi Log Analytics, musi być skonfigurowany dla usługi Azure Monitor dla maszyn wirtualnych.

Aby uzyskać więcej informacji na temat kontrolowania dostępu do obszaru roboczego usługi Log Analytics, zobacz [możesz zarządzać obszarami roboczymi](../../azure-monitor/platform/manage-access.md).

## <a name="how-to-enable-azure-monitor-for-vms-preview"></a>Jak włączyć usługi Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza)

Włączanie usługi Azure Monitor dla maszyn wirtualnych odbywa się przy użyciu jednej z następujących metod opisanych w poniższej tabeli.

| Stan wdrożenia | Metoda | Opis |
|------------------|--------|-------------|
| Jeden zestaw skalowania maszyn wirtualnych platformy Azure lub na maszynie wirtualnej | [Bezpośrednio z poziomu maszyny Wirtualnej](vminsights-enable-single-vm.md) | Pojedynczej maszyny wirtualnej platformy Azure można włączyć, zaznaczając **Insights (wersja zapoznawcza)** bezpośrednio od maszyny Wirtualnej lub maszyny wirtualnej zestawu skalowania. |
| Wiele maszyn wirtualnych platformy Azure lub zestawy skalowania maszyn wirtualnych | [Azure Policy](vminsights-enable-at-scale-policy.md) | Aby umożliwić wielu maszyn wirtualnych platformy Azure przy użyciu usługi Azure Policy i dostępne definicje zasad. |
| Wiele maszyn wirtualnych platformy Azure lub zestawy skalowania maszyn wirtualnych | [Szablony usługi Azure PowerShell lub usługi Azure Resource Manager](vminsights-enable-at-scale-powershell.md) | Wiele zestawów skalowania maszyn wirtualnych platformy Azure lub na maszynie wirtualnej można włączyć w określonej subskrypcji lub grupy zasobów za pomocą szablonów programu Azure PowerShell lub usługi Azure Resource Manager. |
| Chmura hybrydowa | [Włącz dla środowiska hybrydowego](vminsights-enable-hybrid-cloud.md) | Można wdrożyć do maszyn wirtualnych lub fizycznych komputerów, które są hostowane w centrum danych lub innych środowisk w chmurze. |

## <a name="performance-counters-enabled"></a>Liczniki wydajności włączone

Usługa Azure Monitor dla maszyn wirtualnych służy do konfigurowania obszaru roboczego usługi Log Analytics można zebrać liczników wydajności, których używa. Poniższa tabela zawiera listę obiektów i liczników, które są gromadzone co 60 sekund.

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

Firma Microsoft automatycznie zbiera dane dotyczące użycia i wydajności przez korzystanie z usługi Azure Monitor. Firma Microsoft używa tych danych do zapewniania i poprawiania jakości, bezpieczeństwa i integralności usługi. Aby zapewnić dokładne i wydajne funkcje do rozwiązywania problemów, dane z funkcji mapy zawiera informacje o konfiguracji oprogramowania, takie jak system operacyjny i wersję, adres IP, nazwę DNS i nazwę stacji roboczej. Firma Microsoft nie zbiera nazwisk, adresów ani innych informacji kontaktowych.

Aby uzyskać więcej informacji na temat zbierania i wykorzystywania danych, zobacz [Microsoft Online Services Privacy Statement](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy dla maszyny wirtualnej jest włączone monitorowanie, te informacje są dostępne dla analizy dzięki usłudze Azure Monitor dla maszyn wirtualnych. Aby dowiedzieć się, jak korzystać z funkcji Health, zobacz [widok usługi Azure Monitor kondycji maszyn wirtualnych](vminsights-health.md). Aby obejrzeć zależności odnalezionych aplikacji, zobacz [widok usługi Azure Monitor dla maszyn wirtualnych mapy](vminsights-maps.md).
