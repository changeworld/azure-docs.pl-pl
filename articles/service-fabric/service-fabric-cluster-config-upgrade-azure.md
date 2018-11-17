---
title: Uaktualnij konfigurację klastra usługi Azure Service Fabric | Dokumentacja firmy Microsoft
description: Dowiedz się, jak uaktualnić konfigurację, która działa w klastrze usługi Service Fabric na platformie Azure przy użyciu szablonu usługi Resource Manager.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: 66296cc6-9524-4c6a-b0a6-57c253bdf67e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: 9323b393edb808f3d2d069f868deb0b67cd0c871
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2018
ms.locfileid: "51855189"
---
# <a name="upgrade-the-configuration-of-a-cluster-in-azure"></a>Uaktualnij konfigurację klastra na platformie Azure 

W tym artykule opisano sposób dostosowywania różne ustawienia sieci szkieletowej klastra usługi Service Fabric. W przypadku klastrów hostowanych na platformie Azure, można dostosować ustawienia za pośrednictwem [witryny Azure portal](https://portal.azure.com) lub przy użyciu szablonu usługi Azure Resource Manager.

> [!NOTE]
> Nie wszystkie ustawienia są dostępne w portalu. W przypadku, gdy ustawienie wymienione poniżej nie jest dostępna za pośrednictwem portalu dostosować ją przy użyciu szablonu usługi Azure Resource Manager.> 

## <a name="customize-cluster-settings-using-resource-manager-templates"></a>Dostosowywanie ustawień klastra przy użyciu szablonów usługi Resource Manager
Klastry platformy Azure można skonfigurować przy użyciu szablonu usługi Resource Manager w formacie JSON. Aby dowiedzieć się więcej o różnych ustawieniach, zobacz [ustawienia konfiguracji dla klastrów](service-fabric-cluster-fabric-settings.md). Na przykład poniższe kroki pokazują, jak dodać nowe ustawienie *MaxDiskQuotaInMB* do *diagnostyki* sekcji przy użyciu usługi Azure Resource Explorer.

1. Przejdź do strony https://resources.azure.com
2. Przejdź do swojej subskrypcji, rozwijając **subskrypcje** -> **\<Twoja subskrypcja >** -> **resourceGroups**  ->   **\<Your grupa zasobów >** -> **dostawców** -> **Microsoft.ServiceFabric**  ->  **klastrów** -> **\<Your Nazwa_klastra >**
3. W prawym górnym rogu, wybierz **odczytu/zapisu.**
4. Wybierz **Edytuj** i zaktualizuj `fabricSettings` elementu JSON i Dodaj nowy element:

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

Można również dostosować ustawienia klastra w jednym z następujących sposobów przy użyciu usługi Azure Resource Manager:

- Użyj [witryny Azure portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template) do eksportowania i aktualizowanie szablonu Menedżera zasobów.
- Użyj [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-powershell) do wyeksportowania, a następnie zaktualizować szablon usługi Resource Manager.
- Użyj [wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-cli) do wyeksportowania, a następnie zaktualizować szablon usługi Resource Manager.
- Użyj usługi Azure RM PowerShell [AzureRmServiceFabricSetting zestaw](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/Set-AzureRmServiceFabricSetting) i [AzureRmServiceFabricSetting Usuń](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/Remove-AzureRmServiceFabricSetting) polecenia, aby zmodyfikować ustawienia bezpośrednio.
- Użyj wiersza polecenia platformy Azure [az sf klastra ustawienie](https://docs.microsoft.com/cli/azure/sf/cluster/setting) polecenia, aby zmodyfikować ustawienia bezpośrednio.

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [ustawienia klastra usługi Service Fabric](service-fabric-cluster-fabric-settings.md).
* Dowiedz się, jak [skalowania klastra i pomniejszać](service-fabric-cluster-scale-up-down.md).
