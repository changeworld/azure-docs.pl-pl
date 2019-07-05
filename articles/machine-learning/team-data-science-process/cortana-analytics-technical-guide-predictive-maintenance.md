---
title: Przewodnik dotyczący Konserwacja zapobiegawcza na potrzeby lotnictwa i kosmonautyki - zespołu danych dla celów naukowych
description: Podręcznik techniczny do szablonu rozwiązania za pomocą pakietu Microsoft Cortana Intelligence pod kątem konserwacji predykcyjnej w lotnictwie i kosmonautyce, narzędzia i transportu.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 03/15/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=fboylu, previous-ms.author=fboylu
ms.openlocfilehash: feccaea0451ae36d841aae95ed0baf54f90c2700
ms.sourcegitcommit: 3107874d7559ea975e4d55ae33cdf45f4b5485e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/04/2019
ms.locfileid: "67568259"
---
# <a name="technical-guide-to-the-cortana-intelligence-solution-template-for-predictive-maintenance-in-aerospace"></a>Podręcznik techniczny do szablon rozwiązania Cortana Intelligence, pod kątem konserwacji predykcyjnej w lotnictwie i kosmonautyce

> [!Important]
> W tym artykule jest przestarzała. Omówienie konserwacji predykcyjnej w lotnictwie i Kosmonautyce jest nadal są prawidłowe, ale aby uzyskać aktualne informacje, zapoznaj się [— Omówienie rozwiązania dla użytkowników biznesowych](https://github.com/Azure/cortana-intelligence-predictive-maintenance-aerospace).


Szablony rozwiązań są przeznaczone do przyspieszenia procesu tworzenia pokaz E2E na podstawie pakietu Cortana Intelligence. Szablon wdrożonej aprowizuje subskrypcji za pomocą niezbędnych składników pakietu Cortana Intelligence, a następnie kompiluje relacje między nimi. Inicjowania inicjuje również potok danych z przykładowymi danymi z aplikacji generatora danych, która pobrać i zainstalować na komputerze lokalnym, po wdrożeniu szablonu rozwiązania. Dane z generatorem hydrates potoku danych i rozpoczęcia generowania przewidywań uczenia maszynowego, które mogą być następnie wizualizowane na pulpicie nawigacyjnym usługi Power BI.

Proces wdrażania przeprowadzi Cię przez kilka kroków, aby skonfigurować poświadczenia rozwiązania. Upewnij się, że rejestrowanie poświadczenia, takie jak nazwa rozwiązania, nazwę użytkownika i hasło podane podczas wdrażania. 


Cele w tym artykule są:
- Opisano architekturę referencyjną i składniki aprowizowane w Twojej subskrypcji.
- Pokazują, jak zamienić przykładowe dane z użyciem własnych danych. 
- Pokazano, jak zmodyfikować szablon rozwiązania.  

> [!TIP]
> Możesz pobrać i wydrukować [PDF wersję tego artykułu](https://download.microsoft.com/download/F/4/D/F4D7D208-D080-42ED-8813-6030D23329E9/cortana-analytics-technical-guide-predictive-maintenance.pdf).
> 
> 

## <a name="overview"></a>Przegląd
![Architektura konserwacji predykcyjnej](./media/cortana-analytics-technical-guide-predictive-maintenance/predictive-maintenance-architecture.png)

Podczas wdrażania rozwiązania, zostaje uaktywniony usług platformy Azure w ramach pakietu Cortana Analytics (w tym Centrum zdarzeń, usługi Stream Analytics, HDInsight, usługi fabryka danych i usługi Machine Learning). Diagram architektury przedstawia, jak Konserwacja zapobiegawcza na potrzeby lotnictwo i Kosmonautyka szablon rozwiązania jest konstruowany. Możesz zbadać te usługi w witrynie Azure portal, klikając je na diagramie szablonu rozwiązania utworzone przy użyciu wdrażania rozwiązania (z wyjątkiem usługi HDInsight, która jest obsługiwana na żądanie, gdy są wymagane do uruchamiania działań powiązanych potoku i później usunięta).
Pobierz [diagramu w pełnym rozmiarze wersji](https://download.microsoft.com/download/1/9/B/19B815F0-D1B0-4F67-AED3-A40544225FD1/ca-topologies-maintenance-prediction.png).

W poniższych sekcjach opisano elementy rozwiązania.

## <a name="data-source-and-ingestion"></a>Źródło danych i wprowadzania
### <a name="synthetic-data-source"></a>Źródło danych syntetycznego
W przypadku tego szablonu użyte źródło danych jest generowany na podstawie aplikacji pulpitu, Pobierz i uruchom lokalnie, po pomyślnym wdrożeniu.

Aby uzyskać instrukcje, aby pobrać i zainstalować tę aplikację, wybierz węzeł pierwszy, Generator danych konserwacji predykcyjnej, na diagramie szablonu rozwiązania. Instrukcje znajdują się w pasku właściwości. Źródła danych w tej aplikacji [usługi Azure Event Hub](#azure-event-hub) usługi za pomocą punktów danych lub zdarzenia, używane w pozostałej części przepływu rozwiązania. To źródło danych jest tworzony na podstawie publicznie dostępnych danych z [repozytorium danych prognostycznych NASA](https://c3.nasa.gov/dashlink/resources/139/) przy użyciu [Turbofan Engine Degradation Simulation Data Set](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan).

Aplikacja generowanie zdarzeń wypełnia usługi Azure Event Hub tylko wtedy, gdy jest wykonywany na komputerze.  

### <a name="azure-event-hub"></a>Azure Event Hub  
[Usługi Azure Event Hub](https://azure.microsoft.com/services/event-hubs/) service to odbiorca danych wejściowych dostarczonych przez syntetyczne źródło danych.

## <a name="data-preparation-and-analysis"></a>Przygotowywanie danych i analiza  
### <a name="azure-stream-analytics"></a>Usługa Azure Stream Analytics
Użyj [usługi Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) w celu dostarczania wyników analizy w czasie rzeczywistym dotyczących strumienia wejściowego z prawie [usługi Azure Event Hub](#azure-event-hub) usługi. Następnie opublikować wyniki na [usługi Power BI](https://powerbi.microsoft.com) pulpitu nawigacyjnego, a także wszystkie nieprzetworzone zdarzenia przychodzące do archiwum [usługi Azure Storage](https://azure.microsoft.com/services/storage/) usługi do późniejszego przetwarzania przez [usługi Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)usługi.

### <a name="hdinsight-custom-aggregation"></a>HDInsight agregacji niestandardowej
Uruchom [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skryptów (zarządzanych przez usługę Azure Data Factory) przy użyciu HDInsight w celu zapewnienia agregacji nieprzetworzonych zdarzeń, które są archiwizowane za pomocą usługi Azure Stream Analytics.

### <a name="azure-machine-learning"></a>Azure Machine Learning
Tworzyć prognozy na pozostały okres eksploatacji (RUL) określonego silnika samolotowego przy użyciu otrzymanych z danych wejściowych [usługi Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) (zarządzanych przez usługę Azure Data Factory). 

## <a name="data-publishing"></a>Publikowanie danych
### <a name="azure-sql-database"></a>Azure SQL Database
Użyj [usługi Azure SQL Database](https://azure.microsoft.com/services/sql-database/) do przechowywania prognoz otrzymanych przez usługę Azure Machine Learning, które są następnie używane w [usługi Power BI](https://powerbi.microsoft.com) pulpitu nawigacyjnego.

## <a name="data-consumption"></a>Użycie danych
### <a name="power-bi"></a>Power BI
Użyj [usługi Power BI](https://powerbi.microsoft.com) do wyświetlenia pulpitu nawigacyjnego, który zawiera agregacji i alerty, dostarczone przez [usługi Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/), jak również przewidywania pozostałego czasu eksploatacji jest przechowywany w [usługi Azure SQL Database](https://azure.microsoft.com/services/sql-database/) które zostały utworzone przy użyciu [usługi Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/).

## <a name="how-to-bring-in-your-own-data"></a>Jak przenieść w swoich danych
W tej sekcji opisano, jak możesz używać własnych danych na platformie Azure i jakie obszary wymagają zmian danych, które wprowadzasz w tej architekturze.

Jest mało prawdopodobne, że zestaw danych odpowiada zbioru danych używanego przez [Turbofan Engine Degradation Simulation Data Set](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan) używane dla tego szablonu rozwiązania. Opis danych i wymagania są niezwykle istotne, w jak zmodyfikować ten szablon służy do pracy z własnych danych. 

W poniższych sekcjach omówiono te części szablonu, które wymaga modyfikacji, gdy wprowadzono nowy zestaw danych.

### <a name="azure-event-hub"></a>Azure Event Hub
Usługa Azure Event Hub jest ogólny; dane mogą być przesyłane do koncentratora, w formacie CSV lub JSON. Brak specjalnego przetwarzania odbywa się w Centrum zdarzeń Azure, ale jest zrozumieć dane, które są przekazywane do jej.

W tym dokumencie nie opisano sposobu pozyskiwania danych, ale można łatwo wysłać zdarzenia lub dane do Centrum zdarzeń platformy Azure przy użyciu interfejsów API Centrum zdarzeń.

### <a name="azure-stream-analytics-1"></a>Azure Stream Analytics
Usługa Azure Stream Analytics do dostarczania wyników analizy w czasie rzeczywistym prawie odczytywanie ze strumieni danych i wyprowadzanie danych do dowolnej liczby źródeł.

Konserwacja zapobiegawcza lotnictwo i Kosmonautyka szablon rozwiązania zapytania usługi Azure Stream Analytics składa się z czterech zapytania podrzędne, każde zapytanie zużywania zdarzeń z usługi Azure Event Hub, z danych wyjściowych do czterech różnych lokalizacji. Te dane wyjściowe składają się z trzech zestawów danych usługi Power BI i jednej lokalizacji magazynu platformy Azure.

Zapytanie usługi Azure Stream Analytics można znaleźć przez:

* Nawiąż połączenie z portalem Azure
* Lokalizowanie zadania usługi Stream Analytics ![ikony usługi Stream Analytics](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-stream-analytics.png) które zostały wygenerowane, gdy rozwiązanie zostało wdrożone (*na przykład*, **maintenancesa02asapbi** i **maintenancesa02asablob** rozwiązania do konserwacji predykcyjnej)
* Zaznaczenie
  
  * ***Dane wejściowe*** Aby wyświetlić zapytanie wejściowe
  * ***ZAPYTANIE*** do wyświetlania samo zapytanie
  * ***Dane wyjściowe*** do wyświetlania różnych danych wyjściowych

Informacje o budowa zapytania usługi Azure Stream Analytics można znaleźć w [dokumentacja zapytań usługi Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx) w witrynie MSDN.

W tym rozwiązaniu zapytań danych wyjściowych trzech zestawów danych za pomocą niemal analizy w czasie rzeczywistym informacje o przychodzącego strumienia danych do pulpitu nawigacyjnego usługi Power BI w ramach tego szablonu rozwiązania. Ponieważ istnieje niejawna wiedzę na temat formatu danych przychodzących, te zapytania musi być zmieniony zależnie od formatu danych.

Zapytania w drugie zadanie usługi Stream Analytics **maintenancesa02asablob** po prostu wyświetla wszystkie [Centrum zdarzeń](https://azure.microsoft.com/services/event-hubs/) zdarzenia [usługi Azure Storage](https://azure.microsoft.com/services/storage/) i dlatego wymaga żadne zmiany niezależnie od tego, formatu danych jako pełne zdarzenie informacje są przesyłane strumieniowo do usługi storage.

### <a name="azure-data-factory"></a>Azure Data Factory
[Usługi Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) usługi służy do aranżacji przenoszenia i przetwarzania danych. W konserwacji predykcyjnej lotnictwo i Kosmonautyka szablon rozwiązania, usługi data factory składa się z trzech [potoki](../../data-factory/concepts-pipelines-activities.md) , przenoszenie i przetwarzanie danych za pomocą różnych technologii.  Dostęp do Twojej usługi data factory, otwierając węzła usługi Data Factory w dolnej części na diagramie szablonu rozwiązania utworzone przy użyciu wdrażania rozwiązania. Błędy w ramach zestawów danych są spowodowane usługi data factory wdrażane zanim został uruchomiony generator danych. Usuń te błędy można zignorować i nie uniemożliwiają działanie fabryki danych

![Błędy zbiorów danych fabryki danych](./media/cortana-analytics-technical-guide-predictive-maintenance/data-factory-dataset-error.png)

W tej sekcji omówiono niezbędne [potoki i działania](../../data-factory/concepts-pipelines-activities.md) zawarte w [usługi Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/). Oto widok diagramu rozwiązania.

![Azure Data Factory](./media/cortana-analytics-technical-guide-predictive-maintenance/azure-data-factory.png)

Zawiera dwa potoki tej fabryki [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skrypty używane do partycji i agregacji danych. Jeśli zaznaczono inaczej, skryptów znajdują się w [usługi Azure Storage](https://azure.microsoft.com/services/storage/) konto utworzone podczas instalacji. Ich lokalizacja to: maintenancesascript\\\\skryptu\\\\hive\\ \\ (lub name].blob.core.windows.net/maintenancesascript rozwiązania https://[Your).

Podobnie jak [usługi Azure Stream Analytics](#azure-stream-analytics-1) zapytań, [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skryptów ma niejawne informacji na temat informacji na temat formatu danych przychodzących i musi być zmieniony zależnie od formatu danych.

#### <a name="aggregateflightinfopipeline"></a>*AggregateFlightInfoPipeline*
To [potoku](../../data-factory/concepts-pipelines-activities.md) zawiera jedno działanie — [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) czynności za pomocą [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) , które jest uruchamiane [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skrypt w celu podzielenia danych umieścić w [usługi Azure Storage](https://azure.microsoft.com/services/storage/) podczas [usługi Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) zadania.

[Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skryptu dla tego zadania partycjonowania jest ***AggregateFlightInfo.hql***

#### <a name="mlscoringpipeline"></a>*MLScoringPipeline*
To [potoku](../../data-factory/concepts-pipelines-activities.md) zawiera kilka działań, w których efekt jest ocenami prognoz z [usługi Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) eksperymentu skojarzone z tym szablonie rozwiązania.

Dostępne są następujące czynności:

* [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) czynności za pomocą [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) , które jest uruchamiane [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skrypt w celu wykonywania agregacji i inżynieria niezbędne [maszyny na platformie Azure Nauka](https://azure.microsoft.com/services/machine-learning/) eksperymentować.
  [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skryptu dla tego zadania partycjonowania jest ***PrepareMLInput.hql***.
* [Kopiuj](https://msdn.microsoft.com/library/azure/dn835035.aspx) działania, który przenosi wyniki z [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) aktywności do pojedynczego [usługi Azure Storage](https://azure.microsoft.com/services/storage/) uzyskiwał dostęp do obiektów blob [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) działanie.
* [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) wywołania działania [usługi Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) eksperymentu z wynikami umieścić w pojedynczym [usługi Azure Storage](https://azure.microsoft.com/services/storage/) obiektu blob.

#### <a name="copyscoredresultpipeline"></a>*CopyScoredResultPipeline*
To [potoku](../../data-factory/concepts-pipelines-activities.md) zawiera jedno działanie — [kopiowania](https://msdn.microsoft.com/library/azure/dn835035.aspx) działania, który przenosi wyniki [usługi Azure Machine Learning](#azure-machine-learning) eksperymentować z  ***MLScoringPipeline*** do [usługi Azure SQL Database](https://azure.microsoft.com/services/sql-database/) aprowizowany w ramach instalacji szablonu rozwiązania.

### <a name="azure-machine-learning"></a>Azure Machine Learning
[Usługi Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) eksperymentować używane, aby ten szablon rozwiązania zawiera pozostałe przydatne okresu eksploatacji (RUL) silnika samolotu. Eksperyment jest przeznaczony dla zestawu danych, używane i wymaga modyfikacji lub zastąpienia specyficzne dla danych plików odwoływanych.

Aby uzyskać informacji na temat tworzenia eksperymentu usługi Azure Machine Learning, zobacz [konserwacji predykcyjnej: Krok 1 z 3, przygotowania danych i technicznego opracowywania funkcji](https://gallery.cortanaanalytics.com/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2).

## <a name="monitor-progress"></a>Monitoruj postęp
Po uruchomieniu Generator danych, potoku rozpoczyna się niezawierającego i różnych składników rozwiązania Uruchom określonego w następujących akcji polecenia wydane przez usługę data factory. Istnieją dwa sposoby, aby monitorować potok.

1. Jedno z zadań usługi Stream Analytics zapisuje nieprzetworzone dane przychodzące do usługi blob storage. Po kliknięciu składnika magazynu obiektów Blob rozwiązania z ekranu możesz pomyślnie wdrożono rozwiązanie i następnie w prawym okienku kliknij pozycję Otwórz, spowoduje to przejście do [witryny Azure portal](https://portal.azure.com/). Wyświetlonym obszarze kliknij dla obiektów blob. W następnym panelu zobaczysz listę kontenerów. Kliknij pozycję **maintenancesadata**. W ciągu następnych panel jest **rawdata** folderu. Znajdujące się w folderze rawdata są folderów przy użyciu nazwy, takie jak godzina = 17 i godziny 18. Obecność te foldery wskazuje nieprzetworzone dane są generowane na komputerze i przechowywane w magazynie obiektów blob. Pliki csv skończoną rozmiarów MB w tych folderach powinny być widoczne.
2. Ostatnim krokiem w potoku jest zapis danych (na przykład przewidywań uczenia maszynowego) do bazy danych SQL. Trzeba będzie poczekać maksymalnie trzy godziny, dane są wyświetlane w usłudze SQL Database. Jednym ze sposobów, aby monitorować, ile danych jest dostępna w usłudze SQL Database jest za pośrednictwem [witryny Azure portal](https://portal.azure.com/). Na lewym panelu Znajdź bazy danych SQL ![ikonę SQL](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-SQL-databases.png) i kliknij ją. Następnie znajdź bazy danych **pmaintenancedb** i kliknij go. Na następnej stronie u dołu kliknij przycisk Zarządzaj
   
    ![Zarządzanie ikony](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-manage.png)
   
    W tym miejscu możesz kliknąć nowe zapytanie i wykonywania zapytań o liczbę wierszy (na przykład wybierz count(*) z PMResult). Wraz z rozwojem bazy danych należy zwiększyć liczbę wierszy w tabeli.

## <a name="power-bi-dashboard"></a>Pulpit nawigacyjny usługi Power BI

Konfigurowanie pulpitu nawigacyjnego usługi Power BI w celu wizualizacji danych usługi Azure Stream Analytics (ścieżka aktywna) i wyników przewidywań usługi batch z aplikacji Azure machine learning (ścieżka nieaktywna).

### <a name="set-up-the-cold-path-dashboard"></a>Ustawianie pulpitu nawigacyjnego ścieżki nieaktywnej
W potoku danych ścieżki nieaktywnej celem jest uzyskanie predykcyjne pozostałego czasu eksploatacji (pozostały okres eksploatacji) każdego silnika samolotu po zakończeniu lotu (cykl). Wyniki prognozowania jest aktualizowana co 3 godziny przez przewidywanie silników samolotów, które ukończony lot w ciągu ostatnich 3 godzin.

Usługa Power BI nawiązuje połączenie z bazą danych Azure SQL jako źródło danych, w którym są przechowywane wyniki przewidywań. Uwaga: (1) na temat wdrażania rozwiązania, przewidywanie pojawi się w bazie danych maksymalnie 3 godziny.
Plik pbix, dostarczonego z pobranymi Generator zawiera pewne dane inicjatora, dzięki czemu można następnie od razu utworzyć pulpit nawigacyjny usługi Power BI. (2) w tym kroku wymagań wstępnych jest pobrać i zainstalować bezpłatne oprogramowanie [Power BI desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/).

Poniższe kroki prowadzą na temat nawiązywania połączenia z bazą danych SQL została rozszerzona w czasie wdrażania rozwiązania zawierającego dane (na przykład wyników przewidywań) wizualizacji pliku pbix.

1. Uzyskaj poświadczenia bazy danych.
   
   Będziesz potrzebować **bazy danych, nazwę serwera, nazwę bazy danych, nazwę użytkownika i hasło** przed przejściem do następnych kroków. Poniżej przedstawiono kroki poprowadzą Cię, jak je znaleźć.
   
   * Gdy **usługi Azure SQL Database** w szablonie rozwiązań diagram zmieni kolor na zielony, kliknij go, a następnie kliknij przycisk **"Otwórz"** .
   * Zostaną wyświetlone nowe karty/okno przeglądarki zawierające stronę witryny Azure portal. Kliknij przycisk **"Grupy zasobów"** w panelu po lewej stronie.
   * Wybierz subskrypcję, używany w przypadku wdrażania rozwiązania, a następnie wybierz **"YourSolutionName\_ResourceGroup"** .
   * W nowych wyskakiwania panelu kliknij ![ikonę SQL](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-sql.png) ikonę, aby dostęp do bazy danych. Nazwa bazy danych znajduje się obok tej ikony (na przykład **"pmaintenancedb"** ), a **nazwy serwera bazy danych** znajduje się w obszarze właściwości nazwy serwera i powinien wyglądać podobnie do  **YourSolutionName.database.windows.net**.
   * Baza danych **username** i **hasło** są takie same jak nazwa użytkownika i hasło nagrane wcześniej podczas wdrażania rozwiązania.
2. Zaktualizuj źródło danych pliku raportu ścieżki nieaktywnej przy użyciu programu Power BI Desktop.
   
   * W folderze, w której pobrano i rozpakowano plik Generator, kliknij dwukrotnie **usługi Power BI\\PredictiveMaintenanceAerospace.pbix** pliku. Jeśli wszystkie komunikaty ostrzegawcze są wyświetlane po otwarciu pliku, można je zignorować. W górnej części pliku, kliknij przycisk **Edytuj zapytania**.
     
     ![Edytuj zapytania](./media/cortana-analytics-technical-guide-predictive-maintenance/edit-queries.png)
   * Zostaną wyświetlone dwie tabele **RemainingUsefulLife** i **PMResult**. Wybierz pierwszą tabelę, a następnie kliknij przycisk ![ikonę ustawienia zapytania](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-query-settings.png) obok **"Źródło"** w obszarze **ZASTOSOWANE KROKI** po prawej stronie **"Ustawienia zapytania"** Panel. Ignoruj wszystkie komunikaty ostrzegawcze, które są wyświetlane.
   * W punkcie pop okna, Zastąp **"Server"** i **"Database"** przy użyciu własnych nazwy serwera i bazy danych, a następnie kliknij **"OK"** . Dla nazwy serwera, upewnij się, należy określić port 1433 (**YourSolutionName.database.windows.net, 1433**). Pozostaw pole bazy danych jako **pmaintenancedb**. Ignoruj ostrzeżenia, które są wyświetlane na ekranie.
   * W następnym wyskakiwania okna, zobaczysz dwie opcje w okienku po lewej stronie (**Windows** i **bazy danych**). Kliknij przycisk **"Database"** , wypełnij swoje **'Username'** i **"Password"** (jest to nazwa użytkownika i hasło wprowadzone podczas najpierw wdrożono rozwiązanie i utworzone na platformie Azure Usługa SQL database). W ***wybierz poziom, które można zastosować te ustawienia***, zaznacz opcję poziomu bazy danych. Następnie kliknij przycisk **"Połącz z"** .
   * Kliknij drugą tabelę **PMResult** kliknięcie ![ikona Nawigacja](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-navigation.png) obok **"Źródło"** w obszarze **ZASTOSOWANE KROKI** po prawej stronie **"Ustawienia zapytania"** panelu i aktualizowanie nazwy serwera i bazy danych, tak jak w powyższych krokach i kliknij przycisk OK.
   * Gdy masz z przewodnikiem wróć do poprzedniej strony, zamknij okno. Zostanie wyświetlony komunikat — kliknij przycisk **Zastosuj**. Na koniec kliknij **Zapisz** przycisk, aby zapisać zmiany. Plik usługi Power BI ma teraz ustanowione połączenie z serwerem. Jeśli Twoje wizualizacje są puste, upewnij się, że wyczyść zaznaczenia na wizualizacji, aby wizualizować wszystkie dane, klikając ikonę gumki w prawym górnym rogu legendy. Użyj przycisku odświeżania, aby odzwierciedlać nowych danych na wizualizacji. Początkowo widoczne tylko danych inicjatora w wizualizacji jako usługi data factory jest zaplanowane na odświeżanie co 3 godziny. Po 3 godziny zostanie wyświetlony nowych przewidywań zostaną uwzględnione w wizualizacji, podczas odświeżania danych.
3. (Opcjonalnie) Publikowanie pulpitu nawigacyjnego ścieżki nieaktywnej w celu [usługi Power BI online](https://www.powerbi.com/). Należy pamiętać, że ten krok wymaga konta usługi Power BI (lub konta usługi Office 365).
   
   * Kliknij przycisk **"Publikuj"** i kilka sekund później zostanie wyświetlone okno zawierające "Publikowanie w usłudze Power BI sukces!" z zielonym znacznikiem wyboru. Kliknij link poniżej "Otwórz PredictiveMaintenanceAerospace.pbix w usłudze Power BI". Aby uzyskać szczegółowe instrukcje, zobacz [publikowanie z programu Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).
   * Aby utworzyć nowy pulpit nawigacyjny: kliknij **+** dalej, aby zarejestrować **pulpity nawigacyjne** sekcji w okienku po lewej stronie. Wprowadź nazwę "Pokaz konserwacji predykcyjnej" dla tego nowego pulpitu nawigacyjnego.
   * Po otwarciu raportu, kliknij przycisk ![ikonę PINEZKI](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-pin.png) przypiąć wszystkie wizualizacje do pulpitu nawigacyjnego. Aby uzyskać szczegółowe instrukcje, zobacz [przypinanie kafelka do pulpitu nawigacyjnego usługi Power BI z raportu](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report).
     Przejdź do strony pulpitu nawigacyjnego i Dostosuj rozmiar i położenie wizualizacji i Edytuj tytułach. Aby uzyskać szczegółowe instrukcje na temat edytowania Kafelki na pulpicie, zobacz [edytowanie kafelka — zmiany rozmiaru, przenoszenie, zmiana nazwy, numer pin, usuwanie, dodawanie hiperlinku](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Poniżej przedstawiono przykładowy pulpit nawigacyjny z niektóre wizualizacje ścieżki nieaktywnej do niego przypięte.  W zależności od tego, jak długo uruchomieniu usługi generator danych numery na wizualizacje mogą być inne.
     <br/>
     ![Końcowe widoku](./media/cortana-analytics-technical-guide-predictive-maintenance/final-view.png)
     <br/>
   * Aby zaplanować odświeżanie danych, umieść kursor nad **PredictiveMaintenanceAerospace** zestawu danych, kliknij przycisk ![ikonę wielokropka](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-elipsis.png) , a następnie wybierz **Zaplanuj odświeżanie**.
     <br/>
     **Uwaga:** Jeśli widzisz Masaż ostrzeżenie, kliknij przycisk **edytowanie poświadczeń** i upewnij się, że poświadczenia bazy danych są takie same, jak opisano w kroku 1.
     <br/>
     ![Zaplanuj odświeżanie](./media/cortana-analytics-technical-guide-predictive-maintenance/schedule-refresh.png)
     <br/>
   * Rozwiń **Zaplanuj odświeżanie** sekcji. Włącz funkcję "zachować aktualność danych".
     <br/>
   * Zaplanuj odświeżanie, w zależności od potrzeb. Aby uzyskać więcej informacji, zobacz [odświeżania danych w usłudze Power BI](https://support.powerbi.com/knowledgebase/articles/474669-data-refresh-in-power-bi).

### <a name="setup-hot-path-dashboard"></a>Pulpit nawigacyjny ścieżki aktywnej konfiguracji
Poniższe kroki prowadzą, jak wizualizować dane wyjściowe z zadania usługi Stream Analytics, które zostały wygenerowane w czasie wdrażania rozwiązania. A [usługi Power BI online](https://www.powerbi.com/) można wykonać następujące czynności, wymagane jest konto. Jeśli nie masz konta, możesz to zrobić [utworzyć](https://powerbi.microsoft.com/pricing).

1. Dodaj dane wyjściowe usługi Power BI w usłudze Azure Stream Analytics (ASA).
   
   * Musi postępuj zgodnie z instrukcjami [usługi Azure Stream Analytics i Power BI: Pulpit nawigacyjny analizy, aby uzyskać wgląd w czasie rzeczywistym danych przesyłanych strumieniowo](../../stream-analytics/stream-analytics-power-bi-dashboard.md) skonfigurować dane wyjściowe zadania usługi Azure Stream Analytics jako pulpit nawigacyjny usługi Power BI.
   * Zapytanie ASA ma trzy danych wyjściowych, które są **aircraftmonitor**, **aircraftalert**, i **flightsbyhour**. Zapytania można wyświetlić, klikając na karcie zapytania. Odpowiadający każdej z tych tabel, należy dodać dane wyjściowe do ASA. Po dodaniu pierwszego danych wyjściowych (**aircraftmonitor**) upewnij się, że **Alias wyjściowy**, **Nazwa zestawu danych** i **nazwy tabeli** są tego samego (**aircraftmonitor**). Powtórz kroki, aby dodać dane wyjściowe dla **aircraftalert**, i **flightsbyhour**. Po dodaniu wszystkich trzech wyjściowe i uruchomić zadanie ASA, powinna pojawić się komunikat z potwierdzeniem ("Uruchamianie usługi Stream Analytics zadania maintenancesa02asapbi powiodło się.").
2. Zaloguj się do [usługi Power BI w trybie online](https://www.powerbi.com)
   
   * W lewym panelu sekcji zestawów danych w obszarze Mój obszar roboczy ***DATASET*** nazwy **aircraftmonitor**, **aircraftalert**, i **flightsbyhour** powinna zostać wyświetlona. Jest to dane przesyłane strumieniowo, które zostały wypchnięte z usługi Azure Stream Analytics w poprzednim kroku. Zestaw danych **flightsbyhour** mogą nie pojawić się w tym samym czasie co inne dwa zestawy danych ze względu na charakter zapytania SQL pod nim. Jednak należy widoczna po godzinie.
   * Upewnij się, że ***wizualizacje*** okienko jest otwarty i jest wyświetlany po prawej stronie ekranu.
3. Po utworzeniu danych otrzymywanych przez usługi Power BI, możesz rozpocząć wizualizowanie danych przesyłanych strumieniowo. Poniżej są przykładowy pulpit nawigacyjny z wizualizacjami niektóre ścieżki aktywnej do niego przypięte. Możesz utworzyć inne Kafelki pulpitu nawigacyjnego na podstawie odpowiednich zestawów danych. W zależności od tego, jak długo uruchomieniu usługi generator danych numery na wizualizacje mogą być inne.

    ![Widok pulpitu nawigacyjnego](media/cortana-analytics-technical-guide-predictive-maintenance/dashboard-view.png)

1. Poniżej przedstawiono niektóre czynności, aby utworzyć jeden z kafelków, powyżej — "floty widok vs czujnik 11. Próg 48,26" kafelka:
   
   * Kliknij zestaw danych **aircraftmonitor** na lewym panelu sekcji zestawów danych.
   * Kliknij przycisk **wykres liniowy** ikony.
   * Kliknij przycisk **przetwarzane** w **pola** okienka, tak aby wyświetlała w obszarze "Osi" w **wizualizacje** okienka.
   * Kliknij przycisk "s11" i "s11\_alert", aby obie były wyświetlane w obszarze "Values". Kliknij małą strzałkę obok pozycji **s11** i **s11\_alert**, zmień "Sum" na "Średnia".
   * Kliknij przycisk **ZAPISZ** u góry i nazwy raportu "aircraftmonitor." Raport o nazwie "aircraftmonitor" jest wyświetlany w **raporty** sekcji **Nawigator** w okienku po lewej stronie.
   * Kliknij przycisk **numeru Pin Visual** ikonę w prawym górnym rogu tym wykresie liniowym. Okno "Przypnij do pulpitu nawigacyjnego" mogą być wyświetlane w można wybrać pulpit nawigacyjny. Wybierz pozycję "Pokaz konserwacji predykcyjnej", a następnie kliknij pozycję "Przypnij."
   * Umieść kursor myszy nad Kafelek na pulpicie nawigacyjnym, kliknij ikonę "Edytuj" w prawym górnym rogu, aby zmienić jego tytuł "floty widok vs czujnik 11. Próg 48,26" i podtytuł, aby"Średniej przez floty wraz z upływem czasu."

## <a name="delete-your-solution"></a>Usuwanie rozwiązania
Upewnij się, aby zatrzymać generator danych, gdy nie korzystasz aktywnie rozwiązania jako działanie generatora danych spowoduje naliczenie wyższych kosztów. Usuń rozwiązanie, jeśli nie jest używany. Usunięcie rozwiązania powoduje usunięcie wszystkich składników, które są aprowizowane w Twojej subskrypcji po wdrożeniu rozwiązania. Aby usunąć rozwiązanie, kliknij swoją nazwę rozwiązania, w lewym panelu szablon rozwiązania, a następnie kliknij przycisk **Usuń**.

## <a name="cost-estimation-tools"></a>Narzędzia do szacowania kosztów
Następujące dwa narzędzia są dostępne umożliwić użytkownikom lepsze rozumienie całkowite koszty związane z uruchomiona lotnictwo i Kosmonautyka szablon rozwiązania konserwacji predykcyjnej w ramach subskrypcji:

* [Microsoft Azure narzędzie do szacowania kosztów (online)](https://azure.microsoft.com/pricing/calculator/)
* [Microsoft Azure narzędzie do szacowania kosztów (wersja klasyczna)](https://www.microsoft.com/download/details.aspx?id=43376)

