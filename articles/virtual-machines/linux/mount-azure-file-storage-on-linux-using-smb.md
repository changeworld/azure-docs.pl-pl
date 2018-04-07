---
title: Magazyn plików Azure instalacji na maszynach wirtualnych systemu Linux za pomocą protokołu SMB | Dokumentacja firmy Microsoft
description: Jak zainstalować magazyn plików Azure na maszynach wirtualnych systemu Linux za pomocą protokołu SMB 2.0 interfejsu wiersza polecenia platformy Azure
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
ms.date: 02/13/2017
ms.author: v-livech
ms.openlocfilehash: de200c9b18b9d27325bcb92e0d27e83ad7c65811
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="mount-azure-file-storage-on-linux-vms-using-smb"></a>Magazyn plików Azure instalacji na maszynach wirtualnych systemu Linux za pomocą protokołu SMB

W tym artykule przedstawiono sposób korzystać z usługą Magazyn plików Azure na maszynie Wirtualnej systemu Linux przy użyciu instalacji SMB 2.0 interfejsu wiersza polecenia platformy Azure. Magazyn plików Azure oferuje udziały plików w chmurze przy użyciu standardowego protokołu SMB. Czynności te można również wykonać przy użyciu [interfejsu wiersza polecenia platformy Azure w wersji 1.0](mount-azure-file-storage-on-linux-using-smb-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Wymagania są następujące:

- [Konto platformy Azure](https://azure.microsoft.com/pricing/free-trial/)
- [Pliki kluczy publicznych i prywatnych SSH](mac-create-ssh-keys.md)

## <a name="quick-commands"></a>Szybkie polecenia

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
Aby to zrobić, Dodaj następujący wiersz do `/etc/fstab`:

```bash
//myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

## <a name="detailed-walkthrough"></a>Szczegółowy przewodnik

Magazyn plików oferuje udziały plików w chmurze, które używają standardowego protokołu SMB. Najnowsza wersja pliku magazynu można również zainstalować udział plików z dowolnego systemu operacyjnego, który obsługuje protokół SMB 3.0. Użycie instalacji SMB w systemie Linux, otrzymasz łatwe tworzenie kopii zapasowych niezawodne, stała archiwizacji lokalizacji magazynu obsługiwaną przez umowy dotyczącej poziomu usług.

Przenoszenie plików z maszyny Wirtualnej do instalacji SMB, który znajduje się na magazyn plików jest doskonałym sposobem dzienników debugowania. Wynika to z tego samego udziału SMB mogą być instalowane lokalnie na stację roboczą Mac, Linux lub Windows. Najlepszego rozwiązania do przesyłania strumieniowego systemu Linux nie jest SMB lub protokołu SMB nie są wbudowane do obsługi tych obowiązków duże rejestrowania w czasie rzeczywistym Dzienniki aplikacji. Narzędzia rejestrowania dedykowanych, ujednoliconej warstwy, takiego jak Fluentd będzie lepszym rozwiązaniem niż SMB zbierania systemu Linux i aplikacji, rejestrowania danych wyjściowych.

W ramach tego przewodnika szczegółowe możemy utworzyć wymagania wstępne niezbędne do utworzenia udziału plików magazynu i zainstalować go za pośrednictwem protokołu SMB na maszynie Wirtualnej systemu Linux.

1. Utwórz nową grupę zasobów o [Tworzenie grupy az](/cli/azure/group#az_group_create) do przechowywania udziału plików.

    Aby utworzyć grupę zasobów o nazwie `myResourceGroup` w lokalizacji "Zachodnie stany USA", skorzystaj z następującego przykładu:

    ```azurecli
    az group create --name myResourceGroup --location westus
    ```

2. Utwórz konto magazynu platformy Azure z [Tworzenie konta magazynu az](/cli/azure/storage/account#az_storage_account_create) do przechowywania plików rzeczywistych.

    Aby utworzyć konto magazynu o nazwie mojekontomagazynu przy użyciu magazynu Standard_LRS jednostka SKU, skorzystaj z następującego przykładu:

    ```azurecli
    az storage account create --resource-group myResourceGroup \
        --name mystorageaccount \
        --location westus \
        --sku Standard_LRS
    ```

3. Pokaż klucze konta magazynu.

    Podczas tworzenia konta magazynu, klucze konta są tworzone w pary, dzięki czemu można obracać bez przerw w działaniu usługi. Po przełączeniu do drugi klucz w parze, możesz utworzyć nową parę kluczy. Nowe klucze konta magazynu zawsze są tworzone w parach, sprawdzając, czy zawsze jest co najmniej jeden klucz konta magazynu nieużywane gotowe przełączyć się do.

    Wyświetl klucze konta magazynu z [listy kluczy konta magazynu az](/cli/azure/storage/account/keys#az_storage_account_keys_list). Konto magazynu kluczy dla nazwanego `mystorageaccount` przedstawiono w poniższym przykładzie:

    ```azurecli
    az storage account keys list --resource-group myResourceGroup \
        --account-name mystorageaccount
    ```

    Aby wyodrębnić jednego klucza, użyj `--query` flagi. Poniższy przykład wyodrębnia pierwszego klucza (`[0]`):

    ```azurecli
    az storage account keys list --resource-group myResourceGroup \
        --account-name mystorageaccount \
        --query '[0].{Key:value}' --output tsv
    ```

4. Utwórz udział magazynu plików.

    Udział magazynu plików zawiera udział SMB na [utworzyć udział magazynu az](/cli/azure/storage/share#az_storage_share_create). Limit przydziału zawsze jest wyrażona w gigabajtów (GB). Przebiegu w jeden z kluczy z poprzednim `az storage account keys list` polecenia. Utwórz udział o nazwie mystorageshare z przydziału 10 GB, za pomocą w poniższym przykładzie:

    ```azurecli
    az storage share create --name mystorageshare \
        --quota 10 \
        --account-name mystorageaccount \
        --account-key nPOgPR<--snip-->4Q==
    ```

5. Utwórz katalog punktu instalacji.

    Tworzenie katalogu lokalnego w systemie plików Linux należy zainstalować udział SMB. Cokolwiek zapisu lub odczytu z katalogu instalacji lokalnej jest przekazywany do udziału SMB, który znajduje się w pliku magazynu. Aby utworzyć katalogu lokalnego na /mnt/mymountdirectory, skorzystaj z następującego przykładu:

    ```bash
    sudo mkdir -p /mnt/mymountdirectory
    ```

6. Instalowanie udziału SMB do katalogu lokalnego.

    Podaj własne nazwy użytkownika konta magazynu i klucz konta magazynu na potrzeby poświadczeń instalacji:

    ```azurecli
    sudo mount -t cifs //myStorageAccount.file.core.windows.net/mystorageshare /mnt/mymountdirectory -o vers=3.0,username=mystorageaccount,password=mystorageaccountkey,dir_mode=0777,file_mode=0777
    ```

7. Zachowanie instalacji SMB podczas ponownych rozruchów.

    Po ponownym uruchomieniu maszyny Wirtualnej systemu Linux, podczas zamykania odinstalowane jest zainstalowany udział SMB. Ponownie zainstalować udziale SMB podczas rozruchu, należy dodać wiersz do /etc/fstab systemu Linux. Linux używa pliku fstab pojawi się lista systemów plików, które należy zainstalować podczas uruchamiania. Dodawanie udziału SMB zapewnia, że udział magazynu plików jest trwale zainstalowany system plików dla maszyny Wirtualnej systemu Linux. Dodawanie magazynu plików udziału SMB do nowej maszyny Wirtualnej jest możliwe, gdy używasz init chmury.

    ```bash
    //myaccountname.file.core.windows.net/mystorageshare /mnt/mymountdirectory cifs vers=3.0,username=mystorageaccount,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    ```

## <a name="next-steps"></a>Kolejne kroki

- [Dostosowywanie maszyny Wirtualnej systemu Linux podczas tworzenia za pomocą init chmury](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Dodawanie dysku do maszyny wirtualnej z systemem Linux](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Szyfrowanie dysków na Maszynę wirtualną systemu Linux przy użyciu wiersza polecenia platformy Azure](encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
