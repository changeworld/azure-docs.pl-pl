---
title: Funkcje dla Windows i rozszerzeniach maszyn wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jakie rozszerzenia są dostępne dla maszyn wirtualnych platformy Azure, pogrupowane według co dane substancje mogą zapewnić lub które poprawić.
services: virtual-machines-windows
documentationcenter: ''
author: roiyz-msft
manager: gwallace
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 999d63ee-890e-432e-9391-25b3fc6cde28
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: roiyz
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e2cd64b54b1a30080d7942a754bc0c0c72c59f88
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705982"
---
# <a name="virtual-machine-extensions-and-features-for-windows"></a>Rozszerzenia maszyn wirtualnych i funkcji dla Windows

Rozszerzenia maszyn wirtualnych (VM) to małych aplikacji, które zapewniają po wdrożeniu konfiguracji oraz zadania automatyzacji na maszynach wirtualnych platformy Azure. Na przykład, czy maszyna wirtualna wymaga instalacji oprogramowania, oprogramowanie antywirusowe ochrony, aby uruchomić skrypt wewnątrz niej, można użyć rozszerzenia maszyny Wirtualnej. Rozszerzenia maszyn wirtualnych platformy Azure można uruchomić z wiersza polecenia platformy Azure, programu PowerShell, szablony usługi Azure Resource Manager i witryny Azure portal. Rozszerzenia mogą być powiązane z wdrażaniem nowej maszyny Wirtualnej lub uruchamiać dowolnego istniejącego systemu.

Ten artykuł zawiera przegląd rozszerzeń maszyn wirtualnych, wymagania wstępne dotyczące korzystania z rozszerzenia maszyny Wirtualnej platformy Azure i uzyskać wskazówki dotyczące sposobu wykrywania, zarządzanie i usuwanie rozszerzeń maszyn wirtualnych. Ten artykuł zawiera informacje uogólniony, ponieważ wiele rozszerzeń maszyn wirtualnych są dostępne, każdy z potencjalnie unikatowej konfiguracji. Szczegóły specyficzne dla rozszerzenia można znaleźć w każdym dokumencie specyficzne dla poszczególnych rozszerzeń.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="use-cases-and-samples"></a>Przypadki użycia i przykłady

Kilka różnych rozszerzenia maszyny Wirtualnej platformy Azure są dostępne, każdy z określonym przypadek użycia. Oto niektóre przykłady:

- Dotyczy konfiguracji środowiska PowerShell żądanego stanu maszyny Wirtualnej za pomocą rozszerzenia DSC dla Windows. Aby uzyskać więcej informacji, zobacz [rozszerzenia konfiguracji żądanego stanu platformy Azure](dsc-overview.md).
- Konfigurowanie, monitorowanie maszyny wirtualnej za pomocą rozszerzenia maszyny Wirtualnej agenta monitorowania Microsoft. Aby uzyskać więcej informacji, zobacz [łączenie maszyn wirtualnych platformy Azure do dzienników usługi Azure Monitor](../../log-analytics/log-analytics-azure-vm-extension.md).
- Skonfiguruj Maszynę wirtualną platformy Azure przy użyciu programu Chef. Aby uzyskać więcej informacji, zobacz [wdrożenia Automatyzowanie maszyn wirtualnych platformy Azure przy użyciu programu Chef](../windows/chef-automation.md).
- Konfigurowanie, monitorowanie infrastruktury platformy Azure z rozszerzeniem pomocą usługi Datadog. Aby uzyskać więcej informacji, zobacz [blogu pomocą usługi Datadog](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).


Oprócz rozszerzenia procesu rozszerzenie niestandardowego skryptu jest dostępna dla maszyn wirtualnych systemów Windows i Linux. Rozszerzenie niestandardowego skryptu dla Windows umożliwia dowolny skrypt programu PowerShell do uruchamiania na maszynie Wirtualnej. Niestandardowe skrypty są przydatne w przypadku projektowania wdrożenia platformy Azure, które wymagają konfiguracji oprócz jakie natywnych narzędzi platformy Azure może zapewnić. Aby uzyskać więcej informacji, zobacz [rozszerzenia skryptu niestandardowego maszyny Wirtualnej Windows](custom-script-windows.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby obsługiwać rozszerzenia na maszynie Wirtualnej, musisz mieć zainstalowany Agent usługi Windows Azure. Niektóre poszczególne rozszerzenia mają wymagania wstępne, takich jak dostęp do zasobów lub zależności.

### <a name="azure-vm-agent"></a>Agent maszyny wirtualnej platformy Azure

Agent maszyny Wirtualnej platformy Azure zarządza interakcje między Maszyną wirtualną platformy Azure a kontrolerem sieci szkieletowej platformy Azure. Agent maszyny Wirtualnej jest odpowiedzialny za wiele aspektów funkcjonalnych wdrażania i zarządzania nimi maszyn wirtualnych platformy Azure, w tym uruchamianie rozszerzeń maszyn wirtualnych. Agent maszyny Wirtualnej platformy Azure jest preinstalowany w portalu Azure Marketplace, obrazy i można ją zainstalować ręcznie w obsługiwanych systemach operacyjnych. Azure VM Agent for Windows jest znana jako funkcja agentów gości Windows.

Aby uzyskać informacje na temat obsługiwanych systemów operacyjnych i instrukcje dotyczące instalacji, zobacz [agenta maszyny wirtualnej platformy Azure](agent-windows.md).

#### <a name="supported-agent-versions"></a>Wersje obsługiwanych agentach

Aby zapewnić możliwie najlepsze środowisko, istnieją co najmniej wersji agenta. Więcej informacji znajduje się w [tym artykule](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

#### <a name="supported-oses"></a>Obsługiwane systemy operacyjne

Agent gościa Windows działa w wielu systemach operacyjnych, jednak w ramach rozszerzenia ma limit dla systemów operacyjnych z tego rozszerzenia. Więcej informacji znajduje się w [tym artykule](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems
).

Niektóre rozszerzenia nie są obsługiwane we wszystkich systemach operacyjnych i może emitować *51 kodu błędu, nieobsługiwany system operacyjny*. W dokumentacji poszczególnych rozszerzenie obsługi.

#### <a name="network-access"></a>Dostęp do sieci

Pakiety rozszerzeń są pobierane z usługi Azure Storage repozytorium rozszerzeń i przekazywanie stanu rozszerzenia są wysyłane do usługi Azure Storage. Jeśli używasz [obsługiwane](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) wersji agentów, nie trzeba zezwolić na dostęp do usługi Azure Storage w regionie maszyny Wirtualnej, ponieważ agent można używać do przekierowania komunikacji z kontrolerem sieci szkieletowej platformy Azure do komunikacji agenta. Jeśli korzystasz z nieobsługiwanej wersji agenta, należy zezwolić na dostęp ruchu wychodzącego do usługi Azure storage w danym regionie z maszyny Wirtualnej.

> [!IMPORTANT]
> Zablokowanie dostępu do *168.63.129.16* przy użyciu zapory gościa, następnie rozszerzenia kończą się niepowodzeniem niezależnie od powyższych.

Agentów należy używać tylko do pobierania pakietów rozszerzeń i stanu raportowania. Na przykład, czy instalacja rozszerzenia musi pobrać skrypt z usługi GitHub (niestandardowego skryptu) wymagany jest dostęp do usługi Azure Storage (usługa Azure Backup), następnie dodatkowe zapory i sieci zabezpieczeń grupy portów muszą być otwarte. Inne rozszerzenia mają różne wymagania, ponieważ są one aplikacje w ich własnych po prawej stronie. Dla rozszerzeń, które wymagają dostępu do usługi Azure Storage, można zezwolić na dostęp przy użyciu tagów usługi sieciowej grupy zabezpieczeń platformy Azure dla [magazynu](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

Agent gościa Windows nie ma serwera proxy, które obsługują umożliwia przekierowywanie żądań ruchu agenta za pomocą.

## <a name="discover-vm-extensions"></a>Odkryj rozszerzeń maszyn wirtualnych

Wielu różnych rozszerzeń maszyny wirtualnej można używać z maszynami wirtualnymi platformy Azure. Aby wyświetlić pełną listę, należy użyć [Get AzVMExtensionImage](https://docs.microsoft.com/powershell/module/az.compute/get-azvmextensionimage). Poniższy przykład wyświetla listę wszystkich dostępnych rozszerzeń w *WestUS* lokalizacji:

```powershell
Get-AzVmImagePublisher -Location "WestUS" | `
Get-AzVMExtensionImageType | `
Get-AzVMExtensionImage | Select Type, Version
```

## <a name="run-vm-extensions"></a>Uruchamianie rozszerzenia maszyn wirtualnych

Rozszerzenia maszyn wirtualnych platformy Azure, uruchom na istniejących maszynach wirtualnych, co jest przydatne, gdy trzeba wprowadzić zmiany w konfiguracji lub odzyskać łączność już wdrożonej maszyny wirtualnej. Rozszerzenia maszyn wirtualnych dołączany także wdrożeń szablonu usługi Azure Resource Manager. Za pomocą rozszerzeń przy użyciu szablonów usługi Resource Manager, maszyn wirtualnych platformy Azure można wdrożona i skonfigurowana bez interwencji po wdrożeniu.

Następujące metody może służyć do uruchamiania rozszerzenie istniejącej maszyny Wirtualnej.

### <a name="powershell"></a>PowerShell

Istnieje kilka poleceń programu PowerShell do uruchamiania poszczególnych rozszerzeń. Aby wyświetlić listę, należy użyć [Get-Command](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/get-command) i przefiltruj *rozszerzenia*:

```powershell
Get-Command Set-Az*Extension* -Module Az.Compute
```

Dzięki temu dane wyjściowe podobne do następujących:

```powershell
CommandType     Name                                          Version    Source
-----------     ----                                          -------    ------
Cmdlet          Set-AzVMAccessExtension                       4.5.0      Az.Compute
Cmdlet          Set-AzVMADDomainExtension                     4.5.0      Az.Compute
Cmdlet          Set-AzVMAEMExtension                          4.5.0      Az.Compute
Cmdlet          Set-AzVMBackupExtension                       4.5.0      Az.Compute
Cmdlet          Set-AzVMBginfoExtension                       4.5.0      Az.Compute
Cmdlet          Set-AzVMChefExtension                         4.5.0      Az.Compute
Cmdlet          Set-AzVMCustomScriptExtension                 4.5.0      Az.Compute
Cmdlet          Set-AzVMDiagnosticsExtension                  4.5.0      Az.Compute
Cmdlet          Set-AzVMDiskEncryptionExtension               4.5.0      Az.Compute
Cmdlet          Set-AzVMDscExtension                          4.5.0      Az.Compute
Cmdlet          Set-AzVMExtension                             4.5.0      Az.Compute
Cmdlet          Set-AzVMSqlServerExtension                    4.5.0      Az.Compute
Cmdlet          Set-AzVmssDiskEncryptionExtension             4.5.0      Az.Compute
```

W poniższym przykładzie użyto rozszerzenia niestandardowego skryptu, aby pobrać skrypt z repozytorium GitHub na docelowej maszynie wirtualnej, a następnie uruchom skrypt. Aby uzyskać więcej informacji na temat rozszerzenia niestandardowego skryptu, zobacz [Omówienie rozszerzenia niestandardowego skryptu](custom-script-windows.md).

```powershell
Set-AzVMCustomScriptExtension -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" -Name "myCustomScript" `
    -FileUri "https://raw.githubusercontent.com/neilpeterson/nepeters-azure-templates/master/windows-custom-script-simple/support-scripts/Create-File.ps1" `
    -Run "Create-File.ps1" -Location "West US"
```

W poniższym przykładzie rozszerzenie dostępu do maszyny Wirtualnej służy do resetowania hasła administracyjnego maszyny Wirtualnej z systemem Windows hasło tymczasowe. Aby uzyskać więcej informacji na temat rozszerzenia VM Access, zobacz [usługi zresetować Pulpit zdalny na maszynie wirtualnej Windows](../windows/reset-rdp.md). Po uruchomieniu tej należy resetować hasła przy pierwszym logowaniu:

```powershell
$cred=Get-Credential

Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myVMAccess" `
    -Location WestUS -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"
```

`Set-AzVMExtension` Polecenia można uruchomić każde rozszerzenie maszyny Wirtualnej. Aby uzyskać więcej informacji, zobacz [odwołanie do zestawu AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension).


### <a name="azure-portal"></a>Azure Portal

Rozszerzenia maszyn wirtualnych można zastosować do istniejącej maszyny Wirtualnej w witrynie Azure portal. Wybierz maszynę Wirtualną w portalu, wybierz pozycję **rozszerzenia**, a następnie wybierz **Dodaj**. Wybierz rozszerzenie, z listy dostępnych rozszerzeń i postępuj zgodnie z instrukcjami wyświetlanymi w kreatorze.

Instalacja rozszerzenia Microsoft Antimalware w witrynie Azure portal można znaleźć w poniższym przykładzie:

![Instalowanie rozszerzenia ochrony przed złośliwym kodem](./media/features-windows/installantimalwareextension.png)

### <a name="azure-resource-manager-templates"></a>Szablony usługi Azure Resource Manager

Rozszerzenia maszyn wirtualnych można dodać do szablonu usługi Azure Resource Manager i wykonywane przy użyciu wdrażania szablonu. Podczas wdrażania rozszerzenia za pomocą szablonu, można utworzyć w pełni skonfigurowany wdrożeń platformy Azure. Na przykład, następujący kod JSON jest pobierana z Menedżerem zasobów szablon wdraża zestaw maszyn wirtualnych z równoważeniem obciążenia i usługi Azure SQL database, a następnie instaluje aplikację .NET Core na każdej maszynie Wirtualnej. Rozszerzenie maszyny Wirtualnej zajmuje się instalacji oprogramowania.

Aby uzyskać więcej informacji, zobacz [pełny szablon usługi Resource Manager](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

Aby uzyskać więcej informacji na temat tworzenia szablonów usługi Resource Manager, zobacz [szablonów Authoring Azure Resource Manager przy użyciu rozszerzeń maszyn wirtualnych Windows](../windows/template-description.md#extensions).

## <a name="secure-vm-extension-data"></a>Zabezpieczanie danych rozszerzenia maszyny Wirtualnej

Po uruchomieniu rozszerzenia maszyny Wirtualnej, może być konieczne uwzględnienie informacji poufnych, takich jak poświadczenia, nazwy kont magazynu i kluczy dostępu do konta magazynu. Wiele rozszerzeń maszyn wirtualnych obejmują konfiguracji chronionych danych i tylko odszyfrowującego wewnątrz docelowej maszyny Wirtualnej. Każde rozszerzenie ma schemat konkretnej konfiguracji chronionych, a każdy jest szczegółowo opisane w dokumentacji rozszerzenia.

Poniższy przykład pokazuje wystąpienie rozszerzenie niestandardowego skryptu dla Windows. Polecenie do wykonania obejmuje zestaw poświadczeń. W tym przykładzie nie są szyfrowane polecenia do wykonania:

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ],
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

Przenoszenie **polecenie do wykonania** właściwości **chronione** konfiguracja zabezpiecza ciągu wykonywania, jak pokazano w poniższym przykładzie:

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

### <a name="how-do-agents-and-extensions-get-updated"></a>Jak agentów i rozszerzenia zostaje zaktualizowana?

Agenci i rozszerzenia należy udostępnić ten sam mechanizm aktualizacji. Niektóre aktualizacje wymagają dodatkowe reguły zapory.

Gdy aktualizacja jest dostępna, jej jest ona instalowana tylko na maszynie Wirtualnej po zmianie rozszerzenia i inne zmiany modelu maszyny Wirtualnej, takie jak:

- Dyski z danymi
- Rozszerzenia
- Kontener diagnostyki rozruchu
- Wpisy tajne systemu operacyjnego gościa
- Rozmiar maszyny wirtualnej
- Profil sieciowy

Wydawcy aktualizacje były dostępne w regionach w różnym czasie, więc prawdopodobnie masz maszyny wirtualne w różnych regionach w różnych wersjach.

#### <a name="listing-extensions-deployed-to-a-vm"></a>Lista rozszerzeń wdrożone do maszyny Wirtualnej

```powershell
$vm = Get-AzVM -ResourceGroupName "myResourceGroup" -VMName "myVM"
$vm.Extensions | select Publisher, VirtualMachineExtensionType, TypeHandlerVersion
```

```powershell
Publisher             VirtualMachineExtensionType          TypeHandlerVersion
---------             ---------------------------          ------------------
Microsoft.Compute     CustomScriptExtension                1.9
```

#### <a name="agent-updates"></a>Aktualizacje agenta

Agent gościa Windows zawiera tylko *rozszerzenia obsługi kodu*, *kod inicjowania obsługi administracyjnej Windows* jest oddzielony. Można odinstalować agenta gościa Windows. Nie można wyłączyć automatyczne aktualizowanie agenta gościa okna.

*Rozszerzenia obsługi kodu* odpowiada za komunikację przy użyciu usługi Azure Service fabric i obsługi operacji rozszerzenia maszyny Wirtualnej, takie jak zainstalowaniu zgłaszające stan poszczególnych rozszerzeń i aktualizowanie ich usunięcie. Aktualizacje zawierają poprawki zabezpieczeń, poprawki błędów i ulepszenia *rozszerzenia obsługi kodu*.

Aby sprawdzić, której wersji używasz, zobacz [wykrycie zainstalował agenta gościa Windows](agent-windows.md#detect-the-vm-agent).

#### <a name="extension-updates"></a>Aktualizacje rozszerzenia

Po udostępnieniu aktualizacji rozszerzenia agenta gościa Windows pobiera i uaktualnia rozszerzenia. Aktualizacje automatyczne rozszerzenia są albo *pomocnicza* lub *poprawkę*. Można zgodzić się na lub zrezygnować z rozszerzenia *pomocnicza* aktualizacji podczas aprowizacji rozszerzenia. Poniższy przykład pokazuje, jak automatycznie uaktualnić wersje pomocnicze w szablonie usługi Resource Manager za pomocą *autoUpgradeMinorVersion ": ma wartość true,"* :

```json
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
```

Aby uzyskać najnowszą wersję pomocniczą poprawki, zdecydowanie zaleca się zawsze wybierz automatyczną aktualizację we wdrożeniach rozszerzenia. Aktualizacji będących poprawkami, wykonujących poprawki zabezpieczeń lub klucz nie wyraził na nią zgody.

### <a name="how-to-identify-extension-updates"></a>Jak zidentyfikować rozszerzenia aktualizacje

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>Identyfikowanie, jeśli rozszerzenie jest ustawiony za pomocą autoUpgradeMinorVersion na maszynie Wirtualnej

Można wyświetlić w modelu maszyny Wirtualnej rozszerzenie została przygotowana z użyciem "autoUpgradeMinorVersion". Aby sprawdzić, użyj [Get-AzVm](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) i podaj nazwę grupy zasobów i maszynę Wirtualną w następujący sposób:

```powerShell
 $vm = Get-AzVm -ResourceGroupName "myResourceGroup" -VMName "myVM"
 $vm.Extensions
```

Następujące przykładowe dane wyjściowe pokazuje, że *autoUpgradeMinorVersion* ustawiono *true*:

```powershell
ForceUpdateTag              :
Publisher                   : Microsoft.Compute
VirtualMachineExtensionType : CustomScriptExtension
TypeHandlerVersion          : 1.9
AutoUpgradeMinorVersion     : True
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>Identyfikowanie po wystąpieniu autoUpgradeMinorVersion

Aby zobaczyć, kiedy wystąpiła aktualizacja do rozszerzenia, przejrzyj agenta dzienniki na maszynie Wirtualnej o *C:\WindowsAzure\Logs\WaAppAgent.log*

W poniższym przykładzie maszyna wirtualna ma *Microsoft.Compute.CustomScriptExtension 1.8* zainstalowane. Poprawka była dostępna do wersji *1.9*:

```powershell
[INFO]  Getting plugin locations for plugin 'Microsoft.Compute.CustomScriptExtension'. Current Version: '1.8', Requested Version: '1.9'
[INFO]  Auto-Upgrade mode. Highest public version for plugin 'Microsoft.Compute.CustomScriptExtension' with requested version: '1.9', is: '1.9'
```

## <a name="agent-permissions"></a>Uprawnienia agenta

Do wykonywania swoich zadań, agent musi być uruchamiane jako *systemu lokalnego*.

## <a name="troubleshoot-vm-extensions"></a>Rozwiązywanie problemów z rozszerzeniami maszyn wirtualnych

Każde rozszerzenie maszyny Wirtualnej może mieć Rozwiązywanie problemów w określone rozszerzenia. Na przykład korzystając z rozszerzenia niestandardowego skryptu, szczegóły wykonywania skryptu można znaleźć lokalnie na maszynie Wirtualnej, w którym zostało uruchomione rozszerzenia. Wszystkie kroki rozwiązywania problemów specyficznych dla rozszerzenia są szczegółowo opisane w dokumentacji specyficzne dla rozszerzenia.

Następujące kroki dotyczą wszystkich rozszerzeń maszyn wirtualnych.

1. Aby sprawdzić w dzienniku agenta gościa Windows, Przyjrzyj się działanie po rozszerzenie zostało aprowizowane w *C:\WindowsAzure\Logs\WaAppAgent.txt*

2. Sprawdź dzienniki rzeczywiste rozszerzenia, aby uzyskać więcej szczegółów w *C:\WindowsAzure\Logs\Plugins\<extensionName >*

3. Sprawdź rozszerzenia właściwą dokumentację sekcje dotyczące rozwiązywania problemów dla kodów błędu, znane problemy dotyczące itp.

4. Sprawdź dzienniki systemu. Sprawdź, czy inne operacje, które mogą mieć zakłócać rozszerzenie, np. długotrwałe instalacja innej aplikacji, która wymaganego wyłącznego dostępu menedżera.

### <a name="common-reasons-for-extension-failures"></a>Typowe przyczyny błędów rozszerzenia

1. Rozszerzenia mają 20 minut do uruchomienia (wyjątki są rozszerzenia CustomScript, Chef i DSC, które mają 90 minut). Jeśli wdrożenie przekroczy ten czas, jest oznaczana do przekroczenia limitu czasu. Przyczyną tego może być z powodu zasobów maszyn wirtualnych z innych maszyn wirtualnych konfiguracji/uruchamiania zadania zużywające wysokiej ilości zasobów, podczas gdy rozszerzenie jest próby aprowizacji.

2. Minimalne wymagania wstępne nie zostały spełnione. Niektóre rozszerzenia mają zależności jednostek SKU maszyny Wirtualnej, taką jak obrazy HPC. Rozszerzenia mogą wymagać pewnych sieci wymagania dotyczące dostępu, takich jak komunikacja do usługi Azure Storage lub usług publicznych. Inne przykłady może być dostęp do repozytoriów pakietu, wyczerpuje się miejsce na dysku lub ograniczenia zabezpieczeń.

3. Dostęp do Menedżera wyłączne pakietu. W niektórych przypadkach mogą wystąpić długotrwałe konfiguracji maszyny Wirtualnej, a instalacja rozszerzenia usługi powodujące konflikt, których potrzebują wyłączny dostęp do Menedżera pakietów.

### <a name="view-extension-status"></a>Wyświetl stan rozszerzenia

Po wykonaniu rozszerzenia maszyny Wirtualnej względem maszyny Wirtualnej za pomocą [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) zostać zwrócony stan rozszerzenia. *Podstany [0]* pokazują aprowizacja rozszerzenia zakończyło się pomyślnie, co oznacza jego pomyślne wdrożone na maszynie Wirtualnej, ale nie można wykonać rozszerzenia wewnątrz maszyny Wirtualnej, *podstany [1]* .

```powershell
Get-AzVM -ResourceGroupName "myResourceGroup" -VMName "myVM" -Status
```

Dane wyjściowe będą podobne do następujących przykładowych danych wyjściowych:

```powershell
Extensions[0]           :
  Name                  : CustomScriptExtension
  Type                  : Microsoft.Compute.CustomScriptExtension
  TypeHandlerVersion    : 1.9
  Substatuses[0]        :
    Code                : ComponentStatus/StdOut/succeeded
    Level               : Info
    DisplayStatus       : Provisioning succeeded
    Message             : Windows PowerShell \nCopyright (C) Microsoft Corporation. All rights reserved.\n
  Substatuses[1]        :
    Code                : ComponentStatus/StdErr/succeeded
    Level               : Info
    DisplayStatus       : Provisioning succeeded
    Message             : The argument 'cseTest%20Scriptparam1.ps1' to the -File parameter does not exist. Provide the path to an existing '.ps1' file as an argument to the

-File parameter.
  Statuses[0]           :
    Code                : ProvisioningState/failed/-196608
    Level               : Error
    DisplayStatus       : Provisioning failed
    Message             : Finished executing command
```

Stan wykonywania rozszerzenia można także znaleźć w witrynie Azure portal. Aby wyświetlić stan rozszerzenia, wybierz maszynę Wirtualną, wybrać **rozszerzenia**, następnie wybierz żądany rozszerzenia.

### <a name="rerun-vm-extensions"></a>Uruchom ponownie rozszerzeń maszyn wirtualnych

Można wykluczyć sytuacji, w których trzeba można ponownie uruchomić rozszerzenia maszyny Wirtualnej. Możesz ponownie uruchomić rozszerzenia, usuwając go, a następnie ponowne uruchomienie rozszerzenia za pomocą metody wykonywania wybranych przez użytkownika. Aby usunąć rozszerzenie, należy użyć [AzVMExtension Usuń](https://docs.microsoft.com/powershell/module/az.compute/Remove-AzVMExtension) w następujący sposób:

```powershell
Remove-AzVMExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myExtensionName"
```

Możesz również usunąć rozszerzenie w witrynie Azure portal w następujący sposób:

1. Wybierz maszynę Wirtualną.
2. Wybierz **rozszerzenia**.
3. Wybierz żądane rozszerzenie.
4. Wybierz **odinstalować**.

## <a name="common-vm-extensions-reference"></a>Częsta rozszerzenia maszyny Wirtualnej
| Nazwa rozszerzenia | Opis | Więcej informacji |
| --- | --- | --- |
| Rozszerzenie niestandardowego skryptu dla Windows |Uruchamianie skryptów na maszynie wirtualnej platformy Azure |[Rozszerzenie niestandardowego skryptu dla Windows](custom-script-windows.md) |
| Rozszerzenie DSC dla Windows |Rozszerzenie DSC (Desired State Configuration) programu PowerShell |[Rozszerzenie DSC dla Windows](dsc-overview.md) |
| Rozszerzenie Diagnostyki Azure |Zarządzanie diagnostyki platformy Azure |[Rozszerzenie diagnostyki platformy Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Rozszerzenie dostępu do maszyny Wirtualnej platformy Azure |Zarządzanie użytkownikami i poświadczeń |[Rozszerzenie dostępu do maszyny Wirtualnej dla systemu Linux](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat rozszerzeń maszyn wirtualnych, zobacz [omówienie rozszerzeń i funkcji maszyn wirtualnych platformy Azure](overview.md).
