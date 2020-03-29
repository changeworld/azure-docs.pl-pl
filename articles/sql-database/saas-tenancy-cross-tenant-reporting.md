---
title: Raportowanie zapytań w wielu bazach danych
description: Raportowanie między dzierżawcami przy użyciu zapytań rozproszonych.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewers: billgib,ayolubek
ms.date: 01/25/2019
ms.openlocfilehash: c863946934df9990c14e49ef1a0a82bbc55b27c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822079"
---
# <a name="cross-tenant-reporting-using-distributed-queries"></a>Raportowanie między dzierżawcami przy użyciu zapytań rozproszonych

W tym samouczku uruchamiasz zapytania rozproszone w całym zestawie baz danych dzierżawy do raportowania. Te zapytania można wyodrębnić szczegółowe informacje pochowany w codziennych danych operacyjnych Wingtip Bilety SaaS dzierżawców. Aby to zrobić, należy wdrożyć dodatkową bazę danych raportowania na serwerze wykazu i użyć zapytania elastycznego, aby włączyć zapytania rozproszone.


Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> 
> * Jak wdrożyć bazę danych raportowania
> * Jak uruchamiać zapytania rozproszone we wszystkich bazach danych dzierżawy
> * Jak widoki globalne w każdej bazie danych mogą umożliwiać efektywne wykonywanie zapytań między dzierżawcami


Do wykonania zadań opisanych w tym samouczku niezbędne jest spełnienie następujących wymagań wstępnych:


* Wdrożono aplikację Bazy danych SaaS na dzierżawę Wingtip Tickets. Aby wdrożyć w mniej niż pięć minut, zobacz [Wdrażanie i eksplorowanie aplikacji Bazy danych SaaS Wingtip na dzierżawę](saas-dbpertenant-get-started-deploy.md)
* Zainstalowany jest program Azure PowerShell. Aby uzyskać szczegółowe informacje, zobacz [Rozpoczynanie pracy z programem Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* Program SQL Server Management Studio (SSMS) jest zainstalowany. Aby pobrać i zainstalować usługę SSMS, zobacz [Pobieranie programu SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="cross-tenant-reporting-pattern"></a>Wzorzec raportowania między dzierżawcami

![wzorzec kwerend rozproszonych między dzierżawcami](media/saas-tenancy-cross-tenant-reporting/cross-tenant-distributed-query.png)

Jedną z możliwości korzystania z aplikacji SaaS jest użycie ogromnej ilości danych dzierżawy przechowywanych w chmurze w celu uzyskania wglądu w działanie i użycie aplikacji. Te szczegółowe informacje mogą prowadzić do rozwoju funkcji, ulepszeń użyteczności i innych inwestycji w aplikacje i usługi.

Uzyskiwanie dostępu do tych danych w jednej wielodostępnej bazie danych jest łatwe, ale nie jest tak proste, gdy są one znacznie rozproszone, potencjalnie nawet na tysiące baz danych. Jednym z podejść jest użycie [zapytania elastycznego,](sql-database-elastic-query-overview.md)który umożliwia wykonywanie zapytań w rozproszonym zestawie baz danych ze wspólnym schematem. Te bazy danych mogą być dystrybuowane między różnymi grupami zasobów i subskrypcjami, ale muszą współużytkować wspólny login. Elastyczna kwerenda używa bazy danych z jedną *głowicą,* w której zdefiniowane są tabele zewnętrzne, które dublują tabele lub widoki w rozproszonych bazach danych (dzierżawy). Zapytania skierowane do tej głównej bazy danych są kompilowane w celu utworzenia planu zapytania rozproszonego, gdzie części zapytania są w razie potrzeby wypychane do baz danych dzierżawy. Elastyczna kwerenda używa mapy niezależnego fragmentu w bazie danych katalogu do określenia lokalizacji wszystkich baz danych dzierżawy. Konfiguracja i kwerenda bazy danych głowicy są proste przy użyciu standardowego [programu Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference)i obsługują wykonywanie zapytań z narzędzi takich jak Power BI i Excel.

Poprzez dystrybucję zapytań w bazach danych dzierżawy, elastyczne zapytanie zapewnia natychmiastowy wgląd w dane produkcji na żywo. Ponieważ zapytanie elastyczne pobiera dane z potencjalnie wielu baz danych, opóźnienie kwerendy może być wyższe niż równoważne zapytania przesłane do pojedynczej bazy danych z wieloma dzierżawcami. Projektowanie zapytań w celu zminimalizowania danych, które są zwracane do bazy danych head. Elastyczne zapytanie jest często najlepiej nadaje się do wykonywania zapytań o niewielkie ilości danych w czasie rzeczywistym, w przeciwieństwie do tworzenia często używanych lub złożonych zapytań analitycznych lub raportów. Jeśli kwerendy nie działają dobrze, spójrz na [plan wykonania,](https://docs.microsoft.com/sql/relational-databases/performance/display-an-actual-execution-plan) aby zobaczyć, jaka część kwerendy jest wypychany do zdalnej bazy danych i ile danych jest zwracany. Kwerendy, które wymagają złożonej agregacji lub przetwarzania analitycznego może być lepsze uchwyty przez wyodrębnianie danych dzierżawy do bazy danych lub magazynu danych zoptymalizowane pod kątem zapytań analitycznych. Ten wzorzec jest wyjaśniony w [samouczku analizy dzierżawy](saas-tenancy-tenant-analytics.md). 

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Pobierz skrypty aplikacji bazy danych SaaS dla bazy danych Wingtip na dzierżawę

Skrypty bazy danych wielodostępnych aplikacji Wingtip Tickets SaaS i kod źródłowy aplikacji są dostępne w repozytorium [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub. Zapoznaj się z [ogólnymi wskazówkami dotyczącymi](saas-tenancy-wingtip-app-guidance-tips.md) pobierania i odblokowywania skryptów SaaS biletów Wingtip.

## <a name="create-ticket-sales-data"></a>Tworzenie danych sprzedaży biletów

Aby uruchomić zapytania względem bardziej interesującego zestawu danych, utwórz dane sprzedaży biletów, uruchamiając generator biletów.

1. W *programie PowerShell ISE*otwórz ... \\Moduły\\szkoleniowe\\Analizy Operacyjnej Adhoc Raportowanie\\*Demo-AdhocReporting.ps1* skrypt i ustawić następującą wartość:
   * **$DemoScenario** = 1, **Kup bilety na wydarzenia we wszystkich miejscach**.
2. Naciśnij **klawisz F5,** aby uruchomić skrypt i wygenerować sprzedaż biletów. Gdy skrypt jest uruchomiony, kontynuuj kroki opisane w tym samouczku. Dane biletu są wyszukiwane w *uruchom ad hoc rozproszonych kwerend* sekcji sekcji, więc poczekaj na generator biletów, aby zakończyć.

## <a name="explore-the-global-views"></a>Poznaj widoki na całym świecie

W aplikacji Bazy danych SaaS Wingtip Tickets na dzierżawę każda dzierżawa otrzymuje bazę danych. W związku z tym dane zawarte w tabelach bazy danych jest zakres do perspektywy jednej dzierżawy. Jednak podczas wykonywania zapytań we wszystkich bazach danych, ważne jest, że elastyczne zapytanie może traktować dane tak, jakby jest częścią pojedynczej bazy danych logicznej podzielonej przez dzierżawę. 

Aby symulować ten wzorzec, zestaw widoków "globalnych" są dodawane do bazy danych dzierżawy, które projektu identyfikator dzierżawy do każdej z tabel, które są kwerendy globalnie. Na przykład *VenueEvents* widok dodaje obliczony *VenueId* do kolumn rzutowanych z *events* tabeli. Podobnie widoki *VenueTicketPurchases* i *VenueTickets* dodają obliczoną kolumnę *VenueId* wyświetlaną z ich odpowiednich tabel. Widoki te są używane przez kwerendę elastyczną do równoległości kwerend i wypychania ich do odpowiedniej bazy danych zdalnej dzierżawy, gdy znajduje się kolumna *VenueId.* To znacznie zmniejsza ilość zwracanych danych i powoduje znaczny wzrost wydajności dla wielu zapytań. Te widoki globalne zostały wstępnie utworzone we wszystkich bazach danych dzierżawy.

1. Otwórz SSMS i [połącz się&lt;&gt; z serwerem tenants1- USER](saas-tenancy-wingtip-app-guidance-tips.md#explore-database-schema-and-execute-sql-queries-using-ssms).
1. Rozwiń **pozycję Rozwiń bazy danych,** kliknij prawym przyciskiem myszy _contosoconcerthall_i wybierz polecenie **Nowa kwerenda**.
1. Uruchom następujące kwerendy, aby zbadać różnicę między tabelami z jedną dzierżawą a widokami globalnymi:

   ```T-SQL
   -- The base Venue table, that has no VenueId associated.
   SELECT * FROM Venue

   -- Notice the plural name 'Venues'. This view projects a VenueId column.
   SELECT * FROM Venues

   -- The base Events table, which has no VenueId column.
   SELECT * FROM Events

   -- This view projects the VenueId retrieved from the Venues table.
   SELECT * FROM VenueEvents
   ```

W tych widokach *VenueId* jest obliczany jako skrót nazwy miejsca, ale każde podejście może służyć do wprowadzenia unikatowej wartości. To podejście jest podobne do sposobu, w jaki klucz dzierżawy jest obliczany do użycia w katalogu.

Aby zbadać definicję widoku *Miejsca:*

1. W **Eksploratorze obiektów**rozwiń wszystkie widoki **contosoconcerth:** > **Views**

   ![widoki](media/saas-tenancy-cross-tenant-reporting/views.png)

2. Kliknij prawym przyciskiem myszy **dbo. Miejsca .**
3. Wybierz **widok skryptu jako** > okno**edytora create do** > **nowego zapytania**

Skrypt dowolnego z innych widoków *miejsca,* aby zobaczyć, jak dodać *VenueId*.

## <a name="deploy-the-database-used-for-distributed-queries"></a>Wdrażanie bazy danych używanej dla kwerend rozproszonych

To ćwiczenie wdraża _adhocreporting_ bazy danych. Jest to główna baza danych, która zawiera schemat używany do wykonywania zapytań we wszystkich bazach danych dzierżawy. Baza danych jest wdrażana na istniejącym serwerze wykazu, który jest serwerem używanym dla wszystkich baz danych związanych z zarządzaniem w przykładowej aplikacji.

1. w *programie PowerShell ISE*, otwórz ... \\Moduły\\szkoleniowe\\Analiza operacyjna Adhoc Raportowanie\\*Demo-AdhocReporting.ps1*. 

1. Ustaw **$DemoScenario = 2**, _Wdrażanie bazy danych raportowania ad hoc_.

1. Naciśnij **klawisz F5,** aby uruchomić skrypt i utworzyć bazę danych *adhocreporting.*

W następnej sekcji należy dodać schemat do bazy danych, dzięki czemu może służyć do uruchamiania kwerend rozproszonych.

## <a name="configure-the-head-database-for-running-distributed-queries"></a>Konfigurowanie bazy danych "head" do uruchamiania zapytań rozproszonych

To ćwiczenie dodaje schemat (zewnętrzne źródło danych i definicje tabel zewnętrznych) do bazy danych _adhocreporting,_ aby włączyć wykonywanie zapytań we wszystkich bazach danych dzierżawy.

1. Otwórz program SQL Server Management Studio i połącz się z bazą danych raportowania adhoc utworzoną w poprzednim kroku. Nazwa bazy danych jest *adhocreporting*.
2. Otwórz ...\Moduły szkoleniowe\Analiza operacyjna\Raportowanie Adhoc\ _Initialize-AdhocReportingDB.sql_ w programie SSMS.
3. Przejrzyj skrypt SQL i notatkę:

   Elastyczna kwerenda używa poświadczeń o zakresie bazy danych, aby uzyskać dostęp do każdej z baz danych dzierżawy. To poświadczenie musi być dostępne we wszystkich bazach danych i zwykle powinny być przyznawane minimalne prawa wymagane do włączenia tych kwerend.

    ![tworzenie poświadczenia](media/saas-tenancy-cross-tenant-reporting/create-credential.png)

   Z bazy danych wykazu jako zewnętrzne źródło danych, kwerendy są dystrybuowane do wszystkich baz danych zarejestrowanych w katalogu w momencie wykonywania kwerendy. Ponieważ nazwy serwerów są różne dla każdego wdrożenia, ten skrypt pobiera@servernamelokalizację bazy danych katalogu z bieżącego serwera (@ ), gdzie skrypt jest wykonywany.

    ![tworzenie zewnętrznego źródła danych](media/saas-tenancy-cross-tenant-reporting/create-external-data-source.png)

   Tabele zewnętrzne, które odwołują się do widoków globalnych opisanych w poprzedniej sekcji i zdefiniowanych za pomocą **DISTRIBUTION = SHARDED(VenueId)**. Ponieważ każdy *VenueId* mapuje do pojedynczej bazy danych, zwiększa to wydajność dla wielu scenariuszy, jak pokazano w następnej sekcji.

    ![tworzenie tabel zewnętrznych](media/saas-tenancy-cross-tenant-reporting/external-tables.png)

   Tabela lokalna _VenueTypes,_ który jest tworzony i wypełniany. Ta tabela danych odwołania jest powszechne we wszystkich bazach danych dzierżawy, więc może być reprezentowana w tym miejscu jako tabela lokalna i wypełniona wspólnymi danymi. W przypadku niektórych zapytań, po tej tabeli zdefiniowane w bazie danych head można zmniejszyć ilość danych, które muszą zostać przeniesione do bazy danych head.

    ![tworzenie tabeli](media/saas-tenancy-cross-tenant-reporting/create-table.png)

   Jeśli dołączysz tabele odwołań w ten sposób, należy zaktualizować schemat tabeli i dane przy każdej aktualizacji baz danych dzierżawy.

4. Naciśnij **klawisz F5,** aby uruchomić skrypt i zainicjować bazę danych *adhocreporting.* 

Teraz możesz uruchamiać zapytania rozproszone i zbierać szczegółowe informacje we wszystkich dzierżawach!

## <a name="run-distributed-queries"></a>Uruchamianie zapytań rozproszonych

Teraz, gdy baza danych *adhocreporting* jest skonfigurowana, śmiało i uruchom niektóre zapytania rozproszone. Dołącz plan wykonania dla lepszego zrozumienia, gdzie odbywa się przetwarzanie kwerendy. 

Podczas sprawdzania planu wykonania, umieść wskaźnik myszy na ikony planu, aby uzyskać szczegółowe informacje. 

Ważne jest, aby pamiętać, że ustawienie **DISTRIBUTION = SHARDED(VenueId)** po zdefiniowaniu zewnętrznego źródła danych zwiększa wydajność w wielu scenariuszach. Jak każdy *VenueId* mapuje do pojedynczej bazy danych, filtrowanie jest łatwo zrobić zdalnie, zwracając tylko dane potrzebne.

1. Otwórz... \\Moduły\\szkoleniowe\\Analizy Operacyjnej Adhoc Raportowanie\\*Demo-AdhocReportingQueries.sql* w SSMS.
2. Upewnij się, że są połączone z bazy danych **adhocreporting.**
3. Wybierz menu **Kwerenda** i kliknij pozycję **Uwzględnij plan rzeczywistego wykonania**
4. Wyróżnij *kwerendę, które miejsca są aktualnie zarejestrowane?* i naciśnij klawisz **F5**.

   Kwerenda zwraca całą listę miejsc, ilustrując, jak szybko i łatwo jest do kwerendy we wszystkich dzierżawców i zwracać dane z każdej dzierżawy.

   Sprawdź plan i zobacz, że cały koszt znajduje się w zdalnej kwerendzie. Każda baza danych dzierżawy wykonuje kwerendę zdalnie i zwraca informacje o jego miejscu do bazy danych head.

   ![WYBIERZ * Z dbo. Miejscach](media/saas-tenancy-cross-tenant-reporting/query1-plan.png)

5. Zaznacz następną kwerendę i naciśnij klawisz **F5**.

   Ta kwerenda łączy dane z baz danych dzierżawy i lokalnej tabeli *VenueTypes* (lokalna, ponieważ jest to tabela w bazie danych *adhocreporting).*

   Sprawdź plan i zobacz, że większość kosztów to zapytanie zdalne. Każda baza danych dzierżawy zwraca informacje o miejscu i wykonuje sprzężenie lokalne za pomocą lokalnej tabeli *VenueTypes,* aby wyświetlić przyjazną nazwę.

   ![Dołączanie na zdalnych i lokalnych danych](media/saas-tenancy-cross-tenant-reporting/query2-plan.png)

6. Teraz wybierz *w którym dniu było najwięcej sprzedanych biletów?* **F5**

   Ta kwerenda wykonuje nieco bardziej złożone łączenie i agregację. Większość przetwarzania odbywa się zdalnie.  Tylko pojedyncze wiersze, zawierające dzienną sprzedaż biletów każdego miejsca dziennie, są zwracane do bazy danych head.

   ![query](media/saas-tenancy-cross-tenant-reporting/query3-plan.png)


## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> 
> * Uruchamiania rozproszonych zapytań dla wszystkich baz danych dzierżawy
> * Wdrażanie bazy danych raportowania i definiowanie schematu wymaganego do uruchamiania kwerend rozproszonych.


Teraz wypróbuj [samouczek analizy dzierżawy,](saas-tenancy-tenant-analytics.md) aby eksplorować wyodrębnianie danych do oddzielnej bazy danych analizy w celu bardziej złożonego przetwarzania analizy.

## <a name="additional-resources"></a>Zasoby dodatkowe

* Dodatkowe [samouczki, które opierają się na aplikacji Bazy danych SaaS Wingtip Tickets na dzierżawę](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Elastyczne zapytanie](sql-database-elastic-query-overview.md)
