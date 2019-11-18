---
title: Konfiguracja po wdrożeniu przy użyciu rozszerzeń
description: Dowiedz się, jak za pomocą rozszerzeń szablonów Azure Resource Manager zapewnić konfiguracje po wdrożeniu.
author: mumian
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: jgao
ms.openlocfilehash: c7896efb453cac478202efedb268c4bc838aef3a
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150375"
---
# <a name="provide-post-deployment-configurations-by-using-extensions"></a>Zapewnianie konfiguracji po wdrożeniu przy użyciu rozszerzeń

Rozszerzenia szablonów to małe aplikacje, które zapewniają konfigurację po wdrożeniu i zadania automatyzacji w zasobach platformy Azure. Najbardziej popularnym z nich są rozszerzenia maszyny wirtualnej. Zobacz [rozszerzenia i funkcje maszyny wirtualnej dla systemu Windows](../virtual-machines/extensions/features-windows.md)oraz [rozszerzenia i funkcje maszyny wirtualnej w systemie Linux](../virtual-machines/extensions/features-linux.md).

## <a name="extensions"></a>Rozszerzenia

Istniejące rozszerzenia to:

- [Microsoft.Compute/virtualMachines/extensions](https://docs.microsoft.com/azure/templates/microsoft.compute/2018-10-01/virtualmachines/extensions)
- [Microsoft. COMPUTE virtualMachineScaleSets/rozszerzenia](https://docs.microsoft.com/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions)
- [Klastry/rozszerzenia usługi HDInsight firmy Microsoft](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/2018-06-01-preview/clusters/extensions)
- [Microsoft. SQL Servers/Databases/Extensions](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions) 
- [Microsoft. Web/Sites/siteextensions](https://docs.microsoft.com/azure/templates/microsoft.web/2016-08-01/sites/siteextensions)

Aby dowiedzieć się, jakie rozszerzenia są dostępne, przejdź do [odwołania do szablonu](https://docs.microsoft.com/azure/templates/). W polu **Filtruj według tytułu**wprowadź **rozszerzenie**.

Aby dowiedzieć się, jak korzystać z tych rozszerzeń, zobacz:

- [Samouczek: Wdrażanie rozszerzeń maszyn wirtualnych za pomocą szablonów Azure Resource Manager](./resource-manager-tutorial-deploy-vm-extensions.md).
- [Samouczek: Importowanie plików BACPAC SQL za pomocą szablonów Azure Resource Manager](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: Wdrażanie rozszerzeń maszyny wirtualnej za pomocą szablonów Azure Resource Manager](./resource-manager-tutorial-deploy-vm-extensions.md)
