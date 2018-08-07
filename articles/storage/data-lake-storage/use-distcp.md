---
title: Kopiowanie danych do usługi Azure Data Lake Gen2 — wersja zapoznawcza korzystanie z narzędzia Distcp | Dokumentacja firmy Microsoft
description: Kopiowanie danych do i z Data Lake Gen2 — wersja zapoznawcza za pomocą narzędzia Distcp
services: storage
author: seguler
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 06/27/2018
ms.author: seguler
ms.openlocfilehash: 065c4c4315bda209484cc1b2449980e55d4ac798
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2018
ms.locfileid: "39522700"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-data-lake-storage-gen2-preview"></a>Za pomocą narzędzia Distcp do skopiowania danych między Azure Storage Blobs a Data Lake Gen2 — wersja zapoznawcza

Jeśli masz klaster usługi HDInsight z dostępem do usługi Azure Data Lake Gen2 — wersja zapoznawcza, można użyć narzędzi ekosystemu platformy Hadoop, takich jak [Distcp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) do kopiowania danych **do i z** magazynu klastra HDInsight (WASB) do danych Gen2 magazynu Lake zdolne do konta. Ten artykuł zawiera instrukcje na temat za pomocą narzędzia Distcp.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Konto usługi Azure Storage z włączoną funkcją usługi Azure Data Lake Storage (wersja zapoznawcza)**. Aby uzyskać instrukcje na temat jej tworzenia, zobacz [Tworzenie konta magazynu Azure Data Lake Gen2 — wersja zapoznawcza](quickstart-create-account.md)
* **Klaster usługi Azure HDInsight** dzięki dostępowi do konta usługi Data Lake Storage. Zobacz [użycia usługi Azure Data Lake magazynu Gen2 za pomocą usługi Azure HDInsight clusters](use-hdi-cluster.md). Upewnij się, że włączenie pulpitu zdalnego dla klastra.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>Z klastra usługi HDInsight w systemie Linux za pomocą narzędzia Distcp

Klaster usługi HDInsight jest dostarczany za pomocą narzędzia Distcp, która może służyć do skopiowania danych z różnych źródeł na klastrze usługi HDInsight. Po skonfigurowaniu klastra HDInsight i korzystanie z usługi Azure Blob Storage i Azure Data Lake Storage razem z narzędzia Distcp mogą być używane out-of--box można również kopiować dane między. W tej sekcji przyjrzymy się sposób korzystania z narzędzia Distcp.

1. Z poziomu pulpitu za pomocą protokołu SSH Połącz się z klastrem. Zobacz [łączenie z klastrem HDInsight opartych na systemie Linux](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md). Uruchom polecenia w wierszu polecenia SSH.

2. Sprawdź, czy można uzyskać dostęp do obiektów blob magazynu Azure (WASB). Uruchom następujące polecenie:

        hdfs dfs –ls wasb://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/

    Dane wyjściowe powinny dostarczyć listę zawartości obiektu blob magazynu.

3. Podobnie należy sprawdzić, czy są dostępne na koncie usługi Data Lake Storage klastra. Uruchom następujące polecenie:

        hdfs dfs -ls abfs://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/

    Dane wyjściowe powinny dostarczyć listę plików/folderów na koncie usługi Data Lake Storage.

4. Kopiowanie danych z WASB do konta usługi Data Lake Storage za pomocą narzędzia Distcp

        hadoop distcp wasb://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg abfs://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder

    Polecenie kopiuje zawartość **/przykład/data/gutenberg/** folderu w usłudze Blob storage do **/myfolder** na koncie usługi Data Lake Storage.

5. Podobnie za pomocą narzędzia Distcp do skopiowania danych z konta usługi Data Lake Storage do obiektu Blob Storage (WASB).

        hadoop distcp abfs://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder wasb://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg

    Polecenie kopiuje zawartość **/myfolder** w ramach konta Data Lake Store, aby **/przykład/data/gutenberg/** folderu w WASB.

## <a name="performance-considerations-while-using-distcp"></a>Zagadnienia dotyczące wydajności podczas korzystania z narzędzia DistCp

Ponieważ firmy DistCp najniższy poziom szczegółowości jest pojedynczy plik, ustawienie maksymalną liczbę jednoczesnych kopii jest najważniejszym parametr Zoptymalizuj go pod względem magazynu usługi Data Lake. Liczbę jednoczesnych kopii jest kontrolowany przez ustawienie liczby liczby maperów (**m**) parametr w wierszu polecenia. Ten parametr określa maksymalną liczbę liczby maperów, które są używane do kopiowania danych. Wartość domyślna to 20.

**Przykład**

    hadoop distcp wasb://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg abfs://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder -m 100

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>Jak określić liczbę liczby maperów używać?

Oto kilka użytecznych wskazówek.

* **Krok 1: Określanie całkowity rozmiar pamięci usługi YARN** -pierwszym krokiem jest określenie dostępnej do klastra, w którym jest uruchamiane zadanie DistCp pamięci usługi YARN. Te informacje są dostępne w portalu narzędzia Ambari skojarzonego z klastrem. Przejdź do usługi YARN i wyświetlić kartę konfiguracje, aby wyświetlić pamięci usługi YARN. Aby uzyskać całkowitej ilości pamięci usługi YARN, należy pomnożyć pamięci usługi YARN na węzeł z liczbą węzłów, które znajdują się w klastrze.

* **Krok 2: Oblicz liczbę liczby maperów** — wartość **m** jest taki sam, jak iloraz całkowity rozmiar pamięci usługi YARN podzielonej przez rozmiar kontenera YARN. Informacje o rozmiarze kontenera YARN jest dostępna w witrynie portal Ambari. Przejdź do usługi YARN i wyświetlić kartę konfiguracje. Rozmiar kontenera YARN jest wyświetlany w tym oknie. Równanie na liczbę liczby maperów (**m**) jest

        m = (number of nodes * YARN memory for each node) / YARN container size

**Przykład**

Załóżmy, że masz 4 węzły D14v2s w klastrze, a chcesz przetransferować 10 TB danych z 10 różnych folderach. Zawiera foldery różnej ilości danych i rozmiary plików w ramach każdego folderu są różne.

* **Łączna pamięć YARN**: portal z Ambari, możesz określić, że pamięci usługi YARN jest 96 GB dla węzła D14. Dlatego jest całkowity rozmiar pamięci usługi YARN dla cztery węzły klastra: 

        YARN memory = 4 * 96GB = 384GB

* **Liczba liczby maperów**: portal z Ambari, możesz określić, czy rozmiar kontenera YARN jest 3072 dla węzłów klastra D14. Dlatego jest liczba liczby maperów:

        m = (4 nodes * 96GB) / 3072MB = 128 mappers

Jeśli inne aplikacje używają pamięci, można wybrać do użycia tylko część pamięci usługi YARN z klastra dla narzędzia DistCp.

### <a name="copying-large-datasets"></a>Kopiowanie dużych zestawów danych

Gdy jest duży rozmiar zestawu danych do przeniesienia (na przykład > 1 TB) lub jeśli masz wiele różnych folderach, należy rozważyć użycie wielu zadań DistCp. Prawdopodobnie nie są bardziej wydajne, ale rozprzestrzenia się zadania tak, aby w przypadku awarii dowolnego zadania, należy ponownie uruchomić określonego zadania, a nie całego zadania.

### <a name="limitations"></a>Ograniczenia

* Narzędzia DistCp próbuje utworzyć liczby maperów mających podobny do rozmiaru w celu zoptymalizowania wydajności. Zwiększenie liczby maperów może nie zawsze powoduje zwiększenie wydajności.

* Narzędzia DistCp jest ograniczona do tylko jednego mapowania na plik. W związku z tym nie powinna mieć więcej liczby maperów nie znajdują się pliki. Ponieważ DistCp można przypisać tylko jednego mapowania do pliku, ogranicza to ilość współbieżności, który może służyć do kopiowania dużych plików.

* W przypadku niewielkiej liczby dużych plików, następnie należy podzielić je na fragmenty plików 256 MB, które pozwalają uzyskać większą współbieżność potencjalnych. 
