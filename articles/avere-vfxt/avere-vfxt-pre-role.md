---
title: Utwórz rolę Avere z kontroler - vFXT Avere dla platformy Azure
description: Metody, aby utworzyć wymagane rolę RBAC bez kontrolera klastra maszyny Wirtualnej
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 547a0e0ac5254408a4064d627ec4c1e7c354a433
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/31/2018
ms.locfileid: "50634195"
---
# <a name="create-the-avere-vfxt-cluster-runtime-access-role-without-a-controller"></a>Tworzenie roli dostęp do środowiska uruchomieniowego klastra vFXT Avere bez kontrolera

Ten dokument zawiera metodę, aby utworzyć rolę dostępu do węzłów klastra z poziomu wiersza polecenia, przed utworzeniem maszyny Wirtualnej kontrolera klastra. 

Aby utworzyć ją od administratora klastra, przeczytaj [tworzenie roli dostęp do węzła klastra](avere-vfxt-deploy.md#create-the-cluster-node-access-role). Obraz kontrolera obejmuje plik prototypu roli. Możesz zaktualizować plik za pomocą Identyfikatora subskrypcji i użyj go do zdefiniowania roli lokalnie na kontrolerze maszyny Wirtualnej.

## <a name="create-an-azure-rbac-role"></a>Utwórz rolę RBAC platformy Azure

Avere vFXT system używa [kontroli dostępu opartej na rolach](https://docs.microsoft.com/azure/role-based-access-control/) (RBAC), aby autoryzować vFXT węzły klastra do poprawnego wykonywania niezbędnych zadań.  

Jako część normalnego vFXT operacji klastra vFXT poszczególne węzły muszą wykonywać następujące czynności odczytu właściwości zasobów platformy Azure, zarządzania magazynem i kontrolować ustawienia interfejsu sieciowego w innych węzłach. 

Ta rola jest używana tylko w węzłach vFXT dla maszyny Wirtualnej kontrolera klastra.  

Jeśli chcesz utworzyć rolę przed kontrolera, wykonaj następujące kroki: 

1. Otwórz usługę Azure Cloud Shell w witrynie Azure portal lub przejdź do [ https://shell.azure.com ](https://shell.azure.com).

1. Użyj polecenia wiersza polecenia platformy Azure, aby przełączyć się do subskrypcji vFXT:

   ```az account set --subscription YOUR_SUBSCRIPTION_ID```

1. Pobierz definicję roli z obrazu z witryny marketplace i edytować go, należy użyć tych poleceń. 

   ```bash
   wget -O- https://averedistribution.blob.core.windows.net/public/vfxtdistdoc.tgz | tar zxf - avere-cluster.json
   vi avere-cluster.json
   ``` 

4. Edytuj plik Uwzględnij identyfikator swojej subskrypcji i Usuń wiersz powyżej. Zapisz plik jako ``avere-cluster.json``.

   ![Konsoli Edytor tekstu, identyfikator subskrypcji i "Usuń ten wiersz" wybrane do usunięcia](media/avere-vfxt-edit-role.png)

5. Tworzenie roli:  

   ```bash
   az role definition create --role-definition /avere-cluster.json
   ```

Podczas tworzenia klastra, podaj nazwę roli (w tym przypadku `avere-cluster`). Skrypt tworzenia klastra przypisuje rolę w węzłach nowo utworzonego klastra. 

## <a name="sample-cluster-node-runtime-role-definition"></a>Definicja roli środowiska wykonawczego węzła klastra próbki

> [!IMPORTANT] 
> Ta przykładowa definicja została pobrana z wcześniejszej niż GA. wersję produktu. W przypadku innej wersji, który jest pobierany z rozkładem produktu w bieżącej, należy użyć tej wersji.

```json

{
    "AssignableScopes": [
        "/subscriptions/YOUR_SUBSCRIPTION_ID_GOES_HERE"
    ],
    "Name": "avere-cluster",
    "IsCustom": "true",
    "Description": "Avere cluster runtime role",
    "NotActions": [],
    "Actions": [
        "Microsoft.Compute/virtualMachines/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Network/networkInterfaces/write",
        "Microsoft.Network/virtualNetworks/subnets/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/write"
    ],
    "DataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
    ]
}

```