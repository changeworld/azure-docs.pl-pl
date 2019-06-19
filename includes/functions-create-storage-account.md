---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 889b9c0cf944085f5f42ece892d5cac747a27240
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183008"
---
## <a name="create-an-azure-storage-account"></a>Tworzenie konta usługi Azure Storage

Usługa Functions przechowuje informacje o stanie i inne informacje dotyczące funkcji za pomocą konta ogólnego przeznaczenia usługi Azure Storage. Utwórz konto magazynu ogólnego przeznaczenia w utworzonej grupie zasobów przy użyciu polecenia [az storage account create](/cli/azure/storage/account).

W poniższym poleceniu w miejsce symbolu zastępczego `<storage_name>` wstaw unikatową w skali globalnej nazwę konta magazynu. Nazwy kont usługi Storage muszą mieć długość od 3 do 24 znaków i mogą zawierać tylko cyfry i małe litery.

```azurecli-interactive
az storage account create --name <storage_name> --location westeurope --resource-group myResourceGroup --sku Standard_LRS
```

Po utworzeniu konta magazynu w interfejsie wiersza polecenia platformy Azure zostanie wyświetlona informacja podobna do następującej:

```json
{
  "creationTime": "2017-04-15T17:14:39.320307+00:00",
  "id": "/subscriptions/bbbef702-e769-477b-9f16-bc4d3aa97387/resourceGroups/myresourcegroup/...",
  "kind": "Storage",
  "location": "westeurope",
  "name": "myfunctionappstorage",
  "primaryEndpoints": {
    "blob": "https://myfunctionappstorage.blob.core.windows.net/",
    "file": "https://myfunctionappstorage.file.core.windows.net/",
    "queue": "https://myfunctionappstorage.queue.core.windows.net/",
    "table": "https://myfunctionappstorage.table.core.windows.net/"
  },
     ....
    // Remaining output has been truncated for readability.
}
```
