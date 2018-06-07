---
title: Bash w powłoki w chmurze Azure — Szybki Start | Dokumentacja firmy Microsoft
description: Szybki Start dla Bash w powłoce chmury
services: ''
documentationcenter: ''
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: juluk
ms.openlocfilehash: 4b7e4302bba2efed12e19043da1f592bed12a2fd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34608886"
---
# <a name="quickstart-for-bash-in-azure-cloud-shell"></a>Szybki Start dla Bash w chmurze Azure powłoki

Ten dokument zawiera szczegóły dotyczące używania Bash w powłoce chmury Azure w [portalu Azure](https://ms.portal.azure.com/).

> [!NOTE]
> A [programu PowerShell w powłoce chmury Azure](quickstart-powershell.md) szybkiego startu jest również dostępna.

## <a name="start-cloud-shell"></a>Uruchom powłokę chmury
1. Uruchom **powłoki chmury** w górnym menu nawigacyjnym portalu Azure. <br>
![](media/quickstart/shell-icon.png)

2. Wybierz subskrypcję, aby utworzyć konto magazynu i udostępniać pliki programu Microsoft Azure.
3. Wybierz opcję "Utwórz magazyn"

> [!TIP]
> Azure CLI 2.0 są automatycznie uwierzytelniani w każdej sesji.

### <a name="select-the-bash-environment"></a>Wybierz środowisko Bash
Sprawdź, czy środowisko listy rozwijanej z lewej strony okna powłoki mówi `Bash`. <br>
![](media/quickstart/env-selector.png)

### <a name="set-your-subscription"></a>Ustaw swoją subskrypcję
1. Subskrypcje listy, do których masz dostęp do.
```azurecli-interactive
az account list
```

2. Ustaw preferowany subskrypcji: <br>
```azurecli-interactive
az account set --subscription my-subscription-name`
```

> [!TIP]
> Subskrypcja zostanie zapamiętany dla przyszłych sesji przy użyciu `/home/<user>/.azure/azureProfile.json`.

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
Utwórz nową grupę zasobów w WestUS o nazwie "MyRG".
```azurecli-interactive
az group create --location westus --name MyRG
```

### <a name="create-a-linux-vm"></a>Tworzenie maszyny wirtualnej z systemem Linux
Tworzenie maszyny Wirtualnej systemu Ubuntu w nowej grupy zasobów. Azure CLI 2.0 tworzenie kluczy SSH, a następnie skonfigurowanie maszyny Wirtualnej z nimi. <br>

```azurecli-interactive
az vm create -n myVM -g MyRG --image UbuntuLTS --generate-ssh-keys
```

> [!NOTE]
> Przy użyciu `--generate-ssh-keys` nakazuje 2.0 interfejsu wiersza polecenia Azure, aby utworzyć i skonfigurować klucze publiczne i prywatne w maszynie Wirtualnej i `$Home` katalogu. Domyślnie klucze są umieszczane w chmurze powłoki w `/home/<user>/.ssh/id_rsa` i `/home/<user>/.ssh/id_rsa.pub`. Twoje `.ssh` folder jest utrwalona w obrazie 5 GB na udział załączonego pliku użytą do utrwalenia `$Home`.

Nazwa użytkownika na tej maszynie Wirtualnej zostanie nazwy użytkownika używane w chmurze powłoki ($User@Azure:).

### <a name="ssh-into-your-linux-vm"></a>SSH do maszyny Wirtualnej systemu Linux
1. Wyszukaj nazwę maszyny Wirtualnej na pasku wyszukiwania portalu Azure.
2. Kliknij przycisk "Połącz", aby pobrać swoją nazwę maszyny Wirtualnej i publiczny adres IP. <br>
![](media/quickstart/sshcmd-copy.png)

3. SSH z maszyną wirtualną z `ssh` cmd.
```
ssh username@ipaddress
```

Podczas ustanawiania połączenia SSH, powinien zostać wyświetlony monit powitalnej Ubuntu. <br>
![](media/quickstart/ubuntu-welcome.png)

## <a name="cleaning-up"></a>Czyszczenie 
1. Zakończ użytkownika ssh sesji.
```azurecli-interactive
exit
```

2. Usunięcie grupy zasobów i wszystkie zasoby w niej.
```azurecli-interactive
az group delete -n MyRG
```

## <a name="next-steps"></a>Kolejne kroki
[Więcej informacji na temat plików utrwalanie dla Bash w powłoce chmury](persisting-shell-storage.md) <br>
[Więcej informacji na temat usługi Azure CLI 2.0](https://docs.microsoft.com/cli/azure/) <br>
[Dowiedz się więcej o magazyn plików Azure](../storage/files/storage-files-introduction.md) <br>
