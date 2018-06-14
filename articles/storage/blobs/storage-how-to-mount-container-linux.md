---
title: Jak zainstalować magazyn obiektów Blob platformy Azure w systemie plików w systemie Linux | Dokumentacja firmy Microsoft
description: Zainstaluj kontenera magazynu obiektów Blob platformy Azure z BEZPIECZNIK w systemie Linux
services: storage
documentationcenter: linux
author: seguler
manager: jahogg
ms.service: storage
ms.devlang: bash
ms.topic: article
ms.date: 05/10/2018
ms.author: seguler
ms.openlocfilehash: 1098eef15b559c30ef436d8e13bbe02bddb78649
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
ms.locfileid: "34072096"
---
# <a name="how-to-mount-blob-storage-as-a-file-system-with-blobfuse"></a>Jak zainstalować magazyn obiektów Blob jako systemu plików z blobfuse

## <a name="overview"></a>Przegląd
[Blobfuse](https://github.com/Azure/azure-storage-fuse) jest sterownik system plików wirtualnych dla magazynu obiektów Blob Azure, dzięki czemu można uzyskać dostępu do istniejących danych blokowych obiektów blob na koncie magazynu za pomocą systemu plików w systemie Linux. Magazyn obiektów Blob Azure jest usługą obiektu magazynu i w związku z tym nie ma hierarchicznej przestrzeni nazw. Blobfuse zapewnia tej przestrzeni nazw przy użyciu schematu katalogu wirtualnego z użyciem zwykły ukośnik "/" jako ogranicznik.  

W tym przewodniku przedstawiono sposób używania blobfuse i zainstalować kontenera magazynu obiektów Blob na systemu Linux i dostępu do danych. Aby dowiedzieć się więcej na temat blobfuse, przeczytaj szczegółowe informacje w [repozytorium blobfuse](https://github.com/Azure/azure-storage-fuse).

> [!WARNING]
> Blobfuse nie gwarantuje 100% zgodności POSIX, jak po prostu tłumaczy żądań do [interfejsów API REST Blob](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api). Na przykład niepodzielnych POSIX, ale nie w blobfuse są operacje zmiany nazwy.
> Aby uzyskać pełną listę różnic między macierzysty system plików i blobfuse, odwiedź stronę [repozytorium kodu źródłowego blobfuse](https://github.com/azure/azure-storage-fuse).
> 

## <a name="install-blobfuse-on-linux"></a>Zainstaluj blobfuse w systemie Linux
Pliki binarne Blobfuse są dostępne na [repozytoria oprogramowania firmy Microsoft dla systemu Linux](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software). Aby można było zainstalować blobfuse, należy skonfigurować jedno z tych repozytoriów.

### <a name="configure-the-microsoft-package-repository"></a>Konfigurowanie repozytorium pakietów Microsoft
Skonfiguruj [repozytorium pakietów systemu Linux dla produktów firmy Microsoft](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software).

Na przykład w dystrybucji Enterprise Linux 6:
```bash
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/6/packages-microsoft-prod.rpm
```

Podobnie, Zmień adres url do `.../rhel/7/...` wskaż dystrybucji Enterprise Linux 7.

Innym przykładem na Ubuntu 14.04:
```bash
wget https://packages.microsoft.com/config/ubuntu/14.04/packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
sudo apt-get update
```

Podobnie, Zmień adres url do `.../ubuntu/16.04/...` wskaż Ubuntu 16.04 dystrybucji.

### <a name="install-blobfuse"></a>Zainstaluj blobfuse

W dystrybucji Ubuntu/Debian:
```bash
sudo apt-get install blobfuse
```

W dystrybucji Enterprise Linux:
```bash
sudo yum install blobfuse
```

## <a name="prepare-for-mounting"></a>Przygotowanie do zamontowania
Blobfuse wymaga ścieżka tymczasowa w systemie plików do buforu i pamięci podręcznej wszystkie otwarte pliki, co pomaga zapewnia wydajność przypominającej native. Dla tej ścieżki tymczasowej wybierz większości wydajności dysku, lub użyj ramdisk najlepszą wydajność. 

> [!NOTE]
> Blobfuse przechowuje całą zawartość Otwórz plik w tymczasowej ścieżki. Upewnij się, że jest za mało miejsca, aby uwzględnić wszystkie otwarte pliki. 
> 

### <a name="optional-use-a-ramdisk-for-the-temporary-path"></a>(Opcjonalnie) Użyj ramdisk ścieżki tymczasowej
Poniższy przykład tworzy ramdisk 16 GB, a także tworzenie katalogu dla blobfuse. Wybierz rozmiar na podstawie Twoich potrzeb. Ta ramdisk umożliwia blobfuse do otwarte pliki 16 GB rozmiar. 
```bash
sudo mount -t tmpfs -o size=16g tmpfs /mnt/ramdisk
sudo mkdir /mnt/ramdisk/blobfusetmp
sudo chown <youruser> /mnt/ramdisk/blobfusetmp
```

### <a name="use-an-ssd-for-temporary-path"></a>Użyj SSD ścieżki tymczasowej
Na platformie Azure może użyć tymczasowych (SSD) dostępnych dysków na maszyny wirtualne zapewnienie bufora małych opóźnieniach blobfuse. W dystrybucji Ubuntu ten dysk tymczasowych jest zainstalowany na "/ mnt" podczas, gdy jest zainstalowany na "/ mnt/zasobów /' w dystrybucji RedHat i CentOS.

Upewnij się, że użytkownik ma dostęp do ścieżka tymczasowa:
```bash
sudo mkdir /mnt/resource/blobfusetmp
sudo chown <youruser> /mnt/resource/blobfusetmp
```

### <a name="configure-your-storage-account-credentials"></a>Skonfiguruj poświadczenia konta magazynu
Blobfuse wymaga poświadczeń, aby były przechowywane w pliku tekstowym w następującym formacie: 

```
accountName myaccount
accountKey myaccesskey==
containerName mycontainer
```

Po utworzeniu tego pliku, upewnij się, że ograniczenia dostępu, tak aby żaden inny użytkownik może go odczytać.
```bash
chmod 700 fuse_connection.cfg
```

### <a name="create-an-empty-directory-for-mounting"></a>Utwórz pusty katalog do zamontowania
```bash
mkdir ~/mycontainer
```

## <a name="mount"></a>instalacji

> [!NOTE]
> Aby zapoznać się z pełną listą opcje instalacji, sprawdź [repozytorium blobfuse](https://github.com/Azure/azure-storage-fuse#mount-options).  
> 

W celu instalacji blobfuse uruchom następujące polecenie z użytkownika. To polecenie instaluje kontener określony w "/ path/to/fuse_connection.cfg" na lokalizację "/ mojkontener".

```bash
blobfuse ~/mycontainer --tmp-path=/mnt/resource/blobfusetmp  --config-file=/path/to/fuse_connection.cfg -o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120
```

Teraz powinien mieć dostęp do Twojego blokowych obiektów blob za pośrednictwem interfejsów API systemu zwykły plik. Należy pamiętać, że zainstalowanego katalogu jest możliwy tylko przez użytkownika, instalowania, zabezpieczania dostępu. Jeśli chcesz zezwolić na dostęp do wszystkich użytkowników, można zainstalować przy użyciu opcji ```-o allow_other```. 

```bash
cd ~/mycontainer
mkdir test
echo "hello world" > test/blob.txt
```

## <a name="next-steps"></a>Kolejne kroki

* [Strona główna Blobfuse](https://github.com/Azure/azure-storage-fuse#blobfuse)
* [Raport blobfuse problemów](https://github.com/Azure/azure-storage-fuse/issues) 

