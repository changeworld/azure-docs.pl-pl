---
title: Wykonywanie zapytań dla danych z usługi Azure Storage zgodnej z systemem plików HDFS — Azure HDInsight
description: Dowiedz się, jak wykonywać zapytania dotyczące danych z usługi Azure Storage i Azure Data Lake Storage, aby przechowywać wyniki analizy.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/01/2019
ms.openlocfilehash: d934568f09e62ad8c1b472583cbfee79d2c837f6
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936856"
---
# <a name="use-azure-storage-with-azure-hdinsight-clusters"></a>Korzystanie z usługi Azure Storage w połączeniu z klastrami usługi Azure HDInsight

Aby analizować dane w klastrze usługi HDInsight, możesz przechowywać dane w [usłudze Azure Storage](../storage/common/storage-introduction.md), [Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md)/[Azure Data Lake Storage Gen 2](../storage/blobs/data-lake-storage-introduction.md)lub kombinację. Te opcje magazynu umożliwiają bezpieczne usuwanie klastrów usługi HDInsight, które są używane do obliczeń bez utraty danych użytkownika.

Apache Hadoop obsługuje pojęcie domyślnego systemu plików. Domyślny system plików wyznacza domyślny schemat i element authority. Może również służyć do rozpoznawania ścieżek względnych. Podczas procesu tworzenia klastra usługi HDInsight można określić kontener obiektów BLOB w usłudze Azure Storage jako domyślny system plików lub za pomocą usługi HDInsight 3,6. można wybrać usługę Azure Storage lub Azure Data Lake Storage Gen 1/Azure Data Lake Storage Gen 2 jako pliki domyślne System z kilkoma wyjątkami. Aby uzyskać pomoc techniczną dotyczącą korzystania z Data Lake Storage generacji 1 jako magazynu domyślnego i połączonego, zobacz [dostępność klastra usługi HDInsight](./hdinsight-hadoop-use-data-lake-store.md#availability-for-hdinsight-clusters).

W tym artykule omówiono współdziałanie usługi Azure Storage z klastrami usługi HDInsight. Aby dowiedzieć się, jak Data Lake Storage Gen 1 współpracuje z klastrami usługi HDInsight, zobacz [Korzystanie z Azure Data Lake Storage z klastrami Azure HDInsight](hdinsight-hadoop-use-data-lake-store.md). Aby uzyskać więcej informacji na temat tworzenia klastra usługi HDInsight, zobacz [Tworzenie klastrów Apache Hadoop w usłudze HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

Usługa Azure Storage to niezawodne rozwiązanie ogólnego przeznaczenia, które bezproblemowo integruje się z usługą HDInsight. Usługa HDInsight może używać kontenera obiektów blob w usłudze Azure Storage jako domyślnego systemu plików dla klastra. Korzystając z interfejsu rozproszonego systemu plików Hadoop (HDFS), pełny zestaw składników usługi HDInsight może operować bezpośrednio na danych ze strukturą lub bez niej przechowywanych jako obiekty blob.

> [!IMPORTANT]  
> Rodzaj konta magazynu **BlobStorage** może być używany tylko jako magazyn pomocniczy dla klastrów usługi HDInsight.

| Rodzaj konta magazynu | Obsługiwane usługi | Obsługiwane warstwy wydajności | Obsługiwane warstwy dostępu |
|----------------------|--------------------|-----------------------------|------------------------|
| StorageV2 (ogólnego przeznaczenia wersja 2)  | Obiekt blob     | Standardowa (Standard)                    | Gorąca, chłodna, archiwum @ no__t-0   |
| Storage (ogólnego przeznaczenia w wersji 1)   | Obiekt blob     | Standardowa (Standard)                    | ND                    |
| BlobStorage                    | Obiekt blob     | Standardowa (Standard)                    | Gorąca, chłodna, archiwum @ no__t-0   |

Nie zaleca się używania domyślnego kontenera obiektów BLOB do przechowywania danych firmowych. Dobrym rozwiązaniem jest usunięcie domyślnego kontenera obiektów blob po każdym użyciu, aby obniżyć koszty magazynowania. Kontener domyślny zawiera Dzienniki aplikacji i systemu. Koniecznie pobierz dzienniki przed usunięciem kontenera.

Udostępnianie jednego kontenera obiektów BLOB jako domyślnego systemu plików dla wielu klastrów nie jest obsługiwane.

> [!NOTE]  
> Warstwa dostępu archiwalna jest warstwą offline, która ma kilka opóźnień pobierania i nie jest zalecana do użycia z usługą HDInsight. Aby uzyskać więcej informacji, zobacz [Archiwizowanie warstwy dostępu](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier).

Jeśli wybierzesz opcję zabezpieczenia konta magazynu za pomocą ograniczeń **zapory i sieci wirtualnych** w **wybranych sieciach**, należy włączyć wyjątek **Zezwalaj na zaufane usługi firmy Microsoft...** , aby Usługa HDInsight mogła uzyskać dostęp do magazynu koncie.

## <a name="hdinsight-storage-architecture"></a>Architektura magazynu usługi HDInsight

Na poniższym diagramie przedstawiono schemat architektury magazynu usługi HDInsight dotyczący korzystania z usługi Azure Storage:

![Klastry Hadoop używają interfejsu API systemu plików HDFS do uzyskiwania dostępu do danych i ich przechowywania w](./media/hdinsight-hadoop-use-blob-storage/storage-architecture.png "architekturze HDInsight Storage") obiektów BLOB Storage

Usługa HDInsight zapewnia dostęp do rozproszonego systemu plików, który jest lokalnie dołączony do węzłów obliczeniowych. Dostęp do tego systemu plików można uzyskać przy użyciu w pełni kwalifikowanego identyfikatora URI, na przykład:

    hdfs://<namenodehost>/<path>

Ponadto usługa HDInsight zapewnia możliwość dostępu do danych przechowywanych w usłudze Azure Storage. Składnia jest następująca:

    wasbs://<containername>@<accountname>.blob.core.windows.net/<path>

Poniżej przedstawiono kilka zagadnień dotyczących korzystania z konta usługi Azure Storage w połączeniu z klastrami usługi HDInsight.

* **Kontenery w ramach kont magazynu, które są podłączone do klastra:** ponieważ nazwa konta i klucz są kojarzone z klastrem podczas tworzenia, masz pełny dostęp do obiektów blob w tych kontenerach.

* **Publiczne kontenery lub publiczne obiekty blob na kontach magazynu, które NIE są podłączone do klastra:** masz uprawnienia tylko do odczytu obiektów blob w kontenerach.
  
> [!NOTE]  
> Kontenery publiczne pozwalają na pobranie listy wszystkich obiektów blob, które są dostępne w danym kontenerze, oraz pobranie metadanych kontenera. Publiczne obiekty blob umożliwiają dostęp do obiektów blob jedynie osobom znającym dokładny adres URL. Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem do kontenerów i obiektów BLOB](../storage/blobs/storage-manage-access-to-resources.md).

* **Prywatne kontenery na kontach magazynu, które nie są połączone z klastrem:** Nie można uzyskać dostępu do obiektów BLOB w kontenerach, chyba że zostanie zdefiniowane konto magazynu podczas przesyłania zadań WebHCat. Wyjaśnienie jest zawarte w dalszej części tego artykułu.

Konta magazynu zdefiniowane w procesie tworzenia i ich klucze są przechowywane w `%HADOOP_HOME%/conf/core-site.xml` w węzłach klastra. Domyślne działanie usługi HDInsight polega na korzystaniu z kont magazynu zdefiniowanych w pliku core-site.xml. To ustawienie można zmodyfikować za pomocą usługi [Apache Ambari](./hdinsight-hadoop-manage-ambari.md).

Wiele zadań WebHCat, w tym Apache Hive, MapReduce, Apache Hadoop streaming i Apache świni, może zawierać opis kont magazynu i metadanych z nimi. (Obecnie działa to w przypadku trzody chlewnej z kontami magazynu, ale nie dla metadanych). Aby uzyskać więcej informacji, zobacz [Korzystanie z klastra usługi HDInsight z alternatywnymi kontami magazynu i magazynami](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

Obiekty blob mogą być używane z danymi ze strukturą i bez niej. Kontenery obiektów BLOB przechowują dane jako pary klucz/wartość i nie istnieje hierarchia katalogów. Jednak wewnątrz nazwy klucza można użyć znaku ukośnika (/), co sprawi, że będzie wyglądała, jakby plik był przechowywany w ramach struktury katalogów. Na przykład klucz obiektu blob może mieć postać *input/log1.txt*. Katalog *input* w rzeczywistości nie istnieje, ale z powodu obecności znaku ukośnika w nazwie klucza, klucz ma wygląd ścieżki do pliku.

## <a id="benefits"></a>Korzyści z usługi Azure Storage

Niejawny koszt wydajności nielokalizowania klastrów obliczeniowych i zasobów magazynu jest zmniejszany przez sposób, w jaki tworzone są Klastry obliczeniowe w pobliżu zasobów konta magazynu w regionie świadczenia usługi Azure, gdzie sieć o dużej szybkości jest wydajna dla węzły obliczeniowe umożliwiające dostęp do danych w usłudze Azure Storage.

Przechowywanie danych w usłudze Azure Storage zamiast w systemie plików HDFS ma wiele zalet:

* **Udostępnianie i ponowne użycie danych:** dane w systemie plików HDFS znajdują się wewnątrz klastra obliczeniowego. Tylko te aplikacje, które mają dostęp do klastra obliczeniowego mogą używać danych za pomocą interfejsów API systemu plików HDFS. Dostęp do danych w usłudze Azure Storage można uzyskać za pomocą interfejsów API systemu plików HDFS lub za pomocą [interfejsów API REST BLOB Storage](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API). W związku z tym większy zestaw narzędzi i aplikacji (w tym inne klastry HDInsight) może służyć do tworzenia i wykorzystywania danych.

* **Archiwizacja danych:** przechowywanie danych w usłudze Azure Storage pozwala bezpiecznie usuwać klastry usługi HDInsight używane do obliczeń bez utraty danych użytkownika.

* **Koszt magazynowania danych:** długoterminowe przechowywanie danych w systemie plików DFS jest droższe niż przechowywanie danych w usłudze Azure Storage, ponieważ koszt klastra obliczeniowego jest wyższy niż koszt usługi Azure Storage. Ponadto, ponieważ nie trzeba ponownie ładować danych dla każdej generacji klastra obliczeniowego, zapisywanych jest również koszty ładowania danych.

* **Elastyczne skalowanie w poziomie:** chociaż system plików HDFS zapewnia skalowanie w poziomie, skala jest wyznaczana przez liczbę węzłów tworzonych dla klastra. Zmiana skali może stać się procesem bardziej skomplikowanym w porównaniu z elastycznymi możliwościami skalowania, które można uzyskać automatycznie w usłudze Azure Storage.

* **Replikacja geograficzna:** usługę Azure Storage można replikować geograficznie. Chociaż zapewnia to odzyskiwanie geograficzne i nadmiarowość danych, praca w trybie failover w przypadku lokalizacji zreplikowanych geograficznie poważnie wpływa na wydajność i może pociągać za sobą dodatkowe koszty. Dlatego zalecamy, aby wybierać replikację geograficzną w rozsądny sposób i tylko wtedy, gdy wartość danych uzasadnia ponoszenie dodatkowych kosztów.

Niektóre zadania i pakiety MapReduce mogą tworzyć wyniki pośrednie, których nie potrzeba przechowywać w usłudze Azure Storage. W takim przypadku można zdecydować się na przechowywanie danych w lokalnym systemie plików HDFS. W rzeczywistości HDInsight używa systemu plików DFS dla wielu wyników pośrednich w zadaniach Hive i innych procesach.

> [!NOTE]  
> Większość poleceń systemu plików HDFS (na przykład `ls`, `copyFromLocal` i `mkdir`) nadal działają zgodnie z oczekiwaniami. Tylko polecenia specyficzne dla natywnej implementacji systemu plików HDFS (zwanej systemem DFS), takie jak `fschk` i `dfsadmin`, pokazują inne zachowanie w usłudze Azure Storage.

## <a name="address-files-in-azure-storage"></a>Adresowanie plików w usłudze Azure Storage

Schemat identyfikatora URI do uzyskiwania dostępu do plików w usłudze Azure Storage z usługi HDInsight to:

```config
wasbs://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>
```

Schemat identyfikatora URI zapewnia nieszyfrowany dostęp (z prefiksem *wasb:* ) oraz szyfrowany dostęp SSL (z prefiksem *wasbs*). Zalecamy używanie prefiksu *wasbs* wszędzie tam, gdzie to możliwe, nawet w przypadku uzyskiwania dostępu do danych, które znajdują się wewnątrz tego samego regionu w systemie Azure.

@No__t-0 identyfikuje nazwę kontenera obiektów BLOB w usłudze Azure Storage.
@No__t-0 identyfikuje nazwę konta usługi Azure Storage. Wymagana jest w pełni kwalifikowana nazwa domeny (FQDN).

Jeśli nie określono żadnego `<BlobStorageContainerName>` ani `<StorageAccountName>`, używany jest domyślny system plików. W przypadku plików w domyślnym systemie plików można używać ścieżki względnej lub bezwzględnej. Na przykład do pliku *hadoop-mapreduce-examples.jar* dostarczanego z klastrami usługi HDInsight można odwoływać się w jeden z następujących sposobów:

```config
wasbs://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
wasbs:///example/jars/hadoop-mapreduce-examples.jar
/example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]  
> Nazwa pliku to `hadoop-examples.jar` w usłudze HDInsight w wersji 2,1 i 1,6.

Ścieżka jest ścieżką do pliku lub katalogu systemu plików HDFS. Ponieważ kontenery w usłudze Azure Storage to magazyny klucz-wartość, nie ma żadnego prawdziwego systemu plików hierarchicznych. Znak ukośnika (/) wewnątrz klucza obiektu blob jest interpretowany jako separator katalogu. Na przykład nazwą obiektu blob dla pliku *hadoop-mapreduce-examples.jar* jest:

```bash
example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]  
> Podczas pracy z obiektami blob poza usługą HDInsight, większość narzędzi nie rozpoznaje formatu WASB i zamiast tego oczekuje podstawowego formatu ścieżki, takiego jak `example/jars/hadoop-mapreduce-examples.jar`.

## <a name="blob-containers"></a>Kontenery obiektów blob

Aby użyć obiektów blob, należy najpierw utworzyć [konto usługi Azure Storage](../storage/common/storage-create-storage-account.md). W ramach tego procesu należy wskazać region świadczenia usługi Azure, w którym zostanie utworzone konto magazynu. Klaster i konto magazynu muszą być hostowane w tym samym regionie. Baza danych magazyn metadanych Hive SQL Server Database i Apache Oozie baza danych SQL Server muszą również znajdować się w tym samym regionie.

Wszędzie tam, gdzie go umieszczono, każdy utworzony obiekt blob należy do kontenera na koncie usługi Azure Storage. Ten kontener może być istniejącym obiektem blob utworzonym poza usługą HDInsight lub może być kontenerem, który jest tworzony dla klastra usługi HDInsight.

Domyślny kontener obiektów blob przechowuje informacje dotyczące klastra, takie jak dzienniki i historia zadań. Nie należy współużytkować domyślnego kontenera obiektów blob dla wielu klastrów usługi HDInsight. Może to spowodować uszkodzenie historii zadań. Zalecane jest użycie innego kontenera dla każdego klastra i umieszczenie danych udostępnionych na połączonym koncie magazynu określonym we wdrożeniu wszystkich odpowiednich klastrów, a nie na domyślnym koncie magazynu. Aby uzyskać więcej informacji na temat konfigurowania połączonych kont magazynu, zobacz [Tworzenie klastrów usługi HDInsight](hdinsight-hadoop-provision-linux-clusters.md). Jednak po usunięciu oryginalnego klastra usługi HDInsight można ponownie użyć domyślnego kontenera magazynu. W przypadku klastrów HBase można zachować schemat tabeli HBase i dane przez utworzenie nowego klastra HBase przy użyciu domyślnego kontenera obiektów blob, który jest używany przez usunięty klaster HBase.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

## <a name="interacting-with-azure-storage"></a>Korzystanie z usługi Azure Storage

Firma Microsoft udostępnia następujące narzędzia do pracy z usługą Azure Storage:

| Narzędzie | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Interfejs wiersza polecenia platformy Azure](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [Narzędzie AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |

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
* [Korzystanie z Apache świni z usługą HDInsight](hadoop/hdinsight-use-pig.md)
* [Używanie sygnatur dostępu współdzielonego usługi Azure Storage w celu ograniczenia dostępu do danych w usłudze HDInsight](hdinsight-storage-sharedaccesssignature-permissions.md)
* [Korzystanie z usługi Azure Data Lake Storage Gen2 w połączeniu z klastrami usługi Azure HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)
