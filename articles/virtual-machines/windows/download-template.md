---
title: Pobieranie szablonu dla maszyny Wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Pobierz templatefor maszyny Wirtualnej w celu automatyzacji wdrożeń w modelu wdrażania usługi Resource Manager
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 51ef4f51-0942-4249-afea-4a3f87ce1ff8
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/17/2017
ms.author: cynthn
ms.openlocfilehash: 4ef0c09d2d2ea2ed06708342ab45abcaf149c23e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61075884"
---
# <a name="download-the-template-for-a-vm"></a>Pobieranie szablonu dla maszyny wirtualnej
Podczas tworzenia maszyny Wirtualnej na platformie Azure przy użyciu portalu lub programu PowerShell, szablon usługi Resource Manager jest tworzony automatycznie dla Ciebie. Ten szablon umożliwia szybkie duplikowanie wdrożenia. Szablon zawiera informacje o wszystkich zasobów w grupie zasobów. Dla maszyny wirtualnej oznacza to, że szablon zawiera wszystko, co jest tworzony w odniesieniu do maszyny Wirtualnej w tej grupie zasobów, łącznie z zasobami sieciowymi.

## <a name="download-the-template-using-the-portal"></a>Pobierz szablon przy użyciu portalu
1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).
2. Menu po lewej stronie, należy wybrać **maszyn wirtualnych**.
3. Wybierz maszynę wirtualną z listy.
4. Wybierz **Eksportuj szablon**.
5. Wybierz **Pobierz** menu u góry, a następnie zapisz plik zip na komputer lokalny.
6. Otwórz plik zip i wyodrębnić pliki do folderu. Plik zip zawiera plik:
   
   * deploy.ps1
   * Deploy.sh 
   * deployer.RB
   * DeploymentHelper.cs
   * parameters.json
   * template.json

Plik template.json jest szablon.

## <a name="download-the-template-using-powershell"></a>Pobierz szablon przy użyciu programu PowerShell
Możesz również pobrać JSON szablonu plików przy użyciu [AzResourceGroup eksportu](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup) polecenia cmdlet. Możesz użyć `-path` parametru, aby podać nazwę pliku i ścieżkę pliku JSON. W tym przykładzie pokazano, jak pobrać szablon dla grupy zasobów o nazwie **myResourceGroup** do **C:\users\public\downloads** folderu na komputerze lokalnym.

```powershell
    Export-AzResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat wdrażania zasobów za pomocą szablonów, zobacz [Przewodnik po szablonie usługi Resource Manager](../../azure-resource-manager/resource-manager-template-walkthrough.md).

