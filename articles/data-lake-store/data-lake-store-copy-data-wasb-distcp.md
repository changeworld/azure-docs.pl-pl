---
title: Kopiowanie danych do i z serwera WASB do usługi Azure Data Lake Storage Gen1 przy użyciu protokołu DistCp
description: Narzędzie DistCp służy do kopiowania danych do i z obiektów blob usługi Azure Storage do usługi Azure Data Lake Storage Gen1
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 01/03/2020
ms.author: twooley
ms.openlocfilehash: 455e73ece2d46a508b3077c13c8106fe53beb4de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638837"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen1"></a>Kopiowanie danych między obiektami blob usługi Azure Storage a usługą Azure Data Lake Storage Gen1 za pomocą protokołu DistCp

> [!div class="op_single_selector"]
> * [Korzystanie z narzędzia DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [Korzystanie z narzędzia AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Jeśli masz klaster HDInsight z dostępem do usługi Azure Data Lake Storage Gen1, możesz użyć narzędzi ekosystemu Hadoop, takich jak DistCp, aby skopiować dane do i z magazynu klastra HDInsight (WASB) na konto Data Lake Storage Gen1. W tym artykule pokazano, jak używać narzędzia DistCp.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Konto usługi Azure Data Lake Storage Gen1**. Aby uzyskać instrukcje dotyczące tworzenia jednego z nich, zobacz Wprowadzenie do [usługi Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).
* **Klaster usługi Azure HDInsight** z dostępem do konta Usługi Data Lake Storage Gen1. Zobacz [Tworzenie klastra HDInsight z pamięcią Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Upewnij się, że włączysz pulpit zdalny dla klastra.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>Używanie protokołu DistCp z klastra systemu LINUX usługi HDInsight

Klaster HDInsight jest dostarczany z narzędziem DistCp, które może być używane do kopiowania danych z różnych źródeł do klastra HDInsight. Jeśli klaster HDInsight został skonfigurowany do używania magazynu usługi Data Lake Storage Gen1 jako dodatkowego magazynu, można użyć dodatku DistCp out-of-the-box do kopiowania danych do i z konta Data Lake Storage Gen1. W tej sekcji przyjrzymy się, jak korzystać z narzędzia DistCp.

1. Na pulpicie użyj funkcji SSH, aby połączyć się z klastrem. Zobacz [Łączenie się z klastrem HDInsight opartym na systemie Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md). Uruchom polecenia z wiersza SSH.

1. Sprawdź, czy można uzyskać dostęp do obiektów blob usługi Azure Storage (WASB). Uruchom następujące polecenie:

   ```
   hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/
   ```

   Dane wyjściowe zawiera listę zawartości w obiekcie blob magazynu.

1. Podobnie sprawdź, czy można uzyskać dostęp do konta Usługi Data Lake Storage Gen1 z klastra. Uruchom następujące polecenie:

   ```
   hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/
   ```

    Dane wyjściowe zawiera listę plików i folderów na koncie Data Lake Storage Gen1.

1. Użyj DistCp, aby skopiować dane z wasb do konta Data Lake Storage Gen1.

   ```
   hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder
   ```

    Polecenie kopiuje zawartość folderu **/example/data/gutenberg/** w folderze WASB do **/myfolder** na koncie Data Lake Storage Gen1.

1. Podobnie użyj DistCp do kopiowania danych z konta Data Lake Storage Gen1 do WASB.

   ```
   hadoop distcp adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg
   ```

    Polecenie kopiuje zawartość **/myfolder** na koncie Data Lake Storage Gen1 do **folderu /example/data/gutenberg/** w folderze WASB.

## <a name="performance-considerations-while-using-distcp"></a>Zagadnienia dotyczące wydajności podczas korzystania z DistCp

Ponieważ najniższa szczegółowość narzędzia DistCp jest pojedynczym plikiem, ustawienie maksymalnej liczby jednoczesnych kopii jest najważniejszym parametrem optymalizowania go względem pamięci masowej Data Lake Storage Gen1. Liczbę kopii można kontrolować, ustawiając liczbę maperów ("m') parametru w wierszu polecenia. Ten parametr określa maksymalną liczbę maperów, które są używane do kopiowania danych. Wartość domyślna to 20.

Przykład:

```
 hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder -m 100
```

### <a name="how-to-determine-the-number-of-mappers-to-use"></a>Jak określić liczbę maperów do użycia

Oto kilka użytecznych wskazówek.

* **Krok 1: Określ całkowitą pamięć YARN** — pierwszym krokiem jest określenie pamięci YARN dostępnej dla klastra, w której uruchomisz zadanie DistCp. Te informacje są dostępne w portalu Ambari skojarzonym z klastrem. Przejdź do YARN i wyświetl kartę **Configs,** aby wyświetlić pamięć YARN. Aby uzyskać całkowitą pamięć YARN, należy pomnożyć pamięć YARN na węzeł z liczbą węzłów, które masz w klastrze.

* **Krok 2: Oblicz liczbę maperów** — wartość **m** jest równa ilorazowi całkowitej pamięci YARN podzielonej przez rozmiar kontenera YARN. Informacje o rozmiarze kontenera YARN są również dostępne w portalu Ambari. Przejdź do pozycji YARN i wyświetl kartę **Konfiguracje.** Rozmiar kontenera YARN jest wyświetlany w tym oknie. Równanie, aby dotrzeć do liczby maperów (**m**) jest:

   `m = (number of nodes * YARN memory for each node) / YARN container size`

Przykład:

Załóżmy, że masz cztery węzły D14v2s w klastrze i chcesz przenieść 10 TB danych z 10 różnych folderów. Każdy z folderów zawiera różne ilości danych, a rozmiary plików w każdym folderze są różne.

* Całkowita pamięć YARN — z portalu Ambari można ustalić, że pamięć YARN jest 96 GB dla węzła D14. Tak, całkowita pamięć YARN dla klastra czterech węzłów jest:

   `YARN memory = 4 * 96GB = 384GB`

* Liczba maperów — w portalu Ambari można ustalić, że rozmiar kontenera YARN jest 3072 dla węzła klastra D14. Tak więc liczba maperów jest:

   `m = (4 nodes * 96GB) / 3072MB = 128 mappers`

Jeśli inne aplikacje używają pamięci, można użyć tylko części pamięci YARN klastra dla DistCp.

### <a name="copying-large-datasets"></a>Kopiowanie dużych zestawów danych

Gdy rozmiar zestawu danych do przeniesienia jest duży (na przykład > 1 TB) lub jeśli masz wiele różnych folderów, należy rozważyć użycie wielu zadań DistCp. Prawdopodobnie nie ma przyrostu wydajności, ale rozkłada zadania, dzięki czemu jeśli jakiekolwiek zadanie nie powiedzie się, musisz tylko ponownie uruchomić to zadanie zamiast całego zadania.

### <a name="limitations"></a>Ograniczenia

* DistCp próbuje utworzyć maperów, które są podobne rozmiary w celu optymalizacji wydajności. Zwiększenie liczby maperów może nie zawsze zwiększać wydajność.

* DistCp jest ograniczona tylko do jednego mapera na plik. W związku z tym nie powinno mieć więcej maperów niż masz pliki. Ponieważ DistCp można przypisać tylko jeden mapera do pliku, ogranicza to ilość współbieżności, która może służyć do kopiowania dużych plików.

* Jeśli masz niewielką liczbę dużych plików, podziel je na fragmenty plików o rozmiarze 256 MB, aby zapewnić większą potencjalną współbieżność.

* Jeśli kopiujesz z konta magazynu obiektów Blob platformy Azure, zadanie kopiowania może być ograniczone po stronie magazynu obiektów Blob. To obniża wydajność zadania kopiowania. Aby dowiedzieć się więcej o ograniczeniach usługi Azure Blob Storage, zobacz Limity usługi Azure Storage w [limitach subskrypcji i usług platformy Azure.](../azure-resource-manager/management/azure-subscription-service-limits.md)

## <a name="see-also"></a>Zobacz też

* [Kopiowanie danych z obiektów blob usługi Azure Storage do usługi Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [Zabezpieczanie danych w usłudze Data Lake Storage 1. generacji](data-lake-store-secure-data.md)
* [Korzystanie z usługi Azure Data Lake Analytics z usługą Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Korzystanie z usługi Azure HDInsight z usługą Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
