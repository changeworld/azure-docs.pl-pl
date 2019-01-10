---
title: Wdrażanie usługi Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak wdrożyć i skonfigurować usługi Azure Monitor dla maszyn wirtualnych, aby można było zacząć, zrozumienie, jak działa aplikacja rozproszona i problemy z kondycją, które zostały zidentyfikowane.
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
ms.date: 12/07/2018
ms.author: magoedte
ms.openlocfilehash: 3c1caa2485437768781ada2c7271445ccd3c19e1
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2019
ms.locfileid: "54190332"
---
# <a name="deploy-azure-monitor-for-vms-preview"></a>Wdrażanie usługi Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza)
W tym artykule opisano sposób konfigurowania usługi Azure Monitor dla maszyn wirtualnych. Monitoruje kondycję systemu operacyjnego usługi Azure virtual machines (VMs) i zestawy skalowania maszyn wirtualnych i maszyn wirtualnych w danym środowisku. Monitorowanie obejmuje odnajdywania oraz mapowania zależności aplikacji, które mogą być hostowane na nich. 

Należy włączyć usługi Azure Monitor dla maszyn wirtualnych przy użyciu jednej z następujących metod:

* Włącz pojedynczej maszyny wirtualnej platformy Azure, wybierając **Insights (wersja zapoznawcza)** bezpośrednio z poziomu maszyny Wirtualnej.
* Włącz co najmniej dwóch maszyn wirtualnych platformy Azure za pomocą usługi Azure Policy. Za pomocą tej metody wymagane zależności istniejących i nowych maszyn wirtualnych są zainstalowane i poprawnie skonfigurowane. Niezgodne maszyny wirtualne są zgłaszane, dzięki czemu możesz zdecydować, czy chcesz je włączyć, a jaki chcesz je skorygować.
* Włączyć dwie lub więcej maszyn wirtualnych platformy Azure lub na maszynie wirtualnej skalować zestawy skalowania w określonej subskrypcji lub grupy zasobów przy użyciu programu PowerShell.

Dodatkowe informacje na temat poszczególnych metod znajduje się w dalszej części tego artykułu.

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem upewnij się, że rozumiesz, informacje w poniższych sekcjach.

### <a name="log-analytics"></a>Log Analytics

Obszar roboczy usługi Log Analytics jest obecnie obsługiwane w następujących regionach:

- Środkowo-zachodnie stany USA
- Wschodnie stany USA
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

Jeśli w przypadku włączenia monitorowania dla pojedynczej maszyny Wirtualnej platformy Azure w witrynie Azure portal, można utworzyć obszar roboczy, w trakcie tego procesu.

Aby włączyć rozwiązanie dla scenariusza na dużą skalę, należy najpierw skonfigurować następujące w obszarze roboczym usługi Log Analytics:

* Zainstaluj ServiceMap i InfrastructureInsights rozwiązań. Tylko przy użyciu szablonu usługi Azure Resource Manager, który znajduje się w tym artykule można wykonać tej instalacji.
* Konfiguruj obszar roboczy usługi Log Analytics, można zebrać liczników wydajności.

Aby skonfigurować obszar roboczy dla scenariusza na dużą skalę, zobacz [techniczną skonfigurował obszar roboczy usługi Log Analytics, do wdrożenia na dużą skalę](#setup-log-analytics-workspace).

### <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

W poniższej tabeli wymieniono systemy operacyjne Windows i Linux, które są obsługiwane z usługą Azure Monitor dla maszyn wirtualnych. Pełną listę, która szczegóły wersji systemu operacyjnego Linux głównych i pomocniczych i obsługiwane wersje jądra później znajduje się w tej sekcji.

|Wersja systemu operacyjnego |Wydajność |Maps |Health |
|-----------|------------|-----|-------|
|System Windows Server 2016 1803 | X | X | X |
|Windows Server 2016 | X | X | X |
|Windows Server 2012 R2 | X | X | |
|Windows Server 2012 | X | X | |
|Windows Server 2008 R2 | X | X| |
|Red Hat Enterprise Linux (RHEL) 7, 6| X | X| X |
|Naciśnięcie i przytrzymanie 18.04 Ubuntu 16.04, 14.04 | X | X | X |
|CentOS Linux 7, 6 | X | X | X |
|SUSE Linux Enterprise Server (SLES) 12 | X | X | X |
|Oracle Linux 7 | X<sup>1</sup> | | X |
|Oracle Linux 6 | X | X | X |
|Debian 9.4, 8 | X<sup>1</sup> | | X |

<sup>1</sup> funkcja wydajności usługi Azure Monitor dla maszyn wirtualnych jest dostępna tylko z usługi Azure Monitor. Nie jest dostępna, gdy zostanie do niego dostęp bezpośrednio z poziomu okienka po lewej stronie maszyny Wirtualnej platformy Azure.

>[!NOTE]
>Poniższe informacje dotyczą do obsługi systemów operacyjnych Linux:
> - Obsługiwane są tylko wersje domyślne i wersje SMP jądra systemu Linux.
> - Zwalnia jądra niestandardowe, takie jak rozszerzenia adresu fizycznego (PAE) i Xen, nie są obsługiwane w przypadku dowolnej dystrybucji systemu Linux. Na przykład system z ciągu wersji *2.6.16.21-0.8-xen* nie jest obsługiwane.
> - Niestandardowe jądra, tym rekompilacji standardowa jądra nie są obsługiwane.
> - CentOSPlus jądra nie jest obsługiwane.

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

#### <a name="ubuntu-server"></a>Ubuntu Server

| Wersja systemu operacyjnego | Wersja jądra |
|:--|:--|
| Ubuntu 18.04 | jądra 4.15. * |
| Systemu Ubuntu 16.04.3 | jądra 4.15. * |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

#### <a name="oracle-enterprise-linux-6-with-unbreakable-enterprise-kernel"></a>Oracle Enterprise Linux 6 z podzielenie Enterprise jądra
| Wersja systemu operacyjnego | Wersja jądra
|:--|:--|
| 6.2 | Oracle 2.6.32-300 (UEK R1) |
| 6.3 | Oracle 2.6.39-200 (UEK R2) |
| 6.4 | Oracle 2.6.39-400 (UEK R2) |
| 6.5 | Oracle 2.6.39-400 (UEK R2 i386) |
| 6.6 | Oracle 2.6.39-400 (UEK R2 i386) |

#### <a name="oracle-enterprise-linux-5-with-unbreakable-enterprise-kernel"></a>Oracle Enterprise Linux 5 za pomocą podzielenie Enterprise jądra

| Wersja systemu operacyjnego | Wersja jądra
|:--|:--|
| 5.10 | Oracle 2.6.39-400 (UEK R2) |
| 5.11 | Oracle 2.6.39-400 (UEK R2) |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| Wersja systemu operacyjnego | Wersja jądra
|:--|:--|
|12 Z DODATKIEM SP2 | 4.4. * |
|12 Z DODATKIEM SP3 | 4.4. * |

### <a name="the-microsoft-dependency-agent"></a>Program Microsoft Dependency agent
Usługi Azure Monitor dla funkcji mapy maszyny wirtualne, dane są pobierane z Microsoft Dependency agent. Agent zależności zależy od agenta usługi Log Analytics do połączenia z usługi Log Analytics. W związku z tym system musi mieć agenta usługi Log Analytics, zainstalować i skonfigurować za pomocą agenta zależności.

Czy włączyć usługi Azure Monitor dla maszyn wirtualnych z jednej maszyny wirtualnej platformy Azure lub użyć metody wdrożenia na dużą skalę, należy zainstalować agenta w ramach środowiska za pomocą rozszerzenia agenta zależności maszyn wirtualnych platformy Azure.

W środowisku hybrydowym można pobrać i zainstalować agenta zależności na dwa sposoby: Ręcznie lub za pomocą metody automatycznego wdrażania dla maszyn wirtualnych, znajdują się poza systemem Azure.

W poniższej tabeli opisano połączone źródła obsługiwanych przez funkcję mapy w środowisku hybrydowym.

| Połączone źródło | Obsługiwane | Opis |
|:--|:--|:--|
| Agenci dla systemu Windows | Yes | Oprócz [agenta usługi Log Analytics dla Windows](../../azure-monitor/platform/log-analytics-agent.md), agenci Windows wymagają Microsoft Dependency agent. Aby uzyskać pełną listę wersji systemów operacyjnych, zobacz [obsługiwane systemy operacyjne](#supported-operating-systems). |
| Agenci dla systemu Linux | Yes | Oprócz [agenta usługi Log Analytics dla systemu Linux](../../azure-monitor/platform/log-analytics-agent.md), agenci dla systemu Linux wymaga program Microsoft Dependency agent. Aby uzyskać pełną listę wersji systemów operacyjnych, zobacz [obsługiwane systemy operacyjne](#supported-operating-systems). |
| Grupa zarządzania programu System Center Operations Manager | Nie | |

Agent zależności można pobrać z następujących lokalizacji:

| Plik | System operacyjny | Wersja | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.7.4 | A111B92AB6CF28EB68B696C60FE51F980BFDFF78C36A900575E17083972989E0 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.7.4 | AB58F3DB8B1C3DEE7512690E5A65F1DFC41B43831543B5C040FCCE8390F2282C |

## <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach
Aby włączyć i funkcje w usłudze Azure Monitor są dostępne dla maszyn wirtualnych, musisz mieć przypisane następujące role dostępu:

- Aby włączyć rozwiązanie, konieczne jest posiadanie *Współautor usługi Log Analytics* roli.

- Aby wyświetlić kondycję, wydajność i mapowanie danych, konieczne jest posiadanie *Czytelnik monitorowania* roli maszyny wirtualnej platformy Azure. Obszar roboczy usługi Log Analytics, musi być skonfigurowany dla usługi Azure Monitor dla maszyn wirtualnych.

Aby uzyskać więcej informacji na temat kontrolowania dostępu do obszaru roboczego usługi Log Analytics, zobacz [możesz zarządzać obszarami roboczymi](../../azure-monitor/platform/manage-access.md).

## <a name="enable-monitoring-in-the-azure-portal"></a>Aby włączyć monitorowanie w witrynie Azure portal
Aby włączyć monitorowanie maszyny wirtualnej platformy Azure w witrynie Azure portal, wykonaj następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Wybierz **maszyn wirtualnych**.

1. Z listy wybierz maszynę wirtualną.

1. Na stronie maszyny Wirtualnej w **monitorowanie** zaznacz **Insights (wersja zapoznawcza)**.

1. Na **Insights (wersja zapoznawcza)** wybierz opcję **Wypróbuj teraz usługę**.

    ![Włączanie usługi Azure Monitor dla maszyn wirtualnych dla maszyny Wirtualnej](./media/vminsights-onboard/enable-vminsights-vm-portal-01.png)
1. Na **Azure Monitor Insights dołączania** strony, jeśli masz istniejące usługi Log Analytics obszaru roboczego w tej samej subskrypcji, wybierz go na liście rozwijanej.  
    Lista preselects domyślnego obszaru roboczego i lokalizacji, która została wdrożona maszyna wirtualna, aby w ramach subskrypcji. 

    >[!NOTE]
    >Jeśli chcesz utworzyć nowy obszar roboczy usługi Log Analytics do przechowywania danych monitorowania z maszyny Wirtualnej, postępuj zgodnie z instrukcjami [Utwórz obszar roboczy usługi Log Analytics](../../azure-monitor/learn/quick-create-workspace.md) w jednym z obsługiwanych regionów wymienionych wcześniej.

Po włączeniu monitorowania może zająć około 10 minut, zanim będzie można wyświetlić metryki kondycji dla maszyny wirtualnej.

![Włączanie usługi Azure Monitor monitorowania przetwarzanie wdrożenia maszyn wirtualnych](./media/vminsights-onboard/onboard-vminsights-vm-portal-status.png)


## <a name="deploy-at-scale"></a>Wdrażanie w skali
W tej sekcji wdrażania usługi Azure Monitor dla maszyn wirtualnych na dużą skalę za pomocą usługi Azure Policy lub programu Azure PowerShell.

Przed przystąpieniem do wdrażania maszyn wirtualnych, należy wstępnie skonfigurować obszar roboczy usługi Log Analytics, wykonując następujące czynności:

1. Jeśli nie masz jeszcze obszaru roboczego, utwórz taki, który może obsługiwać monitorowanie platformy Azure dla maszyn wirtualnych.  
    Przed kontynuowaniem, zobacz [możesz zarządzać obszarami roboczymi](../../log-analytics/log-analytics-manage-access.md?toc=/azure/azure-monitor/toc.json) Aby zrozumieć zagadnienia dotyczące kosztów, zarządzania i zgodności.

1. Utwórz nowy obszar roboczy, jeśli nie już istnieje, może służyć do obsługi usługi Azure Monitor dla maszyn wirtualnych. Przegląd [możesz zarządzać obszarami roboczymi](../../azure-monitor/platform/manage-access.md?toc=/azure/azure-monitor/toc.json) przed utworzeniem nowego obszaru roboczego, aby poznać zagadnienia dotyczące kosztów, zarządzania i zgodności przed kontynuowaniem.

1. Włącz liczniki wydajności w obszarze roboczym dla kolekcji w systemie Linux i Windows maszyn wirtualnych.

1. Instalowanie i włączanie ServiceMap i InfrastructureInsights rozwiązania w Twoim obszarze roboczym.

### <a name="set-up-a-log-analytics-workspace"></a>Skonfiguruj obszar roboczy usługi Log Analytics
Jeśli nie masz obszaru roboczego usługi Log Analytics, utwórz ją, przeglądając metody, które są zalecane w ["Wymagania wstępne"](#log-analytics) sekcji.

#### <a name="enable-performance-counters"></a>Włącz liczniki wydajności
Jeśli obszar roboczy usługi Log Analytics, który odwołuje się do niej rozwiązanie nie jest już skonfigurowany można zebrać liczników wydajności wymaganych przez to rozwiązanie, należy je włączyć. Można to zrobić na dwa sposoby:
* Ręcznie, zgodnie z opisem w [Windows i Linux źródła danych dotyczących wydajności w usłudze Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md)
* Pobierając i uruchamiając skrypt programu PowerShell, który jest dostępny z [galerii programu PowerShell systemu Azure](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

#### <a name="install-the-servicemap-and-infrastructureinsights-solutions"></a>Instalowanie rozwiązania ServiceMap i InfrastructureInsights
Ta metoda obejmuje szablon JSON, który określa konfigurację włączanie składników rozwiązania w Twoim obszarze roboczym usługi Log Analytics.

Jeśli znasz wdrażanie zasobów za pomocą szablonu, zobacz:
* [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)](../../azure-resource-manager/resource-group-template-deploy.md)
* [Wdrażanie zasobów przy użyciu szablonów usługi Resource Manager i interfejsu wiersza polecenia platformy Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Jeśli zdecydujesz się użyć wiersza polecenia platformy Azure, należy najpierw zainstalować i korzystać z interfejsu wiersza polecenia lokalnie. Musi być uruchomiona wiersza polecenia platformy Azure w wersji 2.0.27 lub nowszej. Aby zidentyfikować wersję, uruchom `az --version`. Jeśli musisz zainstalować lub uaktualnić wiersza polecenia platformy Azure, zobacz [zainstalować interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. Skopiuj i wklej następującą składnię JSON do pliku:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "WorkspaceName": {
                "type": "string"
            },
            "WorkspaceLocation": {
                "type": "string"
            }
        },
        "resources": [
            {
                "apiVersion": "2017-03-15-preview",
                "type": "Microsoft.OperationalInsights/workspaces",
                "name": "[parameters('WorkspaceName')]",
                "location": "[parameters('WorkspaceLocation')]",
                "resources": [
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },

                        "plan": {
                            "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'ServiceMap')]",
                            "promotionCode": ""
                        }
                    },
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },
                        "plan": {
                            "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'InfrastructureInsights')]",
                            "promotionCode": ""
                        }
                    }
                ]
            }
        ]
    }
    ```

1. Zapisz ten plik jako *installsolutionsforvminsights.json* do folderu lokalnego.

1. Edytuj wartości *WorkspaceName*, *ResourceGroupName*, i *WorkspaceLocation*. Wartość *WorkspaceName* jest pełny identyfikator zasobu obszaru roboczego usługi Log Analytics, która zawiera nazwę obszaru roboczego. Wartość *WorkspaceLocation* jest region, w obszarze roboczym jest zdefiniowany w.

1. Możesz wdrożyć ten szablon przy użyciu następującego polecenia programu PowerShell:

    ```powershell
    New-AzureRmResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    Zmiana konfiguracji może potrwać kilka minut. Po jej zakończeniu zostanie wyświetlony komunikat podobny do poniższego, która zawiera wynik:

    ```powershell
    provisioningState       : Succeeded
    ```

### <a name="enable-by-using-azure-policy"></a>Włączyć za pomocą usługi Azure Policy
Aby włączyć usługi Azure Monitor dla maszyn wirtualnych na dużą skalę w taki sposób, że pomaga zapewnić zgodność spójne i automatyczne włączanie użytkownicy nowo aprowizowanych maszyn wirtualnych, firma Microsoft zaleca [usługi Azure Policy](../../azure-policy/azure-policy-introduction.md). Te zasady:

* Wdróż agenta usługi Log Analytics i agenta zależności.
* Raport dotyczący wyniki sprawdzania zgodności.
* Korygowanie dla niezgodnych maszyn wirtualnych.

Aby włączyć usługi Azure Monitor dla maszyn wirtualnych przy użyciu usługi Azure Policy w dzierżawie:

- Przypisz inicjatywę do zakresu: grupy zarządzania, subskrypcji lub grupy zasobów
- Przejrzyj i Skoryguj wyniki sprawdzania zgodności

Aby uzyskać więcej informacji na temat przypisywania zasad platformy Azure, zobacz [Omówienie usługi Azure Policy](../../governance/policy/overview.md#policy-assignment) i przejrzyj [Przegląd grup zarządzania](../../governance/management-groups/index.md) przed kontynuowaniem.

W poniższej tabeli przedstawiono definicje zasad:

|Name (Nazwa) |Opis |Typ |
|-----|------------|-----|
|[Wersja zapoznawcza]: Włączanie usługi Azure Monitor dla maszyn wirtualnych |Włączanie usługi Azure Monitor dla maszyn wirtualnych (VM) z określonego zakresu (grupy zarządzania, subskrypcji lub grupy zasobów). Obszar roboczy usługi Log Analytics przyjmuje jako parametr. |Inicjatywa |
|[Wersja zapoznawcza]: Wdrażanie agenta zależności inspekcji — obraz maszyny Wirtualnej (OS) nieznajdujące się na liście |Raporty maszyny wirtualne jako niezgodne, jeśli obraz maszyny Wirtualnej (OS) nie jest zdefiniowana na liście, a nie zainstalowano agenta. |Zasady |
|[Wersja zapoznawcza]: Wdrażanie agentów inspekcji Log Analytics — obrazu maszyny Wirtualnej (OS) nieznajdujące się na liście |Raporty maszyny wirtualne jako niezgodne, jeśli obraz maszyny Wirtualnej (OS) nie jest zdefiniowana na liście, a nie zainstalowano agenta. |Zasady |
|[Wersja zapoznawcza]: Wdrażanie agenta zależności maszyn wirtualnych systemu Linux |Wdrażanie agenta zależności maszyn wirtualnych systemu Linux, jeśli obraz maszyny Wirtualnej (OS) jest zdefiniowany na liście, a nie zainstalowano agenta. |Zasady |
|[Wersja zapoznawcza]: Wdrażanie agenta zależności dla maszyn wirtualnych Windows |Wdróż agenta zależności Windows VMs, jeśli obraz maszyny Wirtualnej (OS) jest zdefiniowana na liście i nie zainstalowano agenta. |Zasady |
|[Wersja zapoznawcza]: Wdrażanie programu Log Analytics Agent na maszynach wirtualnych systemu Linux |Wdrażanie Log Analytics Agent maszyn wirtualnych systemu Linux, jeśli obraz maszyny Wirtualnej (OS) jest zdefiniowany na liście, a nie zainstalowano agenta. |Zasady |
|[Wersja zapoznawcza]: Wdrażanie programu Log Analytics Agent for Windows VMs |Wdróż Log Analytics Agent dla Windows maszyn wirtualnych, jeśli obraz maszyny Wirtualnej (OS) jest zdefiniowana na liście i nie zainstalowano agenta. |Zasady |

Zasady autonomicznym (nie dołączona inicjatywy) opisano tutaj:

|Name (Nazwa) |Opis |Typ |
|-----|------------|-----|
|[Wersja zapoznawcza]: Inspekcja Analytics obszar roboczy usługi Log dla maszyny Wirtualnej — niezgodność raportu |Raportu maszyn wirtualnych jako niezgodne, jeśli nie są one rejestrowanie do obszaru roboczego usługi Log Analytics w przypisaniu zasad/inicjatywy. |Zasady |

#### <a name="assign-the-azure-monitor-initiative"></a>Przypisz inicjatywę usługi Azure Monitor
W tej wersji początkowej przypisania zasad można utworzyć tylko w witrynie Azure portal. Aby dowiedzieć się, jak wykonać następujące czynności, zobacz [Tworzenie przypisania zasad w witrynie Azure portal](../../governance/policy/assign-policy-portal.md).

1. Aby uruchomić usługę Azure Policy w witrynie Azure portal, wybierz **wszystkich usług**, a następnie wyszukaj i wybierz pozycję **zasad**.

1. W okienku po lewej stronie Azure Policy wybierz **przypisania**.  
    Przypisanie to zasady, które zostały przypisane do określonego zakresu.
    
1. W górnej części **zasady — przypisania** wybierz opcję **przypisania inicjatywy**.

1. Na **przypisania inicjatywy** wybierz opcję **zakres** przez kliknięcie przycisku wielokropka (...) i wybierz grupę zarządzania lub subskrypcji.  
    W tym przykładzie zakres ogranicza przypisania zasad w celu zgrupowania maszyn wirtualnych do wymuszania.
    
1. W dolnej części **zakres** strony, Zapisz zmiany, wybierając **wybierz**.

1. (Opcjonalnie) Aby usunąć jeden lub więcej zasobów z zakresu, zaznacz **wykluczenia**.

1. Wybierz **definicja inicjatywy** wybierz wielokropek (...), aby wyświetlić listę dostępnych definicji  **[Wersja zapoznawcza] włączyć usługi Azure Monitor dla maszyn wirtualnych**, a następnie wybierz pozycję  **Wybierz**.  
    **Nazwa przypisania** okno zostanie automatycznie wypełniona inicjatywy wybranej nazwy, ale można go zmienić. Można również dodać opcjonalny opis. **Przypisany przez** pole jest wypełniane automatycznie w oparciu kto jest zalogowany, a ta wartość jest opcjonalna.
    
1. W **obszaru roboczego usługi Log Analytics** listy rozwijanej liście dla obsługiwany region, wybierz obszar roboczy.

    >[!NOTE]
    >Jeśli obszar roboczy znajduje się poza zakresem przypisania, należy udzielić *Współautor usługi Log Analytics* uprawnień, aby identyfikator przypisania zasad podmiotu zabezpieczeń. Jeśli tego nie zrobisz, takich jak może zostać wyświetlony błąd wdrożenia: `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ... ` Aby udzielić dostępu, zapoznaj się z [jak ręcznie skonfigurować tożsamość zarządzaną](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity).
    >  
    **Tożsamości zarządzanej** pole wyboru jest zaznaczone, ponieważ inicjatywy przypisaniem zawiera zasady z *deployIfNotExists* efekt.
    
1. W **lokalizacji Zarządzanie tożsamościami** listy rozwijanej wybierz odpowiedni region.

1. Wybierz opcję **Przypisz**.

#### <a name="review-and-remediate-the-compliance-results"></a>Przejrzyj i Skoryguj wyniki sprawdzania zgodności

Możesz dowiedzieć się, jak można przejrzeć wyniki sprawdzania zgodności, czytając [zidentyfikować wyniki niezgodności](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources). W okienku po lewej stronie wybierz **zgodności**, a następnie zlokalizuj  **[Wersja zapoznawcza] włączyć usługi Azure Monitor dla maszyn wirtualnych** inicjatywy dla maszyn wirtualnych, które nie są zgodne, zgodnie z przypisania został utworzony.

![Zgodność z zasadami dla maszyn wirtualnych platformy Azure](./media/vminsights-onboard/policy-view-compliance-01.png)

Na podstawie wyników zasad dołączone do tej inicjatywy, maszyny wirtualne są zgłaszane jako niezgodne w następujących scenariuszach:

* Nie jest wdrażana usługa log Analytics lub agenta zależności.  
    Ten scenariusz jest typowy dla zakresu przy użyciu istniejących maszyn wirtualnych. Aby go unikać, należy wdrożyć agentów wymaganych przez [tworzenie zadań korygowania](../../governance/policy/how-to/remediate-resources.md) niezgodne zasady.  
    - [Wersja zapoznawcza]: Deploy Dependency Agent for Linux VMs
    - [Wersja zapoznawcza]: Deploy Dependency Agent for Windows VMs
    - [Wersja zapoznawcza]: Deploy Log Analytics Agent for Linux VMs
    - [Wersja zapoznawcza]: Deploy Log Analytics Agent for Windows VMs

* Obraz maszyny Wirtualnej (OS) nie jest określona w definicji zasad.  
    Zasady wdrażania obejmują tylko maszyny wirtualne, które są wdrażane z dobrze znanych obrazów maszyn wirtualnych platformy Azure. Zapoznaj się z dokumentacją, aby zobaczyć, czy system operacyjny maszyny Wirtualnej jest obsługiwana. Jeśli nie jest obsługiwany, zduplikowania zasady wdrażania i aktualizacji lub zmodyfikuj go, aby utworzyć obraz zgodne.  
    - [Wersja zapoznawcza]: Wdrażanie agenta zależności inspekcji — obraz maszyny Wirtualnej (OS) nieznajdujące się na liście
    - [Wersja zapoznawcza]: Wdrażanie agentów inspekcji Log Analytics — obrazu maszyny Wirtualnej (OS) nieznajdujące się na liście

* Maszyny wirtualne nie są zalogowaniu się do określonego obszaru roboczego usługi Log Analytics.  
    Istnieje możliwość logowania niektórych maszyn wirtualnych w zakresie inicjatywy do obszaru roboczego usługi Log Analytics w innych niż ten, który jest określony w przypisaniu zasad. Ta zasada jest narzędziem, aby identyfikować, które maszyny wirtualne zgłaszanej niezgodny z obszarem roboczym.  
    - [Wersja zapoznawcza]: Audit Log Analytics Workspace for VM - Report Mismatch

### <a name="enable-with-powershell"></a>Włącz przy użyciu programu PowerShell
Aby włączyć usługi Azure Monitor dla maszyn wirtualnych, wiele maszyn wirtualnych i zestawów skalowania maszyn wirtualnych, możesz użyć skryptu programu PowerShell [VMInsights.ps1 instalacji](https://www.powershellgallery.com/packages/Install-VMInsights/1.0), która jest dostępna w galerii programu PowerShell platformy Azure. Ten skrypt wykonuje iterację przez każdy wirtualny maszyny i maszyn wirtualnych zestawu skalowania w ramach subskrypcji, w grupie zasobów o określonym zakresie, który jest określony przez *ResourceGroup*, lub do jednej maszyny Wirtualnej lub maszyny wirtualnej zestawu skalowania, który jest określony przez *Nazwa*. Dla każdej maszyny Wirtualnej lub maszyny wirtualnej zestawu skalowania skrypt sprawdza, czy rozszerzenie maszyny Wirtualnej jest już zainstalowany. Jeśli nie zainstalowano rozszerzenia maszyny Wirtualnej, skrypt próbuje zainstalować go ponownie. Jeśli zainstalowano rozszerzenia maszyny Wirtualnej, skrypt instaluje rozszerzenia maszyny Wirtualnej agenta usługi Log Analytics i zależności.

Ten skrypt wymaga programu Azure PowerShell w wersji modułu 5.7.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.

Aby uzyskać listę szczegółów argumentu oraz przykład użycia skryptu, uruchom `Get-Help`.

```powershell
Get-Help .\Install-VMInsights.ps1 -Detailed

SYNOPSIS
    This script installs VM extensions for Log Analytics and the Dependency agent as needed for VM Insights.


SYNTAX
    .\Install-VMInsights.ps1 [-WorkspaceId] <String> [-WorkspaceKey] <String> [-SubscriptionId] <String> [[-ResourceGroup]
    <String>] [[-Name] <String>] [[-PolicyAssignmentName] <String>] [-ReInstall] [-TriggerVmssManualVMUpdate] [-Approve] [-WorkspaceRegion] <String>
    [-WhatIf] [-Confirm] [<CommonParameters>]


DESCRIPTION
    This script installs or re-configures following on VMs and VM Scale Sets:
    - Log Analytics VM Extension configured to supplied Log Analytics Workspace
    - Dependency Agent VM Extension

    Can be applied to:
    - Subscription
    - Resource Group in a Subscription
    - Specific VM/VM Scale Set
    - Compliance results of a policy for a VM or VM Extension

    Script will show you list of VMs/VM Scale Sets that will apply to and let you confirm to continue.
    Use -Approve switch to run without prompting, if all required parameters are provided.

    If the extensions are already installed will not install again.
    Use -ReInstall switch if you need to for example update the workspace.

    Use -WhatIf if you would like to see what would happen in terms of installs, what workspace configured to, and status of the extension.


PARAMETERS
    -WorkspaceId <String>
        Log Analytics WorkspaceID (GUID) for the data to be sent to

    -WorkspaceKey <String>
        Log Analytics Workspace primary or secondary key

    -SubscriptionId <String>
        SubscriptionId for the VMs/VM Scale Sets
        If using PolicyAssignmentName parameter, subscription that VMs are in

    -ResourceGroup <String>
        <Optional> Resource Group to which the VMs or VM Scale Sets belong

    -Name <String>
        <Optional> To install to a single VM/VM Scale Set

    -PolicyAssignmentName <String>
        <Optional> Take the input VMs to operate on as the Compliance results from this Assignment
        If specified will only take from this source.

    -ReInstall [<SwitchParameter>]
        <Optional> If VM/VM Scale Set is already configured for a different workspace, set this to change to the new workspace

    -TriggerVmssManualVMUpdate [<SwitchParameter>]
        <Optional> Set this flag to trigger update of VM instances in a scale set whose upgrade policy is set to Manual

    -Approve [<SwitchParameter>]
        <Optional> Gives the approval for the installation to start with no confirmation prompt for the listed VMs/VM Scale Sets

    -WorkspaceRegion <String>
        Region the Log Analytics Workspace is in
        Supported values: "East US","eastus","Southeast Asia","southeastasia","West Central US","westcentralus","West Europe","westeurope"
        For Health supported is: "East US","eastus","West Central US","westcentralus"

    -WhatIf [<SwitchParameter>]
        <Optional> See what would happen in terms of installs.
        If extension is already installed will show what workspace is currently configured, and status of the VM extension

    -Confirm [<SwitchParameter>]
        <Optional> Confirm every action

    <CommonParameters>
        This cmdlet supports the common parameters: Verbose, Debug,
        ErrorAction, ErrorVariable, WarningAction, WarningVariable,
        OutBuffer, PipelineVariable, and OutVariable. For more information, see
        about_CommonParameters (https:/go.microsoft.com/fwlink/?LinkID=113216).

    -------------------------- EXAMPLE 1 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup>

    Install for all VMs in a Resource Group in a subscription

    -------------------------- EXAMPLE 2 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup> -ReInstall

    Specify to reinstall extensions even if already installed, for example to update to a different workspace

    -------------------------- EXAMPLE 3 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -PolicyAssignmentName a4f79f8ce891455198c08736 -ReInstall

    Specify to use a PolicyAssignmentName for source, and to reinstall (move to a new workspace)
```

W poniższym przykładzie pokazano, aby włączyć usługi Azure Monitor dla maszyn wirtualnych i zrozumieć oczekiwanych danych wyjściowych za pomocą poleceń programu PowerShell w folderze:

```powershell
$WorkspaceId = "<GUID>"
$WorkspaceKey = "<Key>"
$SubscriptionId = "<GUID>"
.\Install-VMInsights.ps1 -WorkspaceId $WorkspaceId -WorkspaceKey $WorkspaceKey -SubscriptionId $SubscriptionId -WorkspaceRegion eastus

Getting list of VMs or VM ScaleSets matching criteria specified

VMs or VM ScaleSets matching criteria:

db-ws-1 VM running
db-ws2012 VM running

This operation will install the Log Analytics and Dependency agent extensions on above 2 VMs or VM Scale Sets.
VMs in a non-running state will be skipped.
Extension will not be reinstalled if already installed. Use -ReInstall if desired, for example to update workspace

Confirm
Continue?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y

db-ws-1 : Deploying DependencyAgentWindows with name DAExtension
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Deploying DependencyAgentWindows with name DAExtension
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Summary:

Already Onboarded: (0)

Succeeded: (4)
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Connected to different workspace: (0)

Not running - start VM to configure: (0)

Failed: (0)
```

## <a name="enable-for-a-hybrid-environment"></a>Jest włączone w środowisku hybrydowym
W tej sekcji opisano sposób wdrażania maszyn wirtualnych lub fizycznych komputerów, które są hostowane w centrum danych lub innych środowisk w chmurze do monitorowania usługi Azure Monitor dla maszyn wirtualnych.

Usługi Azure Monitor dla agenta zależności mapy maszyn wirtualnych nie przesyłać żadnych danych, a nie wymaga wprowadzania zmian w zapory i porty. Dane mapy są zawsze przesyłane przez agenta usługi Log Analytics w usłudze Azure Monitor, bezpośrednio lub za pośrednictwem [bramy pakietu OMS](../../azure-monitor/platform/gateway.md) Jeśli Twoje informatyczne zasady zabezpieczeń nie pozwalają komputerom w sieci, aby nawiązać połączenie z Internetem.

Przegląd wymagań i metod wdrażania [agenta Log Analytics w systemie Linux i Windows](../../log-analytics/log-analytics-agent-overview.md).

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

Kroki są podsumować w następujący sposób:

1. Zainstaluj agenta programu Log Analytics dla Windows lub Linux.

1. Pobierz i zainstaluj usługi Azure Monitor dla agenta zależności mapy maszyn wirtualnych dla [Windows](https://aka.ms/dependencyagentwindows) lub [Linux](https://aka.ms/dependencyagentlinux).

1. Należy włączyć zbieranie liczników wydajności.

1. Wdrażanie usługi Azure Monitor dla maszyn wirtualnych.

### <a name="install-the-dependency-agent-on-windows"></a>Instalowanie agenta zależności na Windows
Można zainstalować agenta zależności ręcznie na komputerach Windows przez uruchomienie `InstallDependencyAgent-Windows.exe`. Po uruchomieniu tego pliku wykonywalnego bez żadnych opcji, uruchamia Kreatora instalacji, które można wykonać, aby zainstalować agenta interaktywnie.

>[!NOTE]
>*Administrator* uprawnienia są wymagane do zainstalowania lub odinstalowania agenta.

W poniższej tabeli wymieniono parametry, które są obsługiwane przez Instalatora agenta z wiersza polecenia.

| Parametr | Opis |
|:--|:--|
| /? | Zwraca listę opcji wiersza polecenia. |
| /S | Wykonanie dyskretnej instalacji bez interakcji użytkownika. |

Na przykład, aby uruchomić program instalacyjny z `/?` parametr, typ **InstallDependencyAgent Windows.exe /?**.

Pliki agenta zależności Windows są zainstalowane w *C:\Program Files\Microsoft Dependency Agent* domyślnie. Jeśli agenta zależności nie powiedzie się po zakończeniu instalacji, sprawdź dzienniki Aby uzyskać szczegółowe informacje o błędzie. Katalog dziennika jest *%Programfiles%\Microsoft Agent\logs zależności*.

### <a name="install-the-dependency-agent-on-linux"></a>Instalowanie agenta zależności w systemie Linux
Agent zależności jest zainstalowany na serwery z systemem Linux z *InstallDependencyAgent Linux64.bin*, skrypt powłoki, za pomocą samorozpakowujący się plik binarny. Można uruchomić go za pomocą `sh` lub Dodaj uprawnienia do wykonywania do pliku sam.

>[!NOTE]
> Aby zainstalować lub skonfigurować agenta, wymagany jest dostęp na poziomie administratora.
>

| Parametr | Opis |
|:--|:--|
| -help | Pobierz listę opcji wiersza polecenia. |
| -s | Przeprowadź instalację cichą bez monitowania użytkownika. |
| — Sprawdź | Sprawdzanie uprawnień i systemu operacyjnego, ale nie należy instalować agenta. |

Na przykład, aby uruchomić program instalacyjny z `-help` parametr, typ **InstallDependencyAgent Linux64.bin — Pomoc**.

Instalowanie agenta zależności systemu Linux jako użytkownik główny, uruchamiając następujące polecenie `sh InstallDependencyAgent-Linux64.bin`.

Jeśli agenta zależności nie powiedzie się, sprawdź dzienniki Aby uzyskać szczegółowe informacje o błędzie. Na agentach systemu Linux jest katalog dziennika */var/opt/microsoft/dependency-agent/log*.

Pliki agenta zależności są umieszczone w następujących katalogach:

| Pliki | Lokalizacja |
|:--|:--|
| Pliki jądra | /opt/microsoft/dependency-agent |
| Pliki dziennika | /var/opt/microsoft/dependency-agent/log |
| Plik konfiguracji | /etc/opt/microsoft/dependency-agent/config |
| Pliki wykonywalne usługi | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Pliki binarne magazynu | /var/opt/microsoft/dependency-agent/storage |

### <a name="enable-performance-counters"></a>Włącz liczniki wydajności
Jeśli obszar roboczy usługi Log Analytics, który odwołuje się do niej rozwiązanie nie jest już skonfigurowany można zebrać liczników wydajności wymaganych przez to rozwiązanie, należy je włączyć. Można to zrobić na dwa sposoby:
* Ręcznie, zgodnie z opisem w [Windows i Linux źródła danych dotyczących wydajności w usłudze Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md)
* Pobierając i uruchamiając skrypt programu PowerShell, który jest dostępny z [galerii programu PowerShell systemu Azure](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

### <a name="deploy-azure-monitor-for-vms"></a>Wdrażanie usługi Azure Monitor dla maszyn wirtualnych
Ta metoda obejmuje szablon JSON, który określa konfigurację włączanie składników rozwiązania w Twoim obszarze roboczym usługi Log Analytics.

Jeśli znasz wdrażanie zasobów za pomocą szablonu, zobacz:
* [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)](../../azure-resource-manager/resource-group-template-deploy.md)
* [Wdrażanie zasobów przy użyciu szablonów usługi Resource Manager i interfejsu wiersza polecenia platformy Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Jeśli zdecydujesz się użyć wiersza polecenia platformy Azure, należy najpierw zainstalować i korzystać z interfejsu wiersza polecenia lokalnie. Musi być uruchomiona wiersza polecenia platformy Azure w wersji 2.0.27 lub nowszej. Aby zidentyfikować wersję, uruchom `az --version`. Jeśli musisz zainstalować lub uaktualnić wiersza polecenia platformy Azure, zobacz [zainstalować interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

#### <a name="create-and-execute-a-template"></a>Tworzenie i wykonywanie szablonu

1. Skopiuj i wklej następującą składnię JSON do pliku:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "WorkspaceName": {
                "type": "string"
            },
            "WorkspaceLocation": {
                "type": "string"
            }
        },
        "resources": [
            {
                "apiVersion": "2017-03-15-preview",
                "type": "Microsoft.OperationalInsights/workspaces",
                "name": "[parameters('WorkspaceName')]",
                "location": "[parameters('WorkspaceLocation')]",
                "resources": [
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },

                        "plan": {
                            "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'ServiceMap')]",
                            "promotionCode": ""
                        }
                    },
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },
                        "plan": {
                            "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'InfrastructureInsights')]",
                            "promotionCode": ""
                        }
                    }
                ]
            }
        ]
    }
    ```

1. Zapisz ten plik jako *installsolutionsforvminsights.json* do folderu lokalnego.

1. Edytuj wartości *WorkspaceName*, *ResourceGroupName*, i *WorkspaceLocation*. Wartość *WorkspaceName* jest pełny identyfikator zasobu obszaru roboczego usługi Log Analytics, która zawiera nazwę obszaru roboczego. Wartość *WorkspaceLocation* jest region, w obszarze roboczym jest zdefiniowany w.

1. Możesz wdrożyć ten szablon przy użyciu następującego polecenia programu PowerShell:

    ```powershell
    New-AzureRmResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    Zmiana konfiguracji może potrwać kilka minut. Po jej zakończeniu zostanie wyświetlony komunikat podobny do poniższego, która zawiera wynik:

    ```powershell
    provisioningState       : Succeeded
    ```
Po włączeniu monitorowania może zająć około 10 minut, zanim będzie można wyświetlić stan kondycji i metryki dla komputera hybrydowych.

## <a name="performance-counters-enabled"></a>Liczniki wydajności włączone
Usługa Azure Monitor dla maszyn wirtualnych służy do konfigurowania obszaru roboczego usługi Log Analytics można zebrać liczników wydajności, które są używane przez rozwiązania. Poniższa tabela zawiera listę obiektów i liczników skonfigurowane przez to rozwiązanie, które są zbierane co 60 sekund.

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
