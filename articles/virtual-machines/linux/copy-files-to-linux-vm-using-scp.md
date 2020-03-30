---
title: Przenoszenie plików do i z maszyn wirtualnych z systemem Azure Linux za pomocą protokołu SCP
description: Bezpiecznie przenieść pliki do i z maszyny Wirtualnej systemu Linux na platformie Azure przy użyciu protokołu SCP i pary kluczy SSH.
author: cynthn
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.topic: article
ms.date: 07/12/2017
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: a0837790b70de42073338bf085ee0f3976b866f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969602"
---
# <a name="move-files-to-and-from-a-linux-vm-using-scp"></a>Przenoszenie plików do i z maszyny Wirtualnej systemu Linux przy użyciu protokołu SCP

W tym artykule pokazano, jak przenieść pliki ze stacji roboczej do maszyny Wirtualnej systemu Azure Linux lub z maszyny wirtualnej z systemem Azure Linux w dół do stacji roboczej przy użyciu bezpiecznej kopii (SCP). Szybkie i bezpieczne przenoszenie plików między stacją roboczą a maszyną wirtualną z systemem Linux ma kluczowe znaczenie dla zarządzania infrastrukturą platformy Azure. 

W tym artykule potrzebna jest maszyna wirtualna z systemem Linux wdrożona na platformie Azure przy użyciu [plików kluczy publicznych i prywatnych SSH](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Potrzebny jest również klient SCP dla komputera lokalnego. Jest zbudowany na szczycie SSH i zawarte w domyślnej powłoki Bash większości komputerów Linux i Mac i niektóre powłoki Systemu Windows.

## <a name="quick-commands"></a>Szybkie polecenia

Kopiowanie pliku do maszyny Wirtualnej systemu Linux

```bash
scp file azureuser@azurehost:directory/targetfile
```

Kopiowanie pliku w dół z maszyny Wirtualnej systemu Linux

```bash
scp azureuser@azurehost:directory/file targetfile
```

## <a name="detailed-walkthrough"></a>Szczegółowy przewodnik

Jako przykłady przenosimy plik konfiguracji platformy Azure do maszyny Wirtualnej systemu Linux i ściągamy katalog plików dziennika, zarówno przy użyciu kluczy SCP, jak i SSH.   

## <a name="ssh-key-pair-authentication"></a>Uwierzytelnianie pary kluczy SSH

Protokół SCP używa protokołu SSH dla warstwy transportu. SSH obsługuje uwierzytelnianie na hoście docelowym i przenosi plik w zaszyfrowanym tunelu dostarczonym domyślnie z SSH. W przypadku uwierzytelniania SSH można używać nazw użytkowników i haseł. Jednak uwierzytelnianie za pomocą klucza publicznego i prywatnego SSH jest zalecane jako najlepsze rozwiązanie w zakresie zabezpieczeń. Po uwierzytelnieniu połączenia przez protokół SSH protokół SCP rozpoczyna kopiowanie pliku. Przy użyciu prawidłowo `~/.ssh/config` skonfigurowanych kluczy publicznych i prywatnych SSH połączenie SCP można nawiązać za pomocą nazwy serwera (lub adresu IP). Jeśli masz tylko jeden klucz SSH, protokół `~/.ssh/` SCP wyszukuje go w katalogu i domyślnie używa go do logowania się do maszyny Wirtualnej.

Aby uzyskać więcej informacji `~/.ssh/config` na temat konfigurowania kluczy publicznych i prywatnych ssh, zobacz [Tworzenie kluczy SSH](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="scp-a-file-to-a-linux-vm"></a>SCP plik do maszyny Wirtualnej systemu Linux

W pierwszym przykładzie możemy skopiować plik konfiguracji platformy Azure do maszyny Wirtualnej systemu Linux, który jest używany do wdrażania automatyzacji. Ponieważ ten plik zawiera poświadczenia interfejsu API platformy Azure, które zawierają wpisy tajne, bezpieczeństwo jest ważne. Zaszyfrowany tunel dostarczony przez SSH chroni zawartość pliku.

Następujące polecenie kopiuje lokalny plik *platformy Azure/config* do maszyny Wirtualnej platformy Azure z *myserver.eastus.cloudapp.azure.com*FQDN . Nazwa użytkownika administratora na maszynie Wirtualnej platformy Azure jest *azureuser*. Plik jest przeznaczony dla *katalogu /home/azureuser/.* Zastąp własne wartości w tym poleceniu.

```bash
scp ~/.azure/config azureuser@myserver.eastus.cloudapp.com:/home/azureuser/config
```

## <a name="scp-a-directory-from-a-linux-vm"></a>SCP katalog z maszyny Wirtualnej z systemem Linux

W tym przykładzie możemy skopiować katalog plików dziennika z maszyny Wirtualnej systemu Linux w dół do stacji roboczej. Plik dziennika może, ale nie może zawierać poufnych lub tajnych danych. Jednak za pomocą protokołu SCP zapewnia zawartość plików dziennika są szyfrowane. Korzystanie z protokołu SCP do przesyłania plików jest najprostszym sposobem, aby uzyskać katalog dziennika i pliki w dół do stacji roboczej, a jednocześnie jest bezpieczny.

Następujące polecenie kopiuje pliki w katalogu */home/azureuser/logs/* na maszynie Wirtualnej platformy Azure do lokalnego katalogu /tmp:

```bash
scp -r azureuser@myserver.eastus.cloudapp.com:/home/azureuser/logs/. /tmp/
```

Flaga `-r` nakazuje punktowi SCP cykliczne kopiowanie plików i katalogów z punktu katalogu wymienionego w poleceniu.  Należy również zauważyć, że składnia wiersza `cp` polecenia jest podobna do polecenia kopiowania.

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie użytkownikami, SSH oraz sprawdzanie lub naprawianie dysków na maszynach wirtualnych z systemem Azure z systemem Linux przy użyciu rozszerzenia VMAccess](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
