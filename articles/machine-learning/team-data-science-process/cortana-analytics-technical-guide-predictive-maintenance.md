---
title: Konserwacji predykcyjnej w aerospace z platformy Azure — podręcznik techniczny Cortana Intelligence rozwiązania | Dokumentacja firmy Microsoft
description: Podręcznik techniczny do szablonu rozwiązania z Microsoft Cortana Intelligence do konserwacji predykcyjnej w aerospace, narzędzia i transportu.
services: machine-learning
documentationcenter: ''
author: fboylu
manager: jhubbard
editor: cgronlun
ms.assetid: 2c4d2147-0f05-4705-8748-9527c2c1f033
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2017
ms.author: fboylu
ms.openlocfilehash: 2916252c08c599d2e528595a8cdf2abca8ea89a3
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938450"
---
# <a name="technical-guide-to-the-cortana-intelligence-solution-template-for-predictive-maintenance-in-aerospace-and-other-businesses"></a>Podręcznik techniczny do szablonu Cortana analizy rozwiązania dla konserwacji predykcyjnej aerospace i innych firm

>[!Important]
W tym artykule jest przestarzała. Omówienie konserwacji predykcyjnej w Aerospace jest nadal obowiązują, ale aby uzyskać aktualne informacje, zapoznaj się [Omówienie rozwiązania dla firm](https://github.com/Azure/cortana-intelligence-predictive-maintenance-aerospace).


Szablony rozwiązań zostały zaprojektowane w celu przyspieszenia procesu tworzenia pokaz E2E u góry pakietu Cortana Intelligence Suite. Szablon wdrożonej przepisy subskrypcji z niezbędne składniki Cortana Intelligence, a następnie kompiluje relacji między nimi. On również nasion potoku danych z przykładowymi danymi z aplikacji generator danych, które można pobrać i zainstalować na komputerze lokalnym po wdrożeniu szablon rozwiązania. Dane z generatorem hydrates potoku danych i rozpoczęcia generowania prognoz uczenia maszynowego, które mogą być następnie wizualizowane na pulpicie nawigacyjnym usługi Power BI.

Proces wdrażania przeprowadzi Cię przez kilka kroków, aby skonfigurować poświadczenia rozwiązania. Upewnij się, że rekord poświadczeń, takich jak nazwa rozwiązania, nazwę użytkownika i hasło, które należy podać podczas wdrażania. 


Cele w tym artykule są:
- Opisz architektura referencyjna struktury i składników aprowizowane w Twojej subskrypcji.
- Pokazują, jak zamienić przykładowe dane z użyciem własnych danych. 
- Pokazują, jak można zmodyfikować szablon rozwiązania.  

> [!TIP]
> Możesz pobrać i wydrukować [PDF wersja tego artykułu](http://download.microsoft.com/download/F/4/D/F4D7D208-D080-42ED-8813-6030D23329E9/cortana-analytics-technical-guide-predictive-maintenance.pdf).
> 
> 

## <a name="overview"></a>Przegląd
![Architektura konserwacji predykcyjnej](./media/cortana-analytics-technical-guide-predictive-maintenance/predictive-maintenance-architecture.png)

Podczas wdrażania rozwiązania, zostaje uaktywniony usług platformy Azure w ramach pakietu Analytics Cortana (w tym Centrum zdarzeń, Stream Analytics, HDInsight, fabryki danych i Machine Learning). Diagram architektury pokazuje, jak jest tworzony konserwacji predykcyjnej lotniczego szablonu rozwiązania. Badania tych usług w portalu Azure, klikając na diagramie szablon rozwiązania utworzone przy użyciu wdrażania rozwiązania (z wyjątkiem HDInsight, która jest inicjowana na żądanie, gdy są wymagane do uruchomienia działania powiązane potoku i usunąć później).
Pobierz [diagramu w pełnym rozmiarze wersji](http://download.microsoft.com/download/1/9/B/19B815F0-D1B0-4F67-AED3-A40544225FD1/ca-topologies-maintenance-prediction.png).

W poniższych sekcjach opisano elementy rozwiązania.

## <a name="data-source-and-ingestion"></a>Źródło danych i wprowadzanie
### <a name="synthetic-data-source"></a>Źródło danych syntetycznego
W przypadku tego szablonu źródło danych używane jest generowana z aplikacją, która Pobierz i uruchom lokalnie, po pomyślnym wdrożeniu.

Aby uzyskać instrukcje, aby pobrać i zainstalować tę aplikację, wybierz węzeł pierwszy, Generator danych konserwacji predykcyjnej, na diagramie szablon rozwiązania. Instrukcje znajdują się w pasku właściwości. Ta aplikacja źródła [Azure Event Hub](#azure-event-hub) usługi z punktów danych lub zdarzenia, używane w pozostałej części przepływu rozwiązania. To źródło danych jest określana na podstawie publicznie dostępnych danych z [repozytorium danych NASA](https://c3.nasa.gov/dashlink/resources/139/) przy użyciu [zestawu danych symulacji turbowentylatorowe aparat degradacji](http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/#turbofan).

Aplikacja generowania zdarzeń wypełnia Azure Event Hub tylko wtedy, gdy jest wykonywane na tym komputerze.  

### <a name="azure-event-hub"></a>Centrum zdarzeń Azure  
[Azure Event Hub](https://azure.microsoft.com/services/event-hubs/) usługi jest odbiorcą danych wejściowych dostarczonych przez syntetyczne źródło danych.

## <a name="data-preparation-and-analysis"></a>Przygotowanie danych i analiza  
### <a name="azure-stream-analytics"></a>Usługa Azure Stream Analytics
Użyj [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) zapewnienie niemal w czasie rzeczywistym analizy strumienia wejściowego z [Azure Event Hub](#azure-event-hub) usługi. Następnie opublikować wyniki na [usługi Power BI](https://powerbi.microsoft.com) pulpitu nawigacyjnego, jak również wszystkie nieprzetworzone zdarzenia przychodzące do archiwum [usługi Azure Storage](https://azure.microsoft.com/services/storage/) usługi do późniejszego przetwarzania przez [fabryki danych Azure](https://azure.microsoft.com/documentation/services/data-factory/)usługi.

### <a name="hdinsight-custom-aggregation"></a>HDInsight agregacji niestandardowej
Uruchom [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skryptów (zorkiestrowana przez fabryki danych Azure) korzystania z usługi HDInsight w celu zapewnienia agregacji na zdarzenia pierwotnych archiwizowane za pomocą usługi Azure Stream Analytics.

### <a name="azure-machine-learning"></a>Azure Machine Learning
Tworzenia prognoz na pozostałych użytkowania (RUL) aparatem powietrznego określonej przy użyciu wejść odebranych z [Azure Machine Learning usługi](https://azure.microsoft.com/services/machine-learning/) (zorkiestrowana przez fabryki danych Azure). 

## <a name="data-publishing"></a>Publikowanie danych
### <a name="azure-sql-database"></a>Azure SQL Database
Użyj [bazy danych SQL Azure](https://azure.microsoft.com/services/sql-database/) do przechowywania prognoz odbierane przez usługę uczenie maszynowe Azure, które są następnie używane w [usługi Power BI](https://powerbi.microsoft.com) pulpitu nawigacyjnego.

## <a name="data-consumption"></a>Użycie danych
### <a name="power-bi"></a>Power BI
Użyj [usługi Power BI](https://powerbi.microsoft.com) Aby wyświetlić pulpit nawigacyjny, który zawiera agregacji i alerty dostarczonych przez [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/), jak również prognoz RUL przechowywane w [bazy danych SQL Azure](https://azure.microsoft.com/services/sql-database/) które zostały utworzone przy użyciu [usługi Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/).

## <a name="how-to-bring-in-your-own-data"></a>Jak przenieść własne dane
W tej sekcji opisano dotyczące przywracania danych użytkownika na platformie Azure, a obszary wymagają zmian danych, które można przenosić w tej architekturze.

Jest mało prawdopodobne, czy zestaw danych jest zgodna zestawu danych używany przez [zestawu danych symulacji degradacji aparat turbowentylatorowe](http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/#turbofan) używane dla tego szablonu rozwiązania. Opis danych i wymagania są niezwykle istotne w sposób zmodyfikować ten szablon służy do pracy z własnych danych. 

W poniższych sekcjach omówiono części szablonu, które wymagają modyfikacji, jeśli wprowadzono nowy zestaw danych.

### <a name="azure-event-hub"></a>Centrum zdarzeń Azure
Azure Centrum zdarzeń jest rodzajowy; dane mogą być publikowane w Centrum w formacie CSV lub JSON. Brak specjalnego przetwarzania występuje w Centrum zdarzeń platformy Azure, ale jest ważne, aby zrozumieć dane, które są przekazywane do niego.

Ten dokument nie opisuje sposobu pozyskiwania danych, ale można łatwo wysłać zdarzenia lub dane do Centrum zdarzeń platformy Azure przy użyciu interfejsów API Centrum zdarzeń.

### <a name="azure-stream-analytics"></a>Usługa Azure Stream Analytics
Korzystanie z usługi Azure Stream Analytics, zapewnia przekazywaną praktycznie analiz w czasie rzeczywistym za odczytywanie ze strumieni danych i generowanie dowolną liczbę źródeł danych.

Konserwacji predykcyjnej lotniczego szablonu rozwiązania zapytania usługi Azure Stream Analytics składa się z czterech zapytań sub, każdego zapytania zużywające zdarzenia z usługi Azure Event Hub, z danych wyjściowych do czterech różnych lokalizacjach. Te dane wyjściowe składają się z trzech zbiorów danych usługi Power BI i jedną lokalizację magazynu Azure.

Zapytanie usługi Azure Stream Analytics można znaleźć przez:

* Połącz do portalu Azure
* Lokalizowanie zadania usługi analiza strumienia ![ikona Stream Analytics](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-stream-analytics.png) które zostały wygenerowane, gdy rozwiązanie zostało wdrożone (*na przykład*, **maintenancesa02asapbi** i **maintenancesa02asablob** dla rozwiązania konserwacji predykcyjnej)
* Wybieranie
  
  * ***Dane wejściowe*** Aby wyświetlić zapytanie danych wejściowych
  * ***ZAPYTANIE*** Aby wyświetlić samą kwerendę
  * ***GENERUJE*** do wyświetlania różnych danych wyjściowych

Informacji na temat tworzenia zapytań usługi Azure Stream Analytics można znaleźć w [Stream Analytics kwerendy odwołania](https://msdn.microsoft.com/library/azure/dn834998.aspx) w witrynie MSDN.

W tym rozwiązaniu zapytania output trzy zestawy danych z niemal analiz w czasie rzeczywistym informacje o przychodzącego strumienia danych do pulpitu nawigacyjnego usługi Power BI w ramach tego rozwiązania szablonu. Ponieważ nie istnieje niejawna wiedzą na temat formatu przychodzących danych, te zapytania musi być zmieniony oparte na format danych.

Zapytania w zadaniu Stream Analytics drugi **maintenancesa02asablob** po prostu wyświetla wszystkie [Centrum zdarzeń](https://azure.microsoft.com/services/event-hubs/) zdarzenia [usługi Azure Storage](https://azure.microsoft.com/services/storage/) i dlatego wymaga żadne zmiany niezależnie od formatu danych jako zdarzenie pełne informacje przesyłanej strumieniowo do magazynu.

### <a name="azure-data-factory"></a>Azure Data Factory
[Fabryki danych Azure](https://azure.microsoft.com/documentation/services/data-factory/) Usługa uruchamia przepływ i przetwarzania danych. W konserwacji predykcyjnej lotniczego szablonu rozwiązania, fabryki danych składa się z trzech [potoki](../../data-factory/concepts-pipelines-activities.md) czy przenoszenia i przetwarzania danych przy użyciu różnych technologii.  Dostęp do fabrykę danych przez otwarcie węzła fabryki danych w dolnej części diagramu szablon rozwiązania utworzone przy użyciu wdrażania rozwiązania. Błędy w obszarze zestawów danych z powodu fabryki danych wdrażania przed generator danych zostało uruchomione. Te błędy można zignorować i nie zapobiegają fabrykę danych działanie

![Błędy zbiorów danych fabryki danych](./media/cortana-analytics-technical-guide-predictive-maintenance/data-factory-dataset-error.png)

W tej sekcji omówiono niezbędnych [potoków i działania](../../data-factory/concepts-pipelines-activities.md) zawartych w [fabryki danych Azure](https://azure.microsoft.com/documentation/services/data-factory/). Oto widok diagramu rozwiązania.

![Azure Data Factory](./media/cortana-analytics-technical-guide-predictive-maintenance/azure-data-factory.png)

Zawiera dwa potoki tej fabryki [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skryptów używanych na partycje i agregacji danych. Jeśli podano, skryptów znajdują się w [usługi Azure Storage](https://azure.microsoft.com/services/storage/) konto utworzone podczas instalacji. Jest ich lokalizacji: maintenancesascript\\\\skryptu\\\\hive\\ \\ (lub https://[Your name].blob.core.windows.net/maintenancesascript rozwiązania).

Podobnie jak [Azure Stream Analytics](#azure-stream-analytics-1) zapytań, [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skryptów ma niejawne informacji na temat przychodzące format danych i musi być zmieniony w zależności od formatu danych.

#### <a name="aggregateflightinfopipeline"></a>*AggregateFlightInfoPipeline*
To [potoku](../../data-factory/concepts-pipelines-activities.md) zawiera pojedyncze działanie - [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) działania przy użyciu [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) , na którym działa [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) Umieść skrypt do partycjonowania danych w [usługi Azure Storage](https://azure.microsoft.com/services/storage/) podczas [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) zadania.

[Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skryptów jest to zadanie partycjonowania ***AggregateFlightInfo.hql***

#### <a name="mlscoringpipeline"></a>*MLScoringPipeline*
To [potoku](../../data-factory/concepts-pipelines-activities.md) zawiera kilka działań, którego wynik końcowy jest scored prognoz z [usługi Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) eksperymentu skojarzone z tym szablonem rozwiązania.

Dostępne są następujące czynności:

* [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) działania przy użyciu [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) , na którym działa [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skrypt, aby wykonać agregacji i inżynieria niezbędne do [maszyny platformy Azure Learning](https://azure.microsoft.com/services/machine-learning/) eksperymentu.
  [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skryptów jest to zadanie partycjonowania ***PrepareMLInput.hql***.
* [Kopiuj](https://msdn.microsoft.com/library/azure/dn835035.aspx) działanie, które przenosi wyników z [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) działania do jednego [usługi Azure Storage](https://azure.microsoft.com/services/storage/) dostęp do obiektów blob [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) działanie.
* [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) wywołań działania [usługi Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) doświadczenia z wynikami umieścić w pojedynczym [usługi Azure Storage](https://azure.microsoft.com/services/storage/) obiektu blob.

#### <a name="copyscoredresultpipeline"></a>*CopyScoredResultPipeline*
To [potoku](../../data-factory/concepts-pipelines-activities.md) zawiera pojedyncze działanie - [kopiowania](https://msdn.microsoft.com/library/azure/dn835035.aspx) działanie, które przenosi wyniki [usługi Azure Machine Learning](#azure-machine-learning) Poeksperymentuj z  ***MLScoringPipeline*** do [bazy danych SQL Azure](https://azure.microsoft.com/services/sql-database/) udostępniane w ramach instalacji szablon rozwiązania.

### <a name="azure-machine-learning"></a>Azure Machine Learning
[Usługi Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) eksperymentu używane dla pozostałych przydatne w cyklu życia (RUL) aparatu powietrznego zawiera ten szablon rozwiązania. Eksperyment jest przeznaczony dla zestawu danych używane i wymaga modyfikacji lub sprowadzonych zastąpienia specyficzne dla danych.

Aby uzyskać informacji na temat tworzenia eksperymentu uczenia maszynowego Azure, zobacz [konserwacji predykcyjnej: krok 1 z 3, przygotowanie danych i funkcji engineering](http://gallery.cortanaanalytics.com/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2).

## <a name="monitor-progress"></a>Monitoruj postęp
Po uruchomieniu Generator danych, potoku rozpocznie niezawierającego i różne składniki rozwiązania start zasób do następujących akcji polecenia wykonane przez fabryki danych. Istnieją dwa sposoby monitorowania potoku.

1. Jeden z zadania usługi analiza strumienia zapisuje nieprzetworzone dane przychodzące do magazynu obiektów blob. Po kliknięciu składnika magazynu obiektów Blob rozwiązania na ekranie można pomyślnie wdrożyć rozwiązanie i następnie w prawym okienku kliknij polecenie Otwórz, spowoduje to przejście do [portalu Azure](https://portal.azure.com/). Wyświetlonym edytorze kliknij na obiekty BLOB. W panelu dalej możesz wyświetlić listę kontenerów. Polecenie **maintenancesadata**. W następnej panel jest **rawdata** folderu. W folderze rawdata są folderów przy użyciu nazwy, takie jak godzina = 17 i godzina = 18. Obecność tych folderów wskazuje nieprzetworzone dane są generowane na komputerze i przechowywane w magazynie obiektów blob. Powinny pojawić się plików csv o rozmiarze ograniczone w MB w tych folderach.
2. Ostatnim krokiem potoku jest zapis danych (na przykład predykcje uzyskiwane z uczenia maszynowego) do bazy danych SQL. Może być konieczne poczekaj maksymalnie trzy godziny, aby te dane są wyświetlane w bazie danych SQL. Jednym ze sposobów monitorowania, jak dużo danych jest dostępne w bazie danych SQL jest za pośrednictwem [portalu Azure](https://portal.azure.com/). W lewym panelu zlokalizować bazy danych SQL ![ikona SQL](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-SQL-databases.png) i kliknij ją. Zlokalizuj bazę danych **pmaintenancedb** i kliknij go. Na następnej stronie u dołu kliknij przycisk ZARZĄDZAJ
   
    ![Ikona zarządzania](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-manage.png).
   
    W tym miejscu możesz kliknąć na nowe zapytanie i zapytanie o liczbę wierszy (na przykład wybierz count(*) z PMResult). Wraz z rozwojem bazy danych, należy zwiększyć liczbę wierszy w tabeli.

## <a name="power-bi-dashboard"></a>Pulpit nawigacyjny usługi Power BI

Konfigurowanie pulpitu nawigacyjnego usługi Power BI do wizualizacji danych Azure Stream Analytics (ścieżka gorących) i wyniki prognozowania usługi partia zadań Azure machine learning (ścieżka zimnych).

### <a name="set-up-the-cold-path-dashboard"></a>Ustawianie ścieżki zimnych pulpitu nawigacyjnego
Ścieżka zimnych danych potoku celem jest uzyskanie predykcyjnej RUL (pozostałe użytkowania) każdego powietrznego aparatu po zakończeniu transmitowane (cykle). Wynik prognozowania jest aktualizowany co 3 godziny przewidywania aparaty powietrznego, które zakończyły lot w ciągu ostatnich 3 godziny.

Usługi Power BI łączy się z bazą danych Azure SQL jako źródła danych, w którym są przechowywane wyniki prognozowania. Uwaga: 1) dotyczące wdrażania rozwiązania, przewidywanie pojawi się w bazie danych w ciągu 3 godziny.
Dołączonej pobierania Generator plik pbix zawiera niektóre dane inicjatora, dzięki czemu można od razu utworzyć pulpit nawigacyjny usługi Power BI. 2) w tym kroku wstępnie wymagane jest pobranie i zainstalowanie bezpłatnego oprogramowania [Power BI desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/).

Poniższe kroki informacje pomocne w sposób nawiązywania połączenia z bazą danych SQL zostało uruchomione w czasie wdrażania rozwiązania zawierającego dane (na przykład wyniki prognozowania) na potrzeby wizualizacji plików pbix.

1. Pobierz poświadczenia bazy danych.
   
   Będziesz potrzebować **bazy danych, nazwę serwera, nazwa bazy danych, nazwę użytkownika i hasło** przed przejściem do następnej czynności. Poniżej przedstawiono kroki prowadzące jak je znaleźć.
   
   * Raz **usługi Azure SQL Database** w szablonie rozwiązania włącza zielony diagramu, kliknij go, a następnie kliknij przycisk **"Open"**.
   * Zostanie wyświetlone nowe karty/okno przeglądarki, która zostanie wyświetlona strona portalu Azure. Kliknij przycisk **"Grupy zasobów"** w lewym panelu.
   * Wybierz subskrypcję, używany w przypadku wdrażania rozwiązania, a następnie wybierz **"YourSolutionName\_ResourceGroup"**.
   * W nowych pop limit panelu, kliknij przycisk ![ikona SQL](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-sql.png) ikonę, aby dostęp do bazy danych. Nazwa bazy danych jest obok ta ikona (na przykład **"pmaintenancedb"**) i **nazwę serwera bazy danych** znajduje się w obszarze właściwości nazwy serwera i powinien być podobny do  **YourSoutionName.database.windows.net**.
   * Baza danych **username** i **hasło** jest taka sama jak nazwa użytkownika i hasło uprzednio zarejestrowane podczas wdrażania rozwiązania.
2. Aktualizowanie źródła danych pliku raportu zimnych ścieżki Power BI Desktop.
   
   * W folderze, którego pobrano i unzipped Generator plików, kliknij dwukrotnie **PowerBI\\PredictiveMaintenanceAerospace.pbix** pliku. Jeśli po otwarciu pliku wyświetlane wszelkie ostrzeżenia dotyczące wymagań, można je zignorować. W górnej części pliku, kliknij przycisk **"Edytuj zapytania"**.
     
     ![Edytowanie zapytań](./media/cortana-analytics-technical-guide-predictive-maintenance/edit-queries.png)
   * Zobaczysz dwóch tabel, **RemainingUsefulLife** i **PMResult**. Wybierz pierwszej tabeli, a następnie kliknij przycisk ![ikonę ustawienia zapytania](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-query-settings.png) obok **'Source'** w obszarze **"KROKI stosowane"** po prawej stronie **"Ustawienia zapytania"** Panel. Ignoruj wszystkie wyświetlone komunikaty ostrzegawcze.
   * W lokalizacji pop okna, Zastąp **'Server'** i **"Baza danych"** z własne nazwy serwera i bazy danych, a następnie kliknij przycisk **"OK"**. Dla nazwy serwera, upewnij się, że możesz określić port 1433 (**YourSoutionName.database.windows.net, 1433**). Pozostaw pole bazy danych jako **pmaintenancedb**. Ignoruj ostrzeżenia, które są wyświetlane na ekranie.
   * W następnym pop okna, zobaczysz dwie opcje w okienku po lewej stronie (**Windows** i **bazy danych**). Kliknij przycisk **"Baza danych"**, wypełnij Twojej **'Username'** i **"Password"** (jest to nazwa użytkownika i hasła podczas najpierw wdrożyć rozwiązanie i utworzyć platformy Azure Baza danych SQL). W ***wybierz poziom Aby zastosować te ustawienia do***, zaznacz opcję poziomu bazy danych. Następnie kliknij przycisk **"Połącz"**.
   * Kliknij w drugiej tabeli **PMResult** następnie kliknij przycisk ![ikona nawigacji](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-navigation.png) obok **'Source'** w obszarze **"KROKI stosowane"** po prawej stronie **"Ustawienia zapytania"** panelu i zaktualizuj nazwy serwera i bazy danych, tak jak powyższe kroki i kliknij przycisk OK.
   * Gdy są z przewodnikiem wróć do poprzedniej strony, zamknij okno. Zostanie wyświetlony komunikat z — kliknij przycisk **Zastosuj**. Na koniec kliknij **zapisać** przycisk, aby zapisać zmiany. Plik usługi Power BI ma teraz nawiązać połączenia z serwerem. Jeśli Twoje wizualizacje są puste, upewnij się, że wyczyść zaznaczenia na wizualizacje wizualizować wszystkie dane, klikając ikonę gumki w prawym górnym rogu legendy. Użyj przycisku Odśwież, aby odzwierciedlić nowe dane na wizualizacjach. Początkowo widoczne są tylko dane inicjatora w sieci wizualizacji zgodnie z fabryki danych jest zaplanowane odświeżanie co 3 godziny. Po 3 godziny zostanie wyświetlony nowy prognoz, które zostaną uwzględnione w sieci wizualizacji podczas odświeżania danych.
3. (Opcjonalnie) Publikowanie pulpitu zimnych ścieżki [online usługi Power BI](http://www.powerbi.com/). Należy pamiętać, że ten krok wymaga konta usługi Power BI (lub konta usługi Office 365).
   
   * Kliknij przycisk **"Publikuj"** i później kilku sekund zostanie wyświetlone okno "Publikowania Power BI sukcesu!" z zielonym znacznikiem wyboru. Kliknij łącze poniżej "Otwórz PredictiveMaintenanceAerospace.pbix w usłudze Power BI". Aby uzyskać szczegółowe instrukcje, zobacz [publikowania z Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).
   * Aby utworzyć nowy pulpit nawigacyjny: kliknij **+** dalej, aby zarejestrować **pulpity nawigacyjne** sekcji w okienku po lewej stronie. Wprowadź nazwę "Demo konserwacji predykcyjnej" dla tego nowego pulpitu nawigacyjnego.
   * Po otwarciu raportu, kliknij przycisk ![ikonę PINEZKI](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-pin.png) Aby przypiąć wszystkie wizualizacje do pulpitu nawigacyjnego. Aby uzyskać szczegółowe instrukcje, zobacz [przypiąć Kafelek do pulpitu nawigacyjnego usługi Power BI z raportu](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report).
     Przejdź do strony pulpitu nawigacyjnego i Dostosuj rozmiar i położenie sieci wizualizacji i Edytuj ich tytułów. Aby uzyskać szczegółowe instrukcje na temat edytowania swoje Kafelki, zobacz [edycji kafelka — zmiany rozmiaru, Przenieś, Zmień nazwę, numer pin, usuwanie, Dodawanie hiperłącza](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Oto pulpitu nawigacyjnego przykład z niektórych wizualizacjami zimnych ścieżki przypięty do niego.  W zależności od tego, jak długo zostanie uruchomione z generator danych numery na wizualizacjach mogą się różnić.
     <br/>
     ![Widok końcowy](./media/cortana-analytics-technical-guide-predictive-maintenance/final-view.png)
     <br/>
   * Aby zaplanować odświeżanie danych, umieść kursor myszy nad **PredictiveMaintenanceAerospace** zestawu danych, kliknij przycisk ![ikoną wielokropka](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-elipsis.png) , a następnie wybierz **planowanie odświeżania**.
     <br/>
     **Uwaga:** Jeśli widzisz Masaż ostrzeżenie, kliknij przycisk **edytowanie poświadczeń** i upewnij się, że Twoje poświadczenia bazy danych są takie same jak opisany w kroku 1.
     <br/>
     ![Harmonogram odświeżania](./media/cortana-analytics-technical-guide-predictive-maintenance/schedule-refresh.png)
     <br/>
   * Rozwiń węzeł **planowanie odświeżania** sekcji. Włącz funkcję "zachować aktualność danych".
     <br/>
   * Planowanie odświeżania na podstawie Twoich potrzeb. Aby uzyskać więcej informacji, zobacz [odświeżania danych w usłudze Power BI](https://support.powerbi.com/knowledgebase/articles/474669-data-refresh-in-power-bi).

### <a name="setup-hot-path-dashboard"></a>Pulpit nawigacyjny aktywnej ścieżki Instalatora
Poniższe kroki przewodnika jak do wizualizacji danych wyjściowych z zadania usługi analiza strumienia, które zostały wygenerowane w czasie wdrażania rozwiązania. A [online usługi Power BI](http://www.powerbi.com/) konta jest wymagany do wykonania następujących kroków. Jeśli nie masz konta, możesz [utworzyć](https://powerbi.microsoft.com/pricing).

1. Dodawanie danych wyjściowych usługi Power BI w Azure Stream Analytics (ASA).
   
   * Wykonaj instrukcje w [Azure Stream Analytics & usługi Power BI: pulpitu nawigacyjnego analytics uzyskać wgląd w czasie rzeczywistym przesyłać strumieniowo danych](../../stream-analytics/stream-analytics-power-bi-dashboard.md) skonfigurować dane wyjściowe zadania usługi analiza strumienia Azure jako pulpit nawigacyjny usługi Power BI.
   * Zapytanie ASA ma trzy dane wyjściowe, które są **aircraftmonitor**, **aircraftalert**, i **flightsbyhour**. Zapytania można wyświetlić, klikając na karcie zapytania. Odpowiadający każdej z tych tabel, należy dodać danych wyjściowych do ASA. Po dodaniu pierwszego danych wyjściowych (**aircraftmonitor**) upewnij się, że **Alias wyjściowy**, **nazwę zestawu danych** i **nazwy tabeli** są tego samego (**aircraftmonitor**). Powtórz kroki, aby dodać dane wyjściowe dla **aircraftalert**, i **flightsbyhour**. Po dodaniu wszystkich trzech output tabel i uruchomił zadanie ASA, należy pobrać potwierdzenia ("Uruchamianie usługi analiza strumienia zadania maintenancesa02asapbi powiodło się.").
2. Zaloguj się do [online usługi Power BI](http://www.powerbi.com)
   
   * W lewym panelu sekcji zestawów danych w obszarze Mój obszar roboczy ***DATASET*** nazwy **aircraftmonitor**, **aircraftalert**, i **flightsbyhour** powinna zostać wyświetlona. Jest to dane przesyłane strumieniowo, które wypychana z usługi Azure Stream Analytics w poprzednim kroku. Zestaw danych **flightsbyhour** mogą nie występować w tym samym czasie jako inne dwa zestawy danych ze względu na specyfikę zapytania SQL związany z nim. Jednak go powinny być widoczne po upływie godziny.
   * Upewnij się, że ***wizualizacje*** okienko jest otwarty i jest wyświetlany w prawej części ekranu.
3. Po utworzeniu danych otrzymywanych przez usługę Power BI, można uruchomić wizualizację danych przesyłania strumieniowego. Poniżej pulpitu nawigacyjnego przykład z niektórych wizualizacjami aktywnej ścieżki jest przypięta do niego. Można utworzyć innych kafelka pulpitu nawigacyjnego oparte na odpowiednich zestawów danych. W zależności od tego, jak długo zostanie uruchomione z generator danych numery na wizualizacjach mogą się różnić.

    ![Widok pulpitu nawigacyjnego](media\cortana-analytics-technical-guide-predictive-maintenance\dashboard-view.png)

1. Poniżej przedstawiono niektóre czynności, aby utworzyć jedną z powyższych — Kafelki "floty widok vs czujnik 11. Próg 48,26" kafelka:
   
   * Kliknij zestaw danych **aircraftmonitor** w lewym panelu sekcji zestawów danych.
   * Kliknij przycisk **wykres liniowy** ikony.
   * Kliknij przycisk **przetwarzanych** w **pola** okienku, tak że przedstawia w obszarze "Osi" **wizualizacje** okienka.
   * Kliknij przycisk "s11" i "s11\_alert" obydwie będą wyświetlane w obszarze "Wartości". Kliknij strzałkę małych **s11** i **s11\_alert**, zmień "Sum" do "Średnia".
   * Kliknij przycisk **ZAPISAĆ** u góry i nazwa raportu "aircraftmonitor." Raport o nazwie "aircraftmonitor" jest wyświetlany w obszarze **raporty** sekcji **Nawigator** w okienku po lewej stronie.
   * Kliknij przycisk **numeru Pin Visual** ikonę w prawym górnym rogu ten wykres liniowy. Można wybrać pulpit nawigacyjny może wyświetlane okno "Numer Pin do pulpitu nawigacyjnego". Wybierz "Demo konserwacji predykcyjnej", a następnie kliknij przycisk "Numer Pin."
   * Umieść kursor myszy nad tego kafelka na pulpicie nawigacyjnym, kliknij ikonę "edit" w prawym górnym rogu, aby zmienić swoją nazwę na "floty widok vs czujnik 11. Próg 48,26" i pomocniczą, aby"Średnia między floty w czasie".

## <a name="delete-your-solution"></a>Usuwanie rozwiązania
Upewnij się, Zatrzymaj generator danych, gdy nie są aktywnie za pomocą rozwiązania uruchamiania generatora danych będą naliczane wyższe koszty. Usuń rozwiązanie, jeśli nie jest używana. Usunięcie rozwiązania spowoduje usunięcie wszystkich składników, które są udostępniane w ramach Twojej subskrypcji po wdrożeniu rozwiązania. Aby usunąć rozwiązanie, kliknij swoją nazwę rozwiązania, w lewym panelu szablon rozwiązania, a następnie kliknij przycisk **usunąć**.

## <a name="cost-estimation-tools"></a>Narzędzia oszacowanie kosztów
Następujące dwa narzędzia są dostępne lepiej zrozumieć całkowity koszt objętego uruchomiona konserwacji predykcyjnej lotniczego szablon rozwiązania w ramach Twojej subskrypcji:

* [Microsoft Azure koszt narzędzia do szacowania Tool (online)](https://azure.microsoft.com/pricing/calculator/)
* [Microsoft Azure koszt narzędzia do szacowania Tool (desktop)](http://www.microsoft.com/download/details.aspx?id=43376)

