---
title: Fabryki danych platformy Azure — często zadawane pytania
description: Często zadawane pytania dotyczące usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 532dec5a-7261-4770-8f54-bfe527918058
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: f33a59105d1be94ccd4fb24ae3d962b36c2efbdd
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839353"
---
# <a name="azure-data-factory---frequently-asked-questions"></a>Fabryki danych platformy Azure — często zadawane pytania
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącą wersję usługi Data Factory, zobacz [— często zadawane pytania — Data Factory](../frequently-asked-questions.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="general-questions"></a>Pytania ogólne
### <a name="what-is-azure-data-factory"></a>Czym jest usługa Azure Data Factory?
Data Factory to oparta na chmurze Usługa integracji danych, który **automatyzuje operacje przenoszenia i przekształcania danych**. Podobnie jak fabryki, sprzęt, aby przejąć kontrolę surowce i przekształca je w gotowe towary usługa fabryka danych aranżuje istniejące usługi, które są zbierane są oryginalne dane są następnie przekształcane w gotowe do użycia informacje.

Fabryka danych umożliwia tworzenie opartych na danych przepływy pracy służące do przenoszenia danych między zarówno w środowisku lokalnym i magazynami danych w chmurze jak i przetworzenia/przekształcenia danych przy użyciu usług obliczeniowych, takich jak Azure HDInsight i Azure Data Lake Analytics. Po utworzeniu potoku, który wykonuje akcję, które są potrzebne, można zaplanować jego uruchomienie okresowego (co godzinę, codziennie, co tydzień itp.).   

Aby uzyskać więcej informacji, zobacz [omówienie i kluczowe założenia](data-factory-introduction.md).

### <a name="where-can-i-find-pricing-details-for-azure-data-factory"></a>Gdzie mogę znaleźć cennik usługi Azure Data Factory?
Zobacz [stronie szczegółów cennika fabryki danych][adf-pricing-details] Aby uzyskać szczegóły cennika usługi Azure Data Factory.  

### <a name="how-do-i-get-started-with-azure-data-factory"></a>Jak rozpocząć pracę z usługą Azure Data Factory?
* Omówienie usługi Azure Data Factory, zobacz [wprowadzenie do usługi Azure Data Factory](data-factory-introduction.md).
* Samouczek dotyczący sposobu **kopiowania/przenoszenia danych** za pomocą działania kopiowania, zobacz [kopiowanie danych z usługi Azure Blob Storage do usługi Azure SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
* Samouczek dotyczący sposobu **przekształcania danych** przy użyciu działania programu Hive HDInsight. Zobacz [przetwarzania danych, uruchamiając skrypt Hive w klastrze platformy Hadoop](data-factory-build-your-first-pipeline.md)

### <a name="what-is-the-data-factorys-region-availability"></a>Co to jest Data Factory dostępność w poszczególnych regionach?
Data Factory jest dostępna w **zachodnie stany USA** i **Europa Północna**. Usługi obliczeniowe i magazynowe, używane przez fabryki danych może być w innych regionach. Zobacz [obsługiwane regiony](data-factory-introduction.md#supported-regions).

### <a name="what-are-the-limits-on-number-of-data-factoriespipelinesactivitiesdatasets"></a>Jakie są limity liczby danych fabryki/potoków/działania/zestawów danych?
Zobacz **usługi Azure Data Factory Limits limity** części [subskrypcji platformy Azure i limity, przydziały i ograniczenia](../../azure-subscription-service-limits.md#data-factory-limits) artykułu.

### <a name="what-is-the-authoringdeveloper-experience-with-azure-data-factory-service"></a>Co to jest środowisko tworzenia developer, przy użyciu usługi Azure Data Factory?
Autor/tworzenia fabryki danych przy użyciu jednej z następujących narzędzi/zestawów SDK:

* **Program Visual Studio** używasz programu Visual Studio do utworzenia fabryki danych platformy Azure. Zobacz [Tworzenie pierwszego potoku danych przy użyciu programu Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) Aby uzyskać szczegółowe informacje.
* **Program Azure PowerShell** zobacz [tworzenie i monitorowanie fabryki danych Azure przy użyciu programu Azure PowerShell](data-factory-build-your-first-pipeline-using-powershell.md) dla samouczka/wskazówki dotyczące tworzenia fabryki danych przy użyciu programu PowerShell. Zobacz [Data Factory Cmdlet Reference][adf-powershell-reference] zawartości w bibliotece MSDN, aby uzyskać pełną dokumentację poleceń cmdlet usługi Data Factory.
* **Biblioteka klas programu .NET** można programowo tworzyć fabryki danych przy użyciu zestawu SDK .NET usługi Data Factory. Zobacz [tworzenie, monitorowanie i zarządzanie nimi fabryki danych przy użyciu zestawu .NET SDK](data-factory-create-data-factories-programmatically.md) instrukcje dotyczące tworzenia fabryki danych przy użyciu zestawu .NET SDK. Zobacz [odwołanie do biblioteki klas fabryki danych][msdn-class-library-reference] pełną dokumentację zestawu SDK .NET usługi Data Factory.
* **Interfejs API REST** można również użyć interfejsu API REST udostępnianego przez usługę Azure Data Factory, tworzenie i wdrażanie fabryki danych. Zobacz [dokumentacja interfejsu API REST usługi Data Factory][msdn-rest-api-reference] pełną dokumentację interfejsu API REST usługi Data Factory.
* **Szablon usługi Azure Resource Manager** zobacz [samouczka: Tworzenie pierwszej fabryki danych platformy Azure przy użyciu szablonu usługi Azure Resource Manager](data-factory-build-your-first-pipeline-using-arm.md) fo szczegóły.

### <a name="can-i-rename-a-data-factory"></a>Czy mogę zmienić nazwę fabryki danych?
Nie. Podobnie jak inne zasoby platformy Azure nie można zmienić nazwę fabryki danych platformy Azure.

### <a name="can-i-move-a-data-factory-from-one-azure-subscription-to-another"></a>Czy mogę przenieść fabrykę danych z jedną subskrypcją platformy Azure do innego?
Tak. Użyj **przenieść** znajdujący się na swoje blok fabryka danych, jak pokazano na poniższym diagramie:

![Przenieś fabryki danych](media/data-factory-faq/move-data-factory.png)

### <a name="what-are-the-compute-environments-supported-by-data-factory"></a>Co to są środowisk obliczeniowych obsługiwanych przez usługę Data Factory?
Poniższa tabela zawiera listę środowisk obliczeniowych obsługiwanych przez usługi Data Factory i działania, które można uruchomić na nich.

| Środowisko obliczeniowe | activities |
| --- | --- |
| [Klaster HDInsight na żądanie](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) lub [klaster HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) |[DotNet](data-factory-use-custom-activities.md), [Hive](data-factory-hive-activity.md), [Pig](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [Hadoop Streaming](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](data-factory-compute-linked-services.md#azure-batch-linked-service) |[DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning](data-factory-compute-linked-services.md#azure-machine-learning-linked-service) |[Machine Learning działania: Wykonywanie wsadowe i aktualizacja zasobów](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure Data Lake Analytics](data-factory-compute-linked-services.md#azure-data-lake-analytics-linked-service) |[Język U-SQL usługi Data Lake Analytics](data-factory-usql-activity.md) |
| [Usługi Azure SQL](data-factory-compute-linked-services.md#azure-sql-linked-service), [Azure SQL Data Warehouse](data-factory-compute-linked-services.md#azure-sql-data-warehouse-linked-service), [programu SQL Server](data-factory-compute-linked-services.md#sql-server-linked-service) |[Procedura składowana](data-factory-stored-proc-activity.md) |

### <a name="how-does-azure-data-factory-compare-with-sql-server-integration-services-ssis"></a>Jak wygląda porównanie z SQL Server Integration Services (SSIS) w usłudze Azure Data Factory? 
Zobacz [vs usługi Azure Data Factory. SSIS](https://www.sqlbits.com/Sessions/Event15/Azure_Data_Factory_vs_SSIS) prezentacji z jednego z naszych Valuable Professional (najbardziej cenionym specjalistów): Reza Rad. Niektóre zmiany w usłudze Data Factory może nie znajdować pokazu slajdów. Stale dodajemy więcej możliwości usługi Azure Data Factory. Stale dodajemy więcej możliwości usługi Azure Data Factory. Firma Microsoft będzie zawierać te aktualizacje do porównania technologii integracji danych firmy Microsoft trochę czasu w dalszej części tego roku.   

## <a name="activities---faq"></a>Działania — często zadawane pytania
### <a name="what-are-the-different-types-of-activities-you-can-use-in-a-data-factory-pipeline"></a>Jakie są różne rodzaje działań, które można używać w potoku usługi Data Factory?
* [Działania przenoszenia danych](data-factory-data-movement-activities.md) do przenoszenia danych.
* [Działania przekształcania danych](data-factory-data-transformation-activities.md) do przetworzenia/przekształcenia danych.

### <a name="when-does-an-activity-run"></a>Gdy działanie jest uruchamiane?
**Dostępności** ustawienia konfiguracji w tabeli danych wyjściowych danych określa, kiedy działanie jest uruchamiane. W przypadku wejściowych zestawów danych są określone, działanie sprawdza, czy spełnione są wszystkie zależności w danych wejściowych (czyli **gotowe** stanu) przed jej uruchomienie.

## <a name="copy-activity---faq"></a>Działanie kopiowania — często zadawane pytania
### <a name="is-it-better-to-have-a-pipeline-with-multiple-activities-or-a-separate-pipeline-for-each-activity"></a>Lepiej jest potok za pomocą wielu działań lub potoku osobne dla każdego działania?
Potoki są powinien pakietu powiązanych działań. W przypadku zestawów danych, które łączą te elementy nie są używane przez dowolne inne działanie poza potok, należy dysponować działania w jednym potoku. W ten sposób nie należałoby łańcuchu potoku aktywnych okresów, aby były wyrównane ze sobą. Ponadto integralność danych w tabelach wewnętrznych do potoku lepiej są zachowywane podczas aktualizowania potoku. Zasadniczo potoku aktualizacji zatrzymuje wszystkie działania w potoku, usuwa je i tworzy je ponownie. Od tworzenia perspektyw, może być również łatwiej zobaczyć przepływu danych w ramach powiązanych działań w jednym pliku JSON potoku.

### <a name="what-are-the-supported-data-stores"></a>Co to są obsługiwane magazyny danych?
Działanie kopiowania w usłudze Data Factory kopiuje dane z magazynu danych źródła do magazynu danych ujścia. Usługa Data Factory obsługuje następujące magazyny danych. Dane z dowolnego źródła można zapisać do dowolnego ujścia. Kliknij magazyn danych, aby dowiedzieć się, jak kopiować dane do i z tego magazynu.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Magazyny danych oznaczone znakiem * mogą być konfigurowane lokalnie lub w usłudze Azure IaaS i wymagają zainstalowania [bramy zarządzania danymi](data-factory-data-management-gateway.md) na maszynie lokalnej lub w usłudze Azure IaaS.

### <a name="what-are-the-supported-file-formats"></a>Co to są obsługiwane formaty plików?
[!INCLUDE [data-factory-file-format](../../../includes/data-factory-file-format.md)]

### <a name="where-is-the-copy-operation-performed"></a>Gdy odbywa się operacji kopiowania
Zobacz [przenoszenia danych dostępnie](data-factory-data-movement-activities.md#global) sekcji, aby uzyskać szczegółowe informacje. Krótko mówiąc w przypadku lokalnego magazynu danych jest operacji kopiowania jest wykonywane przez bramę zarządzania danymi w środowisku w środowisku lokalnym. Ponadto w przypadku przenoszenia danych między dwoma magazynami w chmurze, operacja kopiowania jest wykonywane w regionie najbliżej lokalizacji ujścia w tej samej lokalizacji geograficznej.

## <a name="hdinsight-activity---faq"></a>Działanie HDInsight — często zadawane pytania
### <a name="what-regions-are-supported-by-hdinsight"></a>Jakie regiony są obsługiwane przez HDInsight?
Zobacz sekcję dotyczącą dostępności geograficznej w następującym artykule: lub [szczegóły cennika usługi HDInsight][hdinsight-supported-regions].

### <a name="what-region-is-used-by-an-on-demand-hdinsight-cluster"></a>Jakie regionie jest używany przez klaster usługi HDInsight na żądanie?
Klaster HDInsight na żądanie jest tworzony w tym samym regionie, w której istnieje używanego przez podany do wykorzystania przy użyciu klastra magazynu.    

### <a name="how-to-associate-additional-storage-accounts-to-your-hdinsight-cluster"></a>Jak skojarzyć dodatkowe konta magazynu do klastra usługi HDInsight?
Jeśli używasz własnego klastra HDInsight (BYOC - Bring Your własnego klastra), zobacz następujące tematy:

* [Przy użyciu klastra usługi HDInsight z kontami magazynu alternatywnej i magazyny metadanych][hdinsight-alternate-storage]
* [Używanie dodatkowych kont magazynu przy użyciu technologii Hive HDInsight][hdinsight-alternate-storage-2]

Jeśli używasz klastra na żądanie, który jest tworzony przez usługę Data Factory, określić dodatkowe konta magazynu dla HDInsight połączonej usługi, tak aby usługa Data Factory można zarejestrować je w Twoim imieniu. W definicji JSON dla połączonej usługi na żądanie, należy użyć **additionalLinkedServiceNames** właściwości w celu określenia konta alternatywne magazynu, jak pokazano w poniższym fragmencie kodu JSON:

```JSON
{
    "name": "MyHDInsightOnDemandLinkedService",
    "properties":
    {
        "type": "HDInsightOnDemandLinkedService",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "linkedServiceName": "LinkedService-SampleData",
            "additionalLinkedServiceNames": [ "otherLinkedServiceName1", "otherLinkedServiceName2" ]
        }
    }
}
```
W powyższym przykładzie otherLinkedServiceName1 i otherLinkedServiceName2 reprezentują połączonych usług, których definicje zawierają poświadczeń wymaganych przez klaster HDInsight dostępu do kont magazynu alternatywne.

## <a name="slices---faq"></a>Wycinki — często zadawane pytania
### <a name="why-are-my-input-slices-not-in-ready-state"></a>Dlaczego są moje wycinki danych wejściowych nie jest w stanie gotowe?
Powszechnym nie ustawia **zewnętrznych** właściwości **true** dla wejściowego zestawu danych, gdy dane wejściowe są zewnętrzne w usłudze data factory (nie utworzone przez usługę data factory).

W poniższym przykładzie, wystarczy ustawić **zewnętrznych** na wartość TRUE **dataset1**.  

**DataFactory1** potoku 1: dataset1 -> działania activity1 -> dataset2 -> activity2 -> potoku dataset3 2: dataset3 -> działanie activity3 -> dataset4

Jeśli masz inną fabryki danych obejmującej potok, który przyjmuje dataset4 (generowany przez potok 2 w usłudze data factory 1), należy oznaczyć dataset4 jako zewnętrzny zestaw danych, ponieważ zestaw danych jest generowany przez fabrykę danych (DataFactory1, nie DataFactory2).  

**DataFactory2**    
1 potok: dataset4 -> działanie activity4 -> dataset5

Jeśli właściwość zewnętrzne są ustawione właściwie, sprawdź, czy dane wejściowe istnieje w lokalizacji określonej w definicji wejściowego zestawu danych.

### <a name="how-to-run-a-slice-at-another-time-than-midnight-when-the-slice-is-being-produced-daily"></a>Jak uruchomić wycinek w innym czasie niż gdy wycinek jest generowany codziennie o północy?
Użyj **przesunięcie** właściwości w celu określenia czasu, jaką ma wycinek, który ma zostać wyprodukowany. Zobacz [dostępności zestawu danych](data-factory-create-datasets.md#dataset-availability) sekcji, aby uzyskać szczegółowe informacje na temat tej właściwości. Poniżej przedstawiono prosty przykład:

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
Dzienne wycinki rozpoczynają się od **6: 00** zamiast północy domyślne.     

### <a name="how-can-i-rerun-a-slice"></a>Jak można ponownie uruchomić wycinek?
Możesz ponownie uruchomić wycinek w jednym z następujących sposobów:

* Ponowne uruchamianie okna działania lub wycinka, należy użyć aplikacji monitorowanie i zarządzanie. Zobacz [Uruchom ponownie wybrane okien działania](data-factory-monitor-manage-app.md#perform-batch-actions) instrukcje.   
* Kliknij przycisk **Uruchom** na pasku poleceń u **WYCINKA danych** blok wycinek w witrynie Azure portal.
* Uruchom **AzDataFactorySliceStatus zestaw** polecenia cmdlet ze stanem równa **oczekiwania** dla wycinka.   

    ```powershell
    Set-AzDataFactorySliceStatus -Status Waiting -ResourceGroupName $ResourceGroup -DataFactoryName $df -TableName $table -StartDateTime "02/26/2015 19:00:00" -EndDateTime "02/26/2015 20:00:00"
    ```
  Zobacz [AzDataFactorySliceStatus zestaw][set-azure-datafactory-slice-status] szczegółowe informacje na temat polecenia cmdlet.

### <a name="how-long-did-it-take-to-process-a-slice"></a>Jak długo trwało przetworzyć wycinek?
Aby dowiedzieć się, jak długo można przetworzyć wycinek danych, należy użyć Eksploratorze okien działania w aplikacji monitorowanie i zarządzanie. Zobacz [Eksploratorze okien działania](data-factory-monitor-manage-app.md#activity-window-explorer) Aby uzyskać szczegółowe informacje.

Można również wykonać następujące czynności w witrynie Azure portal:  

1. Kliknij przycisk **zestawów danych** kafelków na **usługi DATA FACTORY** bloku fabryki danych.
2. Kliknięcie konkretnego zestawu danych **zestawów danych** bloku.
3. Wybierz wycinek, który Cię interesuje z **najnowsze wycinki** listy na **tabeli** bloku.
4. Kliknij działanie uruchamiania z **uruchomienia działania** listy na **WYCINKA danych** bloku.
5. Kliknij przycisk **właściwości** kafelków na **szczegóły URUCHAMIANIA działania** bloku.
6. Powinien zostać wyświetlony **czas trwania** pole z wartością. Ta wartość jest czas poświęcony na przetworzenie wycinka przez.   

### <a name="how-to-stop-a-running-slice"></a>Jak zatrzymać uruchomione wycinka?
Jeśli musisz zatrzymać potoku wykonanie, możesz użyć [AzDataFactoryPipeline Wstrzymaj](/powershell/module/az.datafactory/suspend-azdatafactorypipeline) polecenia cmdlet. Obecnie wstrzymywania potoku nie zatrzymuje wykonań wycinek, które są w toku. Po zakończenia wykonywania w toku, nie dodatkowe wycinka zostaje pobrana.

Jeśli na pewno chcesz zatrzymać natychmiast wszystkich wykonań, jedynym sposobem jest usunięcie potoku i utworzyć ją ponownie. Jeśli zdecydujesz się usunąć potoku, nie musisz usuwać tabele i połączone usługi używane przez potok.

[create-factory-using-dotnet-sdk]: data-factory-create-data-factories-programmatically.md
[msdn-class-library-reference]: /dotnet/api/microsoft.azure.management.datafactories.models
[msdn-rest-api-reference]: /rest/api/datafactory/

[adf-powershell-reference]: /powershell/module/az.datafactory/
[azure-portal]: https://portal.azure.com
[set-azure-datafactory-slice-status]: /powershell/module/az.datafactory/set-Azdatafactoryslicestatus

[adf-pricing-details]: https://go.microsoft.com/fwlink/?LinkId=517777
[hdinsight-supported-regions]: https://azure.microsoft.com/pricing/details/hdinsight/
[hdinsight-alternate-storage]: https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx
[hdinsight-alternate-storage-2]: https://blogs.msdn.com/b/cindygross/archive/2014/05/05/use-additional-storage-accounts-with-hdinsight-hive.aspx
