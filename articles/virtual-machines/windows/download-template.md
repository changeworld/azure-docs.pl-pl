---
title: Pobieranie szablonu dla maszyny wirtualnej platformy Azure | Microsoft Docs
description: Pobierz templatefor maszynę wirtualną, aby pomóc w automatyzowaniu wdrożeń w modelu wdrażania Menedżer zasobów
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
ms.openlocfilehash: 37e833e5ba2fcbc9fcfe0a27d5b607a5c6cdd55b
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70079529"
---
# <a name="download-the-template-for-a-vm"></a>Pobieranie szablonu dla maszyny wirtualnej
Podczas tworzenia maszyny wirtualnej na platformie Azure przy użyciu portalu lub programu PowerShell automatycznie tworzony jest szablon Menedżer zasobów. Możesz użyć tego szablonu, aby szybko zduplikować wdrożenie. Szablon zawiera informacje na temat wszystkich zasobów w grupie zasobów. W przypadku maszyny wirtualnej oznacza to, że szablon zawiera wszystkie elementy, które są tworzone w ramach obsługi maszyny wirtualnej w tej grupie zasobów, w tym zasoby sieciowe.

## <a name="download-the-template-using-the-portal"></a>Pobieranie szablonu przy użyciu portalu
1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).
2. Jedno z menu po lewej stronie, wybierz pozycję **Virtual Machines**.
3. Wybierz maszynę wirtualną z listy.
4. Wybierz pozycję **Eksportuj szablon**.
5. Wybierz pozycję **Pobierz** z menu u góry i Zapisz plik zip na komputerze lokalnym.
6. Otwórz plik zip i Wyodrębnij pliki do folderu. Plik. zip zawiera:
   
   * Wdróż. ps1
   * deploy.sh 
   * Deployer. RB
   * DeploymentHelper.cs
   * Parameters. JSON
   * template.json

Plik Template. JSON jest szablonem.

## <a name="download-the-template-using-powershell"></a>Pobieranie szablonu przy użyciu programu PowerShell
Plik szablonu JSON można także pobrać przy użyciu polecenia cmdlet [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup) . Możesz użyć `-path` parametru, aby podać nazwę pliku i ścieżkę pliku. JSON. Ten przykład pokazuje, jak pobrać szablon dla grupy zasobów o nazwie Moja **zasobów** do folderu **C:\users\public\downloads** na komputerze lokalnym.

```powershell
    Export-AzResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o wdrażaniu zasobów przy użyciu szablonów, zobacz [Przewodnik po szablonach Menedżer zasobów](../../azure-resource-manager/resource-manager-template-walkthrough.md).

