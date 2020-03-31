---
title: Konfiguracja po wdrożeniu przy użyciu rozszerzeń
description: Dowiedz się, jak używać rozszerzeń szablonów usługi Azure Resource Manager w celu zapewnienia konfiguracji po wdrożeniu.
author: mumian
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: jgao
ms.openlocfilehash: b3c4110c8761b3e8daf324d65ac7fa1dcbcdf61f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023501"
---
# <a name="provide-post-deployment-configurations-by-using-extensions"></a>Zapewnianie konfiguracji po wdrożeniu przy użyciu rozszerzeń

Rozszerzenia szablonów to małe aplikacje, które zapewniają konfigurację po wdrożeniu i zadania automatyzacji w zasobach platformy Azure. Najbardziej popularne są rozszerzenia maszyn wirtualnych. Zobacz [Rozszerzenia i funkcje maszyn wirtualnych dla systemu Windows](../../virtual-machines/extensions/features-windows.md)oraz rozszerzenia i [funkcje maszyn wirtualnych dla systemu Linux](../../virtual-machines/extensions/features-linux.md).

## <a name="extensions"></a>Rozszerzenia

Istniejące rozszerzenia to:

- [Microsoft.Compute/virtualMachines/rozszerzenia](https://docs.microsoft.com/azure/templates/microsoft.compute/2018-10-01/virtualmachines/extensions)
- [Microsoft.Compute virtualMachineScaleSets/extensions](https://docs.microsoft.com/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions)
- [Klastry/rozszerzenia usługi Microsoft.HDInsight](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/2018-06-01-preview/clusters)
- [Microsoft.Sql serwery/bazy danych/rozszerzenia](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions) 
- [Microsoft.Web/sites/siteextensions Microsoft.Web/sites/siteextensions Microsoft.Web/sites/siteextensions Microsoft.](https://docs.microsoft.com/azure/templates/microsoft.web/2016-08-01/sites/siteextensions)

Aby dowiedzieć się, dostępne rozszerzenia, przejdź do [odwołania do szablonu](https://docs.microsoft.com/azure/templates/). W **polu Filtr według tytułu**wprowadź **rozszerzenie**.

Aby dowiedzieć się, jak korzystać z tych rozszerzeń, zobacz:

- [Samouczek: Wdrażanie rozszerzeń maszyn wirtualnych za pomocą szablonów usługi Azure Resource Manager](template-tutorial-deploy-vm-extensions.md).
- [Samouczek: importowanie plików BACPAC bazy danych SQL za pomocą szablonów usługi Azure Resource Manager](template-tutorial-deploy-sql-extensions-bacpac.md)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: wdrażanie rozszerzeń maszyny wirtualnej przy użyciu szablonów usługi Azure Resource Manager](template-tutorial-deploy-vm-extensions.md)
