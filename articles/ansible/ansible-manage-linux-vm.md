---
title: Szybki start — zarządzanie maszynami wirtualnymi systemu Linux na platformie Azure przy użyciu aplikacji Ansible
description: W tym przewodniku Szybki start dowiedz się, jak zarządzać maszyną wirtualną systemu Linux na platformie Azure przy użyciu funkcji Ansible
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
ms.topic: quickstart
ms.service: ansible
author: tomarchermsft
manager: gwallace
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: d94858391951aaf9387394afeb5ad2ae373fa7b5
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80239531"
---
# <a name="quickstart-manage-linux-virtual-machines-in-azure-using-ansible"></a>Szybki start: zarządzanie maszynami wirtualnymi systemu Linux na platformie Azure przy użyciu aplikacji Ansible

Rozwiązanie Ansible umożliwia zautomatyzowanie wdrażania i konfigurowania zasobów w Twoim środowisku. W tym artykule użyjesz podręcznika Ansible, aby uruchomić i zatrzymać maszynę wirtualną systemu Linux. 

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [open-source-devops-prereqs-azure-sub.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="stop-a-virtual-machine"></a>Zatrzymywanie maszyny wirtualnej

W tej sekcji można użyć Ansible do deallocate (stop) maszyny wirtualnej platformy Azure.

1. Zaloguj się do [Portalu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Otwórz [powłokę chmury](/azure/cloud-shell/overview).

1. Utwórz plik `azure-vm-stop.yml`o nazwie i otwórz go w edytorze:

    ```bash
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

1. Zastąp `{{ resource_group_name }}` i `{{ vm_name }}` symbole zastępcze wartościami.

1. Zapisz plik i zamknij edytor.

1. Uruchom podręcznik za `ansible-playbook` pomocą polecenia:

    ```bash
    ansible-playbook azure-vm-stop.yml
    ```

1. Po uruchomieniu podręcznika zobaczysz dane wyjściowe podobne do następujących wyników:

    ```bash
    PLAY [Stop Azure VM] ********************************************************

    TASK [Gathering Facts] ******************************************************
    ok: [localhost]

    TASK [Deallocate the Virtual Machine] ***************************************
    changed: [localhost]

    PLAY RECAP ******************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0
    ```

## <a name="start-a-virtual-machine"></a>Uruchamianie maszyny wirtualnej

W tej sekcji można użyć Ansible, aby uruchomić cofniętą (zatrzymaną) maszynę wirtualną platformy Azure.

1. Zaloguj się do [Portalu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Otwórz [powłokę chmury](/azure/cloud-shell/overview).

1. Utwórz plik `azure-vm-start.yml`o nazwie i otwórz go w edytorze:

    ```bash
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

1. Zastąp `{{ resource_group_name }}` i `{{ vm_name }}` symbole zastępcze wartościami.

1. Zapisz plik i zamknij edytor.

1. Uruchom podręcznik za `ansible-playbook` pomocą polecenia:

    ```bash
    ansible-playbook azure-vm-start.yml
    ```

1. Po uruchomieniu podręcznika zobaczysz dane wyjściowe podobne do następujących wyników:

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
> [Samouczek: Zarządzanie dynamicznymi zapasami platformy Azure przy użyciu ansible](./ansible-manage-azure-dynamic-inventories.md)