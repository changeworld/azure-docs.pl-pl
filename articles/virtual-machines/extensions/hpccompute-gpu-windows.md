---
title: Rozszerzenie sterowników procesora GPU NVIDIA — maszyn wirtualnych Windows Azure | Dokumentacja firmy Microsoft
description: Rozszerzenia Microsoft Azure w celu zainstalowania sterowników procesora GPU firmy NVIDIA na serii N obliczeniowych maszyn wirtualnych z systemem Windows.
services: virtual-machines-windows
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/09/2019
ms.author: roiyz
ms.openlocfilehash: 004d6125de6762303db91f3a5ef9ffa16e6e501f
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705951"
---
# <a name="nvidia-gpu-driver-extension-for-windows"></a>Procesor GPU NVIDIA sterownika rozszerzenia dla Windows

## <a name="overview"></a>Omówienie

To rozszerzenie instaluje sterowniki procesora GPU firmy NVIDIA na maszynach wirtualnych serii N Windows. W zależności od rodziny maszyn wirtualnych rozszerzenia instaluje sterowniki CUDA lub SIATKĘ. Po zainstalowaniu NVIDIA wewnętrzne sterowniki przy użyciu tego rozszerzenia akceptowanie i wyrażenie zgody na warunki [Umowa licencyjna użytkownika oprogramowania firmy NVIDIA](https://go.microsoft.com/fwlink/?linkid=874330). W procesie instalacji maszyny Wirtualnej może ponowny rozruch, aby ukończyć instalację sterownika.

Dostępne są instrukcje dotyczące ręcznego instalowania sterowników i bieżące wersje obsługiwanych [tutaj](https://docs.microsoft.com/azure/virtual-machines/windows/n-series-driver-setup).
Rozszerzenie jest również dostępna do zainstalowania sterowników procesora GPU NVIDIA na [maszyny wirtualne z serii N Linux](hpccompute-gpu-linux.md).

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="operating-system"></a>System operacyjny

To rozszerzenie obsługuje następujące OSs:

| Dystrybucja | Version |
|---|---|
| Windows 10 | Core |
| Windows Server 2016 | Core |
| Windows Server 2012R2 | Core |

### <a name="internet-connectivity"></a>Łączność z Internetem

Rozszerzenie Microsoft Azure dla sterowników procesora GPU NVIDIA wymaga, aby docelowa maszyna wirtualna jest połączony z Internetem, a następnie mają dostęp.

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
    "typeHandlerVersion": "1.2",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="properties"></a>Właściwości

| Name (Nazwa) | Wartość / przykład | Typ danych |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.HpcCompute | ciąg |
| type | NvidiaGpuDriverWindows | ciąg |
| typeHandlerVersion | 1.2 | int |


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

## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i pomocy technicznej

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Dane dotyczące stanu wdrożeń rozszerzenia można pobrać z witryny Azure portal i za pomocą programu Azure PowerShell i wiersza polecenia platformy Azure. Aby wyświetlić stan wdrożenia rozszerzeń dla danej maszyny Wirtualnej, uruchom następujące polecenie.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
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
| 100 | Operacja nie obsługiwany lub nie można ukończyć. | Możliwe przyczyny: Nieobsługiwana wersja programu PowerShell, rozmiar maszyny Wirtualnej nie jest maszyn wirtualnych serii N niepowodzenia pobierania danych. Sprawdź pliki dziennika, aby ustalić przyczynę błędu. |
| 240, 840 | Limit czasu operacji. | Ponów próbę wykonania operacji. |
| -1 | Wystąpił wyjątek. | Sprawdź pliki dziennika, aby ustalić przyczynę wystąpienia wyjątku. |
| -5 x | Operacja została przerwana ze względu na oczekujące na ponowny rozruch. | Uruchom ponownie maszynę Wirtualną. Instalacja będzie kontynuowana po ponownym uruchomieniu. Odinstaluj powinno zostać uruchomione ręcznie. |


### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz dodatkowej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ze ekspertów platformy Azure na [forów platformy Azure z subskrypcją MSDN i Stack Overflow](https://azure.microsoft.com/support/community/). Alternatywnie mogą zgłaszać zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz Uzyskaj pomoc techniczną. Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, przeczytaj [pomocy technicznej Microsoft Azure — często zadawane pytania](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat rozszerzeń, zobacz [rozszerzeń maszyn wirtualnych i funkcji dla Windows](features-windows.md).

Aby uzyskać więcej informacji na temat maszyn wirtualnych serii N, zobacz [rozmiarów maszyn wirtualnych GPU zoptymalizowanych pod kątem](../windows/sizes-gpu.md).
