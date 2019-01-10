---
author: tomarchermsft
ms.service: ansible
ms.topic: include
ms.date: 08/09/2018
ms.author: tarcher
ms.openlocfilehash: fe995535cd42571ad96f192883e48f4b6ea1eb0e
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2019
ms.locfileid: "54160407"
---
1. W usłudze Cloud Shell utwórz plik o nazwie `rg.yml`.

    ```bash
    vi rg.yml
    ```

1. Przejdź do trybu wstawiania, naciskając klawisz **I**.

1. Wklej następujący kod do edytora:

   ```yaml
   ---
   - hosts: localhost
     connection: local
     tasks:
       - name: Create resource group
         azure_rm_resourcegroup:
           name: ansible-rg
           location: eastus
         register: rg
       - debug:
           var: rg
   ```

1. Opuść tryb wstawiania, naciskając klawisz **Esc**.

1. Zapisz plik i zamknij edytor VI, wprowadzając następujące polecenie:

    ```bash
    :wq
    ```

1. Uruchom element playbook `rg.yml`:

   ```bash
   ansible-playbook rg.yml
   ```

Wyniki działania polecenia ansible powinien wyglądać podobnie do następujących danych wyjściowych:

```output
PLAY [localhost] *********************************************************************************

TASK [Gathering Facts] ***************************************************************************
ok: [localhost]

TASK [Create resource group] *********************************************************************
changed: [localhost]

TASK [debug] *************************************************************************************
ok: [localhost] => {
    "rg": {
        "changed": true,
        "contains_resources": false,
        "failed": false,
        "state": {
            "id": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/ansible-rg",
            "location": "eastus",
            "name": "ansible-rg",
            "provisioning_state": "Succeeded",
            "tags": null
        }
    }
}

PLAY RECAP ***************************************************************************************
localhost                  : ok=3    changed=1    unreachable=0    failed=0
```