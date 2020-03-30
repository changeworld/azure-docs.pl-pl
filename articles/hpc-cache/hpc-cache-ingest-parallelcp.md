---
title: Pozyskiwania danych usługi Azure HPC Cache — skrypt kopiowania równoległego
description: Jak używać skryptu kopiowania równoległego do przenoszenia danych do miejsca docelowego magazynu obiektów Blob w pamięci podręcznej HPC usługi Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 90e05ad3d42b1009b631630fe476669a9f418d33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74166900"
---
# <a name="azure-hpc-cache-data-ingest---parallel-copy-script-method"></a>Pozyskiwania danych usługi Azure HPC Cache — metoda skryptu kopiowania równoległego

Ten artykuł zawiera instrukcje ``parallelcp`` dotyczące tworzenia skryptu i używania go do przenoszenia danych do kontenera magazynu obiektów Blob do użycia z pamięcią podręczną HPC usługi Azure.

Aby dowiedzieć się więcej o przenoszeniu danych do magazynu obiektów Blob dla pamięci podręcznej HPC platformy Azure, przeczytaj artykuł [Przenoszenie danych do magazynu obiektów Blob platformy Azure.](hpc-cache-ingest.md)

## <a name="create-the-parallelcp-script"></a>Tworzenie skryptu parallelcp

Poniższy skrypt doda plik `parallelcp`wykonywalny . (Ten skrypt jest przeznaczony dla Ubuntu; jeśli ``parallel`` używasz innej dystrybucji, należy zainstalować oddzielnie.)

```bash
sudo touch /usr/bin/parallelcp && sudo chmod 755 /usr/bin/parallelcp && sudo sh -c "/bin/cat >/usr/bin/parallelcp" <<EOM
#!/bin/bash

display_usage() {
    echo -e "\nUsage: \$0 SOURCE_DIR DEST_DIR\n"
}

if [  \$# -le 1 ] ; then
    display_usage
    exit 1
fi

if [[ ( \$# == "--help") ||  \$# == "-h" ]] ; then
    display_usage
    exit 0
fi

SOURCE_DIR="\$1"
DEST_DIR="\$2"

if [ ! -d "\$SOURCE_DIR" ] ; then
    echo "Source directory \$SOURCE_DIR does not exist, or is not a directory"
    display_usage
    exit 2
fi

if [ ! -d "\$DEST_DIR" ] && ! mkdir -p \$DEST_DIR ; then
    echo "Destination directory \$DEST_DIR does not exist, or is not a directory"
    display_usage
    exit 2
fi

if [ ! -w "\$DEST_DIR" ] ; then
    echo "Destination directory \$DEST_DIR is not writeable, or is not a directory"
    display_usage
    exit 3
fi

if ! which parallel > /dev/null ; then
    sudo apt-get update && sudo apt install -y parallel
fi

DIRJOBS=225
JOBS=225
find \$SOURCE_DIR -mindepth 1 -type d -print0 | sed -z "s/\$SOURCE_DIR\///" | parallel --will-cite -j\$DIRJOBS -0 "mkdir -p \$DEST_DIR/{}"
find \$SOURCE_DIR -mindepth 1 ! -type d -print0 | sed -z "s/\$SOURCE_DIR\///" | parallel --will-cite -j\$JOBS -0 "cp -P \$SOURCE_DIR/{} \$DEST_DIR/{}"
EOM
```

## <a name="parallel-copy-example"></a>Przykład kopiowania równoległego

W tym przykładzie użyto skryptu kopiowania równoległego do skompilowania ``glibc`` przy użyciu plików źródłowych w pamięci podręcznej HPC platformy Azure.

Pliki źródłowe są buforowane w punkcie instalacji pamięci podręcznej HPC platformy Azure, a pliki obiektów są przechowywane na lokalnym dysku twardym.

W tym przykładzie używa skryptu ``-j`` ``make`` kopiowania równoległego z opcją i uzyskać równoległość.

```bash
sudo apt-get update
sudo apt install -y gcc bison gcc binutils make parallel
cd
wget https://mirrors.kernel.org/gnu/libc/glibc-2.27.tar.bz2
tar jxf glibc-2.27.tar.bz2
ln -s /nfs/cache1 hpccache
time parallelcp glibc-2.27 avere/glibc-2.27
cd
mkdir obj
mkdir usr
cd obj
/home/azureuser/avere/glibc-2.27/configure --prefix=/home/azureuser/usr
time make -j
```
