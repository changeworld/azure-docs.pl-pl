---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: b0b188e10fe0893734185ced186e3ce24dfd0d21
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/07/2018
ms.locfileid: "53019716"
---
## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Po wykonaniu przykładowego skryptu polecenia wykonaj może służyć do usunięcia grupy zasobów, usługi Azure Cache platformy Azure dla wystąpienia usługi Redis i wszystkie powiązane zasoby w grupie zasobów.

```azurecli
az group delete --name contosoGroup
```