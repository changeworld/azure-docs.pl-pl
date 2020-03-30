---
title: Instalowanie magazynu plików platformy Azure na maszynach wirtualnych z systemem Linux przy użyciu pamięci SMB
description: Jak zainstalować magazyn plików platformy Azure na maszynach wirtualnych z systemem Linux przy użyciu SMB z interfejsem wiersza polecenia platformy Azure
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure
ms.date: 06/28/2018
ms.author: cynthn
ms.openlocfilehash: 0314095a053087a7d490926c41c6ae386c304919
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066645"
---
# <a name="mount-azure-file-storage-on-linux-vms-using-smb"></a>Instalowanie magazynu plików platformy Azure na maszynach wirtualnych z systemem Linux przy użyciu pamięci SMB

W tym artykule pokazano, jak używać usługi azure file storage na maszynie wirtualnej z systemem Linux przy użyciu instalacji SMB z interfejsem wiersza polecenia platformy Azure. Usługa Azure File Storage oferuje udziały plików w chmurze przy użyciu standardowego protokołu SMB. 

Magazyn plików oferuje udziały plików w chmurze, które używają standardowego protokołu SMB. Można zainstalować udział plików z dowolnego systemu operacyjnego obsługującego protokół SMB 3.0. Korzystając z uchwytu SMB w systemie Linux, otrzymujesz łatwe kopie zapasowe do niezawodnej, stałej lokalizacji przechowywania archiwizacji obsługiwanej przez umowę SLA.

Przenoszenie plików z maszyny Wirtualnej do instalacji SMB, która jest hostowana w magazynie plików, to świetny sposób na debugowanie dzienników. Ten sam udział SMB można montować lokalnie na komputerze Mac, Linux lub stacji roboczej systemu Windows. SMB nie jest najlepszym rozwiązaniem do przesyłania strumieniowego dzienników linuksa lub aplikacji w czasie rzeczywistym, ponieważ protokół SMB nie jest zbudowany do obsługi tak ciężkich obowiązków rejestrowania. Dedykowane, ujednolicone narzędzie warstwy rejestrowania, takie jak Fluentd, byłoby lepszym wyborem niż SMB do zbierania danych wyjściowych systemu Linux i rejestrowania aplikacji.

Ten przewodnik wymaga, aby uruchomić platformę Azure CLI w wersji 2.0.4 lub nowszej. Aby odnaleźć wersję, uruchom polecenie **az --version**. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). 


## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów o nazwie *myResourceGroup* w lokalizacji *wschodnich stanów USA.*

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Utwórz nowe konto magazynu w utworzonej grupie zasobów, używając [konta magazynu AZ create](/cli/azure/storage/account). W tym przykładzie tworzy konto magazynu o nazwie *mySTORAGEACCT\<numer losowy>* i umieszcza nazwę tego konta magazynu w zmiennej **STORAGEACCT**. Nazwy kont magazynu muszą `$RANDOM` być unikatowe, przy użyciu dołącza numer na końcu, aby uczynić go unikatowym.

```azurecli
STORAGEACCT=$(az storage account create \
    --resource-group "myResourceGroup" \
    --name "mystorageacct$RANDOM" \
    --location eastus \
    --sku Standard_LRS \
    --query "name" | tr -d '"')
```

## <a name="get-the-storage-key"></a>Pobierz klucz pamięci masowej

Podczas tworzenia konta magazynu klucze konta są tworzone w parach, dzięki czemu można je obracać bez przerw w świadczeniu usług. Po przełączeniu do drugiego klawisza w parze, należy utworzyć nową parę kluczy. Nowe klucze konta magazynu są zawsze tworzone w parach, więc zawsze masz co najmniej jeden nieużyny klucz konta magazynu gotowy do przełączenia się.

Wyświetlanie kluczy konta magazynu przy użyciu [listy kluczy konta magazynu az](/cli/azure/storage/account/keys). W tym przykładzie przechowuje wartość klucza 1 w **zmiennej STORAGEKEY.**

```azurecli
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

## <a name="create-a-file-share"></a>Tworzenie udziału plików

Utwórz udział magazynu plików przy użyciu [udziału magazynu az .](/cli/azure/storage/share) 

Nazwy udziałów muszą być wielkimi literami, cyframi i pojedynczymi myślnikami, ale nie mogą zaczynać się od łącznika. Szczegółowe informacje o nazwach plików i udziałów plików można znaleźć w temacie [Naming and Referencing Shares, Directories, Files, and Metadata](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata) (Nazywanie i odwoływanie się do udziałów, katalogów, plików i metadanych).

W tym przykładzie tworzy udział o nazwie *myshare* z przydziałem 10-GiB. 

```azurecli
az storage share create --name myshare \
    --quota 10 \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY
```

## <a name="create-a-mount-point"></a>Tworzenie punktu instalacji

Aby zainstalować udział plików platformy Azure na komputerze z systemem Linux, należy upewnić się, że masz zainstalowany pakiet **cifs-utils.** Aby uzyskać instrukcje instalacji, zobacz [Instalowanie pakietu cifs-utils dla dystrybucji Linuksa](../../storage/files/storage-how-to-use-files-linux.md#install-cifs-utils).

Usługa Azure Files używa protokołu SMB, który komunikuje się za pomocą portu TCP 445.  Jeśli masz problemy z montażem udziału plików platformy Azure, upewnij się, że zapora nie blokuje portu TCP 445.


```bash
mkdir -p /mnt/MyAzureFileShare
```

## <a name="mount-the-share"></a>Zamontuj udział

Zainstaluj udział pliku platformy Azure w katalogu lokalnym. 

```bash
sudo mount -t cifs //$STORAGEACCT.file.core.windows.net/myshare /mnt/MyAzureFileShare -o vers=3.0,username=$STORAGEACCT,password=$STORAGEKEY,dir_mode=0777,file_mode=0777,serverino
```

Powyższe polecenie używa polecenia [mount](https://linux.die.net/man/8/mount) do zainstalowania udziału plików platformy Azure i opcji specyficznych dla [cifs](https://linux.die.net/man/8/mount.cifs). W szczególności opcje file_mode i dir_mode ustawiają pliki i `0777`katalogi na uprawnienie . Uprawnienie `0777` daje uprawnienia do odczytu, zapisu i wykonywania dla wszystkich użytkowników. Te uprawnienia można zmienić, zastępując wartości innymi [uprawnieniami chmod.](https://en.wikipedia.org/wiki/Chmod) Można również użyć innych opcji [cifs,](https://linux.die.net/man/8/mount.cifs) takich jak gid lub uid. 


## <a name="persist-the-mount"></a>Upotrzyć uchwyt

Po ponownym uruchomieniu maszyny Wirtualnej systemu Linux, zainstalowany udział SMB jest odinstalowywał podczas zamykania. Aby ponownie zamontować udział SMB przy rozruchu, dodaj linię do Linuksa /etc/fstab. Linux używa pliku fstab do listy systemów plików, które musi zamontować podczas procesu rozruchu. Dodanie udziału SMB gwarantuje, że udział magazynu plików jest trwale zainstalowanym systemem plików dla maszyny Wirtualnej systemu Linux. Dodawanie udziału SMB magazynu plików do nowej maszyny Wirtualnej jest możliwe, gdy używasz cloud-init.

```bash
//myaccountname.file.core.windows.net/mystorageshare /mnt/mymountpoint cifs vers=3.0,username=mystorageaccount,password=myStorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

Aby zwiększyć bezpieczeństwo w środowiskach produkcyjnych, należy przechowywać poświadczenia poza fstab.

## <a name="next-steps"></a>Następne kroki

- [Używanie init w chmurze do dostosowywania maszyny Wirtualnej systemu Linux podczas tworzenia](using-cloud-init.md)
- [Dodawanie dysku do maszyny wirtualnej z systemem Linux](add-disk.md)
- [Szyfrowanie dysków platformy Azure dla maszyn wirtualnych z systemem Linux](disk-encryption-overview.md)

