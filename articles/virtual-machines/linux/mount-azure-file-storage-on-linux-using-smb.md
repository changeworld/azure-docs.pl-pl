---
title: Magazyn plików Azure instalacji na maszynach wirtualnych systemu Linux za pomocą protokołu SMB | Dokumentacja firmy Microsoft
description: Jak zainstalować magazyn plików Azure na maszynach wirtualnych systemu Linux za pomocą protokołu SMB z wiersza polecenia platformy Azure
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
ms.openlocfilehash: 2019324030b2e4c469d0b9ba937fb40a9d0675f1
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37099715"
---
# <a name="mount-azure-file-storage-on-linux-vms-using-smb"></a>Magazyn plików Azure instalacji na maszynach wirtualnych systemu Linux za pomocą protokołu SMB


W tym artykule przedstawiono sposób korzystania z usługi Magazyn plików Azure na maszynie Wirtualnej systemu Linux przy użyciu protokołu SMB instalacji z wiersza polecenia platformy Azure. Magazyn plików Azure oferuje udziały plików w chmurze przy użyciu standardowego protokołu SMB. 

Magazyn plików oferuje udziały plików w chmurze, które używają standardowego protokołu SMB. Można zainstalować udział plików z dowolnego systemu operacyjnego, który obsługuje protokół SMB 3.0. Użycie instalacji SMB w systemie Linux, otrzymasz łatwe tworzenie kopii zapasowych niezawodne, stała archiwizacji lokalizacji magazynu obsługiwaną przez umowy dotyczącej poziomu usług.

Przenoszenie plików z maszyny Wirtualnej do instalacji SMB, który znajduje się na magazyn plików jest doskonałym sposobem dzienników debugowania. Tego samego udziału SMB mogą być instalowane lokalnie na stację roboczą Mac, Linux lub Windows. Najlepszego rozwiązania do przesyłania strumieniowego systemu Linux nie jest SMB lub protokołu SMB nie są wbudowane do obsługi tych obowiązków duże rejestrowania w czasie rzeczywistym Dzienniki aplikacji. Narzędzia rejestrowania dedykowanych, ujednoliconej warstwy, takiego jak Fluentd będzie lepszym rozwiązaniem niż SMB zbierania systemu Linux i aplikacji, rejestrowania danych wyjściowych.

W tym przewodniku wymaga, że używasz interfejsu wiersza polecenia Azure w wersji 2.0.4 lub nowszej. Aby odnaleźć wersję, uruchom polecenie **az --version**. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli). 


## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów o nazwie *myResourceGroup* w *wschodnie stany USA* lokalizacji.

```bash
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Utwórz nowe konto magazynu w grupie zasobów, które zostały utworzone za pomocą [Tworzenie konta magazynu az](/cli/azure/storage/account#create). W tym przykładzie tworzy konto magazynu o nazwie *mySTORAGEACCT<random number>*  i umieszcza nazwę tego konta magazynu w zmiennej **STORAGEACCT**. Nazwy konta magazynu musi być unikatowa, przy użyciu `$RANDOM` dołącza numer w celu zapewnienia unikatowości.

```bash
STORAGEACCT=$(az storage account create \
    --resource-group "myResourceGroup" \
    --name "mystorageacct$RANDOM" \
    --location eastus \
    --sku Standard_LRS \
    --query "name" | tr -d '"')
```

## <a name="get-the-storage-key"></a>Pobierz klucz magazynu

Podczas tworzenia konta magazynu, klucze konta są tworzone w pary, dzięki czemu można obracać bez przerw w działaniu usługi. Po przełączeniu do drugi klucz w parze, możesz utworzyć nową parę kluczy. Nowe klucze konta magazynu są tworzone w pary, zawsze, więc zawsze mieć co najmniej jeden klucz konta magazynu nieużywane gotowe przełączyć się do.

Wyświetl klucze konta magazynu przy użyciu [listy kluczy konta magazynu az](/cli/azure/storage/account/keys#list). W tym przykładzie przechowuje wartość klucz 1 w **atrybutu STORAGEKEY** zmiennej.

```bash
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

## <a name="create-a-file-share"></a>Tworzenie udziału plików

Tworzenie pliku magazynu udziale za pomocą [utworzyć udział magazynu az](/cli/azure/storage/share#create). 

Nazwy udziałów muszą być małe litery, cyfry i łączniki pojedynczego, ale nie może zaczynać się od łącznika. Szczegółowe informacje o nazwach plików i udziałów plików można znaleźć w temacie [Naming and Referencing Shares, Directories, Files, and Metadata](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata) (Nazywanie i odwoływanie się do udziałów, katalogów, plików i metadanych).

W tym przykładzie powoduje utworzenie udziału o nazwie *moj_udzial* z przydziału 10 GiB. 

```bash
az storage share create --name myshare \
    --quota 10 \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY
```

## <a name="create-a-mount-point"></a>Utwórz punkt instalacji

Aby zainstalować udział plików na platformę Azure na komputerze z systemem Linux, należy upewnić się, że masz **witryny cifs** zainstalowanym pakietem. Aby uzyskać instrukcje instalacji, zobacz [zainstalować pakiet cifs witryny dla dystrybucji systemu Linux](../../storage/files/storage-how-to-use-files-linux.md#install-cifs-utils).

Usługa pliki Azure korzysta z protokołu SMB, który komunikuje się za pośrednictwem portu TCP 445.  Jeśli masz problemy instalowanie udziału plików platformy Azure, upewnij się, że Zapora nie blokuje TCP port 445.


```bash
mkdir -p /mnt/MyAzureFileShare
```

## <a name="mount-the-share"></a>Instalowanie udziału

Instalowanie udziału plików na platformę Azure do katalogu lokalnego. 

```bash
sudo mount -t cifs //$STORAGEACCT.file.core.windows.net/myshare /mnt/MyAzureFileShare -o vers=3.0,username=$STORAGEACCT,password=$STORAGEKEY,dir_mode=0777,file_mode=0777,serverino
```



## <a name="persist-the-mount"></a>Utrwalanie instalacji

Po ponownym uruchomieniu maszyny Wirtualnej systemu Linux, podczas zamykania odinstalowane jest zainstalowany udział SMB. Ponownie zainstalować udziale SMB podczas rozruchu, należy dodać wiersz do /etc/fstab systemu Linux. Linux używa pliku fstab pojawi się lista systemów plików, które należy zainstalować podczas uruchamiania. Dodawanie udziału SMB zapewnia, że udział magazynu plików jest trwale zainstalowany system plików dla maszyny Wirtualnej systemu Linux. Dodawanie magazynu plików udziału SMB do nowej maszyny Wirtualnej jest możliwe, gdy używasz init chmury.

```bash
//myaccountname.file.core.windows.net/mystorageshare /mnt/mymountpoint cifs vers=3.0,username=mystorageaccount,password=myStorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```
Aby zwiększyć bezpieczeństwo w środowiskach produkcyjnych należy przechowywać swoje poświadczenia poza fstab.

## <a name="next-steps"></a>Kolejne kroki

- [Dostosowywanie maszyny Wirtualnej systemu Linux podczas tworzenia za pomocą init chmury](using-cloud-init.md)
- [Dodawanie dysku do maszyny wirtualnej z systemem Linux](add-disk.md)
- [Szyfrowanie dysków na Maszynę wirtualną systemu Linux przy użyciu wiersza polecenia platformy Azure](encrypt-disks.md)

