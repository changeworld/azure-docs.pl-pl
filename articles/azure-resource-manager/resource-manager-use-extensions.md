---
title: Podaj konfiguracji po wdrożeniu przy użyciu rozszerzeń — Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać rozszerzenia szablonów usługi Azure Resource Manager do dostarczania konfiguracji po wdrożeniu.
services: azure-resource-manager
documentationcenter: na
author: mumian
editor: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/14/2018
ms.author: jgao
ms.openlocfilehash: eb46966c3a28b3fa4c2b23668109b7c5d23a609b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60390872"
---
# <a name="provide-post-deployment-configurations-by-using-extensions"></a>Podaj konfiguracji po wdrożeniu przy użyciu rozszerzeń

Rozszerzenia szablonów są małych aplikacji, które zapewniają konfiguracji po wdrożeniu i zadania automatyzacji dla zasobów platformy Azure. Najbardziej popularne jest rozszerzenia maszyny wirtualnej. Zobacz [rozszerzeń maszyn wirtualnych i funkcji dla Windows](../virtual-machines/extensions/features-windows.md), i [rozszerzenia maszyn wirtualnych i funkcji dla systemu Linux](../virtual-machines/extensions/features-linux.md).

## <a name="extensions"></a>Rozszerzenia

Istniejące rozszerzenia są:

- [Microsoft.Compute/virtualMachines/extensions](https://docs.microsoft.com/azure/templates/microsoft.compute/2018-10-01/virtualmachines/extensions)
- [Microsoft.Compute virtualMachineScaleSets/rozszerzenia](https://docs.microsoft.com/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions)
- [Microsoft.HDInsight klastrów/rozszerzenia](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/2018-06-01-preview/clusters/extensions)
- [Microsoft.Sql serwerów/baz danych/rozszerzenia](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions) 
- [Microsoft.Web/sites/siteextensions](https://docs.microsoft.com/azure/templates/microsoft.web/2016-08-01/sites/siteextensions)

Aby dowiedzieć się, dostępne rozszerzenia, przejdź do [odwołanie do szablonu](https://docs.microsoft.com/azure/templates/). W **Filtruj według tytułu**, wprowadź **rozszerzenia**.

Aby dowiedzieć się, jak korzystać z tych rozszerzeń, zobacz:

- [Samouczek: Wdrażanie rozszerzeń maszyny wirtualnej przy użyciu szablonów usługi Azure Resource Manager](./resource-manager-tutorial-deploy-vm-extensions.md).
- [Samouczek: Zaimportuj pliki SQL BACPAC przy użyciu szablonów usługi Azure Resource Manager](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Samouczek: Wdrażanie rozszerzenia maszyny wirtualnej przy użyciu szablonów usługi Azure Resource Manager](./resource-manager-tutorial-deploy-vm-extensions.md)
