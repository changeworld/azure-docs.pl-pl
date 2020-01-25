---
title: Przenoszenie danych do magazynu obiektów Blob za pomocą łączników SSIS - zespołu danych dla celów naukowych
description: Dowiedz się, jak przenosić dane do lub z usługi Azure Blob Storage przy użyciu pakietu SQL Server Integration Services Feature Pack dla platformy Azure.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 77bfd9d5bcae7bedd673354e32464d5f59bdc9b4
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720875"
---
# <a name="move-data-to-or-from-azure-blob-storage-using-ssis-connectors"></a>Przenoszenie danych do i z usługi Azure Blob Storage za pomocą łączników SSIS
[Programu SQL Server Integration Services Feature Pack dla systemu Azure](https://msdn.microsoft.com/library/mt146770.aspx) zawiera składniki do połączenia z platformą Azure, transfer danych między Azure i lokalnych źródeł danych i przetwarzania danych przechowywanych na platformie Azure.

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

Po przeniesieniu danych lokalnych do chmury klienci mogą uzyskać dostęp do swoich danych z dowolnej usługi platformy Azure, aby wykorzystać pełną moc zestawu technologii platformy Azure. Dane mogą być następnie używane, na przykład w Azure Machine Learning lub w klastrze usługi HDInsight.

Przykłady dotyczące korzystania z tych zasobów platformy Azure znajdują się w przewodnikach [SQL](sql-walkthrough.md) i [HDInsight](hive-walkthrough.md) .

Omówienie canonical scenariuszy, w których korzystanie z usług SSIS do wykonywania potrzeb biznesowych, często używany w scenariusze integracji danych hybrydowych, zobacz [robić więcej przy użyciu programu SQL Server Integration Services Feature Pack dla systemu Azure](https://blogs.msdn.com/b/ssis/archive/2015/06/25/doing-more-with-sql-server-integration-services-feature-pack-for-azure.aspx) blogu.

> [!NOTE]
> Pełne wprowadzenie do usługi Azure blob storage, zapoznaj się [podstawowe informacje o usłudze Azure Blob](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) i [usługi Azure Blob Service](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

## <a name="prerequisites"></a>Wymagania wstępne
Aby wykonać zadania opisane w tym artykule, musisz mieć skonfigurowaną subskrypcję platformy Azure i konto usługi Azure Storage. Aby przekazać lub pobrać dane, potrzebna jest nazwa konta i klucz konta usługi Azure Storage.

* Aby skonfigurować **subskrypcji platformy Azure**, zobacz [bezpłatnej miesięcznej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).
* Instrukcje dotyczące tworzenia **konta magazynu** i uzyskiwania informacji o kontach i kluczach znajdują się w temacie [Informacje o kontach usługi Azure Storage](../../storage/common/storage-create-storage-account.md).

Aby użyć **łączników SSIS**, należy pobrać:

* **SQL Server 2014 lub 2016 — wersje Standard (lub nowszy)** : instalacja obejmuje SQL Server Integration Services.
* **Microsoft SQL Server 2014 lub 2016 Integration Services Feature Pack for Azure**: te łączniki można odpowiednio pobrać z witryny [SQL Server 2014 integration services](https://www.microsoft.com/download/details.aspx?id=47366) i [SQL Server 2016 Integration Services](https://www.microsoft.com/download/details.aspx?id=49492) .

> [!NOTE]
> SSIS został zainstalowany przy użyciu programu SQL Server, ale nie znajduje się w wersji Express. Aby uzyskać informacji na temat aplikacji, które znajdują się w różnych wersjach programu SQL Server, zobacz [wersjach programu SQL Server](https://www.microsoft.com/en-us/server-cloud/products/sql-server-editions/)
> 
> 

Materiały szkoleniowe na SSIS, zobacz [ręce na szkolenie dotyczące usług SSIS](https://www.microsoft.com/sql-server/training-certification)

Aby uzyskać informacje dotyczące sposobu uzyskania w górę i uruchomiona za pomocą SISS tworzyć proste wyodrębniania, przekształcania i ładowania (ETL) pakietów, zobacz [SSIS samouczek: Tworzenie prostego pakietu ETL](https://msdn.microsoft.com/library/ms169917.aspx).

## <a name="download-nyc-taxi-dataset"></a>Pobierz zestaw danych taksówek NYC
Przykładu opisanego w tym miejscu użyć publicznie dostępnego zestawu danych — [rund taksówek NYC](https://www.andresmh.com/nyctaxitrips/) zestawu danych. Zestaw danych składa się z około 173 milionów było taksówek w NYC w roku 2013. Istnieją dwa typy danych: podróży szczegóły danych i taryfy danych. Ponieważ istnieje plik dla każdego miesiąca, mamy 24 pliki, z których każdy jest około 2 GB nieskompresowany.

## <a name="upload-data-to-azure-blob-storage"></a>Przekazywanie danych do usługi Azure blob storage
Aby przenieść dane przy użyciu usług SSIS są wyposażone w pakiecie ze środowiska lokalnego do usługi Azure blob storage, możemy użyć wystąpienia programu [ **zadanie przekazywania obiektów Blob Azure**](https://msdn.microsoft.com/library/mt146776.aspx), pokazano poniżej:

![Konfigurowanie — — — maszyna wirtualna analizy danych](./media/move-data-to-azure-blob-using-ssis/ssis-azure-blob-upload-task.png)

Parametry, które używa zadania są opisane poniżej:

| Pole | Opis |
| --- | --- |
| **AzureStorageConnection** |Określa istniejącego Menedżera połączeń usługi Azure Storage lub tworzy nowy, który odwołuje się do konta usługi Azure Storage, które wskazuje lokalizację, w której są hostowane pliki obiektów BLOB. |
| **BlobContainer** |Określa nazwę kontenera obiektów blob, który przechowuje przekazane pliki jako obiekty blob. |
| **BlobDirectory** |Określa katalog obiektu blob przekazanego pliku jest przechowywania jako blokowe obiekty blob. Katalog obiektów blob znajduje się wirtualny hierarchicznej struktury. Jeśli istnieje już obiekt blob, it ia zastąpione. |
| **LocalDirectory** |Określa katalog lokalny, który zawiera pliki do przekazania. |
| **FileName** |Określa filtr nazw, aby wybrać pliki za pomocą wzorca określonej nazwy. Na przykład MySheet\*xls\* zawiera pliki, takie jak MySheet001.xls i MySheetABC.xlsx |
| **TimeRangeFrom/TimeRangeTo** |Określa filtr przedziału czasu. Pliki zmodyfikowane po *TimeRangeFrom* i przed *TimeRangeTo* są uwzględniane. |

> [!NOTE]
> **AzureStorageConnection** poświadczenia muszą być prawidłowe i **BlobContainer** musi istnieć przed próbą przeniesienia.
> 
> 

## <a name="download-data-from-azure-blob-storage"></a>Pobierz dane z magazynu obiektów blob platformy Azure
Aby pobrać dane z usługi Azure blob storage do magazynu lokalnego przy użyciu funkcji SSIS, należy użyć wystąpienia [zadania pobierania obiektów Blob Azure](https://msdn.microsoft.com/library/mt146779.aspx).

## <a name="more-advanced-ssis-azure-scenarios"></a>Bardziej zaawansowanych scenariuszy SSIS Azure
Pakiet funkcji SSIS umożliwia bardziej złożonych przepływów, które mają być obsługiwane przez zadania tworzenia pakietów, razem. Na przykład danych obiektów blob można źródła danych bezpośrednio w klastrze usługi HDInsight, w której dane wyjściowe mogły zostać pobrane do obiektu blob, a następnie do lokalnego magazynu. SSIS można uruchamiać zadania Hive i Pig, w klastrze usługi HDInsight za pomocą łączników SSIS dodatkowe:

* Aby uruchomić skrypt programu Hive w klastrze usługi Azure HDInsight przy użyciu funkcji SSIS, należy użyć [zadań Hive HDInsight Azure](https://msdn.microsoft.com/library/mt146771.aspx).
* Aby uruchomić skrypt programu Pig w klastrze usługi Azure HDInsight przy użyciu funkcji SSIS, użyj [Azure HDInsight Pig zadań](https://msdn.microsoft.com/library/mt146781.aspx).

