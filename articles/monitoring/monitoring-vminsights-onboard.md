---
title: Dołączanie monitora platformy Azure dla maszyn wirtualnych | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób możesz dodać i skonfigurować usługi Azure Monitor dla maszyn wirtualnych, aby można było zacząć, zrozumienie, jak działa aplikacja rozproszona i jakie problemy z kondycją zostały zidentyfikowane.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: magoedte
ms.openlocfilehash: 2f0568064eed556429675ffb34c84d588ac670d5
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2018
ms.locfileid: "47064360"
---
# <a name="how-to-onboard-the-azure-monitor-for-vms"></a>Jak dołączyć platformy Azure, monitorując dla maszyn wirtualnych 
W tym artykule opisano sposób konfigurowania usługi Azure Monitor dla maszyn wirtualnych w celu monitorowania kondycji systemu operacyjnego w maszynach wirtualnych platformy Azure oraz wykrywanie i mapowanie zależności aplikacji, które może być hostowana na nich.  

Włączanie usługi Azure Monitor dla maszyn wirtualnych odbywa się przy użyciu jednej z następujących metod i szczegółowe informacje na temat korzystania z poszczególnych metod znajdują się w dalszej części tego artykułu.  

* Pojedynczej maszyny wirtualnej platformy Azure, wybierając **Insights (wersja zapoznawcza)** bezpośrednio z poziomu maszyny Wirtualnej.
* Wiele maszyn wirtualnych platformy Azure przy użyciu usługi Azure Policy, aby upewnić się, istniejące i nowe maszyny wirtualne oceniane mają wymagane zależności, które są zainstalowane i są poprawnie skonfigurowane.  Niezgodne maszyny wirtualne są zgłaszane, dzięki czemu można określić na podstawie co nie jest zgodne, w jaki sposób chcesz skorygować.  
* Wiele maszyn wirtualnych platformy Azure lub na maszynie wirtualnej zestawy skalowania w określonej subskrypcji lub grupy zasobów przy użyciu programu PowerShell.

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem upewnij się, że masz następujące zgodnie z opisem w poniższych sekcjach podrzędnych.

### <a name="log-analytics"></a>Log Analytics 

Obecnie są obsługiwane przez obszar roboczy usługi Log Analytics w następujących regionach:

  - Środkowo-zachodnie stany USA  
  - Wschodnie stany USA  
  - Europa Zachodnia  
  - Azja południowo-wschodnia<sup>1</sup>  

<sup>1</sup> ten region nie obsługuje obecnie funkcję kondycji monitora platformy Azure dla maszyn wirtualnych   

Jeśli nie masz obszaru roboczego, możesz je utworzyć za pomocą [usługi Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md)za pośrednictwem [PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json), lub [witryny Azure portal](../log-analytics/log-analytics-quick-create-workspace.md).  

Aby włączyć rozwiązanie, musisz być członkiem roli Współautor usługi Log Analytics. Aby uzyskać więcej informacji na temat kontrolowania dostępu do obszaru roboczego usługi Log Analytics, zobacz [możesz zarządzać obszarami roboczymi](../log-analytics/log-analytics-manage-access.md).

[!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)]

### <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

W poniższej tabeli wymieniono systemy operacyjne Windows i Linux, które są obsługiwane z usługą Azure Monitor dla maszyn wirtualnych.  Pełną listę szczegółowych informacji na temat którego wersji głównych i pomocniczych systemu operacyjnego Linux i obsługiwane wersje jądra zostaną udostępnione później w tej sekcji.

|Wersja systemu operacyjnego |Wydajność |Maps |Health |  
|-----------|------------|-----|-------|  
|System Windows Server 2016 1803 | X | X | X |
|Windows Server 2016 | X | X | X |  
|Windows Server 2012 R2 | X | X | |  
|Windows Server 2012 | X | X | |  
|Windows Server 2008 R2 | X | X| |  
|RHEL 7, 6| X | X| X |  
|Naciśnięcie i przytrzymanie 18.04 Ubuntu 16.04, 14.04 | X | X | X |  
|Centa systemu operacyjnego Linux 7, 6 | X | X | X |  
|SLES 12 | X | X | X |  
|Oracle Linux 7 | X<sup>1</sup> | | X |  
|Oracle Linux 6 | X | X | X |  
|Debian 9.4, 8 | X<sup>1</sup> | | X | 

<sup>1</sup> wydajności funkcji usługi Azure Monitor dla maszyn wirtualnych jest dostępny tylko w usłudze Azure Monitor, nie jest dostępne po użytkownik bezpośrednio uzyskiwać dostęp za pomocą okienka po lewej stronie maszyny Wirtualnej platformy Azure.  

>[!NOTE]
>Poniższe informacje dotyczą do obsługi systemów operacyjnych Linux:  
> - Obsługiwane są tylko wersje domyślne i wersje SMP jądra systemu Linux.  
> - Niestandardowe wydania jądra, takie jak PAE i Xen, nie są obsługiwane dla żadnej dystrybucji systemu Linux. Na przykład systemu z wersji ciąg "2.6.16.21-0.8-xen" nie jest obsługiwane.  
> - Niestandardowe jądra, łącznie z ponownymi kompilacjami standardowych jąder, nie są obsługiwane.  
> - Jądro CentOSPlus nie jest obsługiwane.  


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

### <a name="hybrid-environment-connected-sources"></a>Środowisko hybrydowe połączone źródła
Usługa Azure Monitor dla maszyn wirtualnych mapy dane są pobierane z Microsoft Dependency agent. Agent zależności zależy od agenta usługi Log Analytics do połączenia z usługi Log Analytics. Oznacza to, że system musi mieć agenta usługi Log Analytics, zainstalować i skonfigurować za pomocą agenta zależności.  W poniższej tabeli opisano połączone źródła obsługiwanych przez funkcję mapy w środowisku hybrydowym.

| Połączone źródło | Obsługiwane | Opis |
|:--|:--|:--|
| Agenci dla systemu Windows | Yes | Oprócz [agenta usługi Log Analytics dla Windows](../log-analytics/log-analytics-concept-hybrid.md), agenci Windows wymagają Microsoft Dependency agent. Zobacz [obsługiwane systemy operacyjne](#supported-operating-systems), gdzie znajdziesz pełną listę wersji systemu operacyjnego. |
| Agenci dla systemu Linux | Yes | Oprócz [agenta usługi Log Analytics dla systemu Linux](../log-analytics/log-analytics-concept-hybrid.md), agenci dla systemu Linux wymaga program Microsoft Dependency agent. Zobacz [obsługiwane systemy operacyjne](#supported-operating-systems), gdzie znajdziesz pełną listę wersji systemu operacyjnego. |
| Grupa zarządzania programu System Center Operations Manager | Nie | |  

Na Windows, Microsoft Monitoring Agent (MMA) jest używane zarówno przez System Center Operations Manager i usługi Log Analytics zbieranie i wysyłanie danych monitorowania. System Center Operations Manager i usługi Log Analytics zawiera różne out-gotową do wersji agenta. Każda z tych wersji może raportować do programu System Center Operations Manager, do usługi Log Analytics lub do obu miejsc.  

W systemie Linux agent usługi Log Analytics dla systemu Linux zbiera informacje i wysyła dane do usługi Log Analytics monitorowania.   

Jeśli komputery Windows lub Linux bezpośrednio nie może połączyć się z usługą, należy skonfigurować agenta usługi Log Analytics do łączenia z usługą Log Analytics, za pomocą bramy pakietu OMS. Aby uzyskać więcej informacji na temat sposobu wdrażania i konfigurowania bramy pakietu OMS, zobacz [połączyć komputery bez dostępu do Internetu za pomocą bramy pakietu OMS](../log-analytics/log-analytics-oms-gateway.md).  

Z następującej lokalizacji można pobrać agenta zależności.

| Plik | System operacyjny | Wersja | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.7.1 | 55030ABF553693D8B5112569FB2F97D7C54B66E9990014FC8CC43EFB70DE56C6 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.7.1 | 43C75EF0D34471A0CBCE5E396FFEEF4329C9B5517266108FA5D6131A353D29FE |

## <a name="diagnostic-and-usage-data"></a>Dane diagnostyczne i dane dotyczące użycia
Firma Microsoft automatycznie zbiera dane dotyczące użycia i wydajności przez korzystanie z usługi Azure Monitor. Firma Microsoft używa tych danych do zapewniania i poprawiania jakości, bezpieczeństwa i integralności usługi. Aby zapewnić dokładne i wydajne funkcje do rozwiązywania problemów, dane z funkcji mapy zawiera informacje o konfiguracji oprogramowania, takie jak system operacyjny i wersję, adres IP, nazwę DNS i nazwę stacji roboczej. Firma Microsoft gromadzi nazwisk, adresów ani innych informacji kontaktowych.

Aby uzyskać więcej informacji na temat zbierania i wykorzystywania danych, zobacz [Microsoft Online Services Privacy Statement](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="performance-counters-enabled"></a>Liczniki wydajności włączone
Usługa Azure Monitor dla maszyn wirtualnych służy do konfigurowania obszaru roboczego usługi Log Analytics można zebrać liczników wydajności, używane przez to rozwiązanie.  Poniższa tabela zawiera listę obiektów i liczników skonfigurowane przez to rozwiązanie, które są zbierane co 60 sekund.

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

## <a name="enable-from-the-azure-portal"></a>Korzystanie z witryny Azure portal
Aby włączyć monitorowanie maszyny wirtualnej platformy Azure w witrynie Azure portal, wykonaj następujące czynności:

1. W witrynie Azure portal wybierz **maszyn wirtualnych**. 
2. Z listy wybierz maszynę wirtualną. 
3. Na stronie maszyny Wirtualnej w **monitorowanie** zaznacz **Insights (wersja zapoznawcza)**.
4. Na **Insights (wersja zapoznawcza)** wybierz opcję **Wypróbuj teraz usługę**.

    ![Włączanie usługi Azure Monitor dla maszyn wirtualnych dla maszyny Wirtualnej](./media/monitoring-vminsights-onboard/enable-vminsights-vm-portal-01.png)

5. Na **Azure Monitor Insights dołączania** strony, jeśli masz istniejące usługi Log Analytics obszaru roboczego w tej samej subskrypcji, wybierz go na liście rozwijanej.  Lista preselects domyślnego obszaru roboczego i lokalizacji, która została wdrożona maszyna wirtualna, aby w ramach subskrypcji. 

    >[!NOTE]
    >Jeśli chcesz utworzyć nowy obszar roboczy usługi Log Analytics do przechowywania danych monitorowania z maszyny Wirtualnej, postępuj zgodnie z instrukcjami [Utwórz obszar roboczy usługi Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md) w jednym z obsługiwanych regionów wymienionych wcześniej.   

Po włączeniu monitorowania może zająć około 10 minut, zanim będzie można wyświetlić metryki kondycji dla maszyny wirtualnej. 

![Włączanie usługi Azure Monitor monitorowania przetwarzanie wdrożenia maszyn wirtualnych](./media/monitoring-vminsights-onboard/onboard-vminsights-vm-portal-status.png)

## <a name="enable-using-azure-policy"></a>Włącz przy użyciu usługi Azure Policy
Aby włączyć rozwiązanie dla wielu maszyn wirtualnych platformy Azure, która zapewnia spójne zgodności i automatyczne włączanie dla nowych maszyn wirtualnych aprowizowane, [usługi Azure Policy](../azure-policy/azure-policy-introduction.md) jest zalecane.  Za pomocą usługi Azure Policy, za pomocą zasad, pod warunkiem zapewnia następujące korzyści dla nowych maszyn wirtualnych:

* Włączanie usługi Azure Monitor dla maszyn wirtualnych dla każdej maszyny Wirtualnej w zdefiniowanego zakresu
* Wdróż agenta programu Log Analytics 
* Wdrażanie agenta zależności, aby odnaleźć zależności aplikacji i Pokaż na mapie
* Przeprowadzaj inspekcję obraz systemu operacyjnego maszyny Wirtualnej platformy Azure jest w uprzednio zdefiniowanej listy w definicji zasad  
* Przeprowadzaj inspekcję rejestrowanie zdarzeń maszyny Wirtualnej platformy Azure do obszaru roboczego innego niż określona
* Raport dotyczący wyniki sprawdzania zgodności 
* Obsługa korygowania niezgodnych maszyn wirtualnych

Aby go uaktywnić dla dzierżawy, ten proces wymaga:

- Konfiguruj obszar roboczy Log Analytics wykonując kroki opisane w tym miejscu
- Importowanie definicja inicjatywy do dzierżawy (na poziomie grupy zarządzania lub subskrypcji)
- Przypisz zasady do żądanego zakresu
- Przejrzyj wyniki sprawdzania zgodności

### <a name="add-the-policies-and-initiative-to-your-subscription"></a>Dodaj zasady i inicjatywa do subskrypcji
Aby użyć zasad, można użyć dostarczonego skryptu programu PowerShell — [VMInsightsPolicy.ps1 Dodaj](https://www.powershellgallery.com/packages/Add-VMInsightsPolicy/1.2) dostępne w galerii programu PowerShell systemu Azure, aby zakończyć to zadanie. Skrypt ten dodaje zasady i inicjatywy do Twojej subskrypcji.  Wykonaj poniższe kroki, aby skonfigurować zasady usługi Azure w ramach subskrypcji. 

1. Pobierz skrypt programu PowerShell do lokalnego systemu plików.

2. Użyj następującego polecenia programu PowerShell w folderze, aby dodać zasady. Skrypt obsługuje następujące parametry opcjonalne: 

    ```powershell
    -UseLocalPolicies [<SwitchParameter>]
      <Optional> Load the policies from a local folder instead of https://raw.githubusercontent.com/dougbrad/OnBoardVMInsights/Policy/Policy/

    -SubscriptionId <String>
      <Optional> SubscriptionId to add the Policies/Initiatives to
    -ManagementGroupId <String>
      <Optional> Management Group Id to add the Policies/Initiatives to

    -Approve [<SwitchParameter>]
      <Optional> Gives the approval to add the Policies/Initiatives without any prompt
    ```  

    >[!NOTE]
    >Uwaga: Jeśli planujesz przypisanie inicjatywy/zasady do wielu subskrypcji, definicje muszą być przechowywane w grupie zarządzania, zawierającą subskrypcje, które przypiszesz zasady. W związku z tym należy użyć parametru - ManagementGroupID.
    >
   
    Przykład bez parametrów:  `.\Add-VMInsightsPolicy.ps1`

### <a name="create-a-policy-assignment"></a>Tworzenie przypisania zasad
Po uruchomieniu `Add-VMInsightsPolicy.ps1` skrypt programu PowerShell, następujące inicjatywy i zasady zostaną dodane:

* **Wdrażanie programu Log Analytics Agent for Windows VMs — wersja zapoznawcza**
* **Wdrażanie programu Log Analytics Agent na maszynach wirtualnych systemu Linux — wersja zapoznawcza**
* **Wdrażanie agenta zależności dla maszyn wirtualnych Windows — wersja zapoznawcza**
* **Wdrażanie agenta zależności maszyn wirtualnych systemu Linux — wersja zapoznawcza**
* **Inspekcja Log Analytics Agent wdrożenia — obraz (system operacyjny maszyny Wirtualnej) nieznajdujące się na liście — w wersji zapoznawczej**
* **Inspekcja zależności agenta wdrożenia — obraz (system operacyjny maszyny Wirtualnej) nieznajdujące się na liście — w wersji zapoznawczej**

Następujący parametr inicjatywy zostanie dodana:

- **Zaloguj się Analytice obszaru roboczego** (trzeba podać identyfikator zasobu obszaru roboczego, jeśli zastosowanie przypisania przy użyciu programu PowerShell lub interfejsu wiersza polecenia)

    Maszyny wirtualne dostępne jako niezgodne z zasadami inspekcji **maszyny wirtualne nie znajduje się w zakresie systemu operacyjnego...**  kryteria zasady wdrażania obejmuje tylko maszyny wirtualne, które są wdrażane z dobrze znanych obrazów maszyn wirtualnych platformy Azure. Zajrzyj do dokumentacji, jeśli system operacyjny maszyny Wirtualnej jest obsługiwany lub nie.  Jeśli nie jest, należy skopiować zasady wdrażania i aktualizacji/zmodyfikować, aby utworzyć obraz w zakresie.

Dodaje następujące zasady opcjonalne autonomicznych:

- **Maszyna wirtualna jest skonfigurowana dla niedopasowanych Analytics obszar roboczy usługi Log — wersja zapoznawcza**

    To może służyć do identyfikowania maszyn wirtualnych już skonfigurowaną [rozszerzenia Log Analytics VM Extension](../virtual-machines/extensions/oms-windows.md), ale są skonfigurowane przy użyciu innego obszaru roboczego, niż planowano (co zostało wskazane przez przypisanie zasad). Ten parametr, w jakim identyfikator obszaru roboczego.

W tej wersji początkowej przypisania zasad można tworzyć tylko w witrynie Azure portal. Aby dowiedzieć się, jak wykonać następujące czynności, zobacz [Tworzenie przypisania zasad w witrynie Azure portal](../azure-policy/assign-policy-definition.md).

## <a name="enable-with-powershell"></a>Włącz przy użyciu programu PowerShell
Aby włączyć usługi Azure Monitor dla maszyn wirtualnych, dla wielu maszyn wirtualnych lub maszyn wirtualnych zestawów skalowania, można użyć dostarczonego skryptu programu PowerShell — [VMInsights.ps1 instalacji](https://www.powershellgallery.com/packages/Install-VMInsights/1.0) dostępne w galerii programu PowerShell systemu Azure, aby zakończyć to zadanie.  Ten skrypt iteracji przez co maszyna wirtualna i maszyny Wirtualnej zestawu skalowania w Twojej subskrypcji, w grupie zasobów o określonym zakresie, określony przez *ResourceGroup*, lub do jednej maszyny Wirtualnej lub określony przez zestaw skalowania *nazwa*.  Dla każdej maszyny Wirtualnej lub maszyny Wirtualnej zestawu skalowania skrypt sprawdza, jeśli rozszerzenie maszyny Wirtualnej jest już zainstalowane, a nie podjęto próbę jego ponowną instalację.  W przeciwnym razie przechodzą do instalowania rozszerzeń usługi Log Analytics i maszyn wirtualnych agenta zależności.   

Ten skrypt wymaga programu Azure PowerShell w wersji modułu 5.7.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.

Aby uzyskać pomoc dotyczącą skryptu, można uruchomić `Get-Help` można pobrać listy argumentów szczegóły oraz przykład użycia.   

```powershell
Get-Help .\Install-VMInsights.ps1 -Detailed

SYNOPSIS
    This script installs VM extensions for Log Analytics and Dependency Agent as needed for VM Insights.


SYNTAX
    .\Install-VMInsights.ps1 [-WorkspaceId] <String> [-WorkspaceKey] <String> [-SubscriptionId] <String> [[-ResourceGroup]
    <String>] [[-Name] <String>] [[-PolicyAssignmentName] <String>] [-ReInstall] [-TriggerVmssManualVMUpdate] [-Approve] [-WorkspaceRegion] <String>
    [-WhatIf] [-Confirm] [<CommonParameters>]


DESCRIPTION
    This script installs or re-configures following on VM's and VM Scale Sets:
    - Log Analytics VM Extension configured to supplied Log Analytics Workspace
    - Dependency Agent VM Extension

    Can be applied to:
    - Subscription
    - Resource Group in a Subscription
    - Specific VM/VM Scale Set
    - Compliance results of a policy for a VM or VM Extension

    Script will show you list of VM's/VM Scale Sets that will apply to and let you confirm to continue.
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
        If using PolicyAssignmentName parameter, subscription that VM's are in

    -ResourceGroup <String>
        <Optional> Resource Group to which the VMs or VM Scale Sets belong to

    -Name <String>
        <Optional> To install to a single VM/VM Scale Set

    -PolicyAssignmentName <String>
        <Optional> Take the input VM's to operate on as the Compliance results from this Assignment
        If specified will only take from this source.

    -ReInstall [<SwitchParameter>]
        <Optional> If VM/VM Scale Set is already configured for a different workspace, set this to change to the new workspace

    -TriggerVmssManualVMUpdate [<SwitchParameter>]
        <Optional> Set this flag to trigger update of VM instances in a scale set whose upgrade policy is set to Manual

    -Approve [<SwitchParameter>]
        <Optional> Gives the approval for the installation to start with no confirmation prompt for the listed VM's/VM Scale Sets

    -WorkspaceRegion <String>
        Region the Log Analytics Workspace is in
        Suported values: "East US","eastus","Southeast Asia","southeastasia","West Central US","westcentralus","West Europe","westeurope"
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

    Install for all VM's in a Resource Group in a subscription

    -------------------------- EXAMPLE 2 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup> -ReInstall

    Specify to ReInstall extensions even if already installed, for example to update to a different workspace

    -------------------------- EXAMPLE 3 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -PolicyAssignmentName a4f79f8ce891455198c08736 -ReInstall

    Specify to use a PolicyAssignmentName for source, and to ReInstall (move to a new workspace)
```

W poniższym przykładzie pokazano, aby włączyć usługi Azure Monitor dla maszyn wirtualnych i zrozumieć oczekiwanych danych wyjściowych za pomocą poleceń programu PowerShell w folderze:

```powershell
$WorkspaceId = "<GUID>"
$WorkspaceKey = "<Key>"
$SubscriptionId = "<GUID>"
.\Install-VMInsights.ps1 -WorkspaceId $WorkspaceId -WorkspaceKey $WorkspaceKey -SubscriptionId $SubscriptionId -WorkspaceRegion eastus

Getting list of VM's or VM ScaleSets matching criteria specified

VM's or VM ScaleSets matching criteria:

db-ws-1 VM running
db-ws2012 VM running

This operation will install the Log Analytics and Dependency Agent extensions on above 2 VM's or VM Scale Sets.
VM's in a non-running state will be skipped.
Extension will not be re-installed if already installed. Use -ReInstall if desired, for example to update workspace

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

## <a name="enable-for-hybrid-environment"></a>Włącz dla środowiska hybrydowego
W tej sekcji opisano sposób dołączania maszyn wirtualnych lub fizycznych komputerów są hostowane w centrum danych lub inne środowiska chmury na potrzeby monitorowania usługi Azure Monitor dla maszyn wirtualnych.  

Usługi Azure Monitor dla agenta zależności mapy maszyn wirtualnych nie przesyła wszystkie dane, a nie wymaga wprowadzania zmian w zapory i porty. Danych na mapie są zawsze przesyłane przez agenta usługi Log Analytics w usłudze Azure Monitor, bezpośrednio lub za pośrednictwem [bramy pakietu OMS](../log-analytics/log-analytics-oms-gateway.md) Jeśli Twoje informatyczne zasady zabezpieczeń nie pozwalają komputerom w sieci, aby nawiązać połączenie z Internetem.

Przegląd wymagań i metod wdrażania [agenta Log Analytics w systemie Linux i Windows](../log-analytics/log-analytics-concept-hybrid.md).

Skrócona kroki:

1. Zainstaluj agenta programu Log Analytics dla Windows lub Linux
2. Instalowanie usługi Azure Monitor dla agenta zależności mapy maszyn wirtualnych
3. Włączyć zbieranie liczników wydajności
4. Dołączanie monitora platformy Azure dla maszyn wirtualnych

### <a name="install-the-dependency-agent-on-windows"></a>Instalowanie agenta zależności na Windows 
Agent zależności można zainstalować ręcznie na komputerach Windows przez uruchomienie `InstallDependencyAgent-Windows.exe`. Po uruchomieniu tego pliku wykonywalnego bez żadnych opcji, uruchamia Kreatora instalacji, które można wykonać w celu interaktywnego instalowania.  

>[!NOTE]
>Aby zainstalować lub odinstalować agenta, wymagane są uprawnienia administratora.

W poniższej tabeli wymieniono określone parametry, które są obsługiwane przez Instalatora agenta z wiersza polecenia.  

| Parametr | Opis |
|:--|:--|
| /? | Zwraca listę opcji wiersza polecenia. |
| /S | Należy przeprowadzić instalację dyskretną bez interakcji użytkownika. |

Na przykład, aby uruchomić program instalacyjny z `/?` parametr, typ `InstallDependencyAgent-Windows.exe /?`

Pliki agenta zależności Windows są zainstalowane w `C:\Program Files\Microsoft Dependency Agent` domyślnie.  Jeśli agenta zależności nie powiedzie się po zakończeniu instalacji, sprawdź dzienniki Aby uzyskać szczegółowe informacje o błędzie. Katalog dziennika jest `%Programfiles%\Microsoft Dependency Agent\logs`. 

### <a name="install-the-dependency-agent-on-linux"></a>Instalowanie agenta zależności w systemie Linux
Agent zależności jest zainstalowany na serwery z systemem Linux z `InstallDependencyAgent-Linux64.bin`, skrypt powłoki, za pomocą samorozpakowujący się plik binarny. Można uruchomić go za pomocą `sh` lub Dodaj uprawnienia do wykonywania do pliku sam.

>[!NOTE]
> Aby zainstalować lub skonfigurować agenta, wymagany jest dostęp na poziomie administratora.
> 

| Parametr | Opis |
|:--|:--|
| -help | Pobierz listę opcji wiersza polecenia. |
| -s | Przeprowadź instalację cichą bez monitowania użytkownika. |
| — Sprawdź | Sprawdź uprawnienia i system operacyjny, ale nie instaluj agenta. |

Na przykład, aby uruchomić program instalacyjny z `-help` parametr, typ `InstallDependencyAgent-Linux64.bin -help`.

Instalowanie agenta zależności systemu Linux jako użytkownik główny, uruchamiając następujące polecenie `sh InstallDependencyAgent-Linux64.bin`
    
Jeśli agenta zależności nie powiedzie się, sprawdź dzienniki Aby uzyskać szczegółowe informacje o błędzie. Na agentach systemu Linux jest katalog dziennika `/var/opt/microsoft/dependency-agent/log`.

Pliki agenta zależności są umieszczone w następujących katalogach:

| Pliki | Lokalizacja |
|:--|:--|
| Pliki jądra | /opt/microsoft/dependency-agent |
| Pliki dziennika | /var/opt/microsoft/dependency-agent/log |
| Plik konfiguracji | /etc/opt/microsoft/dependency-agent/config |
| Pliki wykonywalne usługi | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Pliki binarne magazynu | /var/opt/microsoft/dependency-agent/storage |

### <a name="enable-performance-counters"></a>Włącz liczniki wydajności
Jeśli obszar roboczy usługi Log Analytics, przywoływane przez to rozwiązanie nie jest skonfigurowany do już zbierania liczników wydajności wymaganych przez to rozwiązanie, muszą być włączone. Można to zrobić ręcznie, zgodnie z opisem [tutaj](../log-analytics/log-analytics-data-sources-performance-counters.md), lub przez pobranie i uruchomienie skryptu programu PowerShell dostępny w [galerii programu Powershell Azure](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1).
 
### <a name="onboard-azure-monitor-for-vms"></a>Dołączanie monitora platformy Azure dla maszyn wirtualnych
Ta metoda obejmuje szablon JSON, który określa konfigurację, aby włączyć składników rozwiązania do obszaru roboczego usługi Log Analytics.  

Jeśli znasz koncepcji wdrażanie zasobów za pomocą szablonu, zobacz:
* [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)](../azure-resource-manager/resource-group-template-deploy.md)
* [Wdrażanie zasobów przy użyciu szablonów usługi Resource Manager i interfejsu wiersza polecenia platformy Azure](../azure-resource-manager/resource-group-template-deploy-cli.md) 

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
    ```

2. Zapisz ten plik jako **installsolutionsforvminsights.json** do folderu lokalnego.
3. Edytuj wartości **WorkspaceName**, **ResourceGroupName**, i **WorkspaceLocation**.  Wartość **WorkspaceName** jest to pełny identyfikator zasobu obszaru roboczego usługi Log Analytics, która zawiera nazwę obszaru roboczego, a wartość **WorkspaceLocation** to region, obszar roboczy jest zdefiniowany w programie.
4. Wszystko będzie gotowe do wdrożenia tego szablonu, za pomocą następującego polecenia programu PowerShell:

    ```powershell
    New-AzureRmResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    Zmiana konfiguracji może potrwać kilka minut. Po jej zakończeniu zostanie wyświetlony komunikat podobny do poniższego, która zawiera wynik:

    ```powershell
    provisioningState       : Succeeded
    ```
Po włączeniu monitorowania może zająć około 10 minut, zanim będzie można wyświetlić stan kondycji i metryki dla komputera hybrydowych. 

## <a name="next-steps"></a>Kolejne kroki

Przy użyciu funkcji monitorowania, włączone dla maszyny wirtualnej, te informacje są dostępne dla analizy dzięki usłudze Azure Monitor dla maszyn wirtualnych.  Aby dowiedzieć się, jak korzystać z funkcji Health, zobacz [widok usługi Azure Monitor kondycji maszyn wirtualnych](monitoring-vminsights-health.md), lub aby obejrzeć zależności odnalezionych aplikacji, zobacz [widok usługi Azure Monitor dla maszyn wirtualnych jest mapowany](monitoring-vminsights-maps.md).  