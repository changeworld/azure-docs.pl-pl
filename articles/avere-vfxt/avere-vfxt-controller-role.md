---
title: Rola dostępu dostosowane kontroler - vFXT Avere dla platformy Azure
description: Jak utworzyć rolę niestandardowego dostępu do kontrolera klastra vFXT Avere
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: v-erkell
ms.openlocfilehash: 2a0f4a628764aaa561a5567d3435a42da804a994
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2019
ms.locfileid: "58417843"
---
# <a name="customized-controller-access-role"></a>Dostosowana rola dostępu do kontrolera

VFXT Avere kontrolera klaster usługi Azure korzysta z tożsamość zarządzaną i kontroli dostępu opartej na rolach (RBAC), aby zezwalała na tworzenie i zarządzanie nimi w klastrze. 

Domyślnie jest przypisany kontrolera klastra [wbudowana Rola właściciela](../role-based-access-control/built-in-roles.md#owner). Ponadto dostęp do kontrolera jest ograniczone do swojej grupy zasobów — nie można zmodyfikować, elementy poza grupę zasobów klastra.

W tym artykule wyjaśniono, jak utworzyć własną rolę dostępu dla kontrolera klastra, zamiast korzystać z ustawieniem domyślnym. 

## <a name="edit-the-role-prototype"></a>Edytuj prototypu roli

Zacznij od roli prototypu dostępne pod adresem <https://github.com/Azure/Avere/blob/master/src/vfxt/src/roles/AvereContributor.txt>.

```json
{
  "AssignableScopes": [
    "/subscriptions/YOUR SUBSCRIPTION ID HERE"
  ],
  "Name": "Avere custom contributor",
  "IsCustom": true,
  "Description": "Can create and manage an Avere vFXT cluster.",
  "NotActions": [],
  "Actions": [
    "Microsoft.Authorization/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/availabilitySets/*",
    "Microsoft.Compute/virtualMachines/*",
    "Microsoft.Compute/disks/*",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Network/*/read",
    "Microsoft.Network/networkInterfaces/*",
    "Microsoft.Network/virtualNetworks/read",
    "Microsoft.Network/virtualNetworks/subnets/join/action",
    "Microsoft.Network/virtualNetworks/subnets/read",
    "Microsoft.Resources/deployments/*",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Resources/subscriptions/resourceGroups/resources/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Storage/storageAccounts/listKeys/action",
    "Microsoft.Support/*"
  ],
  "DataActions": []
}
```

Dodaj identyfikator subskrypcji dla vFXT Avere wdrożenia platformy Azure w instrukcji AssignableScopes. Dostosować nazwę i dodać lub zmienić definicje, zgodnie z potrzebami. 

Należy zachować ostrożność, Jeśli ograniczysz uprawnienia. Tworzenie klastra może zakończyć się niepowodzeniem, jeśli kontrolera nie ma wystarczające uprawnienia dostępu. 

Aby uzyskać pomoc dotyczącą, jakie uprawnienia kontrolera klastra musi utworzyć klaster, [Otwórz bilet pomocy technicznej](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt). 

Zapisz swoją definicję roli niestandardowej w formacie JSON. 

## <a name="define-the-role"></a>Zdefiniuj rolę 

Wykonaj następujące kroki, aby dodać niestandardową definicję roli do Twojej subskrypcji. 

1. Otwórz usługę Azure Cloud Shell w witrynie Azure portal lub przejdź do [ https://shell.azure.com ](https://shell.azure.com).

1. Użyj polecenia wiersza polecenia platformy Azure, aby przełączyć się do subskrypcji vFXT:

   ```azurecli
   az account set --subscription YOUR_SUBSCRIPTION_ID
   ```

1. Tworzenie roli:

   ```azurecli
   az role definition create --role-definition /avere-contributor-custom.json
   ```

   Przy użyciu nazwy pliku i ścieżkę zamiast ```/avere-contributor-custom.json``` w tym przykładzie. 

Zapisz dane wyjściowe polecenia definicji roli — zawiera identyfikator roli, który należy podać do szablonu tworzenia klastra. 

## <a name="find-the-role-id"></a>Znajdź identyfikator roli

Szablon wdrożenia vFXT Avere wymaga roli Unikatowy identyfikator globalny (GUID) można przypisać niestandardową rolę kontrolera. 

Rola identyfikator GUID jest 32-znakowy ciągu w tym formularzu: 8e3af657-a8ff-443c-a75c-2fe8c4bcb635

Aby wyszukać identyfikator GUID Twojej roli, należy użyć tego polecenia z Twoją nazwą roli w ```--name``` parametru.

```azurecli
az role definition list --query '[*].{roleName:roleName, name:name}' -o table --name 'YOUR ROLE NAME'
```
Wprowadź tego ciągu w **identyfikator roli tworzenia klastra Avere** pola podczas wdrażania Avere vFXT dla platformy Azure.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak wdrożyć Avere vFXT platformy Azure w [wdrożenie klastra vFXT](avere-vfxt-deploy.md)
