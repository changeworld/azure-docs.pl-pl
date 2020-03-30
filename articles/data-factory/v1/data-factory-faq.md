---
title: Fabryka danych platformy Azure — często zadawane pytania
description: Często zadawane pytania dotyczące usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 1b042c0279e458cf83bd5c81147f4b1f25d7687d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645144"
---
# <a name="azure-data-factory---frequently-asked-questions"></a>Fabryka danych platformy Azure — często zadawane pytania
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [często zadawane pytania - Data Factory](../frequently-asked-questions.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="general-questions"></a>Pytania ogólne
### <a name="what-is-azure-data-factory"></a>Czym jest usługa Azure Data Factory?
Fabryka danych to chmurowa usługa integracji danych, która **automatyzuje przenoszenie i przekształcanie danych.** Podobnie jak fabryka, która uruchamia sprzęt do pobierania surowców i przekształcania ich w gotowe towary, Data Factory organizuje istniejące usługi, które zbierają surowe dane i przekształcają je w gotowe do użycia informacje.

Usługa Data Factory umożliwia tworzenie opartych na danych przepływów pracy w celu przenoszenia danych między magazynami danych lokalnych i chmurowych, a także przetwarzania/przekształcania danych przy użyciu usług obliczeniowych, takich jak Azure HDInsight i Usługa Azure Data Lake Analytics. Po utworzeniu potoku, który wykonuje akcję, która jest potrzebna, można zaplanować jego uruchamianie okresowo (co godzinę, codziennie, co tydzień itp.).   

Aby uzyskać więcej informacji, zobacz [Omówienie & pojęcia kluczowe](data-factory-introduction.md).

### <a name="where-can-i-find-pricing-details-for-azure-data-factory"></a>Gdzie mogę znaleźć szczegóły dotyczące cen usługi Azure Data Factory?
Zobacz [szczegóły cenowania fabryki danych, aby][adf-pricing-details] uzyskać szczegółowe informacje o cenach dla fabryki danych platformy Azure.  

### <a name="how-do-i-get-started-with-azure-data-factory"></a>Jak rozpocząć pracę z usługą Azure Data Factory?
* Aby zapoznać się z omówieniem usługi Azure Data Factory, zobacz [Wprowadzenie do usługi Azure Data Factory](data-factory-introduction.md).
* Aby zapoznać się z samouczkiem na temat **kopiowania/przenoszenia danych** przy użyciu działania kopiowania, zobacz [Kopiowanie danych z usługi Azure Blob Storage do usługi Azure SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
* W samouczku na temat **przekształcania danych** przy użyciu aktywności gałęzi usługi HDInsight. Zobacz [Przetwarzanie danych przez uruchomienie skryptu hive w klastrze Hadoop](data-factory-build-your-first-pipeline.md)

### <a name="what-is-the-data-factorys-region-availability"></a>Jaka jest dostępność regionu fabryki danych?
Data Factory jest dostępna w Europie Zachodniej i **Północnej**stanów **Zjednoczonych.** Usługi obliczeniowe i magazynowania używane przez fabryki danych mogą znajdować się w innych regionach. Zobacz [Obsługiwane regiony](data-factory-introduction.md#supported-regions).

### <a name="what-are-the-limits-on-number-of-data-factoriespipelinesactivitiesdatasets"></a>Jakie są limity liczby fabryk danych/potoków/działań/zestawów danych?
Zobacz **sekcję Limity fabryczne usługi Azure** w artykule [Limity subskrypcji i usług platformy Azure, przydziały i ograniczenia.](../../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits)

### <a name="what-is-the-authoringdeveloper-experience-with-azure-data-factory-service"></a>Jakie środowisko jest tworzenie/deweloper z usługą Azure Data Factory?
Fabryki danych można tworzyć/tworzyć przy użyciu jednego z następujących narzędzi/zestawów SDK:

* **Program Visual Studio** Za pomocą programu Visual Studio można utworzyć fabrykę danych platformy Azure. Zobacz [Tworzenie pierwszego potoku danych przy użyciu programu Visual Studio, aby](data-factory-build-your-first-pipeline-using-vs.md) uzyskać szczegółowe informacje.
* **Program Azure PowerShell** Zobacz [Tworzenie i monitorowanie usługi Azure Data Factory przy użyciu programu Azure PowerShell](data-factory-build-your-first-pipeline-using-powershell.md) dla samouczka/przewodnika do tworzenia fabryki danych przy użyciu programu PowerShell. Aby uzyskać kompleksową dokumentację poleceń cmdlet fabryki danych, zobacz zawartość [polecenia cmdlet][adf-powershell-reference] fabryki danych w bibliotece MSDN.
* **Biblioteka klas .NET** Można programowo tworzyć fabryki danych przy użyciu sdk .NET. Zobacz [Tworzenie, monitorowanie i zarządzanie fabrykami danych przy użyciu pliku .NET SDK,](data-factory-create-data-factories-programmatically.md) aby uzyskać wskazówki dotyczące tworzenia fabryki danych przy użyciu pliku .NET SDK. Zobacz [Odwołanie do biblioteki klas fabryki danych, aby][msdn-class-library-reference] uzyskać kompleksową dokumentację sdk data factory .NET.
* **INTERFEJS API ODPOCZYNKU** Można również użyć interfejsu API REST udostępniane przez usługę Azure Data Factory do tworzenia i wdrażania fabryk danych. Zobacz [Odwołanie do interfejsu API REST fabryki danych, aby][msdn-rest-api-reference] uzyskać kompleksową dokumentację interfejsu API REST fabryki danych.
* **Szablon usługi Azure Resource Manager** Zobacz [samouczek: Tworzenie pierwszej fabryki danych platformy Azure przy użyciu szablonu usługi Azure Resource Manager](data-factory-build-your-first-pipeline-using-arm.md) fo szczegóły.

### <a name="can-i-rename-a-data-factory"></a>Czy mogę zmienić nazwę fabryki danych?
Nie. Podobnie jak inne zasoby platformy Azure nie można zmienić nazwy fabryki danych platformy Azure.

### <a name="can-i-move-a-data-factory-from-one-azure-subscription-to-another"></a>Czy mogę przenieść fabrykę danych z jednej subskrypcji platformy Azure do innej?
Tak. Użyj przycisku **Przenieś** na bloku fabryki danych, jak pokazano na poniższym diagramie:

![Przenoszenie fabryki danych](media/data-factory-faq/move-data-factory.png)

### <a name="what-are-the-compute-environments-supported-by-data-factory"></a>Jakie środowiska obliczeniowe są obsługiwane przez fabrykę danych?
Poniższa tabela zawiera listę środowisk obliczeniowych obsługiwanych przez fabrykę danych i działania, które można na nich uruchomić.

| Środowisko obliczeniowe | activities |
| --- | --- |
| [Klaster HDInsight na żądanie](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) lub [własny klaster HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) |[DotNet](data-factory-use-custom-activities.md), [Ul](data-factory-hive-activity.md), [Świnia](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [Hadoop Streaming](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](data-factory-compute-linked-services.md#azure-batch-linked-service) |[Dotnet](data-factory-use-custom-activities.md) |
| [Uczenie maszynowe platformy Azure](data-factory-compute-linked-services.md#azure-machine-learning-linked-service) |[Działania usługi Machine Learning: wykonywanie wsadowe i aktualizacja zasobów](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure Data Lake Analytics](data-factory-compute-linked-services.md#azure-data-lake-analytics-linked-service) |[Data Lake Analytics U-SQL](data-factory-usql-activity.md) |
| [Usługa Azure SQL](data-factory-compute-linked-services.md#azure-sql-linked-service), [usługa Azure SQL Data Warehouse](data-factory-compute-linked-services.md#azure-sql-data-warehouse-linked-service), sql [server](data-factory-compute-linked-services.md#sql-server-linked-service) |[Procedura składowana](data-factory-stored-proc-activity.md) |

### <a name="how-does-azure-data-factory-compare-with-sql-server-integration-services-ssis"></a>Jak usługa Azure Data Factory wypada w porównaniu z usługami integracji programu SQL Server (SSIS)? 
Zobacz prezentację [Usługi Azure Data Factory vs. SSIS](https://www.sqlbits.com/Sessions/Event15/Azure_Data_Factory_vs_SSIS) z jednego z naszych programów MVP (Najbardziej cenionych specjalistów): Reza Rad. Niektóre z ostatnich zmian w fabryce danych mogą nie być wymienione na pokładzie slajdów. Stale dodajemy więcej możliwości do usługi Azure Data Factory. Stale dodajemy więcej możliwości do usługi Azure Data Factory. Te aktualizacje włączymy do porównania technologii integracji danych firmy Microsoft jeszcze w tym roku.   

## <a name="activities---faq"></a>Zajęcia - CZĘSTO ZADAWANE PYTANIA
### <a name="what-are-the-different-types-of-activities-you-can-use-in-a-data-factory-pipeline"></a>Jakie są różne typy działań, których można użyć w potoku usługi Data Factory?
* [Działania związane z przenoszeniem danych](data-factory-data-movement-activities.md) w celu przenoszenia danych.
* [Działania przekształcania danych](data-factory-data-transformation-activities.md) do przetwarzania/przekształcania danych.

### <a name="when-does-an-activity-run"></a>Kiedy działanie jest uruchamiane?
Ustawienie konfiguracji **dostępności** w tabeli danych wyjściowych określa, kiedy działanie jest uruchamiane. Jeśli dane wejściowe są określone, działanie sprawdza, czy wszystkie zależności danych wejściowych są spełnione (czyli stan **Gotowy)** przed rozpoczęciem działania.

## <a name="copy-activity---faq"></a>Aktywność kopiowania — często zadawane pytania
### <a name="is-it-better-to-have-a-pipeline-with-multiple-activities-or-a-separate-pipeline-for-each-activity"></a>Czy lepiej mieć potok z wieloma działaniami lub oddzielny potok dla każdego działania?
Rurociągi mają łączyć powiązane działania. Jeśli zestawy danych, które je łączą, nie są używane przez inne działania poza potokiem, można zachować działania w jednym potoku. W ten sposób nie trzeba łańcucha potoku aktywnych okresów, tak aby były one wyrównane ze sobą. Ponadto integralność danych w tabelach wewnętrznych do potoku jest lepiej zachowane podczas aktualizowania potoku. Aktualizacja potoku zasadniczo zatrzymuje wszystkie działania w potoku, usuwa je i tworzy je ponownie. Z perspektywy tworzenia może być również łatwiej zobaczyć przepływ danych w ramach powiązanych działań w jednym pliku JSON dla potoku.

### <a name="what-are-the-supported-data-stores"></a>Jakie są obsługiwane magazyny danych?
Działanie kopiowania w usłudze Data Factory kopiuje dane z magazynu danych źródła do magazynu danych ujścia. Usługa Data Factory obsługuje następujące magazyny danych. Dane z dowolnego źródła można zapisać do dowolnego ujścia. Kliknij magazyn danych, aby dowiedzieć się, jak kopiować dane do i z tego magazynu.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Magazyny danych oznaczone znakiem * mogą być konfigurowane lokalnie lub w usłudze Azure IaaS i wymagają zainstalowania [bramy zarządzania danymi](data-factory-data-management-gateway.md) na maszynie lokalnej lub w usłudze Azure IaaS.

### <a name="what-are-the-supported-file-formats"></a>Jakie są obsługiwane formaty plików?
[!INCLUDE [data-factory-file-format](../../../includes/data-factory-file-format.md)]

### <a name="where-is-the-copy-operation-performed"></a>Gdzie jest wykonywana operacja kopiowania?
Szczegółowe informacje można znaleźć w sekcji [Globalnie dostępnego przenoszenia danych.](data-factory-data-movement-activities.md#global) Krótko mówiąc, gdy w sprawę zaangażowany jest lokalny magazyn danych, operacja kopiowania jest wykonywana przez bramę zarządzania danymi w środowisku lokalnym. A gdy przenoszenie danych znajduje się między dwoma magazynami w chmurze, operacja kopiowania jest wykonywana w regionie najbliżej lokalizacji ujścia w tej samej lokalizacji geograficznej.

## <a name="hdinsight-activity---faq"></a>Aktywność HDInsight — często zadawane pytania
### <a name="what-regions-are-supported-by-hdinsight"></a>Jakie regiony są obsługiwane przez HDInsight?
Zobacz sekcję Dostępność geograficzna w następującym artykule: lub [Szczegóły cen HDInsight][hdinsight-supported-regions].

### <a name="what-region-is-used-by-an-on-demand-hdinsight-cluster"></a>Jaki region jest używany przez klaster HDInsight na żądanie?
Klaster HDInsight na żądanie jest tworzony w tym samym regionie, w którym istnieje magazyn określony do użycia z klastrem.    

### <a name="how-to-associate-additional-storage-accounts-to-your-hdinsight-cluster"></a>Jak skojarzyć dodatkowe konta magazynu z klastrem HDInsight?
Jeśli używasz własnego klastra HDInsight (BYOC - Bring Your Own Cluster), zobacz następujące tematy:

* [Korzystanie z klastra HDInsight z alternatywnymi kontami magazynu i metasklepami][hdinsight-alternate-storage]
* [Korzystanie z dodatkowych kont magazynu z gałęzią hdinsight][hdinsight-alternate-storage-2]

Jeśli używasz klastra na żądanie, który jest tworzony przez usługę Data Factory, należy określić dodatkowe konta magazynu dla usługi połączonej HDInsight, tak aby usługa Data Factory mogła zarejestrować je w Twoim imieniu. W definicji JSON dla usługi połączonej na żądanie użyj **właściwości additionalLinkedServiceNames,** aby określić alternatywne konta magazynu, jak pokazano we następującym urywek JSON:

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
W powyższym przykładzie otherLinkedServiceName1 i otherLinkedServiceName2 reprezentują połączone usługi, których definicje zawierają poświadczenia, które klaster HDInsight musi uzyskać dostęp do kont magazynu alternatywnego.

## <a name="slices---faq"></a>Plasterki — często zadawane pytania
### <a name="why-are-my-input-slices-not-in-ready-state"></a>Dlaczego moje wycinki wejściowe nie są w stanie Gotowy?
Częstym błędem nie jest ustawienie właściwości **zewnętrznej** **true** na input dataset, gdy dane wejściowe są zewnętrzne do fabryki danych (nie produkowane przez fabrykę danych).

W poniższym przykładzie wystarczy tylko ustawić **wartość zewnętrzna** na true w **zestawie danych1**.  

**DataFactory1** Potok 1: zestaw danych1 -> activity1 -> dataset2 -> activity2 -> dataset3 Pipeline 2: dataset3-> activity3 -> dataset4

Jeśli masz inną fabrykę danych z potoku, który pobiera dataset4 (produkowane przez potok 2 w fabryce danych 1), oznacz zestaw danych4 jako zewnętrzny zestaw danych, ponieważ zestaw danych jest produkowany przez inną fabrykę danych (DataFactory1, nie DataFactory2).  

**DataFactory2**    
Potok 1: zestaw danych4->activity4->zestaw danych5

Jeśli właściwość zewnętrzna jest poprawnie ustawiona, sprawdź, czy dane wejściowe istnieją w lokalizacji określonej w definicji wejściowego zestawu danych.

### <a name="how-to-run-a-slice-at-another-time-than-midnight-when-the-slice-is-being-produced-daily"></a>Jak uruchomić plasterek o innej porze niż o północy, gdy plasterek jest produkowany codziennie?
Użyj właściwości **przesunięcia,** aby określić czas, w którym mają być produkowane plasterek. Zobacz sekcja [dostępności zestawu danych,](data-factory-create-datasets.md#dataset-availability) aby uzyskać szczegółowe informacje na temat tej właściwości. Oto krótki przykład:

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
Dzienne plasterki zaczynają się od **6:00** zamiast domyślnej północy.     

### <a name="how-can-i-rerun-a-slice"></a>Jak mogę ponownie uruchomić plasterek?
Plasterek można ponownie uruchomić w jeden z następujących sposobów:

* Użyj funkcji Monitoruj i zarządzaj aplikacją, aby ponownie uruchomić okno aktywności lub plasterek. Aby uzyskać instrukcje, zobacz [Ponowne rozpoczynanie wybranych okien aktywności.](data-factory-monitor-manage-app.md#perform-batch-actions)   
* Kliknij **przycisk Uruchom** na pasku poleceń na bloku FRAGMENT **DANYCH** dla plasterka w witrynie Azure portal.
* Uruchom polecenie cmdlet **Set-AzDataFactorySliceStatus** ze stanem **ustawionym na Oczekiwanie na** plasterek.   

    ```powershell
    Set-AzDataFactorySliceStatus -Status Waiting -ResourceGroupName $ResourceGroup -DataFactoryName $df -TableName $table -StartDateTime "02/26/2015 19:00:00" -EndDateTime "02/26/2015 20:00:00"
    ```
  Zobacz [Set-AzDataFactorySliceStatus,][set-azure-datafactory-slice-status] aby uzyskać szczegółowe informacje na temat polecenia cmdlet.

### <a name="how-long-did-it-take-to-process-a-slice"></a>Jak długo trzeba czekać na przetworzenie plasterka?
Użyj Eksploratora okien aktywności w Monitorze & Zarządzaj aplikacją, aby wiedzieć, jak długo trwało przetwarzanie plasterka danych. Zobacz [Eksploratora okien aktywności, aby](data-factory-monitor-manage-app.md#activity-window-explorer) uzyskać szczegółowe informacje.

Można również wykonać następujące czynności w witrynie Azure portal:  

1. Kliknij **kafelek Zestawów danych** na bloku **DATA FACTORY** dla fabryki danych.
2. Kliknij określony zestaw danych w bloku **Zestawy danych.**
3. Zaznacz interesujący Cię plasterek z listy **Ostatnie plasterki** na bloku **TABELA.**
4. Kliknij działanie uruchomione z listy **Przebiegi działania** w bloku **PLASTEREK DANYCH.**
5. Kliknij **kafelek Właściwości** na bloku **SZCZEGÓŁY PRZEBIEGU DZIAŁANIA.**
6. Powinno zostać wyświetlene pole **CZAS TRWANIA** z wartością. Ta wartość jest czas czasu poświęconego do przetworzenia plasterka.   

### <a name="how-to-stop-a-running-slice"></a>Jak zatrzymać kolejny plasterek?
Jeśli trzeba zatrzymać potoku z wykonywania, można użyć [Suspend-AzDataFactoryPipeline](/powershell/module/az.datafactory/suspend-azdatafactorypipeline) polecenia cmdlet. Obecnie zawieszenie potoku nie zatrzymuje wykonywania plasterka, które są w toku. Po zakończeniu wykonywania w toku nie jest pobierany żaden dodatkowy plasterek.

Jeśli naprawdę chcesz natychmiast zatrzymać wszystkie wykonania, jedynym sposobem byłoby usunięcie potoku i utworzenie go ponownie. Jeśli zdecydujesz się usunąć potok, NIE trzeba usuwać tabel i połączonych usług używanych przez potok.

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
