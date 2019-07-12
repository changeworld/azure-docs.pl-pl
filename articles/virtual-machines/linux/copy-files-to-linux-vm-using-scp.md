---
title: Przenieś pliki do i z maszyn wirtualnych systemu Linux platformy Azure przy użyciu punktu połączenia usługi | Dokumentacja firmy Microsoft
description: Bezpieczne przenoszenie plików do i z maszyny Wirtualnej z systemem Linux na platformie Azure przy użyciu punktu połączenia usługi i pary kluczy SSH.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: dlepow
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: danlep
ms.subservice: disks
ms.openlocfilehash: 13a2c889ac648e2847d1cc58a60a7b1c0f1fc1e2
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671651"
---
# <a name="move-files-to-and-from-a-linux-vm-using-scp"></a>Przenieś pliki do i z maszyny Wirtualnej systemu Linux przy użyciu punktu połączenia usługi

W tym artykule przedstawiono sposób Przenieś pliki z stację roboczą do maszyny Wirtualnej systemu Linux na platformie Azure lub Maszynie wirtualnej systemu Linux platformy Azure do stacji roboczej, za pomocą Secure Copy (SCP). Przenoszenie plików między tą stacją roboczą a Maszynę wirtualną systemu Linux, szybko i bezpiecznie, jest krytyczny dla zarządzania infrastrukturą platformy Azure. 

W tym artykule potrzebne Linux maszyny Wirtualnej wdrożonej na platformie Azure przy użyciu [SSH pliki publicznych i prywatnych kluczy](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Należy również klientem SCP dla komputera lokalnego. Jest zbudowany na podstawie protokołu SSH i zawarte w domyślnej powłoki Bash większość komputerów z systemami Linux i Mac oraz niektóre powłoki Windows.

## <a name="quick-commands"></a>Szybkie polecenia

Skopiuj plik do maszyny Wirtualnej systemu Linux

```bash
scp file azureuser@azurehost:directory/targetfile
```

Skopiuj plik z maszyny Wirtualnej systemu Linux

```bash
scp azureuser@azurehost:directory/file targetfile
```

## <a name="detailed-walkthrough"></a>Szczegółowy przewodnik

Jako przykład możemy przenieść plik konfiguracji platformy Azure do maszyny Wirtualnej systemu Linux i ściągnąć katalog pliku dziennika, zarówno za pomocą kluczy punkt połączenia usługi i SSH.   

## <a name="ssh-key-pair-authentication"></a>Uwierzytelnianie pary kluczy SSH

Punkt połączenia usługi używa protokołu SSH dla warstwy transportowej. SSH obsługuje uwierzytelnianie na hoście docelowym i przenosi plik szyfrowany tunel domyślnie dostępne przy użyciu protokołu SSH. W przypadku uwierzytelniania SSH można się nazwy użytkowników i hasła. Jednak najlepszym rozwiązaniem bezpieczeństwa zaleca się publicznego i prywatnego klucza uwierzytelniania SSH. Po uwierzytelnieniu połączenia SSH SCP następnie rozpoczyna się kopiowanie pliku. Za pomocą prawidłowo skonfigurowane `~/.ssh/config` i publicznych i prywatnych kluczy SSH, połączenie punkt połączenia usługi można nawiązać, po prostu przy użyciu nazwy serwera (lub adres IP). Jeśli masz tylko jeden klucz SSH, punkt połączenia usługi szuka go w `~/.ssh/` katalogu i używa domyślnie, aby zalogować się do maszyny Wirtualnej.

Aby uzyskać więcej informacji na temat konfigurowania usługi `~/.ssh/config` i publicznych i prywatnych kluczy SSH, zobacz [tworzenie kluczy SSH](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="scp-a-file-to-a-linux-vm"></a>Punkt połączenia usługi plików do maszyny Wirtualnej z systemem Linux

Pierwszy przykład: Firma Microsoft skopiuj plik konfiguracji platformy Azure do maszyny Wirtualnej systemu Linux, który jest używany do wdrażania usługi automation. Ponieważ ten plik zawiera poświadczenia interfejsu API platformy Azure, między innymi wpisami tajnymi, ważne jest zabezpieczeń. Szyfrowany tunel dostarczane za pośrednictwem protokołu SSH chroni zawartość pliku.

Następujące polecenie kopiuje lokalnej *.azure/config* pliku na Maszynie wirtualnej platformy Azure z wykorzystaniem nazwy FQDN *myserver.eastus.cloudapp.azure.com*. Nazwa użytkownika administratora maszyny wirtualnej platformy Azure jest *azureuser*. Plik jest przeznaczona do */home/azureuser/* katalogu. Zastąp wartości w tym poleceniu.

```bash
scp ~/.azure/config azureuser@myserver.eastus.cloudapp.com:/home/azureuser/config
```

## <a name="scp-a-directory-from-a-linux-vm"></a>Punkt połączenia usługi katalogu z maszyny Wirtualnej z systemem Linux

W tym przykładzie firma Microsoft Skopiuj katalog plików dziennika z maszyny Wirtualnej systemu Linux w dół do stacji roboczej. Plik dziennika może lub nie może zawierać dane poufne lub klucza tajnego. Jednak przy użyciu punktu połączenia usługi gwarantuje, że zawartość plików dziennika są szyfrowane. Przy użyciu punktu połączenia usługi do transferu plików jest najprostszym sposobem można pobrać katalogu dziennika i plików w dół do stacji roboczej równocześnie również jest bezpieczne.

Następujące polecenie kopiuje pliki w */home/azureuser/dzienniki/* katalog na maszynie Wirtualnej platformy Azure w katalogu lokalnym folderze/tmp:

```bash
scp -r azureuser@myserver.eastus.cloudapp.com:/home/azureuser/logs/. /tmp/
```

`-r` Flaga interfejsu wiersza polecenia powoduje, że punkt połączenia usługi do skopiowania cyklicznie, pliki i katalogi z punktu katalogu wymienione w poleceniu.  Należy również zauważyć, że składnia wiersza polecenia jest podobny do `cp` skopiuj polecenie.

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie użytkownikami, SSH i sprawdzanie lub napraw dyski na maszynach wirtualnych systemu Linux platformy Azure przy użyciu rozszerzenia VMAccess](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
