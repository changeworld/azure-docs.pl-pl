---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
origin.date: 11/09/2018
ms.date: 12/25/2018
ms.author: v-junlch
ms.openlocfilehash: e5166934c4f7e1ecc917d2a5afcbb26ea9a637a2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60607745"
---
## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Po wykonaniu przykładowego skryptu polecenia wykonaj może służyć do usunięcia grupy zasobów, usługi Azure Cache dla wystąpienia usługi Redis i wszystkie pokrewne zasoby w grupie zasobów.

```azurecli
az group delete --name contosoGroup
```

<!-- ms.date: 12/25/2018 -->