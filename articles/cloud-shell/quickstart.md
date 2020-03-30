---
title: Szybki start usługi Azure Cloud Shell — bash
description: Dowiedz się, jak używać wiersza polecenia Bash w przeglądarce za pomocą usługi Azure Cloud Shell.
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 03/12/2018
ms.author: damaerte
ms.openlocfilehash: 58d795a5aee79e4149864a79a923ce34950b31d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79458073"
---
# <a name="quickstart-for-bash-in-azure-cloud-shell"></a>Szybki start dla bash w usłudze Azure Cloud Shell

W tym dokumencie opisano, jak używać bash w usłudze Azure Cloud Shell w [witrynie Azure portal.](https://ms.portal.azure.com/)

> [!NOTE]
> Program [PowerShell w usłudze Azure Cloud Shell](quickstart-powershell.md) Szybki start jest również dostępny.

## <a name="start-cloud-shell"></a>Uruchamianie usługi Cloud Shell
1. Uruchom **powłokę w chmurze** z górnej nawigacji w witrynie Azure portal. <br>
![](media/quickstart/shell-icon.png)

2. Wybierz subskrypcję, aby utworzyć konto magazynu i udział plików Microsoft Azure.
3. Wybierz "Utwórz magazyn"

> [!TIP]
> Użytkownik jest automatycznie uwierzytelniony dla interfejsu wiersza polecenia platformy Azure w każdej sesji.

### <a name="select-the-bash-environment"></a>Wybierz środowisko Bash
Sprawdź, czy okno rozwijane środowiska z lewej strony `Bash`okna powłoki mówi . <br>
![](media/quickstart/env-selector.png)

### <a name="set-your-subscription"></a>Ustawianie subskrypcji
1. Wyświetl listę subskrypcji, do których masz dostęp.
   ```azurecli-interactive
   az account list
   ```

2. Ustaw preferowaną subskrypcję: <br>
```azurecli-interactive
az account set --subscription 'my-subscription-name'
```

> [!TIP]
> Twoja subskrypcja zostanie zapamiętana `/home/<user>/.azure/azureProfile.json`dla przyszłych sesji przy użyciu .

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
Utwórz nową grupę zasobów w u. WestUS o nazwie "MyRG".
```azurecli-interactive
az group create --location westus --name MyRG
```

### <a name="create-a-linux-vm"></a>Tworzenie maszyny wirtualnej z systemem Linux
Utwórz maszynę wirtualną Ubuntu w nowej grupie zasobów. Narzędzie wiersza polecenia platformy Azure utworzy klucze SSH i skonfiguruje maszynę wirtualną z nimi. <br>

```azurecli-interactive
az vm create -n myVM -g MyRG --image UbuntuLTS --generate-ssh-keys
```

> [!NOTE]
> Za `--generate-ssh-keys` pomocą polecenia platformy Azure do tworzenia i konfigurowania `$Home` kluczy publicznych i prywatnych w maszynie wirtualnej i katalogu. Domyślnie klucze są umieszczane w usłudze Cloud Shell w punkcie `/home/<user>/.ssh/id_rsa` i `/home/<user>/.ssh/id_rsa.pub`. Folder `.ssh` jest utrwalony w załączonym 5 GB obrazie używanym do utrwalania. `$Home`

Twoja nazwa użytkownika na tej maszynie wirtualnejUser@Azure:będzie twoją nazwą użytkownika używaną w usłudze Cloud Shell ($ ).

### <a name="ssh-into-your-linux-vm"></a>SSH do maszyny Wirtualnej z systemem Linux
1. Wyszukaj nazwę maszyny Wirtualnej na pasku wyszukiwania witryny Azure portal.
2. Kliknij "Połącz", aby uzyskać nazwę maszyny Wirtualnej i publiczny adres IP. <br>
   ![](media/quickstart/sshcmd-copy.png)

3. SSH do maszyny Wirtualnej `ssh` z cmd.
   ```
   ssh username@ipaddress
   ```

Po ustanowieniu połączenia SSH powinien zostać wyświetlony monit powitalny Ubuntu. <br>
![](media/quickstart/ubuntu-welcome.png)

## <a name="cleaning-up"></a>Czyszczenie 
1. Wyjdź z sesji ssh.
   ```
   exit
   ```

2. Usuń grupę zasobów i wszystkie zasoby w niej.
   ```azurecli-interactive
   az group delete -n MyRG
   ```

## <a name="next-steps"></a>Następne kroki
[Dowiedz się więcej o utrwalaniu plików basha w usłudze Cloud Shell](persisting-shell-storage.md) <br>
[Dowiedz się więcej o platformie Azure CLI](https://docs.microsoft.com/cli/azure/) <br>
[Dowiedz się więcej o magazynie plików platformy Azure](../storage/files/storage-files-introduction.md) <br>
