---
title: Zarządzanie maszyną wirtualną z systemem Linux na platformie Azure za pomocą rozwiązania Ansible
description: Dowiedz się, jak zarządzać maszyną wirtualną z systemem Linux na platformie Azure za pomocą rozwiązania Ansible
ms.service: ansible
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: quickstart
ms.date: 08/21/2018
ms.openlocfilehash: 66106346b298fae22cce47081916a6c8eec8fd40
ms.sourcegitcommit: 76797c962fa04d8af9a7b9153eaa042cf74b2699
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2018
ms.locfileid: "40250673"
---
# <a name="use-ansible-to-manage-a-linux-virtual-machine-in-azure"></a>Zarządzanie maszyną wirtualną z systemem Linux na platformie Azure za pomocą rozwiązania Ansible
Rozwiązanie Ansible umożliwia zautomatyzowanie wdrażania i konfigurowania zasobów w Twoim środowisku. Rozwiązanie Ansible umożliwia zarządzanie maszynami wirtualnymi na platformie Azure, tak jak ma to miejsce w przypadku każdego innego zasobu. W tym artykule przedstawiono sposób użycia podręcznika rozwiązania Ansible do uruchomienia i zatrzymania maszyny wirtualnej z systemem Linux. 

## <a name="prerequisites"></a>Wymagania wstępne

- **Subskrypcja Azure** — jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- **Skonfiguruj usługę Azure Cloud Shell** lub **zainstaluj i skonfiguruj rozwiązanie Ansible na maszynie wirtualnej z systemem Linux**

  **Konfigurowanie usługi Azure Cloud Shell**

  1. **Skonfiguruj usługę Azure Cloud Shell** — jeśli jesteś nowym użytkownikiem usługi Azure Cloud Shell, zapoznaj się z artykułem [Szybki start: powłoka Bash w usłudze Azure Cloud Shell](/azure/cloud-shell/quickstart), w którym pokazano, jak uruchomić i skonfigurować usługę Cloud Shell. 

  1. **Maszyna wirtualna z systemem Linux** — jeśli nie masz dostępu do maszyny wirtualnej z systemem Linux możesz [utworzyć maszynę wirtualną za pomocą rozwiązania Ansible](ansible-create-vm.md).

  **— LUB —**

  **Instalowanie i konfigurowanie rozwiązania Ansible na maszynie wirtualnej z systemem Linux**

  1. **Zainstaluj rozwiązanie Ansible** — zainstaluj rozwiązanie Ansible na [obsługiwanej platformie Linux](/azure/virtual-machines/linux/ansible-install-configure#install-ansible-on-an-azure-linux-virtual-machine).

  1. **Skonfiguruj rozwiązanie Ansible** - [Tworzenie poświadczeń platformy Azure i konfigurowanie rozwiązania Ansible](/azure/virtual-machines/linux/ansible-install-configure#create-azure-credentials)

## <a name="use-ansible-to-deallocate-stop-an-azure-virtual-machine"></a>Używanie rozwiązania Ansible do cofnięcia przydziału (zatrzymania) maszyny wirtualnej na platformie Azure
W tej sekcji pokazano, w jaki sposób użyć rozwiązania Ansible do cofnięcia przydziału (zatrzymania) maszyny wirtualnej na platformie Azure.

1. Zaloguj się w witrynie [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Otwórz usługę [Cloud Shell](/azure/cloud-shell/overview).

1. Utwórz plik, który ma zawierać podręcznik, i nazwij go `azure_vm_stop.yml`, a następnie otwórz go w edytorze VI w następujący sposób:

  ```azurecli-interactive
  vi azure_vm_stop.yml
  ```

1. Przejdź do trybu wstawiania, naciskając klawisz **I**.

1. Wklej następujący kod przykładowy do edytora:

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

1. Opuść tryb wstawiania, naciskając klawisz **Esc**.

1. Zapisz plik i zamknij edytor VI, wprowadzając następujące polecenie:

    ```bash
    :wq
    ```

1. Uruchom przykładowy podręcznik rozwiązania Ansible.

  ```bash
  ansible-playbook azure_vm_stop.yml
  ```

1. Dane wyjściowe wyglądają mniej więcej tak, jak w poniższym przykładzie, który pokazuje, że przydział maszyny wirtualnej został pomyślnie cofnięty (maszyna wirtualna została zatrzymana):

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

1. Zaloguj się w witrynie [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Otwórz usługę [Cloud Shell](/azure/cloud-shell/overview).

1. Utwórz plik, który ma zawierać podręcznik, i nazwij go `azure_vm_start.yml`, a następnie otwórz go w edytorze VI w następujący sposób:

  ```azurecli-interactive
  vi azure_vm_start.yml
  ```

1. Przejdź do trybu wstawiania, naciskając klawisz **I**.

1. Wklej następujący kod przykładowy do edytora:

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

1. Opuść tryb wstawiania, naciskając klawisz **Esc**.

1. Zapisz plik i zamknij edytor VI, wprowadzając następujące polecenie:

    ```bash
    :wq
    ```

1. Uruchom przykładowy podręcznik rozwiązania Ansible.

  ```bash
  ansible-playbook azure_vm_start.yml
  ```

1. Dane wyjściowe wyglądają mniej więcej tak, jak w poniższym przykładzie, który pokazuje, że maszyna wirtualna została pomyślnie uruchomiona:

    Dane wyjściowe wyglądają mniej więcej tak, jak w poniższym przykładzie, który pokazuje, że maszyna wirtualna została pomyślnie uruchomiona:

    ```bash
    PLAY [Stop Azure VM] ********************************************************

    TASK [Gathering Facts] ******************************************************
    ok: [localhost]

    TASK [Start the Virtual Machine] ********************************************
    changed: [localhost]

    PLAY RECAP ******************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0
    ```

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"] 
> [Use Ansible to manage your Azure dynamic inventories (Zarządzenie spisami dynamicznymi na platformie Azure przy użyciu rozwiązania Ansible)](../../ansible/ansible-manage-azure-dynamic-inventories.md)