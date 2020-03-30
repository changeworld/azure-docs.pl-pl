---
title: Kopiowanie danych do usługi Azure Data Lake Storage Gen2 przy użyciu protokołu DistCp| Dokumenty firmy Microsoft
description: Kopiowanie danych do i z usługi Data Lake Storage Gen2 za pomocą narzędzia DistCp
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 3c09a95309e001def306698bbba4f6d0a1a2804d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255537"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen2"></a>Kopiowanie danych między obiektami blob usługi Azure Storage i usługi Azure Data Lake Storage Gen2 za pomocą protokołu DistCp

Za pomocą [protokołu DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) można kopiować dane między kontem magazynu ogólnego przeznaczenia v2 a kontem magazynu ogólnego przeznaczenia v2 z włączoną hierarchiczną przestrzenią nazw. Ten artykuł zawiera instrukcje dotyczące używania narzędzia DistCp.

DistCp udostępnia różne parametry wiersza polecenia i gorąco zachęcamy do przeczytania tego artykułu w celu optymalizacji korzystania z niego. W tym artykule przedstawiono podstawowe funkcje, skupiając się na jego użyciu do kopiowania danych do hierarchicznego konta włączonego obszaru nazw.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Istniejące konto usługi Azure Storage bez funkcji Data Lake Storage Gen2 (hierarchiczny obszar nazw) włączone**.
* **Konto usługi Azure Storage z włączoną funkcją Data Lake Storage Gen2**. Aby uzyskać instrukcje dotyczące tworzenia jednego z nich, zobacz [Tworzenie konta magazynu usługi Azure Data Lake Storage Gen2](data-lake-storage-quickstart-create-account.md)
* **System plików** utworzony na koncie magazynu z włączoną hierarchiczną przestrzenią nazw.
* **Klaster usługi Azure HDInsight** z dostępem do konta magazynu z włączoną funkcją Data Lake Storage Gen2. Zobacz [Korzystanie z usługi Azure Data Lake Storage Gen2 w połączeniu z klastrami usługi Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Upewnij się, że włączysz pulpit zdalny dla klastra.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>Używanie protokołu DistCp z klastra systemu LINUX usługi HDInsight

Klaster HDInsight jest dostarczany z narzędziem DistCp, które może być używane do kopiowania danych z różnych źródeł do klastra HDInsight. Jeśli klaster HDInsight został skonfigurowany do wspólnego używania usługi Azure Blob Storage i usługi Azure Data Lake Storage, narzędzie DistCp może być używane nieszablonowo do kopiowania danych między nimi. W tej sekcji przyjrzymy się, jak korzystać z narzędzia DistCp.

1. Utwórz sesję SSH w klastrze HDI. Zobacz [Łączenie się z klastrem HDInsight opartym na systemie Linux](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

2. Sprawdź, czy można uzyskać dostęp do istniejącego konta ogólnego przeznaczenia v2 (bez włączonej hierarchicznej przestrzeni nazw).

        hdfs dfs –ls wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/

    Dane wyjściowe powinny dostarczyć listę zawartości w kontenerze.

3. Podobnie sprawdź, czy można uzyskać dostęp do konta magazynu z hierarchiczną przestrzenią nazw włączoną z klastra. Uruchom następujące polecenie:

        hdfs dfs -ls abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/

    Dane wyjściowe powinny zapewniać listę plików/folderów na koncie usługi Data Lake Storage.

4. Użyj DistCp, aby skopiować dane z wasb do konta magazynu usługi Data Lake.

        hadoop distcp wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder

    Polecenie kopiuje zawartość **folderu /example/data/gutenberg/** w magazynie obiektów Blob do **/myfolder** na koncie magazynu usługi Data Lake.

5. Podobnie użyj DistCp do kopiowania danych z konta usługi Data Lake Storage do magazynu obiektów Blob (WASB).

        hadoop distcp abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg

    Polecenie kopiuje zawartość **folderu /myfolder** na koncie magazynu Data Lake store do folderu **/example/data/gutenberg/** w folderze WASB.

## <a name="performance-considerations-while-using-distcp"></a>Zagadnienia dotyczące wydajności podczas korzystania z DistCp

Ponieważ najniższa szczegółowość DistCp jest pojedynczym plikiem, ustawienie maksymalnej liczby jednoczesnych kopii jest najważniejszym parametrem optymalizowania go względem magazynu usługi Data Lake. Liczba jednoczesnych kopii jest równa liczbie maperów (**m**) parametru w wierszu polecenia. Ten parametr określa maksymalną liczbę maperów, które są używane do kopiowania danych. Wartość domyślna to 20.

**Przykład**

    hadoop distcp -m 100 wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>Jak określić liczbę maperów do użycia?

Oto kilka użytecznych wskazówek.

* **Krok 1: Określ całkowitą ilość pamięci dostępnej dla "domyślnej" kolejki aplikacji YARN** — pierwszym krokiem jest określenie pamięci dostępnej dla "domyślnej" kolejki aplikacji YARN. Te informacje są dostępne w portalu Ambari skojarzonym z klastrem. Przejdź do YARN i wyświetl kartę Configs, aby wyświetlić pamięć YARN dostępną dla "domyślnej" kolejki aplikacji. Jest to całkowita dostępna pamięć dla zadania DistCp (który jest rzeczywiście MapReduce zadanie).

* **Krok 2: Oblicz liczbę maperów** — wartość **m** jest równa ilorazowi całkowitej pamięci YARN podzielonej przez rozmiar kontenera YARN. Informacje o rozmiarze kontenera YARN są również dostępne w portalu Ambari. Przejdź do pozycji YARN i wyświetl kartę Konfiguracje. Rozmiar kontenera YARN jest wyświetlany w tym oknie. Równanie, które ma osiągnąć liczbę maperów (**m**) jest

        m = (number of nodes * YARN memory for each node) / YARN container size

**Przykład**

Załóżmy, że masz klaster 4x D14v2s i próbujesz przenieść 10 TB danych z 10 różnych folderów. Każdy z folderów zawiera różne ilości danych, a rozmiary plików w każdym folderze są różne.

* **Całkowita pamięć YARN:** Z portalu Ambari można ustalić, że pamięć YARN jest 96 GB dla węzła D14. Tak, całkowita pamięć YARN dla klastra czterech węzłów jest: 

        YARN memory = 4 * 96GB = 384GB

* **Liczba maperów:** Z portalu Ambari można ustalić, że rozmiar kontenera YARN wynosi 3072 MB dla węzła klastra D14. Tak więc liczba maperów to:

        m = (4 nodes * 96GB) / 3072MB = 128 mappers

Jeśli inne aplikacje używają pamięci, można użyć tylko części pamięci YARN klastra dla DistCp.

### <a name="copying-large-datasets"></a>Kopiowanie dużych zestawów danych

Gdy rozmiar zestawu danych do przeniesienia jest duży (na przykład >1 TB) lub jeśli masz wiele różnych folderów, należy rozważyć użycie wielu zadań DistCp. Prawdopodobnie nie ma wzrostu wydajności, ale rozkłada zadania, tak aby w przypadku niepowodzenia dowolnego zadania wystarczy tylko ponowne uruchomienie tego konkretnego zadania, a nie całego zadania.

### <a name="limitations"></a>Ograniczenia

* DistCp próbuje utworzyć maperów, które są podobne rozmiary w celu optymalizacji wydajności. Zwiększenie liczby maperów może nie zawsze zwiększać wydajność.

* DistCp jest ograniczona tylko do jednego mapera na plik. W związku z tym nie powinno mieć więcej maperów niż masz pliki. Ponieważ DistCp można przypisać tylko jeden mapera do pliku, ogranicza to ilość współbieżności, która może służyć do kopiowania dużych plików.

* Jeśli masz niewielką liczbę dużych plików, należy podzielić je na 256 MB fragmentów plików, aby zapewnić większą potencjalną współbieżność.
