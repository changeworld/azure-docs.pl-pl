---
title: Umożliwia zarządzanie sieci Azure spisów dynamiczne Ansible
description: Dowiedz się, jak użyć Ansible do zarządzania programu Azure spisów dynamiczne
ms.service: ansible
keywords: ansible, azure, metodyki devops, bash, cloudshell, dynamiczne spisu
author: tomarcher
manager: routlaw
ms.author: tarcher
ms.date: 01/14/2018
ms.topic: article
ms.openlocfilehash: f29f4ec64b79738cae2ad684610f4817739825a9
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="use-ansible-to-manage-your-azure-dynamic-inventories"></a>Umożliwia zarządzanie sieci Azure spisów dynamiczne Ansible
Ansible może służyć do pobierania informacji o spisie z różnych źródeł (w tym chmury źródeł, takich jak Azure) do *dynamiczne spisu*. W tym artykule używamy [powłoki chmury Azure](./ansible-run-playbook-in-cloudshell.md) Aby skonfigurować spis dynamiczne Ansible Azure w którym można utworzyć dwie maszyny wirtualne, tagów jednego z tych maszyn wirtualnych i zainstalować Nginx na oznakowanych maszyny wirtualnej.

## <a name="prerequisites"></a>Wymagania wstępne

- **Subskrypcja platformy Azure** — Jeśli nie masz subskrypcji platformy Azure, Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) przed rozpoczęciem.

- **Poświadczenia Azure** - [Azure tworzenia poświadczeń i konfigurowania Ansible](/azure/virtual-machines/linux/ansible-install-configure#create-azure-credentials)

## <a name="create-the-test-virtual-machines"></a>Utwórz testowe maszyny wirtualne

1. Zaloguj się w witrynie [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Otwórz [chmury powłoki](https://docs.microsoft.com/azure/cloud-shell/overview).

1. Utwórz grupę zasobów platformy Azure do przechowywania maszyn wirtualnych w tym samouczku.

    ```azurecli-interactive
    az group create --resource-group ansible-inventory-test-rg --location eastus
    ```

1. Utwórz dwie maszyny wirtualne systemu Linux na platformie Azure przy użyciu jednej z następujących metod:

    - **Podręcznikowym Ansible** — artykułu, [Tworzenie podstawowej maszyny wirtualnej na platformie Azure z Ansible](/azure/virtual-machines/linux/ansible-create-vm) ilustruje sposób tworzenia maszyny wirtualnej z podręcznika dotyczącego Ansible. Jeśli używasz podręcznika dotyczącego do zdefiniowania co najmniej jednej z maszyn wirtualnych, upewnij się, że połączenia SSH jest używana zamiast hasła.

    - **Azure CLI** -problem z następujących poleceń w powłoce chmury do utworzenia dwóch maszyn wirtualnych:

        ```azurecli-interactive
        az vm create --resource-group ansible-inventory-test-rg \
                     --name ansible-inventory-test-vm1 \
                     --image UbuntuLTS --generate-ssh-keys
        ```

        ```azurecli-interactive
        az vm create --resource-group ansible-inventory-test-rg \
                     --name ansible-inventory-test-vm2 \
                     --image UbuntuLTS --generate-ssh-keys
        ```

## <a name="tag-a-virtual-machine"></a>Tag maszyny wirtualnej
Możesz [używaj tagów do organizowania zasobów platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#azure-cli) według kategorii zdefiniowanej przez użytkownika. 

Wprowadź następujące [tag zasobu az](/cli/azure/resource?view=azure-cli-latest.md#az_resource_tag) polecenie, aby oznaczyć maszyny wirtualnej `ansible-inventory-test-vm1` z kluczem `nginx`:

```azurecli-interactive
az resource tag --tags nginx --id /subscriptions/<YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```

## <a name="generate-a-dynamic-inventory"></a>Generowanie spisu dynamiczne
Po utworzeniu maszyn wirtualnych zdefiniowane (i oznakowany), jest Generowanie spisu dynamicznych. Ansible zawiera skrypt w języku Python o nazwie [azure_rm.py](https://github.com/ansible/ansible/blob/devel/contrib/inventory/azure_rm.py) generujący dynamiczne spisu zasobów platformy Azure poprzez żądania interfejsu API z Menedżerem zasobów Azure. Poniższe kroki umożliwia przeprowadzenie przy użyciu `azure_rm.py` skrypt, aby połączyć się z dwóch testów maszyn wirtualnych platformy Azure:

1. Użyj GNU `wget` polecenie, aby pobrać `azure_rm.py` skryptu:

    ```azurecli-interactive
    wget https://raw.githubusercontent.com/ansible/ansible/devel/contrib/inventory/azure_rm.py
    ```

1. Użyj `chmod` polecenie, aby zmienić uprawnienia dostępu do `azure_rm.py` skryptu. Następujące polecenie używa `+x` parametr umożliwia wykonywanie (uruchomionego) określonego pliku (`azure_rm.py`):

    ```azurecli-interactive
    chmod +x azure_rm.py
    ```

1. Użyj [polecenia ansible](https://docs.ansible.com/ansible/2.4/ansible.html) do nawiązania połączenia z grupy zasobów: 

    ```azurecli-interactive
    ansible -i azure_rm.py ansible-inventory-test-rg -m ping 
    ```

1. Po nawiązaniu połączenia można wyświetlić wyniki podobne do następujących danych wyjściowych:

    ```Output
    ansible-inventory-test-vm1 | SUCCESS => {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
    ansible-inventory-test-vm2 | SUCCESS => {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
    ```

## <a name="enable-the-virtual-machine-tag"></a>Włącz tag maszyny wirtualnej
Po ustawieniu żądany tag, musisz "Włącz" tagu. Jednym ze sposobów Włącz tag eksportując tag do zmiennej środowiskowej nosi nazwę `AZURE_TAGS` za pośrednictwem **wyeksportować** polecenia:

```azurecli-interactive
export AZURE_TAGS=nginx
```

Po wyeksportowaniu tagu można spróbować `ansible` ponownie polecenie:

```azurecli-interactive
ansible -i azure_rm.py ansible-inventory-test-rg -m ping 
```

Pojawi się tylko jedna maszyna wirtualna (jeden tag, którego jest zgodna z wartością eksportowane **AZURE_TAGS** zmienną środowiskową):

```Output
ansible-inventory-test-vm1 | SUCCESS => {
    "changed": false,
    "failed": false,
    "ping": "pong"
}
```

## <a name="set-up-nginx-on-the-tagged-vm"></a>Konfigurowanie Nginx na Maszynie wirtualnej oznakowany
Celem tagów jest umożliwienie możliwość szybkiego i łatwego korzystania z podgrupy maszyn wirtualnych. Na przykład, załóżmy, że chcesz zainstalować tylko na maszynach wirtualnych, do których został przypisany tagu Nginx `nginx`. Poniższe kroki przedstawiają, jak łatwo to zrobić:

1. Aby utworzyć plik (zawierać podręcznika użytkownika dotyczącego) o nazwie `nginx.yml` w następujący sposób:

  ```azurecli-interactive
  vi nginx.yml
  ```

1. Wstaw następujący kod do nowo utworzony `nginx.yml` pliku:

    ```yml
    - name: Install and start Nginx on an Azure virtual machine
    hosts: azure
    become: yes
    tasks:
    - name: install nginx
        apt: pkg=nginx state=installed
        notify:
        - start nginx

    handlers:
    - name: start nginx
        service: name=nginx state=started
    ```

1. Uruchom `nginx.yml` podręcznikowym:

  ```azurecli-interactive
  ansible-playbook -i azure_rm.py nginx.yml
  ```

1. Po uruchomieniu podręcznika dotyczącego zostaną wyświetlone wyniki podobne do następujących danych wyjściowych:

    ```Output
    PLAY [Install and start Nginx on an Azure virtual machine] **********

    TASK [Gathering Facts] **********
    ok: [ansible-inventory-test-vm1]

    TASK [install nginx] **********
    changed: [ansible-inventory-test-vm1]

    RUNNING HANDLER [start nginx] **********
    ok: [ansible-inventory-test-vm1]

    PLAY RECAP **********
    ansible-inventory-test-vm1 : ok=3    changed=1    unreachable=0    failed=0
    ```

## <a name="test-nginx-installation"></a>Testowanie instalacji Nginx
W tej części przedstawiono jeden technika, aby sprawdzić, czy Nginx jest zainstalowany na maszynie wirtualnej.

1. Użyj [az vm--adresy ip](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az_vm_list_ip_addresses) polecenie, aby uzyskać adres IP `ansible-inventory-test-vm1` maszyny wirtualnej. Zwrócona wartość (adres IP maszyny wirtualnej) następnie jest używany jako parametr polecenia SSH do nawiązania połączenia z maszyną wirtualną.

    ```azurecli-interactive
    ssh `az vm list-ip-addresses \
    -n ansible-inventory-test-vm1 \
    --query [0].virtualMachine.network.publicIpAddresses[0].ipAddress -o tsv`
    ```

1. [Nginx - v](https://nginx.org/en/docs/switches.html) zwykle używane jest polecenie, aby wydrukować wersję Nginx. Jednak również umożliwia ustalenie, czy zainstalowano Nginx. Wprowadź go podczas połączenia z `ansible-inventory-test-vm1` maszyny wirtualnej.

    ```azurecli-interactive
    nginx -v
    ```

1. Po uruchomieniu `nginx -v` polecenia, zobacz wersji Nginx (drugi wiersz) wskazujące, że zainstalowano Nginx.

    ```Output
    tom@ansible-inventory-test-vm1:~$ nginx -v

    nginx version: nginx/1.10.3 (Ubuntu)
    
    tom@ansible-inventory-test-vm1:~$
    ```

1. Naciśnij klawisz  **&lt;Ctrl > D** klawiatury kombinacja rozłączyć sesję SSH.

1. Wykonywanie poprzednie kroki dla `ansible-inventory-test-vm2` komunikat informacyjny wskazujący, gdzie można uzyskać Nginx (co oznacza, że nie jest zainstalowany na tym etapie) daje maszyny wirtualnej:

    ```Output
    tom@ansible-inventory-test-vm2:~$ nginx -v
    The program 'nginx' can be found in the following packages:
    * nginx-core
    * nginx-extras
    * nginx-full
    * nginx-lightTry: sudo apt install <selected package>
    tom@ansible-inventory-test-vm2:~$
    ```

## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"] 
> [Tworzenie podstawowej maszyny wirtualnej na platformie Azure z Ansible](/azure/virtual-machines/linux/ansible-create-vm)
