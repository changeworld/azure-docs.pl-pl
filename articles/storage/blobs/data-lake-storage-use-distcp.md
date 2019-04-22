---
title: Kopiowanie danych do usługi Azure Data Lake Storage Gen2 korzystanie z narzędzia DistCp | Dokumentacja firmy Microsoft
description: Kopiowanie danych do i z Data Lake Storage Gen2 za pomocą narzędzia DistCp
services: storage
author: seguler
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: seguler
ms.openlocfilehash: 3b58dc8dabc55ba428ce6e35091a6947e5f4a824
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678390"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen2"></a>Kopiowanie danych między obiektów blob usługi Azure Storage i Azure Data Lake Storage Gen2 za pomocą narzędzia DistCp

Możesz użyć [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) do skopiowania danych między kontem magazynu ogólnego przeznaczenia w wersji 2 i konto magazynu ogólnego przeznaczenia w wersji 2 z hierarchicznej przestrzeni nazw, włączone. Ten artykuł zawiera instrukcje na temat za pomocą narzędzia DistCp.

Narzędzia DistCp oferuje szereg parametrów wiersza polecenia i zdecydowanie zachęcamy do przeczytaj ten artykuł, aby zoptymalizować użycie go. W tym artykule przedstawiono podstawowe funkcje podczas koncentrujących się na użytek kopiowanie danych z kontem hierarchicznej przestrzeni nazw, włączone.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Istniejące konto magazynu platformy Azure bez Data Lake Storage Gen2 możliwości (hierarchicznej przestrzeni nazw)**.
* **Konto usługi Azure Storage z włączoną funkcją Data Lake Storage Gen2**. Aby uzyskać instrukcje na temat jej tworzenia, zobacz [Tworzenie konta magazynu Azure Data Lake Storage Gen2](data-lake-storage-quickstart-create-account.md)
* **System plików** , została utworzona w ramach konta magazynu z hierarchicznej przestrzeni nazw, włączone.
* **Klaster usługi Azure HDInsight** dzięki dostępowi do konta magazynu z magazynu Gen2 jeziora danych, włączone. Zobacz [Korzystanie z usługi Azure Data Lake Storage Gen2 w połączeniu z klastrami usługi Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Upewnij się, że włączenie pulpitu zdalnego dla klastra.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>Z klastra usługi HDInsight w systemie Linux za pomocą narzędzia DistCp

Klaster usługi HDInsight jest dostarczany za pomocą narzędzia DistCp, która może służyć do skopiowania danych z różnych źródeł na klastrze usługi HDInsight. Po skonfigurowaniu klastra HDInsight i korzystanie z usługi Azure Blob Storage i Azure Data Lake Storage razem z narzędzia DistCp mogą być używane out-of--box można również kopiować dane między. W tej sekcji przyjrzymy się sposób korzystania z narzędzia DistCp.

1. Utwórz sesję SSH do klastra usługi HDI. Zobacz [łączenie z klastrem HDInsight opartych na systemie Linux](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

2. Sprawdź, czy może uzyskiwać dostęp z ogólnego przeznaczenia istniejące konto w wersji 2 (bez włączone hierarchicznej przestrzeni nazw).

        hdfs dfs –ls wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/

    Dane wyjściowe powinny dostarczyć spis treści w kontenerze.

3. Podobnie należy sprawdzić, czy może uzyskiwać dostęp na koncie magazynu przy użyciu hierarchicznej przestrzeni nazw, można włączyć do klastra. Uruchom następujące polecenie:

        hdfs dfs -ls abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/

    Dane wyjściowe powinny dostarczyć listę plików/folderów na koncie usługi Data Lake Storage.

4. Kopiowanie danych z WASB do konta usługi Data Lake Storage za pomocą narzędzia DistCp

        hadoop distcp wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder

    Polecenie kopiuje zawartość **/przykład/data/gutenberg/** folderu w usłudze Blob storage do **/myfolder** na koncie usługi Data Lake Storage.

5. Podobnie za pomocą narzędzia DistCp do skopiowania danych z konta usługi Data Lake Storage do obiektu Blob Storage (WASB).

        hadoop distcp abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg

    Polecenie kopiuje zawartość **/myfolder** w ramach konta Data Lake Store, aby **/przykład/data/gutenberg/** folderu w WASB.

## <a name="performance-considerations-while-using-distcp"></a>Zagadnienia dotyczące wydajności podczas korzystania z narzędzia DistCp

Ponieważ firmy DistCp najniższy poziom szczegółowości jest pojedynczy plik, ustawienie maksymalną liczbę jednoczesnych kopii jest najważniejszym parametr Zoptymalizuj go pod względem magazynu usługi Data Lake. Liczbę jednoczesnych kopii jest równa liczbie liczby maperów (**m**) parametr w wierszu polecenia. Ten parametr określa maksymalną liczbę liczby maperów, które są używane do kopiowania danych. Wartość domyślna to 20.

**Przykład**

    hadoop distcp wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder -m 100

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>Jak określić liczbę liczby maperów używać?

Oto kilka użytecznych wskazówek.

* **Krok 1. Określić całkowitej ilości pamięci, które są dostępne w kolejce aplikacji usługi YARN "default"** -pierwszym krokiem jest określenie ilość pamięci dostępnej w kolejce aplikacji usługi YARN "default". Te informacje są dostępne w portalu narzędzia Ambari skojarzonego z klastrem. Przejdź do usługi YARN i wyświetlić kartę konfiguracje, aby wyświetlić pamięci usługi YARN w kolejce aplikacji "default". Jest to łączna ilość dostępnej pamięci dla zadania narzędzia DistCp (który jest faktycznie zadania MapReduce).

* **Krok 2. Oblicz liczbę liczby maperów** — wartość **m** jest taki sam, jak iloraz całkowity rozmiar pamięci usługi YARN podzielonej przez rozmiar kontenera YARN. Informacje o rozmiarze kontenera YARN jest dostępna w witrynie portal Ambari. Przejdź do usługi YARN i wyświetlić kartę konfiguracje. Rozmiar kontenera YARN jest wyświetlany w tym oknie. Równanie na liczbę liczby maperów (**m**) jest

        m = (number of nodes * YARN memory for each node) / YARN container size

**Przykład**

Załóżmy, że klaster x D14v2s 4 i chcesz przenosić 10 TB danych na 10 różnych folderach. Zawiera foldery różnej ilości danych i rozmiary plików w ramach każdego folderu są różne.

* **Łączna pamięć YARN**: Z poziomu portalu Ambari należy określić, że pamięci usługi YARN jest 96 GB dla węzła D14. Dlatego jest całkowity rozmiar pamięci usługi YARN dla cztery węzły klastra: 

        YARN memory = 4 * 96GB = 384GB

* **Liczba liczby maperów**: Z poziomu portalu Ambari należy określić, że rozmiar kontenera YARN jest 3,072 MB dla węzłów klastra D14. Dlatego jest liczba liczby maperów:

        m = (4 nodes * 96GB) / 3072MB = 128 mappers

Jeśli inne aplikacje używają pamięci, można wybrać do użycia tylko część pamięci usługi YARN z klastra dla narzędzia DistCp.

### <a name="copying-large-datasets"></a>Kopiowanie dużych zestawów danych

Gdy jest duży rozmiar zestawu danych do przeniesienia (na przykład > 1 TB) lub jeśli masz wiele różnych folderach, należy rozważyć użycie wielu zadań DistCp. Prawdopodobnie nie są bardziej wydajne, ale rozprzestrzenia się zadania tak, aby w przypadku awarii dowolnego zadania, należy ponownie uruchomić określonego zadania, a nie całego zadania.

### <a name="limitations"></a>Ograniczenia

* Narzędzia DistCp próbuje utworzyć liczby maperów mających podobny do rozmiaru w celu zoptymalizowania wydajności. Zwiększenie liczby maperów może nie zawsze powoduje zwiększenie wydajności.

* Narzędzia DistCp jest ograniczona do tylko jednego mapowania na plik. W związku z tym nie powinna mieć więcej liczby maperów nie znajdują się pliki. Ponieważ DistCp można przypisać tylko jednego mapowania do pliku, ogranicza to ilość współbieżności, który może służyć do kopiowania dużych plików.

* W przypadku niewielkiej liczby dużych plików, następnie należy podzielić je na fragmenty plików 256 MB, które pozwalają uzyskać większą współbieżność potencjalnych.
