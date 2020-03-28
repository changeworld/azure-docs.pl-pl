---
title: Samouczek — konfigurowanie dynamicznych zapasów zasobów platformy Azure przy użyciu metody ansible
description: Dowiedz się, jak zarządzać spisami dynamicznymi na platformie Azure przy użyciu rozwiązania Ansible
keywords: ansible, azure, devops, bash, cloudshell, dynamic inventory
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: cd225dcf8a0c307d49e985817b71c491559edb14
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78247858"
---
# <a name="tutorial-configure-dynamic-inventories-of-your-azure-resources-using-ansible"></a>Samouczek: Konfigurowanie dynamicznych zapasów zasobów platformy Azure przy użyciu metody ansible

Za pomocą rozwiązania Ansible można ściągać informacje o spisie z różnych źródeł (w tym ze źródeł w chmurze, takich jak platforma Azure) do *spisu dynamicznego*. 

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Skonfiguruj dwie testowe maszyny wirtualne. 
> * Oznaczanie jednej z maszyn wirtualnych
> * Instalowanie nginx na oznakowanych maszynach wirtualnych
> * Konfigurowanie dynamicznego spisu zapasów zawierającego skonfigurowane zasoby platformy Azure

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-test-vms"></a>Tworzenie testowych maszyn wirtualnych

1. Zaloguj się do [Portalu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Otwórz [powłokę chmury](https://docs.microsoft.com/azure/cloud-shell/overview).

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

### <a name="using-ansible-version--28"></a>Korzystanie z wersji Ansible < 2.8
Wprowadź następujące polecenie [az resource tag](/cli/azure/resource?view=azure-cli-latest.md#az-resource-tag), aby oznaczyć maszynę wirtualną `ansible-inventory-test-vm1` za pomocą klucza `nginx`:

```azurecli-interactive
az resource tag --tags nginx --id /subscriptions/<YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```

### <a name="using-ansible-version--28"></a>Korzystanie z wersji Ansible >= 2.8
Wprowadź następujące polecenie [az resource tag](/cli/azure/resource?view=azure-cli-latest.md#az-resource-tag), aby oznaczyć maszynę wirtualną `ansible-inventory-test-vm1` za pomocą klucza `Ansible=nginx`:

```azurecli-interactive
az resource tag --tags Ansible=nginx --id /subscriptions/<YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```

## <a name="generate-a-dynamic-inventory"></a>Generowanie spisu dynamicznego

Po zdefiniowaniu i oznaczeniu tagami maszyn wirtualnych nadszedł czas na wygenerowanie spisu dynamicznego.

### <a name="using-ansible-version--28"></a>Korzystanie z wersji Ansible < 2.8

Ansible udostępnia skrypt języka Python o nazwie [azure_rm.py,](https://github.com/ansible/ansible/blob/devel/contrib/inventory/azure_rm.py) który generuje dynamiczny spis zasobów platformy Azure. Poniższe kroki pokazują, jak przy użyciu skryptu `azure_rm.py` nawiązać połączenie z dwoma utworzonymi testowymi maszynami wirtualnymi platformy Azure:

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

### <a name="ansible-version--28"></a>Wersja ansible >= 2.8

Począwszy od Ansible 2.8, Ansible zapewnia [wtyczkę dynamicznego spisu platformy Azure.](https://github.com/ansible/ansible/blob/devel/lib/ansible/plugins/inventory/azure_rm.py) Poniższe kroki przebiegają za pomocą wtyczki:

1. Wtyczka spisu wymaga pliku konfiguracyjnego. Plik konfiguracyjny `azure_rm` musi się kończyć `yml` `yaml`i mieć rozszerzenie jednego lub . W tym przykładzie samouczka zapisz `myazure_rm.yml`następujący podręcznik jako:

    ```yml
        plugin: azure_rm
        include_vm_resource_groups:
        - ansible-inventory-test-rg
        auth_source: auto
    
        keyed_groups:
        - prefix: tag
          key: tags
    ```

1. Uruchom następujące polecenie do pingu maszyn wirtualnych w grupie zasobów:

    ```bash
    ansible all -m ping -i ./myazure_rm.yml
    ```

1. Podczas uruchamiania poprzedniego polecenia może pojawić się następujący błąd:

    ```output
    Failed to connect to the host via ssh: Host key verification failed.
    ```
    
    Jeśli zostanie wyświetlony błąd "weryfikacji klucza hosta", dodaj następujący wiersz do pliku konfiguracyjnego Ansible. Plik konfiguracyjny Ansible znajduje się w `/etc/ansible/ansible.cfg` pliku lub `~/.ansible.cfg`.

    ```bash
    host_key_checking = False
    ```

1. Po uruchomieniu podręcznika widoczne są wyniki podobne do następujących danych wyjściowych:
  
    ```output
    ansible-inventory-test-vm1_0324 : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ansible-inventory-test-vm2_8971 : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ```

## <a name="enable-the-vm-tag"></a>Włączanie znacznika maszyny Wirtualnej

### <a name="if-youre-using-ansible--28"></a>Jeśli używasz Ansible < 2.8,

- Po ustawieniu tagu musisz "włączyć" ten tag. Jednym ze sposobów włączenia znacznika jest wyeksportowanie znacznika do zmiennej `AZURE_TAGS` środowiskowej `export` za pomocą polecenia:

    ```console
    export AZURE_TAGS=nginx
    ```
    
- Uruchom następujące polecenie:

    ```bash
    ansible -i azure_rm.py ansible-inventory-test-rg -m ping
    ```
    
    Teraz widzisz tylko jedną maszynę wirtualną (ta, której `AZURE_TAGS` tag odpowiada wartości eksportowanej do zmiennej środowiskowej):

    ```output
       ansible-inventory-test-vm1 | SUCCESS => {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
    ```

### <a name="if-youre-using-ansible---28"></a>Jeśli używasz Ansible >= 2.8

- Uruchom polecenie, `ansible-inventory -i myazure_rm.yml --graph` aby uzyskać następujące dane wyjściowe:

    ```output
        @all:
          |--@tag_Ansible_nginx:
          |  |--ansible-inventory-test-vm1_9e2f
          |--@ungrouped:
          |  |--ansible-inventory-test-vm2_7ba9
    ```

- Można również uruchomić następujące polecenie, aby przetestować połączenie z maszyną wirtualną Nginx:
  
    ```bash
    ansible -i ./myazure_rm.yml -m ping tag_Ansible_nginx
    ```


## <a name="set-up-nginx-on-the-tagged-vm"></a>Konfigurowanie serwera Nginx na maszynie wirtualnej oznaczonej tagiem

Celem tagów jest umożliwienie szybkiej i łatwej pracy z podgrupami maszyn wirtualnych. Załóżmy na przykład, że chcesz zainstalować serwer Nginx tylko na tych maszynach wirtualnych, do których przypisano tag `nginx`. Poniższe kroki pokazują, jak łatwo można to osiągnąć:

1. Tworzenie pliku `nginx.yml`o nazwie :

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

1. Zapisz plik i zamknij edytor.

1. Uruchom podręcznik za `ansible-playbook` pomocą polecenia:

   - Możliwość < 2.8:

     ```bash
     ansible-playbook -i azure_rm.py nginx.yml
     ```

   - Możliwość >= 2,8:

     ```bash
     ansible-playbook  -i ./myazure_rm.yml  nginx.yml --limit=tag_Ansible_nginx
     ```

1. Po uruchomieniu podręcznika zobaczysz dane wyjściowe podobne do następujących wyników:

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

1. Kliknij `<Ctrl>D` kombinację klawiatury, aby odłączyć sesję SSH.

1. Wykonanie powyższych kroków `ansible-inventory-test-vm2` dla maszyny wirtualnej daje komunikat informacyjny wskazujący, gdzie można uzyskać Nginx (co oznacza, że nie masz go zainstalowanego w tym momencie):

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
> [Szybki start: konfigurowanie maszyn wirtualnych systemu Linux na platformie Azure przy użyciu aplikacji Ansible](./ansible-create-vm.md)