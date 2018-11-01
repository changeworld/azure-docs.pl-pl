---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: d44865dc3189a7f9dc05106baf9f4d120e5e8bf6
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2018
ms.locfileid: "50134273"
---
## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az_group_create). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure, takich jak aplikacje funkcji, bazy danych i konta magazynu, oraz zarządzania nimi.

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie `myResourceGroup`.  
Jeśli nie korzystasz z usługi Cloud Shell, najpierw zaloguj się za pomocą polecenia `az login`.

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```
Zasadniczo grupy zasobów i zasoby są tworzone w pobliskim regionie. Aby wyświetlić wszystkie obsługiwane lokalizacje dla planów usługi App Service, uruchom polecenie [az appservice list-locations](/cli/azure/appservice#az-appservice-list-locations).