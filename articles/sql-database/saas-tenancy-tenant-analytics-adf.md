---
title: Wykonywania kwerend analytics do dzierżawy baz danych przy użyciu usługi Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Zapytania analityczne między dzierżawcy przy użyciu danych pobranych z wielu baz danych z bazy danych SQL Azure.
keywords: samouczek usługi sql database
services: sql-database
documentationcenter: ''
author: anumjs
manager: craigg
editor: MightyPen
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/08/2017
ms.author: anjangsh
ms.openlocfilehash: c7580e5481288695d3b5dea8fd0547f5f2c4c2b0
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34644005"
---
# <a name="explore-saas-analytics-with-azure-sql-database-sql-data-warehouse-data-factory-and-power-bi"></a>Eksploruj SaaS analizy w usłudze baza danych SQL Azure SQL Data Warehouse, fabryki danych i usługi Power BI

W tym samouczku opisano pośrednictwem analytics end-to-end. Scenariuszu pokazano, jak analiza danych dzierżawy dają możliwość dostawców oprogramowania do podejmowania decyzji inteligentne. Przy użyciu danych pobranych z każdej dzierżawy bazy danych, użyj analytics Aby uzyskać wgląd w działanie dzierżawy, w tym ich wykorzystania przykładowej aplikacji SaaS biletów Wingtip. Ten scenariusz obejmuje trzy kroki: 

1.  **Wyodrębnianie danych** z każdej dzierżawy bazy danych do magazynu analityka w takim przypadku magazyn danych SQL.
2.  **Optymalizacja wyodrębnione dane** przetwarzania analytics.
3.  Użyj **Business Intelligence** narzędzi do rysowania limit przydatne informacje na temat technologii, które można kierować podejmowania decyzji. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> - Tworzenie dzierżawy, który analityka magazynu dla ładowania.
> - Fabryka danych Azure (ADF) umożliwia wyodrębniania danych z każdej dzierżawy bazy danych do magazynu danych analiz.
> - Optymalizuj wyodrębnione dane (z opcją reorganize na schemat gwiazdy).
> - Wyślij zapytanie do analityka magazynu danych.
> - Użyj usługi Power BI do wizualizacji danych zaznacz trendów w danych dzierżawy i zalecenia dotyczące poprawy.

![architectureOverView](media/saas-tenancy-tenant-analytics/adf_overview.png)

## <a name="analytics-over-extracted-tenant-data"></a>Analiza danych wyodrębnionych dzierżawy

Aplikacji SaaS utrzymywanie potencjalnie ogromnych ilości danych dzierżawcy w chmurze. Te dane mogą zapewnić sformatowanego źródła szczegółowych informacji o działaniu i użycia aplikacji i zachowania dzierżawcom. Te szczegółowe informacje można przeprowadzają opracowanie funkcji, ulepszenia użyteczność i innych inwestycji w aplikacji i platform.

Dostęp do danych dla wszystkich dzierżawców jest proste, gdy wszystkie dane są tylko jeden wielodostępne bazy danych. Ale dostępu jest bardziej złożony, gdy na dużą skalę dystrybuowana do tysięcy baz danych. Jest jednym ze sposobów tame złożoność wyodrębnić dane do bazy danych analizy lub hurtowni danych dla zapytania.

Ten samouczek przedstawia scenariusz aplikacji biletów Wingtip analytics end-to-end. Najpierw [fabryki danych Azure (ADF)](../data-factory/introduction.md) służy jako narzędzie orchestration można wyodrębnić biletów sprzedaży oraz powiązanych danych z każdej dzierżawy bazy danych. Te dane została załadowana do tabel przemieszczania w magazynie usługi analytics. Magazyn analytics może być bazy danych SQL lub SQL Data Warehouse. W tym samouczku używana [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) jako analytics przechowywania.

Następnie wyodrębnione dane są przekształcane i ładowane do zestawu [schematu gwiazdy](https://www.wikipedia.org/wiki/Star_schema) tabel. Tabele składają się z tabeli faktów centralnej plus tabele wymiarów pokrewne:

- Tabela faktów centralnej w schemacie gwiazdkę zawiera dane biletu.
- Tabele wymiarów zawierają dane dotyczące miejsc, zdarzenia, klientów i zakupu daty.

Razem Środkowej i wydajne przetwarzania analitycznego wymiaru tabel Włącz. Schemat gwiazdy używane w tym samouczku jest wyświetlane na poniższej ilustracji:
 
![architectureOverView](media/saas-tenancy-tenant-analytics/starschematables.JPG)

Na koniec proszeni są tabele schematu gwiazdy. Wyniki zapytania są wyświetlane w sposób wizualny przy użyciu usługi Power BI aby wyróżnić wgląd w zachowanie dzierżawcy i ich użycia w aplikacji. Z tego schematu gwiazdy możesz uruchomić zapytania, które udostępniają:

- Kto jest kupowanie biletów i z których miejsce.
- Wzorce i trendy sprzedaży biletów.
- Względne popularne każdego miejsca.

Ten samouczek zawiera podstawowe przykłady szczegółowe informacje, które można zgromadzone dane Wingtip biletów. Zrozumienie, jak każda właściwość jest używana usługa może spowodować dostawcy biletów Wingtip myśleć o ukierunkowanych na bardziej lub mniej active miejsc, na przykład planów innej usługi. 

## <a name="setup"></a>Konfiguracja

### <a name="prerequisites"></a>Wymagania wstępne

> [!NOTE]
> Ten samouczek używa funkcji fabryki danych Azure, które są obecnie w podglądzie ograniczone (parameterization połączonej usługi). Jeśli chcesz zrobić w tym samouczku, podaj identyfikator subskrypcji [tutaj](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxrVywox1_tHk9wgd5P8SVJUNlFINjNEOElTVFdMUEREMjVVUlJCUDdIRyQlQCN0PWcu). Wyślemy potwierdzenie zaraz po włączeniu subskrypcji.

Do wykonania zadań opisanych w tym samouczku niezbędne jest spełnienie następujących wymagań wstępnych:
- Aplikacja Wingtip biletów SaaS bazy danych dla dzierżawy jest wdrożona. Aby wdrożyć w mniej niż 5 minut, zobacz [wdrażania i aplikacji Wingtip SaaS](saas-dbpertenant-get-started-deploy.md).
- Skrypty Wingtip biletów SaaS bazy danych dla dzierżawy i aplikacji [kod źródłowy](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/) są pobierane z usługi GitHub. Zobacz instrukcje pobierania. Upewnij się, *odblokować plik zip* przed wyodrębniania jego zawartość.
- Power BI Desktop jest zainstalowany. [Pobierz program Power BI Desktop](https://powerbi.microsoft.com/downloads/).
- Zainicjowano partii dodatkowi dzierżawcy, zobacz [ **samouczek dzierżaw udostępniania**](saas-dbpertenant-provision-and-catalog.md).

### <a name="create-data-for-the-demo"></a>Utwórz dane dla pokaz

W tym samouczku Eksploruje analytics nad danymi sprzedaży biletów. W tym kroku dane biletu generuje dla wszystkich dzierżawców. W kolejnym kroku to dane są wyodrębniane do analizy. _Upewnij się, udostępnione partii dzierżaw_ (opisanej wcześniej), dzięki czemu masz za mało danych do ujawnia szereg różnych wzorców zakupów biletu.

1. Otwórz w programie PowerShell ISE *...\Learning Modules\Operational Analytics\Tenant Analytics DW\Demo-TenantAnalyticsDW.ps1*i ustawić następującą wartość:
    - **$DemoScenario** = **1** zakupu biletów zdarzeń na wszystkich miejsc
2. Naciśnij klawisz **F5** uruchomić skrypt i utworzyć bilet zakupu historii dla wszystkich miejsc. Z dzierżawcami 20 skrypt generuje dziesiątki tysięcy biletów i może potrwać 10 minut lub dłużej.

### <a name="deploy-sql-data-warehouse-data-factory-and-blob-storage"></a>Wdrażanie magazynu danych SQL, fabryki danych i magazynu obiektów Blob 
W aplikacji biletów Wingtip umowę transakcyjne dane są przesyłane przez wiele baz danych. Fabryka danych Azure (ADF) jest używany do organizowania wyodrębniania, obciążenia i przekształcenie (ELT) tych danych do magazynu danych. Aby załadować dane do usługi SQL Data Warehouse najbardziej wydajnie, wyodrębnia dane do plików pośrednich obiektu blob ADF, a następnie używa [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/design-elt-data-loading) do ładowania danych do magazynu danych.   

W tym kroku wdrożyć dodatkowe zasoby używane w samouczku: Magazyn danych SQL o nazwie _tenantanalytics_, fabryki danych Azure o nazwie _dbtodwload -\<użytkownika\>_  , i konto magazynu platformy Azure o nazwie _wingtipstaging\<użytkownika\>_. Konto magazynu jest używana do tymczasowego przechowywania danych wyodrębnionych plików jako obiekty BLOB, przed załadowaniem do magazynu danych. Ten krok również wdraża schemat magazynu danych i definiuje potoki ADF, które organizowania ELT procesu.
1. Otwórz w programie PowerShell ISE *...\Learning Modules\Operational Analytics\Tenant Analytics DW\Demo-TenantAnalyticsDW.ps1* i ustaw:
    - **$DemoScenario** = **2** wdrożenia magazynu danych analiz dzierżawy, magazynu obiektów blob i fabryki danych 
1. Naciśnij klawisz **F5** Uruchom pokaz skryptu i wdrażanie zasobów platformy Azure. 

Teraz sprawdzić wdrożone zasobów platformy Azure:
#### <a name="tenant-databases-and-analytics-store"></a>Magazyn baz danych i analiza dzierżawy
Użyj [programu SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) do nawiązania połączenia **tenants1-dpt -&lt;użytkownika&gt;**  i **katalogu-dpt -&lt;użytkownika&gt;**  serwerów. Zastąp &lt;użytkownika&gt; o wartości używane w przypadku wdrażania aplikacji. Użyj logowania = *developer* i hasło = *P@ssword1*. Zobacz [Samouczek wprowadzający](saas-dbpertenant-wingtip-app-overview.md) Aby uzyskać więcej pomocy.

![Nawiązać połączenia z serwerem bazy danych SQL z SSMS](media/saas-tenancy-tenant-analytics/ssmsSignIn.JPG)

W Eksploratorze obiektów:

1. Rozwiń węzeł *tenants1-dpt -&lt;użytkownika&gt;*  serwera.
1. Rozwiń węzeł bazy danych i zapoznać się z listą dzierżawy baz danych.
1. Rozwiń węzeł *katalogu-dpt -&lt;użytkownika&gt;*  serwera.
1. Sprawdź, czy widzisz analytics przechowywania zawierających następujące obiekty:
    1. Tabele **raw_Tickets**, **raw_Customers**, **raw_Events** i **raw_Venues** przechowywania danych pierwotnych wyodrębnione z baz danych dzierżawy.
    1. Tabele schematu gwiazdy są **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events**, i **dim_Dates** .
    1. Procedura składowana **sp_transformExtractedData** służy do przekształcania danych i załaduj go do tabel schematu gwiazdy.

![DWtables](media/saas-tenancy-tenant-analytics/DWtables.JPG)

#### <a name="blob-storage"></a>Blob Storage
1. W [Azure Portal](https://ms.portal.azure.com), przejdź do grupy zasobów, który został użyty do wdrożenia aplikacji. Sprawdź, czy konto magazynu o nazwie **wingtipstaging\<użytkownika\>**  został dodany.

  ![DWtables](media/saas-tenancy-tenant-analytics/adf-staging-storage.PNG)

1. Kliknij przycisk **wingtipstaging\<użytkownika\>**  konta magazynu, aby eksplorować obiekty obecne.
1. Kliknij przycisk **obiekty BLOB** kafelka
1. Kliknij kontener **configfile**
1. Sprawdź, czy **configfile** zawiera plik JSON o nazwie **TableConfig.json**. Ten plik zawiera nazwy tabeli źródłowych i docelowych oraz nazw kolumn i nazwę kolumny śledzenia.

#### <a name="azure-data-factory-adf"></a>Fabryki danych Azure (ADF)
W [Azure Portal](https://ms.portal.azure.com) w grupie zasobów, sprawdź, czy fabryka danych Azure o nazwie _dbtodwload -\<użytkownika\>_  został dodany. 

 ![adf_portal](media/saas-tenancy-tenant-analytics/adf-data-factory-portal.png)

Ta sekcja opisuje fabryki danych utworzone. Wykonaj poniższe kroki, aby uruchomić fabryki danych:
1. W portalu kliknij fabryki danych o nazwie **dbtodwload -\<użytkownika\>**.
2. Kliknij przycisk **autora & Monitor** Kafelek, aby uruchomić projektanta fabryki danych w osobnej karcie. 

## <a name="extract-load-and-transform-data"></a>Wyodrębnij obciążenia i przekształcanie danych
Fabryka danych Azure służy do organizowanie wyodrębniania, ładowania i przekształcania danych. W tym samouczku wyodrębniania danych z czterech różnych widoków SQL z każdej z baz danych dzierżawy: **rawTickets**, **rawCustomers**, **rawEvents**, i  **rawVenues**. Widoki te obejmują miejscową identyfikator, więc można stwierdzić danych z każdego miejsca w magazynie danych. Dane są ładowane w odpowiadających jej tabel przemieszczania w magazynie danych: **raw_Tickets**, **raw_customers**, **raw_Events** i **raw_Venue**. Procedura składowana następnie przekształcenia danych pierwotnych i wypełnia tabel schematu gwiazdy: **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events** , i **dim_Dates**.

W poprzedniej sekcji wdrożyć i zainicjować niezbędnych zasobów platformy Azure, w tym fabryki danych. Fabryka danych wdrożonych obejmuje potoki, zestawów danych, połączone usługi, itp., które są wymagane do wyodrębniania, ładowania i przekształcania danych dzierżawy. Przyjrzyjmy się, że te obiekty, a następnie wyzwalacza potoku do przenoszenia danych z baz danych dzierżawy w magazynie danych.

### <a name="data-factory-pipeline-overview"></a>Omówienie potoku fabryki danych
Ta sekcja opisuje obiektów utworzonych w fabryce danych. Poniższa ilustracja opisano ogólny przepływ pracy potoku ADF używane w tym samouczku. Jeśli wolisz Eksploruj potoku później i wyświetlić wyniki najpierw, przejdź do następnej sekcji **wyzwolenia potoku Uruchom**.

![adf_overview](media/saas-tenancy-tenant-analytics/adf-data-factory.PNG)

Na stronie przeglądu, przełącz się do **autora** karcie w lewym panelu i sprawdź, czy istnieją trzy [potoki](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) i trzy [zestawów danych](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services) utworzony.
![adf_author](media/saas-tenancy-tenant-analytics/adf_author_tab.JPG)

Są trzy potoki zagnieżdżone: SQLDBToDW, DBCopy i TableCopy.

**Potok 1 - SQLDBToDW** wyszukuje nazwy dzierżawy baz danych przechowywanych w bazie danych katalogu (nazwa tabeli: [__ShardManagement]. [ ShardsGlobal]) i dla każdej bazy danych dzierżawy, wykonuje **DBCopy** potoku. Po zakończeniu dostarczonych **sp_TransformExtractedData** schematu procedury składowanej jest wykonywana. Tę procedurę składowaną przekształca załadowanych danych w tabelach przemieszczania i wypełnia tabel schematu gwiazdy.

**Potok 2 - DBCopy** odwołuje się do nazwy źródłowego tabel i kolumn w pliku konfiguracji przechowywane w magazynie obiektów blob.  **TableCopy** potoku jest następnie uruchom dla każdego z czterech tabel: TicketFacts, CustomerFacts EventFacts i VenueFacts. **[Foreach](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity)** wykonuje działania równoległe dla wszystkich baz danych 20. ADF pozwala maksymalnie 20 pętli iteracji, aby być wykonywane równolegle. Należy rozważyć utworzenie wielu potoki więcej baz danych.    

**Potok 3 - TableCopy** używa numerów wersji wierszy w bazie danych SQL (_rowversion_) do identyfikowania wierszy, które zostały zmienione lub aktualizowane. To działanie wyszukuje uruchomienia i wersja wiersza końcowego dla wyodrębniania wierszy z tabeli źródłowej. **CopyTracker** tabeli przechowywanych w każdej bazie danych dzierżawy śledzi ostatni wiersz wyodrębniony z każdej tabeli źródłowej w każdym przebiegu. Nowych lub zmienionych wierszy są kopiowane do odpowiadających jej tabel przemieszczania w magazynie danych: **raw_Tickets**, **raw_Customers**, **raw_Venues**, i **raw_ Zdarzenia**. Na koniec ostatniej wersji wierszy jest zapisywany w **CopyTracker** tabela, która ma służyć jako wersje wiersza początkowego dla następnego wyodrębniania. 

Istnieją również trzy sparametryzowane połączonej usługi tego łącza fabryki danych w źródle bazy danych SQL, element docelowy magazyn danych SQL i pośredniego magazynu obiektów Blob. W **autora** kliknij **połączeń** do eksplorowania połączone usługi, jak pokazano na poniższej ilustracji:

![adf_linkedservices](media/saas-tenancy-tenant-analytics/linkedservices.JPG)

Odpowiadającej trzy połączone usługi, istnieją trzy zestawy danych, które odwołują się do danych, używanego w działaniach potoku jako danych wejściowych lub wyjściowych. Eksplorowanie każdego z zestawów danych, aby przyjrzeć się połączenia i parametry używane. _AzureBlob_ wskazuje plik konfiguracji zawierający źródłowa i docelowa tabel i kolumn, a także kolumny śledzenia w każdym źródle.
  
### <a name="data-warehouse-pattern-overview"></a>Omówienie wzorzec magazynu danych
Magazyn danych SQL służy do przechowywania analytics do wykonywania agregacji danych dzierżawy. W tym przykładzie PolyBase jest używana do ładowania danych do magazynu danych SQL. Dane pierwotne została załadowana do tabel przemieszczania mających kolumny tożsamości do śledzenia wierszy, które zostały przekształcone w tabelach schematu gwiazdy. Na poniższej ilustracji przedstawiono wzorzec obciążenia: ![loadingpattern](media/saas-tenancy-tenant-analytics/loadingpattern.JPG)

Wolno tabel na 1 wymiar typu Zmiana wymiaru (SCD) są używane w tym przykładzie. Każdy wymiar ma klucz Surogat zdefiniowane przy użyciu kolumny tożsamości. Najlepszym rozwiązaniem w tabeli wymiaru daty jest wstępnie wypełniane, aby zaoszczędzić czas. Dla innych tabel wymiarów Tworzenie tabeli jako wybierz... Instrukcji (CTAS) służy do tworzenia tabeli tymczasowej zawierająca istniejące wiersze zmodyfikowane i niezmodyfikowane, wraz z kluczy dwuskładnikowego. Jest to zrobić za pomocą IDENTITY_INSERT = ON. Nowe wiersze są następnie wstawione do tabeli z IDENTITY_INSERT = OFF. Łatwe wycofywania zostanie zmieniona nazwa istniejącej tabeli wymiarów i tabela tymczasowa została zmieniona na stają się nową tabelę wymiarów. Przed każdym uruchomieniu usunięcia starego tabeli wymiarów.

Tabele wymiarów są ładowane przed tabeli faktów. Sekwencjonowanie to zapewnia, czy dla każdego nadchodzących faktów, wszystkie wymiary przywoływanego już istnieją. Faktów są załadowane, jest dopasowany klucz business dla każdego odpowiedniego wymiaru i odpowiadające im klucze Surogat są dodawane do każdego faktów.

Ostatnim krokiem transformacji powoduje usunięcie danych tymczasowych gotowe do następnego wykonywania potoku.
   
### <a name="trigger-the-pipeline-run"></a>Wyzwalanie potoku uruchamiania
Wykonaj, który wyodrębnić poniższe kroki, aby uruchomić pełną ładowania i przekształcania potoku dla wszystkich baz danych dzierżawy:
1. W **autora** kartę ADF interfejsie użytkownika, wybierz opcję **SQLDBToDW** potoku, w lewym okienku.
1. Kliknij przycisk **wyzwalacza** i z pobrany menu rozwijane kliknij **wyzwalacza teraz**. Ta akcja uruchamia natychmiast potoku. W scenariuszu produkcyjnym czy określić harmonogram uruchamiania potoku, aby odświeżyć dane zgodnie z harmonogramem.
  ![adf_trigger](media/saas-tenancy-tenant-analytics/adf_trigger.JPG)
1. Na **uruchomić potoku** kliknij przycisk **Zakończ**.
 
### <a name="monitor-the-pipeline-run"></a>Monitorowanie działania potoku
1. W interfejsie użytkownika ADF, przełącz się do **Monitor** kartę z menu po lewej stronie.
1. Kliknij przycisk **Odśwież** momentu stanu potoku SQLDBToDW **zakończyło się pomyślnie**.
  ![adf_monitoring](media/saas-tenancy-tenant-analytics/adf_monitoring.JPG)
1. Połącz się data warehouse przy użyciu narzędzia SSMS i wykonywać zapytania dotyczące tabel schemat gwiazdy, aby sprawdzić, czy dane zostały załadowane w poniższych tabelach.

Po zakończeniu potoku tabeli faktów przechowuje dane dotyczące wszystkich miejsc sprzedaży biletów i tabele wymiarów są wypełniane przy użyciu odpowiednich miejsc, zdarzeń i klientami.

## <a name="data-exploration"></a>Eksploracja danych

### <a name="visualize-tenant-data"></a>Wizualizuj dane dzierżawy

Dane w schemacie gwiazdkę zawiera wszystkie biletu sprzedaży dane potrzebne do analizy. Wizualizacja danych graficznie ułatwia trendy w dużych zestawach danych. W tej sekcji użyjesz **usługi Power BI** do manipulowania i wizualizacji danych dzierżawy w magazynie danych.

Do nawiązania połączenia usługi Power BI i zaimportować widoków, które wcześniej utworzony, wykonaj następujące kroki:

1. Uruchom program Power BI desktop.
2. Na Wstążce głównej wybierz **Pobierz dane**i wybierz **więcej...** z menu.
3. W **Pobierz dane** wybierz **bazy danych SQL Azure**.
4. W oknie nazwy logowania bazy danych, wprowadź nazwę serwera (**katalogu-dpt -&lt;użytkownika&gt;. database.windows.net**). Wybierz **importu** dla **tryb łączności danych**, a następnie kliknij przycisk **OK**. 

    ![znak w do power-bi](./media/saas-tenancy-tenant-analytics/powerBISignIn.PNG)

5. Wybierz **bazy danych** w okienku po lewej stronie, a następnie wprowadź nazwę użytkownika = *developer*, a następnie wprowadź hasło = *P@ssword1*. Kliknij przycisk **Połącz**.  

    ![bazy danych — logowanie](./media/saas-tenancy-tenant-analytics/databaseSignIn.PNG)

6. W **Nawigator** okienku w obszarze bazy danych analizy, wybierz tabele schematu gwiazdy: **fact_Tickets**, **dim_Events**, **dim_Venues**, **dim_Customers** i **dim_Dates**. Następnie wybierz **obciążenia**. 

Gratulacje! Można pomyślnie załadować dane do usługi Power BI. Teraz Poznaj interesujące wizualizacje, aby uzyskać wgląd w dzierżawcom. Przejdźmy, jak analytics zapewniają kilka zaleceń opartych na danych do zespołu firm Wingtip biletów. Zalecenia ułatwiają Optymalizuj środowisko firm modelu i klienta.

Rozpocznij od analizowanie danych sprzedaży biletów, aby sprawdzić zmiany użycia między miejsc. Wybierz opcje wyświetlane w usłudze Power BI do wykreślenia wykresu słupkowego całkowitej liczby sprzedanych w każdym miejscu biletów. (Z powodu zmienności losowej w generatorze biletu, wyniki mogą być różne.)
 
![TotalTicketsByVenues](./media/saas-tenancy-tenant-analytics/TotalTicketsByVenues-DW.PNG)

Poprzedni kreślenia potwierdza, że liczba sprzedanych w każdym miejscu biletów różni się. Miejsc, w których sprzedawać bilety więcej bardziej intensywnie miejsc, w których sprzedawać bilety mniej korzystają z usługą. Może istnieć możliwość tutaj dostosować alokacji zasobów zgodnie z potrzebami innej dzierżawy.

Dodatkowo można analizować danych, aby sprawdzić, jak sprzedaży biletów różnią się w czasie. Wybierz opcje pokazano na poniższej ilustracji w usłudze Power BI do wykreślenia łączna liczba biletów sprzedanych każdego dnia przez 60 dni.
 
![SaleVersusDate](./media/saas-tenancy-tenant-analytics/SaleVersusDate-DW.PNG)

Wykresie przedstawiono tej kolekcji sprzedaży biletów dla niektórych miejsc. Te wartości szczytowe wzmocnienie pomysł, że niektóre miejsc może korzystać z zasobów systemowych nieproporcjonalnie. Do tej pory Brak nie wzorzec widocznych w przypadku nagłego wystąpienia.

Następny umożliwia badanie znaczenia te dni sprzedaży szczytu. Gdy te wartości są wykonywane po Przejdź sprzedaży biletów? Do wykreślenia biletów sprzedawany na dzień, należy wybrać opcje pokazano na poniższej ilustracji w usłudze Power BI.

![SaleDayDistribution](./media/saas-tenancy-tenant-analytics/SaleDistributionPerDay-DW.PNG)

Ten wykres pokazuje, że niektóre miejsc sprzedaży dużą liczbą biletów pierwszego dnia sprzedaży. Jak przejść biletów sprzedaży w tych miejsc, wydaje się mad łazienkowych. W przypadku pozostałych dzierżaw usługi może mieć wpływ na tej serii działania przez kilka miejsc.

Aby przejść do szczegółów w danych ponownie, aby sprawdzać, czy ten mad łazienkowych obowiązuje dla wszystkich zdarzeń obsługiwanych przez te miejsc. W poprzednich powierzchni widać, że Hall porozumieniu Contoso sprzedaje wiele biletów i że Contoso również ma kolekcji w sprzedaży biletów w określone dni. Odtwórz z opcjami usługi Power BI do wykreślenia biletu zbiorcza sprzedaży Hall porozumieniu firmy Contoso, koncentrujących się na trendy sprzedaży dla każdego z jego zdarzeń. Wszystkie zdarzenia według tego samego wzorca sprzedaży? Spróbuj utworzyć wykres tak jak poniżej.

![ContosoSales](media/saas-tenancy-tenant-analytics/EventSaleTrends.PNG)

Ten wykres sprzedaży biletów zbiorczą wraz z upływem czasu Contoso porozumieniu Hall dla każdego zdarzenia pokazuje, że mad łazienkowych nie odbywa się dla wszystkich zdarzeń. Odtwórz opcje filtru, aby Eksploruj trendy sprzedaży dla innych miejsc.

Wgląd w biletu sprzedaży wzorce może prowadzić Wingtip biletów, aby zoptymalizować swojego modelu biznesowego. Możliwe, że zamiast ładowana wszystkich dzierżaw jednakowo, Wingtip wprowadzić warstwy usług z różne poziomy wydajności. Większe miejsc, wymagających sprzedawać bilety więcej dziennie można oferowane wyższego poziomu z wyższej Umowa dotycząca poziomu usług (SLA). Te miejsc może mieć swoje bazy danych, umieszczone w puli z wyższe limity zasobów na bazę danych. Każdej warstwy usług może mieć przydział sprzedaży co godzinę, o dodatkowe opłaty za przekroczenie przydziału. Większe miejsc, w których okresowe seria sprzedaży będzie korzystać z wyższej warstwy, a biletów Wingtip można efektywniej sprzedawać usługi.

W tym samym czasie niektórzy klienci biletów Wingtip reklamację, one mieć trudności z sprzedawać bilety za mało Justify kosztu usługi. Możliwe, że informacje na temat tych technologii ma możliwość zwiększenie sprzedaży biletów dla gorszych wynikach w przypadku miejsc. Wyższy sprzedaży może zwiększyć wartość postrzegana usługi. Fact_Tickets kliknij prawym przyciskiem myszy i wybierz **nowej miary**. Wprowadź poniższe wyrażenie dla nowej miary o nazwie **AverageTicketsSold**:

```
AverageTicketsSold = DIVIDE(DIVIDE(COUNTROWS(fact_Tickets),DISTINCT(dim_Venues[VenueCapacity]))*100, COUNTROWS(dim_Events))
```

Wybierz następujące opcje wizualizacji do wykreślenia sprzedanych przez każdego miejsce, aby określić ich względne Powodzenie biletów procent.

![AvgTicketsByVenues](media/saas-tenancy-tenant-analytics/AvgTicketsByVenues-DW.PNG)

Powyżej kreślenia pokazuje, że mimo że większość miejsc sprzedawać więcej niż 80% bilet, są niektóre klienci do wypełnienia więcej niż połowy ich pozycji. Odtwarzać wartości również wybrać procent maksymalnej lub minimalnej biletów sprzedaży dla każdego miejsca.

## <a name="embedding-analytics-in-your-apps"></a>Osadzanie analityka w aplikacjach 
W tym samouczku ma fokus w analytics dzierżawy między używany do lepszego zrozumienia swoich dzierżaw z dostawcą oprogramowania. Analytics zapewnia także szczegółowe informacje, aby _dzierżawców_, aby ułatwić im bardziej wydajnie zarządzać firmy samodzielnie. 

W przykładzie biletów Wingtip należy wcześniej odnalezionych czy sprzedaży biletów często wykonują wzorce przewidywalne. Wgląd ten może służyć do gorszych wynikach w przypadku miejsc zwiększenie sprzedaży biletów. Być może jest możliwość posługiwanie się machine learning metodami do prognozowania sprzedaży biletów dla zdarzeń. Skutki zmian cen można również modelowane, aby umożliwić wpływ oferty rabatów, należy przewidzieć. Power BI Embedded może być zintegrowane aplikacji zarządzania zdarzeń do wizualizacji przewidywane sprzedaży, w tym wpływ rabaty na całkowita licencje na stanowiska sprzedaży i przychodu ze sprzedaży małej zdarzeń. Z Power BI Embedded można nawet integracji faktycznie stosowania Rabat do ceny biletów, kliknij prawym przyciskiem myszy środowisko wizualizacji.


## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wdrażanie magazynu danych SQL jest wypełniane przy użyciu schematu gwiazdy analizy dzierżawy.
> * Fabryka danych Azure umożliwia wyodrębniania danych z każdej dzierżawy bazy danych do magazynu danych analiz.
> * Optymalizuj wyodrębnione dane (z opcją reorganize na schemat gwiazdy).
> * Wyślij zapytanie do analityka magazynu danych. 
> * Wizualizuj trendów w danych we wszystkich dzierżawców przy użyciu usługi Power BI.

Gratulacje!

## <a name="additional-resources"></a>Zasoby dodatkowe

- Dodatkowe [samouczków, z którymi aplikacji Wingtip SaaS](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
