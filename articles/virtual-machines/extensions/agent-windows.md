---
title: Omówienie agenta maszyny wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Omówienie agenta maszyny wirtualnej platformy Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: danielsollondon
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 0a1f212e-053e-4a39-9910-8d622959f594
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: danis
ms.openlocfilehash: fb29f0f931715b8a6ba5b4528294eb61ef5762c8
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="azure-virtual-machine-agent-overview"></a>Omówienie usługi Azure agenta maszyny wirtualnej
Agent maszyny wirtualnej programu Microsoft Azure (Agent maszyny Wirtualnej) jest bezpieczne, lekkie procesu, który zarządza maszyny wirtualnej (VM) interakcji z kontrolerem sieci szkieletowej Azure. Agent maszyny wirtualnej odgrywa podstawową rolę w procesie włączania i wykonywania rozszerzeń maszyny wirtualnej platformy Azure. Maszyna wirtualna rozszerzeniom konfiguracji po wdrożeniu maszyny wirtualnej, takie jak instalowanie i konfigurowanie oprogramowania. Rozszerzenia maszyny Wirtualnej również włączyć funkcje odzyskiwania, np. zresetowania hasła administracyjnego maszyny wirtualnej. Bez agenta maszyny Wirtualnej Azure nie można uruchomić rozszerzenia maszyny Wirtualnej.

Ten artykuł zawiera szczegóły dotyczące instalacji, wykrywania i usuwania agenta maszyny wirtualnej Azure.

## <a name="install-the-vm-agent"></a>Zainstaluj agenta maszyny Wirtualnej

### <a name="azure-marketplace-image"></a>Obraz Azure Marketplace

Agent maszyny Wirtualnej Azure jest instalowany domyślnie na wszystkie wdrożone za pomocą portalu Azure Marketplace obrazu maszyny Wirtualnej systemu Windows. Podczas wdrażania obrazu portalu Azure Marketplace z portalu, programu PowerShell, interfejsu wiersza polecenia lub szablonu usługi Azure Resource Manager, jest również instalowany Agent maszyny Wirtualnej Azure.

Pakiet agenta gościa z systemem Windows jest podzielony na dwie części:

- Agentem inicjowania obsługi administracyjnej (PA)
- Agent gościa z systemem Windows (WinGA)

Do rozruchu maszyny Wirtualnej musi mieć PA zainstalowany na maszynie Wirtualnej, WinGA nie musi jednak być zainstalowany. W przypadku maszyny Wirtualnej wdrażanie czasu, można wybrać, aby nie instalować WinGA. Poniższy przykład przedstawia sposób wybierania *provisionVmAgent* opcji z szablonem usługi Azure Resource Manager:

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

Jeśli nie masz zainstalowani agenci, nie można używać niektórych usług Azure, takich jak Kopia zapasowa Azure lub zabezpieczeń Azure. Te usługi wymagają rozszerzenia do zainstalowania. Jeśli wdrożono Maszynę wirtualną bez WinGA, można zainstalować najnowszą wersję agenta później.

### <a name="manual-installation"></a>Instalacja ręczna
Agent maszyny Wirtualnej systemu Windows można ręcznie zainstalować za pomocą pakietu Instalatora Windows. Instalacja ręczna może być konieczne utworzenie niestandardowego obrazu maszyny Wirtualnej, który jest wdrożony na platformie Azure. Aby ręcznie zainstalować agenta maszyny Wirtualnej systemu Windows, [Pobierz Instalator agenta maszyny Wirtualnej](http://go.microsoft.com/fwlink/?LinkID=394789).

Agent maszyny Wirtualnej mogą być instalowane przez dwukrotne kliknięcie pliku Instalatora Windows. Automatyczna lub z instalacji nienadzorowanej instalacji agenta maszyny Wirtualnej uruchom następujące polecenie:

```cmd
msiexec.exe /i WindowsAzureVmAgent.2.7.1198.778.rd_art_stable.160617-1120.fre /quiet
```

## <a name="detect-the-vm-agent"></a>Wykryj agenta maszyny Wirtualnej

### <a name="powershell"></a>PowerShell

Moduł programu PowerShell usługi Azure Resource Manager można pobrać informacji o maszynach wirtualnych platformy Azure. Aby wyświetlić informacje o maszynie Wirtualnej, takie jak stan inicjowania obsługi administracyjnej dla agenta maszyny Wirtualnej Azure, użyj [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm):

""powershell"Get-AzureRmVM
```

The following condensed example output shows the the *ProvisionVMAgent* property nested inside *OSProfile*. This property can be used to determine if the VM agent has been deployed to the VM:

```PowerShell
OSProfile                  :
  ComputerName             : myVM
  AdminUsername            : myUserName
  WindowsConfiguration     :
    ProvisionVMAgent       : True
    EnableAutomaticUpdates : True
```

Poniższy skrypt może służyć do zwrócenia zwięzły lista nazw maszyn wirtualnych i stan agenta maszyny Wirtualnej:

```PowerShell
$vms = Get-AzureRmVM

foreach ($vm in $vms) {
    $agent = $vm | Select -ExpandProperty OSProfile | Select -ExpandProperty Windowsconfiguration | Select ProvisionVMAgent
    Write-Host $vm.Name $agent.ProvisionVMAgent
}
```

### <a name="manual-detection"></a>Ręczne wykrywania
Po zalogowaniu się do maszyny Wirtualnej systemu Windows Azure, Menedżer zadań może służyć do sprawdzenia uruchomionych procesów. Aby sprawdzić, czy Agent maszyny Wirtualnej Azure, otwórz Menedżera zadań, kliknij przycisk *szczegóły* , a następnie wyszukaj nazwę procesu **WindowsAzureGuestAgent.exe**. Obecność tego procesu wskazuje, czy agent maszyny Wirtualnej jest zainstalowany.


## <a name="upgrade-the-vm-agent"></a>Uaktualnienie agenta maszyny Wirtualnej
Azure VM Agent dla systemu Windows zostanie automatycznie uaktualniony. Podczas wdrażania nowych maszyn wirtualnych na platformie Azure, otrzymają najnowsza wersja agenta maszyny Wirtualnej w czasie udostępniania maszyny Wirtualnej. Niestandardowe obrazy maszyny Wirtualnej należy ręcznie zaktualizować Aby dołączyć nowy agent maszyny Wirtualnej w czasie tworzenia obrazu.


## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat rozszerzeń maszyny Wirtualnej, zobacz [maszyny wirtualnej platformy Azure — omówienie rozszerzeń i funkcji](overview.md).