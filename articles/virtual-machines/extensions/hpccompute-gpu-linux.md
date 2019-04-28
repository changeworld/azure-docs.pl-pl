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
ms.date: 02/11/2019
ms.author: roiyz
ms.openlocfilehash: 5a184c72da8af0d451902a164c8b71a94a01883f
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62129080"
---
# <a name="nvidia-gpu-driver-extension-for-linux"></a>Rozszerzenie sterowników procesora GPU NVIDIA dla systemu Linux

## <a name="overview"></a>Omówienie

To rozszerzenie instaluje sterowniki procesora GPU firmy NVIDIA na maszynach wirtualnych serii N Linux. W zależności od rodziny maszyn wirtualnych rozszerzenia instaluje sterowniki CUDA lub SIATKĘ. Po zainstalowaniu NVIDIA wewnętrzne sterowniki przy użyciu tego rozszerzenia akceptowanie i wyrażenie zgody na warunki [Umowa licencyjna użytkownika oprogramowania firmy NVIDIA](https://go.microsoft.com/fwlink/?linkid=874330). W procesie instalacji maszyny Wirtualnej może ponowny rozruch, aby ukończyć instalację sterownika.

Rozszerzenie jest również dostępna do zainstalowania sterowników procesora GPU NVIDIA na [maszyn wirtualnych serii N Windows](hpccompute-gpu-windows.md).

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="operating-system"></a>System operacyjny

To rozszerzenie obsługuje poniższe dystrybucje systemu operacyjnego, w zależności od obsługi sterowników dla określonej wersji systemu operacyjnego.

| Dystrybucja | Wersja |
|---|---|
| W systemie Linux: Ubuntu | 16.04 LTS, 18.04 LTS |
| W systemie Linux: Red Hat Enterprise Linux | 7.3, 7.4, 7.5, 7.6 |
| W systemie Linux: CentOS | 7.3, 7.4, 7.5, 7.6 |

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
    "type": "NvidiaGpuDriverLinux",
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
| Wydawcy | Microsoft.HpcCompute | string |
| type | NvidiaGpuDriverLinux | string |
| typeHandlerVersion | 1.2 | int |

### <a name="settings"></a>Ustawienia

Wszystkie ustawienia są opcjonalne. Domyślnym zachowaniem jest nie aktualizacja jądra Jeśli wymagane do instalacji sterowników, zainstaluj najnowszy obsługiwany sterownik i toolkit CUDA (o ile dotyczy).

| Name (Nazwa) | Opis | Wartość domyślna | Prawidłowe wartości | Typ danych |
| ---- | ---- | ---- | ---- | ---- |
| updateOS | Aktualizacji jądra, nawet jeśli nie są wymagane do instalacji sterowników | false | wartość true, false | wartość logiczna |
| driverVersion | NV: Wersja sterownika siatki<br> NC/ND: Wersja zestawu narzędzi CUDA. Najnowsze sterowniki dla architektury CUDA wybrana są instalowane automatycznie. | najnowsza | GRID: "418.70", "410.92", "410.71", "390.75", "390.57", "390.42"<br> CUDA: "10.0.130", "9.2.88", "9.1.85" | string |
| installCUDA | Zainstaluj zestaw narzędzi CUDA. Istotne tylko w przypadku maszyny wirtualne z serii NC/ND. | true | wartość true, false | wartość logiczna |


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
    -ExtensionName "NvidiaGpuDriverLinux" `
    -ExtensionType "NvidiaGpuDriverLinux" `
    -TypeHandlerVersion 1.2 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Poniższy przykład odzwierciedla powyższe przykłady usługi Azure Resource Manager i programu PowerShell i dodaje także ustawienia niestandardowe na przykład do instalowania sterowników innych niż domyślne. W szczególności aktualizacji jądra systemu operacyjnego i instaluje wybrany sterownik CUDA toolkit wersji.

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name NvidiaGpuDriverLinux `
  --publisher Microsoft.HpcCompute `
  --version 1.2 `
  --settings '{ `
    "updateOS": true, `
    "driverVersion": "9.1.85", `
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

Dane wyjściowe wykonywania rozszerzenia jest rejestrowany w następującym pliku:

```bash
/var/log/azure/nvidia-vmext-status
```

### <a name="exit-codes"></a>Kody zakończenia

| Kod zakończenia | Znaczenie | Możliwe działania |
| :---: | --- | --- |
| 0 | Operacja zakończona powodzeniem |
| 1 | Nieprawidłowe użycie rozszerzenia | Sprawdź dziennik wyjścia wykonywania |
| 10 | Usługi integracji systemu Linux dla funkcji Hyper-V i na platformie Azure nie dostępne lub zainstalowane | Sprawdzanie danych wyjściowych lspci |
| 11 | Procesor GPU NVIDIA nie można odnaleźć tego rozmiaru maszyny Wirtualnej | Użyj [obsługiwanych systemach operacyjnych i rozmiar maszyny Wirtualnej](../linux/n-series-driver-setup.md) |
| 12 | Oferta obrazu nie jest obsługiwane |
| 13 | Rozmiar maszyny Wirtualnej nie jest obsługiwane | Wdrażanie przy użyciu maszyn wirtualnych serii N |
| 14 | Operacja powiodła się | Sprawdź dziennik wyjścia wykonywania |


### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz dodatkowej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ze ekspertów platformy Azure na [forów platformy Azure z subskrypcją MSDN i Stack Overflow](https://azure.microsoft.com/support/community/). Alternatywnie mogą zgłaszać zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz Uzyskaj pomoc techniczną. Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, przeczytaj [pomocy technicznej Microsoft Azure — często zadawane pytania](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat rozszerzeń, zobacz [rozszerzenia maszyn wirtualnych i funkcji dla systemu Linux](features-linux.md).

Aby uzyskać więcej informacji na temat maszyn wirtualnych serii N, zobacz [rozmiarów maszyn wirtualnych GPU zoptymalizowanych pod kątem](../linux/sizes-gpu.md).
