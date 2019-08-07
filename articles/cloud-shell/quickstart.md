---
title: Azure Cloud Shell Szybki Start | Microsoft Docs
description: Szybki Start dla Azure Cloud Shell
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
ms.openlocfilehash: 8151013f263c6cf2f90e89fa1c3b0b3025f2ea38
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2019
ms.locfileid: "68741982"
---
# <a name="quickstart-for-bash-in-azure-cloud-shell"></a>Przewodnik Szybki Start dla usługi Bash w Azure Cloud Shell

Ten dokument zawiera szczegółowe informacje dotyczące używania bash w Azure Cloud Shell w [Azure Portal](https://ms.portal.azure.com/).

> [!NOTE]
> Dostępny jest również program [PowerShell w Azure Cloud Shell](quickstart-powershell.md) Szybki Start.

## <a name="start-cloud-shell"></a>Rozpocznij Cloud Shell
1. Uruchom **Cloud Shell** od górnego nawigowania Azure Portal. <br>
![](media/quickstart/shell-icon.png)

2. Wybierz subskrypcję, aby utworzyć konto magazynu i udział plików Microsoft Azure.
3. Wybierz pozycję "Utwórz magazyn"

> [!TIP]
> Użytkownik jest automatycznie uwierzytelniany dla interfejsu wiersza polecenia platformy Azure w każdej sesji.

### <a name="select-the-bash-environment"></a>Wybierz środowisko bash
Upewnij się, że lista rozwijana środowiska z lewej strony okna `Bash`powłoki ma wartość. <br>
![](media/quickstart/env-selector.png)

### <a name="set-your-subscription"></a>Ustawianie subskrypcji
1. Lista subskrypcji, do których masz dostęp.
   ```azurecli-interactive
   az account list
   ```

2. Ustaw preferowaną subskrypcję: <br>
```azurecli-interactive
az account set --subscription 'my-subscription-name'
```

> [!TIP]
> Twoja subskrypcja zostanie zapamiętana na potrzeby przyszłych sesji przy `/home/<user>/.azure/azureProfile.json`użyciu usługi.

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
Utwórz nową grupę zasobów w zachodnim regionie o nazwie "Mojagz".
```azurecli-interactive
az group create --location westus --name MyRG
```

### <a name="create-a-linux-vm"></a>Utwórz maszynę wirtualną z systemem Linux
Utwórz maszynę wirtualną Ubuntu w nowej grupie zasobów. Interfejs wiersza polecenia platformy Azure utworzy klucze SSH i skonfiguruje dla nich maszynę wirtualną. <br>

```azurecli-interactive
az vm create -n myVM -g MyRG --image UbuntuLTS --generate-ssh-keys
```

> [!NOTE]
> Użycie `--generate-ssh-keys` powoduje, że interfejs wiersza polecenia platformy Azure tworzy i konfiguruje klucze publiczne i prywatne w `$Home` maszynie wirtualnej i katalogu. Klucze domyślne są umieszczane w Cloud Shell `/home/<user>/.ssh/id_rsa` w i. `/home/<user>/.ssh/id_rsa.pub` Folder jest utrwalany w dołączonym obrazie 5 GB udziału plików, który będzie używany do utrwalania `$Home`. `.ssh`

Nazwa użytkownika na tej maszynie wirtualnej będzie używana w Cloud Shell ($User@Azure:).

### <a name="ssh-into-your-linux-vm"></a>Użyj protokołu SSH na maszynie wirtualnej z systemem Linux
1. Wyszukaj nazwę maszyny wirtualnej na pasku wyszukiwania Azure Portal.
2. Kliknij przycisk "Połącz", aby uzyskać nazwę i publiczny adres IP maszyny wirtualnej. <br>
   ![](media/quickstart/sshcmd-copy.png)

3. Za pomocą `ssh` polecenia SSH do maszyny wirtualnej.
   ```
   ssh username@ipaddress
   ```

Po ustanowieniu połączenia SSH powinien zostać wyświetlony monit Ubuntu powitalny. <br>
![](media/quickstart/ubuntu-welcome.png)

## <a name="cleaning-up"></a>Czyszczenie 
1. Zakończ sesję SSH.
   ```azurecli-interactive
   exit
   ```

2. Usuń grupę zasobów i wszystkie znajdujące się w niej zasoby.
   ```azurecli-interactive
   az group delete -n MyRG
   ```

## <a name="next-steps"></a>Następne kroki
[Dowiedz się więcej o utrwalaniu plików dla bash w Cloud Shell](persisting-shell-storage.md) <br>
[Informacje o interfejsie wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/) <br>
[Dowiedz się więcej o usłudze Azure Files Storage](../storage/files/storage-files-introduction.md) <br>
