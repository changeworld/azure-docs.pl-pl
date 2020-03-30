---
title: Zapytania raportowania ad hoc w wielu bazach danych
description: Uruchamianie zapytań raportowania ad hoc w wielu bazach danych SQL w przykładzie aplikacji z wieloma dzierżawcami.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: AyoOlubeko
ms.author: craigg
ms.reviewer: sstein
ms.date: 10/30/2018
ms.openlocfilehash: c0d1829c52041446b4feb43d8af262265e2680fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822183"
---
# <a name="run-ad-hoc-analytics-queries-across-multiple-azure-sql-databases"></a>Uruchamianie zapytań analitycznych ad hoc w wielu bazach danych SQL platformy Azure

W tym samouczku uruchomisz zapytania rozproszone w całym zestawie baz danych dzierżawy, aby włączyć interaktywne raportowanie ad hoc. Te zapytania można wyodrębnić szczegółowe informacje pochowany w codziennych danych operacyjnych aplikacji SaaS bilety Wingtip. Aby wykonać te ekstrakcje, należy wdrożyć dodatkową bazę danych analizy na serwerze wykazu i użyć zapytania elastycznego, aby włączyć zapytania rozproszone.


Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> 
> * Jak wdrożyć bazę danych raportowania ad hoc
> * Jak uruchamiać zapytania rozproszone we wszystkich bazach danych dzierżawy


Do wykonania zadań opisanych w tym samouczku niezbędne jest spełnienie następujących wymagań wstępnych:

* Wdrożono aplikację Wingtip Tickets SaaS Multi-tenant Database. Aby wdrożyć w mniej niż pięć minut, zobacz [Wdrażanie i eksplorowanie aplikacji Bazy danych wielodostępnych biletów Wingtip](saas-multitenantdb-get-started-deploy.md)
* Zainstalowany jest program Azure PowerShell. Aby uzyskać szczegółowe informacje, zobacz [Rozpoczynanie pracy z programem Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* Program SQL Server Management Studio (SSMS) jest zainstalowany. Aby pobrać i zainstalować usługę SSMS, zobacz [Pobieranie programu SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="ad-hoc-reporting-pattern"></a>Wzór raportowania ad hoc

![wzorzec raportowania adhoc](media/saas-multitenantdb-adhoc-reporting/adhocreportingpattern_shardedmultitenantDB.png)

Aplikacje SaaS mogą analizować ogromną ilość danych dzierżawy, które są przechowywane centralnie w chmurze. Analizy ujawniają wgląd w działanie i użycie aplikacji. Te szczegółowe informacje mogą prowadzić do rozwoju funkcji, ulepszeń użyteczności i innych inwestycji w aplikacje i usługi.

Uzyskiwanie dostępu do tych danych w jednej wielodostępnej bazie danych jest łatwe, ale nie jest tak proste, gdy są one znacznie rozproszone, potencjalnie nawet na tysiące baz danych. Jednym z podejść jest użycie [zapytania elastycznego,](sql-database-elastic-query-overview.md)który umożliwia wykonywanie zapytań w rozproszonym zestawie baz danych ze wspólnym schematem. Te bazy danych mogą być dystrybuowane między różnymi grupami zasobów i subskrypcjami. Jednak jeden wspólny login musi mieć dostęp do wyodrębniania danych ze wszystkich baz danych. Elastyczna kwerenda używa bazy danych z jedną *głowicą,* w której zdefiniowane są tabele zewnętrzne, które dublują tabele lub widoki w rozproszonych bazach danych (dzierżawy). Zapytania skierowane do tej głównej bazy danych są kompilowane w celu utworzenia planu zapytania rozproszonego, gdzie części zapytania są w razie potrzeby wypychane do baz danych dzierżawy. Elastyczna kwerenda używa mapy niezależnego fragmentu w bazie danych katalogu do określenia lokalizacji wszystkich baz danych dzierżawy. Konfiguracja i kwerenda są proste przy użyciu standardowego [programu Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference)i obsługują zapytania ad hoc z narzędzi takich jak Power BI i Excel.

Poprzez dystrybucję zapytań w bazach danych dzierżawy, elastyczne zapytanie zapewnia natychmiastowy wgląd w dane produkcji na żywo. Jednak ponieważ zapytanie elastyczne pobiera dane z potencjalnie wielu baz danych, opóźnienie kwerendy może czasami być wyższe niż w przypadku równoważnych zapytań przesłanych do bazy danych z jedną z wielu dzierżawców. Pamiętaj, aby zaprojektować kwerendy, aby zminimalizować dane, które są zwracane. Elastyczne zapytanie jest często najlepiej nadaje się do wykonywania zapytań o niewielkie ilości danych w czasie rzeczywistym, w przeciwieństwie do tworzenia często używanych lub złożonych zapytań analitycznych lub raportów. Jeśli kwerendy nie działają dobrze, spójrz na [plan wykonania,](https://docs.microsoft.com/sql/relational-databases/performance/display-an-actual-execution-plan) aby zobaczyć, jaka część kwerendy została wypchnięta do zdalnej bazy danych. I oceń, ile danych jest zwracanych. Zapytania, które wymagają złożonego przetwarzania analitycznego może być lepiej obsługiwane przez zapisywanie wyodrębnionych danych dzierżawy w bazie danych, która jest zoptymalizowana pod kątem zapytań analitycznych. Baza danych SQL i magazyn danych SQL może obsługiwać taką bazę danych analizy.

Ten wzorzec analizy jest wyjaśniony w [samouczku analizy dzierżawy](saas-multitenantdb-tenant-analytics.md).

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Pobierz kod źródłowy aplikacji I skrypty aplikacji Wingtip Tickets SaaS Multi-tenant Database

Skrypty bazy danych wielodostępnych Wingtip Tickets SaaS i kod źródłowy aplikacji są dostępne w repozytorium [GitHub WingtipTicketsSaaS-MultitenantDB.](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) Zapoznaj się z [ogólnymi wskazówkami dotyczącymi](saas-tenancy-wingtip-app-guidance-tips.md) pobierania i odblokowywania skryptów SaaS biletów Wingtip.

## <a name="create-ticket-sales-data"></a>Tworzenie danych sprzedaży biletów

Aby uruchomić zapytania względem bardziej interesującego zestawu danych, utwórz dane sprzedaży biletów, uruchamiając generator biletów.

1. W *programie PowerShell ISE*otwórz ... \\Moduły\\szkoleniowe\\Analizy Operacyjnej Adhoc Raportowanie\\*Demo-AdhocReporting.ps1* skrypt i ustawić następujące wartości:
   * **$DemoScenario** = 1, **Kup bilety na wydarzenia we wszystkich miejscach**.
2. Naciśnij **klawisz F5,** aby uruchomić skrypt i wygenerować sprzedaż biletów. Gdy skrypt jest uruchomiony, kontynuuj kroki opisane w tym samouczku. Dane biletu są wyszukiwane w *uruchom ad hoc rozproszonych kwerend* sekcji sekcji, więc poczekaj na generator biletów, aby zakończyć.

## <a name="explore-the-tenant-tables"></a>Eksplorowanie tabel dzierżawy 

W aplikacji Wingtip Tickets SaaS Multi-tenant Database dzierżawcy są przechowywane w modelu zarządzania dzierżawą hybrydową — gdzie dane dzierżawy są przechowywane w bazie danych z wieloma dzierżawcami lub w bazie danych z jedną dzierżawą i mogą być przenoszone między nimi. Podczas wykonywania zapytań we wszystkich bazach danych dzierżawy, ważne jest, że elastyczne zapytanie może traktować dane tak, jakby jest częścią pojedynczej bazy danych logicznej podzielonej przez dzierżawcę. 

Aby osiągnąć ten wzorzec, wszystkie tabele dzierżawy zawierają *VenueId* kolumny, która identyfikuje dzierżawy danych należy do. *VenueId* jest obliczany jako skrót nazwy miejsca, ale każde podejście może służyć do wprowadzenia unikatową wartość dla tej kolumny. To podejście jest podobne do sposobu, w jaki klucz dzierżawy jest obliczany do użycia w katalogu. Tabele zawierające *VenueId* są używane przez kwerendę elastyczną do równoległości kwerend i wypychania ich do odpowiedniej bazy danych zdalnej dzierżawy. To znacznie zmniejsza ilość danych, które są zwracane i powoduje wzrost wydajności, zwłaszcza gdy istnieje wiele dzierżawców, których dane są przechowywane w bazach danych pojedynczej dzierżawy.

## <a name="deploy-the-database-used-for-ad-hoc-distributed-queries"></a>Wdrażanie bazy danych używanej do kwerend rozproszonych ad hoc

To ćwiczenie wdraża *adhocreporting* bazy danych. Jest to główna baza danych, która zawiera schemat używany do wykonywania zapytań we wszystkich bazach danych dzierżawy. Baza danych jest wdrażana na istniejącym serwerze wykazu, który jest serwerem używanym dla wszystkich baz danych związanych z zarządzaniem w przykładowej aplikacji.

1. Otwórz... \\Moduły\\szkoleniowe\\Analizy Operacyjnej Adhoc Reporting\\*Demo-AdhocReporting.ps1* w programie *PowerShell ISE* i ustawić następujące wartości:
   * **$DemoScenario** = 2, **Wdrażanie bazy danych analizy ad hoc**.

2. Naciśnij **klawisz F5,** aby uruchomić skrypt i utworzyć bazę danych *adhocreporting.*

W następnej sekcji należy dodać schemat do bazy danych, dzięki czemu może służyć do uruchamiania kwerend rozproszonych.

## <a name="configure-the-head-database-for-running-distributed-queries"></a>Konfigurowanie bazy danych "head" do uruchamiania zapytań rozproszonych

To ćwiczenie dodaje schemat (zewnętrzne źródło danych i definicje tabel zewnętrznych) do bazy danych raportowania ad hoc, która umożliwia wykonywanie zapytań we wszystkich bazach danych dzierżawy.

1. Otwórz program SQL Server Management Studio i połącz się z bazą danych raportowania Adhoc utworzoną w poprzednim kroku. Nazwa bazy danych jest *adhocreporting*.
2. Otwórz ...\Moduły szkoleniowe\Analiza operacyjna\Raportowanie Adhoc\ *Initialize-AdhocReportingDB.sql* w programie SSMS.
3. Przejrzyj skrypt SQL i zanotuj następujące czynności:

   Elastyczna kwerenda używa poświadczeń o zakresie bazy danych, aby uzyskać dostęp do każdej z baz danych dzierżawy. To poświadczenie musi być dostępne we wszystkich bazach danych i zwykle powinny być przyznawane minimalne prawa wymagane do włączenia tych zapytań ad hoc.

    ![tworzenie poświadczenia](media/saas-multitenantdb-adhoc-reporting/create-credential.png)

   Korzystając z bazy danych wykazu jako zewnętrznego źródła danych, kwerendy są dystrybuowane do wszystkich baz danych zarejestrowanych w katalogu po uruchomieniu kwerendy. Ponieważ nazwy serwerów są różne dla każdego wdrożenia, ten skrypt inicjowania pobiera@servernamelokalizację bazy danych katalogu, pobierając bieżący serwer (@ ), gdzie skrypt jest wykonywany.

    ![tworzenie zewnętrznego źródła danych](media/saas-multitenantdb-adhoc-reporting/create-external-data-source.png)

   Tabele zewnętrzne, które odwołują się do tabel dzierżawy są definiowane za pomocą **DISTRIBUTION = SHARDED(VenueId)**. To kieruje kwerendę dla określonego *VenueId* do odpowiedniej bazy danych i zwiększa wydajność dla wielu scenariuszy, jak pokazano w następnej sekcji.

    ![tworzenie tabel zewnętrznych](media/saas-multitenantdb-adhoc-reporting/external-tables.png)

   Tabela lokalna *VenueTypes,* który jest tworzony i wypełniany. Ta tabela danych odwołania jest powszechne we wszystkich bazach danych dzierżawy, więc może być reprezentowana w tym miejscu jako tabela lokalna i wypełniona wspólnymi danymi. W przypadku niektórych zapytań może to zmniejszyć ilość danych przenoszonych między bazami danych dzierżawy a bazą danych *adhocreporting.*

    ![tworzenie tabeli](media/saas-multitenantdb-adhoc-reporting/create-table.png)

   Jeśli dołączysz tabele odwołań w ten sposób, należy zaktualizować schemat tabeli i dane przy każdej aktualizacji baz danych dzierżawy.

4. Naciśnij **klawisz F5,** aby uruchomić skrypt i zainicjować bazę danych *adhocreporting.* 

Teraz możesz uruchamiać zapytania rozproszone i zbierać szczegółowe informacje we wszystkich dzierżawach!

## <a name="run-ad-hoc-distributed-queries"></a>Uruchamianie zapytań rozproszonych ad hoc

Teraz, gdy baza danych *adhocreporting* jest skonfigurowana, śmiało i uruchom niektóre zapytania rozproszone. Dołącz plan wykonania dla lepszego zrozumienia, gdzie odbywa się przetwarzanie kwerendy. 

Podczas sprawdzania planu wykonania, umieść wskaźnik myszy na ikony planu, aby uzyskać szczegółowe informacje. 

1. W *SSMS*, otwórz ... \\Moduły\\szkoleniowe\\Analiza operacyjna Adhoc Raportowanie\\*Demo-AdhocReportingQueries.sql*.
2. Upewnij się, że są połączone z bazy danych **adhocreporting.**
3. Wybierz menu **Kwerenda** i kliknij pozycję **Uwzględnij plan rzeczywistego wykonania**
4. Wyróżnij *kwerendę, które miejsca są aktualnie zarejestrowane?* i naciśnij klawisz **F5**.

   Kwerenda zwraca całą listę miejsc, ilustrując, jak szybko i łatwo jest do kwerendy we wszystkich dzierżawców i zwracać dane z każdej dzierżawy.

   Sprawdź plan i zobacz, że cały koszt jest zdalnym zapytaniem, ponieważ po prostu przechodzimy do każdej bazy danych dzierżawy i wybieramy informacje o miejscu.

   ![WYBIERZ * Z dbo. Miejscach](media/saas-multitenantdb-adhoc-reporting/query1-plan.png)

5. Zaznacz następną kwerendę i naciśnij klawisz **F5**.

   Ta kwerenda łączy dane z baz danych dzierżawy i lokalnej tabeli *VenueTypes* (lokalna, ponieważ jest to tabela w bazie danych *adhocreporting).*

   Sprawdź plan i zobacz, że większość kosztów jest zdalnym zapytaniem, ponieważ zapytania o informacje o miejscu każdej dzierżawy (dbo. Miejsca), a następnie wykonaj szybkie sprzężenie lokalne za pomocą lokalnej tabeli *VenueTypes,* aby wyświetlić przyjazną nazwę.

   ![Dołączanie na zdalnych i lokalnych danych](media/saas-multitenantdb-adhoc-reporting/query2-plan.png)

6. Teraz wybierz *w którym dniu było najwięcej sprzedanych biletów?* **F5**

   Ta kwerenda wykonuje nieco bardziej złożone łączenie i agregację. Ważne jest, aby pamiętać, że większość przetwarzania odbywa się zdalnie i po raz kolejny przywracamy tylko wiersze, których potrzebujemy, zwracając tylko jeden wiersz dla łącznej liczby sprzedaży biletów każdego miejsca dziennie.

   ![query](media/saas-multitenantdb-adhoc-reporting/query3-plan.png)


## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> 
> * Uruchamiania rozproszonych zapytań dla wszystkich baz danych dzierżawy
> * Wdrażanie bazy danych raportowania ad hoc i dodawanie schematu do niego do uruchamiania kwerend rozproszonych.

Teraz wypróbuj [samouczek analizy dzierżawy,](saas-multitenantdb-tenant-analytics.md) aby eksplorować wyodrębnianie danych do oddzielnej bazy danych analizy w celu bardziej złożonego przetwarzania analizy.

## <a name="additional-resources"></a>Zasoby dodatkowe

<!-- ??
* Additional [tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application](saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->

* [Elastyczne zapytanie](sql-database-elastic-query-overview.md)
