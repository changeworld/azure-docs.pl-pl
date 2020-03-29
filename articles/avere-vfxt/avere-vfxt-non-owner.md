---
title: Avere vFXT — obejście problemu niebędącego właścicielem — Azure
description: Obejście umożliwiające użytkownikom bez uprawnień właściciela subskrypcji wdrożenie usługi Avere vFXT for Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: 1b411fe465a67f8ea5421ac0dc93348b4e92e8ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76153279"
---
# <a name="authorize-non-owners-to-deploy-avere-vfxt"></a>Autoryzowanie osób niebędących właścicielami do wdrożenia systemu Avere vFXT

Te instrukcje są obejściem, które umożliwia użytkownikowi bez uprawnień właściciela subskrypcji utworzenie systemu Avere vFXT for Azure.

(Zalecanym sposobem wdrożenia systemu VFXT Avere jest wykonanie kroków tworzenia przez użytkownika z uprawnieniami właściciela, jak wyjaśniono w [instrukcji Przygotuj do utworzenia avere vFXT.)](avere-vfxt-prereqs.md)  

Obejście polega na utworzeniu dodatkowej roli dostępu, która daje użytkownikom wystarczające uprawnienia do zainstalowania klastra. Rola musi zostać utworzona przez właściciela subskrypcji, a właściciel musi przypisać ją do odpowiednich użytkowników.

Właściciel subskrypcji musi również [zaakceptować warunki użytkowania](avere-vfxt-prereqs.md) obrazu rynku Avere vFXT.

> [!IMPORTANT]
> Wszystkie te kroki muszą być podjęte przez użytkownika z uprawnieniami właściciela w subskrypcji, która będzie używana dla klastra.

1. Skopiuj te wiersze i `averecreatecluster.json`zapisz je w pliku (na przykład ). Użyj identyfikatora subskrypcji `AssignableScopes` w instrukcji.

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

1. Przypisz tę rolę do użytkownika, który utworzy klaster:

   `az role assignment create --assignee <USERNAME> --scope /subscriptions/<SUBSCRIPTION_ID> --role 'avere-create-cluster'`

Po zakończeniu tego procesu rola daje każdemu użytkownikowi przypisane go następujące uprawnienia do subskrypcji:

* Tworzenie i konfigurowanie infrastruktury sieciowej
* Tworzenie kontrolera klastra
* Uruchamianie skryptów tworzenia klastra z kontrolera klastra w celu utworzenia klastra
