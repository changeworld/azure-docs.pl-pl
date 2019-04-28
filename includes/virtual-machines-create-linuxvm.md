---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: ae29451e3f7ec263f296e69656a5c66045334687
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61126723"
---
1. Zaloguj się do subskrypcji platformy Azure, wykonując kroki opisane w [Połącz z platformą Azure z platformy Azure klasyczny interfejs wiersza polecenia](/cli/azure/authenticate-azure-cli).

2. Upewnij się, że pracujesz w trybie klasycznym wdrożenia, w następujący sposób:

    ```azurecli
    azure config mode asm
    ```

3. Wśród dostępnych obrazów wyszukaj obraz systemu Linux do załadowania w następujący sposób:

   ```azurecli   
    azure vm image list | grep "Linux"
    ```
   
    W oknie wiersza polecenia systemu Windows użyj polecenia **find** zamiast grep.
   
4. Użyj instrukcji `azure vm create`, aby utworzyć maszynę wirtualną przy użyciu obrazu systemu Linux z poprzedniej listy. Ten krok powoduje utworzenie konta magazynu i usługi w chmurze. Można także połączyć tę maszynę wirtualną z istniejącą usługą w chmurze przy użyciu opcji `-c`. Utwórz punkt końcowy SSH, aby zalogować się do maszyny wirtualnej z systemem Linux przy użyciu opcji `-e`. W poniższym przykładzie jest tworzona maszyna wirtualna o nazwie `myVM` przy użyciu obrazu `Ubuntu-14_04_4-LTS` w lokalizacji `West US`, a następnie zostaje dodana nazwa użytkownika `ops`:
   
    ```azurecli
    azure vm create myVM \
        b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB \
        -g ops -p P@ssw0rd! -z "Small" -e -l "West US"
    ```

    Dane wyjściowe są podobne do poniższego przykładu:

    ```azurecli
    info:    Executing command vm create
    + Looking up image b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB
    + Looking up cloud service
    info:    cloud service myVM not found.
    + Creating cloud service
    + Retrieving storage accounts
    + Creating VM
    info:    vm create command OK
    ```
   
   > [!NOTE]
   > W przypadku maszyny wirtualnej z systemem Linux musisz użyć opcji `-e` w instrukcji `vm create`. Po utworzeniu maszyny wirtualnej nie można włączyć protokołu SSH. Aby uzyskać dalsze szczegółowe informacje dotyczące protokołu SSH, zapoznaj się z tematem [How to Use SSH with Linux on Azure](../articles/virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Jak korzystać z protokołu SSH w systemie Linux na platformie Azure).

5. Atrybuty maszyny wirtualnej można sprawdzić za pomocą polecenia `azure vm show`. Poniższy przykład zawiera listę informacji dotyczących maszyny wirtualnej o nazwie `myVM`:

    ```azurecli   
    azure vm show myVM
    ```

6. Uruchom maszynę wirtualną przy użyciu polecenia `azure vm start` w następujący sposób:

    ```azurecli
    azure vm start myVM
    ```

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać szczegółowe informacje dotyczące wszystkich tych poleceń platformy Azure klasycznego interfejsu wiersza polecenia maszyny wirtualnej, przeczytaj [przy użyciu klasycznego wiersza polecenia platformy Azure przy użyciu wdrażania klasycznego interfejsu API](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).

