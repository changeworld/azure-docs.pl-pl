---
title: Azure rozszerzeń maszyny Wirtualnej i funkcje systemu Windows | Dokumentacja firmy Microsoft
description: Dowiedz się, jakie rozszerzenia są dostępne dla maszyn wirtualnych platformy Azure, pogrupowane według co ich Podaj puli zasobów i zwiększyć.
services: virtual-machines-windows
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 999d63ee-890e-432e-9391-25b3fc6cde28
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: danis
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e9e147e2cbe5ff42562d6fcfab62460df48f3d65
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2018
ms.locfileid: "34809730"
---
# <a name="virtual-machine-extensions-and-features-for-windows"></a>Rozszerzenia maszyn wirtualnych i funkcje systemu Windows

Rozszerzenia maszyny wirtualnej platformy Azure (VM) są małe aplikacji, które mają po wdrożeniu i automatyzację zadań na maszynach wirtualnych Azure. Na przykład, jeśli maszyna wirtualna wymaga ochrony oprogramowania antywirusowego, instalacja oprogramowania lub uruchamiania skryptu wewnątrz niej można użyć rozszerzenia maszyny Wirtualnej. Rozszerzenia maszyny Wirtualnej platformy Azure można uruchomić z wiersza polecenia platformy Azure, programu PowerShell, szablonów usługi Azure Resource Manager i portalu Azure. Rozszerzenia mogą być powiązane z nowego wdrożenia maszyny Wirtualnej lub uruchomienia dowolnego istniejącego systemu.

Ten artykuł zawiera omówienie rozszerzeń maszyny Wirtualnej, wymagań wstępnych dotyczących używania rozszerzenia maszyny Wirtualnej platformy Azure i instrukcje dotyczące wykrywania, zarządzanie i usuwanie rozszerzenia maszyny Wirtualnej. Ten artykuł zawiera informacje uogólniony wielu rozszerzeń maszyny Wirtualnej nie są dostępne, każde z nich potencjalnie unikatowych konfiguracji. Szczegółów dotyczących poszczególnych rozszerzeń można znaleźć w każdy dokument specyficzne dla poszczególnych rozszerzenia.

## <a name="use-cases-and-samples"></a>Przypadki użycia i przykłady

Dostępnych jest kilka różnych rozszerzeń maszyny Wirtualnej platformy Azure, każde z nich określony przypadek użycia. Oto niektóre przykłady:

- Dotyczą konfiguracji środowiska PowerShell żądany stan maszyny Wirtualnej z rozszerzenia DSC dla systemu Windows. Aby uzyskać więcej informacji, zobacz [Azure żądany stan konfiguracji rozszerzenia](dsc-overview.md).
- Konfigurowanie monitorowania maszynę Wirtualną za pomocą rozszerzenia maszyny Wirtualnej agenta monitorowania firmy Microsoft. Aby uzyskać więcej informacji, zobacz [łączenie maszyn wirtualnych platformy Azure do analizy dzienników](../../log-analytics/log-analytics-azure-vm-extension.md).
- Konfigurowanie maszyny Wirtualnej platformy Azure przy użyciu Chef. Aby uzyskać więcej informacji, zobacz [wdrażania automatyzacji maszyny Wirtualnej platformy Azure z Chef](../windows/chef-automation.md).
- Skonfiguruj Monitorowanie infrastruktury platformy Azure z rozszerzeniem Datadog. Aby uzyskać więcej informacji, zobacz [Datadog blog](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).


Oprócz rozszerzenia procesu rozszerzenia niestandardowego skryptu jest dostępna dla maszyn wirtualnych w systemach Windows i Linux. Rozszerzenia niestandardowego skryptu dla systemu Windows umożliwia dowolny skrypt programu PowerShell do uruchamiania na maszynie Wirtualnej. Niestandardowe skrypty są przydatne w przypadku projektowania wdrożeń platformy Azure, które wymagają konfiguracji oprócz zapewniają jakie natywnego narzędzia Azure. Aby uzyskać więcej informacji, zobacz [rozszerzenie skryptu niestandardowego maszyny Wirtualnej systemu Windows](custom-script-windows.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby zapewnić obsługę rozszerzeń na maszynie Wirtualnej, należy z zainstalowanym agentem systemu Linux platformy Azure. Niektóre poszczególnych rozszerzeń są spełnione wymagania wstępne, takich jak dostęp do zasobów lub zależności.

### <a name="azure-vm-agent"></a>Agent maszyny wirtualnej platformy Azure

Agent maszyny Wirtualnej platformy Azure zarządza interakcje między maszyny Wirtualnej platformy Azure i kontroler sieci szkieletowej Azure. Agent maszyny Wirtualnej jest odpowiedzialny za dużo aspekty funkcjonalne wdrażania i zarządzania nimi maszynach wirtualnych platformy Azure, łącznie z rozszerzeniami maszyny Wirtualnej. Agent maszyny Wirtualnej platformy Azure jest preinstalowany na portalu Azure Marketplace obrazów i można zainstalować ręcznie w obsługiwanych systemach operacyjnych. Azure VM Agent dla systemu Windows jest nazywana agenta gościa z systemem Windows.

Aby uzyskać informacje dotyczące obsługiwanych systemów operacyjnych i instrukcje dotyczące instalacji, zobacz [agenta maszyny wirtualnej platformy Azure](agent-windows.md).

#### <a name="supported-agent-versions"></a>Agent obsługiwane wersje

Aby zapewnić najlepsze możliwe środowisko, istnieje co najmniej wersji agenta. Więcej informacji znajduje się w [tym artykule](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

#### <a name="supported-oses"></a>Obsługiwane systemy operacyjne

Agent gościa Windows działa na wielu systemów operacyjnych, jednak w ramach rozszerzenia ma limit dla systemów operacyjnych tego rozszerzenia. Aby uzyskać więcej informacji, zobacz [w tym artykule] (https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems ).

Niektóre rozszerzenia nie są obsługiwane we wszystkich systemach operacyjnych i może emitować *51 kodu błędu, "Nieobsługiwany system operacyjny"*. Sprawdź w dokumentacji poszczególnych rozszerzenia obsługi.

#### <a name="network-access"></a>Dostęp do sieci

Pakiety rozszerzeń są pobierane z repozytorium rozszerzenia usługi Azure Storage, a rozszerzenie przekazywania stanie są wysyłane do usługi Azure Storage. Jeśli używasz [obsługiwane](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) wersji agentów, nie trzeba zezwolić na dostęp do usługi Azure Storage w regionie maszyny Wirtualnej, jak można użyć agenta do przekierowania komunikacji z kontrolerem sieci szkieletowej Azure komunikacji agenta. Jeśli używany jest nie obsługiwaną wersję agenta, należy zezwolić wychodzący dostęp do magazynu Azure w tym regionie z maszyny Wirtualnej.

> [!IMPORTANT]
> Zablokowanie dostępu do *168.63.129.1* za pomocą zapory gościa, następnie rozszerzeń kończą się niepowodzeniem niezależnie od powyższego.

Aby pobrać pakiety rozszerzeń i raportowania stanu można tylko agentów. Na przykład, jeśli instalacja rozszerzenia musi pobrać skrypt z serwisu GitHub (niestandardowego skryptu) lub wymagany jest dostęp do usługi Azure Storage (kopia zapasowa Azure), następnie dodatkowe zapory i sieci zabezpieczeń grupy porty muszą być otwarte. Różne rozszerzenia mają różne wymagania, ponieważ są one aplikacji we własnym imieniu. Dla rozszerzeń, które wymagają dostępu do usługi Azure Storage, można zezwolić na dostęp przy użyciu tagów usługi NSG Azure dla [magazynu](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

Agent gościa z systemem Windows nie ma obsługi można przekierować żądania ruchu agenta za pomocą serwera proxy.

## <a name="discover-vm-extensions"></a>Odnajdywanie rozszerzeń maszyny Wirtualnej

Wiele różnych rozszerzeń maszyny Wirtualnej są dostępne do użycia z maszynami wirtualnymi Azure. Aby wyświetlić pełną listę, należy użyć [Get-AzureRmVMExtensionImage](/powershell/module/azurerm.compute/get-azurermvmextensionimage). Poniższy przykład zawiera listę wszystkich dostępnych rozszerzeń w *WestUS* lokalizacji:

```powershell
Get-AzureRmVmImagePublisher -Location "WestUS" | `
Get-AzureRmVMExtensionImageType | `
Get-AzureRmVMExtensionImage | Select Type, Version
```

## <a name="run-vm-extensions"></a>Uruchom rozszerzeń maszyny Wirtualnej

Rozszerzenia maszyny Wirtualnej platformy Azure Uruchom na istniejących maszynach wirtualnych, co jest przydatne, gdy trzeba wprowadzić zmiany w konfiguracji lub Przywróć łączność w przypadku wdrożonej maszyny Wirtualnej. Rozszerzenia maszyny Wirtualnej dołączany także wdrażanie szablonów usługi Azure Resource Manager. Przy użyciu rozszerzeń z szablonami usługi Resource Manager, można wdrożyć i konfigurować bez interwencji po wdrożeniu maszynach wirtualnych platformy Azure.

Następujących metod można uruchomić rozszerzenia dla istniejącej maszyny Wirtualnej.

### <a name="powershell"></a>PowerShell

Istnieje kilka poleceń programu PowerShell do uruchamiania poszczególnych rozszerzeń. Aby wyświetlić listę, należy użyć [Get-Command](/powershell/module/microsoft.powershell.core/get-command) i odfiltrować *rozszerzenia*:

```powershell
Get-Command Set-AzureRM*Extension* -Module AzureRM.Compute
```

Zapewnia to dane wyjściowe podobne do następującego:

```powershell
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Set-AzureRmVMAccessExtension                       4.5.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMADDomainExtension                     4.5.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMAEMExtension                          4.5.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMBackupExtension                       4.5.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMBginfoExtension                       4.5.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMChefExtension                         4.5.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMCustomScriptExtension                 4.5.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMDiagnosticsExtension                  4.5.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMDiskEncryptionExtension               4.5.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMDscExtension                          4.5.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMExtension                             4.5.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMSqlServerExtension                    4.5.0      AzureRM.Compute
Cmdlet          Set-AzureRmVmssDiskEncryptionExtension             4.5.0      AzureRM.Compute
```

W poniższym przykładzie użyto rozszerzenia niestandardowego skryptu do pobrania skryptu z repozytorium GitHub na docelowej maszynie wirtualnej, a następnie uruchom skrypt. Aby uzyskać więcej informacji dotyczących rozszerzenia niestandardowego skryptu, zobacz [Omówienie rozszerzenia niestandardowego skryptu](custom-script-windows.md).

```powershell
Set-AzureRmVMCustomScriptExtension -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" -Name "myCustomScript" `
    -FileUri "https://raw.githubusercontent.com/neilpeterson/nepeters-azure-templates/master/windows-custom-script-simple/support-scripts/Create-File.ps1" `
    -Run "Create-File.ps1" -Location "West US"
```

W poniższym przykładzie rozszerzenia dostępu do maszyny Wirtualnej służy do resetowania hasła administracyjnego maszyny wirtualnej systemu Windows do tymczasowego hasła. Aby uzyskać więcej informacji dotyczących rozszerzenia dostępu do maszyny Wirtualnej, zobacz [usługi pulpitu zdalnego resetowania na maszynie wirtualnej Windows](../windows/reset-rdp.md). Po uruchomieniu tej powinni resetować hasła przy pierwszym logowaniu:

```powershell
$cred=Get-Credential

Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myVMAccess" `
    -Location WestUS -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"
```

`Set-AzureRmVMExtension` Polecenia można uruchomić wszystkie rozszerzenia maszyny Wirtualnej. Aby uzyskać więcej informacji, zobacz [odwołania zestawu AzureRmVMExtension](https://msdn.microsoft.com/library/mt603745.aspx).


### <a name="azure-portal"></a>Azure Portal

Rozszerzenia maszyny Wirtualnej może odnosić się do istniejącej maszyny Wirtualnej za pośrednictwem portalu Azure. Wybierz maszynę Wirtualną w portalu, wybierz **rozszerzenia**, a następnie wybierz pozycję **Dodaj**. Wybierz rozszerzenia, z listy dostępnych rozszerzeń i postępuj zgodnie z instrukcjami w kreatorze.

W poniższym przykładzie przedstawiono instalacja rozszerzenia Microsoft Antimalware z portalu Azure:

![Zainstaluj rozszerzenie ochrony przed złośliwym oprogramowaniem](./media/features-windows/installantimalwareextension.png)

### <a name="azure-resource-manager-templates"></a>Szablony usługi Azure Resource Manager

Rozszerzenia maszyny Wirtualnej można dodać do szablonu usługi Azure Resource Manager i wykonywane przy użyciu wdrażania szablonu. Podczas wdrażania rozszerzenia za pomocą szablonu można tworzyć wdrożeń platformy Azure w pełni skonfigurowany. Na przykład następujący kod JSON jest pobierana z Menedżerem zasobów szablonu wdraża zestaw maszyn wirtualnych z równoważeniem obciążenia i bazy danych Azure SQL, a następnie zainstaluje aplikację .NET Core na każdej maszynie Wirtualnej. Rozszerzenia maszyny Wirtualnej zapewnia obsługę instalacji oprogramowania.

Aby uzyskać więcej informacji, zobacz [pełne szablonu usługi Resource Manager](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

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

Aby uzyskać więcej informacji na temat tworzenia szablonów usługi Resource Manager, zobacz [szablony Authoring Azure Resource Manager z rozszerzeniami maszyny Wirtualnej systemu Windows](../windows/template-description.md#extensions).

## <a name="secure-vm-extension-data"></a>Zabezpieczanie danych rozszerzenia maszyny Wirtualnej

Po uruchomieniu rozszerzenia maszyny Wirtualnej, może być konieczne jest stosowanie poufne informacje, takie jak poświadczeń, nazwy konta magazynu i klucze dostępu do konta magazynu. Wiele rozszerzeń maszyny Wirtualnej obejmują konfiguracji chronionych danych i tylko odszyfrowującego wewnątrz docelowego maszyny Wirtualnej. Każde rozszerzenie ma schematu określonej konfiguracji chronionych, a każdy szczegółowo opisano w dokumentacji konkretnego rozszerzenia.

W poniższym przykładzie przedstawiono wystąpienia rozszerzenia niestandardowego skryptu dla systemu Windows. Wykonanie polecenia zawiera zestaw poświadczeń. Wykonanie polecenia nie są szyfrowane w tym przykładzie:

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

Przenoszenie **polecenie do wykonania** właściwości **chronione** konfiguracja zabezpiecza ciąg wykonywania, jak pokazano w poniższym przykładzie:

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

### <a name="how-do-agents-and-extensions-get-updated"></a>Jak agentów i rozszerzenia aktualizowany?

Agenci i rozszerzenia współużytkować ten sam mechanizm aktualizacji. Niektóre aktualizacje nie wymagają reguł zapory dodatkowe.

Po udostępnieniu aktualizacji jest tylko zainstalowany na Maszynie wirtualnej po zmianie rozszerzenia i inne zmiany modelu maszyny Wirtualnej, takie jak:

- Dyski z danymi
- Rozszerzenia
- Kontener diagnostyki rozruchu
- Klucze tajne systemu operacyjnego gościa
- Rozmiar maszyny wirtualnej
- Profil sieci

Wydawcy aktualizacje były dostępne dla regionów w różnym czasie, co może mieć maszyn wirtualnych w różnych regionach w różnych wersjach.

#### <a name="listing-extensions-deployed-to-a-vm"></a>Lista rozszerzeń wdrożone na maszynie Wirtualnej

```powershell
$vm = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -VMName "myVM"
$vm.Extensions | select Publisher, VirtualMachineExtensionType, TypeHandlerVersion
```

```powershell
Publisher             VirtualMachineExtensionType          TypeHandlerVersion
---------             ---------------------------          ------------------
Microsoft.Compute     CustomScriptExtension                1.9
```

#### <a name="agent-updates"></a>Agent aktualizacji

Agent gościa Windows zawiera tylko *rozszerzenia obsługi kodu*, *kodu inicjowania obsługi systemu Windows* jest oddzielona. Można odinstalować agenta gościa z systemem Windows. Nie można wyłączyć automatyczne aktualizowanie agenta gościa okna.

*Rozszerzenia obsługi kodu* odpowiada do komunikowania się z sieci szkieletowej Azure i obsługi operacji rozszerzenia maszyny Wirtualnej, takie jak instalacji raportowania stanu poszczególnych rozszerzeń i aktualizowanie ich usunięcie. Aktualizacji zawiera poprawki zabezpieczeń, poprawki i udoskonalenia *rozszerzenia obsługi kodu*.

Aby sprawdzić wersję, jakie są uruchomione, zobacz [wykrywanie zainstalowany Agent gościa Windows](agent-windows.md#detect-the-vm-agent).

#### <a name="extension-updates"></a>Aktualizacje rozszerzeń

Po udostępnieniu aktualizacji rozszerzenia agenta gościa Windows pobiera i uaktualnia rozszerzenia. Aktualizacje automatyczne rozszerzenia są albo *pomocnicza* lub *poprawkę*. Można włączyć lub zrezygnować z rozszerzeniami *pomocnicza* aktualizacji podczas obsługi administracyjnej rozszerzenia. Poniższy przykład pokazuje, jak automatycznie uaktualnić wersje pomocnicze w szablonie usługi Resource Manager z *autoUpgradeMinorVersion ": ma wartość true,"*:

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

Aby uzyskać najnowsze poprawki wersji pomocniczej, zdecydowanie zaleca się zawsze wybrać automatyczne aktualizacje w ramach wdrożeń rozszerzenia. Nie można korzystać z aktualizacji będących poprawkami, zawierających poprawki zabezpieczeń lub klucza.

### <a name="how-to-identify-extension-updates"></a>Jak zidentyfikować rozszerzenia aktualizacji

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>Identyfikowanie, jeśli rozszerzenie jest ustawiona z autoUpgradeMinorVersion na maszynie Wirtualnej

Można wyświetlić z modelu maszyny Wirtualnej, jeśli rozszerzenie zostało udostępnione "autoUpgradeMinorVersion". Aby sprawdzić, użyj [Get-AzureRmVm](/powershell/module/azurerm.compute/get-azurermvm) i podaj nazwę grupy zasobów i maszyn wirtualnych w następujący sposób:

```powerShell
 $vm = Get-AzureRmVm -ResourceGroupName "myResourceGroup" -VMName "myVM"
 $vm.Extensions
```

Następujące przykładowe dane wyjściowe wskazuje, że *autoUpgradeMinorVersion* ustawiono *true*:

```powershell
ForceUpdateTag              :
Publisher                   : Microsoft.Compute
VirtualMachineExtensionType : CustomScriptExtension
TypeHandlerVersion          : 1.9
AutoUpgradeMinorVersion     : True
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>Identyfikowanie problemu autoUpgradeMinorVersion

Aby Przejrzyj agenta, zobacz wystąpienia aktualizacji do rozszerzenia dzienniki na maszynie Wirtualnej w *C:\WindowsAzure\Logs\WaAppAgent.log*

W poniższym przykładzie maszyna wirtualna ma *Microsoft.Compute.CustomScriptExtension 1.8* zainstalowane. Poprawka był dostępny do wersji *1.9*:

```powershell
[INFO]  Getting plugin locations for plugin 'Microsoft.Compute.CustomScriptExtension'. Current Version: '1.8', Requested Version: '1.9'
[INFO]  Auto-Upgrade mode. Highest public version for plugin 'Microsoft.Compute.CustomScriptExtension' with requested version: '1.9', is: '1.9'
```

## <a name="agent-permissions"></a>Uprawnienia agenta

Aby wykonać jego zadań podrzędnych, agent musi być uruchamiane jako *systemu lokalnego*.

## <a name="troubleshoot-vm-extensions"></a>Rozwiązywanie problemów z rozszerzeń maszyny Wirtualnej

Każde rozszerzenie maszyny Wirtualnej może być rozwiązywania problemów z określonego rozszerzenia. Na przykład użycie skryptu niestandardowego rozszerzenia skryptu wykonywania szczegóły można znaleźć lokalnie na maszynie Wirtualnej, gdy rozszerzenie zostało uruchomione. Wszystkie kroki rozwiązywania problemów specyficzne dla rozszerzenia są szczegółowo opisane w dokumentacji specyficzne dla rozszerzenia.

Poniższe kroki dotyczą wszystkich rozszerzeń maszyny Wirtualnej.

1. Aby, sprawdź dziennik agenta gościa z systemem Windows, spójrz na działanie, gdy rozszerzenie został aprowizowany w *C:\WindowsAzure\Logs\WaAppAgent.txt*

2. Szczegółowe informacje w dzienniku rzeczywiste rozszerzenia *C:\WindowsAzure\Logs\Plugins\<extensionName >*

3. Sprawdź rozszerzenia szczegółowej dokumentacji sekcje dotyczące rozwiązywania problemów dla kody błędów, znane problemy itp.

4. Sprawdź dzienniki systemu. Sprawdź, czy inne operacje, które mogą mieć zakłócać rozszerzenia, takie jak długo działające instalacja innej aplikacji, co wymagało Menedżera wyłącznego dostępu.

### <a name="common-reasons-for-extension-failures"></a>Najczęstsze przyczyny niepowodzeń rozszerzenia

1. Rozszerzenia mają 20 minut do uruchomienia (wyjątki są rozszerzenia CustomScript, Chef i DSC zainstalowanego w ciągu 90 minut). Jeśli wdrożenie przekroczy ten czas, jest oznaczony jako przekroczenie limitu czasu. Przyczyną tego może być spowodowane zasobów maszyn wirtualnych, inne maszyny Wirtualnej konfiguracji/uruchamiania zadania zużywające wysokiej ilości zasobów podczas, gdy rozszerzenie jest w trakcie udostępniania.

2. Minimalne wymagania wstępne nie zostały spełnione. Niektóre rozszerzenia są zależne na jednostki SKU maszyny Wirtualnej, takich jak obrazy HPC. Rozszerzenia mogą wymagać pewnych sieci wymagania dostępu, na przykład komunikowania się z magazynu Azure lub usługi publiczne. Inne przykłady może być dostęp do repozytoriów pakietu kończy się wolne miejsce na dysku lub ograniczeń zabezpieczeń.

3. Wyłącznego dostępu menedżera. W niektórych przypadkach może się pojawić długotrwała konfiguracji maszyny Wirtualnej i instalacja rozszerzenia powodujące konflikt, których potrzebują wyłącznego dostępu do Menedżera pakietów.

### <a name="view-extension-status"></a>Wyświetl stan rozszerzenia

Po uruchomieniu rozszerzenia maszyny Wirtualnej dla maszyny Wirtualnej, użyj [Get AzureRmVM ](/powershell/module/azurerm.compute/get-azurermvm) ma zostać zwrócony stan rozszerzenia. *Podstany [0]* wskazuje, że rozszerzenia inicjowania obsługi administracyjnej zakończyło się pomyślnie, co oznacza jego pomyślne wdrożone na maszynie wirtualnej, ale nie można wykonać rozszerzenia w ramach maszyny Wirtualnej, *podstany [1]*.

```powershell
Get-AzureRmVM -ResourceGroupName "myResourceGroup" -VMName "myVM" -Status
```

Wynik jest podobny do następującego przykładowe dane wyjściowe:

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

Stan wykonania rozszerzenia można znaleźć w portalu Azure. Aby wyświetlić stan rozszerzenia, wybierz maszynę Wirtualną, wybrać **rozszerzenia**, następnie wybierz żądany rozszerzenia.

### <a name="rerun-vm-extensions"></a>Uruchom ponownie rozszerzeń maszyny Wirtualnej

Może to być przypadków, w których trzeba można ponownie uruchomić rozszerzenia maszyny Wirtualnej. Możesz ponownie uruchomić rozszerzenia, usuwając go i następnie ponowne uruchomienie rozszerzenia metodą wykonywania wybranych przez użytkownika. Aby usunąć rozszerzenie, należy użyć [AzureRmVMExtension Usuń](/powershell/module/AzureRM.Compute/Remove-AzureRmVMExtension) w następujący sposób:

```powershell
Remove-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myExtensionName"
```

Można również usunąć rozszerzenie w portalu Azure w następujący sposób:

1. Wybierz maszynę Wirtualną.
2. Wybierz **rozszerzenia**.
3. Wybierz żądane rozszerzenie.
4. Wybierz **odinstalować**.

## <a name="common-vm-extensions-reference"></a>Typowe odwołanie do rozszerzenia maszyny Wirtualnej
| Nazwa rozszerzenia | Opis | Więcej informacji |
| --- | --- | --- |
| Niestandardowe rozszerzenie skryptu dla systemu Windows |Uruchom skrypty przed maszyny wirtualnej platformy Azure |[Niestandardowe rozszerzenie skryptu dla systemu Windows](custom-script-windows.md) |
| Rozszerzenia konfiguracji DSC dla systemu Windows |Rozszerzenia DSC (konfiguracji żądanego stanu) programu PowerShell |[Rozszerzenia konfiguracji DSC dla systemu Windows](dsc-overview.md) |
| Rozszerzenie Diagnostyki Azure |Zarządzanie Diagnostyka Azure |[Rozszerzenie diagnostyki platformy Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Rozszerzenia dostępu do maszyny Wirtualnej platformy Azure |Zarządzaj użytkownikami i poświadczenia |[Rozszerzenia dostępu do maszyny Wirtualnej dla systemu Linux](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat rozszerzeń maszyny Wirtualnej, zobacz [maszyny wirtualnej platformy Azure — omówienie rozszerzeń i funkcji](overview.md).