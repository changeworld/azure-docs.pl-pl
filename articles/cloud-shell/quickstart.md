---
title: Powłoka bash w przewodniku Szybki Start usługi Azure Cloud Shell | Dokumentacja firmy Microsoft
description: Przewodnik Szybki Start dla programu Bash w usłudze Cloud Shell
services: ''
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: damaerte
ms.openlocfilehash: b8f96de7214a46c9e38182c141343a46c0e28139
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60199621"
---
# <a name="quickstart-for-bash-in-azure-cloud-shell"></a>Przewodnik Szybki Start dla programu Bash w usłudze Azure Cloud Shell

Ten dokument zawiera szczegółowe informacje dotyczące używania funkcji Bash w usłudze Azure Cloud Shell w [witryny Azure portal](https://ms.portal.azure.com/).

> [!NOTE]
> A [programu PowerShell w usłudze Azure Cloud Shell](quickstart-powershell.md) Szybki Start jest również dostępna.

## <a name="start-cloud-shell"></a>Uruchom usługę Cloud Shell
1. Uruchom **Cloud Shell** w górnym menu nawigacyjnym w witrynie Azure Portal. <br>
![](media/quickstart/shell-icon.png)

2. Wybierz subskrypcję, aby utworzyć konto magazynu i udostępnianie plików pakietu Microsoft Azure.
3. Wybierz pozycję "Utwórz magazyn"

> [!TIP]
> Wiersza polecenia platformy Azure są automatycznie uwierzytelniani w każdej sesji.

### <a name="select-the-bash-environment"></a>Wybierz środowisko powłoki Bash
Upewnij się, że środowisko listę rozwijaną z po lewej stronie okna powłoki mówi `Bash`. <br>
![](media/quickstart/env-selector.png)

### <a name="set-your-subscription"></a>Ustaw subskrypcję
1. Subskrypcji listy, do których masz dostęp do.
   ```azurecli-interactive
   az account list
   ```

2. Ustaw preferowany subskrypcji: <br>
```azurecli-interactive
az account set --subscription 'my-subscription-name'
```

> [!TIP]
> Twoja subskrypcja zostanie zapamiętane osobno dla przyszłych sesji przy użyciu `/home/<user>/.azure/azureProfile.json`.

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
Utwórz nową grupę zasobów w WestUS o nazwie "Mojagz".
```azurecli-interactive
az group create --location westus --name MyRG
```

### <a name="create-a-linux-vm"></a>Tworzenie maszyny wirtualnej z systemem Linux
Tworzenie maszyny Wirtualnej systemu Ubuntu w nowej grupie zasobów. Interfejs wiersza polecenia platformy Azure spowoduje tworzenie kluczy SSH i konfigurowanie maszyny Wirtualnej z nimi. <br>

```azurecli-interactive
az vm create -n myVM -g MyRG --image UbuntuLTS --generate-ssh-keys
```

> [!NOTE]
> Za pomocą `--generate-ssh-keys` powoduje, że interfejs wiersza polecenia platformy Azure do tworzenia i konfigurowania kluczy publicznych i prywatnych na maszynie wirtualnej i `$Home` katalogu. Domyślnie klucze są umieszczane w usłudze Cloud Shell w `/home/<user>/.ssh/id_rsa` i `/home/<user>/.ssh/id_rsa.pub`. Twoje `.ssh` folderu są utrwalane w obrazie 5 GB swój udział załączonego pliku użytą do utrwalenia `$Home`.

Nazwa użytkownika na tej maszynie Wirtualnej będzie swoją nazwę użytkownika używane w usłudze Cloud Shell ($User@Azure:).

### <a name="ssh-into-your-linux-vm"></a>SSH z maszyną wirtualną systemu Linux
1. Wyszukaj nazwę maszyny Wirtualnej na pasku usługi Azure search w portalu.
2. Kliknij przycisk "Połącz", aby uzyskać swoją nazwę maszyny Wirtualnej i publicznego adresu IP. <br>
   ![](media/quickstart/sshcmd-copy.png)

3. SSH z maszyną wirtualną za pomocą `ssh` cmd.
   ```
   ssh username@ipaddress
   ```

Po ustanowieniu połączenia SSH, powinien zostać wyświetlony monit powitalnej Ubuntu. <br>
![](media/quickstart/ubuntu-welcome.png)

## <a name="cleaning-up"></a>Czyszczenie 
1. Wyjście usługi ssh sesji.
   ```azurecli-interactive
   exit
   ```

2. Usunięcie grupy zasobów i wszystkie zasoby w niej.
   ```azurecli-interactive
   az group delete -n MyRG
   ```

## <a name="next-steps"></a>Kolejne kroki
[Dowiedz się więcej o utrwalanie plików w programie Bash w usłudze Cloud Shell](persisting-shell-storage.md) <br>
[Dowiedz się więcej o wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/) <br>
[Dowiedz się więcej o usłudze Azure Files storage](../storage/files/storage-files-introduction.md) <br>
