---
title: Rozszerzenie sterowników procesora GPU NVIDIA — maszyny wirtualne systemu Linux platformy Azure | Dokumentacja firmy Microsoft
description: Rozszerzenie Azure firmy Microsoft w celu zainstalowania sterowników procesora GPU firmy NVIDIA na serii N obliczeniowych maszyn wirtualnych z systemem Linux.
services: virtual-machines-linux
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/03/2018
ms.author: danis
ms.openlocfilehash: 1febf81745ed804de59113da4f48376e9564b68f
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37450256"
---
# <a name="nvidia-gpu-driver-extension-for-linux"></a>Rozszerzenie sterowników procesora GPU NVIDIA dla systemu Linux

## <a name="overview"></a>Przegląd

To rozszerzenie instaluje sterowniki procesora GPU firmy NVIDIA na maszynach wirtualnych serii N Linux. W zależności od rodziny maszyn wirtualnych rozszerzenia instaluje sterowniki CUDA lub SIATKĘ. Po zainstalowaniu NVIDIA wewnętrzne sterowniki przy użyciu tego rozszerzenia, jesteś akceptowanie i zgodę na warunki umowy licencyjnej firmy NVIDIA. W procesie instalacji maszyny wirtualnej może ponowny rozruch, aby ukończyć instalację sterownika.

Rozszerzenie jest również dostępna do zainstalowania sterowników procesora GPU NVIDIA na [maszyn wirtualnych serii N Windows](hpccompute-gpu-windows.md).

Warunki umowy licencyjnej użytkownika oprogramowania firmy NVIDIA znajdują się w folderze- https://go.microsoft.com/fwlink/?linkid=874330

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="operating-system"></a>System operacyjny

To rozszerzenie obsługuje następujące OSs:

| Dystrybucja | Wersja |
|---|---|
| Linux: Ubuntu | 16.04 LTS |
| Linux: Red Hat Enterprise Linux | 7.3, 7.4 |
| Linux: CentOS | 7.3, 7.4 |

### <a name="internet-connectivity"></a>Łączność z Internetem

Rozszerzenie Microsoft Azure dla sterowników procesora GPU NVIDIA wymaga, docelowej maszyny wirtualnej jest połączony z Internetem i mają dostęp.

## <a name="extension-schema"></a>Schemat rozszerzenia

Następujący kod JSON zawiera schemat dla rozszerzenia.

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
    "type": "NvidiaGpuDriverLinux",
    "typeHandlerVersion": "1.0",
    "settings": {
    }
  }
}
```

### <a name="property-values"></a>Wartości właściwości

| Name (Nazwa) | Wartość / przykład | Typ danych |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | data |
| Wydawcy | Microsoft.HpcCompute | ciąg |
| type | NvidiaGpuDriverLinux | ciąg |
| typeHandlerVersion | 1.0 | Int |


## <a name="deployment"></a>Wdrożenie


### <a name="azure-resource-manager-template"></a>Szablon usługi Azure Resource Manager 

Rozszerzenia maszyn wirtualnych platformy Azure można wdrażać przy użyciu szablonów usługi Azure Resource Manager. Szablony są idealnym rozwiązaniem w przypadku wdrażania maszyn wirtualnych, które wymagają konfiguracji po wdrożeniu.

Konfiguracji JSON dla rozszerzenia maszyny wirtualnej mogą być zagnieżdżone wewnątrz zasobu maszyny wirtualnej lub umieszczone w katalogu głównego lub najwyższego poziomu szablon JSON usługi Resource Manager. Położenie konfiguracji JSON ma wpływ na wartości nazwy i typu zasobu. Aby uzyskać więcej informacji, zobacz [Ustaw nazwę i typ zasobów podrzędnych](../../azure-resource-manager/resource-manager-template-child-resource.md). 

W poniższym przykładzie założono, że rozszerzenie jest zagnieżdżona w obrębie zasobu maszyny wirtualnej. Zagnieżdżanie rozszerzenia zasobu, za pomocą pliku JSON jest umieszczany w `"resources": []` obiektu maszyny wirtualnej.

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
    "type": "NvidiaGpuDriverLinux",
    "typeHandlerVersion": "1.0",
    "settings": {
    }
  }
}
```

### <a name="powershell"></a>PowerShell

```powershell
Set-AzureRmVMExtension
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Location "southcentralus" `
    -Publisher "Microsoft.HpcCompute" `
    -ExtensionName "NvidiaGpuDriverLinux" `
    -ExtensionType "NvidiaGpuDriverLinux" `
    -TypeHandlerVersion 1.0 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name NvidiaGpuDriverLinux `
  --publisher Microsoft.HpcCompute `
  --version 1.0 `
  --settings '{ `
  }'
```

## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i pomocy technicznej

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Dane dotyczące stanu wdrożeń rozszerzenia można pobrać z witryny Azure portal i za pomocą programu Azure PowerShell i wiersza polecenia platformy Azure. Aby wyświetlić stan wdrożenia rozszerzeń dla danej maszyny Wirtualnej, uruchom następujące polecenie.

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Dane wyjściowe wykonywania rozszerzenia jest rejestrowany w następującym pliku:

```bash
/var/log/azure/nvidia-vmext-status
```

### <a name="exit-codes"></a>Kody zakończenia

| Kod zakończenia | Znaczenie | Możliwe działania |
| :---: | --- | --- |
| 0 | Operacja zakończona powodzeniem |
| 1 | Niepoprawne użycie rozszerzenia. | Skontaktuj się z pomocą techniczną, podając dziennika danych wyjściowych wykonywania. |
| 10 | Usługi integracji systemu Linux dla funkcji Hyper-V i na platformie Azure nie dostępne lub zainstalowane. | Sprawdzanie danych wyjściowych lspci. |
| 11 | Procesor GPU NVIDIA nie można odnaleźć tego rozmiaru maszyny Wirtualnej. | Użyj [obsługiwany rozmiar maszyny Wirtualnej i systemu operacyjnego](../linux/n-series-driver-setup.md). |
| 14 | Operacja powiodła się | |
| 21 | Aktualizacja nie powiodła się w systemie Ubuntu | Sprawdzanie danych wyjściowych "Aktualizuj" sudo"polecenia apt-get" |


### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz dodatkowej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ze ekspertów platformy Azure na [forów platformy Azure z subskrypcją MSDN i Stack Overflow](https://azure.microsoft.com/support/community/). Alternatywnie mogą zgłaszać zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz Uzyskaj pomoc techniczną. Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, przeczytaj [pomocy technicznej Microsoft Azure — często zadawane pytania](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat rozszerzeń, zobacz [rozszerzenia maszyn wirtualnych i funkcji dla systemu Linux](features-linux.md).

Aby uzyskać więcej informacji na temat maszyn wirtualnych serii N, zobacz [rozmiarów maszyn wirtualnych GPU zoptymalizowanych pod kątem](../linux/sizes-gpu.md).