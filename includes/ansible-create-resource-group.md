---
author: tomarcher
ms.service: ansible
ms.topic: include
ms.date: 08/09/2018
ms.author: tarcher
ms.openlocfilehash: fa1f7fe0b4b70aae4f9165197d5d1463df1f2e3b
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2018
ms.locfileid: "40026442"
---
1. W usłudze Cloud Shell Utwórz plik o nazwie `rg.yml`.

    ```bash
    vi rg.yml
    ```

1. Wprowadź trybu wstawiania, wybierając **I** klucza.

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

1. Wyjdź z trybu wstawiania, wybierając **Esc** klucza.

1. Zapisz plik i zamknij Edytor vi, wprowadzając następujące polecenie:

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