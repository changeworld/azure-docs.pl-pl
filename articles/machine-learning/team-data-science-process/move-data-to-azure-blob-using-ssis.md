---
title: Przenoszenie danych do i z magazynu obiektów Blob Azure za pomocą łączników SSIS | Dokumentacja firmy Microsoft
description: Przenoszenie danych do i z magazynu obiektów Blob Azure za pomocą łączników SSIS.
services: machine-learning,storage
documentationcenter: ''
author: deguhath
manager: jhubbard
editor: cgronlun
ms.assetid: 96a1b5fb-34d1-4b9b-8d99-2bb8289e0398
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: deguhath
ms.openlocfilehash: 39feca23532264c1f4c17017f89b064af09e9c1c
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="move-data-to-or-from-azure-blob-storage-using-ssis-connectors"></a>Przenoszenie danych do i z magazynu obiektów Blob Azure za pomocą łączników SSIS
[Programu SQL Server Integration Services Feature Pack dla platformy Azure](https://msdn.microsoft.com/library/mt146770.aspx) udostępnia składników do nawiązania połączenia platformy Azure, transfer danych między Azure i lokalnego źródła danych i przetwarzania danych przechowywanych na platformie Azure.

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

Gdy klienci przeniesione danych lokalnych do chmury, ich do niego dostęp z dowolnej usługi Azure, aby korzystać ze wszystkich możliwości pakiet Azure technologii. Mogą być używane, na przykład w usłudze Azure Machine Learning lub w klastrze usługi HDInsight.

Zazwyczaj jest to być pierwszym krokiem [SQL](sql-walkthrough.md) i [HDInsight](hive-walkthrough.md) wskazówki.

Omówienie canonical scenariusze za pomocą usług SSIS wykonywać typowe w przypadku scenariuszy integracji danych hybrydowych potrzeb biznesowych, zobacz [zrobić więcej z programu SQL Server Integration Services Feature Pack dla platformy Azure](http://blogs.msdn.com/b/ssis/archive/2015/06/25/doing-more-with-sql-server-integration-services-feature-pack-for-azure.aspx) blogu.

> [!NOTE]
> Pełne wprowadzenie do magazynu obiektów blob platformy Azure, można znaleźć w temacie [podstawy obiektów Blob Azure](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) i [usługi obiektów Blob Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

## <a name="prerequisites"></a>Wymagania wstępne
Aby wykonać zadania opisane w tym artykule, musi mieć subskrypcję platformy Azure i skonfigurowane konto magazynu platformy Azure. Należy znać Azure klucz konta magazynu nazwy i konta na przekazywanie lub pobieranie danych.

* Aby skonfigurować **subskrypcji platformy Azure**, zobacz [bezpłatna miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).
* Aby uzyskać instrukcje dotyczące tworzenia **konta magazynu** oraz uzyskiwanie konta i informacje o kluczu, zobacz [kont magazynu Azure o](../../storage/common/storage-create-storage-account.md).

Aby użyć **łączniki SSIS**, należy pobrać:

* **SQL Server 2014 lub 2016 Standard (lub nowszej)**: instalacja zawiera SQL Server Integration Services.
* **Microsoft SQL Server 2014 lub 2016 integracji usług Feature Pack dla systemu Azure**: te mogą być pobierane, odpowiednio z [SQL Server 2014 Integration Services](http://www.microsoft.com/download/details.aspx?id=47366) i [integracji programu SQL Server 2016 Usługi](https://www.microsoft.com/download/details.aspx?id=49492) stron.

> [!NOTE]
> SSIS jest instalowany z serwerem SQL, ale nie znajduje się w wersji Express. Informacje dotyczące poszczególnych aplikacji znajdują się w różnych wersjach programu SQL Server znajdują się w temacie [wersjach programu SQL Server](http://www.microsoft.com/en-us/server-cloud/products/sql-server-editions/)
> 
> 

Materiałów szkoleniowych w sprawie SSIS, zobacz [ręce na szkolenia dotyczące SSIS](http://www.microsoft.com/download/details.aspx?id=20766)

Informacje dotyczące sposobu uzyskania w górę i uruchomić przy użyciu SISS do tworzenia prostych wyodrębniania, przekształcania i ładowania (ETL) pakietów, zobacz [SSIS samouczek: Tworzenie prostego pakietu ETL](https://msdn.microsoft.com/library/ms169917.aspx).

## <a name="download-nyc-taxi-dataset"></a>Pobierz taksówki NYC zestawu danych
Przykład opisanej tutaj Użyj publicznie dostępnych zestawu danych-- [rund taksówki NYC](http://www.andresmh.com/nyctaxitrips/) zestawu danych. Zestaw danych składa się z około 173 milionów taksówki było w NYC 2013 roku. Istnieją dwa typy danych: podróży szczegóły danych i taryfy danych. Ponieważ plik jest dostępny dla każdego miesiąca, mamy 24 plików we wszystkich, z których każdy jest nieskompresowane około 2GB.

## <a name="upload-data-to-azure-blob-storage"></a>Przekazywanie danych do magazynu obiektów blob platformy Azure
Aby przenieść dane przy użyciu usług SSIS funkcji pakiet z lokalnego do magazynu obiektów blob platformy Azure, możemy użyć wystąpienia programu [ **zadań przekazania obiektu Blob Azure**](https://msdn.microsoft.com/library/mt146776.aspx), pokazano poniżej:

![Konfigurowanie danych nauki vm](./media/move-data-to-azure-blob-using-ssis/ssis-azure-blob-upload-task.png)

Parametry używane przez zadania opisane w tym miejscu:

| Pole | Opis |
| --- | --- |
| **AzureStorageConnection** |Określa istniejącą Menedżera połączeń magazynu Azure lub tworzy nowy, która odwołuje się do konta magazynu Azure, który wskazuje, gdzie znajdują się pliki obiektu blob. |
| **BlobContainer** |Określa nazwę kontenera obiektów blob, w którym przechowywane przekazane pliki jako obiekty BLOB. |
| **BlobDirectory** |Określa katalog obiektów blob, w którym przekazanego pliku jest przechowywana jako blokowych obiektów blob. Katalog obiektu blob jest wirtualnego strukturę hierarchiczną. Jeśli istnieje już obiekt blob, it ia zastąpione. |
| **LocalDirectory** |Określa katalog lokalny, który zawiera pliki do przekazania. |
| **FileName** |Określa nazwę filtru i wybierz pliki z wzorcem określonej nazwy. Na przykład MySheet\*xls\* zawiera pliki takie jak MySheet001.xls i MySheetABC.xlsx |
| **TimeRangeFrom/TimeRangeTo** |Określa filtr przedziału czasu. Pliki zmodyfikowane po *TimeRangeFrom* i przed *TimeRangeTo* są uwzględniane. |

> [!NOTE]
> **AzureStorageConnection** poświadczenia muszą być prawidłowe i **BlobContainer** musi istnieć przed próbą przeniesienia.
> 
> 

## <a name="download-data-from-azure-blob-storage"></a>Pobierz dane z magazynu obiektów blob platformy Azure
Podczas pobierania danych z magazynu obiektów blob platformy Azure do lokalnego magazynu z SSIS, używając wystąpienia [zadań przekazania obiektu Blob Azure](https://msdn.microsoft.com/library/mt146779.aspx).

## <a name="more-advanced-ssis-azure-scenarios"></a>Bardziej zaawansowanych scenariuszy SSIS Azure
Pakiet funkcji SSIS umożliwia bardziej złożonych przepływów mają być obsługiwane przez opakowanie zadań jednocześnie. Na przykład obiekt blob może strumieniowego źródła danych bezpośrednio do klastra usługi HDInsight, której wyjście mogły zostać pobrane do obiektu blob, a następnie do lokalnego magazynu. SSIS można uruchamiać zadania Hive i Pig w klastrze usługi HDInsight za pomocą łączników SSIS dodatkowe:

* Aby uruchomić skrypt Hive w klastrze Azure HDInsight z SSIS, użyj [zadań Hive HDInsight Azure](https://msdn.microsoft.com/library/mt146771.aspx).
* Aby uruchomić skrypt programu Pig na klaster Azure HDInsight z SSIS, należy użyć [Azure HDInsight Pig zadań](https://msdn.microsoft.com/library/mt146781.aspx).

