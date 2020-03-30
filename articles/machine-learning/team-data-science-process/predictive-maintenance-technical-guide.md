---
title: Przewodnik po konserwacji predykcyjnej dla przemysłu lotniczego - Team Data Science Process
description: Przewodnik techniczny dotyczący szablonu rozwiązania do konserwacji predykcyjnej w przemyśle lotniczym, użyteczności publicznej i transporcie.
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
ms.openlocfilehash: 0542106f70e96b6c2f63e8ca03d2532de191d365
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477174"
---
# <a name="technical-guide-to-the-solution-template-for-predictive-maintenance-in-aerospace"></a>Przewodnik techniczny dotyczący szablonu rozwiązania do konserwacji predykcyjnej w przemyśle lotniczym

> [!Important]
> Ten artykuł został przestarzały. Dyskusja na temat konserwacji predykcyjnej w przemyśle lotniczym jest nadal aktualna, ale aktualne informacje można znaleźć w [omówienie rozwiązania dla odbiorców biznesowych.](https://github.com/Azure/cortana-intelligence-predictive-maintenance-aerospace)


Szablony rozwiązań zostały zaprojektowane tak, aby przyspieszyć proces tworzenia wersji demonstracyjnej E2E. Wdrożony szablon apowiony subskrypcji z niezbędnych składników, a następnie tworzy relacje między nimi. To również nasiona potoku danych z przykładowych danych z aplikacji generatora danych, które można pobrać i zainstalować na komputerze lokalnym po wdrożeniu szablonu rozwiązania. Dane z generatora nawilżają potok danych i rozpoczynają generowanie prognoz uczenia maszynowego, które następnie można wizualizować na pulpicie nawigacyjnym usługi Power BI.

Proces wdrażania prowadzi użytkownika przez kilka kroków, aby skonfigurować poświadczenia rozwiązania. Upewnij się, że rejestrujesz poświadczenia, takie jak nazwa rozwiązania, nazwa użytkownika i hasło, które są podasz podczas wdrażania. 


Celem tego artykułu są:
- Opis architektury referencyjnej i składników aprowizowanych w ramach subskrypcji.
- Zademonstruj, jak zastąpić przykładowe dane własnymi danymi. 
- Pokaż, jak zmodyfikować szablon rozwiązania.  

## <a name="overview"></a>Omówienie
![Architektura konserwacji predykcyjnej](./media/predictive-maintenance-technical-guide/predictive-maintenance-architecture.png)

Po wdrożeniu rozwiązania aktywuje usługi platformy Azure, w tym Usługi Event Hub, Analiza strumienia, usługa HDInsight, fabryka danych i uczenie maszynowe. Diagram architektury pokazuje, jak predykcyjne konserwacji dla aerospace szablon rozwiązania jest skonstruowany. Można zbadać te usługi w witrynie Azure portal, klikając je w diagramie szablonu rozwiązania utworzonego przy użyciu wdrożenia rozwiązania (z wyjątkiem usługi HDInsight, która jest aprowizowana na żądanie, gdy działania związane z rurociągiem są wymagane do uruchomienia i są usunięte później).
Pobierz [pełnowymiarową wersję diagramu](https://download.microsoft.com/download/1/9/B/19B815F0-D1B0-4F67-AED3-A40544225FD1/ca-topologies-maintenance-prediction.png).

W poniższych sekcjach opisano części rozwiązania.

## <a name="data-source-and-ingestion"></a>Źródło danych i pozyskiwanie danych
### <a name="synthetic-data-source"></a>Syntetyczne źródło danych
Dla tego szablonu używane źródło danych jest generowane z pobranej aplikacji klasycznej, która jest uruchamiana lokalnie po pomyślnym wdrożeniu.

Aby znaleźć instrukcje pobierania i instalowania tej aplikacji, wybierz pierwszy węzeł, Predictive Maintenance Data Generator, na diagramie szablonu rozwiązania. Instrukcje znajdują się na pasku Właściwości. Ta aplikacja dostarcza usługę [Azure Event Hub](#azure-event-hub) z punktami danych lub zdarzeniami, używanymi w pozostałej części przepływu rozwiązania. To źródło danych pochodzi z publicznie dostępnych danych z [repozytorium danych NASA](https://c3.nasa.gov/dashlink/resources/139/) przy użyciu [zestawu danych symulacji degradacji silnika Turbofan.](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan)

Aplikacja do generowania zdarzeń wypełnia usługi Azure Event Hub tylko podczas wykonywania na komputerze.  

### <a name="azure-event-hub"></a>Centrum zdarzeń Azure  
Usługa [Azure Event Hub](https://azure.microsoft.com/services/event-hubs/) jest odbiorcą danych wejściowych dostarczonych przez syntetyczne źródło danych.

## <a name="data-preparation-and-analysis"></a>Przygotowanie i analiza danych  
### <a name="azure-stream-analytics"></a>Azure Stream Analytics
Użyj [usługi Azure Stream Analytics,](https://azure.microsoft.com/services/stream-analytics/) aby zapewnić niemal analizę w czasie rzeczywistym na strumień wejściowy z usługi Azure Event [Hub.](#azure-event-hub) Następnie publikujesz wyniki na pulpicie nawigacyjnym [usługi Power BI,](https://powerbi.microsoft.com) a także archiwizujesz wszystkie nieprzetworzone zdarzenia przychodzące do usługi [Azure Storage](https://azure.microsoft.com/services/storage/) w celu późniejszego przetworzenia przez usługę Azure [Data Factory.](https://azure.microsoft.com/documentation/services/data-factory/)

### <a name="hdinsight-custom-aggregation"></a>Agregacja niestandardowa usługi HDInsight
Uruchom skrypty [hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) (zaaranżowane przez usługę Azure Data Factory) przy użyciu usługi HDInsight w celu zapewnienia agregacji nieprzetworzonych zdarzeń zarchiwizowanych przy użyciu zasobu usługi Azure Stream Analytics.

### <a name="azure-machine-learning"></a>Azure Machine Learning
Wprowadź prognozy dotyczące pozostałego okresu użytkowania (RUL) określonego aparatu samolotu przy użyciu danych wejściowych otrzymanych za pomocą [usługi Azure Machine Learning Service](https://azure.microsoft.com/services/machine-learning/) (zaaranżowanej przez usługę Azure Data Factory). 

## <a name="data-publishing"></a>Publikowanie danych
### <a name="azure-sql-database"></a>Azure SQL Database
Użyj [usługi Azure SQL Database](https://azure.microsoft.com/services/sql-database/) do przechowywania prognoz odebranych przez usługę Azure Machine Learning, które są następnie używane na pulpicie nawigacyjnym usługi Power [BI.](https://powerbi.microsoft.com)

## <a name="data-consumption"></a>Użycie danych
### <a name="power-bi"></a>Power BI
Użyj [usługi Power BI,](https://powerbi.microsoft.com) aby wyświetlić pulpit nawigacyjny zawierający agregacje i alerty dostarczane przez [usługę Azure Stream Analytics,](https://azure.microsoft.com/services/stream-analytics/)a także prognozy RUL przechowywane w [bazie danych SQL azure,](https://azure.microsoft.com/services/sql-database/) które zostały wyprodukowane przy użyciu [usługi Azure Machine Learning.](https://azure.microsoft.com/services/machine-learning/)

## <a name="how-to-bring-in-your-own-data"></a>Jak przywieźć własne dane
W tej sekcji opisano sposób wprowadzania własnych danych na platformę Azure i jakie obszary wymagają zmian dla danych, które wprowadzają do tej architektury.

Jest mało prawdopodobne, że zestaw danych jest zgodny z zestawem danych używanym przez [zestaw danych symulacji degradacji silnika Turbofan](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan) używany dla tego szablonu rozwiązania. Zrozumienie danych i wymagań ma kluczowe znaczenie dla sposobu modyfikowania tego szablonu w celu pracy z własnymi danymi. 

W poniższych sekcjach omówiono części szablonu, które wymagają modyfikacji po wprowadzeniu nowego zestawu danych.

### <a name="azure-event-hub"></a>Centrum zdarzeń Azure
Usługa Azure Event Hub jest ogólna; dane mogą być księgowane w centrum w formacie CSV lub JSON. W usłudze Azure Event Hub nie ma specjalnego przetwarzania, ale ważne jest, aby zrozumieć dane, które są do niego wprowadzane.

W tym dokumencie nie opisano sposobu pozyskiwania danych, ale można łatwo wysyłać zdarzenia lub dane do usługi Azure Event Hub przy użyciu interfejsów API Centrum zdarzeń.

### <a name="azure-stream-analytics"></a><a name="azure-stream-analytics-1"></a>Azure Stream Analytics
Za pomocą zasobu usługi Azure Stream Analytics można udostępniać analizy w czasie zbliżonym do rzeczywistego, odczytując strumienie danych i przesyłając dane do dowolnej liczby źródeł.

W przypadku szablonu rozwiązania predictive maintenance dla organizacji lotniczych zapytanie usługi Azure Stream Analytics składa się z czterech zapytań podrzędnych, z których każda kwerenda zużywa zdarzenia z usługi Azure Event Hub, z danymi wyjściowymi do czterech różnych lokalizacji. Te dane wyjściowe składają się z trzech zestawów danych usługi Power BI i jednej lokalizacji usługi Azure Storage.

Zapytanie usługi Azure Stream Analytics można znaleźć przez:

* Łączenie się z witryną Azure portal
* Lokalizowanie ikony](./media/predictive-maintenance-technical-guide/icon-stream-analytics.png) ![usługi Stream Analytics, które zostały wygenerowane podczas wdrażania rozwiązania *(na przykład* **maintenancesa02asapbi** i **maintenancesa02asablob** dla rozwiązania do konserwacji predykcyjnej)
* Wybieranie
  
  * ***INPUTS,*** aby wyświetlić dane wejściowe kwerendy
  * ***ZAPYTANIE,*** aby wyświetlić samą kwerendę
  * ***WYJŚCIA,*** aby wyświetlić różne wyjścia

Informacje o konstrukcji zapytań usługi Azure Stream Analytics można znaleźć w [odwołaniu do kwerendy usługi Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) w usłudze MSDN.

W tym rozwiązaniu kwerendy wyprowadzają trzy zestawy danych z informacjami analitycznymi w czasie zbliżonymi do rzeczywistego o przychodzącym strumieniu danych do pulpitu nawigacyjnego usługi Power BI dostarczonego jako część tego szablonu rozwiązania. Ponieważ istnieje niejawna wiedza na temat formatu danych przychodzących, te zapytania muszą zostać zmienione na podstawie formatu danych.

Kwerenda w drugim zadaniu usługi Usługi Stream Analytics **maintenancesa02asablob** po prostu wyprowadza wszystkie zdarzenia [usługi Event Hub](https://azure.microsoft.com/services/event-hubs/) do usługi Azure [Storage](https://azure.microsoft.com/services/storage/) i dlatego nie wymaga żadnych zmian niezależnie od formatu danych, ponieważ pełne informacje o zdarzeniu są przesyłane strumieniowo do magazynu.

### <a name="azure-data-factory"></a>Azure Data Factory
Usługa [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) organizuje przenoszenie i przetwarzanie danych. W szablonie predykcyjnej konserwacji rozwiązań dla przemysłu lotniczego fabryka danych składa się z trzech [potoków,](../../data-factory/concepts-pipelines-activities.md) które przenoszą i przetwarzają dane przy użyciu różnych technologii.  Uzyskaj dostęp do fabryki danych, otwierając węzeł Fabryka danych u dołu diagramu szablonu rozwiązania utworzonego przy wdrażaniu rozwiązania. Błędy w zestawach danych są spowodowane fabryki danych wdrażane przed rozpoczęciem generatora danych. Błędy te mogą być ignorowane i nie uniemożliwiają działania fabryki danych.

![Błędy zestawu danych Data Factory](./media/predictive-maintenance-technical-guide/data-factory-dataset-error.png)

W tej sekcji omówiono niezbędne [potoki i działania](../../data-factory/concepts-pipelines-activities.md) zawarte w [usłudze Azure Data Factory.](https://azure.microsoft.com/documentation/services/data-factory/) Oto widok diagramu rozwiązania.

![Azure Data Factory](./media/predictive-maintenance-technical-guide/azure-data-factory.png)

Dwa potoki tej fabryki zawierają skrypty [hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) używane do partycjonowania i agregowania danych. Po zauważeniu skrypty znajdują się na koncie [usługi Azure Storage](https://azure.microsoft.com/services/storage/) utworzonym podczas instalacji. Ich lokalizacja to: maintenancesascript\\\\script\\\\hive\\ \\ (lub https://[Nazwa rozwiązania].blob.core.windows.net/maintenancesascript).

Podobnie jak w przypadku zapytań [usługi Azure Stream Analytics](#azure-stream-analytics-1) skrypty [hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) mają niejawną wiedzę na temat formatu danych przychodzących i muszą zostać zmienione na podstawie formatu danych.

#### <a name="aggregateflightinfopipeline"></a>*AggregateFlightInfoPipeline*
Ten [potok](../../data-factory/concepts-pipelines-activities.md) zawiera pojedyncze działanie — działanie [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) przy użyciu [usługi HDInsightLinkedService,](https://msdn.microsoft.com/library/azure/dn893526.aspx) która uruchamia skrypt [hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) do partycjonowania danych umieszczonych w [usłudze Azure Storage](https://azure.microsoft.com/services/storage/) podczas zadania usługi Azure Stream [Analytics.](https://azure.microsoft.com/services/stream-analytics/)

Skrypt [gałęzi](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) dla tego zadania partycjonowania to ***AggregateFlightInfo.hql***

#### <a name="mlscoringpipeline"></a>*MLScoringPipeline*
Ten [potok](../../data-factory/concepts-pipelines-activities.md) zawiera kilka działań, których wynikiem końcowym jest ocenione prognozy z eksperymentu [usługi Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) skojarzone z tym szablonem rozwiązania.

Działania wliczone w cenę to:

* [Działanie HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) przy użyciu [usługi HDInsightLinkedService,](https://msdn.microsoft.com/library/azure/dn893526.aspx) która uruchamia skrypt [hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) do wykonywania agregacji i inżynierii funkcji niezbędnych do eksperymentu [usługi Azure Machine Learning.](https://azure.microsoft.com/services/machine-learning/)
  Skrypt [gałęzi](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) dla tego zadania partycjonowania jest ***PrepareMLInput.hql***.
* [Kopiuj](https://msdn.microsoft.com/library/azure/dn835035.aspx) działanie, które przenosi wyniki z działania [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) do pojedynczego obiektu blob [usługi Azure Storage,](https://azure.microsoft.com/services/storage/) do który uzyskuje dostęp przez działanie [AzureMLBatchScoring.](https://msdn.microsoft.com/library/azure/dn894009.aspx)
* Działanie [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) wywołuje eksperyment [usługi Azure Machine Learning,](https://azure.microsoft.com/services/machine-learning/) z wynikami umieszczonymi w jednym obiekcie blob [usługi Azure Storage.](https://azure.microsoft.com/services/storage/)

#### <a name="copyscoredresultpipeline"></a>*CopyScoredResultPipeline*
Ten [potok](../../data-factory/concepts-pipelines-activities.md) zawiera pojedyncze działanie — [skopaj](https://msdn.microsoft.com/library/azure/dn835035.aspx) działanie, które przenosi wyniki eksperymentu [usługi Azure Machine Learning](#azure-machine-learning) z narzędzia ***MLScoringPipeline*** do [bazy danych SQL platformy Azure](https://azure.microsoft.com/services/sql-database/) aprowizowanych w ramach instalacji szablonu rozwiązania.

### <a name="azure-machine-learning"></a>Azure Machine Learning
Eksperyment [usługi Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) używany dla tego szablonu rozwiązania zapewnia pozostały okres użytkowania (RUL) aparatu samolotu. Eksperyment jest specyficzny dla zestawu danych używane i wymaga modyfikacji lub zastąpienia specyficzne dla danych wniesionych.

## <a name="monitor-progress"></a>Monitorowanie postępu
Po uruchomieniu generatora danych potok zaczyna się odwadniać, a różne składniki rozwiązania zaczynają uruchamiać działanie zgodnie z poleceniami wydanymi przez fabrykę danych. Istnieją dwa sposoby monitorowania potoku.

* Jedno z zadań usługi Stream Analytics zapisuje nieprzetworzone dane przychodzące do magazynu obiektów blob. Jeśli klikniesz składnik magazynu obiektów Blob rozwiązania z ekranu, który pomyślnie wdrożono rozwiązanie, a następnie kliknij przycisk Otwórz w prawym panelu, przeniesie Cię do [witryny Azure portal](https://portal.azure.com/). Tam kliknij na Blobs. W następnym panelu zobaczysz listę kontenerów. Kliknij **na maintenancesadata**. W następnym panelu znajduje się folder **rawdata.** Wewnątrz folderu rawdata znajdują się foldery o nazwach takich jak hour=17 i hour=18. Obecność tych folderów wskazuje, że nieprzetworzone dane są generowane na komputerze i przechowywane w magazynie obiektów blob. W tych folderach powinny być widoczne pliki csv o skończonej wielkości w MB.
* Ostatnim krokiem potoku jest zapisanie danych (na przykład prognoz z uczenia maszynowego) w bazie danych SQL. Może być czekać maksymalnie trzy godziny na dane pojawiają się w bazie danych SQL. Jednym ze sposobów monitorowania ilości danych dostępnych w bazie danych SQL jest portal [Azure.](https://portal.azure.com/) Na lewym panelu znajdź ikonę ![](./media/predictive-maintenance-technical-guide/icon-SQL-databases.png) SQL DATABASES SQL i kliknij ją. Następnie zlokalizuj swoją bazę danych **pmaintenancedb** i kliknij na nią. Na następnej stronie na dole kliknij MANAGE.
   
    ![Ikona Zarządzaj](./media/predictive-maintenance-technical-guide/icon-manage.png)
   
    W tym miejscu możesz kliknąć nowe zapytanie i zapytać o liczbę wierszy (na przykład wybierz count(*) z PMResult). Wraz z rozwojem bazy danych liczba wierszy w tabeli powinna wzrosnąć.

## <a name="power-bi-dashboard"></a>Pulpit nawigacyjny usługi Power BI

Skonfiguruj pulpit nawigacyjny usługi Power BI, aby wizualizować dane usługi Azure Stream Analytics (gorącą ścieżkę) i wyniki przewidywania partii z uczenia maszynowego platformy Azure (ścieżka zimna).

### <a name="set-up-the-cold-path-dashboard"></a>Konfigurowanie pulpitu nawigacyjnego ścieżki zimnej
W potoku danych o zimnej ścieżce celem jest uzyskanie predykcyjnego RUL (pozostały okres użytkowania) każdego silnika samolotu po zakończeniu lotu (cykl). Wynik prognozy jest aktualizowany co 3 godziny w celu przewidywania silników samolotów, które zakończyły lot w ciągu ostatnich 3 godzin.

Usługa Power BI łączy się z bazą danych SQL Azure jako źródłem danych, w którym są przechowywane wyniki prognozowania. 

Uwaga: 
1.    Po wdrożeniu rozwiązania przewidywanie pojawi się w bazie danych w ciągu 3 godzin. Plik pbix, który został do pobrania generatora zawiera pewne dane źródłowe, dzięki czemu można od razu utworzyć pulpit nawigacyjny usługi Power BI. 
2.    W tym kroku warunkiem jest pobranie i zainstalowanie bezpłatnego oprogramowania [power bi desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/).

Poniższe kroki prowadzą do sposobu podłączenia pliku pbix do bazy danych SQL, która została spun up w momencie wdrażania rozwiązania zawierające dane (na przykład wyniki przewidywania) do wizualizacji.

1. Pobierz poświadczenia bazy danych.
   
   Przed przejściem do kolejnych kroków należy wykonać **nazwę serwera bazy danych, nazwę bazy danych, nazwę użytkownika i hasło.** Oto kroki, które poprowadzą Cię, jak je znaleźć.
   
   * Gdy **"Azure SQL Database"** na diagramie szablonu rozwiązania zmieni kolor na zielony, kliknij go, a następnie kliknij przycisk **"Otwórz"**.
   * Zobaczysz nową kartę/okno przeglądarki, w której jest wyświetlana strona portalu Azure. Kliknij **przycisk "Grupy zasobów"** w lewym panelu.
   * Wybierz subskrypcję używaną do wdrażania rozwiązania, a następnie wybierz **opcję\_"YourSolutionName ResourceGroup"**.
   * W nowym panelu wyskakującym](./media/predictive-maintenance-technical-guide/icon-sql.png) kliknij ikonę ikony ![SQL, aby uzyskać dostęp do bazy danych. Nazwa bazy danych znajduje się obok tej ikony (na przykład **'pmaintenancedb'),** a **nazwa serwera bazy danych** jest wyświetlana w ramach właściwości Nazwa serwera i powinna wyglądać podobnie do **YourSolutionName.database.windows.net**.
   * Nazwa **użytkownika** i **hasło** bazy danych są takie same jak nazwa użytkownika i hasło wcześniej zarejestrowane podczas wdrażania rozwiązania.
2. Zaktualizuj źródło danych pliku raportu ścieżki zimnej za pomocą programu Power BI Desktop.
   
   * W folderze, w którym pobrano i rozpakowałeś plik Generatora, kliknij dwukrotnie plik **\\PowerBI PredictiveMaintenanceAero0space.pbix.** Jeśli podczas otwierania pliku są widoczne komunikaty ostrzegawcze, zignoruj je. W górnej części pliku kliknij przycisk **"Edytuj kwerendy"**.
     
     ![Edytuj zapytania](./media/predictive-maintenance-technical-guide/edit-queries.png)
   * Zobaczysz dwie tabele, **RemainingUsefulLife** i **PMResult**. Wybierz pierwszą tabelę ![i](./media/predictive-maintenance-technical-guide/icon-query-settings.png) kliknij ikonę Ustawienia kwerendy obok **pozycji "Źródło"** w **sekcji "ZASTOSOWANE KROKI"** po prawej stronie panelu **"Ustawienia kwerendy".** Zignoruj wszystkie komunikaty ostrzegawcze, które się pojawią.
   * W wyskakującym oknie zastąp **"Serwer"** i **"Baza danych"** własnymi nazwami serwerów i baz danych, a następnie kliknij przycisk **"OK".** W przypadku nazwy serwera należy określić port 1433 (**YourSolutionName.database.windows.net, 1433**). Pozostaw pole Baza danych jako **pmaintenancedb**. Zignoruj komunikaty ostrzegawcze wyświetlane na ekranie.
   * W następnym wyskakującym oknie zobaczysz dwie opcje w lewym okienku (**Windows** i **Baza danych**). Kliknij **"Baza danych"**, wypełnij **swoją "Nazwę użytkownika"** i **"Hasło"** (nazwę użytkownika i hasło wprowadzone podczas pierwszego wdrożenia rozwiązania i utworzenia bazy danych SQL Azure). W ***obszarze Wybierz poziom, który ma być stosowany do***opcji poziomu bazy danych. Następnie kliknij **przycisk "Połącz"**.
   * Kliknij na drugą tabelę **PMResult** następnie ![kliknij ikonę](./media/predictive-maintenance-technical-guide/icon-navigation.png) nawigacji obok **"Źródło"** w sekcji **"Zastosowane kroki"** po prawej stronie panelu **"Ustawienia kwerendy"** i zaktualizuj nazwy serwerów i baz danych, jak w powyższych krokach i kliknij PRZYCISK OK.
   * Po przejściu z powrotem do poprzedniej strony zamknij okno. Zostanie wyświetlony komunikat - kliknij przycisk **Zastosuj**. Na koniec kliknij przycisk **Zapisz,** aby zapisać zmiany. Plik usługi Power BI nawiązał połączenie z serwerem. Jeśli wizualizacje są puste, zaznacz zaznaczenia wizualizacji, aby zwizualizować wszystkie dane, kliknij ikonę gumki w prawym górnym rogu legend. Użyj przycisku odświeżania, aby odzwierciedlić nowe dane dotyczące wizualizacji. Początkowo dane źródłowe są wyświetlane tylko w wizualizacjach, ponieważ fabryka danych jest zaplanowana do odświeżania co 3 godziny. Po 3 godzinach zobaczysz nowe prognozy odzwierciedlone w wizualizacjach podczas odświeżania danych.
3. (Opcjonalnie) Opublikuj pulpit nawigacyjny ścieżki zimnej w usłudze [Power BI online](https://www.powerbi.com/). Ten krok wymaga konta usługi Power BI (lub konta usługi Office 365).
   
   * Kliknij **przycisk "Publikuj",** a kilka sekund później pojawi się okno z komunikatem "Publikowanie w usłudze Power BI Sukces!" zielonym znacznikiem wyboru. Kliknij łącze poniżej "Otwórz PredictiveMaintenanceAerospace.pbix w usłudze Power BI". Aby znaleźć szczegółowe instrukcje, zobacz [Publikowanie z programu Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).
   * Aby utworzyć nowy pulpit **+** nawigacyjny: kliknij znak obok sekcji **Pulpity nawigacyjne** w lewym okienku. Wprowadź nazwę "Predykcyjne demo konserwacji" dla tego nowego pulpitu nawigacyjnego.
   * Po otwarciu raportu ![kliknij](./media/predictive-maintenance-technical-guide/icon-pin.png) ikonę NUMERU PIN, aby przypiąć wszystkie wizualizacje do pulpitu nawigacyjnego. Aby znaleźć szczegółowe [instrukcje, zobacz Przypinanie kafelka do pulpitu nawigacyjnego usługi Power BI z raportu](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report).
     Przejdź do strony pulpitu nawigacyjnego i dostosuj rozmiar i lokalizację wizualizacji oraz edytuj ich tytuły. Aby znaleźć szczegółowe instrukcje dotyczące edytowania kafelków, zobacz [Edytowanie kafelka — zmienianie rozmiaru, przenoszenie, zmienianie nazwy, przypinanie, usuwanie, dodawanie hiperłącza](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Oto przykładowy pulpit nawigacyjny z przypiętymi do niego wizualizacjami ścieżek zimnych.  W zależności od tego, jak długo uruchamiasz generator danych, liczby na wizualizacjach mogą się różnić.
     <br/>
     ![Widok końcowy](./media/predictive-maintenance-technical-guide/final-view.png)
     <br/>
   * Aby zaplanować odświeżanie danych, umieść wskaźnik myszy na zestawie danych **PredictiveMaintenanceAerospace,** kliknij ![ikonę](./media/predictive-maintenance-technical-guide/icon-elipsis.png) Wielokropek, a następnie wybierz pozycję **Zaplanuj odświeżanie**.
     <br/>
     > [!NOTE]
     > Jeśli zostanie wyświetlony komunikat ostrzegawczy, kliknij pozycję **Edytuj poświadczenia** i upewnij się, że poświadczenia bazy danych są takie same, jak te opisane w kroku 1.
     <br/>
     ![Planowanie odświeżania](./media/predictive-maintenance-technical-guide/schedule-refresh.png)
     <br/>
   * Rozwiń sekcję **Planowanie odświeżania.** Włącz "dbaj o aktualną datę swoich danych".
     <br/>
   * Zaplanuj odświeżanie w zależności od potrzeb. Aby uzyskać więcej informacji, zobacz [Odświeżanie danych w usłudze Power BI](https://support.powerbi.com/knowledgebase/articles/474669-data-refresh-in-power-bi).

### <a name="setup-hot-path-dashboard"></a>Konfigurowanie pulpitu nawigacyjnego ścieżki sieciowej
Poniższe kroki poprowadzą Cię do wizualizacji danych wyjściowych z zadań usługi Stream Analytics, które zostały wygenerowane w momencie wdrażania rozwiązania. Do wykonania następujących czynności wymagane jest konto [usługi Power BI online.](https://www.powerbi.com/) Jeśli nie masz konta, możesz [je utworzyć.](https://powerbi.microsoft.com/pricing)

1. Dodaj dane wyjściowe usługi Power BI w usłudze Azure Stream Analytics (ASA).
   
   * Należy postępować zgodnie z instrukcjami w [usłudze Azure Stream Analytics & usługi Power BI: pulpit nawigacyjny analizy,](../../stream-analytics/stream-analytics-power-bi-dashboard.md) aby uzyskać wgląd danych strumieniowych w czasie rzeczywistym, aby skonfigurować dane wyjściowe zadania usługi Azure Stream Analytics jako pulpit nawigacyjny usługi Power BI.
   * Zapytanie ASA ma trzy wyjścia, które są **aircraftmonitor**, **aircraftalert**i **flightsbyhour**. Kwerendę można wyświetlić, klikając kartę kwerendy. Po dodaniu pierwszego wyjścia **(aircraftmonitor)** upewnij się, że **alias wyjściowy**, **nazwa zestawu danych** i nazwa **tabeli** są takie same **(aircraftmonitor).** Powtórz kroki, aby dodać wyjścia dla **aircraftalert**i **flightsbyhour**. Po dodaniu wszystkich trzech tabel wyjściowych i uruchomieniu zadania ASA powinien zostać wyświetlony komunikat potwierdzający ("Uruchamianie usługi obsługi usługi Stream Analytics maintenancesa02asapbi zakończyło się pomyślnie").
2. Zaloguj się do [usługi Power BI w trybie online](https://www.powerbi.com)
   
   * W sekcji Zestawy danych po lewej stronie w obszarze Mój obszar roboczy powinien pojawić się ***zestaw danych,*** które **wymienią aircraftmonitor**, **aircraftalert**i **flightsbyhour.** Jest to dane przesyłania strumieniowego wypchnięte z usługi Azure Stream Analytics w poprzednim kroku. Dataset **flightsbyhour** może nie być wyświetlane w tym samym czasie co pozostałe dwa zestawy danych ze względu na charakter kwerendy SQL za nim. Jednak powinien pokazać się po godzinie.
   * Upewnij się, że okienko ***Wizualizacje*** jest otwarte i jest wyświetlane po prawej stronie ekranu.
3. Po przejściu danych do usługi Power BI można rozpocząć wizualizację danych przesyłania strumieniowego. Poniżej znajduje się przykładowy pulpit nawigacyjny z przypiętymi do niego wizualizacjami ścieżek gorących. Można tworzyć inne kafelki pulpitu nawigacyjnego na podstawie odpowiednich zestawów danych. W zależności od tego, jak długo uruchamiasz generator danych, liczby na wizualizacjach mogą się różnić.

    ![Widok pulpitu nawigacyjnego](media/predictive-maintenance-technical-guide/dashboard-view.png)

1. Oto kilka kroków do utworzenia jednego z powyższych kafelków – płytki "Widok floty czujnika 11 a próg 48,26":
   
   * Kliknij zestaw danych **aircraftmonitor** w sekcji zestawy danych po lewej stronie panelu.
   * Kliknij ikonę **Wykres liniowy.**
   * Kliknij **pozycję Przetworzone** w okienku **Pola,** tak aby było wyświetlane w obszarze "Oś" w okienku **Wizualizacje.**
   * Kliknij "s11" i "s11\_alert", aby oba były wyświetlane w obszarze "Wartości". Kliknij małą strzałkę obok **alertu s11** i **s11\_**, zmień "Suma" na "Średnia".
   * Kliknij **przycisk ZAPISZ** na górze i nazwij raport "aircraftmonitor". Raport o nazwie "aircraftmonitor" jest wyświetlany w sekcji **Raporty** w okienku **Nawigator** po lewej stronie.
   * Kliknij ikonę **Przypnij wizualizację** w prawym górnym rogu tego wykresu liniowego. Okno "Przypnij do pulpitu nawigacyjnego" może być wyświetlane, aby wybrać pulpit nawigacyjny. Wybierz "Predykcyjne demo konserwacji", a następnie kliknij "Przypnij".
   * Najedź kursorem myszy na ten kafelek na pulpicie nawigacyjnym, kliknij ikonę "edytuj" w prawym górnym rogu, aby zmienić jej tytuł na "Widok floty czujnika 11 vs. próg 48,26" i napisy na "Średnia w całej flocie w czasie".

## <a name="delete-your-solution"></a>Usuwanie rozwiązania
Upewnij się, że zatrzymać generator danych, gdy nie aktywnie przy użyciu rozwiązania jako uruchomienie generatora danych poniesie wyższe koszty. Usuń rozwiązanie, jeśli go nie używasz. Usunięcie rozwiązania usuwa wszystkie składniki aprowizacji w ramach subskrypcji po wdrożeniu rozwiązania. Aby usunąć rozwiązanie, kliknij nazwę rozwiązania w lewym panelu szablonu rozwiązania, a następnie kliknij pozycję **Usuń**.

## <a name="cost-estimation-tools"></a>Narzędzia do szacowania kosztów
Dostępne są następujące dwa narzędzia, które pomogą Ci lepiej zrozumieć całkowite koszty związane z uruchomieniem szablonu rozwiązania predictive maintenance for Aerospace w ramach subskrypcji:

* [Narzędzie do szacowania kosztów platformy Microsoft Azure (w trybie online)](https://azure.microsoft.com/pricing/calculator/)
* [Narzędzie do szacowania kosztów platformy Microsoft Azure (na komputerze)](https://www.microsoft.com/download/details.aspx?id=43376)

