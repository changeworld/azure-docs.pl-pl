---
title: Magazyn plików Azure instalacji na maszynach wirtualnych systemu Linux przy użyciu protokołu SMB 1.0 interfejsu wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Jak zainstalować magazyn plików Azure na maszynach wirtualnych systemu Linux przy użyciu protokołu SMB
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/07/2016
ms.author: v-livech
ms.openlocfilehash: 442c08a03ff3eb8e4c86f8190e16b74744aa9dd3
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
ms.locfileid: "30904835"
---
# <a name="mount-azure-file-storage-on-linux-vms-by-using-smb-with-azure-cli-10"></a>Magazyn plików Azure instalacji na maszynach wirtualnych systemu Linux przy użyciu protokołu SMB 1.0 interfejsu wiersza polecenia platformy Azure

W tym artykule pokazano, jak zainstalować magazyn plików Azure na maszynie Wirtualnej systemu Linux przy użyciu protokołu bloku komunikatów serwera (SMB). Magazyn plików oferuje udziały plików w chmurze przy użyciu standardowego protokołu SMB. Wymagania są następujące:

* [Konto platformy Azure](https://azure.microsoft.com/pricing/free-trial/)
* [Secure Shell (SSH) publicznego i prywatnego klucza plików](mac-create-ssh-keys.md)

## <a name="cli-versions-to-use"></a>Wersje interfejsu wiersza polecenia do użycia
Zadanie można wykonać za pomocą jednej z następujących wersji interfejsu wiersza polecenia (CLI):

- [Azure CLI 1.0](#quick-commands) — nasze interfejsu wiersza polecenia dla klasycznego i zasobów zarządzania wdrażania modeli (w tym artykule)
- [Azure CLI 2.0](mount-azure-file-storage-on-linux-using-smb-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)-naszej nowej generacji interfejsu wiersza polecenia dla model wdrażania zasobów zarządzania


## <a name="quick-commands"></a>Szybkie polecenia
Szybko wykonywania tego zadania, wykonaj kroki opisane w tej sekcji. Aby uzyskać szczegółowe informacje i kontekstu, rozpoczęcie ["Szczegółowy przewodnik"](mount-azure-file-storage-on-linux-using-smb.md#detailed-walkthrough) sekcji.

### <a name="prerequisites"></a>Wymagania wstępne
* Grupy zasobów
* Sieć wirtualna platformy Azure
* Grupa zabezpieczeń sieci przy użyciu protokołu SSH dla ruchu przychodzącego
* Podsieci
* Konto magazynu platformy Azure
* Klucze konta magazynu platformy Azure
* Udział magazynu plików Azure
* A Linux VM

Przykładami należy zastąpić własnymi ustawieniami.

### <a name="create-a-directory-for-the-local-mount"></a>Utwórz katalog dla instalacji lokalnej

```bash
mkdir -p /mnt/mymountpoint
```

### <a name="mount-the-file-storage-smb-share-to-the-mount-point"></a>Zainstaluj magazyn plików udziału SMB do punktu instalacji

```bash
sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mymountpoint -o vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

### <a name="persist-the-mount-after-a-reboot"></a>Utrwalanie instalacji po ponownym uruchomieniu
Dodaj następujący wiersz do `/etc/fstab`:

```bash
//myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

## <a name="detailed-walkthrough"></a>Szczegółowy przewodnik

Magazyn plików oferuje udziały plików w chmurze, które używają standardowego protokołu SMB. Najnowsza wersja pliku magazynu można również zainstalować udział plików z dowolnego systemu operacyjnego, który obsługuje protokół SMB 3.0. Użycie instalacji SMB w systemie Linux, otrzymasz łatwe tworzenie kopii zapasowych niezawodne, stała archiwizacji lokalizacji magazynu obsługiwaną przez umowy dotyczącej poziomu usług.

Przenoszenie plików z maszyny Wirtualnej do instalacji SMB, który znajduje się na magazyn plików jest doskonałym sposobem dzienników debugowania. Wynika to z tego samego udziału SMB mogą być instalowane lokalnie na stację roboczą Mac, Linux lub Windows. Najlepszego rozwiązania do przesyłania strumieniowego systemu Linux nie jest SMB lub protokołu SMB nie są wbudowane do obsługi tych obowiązków duże rejestrowania w czasie rzeczywistym Dzienniki aplikacji. Narzędzia rejestrowania dedykowanych, ujednoliconej warstwy, takiego jak Fluentd będzie lepszym rozwiązaniem niż SMB zbierania systemu Linux i aplikacji, rejestrowania danych wyjściowych.

W ramach tego przewodnika szczegółowe możemy utworzyć wymagania wstępne niezbędne do utworzenia udziału plików magazynu i zainstalować go za pośrednictwem protokołu SMB na maszynie Wirtualnej systemu Linux.

1. Utwórz konto magazynu platformy Azure przy użyciu następującego kodu:

    ```azurecli
    azure storage account create myStorageAccount \
    --sku-name lrs \
    --kind storage \
    -l westus \
    -g myResourceGroup
    ```

2. Pokaż klucze konta magazynu.

    Podczas tworzenia konta magazynu, klucze konta są tworzone w pary, dzięki czemu można obracać bez przerw w działaniu usługi. Po przełączeniu do drugi klucz w parze, możesz utworzyć nową parę kluczy. Nowe klucze konta magazynu zawsze są tworzone w parach, sprawdzając, czy zawsze jest gotowe przełączyć się do co najmniej jeden klucz nieużywane magazynu. Aby wyświetlić klucze konta magazynu, należy użyć poniższego kodu:

    ```azurecli
    azure storage account keys list myStorageAccount \
    --resource-group myResourceGroup
    ```
3. Utwórz udział magazynu plików.

    Udział magazynu plików zawiera udziału SMB. Limit przydziału zawsze jest wyrażona w gigabajtów (GB). Aby utworzyć udział magazynu plików, użyj następującego kodu:

    ```azurecli
    azure storage share create mystorageshare \
    --quota 10 \
    --account-name myStorageAccount \
    --account-key nPOgPR<--snip-->4Q==
    ```

4. Utwórz katalog punktu instalacji.

    Należy utworzyć w systemie Linux do zainstalowania udziału SMB do katalogu lokalnego. Cokolwiek zapisu lub odczytu z katalogu instalacji lokalnej jest przekazywany do udziału SMB, który znajduje się w pliku magazynu. Można utworzyć katalogu, należy użyć poniższego kodu:

    ```bash
    sudo mkdir -p /mnt/mymountdirectory
    ```

5. Instalowanie udziału SMB przy użyciu następującego kodu:

    ```azurecli
    sudo mount -t cifs //myStorageAccount.file.core.windows.net/mystorageshare /mnt/mymountdirectory -o vers=3.0,username=myStorageAccount,password=myStorageAccountkey,dir_mode=0777,file_mode=0777
    ```

6. Zachowanie instalacji SMB podczas ponownych rozruchów.

    Po ponownym uruchomieniu maszyny Wirtualnej systemu Linux, podczas zamykania odinstalowane jest zainstalowany udział SMB. Ponownie zainstalować udziale SMB podczas rozruchu, należy dodać wiersz do /etc/fstab systemu Linux. Linux używa pliku fstab pojawi się lista systemów plików, które należy zainstalować podczas uruchamiania. Dodawanie udziału SMB zapewnia, że udział magazynu plików jest trwale zainstalowany system plików dla maszyny Wirtualnej systemu Linux. Dodawanie magazynu plików udziału SMB do nowej maszyny Wirtualnej jest możliwe, gdy używasz init chmury.

    ```bash
    //myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    ```

## <a name="next-steps"></a>Kolejne kroki

- [Dostosowywanie maszyny Wirtualnej systemu Linux podczas tworzenia za pomocą init chmury](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Dodawanie dysku do maszyny wirtualnej z systemem Linux](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Szyfrowanie dysków na Maszynę wirtualną systemu Linux przy użyciu wiersza polecenia platformy Azure](encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
