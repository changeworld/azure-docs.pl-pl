---
title: Przenoszenie plików do i z maszyn wirtualnych systemu Linux platformy Azure z punktu połączenia usługi | Dokumentacja firmy Microsoft
description: Bezpiecznie przenieść plików do i z maszyny Wirtualnej systemu Linux na platformie Azure przy użyciu połączenia usługi i parę kluczy SSH.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: dlepow
manager: jeconnoc
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
ms.openlocfilehash: 0231e402848e617a46ca70470ba4d3272ace59f7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
ms.locfileid: "30904509"
---
# <a name="move-files-to-and-from-a-linux-vm-using-scp"></a>Przenoszenie plików do i z maszyny Wirtualnej systemu Linux przy użyciu połączenia usługi

W tym artykule przedstawiono sposób przenoszenia plików z stację roboczą do maszyny Wirtualnej systemu Linux na platformie Azure lub systemu Linux maszyny Wirtualnej platformy Azure do stacji roboczej za pomocą bezpiecznego kopiowania (SCP). Przenoszenie plików między tą stacją roboczą a Maszynę wirtualną systemu Linux, szybkie i bezpieczne, jest szczególnie ważne dla zarządzania infrastrukturą platformy Azure. 

W tym artykule należy Linux maszyn wirtualnych wdrożonych w Azure przy użyciu [SSH publiczne i prywatne pliki klucza](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Należy również połączenia klienta dla komputera lokalnego. Jest oparty na SSH i zawarte w domyślnej powłoki Bash większość komputerów z systemami Linux i komputerów Mac i niektóre powłoki systemu Windows.

## <a name="quick-commands"></a>Szybkie polecenia

Skopiuj plik do maszyny Wirtualnej systemu Linux

```bash
scp file azureuser@azurehost:directory/targetfile
```

Skopiuj plik w dół z maszyny Wirtualnej systemu Linux

```bash
scp azureuser@azurehost:directory/file targetfile
```

## <a name="detailed-walkthrough"></a>Szczegółowy przewodnik

Jako przykład możemy przenieść pliku konfiguracji platformy Azure do maszyny Wirtualnej systemu Linux i rozwijana katalog pliku dziennika, zarówno przy użyciu kluczy punkt połączenia usługi oraz SSH.   

## <a name="ssh-key-pair-authentication"></a>Uwierzytelnianie pary kluczy SSH

Punkt połączenia usługi używa SSH dla warstwy transportowej. SSH obsługuje uwierzytelnianie na hoście docelowym i przenosi plik w tunelu zaszyfrowanych domyślnie dostępne przy użyciu protokołu SSH. W przypadku uwierzytelniania SSH można użyć nazwy użytkowników i hasła. Jednak najlepszym rozwiązaniem bezpieczeństwa zaleca się SSH publicznego i prywatnego klucza uwierzytelniania. Po uwierzytelnieniu połączenie SSH SCP następnie rozpoczyna proces kopiowania pliku. Przy użyciu poprawnego skonfigurowania `~/.ssh/config` i publicznymi i prywatnymi kluczy SSH, połączenie punkt połączenia usługi można nawiązać za pomocą tylko nazwa serwera (lub adres IP). Jeśli masz tylko jeden klucz SSH, punkt połączenia usługi szuka go w `~/.ssh/` katalogu i jego domyślnie używa do logowania do maszyny Wirtualnej.

Aby uzyskać więcej informacji na temat konfigurowania sieci `~/.ssh/config` i kluczy publicznych i prywatnych SSH, zobacz [utworzyć SSH klucze](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="scp-a-file-to-a-linux-vm"></a>Punkt połączenia usługi plików na Maszynę wirtualną systemu Linux

Na przykład pierwszy firma Microsoft skopiuj plik konfiguracji platformy Azure do maszyny Wirtualnej systemu Linux, który służy do wdrażania automatyzacji. Ponieważ ten plik zawiera interfejsu API Azure poświadczenia, których klucze tajne, ważne jest zabezpieczeń. Szyfrowany tunel podał SSH chroni zawartość pliku.

Polecenie kopiuje lokalnej *.azure/config* pliku na maszynie Wirtualnej platformy Azure w pełni kwalifikowaną nazwą domeny *myserver.eastus.cloudapp.azure.com*. Nazwa użytkownika administratora na maszynie Wirtualnej Azure jest *azureuser*. Plik jest skierowany do */home/azureuser/* katalogu. Zastąp wartości w tym poleceniu.

```bash
scp ~/.azure/config azureuser@myserver.eastus.cloudapp.com:/home/azureuser/config
```

## <a name="scp-a-directory-from-a-linux-vm"></a>Punkt połączenia usługi katalogu z maszyny Wirtualnej systemu Linux

Na przykład firma Microsoft Skopiuj katalog plików dziennika z maszyny Wirtualnej systemu Linux do stacji roboczej. Plik dziennika może lub nie mogą zawierać poufne dane. Jednak przy użyciu SCP gwarantuje, że zawartość plików dziennika są szyfrowane. Do transferu plików przy użyciu połączenia usługi jest najprostszym sposobem podczas będące również bezpiecznego występuje katalog dziennika i plików do stacji roboczej.

Polecenie kopiuje pliki w */home/azureuser/dzienniki/* katalogu na maszynie Wirtualnej Azure do lokalnego tmp:

```bash
scp -r azureuser@myserver.eastus.cloudapp.com:/home/azureuser/logs/. /tmp/
```

`-r` Flagi interfejsu wiersza polecenia powoduje, że punkt połączenia usługi do kopiowania rekursywnie pliki i katalogi z katalogu punktu wymienionych w poleceniu.  Także zauważyć, że składnia wiersza polecenia jest podobny do `cp` skopiować polecenia.

## <a name="next-steps"></a>Kolejne kroki

* [Zarządzanie użytkownikami, SSH i wyboru lub napraw dyski na maszynach wirtualnych systemu Linux platformy Azure przy użyciu rozszerzenia VMAccess](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)