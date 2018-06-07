---
title: Uruchom analytics między dzierżawcy przy użyciu wyodrębnione dane | Dokumentacja firmy Microsoft
description: Zapytania analityczne między dzierżawcy przy użyciu danych pobranych z wielu baz danych z bazy danych SQL Azure.
keywords: samouczek usługi sql database
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.reviewer: anjangsh; billgib; genemi
ms.openlocfilehash: 68057a2ae5925aa16288844759a34592aa7c7573
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34644964"
---
# <a name="cross-tenant-analytics-using-extracted-data"></a>Analytics między dzierżawcy przy użyciu wyodrębnione dane

W tym samouczku opisano pośrednictwem ukończenia analizy. Scenariuszu pokazano, jak analytics można włączyć firmom podejmowanie decyzji inteligentne. Przy użyciu danych pobranych z każdej dzierżawy bazy danych, użyj analytics Aby uzyskać wgląd w dzierżawy użycia zachowanie i aplikacji. Ten scenariusz obejmuje trzy kroki: 

1.  **Wyodrębnij** danych z każdej dzierżawy bazy danych i **obciążenia** do analityka magazynu.
2.  **Przekształć wyodrębnione dane** przetwarzania analytics.
3.  Użyj **analizy biznesowej** narzędzi do rysowania limit przydatne informacje na temat technologii, które można kierować podejmowania decyzji. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> - Tworzenie dzierżawy, magazynie analytics, aby wyodrębnić dane do.
> - Zadania elastyczne umożliwiają wyodrębniania danych z każdej dzierżawy bazy danych w magazynie usługi analytics.
> - Optymalizuj wyodrębnione dane (z opcją reorganize na schemat gwiazdy).
> - Wyślij zapytanie do bazy danych analizy.
> - Użyj usługi Power BI do wizualizacji danych zaznacz trendów w danych dzierżawy i zalecenia dotyczące poprawy.

![architectureOverView](media/saas-tenancy-tenant-analytics/architectureOverview.png)

## <a name="offline-tenant-analytics-pattern"></a>Wzorzec analytics dzierżawy w trybie offline

Wielodostępnych aplikacji SaaS ma zwykle ogromnych ilości danych dzierżawy, przechowywane w chmurze. Dzięki tym danym firma sformatowanego źródła szczegółowych informacji na temat operacji i użycie aplikacji i zachowania dzierżawcom. Te szczegółowe informacje można przeprowadzają opracowanie funkcji, ulepszenia użyteczność i innych inwestycji w aplikacji i platform.

Uzyskiwanie dostępu do danych dla wszystkich dzierżawców jest proste, gdy wszystkie dane są tylko jeden wielodostępne bazy danych. Ale dostępu jest bardziej złożony, gdy na dużą skalę dystrybuowana do tysięcy baz danych. Jednym ze sposobów tame złożoność oraz zminimalizować wpływ analytics zapytania na danych transakcyjnych jest aby wyodrębnić dane do magazynu celu przeznaczony analytics bazy danych lub danych.

Ten samouczek przedstawia scenariusza pełną analytics dla aplikacji SaaS biletów Wingtip. Najpierw *zadania elastyczne* służy do wyodrębniania danych z każdej dzierżawy bazy danych i załaduj go do tymczasowej tabel w magazynie analytics. Magazyn analytics może być bazy danych SQL lub SQL Data Warehouse. Do wyodrębniania danych na dużą skalę [fabryki danych Azure](../data-factory/introduction.md) jest zalecane.

Następnie dane zagregowane jest przekształcana na zbiór [schematu gwiazdy](https://www.wikipedia.org/wiki/Star_schema) tabel. Tabele składają się z tabeli faktów centralnej plus wymiaru powiązanych tabel.  Bilety Wingtip:

- Tabela faktów centralnej w schemacie gwiazdkę zawiera dane biletu.
- Tabele wymiarów opisano miejsc, zdarzenia, klientów i zakupu daty.

Razem centralnej tabele faktów i wymiarów Włącz wydajność przetwarzania analitycznego. Schemat gwiazdy używane w tym samouczku pokazano na poniższej ilustracji:
 
![architectureOverView](media/saas-tenancy-tenant-analytics/StarSchema.png)

Na koniec analityka magazynu jest poddawany kwerendzie przy użyciu **PowerBI** aby wyróżnić wgląd w zachowanie dzierżawcy i ich użycia aplikacji Wingtip biletów. Uruchamianie zapytań, które:
 
- Pokaż względną popularne każdego miejscową
- Wyróżnij wzorce w sprzedaży biletów dla różnych zdarzeń
- Pokaż względną Powodzenie różnych miejsc w sprzedaży się ich zdarzeń

Opis sposobu każdego dzierżawcy jest przy użyciu usługi służy do zapoznaj się z opcjami monetizing usługi i poprawy usługi, aby ułatwić dzierżawcom więcej powiódł się. W tym samouczku zawiera podstawowe przykłady rodzajów szczegółowe informacje, które mogą być zgromadzone z danych dzierżawy.

## <a name="setup"></a>Konfiguracja

### <a name="prerequisites"></a>Wymagania wstępne

Do wykonania zadań opisanych w tym samouczku niezbędne jest spełnienie następujących wymagań wstępnych:

- Aplikacja Wingtip biletów SaaS bazy danych dla dzierżawy jest wdrożona. Aby wdrożyć w mniej niż 5 minut, zobacz [wdrażania i aplikacji Wingtip SaaS](saas-dbpertenant-get-started-deploy.md)
- Skrypty Wingtip biletów SaaS bazy danych dla dzierżawy i aplikacji [kod źródłowy](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/) są pobierane z usługi GitHub. Zobacz instrukcje pobierania. Upewnij się, *odblokować plik zip* przed wyodrębniania jego zawartość. Zapoznaj się z [ogólne wskazówki](saas-tenancy-wingtip-app-guidance-tips.md) dla czynności, aby pobrać i odblokować skrypty Wingtip biletów SaaS.
- Power BI Desktop jest zainstalowany. [Pobierz program Power BI Desktop](https://powerbi.microsoft.com/downloads/)
- Zainicjowano partii dodatkowi dzierżawcy, zobacz [ **samouczek dzierżaw udostępniania**](saas-dbpertenant-provision-and-catalog.md).
- Zadania konta i zadania konta w bazie danych zostały utworzone. Zobacz odpowiednie kroki w [ **Samouczek zarządzania schematu**](saas-tenancy-schema-management.md#create-a-job-agent-database-and-new-job-agent).

### <a name="create-data-for-the-demo"></a>Utwórz dane dla pokaz

W tym samouczku analizy odbywa się na danych sprzedaży biletów. W bieżącym kroku dane biletu generuje dla wszystkich dzierżawców.  Później tych danych jest wyodrębniany do analizy. *Upewnij się, po uprzednim udostępnieniu partii dzierżaw jak opisano wcześniej, tak aby było możliwe znaczące ilości danych*. Wystarczająco dużą ilość danych mogą uwidaczniać szereg różnych biletu struktury zakupów.

1. Otwórz w programie PowerShell ISE *...\Learning Analytics\Tenant Modules\Operational Analytics\Demo-TenantAnalytics.ps1*i ustawić następującą wartość:
    - **$DemoScenario** = **1** zakupu biletów zdarzeń na wszystkich miejsc
2. Naciśnij klawisz **F5** uruchomić skrypt i utworzyć bilet zakupu historii dla każdego zdarzenia w każdym miejscu.  Skrypt jest uruchamiany na kilka minut wygenerować dziesiątki tysięcy biletów.

### <a name="deploy-the-analytics-store"></a>Wdrażanie magazynu analityka
Często są liczne transakcyjne baz danych zawierających dane dzierżawy. Do analityka jednego magazynu, należy agregacji danych dzierżawy z wielu baz danych transakcyjnych. Agregacja umożliwia wydajne zapytań danych. W tym samouczku bazy danych SQL Azure bazy danych służy do przechowywania zagregowane dane.

W poniższych krokach Wdrażanie magazynu analytics, która jest wywoływana **tenantanalytics**. Wstępnie zdefiniowane tabel, które są wypełniane później w samouczku również wdrażania:
1. Otwórz w programie PowerShell ISE *...\Learning Analytics\Tenant Modules\Operational Analytics\Demo-TenantAnalytics.ps1* 
2. Ustaw zmienną $DemoScenario w skrypcie odpowiadające wybór magazynu analytics:
    - Aby użyć usługi SQL database bez magazynu kolumn, ustaw **$DemoScenario** = **2**
    - Aby używać bazy danych SQL z magazynu kolumn, ustaw **$DemoScenario** = **3**  
3. Naciśnij klawisz **F5** do uruchamiania skryptu pokaz (, który odwołuje się *TenantAnalytics Wdróż<XX>ps1* skryptu) co powoduje magazynu analytics dzierżawy. 

Po wdrożeniu aplikacji i wypełnione interesujące dane dzierżawy, za pomocą [programu SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) nawiązać **tenants1-dpt -&lt;użytkownika&gt;**  i **katalogu-dpt -&lt;użytkownika&gt;**  serwerów przy użyciu identyfikatora logowania = *developer*, hasło = *P@ssword1*. Zobacz [Samouczek wprowadzający](saas-dbpertenant-wingtip-app-overview.md) Aby uzyskać więcej pomocy.

![architectureOverView](media/saas-tenancy-tenant-analytics/ssmsSignIn.png)

W Eksploratorze obiektów wykonaj następujące czynności:

1. Rozwiń węzeł *tenants1-dpt -&lt;użytkownika&gt;*  serwera.
2. Rozwiń węzeł bazy danych i zapoznać się z listą dzierżawy baz danych.
3. Rozwiń węzeł *katalogu-dpt -&lt;użytkownika&gt;*  serwera.
4. Upewnij się, zostanie wyświetlony magazynu analytics i jobaccount bazy danych.

Zobacz następujące elementy bazy danych w Eksploratorze obiektów SSMS, rozwijając węzeł magazynu analytics:

- Tabele **TicketsRawData** i **EventsRawData** przechowywania danych pierwotnych wyodrębnione z baz danych dzierżawy.
- Tabele schematu gwiazdy są **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events**, i **dim_Dates** .
- Procedura składowana służy do wypełniania tabel schematu gwiazdy z tabel danych pierwotnych.

![architectureOverView](media/saas-tenancy-tenant-analytics/tenantAnalytics.png)

## <a name="data-extraction"></a>Wyodrębnianie danych 

### <a name="create-target-groups"></a>Tworzenie grup docelowych 

Przed kontynuowaniem upewnij się, że wdrożono zadania konta i jobaccount bazy danych. W zestawie dalej kroki zadania elastyczne służy do wyodrębniania danych z każdej dzierżawy bazy danych i do przechowywania danych w magazynie analytics. Następnie zadania drugi shreds danych i zapisuje go w tabelach w schemacie gwiazdkę. Te dwa zadania wykonywane w stosunku do dwóch różnych grup docelowych, a mianowicie **TenantGroup** i **AnalyticsGroup**. Zadania wyodrębniania jest uruchamiany przed TenantGroup, który zawiera wszystkie bazy danych dzierżawy. Strzępienia zadanie jest uruchamiane przed AnalyticsGroup, zawierającą tylko magazynu analytics. Tworzenie grup docelowych przy użyciu następujących kroków:

1. W programie SSMS, nawiąż połączenie z **jobaccount** bazy danych w katalogu-dpt -&lt;użytkownika&gt;.
2. Otwórz w programie SSMS, *...\Learning Modules\Operational Analytics\Tenant Analytics\ TargetGroups.sql* 
3. Modyfikowanie @User zmiennej na początku skryptu, zastępując <User> o wartości użytkownika używane w przypadku wdrażania aplikacji Wingtip SaaS.
4. Naciśnij klawisz **F5** uruchomić skrypt, który tworzy dwa docelowych grup.

### <a name="extract-raw-data-from-all-tenants"></a>Wyodrębnienie danych pierwotnych z wszystkich dzierżawców

Modyfikacje danych mogą być częściej *biletu i klienta* danych niż dla *zdarzeń i właściwość* danych. W związku z tym należy wziąć pod uwagę wyodrębniające dane biletu i klienta, oddzielnie i częściej niż wyodrębnić dane zdarzeń i miejsce. W tej sekcji służy do definiowania i planowanie dwóch oddzielnych zadań:

- Wyodrębnij dane biletu i klienta.
- Wyodrębnij dane zdarzeń i miejsce.

Każde zadanie wyodrębnia dane i zapisuje go w magazynie usługi analytics. Brak osobne zadanie shreds wyodrębnione dane w schemacie gwiazdkę analytics.

1. W programie SSMS, nawiąż połączenie z **jobaccount** bazy danych w katalogu-dpt -&lt;użytkownika&gt; serwera.
2. Otwórz w programie SSMS, *...\Learning Modules\Operational Analytics\Tenant Analytics\ExtractTickets.sql*.
3. Modyfikowanie @User w górnej części skryptu i Zamień <User> przy użyciu nazwy użytkownika używane w przypadku wdrażania aplikacji Wingtip SaaS 
4. Naciśnij klawisz F5, aby uruchomić skrypt, który tworzy i uruchamia zadanie, który wyodrębnia dane biletów i klientów z każdej dzierżawy bazy danych. Zadanie zapisuje dane do magazynu analytics.
5. Wyślij zapytanie do tabeli TicketsRawData w bazie danych tenantanalytics zapewnienie tabeli jest wypełniana biletów informacji z wszystkich dzierżawców.

![ticketExtracts](media/saas-tenancy-tenant-analytics/ticketExtracts.png)

Powtórz te czynności, z wyjątkiem Zastąp ten czas **\ExtractTickets.sql** z **\ExtractVenuesEvents.sql** w kroku 2.

Pomyślnie uruchomić zadanie wypełnia tabelę EventsRawData w magazynie analytics z informacjami o miejsc z wszystkich dzierżawców i nowych zdarzeń. 

## <a name="data-reorganization"></a>Reorganizacji danych

### <a name="shred-extracted-data-to-populate-star-schema-tables"></a>"Podrasowane" wyodrębnione dane, aby wypełnić tabel schematu gwiazdy

Następnym krokiem jest "w zestawie tabel, które są zoptymalizowane pod kątem zapytania analityczne Podrasowane" wyodrębnione dane. Schemat gwiazdy jest używany. Tabela faktów centralna zawiera bilet rekordy sprzedaży. Inne tabele są wypełniane przy użyciu powiązanych danych dotyczących miejsc, zdarzeń i klientami. I jest tabela wymiaru czasu. 

W tej części samouczka Zdefiniuj i uruchom zadanie, które scala wyodrębnionego nieprzetworzone dane z danymi w tabelach schematu gwiazdy. Po zakończeniu zadania scalania nieprzetworzone dane są usuwane, pozostawiając tabel gotowy do wypełnienia następnego danych dzierżawy wyodrębnić zadania.

1. W programie SSMS, nawiąż połączenie z **jobaccount** bazy danych w katalogu-dpt -&lt;użytkownika&gt;.
2. Otwórz w programie SSMS, *...\Learning Modules\Operational Analytics\Tenant Analytics\ShredRawExtractedData.sql*.
3. Naciśnij klawisz **F5** do uruchomienia skryptu, aby zdefiniować zadania, który wywołuje sp_ShredRawExtractedData procedury składowanej w magazynie usługi analytics.
4. Zezwalaj na wystarczająco dużo czasu na pomyślnym uruchomieniu zadania.
    - Sprawdź **cyklu życia** kolumnie tabeli jobs.jobs_execution stanu zadania. Upewnij się, że zadanie **zakończyło się pomyślnie** przed kontynuowaniem. Pomyślnego uruchomienia wyświetla dane podobnie jak poniżej:

![rozdrabniania](media/saas-tenancy-tenant-analytics/shreddingJob.PNG)

## <a name="data-exploration"></a>Eksploracja danych

### <a name="visualize-tenant-data"></a>Wizualizuj dane dzierżawy

Dane w tabeli Schemat gwiazdy zawiera wszystkie biletu sprzedaży dane potrzebne do analizy. Aby ułatwić trendy w dużych zestawów danych, należy go graficznie wizualizacji.  W tej sekcji zostanie przedstawiony sposób użycia **usługi Power BI** do manipulowania i wizualizacji danych dzierżawy zostały wyodrębnione i organizowane.

Do nawiązania połączenia usługi Power BI i zaimportować widoków, które wcześniej utworzony, wykonaj następujące kroki:

1. Uruchom program Power BI desktop.
2. Na Wstążce głównej wybierz **Pobierz dane**i wybierz **więcej...** z menu.
3. W **Pobierz dane** okna, wybierz bazę danych SQL Azure.
4. W oknie nazwy logowania bazy danych, wprowadź nazwę serwera (katalogu-dpt -&lt;użytkownika&gt;. database.windows.net). Wybierz **importu** dla **tryb łączności danych**, a następnie kliknij przycisk OK. 

    ![signinpowerbi](./media/saas-tenancy-tenant-analytics/powerBISignIn.PNG)

5. Wybierz **bazy danych** w okienku po lewej stronie, a następnie wprowadź nazwę użytkownika = *developer*, a następnie wprowadź hasło = *P@ssword1*. Kliknij przycisk **Połącz**.  

    ![databasesignin](./media/saas-tenancy-tenant-analytics/databaseSignIn.PNG)

6. W **Nawigator** okienku w obszarze bazy danych analizy, wybierz tabele schematu gwiazdy: fact_Tickets, dim_Events, dim_Venues, dim_Customers i dim_Dates. Następnie wybierz **obciążenia**. 

Gratulacje! Dane zostały pomyślnie załadowane do usługi Power BI. Teraz możesz rozpocząć eksplorowanie interesujące wizualizacje, aby uzyskać wgląd w dzierżawcom. Następnie przeprowadź przez jak analytics można włączyć próba udostępnienia zaleceń opartych na danych do zespołu firm Wingtip biletów. Zalecenia ułatwiają Optymalizuj środowisko firm modelu i klienta.

Należy rozpocząć od analizowanie danych sprzedaży biletów, aby sprawdzić zmiany użycia między miejsc. Wybierz następujące opcje w usłudze Power BI do wykreślenia wykresu słupkowego całkowitej liczby sprzedanych w każdym miejscu biletów. Z powodu zmienności losowej w generatorze biletu wyniki mogą być inne.
 
![TotalTicketsByVenues](./media/saas-tenancy-tenant-analytics/TotalTicketsByVenues.PNG)

Poprzedni kreślenia potwierdza, że liczba sprzedanych w każdym miejscu biletów różni się. Miejsc, w których sprzedawać bilety więcej bardziej intensywnie miejsc, w których sprzedawać bilety mniej korzystają z usługą. Może istnieć możliwość tutaj dostosować alokacji zasobów zgodnie z potrzebami innej dzierżawy.

Dodatkowo można analizować danych, aby sprawdzić, jak sprzedaży biletów różnią się w czasie. Wybierz następujące opcje w usłudze Power BI do wykreślenia łączna liczba biletów sprzedanych każdego dnia przez 60 dni.
 
![SaleVersusDate](./media/saas-tenancy-tenant-analytics/SaleVersusDate.PNG)

Wykres poprzedniego Wyświetla tej kolekcji sprzedaży biletów dla niektórych miejsc. Te wartości szczytowe wzmocnienie pomysł, że niektóre miejsc może korzystać z zasobów systemowych nieproporcjonalnie. Do tej pory Brak nie wzorzec widocznych w przypadku nagłego wystąpienia.

Obok chcesz dokładniej badać znaczenie te dni sprzedaży szczytu. Gdy te wartości są wykonywane po Przejdź sprzedaży biletów? Do wykreślenia biletów sprzedawany na dzień, należy wybrać następujące opcje w usłudze Power BI.

![SaleDayDistribution](./media/saas-tenancy-tenant-analytics/SaleDistributionPerDay.PNG)

Poprzedni kreślenia pokazuje, że niektóre miejsc sprzedaży partii biletów pierwszego dnia sprzedaży. Jak przejść biletów sprzedaży w tych miejsc, wydaje się mad łazienkowych. W przypadku pozostałych dzierżaw usługi może mieć wpływ na tej serii działania przez kilka miejsc.

Aby przejść do szczegółów w danych ponownie, aby sprawdzać, czy ten mad łazienkowych obowiązuje dla wszystkich zdarzeń obsługiwanych przez te miejsc. W poprzednich powierzchni obserwowanych że Hall porozumieniu Contoso sprzedaje partii biletów i że Contoso również ma kolekcji w sprzedaży biletów w określone dni. Odtwórz z opcjami usługi Power BI do wykreślenia biletu zbiorcza sprzedaży Hall porozumieniu firmy Contoso, koncentrujących się na trendy sprzedaży dla każdego z jego zdarzeń. Wszystkie zdarzenia według tego samego wzorca sprzedaży?

![ContosoSales](media/saas-tenancy-tenant-analytics/EventSaleTrends.PNG)

Poprzedni kreślenia dla Contoso porozumieniu Hall pokazuje, że mad łazienkowych nie odbywa się dla wszystkich zdarzeń. Odtwórz opcji filtru, aby wyświetlić trendy sprzedaży dla innych miejsc.

Wgląd w biletu sprzedaży wzorce może prowadzić Wingtip biletów, aby zoptymalizować swojego modelu biznesowego. Możliwe, że zamiast ładowana wszystkich dzierżaw jednakowo, Wingtip wprowadzić warstwy usług z różne poziomy wydajności. Większe miejsc, wymagających sprzedawać bilety więcej dziennie można oferowane wyższego poziomu z wyższej Umowa dotycząca poziomu usług (SLA). Te miejsc może mieć swoje bazy danych, umieszczone w puli z wyższe limity zasobów na bazę danych. Każdej warstwy usług może mieć przydział sprzedaży co godzinę, o dodatkowe opłaty za przekroczenie przydziału. Większe miejsc, w których okresowe seria sprzedaży będzie korzystać z wyższej warstwy, a biletów Wingtip można efektywniej sprzedawać usługi.

W tym samym czasie niektórzy klienci biletów Wingtip reklamację, one mieć trudności z sprzedawać bilety za mało Justify kosztu usługi. Możliwe, że informacje na temat tych technologii ma możliwość zwiększenie sprzedaży biletów dla gorszych wynikach w przypadku miejsc. Wyższy sprzedaży może zwiększyć wartość postrzegana usługi. Fact_Tickets kliknij prawym przyciskiem myszy i wybierz **nowej miary**. Wprowadź poniższe wyrażenie dla nowej miary o nazwie **AverageTicketsSold**:

```
AverageTicketsSold = DIVIDE(DIVIDE(COUNTROWS(fact_Tickets),DISTINCT(dim_Venues[VenueCapacity]))*100, COUNTROWS(dim_Events))
```

Wybierz następujące opcje wizualizacji do wykreślenia sprzedanych przez każdego miejsce, aby określić ich względne Powodzenie biletów procent.

![AvgTicketsByVenues](media/saas-tenancy-tenant-analytics/AvgTicketsByVenues.PNG)

Poprzedni kreślenia pokazuje, że mimo że większość miejsc sprzedawać więcej niż 80% bilet, są niektóre klienci do wypełnienia więcej niż połowy miejsc. Odtwarzać wartości również wybrać procent maksymalnej lub minimalnej biletów sprzedaży dla każdego miejsca.

Wcześniej należy pogłębione analizę, aby sprawdzić, czy sprzedaży biletów często wykonują wzorce przewidywalne. W wyniku tego odnajdywania może pozwolić Wingtip biletów pomocy gorszych wynikach w przypadku miejsc zwiększenie sprzedaży biletów zalecając ceny dynamicznych. Ta odnajdowania można ujawnić możliwość posługiwanie się machine learning metodami do prognozowania sprzedaży biletów dla każdego zdarzenia. Operacje przewidywania dla można również wpływu na przychodu oferty rabaty na sprzedaży biletów. Power BI Embedded może być zintegrowane zdarzeń aplikacji do zarządzania. Integracji może pomóc w wizualizacji prognozowane wartości sprzedaży oraz efekt różnych rabatów. Aplikacji może pomóc opracować optymalne rabatu mają być stosowane bezpośrednio z ekranu analytics.

Zostały spełnione trendów w danych dzierżawy z aplikacji WingTip. Należy rozważyć inne sposoby aplikacji mogą ułatwić podjęcie decyzji biznesowych dla dostawców aplikacji SaaS. Dostawców można lepiej spełnić na potrzeby swoich dzierżaw. Miejmy nadzieję, że w tym samouczku ma wyposażone możesz narzędzia niezbędne do wykonywania analizy danych dzierżawy dla firmom podejmowanie decyzji opartych na danych.

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> - Wdrożone w bazie danych analiz dzierżawy z tabelami wstępnie zdefiniowany schemat gwiazdy
> - Zadania elastyczne umożliwiają wyodrębniania danych z wszystkich baz danych dzierżawy
> - Scal wyodrębnione dane w tabelach w przeznaczone do analityka schematu gwiazdy
> - Zapytanie bazy danych analizy 
> - Umożliwia obserwować trendy w danych dzierżawy usługi Power BI do wizualizacji danych 

Gratulacje!

## <a name="additional-resources"></a>Zasoby dodatkowe

- Dodatkowe [samouczków, z którymi aplikacji Wingtip SaaS](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
- [Zadania elastyczne](sql-database-elastic-jobs-overview.md).
