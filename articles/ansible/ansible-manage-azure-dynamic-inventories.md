---
title: Samouczek — Konfigurowanie dynamicznych spisów zasobów platformy Azure przy użyciu rozwiązania ansible
description: Dowiedz się, jak zarządzać spisami dynamicznymi na platformie Azure przy użyciu rozwiązania Ansible
keywords: ansible, azure, devops, bash, cloudshell, dynamic inventory
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: cd225dcf8a0c307d49e985817b71c491559edb14
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2020
ms.locfileid: "78247858"
---
# <a name="tutorial-configure-dynamic-inventories-of-your-azure-resources-using-ansible"></a>Samouczek: Konfigurowanie dynamicznych spisów zasobów platformy Azure przy użyciu rozwiązania ansible

Za pomocą rozwiązania Ansible można ściągać informacje o spisie z różnych źródeł (w tym ze źródeł w chmurze, takich jak platforma Azure) do *spisu dynamicznego*. 

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Skonfiguruj dwie testowe maszyny wirtualne. 
> * Oznacz jedną z maszyn wirtualnych
> * Zainstaluj Nginx na oznakowanych maszynach wirtualnych
> * Skonfiguruj dynamiczny spis zawierający skonfigurowane zasoby platformy Azure

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-test-vms"></a>Tworzenie testowych maszyn wirtualnych

1. Zaloguj się do [Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Otwórz usługę [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview).

1. Utwórz grupę zasobów platformy Azure do przechowywania maszyn wirtualnych na potrzeby tego samouczka.

    > [!IMPORTANT]  
    > Grupa zasobów platformy Azure utworzona w tym kroku musi mieć nazwę składającą się wyłącznie z małych liter. W przeciwnym razie generowanie spisu dynamicznego nie powiedzie się.

    ```azurecli-interactive
    az group create --resource-group ansible-inventory-test-rg --location eastus
    ```

1. Utwórz dwie maszyny wirtualne systemu Linux na platformie Azure przy użyciu jednej z następujących technik:

    - **Element playbook rozwiązania Ansible** — artykuł [Tworzenie podstawowej maszyny wirtualnej na platformie Azure za pomocą rozwiązania Ansible](./ansible-create-vm.md) pokazuje, jak utworzyć maszynę wirtualną na podstawie elementu playbook rozwiązania Ansible. Jeśli definiujesz jedną lub obydwie maszyny wirtualne za pomocą elementu playbook, upewnij się, że zamiast hasła jest używane połączenie SSH.

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

### <a name="using-ansible-version--28"></a>Używanie rozwiązania ansible w wersji < 2,8
Wprowadź następujące polecenie [az resource tag](/cli/azure/resource?view=azure-cli-latest.md#az-resource-tag), aby oznaczyć maszynę wirtualną `ansible-inventory-test-vm1` za pomocą klucza `nginx`:

```azurecli-interactive
az resource tag --tags nginx --id /subscriptions/<YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```

### <a name="using-ansible-version--28"></a>Używanie rozwiązania ansible w wersji > = 2,8
Wprowadź następujące polecenie [az resource tag](/cli/azure/resource?view=azure-cli-latest.md#az-resource-tag), aby oznaczyć maszynę wirtualną `ansible-inventory-test-vm1` za pomocą klucza `Ansible=nginx`:

```azurecli-interactive
az resource tag --tags Ansible=nginx --id /subscriptions/<YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```

## <a name="generate-a-dynamic-inventory"></a>Generowanie spisu dynamicznego

Po zdefiniowaniu i oznaczeniu tagami maszyn wirtualnych nadszedł czas na wygenerowanie spisu dynamicznego.

### <a name="using-ansible-version--28"></a>Używanie rozwiązania ansible w wersji < 2,8

Rozwiązania ansible udostępnia skrypt w języku Python o nazwie [azure_rm. PR](https://github.com/ansible/ansible/blob/devel/contrib/inventory/azure_rm.py) , który generuje dynamiczny spis zasobów platformy Azure. Poniższe kroki pokazują, jak przy użyciu skryptu `azure_rm.py` nawiązać połączenie z dwoma utworzonymi testowymi maszynami wirtualnymi platformy Azure:

1. Przy użyciu polecenia GNU `wget` pobierz skrypt `azure_rm.py`:

    ```python
    wget https://raw.githubusercontent.com/ansible/ansible/devel/contrib/inventory/azure_rm.py
    ```

1. Przy użyciu polecenia `chmod` zmień uprawnienia dostępu do skryptu `azure_rm.py`. W poniższym poleceniu użyto parametru `+x`, aby zezwolić na wykonanie (uruchomienie) określonego pliku (`azure_rm.py`):

    ```python
    chmod +x azure_rm.py
    ```

1. Przy użyciu [polecenia ansible](https://docs.ansible.com/ansible/2.4/ansible.html) połącz się z grupą zasobów: 

    ```python
    ansible -i azure_rm.py ansible-inventory-test-rg -m ping 
    ```

1. Po nawiązaniu połączenia zostaną wyświetlone wyniki podobne do następujących danych wyjściowych:

    ```output
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

### <a name="ansible-version--28"></a>Wersja rozwiązania ansible > = 2,8

Począwszy od rozwiązania ansible 2,8, rozwiązania ansible udostępnia [wtyczkę dynamicznego spisu platformy Azure](https://github.com/ansible/ansible/blob/devel/lib/ansible/plugins/inventory/azure_rm.py). W poniższych krokach przedstawiono sposób korzystania z wtyczki:

1. Wtyczka spisu wymaga pliku konfiguracji. Plik konfiguracji musi kończyć się `azure_rm` i mieć rozszerzenie `yml` lub `yaml`. Na potrzeby tego przykładu Zapisz następujący element PlayBook jako `myazure_rm.yml`:

    ```yml
        plugin: azure_rm
        include_vm_resource_groups:
        - ansible-inventory-test-rg
        auth_source: auto
    
        keyed_groups:
        - prefix: tag
          key: tags
    ```

1. Uruchom następujące polecenie, aby wysłać polecenie ping do maszyn wirtualnych w grupie zasobów:

    ```bash
    ansible all -m ping -i ./myazure_rm.yml
    ```

1. Podczas uruchamiania poprzedniego polecenia może zostać wyświetlony następujący błąd:

    ```output
    Failed to connect to the host via ssh: Host key verification failed.
    ```
    
    Jeśli zostanie wyświetlony komunikat o błędzie "Weryfikacja klucza hosta", Dodaj następujący wiersz do pliku konfiguracji rozwiązania ansible. Plik konfiguracji rozwiązania ansible znajduje się w lokalizacji `/etc/ansible/ansible.cfg` lub `~/.ansible.cfg`.

    ```bash
    host_key_checking = False
    ```

1. Po uruchomieniu element PlayBook są wyświetlane wyniki podobne do następujących:
  
    ```output
    ansible-inventory-test-vm1_0324 : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ansible-inventory-test-vm2_8971 : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ```

## <a name="enable-the-vm-tag"></a>Włącz Tag maszyny wirtualnej

### <a name="if-youre-using-ansible--28"></a>Jeśli używasz rozwiązania ansible < 2,8,

- Po ustawieniu znacznika należy "Włącz" ten tag. Jednym ze sposobów na włączenie znacznika jest wyeksportowanie znacznika do zmiennej środowiskowej `AZURE_TAGS` za pomocą polecenia `export`:

    ```console
    export AZURE_TAGS=nginx
    ```
    
- Uruchom następujące polecenie:

    ```bash
    ansible -i azure_rm.py ansible-inventory-test-rg -m ping
    ```
    
    Zobaczysz teraz tylko jedną maszynę wirtualną (której tag pasuje do wartości wyeksportowanej do zmiennej środowiskowej `AZURE_TAGS`):

    ```output
       ansible-inventory-test-vm1 | SUCCESS => {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
    ```

### <a name="if-youre-using-ansible---28"></a>Jeśli używasz rozwiązania ansible > = 2,8

- Uruchom polecenie `ansible-inventory -i myazure_rm.yml --graph`, aby uzyskać następujące dane wyjściowe:

    ```output
        @all:
          |--@tag_Ansible_nginx:
          |  |--ansible-inventory-test-vm1_9e2f
          |--@ungrouped:
          |  |--ansible-inventory-test-vm2_7ba9
    ```

- Aby przetestować połączenie z maszyną wirtualną Nginx, można również uruchomić następujące polecenie:
  
    ```bash
    ansible -i ./myazure_rm.yml -m ping tag_Ansible_nginx
    ```


## <a name="set-up-nginx-on-the-tagged-vm"></a>Konfigurowanie serwera Nginx na maszynie wirtualnej oznaczonej tagiem

Celem tagów jest umożliwienie szybkiej i łatwej pracy z podgrupami maszyn wirtualnych. Załóżmy na przykład, że chcesz zainstalować serwer Nginx tylko na tych maszynach wirtualnych, do których przypisano tag `nginx`. Poniższe kroki pokazują, jak łatwo można to osiągnąć:

1. Utwórz plik o nazwie `nginx.yml`:

   ```console
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
            apt: pkg=nginx state=present
            notify:
            - start nginx
    
          handlers:
            - name: start nginx
              service: name=nginx state=started
    ```

1. Zapisz plik i Zamknij Edytor.

1. Uruchom element PlayBook przy użyciu polecenia `ansible-playbook`:

   - Rozwiązania ansible < 2,8:

     ```bash
     ansible-playbook -i azure_rm.py nginx.yml
     ```

   - Rozwiązania ansible > = 2,8:

     ```bash
     ansible-playbook  -i ./myazure_rm.yml  nginx.yml --limit=tag_Ansible_nginx
     ```

1. Po uruchomieniu element PlayBook są wyświetlane dane wyjściowe podobne do następujących:

    ```output
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

    ```console
    nginx -v
    ```

1. Po uruchomieniu polecenia `nginx -v` zobaczysz wersję serwera Nginx (drugi wiersz), co oznacza, że serwer Nginx został zainstalowany.

    ```output
    tom@ansible-inventory-test-vm1:~$ nginx -v

    nginx version: nginx/1.10.3 (Ubuntu)

    tom@ansible-inventory-test-vm1:~$
    ```

1. Kliknij kombinację klawiatury `<Ctrl>D`, aby rozłączyć sesję SSH.

1. Wykonanie powyższych kroków dla maszyny wirtualnej `ansible-inventory-test-vm2` daje komunikat informacyjny wskazujący, że można uzyskać Nginx (co oznacza, że nie jest on zainstalowany w tym momencie):

    ```output
    tom@ansible-inventory-test-vm2:~$ nginx -v
    The program 'nginx' can be found in the following packages:
    * nginx-core
    * nginx-extras
    * nginx-full
    * nginx-lightTry: sudo apt install <selected package>
    tom@ansible-inventory-test-vm2:~$
    ```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"] 
> [Szybki Start: Konfigurowanie maszyn wirtualnych z systemem Linux na platformie Azure przy użyciu rozwiązania ansible](./ansible-create-vm.md)