---
title: Jak zainstalować usługi Azure Blob storage jako systemu plików w systemie Linux | Dokumentacja firmy Microsoft
description: Instalowanie kontenera magazynu obiektów Blob platformy Azure przy użyciu FUSE w systemie Linux
services: storage
author: seguler
ms.service: storage
ms.topic: article
ms.date: 10/11/2018
ms.author: seguler
ms.openlocfilehash: 50378fd7739567b0cc56066168ddd33c3ea14141
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2018
ms.locfileid: "49957058"
---
# <a name="how-to-mount-blob-storage-as-a-file-system-with-blobfuse"></a>Jak zainstalować magazyn obiektów Blob jako systemu plików przy użyciu blobfuse

## <a name="overview"></a>Przegląd
[Blobfuse](https://github.com/Azure/azure-storage-fuse) jest sterownik systemu plików wirtualnych usługi Azure Blob Storage, co pozwala na dostęp do istniejących danych blokowych obiektów blob na koncie magazynu za pomocą systemu plików w systemie Linux. Usługa Azure Blob Storage to usługa magazynu obiektów i dlatego nie jest objęta hierarchicznej przestrzeni nazw. Blobfuse zapewnia tej przestrzeni nazw przy użyciu schematu katalogu wirtualnego przy użyciu zwykły ukośnik "/" jako ogranicznik.  

Ten przewodnik przedstawia sposób użycia blobfuse i zainstaluj kontenera magazynu obiektów Blob dla systemów Linux i dostęp do danych. Aby dowiedzieć się więcej na temat blobfuse, przeczytaj szczegółowe informacje w [repozytorium blobfuse](https://github.com/Azure/azure-storage-fuse).

> [!WARNING]
> Blobfuse nie gwarantuje 100% zgodności POSIX, ponieważ po prostu tłumaczy żądania do [interfejsów API REST usługi Blob](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api). Na przykład operacje zmiany nazwy są niepodzielne w modelu POSIX, ale nie w blobfuse.
> Aby uzyskać pełną listę różnic między macierzysty system plików i blobfuse, odwiedź stronę [repozytorium kodu źródłowego blobfuse](https://github.com/azure/azure-storage-fuse).
> 

## <a name="install-blobfuse-on-linux"></a>Instalowanie blobfuse w systemie Linux
Pliki binarne Blobfuse są dostępne na [repozytoriów oprogramowania firmy Microsoft dla systemu Linux](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software) dystrybucji Ubuntu i RHEL. Aby można było zainstalować blobfuse na tych dystrybucji, należy skonfigurować jedną z repozytoriów, z listy. Możesz również tworzyć pliki binarne z kodu źródłowego, zgodnie z instrukcjami instalacji [tutaj](https://github.com/Azure/azure-storage-fuse/wiki/1.-Installation#option-2---build-from-source) gdy nie są dostępne dla Twojej dystrybucji danych binarnych.

### <a name="configure-the-microsoft-package-repository"></a>Skonfiguruj repozytorium pakietów firmy Microsoft
Konfigurowanie [repozytorium pakietów systemu Linux dla produktów firmy Microsoft](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software).

Na przykład w dystrybucji Enterprise Linux 6:
```bash
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/6/packages-microsoft-prod.rpm
```

Podobnie, Zmień adres url do `.../rhel/7/...` wskaż dystrybucji Enterprise Linux 7.

Inny przykład w systemie Ubuntu 14.04:
```bash
wget https://packages.microsoft.com/config/ubuntu/14.04/packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
sudo apt-get update
```

Podobnie, Zmień adres url do `.../ubuntu/16.04/...` aby wskazywały na dystrybucji Ubuntu 16.04.

### <a name="install-blobfuse"></a>Zainstaluj blobfuse

Ubuntu/Debian dystrybucji:
```bash
sudo apt-get install blobfuse
```

W systemie Linux Enterprise dystrybucji:
```bash
sudo yum install blobfuse
```

## <a name="prepare-for-mounting"></a>Przygotowanie do instalowania
Blobfuse wymaga ścieżka tymczasowa w systemie plików buffer i otwartych plików, co ułatwia zapewnia wydajność przypominającej natywne w pamięci podręcznej. Dla tej ścieżki tymczasowej wybierz większość dysku wydajna, lub użyj dysku ramdisk uzyskać najlepszą wydajność. 

> [!NOTE]
> Blobfuse przechowuje cała zawartość Otwórz plik w tymczasowej ścieżki. Upewnij się, że ma wystarczającej ilości miejsca, aby pomieścić wszystkie otwarte pliki. 
> 

### <a name="optional-use-a-ramdisk-for-the-temporary-path"></a>(Opcjonalnie) Użyj dysku ramdisk dla ścieżka tymczasowa
Poniższy przykład tworzy ramdisk 16 GB, a także tworzenie katalogu dla blobfuse. Wybierz rozmiar, w zależności od potrzeb. Ten dysk RAM umożliwia blobfuse do otwartych plików do 16 GB, rozmiar. 
```bash
sudo mount -t tmpfs -o size=16g tmpfs /mnt/ramdisk
sudo mkdir /mnt/ramdisk/blobfusetmp
sudo chown <youruser> /mnt/ramdisk/blobfusetmp
```

### <a name="use-an-ssd-for-temporary-path"></a>Dyski SSD na użytek ścieżka tymczasowa
Na platformie Azure może użyć efemeryczne dyski (SSD) dostępnych na maszynach wirtualnych, zapewnienie bufora o małych opóźnieniach blobfuse. W dystrybucjach systemu Ubuntu to efemerycznego dysku jest zainstalowana w "/ mnt" podczas, gdy jest zainstalowana w "/ mnt/zasób /" w dystrybucji systemu CentOS i Red Hat.

Upewnij się, że użytkownik ma dostęp do ścieżki tymczasowej:
```bash
sudo mkdir /mnt/resource/blobfusetmp
sudo chown <youruser> /mnt/resource/blobfusetmp
```

### <a name="configure-your-storage-account-credentials"></a>Konfigurowanie poświadczeń konta magazynu
Blobfuse wymaga swoje poświadczenia, które mają być przechowywane w pliku tekstowym w następującym formacie: 

```
accountName myaccount
accountKey storageaccesskey
containerName mycontainer
```

Po utworzeniu tego pliku upewnij się ograniczyć dostęp, dlatego żaden inny użytkownik może go odczytać.
```bash
chmod 700 fuse_connection.cfg
```

> [!NOTE]
> Jeśli utworzono plik konfiguracji na Windows upewnij się uruchomić `dos2unix` oczyszczaj i przekonwertować do formatu Unix. 
>

### <a name="create-an-empty-directory-for-mounting"></a>Utwórz pusty katalog potrzeby instalowania
```bash
mkdir ~/mycontainer
```

## <a name="mount"></a>Instalacji

> [!NOTE]
> Aby uzyskać pełną listę opcji instalacji, sprawdź [repozytorium blobfuse](https://github.com/Azure/azure-storage-fuse#mount-options).  
> 

Z kolei blobfuse instalacji uruchom następujące polecenie z użytkownika. To polecenie instaluje kontenerem określonymi WE "/ path/to/fuse_connection.cfg" do lokalizacji "/ mycontainer".

```bash
blobfuse ~/mycontainer --tmp-path=/mnt/resource/blobfusetmp  --config-file=/path/to/fuse_connection.cfg -o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120
```

Teraz należy dostęp do Twojego blokowych obiektów blob za pośrednictwem interfejsów API systemu zwykły plik. Należy pamiętać, że zainstalowanego katalogu może zostać oceniony jedynie przez użytkownika, instalowania, zabezpieczania dostępu. Jeśli chcesz zezwolić na dostęp do wszystkich użytkowników, można zainstalować za pomocą opcji ```-o allow_other```. 

```bash
cd ~/mycontainer
mkdir test
echo "hello world" > test/blob.txt
```

## <a name="next-steps"></a>Kolejne kroki

* [Strona główna Blobfuse](https://github.com/Azure/azure-storage-fuse#blobfuse)
* [Zgłaszanie problemów blobfuse](https://github.com/Azure/azure-storage-fuse/issues) 

