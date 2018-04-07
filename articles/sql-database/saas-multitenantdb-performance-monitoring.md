---
title: Monitorowanie wydajności podzielonej bazy danych Azure SQL wielodostępne w wielodostępnych aplikacji SaaS | Dokumentacja firmy Microsoft
description: Monitorowanie i zarządzanie nimi wydajności podzielonej bazy danych Azure SQL wielodostępne w wielodostępnych aplikacji SaaS
keywords: samouczek usługi sql database
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 43bac88a7ab6320c5fdcc9dc0fb6b5209bdbcaa3
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="monitor-and-manage-performance-of-sharded-multi-tenant-azure-sql-database-in-a-multi-tenant-saas-app"></a>Monitorowanie i zarządzanie nimi wydajności podzielonej bazy danych Azure SQL wielodostępne w wielodostępnych aplikacji SaaS

W tym samouczku są przedstawione kilka kluczowych scenariuszy zarządzania używana w aplikacji SaaS. Aby symulować działanie dla podzielonej wielodostępnych baz danych za pomocą generatora obciążenia, wbudowaną funkcję monitorowania oraz alertów funkcji bazy danych SQL przedstawiono.

Aplikacja Wingtip biletów SaaS wielodostępne w bazie danych korzysta z modelu podzielonej danych z wieloma dzierżawcami, gdzie miejscową (dzierżawcy) danych jest rozłożona według Identyfikatora dzierżawy potencjalnie wiele baz danych. Podobnie jak w przypadku wielu innych aplikacji SaaS, oczekiwany wzorzec obciążenia dzierżawy charakteryzuje się nieprzewidywalnością i sporadycznością występowania. Innymi słowy, sprzedaż biletów może nastąpić w dowolnej chwili. Aby móc korzystać z tego wzorca użycia typowych bazy danych, bazy danych można skalować w górę i w dół do optymalizowania kosztów rozwiązania. W przypadku tego typu wzorzec ważne jest monitorowanie użycia zasobów bazy danych, aby upewnić się, że obciążenia rozsądnych równoważone potencjalnie wiele baz danych. Należy również zapewnić odpowiednie zasoby pojedynczych baz danych i czy nie osiągnięto ich [jednostek dtu w warstwie](sql-database-what-is-a-dtu.md) limity. W tym samouczku opisuje sposoby monitorowania i zarządzania nimi baz danych i sposobu podjęcia działań naprawczych w odpowiedzi na zmiany obciążenia.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]

> * Symulowanie użycia bazy danych podzielonej wielodostępne uruchamiając generator podana obciążenia
> * Monitorowanie bazy danych jako odpowiada wzrost obciążenia
> * Skalowanie w górę bazy danych w odpowiedzi na obciążenie zwiększona bazy danych
> * Zapewnij dzierżawcy w bazie danych pojedynczej dzierżawy

Do wykonania zadań opisanych w tym samouczku niezbędne jest spełnienie następujących wymagań wstępnych:

* Aplikacja Wingtip biletów SaaS wielodostępne w bazie danych jest wdrażana. Aby wdrożyć w mniej niż 5 minut, zobacz [Wdróż i eksplorowanie Wingtip biletów SaaS wielodostępne w bazie danych aplikacji](saas-multitenantdb-get-started-deploy.md)
* Zainstalowany jest program Azure PowerShell. Aby uzyskać szczegółowe informacje, zobacz [Rozpoczynanie pracy z programem Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-saas-performance-management-patterns"></a>Wprowadzenie do wzorców Zarządzanie wydajności SaaS

Zarządzanie wydajnością bazy danych polega na zbieraniu danych dotyczących wydajności i analizowaniu ich, a następnie reagowaniu na te dane przez dostosowanie parametrów w celu zachowania akceptowalnego czasu odpowiedzi aplikacji. 

### <a name="performance-management-strategies"></a>Strategie zarządzania wydajnością

* Aby uniknąć konieczności ręcznego monitorować wydajność, jest najbardziej efektywne **ustawić alerty, które są wyzwalane w razie Zabłąkana baz danych poza normalne zakresy**.
* Aby odpowiedzieć na krótkoterminowych wahań poziomu wydajności bazy danych, **poziom jednostek dtu w warstwie można skalować w górę lub w dół**. W przypadku tego wahań na podstawie regularnych lub przewidywalną **skalowania bazy danych można zaplanować zautomatyzowane**. Na przykład skalowanie w dół może nastąpić, kiedy przewidywane jest niskie obciążenie — w nocy lub podczas weekendów.
* Odpowiedź do długoterminowego wahań lub zmiany w dzierżawcami, **poszczególnych dzierżawców można przenosić do innych bazy danych**.
* Odpowiedzieć na krótkoterminowej wzrost *poszczególnych* obciążenia dzierżawcy **poszczególnych dzierżawców można pobierane z bazy danych i przypisane poziom wydajności poszczególnych**. Po zmniejsza obciążenie dzierżawcy mogą być zwracane następnie do bazy danych wielu dzierżawców. Jeśli jest to znane z wyprzedzeniem, dzierżawców można przenieść pre-emptively upewnij się, że baza danych ma zawsze zasoby, które są niezbędne i uniknąć wpływu na innych dzierżawców w bazie danych wielodostępnej. Jeśli takie wymaganie jest przewidywalne, na przykład w przypadku oczekiwania na wzmożone zakupy biletów na popularną imprezę, wówczas takie działanie funkcji zarządzania można uwzględnić w aplikacji.

Witryna [Azure Portal](https://portal.azure.com) udostępnia wbudowane funkcje monitorowania i alertów dla większości zasobów. Bazy danych SQL jest dostępna w bazach danych, monitorowanie i alerty. Ten wbudowaną funkcję monitorowania i alertów jest określonych zasobów, dlatego jest łatwe w użyciu dla małej liczby zasobów, ale nie jest wygodne, podczas pracy z wielu zasobów.

Dla dużych scenariuszy, w którym pracujesz z wielu zasobów, [analizy dzienników](https://azure.microsoft.com/services/log-analytics/) mogą być używane. Jest to oddzielne usługa Azure, która udostępnia analityka w porównaniu z emitowany dzienniki diagnostyczne i dane telemetryczne zebrane w obszarze roboczym analizy dzienników. Analiza dzienników może zbierać dane telemetryczne z wielu usług i służyć do wykonywania zapytań i Ustaw alerty.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Pobieranie kodu źródłowego aplikacji Wingtip biletów SaaS wielodostępne w bazie danych i skryptów

Skrypty Wingtip biletów SaaS wielodostępne w bazie danych i kodu źródłowego aplikacji są dostępne w [WingtipTicketsSaaS MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) repozytorium GitHub. Zapoznaj się z [ogólne wskazówki](saas-tenancy-wingtip-app-guidance-tips.md) dla czynności, aby pobrać i odblokować skrypty Wingtip biletów SaaS.

## <a name="provision-additional-tenants"></a>Aprowizacja dodatkowych dzierżaw

Dla dobrą znajomością działania zarządzania i monitorowania wydajności na dużą skalę, w tym samouczku wymaga ma wiele dzierżaw w bazie danych podzielonej wielodostępnej.

Jeśli już po uprzednim udostępnieniu partii dzierżaw w poprzednich instrukcji, przejdź do [Symulacja użycie na wszystkich baz danych dzierżawy](#simulate-usage-on-all-tenant-databases) sekcji.

1. W **PowerShell ISE**, Otwórz... \\Modułów szkoleniowych\\zarządzania i monitorowania wydajności\\*PerformanceMonitoringAndManagement.ps1 pokaz*. Nie zamykaj tego skryptu, gdyż w ramach tego samouczka będzie konieczne uruchomienie kilku scenariuszy.
1. Ustaw zmienną **$DemoScenario** = **1**, _Provision a batch of tenants_ (Aprowizacja partii dzierżaw)
1. Naciśnij klawisz **F5**, aby uruchomić skrypt.

Skrypt wdraża 17 dzierżaw w bazie danych wielodostępne za kilka minut. 

*TenantBatch nowy* skrypt tworzy nowi dzierżawcy z dzierżawcą unikatowy kluczy w ramach bazy danych podzielonej wielodostępnych i inicjuje je z typem nazwę i miejsce dzierżawy. Jest to zgodne z sposób aplikacja udostępnia nową dzierżawę. 

## <a name="simulate-usage-on-all-tenant-databases"></a>Symulowanie użycia we wszystkich baz danych dzierżaw

*PerformanceMonitoringAndManagement.ps1 pokaz* skrypt jest pod warunkiem, która symuluje pracą bazy danych wielu dzierżawców. Obciążenie jest generowana z użyciem jednego ze scenariuszy dostępnych obciążenia:

| Demonstracja | Scenariusz |
|:--|:--|
| 2 | Generowanie normalnym natężeniu obciążenia (przybliżone 30 jednostek dtu w warstwie) |
| 3 | Generowanie obciążenia z dłużej seria dla każdego dzierżawcy|
| 4 | Generowanie obciążenia z wyższej seria jednostek DTU dla poszczególnych dzierżawców (przybliżone 70 jednostek dtu w warstwie)|
| 5 | Generowanie wysokiej intensywność (przybliżone 90 jednostek dtu w warstwie) na pojedynczej dzierżawy plus normalnym natężeniu obciążenie na innych dzierżawców |

Generator obciążenia stosuje obciążenie *syntetyczne* wyłącznie do procesorów dla każdej bazy danych dzierżawy. Generator uruchamia zadanie dla każdej bazy danych dzierżawy, co powoduje cykliczne wywołanie procedury składowanej, która generuje obciążenie. Poziomy obciążenia (w Dtu), czas trwania i interwały są różne dla wszystkich baz danych, symulując dzierżawy nieprzewidywalne działanie.

1. W **PowerShell ISE**, Otwórz... \\Modułów szkoleniowych\\zarządzania i monitorowania wydajności\\*PerformanceMonitoringAndManagement.ps1 pokaz*. Nie zamykaj tego skryptu, gdyż w ramach tego samouczka będzie konieczne uruchomienie kilku scenariuszy.
1. Ustaw **$DemoScenario** = **2**, _Generuj normalnym natężeniu obciążenia_
1. Naciśnij klawisz **F5** do zastosowania obciążenia dla wszystkich dzierżawców.

Wingtip biletów SaaS wielodostępne w bazie danych jest to aplikacja SaaS i rzeczywistych obciążenie aplikacji SaaS jest zazwyczaj sporadyczne i nieprzewidywalnych. Aby to zasymulować, generator w sposób losowy obciąża wszystkie dzierżawy. Kilka minut są wymagane dla wzorca obciążenia się, więc uruchomienie generatora obciążenia 3 – 5 minut przed podjęciem próby wykonania do monitorowania obciążenia w poniższych sekcjach.

> [!IMPORTANT]
> Generator obciążenia działa jako serię zadań w nowym oknie programu PowerShell. Po zamknięciu sesji zatrzymuje generator obciążenia. Generator obciążenia pozostaje w *wywoływania zadania* stanu, w którym generuje obciążenie nowi dzierżawcy, które są udostępniane po rozpoczęciu generatora. Użyj *Ctrl-C* powoduje zatrzymanie wywoływania nowe zadania i zamknięcie skryptu. Generator obciążenia będzie nadal działać, ale tylko na istniejący dzierżawcy.

## <a name="monitor-resource-usage-using-the-azure-portal"></a>Monitorowanie użycia zasobów przy użyciu portalu Azure

Monitorowanie użycia zasobów, będącą wynikiem obciążenia są stosowane, Otwórz portal do bazy danych wielu dzierżawców **tenants1**, zawierający dzierżawcy:

1. Otwórz [portalu Azure](https://portal.azure.com) , a następnie przejdź do serwera *tenants1-mt -&lt;użytkownika&gt;*.
1. Przewiń w dół i Znajdź baz danych i kliknij przycisk **tenants1**. Ta podzielonej wielodostępne baza danych zawiera wszystkie dzierżaw utworzono dotychczas.

![Baza danych wykresu](./media/saas-multitenantdb-performance-monitoring/multitenantdb.png)

Obserwować **jednostek dtu w warstwie** wykresu.

## <a name="set-performance-alerts-on-the-database"></a>Ustaw alerty wydajności bazy danych

Ustawić alert w bazie danych, które wyzwala na \>75% wykorzystania w następujący sposób:

1. Otwórz *tenants1* bazy danych (na *tenants1-mt -&lt;użytkownika&gt;*  serwera) w [portalu Azure](https://portal.azure.com).
1. Kliknij pozycję **Reguły alertów**, a następnie kliknij przycisk **+ Dodaj alert**:

   ![dodawanie alertu](media/saas-multitenantdb-performance-monitoring/add-alert.png)

1. Podaj nazwę, taką jak **Wysoki poziom jednostek DTU**,
1. Ustaw następujące wartości:
   * **Metryka = procent użycia jednostek DTU**
   * **Warunek = większa niż**
   * **Próg = 75**.
   * **Okres = w ciągu ostatnich 30 minut**
1. Dodaj adres e-mail do *email(s) dodatkowe administratora* polu i kliknij przycisk **OK**.

   ![ustawianie alertu](media/saas-multitenantdb-performance-monitoring/set-alert.png)

## <a name="scale-up-a-busy-database"></a>Skalowanie w górę zajęty bazy danych

Jeśli poziom obciążenia zwiększa się w bazie danych do punktu maxes limit bazy danych i osiągnie użycie jednostek DTU 100%, wydajność bazy danych ma wpływ i potencjalnie spowalniając czas odpowiedzi na zapytania.

**Krótka termin**, należy wziąć pod uwagę skalowania bazy danych, aby zapewnić dodatkowe zasoby lub usunięcia dzierżawcy z bazy danych wielu dzierżawców (przenoszenia ich z bazy danych wielu dzierżawców do autonomicznej bazy danych).

**Dłuższym okresie**, należy wziąć pod uwagę optymalizacji zapytań lub indeksu użycia w celu poprawy wydajności bazy danych. W zależności od aplikacji czułość na wydajność wystawia jego najlepsze rozwiązanie można skalować bazy danych, przed jego użycie jednostek DTU 100%. Użyj alertów do wczesnego ostrzegania.

Można symulować zajęty bazy danych przez odpowiednie zwiększenie obciążenia utworzonego przez generator. Powoduje dzierżawcy do serii, częściej, a także zwiększyć obciążenie bazy danych wielu dzierżawców bez zmiany wymagań dotyczących poszczególnych dzierżawców. Skalowanie bazę danych łatwo odbywa się w portalu lub z programu PowerShell. W tym ćwiczeniu użyto portalu.

1. Ustaw *$DemoScenario* = **3**, _obciążenia Generuj z dłużej i częstsze seria na bazę danych_ zwiększyć intensywność agregacji obciążenia na Baza danych bez zmieniania obciążenia szczytowego wymagane przez każdego dzierżawcę.
1. Naciśnij klawisz **F5**, aby zastosować obciążenie do wszystkich baz danych dzierżaw.
1. Przejdź do **tenants1** bazy danych w portalu Azure.

Monitorowanie bazy danych zwiększone użycie jednostek DTU w górnym wykresu. Zajmuje kilka minut na nowe obciążenia wyższej zaczną działać, ale powinny pojawić szybko rozpocząć osiągnęła maksymalny wykorzystania bazy danych, a jako steadies obciążenia na nowy wzorzec, szybko overloads bazy danych.

1. Aby skalować bazy danych, kliknij przycisk **warstwa cenowa (skala Dtu)** w bloku ustawienia.
1. Dostosuj **DTU** ustawienie **100**. 
1. Kliknij przycisk **Zastosuj** Aby przesłać żądanie skalowania bazy danych.

Wróć do **tenants1** > **omówienie** Aby przeglądać wykres monitorowania. Monitorowanie efektu działania zapewnia więcej zasobów bazy danych (chociaż kilka dzierżaw i obciążenia losowego nie zawsze jest łatwo sprawdzić ostatecznie, dopóki nie zostanie uruchomione przez pewien czas). Podczas poszukujesz opatrzone wykresy pamiętać, że 100% górnej wykresu teraz reprezentuje Dtu 100, gdy na wykresie niższe 100% jest nadal Dtu 50.

Bazy danych pozostają w trybie online i są w pełni dostępne podczas całego procesu. Kod aplikacji zawsze mają być zapisywane ponów przerwanie połączenia, a więc będzie ponownie z bazą danych.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Udostępnianie nowej dzierżawy w własną bazę danych 

Podzielonej modelu wielodostępnym pozwala wybrać, czy do udostępnienia nowej dzierżawy w bazie danych wielodostępne równolegle z innymi dzierżawcami lub zainicjować dzierżawcy w bazie danych własnych. Aprowizując dzierżawcy w własną bazę danych, korzysta z izolacji związane z oddzielnej bazy danych, co umożliwia zarządzanie wydajność tej dzierżawy, niezależnie od innych użytkowników, Przywróć tę dzierżawę, niezależnie od innych, itp. Na przykład można umieścić bezpłatnych wersji próbnych lub regularnych klientów w bazie danych z wieloma dzierżawcami i premium klienci w poszczególnych bazach danych.  Jeśli tworzone są izolowane pojedynczej dzierżawy baz danych, ich można nadal będą zarządzane wspólnie w puli elastycznej, aby optymalizować koszty zasobów.

Jeśli już przydzielona nowej dzierżawy w własną bazę danych, Pomiń następnych kilku krokach.

1. W **PowerShell ISE**, Otwórz... \\Modułów szkoleniowych\\ProvisionTenants\\*ProvisionTenants.ps1 pokaz*. 
1. Modyfikowanie **$TenantName = "Wierzbowate Salsa"** i **$VenueType = "taniec"**
1. Ustaw **$Scenario** = **2**, _udostępnić dzierżawcy w bazie danych nowy pojedynczej dzierżawy_
1. Naciśnij klawisz **F5**, aby uruchomić skrypt.

Skrypt zostanie udostępnienia tej dzierżawy w oddzielnej bazy danych, Zarejestruj bazę danych i dzierżawcy w wykazie, a następnie otwórz strony zdarzenia dzierżawy w przeglądarce. Odśwież stronę Centrum zdarzeń i zobaczysz, że "Wierzbowate Salsa" został dodany jako miejsce.

## <a name="manage-performance-of-a-single-database"></a>Zarządzanie wydajności pojedynczej bazy danych

Jeśli pojedynczej dzierżawy w bazie danych wielodostępne napotyka utrzymujących wysokie obciążenie, jego często dominują w aplikacjach zasobów bazy danych i innych dzierżaw w tej samej bazy danych. Jeśli działanie może kontynuować przez pewien czas, dzierżawcy mogą tymczasowo przeniesiony poza bazy danych i do jego własnej pojedynczej dzierżawy bazy danych. Dzięki temu dzierżawcy mają dodatkowe zasoby musi i pełni izoluje od pozostałych dzierżawców.

Tego ćwiczenia symuluje efekt Salsa wierzbowate, u których występują znaczne obciążenie biletów Przejdź w sprzedaży dla popularnych zdarzenia.

1. Otwórz... \\ *PerformanceMonitoringAndManagement.ps1 pokaz* skryptu.
1. Ustaw **$DemoScenario = 5**, _Generowanie typową plus wysokie obciążenie pojedynczej dzierżawy (przybliżone 90 jednostek dtu w warstwie)._
1. Ustaw **$SingleTenantName = Salix Salsa**
1. Wykonaj skrypt, używając klawisza **F5**.

Przejdź do portalu i przejdź do **salixsalsa** > **omówienie** Aby przeglądać wykres monitorowania. 

## <a name="other-performance-management-patterns"></a>Innymi wzorami zarządzania wydajności

**Skalowanie samoobsługi dzierżawcy**

Ponieważ skalowanie jest łatwo zadania o nazwie za pośrednictwem funkcji zarządzania interfejsu API, można łatwo kompilacji możliwość skalowania dzierżawy baz danych do aplikacji uwzględniającym dzierżawy i użyć funkcji usługi SaaS. Na przykład, można umożliwić dzierżawcom samodzielne administrowanie skalowaniem w górę i w dół, być może bezpośrednio powiązane z ich rozliczeniami!

**Skalowanie bazy danych w górę i w dół zgodnie z harmonogramem, aby dopasować wzorców użycia**

W przypadku, gdy użycie agregacji dzierżawy następuje wzorców użycia przewidywalne, można użyć usługi Automatyzacja Azure można skalować bazy danych w górę i w dół zgodnie z harmonogramem. Na przykład skalować bazy danych w dół po 18: 00 i się ponownie przed 6: 00 w dni robocze, gdy wiesz, ma spadek wymagań dotyczących zasobów.

## <a name="next-steps"></a>Kolejne kroki

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Symulowanie użycia bazy danych podzielonej wielodostępne uruchamiając generator podana obciążenia
> * Monitorowanie bazy danych jako odpowiada wzrost obciążenia
> * Skalowanie w górę bazy danych w odpowiedzi na obciążenie zwiększona bazy danych
> * Zapewnij dzierżawcy w bazie danych pojedynczej dzierżawy

## <a name="additional-resources"></a>Zasoby dodatkowe

<!--* [Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment](saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
* [Azure Automation](../automation/automation-intro.md)