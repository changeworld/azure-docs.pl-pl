---
title: Zarządzanie usługi platformy Azure spisami dynamicznymi za pomocą rozwiązania Ansible
description: Dowiedz się, jak zarządzać usługi platformy Azure spisami dynamicznymi za pomocą rozwiązania Ansible
ms.service: ansible
keywords: rozwiązanie ansible, azure, metodyki devops, bash, cloudshell, dynamiczny spis
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 08/09/2018
ms.topic: article
ms.openlocfilehash: 1f19d5918d81acb76936edf8989a556335a3c0df
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51261268"
---
# <a name="use-ansible-to-manage-your-azure-dynamic-inventories"></a>Zarządzanie usługi platformy Azure spisami dynamicznymi za pomocą rozwiązania Ansible
Rozwiązanie Ansible może służyć do pobierania informacji o spisie z różnych źródeł (w tym źródeł w chmurze takich jak Azure) do *dynamiczny spis*. W tym artykule używasz [usługi Azure Cloud Shell](./ansible-run-playbook-in-cloudshell.md) konfigurowanie rozwiązania Ansible Azure dynamiczne spisu w której opisano tworzenie dwóch maszyn wirtualnych, tag jednej z tych maszyn wirtualnych i zainstalować rozwiązanie Nginx otagowane maszyny wirtualnej.

## <a name="prerequisites"></a>Wymagania wstępne

- **Subskrypcja Azure** — jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- **Poświadczenia platformy Azure** - [Azure Utwórz poświadczenia i konfigurowanie rozwiązania Ansible](/azure/virtual-machines/linux/ansible-install-configure#create-azure-credentials)

## <a name="create-the-test-virtual-machines"></a>Tworzenie testowych maszyn wirtualnych

1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Otwórz usługę [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview).

1. Utwórz grupę zasobów platformy Azure do przechowywania maszyn wirtualnych na potrzeby tego samouczka.

    > [!IMPORTANT]  
    > Grupa zasobów platformy Azure, który zostanie utworzony w tym kroku musi mieć nazwę, która jest całkowicie małe. W przeciwnym razie Generowanie dynamiczny spis zakończy się niepowodzeniem.

    ```azurecli-interactive
    az group create --resource-group ansible-inventory-test-rg --location eastus
    ```

1. Utwórz dwie maszyny wirtualne systemu Linux na platformie Azure przy użyciu jednej z następujących technik:

    - **Element playbook rozwiązania Ansible** — artykuł [Tworzenie podstawowych maszyny wirtualnej na platformie Azure za pomocą rozwiązania Ansible](/azure/virtual-machines/linux/ansible-create-vm) pokazano, jak utworzyć maszynę wirtualną z element playbook rozwiązania Ansible. Jeśli używasz elementu playbook zdefiniować jedną lub obie maszyny wirtualne, upewnij się, że połączenie SSH jest używany zamiast hasła.

    - **Interfejs wiersza polecenia Azure** — problem, każde z następujących poleceń w usłudze Cloud Shell Utwórz dwie maszyny wirtualne:

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

## <a name="tag-a-virtual-machine"></a>Tagowanie maszyny wirtualnej
Możesz [organizowania zasobów platformy Azure za pomocą tagów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#azure-cli) według kategorii zdefiniowanych przez użytkownika. 

Wprowadź następujące [az tag zasobu](/cli/azure/resource?view=azure-cli-latest.md#az-resource-tag) polecenie, aby oznaczyć maszynę wirtualną `ansible-inventory-test-vm1` z kluczem `nginx`:

```azurecli-interactive
az resource tag --tags nginx --id /subscriptions/<YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```

## <a name="generate-a-dynamic-inventory"></a>Generowanie dynamiczny spis
Po utworzeniu maszyn wirtualnych zdefiniowanych i oznakowane, nadszedł czas na generowanie dynamiczny spis. Rozwiązanie Ansible zawiera skrypt w języku Python o nazwie [azure_rm.py](https://github.com/ansible/ansible/blob/devel/contrib/inventory/azure_rm.py) generujący dynamiczny spis zasobów platformy Azure, wprowadzając żądań interfejsu API do usługi Azure Resource Manager. W poniższych krokach objaśniono możesz za pośrednictwem przy użyciu `azure_rm.py` skrypt, aby połączyć się z dwóch testów maszyn wirtualnych platformy Azure:

1. Użyj GNU `wget` polecenie, aby pobrać `azure_rm.py` skryptu:

    ```azurecli-interactive
    wget https://raw.githubusercontent.com/ansible/ansible/devel/contrib/inventory/azure_rm.py
    ```

1. Użyj `chmod` polecenie, aby zmienić uprawnienia dostępu do `azure_rm.py` skryptu. Następujące polecenie używa `+x` parametr umożliwia wykonywanie (uruchomione) określonego pliku (`azure_rm.py`):

    ```azurecli-interactive
    chmod +x azure_rm.py
    ```

1. Użyj [polecenia ansible](https://docs.ansible.com/ansible/2.4/ansible.html) połączyć się z grupy zasobów: 

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

## <a name="enable-the-virtual-machine-tag"></a>Włącz tag maszyny wirtualnej
Po ustawieniu żądanego tagu, konieczne jest tag "Włącz". Jednym ze sposobów, aby umożliwić znacznik, eksportując tag do zmiennej środowiskowej nosi nazwę `AZURE_TAGS` za pośrednictwem **wyeksportować** polecenia:

```azurecli-interactive
export AZURE_TAGS=nginx
```

Po tagu została wyeksportowana, możesz spróbować `ansible` ponownie polecenie:

```azurecli-interactive
ansible -i azure_rm.py ansible-inventory-test-rg -m ping 
```

Pojawi się tylko jedna maszyna wirtualna (jeden tag, którego pasuje do wartości eksportowane do **AZURE_TAGS** zmienną środowiskową):

```Output
ansible-inventory-test-vm1 | SUCCESS => {
    "changed": false,
    "failed": false,
    "ping": "pong"
}
```

## <a name="set-up-nginx-on-the-tagged-vm"></a>Konfigurowanie serwera Nginx na maszynie Wirtualnej oznakowane
Celem tagów jest umożliwiają szybko i łatwo pracować z podgrupami maszyn wirtualnych. Na przykład, załóżmy, że chcesz zainstalować rozwiązanie Nginx tylko w przypadku maszyn wirtualnych, do których zostało przypisane tag `nginx`. Poniższe kroki pokazują, jak łatwo można to zrobić w celu wykonania:

1. Aby utworzyć plik (zawiera Podręcznik użytkownika) o nazwie `nginx.yml` w następujący sposób:

  ```azurecli-interactive
  vi nginx.yml
  ```

1. Wstaw następujący kod do nowo utworzonego `nginx.yml` pliku:

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

1. Uruchom `nginx.yml` elementu playbook:

    ```azurecli-interactive
    ansible-playbook -i azure_rm.py nginx.yml
    ```

1. Po uruchomieniu elementu playbook, zostaną wyświetlone wyniki podobne do następujących danych wyjściowych:

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

## <a name="test-nginx-installation"></a>Przetestuj instalację serwera Nginx
W tej sekcji przedstawiono jedna z technik do przetestowania, czy serwer Nginx jest zainstalowany na maszynie wirtualnej.

1. Użyj [az vm-— adresy ip](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-list-ip-addresses) polecenie, aby pobrać adres IP `ansible-inventory-test-vm1` maszyny wirtualnej. Zwrócona wartość (adres IP maszyny wirtualnej) następnie jest używany jako parametr do polecenia SSH można połączyć się z maszyną wirtualną.

    ```azurecli-interactive
    ssh `az vm list-ip-addresses \
    -n ansible-inventory-test-vm1 \
    --query [0].virtualMachine.network.publicIpAddresses[0].ipAddress -o tsv`
    ```

1. Po nawiązaniu połączenia do `ansible-inventory-test-vm1` maszyny wirtualnej, uruchom [nginx - v](https://nginx.org/en/docs/switches.html) polecenie, aby określić, czy jest zainstalowany serwer Nginx.

    ```azurecli-interactive
    nginx -v
    ```

1. Po uruchomieniu `nginx -v` polecenia, zobacz wersję serwera Nginx (drugi wiersz) oznacza to, że zainstalowano serwer Nginx.

    ```Output
    tom@ansible-inventory-test-vm1:~$ nginx -v

    nginx version: nginx/1.10.3 (Ubuntu)
    
    tom@ansible-inventory-test-vm1:~$
    ```

1. Naciśnij klawisz  **&lt;Ctrl > D** za pomocą klawiatury w połączeniu, aby rozłączyć sesję SSH.

1. Wykonywanie kroku kroków dla `ansible-inventory-test-vm2` maszyny wirtualnej daje komunikat informacyjny wskazujący, gdzie można uzyskać serwera Nginx (co oznacza, że nie jest zainstalowany na tym etapie):

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
> [Tworzenie podstawowego maszyny wirtualnej na platformie Azure za pomocą rozwiązania Ansible](/azure/virtual-machines/linux/ansible-create-vm)
