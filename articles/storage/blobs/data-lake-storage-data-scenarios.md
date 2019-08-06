---
title: Scenariusze danych dotyczące Azure Data Lake Storage Gen2 | Microsoft Docs
description: Poznaj różne scenariusze i narzędzia, za pomocą których dane mogą być pozyskiwane, przetwarzane, pobierane i wizualizowane w Data Lake Storage Gen2 (wcześniej znane jako Azure Data Lake Store)
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: normesta
ms.openlocfilehash: 010b7bc38caf83c12dd0d8b8e731fdbad6e45256
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2019
ms.locfileid: "68422867"
---
# <a name="using-azure-data-lake-storage-gen2-for-big-data-requirements"></a>Używanie Azure Data Lake Storage Gen2 do wymagań dotyczących danych Big Data

Istnieją cztery kluczowe etapy przetwarzania danych Big Data:

> [!div class="checklist"]
> * Pozyskiwanie dużych ilości danych w magazynie danych w czasie rzeczywistym lub w partiach
> * Przetwarzanie danych
> * Pobieranie danych
> * Wizualizowanie danych

Zacznij od utworzenia konta magazynu i systemu plików. Następnie Udziel dostępu do danych. Niektóre pierwsze sekcje tego artykułu ułatwiają wykonywanie tych zadań. W pozostałych sekcjach wyróżnimy opcje i narzędzia dla każdej fazy przetwarzania.

## <a name="create-a-data-lake-storage-gen2-account"></a>Utwórz konto Data Lake Storage Gen2

Konto Data Lake Storage Gen2 to konto magazynu z hierarchiczną przestrzenią nazw. 

Aby go utworzyć, zobacz [szybki start: Utwórz konto](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)magazynu Azure Data Lake Storage Gen2.

## <a name="create-a-file-system"></a>Tworzenie systemu plików

*System plików* jest kontenerem dla folderów i plików. Potrzebujesz co najmniej jednego z nich, aby rozpocząć pozyskiwanie danych na koncie magazynu.  Poniżej znajduje się lista narzędzi, których można użyć, aby je utworzyć.

|Tool | Wskazówki |
|---|--|
|Eksplorator usługi Azure Storage | [Utwórz system plików za pomocą Eksplorator usługi Storage](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-explorer#create-a-file-system) |
|Narzędzie AzCopy | [Tworzenie kontenera obiektów blob lub udziału plików za pomocą AzCopyV10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10#transfer-files)|
|Interfejs wiersza polecenia (CLI) systemu plików Hadoop z usługą HDInsight |[Utwórz system plików za pomocą systemu HDFS z usługą HDInsight](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-hdfs-data-lake-storage?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-a-file-system) |
|Kod w notesie Azure Databricks|[Utwórz system plików konta magazynu (Scala)](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-databricks-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-storage-account-file-system) <br><br> [Utwórz system plików i zainstaluj go (Python)](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-databricks-spark?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-a-file-system-and-mount-it)|

Najłatwiej jest tworzyć systemy plików przy użyciu Eksplorator usługi Storage lub AzCopy. Tworzenie systemów plików przy użyciu usługi HDInsight i datakostki zajmuje nieco więcej pracy. Jeśli jednak planujesz używać klastrów usługi HDInsight lub datakostki do przetwarzania danych, wówczas możesz najpierw utworzyć klastry i użyć interfejsu wiersza polecenia systemu plików HDFS do tworzenia systemów.  

## <a name="grant-access-to-the-data"></a>Udzielanie dostępu do danych

Przed rozpoczęciem pozyskiwania danych skonfiguruj odpowiednie uprawnienia dostępu do swojego konta oraz dane na koncie.

Istnieją trzy sposoby udzielenia dostępu:

* Przypisz jedną z tych ról do użytkownika, grupy, tożsamości zarządzanej przez użytkownika lub nazwy głównej usługi:

  [Czytnik danych obiektów blob magazynu](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader)

  [Współautor danych obiektu blob magazynu](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)

  [Właściciel danych obiektów blob magazynu](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

* Użyj tokenu sygnatury dostępu współdzielonego (SAS).

* Użyj klucza konta magazynu.

W tej tabeli przedstawiono sposób udzielania dostępu do poszczególnych usług lub narzędzi platformy Azure.

|Tool | Aby udzielić dostępu | Wskazówki |
|---|--|---|
|Eksplorator usługi Storage| Przypisywanie roli do użytkowników i grup | [Przypisywanie ról administratorów i niebędących administratorami do użytkowników z Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal) |
|Narzędzie AzCopy| Przypisywanie roli do użytkowników i grup <br>**lub**<br> Używanie tokenu sygnatury dostępu współdzielonego| [Przypisywanie ról administratorów i niebędących administratorami do użytkowników z Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)<br><br>[Łatwo twórz sygnaturę dostępu współdzielonego, aby pobrać plik z usługi Azure Storage — za pomocą Eksplorator usługi Azure Storage](https://blogs.msdn.microsoft.com/jpsanders/2017/10/12/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer/)|
|Apache DistCp | Przypisywanie roli do tożsamości zarządzanej przypisanej przez użytkownika | [Tworzenie klastra usługi HDInsight z Data Lake Storage Gen2](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2) |
|Azure Data Factory| Przypisywanie roli do tożsamości zarządzanej przypisanej przez użytkownika<br>**lub**<br> Przypisz roli do nazwy głównej usługi<br>**lub**<br> Korzystanie z klucza konta magazynu | [Właściwości połączonej usługi](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#linked-service-properties) |
|Azure HDInsight| Przypisywanie roli do tożsamości zarządzanej przypisanej przez użytkownika | [Tworzenie klastra usługi HDInsight z Data Lake Storage Gen2](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)|
|Azure Databricks| Przypisywanie roli do nazwy głównej usługi | [Instrukcje: Korzystanie z portalu do tworzenia aplikacji usługi Azure AD i nazwy głównej usługi, która może uzyskiwać dostęp do zasobów](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)|

Aby udzielić dostępu do określonego pliku i folderów, zapoznaj się z tymi artykułami.

* [Ustawianie uprawnień na poziomie plików i katalogów przy użyciu Eksplorator usługi Azure Storage z Azure Data Lake Storage Gen2](https://review.docs.microsoft.com/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer)

* [Listy kontroli dostępu do plików i katalogów](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control#access-control-lists-on-files-and-directories)

Aby dowiedzieć się więcej o konfigurowaniu innych aspektów zabezpieczeń, zobacz [Azure Data Lake Storage Gen2 Przewodnik po zabezpieczeniach](https://docs.microsoft.com/azure/storage/common/storage-data-lake-storage-security-guide?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="ingest-the-data"></a>Pozyskiwanie danych

W tej sekcji przedstawiono różne źródła danych oraz różne sposoby, w których dane mogą być pozyskiwane na koncie Data Lake Storage Gen2.

Pozyskiwanie ![danych w Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/ingest-data.png "Pozyskiwanie danych w Data Lake Storage Gen2")

### <a name="ad-hoc-data"></a>Dane ad hoc

Reprezentuje to mniejsze zestawy danych, które są używane do prototypowania aplikacji danych Big Data. Istnieją różne sposoby pozyskiwania danych ad hoc w zależności od źródła danych. 

Poniżej znajduje się lista narzędzi, których można użyć do pozyskiwania danych ad hoc.

| Źródło danych | Pozyskiwanie przy użyciu |
| --- | --- |
| Komputer lokalny |[Eksplorator usługi Storage](https://azure.microsoft.com/features/storage-explorer/)<br><br>[Narzędzie AzCopy](../common/storage-use-azcopy-v10.md)|
| Azure Storage Blob |[Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md)<br><br>[Narzędzie AzCopy](../common/storage-use-azcopy-v10.md)<br><br>[Pomocą distcp uruchomione w klastrze usługi HDInsight](data-lake-storage-use-distcp.md)|

### <a name="streamed-data"></a>Dane przesyłane strumieniowo

Reprezentuje to dane, które mogą być generowane przez różne źródła, takie jak aplikacje, urządzenia, czujniki itp. Te dane można pozyskać do Data Lake Sorage Gen2 za pomocą różnych narzędzi. Te narzędzia zwykle przechwytuje i przetwarzają dane na zdarzeniach w czasie rzeczywistym, a następnie zapisują zdarzenia w partiach do Data Lake Storage Gen2 tak, aby mogły być dalej przetwarzane.

Poniżej znajduje się lista narzędzi, których można użyć do pozyskiwania danych przesyłanych strumieniowo.

|Tool | Wskazówki |
|---|--|
|Azure HDInsight — burza | [Zapisywanie do Apache Hadoop HDFS z Apache Storm w usłudze HDInsight](https://docs.microsoft.com/azure/hdinsight/storm/apache-storm-write-data-lake-store) |

### <a name="relational-data"></a>Dane relacyjne

Możesz również uzyskać dane źródłowe z relacyjnych baz danych. Przez pewien czas relacyjne bazy danych zbierają ogromne ilości danych, które mogą zapewnić kluczowe informacje, jeśli są przetwarzane za pośrednictwem potoku danych Big Data. Aby przenieść takie dane do Data Lake Storage Gen2, można użyć następujących narzędzi.

Poniżej znajduje się lista narzędzi, których można użyć do pozyskiwania danych relacyjnych.

|Tool | Wskazówki |
|---|--|
|Azure Data Factory | [Działanie kopiowania w usłudze Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview) |

### <a name="web-server-log-data-upload-using-custom-applications"></a>Dane dziennika serwera sieci Web (przekazywanie przy użyciu aplikacji niestandardowych)

Ten typ zestawu danych jest specyficzny dla tego typu, ponieważ analiza danych dzienników serwera sieci Web jest typowym przypadkiem użycia dla aplikacji Big Data i wymaga przekazywania dużych ilości plików dziennika do Data Lake Storage Gen2. Możesz użyć dowolnego z poniższych narzędzi do pisania własnych skryptów lub aplikacji w celu przekazania takich danych.

Poniżej znajduje się lista narzędzi, których można użyć do pozyskiwania danych dziennika serwera sieci Web.

|Tool | Wskazówki |
|---|--|
|Azure Data Factory | [Działanie kopiowania w usłudze Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview)  |

Do przekazywania danych dziennika serwera sieci Web, a także do przekazywania innych rodzajów danych (np. danych społecznościowych mową), dobrym rozwiązaniem jest napisanie własnych niestandardowych skryptów/aplikacji, ponieważ zapewnia ona elastyczność dołączania składnika do przekazywania danych w ramach aplikacja o większej ilości danych Big Data. W niektórych przypadkach ten kod może mieć postać skryptu lub prostego narzędzia wiersza polecenia. W innych przypadkach kod może służyć do integrowania przetwarzania danych Big Data z aplikacją biznesową lub rozwiązaniem.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Dane skojarzone z klastrami usługi Azure HDInsight

Większość typów klastrów usługi HDInsight (Hadoop, HBase, burza) obsługuje Data Lake Storage Gen2 jako repozytorium magazynu danych. Klastry HDInsight uzyskują dostęp do danych z obiektów BLOB usługi Azure Storage (WASB). Aby uzyskać lepszą wydajność, można skopiować dane z WASB do konta Data Lake Storage Gen2 skojarzonego z klastrem. Aby skopiować dane, można użyć następujących narzędzi.

Poniżej znajduje się lista narzędzi, których można użyć do pozyskiwania danych skojarzonych z klastrami usługi HDInsight.

|Tool | Wskazówki |
|---|--|
|Apache DistCp | [Użyj pomocą distcp do kopiowania danych między obiektami BLOB usługi Azure Storage a Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-distcp) |
|Narzędzie AzCopy | [Transferowanie danych za pomocą AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10) |
|Azure Data Factory | [Kopiowanie danych do lub z Azure Data Lake Storage Gen2 przy użyciu Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2) |

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>Dane przechowywane w lokalnych lub IaaSych klastrach Hadoop

Duże ilości danych mogą być przechowywane w istniejących klastrach usługi Hadoop lokalnie na maszynach przy użyciu systemu plików HDFS. Klastry Hadoop mogą znajdować się w lokalnym wdrożeniu lub znajdować się w klastrze IaaS na platformie Azure. Mogą istnieć wymagania, aby skopiować takie dane do Azure Data Lake Storage Gen2 na potrzeby jednego podejścia lub w sposób cykliczny. Istnieją różne opcje, których można użyć w celu osiągnięcia tego celu. Poniżej znajduje się lista alternatyw i skojarzonych z nimi zalet.

| Podejście | Szczegóły | Zalety | Zagadnienia do rozważenia |
| --- | --- | --- | --- |
| Użyj Azure Data Factory (ADF), aby skopiować dane bezpośrednio z klastrów usługi Hadoop do Azure Data Lake Storage Gen2 |[ADF jako źródło danych obsługuje system plików HDFS](../../data-factory/connector-hdfs.md) |System ADF oferuje wbudowaną obsługę systemu plików HDFS oraz kompleksowe zarządzanie i monitorowanie w pierwszej klasie |Wymaga wdrożenia bramy Zarządzanie danymi w środowisku lokalnym lub w klastrze IaaS |
| Użyj pomocą distcp, aby skopiować dane z platformy Hadoop do usługi Azure Storage. Następnie skopiuj dane z usługi Azure Storage do Data Lake Storage Gen2 przy użyciu odpowiedniego mechanizmu. |Dane z usługi Azure Storage można kopiować do Data Lake Storage Gen2 przy użyciu: <ul><li>[Azure Data Factory](../../data-factory/copy-activity-overview.md)</li><li>[Narzędzie AzCopy](../common/storage-use-azcopy-v10.md)</li><li>[Usługa Apache pomocą distcp uruchomiona w klastrach usługi HDInsight](data-lake-storage-use-distcp.md)</li></ul> |Można używać narzędzi open source. |Wieloetapowy proces, który obejmuje wiele technologii |

### <a name="really-large-datasets"></a>Bardzo duże zestawy danych

Do przekazywania zestawów danych, które mają zakres w kilku terabajtach, korzystanie z metod opisanych powyżej może być czasami powolne i kosztowne. W takich przypadkach można użyć usługi Azure ExpressRoute.  

Usługa Azure ExpressRoute umożliwia tworzenie prywatnych połączeń między centrami danych platformy Azure i infrastrukturą lokalną. Zapewnia to niezawodne rozwiązanie do przenoszenia dużych ilości danych. Aby dowiedzieć się więcej, zobacz [dokumentację usługi Azure ExpressRoute](../../expressroute/expressroute-introduction.md).

## <a name="process-the-data"></a>Przetwarzanie danych

Gdy dane są dostępne w Data Lake Storage Gen2 można przeprowadzić analizę tych danych przy użyciu obsługiwanych aplikacji danych Big Data. 

![Analizowanie danych w Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/analyze-data.png "Analizowanie danych w Data Lake Storage Gen2")

Poniżej znajduje się lista narzędzi, których można użyć do uruchamiania zadań analizy danych na danych przechowywanych w Data Lake Storage Gen2.

|Tool | Wskazówki |
|---|--|
|Azure HDInsight | [Korzystanie z usługi Azure Data Lake Storage Gen2 w połączeniu z klastrami usługi Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2) |
|Azure Databricks | [Azure Data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html)<br><br>[Szybki start: Analizowanie danych w Azure Data Lake Storage Gen2 przy użyciu Azure Databricks](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-databricks-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br><br>[Samouczek: Wyodrębnianie, przekształcanie i ładowanie danych przy użyciu Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

## <a name="visualize-the-data"></a>Wizualizacja danych

Za pomocą różnych usług można tworzyć wizualne reprezentacje danych przechowywanych w Data Lake Storage Gen2.

![Wizualizowanie danych w Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/visualize-data.png "Wizualizowanie danych w Data Lake Storage Gen2")

* Za pomocą [Azure Data Factory można przenieść dane z Data Lake Storage Gen2 do Azure SQL Data Warehouse](../../data-factory/copy-activity-overview.md)
* Następnie można [zintegrować Power BI z Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md) , aby utworzyć wizualną reprezentację danych.

## <a name="download-the-data"></a>Pobierz dane

Może być również konieczne pobranie lub przeniesienie danych z Azure Data Lake Storage Gen2 w scenariuszach takich jak:

* Przenieś dane do innych repozytoriów do interfejsu z istniejącymi potokami przetwarzania danych. Na przykład możesz chcieć przenieść dane z Data Lake Storage Gen2 do Azure SQL Database lub SQL Server lokalnych.

* Pobieranie danych na komputer lokalny na potrzeby przetwarzania w środowiskach IDE podczas budowania prototypów aplikacji.

![Dane wyjściowe z Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/egress-data.png "Dane wyjściowe z Data Lake Storage Gen2")

Poniżej znajduje się lista narzędzi, których można użyć do pobierania danych z Data Lake Storage Gen2.

|Tool | Wskazówki |
|---|--|
|Azure Data Factory | [Działanie kopiowania w usłudze Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview) |
|Apache DistCp | [Użyj pomocą distcp do kopiowania danych między obiektami BLOB usługi Azure Storage a Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-distcp) |
