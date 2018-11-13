---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: 23b92f7d1b1c3ef488e182a6443f275365a03d9a
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2018
ms.locfileid: "51572471"
---
## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Po wykonaniu przykładowego skryptu polecenia wykonaj może służyć do usunięcia grupy zasobów, wystąpienie usługi Azure Redis Cache i wszystkie pokrewne zasoby w grupie zasobów.

```azurecli
az group delete --name contosoGroup
```