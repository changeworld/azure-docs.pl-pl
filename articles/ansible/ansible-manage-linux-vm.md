---
title: Szybki Start — zarządzanie maszynami wirtualnymi z systemem Linux na platformie Azure przy użyciu rozwiązania ansible
description: W tym przewodniku szybki start dowiesz się, jak zarządzać maszyną wirtualną z systemem Linux na platformie Azure przy użyciu rozwiązania ansible
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
ms.topic: quickstart
ms.service: ansible
author: tomarchermsft
manager: gwallace
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 90113373535c835e5cddf707bcb520789e596aec
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77202403"
---
# <a name="quickstart-manage-linux-virtual-machines-in-azure-using-ansible"></a>Szybki Start: Zarządzanie maszynami wirtualnymi z systemem Linux na platformie Azure przy użyciu rozwiązania ansible

Rozwiązanie Ansible umożliwia zautomatyzowanie wdrażania i konfigurowania zasobów w Twoim środowisku. W tym artykule opisano uruchamianie i zatrzymywanie maszyny wirtualnej z systemem Linux przy użyciu rozwiązania ansible element PlayBook. 

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [open-source-devops-prereqs-azure-sub.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="stop-a-virtual-machine"></a>Zatrzymaj maszynę wirtualną

W tej sekcji użyto rozwiązania ansible do dealokacji (zatrzymania) maszyny wirtualnej platformy Azure.

1. Zaloguj się do [Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Otwórz usługę [Cloud Shell](/azure/cloud-shell/overview).

1. Utwórz plik o nazwie `azure-vm-stop.yml`i otwórz go w edytorze:

    ```azurecli-interactive
    code azure-vm-stop.yml
    ```

1. Wklej następujący kod przykładowy do edytora:

    ```yaml
    - name: Stop Azure VM
      hosts: localhost
      connection: local
      tasks:
        - name: Stop virtual machine
          azure_rm_virtualmachine:
            resource_group: {{ resource_group_name }}
            name: {{ vm_name }}
            allocated: no
    ```

1. Zastąp symbole zastępcze `{{ resource_group_name }}` i `{{ vm_name }}` wartościami.

1. Zapisz plik i Zamknij Edytor.

1. Uruchom element PlayBook przy użyciu polecenia `ansible-playbook`:

    ```bash
    ansible-playbook azure-vm-stop.yml
    ```

1. Po uruchomieniu element PlayBook są wyświetlane dane wyjściowe podobne do następujących:

    ```bash
    PLAY [Stop Azure VM] ********************************************************

    TASK [Gathering Facts] ******************************************************
    ok: [localhost]

    TASK [Deallocate the Virtual Machine] ***************************************
    changed: [localhost]

    PLAY RECAP ******************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0
    ```

## <a name="start-a-virtual-machine"></a>Uruchom maszynę wirtualną

W tej sekcji użyjesz rozwiązania ansible, aby uruchomić cofniętą alokację (zatrzymaną) maszynę wirtualną platformy Azure.

1. Zaloguj się do [Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Otwórz usługę [Cloud Shell](/azure/cloud-shell/overview).

1. Utwórz plik o nazwie `azure-vm-start.yml`i otwórz go w edytorze:

    ```azurecli-interactive
    code azure-vm-start.yml
    ```

1. Wklej następujący kod przykładowy do edytora:

    ```yaml
    - name: Start Azure VM
      hosts: localhost
      connection: local
      tasks:
        - name: Start virtual machine
          azure_rm_virtualmachine:
            resource_group: {{ resource_group_name }}
            name: {{ vm_name }}
    ```

1. Zastąp symbole zastępcze `{{ resource_group_name }}` i `{{ vm_name }}` wartościami.

1. Zapisz plik i Zamknij Edytor.

1. Uruchom element PlayBook przy użyciu polecenia `ansible-playbook`:

    ```bash
    ansible-playbook azure-vm-start.yml
    ```

1. Po uruchomieniu element PlayBook są wyświetlane dane wyjściowe podobne do następujących:

    ```bash
    PLAY [Start Azure VM] ********************************************************

    TASK [Gathering Facts] ******************************************************
    ok: [localhost]

    TASK [Start the Virtual Machine] ********************************************
    changed: [localhost]

    PLAY RECAP ******************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0
    ```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"] 
> [Samouczek: Zarządzanie spisami dynamicznymi platformy Azure za pomocą rozwiązania ansible](./ansible-manage-azure-dynamic-inventories.md)