---
title: Avere vFXT niebędącego właścicielem — Azure
description: Obejście, aby umożliwić użytkownikom bez uprawnień właściciela subskrypcji wdrażanie avere vFXT dla platformy Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: rohogue
ms.openlocfilehash: 77fc5a53c8bdc389c24cd1e6406415eefc3f167b
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256184"
---
# <a name="authorize-non-owners-to-deploy-avere-vfxt"></a>Autoryzowanie osób niebędących właścicielami do wdrożenia systemu Avere vFXT

Te instrukcje to obejście, które umożliwia użytkownikowi bez uprawnień właściciela subskrypcji tworzenie avere vFXT dla systemu Azure.

(Zalecanym sposobem wdrożenia systemu avere vFXT jest posiadanie użytkownikowi z uprawnieniami właściciela wykonania kroków tworzenia, zgodnie z opisem w sekcji [przygotowanie do utworzenia avere vFXT](avere-vfxt-prereqs.md).)  

Obejście obejmuje utworzenie dodatkowej roli dostępu zapewniającej użytkownikom wystarczające uprawnienia do instalacji klastra. Rola musi być utworzona przez właściciela subskrypcji, a właściciel musi przypisać go do odpowiednich użytkowników. 

Właściciel subskrypcji musi również [zaakceptować warunki użytkowania](avere-vfxt-prereqs.md) obrazu witryny Marketplace avere vFXT. 

> [!IMPORTANT] 
> Wszystkie te kroki muszą zostać wykonane przez użytkownika z uprawnieniami właściciela do subskrypcji, która będzie używana w klastrze.

1. Skopiuj te wiersze i Zapisz je w pliku (na przykład `averecreatecluster.json`). Użyj identyfikatora subskrypcji w instrukcji `AssignableScopes`.

   ```json
   {
       "AssignableScopes": ["/subscriptions/<SUBSCRIPTION_ID>"],
       "Name": "avere-create-cluster",
       "IsCustom": "true"
       "Description": "Can create Avere vFXT clusters",
       "NotActions": [],
       "Actions": [
           "Microsoft.Authorization/*/read",
           "Microsoft.Authorization/roleAssignments/*",
           "Microsoft.Authorization/roleDefinitions/*",
           "Microsoft.Compute/*/read",
           "Microsoft.Compute/availabilitySets/*",
           "Microsoft.Compute/virtualMachines/*",
           "Microsoft.Network/*/read",
           "Microsoft.Network/networkInterfaces/*",
           "Microsoft.Network/routeTables/write",
           "Microsoft.Network/routeTables/delete",
           "Microsoft.Network/routeTables/routes/delete",
           "Microsoft.Network/virtualNetworks/subnets/join/action",
           "Microsoft.Network/virtualNetworks/subnets/read",
   
           "Microsoft.Resources/subscriptions/resourceGroups/read",
           "Microsoft.Resources/subscriptions/resourceGroups/resources/read",
           "Microsoft.Storage/*/read",
           "Microsoft.Storage/storageAccounts/listKeys/action"
       ],
   }
   ```

1. Uruchom to polecenie, aby utworzyć rolę:

   `az role definition create --role-definition <PATH_TO_FILE>`

    Przykład:
    ```azurecli
    az role definition create --role-definition ./averecreatecluster.json
    ```

1. Przypisz tę rolę użytkownikowi, który utworzy klaster:

   `az role assignment create --assignee <USERNAME> --scope /subscriptions/<SUBSCRIPTION_ID> --role 'avere-create-cluster'`

Po wykonaniu tej procedury wszyscy użytkownicy przypisani do tej roli mają następujące uprawnienia do subskrypcji: 

* Tworzenie i Konfigurowanie infrastruktury sieciowej
* Tworzenie kontrolera klastra
* Uruchamianie skryptów tworzenia klastra z kontrolera klastra w celu utworzenia klastra
