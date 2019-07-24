---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: a2994602f857c2c8ff9f935b649a8d3e94c10dca
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444114"
---
## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure, takich jak aplikacje funkcji, bazy danych i konta magazynu, oraz zarządzania nimi.

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie `myResourceGroup`.  
Jeśli nie korzystasz z usługi Cloud Shell, najpierw zaloguj się za pomocą polecenia `az login`.

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

Zwykle utworzysz grupę zasobów i zasoby w [regionie](https://azure.microsoft.com/global-infrastructure/regions/) . 
