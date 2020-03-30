---
title: 'Aplikacja Saas: monitorowanie wydajności wielu baz danych'
description: Monitorowanie i zarządzanie wydajnością baz danych i pul SQL platformy Azure w aplikacji SaaS z wieloma dzierżawcami
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 34c50795567615637e31446ad3dc51a5e1b355f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214471"
---
# <a name="monitor-and-manage-performance-of-azure-sql-databases-and-pools-in-a-multi-tenant-saas-app"></a>Monitorowanie i zarządzanie wydajnością baz danych i pul SQL platformy Azure w aplikacji SaaS z wieloma dzierżawcami

W tym samouczku zbadano kilka kluczowych scenariuszy zarządzania wydajnością używanych w aplikacjach SaaS. Za pomocą generatora obciążenia do symulacji działania we wszystkich bazach danych dzierżawy, wbudowane funkcje monitorowania i alertów bazy danych SQL i pul elastycznych są demonstrowane.

Aplikacja Wingtip Tickets SaaS Database per Tenant używa modelu danych z jedną dzierżawą, w którym każde miejsce (dzierżawa) ma własną bazę danych. Podobnie jak w przypadku wielu innych aplikacji SaaS, oczekiwany wzorzec obciążenia dzierżawy charakteryzuje się nieprzewidywalnością i sporadycznością występowania. Innymi słowy, sprzedaż biletów może nastąpić w dowolnej chwili. Aby skorzystać z tego wzorca użycia typowej bazy danych, bazy danych dzierżawy są wdrażane w pulach elastycznych. Elastyczne pule umożliwiają optymalizację kosztu rozwiązania dzięki udostępnieniu zasobów pomiędzy wieloma bazami danych. W przypadku tego typu wzorca ważne jest, aby monitorować użycie zasobów bazy danych i puli, co ma na celu rozsądne równoważenie obciążenia między pulami. Należy także upewnić się, że pojedyncze bazy danych posiadają odpowiednie zasoby, i że pule nie zbliżają się do swoich limitów liczby jednostek [eDTU](sql-database-purchase-models.md#dtu-based-purchasing-model). W tym samouczku przedstawiono metody monitorowania baz danych i pul oraz zarządzania nimi, a także wykonywanie akcji naprawczych w odpowiedzi na wahania obciążenia.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> 
> * Symulowanie korzystania z baz danych dzierżaw przy użyciu dostarczonego generatora obciążenia
> * Monitorowanie reakcji baz danych dzierżaw na wzrost obciążenia
> * Skalowanie w górę elastycznej puli w reakcji na zwiększone obciążenie bazy danych
> * Aprowizacja drugiej elastycznej puli w celu równoważenia aktywności baz danych


Do wykonania zadań opisanych w tym samouczku niezbędne jest spełnienie następujących wymagań wstępnych:

* Wdrożono aplikację Bazy danych SaaS na dzierżawę Wingtip Tickets. Aby wdrożyć w mniej niż pięć minut, zobacz [Wdrażanie i eksplorowanie aplikacji Bazy danych SaaS Wingtip na dzierżawę](saas-dbpertenant-get-started-deploy.md)
* Zainstalowany jest program Azure PowerShell. Aby uzyskać szczegółowe informacje, zobacz [Rozpoczynanie pracy z programem Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-saas-performance-management-patterns"></a>Wprowadzenie do wzorców zarządzania wydajnością SaaS

Zarządzanie wydajnością bazy danych polega na zbieraniu danych dotyczących wydajności i analizowaniu ich, a następnie reagowaniu na te dane przez dostosowanie parametrów w celu zachowania akceptowalnego czasu odpowiedzi aplikacji. Podczas hostowania wielu dzierżaw pul elastycznych są opłacalnym sposobem dostarczania zasobów i zarządzania nimi dla grupy baz danych z nieprzewidywalnymi obciążeniami. Przy pewnych wzorcach obciążenia korzyści mogą się pojawić przy zarządzaniu w puli zaledwie dwoma bazami danych S3.

![diagram aplikacji](./media/saas-dbpertenant-performance-monitoring/app-diagram.png)

Pule i bazy danych w pulach powinny być monitorowane, aby upewnić się, że pozostają w dopuszczalnych zakresach wydajności. Dostroić konfigurację puli, aby zaspokoić potrzeby zagregowanego obciążenia wszystkich baz danych, zapewniając, że eDTU puli są odpowiednie dla ogólnego obciążenia. Dostosuj minimalne i maksymalne wartości eDTU dla bazy danych do wartości odpowiadających wymaganiom konkretnych aplikacji.

### <a name="performance-management-strategies"></a>Strategie zarządzania wydajnością

* Aby uniknąć konieczności ręcznego monitorowania wydajności, najbardziej efektywne jest **ustawianie alertów wyzwalających, gdy bazy danych lub pule błądzą poza normalnymi zakresami.**
* Aby odpowiedzieć na krótkoterminowe wahania łącznej wielkości obliczeń puli, **poziom jednostek eDTU puli można skalować w górę lub w dół**. Jeśli takie wahania występują regularnie lub są przewidywalne, **można zaplanować automatyczne skalowanie puli**. Na przykład skalowanie w dół może nastąpić, kiedy przewidywane jest niskie obciążenie — w nocy lub podczas weekendów.
* Odpowiedzią na wahania długoterminowe lub zmiany liczby baz danych jest **przeniesienie pojedynczych baz danych do innych pul**.
* Aby odpowiedzieć na krótkoterminowe wzrosty *w poszczególnych* baz danych obciążenia **poszczególnych baz danych mogą być wyjęte z puli i przypisane indywidualny rozmiar obliczeń**. Po zmniejszeniu obciążenia można zwrócić bazę danych do puli. Gdy jest to znane z wyprzedzeniem, bazy danych mogą być przenoszone prewyminawnie, aby upewnić się, że baza danych zawsze ma zasoby, których potrzebuje, i aby uniknąć wpływu na inne bazy danych w puli. Jeśli takie wymaganie jest przewidywalne, na przykład w przypadku oczekiwania na wzmożone zakupy biletów na popularną imprezę, wówczas takie działanie funkcji zarządzania można uwzględnić w aplikacji.

Witryna [Azure Portal](https://portal.azure.com) udostępnia wbudowane funkcje monitorowania i alertów dla większości zasobów. W usłudze SQL Database funkcje monitorowania i zgłaszania alertów są dostępne na poziomie baz danych i pul. To wbudowane monitorowanie i alerty jest specyficzne dla zasobów, więc jest wygodny w użyciu dla niewielkiej liczby zasobów, ale nie jest bardzo wygodne podczas pracy z wieloma zasobami.

W przypadku scenariuszy o dużej objętości, w których pracujesz z wieloma zasobami, można użyć [dzienników usługi Azure Monitor.](saas-dbpertenant-log-analytics.md) Jest to oddzielna usługa platformy Azure, która zapewnia analizę za pomocą emitowanych dzienników zebranych w obszarze roboczym usługi Log Analytics. Dzienniki usługi Azure Monitor mogą zbierać dane telemetryczne z wielu usług i służyć do wykonywania zapytań i ustawiania alertów.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Pobierz skrypty aplikacji bazy danych SaaS dla bazy danych Wingtip na dzierżawę

Skrypty bazy danych wielodostępnych aplikacji Wingtip Tickets SaaS i kod źródłowy aplikacji są dostępne w repozytorium [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub. Zapoznaj się z [ogólnymi wskazówkami dotyczącymi](saas-tenancy-wingtip-app-guidance-tips.md) pobierania i odblokowywania skryptów SaaS biletów Wingtip.

## <a name="provision-additional-tenants"></a>Aprowizacja dodatkowych dzierżaw

Pule stają się opłacalne nawet w przypadku zaledwie dwóch baz danych nas poziomie S3, a związane z nimi oszczędności wzrastają wraz ze zwiększeniem liczby baz danych w puli, co jest spowodowane efektem uśredniania. Aby dobrze zrozumieć działanie monitorowania wydajności i zarządzania nią w dużej skali, w tym samouczku zalecamy wdrożenie co najmniej 20 baz danych.

Jeśli już zainicjowano aprowizacji partii dzierżawców w poprzednim samouczku, przejdź do [symulacji użycia na wszystkich bazach danych dzierżawy](#simulate-usage-on-all-tenant-databases) sekcji.

1. W **powershell ISE**, otwórz ... \\Moduły\\szkoleniowe Monitorowanie\\wydajności i zarządzanie*Demo-PerformanceMonitoringAndManagement.ps1*. Nie zamykaj tego skryptu, gdyż w ramach tego samouczka będzie konieczne uruchomienie kilku scenariuszy.
1. Ustaw **$DemoScenario** = **1**, **Aprowizuj partię dzierżawców**
1. Naciśnij klawisz **F5**, aby uruchomić skrypt.

Ten skrypt wdroży 17 dzierżaw w czasie krótszym niż pięć minut.

*Skrypt New-TenantBatch* używa zagnieżdżonego lub połączonego zestawu szablonów [Menedżera zasobów,](../azure-resource-manager/index.yml) które tworzą partię dzierżawców, która domyślnie kopiuje **bazę danych basetenantdb** na serwerze katalogu w celu utworzenia nowych baz danych dzierżawy, a następnie rejestruje je w katalogu, a na koniec inicjuje je z nazwą dzierżawy i typem miejsca. Jest to zgodne ze sposobem, w jaki aplikacja przepisuje nową dzierżawę. Wszelkie zmiany wprowadzone do *basetenantdb* są stosowane do wszystkich nowych dzierżaw aprowizowanych później. Zobacz [samouczek Zarządzanie schematami,](saas-tenancy-schema-management.md) aby zobaczyć, jak wprowadzić zmiany schematu w *istniejących* bazach danych dzierżawy (w tym w bazie danych *basetenantdb).*

## <a name="simulate-usage-on-all-tenant-databases"></a>Symulowanie użycia we wszystkich baz danych dzierżaw

*Skrypt Demo-PerformanceMonitoringAndManagement.ps1* jest pod warunkiem, że symuluje obciążenie uruchomione dla wszystkich baz danych dzierżawy. Obciążenie jest generowane przy użyciu jednego z dostępnych scenariuszy obciążenia:

| Demonstracja | Scenariusz |
|:--|:--|
| 2 | Generowanie normalnego obciążenia intensywnością (około 40 DTU) |
| 3 | Generowanie obciążenia z dłuższymi i częstszymi skokami wartości dla każdej bazy danych|
| 4 | Generowanie obciążenia przy wyższych seriach DTU na bazę danych (około 80 DTU)|
| 5 | Generowanie normalnego obciążenia plus duże obciążenie dla pojedynczej dzierżawy (około 95 jednostek DTU)|
| 6 | Generowanie niezrównoważonego obciążenia dla wielu pul|

Generator obciążenia stosuje obciążenie *syntetyczne* wyłącznie do procesorów dla każdej bazy danych dzierżawy. Generator uruchamia zadanie dla każdej bazy danych dzierżawy, co powoduje cykliczne wywołanie procedury składowanej, która generuje obciążenie. Poziomy obciążenia (mierzone w jednostkach eDTU), czas trwania i interwały są zróżnicowane dla wszystkich baz danych, co symuluje nieprzewidywalną aktywność dzierżawy.

1. W **powershell ISE**, otwórz ... \\Moduły\\szkoleniowe Monitorowanie\\wydajności i zarządzanie*Demo-PerformanceMonitoringAndManagement.ps1*. Nie zamykaj tego skryptu, gdyż w ramach tego samouczka będzie konieczne uruchomienie kilku scenariuszy.
1. Ustaw **$DemoScenario** = **2**, *Generowanie normalnego obciążenia natężenia.*
1. Naciśnij klawisz **F5**, aby zastosować obciążenie do wszystkich baz danych dzierżaw.

Baza danych SaaS Wingtip na dzierżawę to aplikacja SaaS, a rzeczywiste obciążenie aplikacji SaaS jest zazwyczaj sporadyczne i nieprzewidywalne. Aby to zasymulować, generator w sposób losowy obciąża wszystkie dzierżawy. Kilka minut są potrzebne do wzorca obciążenia pojawiają się, więc uruchomić generator obciążenia przez 3-5 minut przed podjęciem próby monitorowania obciążenia w poniższych sekcjach.

> [!IMPORTANT]
> Generator obciążenia działa jako seria zadań w lokalnej sesji programu PowerShell. Karta *Demo-PerformanceMonitoringAndManagement.ps1* musi pozostać otwarta! Zamknięcie karty lub wstrzymanie pracy komputera spowoduje zatrzymanie generatora obciążenia. Generator obciążenia pozostaje w stanie *wywoływania zadań,* gdzie generuje obciążenie dla wszystkich nowych dzierżaw, które są aprowizacji po uruchomieniu generatora. Użyj *Ctrl-C,* aby zatrzymać wywoływanie nowych zadań i zakończyć skrypt. Generator obciążenia będzie nadal działać, ale tylko na istniejących dzierżaw.

## <a name="monitor-resource-usage-using-the-azure-portal"></a>Monitorowanie użycia zasobów za pomocą witryny Azure portal

Aby monitorować użycie zasobów wynikające z zastosowanego obciążenia, otwórz portal do puli zawierającej bazy danych dzierżawy:

1. Otwórz [portal azure](https://portal.azure.com) i przejdź do *serwera&lt;tenants1-dpt- USER.&gt; *
1. Przewiń w dół, zlokalizuj elastyczne pule i kliknij pozycję **Pool1**. Ta pula zawiera wszystkie bazy danych dzierżaw utworzone dotychczas.

Obserwuj **wykresy monitorowania puli elastycznej** i **elastycznej bazy danych.**

Wykorzystanie zasobów puli jest zagregowane wykorzystanie bazy danych dla wszystkich baz danych w puli. Wykres bazy danych przedstawia pięć najgorętszych baz danych:

![wykres bazy danych](./media/saas-dbpertenant-performance-monitoring/pool1.png)

Ponieważ istnieją dodatkowe bazy danych w puli poza pierwszą piątką, wykorzystanie puli pokazuje działanie, które nie jest odzwierciedlone w pierwszej piątce wykresu baz danych. Aby uzyskać dodatkowe informacje, kliknij pozycję **Wykorzystanie zasobów bazy danych:**

![wykorzystanie zasobów bazy danych](./media/saas-dbpertenant-performance-monitoring/database-utilization.png)


## <a name="set-performance-alerts-on-the-pool"></a>Ustawianie alertów wydajności dla puli

Ustaw alert na puli, który \>wyzwala na 75% wykorzystania w następujący sposób:

1. Otwórz *pulę1* (na serwerze *\<użytkowników\> tenants1-dpt)* w [witrynie Azure portal](https://portal.azure.com).
1. Kliknij pozycję **Reguły alertów**, a następnie kliknij przycisk **+ Dodaj alert**:

   ![dodawanie alertu](media/saas-dbpertenant-performance-monitoring/add-alert.png)

1. Podaj nazwę, taką jak **Wysoki poziom jednostek DTU**,
1. Ustaw następujące wartości:
   * **Metryka = Procent eDTU**
   * **Warunek = większy niż**
   * **Próg = 75**
   * **Okres = W ciągu ostatnich 30 minut**
1. Dodaj adres e-mail do pola *Dodatkowe wiadomości e-mail administratora* i kliknij przycisk **OK**.

   ![ustawianie alertu](media/saas-dbpertenant-performance-monitoring/alert-rule.png)


## <a name="scale-up-a-busy-pool"></a>Skalowanie mocno obciążonej puli w górę

Jeśli poziom zagregowanego obciążenia puli zwiększy się do poziomu maksymalnych możliwości puli i osiągnie 100 % wykorzystania jednostek eDTU, będzie to miało wpływ na wydajność poszczególnych baz danych, co potencjalnie może wydłużyć czasy odpowiedzi na zapytania dla wszystkich baz danych w puli.

**Krótkoterminowe,** należy rozważyć skalowanie puli w celu zapewnienia dodatkowych zasobów lub usunięcie baz danych z puli (przenoszenie ich do innych pul lub z puli do warstwy usług autonomicznych).

**W dłuższej perspektywie**, należy rozważyć optymalizację zapytań lub użycia indeksu w celu zwiększenia wydajności bazy danych. W zależności od wrażliwości aplikacji na problemy wydajnościowe najlepszym rozwiązaniem jest skalowanie puli w górę przed osiągnięciem przez nią wykorzystania jednostek eDTU równego 100%. Użyj alertów do wczesnego ostrzegania.

Zajętość puli można zasymulować, zwiększając obciążenia wytwarzane przez generator. Powoduje, że bazy danych pęknąć częściej i na dłużej, zwiększenie obciążenia agregacji w puli bez zmiany wymagań poszczególnych baz danych. Skalowanie puli w górę można łatwo przeprowadzić w portalu lub z programu PowerShell. W tym ćwiczeniu użyto portalu.

1. Ustaw *$DemoScenario* = **3**, _Generowanie obciążenia z dłuższymi i częstszymi seriami na bazę danych,_ aby zwiększyć intensywność obciążenia agregacji w puli bez zmiany obciążenia szczytowego wymaganego przez każdą bazę danych.
1. Naciśnij klawisz **F5**, aby zastosować obciążenie do wszystkich baz danych dzierżaw.

1. Przejdź do **puli1** w witrynie Azure portal.

Monitoruj zwiększone użycie eDTU puli na górnym wykresie. To trwa kilka minut dla nowego większego obciążenia kopać w, ale należy szybko zobaczyć puli zaczynają trafić maksymalne wykorzystanie, a jak obciążenie steadies do nowego wzorca, szybko przeciąża puli.

1. Aby skalować w górę puli, kliknij pozycję **Konfiguruj pulę** u góry strony **Pool1.**
1. Dostosuj ustawienie **EDTU puli** do **100**. Zmiana liczby jednostek eDTU puli nie zmienia ustawienia poszczególnych baz danych (które nadal ma maksymalną wartość 50 jednostek eDTU na bazę danych). Ustawienia dla bazy danych można zobaczyć po prawej stronie strony **Konfigurowanie puli.**
1. Kliknij **przycisk Zapisz,** aby przesłać żądanie skalowania puli.

Wróć do **przeglądu pool1,** > **Overview** aby wyświetlić wykresy monitorowania. Monitoruj efekt dostarczania puli więcej zasobów (chociaż z kilkoma bazami danych i losowo załadowanym obciążeniem nie zawsze jest łatwo zobaczyć ostatecznie, dopóki nie uruchomisz przez jakiś czas). Patrząc na wykresy należy sobie zdawać sprawę, że 100% na górnym wykresie odpowiada teraz 100 jednostkom eDTU, podczas gdy na dolnym wykresie 100% nadal odpowiada 50 jednostkom eDTU, gdyż maksymalna wartość dla pojedynczej bazy danych nadal wynosi 50 jednostek eDTU.

Bazy danych pozostają w trybie online i są w pełni dostępne podczas całego procesu. W ostatniej chwili przed włączeniem dla każdej bazy danych nowej wartości eDTU dla puli wszystkie aktywne połączenia zostają przerwane. Kod aplikacji powinien być napisany w taki sposób, aby zawsze próbować ponownie nawiązać przerwane połączenie, by móc ponownie połączyć się z bazą danych w puli przeskalowanej w górę.

## <a name="load-balance-between-pools"></a>Równoważenie obciążenia między pulami

Alternatywnym rozwiązaniem w stosunku do skalowania puli w górę jest utworzenie drugiej puli i przeniesienie do niej baz danych w celu zrównoważenia obciążenia między dwoma pulami. Aby to rozwiązanie zadziałało, nowa pula musi zostać utworzona na tym samym serwerze co pierwsza.

1. W [witrynie Azure portal](https://portal.azure.com)otwórz serwer **&lt;tenants1-dpt- USER.&gt; **
1. Kliknij **+ Nowa pula,** aby utworzyć pulę na bieżącym serwerze.
1. W szablonie **Puli elastycznej:**

   1. Ustaw **nazwę** na *Pool2*.
   1. Pozostaw warstwę cenową **Pula Standardowa**.
   1. Kliknij **pozycję Konfiguruj pulę**,
   1. Ustaw **eDTU puli** do *50 eDTU*.
   1. Kliknij **pozycję Dodaj bazy danych,** aby wyświetlić listę baz danych na serwerze, które można dodać do *puli2*.
   1. Wybierz dowolne 10 baz danych, aby przenieść je do nowej puli, a następnie kliknij przycisk **Wybierz**. Jeśli używasz generatora obciążenia, usługa już wie, że twój profil wydajności wymaga większej puli niż domyślny rozmiar eDTU 50 i zaleca, począwszy od ustawienia eDTU 100.

      ![Zalecenie](media/saas-dbpertenant-performance-monitoring/configure-pool.png)

   1. W tym samouczku pozostaw wartość domyślną na 50 eDTU i kliknij ponownie przycisk **Wybierz.**
   1. Wybierz **przycisk OK,** aby utworzyć nową pulę i przenieść do niej wybrane bazy danych.

Tworzenie puli i przenoszenie baz danych zajmuje kilka minut. Ponieważ bazy danych są przenoszone, pozostają one w trybie online i są w pełni dostępne do ostatniej chwili, w którym to momencie wszelkie otwarte połączenia są zamknięte. Tak długo, jak masz pewne logiki ponawiania, klienci będą następnie łączyć się z bazą danych w nowej puli.

Przejdź do **puli2** (na serwerze *\<dzierżawy1-dpt- user),\> * aby otworzyć pulę i monitorować jej wydajność. Jeśli go nie widzisz, poczekaj na wykonanie obsługi administracyjnej nowej puli.

Teraz widzisz, że użycie zasobów w *puli1* spadła i że *Pool2* jest teraz podobnie załadowany.

## <a name="manage-performance-of-an-individual-database"></a>Zarządzanie wydajnością pojedynczej bazy danych

Jeśli pojedyncza baza danych w puli doświadcza stałego wysokiego obciążenia, w zależności od konfiguracji puli, może mieć tendencję do zdominowania zasobów w puli i wpływ na inne bazy danych. Jeśli działanie może być kontynuowane przez pewien czas, baza danych może być tymczasowo przeniesiona z puli. Dzięki temu bazy danych, aby mieć dodatkowe zasoby, których potrzebuje i izoluje go od innych baz danych.

W tym ćwiczeniu zostanie zasymulowane zwiększone obciążenie dotyczące miejsca Contoso Concert Hall wywołane wzmożonym pobytem na bilety na popularny koncert.

1. W **programie PowerShell ISE**otwórz ... \\ *Skrypt Demo-PerformanceMonitoringAndManagement.ps1.*
1. Ustaw **$DemoScenario = 5, Generowanie normalnego obciążenia plus duże obciążenie dla pojedynczej dzierżawy (około 95 jednostek DTU).**
1. Ustaw wartość zmiennej **$SingleTenantDatabaseName = contosoconcerthall**
1. Wykonaj skrypt, używając klawisza **F5**.


1. W [witrynie Azure portal](https://portal.azure.com)przejdź do listy baz danych na serwerze *\<użytkownika\> tenants1-dpt.* 
1. Kliknij **contosoconcerthall** bazy danych.
1. Kliknij pulę, w która znajduje **się** w pulę. Znajdź pulę w sekcji **Basen elastyczny.**

1. Sprawdź wykres **monitorowania puli elastycznej** i poszukaj zwiększonego użycia eDTU puli. Po minucie lub dwóch powinno być widoczne wyższe obciążenie, które wkrótce osiągnie poziom 100% wykorzystania puli.
2. Sprawdź **elastyczny** wyświetlacz monitorowania bazy danych, który pokazuje najgorętsze bazy danych w ciągu ostatniej godziny. *Contosoconcerthall* bazy danych powinny wkrótce pojawić się jako jeden z pięciu najgorętszych baz danych.
3. **Kliknij wykres monitorowania elastycznej bazy danych** **chart** i otworzy stronę Wykorzystanie zasobów **bazy danych,** na której można monitorować dowolną z baz danych. Dzięki temu można wyizolować wyświetlanie *dla contosoconcerthall* bazy danych.
4. Z listy baz danych kliknij **contosoconcerthall**.
5. Kliknij **pozycję Warstwa cenowa (skalowanie jednostek DTU),** aby otworzyć stronę **Konfigurowanie wydajności,** na której można ustawić autonomiczny rozmiar obliczeń dla bazy danych.
6. Kliknij kartę **Standardowa**, aby otworzyć opcje skalowania w warstwie Standardowa.
7. Przesuń **suwak DTU** w prawo, aby wybrać **100** obiektów DTU. Należy zauważyć, że odpowiada to celowi usługi, **S3**.
8. Kliknij przycisk **Zastosuj,** aby przenieść bazę danych z puli i uczynić ją standardową bazą danych *S3.*
9. Po zakończeniu skalowania należy monitorować wpływ na contosoconcerthall bazy danych i Pool1 na puli elastycznej i bloków bazy danych.

Po wysokim obciążeniu contosoconcerthall bazy danych ustępuje należy niezwłocznie zwrócić go do puli, aby zmniejszyć jego koszt. Jeśli nie jest jasne, kiedy to się stanie, można ustawić alert w bazie danych, który wyzwoli, gdy jego użycie DTU spadnie poniżej maksymalnej bazy danych w puli. Przenoszenie bazy danych do puli opisano w ćwiczeniu 5.

## <a name="other-performance-management-patterns"></a>Inne wzorce zarządzania wydajnością

**Skalowanie wyprzedzające** W powyższym ćwiczeniu, w którym zbadano sposób skalowania izolowanej bazy danych, wiesz, której bazy danych szukać. Jeśli kierownictwo Contoso Concert Hall poinformowało Wingtips o zbliżającej się sprzedaży biletów, baza danych mogła zostać przeniesiona z puli prewktuzycznie. W przeciwnym razie konieczne byłoby skonfigurowanie alertów dla puli lub bazy danych w celu informowania o zmieniającej się sytuacji. Byłoby wysoce nieprzyjemne, gdyby wiadomość o zdarzeniu pochodziła od innych dzierżawców w puli, skarżących się na pogorszenie wydajności. Jeśli jednak dzierżawca może przewidzieć, jak długo będą mu potrzebne dodatkowe zasoby, możesz tak skonfigurować element Runbook usługi Azure Automation, aby przenieść bazę danych z puli, a następnie z powrotem do puli zgodnie z ustalonym harmonogramem.

**Samoobsługowe skalowanie przez dzierżawcę** Ponieważ skalowanie jest zadaniem łatwo wywoływanym za pośrednictwem interfejsu API zarządzania, można w prosty sposób wbudować możliwość skalowania baz danych dzierżawy w aplikację używaną przez dzierżawcę i zaoferować ją jako funkcję usługi SaaS. Na przykład, można umożliwić dzierżawcom samodzielne administrowanie skalowaniem w górę i w dół, być może bezpośrednio powiązane z ich rozliczeniami!

**Skalowanie puli w górę i w dół zgodnie z harmonogramem odpowiadającym wzorcom użycia**

W przypadku, gdy zagregowane wykorzystanie dzierżaw jest zgodne z przewidywalnymi wzorcami użycia można użyć usługi Azure Automation do skalowania puli w górę i w dół zgodnie z harmonogramem. Na przykład pulę można skalować w dół po godzinie 18:00 i ponownie w górę przed godziną 6:00 w dni robocze, jeśli wiadomo, że pomiędzy tymi godzinami występuje spadek wymagań dotyczących zasobów.



## <a name="next-steps"></a>Następne kroki

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Symulowanie korzystania z baz danych dzierżaw przy użyciu dostarczonego generatora obciążenia
> * Monitorowanie reakcji baz danych dzierżaw na wzrost obciążenia
> * Skalowanie w górę elastycznej puli w reakcji na zwiększone obciążenie bazy danych
> * Aprowizacja drugiej elastycznej puli w celu równoważenia aktywności baz danych

[Samouczek przywracania pojedynczej dzierżawy](saas-dbpertenant-restore-single-tenant.md)


## <a name="additional-resources"></a>Zasoby dodatkowe

* Dodatkowe [samouczki, które opierają się na wdrożeniu aplikacji bazy danych SaaS Wingtip na dzierżawę](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Pule elastyczne SQL](sql-database-elastic-pool.md)
* [Azure Automation](../automation/automation-intro.md)
* [Dzienniki usługi Azure Monitor](saas-dbpertenant-log-analytics.md) — konfigurowanie i używanie samouczka dzienników usługi Azure Monitor
