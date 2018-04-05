---
title: Prognozy w podręczniku technicznym energii | Dokumentacja firmy Microsoft
description: Podręcznik techniczny do szablonu rozwiązania z Microsoft Cortana Intelligence dla Prognoza energii.
services: cortana-analytics
documentationcenter: ''
author: yijichen
manager: ilanr9
editor: yijichen
ms.assetid: 7f1a866b-79b7-4b97-ae3e-bc6bebe8c756
ms.service: cortana-analytics
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2016
ms.author: inqiu;yijichen;ilanr9
ms.openlocfilehash: bb3520d36e4c34c752fe388f3126da285e2161cd
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="technical-guide-to-the-cortana-intelligence-solution-template-for-demand-forecast-in-energy"></a>Podręcznik techniczny do szablonu Cortana analizy rozwiązania dla Prognoza energii
## <a name="overview"></a>**Omówienie**
Szablony rozwiązań zostały zaprojektowane w celu przyspieszenia procesu tworzenia pokaz E2E u góry pakietu Cortana Intelligence Suite. Szablon wdrożonej udostępnia subskrypcji z wymagany składnik Cortana Intelligence i tworzenie relacji między. On również nasion potoku danych z przykładowymi danymi pobierania wygenerowanych z aplikacji symulacji danych. Pobrać symulatora danych z łączem i zainstaluj go na komputerze lokalnym, zajrzyj do plik readme.txt, aby instrukcje dotyczące przy użyciu symulatora. Dane generowane przez symulator hydrates potoku danych i rozpoczęcia generowania prognozowania uczenia maszynowego, które mogą być następnie wizualizowane na pulpicie nawigacyjnym usługi Power BI.

Szablon rozwiązania można znaleźć [tutaj](https://gallery.cortanaintelligence.com/SolutionTemplate/Demand-Forecasting-for-Energy-1)

Proces wdrażania przeprowadzi Cię przez kilka kroków, aby skonfigurować poświadczenia rozwiązania. Upewnij się, że rekord tych poświadczeń, takich jak nazwa rozwiązania, nazwę użytkownika i hasło, które należy podać podczas wdrażania.

Celem niniejszego dokumentu jest architektura referencyjna struktury i różnych składników udostępniane w ramach subskrypcji w ramach tego rozwiązania szablonu. Dokument zawiera również informacje o sposób zamienić przykładowe dane, dane osobnego można wyświetlać informacje na temat technologii/prognoz w przypadku danych. Ponadto dokumencie omówiono części szablon rozwiązania, który musi być modyfikowany, jeśli chcesz dostosować rozwiązania z użyciem własnych danych. Instrukcje dotyczące sposobu tworzenia pulpitu nawigacyjnego usługi Power BI dla tego szablonu rozwiązania znajdują się na końcu.

## <a name="details"></a>**Szczegóły**
![](media/cortana-analytics-technical-guide-demand-forecast/ca-topologies-energy-forecasting.png)

### <a name="architecture-explained"></a>Architektura wyjaśniono
Po wdrożeniu rozwiązania zostaną aktywowane różne usługi platformy Azure w ramach Cortana Analytics Suite (to znaczy Centrum zdarzeń, Stream Analytics, HDInsight, fabryki danych, Machine Learning *itp.*). Diagram architektury wskazuje na wysokim poziomie, jak funkcja prognozowania żądanie energii szablon rozwiązania jest tworzony z end-to-end. Te usługi można zbadać, klikając je na diagramie szablon rozwiązania utworzone przy użyciu wdrażania rozwiązania. W poniższych sekcjach opisano każdego z nich.

## <a name="data-source-and-ingestion"></a>**Źródło danych i wprowadzanie**
### <a name="synthetic-data-source"></a>Źródło danych syntetycznego
W przypadku tego szablonu źródło danych używane jest generowana z aplikacją, która Pobierz i uruchom lokalnie, po pomyślnym wdrożeniu. Zapoznać się z instrukcją, aby pobrać i zainstalować tę aplikację na pasku właściwości po wybraniu pierwszego węzła o nazwie symulatora danych prognozowania energii na diagramie szablon rozwiązania. Ta aplikacja źródła [Azure Event Hub](#azure-event-hub) usługi z punktów danych lub zdarzeń, które są używane w pozostałej części przepływu rozwiązania.

Aplikacja generowania zdarzeń wypełnia Azure Event Hub tylko wtedy, gdy jest wykonywane na tym komputerze.

### <a name="azure-event-hub"></a>Azure Event Hub
[Azure Event Hub](https://azure.microsoft.com/services/event-hubs/) usługi jest odbiorcą danych wejściowych dostarczonych przez źródło danych syntetycznego opisem.

## <a name="data-preparation-and-analysis"></a>**Przygotowanie danych i analiza**
### <a name="azure-stream-analytics"></a>Usługa Azure Stream Analytics
[Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) usługi służy do zapewnia przekazywaną praktycznie w czasie rzeczywistym analizy strumienia wejściowego z [Azure Event Hub](#azure-event-hub) usługi i opublikować wyniki na [usługi Power BI](https://powerbi.microsoft.com) pulpitu nawigacyjnego, jak również wszystkie nieprzetworzone zdarzenia przychodzące do archiwizacji [usługi Azure Storage](https://azure.microsoft.com/services/storage/) usługi do późniejszego przetwarzania przez [fabryki danych Azure](https://azure.microsoft.com/documentation/services/data-factory/) usługi.

### <a name="hdinsight-custom-aggregation"></a>Niestandardowe HDInsight agregacji
Usługa Azure HDInsight służy do uruchamiania [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skryptów (zorkiestrowana przez fabryki danych Azure) do świadczenia agregacji w pierwotnych zdarzenia, które zostały zarchiwizowane przy użyciu usługi Azure Stream Analytics.

### <a name="azure-machine-learning"></a>Azure Machine Learning
[Usługi Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) (zorkiestrowana przez fabryki danych Azure), używana jest usługa aby prognozy na zużycie energii przyszłych podane dane wejściowe odebrane danego regionu.

## <a name="data-publishing"></a>**Publikowanie danych**
### <a name="azure-sql-database-service"></a>Usługa bazy danych Azure SQL
[Bazy danych SQL Azure](https://azure.microsoft.com/services/sql-database/) usługi jest używany do przechowywania (zarządzane przez fabryki danych Azure) prognoz odbierane przez usługę uczenie maszynowe Azure, który jest używany w [usługi Power BI](https://powerbi.microsoft.com) pulpitu nawigacyjnego.

## <a name="data-consumption"></a>**Użycie danych**
### <a name="power-bi"></a>Power BI
[Usługi Power BI](https://powerbi.microsoft.com) usługi służy do pokazywania pulpit nawigacyjny, który zawiera agregacji dostarczonych przez [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) usługi, a także żądanie prognozy przechowywane w wynikach [bazy danych SQL Azure](https://azure.microsoft.com/services/sql-database/) które zostały utworzone przy użyciu [usługi Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) usługi. Instrukcje dotyczące sposobu tworzenia pulpitu nawigacyjnego usługi Power BI dla tego szablonu rozwiązania można znaleźć w poniższej sekcji.

## <a name="how-to-bring-in-your-own-data"></a>**Jak przenieść własne dane**
W tej sekcji opisano dotyczące przywracania danych użytkownika na platformie Azure, a obszary wymagałyby zmiany danych, które można przenosić w tej architekturze.

Jest mało prawdopodobne, że wszelkie zestawu danych, które można przenosić spowoduje dopasowanie używane dla tego szablonu rozwiązania zestawu danych. Opis danych i wymagania są niezwykle istotne w sposób zmodyfikować ten szablon służy do pracy z własnych danych. Jeśli są nowe do usługi Azure Machine Learning, wprowadzenie do niej można uzyskać, korzystając w przykładzie w [Tworzenie pierwszego eksperymentu](machine-learning/studio/create-experiment.md).

W poniższych sekcjach omówiono części szablonu, który wymaga modyfikacji, jeśli wprowadzono nowy zestaw danych.

### <a name="azure-event-hub"></a>Azure Event Hub
[Azure Event Hub](https://azure.microsoft.com/services/event-hubs/) usługi jest ogólny, w taki sposób, że dane mogą być publikowane w Centrum w formacie CSV lub JSON. Brak specjalnego przetwarzania występuje w Centrum zdarzeń platformy Azure, ale należy pamiętać, że rozumiesz dane, które są przekazywane do niego.

Ten dokument nie opisuje sposobu pozyskiwania danych, ale jeden łatwe wysyłanie zdarzenia lub dane do usługi Azure Event Hub, za pomocą [API Centrum zdarzeń](event-hubs/event-hubs-programming-guide.md).

### <a name="azure-stream-analytics"></a>Usługa Azure Stream Analytics
[Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) usługi służy do zapewnia przekazywaną praktycznie analiz w czasie rzeczywistym za odczytywanie ze strumieni danych i generowanie dowolną liczbę źródeł danych.

Żądanie Prognozowanie na szablon rozwiązania energii zapytania usługi Azure Stream Analytics składa się z dwóch podzapytań, korzystanie z każdego zdarzenia z usługi Azure Event Hub jako dane wejściowe i konieczności dane wyjściowe z dwóch różnych lokalizacjach. Te dane wyjściowe zawierają zestawy danych usługi Power BI oraz jedną lokalizację magazynu Azure.

[Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) można znaleźć zapytania według:

* Logowanie do [portalu Azure](https://portal.azure.com/)
* Lokalizowanie zadania usługi analiza strumienia ![](media/cortana-analytics-technical-guide-demand-forecast/icon-stream-analytics.png) które zostały wygenerowane, gdy rozwiązanie zostało wdrożone. Jeden jest przekazywanie danych do magazynu obiektów blob (na przykład mytest1streaming432822asablob) i jeden z nich ma przekazywanie danych do usługi Power BI (na przykład mytest1streaming432822asapbi).
* Wybieranie

  * ***Dane wejściowe*** Aby wyświetlić zapytanie danych wejściowych
  * ***ZAPYTANIE*** Aby wyświetlić samą kwerendę
  * ***GENERUJE*** do wyświetlania różnych danych wyjściowych

Informacji na temat tworzenia zapytań usługi Azure Stream Analytics można znaleźć w [Stream Analytics kwerendy odwołania](https://msdn.microsoft.com/library/azure/dn834998.aspx) w witrynie MSDN.

W tym rozwiązaniu zadanie usługi analiza strumienia Azure, który wyprowadza zestaw danych o niemal analiz w czasie rzeczywistym informacje o przychodzącego strumienia danych do pulpitu nawigacyjnego usługi Power BI jest dostarczane jako część tego szablonu rozwiązania. Ponieważ nie istnieje niejawna wiedzą na temat formatu przychodzących danych, te zapytania musi zostać zmienione w zależności od formatu danych.

Inne zadania usługi analiza strumienia Azure Wyświetla wszystkie [Centrum zdarzeń](https://azure.microsoft.com/services/event-hubs/) zdarzenia [usługi Azure Storage](https://azure.microsoft.com/services/storage/) i dlatego wymaga informacji pełnej zdarzeń przesyłanego strumieniowo do magazynu nie zmian niezależnie od formatu danych.

### <a name="azure-data-factory"></a>Azure Data Factory
[Fabryki danych Azure](https://azure.microsoft.com/documentation/services/data-factory/) Usługa uruchamia przepływ i przetwarzania danych. W żądanie Prognozowanie na szablon rozwiązania energii 12 składa się z fabryką danych [potoki](data-factory/concepts-pipelines-activities.md) czy przenoszenia i przetwarzania danych przy użyciu różnych technologii.

  Można uzyskać dostępu do fabrykę danych przez otwarcie węzła fabryki danych w dolnej części diagramu szablon rozwiązania utworzone przy użyciu wdrażania rozwiązania. Zostanie wyświetlony fabryki danych w portalu Azure. Jeśli błędy są wyświetlane w obszarze zestawów danych, możesz zignorować te są one z powodu fabryki danych wdrażany przed generator danych zostało uruchomione. Te błędy nie uniemożliwiają działanie fabryką danych.

W tej sekcji omówiono niezbędnych [potoki](data-factory/concepts-pipelines-activities.md) i [działania](data-factory/concepts-pipelines-activities.md) zawartych w [fabryki danych Azure](https://azure.microsoft.com/documentation/services/data-factory/). Poniższy rysunek jest widok diagramu rozwiązania:

![](media/cortana-analytics-technical-guide-demand-forecast/ADF2.png)

Zawiera pięć potoki tej fabryki [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skrypty, które są używane na partycje i agregacji danych. Jeśli podano, skryptów znajdują się w [usługi Azure Storage](https://azure.microsoft.com/services/storage/) konto utworzone podczas instalacji. Jest ich lokalizacji: demandforecasting\\\\skryptu\\\\hive\\ \\ (lub https://[Your name].blob.core.windows.net/demandforecasting rozwiązania).

Podobnie jak [Azure Stream Analytics](#azure-stream-analytics-1) zapytań, [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skryptów ma niejawne informacji na temat formatu danych przychodzących, te zapytania musi zostać zmienione w zależności od formatu danych i [Inżynieria](machine-learning/team-data-science-process/create-features.md) wymagania.

#### <a name="aggregatedemanddatato1hrpipeline"></a>*AggregateDemandDataTo1HrPipeline*
To [potoku](data-factory/concepts-pipelines-activities.md) zawiera pojedyncze działanie - [HDInsightHive](data-factory/transform-data-using-hadoop-hive.md) działania przy użyciu [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) , na którym działa [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skryptu w celu agregacji żądanie przesyłanej strumieniowo w danych co 10 sekund w poziomie podstacji co godzinę poziom region i umieść w [usługi Azure Storage](https://azure.microsoft.com/services/storage/) przez zadanie usługi analiza strumienia Azure.

[Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skryptów jest to zadanie partycjonowania ***AggregateDemandRegion1Hr.hql***

#### <a name="loadhistorydemanddatapipeline"></a>*LoadHistoryDemandDataPipeline*
To [potoku](data-factory/concepts-pipelines-activities.md) zawiera dwa działania:

* [HDInsightHive](data-factory/transform-data-using-hadoop-hive.md) działania przy użyciu [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) , na którym działają skryptu Hive agregacji godzinowej danych żądanie historii w poziomie podstacji co godzinę poziom region i wprowadzane w usłudze Azure Storage w strumieniu Azure Zadania usługi analiza
* [Kopiuj](https://msdn.microsoft.com/library/azure/dn835035.aspx) działanie, które przenosi dane zagregowane z obiektu blob magazynu Azure do bazy danych SQL Azure udostępniony jako część instalacji szablon rozwiązania.

[Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skryptów dla tego zadania jest ***AggregateDemandHistoryRegion.hql***.

#### <a name="mlscoringregionxpipeline"></a>*MLScoringRegionXPipeline*
Te [potoki](data-factory/concepts-pipelines-activities.md) zawiera kilka działań i którego wynik końcowy jest scored predykcje uzyskiwane z eksperymentu uczenia maszynowego Azure skojarzone z tym szablonem rozwiązania. Są one niemal identyczne, z wyjątkiem każdego z nich obsługuje tylko inny region jest wykonywana przez inną RegionID przekazywany w potoku ADF i skryptu hive dla każdego regionu.  
Dostępne są następujące działania zawarte w tym potoku:

* [HDInsightHive](data-factory/transform-data-using-hadoop-hive.md) działania przy użyciu [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) , na którym działają skryptu Hive, aby wykonać agregacji i inżynieria niezbędne do eksperymentu uczenia maszynowego Azure. Skrypty gałęzi dla tego zadania są odpowiednich ***PrepareMLInputRegionX.hql***.
* [Kopia](https://msdn.microsoft.com/library/azure/dn835035.aspx) działanie, które przenosi wyników z [HDInsightHive](data-factory/transform-data-using-hadoop-hive.md) do pojedynczego obiektu blob usługi Azure Storage, które mogą być dostępu przez działania [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) działania.
* [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) działania, która wywołuje eksperymentu uczenia maszynowego Azure, co powoduje wyniki są umieszczone w jednym obiekcie blob magazynu Azure.

#### <a name="copyscoredresultregionxpipeline"></a>*CopyScoredResultRegionXPipeline*
To [potoku](data-factory/concepts-pipelines-activities.md) zawiera pojedyncze działanie - [kopiowania](https://msdn.microsoft.com/library/azure/dn835035.aspx) działanie, które przenosi wyniki eksperymentu uczenia maszynowego Azure z odpowiednich ***MLScoringRegionXPipeline***do bazy danych SQL Azure udostępniony jako część instalacji szablon rozwiązania.

#### <a name="copyaggdemandpipeline"></a>*CopyAggDemandPipeline*
To [potoku](data-factory/concepts-pipelines-activities.md) zawiera pojedyncze działanie - [kopiowania](https://msdn.microsoft.com/library/azure/dn835035.aspx) działanie, które przenosi dane zagregowane bieżące żądanie z ***LoadHistoryDemandDataPipeline*** do bazy danych SQL Azure Baza danych udostępniony jako część instalacji szablon rozwiązania.

#### <a name="copyregiondatapipeline-copysubstationdatapipeline-copytopologydatapipeline"></a>*CopyRegionDataPipeline, CopySubstationDataPipeline, CopyTopologyDataPipeline*
To [potoku](data-factory/concepts-pipelines-activities.md) zawiera pojedyncze działanie - [kopiowania](https://msdn.microsoft.com/library/azure/dn835035.aspx) działanie, które przenosi dane referencyjne Region/podstacji/Topologygeo, które są przekazywane do obiektu blob magazynu Azure w ramach szablon rozwiązania Instalacja z bazą danych SQL Azure, udostępniony jako część instalacji szablon rozwiązania.

### <a name="azure-machine-learning"></a>Azure Machine Learning
[Usługi Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) eksperymentu używane dla ten szablon rozwiązania umożliwia prognozowanie żądanie regionu. Eksperyment specyficzne dla zestawu danych używane i dlatego wymaga modyfikacji lub zastąpienia specyficzne dla danych, które są.

## <a name="monitor-progress"></a>**Monitoruj postęp**
Po uruchomieniu Generator danych, potoku rozpocznie uzyskiwanie uwodniony i różne składniki rozwiązania start zasób do następujących akcji polecenia wykonane przez fabryki danych. Istnieją dwa sposoby monitorowania potoku.

1. Sprawdzenie, czy dane z magazynu obiektów Blob Azure.

    Jeden z zadania usługi analiza strumienia zapisuje nieprzetworzone dane przychodzące do magazynu obiektów blob. Po kliknięciu **magazyn obiektów Blob Azure** składnika rozwiązania z ekranu pomyślnie wdrożone rozwiązanie, a następnie kliknij przycisk **Otwórz** na prawym panelu, spowoduje to przejście do [Azure Portal](https://portal.azure.com). Wyświetlonym edytorze kliknij na **obiekty BLOB**. W panelu dalej możesz wyświetlić listę kontenerów. Polecenie **"energysadata"**. W następnym panelu, zobacz **"demandongoing"** folderu. W folderze rawdata Zobacz folderów przy użyciu nazwy, takie jak Data = 2016-01-28 itp. Jeśli widzisz tych folderów wskazuje pomyślnie jest nieprzetworzone dane są generowane na komputerze i przechowywane w magazynie obiektów blob. Powinny pojawić się pliki, które powinny mieć ograniczone rozmiary MB w tych folderach.
2. Sprawdzenie, czy dane z bazy danych SQL Azure.

    Ostatnim krokiem potoku jest zapis danych (na przykład predykcje uzyskiwane z uczenia maszynowego) do bazy danych SQL. Może być konieczne poczekaj maksymalnie dwie godziny, aby te dane są wyświetlane w bazie danych SQL. Jednym ze sposobów monitorowania, jak dużo danych jest dostępne w bazie danych SQL jest za pośrednictwem [portalu Azure](https://portal.azure.com/). W lewym panelu, zlokalizuj baz danych SQL![](media/cortana-analytics-technical-guide-demand-forecast/SQLicon2.png) i kliknij ją. Następnie odszukaj bazy danych (tj. demo123456db) i kliknij go. Na następnej stronie w obszarze **"Połącz z bazą danych"** kliknij **"Uruchom języka Transact-SQL zapytań dotyczących bazy danych SQL"**.

    W tym miejscu możesz kliknąć opcję Nowa kwerenda i zapytanie o liczbę wierszy (na przykład "select count(*) z DemandRealHourly)" wraz z rozwojem bazy danych, liczba wierszy w tabeli należy zwiększyć.)
3. Sprawdzenie, czy dane z pulpitu nawigacyjnego usługi Power BI.

    Pulpit nawigacyjny aktywnej ścieżki usługi Power BI można skonfigurować do monitorowania nieprzetworzone dane przychodzące. Postępuj zgodnie z instrukcjami w sekcji "Power BI pulpitu nawigacyjnego".

## <a name="power-bi-dashboard"></a>**Pulpit nawigacyjny programu Power BI**
### <a name="overview"></a>Przegląd
W tej sekcji opisano, jak skonfigurować pulpit nawigacyjny usługi Power BI, aby wizualizować dane w czasie rzeczywistym z usługi Azure stream analytics (ścieżka gorących), a także do przewidywania wyników z Azure machine learning (ścieżka zimnych).

### <a name="setup-hot-path-dashboard"></a>Pulpit nawigacyjny aktywnej ścieżki Instalatora
Poniższe kroki ułatwiają jak do wizualizacji danych w czasie rzeczywistym danych wyjściowych z zadania usługi analiza strumienia, które zostały wygenerowane w czasie wdrażania rozwiązania. A [online usługi Power BI](http://www.powerbi.com/) konta jest wymagany do wykonania następujących kroków. Jeśli nie masz konta, możesz [utworzyć](https://powerbi.microsoft.com/pricing).

1. Dodawanie danych wyjściowych usługi Power BI w Azure Stream Analytics (ASA).

   * Musisz postępować zgodnie z instrukcjami wyświetlanymi w [Azure Stream Analytics & usługi Power BI: pulpitu nawigacyjnego analytics w czasie rzeczywistym uzyskać wgląd w czasie rzeczywistym przesyłać strumieniowo danych](stream-analytics/stream-analytics-power-bi-dashboard.md) skonfigurować dane wyjściowe zadania usługi analiza strumienia Azure jako pulpit nawigacyjny usługi Power BI .
   * Znajdź zadania usługi analiza strumienia w Twojej [portalu Azure](https://portal.azure.com). Nazwa zadania powinna być: YourSolutionName + streamingjob"" + losowe numer + "asapbi" (tj. demostreamingjob123456asapbi).
   * Dodawanie danych wyjściowych zadania ASA usługi Power BI. Ustaw **Output Alias** jako **"PBIoutput"**. Ustaw użytkownika **nazwę zestawu danych** i **Nazwa tabeli** jako **"EnergyStreamData"**. Po dodaniu dane wyjściowe, kliknij przycisk **"Start"** w dolnej części strony, aby uruchomić zadanie usługi Stream Analytics. Należy uzyskać potwierdzenia (na przykład "Uruchamianie zadania stream analytics zakończyło się pomyślnie myteststreamingjob12345asablob").
2. Zaloguj się do [online usługi Power BI](http://www.powerbi.com)

   * W lewym panelu sekcji zestawów danych w obszarze Mój obszar roboczy, można wyświetlić nowy zestaw danych, przedstawiający na lewym panelu usługi Power BI. Jest to dane przesyłane strumieniowo, które wypychana z usługi Azure Stream Analytics w poprzednim kroku.
   * Upewnij się, że ***wizualizacje*** okienko jest otwarty i jest wyświetlany w prawej części ekranu.
3. Utwórz kafelka "Żądanie przez sygnatura czasowa":

   * Kliknij zestaw danych **"EnergyStreamData"** w lewym panelu sekcji zestawów danych.
   * Kliknij przycisk **"Wykres liniowy"** ikona ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic8.png).
   * Kliknij przycisk "EnergyStreamData" w **pola** panelu.
   * Kliknij przycisk **"Timestamp"** i upewnij się, że będzie wyświetlana w obszarze "Osi". Kliknij przycisk **"Obciążenia"** i upewnij się, że będzie wyświetlana w obszarze "Wartości".
   * Kliknij przycisk **ZAPISAĆ** u góry i nazwę raportu, ponieważ "EnergyStreamDataReport". Raport o nazwie "EnergyStreamDataReport" jest wyświetlany w sekcji raportów w okienku Nawigator po lewej stronie.
   * Kliknij przycisk **"Numer Pin Visual"** ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic6.png) ikonę w prawym górnym rogu ten wykres liniowy, okno "Numer Pin do pulpitu nawigacyjnego" wyświetlani może można wybrać pulpitu nawigacyjnego. Wybierz "EnergyStreamDataReport", a następnie kliknij przycisk "Numer Pin".
   * Umieść kursor myszy nad tego kafelka na pulpicie nawigacyjnym kliknij "Edytuj" ikonę w prawym górnym rogu zmienić jego tytuł jako "Żądanie przez sygnatury czasowej"
4. Utwórz innych kafelka pulpitu nawigacyjnego oparte na odpowiednich zestawów danych. Widok pulpitu nawigacyjnego końcowego: ![](media/cortana-analytics-technical-guide-demand-forecast/PBIFullScreen.png)

### <a name="setup-cold-path-dashboard"></a>Pulpit nawigacyjny zimnych ścieżki Instalatora
Ścieżka zimnych danych potoku niezbędne celem jest uzyskanie prognozy każdego regionu. Usługi Power BI łączy się z bazą danych Azure SQL jako źródła danych, w którym są przechowywane wyniki prognozowania.

> [!NOTE]
> 1) Może potrwać kilka godzin do zbierania wyników wystarczająco prognozy pulpitu nawigacyjnego. Zaleca się rozpocząć ten proces 2 do 3 godzin po biedzie generator danych. 2) w tym kroku wstępnie wymagane jest pobranie i zainstalowanie bezpłatnego oprogramowania [Power BI desktop](https://powerbi.microsoft.com/desktop).
>
>

1. Pobierz poświadczenia bazy danych.

   Należy **bazy danych, nazwę serwera, nazwa bazy danych, nazwę użytkownika i hasło** przed przejściem do następnej czynności. Poniżej przedstawiono kroki prowadzące jak je znaleźć.

   * Raz **"Azure SQL Database"** w szablonie rozwiązania włącza zielony diagramu, kliknij go, a następnie kliknij przycisk **"Otwórz"**. Jest kierowane do portalu Azure, a także zostanie otwarta strona informacji z bazy danych.
   * Na stronie można znaleźć sekcji "Baza danych". Znajdują się bazy danych, które zostały utworzone. Nazwa bazy danych powinna być **"Z nazwą rozwiązania liczbę losową +"db""** (na przykład "mytest12345db").
   * Bazy danych, kliknij nazwę serwera bazy danych w nowych pop limit panelu, można znaleźć u góry. Nazwa serwera bazy danych powinna być `"Your Solution Name + Random Number + 'database.windows.net,1433'"` (na przykład "mytest12345.database.windows.net,1433").
   * Baza danych **username** i **hasło** jest taka sama jak nazwa użytkownika i hasło uprzednio zarejestrowane podczas wdrażania rozwiązania.
2. Aktualizacja ze źródłem danych zimnych ścieżki pliku usługi Power BI

   * Upewnij się, że zainstalowano najnowszą wersję [Power BI desktop](https://powerbi.microsoft.com/desktop).
   * W **"DemandForecastingDataGeneratorv1.0"** pobrać folder, kliknij dwukrotnie **"Power BI Template\DemandForecastPowerBI.pbix"** pliku. Wizualizacje początkowej są oparte na danych zastępczego. **Uwaga:** Jeśli zostanie wyświetlony komunikat o błędzie, upewnij się, że zainstalowano najnowszą wersję programu Power BI Desktop.

     Po otwarciu górnej części pliku, kliknij przycisk **"Edytuj zapytania"**. W lokalizacji pop w oknie kliknij dwukrotnie **'Source'** na prawym panelu.
     ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic1.png)
   * W lokalizacji pop okna, Zastąp **"Server"** i **"Baza danych"** z własne nazwy serwera i bazy danych, a następnie kliknij przycisk **"OK"**. Dla nazwy serwera, upewnij się, że możesz określić port 1433 (**YourSolutionName.database.windows.net, 1433**). Ignoruj ostrzeżenia, które są wyświetlane na ekranie.
   * W następnym pop okna, zobaczysz dwie opcje w okienku po lewej stronie (**Windows** i **bazy danych**). Kliknij przycisk **"Baza danych"**, wypełnij Twojej **"Nazwa_użytkownika"** i **"Password"** (jest to nazwa użytkownika i hasła podczas najpierw wdrożyć rozwiązanie i utworzyć bazę danych Azure SQL). W ***wybierz poziom Aby zastosować te ustawienia do***, zaznacz opcję poziomu bazy danych. Następnie kliknij przycisk **"Połącz"**.
   * Gdy są z przewodnikiem wróć do poprzedniej strony, zamknij okno. POP komunikatów wychodzących — kliknij **Zastosuj**. Na koniec kliknij **zapisać** przycisk, aby zapisać zmiany. Plik usługi Power BI ma teraz nawiązać połączenia z serwerem. Jeśli Twoje wizualizacje są puste, upewnij się, że wyczyść zaznaczenia na wizualizacje wizualizować wszystkie dane, klikając ikonę gumki w prawym górnym rogu legendy. Użyj przycisku Odśwież, aby odzwierciedlić nowe dane na wizualizacjach. Początkowo widoczne są tylko dane inicjatora w sieci wizualizacji zgodnie z fabryki danych jest zaplanowane odświeżanie co 3 godziny. Po 3 godziny zobaczysz nowy prognoz, które zostaną uwzględnione w sieci wizualizacji podczas odświeżania danych.
3. (Opcjonalnie) Publikowanie pulpitu zimnych ścieżki [online usługi Power BI](http://www.powerbi.com/). Należy pamiętać, że ten krok wymaga konta usługi Power BI (lub konta usługi Office 365).

   * Kliknij przycisk **"Publikuj"** i później kilku sekund zostanie wyświetlone okno "Publikowania Power BI sukcesu!" z zielonym znacznikiem wyboru. Kliknij poniższe łącze "Demoprediction.pbix Otwórz w usłudze Power BI". Aby uzyskać szczegółowe instrukcje, zobacz [publikowania z Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).
   * Aby utworzyć nowy pulpit nawigacyjny: kliknij **+** dalej, aby zarejestrować **pulpity nawigacyjne** sekcji w okienku po lewej stronie. Wprowadź nazwę "Żądanie prognozowania pokaz" dla tego nowego pulpitu nawigacyjnego.
   * Po otwarciu raportu, kliknij przycisk ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic6.png) Aby przypiąć wszystkie wizualizacje do pulpitu nawigacyjnego. Aby uzyskać szczegółowe instrukcje, zobacz [przypiąć Kafelek do pulpitu nawigacyjnego usługi Power BI z raportu](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report).
     Przejdź do strony pulpitu nawigacyjnego i Dostosuj rozmiar i położenie sieci wizualizacji i Edytuj ich tytułów. Aby uzyskać szczegółowe instrukcje na temat edytowania swoje Kafelki, zobacz [edycji kafelka — zmiany rozmiaru, Przenieś, Zmień nazwę, numer pin, usuwanie, Dodawanie hiperłącza](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Oto pulpitu nawigacyjnego przykład z niektórych wizualizacjami zimnych ścieżki przypięty do niego.

     ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic7.png)
4. (Opcjonalnie) Harmonogram odświeżania źródła danych.

   * Aby zaplanować odświeżanie danych, umieść kursor myszy nad **końcowa EnergyBPI** zestawu danych, kliknij przycisk ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic3.png) , a następnie wybierz **planowanie odświeżania**.
     **Uwaga:** Jeśli widzisz Masaż ostrzeżenie, kliknij przycisk **edytowanie poświadczeń** i upewnij się, że Twoje poświadczenia bazy danych są takie same jak opisany w kroku 1.

     ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic4.png)
   * Rozwiń węzeł **planowanie odświeżania** sekcji. Włącz funkcję "zachować aktualność danych".
   * Planowanie odświeżania na podstawie Twoich potrzeb. Aby uzyskać więcej informacji, zobacz [odświeżania danych w usłudze Power BI](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/).

## <a name="how-to-delete-your-solution"></a>**Jak usunąć rozwiązania**
Upewnij się, Zatrzymaj generator danych, gdy nie są aktywnie za pomocą rozwiązania uruchamiania generatora danych ponosi wyższe koszty. Usuń rozwiązanie, jeśli nie jest używana. Usunięcie rozwiązania spowoduje usunięcie wszystkich składników, które są udostępniane w ramach Twojej subskrypcji po wdrożeniu rozwiązania. Aby usunąć rozwiązania kliknij swoją nazwę rozwiązania, w lewym panelu szablon rozwiązania i kliknij przycisk Usuń.

## <a name="cost-estimation-tools"></a>**Narzędzia do szacowania kosztów**
Następujące dwa narzędzia są dostępne lepiej zrozumieć całkowity koszt objętego uruchomiona prognozowania żądanie szablon rozwiązania energii w ramach subskrypcji:

* [Microsoft Azure koszt narzędzia do szacowania Tool (online)](https://azure.microsoft.com/pricing/calculator/)
* [Microsoft Azure koszt narzędzia do szacowania Tool (desktop)](http://www.microsoft.com/download/details.aspx?id=43376)

## <a name="acknowledgements"></a>**Potwierdzeń**
W tym artykule jest przypisany przez naukowca danych Yijing Chen i oprogramowania inżyniera min. Qiu firmy Microsoft.
