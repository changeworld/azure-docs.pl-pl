---
title: Wykonywanie zapytań dla danych z usługi Azure Storage zgodnej z systemem plików HDFS — Azure HDInsight
description: Dowiedz się, jak wykonywać zapytania o dane z magazynu platformy Azure i usługi Azure Data Lake Storage do przechowywania wyników analiz.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/23/2019
ms.openlocfilehash: 6e0192029decef95dcaecc0c60dce5fd5b6f99ff
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66479908"
---
# <a name="use-azure-storage-with-azure-hdinsight-clusters"></a>Korzystanie z usługi Azure Storage w połączeniu z klastrami usługi Azure HDInsight

Aby analizować dane w klastrze HDInsight, można przechowywać danych albo w [usługi Azure Storage](../storage/common/storage-introduction.md), [usługi Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md)/[usługi Azure Data Lake Storage Gen 2](../storage/blobs/data-lake-storage-introduction.md), lub w połączeniu. Te opcje magazynu pozwalają bezpiecznie usuwać klastry HDInsight, które są używane do obliczeń bez utraty danych użytkownika.

Apache Hadoop obsługuje pojęcie domyślnego systemu plików. Domyślny system plików wyznacza domyślny schemat i element authority. Może również służyć do rozpoznawania ścieżek względnych. W trakcie procesu tworzenia klastra HDInsight w usłudze Azure Storage jako domyślny system plików można wskazać kontener obiektów blob lub usługi HDInsight 3.6, można wybrać usługę Azure Storage lub Azure Data Lake Storage ogólnego 1 / usługi Azure Data Lake Storage Gen 2 jako domyślne pliki system z pewnymi wyjątkami. Aby uzyskać możliwość wykorzystania Data Lake Storage Gen 1 jako domyślnej i połączonego magazynu, zobacz [dostępność dla klastra HDInsight](./hdinsight-hadoop-use-data-lake-store.md#availability-for-hdinsight-clusters).

W tym artykule omówiono współdziałanie usługi Azure Storage z klastrami usługi HDInsight. Aby dowiedzieć się, jak Data Lake Storage Gen 1 działa z klastrami HDInsight, zobacz [usługi użycia usługi Azure Data Lake Storage za pomocą usługi Azure HDInsight clusters](hdinsight-hadoop-use-data-lake-store.md). Aby uzyskać więcej informacji na temat tworzenia klastra usługi HDInsight, zobacz [klastrów utworzyć Apache Hadoop w HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

Usługa Azure Storage to niezawodne rozwiązanie ogólnego przeznaczenia, które bezproblemowo integruje się z usługą HDInsight. Usługa HDInsight może używać kontenera obiektów blob w usłudze Azure Storage jako domyślnego systemu plików dla klastra. Korzystając z interfejsu rozproszonego systemu plików Hadoop (HDFS), pełny zestaw składników usługi HDInsight może operować bezpośrednio na danych ze strukturą lub bez niej przechowywanych jako obiekty blob.

> [!WARNING]  
> Rodzaj konta magazynu **magazynu obiektów blob** należy używać tylko jako pomocniczego magazynu dla klastrów HDInsight.

| Rodzaj konta magazynu | Obsługiwane usługi | Warstwy wydajności obsługiwane | Warstwy dostępu obsługiwane |
|----------------------|--------------------|-----------------------------|------------------------|
| StorageV2 (ogólnego przeznaczenia wersja 2)  | Obiekt blob     | Standardowa (Standard)                    | Gorąca, chłodna, archiwum\*   |
| Magazyn (ogólnego przeznaczenia w wersji 1)   | Obiekt blob     | Standardowa (Standard)                    | ND                    |
| BlobStorage                    | Obiekt blob     | Standardowa (Standard)                    | Gorąca, chłodna, archiwum\*   |

Używanie domyślnego kontenera obiektów blob do przechowywania danych firmowych nie jest zalecane. Dobrym rozwiązaniem jest usunięcie domyślnego kontenera obiektów blob po każdym użyciu, aby obniżyć koszty magazynowania. Domyślny kontener zawiera aplikacji i systemu dzienniki. Koniecznie pobierz dzienniki przed usunięciem kontenera.

Udostępnianie pojedynczego kontenera obiektów blob jako domyślnego systemu plików dla wielu klastrów nie jest obsługiwane.

> [!NOTE]  
> Warstwa dostępu archiwum jest w trybie offline warstwę, która ma kilka opóźnieniem godzinę i nie jest zalecane do użycia z usługą HDInsight. Aby uzyskać więcej informacji, zobacz [warstwę dostępu archiwum](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier).

Jeśli chcesz zabezpieczyć swoje konto magazynu z **zapory i sieci wirtualne** ograniczenia dotyczące **wybrane sieci**, pamiętaj włączyć wyjątek **Zezwalaj na zaufane firmy Microsoft usługi...**  tak, aby HDInsight można uzyskać dostępu do konta magazynu.

## <a name="hdinsight-storage-architecture"></a>Architektura magazynu usługi HDInsight
Na poniższym diagramie przedstawiono schemat architektury magazynu usługi HDInsight dotyczący korzystania z usługi Azure Storage:

![Klastry Hadoop używają interfejsu API systemu plików HDFS w celu dostępu do danych strukturalnych i bez struktury oraz przechowywania ich w usłudze Blob Storage.](./media/hdinsight-hadoop-use-blob-storage/HDI.WASB.Arch.png "Architektura usługi DHInsight Storage")

Usługa HDInsight zapewnia dostęp do rozproszonego systemu plików, który jest lokalnie dołączony do węzłów obliczeniowych. Dostęp do tego systemu plików można uzyskać przy użyciu w pełni kwalifikowanego identyfikatora URI, na przykład:

    hdfs://<namenodehost>/<path>

Ponadto usługa HDInsight zapewnia możliwość dostępu do danych przechowywanych w usłudze Azure Storage. Składnia jest następująca:

    wasb://<containername>@<accountname>.blob.core.windows.net/<path>

Poniżej przedstawiono kilka zagadnień dotyczących korzystania z konta usługi Azure Storage w połączeniu z klastrami usługi HDInsight.

* **Kontenery na kontach magazynu, które są podłączone do klastra:** Ponieważ nazwa konta i klucz są kojarzone z klastrem podczas tworzenia, masz pełny dostęp do obiektów blob w tych kontenerach.

* **Publiczne kontenery lub publiczne obiekty BLOB na kontach magazynu, które nie są podłączone do klastra:** Masz uprawnienia tylko do odczytu do obiektów blob w kontenerach.
  
> [!NOTE]  
> Kontenery publiczne pozwalają na pobranie listy wszystkich obiektów blob, które są dostępne w danym kontenerze, oraz pobranie metadanych kontenera. Publiczne obiekty blob umożliwiają dostęp do obiektów blob jedynie osobom znającym dokładny adres URL. Aby uzyskać więcej informacji, zobacz [zarządzanie dostępem do kontenerów i obiektów blob](../storage/blobs/storage-manage-access-to-resources.md).

* **Prywatne kontenery na kontach magazynu, które nie są podłączone do klastra:** Nie masz dostępu do obiektów blob w kontenerach, chyba że zdefiniujesz konto magazynu podczas przesyłania zadań WebHCat. Wyjaśnienie jest zawarte w dalszej części tego artykułu.

Konta magazynu, które są zdefiniowane w procesie tworzenia oraz ich klucze są przechowywane w `%HADOOP_HOME%/conf/core-site.xml` w węzłach klastra. Domyślne działanie usługi HDInsight polega na korzystaniu z kont magazynu zdefiniowanych w pliku core-site.xml. Możesz zmodyfikować to ustawienie przy użyciu [Apache Ambari](./hdinsight-hadoop-manage-ambari.md).

Wiele zadań WebHCat, w tym Apache Hive, MapReduce, przesyłanie strumieniowe usługi Apache Hadoop i Apache Pig, może przenosić Opis kont magazynu i metadane z nimi. (W przypadku technologii Pig obecnie działa to z kontami magazynu, ale nie dla metadanych). Aby uzyskać więcej informacji, zobacz [Using an HDInsight Cluster with Alternate Storage Accounts and Metastores](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx) (Używanie klastra usługi HDInsight z alternatywnymi kontami magazynu i magazynami metadanych).

Obiekty blob mogą być używane z danymi ze strukturą i bez niej. Kontenery obiektów blob przechowują dane jako pary klucz/wartość, bez hierarchii katalogów. Jednak wewnątrz nazwy klucza można użyć znaku ukośnika (/), co sprawi, że będzie wyglądała, jakby plik był przechowywany w ramach struktury katalogów. Na przykład klucz obiektu blob może mieć postać *input/log1.txt*. Katalog *input* w rzeczywistości nie istnieje, ale z powodu obecności znaku ukośnika w nazwie klucza, klucz ma wygląd ścieżki do pliku.

## <a id="benefits"></a>Korzyści z usługi Azure Storage
Wynikowy koszt wydajności związany z nie kolokowanie klastrów obliczeniowych i zasobów magazynu jest zmniejszany przez sposób tworzenia klastrów obliczeniowych w pobliżu zasobów konta magazynu w obrębie regionu Azure, gdzie szybkie sieci zapewniają wydajny obliczenia węzły w celu uzyskania dostępu do danych wewnątrz usługi Azure storage.

Przechowywanie danych w usłudze Azure Storage zamiast w systemie plików HDFS ma wiele zalet:

* **Ponowne użycie danych i udostępniania:** Dane w systemie plików HDFS znajdują się wewnątrz klastra obliczeniowego. Tylko te aplikacje, które mają dostęp do klastra obliczeniowego mogą używać danych za pomocą interfejsów API systemu plików HDFS. Dane w usłudze Azure storage jest możliwy za pośrednictwem interfejsów API systemu plików HDFS lub za pomocą [interfejsów API REST usługi Blob Storage](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API). W związku z tym większy zestaw narzędzi i aplikacji (w tym inne klastry HDInsight) może służyć do tworzenia i wykorzystywania danych.

* **Archiwizacja danych:** Przechowywanie danych w usłudze Azure storage pozwala klastry HDInsight używane do obliczeń, można bezpiecznie usunąć bez utraty danych użytkownika.

* **Koszt magazynowania danych:** Przechowywanie danych w systemie plików DFS w perspektywie długoterminowej jest droższe niż przechowywanie danych w usłudze Azure storage, ponieważ koszt klastra obliczeniowego jest wyższy niż koszt usługi Azure storage. Ponadto ponieważ nie trzeba ponownie ładować danych przy każdej generacji klastra obliczeniowego, oszczędzamy również koszty ładowania danych.

* **Elastyczne skalowanie w poziomie:** Mimo że system plików HDFS zapewnia system plików skalowanych w poziomie, skala jest wyznaczana przez liczbę węzłów tworzonych dla klastra. Zmiana skali może stać się procesem bardziej skomplikowanym w porównaniu z elastycznymi możliwościami skalowania, które można uzyskać automatycznie w usłudze Azure Storage.

* **Replikacja geograficzna:** Usługi Azure storage można replikować geograficznie. Chociaż zapewnia to odzyskiwanie geograficzne i nadmiarowość danych, praca w trybie failover w przypadku lokalizacji zreplikowanych geograficznie poważnie wpływa na wydajność i może pociągać za sobą dodatkowe koszty. Dlatego zalecamy, aby wybierać replikację geograficzną w rozsądny sposób i tylko wtedy, gdy wartość danych uzasadnia ponoszenie dodatkowych kosztów.

Niektóre zadania i pakiety MapReduce mogą tworzyć wyniki pośrednie, których nie potrzeba przechowywać w usłudze Azure Storage. W takim przypadku można zdecydować się na przechowywanie danych w lokalnym systemie plików HDFS. W rzeczywistości HDInsight używa systemu plików DFS dla wielu wyników pośrednich w zadaniach Hive i innych procesach.

> [!NOTE]  
> Większość poleceń systemu plików HDFS (na przykład `ls`, `copyFromLocal` i `mkdir`) nadal działa zgodnie z oczekiwaniami. Tylko polecenia specyficzne dla natywnych implementacji systemu plików HDFS (określanych jako systemu plików DFS), takie jak `fschk` i `dfsadmin`, Pokaż różne zachowanie w usłudze Azure storage.

## <a name="address-files-in-azure-storage"></a>Adresowanie plików w usłudze Azure Storage
Schemat identyfikatora URI do uzyskiwania dostępu do plików w usłudze Azure Storage z usługi HDInsight to:

```config
wasb://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>
```

Schemat identyfikatora URI zapewnia nieszyfrowany dostęp (z prefiksem *wasb:* ) oraz szyfrowany dostęp SSL (z prefiksem *wasbs*). Zalecamy używanie prefiksu *wasbs* wszędzie tam, gdzie to możliwe, nawet w przypadku uzyskiwania dostępu do danych, które znajdują się wewnątrz tego samego regionu w systemie Azure.

`<BlobStorageContainerName>` Identyfikuje nazwę kontenera obiektów blob w usłudze Azure storage.
`<StorageAccountName>` Identyfikuje nazwę konta usługi Azure Storage. Wymagana jest w pełni kwalifikowana nazwa domeny (FQDN).

Jeśli żadna `<BlobStorageContainerName>` ani `<StorageAccountName>` został określony, używany jest domyślny system plików. W przypadku plików w domyślnym systemie plików można używać ścieżki względnej lub bezwzględnej. Na przykład do pliku *hadoop-mapreduce-examples.jar* dostarczanego z klastrami usługi HDInsight można odwoływać się w jeden z następujących sposobów:

```config
wasb://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
wasb:///example/jars/hadoop-mapreduce-examples.jar
/example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]  
> Nazwa pliku jest `hadoop-examples.jar` w klastrach w wersji 2.1 i 1.6 HDInsight.

Ścieżka jest nazwa ścieżki pliku lub katalogu HDFS. Ponieważ kontenery w usłudze Azure storage przechowują pary klucz wartość, nie ma żadnych prawdziwy hierarchiczny system plików. Znak ukośnika (/) wewnątrz klucza obiektu blob jest interpretowany jako separator katalogu. Na przykład nazwą obiektu blob dla pliku *hadoop-mapreduce-examples.jar* jest:

```bash
example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]  
> Podczas pracy z obiektami blob poza usługą HDInsight, większość narzędzi nie rozpoznaje formatu WASB i zamiast tego oczekuje podstawowego formatu ścieżki, takiego jak `example/jars/hadoop-mapreduce-examples.jar`.

##  <a name="blob-containers"></a>Kontenery obiektów blob
Aby użyć obiektów blob, należy najpierw utworzyć [konta usługi Azure Storage](../storage/common/storage-create-storage-account.md). W ramach tego procesu należy wskazać region świadczenia usługi Azure, w którym zostanie utworzone konto magazynu. Klaster i konto magazynu muszą być hostowane w tym samym regionie. Bazy danych programu SQL Server magazynu metadanych Hive i baza danych SQL Server magazynu metadanych programu Apache Oozie również muszą znajdować się w tym samym regionie.

Wszędzie tam, gdzie go umieszczono, każdy utworzony obiekt blob należy do kontenera na koncie usługi Azure Storage. Ten kontener może być istniejącym obiektem blob utworzonym poza usługą HDInsight lub może być kontenerem, który jest tworzony dla klastra usługi HDInsight.

Domyślny kontener obiektów blob przechowuje informacje dotyczące klastra, takie jak dzienniki i historia zadań. Nie należy współużytkować domyślnego kontenera obiektów blob dla wielu klastrów usługi HDInsight. Może to spowodować uszkodzenie historii zadań. Zalecane jest stosowanie różnych kontenerów do każdego klastra i umieszczanie udostępnionych danych w połączonym koncie magazynu określonym we wdrożeniu wszystkich odpowiednich klastrów zamiast domyślnego konta magazynu. Aby uzyskać więcej informacji na temat konfigurowania połączonych kont magazynu, zobacz [klastrów HDInsight tworzenie](hdinsight-hadoop-provision-linux-clusters.md). Jednak po usunięciu oryginalnego klastra usługi HDInsight można ponownie użyć domyślnego kontenera magazynu. W przypadku klastrów HBase można zachować schemat tabeli HBase i dane przez utworzenie nowego klastra HBase przy użyciu domyślnego kontenera obiektów blob, używanego przez klaster HBase, który został usunięty.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

## <a name="interacting-with-azure-storage"></a>Wchodzenie w interakcje z usługą Azure storage

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

## <a name="next-steps"></a>Kolejne kroki

W tym artykule przedstawiono sposób korzystania z magazynu Azure zgodnego z systemem plików HDFS w połączeniu z usługą HDInsight. Podane tu informacje umożliwiają tworzenie skalowalnych, długoterminowych rozwiązań do pozyskiwania danych archiwalnych i używanie usługi HDInsight w celu efektywnego wykorzystywania informacji przechowywanych w postaci danych ze strukturą i bez niej.

Aby uzyskać więcej informacji, zobacz:

* [Rozpoczynanie pracy z usługą Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Wprowadzenie do usługi Azure Data Lake Storage](../data-lake-store/data-lake-store-get-started-portal.md)
* [Przekazywanie danych do HDInsight](hdinsight-upload-data.md)
* [Use Apache Hive z HDInsight](hadoop/hdinsight-use-hive.md)
* [Apache Pig za pomocą HDInsight](hadoop/hdinsight-use-pig.md)
* [Ograniczania dostępu do danych za pomocą HDInsight za pomocą sygnatur dostępu współdzielonego magazynu platformy Azure](hdinsight-storage-sharedaccesssignature-permissions.md)
* [Korzystanie z usługi Azure Data Lake Storage Gen2 w połączeniu z klastrami usługi Azure HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)