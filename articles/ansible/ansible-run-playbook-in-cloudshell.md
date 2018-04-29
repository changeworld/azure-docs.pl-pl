---
title: Uruchom Ansible z Bash w chmurze Azure powłoki
description: Dowiedz się, jak wykonywać różne zadania Ansible z Bash w powłoce chmury Azure
ms.service: ansible
keywords: ansible, azure, metodyki devops, bash, cloudshell, podręcznikowym, bash
author: tomarcher
manager: routlaw
ms.author: tarcher
ms.date: 02/01/2018
ms.topic: article
ms.openlocfilehash: 9fe65f4cf10119002bcb7a3855d112d850e20f1a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="run-ansible-with-bash-in-azure-cloud-shell"></a>Uruchom Ansible z Bash w chmurze Azure powłoki

W tym samouczku Dowiedz się jak wykonywać różne zadania Ansible z Bash w powłoce chmury. Te zadania obejmują połączenie z maszyną wirtualną i tworzenie playbooks Ansible do tworzenia i usuwania grupy zasobów platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- **Subskrypcja platformy Azure** — Jeśli nie masz subskrypcji platformy Azure, Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) przed rozpoczęciem.

- **Poświadczenia Azure** - [Azure tworzenia poświadczeń i konfigurowania Ansible](/azure/virtual-machines/linux/ansible-install-configure#create-azure-credentials)

- **Skonfiguruj powłoki chmury Azure** — Jeśli jesteś nowym użytkownikiem powłoki chmury Azure, artykuł [szybkiego startu dla Bash w powłoce chmury Azure](https://docs.microsoft.com/azure/cloud-shell/quickstart) ilustruje sposób uruchomienia i skonfigurowania powłoki chmury. W tym miejscu uruchomić dedykowanej witrynie sieci Web dla powłoki chmury:

[![Uruchom powłokę chmury](https://shell.azure.com/images/launchcloudshell.png "Uruchom powłokę chmury")](https://shell.azure.com)

## <a name="use-ansible-to-connect-to-a-vm"></a>Użyj Ansible nawiązywania połączenia z maszyną wirtualną
Ansible został utworzony skrypt w języku Python o nazwie [azure_rm.py](https://github.com/ansible/ansible/blob/devel/contrib/inventory/azure_rm.py) generujący dynamiczne spisu zasobów platformy Azure poprzez żądania interfejsu API z Menedżerem zasobów Azure. Poniższe kroki umożliwia przeprowadzenie przy użyciu `azure_rm.py` skryptu, aby nawiązać połączenie maszyny wirtualnej platformy Azure:

1. Otwórz Bash w powłoce chmury. Typ powłoki jest oznaczona po lewej stronie okna powłoki chmury.

1. Jeśli nie masz maszynę wirtualną do użycia, wprowadź następujące polecenia, powłokę chmury, aby utworzyć maszynę wirtualną, z którą chcesz przetestować:

  ```azurecli-interactive
  az group create --resource-group ansible-test-rg --location eastus
  ```

  ```azurecli-interactive
  az vm create --resource-group ansible-test-rg --name ansible-test-vm --image UbuntuLTS --generate-ssh-keys
  ```

1. Użyj GNU `wget` polecenie, aby pobrać `azure_rm.py` skryptu:

  ```azurecli-interactive
  wget https://raw.githubusercontent.com/ansible/ansible/devel/contrib/inventory/azure_rm.py
  ```

1. Użyj `chmod` polecenie, aby zmienić uprawnienia dostępu do `azure_rm.py` skryptu. Następujące polecenie używa `+x` parametr umożliwia wykonywanie (uruchomionego) określonego pliku (`azure_rm.py`):

  ```azurecli-interactive
  chmod +x azure_rm.py
  ```

1. Użyj [polecenia ansible](https://docs.ansible.com/ansible/2.4/ansible.html) nawiązywania połączenia z maszyną wirtualną: 

  ```azurecli-interactive
  ansible -i azure_rm.py ansible-test-vm -m ping
  ```

  Po nawiązaniu połączenia powinny być widoczne wyniki podobne do danych wyjściowych:

  ```Output
  The authenticity of host 'nn.nnn.nn.nn (nn.nnn.nn.nn)' can't be established.
  ECDSA key fingerprint is SHA256:&lt;some value>.
  Are you sure you want to continue connecting (yes/no)? yes
  test-ansible-vm | SUCCESS => {
      "changed": false,
      "failed": false,
      "ping": "pong"
  }
  ```

1. Jeśli utworzono maszynę wirtualną i grupy zasobów w tej sekcji

  ```azurecli-interactive
  az group delete -n <resourceGroup>
  ```

## <a name="run-a-playbook-in-cloud-shell"></a>Uruchamianie elementu playbook w usłudze Cloud Shell
[Podręcznikowym ansible](https://docs.ansible.com/ansible/2.4/ansible-playbook.html) playbooks Ansible, uruchamiania zadań na docelowej hostami wykonuje polecenie. W tej sekcji przedstawiono przy użyciu powłoki chmury, aby utworzyć i wykonania dwóch playbooks — jednego do utworzenia grupy zasobów, a drugie, aby usunąć grupę zasobów. 

1. Utwórz plik o nazwie `rg.yml` w następujący sposób:

  ```azurecli-interactive
  vi rg.yml
  ```

1. Skopiuj następującą zawartość do okna powłoki chmury (hostuje teraz wystąpienie edytora VI):

  ```yml
  - name: My first Ansible Playbook
    hosts: localhost
    connection: local
    tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: demoresourcegroup
        location: eastus
  ```

1. Zapisz plik i zamknij Edytor VI wprowadzając `:wq` i naciskając klawisz &lt;Enter >.

1. Użyj `ansible-playbook` polecenie do uruchomienia `rg.yml` podręcznikowym:

  ```azurecli-interactive
  ansible-playbook rg.yml
  ```

1. Powinny być widoczne wyniki podobne do następujących danych wyjściowych:

  ```Output
  PLAY [My first Ansible Playbook] **********

  TASK [Gathering Facts] **********
  ok: [localhost]

  TASK [Create a resource group] **********
  changed: [localhost]

  PLAY RECAP **********
  localhost : ok=2 changed=1 unreachable=0 failed=0
  ```

1. Weryfikacja wdrożenia:

  ```azurecli-interactive
  az group show -n demoresourcegroup
  ```

1. Teraz, gdy masz utworzone grupy zasobów, należy utworzyć drugi podręcznikowym Ansible, aby usunąć grupę zasobów:

  ```azurecli-interactive
  vi rg2.yml
  ```

1. Skopiuj następującą zawartość do okna powłoki chmury (hostuje teraz wystąpienie edytora VI):

  ```yml
  - name: My second Ansible Playbook
    hosts: localhost
    connection: local
    tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: demoresourcegroup
        state: absent
  ```

1. Zapisz plik i zamknij Edytor VI wprowadzając `:wq` i naciskając klawisz &lt;Enter >.

1. Użyj `ansible-playbook` polecenie do uruchomienia `rg2.yml` podręcznikowym:

  ```azurecli-interactive
  ansible-playbook rg.yml
  ```

1. Powinny być widoczne wyniki podobne do następujących danych wyjściowych:

  ```Output
  The output is as following. 
  PLAY [My second Ansible Playbook] **********

  TASK [Gathering Facts] **********
  ok: [localhost]

  TASK [Delete a resource group] **********
  changed: [localhost]

  PLAY RECAP **********
  localhost : ok=2 changed=1 unreachable=0 failed=0
  ```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"] 
> [Tworzenie podstawowej maszyny wirtualnej na platformie Azure z Ansible](/azure/virtual-machines/linux/ansible-create-vm)
