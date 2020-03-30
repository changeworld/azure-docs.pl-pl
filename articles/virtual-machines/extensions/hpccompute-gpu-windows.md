---
title: Rozszerzenie sterownika procesora graficznego NVIDIA — maszyny wirtualne systemu Windows platformy Azure
description: Rozszerzenie Microsoft Azure do instalowania sterowników gpu NVIDIA na maszynach wirtualnych z serii N z systemem Windows.
services: virtual-machines-windows
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/09/2019
ms.author: akjosh
ms.openlocfilehash: c388f433327b5328483f10fbef637a6fdfd08832
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250545"
---
# <a name="nvidia-gpu-driver-extension-for-windows"></a>Rozszerzenie sterownika procesora graficznego NVIDIA dla systemu Windows

## <a name="overview"></a>Omówienie

To rozszerzenie instaluje sterowniki GPU NVIDIA na maszynach wirtualnych z serii Windows N. W zależności od rodziny maszyn wirtualnych rozszerzenie instaluje sterowniki CUDA lub GRID. Podczas instalowania sterowników NVIDIA przy użyciu tego rozszerzenia użytkownik akceptuje i akceptuje warunki [umowy licencyjnej użytkownika końcowego firmy NVIDIA.](https://go.microsoft.com/fwlink/?linkid=874330) Podczas procesu instalacji maszyna wirtualna może ponownie uruchomić komputer, aby zakończyć konfigurację sterownika.

Instrukcje dotyczące ręcznej instalacji sterowników i bieżących obsługiwanych wersji są dostępne [tutaj.](https://docs.microsoft.com/azure/virtual-machines/windows/n-series-driver-setup)
Dostępne jest również rozszerzenie do instalowania sterowników GPU NVIDIA na [maszynach wirtualnych z serii N z systemem Linux.](hpccompute-gpu-linux.md)

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="operating-system"></a>System operacyjny

To rozszerzenie obsługuje następujące system operacyjny:

| Dystrybucja | Wersja |
|---|---|
| Windows 10 | Podstawowe |
| Windows Server 2016 | Podstawowe |
| Windows Server 2012 R2 | Podstawowe |

### <a name="internet-connectivity"></a>Łączność z Internetem

Rozszerzenie Microsoft Azure dla sterowników gpu NVIDIA wymaga, aby docelowa maszyna wirtualna była podłączona do Internetu i miała dostęp.

## <a name="extension-schema"></a>Schemat rozszerzenia

Poniżej przedstawiono schemat rozszerzenia.

```json
{
  "name": "<myExtensionName>",
  "type": "extensions",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <myVM>)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "NvidiaGpuDriverWindows",
    "typeHandlerVersion": "1.2",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="properties"></a>Właściwości

| Nazwa | Wartość / Przykład | Typ danych |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| wydawca | Microsoft.HpcCompute | ciąg |
| type | NvidiaGpuDriverWindows | ciąg |
| typHandlerVersion | 1.2 | int |


## <a name="deployment"></a>wdrażania

### <a name="azure-resource-manager-template"></a>Szablon usługi Azure Resource Manager 

Rozszerzenia maszyn wirtualnych platformy Azure można wdrożyć za pomocą szablonów usługi Azure Resource Manager. Szablony są idealne podczas wdrażania co najmniej jednej maszyny wirtualnej, które wymagają konfiguracji po wdrożeniu.

Konfiguracja JSON dla rozszerzenia maszyny wirtualnej może być zagnieżdżona wewnątrz zasobu maszyny wirtualnej lub umieszczona na głównym lub najwyższym poziomie szablonu JSON Menedżera zasobów. Położenie konfiguracji JSON wpływa na wartość nazwy i typu zasobu. Aby uzyskać więcej informacji, zobacz [Ustawianie nazwy i typu zasobów podrzędnych](../../azure-resource-manager/resource-manager-template-child-resource.md). 

W poniższym przykładzie przyjęto założenie, że rozszerzenie jest zagnieżdżone wewnątrz zasobu maszyny wirtualnej. Podczas zagnieżdżania zasobu rozszerzenia `"resources": []` JSON jest umieszczany w obiekcie maszyny wirtualnej.

```json
{
  "name": "myExtensionName",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', myVM)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "NvidiaGpuDriverWindows",
    "typeHandlerVersion": "1.2",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="powershell"></a>PowerShell

```powershell
Set-AzVMExtension
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Location "southcentralus" `
    -Publisher "Microsoft.HpcCompute" `
    -ExtensionName "NvidiaGpuDriverWindows" `
    -ExtensionType "NvidiaGpuDriverWindows" `
    -TypeHandlerVersion 1.2 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name NvidiaGpuDriverWindows `
  --publisher Microsoft.HpcCompute `
  --version 1.2 `
  --settings '{ `
  }'
```

## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i pomoc techniczna

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Dane dotyczące stanu wdrożeń rozszerzeń można pobrać z witryny Azure portal i przy użyciu platformy Azure PowerShell i interfejsu wiersza polecenia platformy Azure. Aby wyświetlić stan wdrożenia rozszerzeń dla danej maszyny Wirtualnej, uruchom następujące polecenie.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Dane wyjściowe wykonania rozszerzenia są rejestrowane w następującym katalogu:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.HpcCompute.NvidiaGpuDriverMicrosoft\
```

### <a name="error-codes"></a>Kody błędów

| Kod błędu | Znaczenie | Możliwe działanie |
| :---: | --- | --- |
| 0 | Operacja udana |
| 1 | Operacja zakończyła się sukcesem. Wymagane ponowne uruchomienie. |
| 100 | Operacja nie jest obsługiwana lub nie można jej ukończyć. | Możliwe przyczyny: Wersja programu PowerShell nie jest obsługiwana, rozmiar maszyny Wirtualnej nie jest maszyną wirtualną z serii N, niepowodzenie pobierania danych. Sprawdź pliki dziennika, aby ustalić przyczynę błędu. |
| 240, 840 | Limit czasu operacji. | Ponów próbę wykonania operacji. |
| -1 | Wystąpił wyjątek. | Sprawdź pliki dziennika, aby ustalić przyczynę wyjątku. |
| -5x | Operacja przerwana z powodu oczekującego ponownego uruchomienia. | Uruchom ponownie maszynę wirtualną. Instalacja będzie kontynuowana po ponownym uruchomieniu komputera. Odinstalowanie powinno być wywoływane ręcznie. |


### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz więcej pomocy w dowolnym momencie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na [forach MSDN Azure i Stack Overflow](https://azure.microsoft.com/support/community/). Alternatywnie można zgłosić zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc techniczną. Aby uzyskać informacje na temat korzystania z pomocy technicznej platformy Azure, przeczytaj często zadawane [pytania dotyczące pomocy technicznej platformy Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat rozszerzeń, zobacz [Rozszerzenia i funkcje maszyn wirtualnych dla systemu Windows](features-windows.md).

Aby uzyskać więcej informacji na temat maszyn wirtualnych z serii N, zobacz [Rozmiary maszyn wirtualnych zoptymalizowane pod kątem procesora GPU](../windows/sizes-gpu.md).
