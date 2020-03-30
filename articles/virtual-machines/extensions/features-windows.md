---
title: Rozszerzenia i funkcje maszyn wirtualnych platformy Azure dla systemu Windows
description: Dowiedz się, jakie rozszerzenia są dostępne dla maszyn wirtualnych platformy Azure, pogrupowane według tego, co zapewniają lub ulepszają.
services: virtual-machines-windows
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 999d63ee-890e-432e-9391-25b3fc6cde28
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: akjosh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cd6439bf1b1f52b8e63819e8e519fc4971d1bc2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066851"
---
# <a name="virtual-machine-extensions-and-features-for-windows"></a>Rozszerzenia i funkcje maszyn wirtualnych dla systemu Windows

Rozszerzenia maszyny wirtualnej platformy Azure to małe aplikacje, które zapewniają konfigurację po wdrożeniu i zadania automatyzacji na maszynach wirtualnych platformy Azure. Na przykład jeśli maszyna wirtualna wymaga instalacji oprogramowania, ochrony antywirusowej lub uruchomienia skryptu w swoim środowisku, można użyć rozszerzenia maszyny wirtualnej. Rozszerzenia maszyn wirtualnych platformy Azure można uruchamiać przy użyciu interfejsu wiersza polecenia platformy Azure, programu PowerShell, szablonów usługi Azure Resource Manager i witryny Azure Portal. Rozszerzenia można połączyć w pakiecie z nowym wdrożeniem nowej maszyny wirtualnej lub uruchamiać w dowolnym istniejącym systemie.

Ten artykuł zawiera omówienie rozszerzeń maszyn wirtualnych, wymagania wstępne dotyczące używania rozszerzeń maszyn wirtualnych platformy Azure oraz wskazówki dotyczące wykrywania rozszerzeń maszyn wirtualnych, zarządzania nimi i ich usuwania. Ten artykuł zawiera uogólnione informacje, ponieważ dostępnych jest wiele rozszerzeń maszyn wirtualnych, z których każdy ma potencjalnie unikatową konfigurację. Szczegóły specyficzne dla rozszerzenia można znaleźć w każdym dokumencie specyficznym dla poszczególnych rozszerzeń.

 

## <a name="use-cases-and-samples"></a>Przypadki użycia i próbki

Dostępnych jest kilka różnych rozszerzeń maszyn wirtualnych platformy Azure, z których każdy ma określony przypadek użycia. Oto niektóre przykłady:

- Zastosuj konfiguracje żądanego stanu programu PowerShell do maszyny Wirtualnej z rozszerzeniem DSC dla systemu Windows. Aby uzyskać więcej informacji, zobacz [rozszerzenie konfiguracji żądanego stanu platformy Azure](dsc-overview.md).
- Skonfiguruj monitorowanie maszyny Wirtualnej z rozszerzeniem maszyny Wirtualnej agenta usługi Log Analytics Agent. Aby uzyskać więcej informacji, zobacz [Łączenie maszyn wirtualnych platformy Azure z dziennikami usługi Azure Monitor](../../log-analytics/log-analytics-azure-vm-extension.md).
- Konfigurowanie maszyny Wirtualnej platformy Azure przy użyciu programu Chef. Aby uzyskać więcej informacji, zobacz [Automatyzacja wdrażania maszyn wirtualnych platformy Azure za pomocą programu Chef](../../chef/chef-automation.md).
- Skonfiguruj monitorowanie infrastruktury platformy Azure za pomocą rozszerzenia Datadog. Aby uzyskać więcej informacji, zobacz [blog Datadog](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).


Oprócz rozszerzeń specyficznych dla procesu rozszerzenie skryptu niestandardowego jest dostępne zarówno dla maszyn wirtualnych systemu Windows, jak i systemu Linux. Rozszerzenie Skrypt niestandardowy dla systemu Windows umożliwia uruchamianie dowolnego skryptu programu PowerShell na maszynie wirtualnej. Skrypty niestandardowe są przydatne do projektowania wdrożeń platformy Azure, które wymagają konfiguracji wykraczającej poza to, co może zapewnić natywne narzędzia platformy Azure. Aby uzyskać więcej informacji, zobacz [Rozszerzenie skryptu niestandardowego maszyny Wirtualnej systemu Windows](custom-script-windows.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby obsłużyć rozszerzenie na maszynie Wirtualnej, potrzebujesz zainstalowanego agenta systemu Windows azure. Niektóre poszczególne rozszerzenia mają wymagania wstępne, takie jak dostęp do zasobów lub zależności.

### <a name="azure-vm-agent"></a>Agent maszyny wirtualnej platformy Azure

Agent maszyny wirtualnej platformy Azure zarządza interakcjami między maszyną wirtualną platformy Azure a kontrolerem sieci szkieletowej platformy Azure. Agent maszyny wirtualnej jest odpowiedzialny za wiele funkcjonalnych aspektów wdrażania maszyn wirtualnych platformy Azure i zarządzania nimi, w tym uruchamianie rozszerzeń maszyn wirtualnych. Agent maszyny wirtualnej platformy Azure jest preinstalowany w obrazach usługi Azure Marketplace i można go zainstalować ręcznie w obsługiwanych systemach operacyjnych. Agent maszyny wirtualnej platformy Azure dla systemu Windows jest znany jako agent gościa systemu Windows.

Aby uzyskać informacje na temat obsługiwanych systemów operacyjnych i instrukcji instalacji, zobacz [agent maszyny wirtualnej platformy Azure](agent-windows.md).

#### <a name="supported-agent-versions"></a>Obsługiwane wersje agentów

Aby zapewnić najlepsze możliwe wrażenia, istnieją minimalne wersje agenta. Aby uzyskać więcej informacji, zobacz [ten artykuł](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

#### <a name="supported-oses"></a>Obsługiwane oses

Agent gościa systemu Windows działa na wielu systemach operacyjnych, jednak struktura rozszerzeń ma limit dla systemów operacyjnych, które rozszerzenia. Aby uzyskać więcej informacji, zobacz [ten artykuł](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems
).

Niektóre rozszerzenia nie są obsługiwane we wszystkich systemach operacyjnych i mogą emitować *kod błędu 51, "Nieobsługicony system operacyjny".* Sprawdź indywidualną dokumentację rozszerzenia pod kątem możliwości obsługi.

#### <a name="network-access"></a>Dostęp do sieci

Pakiety rozszerzeń są pobierane z repozytorium rozszerzenia usługi Azure Storage, a przekazywanie stanu rozszerzenia jest księgowane w usłudze Azure Storage. Jeśli używasz [obsługiwanej](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) wersji agentów, nie musisz zezwalać na dostęp do usługi Azure Storage w regionie maszyny Wirtualnej, ponieważ można użyć agenta, aby przekierować komunikację do kontrolera sieci szkieletowej platformy Azure w celu komunikacji agenta (funkcja HostGAPlugin za pośrednictwem uprzywilejowanego kanału w prywatnym usłudze IP [168.63.129.16).](https://docs.microsoft.com/azure/virtual-network/what-is-ip-address-168-63-129-16) Jeśli korzystasz z nieobjętej do sieci wersji agenta, musisz zezwolić na dostęp wychodzący do magazynu platformy Azure w tym regionie z maszyny Wirtualnej.

> [!IMPORTANT]
> Jeśli zablokowano dostęp do *168.63.129.16* przy użyciu zapory gościa lub serwera proxy, rozszerzenia nie powiedzie się niezależnie od powyższych. Wymagane są porty 80, 443 i 32526.

Agenci mogą być używane tylko do pobierania pakietów rozszerzeń i stanu raportowania. Jeśli na przykład instalacja rozszerzenia musi pobrać skrypt z usługi GitHub (skrypt niestandardowy) lub będzie wymagać dostępu do usługi Azure Storage (Azure Backup), należy otworzyć dodatkowe porty zapory/sieciowej grupy zabezpieczeń. Różne rozszerzenia mają różne wymagania, ponieważ są aplikacje w sobie. W przypadku rozszerzeń, które wymagają dostępu do usługi Azure Storage lub usługi Azure Active Directory, można zezwolić na dostęp przy użyciu [tagów usługi NSG platformy Azure](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) do magazynu lub usługi AzureActiveDirectory.

Agent gościa systemu Windows nie obsługuje serwera proxy, aby przekierować żądania ruchu agenta za pośrednictwem, co oznacza, że Agent gościa systemu Windows będzie polegać na niestandardowym serwerze proxy (jeśli go masz), aby uzyskać dostęp do zasobów w Internecie lub na hoście za pośrednictwem adresu IP 168.63.129.16.

## <a name="discover-vm-extensions"></a>Odnajduj rozszerzenia maszyn wirtualnych

Wielu różnych rozszerzeń maszyny wirtualnej można używać z maszynami wirtualnymi platformy Azure. Aby wyświetlić pełną listę, użyj [pliku Get-AzVMExtensionImage](https://docs.microsoft.com/powershell/module/az.compute/get-azvmextensionimage). Poniższy przykład zawiera listę wszystkich dostępnych rozszerzeń w lokalizacji *WestUS:*

```powershell
Get-AzVmImagePublisher -Location "WestUS" | `
Get-AzVMExtensionImageType | `
Get-AzVMExtensionImage | Select Type, Version
```

## <a name="run-vm-extensions"></a>Uruchamianie rozszerzeń maszyn wirtualnych

Rozszerzenia maszyn wirtualnych platformy Azure są uruchamiane na istniejących maszynach wirtualnych, co jest przydatne, gdy trzeba wprowadzić zmiany konfiguracji lub odzyskać łączność na już wdrożonej maszynie wirtualnej. Rozszerzenia maszyn wirtualnych można również łączyć z wdrożeniami szablonów usługi Azure Resource Manager. Za pomocą rozszerzeń z szablonami Usługi Resource Manager, maszyny wirtualne platformy Azure można wdrożyć i skonfigurować bez interwencji po wdrożeniu.

Następujące metody mogą służyć do uruchamiania rozszerzenia względem istniejącej maszyny Wirtualnej.

### <a name="powershell"></a>PowerShell

Istnieje kilka poleceń programu PowerShell do uruchamiania poszczególnych rozszerzeń. Aby wyświetlić listę, użyj [polecenia Get](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/get-command) i filtruj w *rozszerzeniu:*

```powershell
Get-Command Set-Az*Extension* -Module Az.Compute
```

Zapewnia to dane wyjściowe podobne do następujących:

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

W poniższym przykładzie użyto rozszerzenia Skrypt niestandardowy, aby pobrać skrypt z repozytorium GitHub na docelową maszynę wirtualną, a następnie uruchomić skrypt. Aby uzyskać więcej informacji na temat rozszerzenia Skrypt niestandardowy, zobacz [omówienie rozszerzenia skryptu niestandardowego](custom-script-windows.md).

```powershell
Set-AzVMCustomScriptExtension -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" -Name "myCustomScript" `
    -FileUri "https://raw.githubusercontent.com/neilpeterson/nepeters-azure-templates/master/windows-custom-script-simple/support-scripts/Create-File.ps1" `
    -Run "Create-File.ps1" -Location "West US"
```

W poniższym przykładzie rozszerzenie programu VM Access służy do resetowania hasła administracyjnego maszyny Wirtualnej systemu Windows do hasła tymczasowego. Aby uzyskać więcej informacji na temat rozszerzenia programu VM Access, zobacz [Resetowanie usługi pulpitu zdalnego w maszynie Wirtualnej systemu Windows](../windows/reset-rdp.md). Po uruchomieniu tego, należy zresetować hasło przy pierwszym logowaniu:

```powershell
$cred=Get-Credential

Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myVMAccess" `
    -Location WestUS -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"
```

Polecenia `Set-AzVMExtension` można użyć do uruchomienia dowolnego rozszerzenia maszyny Wirtualnej. Aby uzyskać więcej informacji, zobacz [odwołanie Set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension).


### <a name="azure-portal"></a>Portal Azure

Rozszerzenia maszyn wirtualnych można zastosować do istniejącej maszyny Wirtualnej za pośrednictwem witryny Azure portal. Wybierz maszynę wirtualną w portalu, wybierz pozycję **Rozszerzenia,** a następnie wybierz pozycję **Dodaj**. Wybierz żądane rozszerzenie z listy dostępnych rozszerzeń i postępuj zgodnie z instrukcjami kreatora.

W poniższym przykładzie przedstawiono instalację rozszerzenia ochrony przed złośliwym oprogramowaniem firmy Microsoft z witryny Azure portal:

![Instalowanie rozszerzenia ochrony przed złośliwym oprogramowaniem](./media/features-windows/installantimalwareextension.png)

### <a name="azure-resource-manager-templates"></a>Szablony usługi Azure Resource Manager

Rozszerzenia maszyn wirtualnych można dodać do szablonu usługi Azure Resource Manager i wykonać z wdrożeniem szablonu. Podczas wdrażania rozszerzenia z szablonem można utworzyć w pełni skonfigurowane wdrożenia platformy Azure. Na przykład następujący JSON jest pobierana z szablonu Menedżera zasobów wdraża zestaw maszyn wirtualnych z równoważeniem obciążenia i bazy danych SQL platformy Azure, a następnie instaluje aplikację .NET Core na każdej maszynie wirtualnej. Rozszerzenie maszyny Wirtualnej zajmuje się instalacją oprogramowania.

Aby uzyskać więcej informacji, zobacz [pełny szablon Menedżera zasobów](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

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

Aby uzyskać więcej informacji na temat tworzenia szablonów Menedżera zasobów, zobacz [Tworzenie szablonów usługi Azure Resource Manager z rozszerzeniami maszyn wirtualnych systemu Windows](../windows/template-description.md#extensions).

## <a name="secure-vm-extension-data"></a>Bezpieczne dane rozszerzenia maszyny Wirtualnej

Po uruchomieniu rozszerzenia maszyny Wirtualnej może być konieczne uwzględnienie poufnych informacji, takich jak poświadczenia, nazwy kont magazynu i klucze dostępu do konta magazynu. Wiele rozszerzeń maszyn wirtualnych zawiera chroniona konfiguracja, która szyfruje dane i tylko odszyfrowuje je wewnątrz docelowej maszyny Wirtualnej. Każde rozszerzenie ma określony schemat konfiguracji chronione, a każdy jest szczegółowo opisany w dokumentacji specyficznej dla rozszerzenia.

W poniższym przykładzie pokazano wystąpienie rozszerzenia skryptu niestandardowego dla systemu Windows. Polecenie do wykonania zawiera zestaw poświadczeń. W tym przykładzie polecenie do wykonania nie jest szyfrowane:

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

Przeniesienie **polecenia do wykonania** właściwości do **chronionej** konfiguracji zabezpiecza ciąg wykonania, jak pokazano w poniższym przykładzie:

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

Na maszynie wirtualnej usługi Azure IaaS korzystającej z rozszerzeń w konsoli certyfikatów mogą być widoczne certyfikaty, które mają podmiot **_Generator certyfikatów CRP_** platformy Windows Azure . Na klasycznej maszynie wirtualnej RDFE te certyfikaty mają nazwę podmiotu **_Zarządzanie usługami systemu Windows Azure dla rozszerzeń_**.

Certyfikaty te zabezpieczają komunikację między maszyną wirtualną a jej hostem podczas przesyłania chronionych ustawień (hasło, inne poświadczenia) używanych przez rozszerzenia. Certyfikaty są tworzone przez kontroler sieci szkieletowej platformy Azure i przekazywane do agenta maszyny Wirtualnej. Jeśli zatrzymujesz i uruchamiasz maszynę wirtualną każdego dnia, kontroler sieci szkieletowej może utworzyć nowy certyfikat. Certyfikat jest przechowywany w magazynie certyfikatów osobistych komputera. Certyfikaty te można usunąć. Agent maszyny Wirtualnej ponownie tworzy certyfikaty w razie potrzeby.

### <a name="how-do-agents-and-extensions-get-updated"></a>W jaki sposób agenci i rozszerzenia są aktualizowane?

Agenci i rozszerzenia współużytkuje ten sam mechanizm aktualizacji. Niektóre aktualizacje nie wymagają dodatkowych reguł zapory.

Gdy aktualizacja jest dostępna, jest instalowana na maszynie Wirtualnej tylko wtedy, gdy nastąpi zmiana rozszerzeń i inne zmiany modelu maszyny Wirtualnej, takie jak:

- Dyski z danymi
- Rozszerzenia
- Kontener diagnostyki rozruchu
- Tajemnice systemu operacyjnego gościa
- Rozmiar maszyny wirtualnej
- Profil sieciowy

Wydawcy udostępniają aktualizacje regionom o różnych porach, dzięki czemu można mieć maszyny wirtualne w różnych regionach w różnych wersjach.

#### <a name="listing-extensions-deployed-to-a-vm"></a>Rozszerzeń listy wdrożonych na maszynie wirtualnej

```powershell
$vm = Get-AzVM -ResourceGroupName "myResourceGroup" -VMName "myVM"
$vm.Extensions | select Publisher, VirtualMachineExtensionType, TypeHandlerVersion
```

```powershell
Publisher             VirtualMachineExtensionType          TypeHandlerVersion
---------             ---------------------------          ------------------
Microsoft.Compute     CustomScriptExtension                1.9
```

#### <a name="agent-updates"></a>Aktualizacje agentów

Agent gościa systemu Windows zawiera tylko *kod obsługi rozszerzeń,* *kod inicjowania obsługi systemu Windows* jest oddzielny. Agenta gościa systemu Windows można odinstalować. Nie można wyłączyć automatycznej aktualizacji agenta gościa okna.

*Kod obsługi rozszerzeń* jest odpowiedzialny za komunikowanie się z siecią szkieletową platformy Azure i obsługę operacji rozszerzeń maszyn wirtualnych, takich jak instalacje, stan raportowania, aktualizowanie poszczególnych rozszerzeń i ich usuwanie. Aktualizacje zawierają poprawki zabezpieczeń, poprawki błędów i ulepszenia *kodu obsługi rozszerzeń*.

Aby sprawdzić, jaką wersję masz, zobacz [Wykrywanie zainstalowanego programu Windows Guest Agent](agent-windows.md#detect-the-vm-agent).

#### <a name="extension-updates"></a>Aktualizacje rozszerzeń

Gdy aktualizacja rozszerzenia jest dostępna, agent gościa systemu Windows pobiera i uaktualnia rozszerzenie. Automatyczne aktualizacje rozszerzeń są *niewielkie* lub *poprawki*. Możesz wyrazić zgodę na rozszerzenia *Drobne* aktualizacje lub zrezygnować z nich podczas udostępniania rozszerzenia. W poniższym przykładzie pokazano, jak automatycznie uaktualnić wersje pomocnicze w szablonie Menedżera zasobów za pomocą *autoUpgradeMinorVersion": true,'*:

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

Aby uzyskać najnowsze drobne poprawki błędów w wersji, zdecydowanie zaleca się, aby zawsze wybierać automatyczną aktualizację w wdrożeniach rozszerzeń. Nie można zrezygnować z aktualizacji poprawek, które zawierają zabezpieczenia lub poprawki błędów kluczy.

### <a name="how-to-identify-extension-updates"></a>Jak zidentyfikować aktualizacje rozszerzenia

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>Określenie, czy rozszerzenie jest ustawione z autoUpgradeMinorVersion na maszynie Wirtualnej

Z modelu maszyny Wirtualnej można zobaczyć, czy rozszerzenie zostało zainicjowane za pomocą funkcji "autoUpgradeMinorVersion". Aby to sprawdzić, użyj [funkcji Get-AzVm](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) i podaj następującą nazwę grupy zasobów i maszyny wirtualnej:

```powerShell
 $vm = Get-AzVm -ResourceGroupName "myResourceGroup" -VMName "myVM"
 $vm.Extensions
```

Poniższe przykładowe dane wyjściowe pokazują, że *autoUpgradeMinorVersion* jest ustawiona na *true:*

```powershell
ForceUpdateTag              :
Publisher                   : Microsoft.Compute
VirtualMachineExtensionType : CustomScriptExtension
TypeHandlerVersion          : 1.9
AutoUpgradeMinorVersion     : True
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>Identyfikowanie, kiedy wystąpiła funkcja autoUpgradeMinorVersion

Aby zobaczyć, kiedy nastąpiła aktualizacja rozszerzenia, przejrzyj dzienniki agenta na maszynie Wirtualnej pod adresem *C:\WindowsAzure\Logs\WaAppAgent.log*

W poniższym przykładzie maszyna wirtualna miała zainstalowany *system Microsoft.Compute.CustomScriptExtension 1.8.* Poprawka była dostępna w wersji *1.9:*

```powershell
[INFO]  Getting plugin locations for plugin 'Microsoft.Compute.CustomScriptExtension'. Current Version: '1.8', Requested Version: '1.9'
[INFO]  Auto-Upgrade mode. Highest public version for plugin 'Microsoft.Compute.CustomScriptExtension' with requested version: '1.9', is: '1.9'
```

## <a name="agent-permissions"></a>Uprawnienia agenta

Aby wykonać swoje zadania, agent musi działać jako *system lokalny*.

## <a name="troubleshoot-vm-extensions"></a>Rozwiązywanie problemów z rozszerzeniami maszyn wirtualnych

Każde rozszerzenie maszyny Wirtualnej może mieć kroki rozwiązywania problemów specyficzne dla rozszerzenia. Na przykład podczas korzystania z rozszerzenia skryptu niestandardowego szczegóły wykonywania skryptu można znaleźć lokalnie na maszynie Wirtualnej, gdzie zostało uruchomione rozszerzenie. Wszelkie kroki rozwiązywania problemów specyficzne dla rozszerzenia są szczegółowo opisane w dokumentacji specyficznej dla rozszerzenia.

Poniższe kroki rozwiązywania problemów dotyczą wszystkich rozszerzeń maszyn wirtualnych.

1. Aby sprawdzić dziennik agenta gościa systemu Windows, przyjrzyj się aktywności, gdy rozszerzenie było aprowizowane w *języku C:\WindowsAzure\Logs\WaAppAgent.log*

2. Sprawdź rzeczywiste dzienniki rozszerzeń, aby uzyskać więcej szczegółów w *C:\WindowsAzure\Logs\Plugins\<extensionName>*

3. Sprawdź sekcje rozwiązywania problemów z dokumentacją dotyczącą rozszerzenia, aby uzyskać kody błędów, znane problemy itp.

4. Spójrz na dzienniki systemowe. Sprawdź, czy inne operacje, które mogły zakłócać rozszerzenie, takie jak długotrwała instalacja innej aplikacji, która wymagała wyłącznego dostępu menedżera pakietów.

### <a name="common-reasons-for-extension-failures"></a>Typowe przyczyny awarii rozszerzenia

1. Rozszerzenia mają 20 minut do uruchomienia (wyjątki to rozszerzenia CustomScript, Chef i DSC, które mają 90 minut). Jeśli wdrożenie przekracza ten czas, jest oznaczony jako limit czasu. Przyczyną tego może być ze względu na maszyny wirtualne o niskiej ilości zasobów, inne konfiguracje maszyn wirtualnych/uruchamianie zadań zużywających duże ilości zasobów, podczas gdy rozszerzenie próbuje aprowizować.

2. Minimalne wymagania wstępne nie zostały spełnione. Niektóre rozszerzenia mają zależności od jednostek SKU maszyn wirtualnych, takich jak obrazy HPC. Rozszerzenia mogą wymagać pewnych wymagań dotyczących dostępu do sieci, takich jak komunikowanie się z usługą Azure Storage lub usług publicznych. Inne przykłady mogą to być dostęp do repozytoriów pakietów, brak miejsca na dysku lub ograniczenia zabezpieczeń.

3. Ekskluzywny dostęp do menedżera pakietów. W niektórych przypadkach może wystąpić długotrwała konfiguracja maszyny Wirtualnej i instalacji rozszerzenia powodujące konflikt, gdzie oba one potrzebują wyłącznego dostępu do menedżera pakietów.

### <a name="view-extension-status"></a>Wyświetl stan rozszerzenia

Po uruchomieniu rozszerzenia maszyny Wirtualnej względem maszyny Wirtualnej należy użyć [funkcji Get-AzVM,](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) aby zwrócić stan rozszerzenia. *Podstatusy[0]* pokazuje, że inicjowanie obsługi administracyjnej rozszerzenia powiodło się, co oznacza, że pomyślnie wdrożone na maszynie wirtualnej, ale wykonanie rozszerzenia wewnątrz maszyny Wirtualnej nie powiodło się, *Podstatusy[1]*.

```powershell
Get-AzVM -ResourceGroupName "myResourceGroup" -VMName "myVM" -Status
```

Dane wyjściowe są podobne do następujących przykładowych danych wyjściowych:

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

Stan wykonania rozszerzenia można również znaleźć w witrynie Azure portal. Aby wyświetlić stan rozszerzenia, wybierz maszynę wirtualną, wybierz **pozycję Rozszerzenia**, a następnie wybierz żądane rozszerzenie.

### <a name="rerun-vm-extensions"></a>Ponowne przyokazanie rozszerzeń maszyn wirtualnych

Mogą wystąpić przypadki, w których rozszerzenie maszyny Wirtualnej musi zostać ponownie uruchom. Rozszerzenie można ponownie uruchomić, usuwając je, a następnie ponownie uruchomić rozszerzenie przy pomocą wybranej metody wykonywania. Aby usunąć rozszerzenie, należy użyć [usuń-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/Remove-AzVMExtension) w następujący sposób:

```powershell
Remove-AzVMExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myExtensionName"
```

Można również usunąć rozszerzenie w witrynie Azure portal w następujący sposób:

1. Wybierz maszynę wirtualną.
2. Wybierz **pozycję Rozszerzenia**.
3. Wybierz żądane rozszerzenie.
4. Wybierz **pozycję Odinstaluj**.

## <a name="common-vm-extensions-reference"></a>Typowe odwołanie do rozszerzeń maszyn wirtualnych
| Nazwa rozszerzenia | Opis | Więcej informacji |
| --- | --- | --- |
| Rozszerzenie niestandardowego skryptu dla systemu Windows |Uruchamianie skryptów na maszynie wirtualnej platformy Azure |[Rozszerzenie niestandardowego skryptu dla systemu Windows](custom-script-windows.md) |
| Rozszerzenie DSC dla systemu Windows |Rozszerzenie programu PowerShell DSC (konfiguracja żądanego stanu) |[Rozszerzenie DSC dla systemu Windows](dsc-overview.md) |
| Rozszerzenie Diagnostyki Azure |Zarządzanie diagnostyką platformy Azure |[Rozszerzenie diagnostyki platformy Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Rozszerzenie programu Azure VM Access |Zarządzanie użytkownikami i poświadczeniami |[Rozszerzenie dostępu do maszyn wirtualnych dla systemu Linux](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat rozszerzeń maszyn wirtualnych, zobacz [omówienie rozszerzeń i funkcji maszyn wirtualnych platformy Azure.](overview.md)
