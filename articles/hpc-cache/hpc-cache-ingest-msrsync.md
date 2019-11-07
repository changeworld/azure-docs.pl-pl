---
title: Pobieranie danych z pamięci podręcznej platformy Azure HPC — msrsync
description: Jak używać msrsync do przenoszenia danych do docelowego magazynu obiektów BLOB w pamięci podręcznej platformy Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 3e5937a036763fab57f9e37494ace33e8452b1f2
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582273"
---
# <a name="azure-hpc-cache-data-ingest---msrsync-method"></a>Pobieranie danych z pamięci podręcznej platformy Azure HPC — Metoda msrsync

Ten artykuł zawiera szczegółowe instrukcje dotyczące używania narzędzia ``msrsync`` do kopiowania danych do kontenera usługi Azure Blob Storage w celu użycia z pamięcią podręczną Azure HPC.

Aby dowiedzieć się więcej na temat przenoszenia danych do magazynu obiektów BLOB w pamięci podręcznej platformy Azure HPC, przeczytaj artykuł [przenoszenie danych do usługi Azure Blob Storage dla pamięci podręcznej Azure HPC](hpc-cache-ingest.md).

Narzędzie ``msrsync`` może służyć do przenoszenia danych do docelowego magazynu zaplecza dla pamięci podręcznej platformy Azure HPC. To narzędzie służy do optymalizowania użycia przepustowości przez uruchomienie wielu równoległych procesów ``rsync``. Jest on dostępny w witrynie GitHub w https://github.com/jbd/msrsync.

``msrsync`` dzieli Katalog źródłowy na oddzielne "zasobniki", a następnie uruchamia poszczególne procesy ``rsync`` w każdym przedziale.

Testowanie wstępne przy użyciu maszyny wirtualnej z czterema rdzeniami wykazało najlepszą wydajność podczas korzystania z 64 procesów. Użyj opcji ``msrsync`` ``-p``, aby ustawić liczbę procesów na 64.

Należy pamiętać, że ``msrsync`` mogą zapisywać tylko woluminy lokalne i z nich. Źródło i miejsce docelowe muszą być dostępne jako instalacje lokalne na stacji roboczej używanej do wydawania polecenia.

Postępuj zgodnie z poniższymi instrukcjami, aby użyć ``msrsync`` do wypełniania usługi Azure Blob Storage za pomocą usługi Azure HPC cache:

1. Zainstaluj ``msrsync`` i jej wymagania wstępne (``rsync`` i Python 2,6 lub nowsze)
1. Określ łączną liczbę plików i katalogów, które mają zostać skopiowane.

   Na przykład użyj ``prime.py`` narzędzia z argumentami ```prime.py --directory /path/to/some/directory``` (dostępne przez pobranie <https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py>).

   Jeśli nie korzystasz z ``prime.py``, możesz obliczyć liczbę elementów za pomocą narzędzia GNU ``find`` w następujący sposób:

   ```bash
   find <path> -type f |wc -l         # (counts files)
   find <path> -type d |wc -l         # (counts directories)
   find <path> |wc -l                 # (counts both)
   ```

1. Podziel liczbę elementów na 64, aby określić liczbę elementów w procesie. Użyj tej liczby z opcją ``-f``, aby ustawić rozmiar zasobników po uruchomieniu polecenia.

1. Wydaj polecenie ``msrsync``, aby skopiować pliki:

   ```bash
   msrsync -P --stats -p64 -f<ITEMS_DIV_64> --rsync "-ahv --inplace" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Na przykład to polecenie jest przeznaczone do przenoszenia plików 11 000 w procesach 64 z/test/Source-Repository do/mnt/hpccache/Repository:

   ``mrsync -P --stats -p64 -f170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/hpccache/repository``
