---
title: Jak zainstalować magazyn obiektów Blob platformy Azure jako system plików w systemie Linux | Dokumenty firmy Microsoft
description: Instalowanie kontenera magazynu obiektów Blob platformy Azure z systemem FUSE w systemie Linux
author: rishabpoh
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 2/1/2019
ms.author: ripohane
ms.reviewer: dineshm
ms.openlocfilehash: a0a03df59bc6ecffcb4f0a701616297f2da78fdb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061424"
---
# <a name="how-to-mount-blob-storage-as-a-file-system-with-blobfuse"></a>Jak zamontować magazyn obiektów Blob jako system plików z bluzką

## <a name="overview"></a>Omówienie
[Blobfuse](https://github.com/Azure/azure-storage-fuse) jest sterownikiem wirtualnego systemu plików usługi Azure Blob Storage. Blobfuse pozwala uzyskać dostęp do istniejących danych blokowych obiektów blob na koncie pamięci masowej za pośrednictwem systemu plików Linux. Blobfuse używa schematu katalogu wirtualnego z ukośnikiem "/" jako ogranicznikiem.  

W tym przewodniku pokazano, jak używać blobfuse i zainstalować kontener magazynu obiektów Blob w systemie Linux i uzyskać dostęp do danych. Aby dowiedzieć się więcej o bluzce, przeczytaj szczegóły w [repozytorium bluzki](https://github.com/Azure/azure-storage-fuse).

> [!WARNING]
> Blobfuse nie gwarantuje zgodności z POSIX 100%, ponieważ po prostu przekłada żądania na [interfejsy API spoczynku obiektów blob.](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api) Na przykład operacje zmiany nazwy są niepodzielne w POSIX, ale nie w blobfuse.
> Aby uzyskać pełną listę różnic między natywnym systemem plików a bluzką, odwiedź [repozytorium kodu źródłowego bluzki](https://github.com/azure/azure-storage-fuse).
> 

## <a name="install-blobfuse-on-linux"></a>Zainstaluj bluzkę na Linuksie
Pliki binarne blobfuse są dostępne w [repozytoriach oprogramowania firmy Microsoft dla systemów Linux](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software) dla dystrybucji Ubuntu i RHEL. Aby zainstalować bluzkę w tych dystrybucjach, skonfiguruj jedno z repozytoriów z listy. Można również utworzyć pliki binarne z kodu źródłowego zgodnie z [kroki instalacji usługi Azure Storage,](https://github.com/Azure/azure-storage-fuse/wiki/1.-Installation#option-2---build-from-source) jeśli nie ma żadnych plików binarnych dostępnych dla dystrybucji.

Blobfuse obsługuje instalację na Ubuntu 14.04, 16.04 i 18.04. Uruchom to polecenie, aby upewnić się, że masz jedną z tych wersji wdrożonych:
```
lsb_release -a
```

### <a name="configure-the-microsoft-package-repository"></a>Konfigurowanie repozytorium pakietów firmy Microsoft
Skonfiguruj [repozytorium pakietów systemu Linux dla produktów firmy Microsoft](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software).

Na przykład w dystrybucji Enterprise Linux 6:
```bash
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/6/packages-microsoft-prod.rpm
```

Podobnie zmień adres URL, aby `.../rhel/7/...` wskazać dystrybucję Enterprise Linux 7.

Inny przykład na dystrybucji Ubuntu 14.04:
```bash
wget https://packages.microsoft.com/config/ubuntu/14.04/packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
sudo apt-get update
```

Podobnie zmień adres URL `.../ubuntu/16.04/...` `.../ubuntu/18.04/...` na inną wersję Ubuntu lub odwołujesz się do innej wersji.

### <a name="install-blobfuse"></a>Zainstaluj bluzkę

W dystrybucji Ubuntu/Debian:
```bash
sudo apt-get install blobfuse
```

W przypadku dystrybucji systemu Linux dla przedsiębiorstw:
```bash    
sudo yum install blobfuse
```

## <a name="prepare-for-mounting"></a>Przygotuj się do montażu
Blobfuse zapewnia wydajność natywnej, wymagając tymczasowej ścieżki w systemie plików do buforowania i buforowania wszystkich otwartych plików. Dla tej ścieżki tymczasowej wybierz najbardziej wydajny dysk lub użyj ramdisk, aby uzyskać najlepszą wydajność. 

> [!NOTE]
> Blobfuse przechowuje całą otwartą zawartość pliku w ścieżce tymczasowej. Upewnij się, że ma wystarczająco dużo miejsca, aby pomieścić wszystkie otwarte pliki. 
> 

### <a name="optional-use-a-ramdisk-for-the-temporary-path"></a>(Opcjonalnie) Użyj ramdisk dla ścieżki tymczasowej
Poniższy przykład tworzy ramdisk 16 GB i katalog dla blobfuse. Wybierz rozmiar w zależności od potrzeb. Ten ramdisk pozwala blobfuse otworzyć pliki do 16 GB w rozmiarze. 
```bash
sudo mount -t tmpfs -o size=16g tmpfs /mnt/ramdisk
sudo mkdir /mnt/ramdisk/blobfusetmp
sudo chown <youruser> /mnt/ramdisk/blobfusetmp
```

### <a name="use-an-ssd-as-a-temporary-path"></a>Używanie ssd jako ścieżki tymczasowej
Na platformie Azure można użyć dysków tymczasowych (SSD) dostępnych na maszynach wirtualnych, aby zapewnić bufor o małym opóźnieniu dla blobfuse. W dystrybucjach Ubuntu ten efemeryczny dysk jest zamontowany na '/mnt'. W dystrybucjach Red Hat i CentOS dysk jest zamontowany na '/mnt/resource/'.

Upewnij się, że użytkownik ma dostęp do ścieżki tymczasowej:
```bash
sudo mkdir /mnt/resource/blobfusetmp -p
sudo chown <youruser> /mnt/resource/blobfusetmp
```

### <a name="configure-your-storage-account-credentials"></a>Konfigurowanie poświadczeń konta magazynu
Blobfuse wymaga poświadczeń, które mają być przechowywane w pliku tekstowym w następującym formacie: 

```
accountName myaccount
accountKey storageaccesskey
containerName mycontainer
```
Jest `accountName` to prefiks dla konta magazynu - nie pełny adres URL.

Utwórz ten plik przy użyciu:

```
touch ~/fuse_connection.cfg
```

Po utworzeniu i edycji tego pliku należy ograniczyć dostęp, aby żaden inny użytkownik nie mógł go odczytać.
```bash
chmod 600 fuse_connection.cfg
```

> [!NOTE]
> Jeśli plik konfiguracyjny został utworzony w `dos2unix` systemie Windows, upewnij się, że uruchomiono, aby odkażać i konwertować plik na format Unix. 
>

### <a name="create-an-empty-directory-for-mounting"></a>Tworzenie pustego katalogu do montażu
```bash
mkdir ~/mycontainer
```

## <a name="mount"></a>Instalacja

> [!NOTE]
> Aby uzyskać pełną listę opcji montażu, sprawdź [repozytorium bluzki](https://github.com/Azure/azure-storage-fuse#mount-options).  
> 

Aby zamontować bluzkę, uruchom następujące polecenie z użytkownikiem. To polecenie montuje kontener określony w "/path/to/fuse_connection.cfg" na lokalizacji "/mycontainer".

```bash
sudo blobfuse ~/mycontainer --tmp-path=/mnt/resource/blobfusetmp  --config-file=/path/to/fuse_connection.cfg -o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120
```

Teraz powinieneś mieć dostęp do bloków obiektów blob za pośrednictwem zwykłych interfejsów API systemu plików. Użytkownik, który instaluje katalog jest jedyną osobą, która może uzyskać do niego dostęp, domyślnie, co zabezpiecza dostęp. Aby umożliwić dostęp wszystkim użytkownikom, można ```-o allow_other```zamontować za pomocą opcji . 

```bash
cd ~/mycontainer
mkdir test
echo "hello world" > test/blob.txt
```

## <a name="next-steps"></a>Następne kroki

* [Strona główna bluzki](https://github.com/Azure/azure-storage-fuse#blobfuse)
* [Zgłaszanie problemów z bluzką](https://github.com/Azure/azure-storage-fuse/issues) 

