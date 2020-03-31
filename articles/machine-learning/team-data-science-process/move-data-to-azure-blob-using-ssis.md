---
title: Przenoszenie danych magazynu obiektów Blob za pomocą łączników SSIS — proces nauki o danych zespołowych
description: Dowiedz się, jak przenieść dane do lub z usługi Azure Blob Storage przy użyciu pakietu funkcji sql server integration services dla platformy Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720875"
---
# <a name="move-data-to-or-from-azure-blob-storage-using-ssis-connectors"></a>Przenoszenie danych do lub z usługi Azure Blob Storage przy użyciu łączników SSIS
[Pakiet funkcji SQL Server Integration Services dla platformy Azure](https://msdn.microsoft.com/library/mt146770.aspx) zawiera składniki do łączenia się z platformą Azure, przesyłania danych między platformą Azure i lokalnymi źródłami danych oraz przetwarzania danych przechowywanych na platformie Azure.

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

Gdy klienci przeniosą dane lokalne do chmury, mogą uzyskać dostęp do swoich danych z dowolnej usługi platformy Azure, aby wykorzystać pełną moc pakietu technologii platformy Azure. Dane mogą być następnie używane, na przykład w usłudze Azure Machine Learning lub w klastrze HDInsight.

Przykłady korzystania z tych zasobów platformy Azure znajdują się w instruktamencie [SQL](sql-walkthrough.md) i [HDInsight.](hive-walkthrough.md)

Aby zapoznać się ze scenariuszami kanonicznymi, które używają SSIS do osiągania potrzeb biznesowych typowych w scenariuszach integracji danych hybrydowych, zobacz [Wykonywanie więcej informacji za pomocą pakietu funkcji sql server integration services dla platformy Azure.](https://blogs.msdn.com/b/ssis/archive/2015/06/25/doing-more-with-sql-server-integration-services-feature-pack-for-azure.aspx)

> [!NOTE]
> Aby uzyskać pełne wprowadzenie do magazynu obiektów blob platformy Azure, zobacz [Podstawowe usługi Azure Blob](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) i [usługi Azure Blob.](https://msdn.microsoft.com/library/azure/dd179376.aspx)
> 
> 

## <a name="prerequisites"></a>Wymagania wstępne
Aby wykonać zadania opisane w tym artykule, musisz mieć skonfigurowaną subskrypcję platformy Azure i konto usługi Azure Storage. Do przekazywania lub pobierania danych potrzebna jest nazwa konta usługi Azure Storage i klucz konta.

* Aby skonfigurować **subskrypcję platformy Azure,** zobacz [Bezpłatna miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).
* Aby uzyskać instrukcje dotyczące tworzenia **konta magazynu** oraz uzyskiwania informacji o koncie i kluczach, zobacz Informacje [o kontach usługi Azure Storage](../../storage/common/storage-create-storage-account.md).

Aby korzystać ze **złączy SSIS,** należy pobrać:

* **SQL Server 2014 lub 2016 Standard (lub wyższe)**: Install zawiera usługi integracji programu SQL Server.
* **Pakiet funkcji usług integracyjnych programu Microsoft SQL Server 2014 lub 2016 dla platformy Azure:** te łączniki można pobrać odpowiednio ze stron [usług integracji programu SQL Server 2014](https://www.microsoft.com/download/details.aspx?id=47366) i [usług integracyjnych programu SQL Server 2016.](https://www.microsoft.com/download/details.aspx?id=49492)

> [!NOTE]
> SSIS jest zainstalowany z programem SQL Server, ale nie jest uwzględniony w wersji Express. Aby uzyskać informacje o tym, jakie aplikacje są zawarte w różnych wersjach programu SQL Server, zobacz [SQL Server Editions](https://www.microsoft.com/en-us/server-cloud/products/sql-server-editions/)
> 
> 

Materiały szkoleniowe dotyczące SSIS można znaleźć [w trybie praktycznym dla SSIS](https://www.microsoft.com/sql-server/training-certification)

Aby uzyskać informacje na temat uruchamiania za pomocą SISS do tworzenia prostych pakietów ekstrakcji, transformacji i ładowania (ETL), zobacz [SSIS Tutorial: Tworzenie prostego pakietu ETL](https://msdn.microsoft.com/library/ms169917.aspx).

## <a name="download-nyc-taxi-dataset"></a>Pobierz zestaw danych NYC Taxi
W przykładzie opisanym w tym miejscu użyto publicznie dostępnego zestawu danych — zestawu danych [NYC Taxi Trips.](https://www.andresmh.com/nyctaxitrips/) Zestaw danych składa się z około 173 milionów przejazdów taksówką w Nowym Jorku w roku 2013. Istnieją dwa typy danych: dane szczegółów podróży i dane taryfy. Ponieważ jest plik dla każdego miesiąca, mamy 24 pliki, z których każdy jest o 2 GB nieskompresowany.

## <a name="upload-data-to-azure-blob-storage"></a>Przekazywanie danych do magazynu obiektów blob platformy Azure
Aby przenieść dane przy użyciu pakietu funkcji SSIS z lokalnego magazynu obiektów blob platformy Azure, używamy wystąpienia [**zadania przekazywania obiektów Blob platformy Azure,**](https://msdn.microsoft.com/library/mt146776.aspx)pokazane tutaj:

![configure-data-science-vm](./media/move-data-to-azure-blob-using-ssis/ssis-azure-blob-upload-task.png)

Parametry używane przez zadanie są opisane w tym miejscu:

| Pole | Opis |
| --- | --- |
| **Połączenie usługi AzureStorageConnection** |Określa istniejący Menedżer połączeń usługi Azure Storage lub tworzy nowy, który odwołuje się do konta usługi Azure Storage, który wskazuje, gdzie są hostowane pliki obiektów blob. |
| **Obiekt BlobContainer** |Określa nazwę kontenera obiektów blob, który przechowuje przekazane pliki jako obiekty blob. |
| **Kropelka blob** |Określa katalog obiektów blob, w którym przekazany plik jest przechowywany jako blokowy obiekt blob. Katalog obiektów blob jest wirtualną strukturą hierarchiczną. Jeśli obiekt blob już istnieje, zastąpił ją. |
| **LocalDirectory (LocalDirectory)** |Określa katalog lokalny zawierający pliki, które mają zostać przekazane. |
| **Pod nazwą** |Określa filtr nazw, aby wybrać pliki o określonym wzorcu nazw. Na przykład mysheet\*xls\* zawiera pliki, takie jak MySheet001.xls i MySheetABC.xlsx |
| **TimeRangeFrom/TimeRangeTo** |Określa filtr zakresu czasu. Pliki zmodyfikowane po *TimeRangeFrom* i przed *TimeRangeTo* są uwzględniane. |

> [!NOTE]
> Poświadczenia **Usługi AzureStorageConnection** muszą być poprawne i **BlobContainer** musi istnieć przed próbą transferu.
> 
> 

## <a name="download-data-from-azure-blob-storage"></a>Pobieranie danych z magazynu obiektów blob platformy Azure
Aby pobrać dane z magazynu obiektów blob platformy Azure do magazynu lokalnego z usługą SSIS, należy użyć wystąpienia [zadania pobierania obiektów Blob platformy Azure](https://msdn.microsoft.com/library/mt146779.aspx).

## <a name="more-advanced-ssis-azure-scenarios"></a>Bardziej zaawansowane scenariusze SSIS-Azure
Pakiet funkcji SSIS umożliwia wykonywanie bardziej złożonych przepływów przez zadania pakowania razem. Na przykład dane obiektu blob można przesyłać bezpośrednio do klastra HDInsight, którego dane wyjściowe można pobrać z powrotem do obiektu blob, a następnie do magazynu lokalnego. SSIS można uruchamiać zadania hive i pig w klastrze HDInsight przy użyciu dodatkowych złączy SSIS:

* Aby uruchomić skrypt hive w klastrze usługi Azure HDInsight z usługą SSIS, należy użyć [zadania gałęzi usługi Azure HDInsight.](https://msdn.microsoft.com/library/mt146771.aspx)
* Aby uruchomić skrypt Świnia w klastrze usługi Azure HDInsight z SSIS, użyj [zadania świni usługi Azure HDInsight](https://msdn.microsoft.com/library/mt146781.aspx).

