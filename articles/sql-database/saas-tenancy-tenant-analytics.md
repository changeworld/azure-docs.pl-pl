---
title: Analiza między dzierżawcami przy użyciu wyodrębnionych danych
description: Zapytania analityczne między dzierżawcami przy użyciu danych wyodrębnionych z wielu baz danych usługi Azure SQL Database w jednej aplikacji dzierżawy.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: anjangsh,billgib,genemi
ms.date: 12/18/2018
ms.openlocfilehash: c589d9619da8b5150d0fb4752625571c48393552
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73826377"
---
# <a name="cross-tenant-analytics-using-extracted-data---single-tenant-app"></a>Analiza między dzierżawcami przy użyciu wyodrębnionych danych — aplikacja z jedną dzierżawą
 
W tym samouczku przejdziesz przez kompletny scenariusz analizy dla implementacji pojedynczej dzierżawy. Scenariusz pokazuje, w jaki sposób analizy mogą umożliwić firmom podejmowanie inteligentnych decyzji. Korzystając z danych wyodrębnionych z każdej bazy danych dzierżawy, można użyć analizy, aby uzyskać wgląd w zachowanie dzierżawy, w tym ich użycie przykładowej aplikacji SaaS bilety Wingtip. Ten scenariusz obejmuje trzy kroki: 

1.  **Wyodrębnij** dane z każdej bazy danych dzierżawy i **załaduj** do magazynu analizy.
2.  **Przekształć wyodrębnione dane** do przetwarzania analitycznego.
3.  Korzystaj z narzędzi **analizy biznesowej,** aby uzyskać przydatne informacje, które mogą kierować podejmowaniem decyzji. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> - Utwórz magazyn analizy dzierżawy, aby wyodrębnić dane.
> - Użyj zadań elastycznych, aby wyodrębnić dane z każdej bazy danych dzierżawy do magazynu analizy.
> - Optymalizacja wyodrębnionych danych (reorganizacja do schematu gwiazdy).
> - Kwerenda bazy danych analizy.
> - Użyj usługi Power BI do wizualizacji danych, aby wyróżnić trendy w danych dzierżawy i zalecić ulepszenia.

![architekturaOverView](media/saas-tenancy-tenant-analytics/architectureOverview.png)

## <a name="offline-tenant-analytics-pattern"></a>Wzorzec analizy dzierżawy w trybie offline

Wielodostępne aplikacje SaaS zazwyczaj mają ogromną ilość danych dzierżawy przechowywanych w chmurze. Te dane zapewniają bogate źródło szczegółowych informacji na temat działania i użycia aplikacji oraz zachowania dzierżawców. Te szczegółowe informacje mogą prowadzić do rozwoju funkcji, ulepszeń użyteczności i innych inwestycji w aplikację i platformę.

Uzyskiwanie dostępu do danych dla wszystkich dzierżawców jest proste, gdy wszystkie dane są w jednej bazie danych z wieloma dzierżawami. Ale dostęp jest bardziej złożony, gdy są dystrybuowane na dużą skalę w potencjalnie tysiące baz danych. Jednym ze sposobów, aby oswoić złożoność i zminimalizować wpływ zapytań analitycznych na dane transakcyjne jest wyodrębnienie danych do specjalnie zaprojektowanej bazy danych analizy lub magazynu danych.

W tym samouczku przedstawiono kompletny scenariusz analizy dla aplikacji SaaS bilety Wingtip. Najpierw *zadania elastyczne* jest używany do wyodrębniania danych z każdej bazy danych dzierżawy i załadować go do tabel przejściowych w magazynie analizy. Magazyn analizy może być bazą danych SQL lub magazynem danych SQL. W przypadku wyodrębniania danych na dużą skalę zalecana jest [usługa Azure Data Factory.](../data-factory/introduction.md)

Następnie zagregowane dane są przekształcane w zestaw tabel [schematu gwiazdy.](https://www.wikipedia.org/wiki/Star_schema) Tabele składają się z centralnej tabeli faktów oraz powiązanych tabel wymiarów.  Dla biletów Wingtip:

- Centralna tabela faktów w schemacie gwiazdy zawiera dane biletu.
- Tabele wymiarów opisują miejsca, wydarzenia, klientów i daty zakupu.

Razem centralne tabele faktów i wymiarów umożliwiają wydajne przetwarzanie analityczne. Schemat gwiazdy używany w tym samouczku jest pokazany na poniższej ilustracji:
 
![architekturaOverView](media/saas-tenancy-tenant-analytics/StarSchema.png)

Na koniec magazyn analizy jest wyszukiwany przy użyciu **usługi PowerBI,** aby wyróżnić wgląd w zachowanie dzierżawy i ich użycie aplikacji Wingtip Tickets. Można uruchomić kwerendy, które:
 
- Pokaż względną popularność każdego miejsca
- Wyróżnianie wzorców w sprzedaży biletów dla różnych wydarzeń
- Pokaż względny sukces różnych miejsc w wyprzedaniu ich imprezy

Zrozumienie, jak każdy dzierżawca korzysta z usługi jest używany do eksplorowania opcji zarabiania na usłudze i ulepszania usługi, aby pomóc dzierżawcom odnieść większy sukces. Ten samouczek zawiera podstawowe przykłady rodzajów szczegółowych informacji, które można zbierać z danych dzierżawy.

## <a name="setup"></a>Konfiguracja

### <a name="prerequisites"></a>Wymagania wstępne

Do wykonania zadań opisanych w tym samouczku niezbędne jest spełnienie następujących wymagań wstępnych:

- Wdrożono aplikację Bazy danych SaaS na dzierżawę Wingtip Tickets. Aby wdrożyć w mniej niż pięć minut, zobacz [Wdrażanie i eksplorowanie aplikacji SaaS Wingtip](saas-dbpertenant-get-started-deploy.md)
- Skrypty bazy danych SaaS na dzierżawę i [kod źródłowy](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/) aplikacji są pobierane z usługi GitHub. Zobacz instrukcje pobierania. Pamiętaj, aby *odblokować plik zip* przed wyodrębnieniem jego zawartości. Zapoznaj się z [ogólnymi wskazówkami dotyczącymi](saas-tenancy-wingtip-app-guidance-tips.md) pobierania i odblokowywania skryptów SaaS biletów Wingtip.
- Program Power BI Desktop jest zainstalowany. [Pobieranie programu Power BI Desktop](https://powerbi.microsoft.com/downloads/)
- Partia dodatkowych dzierżaw został aprowizny, zobacz [**instruktaż dzierżawy obsługi .**](saas-dbpertenant-provision-and-catalog.md)
- Utworzono bazę danych konta zlecenia i konta zadania. Zobacz odpowiednie kroki w [**samouczku zarządzania schematem**](saas-tenancy-schema-management.md#create-a-job-agent-database-and-new-job-agent).

### <a name="create-data-for-the-demo"></a>Tworzenie danych dla wersji demonstracyjnej

W tym samouczku analiza jest wykonywana na danych sprzedaży biletów. W bieżącym kroku generujesz dane biletów dla wszystkich dzierżawców.  Później te dane są wyodrębniane do analizy. *Upewnij się, że udostępniłeś partię dzierżaw, jak opisano wcześniej, tak aby mieć znaczącą ilość danych.* Wystarczająco duża ilość danych może ujawnić szereg różnych wzorców zakupu biletów.

1. W programie PowerShell ISE otwórz *...\Learning Modules\Operational Analytics\Tenant Analytics\Demo-TenantAnalytics.ps1*i ustaw następującą wartość:
    - **$DemoScenario**1 Kup bilety na wydarzenia we wszystkich miejscach**1**  = 
2. Naciśnij **klawisz F5,** aby uruchomić skrypt i utworzyć historię zakupów biletów dla każdego wydarzenia w każdym miejscu.  Skrypt działa przez kilka minut, aby wygenerować dziesiątki tysięcy biletów.

### <a name="deploy-the-analytics-store"></a>Wdrażanie magazynu analiz
Często istnieje wiele transakcyjnych baz danych, które razem przechowują wszystkie dane dzierżawy. Należy agregować dane dzierżawy z wielu transakcyjnych baz danych w jednym magazynie analizy. Agregacja umożliwia wydajne zapytanie danych. W tym samouczku baza danych usługi Azure SQL Database jest używana do przechowywania zagregowanych danych.

W poniższych krokach można wdrożyć magazyn analizy, który jest nazywany **tenantanalytics**. Można również wdrożyć wstępnie zdefiniowane tabele, które są wypełniane w dalszej części samouczka:
1. W programie PowerShell ISE otwórz *...\Moduły szkoleniowe\Analiza operacyjna\Analityka dzierżawy\Analiza demo-tenant.ps1* 
2. Ustaw zmienną $DemoScenario w skrypcie, aby dopasować się do wybranego magazynu analiz:
    - Aby korzystać z bazy danych SQL bez magazynu kolumn, ustaw **$DemoScenario** = **2**
    - Aby użyć bazy danych SQL ze magazynem kolumn, ustaw **$DemoScenario** = **3**  
3. Naciśnij **klawisz F5,** aby uruchomić skrypt demonstracyjny (który wywołuje *\<deploy-tenantanalytics XX>.ps1* skrypt), który tworzy magazyn analizy dzierżawy. 

Teraz, gdy wdrożono aplikację i wypełniłeś ją interesującymi danymi dzierżawy, użyj [SQL Server Management Studio (SSMS),](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) aby połączyć **tenants1-dpt-&lt;Użytkownik&gt; ** i **katalog-dpt-&lt;&gt; ** Serwery użytkownika za pomocą loginu = *dewelopera*, Hasło = *P\@ssword1*. Zobacz [samouczek wprowadzający, aby](saas-dbpertenant-wingtip-app-overview.md) uzyskać więcej wskazówek.

![architekturaOverView](media/saas-tenancy-tenant-analytics/ssmsSignIn.png)

W Eksploratorze obiektów wykonaj następujące czynności:

1. Rozwiń *serwer tenants1-dpt-&lt;User.&gt; *
2. Rozwiń węzeł Bazy danych i zobacz listę baz danych dzierżawy.
3. Rozwiń *&lt;katalog-dpt-&gt; Serwer użytkownika.*
4. Sprawdź, czy widzisz magazyn analizy i bazę danych jobaccount.

Zobacz następujące elementy bazy danych w Eksploratorze obiektów SSMS, rozwijając węzeł magazynu analizy:

- Tabele **TicketsRawData** i **EventsRawData** przechowywać nieprzetworzone wyodrębnione dane z baz danych dzierżawy.
- Tabele schematów gwiazdek są **fact_Tickets** **, dim_Customers**, **dim_Venues,** **dim_Events**i **dim_Dates**.
- Procedura składowana służy do wypełniania tabel schematu gwiazdy z tabel nieprzetworzonych danych.

![architekturaOverView](media/saas-tenancy-tenant-analytics/tenantAnalytics.png)

## <a name="data-extraction"></a>Ekstrakcja danych 

### <a name="create-target-groups"></a>Tworzenie grup docelowych 

Przed kontynuowaniem upewnij się, że wdrożono konto zadania i bazę danych jobaccount. W następnym zestawie kroków zadania elastyczne służy do wyodrębniania danych z każdej bazy danych dzierżawy i do przechowywania danych w magazynie analizy. Następnie drugie zadanie strzępuje dane i przechowuje je w tabelach w schemacie gwiazdy. Te dwa zadania są uruchamiane przeciwko dwóm różnym grupom docelowym, a mianowicie **TenantGroup** i **AnalyticsGroup**. Zadanie wyodrębniania jest uruchamiane względem TenantGroup, która zawiera wszystkie bazy danych dzierżawy. Zadanie rozdrabniania jest uruchamiane przeciwko grupie AnalyticsGroup, która zawiera tylko magazyn analizy. Utwórz grupy docelowe, wykonując następujące kroki:

1. W SSMS połącz się z bazą danych **jobaccount** w catalog-dpt-&lt;User&gt;.
2. W SSMS otwórz *...\Moduły szkoleniowe\Analiza operacyjna\Analiza dzierżawy\ TargetGroups.sql* 
3. Zmodyfikuj zmienną @User u `<User>` góry skryptu, zastępując ją wartością użytkownika używaną podczas wdrażania aplikacji SaaS Wingtip.
4. Naciśnij **klawisz F5,** aby uruchomić skrypt, który tworzy dwie grupy docelowe.

### <a name="extract-raw-data-from-all-tenants"></a>Wyodrębnianie nieprzetworzonych danych ze wszystkich dzierżawców

Szeroko zakrojone modyfikacje danych mogą występować częściej w przypadku danych *biletowych i danych klienta* niż w przypadku danych *o zdarzeniach i miejscu.* W związku z tym należy rozważyć wyodrębnianie danych biletu i klienta oddzielnie i częściej niż wyodrębnić dane zdarzeń i miejsc. W tej sekcji należy zdefiniować i zaplanować dwa oddzielne zadania:

- Wyodrębnij dane biletów i klientów.
- Wyodrębnij dane zdarzeń i miejsc.

Każde zadanie wyodrębnia swoje dane i publikuje go w magazynie analiz. Istnieje oddzielne zadanie strzępy wyodrębnione dane do schematu gwiazdy analizy.

1. W systemie SSMS połącz się z bazą&lt;danych&gt; **jobaccount** na serwerze użytkownika catalog-dpt- User.
2. W programie SSMS otwórz *...\Moduły szkoleniowe\Analiza operacyjna\Analityka dzierżawy\ExtractTickets.sql*.
3. Modyfikowanie @User u góry skryptu `<User>` i zastępowanie nazwą użytkownika używaną podczas wdrażania aplikacji SaaS wingtip 
4. Naciśnij klawisz F5, aby uruchomić skrypt, który tworzy i uruchamia zadanie, które wyodrębnia dane biletów i klientów z każdej bazy danych dzierżawy. Zadanie zapisuje dane w magazynie analiz.
5. Kwerenda TicketsRawData tabeli w bazie danych tenantanalytics, aby upewnić się, że tabela jest wypełniona informacji o biletach od wszystkich dzierżawców.

![ticketWyświetlacze](media/saas-tenancy-tenant-analytics/ticketExtracts.png)

Powtórz poprzednie kroki, z wyjątkiem tego czasu zastąpić **\ExtractTickets.sql** z **\ExtractVenuesEvents.sql** w kroku 2.

Pomyślne uruchomienie zadania wypełnia tabelę EventsRawData w magazynie analizy nowymi zdarzeniami i informacjami o miejscach od wszystkich dzierżawców. 

## <a name="data-reorganization"></a>Reorganizacja danych

### <a name="shred-extracted-data-to-populate-star-schema-tables"></a>Rozdrabniane dane w celu zapełnienia tabel schematu gwiazdy

Następnym krokiem jest strzępy wyodrębnione nieprzetworzone dane do zestawu tabel, które są zoptymalizowane pod kątem zapytań analitycznych. Używany jest schemat gwiazdy. Centralna tabela faktów zawiera indywidualne rekordy sprzedaży biletów. Inne tabele są wypełniane powiązanymi danymi o miejscach, zdarzeniach i klientach. Są też tabele wymiarów czasu. 

W tej sekcji samouczka można zdefiniować i uruchomić zadanie, które scala wyodrębnione nieprzetworzone dane z danymi w tabelach schematu gwiazdy. Po zakończeniu zadania scalania nieprzetworzone dane są usuwane, pozostawiając tabele gotowe do zapełnienia przez następne zadanie wyodrębniania danych dzierżawy.

1. W SSMS połącz się z bazą danych **jobaccount** w catalog-dpt-&lt;User&gt;.
2. W programie SSMS otwórz *plik ...\Moduły szkoleniowe\Analiza operacyjna\Analityka dzierżawy\ShredRawExtractedData.sql*.
3. Naciśnij **klawisz F5,** aby uruchomić skrypt, aby zdefiniować zadanie, które wywołuje sp_ShredRawExtractedData procedura składowana w magazynie analiz.
4. Pozostaw wystarczająco dużo czasu, aby zadanie działało pomyślnie.
    - Sprawdź kolumnę **Cyklu życia** zadania.jobs_execution tabeli stanu zadania. Upewnij się, że zadanie **powiodło się** przed kontynuowaniem. Pomyślne uruchomienie wyświetla dane podobne do następującego wykresu:

![Rozdrabniania](media/saas-tenancy-tenant-analytics/shreddingJob.PNG)

## <a name="data-exploration"></a>Eksploracja danych

### <a name="visualize-tenant-data"></a>Wizualizuj dane dzierżawy

Dane w tabeli schematu gwiazdy zawiera wszystkie dane sprzedaży biletów potrzebne do analizy. Aby ułatwić wyświetlanie trendów w dużych zestawach danych, należy je graficznie wizualizować.  W tej sekcji dowiesz się, jak używać **usługi Power BI** do manipulowania i wizualizowania wyodrębnionych i zorganizowanych danych dzierżawy.

Aby połączyć się z programem Power BI i zaimportować widoki utworzone wcześniej, należy wykonać następujące czynności:

1. Uruchamianie programu Power BI dla komputerów stacjonarnych.
2. Na wstążce Strona główna wybierz pozycję **Pobierz dane**i wybierz pozycję **Więcej...** z menu.
3. W oknie **Pobierz dane** wybierz pozycję Baza danych SQL platformy Azure.
4. W oknie logowania do bazy danych wprowadź nazwę&lt;serwera&gt;(catalog-dpt- User .database.windows.net). Wybierz **pozycję Importuj** dla **trybu łączności danych,** a następnie kliknij przycisk OK. 

    ![signinpowerbi](./media/saas-tenancy-tenant-analytics/powerBISignIn.PNG)

5. Wybierz **opcję Baza danych** w lewym okienku, a następnie wprowadź nazwę użytkownika = *deweloper*i wprowadź hasło = *P\@ssword1*. Kliknij pozycję **Połącz**.  

    ![sygn.](./media/saas-tenancy-tenant-analytics/databaseSignIn.PNG)

6. W okienku **Nawigator** w bazie danych analizy wybierz tabele schematów gwiazd: fact_Tickets, dim_Events, dim_Venues, dim_Customers i dim_Dates. Następnie wybierz pozycję **Załaduj**. 

Gratulacje! Dane zostały pomyślnie załadowane do usługi Power BI. Teraz możesz rozpocząć eksplorowanie interesujących wizualizacji, aby uzyskać wgląd w dzierżawców. Następnie przejdziesz przez sposób, w jaki analiza może umożliwić dostarczanie rekomendacji opartych na danych zespołowi biznesowemu Wingtip Tickets. Zalecenia mogą pomóc w optymalizacji modelu biznesowego i obsługi klienta.

Zacznij od analizy danych sprzedaży biletów, aby zobaczyć różnice w użyciu w różnych miejscach. Wybierz następujące opcje w usłudze Power BI, aby wykreślić wykres słupkowy całkowitej liczby biletów sprzedanych przez każde miejsce. Ze względu na losowe różnice w generatorze biletów, twoje wyniki mogą być różne.
 
![TotalTicketsByVenues](./media/saas-tenancy-tenant-analytics/TotalTicketsByVenues.PNG)

Poprzednia działka potwierdza, że liczba biletów sprzedanych przez każde miejsce jest różna. Miejsca, które sprzedają więcej biletów, korzystają z usługi w większym stopniu niż miejsca, w których sprzedaje się mniej biletów. W tym miejscu może istnieć możliwość dostosowania alokacji zasobów do różnych potrzeb dzierżawy.

Możesz dalej analizować dane, aby zobaczyć, jak sprzedaż biletów zmienia się w czasie. Wybierz następujące opcje w usłudze Power BI, aby wykreślić łączną liczbę sprzedanych biletów każdego dnia przez okres 60 dni.
 
![Data sprzedaży](./media/saas-tenancy-tenant-analytics/SaleVersusDate.PNG)

Na poprzednim wykresie jest wyświetlany skok sprzedaży biletów w niektórych miejscach. Te skoki wzmacniają ideę, że niektóre miejsca mogą zużywać zasoby systemowe nieproporcjonalnie. Do tej pory nie ma oczywistego wzorca, gdy występują kolce.

Następnie chcesz dalej zbadać znaczenie tych szczytowych dni sprzedaży. Kiedy te szczyty występują po sprzedaży biletów? Aby wykreślić bilety sprzedane dziennie, wybierz następujące opcje w usłudze Power BI.

![SprzedażDayRozdystrybucja](./media/saas-tenancy-tenant-analytics/SaleDistributionPerDay.PNG)

Poprzednia działka pokazuje, że niektóre lokale sprzedają dużo biletów w pierwszym dniu sprzedaży. Jak tylko bilety trafią do sprzedaży w tych miejscach, wydaje się, że jest szalony pośpiech. Ten wzrost aktywności przez kilka miejsc może mieć wpływ na usługę dla innych dzierżawców.

Możesz przejść do szczegółów danych ponownie, aby zobaczyć, czy ten szalony pośpiech jest prawdziwy dla wszystkich wydarzeń organizowanych przez te miejsca. W poprzednich działkach zaobserwowano, że Contoso Concert Hall sprzedaje dużo biletów, a Contoso ma również skok sprzedaży biletów w określone dni. Poeksij z opcjami usługi Power BI, aby wykreślić skumulowaną sprzedaż biletów dla sali koncertowej Contoso, koncentrując się na trendach sprzedaży dla każdego z wydarzeń. Czy wszystkie zdarzenia są zgodne z tym samym wzorcem sprzedaży?

![ContosoSales (właso)](media/saas-tenancy-tenant-analytics/EventSaleTrends.PNG)

Poprzednia fabuła dla Sali Koncertowej Contoso pokazuje, że szalony pośpiech nie zdarza się we wszystkich wydarzeniach. Poeks wyjrzyj się z opcjami filtrów, aby zobaczyć trendy sprzedaży w innych miejscach.

Wgląd w wzorce sprzedaży biletów może prowadzić Wingtip Bilety do optymalizacji ich modelu biznesowego. Zamiast pobierać opłaty za wszystkie dzierżawy jednakowo, być może Wingtip należy wprowadzić warstwy usług o różnych rozmiarach obliczeń. Większe lokale, które muszą sprzedawać więcej biletów dziennie, mogą otrzymać wyższą warstwę z umową o wyższym poziomie usług (SLA). Te miejsca mogą mieć swoje bazy danych umieszczone w puli z wyższymi limitami zasobów dla bazy danych. Każda warstwa usług może mieć godzinową alokację sprzedaży, z dodatkowymi opłatami pobieranymi za przekroczenie alokacji. Większe miejsca, które mają okresowe wzrosty sprzedaży, skorzystają z wyższych poziomów, a bilety Wingtip mogą bardziej efektywnie zarabiać na swoich usługach.

Tymczasem niektórzy klienci Wingtip Tickets skarżą się, że mają trudności ze sprzedażą wystarczającej ilości biletów, aby uzasadnić koszt usługi. Być może w tych spostrzeżeniach istnieje możliwość zwiększenia sprzedaży biletów dla miejsc o słabych wynikach. Wyższa sprzedaż zwiększyłaby postrzeganą wartość usługi. Kliknij prawym przyciskiem myszy fact_Tickets i wybierz polecenie **Nowa miara**. Wprowadź następujące wyrażenie dla nowej miary o nazwie **AverageTicketsSold**:

```
AverageTicketsSold = AVERAGEX( SUMMARIZE( TableName, TableName[Venue Name] ), CALCULATE( SUM(TableName[Tickets Sold] ) ) )
```

Wybierz następujące opcje wizualizacji, aby wykreślić procent biletów sprzedanych przez każde miejsce, aby określić ich względny sukces.

![AvgTicketsByVenues](media/saas-tenancy-tenant-analytics/AvgTicketsByVenues.PNG)

Poprzednia działka pokazuje, że chociaż większość lokali sprzedaje ponad 80% swoich biletów, niektóre z nich mają trudności z obsadzeniem ponad połowy miejsc. Poeks wyekslij się z Dobrze wartości, aby wybrać maksymalny lub minimalny procent biletów sprzedanych dla każdego miejsca.

Wcześniej pogłębiłeś swoją analizę, aby odkryć, że sprzedaż biletów ma tendencję do podążania za przewidywalnymi wzorcami. To odkrycie może pozwolić, aby bilety Wingtip pomagały w słabszych miejscach zwiększyć sprzedaż biletów, zalecając dynamiczne ceny. To odkrycie może ujawnić możliwość stosowania technik uczenia maszynowego do przewidywania sprzedaży biletów dla każdego zdarzenia. Można również przewidzieć wpływ na przychody z oferowania rabatów na sprzedaż biletów. Usługa Power BI Embedded może być zintegrowana z aplikacją do zarządzania zdarzeniami. Integracja może pomóc wizualizować przewidywaną sprzedaż i wpływ różnych rabatów. Aplikacja może pomóc w opracowaniu optymalnego rabatu, który ma być stosowany bezpośrednio z wyświetlacza analitycznego.

Zaobserwowano trendy w danych dzierżawy z aplikacji WingTip. Można rozważyć inne sposoby aplikacji może informować decyzje biznesowe dla dostawców aplikacji SaaS. Sprzedawcy mogą lepiej zaspokoić potrzeby swoich najemców. Mam nadzieję, że ten samouczek wyposażył Cię w narzędzia niezbędne do przeprowadzania analiz danych dzierżawy, aby umożliwić firmom podejmowanie decyzji opartych na danych.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> - Wdrożono bazę danych analizy dzierżawy ze wstępnie zdefiniowanymi tabelami schematów gwiazd
> - Używane zadania elastyczne do wyodrębniania danych ze wszystkich baz danych dzierżawy
> - Scalanie wyodrębnionych danych w tabele w schemacie gwiazd przeznaczonym do analizy
> - Wykonywanie zapytań z bazy danych analizy 
> - Używanie usługi Power BI do wizualizacji danych w celu obserwowania trendów w danych dzierżawy 

Gratulacje!

## <a name="additional-resources"></a>Zasoby dodatkowe

- Dodatkowe [samouczki, które opierają się na aplikacji Wingtip SaaS](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
- [Elastyczne zadania](elastic-jobs-overview.md).
- [Analiza między dzierżawcami przy użyciu wyodrębnionych danych — aplikacja wielodostępna](saas-multitenantdb-tenant-analytics.md)