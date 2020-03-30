---
title: Uruchamianie zapytań analitycznych względem baz danych dzierżawy
description: Zapytania analityczne między dzierżawcami przy użyciu danych wyodrębnionych z bazy danych SQL Azure, magazynu danych SQL, usługi Azure Data Factory lub usługi Power BI.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: anumjs
ms.author: anjangsh
ms.reviewer: MightyPen, sstein
ms.date: 12/18/2018
ms.openlocfilehash: 4791cd3a6b6f72c5d9ee4ca828d66b0d361f356c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73816783"
---
# <a name="explore-saas-analytics-with-azure-sql-database-sql-data-warehouse-data-factory-and-power-bi"></a>Eksplorowanie analizy SaaS za pomocą usługi Azure SQL Database, magazynu danych SQL, fabryki danych i usługi Power BI

W tym samouczku przechodzisz przez scenariusz analizy end-to-end. Scenariusz pokazuje, jak analiza danych dzierżawy może umożliwić dostawcom oprogramowania podejmowanie inteligentnych decyzji. Korzystając z danych wyodrębnionych z każdej bazy danych dzierżawy, można użyć analizy, aby uzyskać wgląd w zachowanie dzierżawy, w tym ich użycie przykładowej aplikacji SaaS bilety Wingtip. Ten scenariusz obejmuje trzy kroki: 

1.  **Wyodrębnij dane** z każdej bazy danych dzierżawy do magazynu analizy, w tym przypadku magazynu danych SQL.
2.  **Optymalizacja wyodrębnionych danych** do przetwarzania analitycznego.
3.  Narzędzia **analizy biznesowej wykorzystują** przydatne informacje, które mogą prowadzić do podejmowania decyzji. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> - Utwórz magazyn analizy dzierżawy do ładowania.
> - Użyj usługi Azure Data Factory (ADF), aby wyodrębnić dane z każdej bazy danych dzierżawy do magazynu danych analizy.
> - Optymalizacja wyodrębnionych danych (reorganizacja do schematu gwiazdy).
> - Kwerenda w magazynie danych analizy.
> - Użyj usługi Power BI do wizualizacji danych, aby wyróżnić trendy w danych dzierżawy i zalecić ulepszenia.

![architekturaOverView](media/saas-tenancy-tenant-analytics/adf_overview.png)

## <a name="analytics-over-extracted-tenant-data"></a>Analiza wyodrębnionych danych dzierżawy

Aplikacje SaaS przechowują potencjalnie ogromną ilość danych dzierżawy w chmurze. Te dane mogą zapewnić bogate źródło szczegółowych informacji na temat działania i użycia aplikacji oraz zachowania dzierżawców. Te szczegółowe informacje mogą prowadzić do rozwoju funkcji, ulepszeń użyteczności i innych inwestycji w aplikacje i platformę.

Uzyskiwanie dostępu do danych dla wszystkich dzierżawców jest proste, gdy wszystkie dane są w jednej bazie danych z wieloma dzierżawami. Ale dostęp jest bardziej złożony, gdy są dystrybuowane na dużą skalę w tysiącach baz danych. Jednym ze sposobów oswajania złożoności jest wyodrębnienie danych do bazy danych analizy lub magazynu danych dla kwerendy.

W tym samouczku przedstawiono scenariusz analizy end-to-end dla aplikacji Bilety Wingtip. Po pierwsze [usługa Azure Data Factory (ADF)](../data-factory/introduction.md) jest używana jako narzędzie aranżacji do wyodrębniania sprzedaży biletów i powiązanych danych z każdej bazy danych dzierżawy. Te dane są ładowane do tabel przemieszczania w magazynie analizy. Magazyn analizy może być bazą danych SQL lub magazynem danych SQL. W tym samouczku użyto [magazynu danych SQL](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) jako magazynu analizy.

Następnie wyodrębnione dane są przekształcane i ładowane do zestawu tabel [schematu gwiazdy.](https://www.wikipedia.org/wiki/Star_schema) Tabele składają się z centralnej tabeli faktów plus powiązane tabele wymiarów:

- Centralna tabela faktów w schemacie gwiazdy zawiera dane biletu.
- Tabele wymiarów zawierają dane o miejscach, zdarzeniach, klientach i datach zakupu.

Razem tabele centralne i wymiarowe umożliwiają wydajne przetwarzanie analityczne. Schemat gwiazdy używany w tym samouczku jest wyświetlany na poniższej ilustracji:
 
![architekturaOverView](media/saas-tenancy-tenant-analytics/starschematables.JPG)

Na koniec tabele schematu gwiazdy są wyszukiwane. Wyniki kwerend są wyświetlane wizualnie za pomocą usługi Power BI, aby wyróżnić wgląd w zachowanie dzierżawy i ich wykorzystanie aplikacji. Za pomocą tego schematu gwiazdy można uruchomić kwerendy, które ujawniają:

- Kto kupuje bilety i z którego miejsca.
- Wzorce i trendy w sprzedaży biletów.
- Względna popularność każdego miejsca.

Ten samouczek zawiera podstawowe przykłady szczegółowych informacji, które można zbierać z danych Wingtip Bilety. Zrozumienie, jak każde miejsce korzysta z usługi może spowodować, że dostawca biletów Wingtip myśleć o różnych planów usług przeznaczonych na mniej lub bardziej aktywnych miejscach, na przykład. 

## <a name="setup"></a>Konfiguracja

### <a name="prerequisites"></a>Wymagania wstępne

> [!NOTE]
> W tym samouczku użyto funkcji usługi Azure Data Factory, które są obecnie w ograniczonej wersji zapoznawczej (parametryzacja połączonej usługi). Jeśli chcesz zrobić ten samouczek, podaj identyfikator subskrypcji [tutaj](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxrVywox1_tHk9wgd5P8SVJUNlFINjNEOElTVFdMUEREMjVVUlJCUDdIRyQlQCN0PWcu). Wyślemy Ci potwierdzenie, gdy tylko twoja subskrypcja zostanie włączona.

Do wykonania zadań opisanych w tym samouczku niezbędne jest spełnienie następujących wymagań wstępnych:
- Wdrożono aplikację Bazy danych SaaS na dzierżawę Wingtip Tickets. Aby wdrożyć w mniej niż pięć minut, zobacz [Wdrażanie i eksplorowanie aplikacji SaaS Wingtip](saas-dbpertenant-get-started-deploy.md).
- Skrypty bazy danych SaaS na dzierżawę i [kod źródłowy](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/) aplikacji są pobierane z usługi GitHub. Zobacz instrukcje pobierania. Pamiętaj, aby *odblokować plik zip* przed wyodrębnieniem jego zawartości.
- Program Power BI Desktop jest zainstalowany. [Pobierz program Power BI Desktop](https://powerbi.microsoft.com/downloads/).
- Partia dodatkowych dzierżaw został aprowizny, zobacz [**instruktaż dzierżawy obsługi .**](saas-dbpertenant-provision-and-catalog.md)

### <a name="create-data-for-the-demo"></a>Tworzenie danych dla wersji demonstracyjnej

W tym samouczku przeanalizowane analizy danych sprzedaży biletów. W tym kroku można wygenerować dane biletu dla wszystkich dzierżawców. W późniejszym kroku te dane są wyodrębniane do analizy. _Upewnij się, że aprowizacji partii dzierżawy_ (zgodnie z opisem wcześniej), tak aby masz wystarczającą ilość danych, aby udostępnić szereg różnych wzorców zakupu biletów.

1. W programie PowerShell ISE otwórz *...\Learning Modules\Operational Analytics\Tenant Analytics DW\Demo-TenantAnalyticsDW.ps1*i ustaw następującą wartość:
    - **$DemoScenario**1 Kup bilety na wydarzenia we wszystkich miejscach**1**  = 
2. Naciśnij **klawisz F5,** aby uruchomić skrypt i utworzyć historię zakupów biletów dla wszystkich miejsc. Z 20 najemców, skrypt generuje dziesiątki tysięcy biletów i może potrwać 10 minut lub więcej.

### <a name="deploy-sql-data-warehouse-data-factory-and-blob-storage"></a>Wdrażanie magazynu danych SQL, fabryki danych i magazynu obiektów blob 
W aplikacji Bilety Wingtip dane transakcyjne dzierżawców są dystrybuowane przez wiele baz danych. Usługa Azure Data Factory (ADF) służy do organizowania wyodrębniania, ładowania i przekształcania (ELT) tych danych w magazynie danych. Aby załadować dane do magazynu danych SQL najbardziej efektywnie, ADF wyodrębnia dane do pośrednich plików obiektów blob, a następnie używa [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/design-elt-data-loading) do załadowania danych do magazynu danych.   

W tym kroku można wdrożyć dodatkowe zasoby używane w samouczku: magazyn danych SQL o nazwie _tenantanalytics_, fabryka danych platformy Azure o nazwie _dbtodwload-\<użytkownik\>_ i konto magazynu platformy Azure o nazwie _wingtipstaging\<użytkownika\>_. Konto magazynu służy do tymczasowego przechowywania wyodrębnionych plików danych jako obiektów blob przed ich załadowaniem do magazynu danych. Ten krok wdraża również schemat magazynu danych i definiuje potoki podajnika ADF, które organizują proces ELT.
1. W programie PowerShell ISE otwórz *...\Learning Modules\Operational Analytics\Tenant Analytics DW\Demo-TenantAnalyticsDW.ps1* i ustaw:
    - **$DemoScenario**2 Wdrażanie magazynu danych analizy dzierżawy, magazynu obiektów blob i fabryki danych**2**  =  
1. Naciśnij **klawisz F5,** aby uruchomić skrypt demonstracyjny i wdrożyć zasoby platformy Azure. 

Teraz przejrzyj wdrożone zasoby platformy Azure:
#### <a name="tenant-databases-and-analytics-store"></a>Bazy danych dzierżawców i magazyn analiz
Program [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) umożliwia łączenie się z serwerami użytkowników **&lt;&gt; ** **&lt;&gt; ** tenants1-dpt- user i catalog-dpt. Zamień &lt;użytkownika&gt; na wartość używaną podczas wdrażania aplikacji. Użyj login = *deweloper* i hasło = *P\@ssword1*. Zobacz [samouczek wprowadzający, aby](saas-dbpertenant-wingtip-app-overview.md) uzyskać więcej wskazówek.

![Łączenie się z serwerem bazy danych SQL z usługi SSMS](media/saas-tenancy-tenant-analytics/ssmsSignIn.JPG)

W Eksploratorze obiektów:

1. Rozwiń *serwer&lt;użytkownika tenants1-dpt- user.&gt; *
1. Rozwiń węzeł Bazy danych i zobacz listę baz danych dzierżawy.
1. Rozwiń *serwer użytkownika&lt;catalog-dpt- user.&gt; *
1. Sprawdź, czy widzisz magazyn analizy zawierający następujące obiekty:
    1. Tabele **raw_Tickets,** **raw_Customers,** **raw_Events** i **raw_Venues** przechowują nieprzetworzone wyodrębnione dane z baz danych dzierżawy.
    1. Tabele schematów gwiazdek są **fact_Tickets** **, dim_Customers**, **dim_Venues,** **dim_Events**i **dim_Dates**.
    1. Procedura **składowana, sp_transformExtractedData** jest używany do przekształcania danych i załadować je do tabel schematu gwiazdy.

![Stoły DWtables](media/saas-tenancy-tenant-analytics/DWtables.JPG)

#### <a name="blob-storage"></a>Blob Storage
1. W [witrynie Azure Portal](https://ms.portal.azure.com)przejdź do grupy zasobów używanej do wdrażania aplikacji. Sprawdź, czy dodano konto magazynu o nazwie **\<wingtipstaging user.\> **

   ![Stoły DWtables](media/saas-tenancy-tenant-analytics/adf-staging-storage.PNG)

1. Kliknij konto magazynu **\<użytkownika\> wingtipstaging,** aby eksplorować obecne obiekty.
1. Kliknij **kafelek obiektów blob**
1. Kliknij **plik konfiguratywny kontenera**
1. Sprawdź, czy **plik konfiguracyjny** zawiera plik JSON o nazwie **TableConfig.json**. Ten plik zawiera nazwy tabel źródłowych i docelowych, nazwy kolumn i nazwę kolumny modułu śledzącego.

#### <a name="azure-data-factory-adf"></a>Fabryka danych platformy Azure (ADF)
W [witrynie Azure Portal](https://ms.portal.azure.com) w grupie zasobów sprawdź, czy została dodana usługa Azure Data Factory o nazwie _\<dbtodwload- user.\> _ 

 ![adf_portal](media/saas-tenancy-tenant-analytics/adf-data-factory-portal.png)

W tej sekcji eksploruje utworzonej fabryki danych. Wykonaj poniższe czynności, aby uruchomić fabrykę danych:
1. W portalu kliknij fabrykę danych o nazwie **\<dbtodwload- user\>**.
2. Kliknij **polecenie Autor & Monitoruj** kafelek, aby uruchomić projektanta fabryki danych na osobnej karcie. 

## <a name="extract-load-and-transform-data"></a>Wyodrębnianie, ładowanie i przekształcanie danych
Usługa Azure Data Factory służy do organizowania wyodrębniania, ładowania i przekształcania danych. W tym samouczku wyodrębnić dane z czterech różnych widoków SQL z każdej z baz danych dzierżawy: **rawTickets**, **rawCustomers**, **rawEvents**i **rawVenues**. Widoki te obejmują identyfikator miejsca, dzięki czemu można rozróżniać dane z każdego miejsca w magazynie danych. Dane są ładowane do odpowiednich tabel przemieszczania w magazynie danych: **raw_Tickets**, **raw_customers,** **raw_Events** i **raw_Venue**. Procedura składowana przekształca następnie nieprzetworzone dane i wypełnia tabele schematów gwiazd: **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events**i **dim_Dates**.

W poprzedniej sekcji wdrożono i zainicjowano niezbędne zasoby platformy Azure, w tym fabrykę danych. Wdrożona fabryka danych zawiera potoki, zestawy danych, połączone usługi itp., wymagane do wyodrębniania, ładowania i przekształcania danych dzierżawy. Przejrzyjmy te obiekty dalej, a następnie wyzwolić potok, aby przenieść dane z baz danych dzierżawy do magazynu danych.

### <a name="data-factory-pipeline-overview"></a>Omówienie potoku fabryki danych
W tej sekcji eksploruje obiekty utworzone w fabryce danych. Na poniższym rysunku opisano ogólny przepływ pracy potoku usługi ADF używany w tym samouczku. Jeśli wolisz eksplorować potok później i najpierw zobaczyć wyniki, przejdź do następnej sekcji **Wyzwalanie przebiegu potoku**.

![adf_overview](media/saas-tenancy-tenant-analytics/adf-data-factory.PNG)

Na stronie przeglądu przełącz się na kartę **Autor** po lewej stronie panelu i obserwuj, że utworzono trzy [potoki](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) i trzy [zestawy danych.](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services)
![adf_author](media/saas-tenancy-tenant-analytics/adf_author_tab.JPG)

Trzy zagnieżdżone potoki to: SQLDBToDW, DBCopy i TableCopy.

**Potok 1 — SQLDBToDW** wyszukuje nazwy baz danych dzierżawców przechowywanych w bazie danych wykazu (nazwa tabeli: [__ShardManagement].[ ShardsGlobal]) i dla każdej bazy danych dzierżawy wykonuje potok **DBCopy.** Po zakończeniu wykonywane są **podane sp_TransformExtractedData** schematu procedury składowanej. Ta procedura składowana przekształca załadowane dane w tabelach przemieszczania i wypełnia tabele schematu gwiazdy.

**Potok 2 — DBCopy wyszukuje** nazwy tabel źródłowych i kolumn z pliku konfiguracji przechowywanego w magazynie obiektów blob.  Potok **TableCopy** jest następnie uruchamiany dla każdej z czterech tabel: TicketFacts, CustomerFacts, EventFacts i VenueFacts. Działanie **[Foreach](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity)** wykonuje równolegle dla wszystkich 20 baz danych. ADF umożliwia równoległe uruchamianie maksymalnie 20 iteracji pętli. Należy rozważyć utworzenie wielu potoków dla większej liczby baz danych.    

**Potok 3 — TableCopy** używa numerów wersji wiersza w bazie danych SQL _(rowversion)_ do identyfikowania wierszy, które zostały zmienione lub zaktualizowane. To działanie wyszukuje wersję wiersza początkowego i końcowego do wyodrębniania wierszy z tabel źródłowych. Tabela **CopyTracker** przechowywane w każdej bazie danych dzierżawy śledzi ostatni wiersz wyodrębniony z każdej tabeli źródłowej w każdym uruchomieniu. Nowe lub zmienione wiersze są kopiowane do odpowiednich tabel przemieszczania w magazynie danych: **raw_Tickets**, **raw_Customers,** **raw_Venues**i **raw_Events**. Na koniec ostatnia wersja wiersza jest zapisywana w tabeli **CopyTracker,** która ma być używana jako początkowa wersja wiersza dla następnego wyodrębniania. 

Istnieją również trzy sparametryzowane połączone usługi, które łączą fabrykę danych ze źródłowymi bazami danych SQL, docelowym magazynem danych SQL i pośrednim magazynem obiektów Blob. Na karcie **Autor** kliknij pozycję Połączenia, aby zapoznać się z **połączonymi usługami,** jak pokazano na poniższej ilustracji:

![adf_linkedservices](media/saas-tenancy-tenant-analytics/linkedservices.JPG)

Odpowiadający trzem połączonym usługom istnieją trzy zestawy danych, które odwołują się do danych używanych w działaniach potoku jako dane wejściowe lub wyjściowe. Eksploruj każdy z zestawów danych, aby obserwować połączenia i używane parametry. _AzureBlob_ wskazuje plik konfiguracji zawierający tabele i kolumny źródłowe i docelowe, a także kolumnę modułu śledzącego w każdym źródle.
  
### <a name="data-warehouse-pattern-overview"></a>Omówienie wzorca magazynu danych
Usługa SQL Data Warehouse jest używana jako magazyn analizy do wykonywania agregacji danych dzierżawy. W tym przykładzie PolyBase służy do ładowania danych do magazynu danych SQL. Nieprzetworzone dane są ładowane do tabel przejściowych, które mają kolumnę tożsamości, aby śledzić wiersze, które zostały przekształcone w tabelach schematu gwiazdy. Na poniższej ilustracji ![przedstawiono wzorzec ładowania: loadingpattern](media/saas-tenancy-tenant-analytics/loadingpattern.JPG)

W tym przykładzie używane są tabele wymiarów powoli zmieniających wymiar (SCD) typu 1. Każdy wymiar ma klucz zastępczy zdefiniowany przy użyciu kolumny tożsamości. Najlepszym rozwiązaniem jest wstępnie wypełniona tabela wymiarów dat, aby zaoszczędzić czas. W przypadku innych tabel wymiarów utwórz tabelę jako wybierz... (CTAS) instrukcja służy do tworzenia tabeli tymczasowej zawierającej istniejące zmodyfikowane i niemodyfikowane wiersze, wraz z kluczami zastępczymi. Odbywa się to za pomocą IDENTITY_INSERT=ON. Nowe wiersze są następnie wstawiane do tabeli z IDENTITY_INSERT=OFF. W celu łatwego wycofywania nazwa istniejącej tabeli wymiarów została zmieniona, a tabela tymczasowa została zmieniona na nową tabelę wymiarów. Przed każdym uruchomieniem zostanie usunięta stara tabela wymiarów.

Tabele wymiarów są ładowane przed tabelą faktów. To sekwencjonowanie gwarantuje, że dla każdego przychodzącego faktu wszystkie wymiary, do których istnieją. W miarę ładowania faktów klucz biznesowy dla każdego odpowiedniego wymiaru jest dopasowywany, a odpowiednie klucze zastępcze są dodawane do każdego faktu.

Ostatnim krokiem transformacji usuwa dane przemieszczania gotowe do następnego wykonania potoku.
   
### <a name="trigger-the-pipeline-run"></a>Wyzwalanie uruchomienia potoku
Wykonaj poniższe kroki, aby uruchomić kompletny potok wyodrębniania, ładowania i przekształcania dla wszystkich baz danych dzierżawy:
1. Na karcie **Autor** interfejsu użytkownika usługi ADF wybierz potok **SQLDBToDW** z lewego okienka.
1. Kliknij **przycisk Wyzwalacz** i z menu ściąganego kliknij przycisk **Wyzwalaj teraz**. Ta akcja uruchamia potok natychmiast. W scenariuszu produkcji należy zdefiniować harmonogram uruchamiania potoku, aby odświeżyć dane zgodnie z harmonogramem.
  ![adf_trigger](media/saas-tenancy-tenant-analytics/adf_trigger.JPG)
1. Na stronie **Przebieg potoku** kliknij pozycję **Zakończ**.
 
### <a name="monitor-the-pipeline-run"></a>Monitorowanie działania potoku
1. W interfejsie użytkownika ADF przełącz się na kartę **Monitor** z menu po lewej stronie.
1. Kliknij **przycisk Odśwież,** dopóki stan potoku SQLDBToDW nie **powiedzie się**.
  ![adf_monitoring](media/saas-tenancy-tenant-analytics/adf_monitoring.JPG)
1. Połącz się z magazynem danych za pomocą usługi SSMS i przesuń kwerendę w tabelach schematów gwiazdek, aby sprawdzić, czy dane zostały załadowane w tych tabelach.

Po zakończeniu potoku tabela faktów zawiera dane sprzedaży biletów dla wszystkich miejsc, a tabele wymiarów są wypełniane odpowiednimi miejscami, zdarzeniami i klientami.

## <a name="data-exploration"></a>Eksploracja danych

### <a name="visualize-tenant-data"></a>Wizualizuj dane dzierżawy

Dane w schemacie gwiazdy zawiera wszystkie dane sprzedaży biletów potrzebne do analizy. Graficzne wizualizacje danych ułatwia wyświetlanie trendów w dużych zestawach danych. W tej sekcji służy **usługa Power BI** do manipulowania i wizualizacji danych dzierżawy w magazynie danych.

Aby połączyć się z programem Power BI i zaimportować widoki utworzone wcześniej, należy wykonać następujące czynności:

1. Uruchamianie programu Power BI dla komputerów stacjonarnych.
2. Na wstążce Strona główna wybierz pozycję **Pobierz dane**i wybierz pozycję **Więcej...** z menu.
3. W oknie **Pobierz dane** wybierz pozycję Azure **SQL Database**.
4. W oknie logowania do bazy danych wprowadź nazwę serwera (**catalog-dpt-&lt;&gt;User .database.windows.net**). Wybierz **pozycję Importuj** dla **trybu łączności danych,** a następnie kliknij przycisk **OK**. 

    ![logowanie do zasilania-bi](./media/saas-tenancy-tenant-analytics/powerBISignIn.PNG)

5. Wybierz **opcję Baza danych** w lewym okienku, a następnie wprowadź nazwę użytkownika = *deweloper*i wprowadź hasło = *P\@ssword1*. Kliknij pozycję **Połącz**.  

    ![logowanie do bazy danych](./media/saas-tenancy-tenant-analytics/databaseSignIn.PNG)

6. W okienku **Nawigator** w bazie danych analizy wybierz tabele schematów gwiazdek: **fact_Tickets**, **dim_Events**, **dim_Venues**, **dim_Customers** i **dim_Dates**. Następnie wybierz pozycję **Załaduj**. 

Gratulacje! Dane zostały pomyślnie załadowane do usługi Power BI. Teraz eksploruj interesujące wizualizacje, aby uzyskać wgląd w dzierżawców. Przejmijmy, jak analizy mogą dostarczyć niektóre rekomendacje oparte na danych do zespołu biznesowego Wingtip Tickets. Zalecenia mogą pomóc w optymalizacji modelu biznesowego i obsługi klienta.

Zacznij od analizy danych sprzedaży biletów, aby zobaczyć różnice w użyciu w różnych miejscach. Wybierz opcje wyświetlane w usłudze Power BI, aby wykreślić wykres słupkowy całkowitej liczby biletów sprzedanych przez każde miejsce. (Ze względu na losowe różnice w generatorze biletów, wyniki mogą być różne.)
 
![TotalTicketsByVenues](./media/saas-tenancy-tenant-analytics/TotalTicketsByVenues-DW.PNG)

Poprzednia działka potwierdza, że liczba biletów sprzedanych przez każde miejsce jest różna. Miejsca, które sprzedają więcej biletów, korzystają z usługi w większym stopniu niż miejsca, w których sprzedaje się mniej biletów. W tym miejscu może istnieć możliwość dostosowania alokacji zasobów do różnych potrzeb dzierżawy.

Możesz dalej analizować dane, aby zobaczyć, jak sprzedaż biletów zmienia się w czasie. Wybierz opcje pokazane na poniższej ilustracji w usłudze Power BI, aby wykreślić całkowitą liczbę sprzedanych biletów każdego dnia przez okres 60 dni.
 
![Data sprzedaży](./media/saas-tenancy-tenant-analytics/SaleVersusDate-DW.PNG)

Poprzedni wykres pokazuje, że skok sprzedaży biletów dla niektórych miejsc. Te skoki wzmacniają ideę, że niektóre miejsca mogą zużywać zasoby systemowe nieproporcjonalnie. Do tej pory nie ma oczywistego wzorca, gdy występują kolce.

Następnie zbadajmy znaczenie tych szczytowych dni sprzedaży. Kiedy te szczyty występują po sprzedaży biletów? Aby wykreślić bilety sprzedane dziennie, wybierz opcje pokazane na poniższym obrazie w usłudze Power BI.

![SprzedażDayRozdystrybucja](./media/saas-tenancy-tenant-analytics/SaleDistributionPerDay-DW.PNG)

Ta działka pokazuje, że niektóre lokale sprzedają dużą liczbę biletów w pierwszym dniu sprzedaży. Jak tylko bilety trafią do sprzedaży w tych miejscach, wydaje się, że jest szalony pośpiech. Ten wzrost aktywności przez kilka miejsc może mieć wpływ na usługę dla innych dzierżawców.

Możesz przejść do szczegółów danych ponownie, aby zobaczyć, czy ten szalony pośpiech jest prawdziwy dla wszystkich wydarzeń organizowanych przez te miejsca. W poprzednich działkach widzieliście, że Contoso Concert Hall sprzedaje wiele biletów, a Contoso ma również skok sprzedaży biletów w określone dni. Poeksij z opcjami usługi Power BI, aby wykreślić skumulowaną sprzedaż biletów dla sali koncertowej Contoso, koncentrując się na trendach sprzedaży dla każdego z wydarzeń. Czy wszystkie zdarzenia są zgodne z tym samym wzorcem sprzedaży? Spróbuj stworzyć fabułę taką jak poniższa.

![ContosoSales (właso)](media/saas-tenancy-tenant-analytics/EventSaleTrends.PNG)

Ta działka skumulowanej sprzedaży biletów w czasie dla Sali Koncertowej Contoso dla każdego wydarzenia pokazuje, że szalony pośpiech nie zdarza się dla wszystkich wydarzeń. Poeks wyjrzyj się z opcjami filtrów, aby poznać trendy sprzedaży w innych miejscach.

Wgląd w wzorce sprzedaży biletów może prowadzić Wingtip Bilety do optymalizacji ich modelu biznesowego. Zamiast pobierać opłaty za wszystkie dzierżawy jednakowo, być może Wingtip należy wprowadzić warstwy usług o różnych rozmiarach obliczeń. Większe lokale, które muszą sprzedawać więcej biletów dziennie, mogą otrzymać wyższą warstwę z umową o wyższym poziomie usług (SLA). Te miejsca mogą mieć swoje bazy danych umieszczone w puli z wyższymi limitami zasobów dla bazy danych. Każda warstwa usług może mieć godzinową alokację sprzedaży, z dodatkowymi opłatami pobieranymi za przekroczenie alokacji. Większe miejsca, które mają okresowe wzrosty sprzedaży, skorzystają z wyższych poziomów, a bilety Wingtip mogą bardziej efektywnie zarabiać na swoich usługach.

Tymczasem niektórzy klienci Wingtip Tickets skarżą się, że mają trudności ze sprzedażą wystarczającej ilości biletów, aby uzasadnić koszt usługi. Być może w tych spostrzeżeniach istnieje możliwość zwiększenia sprzedaży biletów dla miejsc o słabych wynikach. Wyższa sprzedaż zwiększyłaby postrzeganą wartość usługi. Kliknij prawym przyciskiem myszy fact_Tickets i wybierz polecenie **Nowa miara**. Wprowadź następujące wyrażenie dla nowej miary o nazwie **AverageTicketsSold**:

```
AverageTicketsSold = DIVIDE(DIVIDE(COUNTROWS(fact_Tickets),DISTINCT(dim_Venues[VenueCapacity]))*100, COUNTROWS(dim_Events))
```

Wybierz następujące opcje wizualizacji, aby wykreślić procent biletów sprzedanych przez każde miejsce, aby określić ich względny sukces.

![AvgTicketsByVenues](media/saas-tenancy-tenant-analytics/AvgTicketsByVenues-DW.PNG)

Powyższa działka pokazuje, że chociaż większość lokali sprzedaje ponad 80% swoich biletów, niektóre z nich mają trudności z obsadzeniem ponad połowy miejsc. Poeks wyekslij się z Dobrze wartości, aby wybrać maksymalny lub minimalny procent biletów sprzedanych dla każdego miejsca.

## <a name="embedding-analytics-in-your-apps"></a>Osadzanie analiz w aplikacjach 
Ten samouczek koncentruje się na analizie między dzierżawcami, która ma na celu poprawę zrozumienia przez dostawcę oprogramowania ich dzierżawców. Analytics może również dostarczać _wglądu do najemców,_ aby pomóc im w skuteczniejszym zarządzaniu swoją działalnością. 

W przykładzie Bilety Wingtip wcześniej odkryto, że sprzedaż biletów mają tendencję do przewidywalnych wzorców. Ten wgląd może być wykorzystany, aby pomóc słabszym miejscom zwiększyć sprzedaż biletów. Być może istnieje możliwość stosowania technik uczenia maszynowego do przewidywania sprzedaży biletów na wydarzenia. Skutki zmian cen mogą być również modelowane, aby umożliwić przewidywanie wpływu oferowania rabatów. Usługa Power BI Embedded może zostać zintegrowana z aplikacją do zarządzania zdarzeniami w celu wizualizacji przewidywanej sprzedaży, w tym wpływu rabatów na całkowitą sprzedaż miejsc i przychodów z wydarzeń o niskiej sprzedaży. Dzięki usłudze Power BI Embedded można nawet zintegrować faktyczne zastosowanie rabatu do cen biletów, bezpośrednio w wizualizacji.


## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wdrażanie magazynu danych SQL wypełnionego schematem gwiazdy do analizy dzierżawy.
> * Użyj usługi Azure Data Factory, aby wyodrębnić dane z każdej bazy danych dzierżawy do magazynu danych analizy.
> * Optymalizacja wyodrębnionych danych (reorganizacja do schematu gwiazdy).
> * Kwerenda w magazynie danych analizy. 
> * Użyj usługi Power BI do wizualizacji trendów w danych we wszystkich dzierżawach.

Gratulacje!

## <a name="additional-resources"></a>Zasoby dodatkowe

- Dodatkowe [samouczki, które opierają się na aplikacji Wingtip SaaS](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
