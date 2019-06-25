---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: c20f86fe7fdcfc7ecc940923a8c98fa1fbf4cf65
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183007"
---
## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure, takich jak aplikacje funkcji, bazy danych i konta magazynu, oraz zarządzania nimi.

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie `myResourceGroup`.  
Jeśli nie korzystasz z usługi Cloud Shell, najpierw zaloguj się za pomocą polecenia `az login`.

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```
Zasadniczo grupy zasobów i zasoby są tworzone w pobliskim regionie. Aby wyświetlić wszystkie obsługiwane lokalizacje dla planów usługi App Service, uruchom polecenie [az appservice list-locations](/cli/azure/appservice#az-appservice-list-locations).
