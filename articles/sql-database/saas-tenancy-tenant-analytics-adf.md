---
title: Uruchamianie zapytań analitycznych dla dzierżawy z bazami danych przy użyciu usługi Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Zapytania analityczne międzydzierżawowa, przy użyciu danych pobranych z usługi Azure SQL Database, SQL Data Warehouse, Azure Data Factory lub usługi Power BI.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anumjs
ms.author: anjangsh
ms.reviewer: MightyPen, sstein
manager: craigg
ms.date: 12/18/2018
ms.openlocfilehash: a658e2fe32ec95dfabad54684a0c9095af7a341d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61485081"
---
# <a name="explore-saas-analytics-with-azure-sql-database-sql-data-warehouse-data-factory-and-power-bi"></a>Poznaj analizy SaaS przy użyciu usługi Azure SQL Database, SQL Data Warehouse, Data Factory i Power BI

W tym samouczku opisano scenariusz end-to-end analizy. Scenariusz pokazuje, jak analiza danych dzierżawy mogą zapewniać dostawcami oprogramowania, aby podejmować inteligentne decyzje. Przy użyciu danych wyodrębnionych z każdej bazy danych dzierżawy, umożliwia analizę wgląd w zachowanie dzierżawy, w tym korzystanie z przykładowej aplikacji SaaS o nazwie Wingtip Tickets. Ten scenariusz obejmuje trzy kroki: 

1.  **Wyodrębnianie danych** z każdej dzierżawy bazy danych do magazynu usługi analytics, w tym przypadku usługa SQL Data Warehouse.
2.  **Optymalizowanie wyodrębnione dane** przetwarzania analizy.
3.  Użyj **analizy biznesowej** narzędzi do rysowania się przydatne informacje szczegółowe, które mogą ukierunkować podejmowanie decyzji. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> - Tworzenie dzierżawy, który analytics przechowywać dotyczące ładowania.
> - Użyj usługi Azure Data Factory (ADF) do wyodrębniania danych z każdej bazy danych dzierżawy do magazynu danych analiz.
> - Optymalizuj wyodrębnione dane (z opcją reorganize w schemacie gwiazdy).
> - Zapytania magazynu danych analiz.
> - Użyj usługi Power BI do wizualizacji danych do wyróżnienia trendów w danych dzierżawy i zalecenia dotyczące ulepszenia.

![architectureOverView](media/saas-tenancy-tenant-analytics/adf_overview.png)

## <a name="analytics-over-extracted-tenant-data"></a>Analiza danych wyodrębnionych dzierżawy

Aplikacje SaaS przytrzymaj potencjalnie ogromnych ilości danych dzierżawy w chmurze. Tych danych może zapewnić bogate źródło szczegółowych informacji na temat operacji i użycia aplikacji i zachowania dzierżawcy. Te informacje mogą ukierunkować rozwój funkcji, usprawnienia w zakresie użyteczności oraz inne inwestycje w aplikacje i platformy.

Dostęp do danych dla wszystkich dzierżaw jest proste, gdy wszystkie dane znajdują się w tylko jednej wielodostępnej bazie danych. Ale dostęp do bardziej złożonych, gdy rozproszone na dużą skalę na tysiące baz danych. Jest jednym ze sposobów oswoić złożoność do wyodrębniania danych analitycznej bazy danych lub magazynu danych dla zapytania.

Ten samouczek przedstawia scenariusz end-to-end analizy aplikacji Wtp. Po pierwsze, [usługi Azure Data Factory (ADF)](../data-factory/introduction.md) jest używany jako narzędzia do aranżacji można wyodrębnić bilety sprzedaży oraz powiązanych danych z każdej bazy danych dzierżawy. Te dane są ładowane do tabel przemieszczania w magazynie usługi analytics. Magazyn usługi analytics można albo bazą danych SQL Database lub SQL Data Warehouse. W tym samouczku [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) jako analizami magazyn.

Następnie przekształcane i ładowane do zestawu danych wyodrębnionych [schematu gwiazdy](https://www.wikipedia.org/wiki/Star_schema) tabel. Tabele składają się z tabelą faktów centralną, a także powiązane tabele wymiarów:

- Tabela faktów centralnej w schemacie gwiazdy zawiera dane biletu.
- Tabele wymiarów zawierają dane dotyczące miejsca, zdarzenia, klientów i Kup daty.

Razem Środkowej i wydajnego przetwarzania analitycznego wymiaru tabel Włącz. Używane w tym samouczku schematu gwiazdy jest wyświetlany na poniższej ilustracji:
 
![architectureOverView](media/saas-tenancy-tenant-analytics/starschematables.JPG)

Na koniec badane są tabele schematu gwiazdy. Wyniki zapytania są wyświetlane wizualnie przy użyciu usługi Power BI, aby wyróżnić szczegółowe informacje dotyczące zachowania dzierżawcy i ich użycie aplikacji. Za pomocą tego schematu gwiazdy można uruchamiać zapytania, które uwidaczniają:

- Kto jest zakupy biletów i z których miejsc.
- Wzorce i trendy sprzedaży biletów.
- Względna popularność każdego miejsca.

Ten samouczek zawiera szczegółowe informacje, które można zebrać dane o nazwie Wingtip Tickets podstawowe przykłady. Zrozumienie, jak na każde miejsce jest używana usługa może spowodować dostawcy Wingtip Tickets wziąć pod uwagę różne plany usługi ukierunkowanych na bardziej lub mniej aktywne miejsca, na przykład. 

## <a name="setup"></a>Konfiguracja

### <a name="prerequisites"></a>Wymagania wstępne

> [!NOTE]
> W tym samouczku korzysta z funkcji usługi Azure Data Factory, które są obecnie dostępne w ograniczonej wersji zapoznawczej (połączona usługa parametryzacji). Jeśli użytkownik chce skorzystać z tego samouczka, Prześlij identyfikator swojej subskrypcji [tutaj](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxrVywox1_tHk9wgd5P8SVJUNlFINjNEOElTVFdMUEREMjVVUlJCUDdIRyQlQCN0PWcu). Firma Microsoft wyśle Ci potwierdzenie tak szybko, jak Twoja subskrypcja została włączona.

Do wykonania zadań opisanych w tym samouczku niezbędne jest spełnienie następujących wymagań wstępnych:
- Aplikacja o nazwie Wingtip Tickets SaaS bazy danych dla dzierżawy jest wdrażana. Aby wdrożyć w mniej niż pięć minut, zobacz [wdrażanie i eksplorowanie aplikacji SaaS o nazwie Wingtip](saas-dbpertenant-get-started-deploy.md).
- Skryptów aplikacji Wingtip Tickets SaaS bazy danych dla dzierżawy i aplikacji [kod źródłowy](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/) są pobierane z usługi GitHub. Zobacz instrukcje pobierania. Pamiętaj, aby *odblokować plik zip* weryfikowany przed wyodrębnianiem jego zawartość.
- Usługa Power BI Desktop jest zainstalowana. [Pobieranie programu Power BI Desktop](https://powerbi.microsoft.com/downloads/).
- Zainicjowano partii dodatkowych dzierżaw, zobacz [ **Aprowizowanie dzierżaw w ramach samouczka**](saas-dbpertenant-provision-and-catalog.md).

### <a name="create-data-for-the-demo"></a>Tworzenie danych demonstracyjnych

W tym samouczku przedstawiono analizę danych sprzedaży biletów. W tym kroku możesz wygenerować dane biletu dla wszystkich dzierżaw. W kolejnym kroku to dane są wyodrębniane do analizy. _Upewnij się, aprowizowane partii dzierżaw_ (jak opisano wcześniej), aby mieć wystarczającej ilości danych, aby udostępnić szeroką gamę różnych biletu wzorce zakupowe.

1. Otwórz w środowisku PowerShell ISE *...\Learning Modules\Operational Analytics\Tenant Analytics DW\Demo-TenantAnalyticsDW.ps1*i Ustaw następującą wartość:
    - **$DemoScenario** = **1** zakup biletów dla zdarzeń na wszystkie lokalizacje
2. Naciśnij klawisz **F5** Uruchom skrypt i utworzyć historię dla wszystkich miejsc zakupów biletów. Z 20 dzierżawami skrypt generuje dziesiątki tysięcy biletów i może potrwać 10 minut lub dłużej.

### <a name="deploy-sql-data-warehouse-data-factory-and-blob-storage"></a>Wdrażanie fabryki danych, SQL Data Warehouse i Blob Storage 
W aplikacji Wingtip Tickets dzierżawców transakcyjnych dane są rozproszone w wielu bazach danych. Usługa Azure Data Factory (ADF) jest używany do organizowania wyodrębniania, ładowania i przekształcania (ELT) tych danych do magazynu danych. Ładowanie danych do usługi SQL Data Warehouse najbardziej efektywne, wyodrębnia dane do plików pośrednich obiektu blob ADF, a następnie używa [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/design-elt-data-loading) do ładowania danych do magazynu danych.   

W tym kroku wdrożysz dodatkowe zasoby używane w tym samouczku: usługa SQL Data Warehouse o nazwie _tenantanalytics_, fabryki danych Azure o nazwie _dbtodwload -\<użytkownika\>_  , oraz konto magazynu platformy Azure o nazwie _wingtipstaging\<użytkownika\>_ . Konto magazynu jest używane do tymczasowego przechowywania plików wyodrębnione dane jako obiekty BLOB, zanim zostaną załadowane do magazynu danych. Ten krok również wdraża schemat magazynu danych i definiuje potoki usługi ADF, służących do organizowania procesu ELT.
1. Otwórz w środowisku PowerShell ISE *...\Learning Modules\Operational Analytics\Tenant Analytics DW\Demo-TenantAnalyticsDW.ps1* i ustaw:
    - **$DemoScenario** = **2** wdrożenia magazynu danych analiz dzierżawy, magazynu obiektów blob i data factory 
1. Naciśnij klawisz **F5** uruchomić skrypt pokazowy i wdrażaj zasoby platformy Azure. 

Przejrzyj teraz przez wdrożone zasoby platformy Azure:
#### <a name="tenant-databases-and-analytics-store"></a>Magazyn baz danych i analizy dzierżawy
Użyj [programu SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) połączyć się z **tenants1-dpt -&lt;użytkownika&gt;**  i **katalogu-dpt -&lt;użytkownika&gt;**  serwerów. Zastąp &lt;użytkownika&gt; o wartości podanej podczas wdrażania aplikacji. Użyj nazwy logowania = *developer* i hasło = *P\@ssword1*. Zobacz [Samouczek wprowadzający](saas-dbpertenant-wingtip-app-overview.md) Aby uzyskać więcej wskazówek.

![Nawiązać połączenie z serwerem usługi SQL Database z programu SSMS](media/saas-tenancy-tenant-analytics/ssmsSignIn.JPG)

W Eksploratorze obiektów:

1. Rozwiń *tenants1-dpt -&lt;użytkownika&gt;*  serwera.
1. Rozwiń węzeł bazy danych i wyświetlić listę baz danych dzierżaw.
1. Rozwiń *catalog-dpt -&lt;użytkownika&gt;*  serwera.
1. Sprawdź, czy widzisz analizy, przechowywania, zawierającą następujące obiekty:
    1. Tabele **raw_Tickets**, **raw_Customers**, **raw_Events** i **raw_Venues** przechowywania nieprzetworzone dane wyodrębnione z baz danych dzierżaw.
    1. Tabele w schemacie gwiazdy są **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events**, i **dim_Dates** .
    1. Procedura składowana **sp_transformExtractedData** jest używany do przekształcania danych i ładuje je do tabel w schemacie gwiazdy.

![DWtables](media/saas-tenancy-tenant-analytics/DWtables.JPG)

#### <a name="blob-storage"></a>Blob Storage
1. W [witryny Azure Portal](https://ms.portal.azure.com), przejdź do grupy zasobów, którego używano do wdrażania aplikacji. Sprawdź, czy konto magazynu o nazwie **wingtipstaging\<użytkownika\>**  został dodany.

   ![DWtables](media/saas-tenancy-tenant-analytics/adf-staging-storage.PNG)

1. Kliknij przycisk **wingtipstaging\<użytkownika\>**  konta magazynu, aby eksplorować obiekty obecne.
1. Kliknij przycisk **obiektów blob** kafelka
1. Kliknij kontener **configfile**
1. Upewnij się, że **configfile** zawiera plik JSON o nazwie **TableConfig.json**. Ten plik zawiera nazw tabel źródłowych i docelowych, nazwy kolumn i nazwę kolumny śledzenia.

#### <a name="azure-data-factory-adf"></a>Azure Data Factory (ADF)
W [witryny Azure Portal](https://ms.portal.azure.com) w grupie zasobów, sprawdź, czy fabryki danych Azure o nazwie _dbtodwload -\<użytkownika\>_  został dodany. 

 ![adf_portal](media/saas-tenancy-tenant-analytics/adf-data-factory-portal.png)

W tej sekcji przedstawiono utworzyć fabrykę danych. Wykonaj poniższe kroki, aby uruchomić usługi data factory:
1. W portalu kliknij fabrykę danych o nazwie **dbtodwload -\<użytkownika\>** .
2. Kliknij przycisk **tworzenie i monitorowanie** Kafelek, aby uruchomić projektanta usługi Data Factory na osobnej karcie. 

## <a name="extract-load-and-transform-data"></a>Wyodrębnij obciążenia i przekształcania danych
Usługa Azure Data Factory służy do aranżacji wyodrębniania, ładowania i przekształcania danych. W tym samouczku wyodrębniania danych z czterech różnych widoków SQL z każdej z baz danych dzierżaw: **rawTickets**, **rawCustomers**, **rawEvents**, i  **rawVenues**. Widoki te obejmują właściwości identyfikatora, dzięki czemu można stwierdzić, dane z każdego miejsca w magazynie danych. Dane są ładowane do odpowiadających jej tabel tymczasowych w magazynie danych: **raw_Tickets**, **raw_customers**, **raw_Events** i **raw_Venue**. Procedura składowana następnie przekształca pierwotne dane i wypełnia tabele schematu gwiazdy: **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events** , i **dim_Dates**.

W poprzedniej sekcji wdrożyć i zainicjować wymaganych zasobów platformy Azure, w tym usługi data factory. Fabryka danych wdrożonej zawiera potoków, zestawy danych, połączonych usług, itp., które są wymagane do wyodrębniania, ładowania i przekształcania danych dzierżawy. Przyjrzyjmy się, że te obiekty, a następnie wyzwalacz potoku do przenoszenia danych z baz danych dzierżaw w magazynie danych.

### <a name="data-factory-pipeline-overview"></a>Omówienie potoku usługi Data factory
W tej sekcji przedstawiono obiekty utworzone w usłudze data factory. Następujący rysunek opisuje ogólnego przepływu pracy w potoku usługi ADF używanych w tym samouczku. Jeśli wolisz potoku później i wyświetlić wyniki najpierw przejść do następnej sekcji **wyzwalanie uruchomienia potoku**.

![adf_overview](media/saas-tenancy-tenant-analytics/adf-data-factory.PNG)

Na stronie przeglądu, przełącz się do **Autor** kartę w lewym panelu i sprawdź, czy dostępne są trzy [potoki](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) i trzema [zestawów danych](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services) utworzone.
![adf_author](media/saas-tenancy-tenant-analytics/adf_author_tab.JPG)

Trzy potoki zagnieżdżone są: SQLDBToDW DBCopy i TableCopy.

**Potok 1 - SQLDBToDW** wyszukuje nazwy baz danych dzierżaw, przechowywane w bazie danych wykazów (nazwa tabeli: [__ShardManagement]. [ ShardsGlobal]) i dla każdej bazy danych dzierżawy, wykonuje **DBCopy** potoku. Po zakończeniu podane **sp_TransformExtractedData** schematu procedury składowanej jest wykonywany. Tę procedurę składowaną przekształca załadowanych danych w tabelach przemieszczania i wypełnia tabele schematu gwiazdy.

**Potok 2 - DBCopy** odwołuje się do nazw tabel źródłowych i kolumn w pliku konfiguracji przechowywanych w magazynie obiektów blob.  **TableCopy** następnie uruchomienie potoku dla każdego z czterema tabelami: TicketFacts, CustomerFacts, EventFacts i VenueFacts. **[Foreach](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity)** działanie wykonywane równolegle we wszystkich 20 bazach danych. ADF pozwala maksymalnie 20 pętli iteracji do równoległego uruchamiania. Należy rozważyć utworzenie wiele potoków dla większej liczby baz danych.    

**Potok 3 - TableCopy** używa wiersz numery wersji w bazie danych SQL (_rowversion_) do identyfikowania wierszy, które zostały zmienione lub aktualizowane. To działanie wyszukuje rozpoczęcia i zakończenia wersji wiersza do wyodrębniania wierszy z tabel źródłowych. **CopyTracker** tabel przechowywanych w każdej bazie danych dzierżawy śledzi ostatni wiersz wyodrębnione z każdej tabeli źródłowej, w każdym przebiegu. Nowe lub zostały zmienione wiersze są kopiowane do odpowiedniego tabel przemieszczania w magazynie danych: **raw_Tickets**, **raw_Customers**, **raw_Venues**, i **raw_ Zdarzenia**. Na koniec ostatniej wersji wierszy jest zapisywany w **CopyTracker** tabeli, aby służyć jako wersja wiersza początkowego dla następnego wyodrębniania. 

Istnieją również trzy sparametryzowane połączone usługi, ten link fabrykę danych źródłowych baz danych SQL, element docelowy magazyn danych SQL i pośredni magazyn obiektów Blob. W **Autor** kartę, kliknij pozycję **połączeń** Eksplorowanie połączonych usług, jak pokazano na poniższej ilustracji:

![adf_linkedservices](media/saas-tenancy-tenant-analytics/linkedservices.JPG)

Odpowiadający trzy połączone usługi, istnieją trzy zestawów danych, które odwołują się do danych, używane w działaniach potoku jako dane wejściowe lub wyjściowe. Zapoznaj się z wszystkich zestawów danych, aby obserwować połączeń i parametrów używanych. _AzureBlob_ wskazuje plik konfiguracyjny zawierający źródłowe i docelowe tabele i kolumny, a także w kolumnie tracker każdego źródła.
  
### <a name="data-warehouse-pattern-overview"></a>Omówienie wzorca magazynu danych
Usługa SQL Data Warehouse służy do przechowywania analizy do przeprowadzania agregacji danych dzierżawy. W tym przykładzie PolyBase jest używana do ładowania danych do magazynu danych SQL. Nieprzetworzone dane są ładowane do tabel przemieszczania, które mają kolumnę tożsamości, aby śledzić wiersze, które zostały przekształcone w tabelach schematu gwiazdy. Na poniższej ilustracji przedstawiono wzorzec obciążenia: ![loadingpattern](media/saas-tenancy-tenant-analytics/loadingpattern.JPG)

Wolno tabel na 1 wymiar typu — Zmiana wymiaru (SCD) są używane w tym przykładzie. Każdy wymiar ma klucz zastępczy zdefiniowane przy użyciu kolumny tożsamości. Najlepszym rozwiązaniem jest tabeli wymiarów daty są wstępnie wypełnione, aby zaoszczędzić czas. Dla innych tabel wymiarów Tworzenie tabeli instrukcji SELECT... Instrukcja (CTAS) jest używana do tworzenia tabeli tymczasowej zawierających istniejące wiersze zmodyfikowane i niezmodyfikowane, wraz z kluczy zastępczy. Jest to zrobić za pomocą IDENTITY_INSERT = ON. Nowe wiersze są następnie wstawione do tabeli za pomocą IDENTITY_INSERT = OFF. Łatwe wycofywania, aby uzyskać istniejącej tabeli wymiarów została zmieniona, a tabela tymczasowa została zmieniona na stać się nową tabelę wymiarów. Przed każdym przebiegu zostanie usunięty ze starej tabeli wymiarów.

Tabele wymiarów są załadowane przed tabeli faktów. Sekwencjonowanie to gwarantuje, czy dla każdego nadchodzących fakt wszystkie wymiary odwołania już istnieją. Jak fakty są ładowane, klucza biznesowego dla każdego odpowiedniego wymiaru jest dopasowywany i odpowiadających im kluczy zastępcze są dodawane do każdego faktów.

Ostatnim krokiem przekształcenia usuwa przemieszczania danych gotowe do następnego wykonania potoku.
   
### <a name="trigger-the-pipeline-run"></a>Wyzwalanie uruchomienia potoku
Obserwuj poniższe kroki, aby uruchomić pełne wyodrębniania, ładowania i transformacji potoku dla wszystkich baz danych dzierżawy:
1. W **Autor** kartę interfejsu użytkownika usługi ADF, wybierz opcję **SQLDBToDW** potoku z poziomu okienka po lewej stronie.
1. Kliknij przycisk **wyzwalacza** i ściągnięty menu rozwijane kliknij **Wyzwól teraz**. Ta akcja uruchamia potok natychmiast. W scenariuszu produkcji zdefiniujesz harmonogram uruchamiania potoku, aby odświeżyć dane zgodnie z harmonogramem.
  ![adf_trigger](media/saas-tenancy-tenant-analytics/adf_trigger.JPG)
1. Na **uruchomienie potoku** kliknij **Zakończ**.
 
### <a name="monitor-the-pipeline-run"></a>Monitorowanie działania potoku
1. W interfejsie użytkownika usługi ADF, przełącz się do **Monitor** kartę z menu po lewej stronie.
1. Kliknij przycisk **Odśwież** momentu stan potoku SQLDBToDW **Powodzenie**.
  ![adf_monitoring](media/saas-tenancy-tenant-analytics/adf_monitoring.JPG)
1. Połączyć się z magazynem danych za pomocą programu SSMS i wykonywać zapytania dotyczące tabel schematu gwiazdy, aby sprawdzić, czy dane zostały załadowane w tych tabelach.

Po zakończeniu potoku Tabela faktów zawiera dane sprzedaży biletów dla wszystkich miejsc, a tabele wymiarów są wypełniane przy użyciu odpowiednich miejsc, zdarzeń i klientów.

## <a name="data-exploration"></a>Eksploracja danych

### <a name="visualize-tenant-data"></a>Wizualizowanie danych dzierżawy

Dane w schemacie gwiazdy zawiera wszystkie sprzedaży dane biletu potrzebami analizy. Wizualizowanie danych w formie graficznej sprawia, że łatwiej zobaczyć trendy w dużych zestawów danych. W tej sekcji użyjesz **usługi Power BI** do manipulowania i wizualizowanie danych dzierżawy w magazynie danych.

Aby połączyć usługę Power BI i zaimportować widoków, która została utworzona wcześniej, wykonaj następujące czynności:

1. Uruchom usługi Power BI desktop.
2. Na karcie wstążki Narzędzia główne, wybierz **Pobierz dane**i wybierz **więcej...** w menu.
3. W **Pobierz dane** wybierz **usługi Azure SQL Database**.
4. W oknie logowania bazy danych, wprowadź nazwę serwera (**catalog-dpt -&lt;użytkownika&gt;. database.windows.net**). Wybierz **importu** dla **tryb łączności danych**, a następnie kliknij przycisk **OK**. 

    ![Sign w do power-bi](./media/saas-tenancy-tenant-analytics/powerBISignIn.PNG)

5. Wybierz **bazy danych** w okienku po lewej stronie, a następnie wprowadź nazwę użytkownika = *developer*, a następnie wprowadź hasło = *P\@ssword1*. Kliknij przycisk **Połącz**.  

    ![database-sign-in](./media/saas-tenancy-tenant-analytics/databaseSignIn.PNG)

6. W **Nawigator** okienku w obszarze analitycznej bazy danych, wybierz tabele schematu gwiazdy: **fact_Tickets**, **dim_Events**, **dim_Venues**, **dim_Customers** i **dim_Dates**. Następnie wybierz pozycję **obciążenia**. 

Gratulacje! Pomyślnie ładowania danych do usługi Power BI. Teraz możesz eksplorować interesujące wizualizacje w celu uzyskania szczegółowych informacji o Twojej dzierżawy. Przejdźmy teraz przez jak analytics zapewnia kilka zaleceń opartych na danych, które zespół biznesowy o nazwie Wingtip Tickets. Zalecenia mogą pomóc poprawić komfort modelu i klientów biznesowych.

Rozpocznij od analizowanie danych sprzedaży biletów, aby zobaczyć zmienność użycie różnych miejsc. Wybierz opcje widoczne w usłudze Power BI do wykreślenia wykresu słupkowego z łączną liczbą biletów sprzedawane przez każdego miejsca. (Z powodu zmienności losowej w generatorze biletu, wyniki mogą być różne.)
 
![TotalTicketsByVenues](./media/saas-tenancy-tenant-analytics/TotalTicketsByVenues-DW.PNG)

Poprzedni diagram potwierdza, że różni się liczbą biletów sprzedawane przez każde miejsce. Lokalizacje, które sprzedawać bilety więcej korzystają z usługi bardziej intensywnie miejsc, które sprzedawać bilety mniejszej liczby. Może to być szansy sprzedaży w tym miejscu można dostosować alokacji zasobów zgodnie z potrzebami innej dzierżawy.

Może dalej analizować dane, aby zobaczyć, jak sprzedaż biletów różnią się wraz z upływem czasu. Wybierz opcje pokazano na poniższej ilustracji w usłudze Power BI do wykreślenia całkowita liczba sprzedanych każdego dnia przez 60 dni biletów.
 
![SaleVersusDate](./media/saas-tenancy-tenant-analytics/SaleVersusDate-DW.PNG)

Powyższym wykresie przedstawiono tego niektóre lokalizacje nagły sprzedaży biletów. Te wartości graniczne wzmocnienie pomysł, że niektóre miejsca może zużywałoby zasobów systemowych nieproporcjonalnie. Do tej pory w nie ma żadnych oczywistych wzorzec po wystąpieniu skokom.

Dalej umożliwia badanie znaczenia te dni szczytu w sprzedaży. Gdy te wartości szczytowe występować po biletów, przejdź do sprzedaży? Do wykreślenia sprzedanych dziennie biletów, wybierz opcje pokazano na poniższej ilustracji w usłudze Power BI.

![SaleDayDistribution](./media/saas-tenancy-tenant-analytics/SaleDistributionPerDay-DW.PNG)

Ten wykres pokazuje, że niektóre venues przekonywać dużej liczby biletów pierwszego dnia sprzedaży. Jak przejść bilety na sprzedaż w tych miejsc, prawdopodobnie mad łazienkowych. Tej serii działania przez kilka venues mogą mieć wpływ na usługi w przypadku innych dzierżaw.

Możesz przejść do szczegółów danych ponownie, aby sprawdzić, czy ten mad łazienkowych jest wartość true dla wszystkich zdarzeń obsługiwanych przez te lokalizacje. W poprzednim powierzchni pokazano, że firmy Contoso Concert Hall sprzedaje wiele biletów i że Contoso również ma największy wzrost sprzedaży biletów w w określonych dniach. Poeksperymentuj z opcji usługi Power BI do wykreślenia sprzedaż biletów zbiorcza dla firmy Contoso Concert Hall, skupiając się na trendy sprzedaży dla każdego z jego zdarzeń. Wszystkie zdarzenia wykonuj tego samego wzorca sprzedaż? Spróbuj utworzyć wykres, tak jak poniżej.

![ContosoSales](media/saas-tenancy-tenant-analytics/EventSaleTrends.PNG)

Tego wykresu skumulowanego biletu sprzedaży wraz z upływem czasu dla firmy Contoso Concert Hall dla każdego zdarzenia pokazuje, że mad łazienkowych nie odbywa się dla wszystkich zdarzeń. Poeksperymentuj z opcje filtru, aby Eksploruj trendy sprzedaży dla innych miejsc.

Wgląd w wzorców sprzedaży biletów może prowadzić Wtp w celu zoptymalizowania swojego modelu biznesowego. Być może zamiast ładowana wszystkich dzierżaw w równym stopniu, o nazwie Wingtip powinno wprowadzić warstwy usługi o różnych rozmiarów wystąpień obliczeniowych. Większe miejsc, wymagających do sprzedaży biletów więcej dziennie mogłoby być oferowane wyższego poziomu z wyższym Umowa dotycząca poziomu usług (SLA). Te miejsca może mieć swoje bazy danych, umieszczane w puli z wyższymi limitami zasobów bazy danych. Każda warstwa usługi może mieć godzinowe alokacji sprzedaży, za pomocą dodatkowe opłaty za przekroczenia przydziału. Venues większe, mających okresowe wzrosty sprzedaży będą korzystać z wyższej warstwy, a Wingtip Tickets można efektywniej Zarabiaj usługi.

W międzyczasie niektórzy klienci Wingtip Tickets reklamację, mogą mieć trudności z sprzedawać za mało bilety ponoszenie kosztów usługi. Być może w wglądów istnieje możliwość zwiększania sprzedaży biletów dla gorszych wynikach w przypadku miejsca. Wyższe sprzedaży wydłuży postrzegany wartość usługi. Kliknij prawym przyciskiem myszy fact_Tickets, a następnie wybierz pozycję **nową miarę**. Wprowadź następujące wyrażenie dla nowej miary o nazwie **AverageTicketsSold**:

```
AverageTicketsSold = DIVIDE(DIVIDE(COUNTROWS(fact_Tickets),DISTINCT(dim_Venues[VenueCapacity]))*100, COUNTROWS(dim_Events))
```

Wybierz następujące opcje wizualizacji, do wykreślenia bilety procent sprzedawane przez każdego miejsca, aby określić ich względną Powodzenie.

![AvgTicketsByVenues](media/saas-tenancy-tenant-analytics/AvgTicketsByVenues-DW.PNG)

Wykres powyżej przedstawiono, mimo że większość venues sprzedawać ponad 80% biletami, niektóre ciężko jest wypełnienie ponad połowę stanowiska. Poeksperymentuj z wartości również wybrać procent maksymalnej lub minimalnej biletów sprzedaży dla każdego miejsca.

## <a name="embedding-analytics-in-your-apps"></a>Osadzasz analizę w swoich aplikacjach 
W tym samouczku skupiła się na analytics międzydzierżawowa wykorzystywane do ulepszania zrozumieć dzierżawcom, dostawcy oprogramowania. Analiza można też podać szczegółowe informacje umożliwiające _dzierżaw_, aby pomóc im efektywniej zarządzać swoją działalność samodzielnie. 

W przykładzie aplikacji Wingtip Tickets możesz wcześniej odnalezionych sprzedaż biletów zwykle postępuj zgodnie z przewidywalnymi wzorcami. Te szczegółowe informacje mogą służyć do sprzedaży biletów boost venues gorszych wynikach w przypadku. Być może jest możliwość stosować techniki uczenia maszyny do prognozowania sprzedaży biletów dla zdarzeń. Efekty zmian cen można również modelować, umożliwiające oferty rabatów można przewidzieć wpływ. Power BI Embedded może być zintegrowane zdarzeń aplikacji do zarządzania wizualizacji przewidywane sprzedaży, włącznie ze wpływ rabaty na stanowiska całkowitej sprzedaży i przychody ze sprzedaży niski zdarzenia. Za pomocą Power BI Embedded można nawet zintegrować faktycznie stosowania ten rabat do cen biletu, kliknij prawym przyciskiem myszy w środowisku wizualizacji.


## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wdróż usłudze SQL Data Warehouse wypełniane przy użyciu schematu gwiazdy dla informacji analitycznych.
> * Azure Data Factory służy do wyodrębniania danych z każdej bazy danych dzierżawy do magazynu danych analiz.
> * Optymalizuj wyodrębnione dane (z opcją reorganize w schemacie gwiazdy).
> * Zapytania magazynu danych analiz. 
> * Usługa Power BI umożliwia wizualizowanie trendów w danych we wszystkich dzierżawach.

Gratulacje!

## <a name="additional-resources"></a>Dodatkowe zasoby

- Dodatkowe [samouczki nawiązujące do aplikacji SaaS o nazwie Wingtip](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
