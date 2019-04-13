---
title: Jak zainstalować usługi Azure Blob storage jako systemu plików w systemie Linux | Dokumentacja firmy Microsoft
description: Instalowanie kontenera magazynu obiektów Blob platformy Azure przy użyciu FUSE w systemie Linux
services: storage
author: seguler
ms.service: storage
ms.topic: article
ms.date: 2/1/2019
ms.author: seguler
ms.openlocfilehash: eadf52afd115eb1cb642082cea4b9f338bd44914
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/12/2019
ms.locfileid: "59521657"
---
# <a name="how-to-mount-blob-storage-as-a-file-system-with-blobfuse"></a>Jak zainstalować magazyn obiektów Blob jako systemu plików przy użyciu blobfuse

## <a name="overview"></a>Omówienie
[Blobfuse](https://github.com/Azure/azure-storage-fuse) jest sterownikiem wirtualnego systemu plików usługi Azure Blob Storage. Blobfuse umożliwia dostęp do istniejących danych blokowych obiektów blob na koncie magazynu za pomocą systemu plików w systemie Linux. Usługa Azure Blob storage to usługa magazynu obiektów i nie ma hierarchicznej przestrzeni nazw. Blobfuse zapewnia tej przestrzeni nazw przy użyciu schematu katalogu wirtualnego do przodu ukośnik "/" jako ogranicznik.  

Ten przewodnik przedstawia sposób użycia blobfuse i zainstaluj kontenera magazynu obiektów Blob dla systemów Linux i dostęp do danych. Aby dowiedzieć się więcej na temat blobfuse, przeczytaj szczegółowe informacje w [repozytorium blobfuse](https://github.com/Azure/azure-storage-fuse).

> [!WARNING]
> Blobfuse nie gwarantuje zgodności POSIX 100%, ponieważ po prostu tłumaczy żądania do [interfejsów API REST usługi Blob](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api). Na przykład operacje zmiany nazwy są niepodzielne w modelu POSIX, ale nie w blobfuse.
> Aby uzyskać pełną listę różnic między macierzysty system plików i blobfuse, odwiedź stronę [repozytorium kodu źródłowego blobfuse](https://github.com/azure/azure-storage-fuse).
> 

## <a name="install-blobfuse-on-linux"></a>Instalowanie blobfuse w systemie Linux
Pliki binarne Blobfuse są dostępne na [repozytoriów oprogramowania firmy Microsoft dla systemu Linux](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software) dystrybucji Ubuntu i RHEL. Aby zainstalować blobfuse na tych dystrybucji, należy skonfigurować jedną z repozytoriów, z listy. Możesz również tworzyć plików binarnych, od następującego kodu źródłowego [kroki instalacji usługi Azure Storage](https://github.com/Azure/azure-storage-fuse/wiki/1.-Installation#option-2---build-from-source) gdy nie są dostępne dla Twojej dystrybucji danych binarnych.

Blobfuse obsługuje instalację w systemie Ubuntu 14.04 i 16.04, 18.04. Uruchom następujące polecenie, aby upewnić się, że jedna z tych wersji wdrożyć:
```
lsb_release -a
```

### <a name="configure-the-microsoft-package-repository"></a>Skonfiguruj repozytorium pakietów firmy Microsoft
Konfigurowanie [repozytorium pakietów systemu Linux dla produktów firmy Microsoft](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software).

Na przykład w dystrybucji Enterprise Linux 6:
```bash
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/6/packages-microsoft-prod.rpm
```

Podobnie, Zmień adres URL do `.../rhel/7/...` wskaż dystrybucji Enterprise Linux 7.

Innym przykładem na dystrybucji Ubuntu 14.04:
```bash
wget https://packages.microsoft.com/config/ubuntu/14.04/packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
sudo apt-get update
```

Podobnie, Zmień adres URL do `.../ubuntu/16.04/...` lub `.../ubuntu/18.04/...` można odwoływać się do innej wersji systemu Ubuntu.

### <a name="install-blobfuse"></a>Zainstaluj blobfuse

On an Ubuntu/Debian distribution:
```bash
sudo apt-get install blobfuse
```

W systemie Linux Enterprise dystrybucji:
```bash    
sudo yum install blobfuse
```

## <a name="prepare-for-mounting"></a>Przygotowanie do instalowania
Blobfuse zapewnia wydajność jak natywne, wymagając ścieżka tymczasowa w systemie plików, aby buforować i wszystkie otwarte pliki w pamięci podręcznej. Dla tej ścieżki tymczasowej wybierz większość dysku wydajna, lub użyj dysku ramdisk uzyskać najlepszą wydajność. 

> [!NOTE]
> Blobfuse przechowuje cała zawartość Otwórz plik w tymczasowej ścieżki. Upewnij się, że ma wystarczającej ilości miejsca, aby pomieścić wszystkie otwarte pliki. 
> 

### <a name="optional-use-a-ramdisk-for-the-temporary-path"></a>(Opcjonalnie) Użyj dysku ramdisk dla ścieżka tymczasowa
Poniższy przykład tworzy ramdisk 16 GB i katalog blobfuse. Wybierz rozmiar, w zależności od potrzeb. Ten dysk RAM umożliwia blobfuse do otwartych plików do 16 GB, rozmiar. 
```bash
sudo mount -t tmpfs -o size=16g tmpfs /mnt/ramdisk
sudo mkdir /mnt/ramdisk/blobfusetmp
sudo chown <youruser> /mnt/ramdisk/blobfusetmp
```

### <a name="use-an-ssd-as-a-temporary-path"></a>Użyj dyski SSD jako ścieżka tymczasowa
Na platformie Azure może użyć efemeryczne dyski (SSD) dostępnych na maszynach wirtualnych, zapewnienie bufora o małych opóźnieniach blobfuse. W dystrybucjach systemu Ubuntu to efemerycznego dysku jest zainstalowana w "/ mnt". W dystrybucji systemu Red Hat i CentOS, dysk jest zainstalowany na "/ mnt/zasób /".

Upewnij się, że użytkownik ma dostęp do ścieżki tymczasowej:
```bash
sudo mkdir /mnt/resource/blobfusetmp -p
sudo chown <youruser> /mnt/resource/blobfusetmp
```

### <a name="configure-your-storage-account-credentials"></a>Konfigurowanie poświadczeń konta magazynu
Blobfuse wymaga swoje poświadczenia, które mają być przechowywane w pliku tekstowym w następującym formacie: 

```
accountName myaccount
accountKey storageaccesskey
containerName mycontainer
```
`accountName` To prefiks konta magazynu — nie pełny adres URL.

Tworzenie przy użyciu tego pliku:

```
touch ~/fuse_connection.cfg
```

Po utworzeniu i edytując ten plik, upewnij się ograniczyć dostęp, aby inni użytkownicy nie może go odczytać.
```bash
chmod 600 fuse_connection.cfg
```

> [!NOTE]
> Jeśli utworzono plik konfiguracji na Windows upewnij się uruchomić `dos2unix` oczyszczaj i przekonwertować plik do formatu Unix. 
>

### <a name="create-an-empty-directory-for-mounting"></a>Utwórz pusty katalog potrzeby instalowania
```bash
mkdir ~/mycontainer
```

## <a name="mount"></a>Instalacja

> [!NOTE]
> Aby uzyskać pełną listę opcji instalacji, sprawdź [repozytorium blobfuse](https://github.com/Azure/azure-storage-fuse#mount-options).  
> 

Aby blobfuse instalacji, uruchom następujące polecenie z użytkownika. To polecenie instaluje kontenerem określonymi WE "/ path/to/fuse_connection.cfg" do lokalizacji "/ mycontainer".

```bash
sudo blobfuse ~/mycontainer --tmp-path=/mnt/resource/blobfusetmp  --config-file=/path/to/fuse_connection.cfg -o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120
```

Teraz należy dostęp do Twojego blokowych obiektów blob za pośrednictwem interfejsów API systemu zwykły plik. Użytkownik, który instaluje katalogu jest jedyną osobą, która do niego dostęp, domyślnie, co ogranicza dostęp. Aby zezwolić na dostęp do wszystkich użytkowników, można zainstalować za pomocą opcji ```-o allow_other```. 

```bash
cd ~/mycontainer
mkdir test
echo "hello world" > test/blob.txt
```

## <a name="next-steps"></a>Kolejne kroki

* [Strona główna Blobfuse](https://github.com/Azure/azure-storage-fuse#blobfuse)
* [Zgłaszanie problemów blobfuse](https://github.com/Azure/azure-storage-fuse/issues) 

