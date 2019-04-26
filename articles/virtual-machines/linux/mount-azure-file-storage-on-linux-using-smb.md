---
title: Instalowanie usługi Azure File storage na maszynach wirtualnych systemu Linux przy użyciu protokołu SMB | Dokumentacja firmy Microsoft
description: Jak zainstalować usługę Azure File storage na maszynach wirtualnych systemu Linux przy użyciu protokołu SMB przy użyciu wiersza polecenia platformy Azure
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/28/2018
ms.author: cynthn
ms.openlocfilehash: 4b3bba1da5238655ca749f6464c539e53ca48f27
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60542785"
---
# <a name="mount-azure-file-storage-on-linux-vms-using-smb"></a>Instalowanie usługi Azure File storage na maszynach wirtualnych systemu Linux przy użyciu protokołu SMB

W tym artykule pokazano, jak używać usługi Azure File storage na maszynie Wirtualnej z systemem Linux przy użyciu protokołu SMB instalacji z wiersza polecenia platformy Azure. Usługa Azure File storage oferuje udziały plików w chmurze przy użyciu standardowego protokołu SMB. 

Usługa File storage oferuje udziały plików w chmurze, korzystające ze standardowego protokołu SMB. Można zainstalować udział plików z dowolnego systemu operacyjnego, który obsługuje protokół SMB 3.0. Gdy używasz instalacji SMB w systemie Linux, można korzystać łatwe tworzenie kopii zapasowych niezawodne, trwały archiwizacji lokalizację magazynu, który jest obsługiwany przez umowy SLA.

Przenoszenie plików z maszyny Wirtualnej do instalacji SMB, który znajduje się File Storage jest doskonałym sposobem debugowania dzienniki. Ten sam udział SMB mogą być instalowane lokalnie na swojej stacji roboczej Mac, Linux lub Windows. Protokół SMB jest najlepsze rozwiązanie do przesyłania strumieniowego systemu Linux lub dzienniki aplikacji w czasie rzeczywistym, ponieważ protokół SMB nie został opracowany pod kątem obsługi tych obowiązków mocno rejestrowania. Narzędzia warstwy rejestrowania dedykowaną, ujednolicone, takiego jak Fluentd będzie lepszym rozwiązaniem niż SMB zbierania systemu Linux i aplikacji, rejestrowanie danych wyjściowych.

Ten przewodnik wymaga, że korzystasz z wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Aby odnaleźć wersję, uruchom polecenie **az --version**. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). 


## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów o nazwie *myResourceGroup* w *wschodnie stany USA* lokalizacji.

```bash
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Utwórz nowe konto magazynu w grupie zasobów, które zostały utworzone za pomocą [Tworzenie konta magazynu az](/cli/azure/storage/account). W tym przykładzie tworzone jest konto magazynu o nazwie *mySTORAGEACCT\<losową liczbę >* i umieszczenie nazwy tego konta magazynu w zmiennej **STORAGEACCT**. Nazwy kont magazynu muszą być unikatowe, za pomocą `$RANDOM` dołącza numer-to-end, aby była unikatowa.

```bash
STORAGEACCT=$(az storage account create \
    --resource-group "myResourceGroup" \
    --name "mystorageacct$RANDOM" \
    --location eastus \
    --sku Standard_LRS \
    --query "name" | tr -d '"')
```

## <a name="get-the-storage-key"></a>Pobieranie klucza magazynu

Podczas tworzenia konta magazynu, klucze konta są tworzone w parach, dzięki czemu można obracać bez żadnych zakłóceń. Po przełączeniu się do drugiego klucza w parze, utworzysz nową parę kluczy. Nowe klucze konta magazynu są zawsze tworzone w parach, dzięki czemu zawsze mieć co najmniej jeden klucz konta magazynu nieużywane gotowe przełączyć się do.

Wyświetl klucze konta magazynu przy użyciu [listy kluczy kont magazynu az](/cli/azure/storage/account/keys). W tym przykładzie przechowuje wartość klucz 1 w **atrybutu STORAGEKEY** zmiennej.

```bash
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

## <a name="create-a-file-share"></a>Tworzenie udziału plików

Utwórz plik magazynu udziału za pomocą [Utwórz udział magazynu az](/cli/azure/storage/share). 

Nazwy udziałów muszą być małe litery, cyfry i pojedyncze łączniki, ale nie może zaczynać się łącznikiem. Szczegółowe informacje o nazwach plików i udziałów plików można znaleźć w temacie [Nazywanie i odwoływanie się do udziałów, katalogów, plików i metadanych](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

W tym przykładzie tworzy udział o nazwie *myshare* z limitem przydziału 10 GiB. 

```bash
az storage share create --name myshare \
    --quota 10 \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY
```

## <a name="create-a-mount-point"></a>Utwórz punkt instalacji

Aby zainstalować udział plików platformy Azure na komputer z systemem Linux, należy się upewnić, że masz **cifs utils** zainstalowany pakiet. Aby uzyskać instrukcje dotyczące instalacji, zobacz [zainstaluj pakiet cifs utils dla dystrybucji systemu Linux](../../storage/files/storage-how-to-use-files-linux.md#install-cifs-utils).

Usługa Azure Files korzysta z protokołu SMB, który komunikuje się za pośrednictwem portu TCP 445.  Jeśli występują problemy z instalowania udziału plików platformy Azure, upewnij się, że Zapora nie blokuje portu TCP 445.


```bash
mkdir -p /mnt/MyAzureFileShare
```

## <a name="mount-the-share"></a>Instalowanie udziału

Instalowanie udziału plików platformy Azure do katalogu lokalnego. 

```bash
sudo mount -t cifs //$STORAGEACCT.file.core.windows.net/myshare /mnt/MyAzureFileShare -o vers=3.0,username=$STORAGEACCT,password=$STORAGEKEY,dir_mode=0777,file_mode=0777,serverino
```

Powyższe polecenie używa [instalacji](https://linux.die.net/man/8/mount) polecenie, aby zainstalować udział plików platformy Azure i opcje specyficzne dla [cifs](https://linux.die.net/man/8/mount.cifs). W szczególności file_mode i dir_mode opcje zestawu plików i katalogów do uprawnień `0777`. `0777` Umożliwia uprawnień odczytu, zapisu i wykonywania uprawnień dla wszystkich użytkowników. Te uprawnienia można zmienić, zastępując wartości z innymi [uprawnienia chmod](https://en.wikipedia.org/wiki/Chmod). Można również użyć innych [cifs](https://linux.die.net/man/8/mount.cifs) opcje, takie jak identyfikator GID dostępu lub identyfikatora uid. 


## <a name="persist-the-mount"></a>Utrwalanie instalacji

Po ponownym uruchomieniu maszyny Wirtualnej systemu Linux podczas zamykania jest odinstalowane zainstalowanego udziału SMB. Aby ponownie zainstalować udziału SMB podczas rozruchu, dodanie wiersza do/etc/fstab w systemie Linux. Linux używa pliku fstab, aby wyświetlić listę systemów plików, których potrzebuje do zainstalowania podczas procesu rozruchu. Dodawanie udziału SMB zapewnia udział usługi File storage trwale zainstalowany system plików dla maszyny Wirtualnej systemu Linux. Dodawanie usługi File storage udziału SMB do nowej maszyny Wirtualnej jest możliwe w przypadku, gdy korzystasz z pakietu cloud-init.

```bash
//myaccountname.file.core.windows.net/mystorageshare /mnt/mymountpoint cifs vers=3.0,username=mystorageaccount,password=myStorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```
Aby zwiększyć bezpieczeństwo w środowiskach produkcyjnych należy przechowywać swoje poświadczenia poza fstab.

## <a name="next-steps"></a>Kolejne kroki

- [Dostosowywanie maszyny Wirtualnej z systemem Linux podczas tworzenia za pomocą pakietu cloud-init](using-cloud-init.md)
- [Dodawanie dysku do maszyny wirtualnej z systemem Linux](add-disk.md)
- [Szyfrowanie dysków na maszynie Wirtualnej z systemem Linux przy użyciu wiersza polecenia platformy Azure](encrypt-disks.md)

