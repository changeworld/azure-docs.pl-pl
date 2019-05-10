---
title: Samouczek — Konfigurowanie spisami dynamicznymi zasobów platformy Azure za pomocą rozwiązania Ansible | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzać spisami dynamicznymi na platformie Azure przy użyciu rozwiązania Ansible
keywords: ansible, azure, devops, bash, cloudshell, dynamic inventory
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 46b13fae437a555edf0bdd0b0d4c1496d7596e0f
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2019
ms.locfileid: "65230698"
---
# <a name="tutorial-configure-dynamic-inventories-of-your-azure-resources-using-ansible"></a>Samouczek: Konfigurowanie spisami dynamicznymi zasobów platformy Azure za pomocą rozwiązania Ansible

Za pomocą rozwiązania Ansible można ściągać informacje o spisie z różnych źródeł (w tym ze źródeł w chmurze, takich jak platforma Azure) do *spisu dynamicznego*. 

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Konfigurowanie dwóch testowych maszyn wirtualnych. 
> * Tag jednej z maszyn wirtualnych
> * Zainstalować rozwiązanie Nginx na oznakowanych maszyn wirtualnych
> * Konfigurowanie dynamicznego spis, który zawiera skonfigurowany zasobów platformy Azure

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-test-vms"></a>Utwórz test maszyn wirtualnych

1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Otwórz usługę [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview).

1. Utwórz grupę zasobów platformy Azure do przechowywania maszyn wirtualnych na potrzeby tego samouczka.

    > [!IMPORTANT]  
    > Grupa zasobów platformy Azure utworzona w tym kroku musi mieć nazwę składającą się wyłącznie z małych liter. W przeciwnym razie generowanie spisu dynamicznego nie powiedzie się.

    ```azurecli-interactive
    az group create --resource-group ansible-inventory-test-rg --location eastus
    ```

1. Utwórz dwie maszyny wirtualne systemu Linux na platformie Azure przy użyciu jednej z następujących technik:

    - **Element playbook rozwiązania Ansible** — artykuł [Tworzenie podstawowej maszyny wirtualnej na platformie Azure za pomocą rozwiązania Ansible](/azure/virtual-machines/linux/ansible-create-vm) pokazuje, jak utworzyć maszynę wirtualną na podstawie elementu playbook rozwiązania Ansible. Jeśli definiujesz jedną lub obydwie maszyny wirtualne za pomocą elementu playbook, upewnij się, że zamiast hasła jest używane połączenie SSH.

    - **Interfejs wiersza polecenia platformy Azure** — aby utworzyć dwie maszyny wirtualne, uruchom każde z następujących poleceń w usłudze Cloud Shell:

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

## <a name="tag-a-vm"></a>Tagowanie maszyny wirtualnej

Możesz [użyć tagów, aby zorganizować zasoby platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#azure-cli) według kategorii zdefiniowanych przez użytkownika. 

Wprowadź następujące polecenie [az resource tag](/cli/azure/resource?view=azure-cli-latest.md#az-resource-tag), aby oznaczyć maszynę wirtualną `ansible-inventory-test-vm1` za pomocą klucza `nginx`:

```azurecli-interactive
az resource tag --tags nginx --id /subscriptions/<YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```
## <a name="generate-a-dynamic-inventory"></a>Generowanie spisu dynamicznego

Po zdefiniowaniu i oznaczeniu tagami maszyn wirtualnych nadszedł czas na wygenerowanie spisu dynamicznego.

### <a name="using-ansible-version--28"></a>Za pomocą rozwiązania Ansible w wersji < 2.8

Rozwiązanie Ansible zawiera skrypt w języku Python o nazwie [azure_rm.py](https://github.com/ansible/ansible/blob/devel/contrib/inventory/azure_rm.py) generujący dynamiczny spis zasobów platformy Azure. Poniższe kroki pokazują, jak przy użyciu skryptu `azure_rm.py` nawiązać połączenie z dwoma utworzonymi testowymi maszynami wirtualnymi platformy Azure:

1. Przy użyciu polecenia GNU `wget` pobierz skrypt `azure_rm.py`:

    ```azurecli-interactive
    wget https://raw.githubusercontent.com/ansible/ansible/devel/contrib/inventory/azure_rm.py
    ```

1. Przy użyciu polecenia `chmod` zmień uprawnienia dostępu do skryptu `azure_rm.py`. W poniższym poleceniu użyto parametru `+x`, aby zezwolić na wykonanie (uruchomienie) określonego pliku (`azure_rm.py`):

    ```azurecli-interactive
    chmod +x azure_rm.py
    ```

1. Przy użyciu [polecenia ansible](https://docs.ansible.com/ansible/2.4/ansible.html) połącz się z grupą zasobów: 

    ```azurecli-interactive
    ansible -i azure_rm.py ansible-inventory-test-rg -m ping 
    ```

1. Po nawiązaniu połączenia zostaną wyświetlone wyniki podobne do następujących danych wyjściowych:

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

### <a name="ansible-version--28"></a>Rozwiązanie Ansible w wersji > = 2.8

Począwszy od Ansible 2.8 udostępnia rozwiązania Ansible [wtyczka usługi Azure dynamiczny spis](https://github.com/ansible/ansible/blob/devel/lib/ansible/plugins/inventory/azure_rm.py). W poniższych krokach objaśniono za pomocą wtyczki:

1. Wtyczka magazynu wymaga pliku konfiguracji. Plik konfiguracji może kończyć się `azure_rm` i mieć rozszerzenie albo `yml` lub `yaml`. W tym przykładzie samouczek Zapisz następujący element playbook jako `myazure_rm.yml`:

    ```yml
    plugin: azure_rm
    include_vm_resource_groups:
    - ansible-inventory-test-rg
    auth_source: auto
    ```

1. Uruchom następujące polecenie, aby wykonać polecenie ping maszyn wirtualnych w grupie zasobów:

    ```bash
    ansible all -m ping -i ./myazure_rm.yml
    ```

1. Gdy uruchomienie poprzedzającego polecenia, może zostać wyświetlony następujący błąd:

    ```Output
    Failed to connect to the host via ssh: Host key verification failed.
    ```
    
    Jeśli wystąpi błąd "weryfikację klucza hosta", Dodaj następujący wiersz do pliku konfiguracji rozwiązania Ansible. Rozwiązanie Ansible plik konfiguracji znajduje się w `/etc/ansible/ansible.cfg`.

    ```bash
    host_key_checking = False
    ```

1. Po uruchomieniu elementu playbook, zostaną wyświetlone wyniki podobne do następujących danych wyjściowych:
  
    ```Output
    ansible-inventory-test-vm1_0324 : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ansible-inventory-test-vm2_8971 : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ```

## <a name="enable-the-vm-tag"></a>Włącz tag maszyny Wirtualnej
Po ustawieniu tag, musisz "Włącz" tego tagu. Jest jednym ze sposobów, aby umożliwić znacznik, eksportując tag do zmiennej środowiskowej `AZURE_TAGS` za pośrednictwem `export` polecenia:

```azurecli-interactive
export AZURE_TAGS=nginx
```

- Jeśli używasz rozwiązania Ansible < 2.8, uruchom następujące polecenie:

    ```bash
    ansible -i azure_rm.py ansible-inventory-test-rg -m ping
    ```

- Jeśli używasz rozwiązania Ansible > = 2.8, uruchom następujące polecenie:
  
    ```bash
    ansible all -m ping -i ./myazure_rm.yml
    ```

Pojawi się tylko jedna maszyna wirtualna (jeden tag, którego pasuje do wartości eksportowane do `AZURE_TAGS` zmienną środowiskową):

```Output
ansible-inventory-test-vm1 | SUCCESS => {
    "changed": false,
    "failed": false,
    "ping": "pong"
}
```

## <a name="set-up-nginx-on-the-tagged-vm"></a>Konfigurowanie serwera Nginx na maszynie wirtualnej oznaczonej tagiem

Celem tagów jest umożliwienie szybkiej i łatwej pracy z podgrupami maszyn wirtualnych. Załóżmy na przykład, że chcesz zainstalować serwer Nginx tylko na tych maszynach wirtualnych, do których przypisano tag `nginx`. Poniższe kroki pokazują, jak łatwo można to osiągnąć:

1. Utwórz plik o nazwie `nginx.yml`:

   ```azurecli-interactive
   code nginx.yml
   ```

1. Wklej następujący kod przykładowy do edytora:

    ```yml
    ---
    - name: Install and start Nginx on an Azure virtual machine
      hosts: all
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

1. Zapisz plik i zamknij Edytor.

1. Uruchamianie elementu playbook, przy użyciu `ansible-playbook` polecenia:

   - Rozwiązanie Ansible < 2.8:

    ```bash
    ansible-playbook -i azure_rm.py nginx.yml
    ```

   - Rozwiązanie Ansible > = 2.8:

    ```bash
     ansible-playbook  -i ./myazure_rm.yml  nginx.yml
    ```

1. Po uruchomieniu elementu playbook, zobaczysz dane wyjściowe podobne do następujących wyników:

    ```Output
    PLAY [Install and start Nginx on an Azure virtual machine] 

    TASK [Gathering Facts] 
    ok: [ansible-inventory-test-vm1]

    TASK [install nginx] 
    changed: [ansible-inventory-test-vm1]

    RUNNING HANDLER [start nginx] 
    ok: [ansible-inventory-test-vm1]

    PLAY RECAP 
    ansible-inventory-test-vm1 : ok=3    changed=1    unreachable=0    failed=0
    ```

## <a name="test-nginx-installation"></a>Testowanie instalacji serwera Nginx

W tej sekcji przedstawiono jedną technikę, za pomocą której można sprawdzić, czy na maszynie wirtualnej jest zainstalowane oprogramowanie Nginx.

1. Przy użyciu polecenia [az vm list-ip-addresses](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-list-ip-addresses) pobierz adres IP maszyny wirtualnej `ansible-inventory-test-vm1`. Zwrócona wartość (adres IP maszyny wirtualnej) jest następnie używana jako parametr w poleceniu SSH do nawiązania połączenia z maszyną wirtualną.

    ```azurecli-interactive
    ssh `az vm list-ip-addresses \
    -n ansible-inventory-test-vm1 \
    --query [0].virtualMachine.network.publicIpAddresses[0].ipAddress -o tsv`
    ```

1. Po nawiązaniu połączenia z maszyną wirtualną `ansible-inventory-test-vm1` uruchom polecenie [nginx -v](https://nginx.org/en/docs/switches.html), aby określić, czy serwer Nginx jest zainstalowany.

    ```azurecli-interactive
    nginx -v
    ```

1. Po uruchomieniu polecenia `nginx -v` zobaczysz wersję serwera Nginx (drugi wiersz), co oznacza, że serwer Nginx został zainstalowany.

    ```Output
    tom@ansible-inventory-test-vm1:~$ nginx -v

    nginx version: nginx/1.10.3 (Ubuntu)

    tom@ansible-inventory-test-vm1:~$
    ```

1. Kliknij przycisk `<Ctrl>D` za pomocą klawiatury w połączeniu, aby rozłączyć sesję SSH.

1. Wykonując poprzednie kroki dla `ansible-inventory-test-vm2` maszyny wirtualnej daje komunikat informacyjny wskazujący, gdzie można uzyskać serwera Nginx (co oznacza, że nie jest zainstalowany na tym etapie):

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
> [Szybki start: Konfigurowanie maszyn wirtualnych systemu Linux na platformie Azure, za pomocą rozwiązania Ansible](/azure/virtual-machines/linux/ansible-create-vm)