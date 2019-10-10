---
title: Pozyskiwanie danych w pamięci podręcznej platformy Azure HPC — msrsync
description: Jak używać msrsync do przenoszenia danych do docelowego magazynu obiektów BLOB w pamięci podręcznej platformy Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: rohogue
ms.openlocfilehash: 6eac6c367be42021a4654f85c8f4ec980c9f6925
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255284"
---
# <a name="azure-hpc-cache-preview-data-ingest---msrsync-method"></a>Azure HPC cache (wersja zapoznawcza) Metoda pozyskiwania danych — msrsync

Ten artykuł zawiera szczegółowe instrukcje dotyczące korzystania z narzędzia ``msrsync`` do kopiowania danych do kontenera usługi Azure Blob Storage w celu użycia z pamięcią podręczną platformy Azure HPC.

Aby dowiedzieć się więcej na temat przenoszenia danych do magazynu obiektów BLOB w pamięci podręcznej platformy Azure HPC, przeczytaj artykuł [przenoszenie danych do usługi Azure Blob Storage dla pamięci podręcznej Azure HPC](hpc-cache-ingest.md).

Narzędzie ``msrsync`` może służyć do przenoszenia danych do docelowego magazynu zaplecza dla pamięci podręcznej platformy Azure HPC. To narzędzie służy do optymalizowania użycia przepustowości przez uruchomienie wielu równoległych procesów ``rsync``. Jest on dostępny w witrynie GitHub pod adresem https://github.com/jbd/msrsync.

``msrsync`` dzieli Katalog źródłowy na oddzielne "zasobniki", a następnie uruchamia poszczególne procesy ``rsync`` dla każdego przedziału.

Testowanie wstępne przy użyciu maszyny wirtualnej z czterema rdzeniami wykazało najlepszą wydajność podczas korzystania z 64 procesów. Użyj opcji ``msrsync`` ``-p``, aby ustawić liczbę procesów na 64.

Należy pamiętać, że ``msrsync`` może zapisywać tylko na woluminach lokalnych i z nich. Źródło i miejsce docelowe muszą być dostępne jako instalacje lokalne na stacji roboczej używanej do wydawania polecenia.

Postępuj zgodnie z poniższymi instrukcjami, aby użyć ``msrsync`` do wypełniania usługi Azure Blob Storage za pomocą usługi Azure HPC cache:

1. Zainstaluj ``msrsync`` i jego wymagania wstępne (``rsync`` i Python 2,6 lub nowsze)
1. Określ łączną liczbę plików i katalogów, które mają zostać skopiowane.

   Na przykład użyj narzędzia ``prime.py`` z argumentami ```prime.py --directory /path/to/some/directory``` (dostępne przez pobranie <https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py>).

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
