---
title: Kopiowanie danych do i z WASB do usługi Azure Data Lake Storage Gen1 korzystanie z narzędzia Distcp | Dokumentacja firmy Microsoft
description: Kopiowanie danych do i z obiektów blob usługi Azure Storage do usługi Azure Data Lake Storage Gen1 za pomocą narzędzia Distcp
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: ae2e9506-69dd-4b95-8759-4dadca37ea70
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: fbefe233ce0d2477982faf0a9f38a73062e0c7a1
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58884469"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen1"></a>Kopiowanie danych między obiektów blob usługi Azure Storage i Azure Data Lake Storage Gen1 za pomocą narzędzia Distcp
> [!div class="op_single_selector"]
> * [Korzystanie z narzędzia DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [Korzystanie z narzędzia AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Jeśli masz klaster usługi HDInsight z dostępem do usługi Azure Data Lake Storage Gen1, można użyć narzędzi ekosystemu platformy Hadoop, takich jak narzędzia Distcp do skopiowania danych **do i z** magazynu klastra HDInsight (WASB) do konta Data Lake Storage Gen1. Ten artykuł zawiera instrukcje na temat za pomocą narzędzia Distcp.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Konto usługi Azure Data Lake Storage Gen1**. Aby uzyskać instrukcje na temat jej tworzenia, zobacz [Rozpoczynanie pracy z usługą Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Klaster usługi Azure HDInsight** dzięki dostępowi do konta Data Lake Storage Gen1. Zobacz [Tworzenie klastra HDInsight z usługą Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Upewnij się, że włączenie pulpitu zdalnego dla klastra.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>Z klastra usługi HDInsight w systemie Linux za pomocą narzędzia Distcp

Klaster usługi HDInsight jest dostarczany za pomocą narzędzia Distcp, która może służyć do skopiowania danych z różnych źródeł na klastrze usługi HDInsight. Po skonfigurowaniu klastra HDInsight w Data Lake Storage Gen1 jako magazyn dodatkowy z narzędzia Distcp może być używany out-of box można skopiować danych do i z z konta Data Lake Storage Gen1. W tej sekcji przyjrzymy się sposób korzystania z narzędzia Distcp.

1. Z poziomu pulpitu za pomocą protokołu SSH Połącz się z klastrem. Zobacz [łączenie z klastrem HDInsight opartych na systemie Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md). Uruchom polecenia w wierszu polecenia SSH.

2. Sprawdź, czy można uzyskać dostęp do obiektów blob magazynu Azure (WASB). Uruchom następujące polecenie:

        hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/

    Dane wyjściowe powinny dostarczyć listę zawartości obiektu blob magazynu.

3. Podobnie należy sprawdzić, czy można uzyskać dostępu do konta Data Lake Storage Gen1 z klastra. Uruchom następujące polecenie:

        hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/

    Dane wyjściowe powinny dostarczyć listę plików/folderów, w ramach konta Data Lake Storage Gen1.

4. Kopiowanie danych z WASB do konta Data Lake Storage Gen1 za pomocą narzędzia Distcp

        hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder

    Polecenie kopiuje zawartość **/przykład/data/gutenberg/** folderu w WASB do **/myfolder** w ramach konta Data Lake Storage Gen1.

5. Podobnie za pomocą narzędzia Distcp kopiowanie danych z konta Data Lake Storage Gen1 do WASB.

        hadoop distcp adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg

    Polecenie kopiuje zawartość **/myfolder** w ramach konta Data Lake Storage Gen1 do **/przykład/data/gutenberg/** folderu w WASB.

## <a name="performance-considerations-while-using-distcp"></a>Zagadnienia dotyczące wydajności podczas korzystania z narzędzia DistCp

Ponieważ firmy DistCp najniższy poziom szczegółowości jest pojedynczy plik, ustawienie maksymalną liczbę jednoczesnych kopii jest najważniejszym parametr Zoptymalizuj go pod względem Data Lake Storage Gen1. Liczbę jednoczesnych kopii jest kontrolowany przez ustawienie liczby liczby maperów ('M ') parametr w wierszu polecenia. Ten parametr określa maksymalną liczbę liczby maperów, które są używane do kopiowania danych. Wartość domyślna to 20.

**Przykład**

    hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder -m 100

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>Jak określić liczbę liczby maperów używać?

Oto kilka użytecznych wskazówek.

* **Krok 1. Całkowity rozmiar pamięci usługi YARN określić** -pierwszym krokiem jest określenie dostępnej do klastra, w którym jest uruchamiane zadanie DistCp pamięci usługi YARN. Te informacje są dostępne w portalu narzędzia Ambari skojarzonego z klastrem. Przejdź do usługi YARN i wyświetlić kartę konfiguracje, aby wyświetlić pamięci usługi YARN. Aby uzyskać całkowitej ilości pamięci usługi YARN, należy pomnożyć pamięci usługi YARN na węzeł z liczbą węzłów, które znajdują się w klastrze.

* **Krok 2. Oblicz liczbę liczby maperów** — wartość **m** jest taki sam, jak iloraz całkowity rozmiar pamięci usługi YARN podzielonej przez rozmiar kontenera YARN. Informacje o rozmiarze kontenera YARN jest dostępna w witrynie portal Ambari. Przejdź do usługi YARN i wyświetlić kartę konfiguracje. Rozmiar kontenera YARN jest wyświetlany w tym oknie. Równanie na liczbę liczby maperów (**m**) jest

        m = (number of nodes * YARN memory for each node) / YARN container size

**Przykład**

Załóżmy, że masz 4 węzły D14v2s w klastrze, a chcesz przetransferować 10 TB danych z 10 różnych folderach. Zawiera foldery różnej ilości danych i rozmiary plików w ramach każdego folderu są różne.

* Łączna liczba pamięci usługi YARN — portalu z Ambari stwierdzisz, że pamięci usługi YARN jest 96 GB dla węzła D14. Dlatego jest całkowity rozmiar pamięci usługi YARN dla cztery węzły klastra: 

        YARN memory = 4 * 96GB = 384GB

* Liczba liczby maperów — portalu z Ambari, możesz określić, czy rozmiar kontenera YARN jest 3072 dla węzłów klastra D14. Dlatego jest liczba liczby maperów:

        m = (4 nodes * 96GB) / 3072MB = 128 mappers

Jeśli inne aplikacje używają pamięci, można wybrać do użycia tylko część pamięci usługi YARN z klastra dla narzędzia DistCp.

### <a name="copying-large-datasets"></a>Kopiowanie dużych zestawów danych

Gdy jest duży rozmiar zestawu danych do przeniesienia (na przykład > 1 TB) lub jeśli masz wiele różnych folderach, należy rozważyć użycie wielu zadań DistCp. Prawdopodobnie nie są bardziej wydajne, ale rozprzestrzenia się zadania tak, aby w przypadku awarii dowolnego zadania, należy ponownie uruchomić określonego zadania, a nie całego zadania.

### <a name="limitations"></a>Ograniczenia

* Narzędzia DistCp próbuje utworzyć liczby maperów mających podobny do rozmiaru w celu zoptymalizowania wydajności. Zwiększenie liczby maperów może nie zawsze powoduje zwiększenie wydajności.

* Narzędzia DistCp jest ograniczona do tylko jednego mapowania na plik. W związku z tym nie powinna mieć więcej liczby maperów nie znajdują się pliki. Ponieważ DistCp można przypisać tylko jednego mapowania do pliku, ogranicza to ilość współbieżności, który może służyć do kopiowania dużych plików.

* W przypadku niewielkiej liczby dużych plików, następnie należy podzielić je na fragmenty plików 256 MB, które pozwalają uzyskać większą współbieżność potencjalnych. 
 
* Jeśli są kopiowane z konta usługi Azure Blob Storage, zadanie kopiowania może ograniczone po stronie magazynu obiektów blob. To obniża wydajność zadania kopiowania. Aby dowiedzieć się więcej o limitach usługi Azure Blob Storage, zobacz limity usługi Azure Storage w [limity usług i subskrypcji platformy Azure](../azure-subscription-service-limits.md).

## <a name="see-also"></a>Zobacz także
* [Kopiowanie danych z obiektów blob usługi Azure Storage do Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [Zabezpieczanie danych w usłudze Data Lake Storage 1. generacji](data-lake-store-secure-data.md)
* [Za pomocą usług Azure Data Lake Analytics Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usługa Azure HDInsight za pomocą programu Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
