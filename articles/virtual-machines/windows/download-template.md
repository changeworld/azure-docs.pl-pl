---
title: Pobierz szablon maszyny wirtualnej Azure | Dokumentacja firmy Microsoft
description: Pobierz templatefor maszyny Wirtualnej, aby ułatwić Automatyzowanie wdrożeń w modelu wdrażania usługi Resource Manager
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
ms.openlocfilehash: 93ed84cb146119c877c3a143c5f7af9ca8ba0656
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2017
ms.locfileid: "26055793"
---
# <a name="download-the-template-for-a-vm"></a>Pobieranie szablonu dla maszyny wirtualnej
Po utworzeniu maszyny Wirtualnej na platformie Azure za pomocą portalu lub programu PowerShell szablonu usługi Resource Manager jest tworzony automatycznie dla Ciebie. Ten szablon umożliwia szybkie duplikowanie wdrożenia. Szablon zawiera informacje na temat wszystkich zasobów w grupie zasobów. Dla maszyny wirtualnej oznacza to, że szablon zawiera wszystko, co jest tworzony w związku z maszyny Wirtualnej w ramach grupy zasobów, włącznie z zasobów sieciowych.

## <a name="download-the-template-using-the-portal"></a>Pobieranie szablonu przy użyciu portalu
1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).
2. Menu po lewej stronie, należy wybrać **maszyn wirtualnych**.
3. Wybierz maszynę wirtualną z listy.
4. Wybierz **skryptu automatyzacji**.
5. Wybierz **Pobierz** w menu u góry, a następnie zapisz plik zip na komputerze lokalnym.
6. Otwórz plik zip i Wyodrębnij pliki do folderu. Plik zip zawiera:
   
   * Deploy.ps1
   * Deploy.sh 
   * deployer.RB
   * DeploymentHelper.cs
   * parameters.JSON następującym kodem
   * Template.JSON

Plik template.json jest szablon.

## <a name="download-the-template-using-powershell"></a>Pobieranie szablonu przy użyciu programu PowerShell
Możesz również pobrać JSON szablonu plików przy użyciu [Export-AzureRMResourceGroup](https://msdn.microsoft.com/library/mt715427.aspx) polecenia cmdlet. Można użyć `-path` parametr, aby podać nazwę pliku i ścieżkę do pliku JSON. W tym przykładzie pokazano, jak pobrać szablonu dla grupy zasobów o nazwie **myResourceGroup** do **C:\users\public\downloads** folderu na komputerze lokalnym.

```powershell
    Export-AzureRmResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o wdrażaniu zasobów przy użyciu szablonów, zobacz [Przewodnik po szablonie usługi Resource Manager](../../azure-resource-manager/resource-manager-template-walkthrough.md).

