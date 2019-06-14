---
title: Obejście niebędących właścicielami vFXT Avere - Azure
description: Obejście, aby umożliwić użytkownikom bez uprawnień właściciela subskrypcji, aby wdrożyć Avere vFXT dla platformy Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: e72e6d969649de09389ee38b94e874fad98ee08f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60409213"
---
# <a name="authorize-non-owners-to-deploy-avere-vfxt"></a>Autoryzowanie osób niebędących właścicielami do wdrożenia systemu Avere vFXT

Te instrukcje są obejście tego problemu, umożliwiająca użytkownikowi bez subskrypcji uprawnienia właściciela do tworzenia vFXT Avere dla systemu Azure.

(Zalecany sposób wdrażania Avere vFXT systemu jest użytkownik z uprawnieniami właściciela czy tworzenie kroki, jak wyjaśniono w [przed rozpoczęciem tworzenia Avere vFXT](avere-vfxt-prereqs.md).)  

Obejście polega na utworzeniu roli dodatkowe prawa dostępu, który zapewnia swoim użytkownikom wystarczające uprawnienia do instalacji klastra. Rola musi zostać utworzona przez właściciela subskrypcji i właściciel musi przypisać ją do odpowiednich użytkowników. 

Właściciel subskrypcji również musi [akceptowanie warunków użytkowania](avere-vfxt-prereqs.md) dla obrazu z witryny marketplace vFXT Avere. 

> [!IMPORTANT] 
> Wszystkie te kroki należy podjąć przez użytkownika z uprawnieniami właściciela na subskrypcji, która będzie używana dla klastra.

1. Skopiuj następujące wiersze i zapisywać je w pliku (na przykład `averecreatecluster.json`). Użyj Identyfikatora subskrypcji w `AssignableScopes` instrukcji.

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

1. Uruchom następujące polecenie, aby utworzyć rolę:

   `az role definition create --role-definition <PATH_TO_FILE>`

    Przykład:
    ```azurecli
    az role definition create --role-definition ./averecreatecluster.json
    ```

1. Tę rolę można przypisać do użytkownika, który spowoduje utworzenie klastra:

   `az role assignment create --assignee <USERNAME> --scope /subscriptions/<SUBSCRIPTION_ID> --role 'avere-create-cluster'`

Po tej procedurze każdy użytkownik przypisany do tej roli ma następujące uprawnienia do subskrypcji: 

* Tworzenie i konfigurowanie infrastruktury sieciowej
* Tworzenie kontrolera klastra
* Uruchamianie skryptów tworzenia klastra z kontrolera klastra, aby utworzyć klaster
