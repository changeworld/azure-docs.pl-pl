---
title: Przewodnik Szybki Start — Zarządzanie maszynami wirtualnymi systemu Linux na platformie Azure za pomocą rozwiązania Ansible | Dokumentacja firmy Microsoft
description: W tym przewodniku Szybki Start Dowiedz się, jak zarządzać maszyny wirtualnej z systemem Linux na platformie Azure za pomocą rozwiązania Ansible
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
ms.topic: quickstart
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: cab179980f6093bf259556fd690e55c99a817c79
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2019
ms.locfileid: "63760666"
---
# <a name="quickstart-manage-linux-virtual-machines-in-azure-using-ansible"></a>Szybki start: Zarządzanie maszynami wirtualnymi systemu Linux na platformie Azure za pomocą rozwiązania Ansible

Rozwiązanie Ansible umożliwia zautomatyzowanie wdrażania i konfigurowania zasobów w Twoim środowisku. W tym artykule umożliwia element playbook rozwiązania Ansible uruchamiają i zatrzymują maszyny wirtualnej systemu Linux. 

## <a name="prerequisites"></a>Wymagania wstępne

- [!INCLUDE [open-source-devops-prereqs-azure-sub.md](../../../includes/open-source-devops-prereqs-azure-subscription.md)]
- [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="stop-a-virtual-machine"></a>Zatrzymaj maszynę wirtualną

W tej sekcji za pomocą rozwiązania Ansible na cofnięcie przydziału maszyny wirtualnej platformy Azure (Zatrzymaj).

1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Otwórz usługę [Cloud Shell](/azure/cloud-shell/overview).

1. Utwórz plik o nazwie `azure-vm-stop.yml`, a następnie otwórz go w edytorze:

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

1. Zastąp `{{ resource_group_name }}` i `{{ vm_name }}` symbole zastępcze własnymi wartościami.

1. Zapisz plik i zamknij Edytor.

1. Uruchamianie elementu playbook, przy użyciu `ansible-playbook` polecenia:

    ```bash
    ansible-playbook azure-vm-stop.yml
    ```

1. Po uruchomieniu elementu playbook, zobaczysz dane wyjściowe podobne do następujących wyników:

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

W tej sekcji za pomocą rozwiązania Ansible można uruchomić przydział zostanie cofnięty (zatrzymane) maszyny wirtualnej platformy Azure.

1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Otwórz usługę [Cloud Shell](/azure/cloud-shell/overview).

1. Utwórz plik o nazwie `azure-vm-start.yml`, a następnie otwórz go w edytorze:

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

1. Zastąp `{{ resource_group_name }}` i `{{ vm_name }}` symbole zastępcze własnymi wartościami.

1. Zapisz plik i zamknij Edytor.

1. Uruchamianie elementu playbook, przy użyciu `ansible-playbook` polecenia:

    ```bash
    ansible-playbook azure-vm-start.yml
    ```

1. Po uruchomieniu elementu playbook, zobaczysz dane wyjściowe podobne do następujących wyników:

    ```bash
    PLAY [Start Azure VM] ********************************************************

    TASK [Gathering Facts] ******************************************************
    ok: [localhost]

    TASK [Start the Virtual Machine] ********************************************
    changed: [localhost]

    PLAY RECAP ******************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0
    ```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"] 
> [Samouczek: Zarządzanie Azure spisami dynamicznymi za pomocą rozwiązania Ansible](~/articles/ansible/ansible-manage-azure-dynamic-inventories.md)