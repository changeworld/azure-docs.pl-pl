---
title: Prognozy w podręczniku technicznym energii
description: Podręcznik techniczny do szablonu rozwiązania za pomocą pakietu Microsoft Cortana Intelligence uzyskać prognozę energii.
services: machine-learning
author: garyericson
manager: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/16/2016
ms.author: garye
ms.openlocfilehash: 6b80e73dec7d0e03823a8aa2867ee91bfb68f560
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58893643"
---
# <a name="technical-guide-to-the-cortana-intelligence-solution-template-for-demand-forecast-in-energy"></a>Podręcznik techniczny do szablon rozwiązania Cortana Intelligence, dla prognozę energii
## **<a name="overview"></a>Przegląd**
Szablony rozwiązań są przeznaczone do przyspieszenia procesu tworzenia pokaz E2E na podstawie pakietu Cortana Intelligence. Szablon wdrożonej aprowizuje subskrypcji za pomocą niezbędnych składników pakietu Cortana Intelligence i utworzyć relacje między. Inicjowania on również inicjuje potok danych z przykładowymi danymi generowanych z poziomu aplikacji symulacji dane. Pobierz symulator danych z linku podanego i zainstaluj go na komputerze lokalnym, zobacz plik readme.txt na potrzeby instrukcji na temat korzystania z symulatora. Danych generowanych przez symulator hydrates potoku danych i rozpoczęcia generowania prognoz uczenia maszynowego, które mogą być następnie wizualizowane na pulpicie nawigacyjnym usługi Power BI.

Szablon rozwiązania można znaleźć [tutaj](https://gallery.cortanaintelligence.com/SolutionTemplate/Demand-Forecasting-for-Energy-1)

Proces wdrażania przeprowadzi Cię przez kilka kroków, aby skonfigurować poświadczenia rozwiązania. Upewnij się, że rejestrowanie tych poświadczeń, takich jak nazwa rozwiązania, nazwę użytkownika i hasło podane podczas wdrażania.

Celem tego dokumentu jest opisano architekturę referencyjną i różnych składników aprowizowane w Twojej subskrypcji w ramach tego szablonu rozwiązania. Dokument również opowiada, jak zastąpić dane przykładowe, rzeczywiste dane samodzielnie, aby można było zobaczyć insights/prognoz, w przypadku danych. Ponadto dokument opowiada o części szablonu rozwiązania, który musi być modyfikowane, jeśli chcesz dostosować rozwiązanie z użyciem własnych danych. Instrukcje dotyczące sposobu tworzenia pulpitu nawigacyjnego usługi Power BI dla tego szablonu rozwiązania znajdują się na końcu.

## **<a name="details"></a>Szczegóły**
![](media/cortana-analytics-technical-guide-demand-forecast/ca-topologies-energy-forecasting.png)

### <a name="architecture-explained"></a>Architektura wyjaśniono
Po wdrożeniu rozwiązania są aktywowane różne usługi platformy Azure w ramach pakietu Cortana Analytics (oznacza to, że Centrum zdarzeń usługi Stream Analytics, HDInsight, Data Factory, usługi Machine Learning *itp.*). Diagram architektury pokazuje, na wysokim poziomie, jak prognozowania zapotrzebowania na energię szablon rozwiązania jest zbudowany z end-to-end. Można zbadać te usługi, klikając je na diagram szablonu rozwiązania utworzone przy użyciu wdrażania rozwiązania. W poniższych sekcjach opisano każdego z nich.

## **<a name="data-source-and-ingestion"></a>Źródło danych i wprowadzania**
### <a name="synthetic-data-source"></a>Źródło danych syntetycznego
W przypadku tego szablonu użyte źródło danych jest generowany na podstawie aplikacji pulpitu, Pobierz i uruchom lokalnie, po pomyślnym wdrożeniu. Dowiedz się z instrukcjami, aby pobrać i zainstalować tę aplikację na pasku właściwości po wybraniu pierwszego węzła o nazwie symulator danych prognozowania energii na diagramie szablonu rozwiązania. Źródła danych w tej aplikacji [usługi Azure Event Hub](#azure-event-hub) usługi za pomocą punktów danych lub zdarzenia, które są używane w pozostałej części przepływu rozwiązania.

Aplikacja generowanie zdarzeń wypełnia usługi Azure Event Hub tylko wtedy, gdy jest wykonywany na komputerze.

### <a name="azure-event-hub"></a>Centrum zdarzeń Azure
[Usługi Azure Event Hub](https://azure.microsoft.com/services/event-hubs/) service to odbiorca danych wejściowych dostarczonych przez opisane syntetyczne źródło danych.

## **<a name="data-preparation-and-analysis"></a>Przygotowywanie danych i analiza**
### <a name="azure-stream-analytics"></a>Usługa Azure Stream Analytics
[Usługi Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) usługa jest używana do dostarczania wyników analizy w czasie rzeczywistym dotyczących strumienia wejściowego z prawie [usługi Azure Event Hub](#azure-event-hub) usługi i publikuje wyniki na [usługi Power BI](https://powerbi.microsoft.com)pulpitu nawigacyjnego, a także wszystkie nieprzetworzone zdarzenia przychodzące do archiwizacji [usługi Azure Storage](https://azure.microsoft.com/services/storage/) usługi do późniejszego przetwarzania przez [usługi Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) usługi.

### <a name="hdinsight-custom-aggregation"></a>HDInsight Custom agregacji
Usługa Azure HDInsight jest używana do uruchamiania [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skryptów (zarządzanych przez usługę Azure Data Factory) w celu zapewnienia agregacji nieprzetworzonych zdarzeń zarchiwizowanych przy użyciu usługi Azure Stream Analytics.

### <a name="azure-machine-learning"></a>Azure Machine Learning
[Usługi Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) (zarządzanych przez usługę Azure Data Factory), używana jest usługa do prognozowania na przyszłe zużycie z danym regionie na podstawie otrzymanych danych wejściowych.

## **<a name="data-publishing"></a>Publikowanie danych**
### <a name="azure-sql-database-service"></a>Usługa bazy danych Azure SQL
[Usługi Azure SQL Database](https://azure.microsoft.com/services/sql-database/) usługa jest używana do przechowywania (zarządzane przez usługę Azure Data Factory) prognoz otrzymanych przez usługę Azure Machine Learning, która jest używany w [usługi Power BI](https://powerbi.microsoft.com) pulpitu nawigacyjnego.

## **<a name="data-consumption"></a>Użycie danych**
### <a name="power-bi"></a>Power BI
[Usługi Power BI](https://powerbi.microsoft.com) usługa służy do wyświetlenia pulpitu nawigacyjnego, który zawiera agregacji, dostarczone przez [usługi Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) usługi, a także żądanie Prognozowanie wyników przechowywanych w [Azure SQL Baza danych](https://azure.microsoft.com/services/sql-database/) , zostały utworzone przy użyciu [usługi Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) usługi. Instrukcje dotyczące sposobu tworzenia pulpitu nawigacyjnego usługi Power BI, ten szablon rozwiązania można znaleźć w poniższej sekcji.

## **<a name="how-to-bring-in-your-own-data"></a>Jak przenieść w swoich danych**
W tej sekcji opisano, jak możesz używać własnych danych na platformie Azure i jakie obszary wymagałyby zmiany danych, które wprowadzasz w tej architekturze.

Jest mało prawdopodobne, że dowolnym zestawie danych, których można będzie pasował zbioru danych używanego dla tego szablonu rozwiązania. Opis danych i wymagania są niezwykle istotne, w jak zmodyfikować ten szablon służy do pracy z własnych danych. Jeśli zaczynasz do usługi Azure Machine Learning, wprowadzenie do niego można uzyskać przy użyciu przykładu w [jak utworzyć swój pierwszy eksperyment z zakresu](machine-learning/studio/create-experiment.md).

W poniższych sekcjach omówiono części szablonu, który wymaga modyfikacji, gdy wprowadzono nowy zestaw danych.

### <a name="azure-event-hub"></a>Centrum zdarzeń Azure
[Usługi Azure Event Hub](https://azure.microsoft.com/services/event-hubs/) usługa jest ogólny, w taki sposób, że dane mogą być przesyłane do koncentratora, w formacie CSV lub JSON. Brak specjalnego przetwarzania odbywa się w Centrum zdarzeń Azure, ale ważne jest, aby zrozumieć dane, które są przekazywane do jej.

W tym dokumencie nie opisano sposobu pozyskiwania danych, ale jeden umożliwia łatwe wysyłanie zdarzenia lub dane do usługi Azure Event Hub przy użyciu [API Centrum zdarzeń](event-hubs/event-hubs-programming-guide.md).

### <a name="azure-stream-analytics"></a>Usługa Azure Stream Analytics
[Usługi Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) usługa jest używana do dostarczania wyników analizy w czasie rzeczywistym prawie odczytywanie ze strumieni danych i wyprowadzanie danych na dowolną liczbę źródeł.

Do prognozowania zapotrzebowania na energię szablon rozwiązania zapytania usługi Azure Stream Analytics składa się z dwóch podzapytań, każdy wykorzystywanie zdarzeń z usługi Azure Event Hub jako dane wejściowe i konieczności dane wyjściowe z dwóch różnych lokalizacjach. Te dane wyjściowe składają się z jednego zestawu danych usługi Power BI i jednej lokalizacji magazynu platformy Azure.

[Usługi Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) zapytania można znaleźć przez:

* Logowanie do [witryny Azure portal](https://portal.azure.com/)
* Lokalizowanie zadania usługi stream analytics ![](media/cortana-analytics-technical-guide-demand-forecast/icon-stream-analytics.png) które zostały wygenerowane, gdy rozwiązanie zostało wdrożone. Jeden jest przeznaczony dla wypychanie danych do magazynu obiektów blob (na przykład mytest1streaming432822asablob) i jeden z nich ma wypychanie danych do usługi Power BI (na przykład mytest1streaming432822asapbi).
* Zaznaczenie

  * ***Dane wejściowe*** Aby wyświetlić zapytanie wejściowe
  * ***ZAPYTANIE*** do wyświetlania samo zapytanie
  * ***Dane wyjściowe*** do wyświetlania różnych danych wyjściowych

Informacje o budowa zapytania usługi Azure Stream Analytics można znaleźć w [dokumentacja zapytań usługi Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx) w witrynie MSDN.

W tym rozwiązaniu zadania usługi Azure Stream Analytics, która generuje zestaw danych o niemal analizy w czasie rzeczywistym informacje o przychodzącego strumienia danych do pulpitu nawigacyjnego usługi Power BI jest dostarczany jako część tego szablonu rozwiązania. Ponieważ istnieje niejawna wiedzę na temat formatu danych przychodzących, te zapytania musi zostać zmienione zależnie od formatu danych.

Inne zadanie usługi Azure Stream Analytics wysyła wszystkie [Centrum zdarzeń](https://azure.microsoft.com/services/event-hubs/) zdarzenia [usługi Azure Storage](https://azure.microsoft.com/services/storage/) i dlatego wymaga nie zmian niezależnie od tego, formatu danych, ponieważ informacje pełne zdarzenie jest przesyłany strumieniowo do usługi storage.

### <a name="azure-data-factory"></a>Azure Data Factory
[Usługi Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) usługi służy do aranżacji przenoszenia i przetwarzania danych. W prognozowania zapotrzebowania na energię szablon rozwiązania usługi data factory składa się z 12 [potoki](data-factory/concepts-pipelines-activities.md) , przenoszenie i przetwarzanie danych za pomocą różnych technologii.

  Możesz uzyskać dostęp fabryki danych, otwierając węzła usługi Data Factory w dolnej części na diagramie szablonu rozwiązania utworzone przy użyciu wdrażania rozwiązania. Zostanie wyświetlona data factory w witrynie Azure portal. Zostaną wyświetlone błędy w ramach zestawów danych, możesz zignorować te są one ze względu na wdrażane, zanim został uruchomiony generator danych usługi fabryka danych. Te błędy nie uniemożliwiają działanie fabryki danych.

W tej sekcji omówiono niezbędne [potoki](data-factory/concepts-pipelines-activities.md) i [działania](data-factory/concepts-pipelines-activities.md) zawarte w [usługi Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/). Poniższy rysunek jest widok diagramu rozwiązania:

![](media/cortana-analytics-technical-guide-demand-forecast/ADF2.png)

Zawiera pięć potoków tej fabryki [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skrypty, które są używane do partycji i agregacji danych. Jeśli zaznaczono inaczej, skryptów znajdują się w [usługi Azure Storage](https://azure.microsoft.com/services/storage/) konto utworzone podczas instalacji. Ich lokalizacja to: demandforecasting\\\\skryptu\\\\hive\\ \\ (lub name].blob.core.windows.net/demandforecasting rozwiązania https://[Your).

Podobnie jak [usługi Azure Stream Analytics](#azure-stream-analytics-1) zapytań, [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) aby skrypty miały niejawne wiedzę na temat formatu danych przychodzących, te zapytania należałoby można zmienić na format danych i podstawie[Inżynieria funkcji](machine-learning/team-data-science-process/create-features.md) wymagania.

#### *<a name="aggregatedemanddatato1hrpipeline"></a>AggregateDemandDataTo1HrPipeline*
To [potoku](data-factory/concepts-pipelines-activities.md) zawiera jedno działanie — [HDInsightHive](data-factory/transform-data-using-hadoop-hive.md) czynności za pomocą [HDInsightLinkedService](/previous-versions/azure/dn893526(v=azure.100)) , które jest uruchamiane [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skrypt żądanie przesyłane strumieniowo w agregacji danych co 10 sekund na poziomie Podstacja co godzinę na poziomie regionu i umieścić w [usługi Azure Storage](https://azure.microsoft.com/services/storage/) za pomocą zadania usługi Azure Stream Analytics.

[Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skryptu dla tego zadania partycjonowania jest ***AggregateDemandRegion1Hr.hql***

#### *<a name="loadhistorydemanddatapipeline"></a>LoadHistoryDemandDataPipeline*
To [potoku](data-factory/concepts-pipelines-activities.md) zawiera dwa działania:

* [HDInsightHive](data-factory/transform-data-using-hadoop-hive.md) czynności za pomocą [HDInsightLinkedService](/previous-versions/azure/dn893526(v=azure.100)) uruchamiające skrypt Hive w celu agregowania godzinowe dane żądanie historii w poziomie Podstacja co godzinę na poziomie regionu i umieścić w usłudze Azure Storage w usłudze Azure Stream Zadanie usługi Analytics
* [Kopiuj](/previous-versions/azure/dn835035(v=azure.100)) działanie, które przenosi dane zagregowane z rozszerzenia Azure Storage blob do usługi Azure SQL Database, które zostało zaaprowizowane w ramach instalacji szablonu rozwiązania.

[Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skryptu dla tego zadania jest ***AggregateDemandHistoryRegion.hql***.

#### *<a name="mlscoringregionxpipeline"></a>MLScoringRegionXPipeline*
Te [potoki](data-factory/concepts-pipelines-activities.md) zawiera kilka działań, a wynik końcowy ocenami przewidywań eksperymentu usługi Azure Machine Learning, skojarzone z tym szablonie rozwiązania. Są niemal identyczne, z wyjątkiem każdej z nich obsługuje tylko innym regionie, która jest wykonywana przez różne RegionID przekazywany w potoku usługi ADF i skrypt programu hive dla każdego regionu.  
Działania zawarte w tym potoku są:

* [HDInsightHive](data-factory/transform-data-using-hadoop-hive.md) czynności za pomocą [HDInsightLinkedService](/previous-versions/azure/dn893526(v=azure.100)) uruchamiające skrypt Hive w celu wykonywania agregacji i inżynieria niezbędne do eksperymentu usługi Azure Machine Learning. Skrypty Hive dla tego zadania są odpowiednie ***PrepareMLInputRegionX.hql***.
* [Kopiuj](/previous-versions/azure/dn835035(v=azure.100)) działania, który przenosi wyniki z [HDInsightHive](data-factory/transform-data-using-hadoop-hive.md) działanie z pojedynczym obiektem blob usługi Azure Storage, które mogą być prawa dostępu wszystkim [AzureMLBatchScoring](/previous-versions/azure/dn894009(v=azure.100)) działania.
* [AzureMLBatchScoring](/previous-versions/azure/dn894009(v=azure.100)) działania, który wywołuje eksperymentu usługi Azure Machine Learning, co skutkuje wyniki umieszczeniem w jeden obiekt blob usługi Azure Storage.

#### *<a name="copyscoredresultregionxpipeline"></a>CopyScoredResultRegionXPipeline*
To [potoku](data-factory/concepts-pipelines-activities.md) zawiera jedno działanie — [kopiowania](/previous-versions/azure/dn835035(v=azure.100)) działanie, które przenosi wynikiem eksperymentu uczenia maszynowego Azure z odpowiednimi ***MLScoringRegionXPipeline***do usługi Azure SQL Database, które zostało zaaprowizowane w ramach instalacji szablonu rozwiązania.

#### *<a name="copyaggdemandpipeline"></a>CopyAggDemandPipeline*
To [potoku](data-factory/concepts-pipelines-activities.md) zawiera jedno działanie — [kopiowania](/previous-versions/azure/dn835035(v=azure.100)) działanie, które przenosi dane zagregowane bieżące żądanie z ***LoadHistoryDemandDataPipeline*** do usługi Azure SQL Bazy danych, które zostało zaaprowizowane w ramach instalacji szablonu rozwiązania.

#### *<a name="copyregiondatapipeline-copysubstationdatapipeline-copytopologydatapipeline"></a>CopyRegionDataPipeline, CopySubstationDataPipeline, CopyTopologyDataPipeline*
To [potoku](data-factory/concepts-pipelines-activities.md) zawiera jedno działanie — [kopiowania](/previous-versions/azure/dn835035(v=azure.100)) działanie, które przenosi dane referencyjne regionu/Podstacja/Topologygeo, które są przekazywane do usługi Azure Storage blob jako część szablonu rozwiązania Instalacja do usługi Azure SQL Database, które zostało zaaprowizowane w ramach instalacji szablonu rozwiązania.

### <a name="azure-machine-learning"></a>Azure Machine Learning
[Usługi Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) eksperymentować używane dla tego szablonu rozwiązania umożliwia prognozowanie popytu na region. Eksperyment zależy od zestawu danych, używane i dlatego wymaga modyfikacji lub zastąpienia specyficzne dla danych, który zostanie przełączony w tryb w.

## **<a name="monitor-progress"></a>Monitoruj postęp**
Po uruchomieniu Generator danych, potoku rozpocznie pobieranie uwodniony i różnych składników rozwiązania Uruchom określonego w następujących akcji polecenia wydane przez usługę Data Factory. Istnieją dwa sposoby, które można monitorować potok.

1. Sprawdź dane z usługi Azure Blob Storage.

    Jedno z zadań usługi Stream Analytics zapisuje nieprzetworzone dane przychodzące do usługi blob storage. Po kliknięciu **usługi Azure Blob Storage** składnik rozwiązania z ekranu pomyślnie wdrożono rozwiązanie, a następnie kliknij przycisk **Otwórz** w prawym okienku, spowoduje to przejście do [platformy Azure Portal](https://portal.azure.com). Wyświetlonym obszarze kliknij na **obiektów blob**. W następnym panelu zobaczysz listę kontenerów. Kliknij pozycję **"energysadata"**. W następnym panelu zobaczysz **"demandongoing"** folderu. W folderze rawdata zobaczysz folderów przy użyciu nazwy, takie jak Data = 2016-01-28 itp. Jeśli widzisz te foldery, wskazuje że nieprzetworzone dane są pomyślnie są generowane na komputerze i przechowywane w magazynie obiektów blob. Powinieneś zobaczyć pliki, które powinny mieć skończony rozmiar w Megabajtach w tych folderach.
2. Sprawdź dane z usługi Azure SQL Database.

    Ostatnim krokiem w potoku jest zapis danych (na przykład przewidywań uczenia maszynowego) do bazy danych SQL. Trzeba będzie poczekać maksymalnie dwie godziny, dane są wyświetlane w usłudze SQL Database. Jednym ze sposobów, aby monitorować, ile danych jest dostępna w usłudze SQL Database jest za pośrednictwem [witryny Azure portal](https://portal.azure.com/). Na lewym panelu, Znajdź bazy danych SQL![](media/cortana-analytics-technical-guide-demand-forecast/SQLicon2.png) i kliknij ją. Następnie znajdź bazy danych (czyli demo123456db) i kliknij ją. Na następnej stronie w obszarze **"Połącz z bazą danych"** kliknij **"Run Transact-SQL zapytania względem bazy danych SQL"**.

    W tym miejscu możesz kliknąć nowe zapytanie i wykonywania zapytań o liczbę wierszy (na przykład "Wybierz count(*) z DemandRealHourly)" wraz ze wzrostem natężenia bazy danych, liczba wierszy w tabeli należy zwiększyć.)
3. Sprawdź dane z poziomu pulpitu nawigacyjnego usługi Power BI.

    Pulpit nawigacyjny ścieżki aktywnej usługi Power BI można skonfigurować do monitorowania nieprzetworzone dane przychodzące. Postępuj zgodnie instrukcjami w sekcji "Pulpit nawigacyjny usługi Power BI".

## **<a name="power-bi-dashboard"></a>Pulpit nawigacyjny usługi Power BI**
### <a name="overview"></a>Przegląd
W tej sekcji opisano sposób konfigurowania pulpit nawigacyjny usługi Power BI, aby wizualizować dane w czasie rzeczywistym z usługi Azure stream analytics (ścieżka aktywna), a także do przewidywania wyników z usługi Azure machine learning (ścieżka nieaktywna).

### <a name="setup-hot-path-dashboard"></a>Pulpit nawigacyjny ścieżki aktywnej konfiguracji
Poniższe kroki prowadzą, jak wizualizować dane w czasie rzeczywistym dane wyjściowe zadania usługi Stream Analytics, które zostały wygenerowane w czasie wdrażania rozwiązania. A [usługi Power BI online](https://www.powerbi.com/) można wykonać następujące czynności, wymagane jest konto. Jeśli nie masz konta, możesz to zrobić [utworzyć](https://powerbi.microsoft.com/pricing).

1. Dodaj dane wyjściowe usługi Power BI w usłudze Azure Stream Analytics (ASA).

   * Należy wykonać instrukcje w [usługi Azure Stream Analytics i Power BI: Pulpit nawigacyjny analizy w czasie rzeczywistym o wgląd w czasie rzeczywistym danych przesyłanych strumieniowo](stream-analytics/stream-analytics-power-bi-dashboard.md) skonfigurować dane wyjściowe zadania usługi Azure Stream Analytics jako pulpit nawigacyjny usługi Power BI.
   * Znajdź zadanie usługi stream analytics w swojej [witryny Azure portal](https://portal.azure.com). Nazwa zadania powinny być: YourSolutionName + streamingjob"" + losowe liczba + "asapbi" (czyli demostreamingjob123456asapbi).
   * Dodaj dane wyjściowe zadania ASA usługi Power BI. Ustaw **danych wyjściowych Alias** jako **"PBIoutput"**. Ustaw użytkownika **Nazwa zestawu danych** i **nazwy tabeli** jako **"EnergyStreamData"**. Po dodaniu danych wyjściowych kliknij **"Start"** w dolnej części strony Aby uruchomić zadanie usługi Stream Analytics. Powinna pojawić się komunikat z potwierdzeniem (na przykład "Uruchamianie zadania usługi stream analytics zakończyło się pomyślnie myteststreamingjob12345asablob").
2. Zaloguj się do [usługi Power BI w trybie online](https://www.powerbi.com)

   * W lewym panelu, sekcji zestawów danych w obszarze Mój obszar roboczy, można wyświetlić nowy zestaw danych, przedstawiający na lewym panelu w usłudze Power BI. Jest to dane przesyłane strumieniowo, które zostały wypchnięte z usługi Azure Stream Analytics w poprzednim kroku.
   * Upewnij się, że ***wizualizacje*** okienko jest otwarty i jest wyświetlany po prawej stronie ekranu.
3. Tworzenie kafelka "Żądanie przez Timestamp":

   * Kliknij zestaw danych **"EnergyStreamData"** na lewym panelu sekcji zestawów danych.
   * Kliknij przycisk **"Wykres liniowy"** ikonę ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic8.png).
   * Kliknij pozycję "EnergyStreamData" w **pola** panelu.
   * Kliknij przycisk **"Timestamp"** i upewnij się, pokazuje, w obszarze "Osi". Kliknij przycisk **"Ładowanie"** i upewnij się, pokazuje, w obszarze "Values".
   * Kliknij przycisk **ZAPISZ** u góry i nazwy raportu jako "EnergyStreamDataReport". Raport o nazwie "EnergyStreamDataReport" jest wyświetlany w sekcji raportów w okienku Nawigatora po lewej stronie.
   * Kliknij przycisk **"Numer Pin Visual"** ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic6.png) ikonę w prawym górnym rogu tym wykresie liniowym, okno "Przypnij do pulpitu nawigacyjnego" może być widoczna dla można wybrać pulpit nawigacyjny. Wybierz pozycję "EnergyStreamDataReport", a następnie kliknij pozycję "Przypnij".
   * Umieść kursor myszy nad tego kafelka na pulpicie nawigacyjnym, kliknij przycisk "edit" ikonę w prawym górnym rogu można zmienić jego tytuł, np. "Żądanie przez Timestamp"
4. Utwórz inne Kafelki pulpitu nawigacyjnego na podstawie odpowiednich zestawów danych. Widok pulpitu nawigacyjnego końcowego: ![](media/cortana-analytics-technical-guide-demand-forecast/PBIFullScreen.png)

### <a name="setup-cold-path-dashboard"></a>Pulpit nawigacyjny ścieżki nieaktywnej konfiguracji
W potoku danych ścieżki nieaktywnej essential celem jest prognozę żądanie każdego regionu. Usługa Power BI nawiązuje połączenie z bazą danych Azure SQL jako źródło danych, w którym są przechowywane wyniki przewidywań.

> [!NOTE]
> (1) może potrwać kilka godzin, aby zbierać wystarczająco prognozy wyników na pulpicie nawigacyjnym. Zalecamy uruchomienie tego procesu 2 – 3 godziny po biedzie generator danych. (2) w tym kroku wymagań wstępnych jest pobrać i zainstalować bezpłatne oprogramowanie [Power BI desktop](https://powerbi.microsoft.com/desktop).
>
>

1. Uzyskaj poświadczenia bazy danych.

   Potrzebujesz **bazy danych, nazwę serwera, nazwę bazy danych, nazwę użytkownika i hasło** przed przejściem do następnych kroków. Poniżej przedstawiono kroki poprowadzą Cię, jak je znaleźć.

   * Gdy **"Azure SQL Database"** w szablonie rozwiązań diagram zmieni kolor na zielony, kliknij go, a następnie kliknij przycisk **"Otwarte"**. Jest kierowane do witryny Azure portal, a także zostanie otwarta strona informacji z bazy danych.
   * Na stronie można znaleźć sekcji "Baza danych". Wyświetla ona na poziomie bazy danych, które zostały utworzone. Nazwa bazy danych powinna być **"Twoja nazwa rozwiązania + liczbę losową +"db""** (na przykład "mytest12345db").
   * Następnie kliknij bazę danych w nowej wyskakiwania panelu można znaleźć nazwę serwera bazy danych, w górnej części. Nazwa serwera bazy danych powinna być `"Your Solution Name + Random Number + 'database.windows.net,1433'"` (na przykład "mytest12345.database.windows.net,1433").
   * Baza danych **username** i **hasło** są takie same jak nazwa użytkownika i hasło nagrane wcześniej podczas wdrażania rozwiązania.
2. Zaktualizuj źródło danych plik usługi Power BI ścieżki nieaktywnej

   * Upewnij się, że zainstalowano najnowszą wersję [Power BI desktop](https://powerbi.microsoft.com/desktop).
   * W **"DemandForecastingDataGeneratorv1.0"** folder został pobrany, kliknij dwukrotnie **"Power BI Template\DemandForecastPowerBI.pbix"** pliku. Początkowa wizualizacje są oparte na fikcyjnymi danymi. **Uwaga:** Jeśli widzisz komunikat o błędzie, upewnij się, że zainstalowano najnowszą wersję programu Power BI Desktop.

     Gdy zostanie otwarte w górnej części pliku, kliknij przycisk **Edytuj zapytania**. W punkcie pop okna, kliknij dwukrotnie **"Źródło"** na prawym panelu.
     ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic1.png)
   * W punkcie pop okna, Zastąp **"Server"** i **"Baza danych"** przy użyciu własnych nazwy serwera i bazy danych, a następnie kliknij **"OK"**. Dla nazwy serwera, upewnij się, należy określić port 1433 (**YourSolutionName.database.windows.net, 1433**). Ignoruj ostrzeżenia, które są wyświetlane na ekranie.
   * W następnym wyskakiwania okna, zobaczysz dwie opcje w okienku po lewej stronie (**Windows** i **bazy danych**). Kliknij przycisk **"Baza danych"**, wypełnij swoje **"Username"** i **"Password"** (jest to nazwa użytkownika i hasło wprowadzone podczas najpierw wdrożono rozwiązanie i utworzone na platformie Azure Usługa SQL database). W ***wybierz poziom, które można zastosować te ustawienia***, zaznacz opcję poziomu bazy danych. Następnie kliknij przycisk **"Połącz"**.
   * Gdy masz z przewodnikiem wróć do poprzedniej strony, zamknij okno. POP komunikatów w poziomie — kliknij **Zastosuj**. Na koniec kliknij **Zapisz** przycisk, aby zapisać zmiany. Plik usługi Power BI ma teraz ustanowione połączenie z serwerem. Jeśli Twoje wizualizacje są puste, upewnij się, że wyczyść zaznaczenia na wizualizacji, aby wizualizować wszystkie dane, klikając ikonę gumki w prawym górnym rogu legendy. Użyj przycisku odświeżania, aby odzwierciedlać nowych danych na wizualizacji. Początkowo widoczne tylko danych inicjatora w wizualizacji jako usługi data factory jest zaplanowane na odświeżanie co 3 godziny. Po 3 godziny zostanie wyświetlony nowych przewidywań zostaną uwzględnione w wizualizacji, podczas odświeżania danych.
3. (Opcjonalnie) Publikowanie pulpitu nawigacyjnego ścieżki nieaktywnej w celu [usługi Power BI online](https://www.powerbi.com/). Należy pamiętać, że ten krok wymaga konta usługi Power BI (lub konta usługi Office 365).

   * Kliknij przycisk **"Publikuj"** i kilka sekund później zostanie wyświetlone okno zawierające "Publikowanie w usłudze Power BI sukces!" z zielonym znacznikiem wyboru. Kliknij poniższe łącze "Otwórz demoprediction.pbix w usłudze Power BI". Aby uzyskać szczegółowe instrukcje, zobacz [publikowanie z programu Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).
   * Aby utworzyć nowy pulpit nawigacyjny: kliknij **+** dalej, aby zarejestrować **pulpity nawigacyjne** sekcji w okienku po lewej stronie. Wprowadź nazwę "Pokaz prognozowania popytu" dla tego nowego pulpitu nawigacyjnego.
   * Po otwarciu raportu, kliknij przycisk ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic6.png) Aby przypiąć wszystkie wizualizacje do pulpitu nawigacyjnego. Aby uzyskać szczegółowe instrukcje, zobacz [przypinanie kafelka do pulpitu nawigacyjnego usługi Power BI z raportu](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report).
     Przejdź do strony pulpitu nawigacyjnego i Dostosuj rozmiar i położenie wizualizacji i Edytuj tytułach. Aby uzyskać szczegółowe instrukcje na temat edytowania Kafelki na pulpicie, zobacz [edytowanie kafelka — zmiany rozmiaru, przenoszenie, zmiana nazwy, numer pin, usuwanie, dodawanie hiperlinku](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Poniżej przedstawiono przykładowy pulpit nawigacyjny z niektóre wizualizacje ścieżki nieaktywnej do niego przypięte.

     ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic7.png)
4. (Opcjonalnie) Zaplanuj Odświeżanie źródła danych.

   * Aby zaplanować odświeżanie danych, umieść kursor nad **EnergyBPI Final** zestawu danych, kliknij przycisk ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic3.png) , a następnie wybierz **Zaplanuj odświeżanie**.
     **Uwaga:** Jeśli widzisz Masaż ostrzeżenie, kliknij przycisk **edytowanie poświadczeń** i upewnij się, że poświadczenia bazy danych są takie same, jak opisano w kroku 1.

     ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic4.png)
   * Rozwiń **Zaplanuj odświeżanie** sekcji. Włącz funkcję "zachować aktualność danych".
   * Zaplanuj odświeżanie, w zależności od potrzeb. Aby uzyskać więcej informacji, zobacz [odświeżania danych w usłudze Power BI](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/).

## **<a name="how-to-delete-your-solution"></a>Jak usunąć rozwiązania**
Upewnij się, aby zatrzymać generator danych, gdy nie korzystasz aktywnie rozwiązania jako działanie generatora danych spowoduje naliczenie wyższych kosztów. Usuń rozwiązanie, jeśli nie jest używany. Usunięcie rozwiązania powoduje usunięcie wszystkich składników, które są aprowizowane w Twojej subskrypcji po wdrożeniu rozwiązania. Aby usunąć rozwiązanie kliknąć nazwę rozwiązania w lewym panelu szablon rozwiązania, a następnie kliknij przycisk Usuń.

## **<a name="cost-estimation-tools"></a>Narzędzia do szacowania kosztów**
Następujące dwa narzędzia są dostępne umożliwić użytkownikom lepsze rozumienie całkowite koszty związane z uruchomionej Prognozowanie popytu na szablon rozwiązania energetycznego w Twojej subskrypcji:

* [Microsoft Azure narzędzie do szacowania kosztów (online)](https://azure.microsoft.com/pricing/calculator/)
* [Microsoft Azure narzędzie do szacowania kosztów (wersja klasyczna)](https://www.microsoft.com/download/details.aspx?id=43376)

## **<a name="acknowledgements"></a>Potwierdzenia**
W tym artykule jest tworzone, badacz danych Yijing Chen i programista Min Qiu w firmie Microsoft.
