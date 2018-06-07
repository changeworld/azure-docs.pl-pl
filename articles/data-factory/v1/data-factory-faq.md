---
title: Fabryka danych Azure — często zadawane pytania
description: Często zadawane pytania dotyczące usługi fabryka danych Azure.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 532dec5a-7261-4770-8f54-bfe527918058
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 12422a1123673feac1977fa5f52f49b2cdfda777
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34621350"
---
# <a name="azure-data-factory---frequently-asked-questions"></a>Fabryka danych Azure — często zadawane pytania
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory, która jest ogólnie dostępna (GA). Jeśli używasz wersji 2 usługi fabryka danych, która jest w wersji zapoznawczej, zobacz [często zadawane pytania — fabryki danych w wersji 2](../frequently-asked-questions.md).

## <a name="general-questions"></a>Pytania ogólne
### <a name="what-is-azure-data-factory"></a>Czym jest usługa Azure Data Factory?
Fabryka danych jest oparty na chmurze Usługa integracji danych **automatyzuje przepływu i przekształcania danych**. Podobnie jak fabryka, która uruchamia urządzeń, które mają zostać pobrane materiałów i przekształcania gotowych fabryki danych organizuje istniejących usług, które zbierać dane i przetransformować je w informacji gotowe do użycia.

Fabryka danych służy do tworzenia opartych na danych przepływy pracy w celu przenoszenia danych między zarówno lokalnych i magazyny danych w chmurze jak i procesu/Przekształcanie danych za pomocą usług obliczeniowych, takich jak Azure HDInsight i usługą Azure Data Lake Analytics. Po utworzeniu potok, który wykonuje akcję, które są potrzebne, można zaplanować jego uruchomienie okresowo (co godzinę, codziennie, co tydzień itp.).   

Aby uzyskać więcej informacji, zobacz [Przegląd & kluczowe założenia](data-factory-introduction.md).

### <a name="where-can-i-find-pricing-details-for-azure-data-factory"></a>Gdzie można znaleźć szczegóły cennika dla fabryki danych Azure?
Zobacz [strony Szczegóły cennika fabryki danych] [ adf-pricing-details] uzyskać informacje o cenach dla fabryki danych Azure.  

### <a name="how-do-i-get-started-with-azure-data-factory"></a>Jak rozpocząć pracę z fabryki danych Azure?
* Omówienie usługi fabryka danych Azure, zobacz [wprowadzenie do fabryki danych Azure](data-factory-introduction.md).
* Samouczek dotyczący sposobu **skopiowania/przeniesienia danych** za pomocą działania kopiowania, zobacz [skopiować dane z magazynu obiektów Blob Azure do usługi Azure SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
* Samouczek dotyczący sposobu **przekształcania danych** za pomocą działania Hive HDInsight. Zobacz [przetwarzania danych przez uruchomienie skryptu Hive w klastrze usługi Hadoop](data-factory-build-your-first-pipeline.md)

### <a name="what-is-the-data-factorys-region-availability"></a>Co to jest dostępność region s fabryki danych?
Fabryka danych jest dostępna w **zachodnie nam** i **Europa Północna, Europa**. Usługi obliczeniowe i Magazyn używany przez fabryki danych może być w różnych regionach. Zobacz [obsługiwane regiony](data-factory-introduction.md#supported-regions).

### <a name="what-are-the-limits-on-number-of-data-factoriespipelinesactivitiesdatasets"></a>Jakie są limity liczby danych fabryki/potoki/działań/zestaw danych?
Zobacz **limity fabryki danych Azure** sekcji [subskrypcji platformy Azure i ograniczenia usługi, przydziały i ograniczenia](../../azure-subscription-service-limits.md#data-factory-limits) artykułu.

### <a name="what-is-the-authoringdeveloper-experience-with-azure-data-factory-service"></a>Co to jest środowisko tworzenia developer usłudze fabryka danych Azure?
Autor/tworzenia fabryki danych przy użyciu jednej z następujących narzędzi/zestawów SDK:

* **Azure portal** bloków fabryki danych w portalu Azure udostępniają interfejs użytkownika sformatowanego do tworzenia usług ad połączone fabryki danych. **Edytor fabryki danych**, która jest również częścią portalu, pozwala na łatwe tworzenie połączonej usługi, tabele zestawów danych i potoki, określając definicji JSON dla tych artefaktów. Zobacz [kompilacji swój pierwszy potok danych przy użyciu portalu Azure](data-factory-build-your-first-pipeline-using-editor.md) przykład przy użyciu portalu/Edytor do tworzenia i wdrażania fabryki danych.
* **Visual Studio** Visual Studio umożliwia tworzenie fabryki danych Azure. Zobacz [kompilacji swój pierwszy potok danych przy użyciu programu Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) szczegółowe informacje.
* **Program Azure PowerShell** zobacz [monitor fabryki danych Azure przy użyciu programu Azure PowerShell i Utwórz](data-factory-build-your-first-pipeline-using-powershell.md) samouczek/Przewodnik tworzenia fabryki danych przy użyciu programu PowerShell. Zobacz [odwołania do polecenia Cmdlet fabryki danych] [ adf-powershell-reference] zawartości w bibliotece MSDN dla pełna dokumentacja poleceń cmdlet fabryki danych.
* **Biblioteka klas programu .NET** programowego można utworzyć fabryki danych przy użyciu zestawu .NET SDK fabryki danych. Zobacz [tworzenie, monitorowanie i zarządzanie nimi przy użyciu zestawu .NET SDK fabryki danych](data-factory-create-data-factories-programmatically.md) Przewodnik tworzenia fabryki danych przy użyciu zestawu .NET SDK. Zobacz [odwołanie do biblioteki klasy fabryki danych] [ msdn-class-library-reference] kompleksowe dokumentacji zestawu SDK .NET fabryki danych.
* **Interfejs API REST** umożliwia także udostępnianych przez usługi fabryka danych Azure z interfejsu API REST do tworzenia i wdrażania fabryki danych. Zobacz [dokumentacja interfejsu API REST fabryki danych] [ msdn-rest-api-reference] dla pełna dokumentacja interfejsu API REST fabryki danych.
* **Szablonu usługi Azure Resource Manager** zobacz [samouczek: Tworzenie pierwszego fabrykę danych Azure przy użyciu szablonu usługi Azure Resource Manager](data-factory-build-your-first-pipeline-using-arm.md) fo szczegóły.

### <a name="can-i-rename-a-data-factory"></a>Czy można zmienić fabryki danych?
Nie. Podobnie jak inne zasoby platformy Azure nie można zmienić nazwy fabryki danych Azure.

### <a name="can-i-move-a-data-factory-from-one-azure-subscription-to-another"></a>Można przenieść fabryka danych z jedną subskrypcją platformy Azure do innego?
Tak. Użyj **Przenieś** przycisku w bloku fabryki danych, jak pokazano na poniższym diagramie:

![Przenieś fabryki danych](media/data-factory-faq/move-data-factory.png)

### <a name="what-are-the-compute-environments-supported-by-data-factory"></a>Co to są środowiska obliczeniowe obsługiwane przez fabryki danych?
Poniższa tabela zawiera listę środowiska obliczeniowe obsługiwane przez fabryki danych i działań, które można uruchomić na nich.

| Środowisko obliczeniowe | activities |
| --- | --- |
| [Klaster usługi HDInsight na żądanie](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) lub [klastrem usługi HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) |[DotNet](data-factory-use-custom-activities.md), [Hive](data-factory-hive-activity.md), [Pig](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [przesyłania strumieniowego usługi Hadoop](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](data-factory-compute-linked-services.md#azure-batch-linked-service) |[DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning](data-factory-compute-linked-services.md#azure-machine-learning-linked-service) |[Działania usługi Machine Learning: wykonywanie wsadowe i aktualizacja zasobów](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure Data Lake Analytics](data-factory-compute-linked-services.md#azure-data-lake-analytics-linked-service) |[Język U-SQL usługi Data Lake Analytics](data-factory-usql-activity.md) |
| [Azure SQL](data-factory-compute-linked-services.md#azure-sql-linked-service), [magazyn danych Azure SQL](data-factory-compute-linked-services.md#azure-sql-data-warehouse-linked-service), [programu SQL Server](data-factory-compute-linked-services.md#sql-server-linked-service) |[Procedura składowana](data-factory-stored-proc-activity.md) |

### <a name="how-does-azure-data-factory-compare-with-sql-server-integration-services-ssis"></a>Jak fabryki danych Azure porównania z programu SQL Server Integration Services (SSIS)? 
Zobacz [vs fabryki danych Azure. SSIS](http://www.sqlbits.com/Sessions/Event15/Azure_Data_Factory_vs_SSIS) prezentacji z jednego z naszych MVP (najbardziej ważnych specjalistów): Reza Rad. Niektóre zmiany w fabryce danych może nie znajdować pokaz slajdów. Dodajemy stale więcej możliwości z fabryką danych Azure. Dodajemy stale więcej możliwości z fabryką danych Azure. Firma Microsoft będzie włączyć te aktualizacje do porównania danych integrację technologii firmy Microsoft chwilę później w tym roku.   

## <a name="activities---faq"></a>Działania — często zadawane pytania
### <a name="what-are-the-different-types-of-activities-you-can-use-in-a-data-factory-pipeline"></a>Jakie są różne typy działań, do których można używać w potoku fabryki danych?
* [Działania przepływu danych](data-factory-data-movement-activities.md) do przenoszenia danych.
* [Działania przekształcania danych](data-factory-data-transformation-activities.md) do procesu/przekształcenia danych.

### <a name="when-does-an-activity-run"></a>Gdy działanie jest uruchamiane?
**Dostępności** określa ustawienia konfiguracji w tabeli danych dane wyjściowe po uruchomieniu działania. Jeśli podano zestawów danych wejściowych, działanie sprawdza, czy spełnione są wszystkie zależności w danych wejściowych (to znaczy **gotowe** stanu) przed jej uruchomienie.

## <a name="copy-activity---faq"></a>Kopiuj działania — często zadawane pytania
### <a name="is-it-better-to-have-a-pipeline-with-multiple-activities-or-a-separate-pipeline-for-each-activity"></a>Jest to lepiej potoku z wielu działań lub oddzielnego potoku dla każdego działania?
Potoki mają pakietu powiązanych działań. Jeśli zestawów danych, które łączą te elementy nie są używane przez inne działania poza potoku, należy dysponować działania w jednej potoku. Dzięki temu nie musisz łańcuchu potoku aktywnych okresów tak aby były one ze sobą. Ponadto integralność danych w tabelach wewnętrzny do potoku lepiej jest zachowywany podczas aktualizowania potoku. Zasadniczo potoku aktualizacji zatrzymuje wszystkich działań w potoku, usuwa je i tworzy je ponownie. Z perspektywy tworzenia może być również widoczność przepływ danych w ramach pokrewne działania w jednym pliku JSON dla potoku.

### <a name="what-are-the-supported-data-stores"></a>Co to są obsługiwane magazynów?
Działanie kopiowania w usłudze Data Factory kopiuje dane z magazynu danych źródła do magazynu danych ujścia. Usługa Data Factory obsługuje następujące magazyny danych. Dane z dowolnego źródła można zapisać do dowolnego ujścia. Kliknij magazyn danych, aby dowiedzieć się, jak kopiować dane do i z tego magazynu.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Magazyny danych oznaczone znakiem * mogą być konfigurowane lokalnie lub w usłudze Azure IaaS i wymagają zainstalowania [bramy zarządzania danymi](data-factory-data-management-gateway.md) na maszynie lokalnej lub w usłudze Azure IaaS.

### <a name="what-are-the-supported-file-formats"></a>Co to są obsługiwane formaty plików?
[!INCLUDE [data-factory-file-format](../../../includes/data-factory-file-format.md)]

### <a name="where-is-the-copy-operation-performed"></a>Gdzie jest wykonywane operacji kopiowania
Zobacz [przenoszenia danych dostępnego globalnie](data-factory-data-movement-activities.md#global) sekcji, aby uzyskać szczegółowe informacje. Krótko mówiąc gdy jest używany z lokalnym magazynem danych, operacji kopiowania jest wykonywane przez bramy zarządzania danymi w środowisku lokalnym. I gdy przenoszenia danych między dwa sklepy chmury, operacja kopiowania odbywa się w regionie się najbliżej lokalizacji zbiornika w tej samej lokalizacji geograficznej.

## <a name="hdinsight-activity---faq"></a>Działanie usługi HDInsight — często zadawane pytania
### <a name="what-regions-are-supported-by-hdinsight"></a>Jakie regiony są obsługiwane przez HDInsight?
Zobacz sekcję dotyczącą dostępności geograficznej w następującym artykule: lub [szczegóły cennika usługi HDInsight][hdinsight-supported-regions].

### <a name="what-region-is-used-by-an-on-demand-hdinsight-cluster"></a>Jakie region jest używane przez klaster usługi HDInsight na żądanie?
Klaster usługi HDInsight na żądanie jest tworzony w tym samym regionie, w którym istnieje określona do użycia w klastrze magazynu.    

### <a name="how-to-associate-additional-storage-accounts-to-your-hdinsight-cluster"></a>Jak skojarzyć dodatkowe konta magazynu do klastra usługi HDInsight?
Jeśli korzystasz z własnego klastra usługi HDInsight (BYOC - Przenieś swój własny klastra), zobacz następujące tematy:

* [Używanie klastra usługi HDInsight z kont magazynu alternatywny i magazyny][hdinsight-alternate-storage]
* [Użyj dodatkowych kont magazynu przy użyciu HDInsight Hive][hdinsight-alternate-storage-2]

Jeśli korzystasz z klastra na żądanie, który jest tworzony przez usługi fabryka danych, określ dodatkowe konta magazynu dla usługi HDInsight połączonej usługi, dzięki czemu usługi fabryka danych można zarejestrować je w Twoim imieniu. W definicji JSON połączonej usługi na żądanie, użyj **additionalLinkedServiceNames** właściwości w celu określenia konta magazynu alternatywny, jak pokazano w poniższy fragment kodu JSON:

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
W powyższym przykładzie otherLinkedServiceName1 i otherLinkedServiceName2 reprezentują połączone usługi, których definicje zawierają klastra usługi HDInsight wymaga dostępu do kont magazynu alternatywnych poświadczeń.

## <a name="slices---faq"></a>Wycinki — często zadawane pytania
### <a name="why-are-my-input-slices-not-in-ready-state"></a>Dlaczego są moje wycinków wejściowy nie jest w stanie gotowe?
Powszechnym błędem jest to ustawienie nie zostanie **zewnętrznych** właściwości **true** na wejściowego zestawu danych, gdy danych wejściowych jest zewnętrznych z fabryką danych (nie produkowane przez fabryki danych).

W poniższym przykładzie, wystarczy ustawić **zewnętrznych** true na **dataset1**.  

**DataFactory1** potoku 1: dataset1 -> działania activity1 -> dataset2 -> activity2 -> potoku dataset3 2: dataset3 -> activity3 -> dataset4

Jeśli masz inną fabryka danych z potok, który przyjmuje dataset4 (utworzonych w ramach potoku 2 w fabryce danych 1), oznacz dataset4 jako zewnętrzny zestaw danych, ponieważ zestaw danych jest generowany przez fabrykę danych (DataFactory1, nie DataFactory2).  

**DataFactory2**    
Potok 1: działanie activity4 -> dataset4 -> dataset5

Jeśli właściwość zewnętrzne są ustawione właściwie, sprawdź, czy dane wejściowe istnieje w lokalizacji określonej w definicji zestawu danych wejściowych.

### <a name="how-to-run-a-slice-at-another-time-than-midnight-when-the-slice-is-being-produced-daily"></a>Jak uruchomić wycinek w innym czasie niż północy po wycinek jest tworzonym codziennie?
Użyj **przesunięcie** właściwości, aby określić czas, w którym ma wycinek do wyprodukowania. Zobacz [dostępności zestawu danych](data-factory-create-datasets.md#dataset-availability) sekcji, aby uzyskać szczegółowe informacje o tej właściwości. Poniżej przedstawiono prosty przykład:

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
Codzienne wycinków rozpoczęli **6: 00** zamiast domyślnego północy.     

### <a name="how-can-i-rerun-a-slice"></a>Jak można ponownie uruchomić wycinek?
Wycinek, można uruchomić w jednym z następujących sposobów:

* Umożliwia monitorowanie i zarządzanie aplikacjami Uruchom okno działania lub wycinka. Zobacz [Uruchom ponownie wybrane działanie windows](data-factory-monitor-manage-app.md#perform-batch-actions) instrukcje.   
* Kliknij przycisk **Uruchom** na pasku poleceń u **WYCINKA danych** bloku dla wycinka w portalu Azure.
* Uruchom **AzureRmDataFactorySliceStatus zestaw** polecenia cmdlet, o stanie ustawioną **oczekiwania** dla wycinka.   

    ```PowerShell
    Set-AzureRmDataFactorySliceStatus -Status Waiting -ResourceGroupName $ResourceGroup -DataFactoryName $df -TableName $table -StartDateTime "02/26/2015 19:00:00" -EndDateTime "02/26/2015 20:00:00"
    ```
Zobacz [AzureRmDataFactorySliceStatus zestaw] [ set-azure-datafactory-slice-status] szczegółowe informacje na temat polecenia cmdlet.

### <a name="how-long-did-it-take-to-process-a-slice"></a>Jak długo trwało przetworzyć wycinek?
Użyj Eksploratora okna działania monitora i zarządzania aplikacji należy wiedzieć, jak długo trwało przetwarzania wycinka danych. Zobacz [działania okna Eksploratora](data-factory-monitor-manage-app.md#activity-window-explorer) szczegółowe informacje.

Można również wykonać następujące czynności w portalu Azure:  

1. Kliknij przycisk **zestawów danych** Kafelek na **FABRYKI danych** bloku fabryką danych.
2. Kliknięcie określonego zestawu danych **zestawów danych** bloku.
3. Wybierz wycinek, który chcesz z **ostatnich wycinków** listy na **tabeli** bloku.
4. Kliknij przycisk uruchamiania z działania **uruchomień działania** listy na **WYCINKA danych** bloku.
5. Kliknij przycisk **właściwości** Kafelek na **szczegóły uruchomienia działania** bloku.
6. Powinny pojawić się **czas trwania** pole z wartością. Ta wartość jest czasu przetwarzania wycinka.   

### <a name="how-to-stop-a-running-slice"></a>Jak zatrzymać uruchomione wycinek?
Jeśli chcesz zatrzymać potoku wykonywanie, możesz użyć [Suspend-AzureRmDataFactoryPipeline](/powershell/module/azurerm.datafactories/suspend-azurermdatafactorypipeline) polecenia cmdlet. Obecnie wstrzymywania potoku nie zatrzymuje wykonaniami wycinka, które są w toku. Po zakończyć wykonaniami w toku, nie dodatkowe wycinek zostaje pobrana.

Czy na pewno chcesz zatrzymać wszystkie wykonaniami natychmiast, jedynym sposobem byłoby potoku Usuń i utwórz go ponownie. Jeśli wybierzesz opcję usunięcia potoku, Usuń tabele i połączone usługi używane przez potok nie jest konieczne.

[create-factory-using-dotnet-sdk]: data-factory-create-data-factories-programmatically.md
[msdn-class-library-reference]: /dotnet/api/microsoft.azure.management.datafactories.models
[msdn-rest-api-reference]: /rest/api/datafactory/

[adf-powershell-reference]: /powershell/resourcemanager/azurerm.datafactories/v2.3.0/azurerm.datafactories
[azure-portal]: http://portal.azure.com
[set-azure-datafactory-slice-status]: /powershell/resourcemanager/azurerm.datafactories/v2.3.0/set-azurermdatafactoryslicestatus

[adf-pricing-details]: http://go.microsoft.com/fwlink/?LinkId=517777
[hdinsight-supported-regions]: http://azure.microsoft.com/pricing/details/hdinsight/
[hdinsight-alternate-storage]: http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx
[hdinsight-alternate-storage-2]: http://blogs.msdn.com/b/cindygross/archive/2014/05/05/use-additional-storage-accounts-with-hdinsight-hive.aspx
