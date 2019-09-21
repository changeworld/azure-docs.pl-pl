---
title: Omówienie agenta maszyny wirtualnej platformy Azure | Microsoft Docs
description: Omówienie agenta maszyny wirtualnej platformy Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: axayjo
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: 0a1f212e-053e-4a39-9910-8d622959f594
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/20/2019
ms.author: akjosh
ms.openlocfilehash: 7c163dd48e53a3116d58cb94988f2822ddede5e5
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71169129"
---
# <a name="azure-virtual-machine-agent-overview"></a>Omówienie agenta maszyny wirtualnej platformy Azure
Agent maszyny wirtualnej Microsoft Azure (Agent VM) to bezpieczny, lekki proces zarządzający interakcją maszyny wirtualnej z kontrolerem sieci szkieletowej Azure. Agent maszyny wirtualnej odgrywa podstawową rolę w procesie włączania i wykonywania rozszerzeń maszyny wirtualnej platformy Azure. Rozszerzenia maszyn wirtualnych umożliwiają konfigurację po wdrożeniu maszyny wirtualnej, taką jak instalowanie i Konfigurowanie oprogramowania. Rozszerzenia maszyn wirtualnych umożliwiają również włączenie funkcji odzyskiwania, takich jak resetowanie hasła administracyjnego maszyny wirtualnej. Bez agenta maszyny wirtualnej platformy Azure nie można uruchomić rozszerzeń maszyn wirtualnych.

Ten artykuł zawiera szczegółowe informacje dotyczące instalacji, wykrywania i usuwania agenta maszyny wirtualnej platformy Azure.

## <a name="install-the-vm-agent"></a>Zainstaluj agenta maszyny wirtualnej

### <a name="azure-marketplace-image"></a>Obraz witryny Azure Marketplace

Agent maszyny wirtualnej platformy Azure jest instalowany domyślnie na dowolnej maszynie wirtualnej z systemem Windows wdrożonej z obrazu portalu Azure Marketplace. Po wdrożeniu obrazu witryny Azure Marketplace z poziomu portalu, programu PowerShell, interfejsu wiersza polecenia lub szablonu Azure Resource Manager jest również instalowany Agent maszyny wirtualnej platformy Azure.

Pakiet agenta gościa systemu Windows jest podzielony na dwie części:

- Agent aprowizacji (PA)
- Agent gościa systemu Windows (skrzydło)

Aby uruchomić maszynę wirtualną, należy zainstalować na maszynie wirtualnej PA, ale nie trzeba instalować tego skrzydła. Po wdrożeniu maszyny wirtualnej można wybrać opcję nie instaluj skrzydła. Poniższy przykład pokazuje, jak wybrać opcję *provisionVmAgent* z szablonem Azure Resource Manager:

```json
"resources": [{
"name": "[parameters('virtualMachineName')]",
"type": "Microsoft.Compute/virtualMachines",
"apiVersion": "2016-04-30-preview",
"location": "[parameters('location')]",
"dependsOn": ["[concat('Microsoft.Network/networkInterfaces/', parameters('networkInterfaceName'))]"],
"properties": {
    "osProfile": {
    "computerName": "[parameters('virtualMachineName')]",
    "adminUsername": "[parameters('adminUsername')]",
    "adminPassword": "[parameters('adminPassword')]",
    "windowsConfiguration": {
        "provisionVmAgent": "false"
}
```

Jeśli nie masz zainstalowanych agentów, nie możesz używać niektórych usług platformy Azure, takich jak Azure Backup lub zabezpieczenia platformy Azure. Te usługi wymagają zainstalowania rozszerzenia. Jeśli maszyna wirtualna została wdrożona bez skrzydła, można zainstalować najnowszą wersję agenta później.

### <a name="manual-installation"></a>Instalacja ręczna
Agenta maszyny wirtualnej z systemem Windows można zainstalować ręcznie przy użyciu pakietu Instalatora Windows. Jeśli tworzysz niestandardowy obraz maszyny wirtualnej wdrożony na platformie Azure, może być konieczna instalacja ręczna. Aby ręcznie zainstalować agenta maszyny wirtualnej z systemem Windows, [Pobierz instalatora agenta maszyny wirtualnej](https://go.microsoft.com/fwlink/?LinkID=394789). Agent maszyny wirtualnej jest obsługiwany w systemie Windows Server 2008 R2 lub nowszym.

Agenta maszyny wirtualnej można zainstalować przez dwukrotne kliknięcie pliku Instalatora Windows. W przypadku zautomatyzowanej lub nienadzorowanej instalacji agenta maszyny wirtualnej Uruchom następujące polecenie:

```cmd
msiexec.exe /i WindowsAzureVmAgent.2.7.1198.778.rd_art_stable.160617-1120.fre /quiet
```

### <a name="prerequisites"></a>Wymagania wstępne
Agent maszyny wirtualnej z systemem Windows wymaga co najmniej systemu Windows Server 2008 R2 (64-BITS) do uruchomienia przy użyciu programu .NET Framework 4,0.

## <a name="detect-the-vm-agent"></a>Wykrywanie agenta maszyny wirtualnej

### <a name="powershell"></a>PowerShell

Moduł Azure Resource Manager PowerShell może służyć do pobierania informacji o maszynach wirtualnych platformy Azure. Aby wyświetlić informacje dotyczące maszyny wirtualnej, na przykład stanu aprowizacji agenta maszyny wirtualnej platformy Azure, użyj polecenie [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm):

```powershell
Get-AzVM
```

Następujące wąskie przykładowe dane wyjściowe pokazują Właściwość *ProvisionVMAgent* zagnieżdżoną wewnątrz *OSProfile*. Ta właściwość może służyć do określenia, czy Agent maszyny wirtualnej został wdrożony na maszynie wirtualnej:

```powershell
OSProfile                  :
  ComputerName             : myVM
  AdminUsername            : myUserName
  WindowsConfiguration     :
    ProvisionVMAgent       : True
    EnableAutomaticUpdates : True
```

Poniższy skrypt może służyć do zwrócenia zwięzłej listy nazw maszyn wirtualnych i stanu agenta maszyny wirtualnej:

```powershell
$vms = Get-AzVM

foreach ($vm in $vms) {
    $agent = $vm | Select -ExpandProperty OSProfile | Select -ExpandProperty Windowsconfiguration | Select ProvisionVMAgent
    Write-Host $vm.Name $agent.ProvisionVMAgent
}
```

### <a name="manual-detection"></a>Wykrywanie ręczne

Po zalogowaniu się do maszyny wirtualnej z systemem Windows Menedżer zadań może służyć do badania uruchomionych procesów. Aby sprawdzić agenta maszyny wirtualnej platformy Azure, Otwórz Menedżera zadań, kliknij kartę *szczegóły* , a następnie wyszukaj nazwę procesu **WindowsAzureGuestAgent. exe**. Obecność tego procesu wskazuje, że Agent maszyny wirtualnej jest zainstalowany.


## <a name="upgrade-the-vm-agent"></a>Uaktualnianie agenta maszyny wirtualnej
Agent maszyny wirtualnej platformy Azure dla systemu Windows jest automatycznie uaktualniany. Gdy nowe maszyny wirtualne są wdrażane na platformie Azure, otrzymują najnowszego agenta maszyny wirtualnej na czas udostępniania maszyny wirtualnej. Niestandardowe obrazy maszyn wirtualnych należy zaktualizować ręcznie, aby uwzględnić nowego agenta maszyny wirtualnej podczas tworzenia obrazu.


## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji o rozszerzeniach maszyn wirtualnych, zobacz [Omówienie rozszerzeń i funkcji maszyny wirtualnej platformy Azure](overview.md).
