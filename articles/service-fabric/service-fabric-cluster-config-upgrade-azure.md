---
title: Uaktualnianie konfiguracji klastra sieci szkieletowej usług Azure
description: Dowiedz się, jak uaktualnić konfigurację, która uruchamia klaster sieci szkieletowej usług na platformie Azure przy użyciu szablonu Menedżera zasobów.
author: dkkapur
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: 476a2d910b916ea29132b108478d06f756454813
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75463285"
---
# <a name="upgrade-the-configuration-of-a-cluster-in-azure"></a>Uaktualnianie konfiguracji klastra na platformie Azure 

W tym artykule opisano sposób dostosowywania różnych ustawień sieci szkieletowej klastra sieci szkieletowej usług. W przypadku klastrów hostowanych na platformie Azure można dostosować ustawienia za pośrednictwem [witryny Azure portal](https://portal.azure.com) lub przy użyciu szablonu usługi Azure Resource Manager.

> [!NOTE]
> Nie wszystkie ustawienia są dostępne w portalu i najlepszym rozwiązaniem jest [dostosowanie go przy użyciu szablonu usługi Azure Resource Manager;](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code) Portal jest tylko dla dewelopera\Test szkieletu usługi.
> 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="customize-cluster-settings-using-resource-manager-templates"></a>Dostosowywanie ustawień klastra przy użyciu szablonów Menedżera zasobów
Klastry platformy Azure można skonfigurować za pomocą szablonu Menedżera zasobów JSON. Aby dowiedzieć się więcej o różnych ustawieniach, zobacz [Ustawienia konfiguracji klastrów](service-fabric-cluster-fabric-settings.md). Na przykład poniższe kroki pokazują, jak dodać nowe ustawienie *MaxDiskQuotaInMB* do sekcji *Diagnostyka* przy użyciu Eksploratora zasobów platformy Azure.

1. Przejdź do strony https://resources.azure.com
2. Przejdź do subskrypcji, rozszerzając **subskrypcje** -> **\<Twoja subskrypcja>**  ->  **resourceGrupy** -> **\<grupy zasobów>**  ->  **dostawców** -> **Microsoft.ServiceFabric** -> **klastry** -> **\<Nazwa klastra>**
3. W prawym górnym rogu wybierz pozycję **Odczyt/Zapis.**
4. Wybierz **edytuj** i `fabricSettings` zaktualizuj element JSON i dodaj nowy element:

```json
      {
        "name": "Diagnostics",
        "parameters": [
          {
            "name": "MaxDiskQuotaInMB",
            "value": "65536"
          }
        ]
      }
```

Ustawienia klastra można również dostosować w jeden z następujących sposobów za pomocą usługi Azure Resource Manager:

- Użyj [witryny Azure Portal,](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template) aby wyeksportować i zaktualizować szablon Menedżera zasobów.
- Użyj [programu PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-powershell) do eksportowania i aktualizowania szablonu Menedżera zasobów.
- Użyj [interfejsu wiersza polecenia platformy Azure,](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-cli) aby wyeksportować i zaktualizować szablon Menedżera zasobów.
- Użyj polecenia Azure PowerShell [Set-AzServiceFabricSetting](https://docs.microsoft.com/powershell/module/az.servicefabric/Set-azServiceFabricSetting) i [Remove-AzServiceFabricSetting,](https://docs.microsoft.com/powershell/module/az.servicefabric/Remove-azServiceFabricSetting) aby bezpośrednio zmodyfikować to ustawienie.
- Użyj poleceń [ustawień klastra az sf](https://docs.microsoft.com/cli/azure/sf/cluster/setting) platformy Azure, aby bezpośrednio zmodyfikować to ustawienie.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [ustawieniach klastra sieci szkieletowej usług](service-fabric-cluster-fabric-settings.md).
* Dowiedz się, jak [skalować klaster w i na zewnątrz](service-fabric-cluster-scale-up-down.md).
