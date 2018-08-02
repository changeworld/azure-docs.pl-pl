---
title: Rozszerzenie sterowników procesora GPU NVIDIA — maszyn wirtualnych Windows Azure | Dokumentacja firmy Microsoft
description: Rozszerzenia Microsoft Azure w celu zainstalowania sterowników procesora GPU firmy NVIDIA na serii N obliczeniowych maszyn wirtualnych z systemem Windows.
services: virtual-machines-windows
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/03/2018
ms.author: roiyz
ms.openlocfilehash: 463a00823997f1acfb65fdd739a093e556982a61
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39411954"
---
# <a name="nvidia-gpu-driver-extension-for-windows"></a>Procesor GPU NVIDIA sterownika rozszerzenia dla Windows

## <a name="overview"></a>Przegląd

To rozszerzenie instaluje sterowniki procesora GPU firmy NVIDIA na maszynach wirtualnych serii N Windows. W zależności od rodziny maszyn wirtualnych rozszerzenia instaluje sterowniki CUDA lub SIATKĘ. Po zainstalowaniu NVIDIA wewnętrzne sterowniki przy użyciu tego rozszerzenia, jesteś akceptowanie i zgodę na warunki umowy licencyjnej firmy NVIDIA. W procesie instalacji maszyny wirtualnej może ponowny rozruch, aby ukończyć instalację sterownika.

Rozszerzenie jest również dostępna do zainstalowania sterowników procesora GPU NVIDIA na [maszyny wirtualne z serii N Linux](hpccompute-gpu-linux.md).

Warunki umowy licencyjnej użytkownika oprogramowania firmy NVIDIA znajdują się w folderze- https://go.microsoft.com/fwlink/?linkid=874330

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="operating-system"></a>System operacyjny

To rozszerzenie obsługuje następujące OSs:

| Dystrybucja | Wersja |
|---|---|
| Windows 10 | Podstawowe |
| Windows Server 2016 | Podstawowe |
| Windows Server 2012 R2 | Podstawowe |

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
    "type": "NvidiaGpuDriverWindows",
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
| type | NvidiaGpuDriverWindows | ciąg |
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
    "type": "NvidiaGpuDriverWindows",
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
    -ExtensionName "NvidiaGpuDriverWindows" `
    -ExtensionType "NvidiaGpuDriverWindows" `
    -TypeHandlerVersion 1.0 `
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

Dane wyjściowe wykonania rozszerzenie jest zarejestrowane do następującego katalogu:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.HpcCompute.NvidiaGpuDriverMicrosoft\
```

### <a name="error-codes"></a>Kody błędów

| Kod błędu | Znaczenie | Możliwe działania |
| :---: | --- | --- |
| 0 | Operacja zakończona powodzeniem |
| 1 | Operacja zakończona powodzeniem. Wymagany jest ponowny rozruch. |
| 4, 10 | Limit czasu operacji. | Ponów próbę wykonania operacji.
| -1 | Wystąpił wyjątek. | Sprawdź pliki dziennika, aby ustalić przyczynę wystąpienia wyjątku. |
| -5 x | Operacja została przerwana ze względu na oczekujące na ponowny rozruch. | Uruchom ponownie maszynę Wirtualną. Instalacja będzie kontynuowana po ponownym uruchomieniu. Odinstaluj powinno zostać uruchomione ręcznie. |


### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz dodatkowej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ze ekspertów platformy Azure na [forów platformy Azure z subskrypcją MSDN i Stack Overflow](https://azure.microsoft.com/support/community/). Alternatywnie mogą zgłaszać zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz Uzyskaj pomoc techniczną. Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, przeczytaj [pomocy technicznej Microsoft Azure — często zadawane pytania](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat rozszerzeń, zobacz [rozszerzeń maszyn wirtualnych i funkcji dla Windows](features-windows.md).

Aby uzyskać więcej informacji na temat maszyn wirtualnych serii N, zobacz [rozmiarów maszyn wirtualnych GPU zoptymalizowanych pod kątem](../windows/sizes-gpu.md).