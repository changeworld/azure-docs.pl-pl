---
title: Włączanie i wyłączanie konsoli szeregowej platformy Azure | Dokumenty firmy Microsoft
description: Jak włączyć i wyłączyć usługę Azure Serial Console
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 8/20/2019
ms.author: alsin
ms.openlocfilehash: e09e08f8ba36cf576bc27551254225adee3bb0fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451303"
---
# <a name="enable-and-disable-the-azure-serial-console"></a>Włączanie i wyłączanie konsoli szeregowej platformy Azure

Podobnie jak w przypadku innych zasobów, konsola szeregowa platformy Azure może być włączona i wyłączona. Konsola szeregowa jest domyślnie włączona dla wszystkich subskrypcji na globalnej platformie Azure. Obecnie wyłączenie konsoli szeregowej spowoduje wyłączenie usługi dla całej subskrypcji. Wyłączenie lub ponowne włączenie konsoli szeregowej dla subskrypcji wymaga dostępu na poziomie współautora lub powyżej w ramach subskrypcji.

Można również wyłączyć konsolę szeregową dla pojedynczego wystąpienia zestawu skalowania maszyny wirtualnej lub maszyny wirtualnej, wyłączając diagnostykę rozruchu. Będzie wymagać dostępu na poziomie współautora lub powyżej zarówno na zestaw skalowania maszyny wirtualnej/maszyny wirtualnej i konta magazynu diagnostyki rozruchu.

## <a name="vm-level-disable"></a>Wyłączanie na poziomie maszyny Wirtualnej
Konsolę szeregową można wyłączyć dla określonej maszyny wirtualnej lub skalowania maszyny wirtualnej ustawionej przez wyłączenie ustawienia diagnostyki rozruchu. Wyłącz diagnostykę rozruchu z witryny Azure Portal, aby wyłączyć konsolę szeregową dla maszyny wirtualnej lub zestawu skalowania maszyny wirtualnej. Jeśli używasz konsoli szeregowej na zestawie skalowania maszyny wirtualnej, upewnij się, że uaktualnisz wystąpienia zestawu skalowania maszyny wirtualnej do najnowszego modelu.


## <a name="subscription-level-enabledisable"></a>Włączanie/wyłączanie na poziomie subskrypcji

> [!NOTE]
> Przed uruchomieniem tego polecenia upewnij się, że znajdujesz się w odpowiedniej chmurze (Azure Public Cloud, Azure US Government Cloud). Możesz sprawdzić `az cloud list` i ustawić chmurę za pomocą `az cloud set -n <Name of cloud>`.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Konsola szeregowa może być wyłączona i ponownie włączona dla całej subskrypcji przy użyciu następujących poleceń w interfejsie wiersza polecenia platformy Azure (można użyć przycisku "Wypróbuj", aby uruchomić wystąpienie usługi Azure Cloud Shell, w którym można uruchomić polecenia):

Aby wyłączyć konsolę szeregową dla subskrypcji, użyj następujących poleceń:
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource invoke-action --action disableConsole --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --api-version="2018-05-01"
```

Aby włączyć konsolę szeregową dla subskrypcji, użyj następujących poleceń:
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource invoke-action --action enableConsole --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --api-version="2018-05-01"
```

Aby uzyskać bieżący stan włączone/wyłączone konsoli szeregowej dla subskrypcji, należy użyć następujących poleceń:
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource show --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --output=json --api-version="2018-05-01" | jq .properties
```

### <a name="powershell"></a>PowerShell

Konsola szeregowa można również włączyć i wyłączyć za pomocą programu PowerShell.

Aby wyłączyć konsolę szeregową dla subskrypcji, użyj następujących poleceń:
```azurepowershell-interactive
$subscription=(Get-AzContext).Subscription.Id

Invoke-AzResourceAction -Action disableConsole -ResourceId /subscriptions/$subscription/providers/Microsoft.SerialConsole/consoleServices/default -ApiVersion 2018-05-01
```

Aby włączyć konsolę szeregową dla subskrypcji, użyj następujących poleceń:
```azurepowershell-interactive
$subscription=(Get-AzContext).Subscription.Id

Invoke-AzResourceAction -Action enableConsole -ResourceId /subscriptions/$subscription/providers/Microsoft.SerialConsole/consoleServices/default -ApiVersion 2018-05-01
```

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [maszynach wirtualnych azure serial console dla systemów linux](./serial-console-linux.md)
* Dowiedz się więcej o [platformie Azure Serial Console dla maszyn wirtualnych z systemem Windows](./serial-console-windows.md)
* Dowiedz się więcej o [opcjach zarządzania energią w konsoli szeregowej platformy Azure](./serial-console-power-options.md)