---
title: Uruchamianie zapytań analitycznych dla baz danych Azure SQL | Dokumentacja firmy Microsoft
description: Zapytania analityczne międzydzierżawowa, przy użyciu danych wyodrębnionych z wielu baz danych Azure SQL Database aplikacji z wieloma dzierżawami.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: anjangsh,billgib,genemi
manager: craigg
ms.date: 09/19/2018
ms.openlocfilehash: 4bf97c0c447bfabc1454959d457bbd50f3490299
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66242798"
---
# <a name="cross-tenant-analytics-using-extracted-data---multi-tenant-app"></a>Analizować międzydzierżawowa, przy użyciu danych wyodrębnionych - aplikacji wielodostępnej
 
W tym samouczku opisano scenariusz pełną analizę dotyczący wdrażania wielodostępnej. Scenariusz pokazuje, jak włączyć analytics przedsiębiorstwom podejmować inteligentne decyzje. Przy użyciu danych pobranych z bazy danych podzielonej na fragmenty, umożliwia analizę wgląd w zachowanie dzierżawy, w tym korzystanie z przykładowej aplikacji SaaS o nazwie Wingtip Tickets. Ten scenariusz obejmuje trzy kroki: 

1.  **Wyodrębnianie danych** z każdej bazy danych dzierżawy do magazynu usługi analytics.
2.  **Optymalizowanie wyodrębnione dane** przetwarzania analizy.
3.  Użyj **analizy biznesowej** narzędzi do rysowania się przydatne informacje szczegółowe, które mogą ukierunkować podejmowanie decyzji. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> - Tworzenie dzierżawy, do której analytics przechowywać do wyodrębniania danych do.
> - Zadania elastyczne umożliwiają wyodrębnianie danych z każdej bazy danych dzierżawy w magazynie usługi analytics.
> - Optymalizuj wyodrębnione dane (z opcją reorganize w schemacie gwiazdy).
> - Twórz zapytania bazy danych analizy.
> - Użyj usługi Power BI do wizualizacji danych do wyróżnienia trendów w danych dzierżawy i zalecenia dotyczące ulepszenia.

![architectureOverView](media/saas-multitenantdb-tenant-analytics/architectureOverview.png)

## <a name="offline-tenant-analytics-pattern"></a>Wzorzec analizy dzierżawy w trybie offline

Aplikacje SaaS, które tworzysz mają dostęp do ogromnej ilości danych dzierżawy, przechowywane w chmurze. Dane zapewnia rozbudowane źródło szczegółowych informacji o operacjach i użyciu aplikacji, a także o zachowaniu dzierżawcy. Te informacje mogą ukierunkować rozwój funkcji, usprawnienia w zakresie użyteczności oraz inne inwestycje w aplikacje i platformę.

Dostęp do danych dla wszystkich dzierżaw jest proste, gdy wszystkie dane znajdują się w tylko jednej wielodostępnej bazie danych. Ale dostęp jest bardziej złożonych, gdy rozproszone na dużą skalę na tysiące baz danych. Jest jednym ze sposobów oswoić złożoność do wyodrębniania danych analitycznej bazy danych lub magazynu danych. Możesz następnie utworzyć zapytanie względem magazynu danych, aby zbierać szczegółowe informacje z danych biletów od wszystkich dzierżaw.

Ten samouczek przedstawia pełną analizę scenariusz dotyczący tej przykładowej aplikacji SaaS. Po pierwsze, elastyczne zadania są używane do zaplanowania wyodrębnianie danych z każdej bazy danych dzierżawy. Dane są przesyłane do magazynu usługi analytics. Magazyn usługi analytics można albo bazą danych SQL Database lub SQL Data Warehouse. Do wyodrębnienia danych na dużą skalę [usługi Azure Data Factory](../data-factory/introduction.md) jest commended.

Następnie dane zagregowane jest rozdrobniony na zbiór [schematu gwiazdy](https://www.wikipedia.org/wiki/Star_schema) tabel. Tabele składają się z tabelą faktów centralną, a także powiązane tabele wymiarów:

- Tabela faktów centralnej w schemacie gwiazdy zawiera dane biletu.
- Tabele wymiarów zawierają dane dotyczące miejsca, zdarzenia, klientów i Kup daty.

Razem Środkowej i wydajnego przetwarzania analitycznego wymiaru tabel Włącz. Używane w tym samouczku schematu gwiazdy jest wyświetlany na poniższej ilustracji:
 
![StarSchema](media/saas-multitenantdb-tenant-analytics/StarSchema.png)

Na koniec badane są tabele schematu gwiazdy. Wyniki zapytania są wyświetlane wizualnie aby wyróżnić szczegółowe informacje dotyczące zachowania dzierżawcy i ich użycie aplikacji. Ten schemat gwiazdy umożliwia uruchamianie zapytań, pomagających odnaleźć elementy, takie jak następujące:

- Kto jest zakupy biletów i z których miejsc.
- Ukryte wzorce i trendy w następujących obszarach:
    - Sprzedaż biletów.
    - Względna popularność każdego miejsca.

Zrozumienie, jak spójnie każdego dzierżawcy jest korzystanie z niej zapewnia możliwość tworzenia planów usług, które spełniają potrzeby do swoich potrzeb. Ten samouczek zawiera podstawowe przykłady szczegółowe informacje, które można zebrać dane dzierżawy.

## <a name="setup"></a>Konfiguracja

### <a name="prerequisites"></a>Wymagania wstępne

Do wykonania zadań opisanych w tym samouczku niezbędne jest spełnienie następujących wymagań wstępnych:

- Aplikacja o nazwie Wingtip Tickets SaaS wielodostępnej w bazie danych jest wdrażana. Aby wdrożyć w mniej niż pięć minut, zobacz [wdrażanie i eksplorowanie aplikacji Wingtip Tickets SaaS wielodostępnej w bazie danych](saas-multitenantdb-get-started-deploy.md)
- Skryptów aplikacji Wingtip SaaS i aplikacji [kod źródłowy](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB) są pobierane z usługi GitHub. Pamiętaj, aby *odblokować plik zip* weryfikowany przed wyodrębnianiem jego zawartość. Zapoznaj się z [ogólne wskazówki dotyczące](saas-tenancy-wingtip-app-guidance-tips.md) kroków pobrać i odblokować skrypty SaaS o nazwie Wingtip Tickets.
- Usługa Power BI Desktop jest zainstalowana. [Pobieranie programu Power BI Desktop](https://powerbi.microsoft.com/downloads/)
- Zainicjowano partii dodatkowych dzierżaw, zobacz [ **Aprowizowanie dzierżaw w ramach samouczka**](saas-multitenantdb-provision-and-catalog.md).
- Agent zadań i baza danych agenta zadań zostały utworzone. Zobacz odpowiednie kroki w [ **samouczka dotyczącego zarządzania schematu**](saas-multitenantdb-schema-management.md#create-a-job-agent-database-and-new-job-agent).

### <a name="create-data-for-the-demo"></a>Tworzenie danych demonstracyjnych

W tym samouczku jest wykonywana analiza danych sprzedaży biletów. Bieżący krok musisz wygenerować dane biletu dla wszystkich dzierżaw.  Później ten dane są wyodrębniane do analizy. *Upewnij się, dostarczonych partii dzierżaw opisany wcześniej, by były zrozumiałe ilość danych*. Wystarczająco dużą ilość danych może narazić szeroką gamę różnych wzorcami zakupów biletów.

1. W **PowerShell ISE**, otwórz *...\Learning Analytics\Tenant Modules\Operational Analytics\Demo-TenantAnalytics.ps1*i Ustaw następującą wartość:
    - **$DemoScenario** = **1** zakup biletów dla zdarzeń na wszystkie lokalizacje
2. Naciśnij klawisz **F5** Uruchom skrypt i utworzyć historię dla każdego zdarzenia w każdym miejscu zakupów biletów.  Skrypt jest uruchamiany przez kilka minut wygenerować dziesiątki tysięcy biletów.

### <a name="deploy-the-analytics-store"></a>Wdrażanie magazynu analytics
Często są liczne transakcyjnych podzielonej na fragmenty baz danych zawierających wszystkie dane dzierżawy. Z bazy danych podzielonej na fragmenty należy agregacji danych dzierżawy, do magazynu jednego analizy. Agregacja umożliwia wydajne zapytania na danych. W tym samouczku bazą danych Azure SQL Database służy do przechowywania zagregowanych danych.

W poniższych krokach wdrożyć magazyn analizy, który jest nazywany **tenantanalytics**. Można także wdrożyć wstępnie zdefiniowanych tabel, które są wypełnione w dalszej części tego samouczka:
1. Otwórz w środowisku PowerShell ISE *...\Learning Analytics\Tenant Modules\Operational Analytics\Demo-TenantAnalytics.ps1* 
2. Ustaw wartość zmiennej $DemoScenario w skrypcie, aby dopasować się do wyboru analizy magazynu. Dla celów szkoleniowych, zaleca się bazy danych SQL database bez magazynu kolumn.
    - Aby użyć usługi SQL database bez magazynu kolumn, należy ustawić **$DemoScenario** = **2**
    - Usługa SQL database za pomocą magazynu kolumn, ustawianie **$DemoScenario** = **3**  
3. Naciśnij klawisz **F5** Aby uruchomić skrypt pokazowy (wywołująca *TenantAnalytics Wdróż\<XX > .ps1* skryptu) tworzy magazyn analizy dzierżawy. 

Skoro masz wdrożono aplikację i wypełnione interesujących danych dzierżawy, użyj [programu SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) połączyć **tenants1-mt -\<użytkownika\>**  i **katalogu-mt -\<użytkownika\>**  serwerów przy użyciu identyfikatora logowania = *developer*, hasło = *P\@ssword1*.

![architectureOverView](media/saas-multitenantdb-tenant-analytics/ssmsSignIn.png)

W Eksploratorze obiektów wykonaj następujące czynności:

1. Rozwiń *tenants1-mt -\<użytkownika\>*  serwera.
2. Rozwiń węzeł bazy danych, a następnie zobacz *tenants1* bazy danych zawierającej wiele dzierżaw.
3. Rozwiń *catalog-mt -\<użytkownika\>*  serwera.
4. Sprawdź, czy widzisz analizy magazyn i bazę danych jobaccount.

Zobacz poniższe elementy bazy danych w Eksploratorze obiektów programu SSMS, rozwijając węzeł magazynu analytics:

- Tabele **TicketsRawData** i **EventsRawData** przechowywania nieprzetworzone dane wyodrębnione z baz danych dzierżaw.
- Tabele w schemacie gwiazdy są **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events**, i **dim_Dates** .
- **Sp_ShredRawExtractedData** procedura składowana służy do Wypełnianie tabel schematu gwiazdy z tabel danych pierwotnych.

![tenantAnalytics](media/saas-multitenantdb-tenant-analytics/tenantAnalytics.png)

## <a name="data-extraction"></a>Wyodrębnianie danych 

### <a name="create-target-groups"></a>Tworzenie grup docelowych 

Przed kontynuowaniem upewnij się, że wdrożono bazy danych konta i jobaccount zadania. W następnego zestawu kroków zadań elastycznych jest używany do wyodrębniania danych z bazy danych podzielonej na fragmenty dzierżaw i przechowywanie danych w magazynie usługi analytics. Następnie drugie zadanie shreds danych i zapisuje go do tabel w schemacie gwiazdy. Te dwa zadania uruchamiane dwóch różnych grup docelowych, to znaczy **TenantGroup** i **AnalyticsGroup**. Uruchamia zadanie wyodrębniania względem TenantGroup, która zawiera wszystkich baz danych dzierżaw. Strzępienia zadanie jest uruchamiane przed AnalyticsGroup, zawierającą tylko magazyn analizy. Tworzenie grup docelowych wykonując następujące kroki:

1. W programie SSMS, połącz się z **jobaccount** bazy danych w wykazie-mt -\<użytkownika\>.
2. W programie SSMS Otwórz *...\Learning Modules\Operational Analytics\Tenant Analytics\ TargetGroups.sql* 
3. Modyfikowanie @User zmiennej u góry skryptu, zastępując `<User>` wartością użytkownika podanej podczas wdrażania aplikacji Wingtip Tickets SaaS wielodostępnej w bazie danych.
4. Naciśnij klawisz **F5** do uruchomienia skryptu, który tworzy dwa docelowe grupy.

### <a name="extract-raw-data-from-all-tenants"></a>Wyodrębnij nieprzetworzone dane ze wszystkich dzierżaw

Transakcje mogą występować częściej, co jest niezbędne dla *biletu i klienta* danych niż *zdarzeń i właściwości* danych. W związku z tym należy wziąć pod uwagę wyodrębnianie biletów i danych klientów, oddzielnie i częściej niż wyodrębnić dane zdarzeń i właściwości. W tej sekcji służy do definiowania i planowanie dwóch oddzielnych zadań:

- Wyodrębnij dane biletu i klientów.
- Wyodrębnij dane zdarzeń i właściwości.

Każde zadanie wyodrębnia dane i publikuje go do magazynu usługi analytics. Istnieją osobne zadanie shreds wyodrębnione dane w schemacie gwiazdy analizy.

1. W programie SSMS, połącz się z **jobaccount** bazy danych w wykazie-mt -\<użytkownika\> serwera.
2. W programie SSMS Otwórz *...\Learning Modules\Operational Analytics\Tenant Analytics\ExtractTickets.sql*.
3. Modyfikowanie @User u góry skryptu i Zastąp `<User>` przy użyciu nazwy użytkownika podanej podczas wdrażania aplikacji Wingtip Tickets SaaS wielodostępnej w bazie danych. 
4. Naciśnij klawisz **F5** do uruchomienia skryptu, który tworzy i uruchamia zadanie, który wyodrębnia dane biletów i klientów z każdej bazy danych dzierżawy. Zadanie zapisuje dane do magazynu usługi analytics.
5. Zapytania w tabeli TicketsRawData w bazie danych tenantanalytics, aby upewnić się, że tabela zostanie wypełniona informacji biletów ze wszystkich dzierżaw.

![ticketExtracts](media/saas-multitenantdb-tenant-analytics/ticketExtracts.png)

Powtórz te czynności, z wyjątkiem Zastąp ten czas **\ExtractTickets.sql** z **\ExtractVenuesEvents.sql** w kroku 2.

Pomyślnie uruchomiono zadanie zadanie wypełnia tabelę EventsRawData w sklepie analytics przy użyciu nowych zdarzeń i informacji lokalizacje, ze wszystkich dzierżaw. 

## <a name="data-reorganization"></a>Reorganizacja danych

### <a name="shred-extracted-data-to-populate-star-schema-tables"></a>Typu wyodrębnione dane, aby wypełnić tabele schematu gwiazdy

Następnym krokiem jest typu wyodrębnione dane pierwotne w zestaw tabel, które są zoptymalizowane pod kątem zapytań analitycznych. W schemacie gwiazdy jest używany. Tabela faktów centralna zawiera bilet rekordy sprzedaży. Tabele wymiarów są wypełniane przy użyciu danych dotyczących miejsc, zdarzenia, klientów i Kup daty. 

W tej części samouczka zdefiniować i uruchomić zadanie, które scala wyodrębnione dane nieprzetworzone dane w tabelach schematu gwiazdy. Po zakończeniu zadania scalania nieprzetworzone dane są usuwane, pozostawiając w tabelach, które są gotowe do wypełnienia następnego danych dzierżawy wyodrębnić zadania.

1. W programie SSMS, połącz się z **jobaccount** bazy danych w wykazie-mt -\<użytkownika\>.
2. W programie SSMS Otwórz *...\Learning Modules\Operational Analytics\Tenant Analytics\ShredRawExtractedData.sql*.
3. Naciśnij klawisz **F5** do uruchomienia skryptu, aby zdefiniować zadania, który wywołuje sp_ShredRawExtractedData procedurę składowaną w magazynie usługi analytics.
4. Poczekać na zadanie zostało uruchomione pomyślnie.
    - Sprawdź **cyklu życia** kolumny tabeli jobs.jobs_execution stanu zadania. Upewnij się, że zadanie **Powodzenie** przed kontynuowaniem. Pomyślny przebieg wyświetla dane, podobnie do poniższej tabeli:

![shreddingJob](media/saas-multitenantdb-tenant-analytics/shreddingJob.PNG)

## <a name="data-exploration"></a>Eksploracja danych

### <a name="visualize-tenant-data"></a>Wizualizowanie danych dzierżawy

Dane w tabeli schematu gwiazdy zawiera wszystkie sprzedaży dane biletu potrzebami analizy. Aby ułatwić trendy w dużych zestawach danych, musisz zwizualizować go w formie graficznej.  W tej sekcji dowiesz się, jak używać **usługi Power BI** do manipulowania i wizualizuj dane dzierżawy zostały wyodrębnione i zorganizowane.

Aby połączyć usługę Power BI i zaimportować widoków, która została utworzona wcześniej, wykonaj następujące czynności:

1. Uruchom usługi Power BI desktop.
2. Na karcie wstążki Narzędzia główne, wybierz **Pobierz dane**i wybierz **więcej...** w menu.
3. W **Pobierz dane** okna, wybierz bazę danych SQL Azure.
4. W oknie logowania bazy danych, wprowadź nazwę serwera (catalog-mt -\<użytkownika\>. database.windows.net). Wybierz **importu** dla **tryb łączności danych**, a następnie kliknij przycisk OK. 

    ![powerBISignIn](media/saas-multitenantdb-tenant-analytics/powerBISignIn.PNG)

5. Wybierz **bazy danych** w okienku po lewej stronie, a następnie wprowadź nazwę użytkownika = *developer*, a następnie wprowadź hasło = *P\@ssword1*. Kliknij przycisk **Połącz**.  

    ![DatabaseSignIn](media/saas-multitenantdb-tenant-analytics/databaseSignIn.PNG)

6. W **Nawigator** okienku w obszarze analitycznej bazy danych, wybierz tabele schematu gwiazdy: fact_Tickets, dim_Events, dim_Venues, dim_Customers i dim_Dates. Następnie wybierz pozycję **obciążenia**. 

Gratulacje! Pomyślnie załadowano dane do usługi Power BI. Teraz możesz rozpocząć eksplorację interesujące wizualizacje w celu uzyskania szczegółowych informacji dzierżawcy. Dalej opisano jak analizy umożliwiają zalecenia oparte na danych do zespołu firmy Wingtip Tickets. Zalecenia mogą pomóc poprawić komfort modelu i klientów biznesowych.

Należy rozpocząć od analizowanie danych sprzedaży biletów, aby zobaczyć zmienność użycie różnych miejsc. Wybierz następujące opcje w usłudze Power BI do wykreślenia wykresu słupkowego z łączną liczbą biletów sprzedawane przez każdego miejsca. Z powodu zmienności losowej w generatorze biletu wyniki mogą się różnić.
 
![TotalTicketsByVenues](media/saas-multitenantdb-tenant-analytics/TotalTicketsByVenues.PNG)

Poprzedni diagram potwierdza, że różni się liczbą biletów sprzedawane przez każde miejsce. Lokalizacje, które sprzedawać bilety więcej korzystają z usługi bardziej intensywnie miejsc, które sprzedawać bilety mniejszej liczby. Może to być szansy sprzedaży w tym miejscu można dostosować alokacji zasobów zgodnie z potrzebami innej dzierżawy.

Może dalej analizować dane, aby zobaczyć, jak sprzedaż biletów różnią się wraz z upływem czasu. Wybierz następujące opcje w usłudze Power BI do wykreślenia całkowita liczba sprzedanych każdego dnia przez 60 dni biletów.
 
![SaleVersusDate](media/saas-multitenantdb-tenant-analytics/SaleVersusDate.PNG)

Poprzedni wykres przedstawia tego niektóre lokalizacje nagły sprzedaży biletów. Te wartości graniczne wzmocnienie pomysł, że niektóre miejsca może zużywałoby zasobów systemowych nieproporcjonalnie. Do tej pory w nie ma żadnych oczywistych wzorzec po wystąpieniu skokom.

Następnie chcesz dokładniej badać znaczenia te dni sprzedaży szczytu. Gdy te wartości szczytowe występować po biletów, przejdź do sprzedaży? Do wykreślenia sprzedanych dziennie biletów, należy wybrać następujące opcje w usłudze Power BI.

![SaleDayDistribution](media/saas-multitenantdb-tenant-analytics/SaleDistributionPerDay.PNG)

Poprzedni Diagram pokazuje, że niektóre lokalizacje przekonywać partii biletów pierwszego dnia sprzedaży. Jak przejść bilety na sprzedaż w tych miejsc, prawdopodobnie mad łazienkowych. Tej serii działania przez kilka venues mogą mieć wpływ na usługi w przypadku innych dzierżaw.

Możesz przejść do szczegółów danych ponownie, aby sprawdzić, czy ten mad łazienkowych jest wartość true dla wszystkich zdarzeń obsługiwanych przez te lokalizacje. W poprzednim powierzchni obserwuje że Contoso Concert Hall sprzedaje partii biletów i że Contoso również ma największy wzrost sprzedaży biletów w określonych dniach. Poeksperymentuj z opcji usługi Power BI do wykreślenia sprzedaż biletów zbiorcza dla firmy Contoso Concert Hall, skupiając się na trendy sprzedaży dla każdego z jego zdarzeń. Wszystkie zdarzenia wykonuj tego samego wzorca sprzedaż?

![ContosoSales](media/saas-multitenantdb-tenant-analytics/EventSaleTrends.PNG)

Poprzedni diagram dla firmy Contoso Concert Hall przedstawia łazienkowych mad — nie jest realizowane dla wszystkich zdarzeń. Poeksperymentuj z opcje filtru, aby zobaczyć trendy sprzedaży dla innych miejsc.

Wgląd w wzorców sprzedaży biletów może prowadzić Wtp w celu zoptymalizowania swojego modelu biznesowego. Być może zamiast ładowana wszystkich dzierżaw w równym stopniu, o nazwie Wingtip powinno wprowadzić warstwy usługi o różnych rozmiarów wystąpień obliczeniowych. Większe miejsc, wymagających do sprzedaży biletów więcej dziennie mogłoby być oferowane wyższego poziomu z wyższym Umowa dotycząca poziomu usług (SLA). Te miejsca może mieć swoje bazy danych, umieszczane w puli z wyższymi limitami zasobów bazy danych. Każda warstwa usługi może mieć godzinowe alokacji sprzedaży, za pomocą dodatkowe opłaty za przekroczenia przydziału. Venues większe, mających okresowe wzrosty sprzedaży będą korzystać z wyższej warstwy, a Wingtip Tickets można efektywniej Zarabiaj usługi.

W międzyczasie niektórzy klienci Wingtip Tickets reklamację, mogą mieć trudności z sprzedawać za mało bilety ponoszenie kosztów usługi. Być może w wglądów ma możliwość sprzedaż biletów dla w ramach wykonywania miejsc. Wyższe sprzedaży wydłuży postrzegany wartość usługi. Kliknij prawym przyciskiem myszy fact_Tickets, a następnie wybierz pozycję **nową miarę**. Wprowadź następujące wyrażenie dla nowej miary o nazwie **AverageTicketsSold**:

```
AverageTicketsSold = DIVIDE(DIVIDE(COUNTROWS(fact_Tickets),DISTINCT(dim_Venues[VenueCapacity]))*100, COUNTROWS(dim_Events))
```

Wybierz następujące opcje wizualizacji, do wykreślenia bilety procent sprzedawane przez każdego miejsca, aby określić ich względną Powodzenie.

![analyticsViews](media/saas-multitenantdb-tenant-analytics/AvgTicketsByVenues.PNG)

Poprzedni diagram przedstawia, mimo że większość venues sprzedawać ponad 80% biletami, niektóre ciężko jest wypełnienie ponad połowę stanowiska. Poeksperymentuj z wartości również wybrać procent maksymalnej lub minimalnej biletów sprzedaży dla każdego miejsca.

Wcześniej pogłębione się analizę, aby sprawdzić, czy sprzedaż biletów zwykle postępuj zgodnie z przewidywalnymi wzorcami. W wyniku tego odnajdywania może pozwolić pomocy Wingtip Tickets gorszych wynikach w przypadku sprzedaży biletów boost venues przez polecanie ceny dynamicznych. Ta funkcja odnajdywania może spowodować ujawnienie szansy sprzedaży mogą wykorzystać techniki uczenia maszyny do prognozowania sprzedaży biletów dla każdego zdarzenia. Prognozy może również dla negatywny wpływ na przychody oferty rabaty na sprzedaż biletów. Power BI Embedded może być zintegrowane zdarzeń aplikacji do zarządzania. Integracja może pomóc w wizualizacji przewidywana sprzedaż i efekt różnych rabatów. Aplikacja może pomóc, należy opracować optymalne rabat w wysokości do zastosowania bezpośrednio z ekranu analizy.

Zaobserwowano trendy w danych dzierżawy z poziomu aplikacji Wingtip Tickets SaaS wielodostępnej w bazie danych. Można rozważyć inne sposoby aplikacji mogą ułatwić podjęcie decyzji biznesowych, dla dostawców aplikacji SaaS. Dostawców może lepiej adresowanych do potrzeb dzierżawcom. Miejmy nadzieję w tym samouczku został wyposażony w możesz narzędzi niezbędnych do przeprowadzenia analizy danych dzierżawy umożliwiające przedsiębiorstwom podejmować decyzje oparte na danych.

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> - Wdrożone analitycznej bazy danych dzierżaw z tabelami wstępnie zdefiniowany schemat gwiazdy
> - Zadania elastyczne umożliwiają wyodrębnianie danych z wszystkich baz danych dzierżawy
> - Scalanie wyodrębnionych danych do tabel w schemacie gwiazdy zaprojektowane na potrzeby analizy
> - Zapytanie analitycznej bazy danych 
> - Usługa Power BI do wizualizacji danych, aby obserwować trendy w danych dzierżawy 

Gratulacje!

## <a name="additional-resources"></a>Dodatkowe zasoby

Dodatkowe [samouczki nawiązujące do aplikacji SaaS o nazwie Wingtip](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials). 
- [Zadania elastyczne](elastic-jobs-overview.md).
- [Cross-tenant analytics przy użyciu wyodrębnione dane — aplikacja jednej dzierżawy](saas-tenancy-tenant-analytics.md) 