---
title: Monitorowanie wydajności wiele baz danych Azure SQL w wielodostępnych aplikacji SaaS | Dokumentacja firmy Microsoft
description: Monitorowanie i zarządzanie nimi wydajności bazy danych Azure SQL i pul w wielodostępnych aplikacji SaaS
keywords: samouczek usługi sql database
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: d8e260b8dabb4c6823d59374a7b8661e024f1b3d
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36752275"
---
# <a name="monitor-and-manage-performance-of-azure-sql-databases-and-pools-in-a-multi-tenant-saas-app"></a>Monitorowanie i zarządzanie nimi wydajności bazy danych Azure SQL i pul w wielodostępnych aplikacji SaaS

W tym samouczku są przedstawione kilka kluczowych scenariuszy zarządzania używana w aplikacji SaaS. Za pomocą generatora obciążenia, aby symulować działanie dla wszystkich baz danych dzierżawy, wbudowaną funkcję monitorowania oraz alertów funkcje bazy danych SQL i pule elastyczne przedstawiono.

Aplikacja Wingtip biletów SaaS bazy danych dla dzierżawy korzysta z modelu danych pojedynczej dzierżawy, gdzie każda właściwość (dzierżawcy) ma własne bazy danych. Podobnie jak w przypadku wielu innych aplikacji SaaS, oczekiwany wzorzec obciążenia dzierżawy charakteryzuje się nieprzewidywalnością i sporadycznością występowania. Innymi słowy, sprzedaż biletów może nastąpić w dowolnej chwili. Aby jak najlepiej wykorzystać ten typowy wzorzec korzystania z bazy danych, bazy danych dzierżaw zostały wdrożone w elastycznych pulach baz danych. Elastyczne pule umożliwiają optymalizację kosztu rozwiązania dzięki udostępnieniu zasobów pomiędzy wieloma bazami danych. W przypadku tego typu wzorca ważne jest, aby monitorować użycie zasobów bazy danych i puli, co ma na celu rozsądne równoważenie obciążenia między pulami. Należy także upewnić się, że pojedyncze bazy danych posiadają odpowiednie zasoby, i że pule nie zbliżają się do swoich limitów liczby jednostek [eDTU](sql-database-service-tiers.md#what-are-database-transaction-units-dtus). W tym samouczku przedstawiono metody monitorowania baz danych i pul oraz zarządzania nimi, a także wykonywanie akcji naprawczych w odpowiedzi na wahania obciążenia.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]

> * Symulowanie korzystania z baz danych dzierżaw przy użyciu dostarczonego generatora obciążenia
> * Monitorowanie reakcji baz danych dzierżaw na wzrost obciążenia
> * Skalowanie w górę elastycznej puli w reakcji na zwiększone obciążenie bazy danych
> * Aprowizacja drugiej elastycznej puli w celu równoważenia aktywności baz danych


Do wykonania zadań opisanych w tym samouczku niezbędne jest spełnienie następujących wymagań wstępnych:

* Aplikacja Wingtip biletów SaaS bazy danych dla dzierżawy jest wdrażana. Aby wdrożyć w mniej niż 5 minut, zobacz [Wdróż i eksplorowanie aplikacji Wingtip biletów SaaS bazy danych dla dzierżawcy](saas-dbpertenant-get-started-deploy.md)
* Zainstalowany jest program Azure PowerShell. Aby uzyskać szczegółowe informacje, zobacz [Rozpoczynanie pracy z programem Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-saas-performance-management-patterns"></a>Wprowadzenie do wzorców Zarządzanie wydajności SaaS

Zarządzanie wydajnością bazy danych polega na zbieraniu danych dotyczących wydajności i analizowaniu ich, a następnie reagowaniu na te dane przez dostosowanie parametrów w celu zachowania akceptowalnego czasu odpowiedzi aplikacji. W przypadku hostowania wielu dzierżaw elastyczne pule baz danych stanowią ekonomiczny sposób udostępniania zasobów i zarządzania nimi dla grupy baz danych obciążanych w nieprzewidywalny sposób. Przy pewnych wzorcach obciążenia korzyści mogą się pojawić przy zarządzaniu w puli zaledwie dwoma bazami danych S3.

![diagram aplikacji](./media/saas-dbpertenant-performance-monitoring/app-diagram.png)

Pul i baz danych w puli, powinny być monitorowane w celu zapewnienia, że te komputery pozostaną w dopuszczalnych zakresach wydajności. Dostrajania konfiguracji puli na potrzeby agregacji obciążenia wszystkich baz danych, zapewnienie odpowiedniej dla obciążenia ogólną jednostek Edtu puli. Dostosuj minimalne i maksymalne wartości eDTU dla bazy danych do wartości odpowiadających wymaganiom konkretnych aplikacji.

### <a name="performance-management-strategies"></a>Strategie zarządzania wydajnością

* Aby uniknąć konieczności ręcznego monitorować wydajność, jest najbardziej efektywne **ustawić alerty, które są wyzwalane w razie baz danych lub pul Zabłąkana poza normalne zakresy**.
* Odpowiedzią na krótkoterminowe wahania zagregowanego poziomu wydajności puli **może być skalowanie w górę lub w dół poziomu jednostek eDTU puli**. Jeśli takie wahania występują regularnie lub są przewidywalne, **można zaplanować automatyczne skalowanie puli**. Na przykład skalowanie w dół może nastąpić, kiedy przewidywane jest niskie obciążenie — w nocy lub podczas weekendów.
* Odpowiedzią na wahania długoterminowe lub zmiany liczby baz danych jest **przeniesienie pojedynczych baz danych do innych pul**.
* Odpowiedzieć na krótkoterminowej wzrost *poszczególnych* obciążenie bazy danych **pojedynczych baz danych można znaleźć poza puli i przypisane poziom wydajności poszczególnych**. Po zmniejszeniu obciążenia można zwrócić bazę danych do puli. Jeśli jest to znane z wyprzedzeniem, baz danych można przenieść pre-emptively upewnij się, że baza danych ma zawsze zasoby, które są niezbędne i uniknąć wpływu na innych baz danych w puli. Jeśli takie wymaganie jest przewidywalne, na przykład w przypadku oczekiwania na wzmożone zakupy biletów na popularną imprezę, wówczas takie działanie funkcji zarządzania można uwzględnić w aplikacji.

Witryna [Azure Portal](https://portal.azure.com) udostępnia wbudowane funkcje monitorowania i alertów dla większości zasobów. W usłudze SQL Database funkcje monitorowania i zgłaszania alertów są dostępne na poziomie baz danych i pul. Ten wbudowaną funkcję monitorowania i alertów jest określonych zasobów, dlatego jest łatwe w użyciu dla małej liczby zasobów, ale nie jest wygodną podczas pracy z wielu zasobów.

Dla dużych scenariuszy, w którym pracujesz z wielu zasobów, [analizy dzienników](saas-dbpertenant-log-analytics.md) mogą być używane. Jest to oddzielne usługa Azure, która udostępnia analityka w porównaniu z emitowany dzienniki diagnostyczne i dane telemetryczne zebrane w obszarze roboczym analizy dzienników. Analiza dzienników może zbierać dane telemetryczne z wielu usług i służyć do wykonywania zapytań i Ustaw alerty.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Pobierz skrypty aplikacji Wingtip biletów SaaS bazy danych dla dzierżawcy

Skrypty Wingtip biletów SaaS wielodostępne w bazie danych i kodu źródłowego aplikacji są dostępne w [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) repozytorium GitHub. Zapoznaj się z [ogólne wskazówki](saas-tenancy-wingtip-app-guidance-tips.md) dla czynności, aby pobrać i odblokować skrypty Wingtip biletów SaaS.

## <a name="provision-additional-tenants"></a>Aprowizacja dodatkowych dzierżaw

Pule stają się opłacalne nawet w przypadku zaledwie dwóch baz danych nas poziomie S3, a związane z nimi oszczędności wzrastają wraz ze zwiększeniem liczby baz danych w puli, co jest spowodowane efektem uśredniania. Aby dobrze zrozumieć działanie monitorowania wydajności i zarządzania nią w dużej skali, w tym samouczku zalecamy wdrożenie co najmniej 20 baz danych.

Jeśli partii dzierżawców jest już przydzielona w poprzednich instrukcji, przejdź do [Symulacja użycie na wszystkich baz danych dzierżawy](#simulate-usage-on-all-tenant-databases) sekcji.

1. W **PowerShell ISE**, Otwórz... \\Modułów szkoleniowych\\zarządzania i monitorowania wydajności\\*PerformanceMonitoringAndManagement.ps1 pokaz*. Nie zamykaj tego skryptu, gdyż w ramach tego samouczka będzie konieczne uruchomienie kilku scenariuszy.
1. Ustaw zmienną **$DemoScenario** = **1**, **Provision a batch of tenants** (Aprowizacja partii dzierżaw)
1. Naciśnij klawisz **F5**, aby uruchomić skrypt.

Ten skrypt wdroży 17 dzierżaw w czasie krótszym niż pięć minut.

*TenantBatch nowy* skrypt używa zestawu połączonych lub zagnieżdżone [Resource Manager](../azure-resource-manager/index.md) szablonów, które tworzenia partii dzierżawcy, która domyślnie kopiuje bazę danych **basetenantdb**na serwerze katalogu do utworzenia nowej dzierżawy baz danych, następnie rejestruje je w katalogu, a ostatecznie inicjuje je z typem dzierżawy nazwę i miejsce. Jest to zgodne z sposób aplikacja udostępnia nową dzierżawę. Wszelkie zmiany wprowadzone do *basetenantdb* są stosowane do dowolnego nowi dzierżawcy następnie udostępniane. Zobacz [samouczek Zarządzanie schematami](saas-tenancy-schema-management.md) na temat sposobu wprowadzania zmian schematu *istniejących* dzierżawy baz danych (w tym *basetenantdb* bazy danych).

## <a name="simulate-usage-on-all-tenant-databases"></a>Symulowanie użycia we wszystkich baz danych dzierżaw

*PerformanceMonitoringAndManagement.ps1 pokaz* skryptów jest pod warunkiem że symuluje pracą dzierżawy w przypadku wszystkich baz danych. Obciążenie jest generowana z użyciem jednego ze scenariuszy dostępnych obciążenia:

| Demonstracja | Scenariusz |
|:--|:--|
| 2 | Generowanie obciążenia o normalnym natężeniu (ok. 40 jednostek DTU) |
| 3 | Generowanie obciążenia z dłuższymi i częstszymi skokami wartości dla każdej bazy danych|
| 4 | Generowanie obciążenia o wyższych skokach wartości DTU dla każdej bazy danych (ok. 80 jednostek DTU)|
| 5 | Generowanie normalnego obciążenia oraz wysokiego obciążenia dla jednej dzierżawy (ok. 95 jednostek DTU)|
| 6 | Generowanie niezrównoważonego obciążenia dla wielu pul|

Generator obciążenia stosuje obciążenie *syntetyczne* wyłącznie do procesorów dla każdej bazy danych dzierżawy. Generator uruchamia zadanie dla każdej bazy danych dzierżawy, co powoduje cykliczne wywołanie procedury składowanej, która generuje obciążenie. Poziomy obciążenia (mierzone w jednostkach eDTU), czas trwania i interwały są zróżnicowane dla wszystkich baz danych, co symuluje nieprzewidywalną aktywność dzierżawy.

1. W **PowerShell ISE**, Otwórz... \\Modułów szkoleniowych\\zarządzania i monitorowania wydajności\\*PerformanceMonitoringAndManagement.ps1 pokaz*. Nie zamykaj tego skryptu, gdyż w ramach tego samouczka będzie konieczne uruchomienie kilku scenariuszy.
1. Ustaw **$DemoScenario** = **2**, *Generuj normalnym natężeniu obciążenia*.
1. Naciśnij klawisz **F5**, aby zastosować obciążenie do wszystkich baz danych dzierżaw.

Wingtip biletów SaaS bazy danych dla dzierżawy jest aplikacji SaaS i rzeczywistych obciążenie aplikacji SaaS jest zazwyczaj sporadyczne i nieprzewidywalny. Aby to zasymulować, generator w sposób losowy obciąża wszystkie dzierżawy. Kilka minut są wymagane dla wzorca obciążenia się, więc uruchomienie generatora obciążenia 3 – 5 minut przed podjęciem próby wykonania do monitorowania obciążenia w poniższych sekcjach.

> [!IMPORTANT]
> Generator obciążenia działa jako seria zadań w lokalnej sesji programu PowerShell. Karta *Demo-PerformanceMonitoringAndManagement.ps1* musi pozostać otwarta! Zamknięcie karty lub wstrzymanie pracy komputera spowoduje zatrzymanie generatora obciążenia. Generator obciążenia pozostaje w *wywoływania zadania* stanu, w którym generuje obciążenie nowi dzierżawcy, które są udostępniane po rozpoczęciu generatora. Użyj *Ctrl-C* powoduje zatrzymanie wywoływania nowe zadania i zamknięcie skryptu. Generator obciążenia będzie nadal działać, ale tylko na istniejący dzierżawcy.

## <a name="monitor-resource-usage-using-the-azure-portal"></a>Monitorowanie użycia zasobów przy użyciu portalu Azure

Aby monitorować użycie zasobów, będącą wynikiem obciążenia są stosowane, Otwórz portal do puli zawierającej baz danych dzierżawy:

1. Otwórz [portalu Azure](https://portal.azure.com) i przejdź do *tenants1-dpt -&lt;użytkownika&gt;*  serwera.
1. Przewiń w dół, zlokalizuj elastyczne pule i kliknij pozycję **Pool1**. Ta pula zawiera wszystkie bazy danych dzierżaw utworzone dotychczas.

Obserwować **monitorowania puli elastycznej** i **elastycznej bazy danych monitorowania** wykresów.

Użycie zasobów w puli jest użycie agregacji bazy danych dla wszystkich baz danych w puli. Wykres bazy danych zawiera pięć najnowszych baz danych:

![Baza danych wykresu](./media/saas-dbpertenant-performance-monitoring/pool1.png)

Ponieważ w puli powyżej najwyższego pięć dodatkowych baz danych, użycie puli zawiera działanie, które nie zostaną uwzględnione w górnym wykresu pięć baz danych. Aby uzyskać więcej informacji, kliknij przycisk **wykorzystania zasobów bazy danych**:

![wykorzystanie zasobów bazy danych](./media/saas-dbpertenant-performance-monitoring/database-utilization.png)


## <a name="set-performance-alerts-on-the-pool"></a>Ustawianie alertów wydajności dla puli

Ustaw alert w puli, który wyzwala na \>75% wykorzystania w następujący sposób:

1. Otwórz *Pool1* (na *tenants1-dpt -\<użytkownika\>*  serwera) w [portalu Azure](https://portal.azure.com).
1. Kliknij pozycję **Reguły alertów**, a następnie kliknij przycisk **+ Dodaj alert**:

   ![dodawanie alertu](media/saas-dbpertenant-performance-monitoring/add-alert.png)

1. Podaj nazwę, taką jak **Wysoki poziom jednostek DTU**,
1. Ustaw następujące wartości:
   * **Metryka = Procent eDTU**
   * **Warunek = większa niż**
   * **Próg = 75**
   * **Okres = w ciągu ostatnich 30 minut**
1. Dodaj adres e-mail do *email(s) dodatkowe administratora* polu i kliknij przycisk **OK**.

   ![ustawianie alertu](media/saas-dbpertenant-performance-monitoring/alert-rule.png)


## <a name="scale-up-a-busy-pool"></a>Skalowanie mocno obciążonej puli w górę

Jeśli poziom zagregowanego obciążenia puli zwiększy się do poziomu maksymalnych możliwości puli i osiągnie 100 % wykorzystania jednostek eDTU, będzie to miało wpływ na wydajność poszczególnych baz danych, co potencjalnie może wydłużyć czasy odpowiedzi na zapytania dla wszystkich baz danych w puli.

**Krótkoterminowe**, należy wziąć pod uwagę skalowaniu pulę, aby zapewnić dodatkowe zasoby lub usunięcia baz danych z puli (przeniesienie ich do innej puli lub z puli do warstwy usług autonomicznej).

**Dłuższym okresie**, należy wziąć pod uwagę optymalizacji zapytań lub indeksu użycia w celu poprawy wydajności bazy danych. W zależności od wrażliwości aplikacji na problemy wydajnościowe najlepszym rozwiązaniem jest skalowanie puli w górę przed osiągnięciem przez nią wykorzystania jednostek eDTU równego 100%. Użyj alertów do wczesnego ostrzegania.

Zajętość puli można zasymulować, zwiększając obciążenia wytwarzane przez generator. Powoduje baz danych do serii, częściej, a także zwiększenia łącznej obciążenia w puli bez zmiany wymagań dotyczących poszczególnych baz danych. Skalowanie puli w górę można łatwo przeprowadzić w portalu lub z programu PowerShell. W tym ćwiczeniu użyto portalu.

1. Ustaw wartość zmiennej *$DemoScenario* = **3**, _Generate load with longer and more frequent bursts per database_ (Generowanie obciążenia z dłuższymi i częstszymi skokami wartości dla każdej bazy danych) w celu zwiększenia intensywności zagregowanego obciążenia w puli bez zmiany szczytowego obciążenia wymaganego dla każdej bazy danych.
1. Naciśnij klawisz **F5**, aby zastosować obciążenie do wszystkich baz danych dzierżaw.

1. Przejdź do **Pool1** w portalu Azure.

Monitoruj użycie w jednostkach eDTU puli zwiększona na wykresie górnej. Zajmuje kilka minut na nowe obciążenia wyższej zaczną działać, ale powinny pojawić szybko rozpocząć osiągnęła maksymalny wykorzystania puli, a jako steadies obciążenia na nowy wzorzec, szybko overloads puli.

1. Aby skalować do puli, kliknij przycisk **Konfigurowanie puli** w górnej części **Pool1** strony.
1. Dostosuj **eDTU puli** ustawienie **100**. Zmiana liczby jednostek eDTU puli nie zmienia ustawienia poszczególnych baz danych (które nadal ma maksymalną wartość 50 jednostek eDTU na bazę danych). Można wyświetlić ustawienia na bazę danych po prawej stronie **Konfigurowanie puli** strony.
1. Kliknij przycisk **zapisać** Aby przesłać żądanie skalowania puli.

Wróć do **Pool1** > **omówienie** Aby przeglądać wykres monitorowania. Monitorowanie efektu działania zapewnia więcej zasobów do puli (chociaż z kilku baz danych i losowego obciążenia nie zawsze jest łatwo sprawdzić ostatecznie, dopóki nie zostanie uruchomione przez pewien czas). Patrząc na wykresy należy sobie zdawać sprawę, że 100% na górnym wykresie odpowiada teraz 100 jednostkom eDTU, podczas gdy na dolnym wykresie 100% nadal odpowiada 50 jednostkom eDTU, gdyż maksymalna wartość dla pojedynczej bazy danych nadal wynosi 50 jednostek eDTU.

Bazy danych pozostają w trybie online i są w pełni dostępne podczas całego procesu. W ostatniej chwili przed włączeniem dla każdej bazy danych nowej wartości eDTU dla puli wszystkie aktywne połączenia zostają przerwane. Kod aplikacji powinien być napisany w taki sposób, aby zawsze próbować ponownie nawiązać przerwane połączenie, by móc ponownie połączyć się z bazą danych w puli przeskalowanej w górę.

## <a name="load-balance-between-pools"></a>Równoważenie obciążenia między zestawami

Alternatywnym rozwiązaniem w stosunku do skalowania puli w górę jest utworzenie drugiej puli i przeniesienie do niej baz danych w celu zrównoważenia obciążenia między dwoma pulami. Aby to rozwiązanie zadziałało, nowa pula musi zostać utworzona na tym samym serwerze co pierwsza.

1. W [portalu Azure](https://portal.azure.com), otwórz **tenants1-dpt -&lt;użytkownika&gt;**  serwera.
1. Kliknij przycisk **+ nowej puli** Aby utworzyć pulę na bieżącym serwerze.
1. Na **elastycznej puli baz danych** szablonu:

    1. Ustaw **nazwa** do *Pool2*.
    1. Pozostaw warstwę cenową **Pula Standardowa**.
    1. Kliknij przycisk **Konfiguruj pulę**,
    1. Ustaw **eDTU puli** do *50 eDTU*.
    1. Kliknij przycisk **dodać bazy danych** umożliwia wyświetlenie listy baz danych na serwerze, który można dodać do *Pool2*.
    1. Zaznacz wszystkie 10 bazy danych, aby przenieść je do nowej puli, a następnie kliknij przycisk **wybierz**. Jeśli działała generator obciążenia, usługa już wie, że Twój profil wydajności wymaga puli większych niż 50 eDTU domyślny rozmiar i zaleca, począwszy od 100 ustawienia liczby jednostek eDTU.

    ![zalecenie](media/saas-dbpertenant-performance-monitoring/configure-pool.png)

    1. W tym samouczku, pozostaw wartość domyślną w Edtu 50, a następnie kliknij przycisk **wybierz** ponownie.
    1. Wybierz **OK** Utwórz nową pulę i przejść do niej wybranych baz danych.

Tworzenie puli i przenoszenie bazy danych zajmuje kilka minut. Jak pozostają online i jest całkowicie dostępny do momentu ostatnio przenoszeniu baz danych, w którym wszystkie otwarte połączenia są zamykane. Tak długo, jak masz logikę ponawiania klienci będą następnie połączenia z bazą danych w nowej puli.

Przejdź do **Pool2** (na *tenants1-dpt -\<użytkownika\>*  serwera) do otwierania puli i monitorować jego wydajność. Jeśli nie widzisz, poczekaj, aż Inicjowanie obsługi administracyjnej nowej puli, aby zakończyć.

Pojawi się że użycie zasobów na *Pool1* spadła oraz że *Pool2* podobnie jest załadowane.

## <a name="manage-performance-of-a-single-database"></a>Zarządzanie wydajności pojedynczej bazy danych

Jeśli stałe zwiększone obciążenie dotyczy pojedynczej bazy danych w puli, to w zależności od konfiguracji puli może ona zdominować zasoby w puli i wpływać na pozostałe bazy danych. Działanie będzie prawdopodobnie będą kontynuowane przez pewien czas, bazy danych można tymczasowo przeniesiony poza puli. Dzięki temu bazy danych ma dodatkowe zasoby musi i izoluje go od innych baz danych.

W tym ćwiczeniu zostanie zasymulowane zwiększone obciążenie dotyczące miejsca Contoso Concert Hall wywołane wzmożonym pobytem na bilety na popularny koncert.

1. W **PowerShell ISE**, Otwórz... \\ *PerformanceMonitoringAndManagement.ps1 pokaz* skryptu.
1. Ustaw wartość zmiennej **$DemoScenario = 5, Generate a normal load plus a high load on a single tenant (approx. 95 DTU)** (Generowanie normalnego obciążenia oraz wysokiego obciążenia dla jednej dzierżawy (ok. 95 jednostek DTU)).
1. Ustaw wartość zmiennej **$SingleTenantDatabaseName = contosoconcerthall**
1. Wykonaj skrypt, używając klawisza **F5**.


1. W [portalu Azure](https://portal.azure.com), przejdź do listy baz danych na *tenants1-dpt -\<użytkownika\>*  serwera. 
1. Polecenie **contosoconcerthall** bazy danych.
1. Kliknij w puli który **contosoconcerthall** w. Zlokalizuj puli w **elastycznej puli baz danych** sekcji.

1. Sprawdź **monitorowania puli elastycznej** wykresu i poszukaj użycie w jednostkach eDTU puli zwiększona. Po minucie lub dwóch powinno być widoczne wyższe obciążenie, które wkrótce osiągnie poziom 100% wykorzystania puli.
2. Sprawdź **elastycznej bazy danych monitorowania** wyświetlić, który wskazuje najnowszych baz danych w ciągu ostatnich godzin. *Contosoconcerthall* bazy danych wkrótce powinny się wyświetlać jako jednej z pięciu najnowszych baz danych.
3. **Kliknij pozycję Monitorowanie elastycznej bazy danych** **wykresu** i otwiera **wykorzystania zasobów bazy danych** strony, których można monitorować żadnego z bazy danych. W ten sposób można odizolować wyświetlania dla *contosoconcerthall* bazy danych.
4. Z listy baz danych, kliknij przycisk **contosoconcerthall**.
5. Kliknij przycisk **warstwy cenowej (skala Dtu)** otworzyć **skonfigurować wydajności** strony, w którym można ustawić poziomu wydajności autonomicznej bazy danych.
6. Kliknij kartę **Standardowa**, aby otworzyć opcje skalowania w warstwie Standardowa.
7. Slajd **jednostek dtu w warstwie suwaka** prawo, aby wybrać **100** Dtu. Uwaga odpowiada cel usługi **S3**.
8. Kliknij przycisk **Zastosuj** Aby przenieść bazę danych z puli i zapewnić ich *standardowa S3* bazy danych.
9. Po zakończeniu skalowanie monitorowanie wpływu na bazie contosoconcerthall i Pool1 na elastyczne bloków puli i bazy danych.

Po zaspokojenie wysokie obciążenie bazy danych contosoconcerthall należy niezwłocznie powraca do puli, aby zmniejszyć koszt. Jeśli nie jest jasne podczas którego nastąpi można ustawić alert w bazie danych który zostanie wyzwolone w momencie jego użycie jednostek DTU spadnie poniżej na bazę danych w puli max. Przenoszenie bazy danych do puli opisano w ćwiczeniu 5.

## <a name="other-performance-management-patterns"></a>Innymi wzorami zarządzania wydajności

**Skalowanie uprzedzające** w wykonywaniu powyżej której zostały przedstawione jak skalować izolowanej bazy danych, wiedziały bazę danych, która ma zostać wyszukane. Jeśli zarządzanie Hall porozumieniu Contoso poinformowała Wingtips o zbliżającym się sprzedaży biletów, bazy danych może zostały usunięte z puli pre-emptively. W przeciwnym razie konieczne byłoby skonfigurowanie alertów dla puli lub bazy danych w celu informowania o zmieniającej się sytuacji. Byłoby wysoce nieprzyjemne, gdyby wiadomość o zdarzeniu pochodziła od innych dzierżawców w puli, skarżących się na pogorszenie wydajności. Jeśli jednak dzierżawca może przewidzieć, jak długo będą mu potrzebne dodatkowe zasoby, możesz tak skonfigurować element Runbook usługi Azure Automation, aby przenieść bazę danych z puli, a następnie z powrotem do puli zgodnie z ustalonym harmonogramem.

**Samoobsługowe skalowanie przez dzierżawcę** Ponieważ skalowanie jest zadaniem łatwo wywoływanym za pośrednictwem interfejsu API zarządzania, można w prosty sposób wbudować możliwość skalowania baz danych dzierżawy w aplikację używaną przez dzierżawcę i zaoferować ją jako funkcję usługi SaaS. Na przykład, można umożliwić dzierżawcom samodzielne administrowanie skalowaniem w górę i w dół, być może bezpośrednio powiązane z ich rozliczeniami!

**Skalowanie puli w górę i w dół zgodnie z harmonogramem, aby dopasować wzorców użycia**

W przypadku, gdy zagregowane wykorzystanie dzierżaw jest zgodne z przewidywalnymi wzorcami użycia można użyć usługi Azure Automation do skalowania puli w górę i w dół zgodnie z harmonogramem. Na przykład pulę można skalować w dół po godzinie 18:00 i ponownie w górę przed godziną 6:00 w dni robocze, jeśli wiadomo, że pomiędzy tymi godzinami występuje spadek wymagań dotyczących zasobów.



## <a name="next-steps"></a>Kolejne kroki

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Symulowanie korzystania z baz danych dzierżaw przy użyciu dostarczonego generatora obciążenia
> * Monitorowanie reakcji baz danych dzierżaw na wzrost obciążenia
> * Skalowanie w górę elastycznej puli w reakcji na zwiększone obciążenie bazy danych
> * Aprowizacja drugiej elastycznej puli w celu równoważenia aktywności baz danych

[Samouczek przywracania pojedynczej dzierżawy](saas-dbpertenant-restore-single-tenant.md)


## <a name="additional-resources"></a>Zasoby dodatkowe

* Dodatkowe [samouczki, które zależą od Wingtip biletów SaaS bazy danych dla dzierżawy wdrożenia aplikacji](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Pule elastyczne SQL](sql-database-elastic-pool.md)
* [Azure Automation](../automation/automation-intro.md)
* [Log Analytics](saas-dbpertenant-log-analytics.md) — samouczek konfigurowania usługi Log Analytics i korzystania z niej
