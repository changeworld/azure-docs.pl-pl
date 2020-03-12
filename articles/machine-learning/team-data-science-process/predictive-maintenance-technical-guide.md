---
title: Przewodnik dotyczący Konserwacja zapobiegawcza na potrzeby lotnictwa i kosmonautyki - zespołu danych dla celów naukowych
description: Przewodnik techniczny dotyczący szablonu rozwiązania do konserwacji predykcyjnej w programie Aerospace, narzędziach i transportach.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: previous-author=fboylu, previous-ms.author=fboylu
ms.openlocfilehash: 3b3a0b00ee6e1e170023584c2e643a5802166428
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79087595"
---
# <a name="technical-guide-to-the-solution-template-for-predictive-maintenance-in-aerospace"></a>Przewodnik techniczny dotyczący szablonu rozwiązania do konserwacji predykcyjnej w programie Aerospace

> [!Important]
> W tym artykule jest przestarzała. Dyskusja dotycząca konserwacji predykcyjnej w programie Aerospace nadal jest istotna, ale w przypadku bieżących informacji zapoznaj się z tematem [Omówienie rozwiązania dla odbiorców w biznesie](https://github.com/Azure/cortana-intelligence-predictive-maintenance-aerospace).


Szablony rozwiązań zostały zaprojektowane w celu przyspieszenia procesu tworzenia demonstracji E2E. Wdrożony szablon inicjuje subskrypcję z niezbędnymi składnikami, a następnie kompiluje relacje między nimi. Inicjowania inicjuje również potok danych z przykładowymi danymi z aplikacji generatora danych, która pobrać i zainstalować na komputerze lokalnym, po wdrożeniu szablonu rozwiązania. Dane z generatorem hydrates potoku danych i rozpoczęcia generowania przewidywań uczenia maszynowego, które mogą być następnie wizualizowane na pulpicie nawigacyjnym usługi Power BI.

Proces wdrażania przeprowadzi Cię przez kilka kroków, aby skonfigurować poświadczenia rozwiązania. Upewnij się, że rejestrowanie poświadczenia, takie jak nazwa rozwiązania, nazwę użytkownika i hasło podane podczas wdrażania. 


Cele w tym artykule są:
- Opisano architekturę referencyjną i składniki aprowizowane w Twojej subskrypcji.
- Pokazują, jak zamienić przykładowe dane z użyciem własnych danych. 
- Pokazano, jak zmodyfikować szablon rozwiązania.  

## <a name="overview"></a>Omówienie
![Architektura konserwacji predykcyjnej](./media/predictive-maintenance-technical-guide/predictive-maintenance-architecture.png)

Wdrożenie rozwiązania aktywuje usługi platformy Azure, w tym centrum zdarzeń, Stream Analytics, HDInsight, Data Factory i Machine Learning. Diagram architektury przedstawia, jak Konserwacja zapobiegawcza na potrzeby lotnictwo i Kosmonautyka szablon rozwiązania jest konstruowany. Możesz zbadać te usługi w witrynie Azure portal, klikając je na diagramie szablonu rozwiązania utworzone przy użyciu wdrażania rozwiązania (z wyjątkiem usługi HDInsight, która jest obsługiwana na żądanie, gdy są wymagane do uruchamiania działań powiązanych potoku i później usunięta).
Pobierz [pełną wersję diagramu](https://download.microsoft.com/download/1/9/B/19B815F0-D1B0-4F67-AED3-A40544225FD1/ca-topologies-maintenance-prediction.png).

W poniższych sekcjach opisano elementy rozwiązania.

## <a name="data-source-and-ingestion"></a>Źródło danych i wprowadzania
### <a name="synthetic-data-source"></a>Źródło danych syntetycznego
W przypadku tego szablonu używane źródło danych jest generowane na podstawie pobranej aplikacji klasycznej, która jest uruchamiana lokalnie po pomyślnym wdrożeniu.

Aby uzyskać instrukcje, aby pobrać i zainstalować tę aplikację, wybierz węzeł pierwszy, Generator danych konserwacji predykcyjnej, na diagramie szablonu rozwiązania. Instrukcje znajdują się w pasku właściwości. Ta aplikacja przesyła strumieniowo usługę [Azure Event Hub](#azure-event-hub) do punktów danych lub zdarzeń używanych w pozostałej części przepływu rozwiązania. To źródło danych pochodzi z publicznie dostępnych danych z [repozytorium danych NASA](https://c3.nasa.gov/dashlink/resources/139/) przy użyciu [zestawu danych symulacji degradacji aparatu TurboFan](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan).

Aplikacja generowanie zdarzeń wypełnia usługi Azure Event Hub tylko wtedy, gdy jest wykonywany na komputerze.  

### <a name="azure-event-hub"></a>Centrum zdarzeń Azure  
Usługa [Azure Event Hub](https://azure.microsoft.com/services/event-hubs/) to odbiorca danych wejściowych dostarczonych przez syntetyczne źródło danych.

## <a name="data-preparation-and-analysis"></a>Przygotowywanie danych i analiza  
### <a name="azure-stream-analytics"></a>Usługa Azure Stream Analytics
Użyj [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) , aby zapewnić analizę w czasie rzeczywistym w strumieniu wejściowym z usługi [Azure Event Hub](#azure-event-hub) . Następnie można opublikować wyniki na pulpicie nawigacyjnym [Power BI](https://powerbi.microsoft.com) , a także zarchiwizować wszystkie nieprzetworzone zdarzenia przychodzące do usługi [Azure Storage](https://azure.microsoft.com/services/storage/) w celu późniejszego przetworzenia przez usługę [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) .

### <a name="hdinsight-custom-aggregation"></a>HDInsight agregacji niestandardowej
Uruchamianie skryptów [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) (zorganizowanych przez Azure Data Factory) za pomocą usługi HDInsight w celu zapewnienia agregacji dla nieprzetworzonych zdarzeń archiwizowanych przy użyciu zasobu Azure Stream Analytics.

### <a name="azure-machine-learning"></a>Azure Machine Learning
Wykonaj przewidywania dotyczące pozostałego okresu eksploatacji (pozostałego czasu eksploatacji) dla określonego silnika samolotu przy użyciu danych wejściowych otrzymanych z [usługą Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) (organizacja Azure Data Factory). 

## <a name="data-publishing"></a>Publikowanie danych
### <a name="azure-sql-database"></a>Azure SQL Database
Użyj [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) do przechowywania prognoz odebranych przez Azure Machine Learning, które są następnie używane na pulpicie nawigacyjnym [Power BI](https://powerbi.microsoft.com) .

## <a name="data-consumption"></a>Użycie danych
### <a name="power-bi"></a>Power BI
Użyj [Power BI](https://powerbi.microsoft.com) , aby wyświetlić pulpit nawigacyjny zawierający agregacje i alerty udostępniane przez [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/), a także przewidywania pozostałego czasu eksploatacji przechowywane w [Azure SQL Databaseach](https://azure.microsoft.com/services/sql-database/) , które zostały utworzone przy użyciu [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/).

## <a name="how-to-bring-in-your-own-data"></a>Jak przenieść w swoich danych
W tej sekcji opisano, jak możesz używać własnych danych na platformie Azure i jakie obszary wymagają zmian danych, które wprowadzasz w tej architekturze.

Jest mało prawdopodobne, że zestaw danych jest zgodny z zestawem wyników używanym przez [zestawienie symulacji degradacji aparatu TurboFan](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan) używany dla tego szablonu rozwiązania. Opis danych i wymagania są niezwykle istotne, w jak zmodyfikować ten szablon służy do pracy z własnych danych. 

W poniższych sekcjach omówiono te części szablonu, które wymaga modyfikacji, gdy wprowadzono nowy zestaw danych.

### <a name="azure-event-hub"></a>Centrum zdarzeń Azure
Usługa Azure Event Hub jest ogólny; dane mogą być przesyłane do koncentratora, w formacie CSV lub JSON. Brak specjalnego przetwarzania odbywa się w Centrum zdarzeń Azure, ale jest zrozumieć dane, które są przekazywane do jej.

W tym dokumencie nie opisano sposobu pozyskiwania danych, ale można łatwo wysłać zdarzenia lub dane do Centrum zdarzeń platformy Azure przy użyciu interfejsów API Centrum zdarzeń.

### <a name="azure-stream-analytics-1"></a>Azure Stream Analytics
Użyj zasobu Azure Stream Analytics, aby zapewnić analizę niemal w czasie rzeczywistym przez odczyt z strumieni danych i wyprowadzanie danych do dowolnej liczby źródeł.

Konserwacja zapobiegawcza lotnictwo i Kosmonautyka szablon rozwiązania zapytania usługi Azure Stream Analytics składa się z czterech zapytania podrzędne, każde zapytanie zużywania zdarzeń z usługi Azure Event Hub, z danych wyjściowych do czterech różnych lokalizacji. Te dane wyjściowe składają się z trzech zestawów danych usługi Power BI i jednej lokalizacji magazynu platformy Azure.

Zapytanie usługi Azure Stream Analytics można znaleźć przez:

* Nawiąż połączenie z portalem Azure
* Lokalizowanie Stream Analytics ![Stream Analytics ikony](./media/predictive-maintenance-technical-guide/icon-stream-analytics.png), które zostały wygenerowane podczas wdrażania rozwiązania (*na przykład* **maintenancesa02asapbi** i **maintenancesa02asablob** dla rozwiązania do konserwacji predykcyjnej)
* Zaznaczenie
  
  * ***Dane*** wejściowe dotyczące wyświetlania zapytania
  * ***Zapytanie*** w celu wyświetlenia samego zapytania
  * Dane ***wyjściowe*** umożliwiające wyświetlenie różnych danych wyjściowych

Informacje na temat konstrukcji zapytania Azure Stream Analytics można znaleźć w dokumentacji dotyczącej [zapytań Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) w witrynie MSDN.

W tym rozwiązaniu zapytań danych wyjściowych trzech zestawów danych za pomocą niemal analizy w czasie rzeczywistym informacje o przychodzącego strumienia danych do pulpitu nawigacyjnego usługi Power BI w ramach tego szablonu rozwiązania. Ponieważ istnieje niejawna wiedzę na temat formatu danych przychodzących, te zapytania musi być zmieniony zależnie od formatu danych.

Zapytanie w drugim Stream Analytics zadania **maintenancesa02asablob** po prostu wyprowadza wszystkie zdarzenia [centrum zdarzeń](https://azure.microsoft.com/services/event-hubs/) do [usługi Azure Storage](https://azure.microsoft.com/services/storage/) , dlatego nie wymaga żadnych zmian niezależnie od formatu danych, ponieważ pełne informacje o zdarzeniu są przesyłane strumieniowo do magazynu.

### <a name="azure-data-factory"></a>Azure Data Factory
Usługa [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) organizuje przenoszenie i przetwarzanie danych. W szablonie rozwiązania do konserwacji predykcyjnej dla programu Aerospace Fabryka danych składa się z trzech [potoków](../../data-factory/concepts-pipelines-activities.md) , które przechodzą i przetwarzają dane przy użyciu różnych technologii.  Dostęp do Twojej usługi data factory, otwierając węzła usługi Data Factory w dolnej części na diagramie szablonu rozwiązania utworzone przy użyciu wdrażania rozwiązania. Błędy w ramach zestawów danych są spowodowane usługi data factory wdrażane zanim został uruchomiony generator danych. Usuń te błędy można zignorować i nie uniemożliwiają działanie fabryki danych

![Błędy zbiorów danych fabryki danych](./media/predictive-maintenance-technical-guide/data-factory-dataset-error.png)

W tej sekcji omówiono wymagane [potoki i działania](../../data-factory/concepts-pipelines-activities.md) zawarte w [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/). Oto widok diagramu rozwiązania.

![Azure Data Factory](./media/predictive-maintenance-technical-guide/azure-data-factory.png)

Dwa potoki tej fabryki zawierają skrypty [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) używane do partycjonowania i agregowania danych. Po zanotowaniu te skrypty znajdują się na koncie [usługi Azure Storage](https://azure.microsoft.com/services/storage/) utworzonym podczas instalacji. Ich lokalizacja to: maintenancesascript\\\\skrypt\\\\Hive\\\\ (lub https://[Nazwa rozwiązania]. blob. Core. Windows. NET/maintenancesascript).

Podobnie jak w przypadku zapytań [Azure Stream Analytics](#azure-stream-analytics-1) , skrypty programu [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) mają niejawną wiedzę na temat przychodzącego formatu danych i należy je zmienić w oparciu o format danych.

#### <a name="aggregateflightinfopipeline"></a>*AggregateFlightInfoPipeline*
Ten [potok](../../data-factory/concepts-pipelines-activities.md) zawiera jedno działanie — działanie [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) za pomocą [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) , które uruchamia skrypt programu [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) w celu partycjonowania danych umieszczonych w [usłudze Azure Storage](https://azure.microsoft.com/services/storage/) w ramach zadania [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) .

Skrypt [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) dla tego zadania partycjonowania to ***AggregateFlightInfo. HQL.***

#### <a name="mlscoringpipeline"></a>*MLScoringPipeline*
Ten [potok](../../data-factory/concepts-pipelines-activities.md) zawiera kilka działań, których wynikiem jest wynikowe przewidywania z [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) eksperymentu skojarzonym z tym szablonem rozwiązania.

Dostępne są następujące czynności:

* Działanie [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) przy użyciu [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) , który uruchamia skrypt [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) w celu wykonywania agregacji i inżynierów funkcji niezbędnych do [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) eksperymentu.
  Skrypt [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) dla tego zadania partycjonowania to ***PrepareMLInput. HQL***.
* Działanie [kopiowania](https://msdn.microsoft.com/library/azure/dn835035.aspx) , które przenosi wyniki z działania [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) do pojedynczego obiektu BLOB [usługi Azure Storage](https://azure.microsoft.com/services/storage/) , do którego uzyskuje dostęp działanie [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) .
* Działanie [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) wywołuje eksperyment [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) z wynikami umieszczanymi w pojedynczym obiekcie blob [usługi Azure Storage](https://azure.microsoft.com/services/storage/) .

#### <a name="copyscoredresultpipeline"></a>*CopyScoredResultPipeline*
Ten [potok](../../data-factory/concepts-pipelines-activities.md) zawiera jedno działanie — działanie [kopiowania](https://msdn.microsoft.com/library/azure/dn835035.aspx) , które przenosi wyniki [Azure Machine Learning](#azure-machine-learning) eksperymentu z ***MLScoringPipeline*** do [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) aprowizacji w ramach instalacji szablonu rozwiązania.

### <a name="azure-machine-learning"></a>Azure Machine Learning
[Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) eksperyment używany dla tego szablonu rozwiązania zapewnia pozostały okres eksploatacji (pozostałego czasu eksploatacji) silnika samolotu. Eksperyment jest przeznaczony dla zestawu danych, używane i wymaga modyfikacji lub zastąpienia specyficzne dla danych plików odwoływanych.

## <a name="monitor-progress"></a>Monitoruj postęp
Po uruchomieniu Generator danych, potoku rozpoczyna się niezawierającego i różnych składników rozwiązania Uruchom określonego w następujących akcji polecenia wydane przez usługę data factory. Istnieją dwa sposoby, aby monitorować potok.

* Jedno z zadań usługi Stream Analytics zapisuje nieprzetworzone dane przychodzące do usługi blob storage. Jeśli klikniesz Blob Storage składnik rozwiązania z ekranu, który pomyślnie wdrożono rozwiązanie, a następnie kliknij przycisk Otwórz w prawym panelu, przeprowadzisz Cię do [Azure Portal](https://portal.azure.com/). Wyświetlonym obszarze kliknij dla obiektów blob. W następnym panelu zobaczysz listę kontenerów. Kliknij pozycję **maintenancesadata**. W następnym panelu znajduje się folder **rawData** . Znajdujące się w folderze rawdata są folderów przy użyciu nazwy, takie jak godzina = 17 i godziny 18. Obecność te foldery wskazuje nieprzetworzone dane są generowane na komputerze i przechowywane w magazynie obiektów blob. Pliki csv skończoną rozmiarów MB w tych folderach powinny być widoczne.
* Ostatnim krokiem w potoku jest zapis danych (na przykład przewidywań uczenia maszynowego) do bazy danych SQL. Trzeba będzie poczekać maksymalnie trzy godziny, dane są wyświetlane w usłudze SQL Database. Jednym ze sposobów monitorowania ilości danych dostępnych w SQL Database jest przechodzenie przez [Azure Portal](https://portal.azure.com/). Na panelu po lewej stronie Znajdź pozycję bazy danych SQL ![ikonę SQL](./media/predictive-maintenance-technical-guide/icon-SQL-databases.png) i kliknij ją. Następnie Znajdź **pmaintenancedb** bazy danych i kliknij ją. Na następnej stronie u dołu kliknij przycisk Zarządzaj
   
    ![Zarządzanie ikony](./media/predictive-maintenance-technical-guide/icon-manage.png)
   
    W tym miejscu możesz kliknąć nowe zapytanie i wykonywania zapytań o liczbę wierszy (na przykład wybierz count(*) z PMResult). Wraz z rozwojem bazy danych należy zwiększyć liczbę wierszy w tabeli.

## <a name="power-bi-dashboard"></a>Pulpit nawigacyjny usługi Power BI

Konfigurowanie pulpitu nawigacyjnego usługi Power BI w celu wizualizacji danych usługi Azure Stream Analytics (ścieżka aktywna) i wyników przewidywań usługi batch z aplikacji Azure machine learning (ścieżka nieaktywna).

### <a name="set-up-the-cold-path-dashboard"></a>Ustawianie pulpitu nawigacyjnego ścieżki nieaktywnej
W potoku danych ścieżki nieaktywnej celem jest uzyskanie predykcyjne pozostałego czasu eksploatacji (pozostały okres eksploatacji) każdego silnika samolotu po zakończeniu lotu (cykl). Wyniki prognozowania jest aktualizowana co 3 godziny przez przewidywanie silników samolotów, które ukończony lot w ciągu ostatnich 3 godzin.

Power BI nawiązuje połączenie z Azure SQL Database jako źródła danych, w którym są przechowywane wyniki przewidywania. 

Uwaga: 
1.    W przypadku wdrażania rozwiązania prognozowanie będzie widoczne w bazie danych w ciągu 3 godzin. Plik pbix, dostarczonego z pobranymi Generator zawiera pewne dane inicjatora, dzięki czemu można następnie od razu utworzyć pulpit nawigacyjny usługi Power BI. 
2.    W tym kroku wymagane jest pobranie i zainstalowanie bezpłatnego oprogramowania [Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/).

Poniższe kroki prowadzą na temat nawiązywania połączenia z bazą danych SQL została rozszerzona w czasie wdrażania rozwiązania zawierającego dane (na przykład wyników przewidywań) wizualizacji pliku pbix.

1. Uzyskaj poświadczenia bazy danych.
   
   Przed przejściem do następnych kroków będziesz potrzebować **nazwy serwera bazy danych, nazwy bazy danych, nazwy użytkownika i hasła** . Poniżej przedstawiono kroki poprowadzą Cię, jak je znaleźć.
   
   * Gdy **element "Azure SQL Database"** na diagramie szablonu rozwiązania zmieni kolor na zielony, kliknij go, a następnie kliknij przycisk **"Otwórz"** .
   * Zostanie wyświetlona nowa karta/okno przeglądarki, w której zostanie wyświetlona strona Azure Portal. Na panelu po lewej stronie kliknij pozycję **"grupy zasobów"** .
   * Wybierz subskrypcję używaną podczas wdrażania rozwiązania, a następnie wybierz pozycję **"YourSolutionName\_resourceName"** .
   * Aby uzyskać dostęp do bazy danych, w panelu nowy wyskakujący kliknij ikonę ![SQL](./media/predictive-maintenance-technical-guide/icon-sql.png). Nazwa bazy danych jest obok tej ikony (na przykład **"pmaintenancedb"** ), a **Nazwa serwera bazy danych** jest wyświetlana w obszarze właściwości Nazwa serwera i powinna wyglądać podobnie do **YourSolutionName.Database.Windows.NET**.
   * **Nazwa użytkownika** i **hasło** bazy danych są takie same jak nazwa użytkownika i hasło, które zostały wcześniej zarejestrowane podczas wdrażania rozwiązania.
2. Zaktualizuj źródło danych pliku raportu ścieżki nieaktywnej przy użyciu programu Power BI Desktop.
   
   * W folderze, do którego został pobrany i rozpakowany plik generatora, kliknij dwukrotnie plik **pbix\\PredictiveMaintenanceAerospace.** Jeśli wszystkie komunikaty ostrzegawcze są wyświetlane po otwarciu pliku, można je zignorować. W górnej części pliku kliknij pozycję **"Edytuj zapytania"** .
     
     ![Edytuj zapytania](./media/predictive-maintenance-technical-guide/edit-queries.png)
   * Zobaczysz dwie tabele, **RemainingUsefulLife** i **PMResult**. Wybierz pierwszą tabelę, a następnie kliknij przycisk ![ikona ustawień zapytania](./media/predictive-maintenance-technical-guide/icon-query-settings.png) obok pozycji **"Źródło"** w obszarze **"zastosowane kroki"** po prawej stronie panelu **Ustawienia zapytania** . Ignoruj wszystkie komunikaty ostrzegawcze, które są wyświetlane.
   * W oknie podręcznym Zastąp **wartość "serwer"** i **"baza danych"** własnymi nazwami serwera i bazy danych, a następnie kliknij przycisk **"OK"** . W polu Nazwa serwera upewnij się, że określono port 1433 (**YourSolutionName.Database.Windows.NET, 1433**). Pozostaw pole bazy danych jako **pmaintenancedb**. Ignoruj ostrzeżenia, które są wyświetlane na ekranie.
   * W następnym oknie podręcznym zobaczysz dwie opcje w okienku po lewej stronie (**system Windows** i **baza danych**). Kliknij pozycję **"baza danych"** , wprowadź nazwę **"username"** i **"Password** " (nazwę użytkownika i hasło wprowadzone podczas pierwszego wdrożenia rozwiązania i utworzyć Azure SQL Database). W obszarze ***Wybierz poziom, do którego mają zostać zastosowane te ustawienia***Sprawdź opcję poziomu bazy danych. Następnie kliknij pozycję **"Połącz"** .
   * Kliknij drugą tabelę **PMResult** , a następnie kliknij przycisk ![ikona nawigacji](./media/predictive-maintenance-technical-guide/icon-navigation.png) obok pozycji **"Źródło"** w obszarze **"zastosowane kroki"** w prawym okienku **"Ustawienia zapytania"** i zaktualizuj nazwy serwera i bazy danych zgodnie z powyższymi krokami i kliknij przycisk OK.
   * Gdy masz z przewodnikiem wróć do poprzedniej strony, zamknij okno. Zostanie wyświetlony komunikat — kliknij przycisk **Zastosuj**. Na koniec kliknij przycisk **Zapisz** , aby zapisać zmiany. Plik usługi Power BI ma teraz ustanowione połączenie z serwerem. Jeśli Twoje wizualizacje są puste, upewnij się, że wyczyść zaznaczenia na wizualizacji, aby wizualizować wszystkie dane, klikając ikonę gumki w prawym górnym rogu legendy. Użyj przycisku odświeżania, aby odzwierciedlać nowych danych na wizualizacji. Początkowo widoczne tylko danych inicjatora w wizualizacji jako usługi data factory jest zaplanowane na odświeżanie co 3 godziny. Po 3 godziny zostanie wyświetlony nowych przewidywań zostaną uwzględnione w wizualizacji, podczas odświeżania danych.
3. Obowiązkowe Opublikuj pulpit nawigacyjny zimnej ścieżki, aby [Power BI online](https://www.powerbi.com/). Ten krok wymaga konta Power BI (lub konta Office 365).
   
   * Kliknięcie przycisku **"Publikuj"** i kilku sekund spowoduje wyświetlenie okna zawierającego komunikat "publikowanie w Power BI powodzenie!" z zielonym znacznikiem wyboru. Kliknij link poniżej "Otwórz PredictiveMaintenanceAerospace.pbix w usłudze Power BI". Aby uzyskać szczegółowe instrukcje, zobacz temat [Publikowanie z Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).
   * Aby utworzyć nowy pulpit nawigacyjny: kliknij znak **+** obok sekcji **pulpity nawigacyjne** w okienku po lewej stronie. Wprowadź nazwę "Pokaz konserwacji predykcyjnej" dla tego nowego pulpitu nawigacyjnego.
   * Po otwarciu raportu kliknij przycisk ![ikona numeru PIN](./media/predictive-maintenance-technical-guide/icon-pin.png), aby przypiąć wszystkie wizualizacje do pulpitu nawigacyjnego. Aby uzyskać szczegółowe instrukcje, zobacz [Przypinanie kafelka do pulpitu nawigacyjnego Power BI z raportu](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report).
     Przejdź do strony pulpitu nawigacyjnego i Dostosuj rozmiar i położenie wizualizacji i Edytuj tytułach. Aby uzyskać szczegółowe instrukcje dotyczące edytowania kafelków, zobacz [Edytowanie kafelka — Zmienianie rozmiaru, przenoszenie, zmienianie nazwy, przypinanie, usuwanie, Dodawanie hiperlinku](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Poniżej przedstawiono przykładowy pulpit nawigacyjny z niektóre wizualizacje ścieżki nieaktywnej do niego przypięte.  W zależności od tego, jak długo uruchomieniu usługi generator danych numery na wizualizacje mogą być inne.
     <br/>
     ![widoku końcowego](./media/predictive-maintenance-technical-guide/final-view.png)
     <br/>
   * Aby zaplanować odświeżanie danych, umieść kursor myszy nad **PredictiveMaintenanceAerospace** zestawu danych, kliknij ikonę ![wielokropka](./media/predictive-maintenance-technical-guide/icon-elipsis.png) a następnie wybierz pozycję **Zaplanuj odświeżanie**.
     <br/>
     **Uwaga:** Jeśli zobaczysz ostrzeżenie Massage, kliknij przycisk **Edytuj poświadczenia** i upewnij się, że poświadczenia bazy danych są takie same jak te opisane w kroku 1.
     <br/>
     ![Zaplanuj odświeżanie](./media/predictive-maintenance-technical-guide/schedule-refresh.png)
     <br/>
   * Rozwiń sekcję **Zaplanuj odświeżanie** . Włącz funkcję "zachować aktualność danych".
     <br/>
   * Zaplanuj odświeżanie, w zależności od potrzeb. Aby uzyskać więcej informacji, zobacz [odświeżanie danych w Power BI](https://support.powerbi.com/knowledgebase/articles/474669-data-refresh-in-power-bi).

### <a name="setup-hot-path-dashboard"></a>Pulpit nawigacyjny ścieżki aktywnej konfiguracji
Poniższe kroki prowadzą, jak wizualizować dane wyjściowe z zadania usługi Stream Analytics, które zostały wygenerowane w czasie wdrażania rozwiązania. Do wykonania poniższych kroków jest wymagane konto [Power BI online](https://www.powerbi.com/) . Jeśli nie masz konta, możesz go [utworzyć](https://powerbi.microsoft.com/pricing).

1. Dodaj dane wyjściowe usługi Power BI w usłudze Azure Stream Analytics (ASA).
   
   * Musisz postępować zgodnie z instrukcjami w [Azure Stream Analytics & Power BI: pulpit nawigacyjny analizy do wglądu w dane przesyłane strumieniowo](../../stream-analytics/stream-analytics-power-bi-dashboard.md) w czasie rzeczywistym w celu skonfigurowania danych wyjściowych zadania Azure Stream Analytics jako pulpitu nawigacyjnego Power BI.
   * Zapytanie ASA ma trzy dane wyjściowe, które są **aircraftmonitor**, **aircraftalert**i **flightsbyhour**. Możesz wyświetlić zapytanie, klikając kartę zapytanie. odpowiadające każdej z tych tabel należy dodać dane wyjściowe do ASA. Po dodaniu pierwszego danych wyjściowych (**aircraftmonitor**) Upewnij się, że **alias danych wyjściowych**, **Nazwa zestawu danych** i **Nazwa tabeli** są takie same (**aircraftmonitor**). Powtórz kroki, aby dodać wyjściowe dla **aircraftalert**i **flightsbyhour**. Po dodaniu wszystkich trzech wyjściowe i uruchomić zadanie ASA, powinna pojawić się komunikat z potwierdzeniem ("Uruchamianie usługi Stream Analytics zadania maintenancesa02asapbi powiodło się.").
2. Logowanie do usługi [Power BI online](https://www.powerbi.com)
   
   * W sekcji zestawy danych w panelu po lewej stronie w obszarze mój obszar roboczy, powinny być wyświetlane nazwy ***DataSet*** **aircraftmonitor**, **aircraftalert**i **flightsbyhour** . Jest to dane przesyłane strumieniowo, które zostały wypchnięte z usługi Azure Stream Analytics w poprzednim kroku. Zestaw danych **flightsbyhour** może nie być wyświetlany w tym samym czasie co pozostałe dwa zbiory, ze względu na charakter zapytania SQL. Jednak należy widoczna po godzinie.
   * Upewnij się, że okienko ***wizualizacje*** jest otwarte i jest widoczne po prawej stronie ekranu.
3. Po utworzeniu danych otrzymywanych przez usługi Power BI, możesz rozpocząć wizualizowanie danych przesyłanych strumieniowo. Poniżej są przykładowy pulpit nawigacyjny z wizualizacjami niektóre ścieżki aktywnej do niego przypięte. Możesz utworzyć inne Kafelki pulpitu nawigacyjnego na podstawie odpowiednich zestawów danych. W zależności od tego, jak długo uruchomieniu usługi generator danych numery na wizualizacje mogą być inne.

    ![Widok pulpitu nawigacyjnego](media/predictive-maintenance-technical-guide/dashboard-view.png)

1. Poniżej przedstawiono kilka kroków, które należy wykonać, aby utworzyć jeden z powyższych kafelków — "widok floty czujnika 11 a próg 48,26":
   
   * Kliknij pozycję DataSet **aircraftmonitor** w sekcji zestawy danych po lewej stronie.
   * Kliknij ikonę **wykresu liniowego** .
   * Kliknij pozycję **przetworzone** w okienku **pola** , aby było wyświetlane w obszarze "oś" w okienku **wizualizacje** .
   * Kliknij pozycję "S11" i "S11\_Alert", aby były one wyświetlane w obszarze "wartości". Kliknij małą strzałkę obok pozycji **S11** i **S11\_alertu**, a następnie zmień wartość "Sum" na "Średnia".
   * Kliknij przycisk **Zapisz** u góry i nadaj raportowi nazwę "aircraftmonitor". Raport o nazwie "aircraftmonitor" jest wyświetlany w sekcji **raporty** w okienku **Nawigator** po lewej stronie.
   * Kliknij ikonę **pinezki** w prawym górnym rogu tego wykresu liniowego. Okno "Przypnij do pulpitu nawigacyjnego" mogą być wyświetlane w można wybrać pulpit nawigacyjny. Wybierz pozycję "Pokaz konserwacji predykcyjnej", a następnie kliknij pozycję "Przypnij."
   * Umieść wskaźnik myszy nad tym kafelkiem na pulpicie nawigacyjnym, a następnie kliknij ikonę "Edytuj" w prawym górnym rogu, aby zmienić jej tytuł na "widok floty z czujnika 11 vs. próg 48,26" i podtytuł na "Średnia między flotą w czasie".

## <a name="delete-your-solution"></a>Usuwanie rozwiązania
Upewnij się, aby zatrzymać generator danych, gdy nie korzystasz aktywnie rozwiązania jako działanie generatora danych spowoduje naliczenie wyższych kosztów. Usuń rozwiązanie, jeśli nie jest używany. Usunięcie rozwiązania powoduje usunięcie wszystkich składników, które są aprowizowane w Twojej subskrypcji po wdrożeniu rozwiązania. Aby usunąć rozwiązanie, kliknij nazwę rozwiązania w lewym panelu szablonu rozwiązania, a następnie kliknij przycisk **Usuń**.

## <a name="cost-estimation-tools"></a>Narzędzia do szacowania kosztów
Następujące dwa narzędzia są dostępne umożliwić użytkownikom lepsze rozumienie całkowite koszty związane z uruchomiona lotnictwo i Kosmonautyka szablon rozwiązania konserwacji predykcyjnej w ramach subskrypcji:

* [Narzędzie Microsoft Azure Cost szacowania (online)](https://azure.microsoft.com/pricing/calculator/)
* [Narzędzie Microsoft Azure Cost szacowania (Desktop)](https://www.microsoft.com/download/details.aspx?id=43376)

