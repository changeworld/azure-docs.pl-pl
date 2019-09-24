---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 4dd43c5bcc5a0e9a734db4ca9a4b3d7137f85250
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/23/2019
ms.locfileid: "71203170"
---
## <a name="create-an-azure-storage-account"></a>Tworzenie konta usługi Azure Storage

Usługa Functions przechowuje informacje o stanie i inne informacje dotyczące funkcji za pomocą konta ogólnego przeznaczenia usługi Azure Storage. Utwórz konto magazynu ogólnego przeznaczenia w utworzonej grupie zasobów przy użyciu polecenia [az storage account create](/cli/azure/storage/account#az-storage-account-create).

W poniższym poleceniu w miejsce symbolu zastępczego `<storage_name>` wstaw unikatową w skali globalnej nazwę konta magazynu. Nazwy kont usługi Storage muszą mieć długość od 3 do 24 znaków i mogą zawierać tylko cyfry i małe litery.

```azurecli-interactive
az storage account create --name <storage_name> --location westeurope --resource-group myResourceGroup --sku Standard_LRS
```
