---
title: Scenariusze danych dotyczących usługi Azure Data Lake Storage Gen2 | Dokumentacja firmy Microsoft
description: Zapoznać się z różnymi scenariuszami i narzędzi przy użyciu danych, które można pozyskiwane, przetwarzane, pobrane i wizualizowane w Gen2 magazynu Data Lake z (wcześniej znane jako usługi Azure Data Lake Store)
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: normesta
ms.openlocfilehash: fd3875c5c78a02efab1251166ec7113902be3e08
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723240"
---
# <a name="using-azure-data-lake-storage-gen2-for-big-data-requirements"></a>Za pomocą usługi Azure Data Lake Storage Gen2 dla wymagających danych big Data

Przetwarzanie danych big Data są czterech etapach klucza:

> [!div class="checklist"]
> * Wprowadzania dużych ilości danych w magazynie danych w czasie rzeczywistym lub w partiach
> * Przetwarzanie danych
> * Pobieranie danych
> * Wizualizację danych

Rozpocznij od utworzenia konta magazynu i system plików. Następnie należy przyznać dostęp do danych. Pierwsze kilka sekcje w tym artykule ułatwiają wykonywanie tych zadań. W pozostałych sekcjach podkreślimy narzędzia i opcje dla każdej fazy przetwarzania.

## <a name="create-a-data-lake-storage-gen2-account"></a>Tworzenie konta Data Lake Storage Gen2

Konta Data Lake Storage Gen2 jest konto magazynu, który ma hierarchicznej przestrzeni nazw. 

Aby go utworzyć, zobacz [Szybki Start: Tworzenie konta magazynu Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="create-a-file-system"></a>Tworzenie systemu plików

A *system plików* jest kontenerem dla plików i folderów. Należy co najmniej jeden z nich, aby rozpocząć, umożliwiając pozyskiwanie danych na koncie magazynu.  Oto lista narzędzi służących do ich utworzenia.

|Narzędzie | Wskazówki |
|---|--|
|Eksplorator usługi Azure Storage | [Tworzenie systemu plików przy użyciu Eksploratora usługi Storage](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-explorer#create-a-file-system) |
|Narzędzie AzCopy | [Tworzenie kontenera obiektów Blob lub udziału plików przy użyciu AzCopyV10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10#transfer-files)|
|Plik usługi Hadoop (HDFS) systemu interfejsu wiersza polecenia (CLI) z HDInsight |[Tworzenie systemu plików przy użyciu systemu plików HDFS z HDInsight](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-hdfs-data-lake-storage?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-a-file-system) |
|Kod w usłudze Azure notesu usługi Databricks|[Tworzenie systemu plików konta magazynu (w języku Scala)](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-databricks-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-storage-account-file-system) <br><br> [Tworzenie systemu plików i zainstalować go (Python)](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-databricks-spark?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-a-file-system-and-mount-it)|

Najłatwiej można utworzyć systemów plików za pomocą Eksploratora usługi Storage lub narzędzia AzCopy. Zajmuje trochę więcej pracy do tworzenia systemów plików za pomocą HDInsight i usługi Databricks. Jednak jeśli zamierzasz używać HDInsight lub Databricks klastrów do przetwarzania danych, mimo to, następnie może najpierw utworzyć klastry usługi i użyć interfejsu wiersza polecenia systemu plików HDFS z systemami plików Utwórz.  

## <a name="grant-access-to-the-data"></a>Udzielanie dostępu do danych

Skonfiguruj odpowiednie uprawnienia dostępu do konta i danych na swoim koncie, przed rozpoczęciem wprowadzania danych.

Ma trzy sposoby, w celu udzielenia dostępu:

* Przypisać jedną z tych ról do użytkowników, grupy, tożsamości zarządzanej przez użytkownika lub nazwy głównej usługi:

  [Storage Blob Data Reader](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader)

  [Współautor danych obiektu Blob magazynu](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)

  [Właściciel danych obiektu Blob magazynu](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

* Używają tokenu sygnatury dostępu współdzielonego (SAS).

* Użyj klucza konta magazynu.

Ta tabela pokazuje, jak udzielić dostępu dla każdej usługi platformy Azure lub narzędzia.

|Narzędzie | Aby udzielić dostępu | Wskazówki |
|---|--|---|
|Storage Explorer| Przypisywanie roli do użytkowników i grup | [Przypisywanie ról administratora i użytkowników niebędących administratorami do użytkowników z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal) |
|Narzędzie AzCopy| Przypisywanie roli do użytkowników i grup <br>**lub**<br> Użyj tokenu sygnatury dostępu Współdzielonego| [Przypisywanie ról administratora i użytkowników niebędących administratorami do użytkowników z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)<br><br>[Łatwo utworzyć sygnaturę dostępu Współdzielonego, aby pobrać plik z usługi Azure Storage — za pomocą Eksploratora usługi Azure Storage](https://blogs.msdn.microsoft.com/jpsanders/2017/10/12/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer/)|
|Apache DistCp | Przypisywanie roli do zarządzanych tożsamości przypisanych przez użytkownika | [Tworzenie klastra usługi HDInsight za pomocą Data Lake Storage Gen2](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2) |
|Azure Data Factory| Przypisywanie roli do tożsamości przypisanych — zarządzane przez użytkownika<br>**lub**<br> Przypisz rolę wymaganą do nazwy głównej usługi<br>**lub**<br> Użyj klucza konta magazynu | [Właściwości usługi połączonej](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#linked-service-properties) |
|Azure HDInsight| Przypisywanie roli do zarządzanych tożsamości przypisanych przez użytkownika | [Tworzenie klastra usługi HDInsight za pomocą Data Lake Storage Gen2](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)|
|Azure Databricks| Przypisywanie roli do jednostki usługi | [Instrukcje: Aby utworzyć aplikację usługi Azure AD i jednostkę usługi, które mogą uzyskiwać dostęp do zasobów, użyj portalu](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)|

Aby udzielić dostępu do określonych plików i folderów, zobacz następujące artykuły.

* [Zestaw plików i katalogów poziomu uprawnień przy użyciu Eksploratora usługi Azure Storage za pomocą usługi Azure Data Lake Storage Gen2](https://review.docs.microsoft.com/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer)

* [Listy kontroli dostępu do plików i katalogów](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control#access-control-lists-on-files-and-directories)

Aby dowiedzieć się więcej o konfigurowaniu innych aspektów zabezpieczeń, zobacz [Przewodnik po zabezpieczeniach usługi Azure Data Lake Storage Gen2](https://review.docs.microsoft.com/azure/storage/common/storage-data-lake-storage-security-guide?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="ingest-the-data"></a>Pozyskiwanie danych

W tej sekcji opisano różne źródła danych i różne sposoby, w którym można pozyskać danych do konta Data Lake Storage Gen2.

![Pozyskiwanie danych do Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/ingest-data.png "pozyskiwania danych w Data Lake Storage Gen2")

### <a name="ad-hoc-data"></a>Danych ad hoc

Ta pozycja reprezentuje mniejszych zestawów danych, które są używane do tworzenia prototypów aplikacji danych big data. Istnieją różne sposoby pozyskiwania danych ad hoc w zależności od źródła danych. 

Oto lista narzędzi, których można użyć w celu pozyskiwania danych ad hoc.

| Źródło danych | Za pomocą pozyskiwania |
| --- | --- |
| Komputer lokalny |[Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)<br><br>[Narzędzie AzCopy](../common/storage-use-azcopy-v10.md)|
| Azure Storage Blob |[Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md)<br><br>[Narzędzie AzCopy](../common/storage-use-azcopy-v10.md)<br><br>[Uruchomione w klastrze HDInsight narzędzia DistCp](data-lake-storage-use-distcp.md)|

### <a name="streamed-data"></a>Dane przesyłane strumieniowo

Reprezentuje dane, które mogą być generowane przez różnych źródeł, takich jak aplikacje, urządzenia, czujników itp. Te dane można pozyskać do Sorage Gen2 jeziora danych za pomocą różnych narzędzi. Te narzędzia będą zwykle przechwytywania i przetwarzania danych na podstawie zdarzeń, zdarzenia w czasie rzeczywistym i następnie zapisywania zdarzeń w partiach w Data Lake Storage Gen2 dzięki czemu mogą być dalej przetwarzane.

Oto lista narzędzi, których można użyć, aby pozyskiwać dane przesyłane strumieniowo.

|Narzędzie | Wskazówki |
|---|--|
|Usługa Azure HDInsight Storm | [Zapisu Apache Hadoop HDFS z systemu Storm Apache na HDInsight](https://docs.microsoft.com/azure/hdinsight/storm/apache-storm-write-data-lake-store) |

### <a name="relational-data"></a>Dane relacyjne

Można również źródła danych z relacyjnej bazy danych. W okresie czasu relacyjne bazy danych zbierania bardzo duże ilości danych, co może zapewnić kluczowych wniosków, przetwarzanie za pomocą potoku danych big data. Aby przenieść dane do Data Lake Storage Gen2, można użyć następujących narzędzi.

Oto lista narzędzi, których można użyć w celu pozyskiwania danych relacyjnych.

|Narzędzie | Wskazówki |
|---|--|
|Azure Data Factory | [Działanie kopiowania w usłudze Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview) |

### <a name="web-server-log-data-upload-using-custom-applications"></a>Dane dzienników serwera sieci Web (przy użyciu niestandardowych aplikacji przekazywania)

Ten typ zestawu danych zwrócono szczególną ponieważ analizy danych dziennika serwera sieci web jest typowy przypadek użycia dla aplikacji przetwarzających dane big Data i wymaga dużych woluminów, plików dziennika do przesłania do Data Lake Storage Gen2. Jedną z następujących narzędzi umożliwia pisanie własnego skryptu lub aplikacji do przekazania takich danych.

Oto lista narzędzi, których można użyć w celu pozyskiwania danych dzienników serwera sieci Web.

|Narzędzie | Wskazówki |
|---|--|
|Azure Data Factory | [Działanie kopiowania w usłudze Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview)  |

Do przekazywania danych dzienników serwera sieci web, a także do przekazywania innych typów danych (np. Dane społecznościowe tonacji) jest dobrym sposobem zapisu własnych niestandardowych skryptów/aplikacji, ponieważ zapewnia elastyczność obejmują przekazywanie składnik jako część danych większej aplikacji danych big data. W niektórych przypadkach ten kod może mieć postać skryptu lub narzędzie wiersza polecenia proste. W innych przypadkach kod może służyć do przetwarzania danych big Data Integrowanie aplikacji biznesowych lub rozwiązań.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Dane skojarzone z klastrami usługi Azure HDInsight

Większość typów klastrów HDInsight (Hadoop, HBase, Storm) obsługują Data Lake Storage Gen2 jako repozytorium magazynów danych. Klastry HDInsight dostęp do danych z usługi Azure blob Storage (WASB). Lepszą wydajność możesz skopiować dane z WASB do konta Data Lake Storage Gen2 skojarzonego z klastrem. Aby skopiować dane, można użyć następujących narzędzi.

Oto lista narzędzi, których można użyć w celu pozyskiwania danych związanych z klastrami HDInsight.

|Narzędzie | Wskazówki |
|---|--|
|Apache DistCp | [Kopiowanie danych między obiektów blob usługi Azure Storage i Azure Data Lake Storage Gen2 za pomocą narzędzia DistCp](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-distcp) |
|Narzędzie AzCopy | [Transferowanie danych za pomocą narzędzia AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10) |
|Azure Data Factory | [Kopiuj dane do / z usługi Azure Data Lake Storage Gen2 przy użyciu usługi Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2) |

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>Dane przechowywane w lokalnych lub IaaS Hadoop klastrów

Duże ilości danych mogą być przechowywane w istniejących klastrów usługi Hadoop, lokalnie na komputerach przy użyciu systemu plików HDFS. Klastry usługi Hadoop może być we wdrożeniu w środowisku lokalnym lub może znajdować się w klastrze usługi IaaS na platformie Azure. Może to być wymagania dotyczące kopiowania tych danych do usługi Azure Data Lake Storage Gen2 jednorazowe podejście lub w sposób cykliczne. Istnieją różne opcje, których można to osiągnąć. Poniżej przedstawiono listę alternatywnych skojarzone wady i zalety.

| Podejście | Szczegóły | Zalety | Zagadnienia do rozważenia |
| --- | --- | --- | --- |
| Kopiowanie danych bezpośrednio z klastrami usługi Hadoop do usługi Azure Data Lake Storage Gen2 za pomocą usługi Azure Data Factory (ADF) |[ADF obsługuje system plików HDFS jako źródło danych](../../data-factory/connector-hdfs.md) |ADF zapewnia obsługę poza pole dla systemu plików HDFS oraz najwyższej klasy end-to-end, zarządzania i monitorowania |Wymaga bramy zarządzania danymi do wdrożenia lokalnie lub w modelu IaaS klastra |
| Kopiowanie danych z usługi Hadoop do usługi Azure Storage za pomocą narzędzia Distcp. Następnie skopiuj dane z usługi Azure Storage Data Lake Storage Gen2 przy użyciu odpowiedniego mechanizmu. |Możesz skopiować dane z usługi Azure Storage, Data Lake Storage Gen2 za pomocą: <ul><li>[Azure Data Factory](../../data-factory/copy-activity-overview.md)</li><li>[Narzędzie AzCopy](../common/storage-use-azcopy-v10.md)</li><li>[Apache DistCp uruchamianie w klastrach HDInsight](data-lake-storage-use-distcp.md)</li></ul> |Można użyć narzędzi typu open source. |Wieloetapowy proces, który obejmuje wiele technologii |

### <a name="really-large-datasets"></a>Bardzo dużych zestawów danych

Do przekazywania zestawów danych, które zakresu w kilku terabajtów, za pomocą metod opisanych wyżej czasami może być powolne i kosztowne. W takich przypadkach można użyć usługi Azure ExpressRoute.  

Usługa Azure ExpressRoute umożliwia tworzenie prywatnych połączeń między centrami danych platformy Azure a infrastrukturą lokalną. Zapewnia niezawodne opcja przesyłania dużych ilości danych. Aby dowiedzieć się więcej, zobacz [dokumentacji usługi Azure ExpressRoute](../../expressroute/expressroute-introduction.md).

## <a name="process-the-data"></a>Przetwarzanie danych

Gdy dane są dostępne w Data Lake Storage Gen2 analizy można uruchomić na tych danych przy użyciu aplikacji obsługiwanej danych big data. 

![Analizowanie danych w Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/analyze-data.png "analizy danych Data Lake Storage Gen2")

Oto lista narzędzi, których można uruchamiać zadania analizy danych na dane, które są przechowywane w Data Lake Storage Gen2.

|Narzędzie | Wskazówki |
|---|--|
|Azure HDInsight | [Korzystanie z usługi Azure Data Lake Storage Gen2 w połączeniu z klastrami usługi Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2) |
|Azure Databricks | [Azure Data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html)<br><br>[Szybki start: Analizowanie danych w usłudze Azure Data Lake magazynu Gen2 przy użyciu usługi Azure Databricks](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-databricks-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br><br>[Samouczek: Wyodrębnianie, przekształcanie i ładowanie danych za pomocą usługi Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

## <a name="visualize-the-data"></a>Wizualizacja danych

Kombinacja services umożliwia tworzenie wizualnej reprezentacji danych przechowywanych w Data Lake Storage Gen2.

![Wizualizowanie danych w Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/visualize-data.png "wizualizować dane w Data Lake Storage Gen2")

* Można uruchomić za pomocą [usługi Azure Data Factory, aby przenieść dane z Gen2 magazynu jezioro danych Azure SQL Data Warehouse](../../data-factory/copy-activity-overview.md)
* Po tym można [Integracja usługi Power BI z usługą Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md) do tworzenia wizualnych reprezentacji danych.

## <a name="download-the-data"></a>Pobierz dane

Można także pobrać lub przenieść dane z usługi Azure Data Lake Storage Gen2 dla scenariuszy, takich jak:

* Przenoszenie danych do innych repozytoriów w interfejsie przy użyciu istniejących potoków przetwarzania danych. Na przykład można przenieść dane z Data Lake Storage Gen2 do usługi Azure SQL Database lub lokalnego programu SQL Server.

* Pobierz dane na komputerze lokalnym do przetworzenia w środowiskach IDE podczas kompilowania aplikacji prototypów.

![Ruch wychodzący danych z Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/egress-data.png "ruch wychodzący danych z Data Lake Storage Gen2")

Oto lista narzędzi, których można użyć, aby pobrać dane z Data Lake Storage Gen2.

|Narzędzie | Wskazówki |
|---|--|
|Azure Data Factory | [Działanie kopiowania w usłudze Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview) |
|Apache DistCp | [Kopiowanie danych między obiektów blob usługi Azure Storage i Azure Data Lake Storage Gen2 za pomocą narzędzia DistCp](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-distcp) |
