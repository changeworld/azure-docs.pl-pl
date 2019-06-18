---
title: 'Aplikacja Saas: Monitorowanie wydajności wielu baz danych Azure SQL | Dokumentacja firmy Microsoft'
description: Monitorowanie i zarządzanie wydajnością bazy danych Azure SQL i pul baz danych w aplikacji SaaS z wieloma dzierżawami
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 075d0e2471457e1a585f7fdea9b523b1d13499c7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61388646"
---
# <a name="monitor-and-manage-performance-of-azure-sql-databases-and-pools-in-a-multi-tenant-saas-app"></a>Monitorowanie i zarządzanie wydajnością bazy danych Azure SQL i pul baz danych w aplikacji SaaS z wieloma dzierżawami

W tym samouczku zbadano kilka podstawowych scenariuszy zarządzania wydajnością używanych w aplikacjach SaaS. Przy użyciu generator obciążenia, aby symulować działanie we wszystkich bazach danych dzierżaw, wbudowane funkcje monitorowania i alertów bazy danych SQL i pule elastyczne są przedstawione w artykule.

Aplikacji Wingtip Tickets SaaS bazy danych dla dzierżawy używa modelu danych do pojedynczej dzierżawy, którym każde miejsce (dzierżawca) ma swoją własną bazę danych. Podobnie jak w przypadku wielu innych aplikacji SaaS, oczekiwany wzorzec obciążenia dzierżawy charakteryzuje się nieprzewidywalnością i sporadycznością występowania. Innymi słowy, sprzedaż biletów może nastąpić w dowolnej chwili. Aby móc korzystać z tego wzorca użycia typowych bazy danych, baz danych dzierżaw są wdrażane do pul elastycznych. Elastyczne pule umożliwiają optymalizację kosztu rozwiązania dzięki udostępnieniu zasobów pomiędzy wieloma bazami danych. W przypadku tego typu wzorca ważne jest, aby monitorować użycie zasobów bazy danych i puli, co ma na celu rozsądne równoważenie obciążenia między pulami. Należy także upewnić się, że pojedyncze bazy danych posiadają odpowiednie zasoby, i że pule nie zbliżają się do swoich limitów liczby jednostek [eDTU](sql-database-purchase-models.md#dtu-based-purchasing-model). W tym samouczku przedstawiono metody monitorowania baz danych i pul oraz zarządzania nimi, a także wykonywanie akcji naprawczych w odpowiedzi na wahania obciążenia.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> 
> * Symulowanie korzystania z baz danych dzierżaw przy użyciu dostarczonego generatora obciążenia
> * Monitorowanie reakcji baz danych dzierżaw na wzrost obciążenia
> * Skalowanie w górę elastycznej puli w reakcji na zwiększone obciążenie bazy danych
> * Aprowizacja drugiej elastycznej puli w celu równoważenia aktywności baz danych


Do wykonania zadań opisanych w tym samouczku niezbędne jest spełnienie następujących wymagań wstępnych:

* Aplikacja o nazwie Wingtip Tickets SaaS bazy danych dla dzierżawy jest wdrażana. Aby wdrożyć w mniej niż pięć minut, zobacz [wdrażanie i eksplorowanie aplikacji Wingtip Tickets SaaS bazy danych dla dzierżawy](saas-dbpertenant-get-started-deploy.md)
* Zainstalowany jest program Azure PowerShell. Aby uzyskać szczegółowe informacje, zobacz [Rozpoczynanie pracy z programem Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-saas-performance-management-patterns"></a>Wprowadzenie do wzorców zarządzania wydajnością SaaS

Zarządzanie wydajnością bazy danych polega na zbieraniu danych dotyczących wydajności i analizowaniu ich, a następnie reagowaniu na te dane przez dostosowanie parametrów w celu zachowania akceptowalnego czasu odpowiedzi aplikacji. W przypadku hostowania wielu dzierżaw, pule elastyczne są ekonomiczny sposób zapewnienia zasobów i zarządzanie nimi dla grupy baz danych obciążanych w nieprzewidywalny. Przy pewnych wzorcach obciążenia korzyści mogą się pojawić przy zarządzaniu w puli zaledwie dwoma bazami danych S3.

![Diagram aplikacji](./media/saas-dbpertenant-performance-monitoring/app-diagram.png)

Pul i baz danych w pulach, należy monitorować, aby upewnić się, że te komputery pozostaną w dopuszczalnym zakresie wydajności. Dostosuj konfigurację puli do potrzeb zagregowanego obciążenia wszystkich baz danych, zapewnienie, że jednostki Edtu puli odpowiednią dla całkowitego obciążenia. Dostosuj minimalne i maksymalne wartości eDTU dla bazy danych do wartości odpowiadających wymaganiom konkretnych aplikacji.

### <a name="performance-management-strategies"></a>Strategie zarządzania wydajnością

* Aby uniknąć konieczności ręcznego monitorowania wydajności, jest najbardziej efektywne, aby **ustawić alerty, które mogą powodować podczas bazy danych lub pule wyjdą poza przewidziane zakresy**.
* Aby odpowiedzieć na krótkoterminowe wahania zagregowanego obliczeń rozmiaru puli, **poziomu jednostek eDTU puli mogą być skalowane w górę lub w dół**. Jeśli takie wahania występują regularnie lub są przewidywalne, **można zaplanować automatyczne skalowanie puli**. Na przykład skalowanie w dół może nastąpić, kiedy przewidywane jest niskie obciążenie — w nocy lub podczas weekendów.
* Odpowiedzią na wahania długoterminowe lub zmiany liczby baz danych jest **przeniesienie pojedynczych baz danych do innych pul**.
* Aby odpowiedzieć na krótkoterminowe zwiększenie *poszczególnych* obciążenie bazy danych **poszczególnych baz danych można wyjąć z puli i przypisane rozmiar poszczególnych obliczeń**. Po zmniejszeniu obciążenia można zwrócić bazę danych do puli. Gdy jest to znane z wyprzedzeniem, baz danych można przenosić uprzedzając zdarzenie upewnij się, że baza danych zawsze ma zasoby, których potrzebuje i uniknąć wpływu na innych baz danych w puli. Jeśli takie wymaganie jest przewidywalne, na przykład w przypadku oczekiwania na wzmożone zakupy biletów na popularną imprezę, wówczas takie działanie funkcji zarządzania można uwzględnić w aplikacji.

Witryna [Azure Portal](https://portal.azure.com) udostępnia wbudowane funkcje monitorowania i alertów dla większości zasobów. W usłudze SQL Database funkcje monitorowania i zgłaszania alertów są dostępne na poziomie baz danych i pul. To wbudowane funkcje monitorowania i alertów zależy od konkretnych zasobów, dlatego jest łatwa w użyciu dla małej liczby zasobów, ale przestaje być wygodna przy pracy z wieloma zasobami.

Dla dużej liczby scenariuszy, w którym pracujesz z wieloma zasobami, [dzienniki usługi Azure Monitor](saas-dbpertenant-log-analytics.md) mogą być używane. Jest to osobna usługa Azure oferuje analytics za pośrednictwem emitowany dzienników diagnostycznych i danych telemetrycznych zebranych w obszarze roboczym usługi Log Analytics. Dzienniki platformy Azure Monitor umożliwia zbieranie danych telemetrycznych z wielu usług i służyć do tworzenia zapytań i ustawiania alertów.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Pobieranie skryptów aplikacji Wingtip Tickets SaaS bazy danych dla dzierżawy

Skryptów aplikacji Wingtip Tickets SaaS wielodostępnej w bazie danych i kod źródłowy aplikacji są dostępne w [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) repozytorium GitHub. Zapoznaj się z [ogólne wskazówki dotyczące](saas-tenancy-wingtip-app-guidance-tips.md) kroków pobrać i odblokować skrypty SaaS o nazwie Wingtip Tickets.

## <a name="provision-additional-tenants"></a>Aprowizacja dodatkowych dzierżaw

Pule stają się opłacalne nawet w przypadku zaledwie dwóch baz danych nas poziomie S3, a związane z nimi oszczędności wzrastają wraz ze zwiększeniem liczby baz danych w puli, co jest spowodowane efektem uśredniania. Aby dobrze zrozumieć działanie monitorowania wydajności i zarządzania nią w dużej skali, w tym samouczku zalecamy wdrożenie co najmniej 20 baz danych.

Jeśli w pracy z poprzednim samouczkiem aprowizowano już partię dzierżaw, przejdź do [symulowanie użycia we wszystkich bazach danych dzierżaw](#simulate-usage-on-all-tenant-databases) sekcji.

1. W **PowerShell ISE**, Otwórz... \\Learning Modules\\zarządzania i monitorowania wydajności\\*Demo-PerformanceMonitoringAndManagement.ps1*. Nie zamykaj tego skryptu, gdyż w ramach tego samouczka będzie konieczne uruchomienie kilku scenariuszy.
1. Ustaw zmienną **$DemoScenario** = **1**, **Provision a batch of tenants** (Aprowizacja partii dzierżaw)
1. Naciśnij klawisz **F5**, aby uruchomić skrypt.

Ten skrypt wdroży 17 dzierżaw w czasie krótszym niż pięć minut.

*New-TenantBatch* skrypt używa zestawu połączonych lub zagnieżdżonych [usługi Resource Manager](../azure-resource-manager/index.yml) szablonów, Utwórz partię dzierżaw, domyślnie kopiujących bazę danych, które **basetenantdb**na serwerze wykazu, aby utworzyć nową dzierżawę bazy danych, a następnie rejestruje je w katalogu, a na koniec inicjuje je przy użyciu dzierżawy nazwy i typu miejsca. Jest to zgodne ze sposobem jej aprowizacji nowej dzierżawy. Wszelkie zmiany wprowadzone do *basetenantdb* są stosowane do nowych dzierżawach aprowizowane po tej dacie. Zobacz [Samouczek zarządzania schematami](saas-tenancy-schema-management.md) można zobaczyć, jak wykonywać zmiany w schematach *istniejących* baz danych dzierżaw (w tym *basetenantdb* bazy danych).

## <a name="simulate-usage-on-all-tenant-databases"></a>Symulowanie użycia we wszystkich baz danych dzierżaw

*Demo-PerformanceMonitoringAndManagement.ps1* skrypt ma pod warunkiem że symuluje obciążenia wszystkich baz danych dzierżaw. Obciążenie jest generowana z użyciem jednego ze scenariuszy dostępne obciążenia:

| Demonstracja | Scenariusz |
|:--|:--|
| 2 | Generowanie obciążenia o normalnym natężeniu (około 40 jednostek DTU) |
| 3 | Generowanie obciążenia z dłuższymi i częstszymi skokami wartości dla każdej bazy danych|
| 4 | Generowanie obciążenia o wyższych wzrosty liczby jednostek DTU na bazę danych (około 80 jednostek DTU)|
| 5 | Generowanie normalnego obciążenia oraz wysokiego obciążenia dla jednej dzierżawy (około 95 jednostek DTU)|
| 6 | Generowanie niezrównoważonego obciążenia dla wielu pul|

Generator obciążenia stosuje obciążenie *syntetyczne* wyłącznie do procesorów dla każdej bazy danych dzierżawy. Generator uruchamia zadanie dla każdej bazy danych dzierżawy, co powoduje cykliczne wywołanie procedury składowanej, która generuje obciążenie. Poziomy obciążenia (mierzone w jednostkach eDTU), czas trwania i interwały są zróżnicowane dla wszystkich baz danych, co symuluje nieprzewidywalną aktywność dzierżawy.

1. W **PowerShell ISE**, Otwórz... \\Learning Modules\\zarządzania i monitorowania wydajności\\*Demo-PerformanceMonitoringAndManagement.ps1*. Nie zamykaj tego skryptu, gdyż w ramach tego samouczka będzie konieczne uruchomienie kilku scenariuszy.
1. Ustaw **$DemoScenario** = **2**, *obciążenia o normalnym natężeniu Generuj*.
1. Naciśnij klawisz **F5**, aby zastosować obciążenie do wszystkich baz danych dzierżaw.

O nazwie Wingtip Tickets SaaS bazy danych dla dzierżawy jest aplikacją SaaS, a rzeczywiste obciążenie dla aplikacji SaaS jest zazwyczaj sporadyczne i nieprzewidywalne. Aby to zasymulować, generator w sposób losowy obciąża wszystkie dzierżawy. Kilka minut, zanim są wymagane dla wzorca obciążenia się, więc uruchomienie generatora obciążenia 3 – 5 minut przed próbą monitorowania obciążenia w poniższych sekcjach.

> [!IMPORTANT]
> Generator obciążenia działa jako seria zadań w lokalnej sesji programu PowerShell. Karta *Demo-PerformanceMonitoringAndManagement.ps1* musi pozostać otwarta! Zamknięcie karty lub wstrzymanie pracy komputera spowoduje zatrzymanie generatora obciążenia. Generator obciążenia pozostają w *wywoływanie zadania* stanu, w którym generuje obciążenie na nowych dzierżaw, które są udostępniane po uruchomieniu generator. Użyj *Ctrl-C* Aby zatrzymać wywoływania nowe zadania i zamknąć okno skryptu. Generator obciążenia będą nadal uruchomione, ale tylko w przypadku istniejących dzierżaw.

## <a name="monitor-resource-usage-using-the-azure-portal"></a>Monitorowanie wykorzystania zasobów przy użyciu witryny Azure portal

Aby monitorować użycie zasobów wynikające z zastosowanego obciążenia, należy otworzyć portal dla puli zawierającej bazy danych dzierżaw:

1. Otwórz [witryny Azure portal](https://portal.azure.com) i przejdź do *tenants1-dpt -&lt;użytkownika&gt;*  serwera.
1. Przewiń w dół, zlokalizuj elastyczne pule i kliknij pozycję **Pool1**. Ta pula zawiera wszystkie bazy danych dzierżaw utworzone dotychczas.

Obserwuj **monitorowanie pul elastycznych** i **monitorowanie elastycznych baz danych** wykresów.

Wykorzystanie zasobów w puli to wykorzystania bazy danych agregacji dla wszystkich baz danych w puli. Wykres bazy danych przedstawia pięć najbardziej aktywnych baz danych:

![Baza danych wykresu](./media/saas-dbpertenant-performance-monitoring/pool1.png)

Ponieważ istnieją dodatkowe bazy danych w puli oprócz pięć najważniejszych, Wykres wykorzystania puli pokazuje działanie, która nie jest uwzględniona w górnym wykresie pięciu baz danych. Aby uzyskać więcej informacji, kliknij przycisk **wykorzystanie zasobów bazy danych**:

![wykorzystanie zasobów bazy danych](./media/saas-dbpertenant-performance-monitoring/database-utilization.png)


## <a name="set-performance-alerts-on-the-pool"></a>Ustawianie alertów wydajności dla puli

Ustawianie alertu na pulę, który jest wyzwalany przy \>75% wykorzystania w następujący sposób:

1. Otwórz *Pool1* (na *tenants1-dpt -\<użytkownika\>*  serwera) w [witryny Azure portal](https://portal.azure.com).
1. Kliknij pozycję **Reguły alertów**, a następnie kliknij przycisk **+ Dodaj alert**:

   ![dodawanie alertu](media/saas-dbpertenant-performance-monitoring/add-alert.png)

1. Podaj nazwę, taką jak **Wysoki poziom jednostek DTU**,
1. Ustaw następujące wartości:
   * **Metryka = Procent eDTU**
   * **Warunek = większa niż**
   * **Próg = 75**
   * **Okres = w ciągu ostatnich 30 minut**
1. Dodaj adres e-mail do *email(s) dodatkowego administratora* pole, a następnie kliknij przycisk **OK**.

   ![ustawianie alertu](media/saas-dbpertenant-performance-monitoring/alert-rule.png)


## <a name="scale-up-a-busy-pool"></a>Skalowanie mocno obciążonej puli w górę

Jeśli poziom zagregowanego obciążenia puli zwiększy się do poziomu maksymalnych możliwości puli i osiągnie 100 % wykorzystania jednostek eDTU, będzie to miało wpływ na wydajność poszczególnych baz danych, co potencjalnie może wydłużyć czasy odpowiedzi na zapytania dla wszystkich baz danych w puli.

**Krótkoterminowe**, należy rozważyć skalowanie w górę puli w celu zapewnienia jej dodatkowych zasobów lub usunięcie baz danych z puli (przeniesienie ich do innych pul lub z puli do warstwy usług autonomicznych).

**Dłuższy okres**, należy wziąć pod uwagę Optymalizacja zapytań lub użycie indeksu w celu poprawy wydajności bazy danych. W zależności od wrażliwości aplikacji na problemy wydajnościowe najlepszym rozwiązaniem jest skalowanie puli w górę przed osiągnięciem przez nią wykorzystania jednostek eDTU równego 100%. Użyj alertów do wczesnego ostrzegania.

Zajętość puli można zasymulować, zwiększając obciążenia wytwarzane przez generator. Porzucanie bazy danych szybciej częściej, a także już, zwiększa zagregowane obciążenie puli, nie wpływając na wymagania poszczególnych baz danych. Skalowanie puli w górę można łatwo przeprowadzić w portalu lub z programu PowerShell. W tym ćwiczeniu użyto portalu.

1. Ustaw wartość zmiennej *$DemoScenario* = **3**, _Generate load with longer and more frequent bursts per database_ (Generowanie obciążenia z dłuższymi i częstszymi skokami wartości dla każdej bazy danych) w celu zwiększenia intensywności zagregowanego obciążenia w puli bez zmiany szczytowego obciążenia wymaganego dla każdej bazy danych.
1. Naciśnij klawisz **F5**, aby zastosować obciążenie do wszystkich baz danych dzierżaw.

1. Przejdź do **Pool1** w witrynie Azure portal.

Monitoruj użycie jednostek eDTU puli o zwiększonym na górnym wykresie. Zajmuje kilka minut, zanim nowe wyższe obciążenie, ale szybko zauważysz puli trafić maksymalnego wykorzystania, a jako obciążenie zbliżanie do nowego wzorca, szybkie przeciążenie puli.

1. Aby przeskalować pulę w górę, kliknij przycisk **skonfigurować pulę** w górnej części **Pool1** strony.
1. Dostosuj **pula — eDTU** ustawienie **100**. Zmiana liczby jednostek eDTU puli nie zmienia ustawienia poszczególnych baz danych (które nadal ma maksymalną wartość 50 jednostek eDTU na bazę danych). Można wyświetlić ustawienia poszczególnych baz danych na prawej krawędzi **skonfigurować pulę** strony.
1. Kliknij przycisk **Zapisz** można przesłać żądania do skalowania puli.

Wróć do **Pool1** > **Przegląd** Aby wyświetlić wykresy monitorowania. Monitorowanie efektu działania zapewniania większej ilości zasobów puli (jednak z kilku baz danych i losowego obciążenia nie zawsze jest łatwo go dostrzec dopóki nie zostanie uruchomione przez pewien czas). Patrząc na wykresy należy sobie zdawać sprawę, że 100% na górnym wykresie odpowiada teraz 100 jednostkom eDTU, podczas gdy na dolnym wykresie 100% nadal odpowiada 50 jednostkom eDTU, gdyż maksymalna wartość dla pojedynczej bazy danych nadal wynosi 50 jednostek eDTU.

Bazy danych pozostają w trybie online i są w pełni dostępne podczas całego procesu. W ostatniej chwili przed włączeniem dla każdej bazy danych nowej wartości eDTU dla puli wszystkie aktywne połączenia zostają przerwane. Kod aplikacji powinien być napisany w taki sposób, aby zawsze próbować ponownie nawiązać przerwane połączenie, by móc ponownie połączyć się z bazą danych w puli przeskalowanej w górę.

## <a name="load-balance-between-pools"></a>Równoważenie obciążenia między pulami

Alternatywnym rozwiązaniem w stosunku do skalowania puli w górę jest utworzenie drugiej puli i przeniesienie do niej baz danych w celu zrównoważenia obciążenia między dwoma pulami. Aby to rozwiązanie zadziałało, nowa pula musi zostać utworzona na tym samym serwerze co pierwsza.

1. W [witryny Azure portal](https://portal.azure.com), otwórz **tenants1-dpt -&lt;użytkownika&gt;**  serwera.
1. Kliknij przycisk **+ nowa pula** Aby utworzyć pulę na bieżącym serwerze.
1. Na **puli elastycznej** szablonu:

   1. Ustaw **nazwa** do *Pool2*.
   1. Pozostaw warstwę cenową **Pula Standardowa**.
   1. Kliknij przycisk **Konfiguruj pulę**,
   1. Ustaw **pula — eDTU** do *50 jednostek eDTU*.
   1. Kliknij przycisk **Dodawanie baz danych** umożliwia wyświetlenie listy baz danych na serwerze, który można dodać do *Pool2*.
   1. Wybierz wszystkie 10 bazy danych, aby przenieść je do nowej puli, a następnie kliknij przycisk **wybierz**. Jeśli został już uruchomiony generator obciążenia, usługa już wie, że Twój profil wydajności wymaga większą pulę niż domyślny rozmiar 50 jednostek eDTU i zaleca się, rozpoczynając od 100 ustawieniem eDTU.

      ![Zalecenie](media/saas-dbpertenant-performance-monitoring/configure-pool.png)

   1. W tym samouczku pozostaw domyślny odpowiada 50 jednostkom Edtu, a następnie kliknij przycisk **wybierz** ponownie.
   1. Wybierz **OK** Aby utworzyć nową pulę i przenieść wybranych baz danych do niego.

Tworzenie puli i przeniesienie baz danych trwa kilka minut. Jako że są one nadal online i jest w pełni dostępna do ostatniej chwili przenoszeniu baz danych, w tym momencie wszystkie otwarte połączenia zostaną zamknięte. Tak długo, jak masz logikę ponawiania, klienci będą następnie łączyć z bazą danych w nowej puli.

Przejdź do **Pool2** (na *tenants1-dpt -\<użytkownika\>*  serwera) do otwierania puli i monitorować jej wydajność. Jeśli nie widzisz, poczekaj, aż Inicjowanie obsługi administracyjnej nowej puli, aby zakończyć.

Teraz widać to użycie zasobów na *Pool1* została obniżona i *Pool2* teraz jest podobnie obciążona.

## <a name="manage-performance-of-an-individual-database"></a>Zarządzanie wydajnością poszczególnych baz danych

Jeśli obciążenie, w zależności od konfiguracji puli poszczególne bazy danych w puli jego zwykle zdominować zasoby w puli i wpływać na inne bazy danych. W przypadku prawdopodobnie przez pewien czas działanie bazy danych można tymczasowo przeniesiony z puli. Dzięki temu bazy danych do dodatkowych zasobów wymaga, a jednocześnie zostanie odizolowana od innych baz danych.

W tym ćwiczeniu zostanie zasymulowane zwiększone obciążenie dotyczące miejsca Contoso Concert Hall wywołane wzmożonym pobytem na bilety na popularny koncert.

1. W **PowerShell ISE**, Otwórz... \\ *Demo-PerformanceMonitoringAndManagement.ps1* skryptu.
1. Ustaw **$DemoScenario = 5, generowanie normalnego obciążenia oraz wysokiego obciążenia dla jednej dzierżawy (około 95 jednostek DTU).**
1. Ustaw wartość zmiennej **$SingleTenantDatabaseName = contosoconcerthall**
1. Wykonaj skrypt, używając klawisza **F5**.


1. W [witryny Azure portal](https://portal.azure.com), przejdź do listy baz danych na *tenants1-dpt -\<użytkownika\>*  serwera. 
1. Kliknij pozycję **contosoconcerthall** bazy danych.
1. Kliknij pulę, **contosoconcerthall** trwa. Znajdź puli w **puli elastycznej** sekcji.

1. Sprawdzanie **monitorowanie pul elastycznych** wykresów i poszukaj puli o zwiększonym użycie jednostek eDTU. Po minucie lub dwóch powinno być widoczne wyższe obciążenie, które wkrótce osiągnie poziom 100% wykorzystania puli.
2. Sprawdzanie **monitorowanie elastycznych baz danych** wyświetlania, które wyświetla najbardziej aktywnych baz danych w ciągu ostatniej godziny. *Contosoconcerthall* bazy danych wkrótce powinna zostać wyświetlona jako jednego z pięciu najbardziej aktywnych baz danych.
3. **Kliknij pozycję Monitorowanie elastycznych baz danych** **wykresu** a zostanie on otworzony **wykorzystanie zasobów bazy danych** strony, w którym można monitorować baz danych. W ten sposób można odizolować wyświetlać wyłącznie informacje dotyczące *contosoconcerthall* bazy danych.
4. Z listy baz danych, kliknij przycisk **contosoconcerthall**.
5. Kliknij przycisk **warstwa cenowa (jednostki Dtu skalowania)** otworzyć **Konfigurowanie wydajności** strony, w którym można ustawić rozmiar obliczeń autonomicznej bazy danych.
6. Kliknij kartę **Standardowa**, aby otworzyć opcje skalowania w warstwie Standardowa.
7. Przesuń **suwak DTU** prawo, aby wybrać **100** jednostek Dtu. Uwaga odpowiada to celowi usługi **S3**.
8. Kliknij przycisk **Zastosuj** przenieść bazę danych z puli i *— warstwa standardowa S3* bazy danych.
9. Po zakończeniu skalowanie, monitorowanie wpływu na bazy danych contosoconcerthall oraz Pool1 na bloki elastycznej puli i bazy danych.

Gdy zmniejszenia wysokie obciążenie bazy danych contosoconcerthall należy niezwłocznie powraca do puli Aby zmniejszyć jego koszt. Jeśli nie wiadomo, kiedy to nastąpi, można ustawić alert w bazie danych, będzie wyzwalany, gdy jego użycie jednostek DTU spadnie poniżej na bazę danych maksymalna w puli. Przenoszenie bazy danych do puli opisano w ćwiczeniu 5.

## <a name="other-performance-management-patterns"></a>Inne wzorce zarządzania wydajnością

**Skalowanie uprzedzające** w ćwiczeniu powyżej gdzie eksplorować sposób skalowania izolowanej bazy danych, wiedzieliśmy bazę danych, która do wyszukiwania. Jeśli zarządzanie firmy Contoso Concert Hall poinformował Wingtips zbliżającego się sprzedaży biletów, bazy danych może zostać zabrana puli uprzedzając zdarzenie. W przeciwnym razie konieczne byłoby skonfigurowanie alertów dla puli lub bazy danych w celu informowania o zmieniającej się sytuacji. Byłoby wysoce nieprzyjemne, gdyby wiadomość o zdarzeniu pochodziła od innych dzierżawców w puli, skarżących się na pogorszenie wydajności. Jeśli jednak dzierżawca może przewidzieć, jak długo będą mu potrzebne dodatkowe zasoby, możesz tak skonfigurować element Runbook usługi Azure Automation, aby przenieść bazę danych z puli, a następnie z powrotem do puli zgodnie z ustalonym harmonogramem.

**Samoobsługowe skalowanie przez dzierżawcę** Ponieważ skalowanie jest zadaniem łatwo wywoływanym za pośrednictwem interfejsu API zarządzania, można w prosty sposób wbudować możliwość skalowania baz danych dzierżawy w aplikację używaną przez dzierżawcę i zaoferować ją jako funkcję usługi SaaS. Na przykład, można umożliwić dzierżawcom samodzielne administrowanie skalowaniem w górę i w dół, być może bezpośrednio powiązane z ich rozliczeniami!

**Skalowanie puli w górę i w dół zgodnie z harmonogramem odpowiadającym wzorcom użycia**

W przypadku, gdy zagregowane wykorzystanie dzierżaw jest zgodne z przewidywalnymi wzorcami użycia można użyć usługi Azure Automation do skalowania puli w górę i w dół zgodnie z harmonogramem. Na przykład pulę można skalować w dół po godzinie 18:00 i ponownie w górę przed godziną 6:00 w dni robocze, jeśli wiadomo, że pomiędzy tymi godzinami występuje spadek wymagań dotyczących zasobów.



## <a name="next-steps"></a>Kolejne kroki

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Symulowanie korzystania z baz danych dzierżaw przy użyciu dostarczonego generatora obciążenia
> * Monitorowanie reakcji baz danych dzierżaw na wzrost obciążenia
> * Skalowanie w górę elastycznej puli w reakcji na zwiększone obciążenie bazy danych
> * Aprowizacja drugiej elastycznej puli w celu równoważenia aktywności baz danych

[Samouczek przywracania pojedynczej dzierżawy](saas-dbpertenant-restore-single-tenant.md)


## <a name="additional-resources"></a>Dodatkowe zasoby

* Dodatkowe [samouczki nawiązujące do wdrożenia aplikacji Wingtip Tickets SaaS bazy danych dla dzierżawy](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Pule elastyczne SQL](sql-database-elastic-pool.md)
* [Azure Automation](../automation/automation-intro.md)
* [Dzienniki platformy Azure Monitor](saas-dbpertenant-log-analytics.md) — ustawienie się i korzystać z funkcji samouczek dzienniki usługi Azure Monitor
