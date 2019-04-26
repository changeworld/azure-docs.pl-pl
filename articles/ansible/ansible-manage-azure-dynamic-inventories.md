---
title: Zarządzanie spisami dynamicznymi na platformie Azure przy użyciu rozwiązania Ansible
description: Dowiedz się, jak zarządzać spisami dynamicznymi na platformie Azure przy użyciu rozwiązania Ansible
ms.service: azure
keywords: ansible, azure, devops, bash, cloudshell, dynamic inventory
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 08/09/2018
ms.topic: tutorial
ms.openlocfilehash: 0ef754b792654281f2a12b8eee613434896d5476
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60396618"
---
# <a name="use-ansible-to-manage-your-azure-dynamic-inventories"></a>Zarządzanie spisami dynamicznymi na platformie Azure przy użyciu rozwiązania Ansible
Za pomocą rozwiązania Ansible można ściągać informacje o spisie z różnych źródeł (w tym ze źródeł w chmurze, takich jak platforma Azure) do *spisu dynamicznego*. W tym artykule skonfigurujesz spis dynamiczny platformy Azure rozwiązania Ansible przy użyciu usługi [Azure Cloud Shell](./ansible-run-playbook-in-cloudshell.md), następnie utworzysz w nim dwie maszyny wirtualne, jedną z tych maszyn wirtualnych oznaczysz tagiem i zainstalujesz na niej serwer Nginx.

## <a name="prerequisites"></a>Wymagania wstępne

- **Subskrypcja Azure** — jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- **Poświadczenia platformy Azure** - [Tworzenie poświadczeń platformy Azure i konfigurowanie rozwiązania Ansible](/azure/virtual-machines/linux/ansible-install-configure#create-azure-credentials)

## <a name="create-the-test-virtual-machines"></a>Tworzenie testowych maszyn wirtualnych

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

## <a name="tag-a-virtual-machine"></a>Oznaczanie tagiem maszyny wirtualnej
Możesz [użyć tagów, aby zorganizować zasoby platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#azure-cli) według kategorii zdefiniowanych przez użytkownika. 

Wprowadź następujące polecenie [az resource tag](/cli/azure/resource?view=azure-cli-latest.md#az-resource-tag), aby oznaczyć maszynę wirtualną `ansible-inventory-test-vm1` za pomocą klucza `nginx`:

```azurecli-interactive
az resource tag --tags nginx --id /subscriptions/<YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```

## <a name="generate-a-dynamic-inventory"></a>Generowanie spisu dynamicznego
Po zdefiniowaniu i oznaczeniu tagami maszyn wirtualnych nadszedł czas na wygenerowanie spisu dynamicznego. Rozwiązanie Ansible zawiera skrypt w języku Python o nazwie [azure_rm.py](https://github.com/ansible/ansible/blob/devel/contrib/inventory/azure_rm.py), który generuje spis dynamiczny zasobów platformy Azure, wysyłając żądania interfejsu API do usługi Azure Resource Manager. Poniższe kroki pokazują, jak przy użyciu skryptu `azure_rm.py` nawiązać połączenie z dwoma utworzonymi testowymi maszynami wirtualnymi platformy Azure:

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

## <a name="enable-the-virtual-machine-tag"></a>Włączanie tagu maszyny wirtualnej
Po ustawieniu żądanego tagu trzeba go „włączyć”. Jednym ze sposobów włączenia tagu jest wyeksportowanie go do zmiennej środowiskowej o nazwie `AZURE_TAGS` za pośrednictwem polecenia **eksportu**:

```azurecli-interactive
export AZURE_TAGS=nginx
```

Gdy tag zostanie wyeksportowany, możesz ponownie wypróbować polecenie `ansible`:

```azurecli-interactive
ansible -i azure_rm.py ansible-inventory-test-rg -m ping 
```

Teraz zostanie wyświetlona tylko jedna maszyna wirtualna (ta, której tag jest zgodny z wartością wyeksportowaną do zmiennej środowiskowej **AZURE_TAGS**):

```Output
ansible-inventory-test-vm1 | SUCCESS => {
    "changed": false,
    "failed": false,
    "ping": "pong"
}
```

## <a name="set-up-nginx-on-the-tagged-vm"></a>Konfigurowanie serwera Nginx na maszynie wirtualnej oznaczonej tagiem
Celem tagów jest umożliwienie szybkiej i łatwej pracy z podgrupami maszyn wirtualnych. Załóżmy na przykład, że chcesz zainstalować serwer Nginx tylko na tych maszynach wirtualnych, do których przypisano tag `nginx`. Poniższe kroki pokazują, jak łatwo można to osiągnąć:

1. Utwórz plik (który będzie zawierał element playbook) o nazwie `nginx.yml` w następujący sposób:

   ```azurecli-interactive
   vi nginx.yml
   ```

1. Wstaw następujący kod do nowo utworzonego pliku `nginx.yml`:

    ```yml
    ---
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

1. Uruchom element playbook `nginx.yml`:

    ```azurecli-interactive
    ansible-playbook -i azure_rm.py nginx.yml
    ```

1. Po uruchomieniu elementu playbook zostaną wyświetlone wyniki podobne do następujących danych wyjściowych:

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

1. Naciśnij kombinację kalwiszy **&lt;Ctrl>D**, aby rozłączyć sesję SSH.

1. Wykonanie poprzednich kroków dla maszyny wirtualnej `ansible-inventory-test-vm2` powoduje wyświetlenie komunikatu z informacją, gdzie można uzyskać serwer Nginx (co wskazuje, że nie został on jeszcze zainstalowany):

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
> [Tworzenie podstawowej maszyny wirtualnej na platformie Azure za pomocą rozwiązania Ansible](/azure/virtual-machines/linux/ansible-create-vm)
