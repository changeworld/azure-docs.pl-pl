---
title: Pobierz szablon maszyny Wirtualnej platformy Azure
description: Pobierz szablon dla maszyny Wirtualnej, aby ułatwić automatyzację wdrożeń w modelu wdrażania Menedżera zasobów
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 51ef4f51-0942-4249-afea-4a3f87ce1ff8
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 11/17/2017
ms.author: cynthn
ms.openlocfilehash: c73026515f0d7fde4e2f82838696700b1bb17c77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033543"
---
# <a name="download-the-template-for-a-vm"></a>Pobieranie szablonu dla maszyny wirtualnej
Podczas tworzenia maszyny Wirtualnej na platformie Azure przy użyciu portalu lub programu PowerShell szablon Menedżera zasobów jest tworzony automatycznie dla Ciebie. Za pomocą tego szablonu można szybko powielić wdrożenie. Szablon zawiera informacje o wszystkich zasobach w grupie zasobów. Dla maszyny wirtualnej oznacza to, że szablon zawiera wszystko, co jest tworzone w celu obsługi maszyny Wirtualnej w tej grupie zasobów, w tym zasobów sieciowych.

## <a name="download-the-template-using-the-portal"></a>Pobierz szablon za pomocą portalu
1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).
2. Jedno z lewego menu wybierz opcję **Maszyny wirtualne**.
3. Wybierz maszynę wirtualną z listy.
4. Wybierz **pozycję Eksportuj szablon**.
5. Wybierz **polecenie Pobierz** z menu u góry i zapisz plik zip na komputerze lokalnym.
6. Otwórz plik zip i wyodrębnij pliki do folderu. Plik .zip zawiera:
   
   * parameters.json
   * plik template.json

Plik template.json jest szablonem.

## <a name="download-the-template-using-powershell"></a>Pobieranie szablonu przy użyciu programu PowerShell
Plik szablonu .json można również pobrać za pomocą polecenia cmdlet [Export-AzResourceGroup.](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup) Za pomocą `-path` tego parametru można podać nazwę pliku i ścieżkę dla pliku .json. W tym przykładzie pokazano, jak pobrać szablon grupy zasobów o nazwie **myResourceGroup** do folderu **C:\users\public\downloads** na komputerze lokalnym.

```powershell
    Export-AzResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o wdrażaniu zasobów przy użyciu szablonów, zobacz Przewodnik [po szablonie Menedżera zasobów](../../azure-resource-manager/resource-manager-template-walkthrough.md).

