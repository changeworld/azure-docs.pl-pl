---
title: Zarządzanie maszyną wirtualną z systemem Linux na platformie Azure za pomocą rozwiązania Ansible
description: Dowiedz się, jak zarządzać maszyną wirtualną z systemem Linux na platformie Azure za pomocą rozwiązania Ansible
ms.service: virtual-machines-linux
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: quickstart
ms.date: 09/27/2018
ms.openlocfilehash: 8f97cf8a4231e9a2144f27c0540de96574e13795
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60188212"
---
# <a name="use-ansible-to-manage-a-linux-virtual-machine-in-azure"></a>Zarządzanie maszyną wirtualną z systemem Linux na platformie Azure za pomocą rozwiązania Ansible
Rozwiązanie Ansible umożliwia zautomatyzowanie wdrażania i konfigurowania zasobów w Twoim środowisku. Rozwiązanie Ansible umożliwia zarządzanie maszynami wirtualnymi na platformie Azure, tak jak ma to miejsce w przypadku każdego innego zasobu. W tym artykule przedstawiono sposób użycia podręcznika rozwiązania Ansible do uruchomienia i zatrzymania maszyny wirtualnej z systemem Linux. 

## <a name="prerequisites"></a>Wymagania wstępne

- **Subskrypcja Azure** — jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

## <a name="use-ansible-to-deallocate-stop-an-azure-virtual-machine"></a>Używanie rozwiązania Ansible do cofnięcia przydziału (zatrzymania) maszyny wirtualnej na platformie Azure
W tej sekcji pokazano, w jaki sposób użyć rozwiązania Ansible do cofnięcia przydziału (zatrzymania) maszyny wirtualnej na platformie Azure.

1.  Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1.  Otwórz usługę [Cloud Shell](/azure/cloud-shell/overview).

1.  Utwórz plik, który ma zawierać podręcznik, i nazwij go `azure-vm-stop.yml`, a następnie otwórz go w edytorze VI w następujący sposób:

    ```azurecli-interactive
    vi azure-vm-stop.yml
    ```

1.  Przejdź do trybu wstawiania, naciskając klawisz **I**.

1.  Wklej następujący kod przykładowy do edytora:

    ```yaml
    - name: Stop Azure VM
      hosts: localhost
      connection: local
      tasks:
      - name: Deallocate the virtual machine
        azure_rm_virtualmachine:
          resource_group: myResourceGroup
          name: myVM
          allocated: no
    ```

1.  Opuść tryb wstawiania, naciskając klawisz **Esc**.

1.  Zapisz plik i zamknij edytor VI, wprowadzając następujące polecenie:

    ```bash
    :wq
    ```

1.  Uruchom przykładowy podręcznik rozwiązania Ansible.

    ```bash
    ansible-playbook azure-vm-stop.yml
    ```

1.  Dane wyjściowe wyglądają mniej więcej tak, jak w poniższym przykładzie, który pokazuje, że przydział maszyny wirtualnej został pomyślnie cofnięty (maszyna wirtualna została zatrzymana):

    ```bash
    PLAY [Stop Azure VM] ********************************************************

    TASK [Gathering Facts] ******************************************************
    ok: [localhost]

    TASK [Deallocate the Virtual Machine] ***************************************
    changed: [localhost]

    PLAY RECAP ******************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0
    ```

## <a name="use-ansible-to-start-a-deallocated-stopped-azure-virtual-machine"></a>Używanie rozwiązania Ansible do uruchomienia maszyny wirtualnej, której przydział cofnięto (zatrzymanej maszyny wirtualnej)
W tej sekcji pokazano, w jaki sposób użyć rozwiązania Ansible do uruchomienia maszyny wirtualnej, której przydział cofnięto (zatrzymanej maszyny wirtualnej).

1.  Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1.  Otwórz usługę [Cloud Shell](/azure/cloud-shell/overview).

1.  Utwórz plik, który ma zawierać podręcznik, i nazwij go `azure-vm-start.yml`, a następnie otwórz go w edytorze VI w następujący sposób:

    ```azurecli-interactive
    vi azure-vm-start.yml
    ```

1.  Przejdź do trybu wstawiania, naciskając klawisz **I**.

1.  Wklej następujący kod przykładowy do edytora:

    ```yaml
    - name: Start Azure VM
      hosts: localhost
      connection: local
      tasks:
      - name: Start the virtual machine
        azure_rm_virtualmachine:
          resource_group: myResourceGroup
          name: myVM
    ```

1.  Opuść tryb wstawiania, naciskając klawisz **Esc**.

1.  Zapisz plik i zamknij edytor VI, wprowadzając następujące polecenie:

    ```bash
    :wq
    ```

1.  Uruchom przykładowy podręcznik rozwiązania Ansible.

    ```bash
    ansible-playbook azure-vm-start.yml
    ```

1.  Dane wyjściowe wyglądają mniej więcej tak, jak w poniższym przykładzie, który pokazuje, że maszyna wirtualna została pomyślnie uruchomiona:

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
> [Use Ansible to manage your Azure dynamic inventories (Zarządzenie spisami dynamicznymi na platformie Azure przy użyciu rozwiązania Ansible)](~/articles/ansible/ansible-manage-azure-dynamic-inventories.md)