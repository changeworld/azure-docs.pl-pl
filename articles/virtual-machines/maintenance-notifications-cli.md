---
title: Otrzymuj powiadomienia o konserwacji przy użyciu interfejsu wiersza polecenia
description: Wyświetl powiadomienia o konserwacji dla maszyn wirtualnych uruchomionych na platformie Azure i rozpocznij samoobsługową konserwację przy użyciu interfejsu wiersza polecenia platformy Azure.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: 4ad57c1c71a51f948bd405a5487a1e27e36bfff7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920896"
---
# <a name="handling-planned-maintenance-notifications-using-the-azure-cli"></a>Obsługa powiadomień o planowanej konserwacji przy użyciu interfejsu wiersza polecenia platformy Azure

**Ten artykuł dotyczy maszyn wirtualnych z systemem Linux i Windows.**

Można użyć interfejsu wiersza polecenia, aby zobaczyć, kiedy maszyny wirtualne są zaplanowane do [konserwacji](maintenance-notifications.md). Informacje o planowanej konserwacji są dostępne w [widoku get-instance az vm](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-get-instance-view).
 
Informacje o konserwacji są zwracane tylko wtedy, gdy planowana jest konserwacja. 

```azurecli-interactive
az vm get-instance-view -n myVM -g myResourceGroup --query instanceView.maintenanceRedeployStatus
```

## <a name="start-maintenance"></a>Rozpocznij konserwację

Następujące wywołanie rozpocznie konserwację na `IsCustomerInitiatedMaintenanceAllowed` maszynie Wirtualnej, jeśli jest ustawiona na true.

```azurecli-interactive
az vm perform-maintenance -g myResourceGroup -n myVM 
```

## <a name="classic-deployments"></a>Klasyczne wdrożenia

[!INCLUDE [classic-vm-deprecation](../../includes/classic-vm-deprecation.md)]

Jeśli nadal masz starsze maszyny wirtualne, które zostały wdrożone przy użyciu klasycznego modelu wdrażania, można użyć klasycznego interfejsu wiersza polecenia platformy Azure do wykonywania zapytań dotyczących maszyn wirtualnych i inicjowania konserwacji.

Upewnij się, że jesteś w odpowiednim trybie do pracy z klasyczną maszyną wirtualną, wpisując:

```
azure config mode asm
```

Aby uzyskać stan konserwacji maszyny wirtualnej o nazwie *myVM,* wpisz:

```
azure vm show myVM 
``` 

Aby rozpocząć konserwację klasycznej maszyny Wirtualnej o nazwie *myVM* w usłudze *myService* i wedrożeniu *myDeployment,* należy wpisać:

```
azure compute virtual-machine initiate-maintenance --service-name myService --name myDeployment --virtual-machine-name myVM
```

## <a name="next-steps"></a>Następne kroki

Można również obsługiwać planowaną konserwację za pomocą [programu Azure PowerShell](maintenance-notifications-powershell.md) lub [portalu.](maintenance-notifications-portal.md)
