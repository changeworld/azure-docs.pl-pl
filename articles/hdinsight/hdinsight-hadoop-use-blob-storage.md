---
title: Wykonywanie zapytań dla danych z usługi Azure Storage zgodnej z systemem plików HDFS — Azure HDInsight
description: Dowiedz się, jak wykonywać zapytania dotyczące danych z usługi Azure Storage i Azure Data Lake Storage, aby przechowywać wyniki analizy.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: f496f6c06d36f817b0a933bdc68d5c53f308e3f2
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78192629"
---
# <a name="use-azure-storage-with-azure-hdinsight-clusters"></a>Korzystanie z usługi Azure Storage w połączeniu z klastrami usługi Azure HDInsight

Aby analizować dane w klastrze usługi HDInsight, możesz przechowywać dane w [usłudze Azure Storage](../storage/common/storage-introduction.md), [Azure Data Lake Storage gen 1](../data-lake-store/data-lake-store-overview.md)/[Azure Data Lake Storage Gen 2](../storage/blobs/data-lake-storage-introduction.md)lub kombinację. Te opcje magazynu umożliwiają bezpieczne usuwanie klastrów usługi HDInsight, które są używane do obliczeń bez utraty danych użytkownika.

Apache Hadoop obsługuje pojęcie domyślnego systemu plików. Domyślny system plików wyznacza domyślny schemat i element authority. Może również służyć do rozpoznawania ścieżek względnych. Podczas procesu tworzenia klastra usługi HDInsight jako domyślny system plików można określić kontener obiektów BLOB w usłudze Azure Storage, a w przypadku usługi HDInsight 3,6 można wybrać opcję Azure Storage lub Azure Data Lake Storage Gen 1/Azure Data Lake Storage Gen 2 jako domyślny system Files z kilkoma wyjątkami. Aby uzyskać pomoc techniczną dotyczącą korzystania z Data Lake Storage generacji 1 jako magazynu domyślnego i połączonego, zobacz [dostępność klastra usługi HDInsight](./hdinsight-hadoop-use-data-lake-store.md#availability-for-hdinsight-clusters).

W tym artykule omówiono współdziałanie usługi Azure Storage z klastrami usługi HDInsight. Aby dowiedzieć się, jak Data Lake Storage Gen 1 współpracuje z klastrami usługi HDInsight, zobacz [Korzystanie z Azure Data Lake Storage z klastrami Azure HDInsight](hdinsight-hadoop-use-data-lake-store.md). Aby uzyskać więcej informacji na temat tworzenia klastra usługi HDInsight, zobacz [Tworzenie klastrów Apache Hadoop w usłudze HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

> [!IMPORTANT]  
> Rodzaj konta magazynu **BlobStorage** może być używany tylko jako magazyn pomocniczy dla klastrów usługi HDInsight.

| Rodzaj konta magazynu | Obsługiwane usługi | Obsługiwane warstwy wydajności | Obsługiwane warstwy dostępu |
|----------------------|--------------------|-----------------------------|------------------------|
| StorageV2 (ogólnego przeznaczenia wersja 2)  | Obiekt blob     | Standard                    | Gorące, chłodne i archiwalne\*   |
| Storage (ogólnego przeznaczenia w wersji 1)   | Obiekt blob     | Standard                    | Nie dotyczy                    |
| BlobStorage                    | Obiekt blob     | Standard                    | Gorące, chłodne i archiwalne\*   |

Nie zaleca się używania domyślnego kontenera obiektów BLOB do przechowywania danych firmowych. Dobrym rozwiązaniem jest usunięcie domyślnego kontenera obiektów blob po każdym użyciu, aby obniżyć koszty magazynowania. Kontener domyślny zawiera Dzienniki aplikacji i systemu. Koniecznie pobierz dzienniki przed usunięciem kontenera.

Udostępnianie jednego kontenera obiektów BLOB jako domyślnego systemu plików dla wielu klastrów nie jest obsługiwane.

> [!NOTE]  
> Warstwa dostępu archiwalna jest warstwą offline, która ma kilka opóźnień pobierania i nie jest zalecana do użycia z usługą HDInsight. Aby uzyskać więcej informacji, zobacz [Archiwizowanie warstwy dostępu](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier).

## <a name="access-files-from-within-cluster"></a>Dostęp do plików z klastra

Istnieje kilka sposobów uzyskiwania dostępu do plików w Data Lake Storage z klastra usługi HDInsight. Schemat identyfikatora URI zapewnia nieszyfrowany dostęp (z prefiksem *wasb:* ) oraz szyfrowany dostęp SSL (z prefiksem *wasbs*). Zalecamy używanie prefiksu *wasbs* wszędzie tam, gdzie to możliwe, nawet w przypadku uzyskiwania dostępu do danych, które znajdują się wewnątrz tego samego regionu w systemie Azure.

* **Przy użyciu w pełni kwalifikowanej nazwy**. W przypadku tej metody należy podać pełną ścieżkę do pliku, do którego chcesz uzyskać dostęp.

    ```
    wasb://<containername>@<accountname>.blob.core.windows.net/<file.path>/
    wasbs://<containername>@<accountname>.blob.core.windows.net/<file.path>/
    ```

* **Przy użyciu skróconego formatu ścieżki**. To podejście zastępuje ścieżkę do katalogu głównego klastra przy użyciu:

    ```
    wasb:///<file.path>/
    wasbs:///<file.path>/
    ```

* **Przy użyciu ścieżki względnej**. W przypadku tej metody należy podać tylko względną ścieżkę do pliku, do którego chcesz uzyskać dostęp.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Przykłady dostępu do danych

Przykłady są oparte na [połączeniu SSH](./hdinsight-hadoop-linux-use-ssh-unix.md) z węzłem głównym klastra. W przykładach użyto wszystkich trzech schematów URI. Zastąp `CONTAINERNAME` i `STORAGEACCOUNT` odpowiednimi wartościami

#### <a name="a-few-hdfs-commands"></a>Kilka poleceń systemu plików HDFS

1. Utwórz prosty plik w magazynie lokalnym.

    ```bash
    touch testFile.txt
    ```

1. Utwórz katalogi w magazynie klastra.

    ```bash
    hdfs dfs -mkdir wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -mkdir wasbs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Skopiuj dane z magazynu lokalnego do magazynu klastra.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  wasbs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. Wyświetlanie zawartości katalogu w magazynie klastra.

    ```bash
    hdfs dfs -ls wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -ls wasbs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

> [!NOTE]  
> Podczas pracy z obiektami blob poza usługą HDInsight, większość narzędzi nie rozpoznaje formatu WASB i zamiast tego oczekuje podstawowego formatu ścieżki, takiego jak `example/jars/hadoop-mapreduce-examples.jar`.

#### <a name="creating-a-hive-table"></a>Tworzenie tabeli programu Hive

Trzy lokalizacje plików są pokazane w celach ilustracyjnych. W przypadku rzeczywistego wykonywania należy użyć tylko jednego z wpisów `LOCATION`.

```hql
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/example/data/';
LOCATION 'wasbs:///example/data/';
LOCATION '/example/data/';
```

## <a name="access-files-from-outside-cluster"></a>Dostęp do plików z klastra zewnętrznego

Firma Microsoft udostępnia następujące narzędzia do pracy z usługą Azure Storage:

| Narzędzie | Linux | OS X | System Windows |
| --- |:---:|:---:|:---:|
| [Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Interfejs wiersza polecenia platformy Azure](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [Narzędzie AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |

## <a name="identify-storage-path-from-ambari"></a>Zidentyfikuj ścieżkę magazynu z Ambari

* Aby zidentyfikować pełną ścieżkę do skonfigurowanego domyślnego magazynu, przejdź do:

     > **Konfiguracja** systemu plików **HDFS** i wprowadź `fs.defaultFS` w polu wejściowym filtru.

* Aby sprawdzić, czy magazyn wasb jest skonfigurowany jako magazyn pomocniczy, przejdź do:

     > **Konfiguracja** systemu plików **HDFS** i wprowadź `blob.core.windows.net` w polu wejściowym filtru.

Aby uzyskać ścieżkę przy użyciu interfejsu API REST Ambari, zobacz temat [pobieranie magazynu domyślnego](./hdinsight-hadoop-manage-ambari-rest-api.md#get-the-default-storage).

## <a name="blob-containers"></a>Kontenery obiektów blob

Aby użyć obiektów blob, należy najpierw utworzyć [konto usługi Azure Storage](../storage/common/storage-create-storage-account.md). W ramach tego procesu należy wskazać region świadczenia usługi Azure, w którym zostanie utworzone konto magazynu. Klaster i konto magazynu muszą być hostowane w tym samym regionie. Baza danych magazyn metadanych Hive SQL Server Database i Apache Oozie baza danych SQL Server muszą również znajdować się w tym samym regionie.

Wszędzie tam, gdzie go umieszczono, każdy utworzony obiekt blob należy do kontenera na koncie usługi Azure Storage. Ten kontener może być istniejącym obiektem blob utworzonym poza usługą HDInsight lub może być kontenerem, który jest tworzony dla klastra usługi HDInsight.

Domyślny kontener obiektów blob przechowuje informacje dotyczące klastra, takie jak dzienniki i historia zadań. Nie należy współużytkować domyślnego kontenera obiektów blob dla wielu klastrów usługi HDInsight. Może to spowodować uszkodzenie historii zadań. Zalecane jest użycie innego kontenera dla każdego klastra i umieszczenie danych udostępnionych na połączonym koncie magazynu określonym we wdrożeniu wszystkich odpowiednich klastrów, a nie na domyślnym koncie magazynu. Aby uzyskać więcej informacji na temat konfigurowania połączonych kont magazynu, zobacz [Tworzenie klastrów usługi HDInsight](hdinsight-hadoop-provision-linux-clusters.md). Jednak po usunięciu oryginalnego klastra usługi HDInsight można ponownie użyć domyślnego kontenera magazynu. W przypadku klastrów HBase można zachować schemat tabeli HBase i dane przez utworzenie nowego klastra HBase przy użyciu domyślnego kontenera obiektów blob, który jest używany przez usunięty klaster HBase.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

## <a name="use-additional-storage-accounts"></a>Używanie dodatkowych kont magazynu

Podczas tworzenia klastra usługi HDInsight należy wskazać konto usługi Azure Storage, które ma zostać skojarzone z tym klastrem. Oprócz tego konta magazynu można dodać dodatkowe konta magazynu z tej samej subskrypcji platformy Azure lub różnych subskrypcji platformy Azure podczas procesu tworzenia lub po utworzeniu klastra. Aby uzyskać instrukcje dotyczące dodawania kolejnych kont magazynu, zobacz [Tworzenie klastrów usługi HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

> [!WARNING]  
> Korzystanie z dodatkowego konta magazynu w innej lokalizacji niż klaster usługi HDInsight nie jest obsługiwane.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób korzystania z magazynu Azure zgodnego z systemem plików HDFS w połączeniu z usługą HDInsight. Podane tu informacje umożliwiają tworzenie skalowalnych, długoterminowych rozwiązań do pozyskiwania danych archiwalnych i używanie usługi HDInsight w celu efektywnego wykorzystywania informacji przechowywanych w postaci danych ze strukturą i bez niej.

Aby uzyskać więcej informacji, zobacz:

* [Rozpoczynanie pracy z usługą Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Wprowadzenie do Azure Data Lake Storage](../data-lake-store/data-lake-store-get-started-portal.md)
* [Przekazywanie danych do usługi HDInsight](hdinsight-upload-data.md)
* [Korzystanie z Apache Hive z usługą HDInsight](hadoop/hdinsight-use-hive.md)
* [Używanie sygnatur dostępu współdzielonego usługi Azure Storage w celu ograniczenia dostępu do danych w usłudze HDInsight](hdinsight-storage-sharedaccesssignature-permissions.md)
* [Korzystanie z usługi Azure Data Lake Storage Gen2 w połączeniu z klastrami usługi Azure HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)
* [Samouczek: Wyodrębnianie, przekształcanie i ładowanie danych przy użyciu interakcyjnych zapytań w usłudze Azure HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
