---
title: Omówienie agenta maszyny wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Omówienie agenta maszyny wirtualnej platformy Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: roiyz-msft
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: 0a1f212e-053e-4a39-9910-8d622959f594
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: roiyz
ms.openlocfilehash: 3de0e7ac20296544f7ca02030056aa60542cb0b0
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706151"
---
# <a name="azure-virtual-machine-agent-overview"></a>Omówienie agenta maszyny wirtualnej na platformie Azure
Agent maszyny wirtualnej usług Microsoft Azure (Agent maszyny Wirtualnej) jest bezpieczne, uproszczonego procesu, który zarządza interakcją maszyny wirtualnej (VM) z kontrolerem sieci szkieletowej platformy Azure. Agent maszyny wirtualnej odgrywa podstawową rolę w procesie włączania i wykonywania rozszerzeń maszyny wirtualnej platformy Azure. Rozszerzenia maszyny Wirtualnej Włącz konfigurację po wdrożeniu maszyny wirtualnej, takie jak instalowanie i konfigurowanie oprogramowania. Rozszerzenia maszyn wirtualnych również włączyć funkcje odzyskiwania, takie jak zresetować hasło administracyjne maszyny wirtualnej. Bez agenta maszyny Wirtualnej platformy Azure nie można uruchomić rozszerzenia maszyn wirtualnych.

Ten artykuł szczegółowo opisuje instalacji, wykrywanie i usuwanie programu Agent maszyny wirtualnej platformy Azure.

## <a name="install-the-vm-agent"></a>Zainstaluj agenta maszyny Wirtualnej

### <a name="azure-marketplace-image"></a>Usługa Azure obrazu z witryny Marketplace

Agent maszyny Wirtualnej platformy Azure jest instalowany domyślnie na dowolnej maszynie Wirtualnej z Windows wdrażanych za pomocą obrazu portalu Azure Marketplace. Podczas wdrażania obrazu z witryny Azure Marketplace w portalu, programu PowerShell, interfejsu wiersza polecenia lub szablonu usługi Azure Resource Manager, jest również instalowany Agent maszyny Wirtualnej platformy Azure.

Pakiet agenta gościa Windows jest dzielony na dwie części:

- Agenta inicjowania obsługi administracyjnej (PA)
- Agent gościa Windows (WinGA)

Rozruch maszyny Wirtualnej musi mieć PA zainstalowane na maszynie Wirtualnej, WinGA nie musi jednak być zainstalowany. Na maszynie Wirtualnej należy wdrożyć czasu, możesz wybrać, aby nie instalować WinGA. Poniższy przykład pokazuje sposób wybierania *provisionVmAgent* opcji za pomocą szablonu usługi Azure Resource Manager:

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

Jeśli nie masz zainstalowanych agentów, nie można używać niektórych usług platformy Azure, takich jak usługi Azure Backup lub zabezpieczeń platformy Azure. Te usługi wymagają rozszerzenia do zainstalowania. Jeśli wdrożono maszynę Wirtualną bez WinGA można zainstalować najnowszą wersję agenta, w dalszej części.

### <a name="manual-installation"></a>Instalacja ręczna
Agent maszyny Wirtualnej Windows można zainstalować ręcznie za pomocą pakietu Instalatora Windows. Ręczna instalacja może być konieczne utworzenie niestandardowego obrazu maszyny Wirtualnej, który jest wdrożony na platformie Azure. Aby ręcznie zainstalować agenta maszyny Wirtualnej Windows, [pobrać Instalatora agenta maszyny Wirtualnej](https://go.microsoft.com/fwlink/?LinkID=394789).

Agent maszyny Wirtualnej mogą być instalowane przez dwukrotne kliknięcie pliku Instalatora Windows. Automatyczna lub z instalacji nienadzorowanej instalacji agenta maszyny Wirtualnej uruchom następujące polecenie:

```cmd
msiexec.exe /i WindowsAzureVmAgent.2.7.1198.778.rd_art_stable.160617-1120.fre /quiet
```

## <a name="detect-the-vm-agent"></a>Wykrywanie agenta maszyny Wirtualnej

### <a name="powershell"></a>PowerShell

Moduł Azure PowerShell Resource Manager może służyć do pobierania informacji o maszynach wirtualnych platformy Azure. Aby wyświetlić informacje o maszynie Wirtualnej, takie jak stan aprowizacji agenta maszyny Wirtualnej platformy Azure, użyj [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm):

```powershell
Get-AzVM
```

Dane wyjściowe poniższego, skróconego przykładu *ProvisionVMAgent* właściwości zagnieżdżone wewnątrz *OSProfile*. Ta właściwość może służyć do określenia, jeśli agent maszyny Wirtualnej został wdrożony na maszynie Wirtualnej:

```powershell
OSProfile                  :
  ComputerName             : myVM
  AdminUsername            : myUserName
  WindowsConfiguration     :
    ProvisionVMAgent       : True
    EnableAutomaticUpdates : True
```

Poniższy skrypt może służyć do zwrócenia zwięzłe listę nazw maszyn wirtualnych i stan agenta maszyny Wirtualnej:

```powershell
$vms = Get-AzVM

foreach ($vm in $vms) {
    $agent = $vm | Select -ExpandProperty OSProfile | Select -ExpandProperty Windowsconfiguration | Select ProvisionVMAgent
    Write-Host $vm.Name $agent.ProvisionVMAgent
}
```

### <a name="manual-detection"></a>Ręczne wykrywanie

Po zalogowaniu się do maszyny Wirtualnej z systemem Windows, Menedżera zadań może służyć do sprawdzenia uruchomionych procesów. Aby sprawdzić, czy Agent maszyny Wirtualnej platformy Azure, otwórz Menedżera zadań, kliknij *szczegóły* kartę i wyszukaj nazwę procesu **WindowsAzureGuestAgent.exe**. Obecność tego procesu wskazuje, że agent maszyny Wirtualnej jest zainstalowany.


## <a name="upgrade-the-vm-agent"></a>Uaktualnij agenta maszyny Wirtualnej
Azure VM Agent for Windows zostaje automatycznie uaktualnione. Podczas wdrażania nowych maszyn wirtualnych na platformie Azure, otrzyma najnowszą wersję agenta maszyny Wirtualnej podczas aprowizacji maszyny Wirtualnej. Niestandardowe obrazy maszyn wirtualnych należy ręcznie zaktualizować do uwzględnienia nowego agenta maszyny Wirtualnej w czasie tworzenia obrazu.


## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat rozszerzeń maszyn wirtualnych, zobacz [omówienie rozszerzeń i funkcji maszyn wirtualnych platformy Azure](overview.md).
