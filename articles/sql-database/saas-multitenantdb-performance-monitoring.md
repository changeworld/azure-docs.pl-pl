---
title: Monitorowanie wydajności podzielonej na fragmenty wielodostępnych Azure SQL database w aplikacji SaaS z wieloma dzierżawami | Dokumentacja firmy Microsoft
description: Monitorowanie i zarządzanie wydajnością podzielonej na fragmenty bazy danych Azure SQL wielu dzierżaw w aplikacji SaaS z wieloma dzierżawami
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
ms.openlocfilehash: be7dbe35800bbe911bc56d1883462534a16499a0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61485592"
---
# <a name="monitor-and-manage-performance-of-sharded-multi-tenant-azure-sql-database-in-a-multi-tenant-saas-app"></a>Monitorowanie i zarządzanie wydajnością podzielonej na fragmenty bazy danych Azure SQL wielu dzierżaw w aplikacji SaaS z wieloma dzierżawami

W tym samouczku zbadano kilka podstawowych scenariuszy zarządzania wydajnością używanych w aplikacjach SaaS. Za pomocą generator obciążenia, aby symulować działanie w bazach danych w wielu dzierżawców podzielonej na fragmenty, wbudowane funkcje monitorowania i alertów usługi SQL Database zostały przedstawione.

Aplikacji Wingtip Tickets SaaS wielodostępnej w bazie danych używa modelu danych podzielonych na fragmenty wielodostępne, dane miejsce (dzierżawca) jest dystrybuowane za pomocą Identyfikatora dzierżawy potencjalnie wielu bazom danych. Podobnie jak w przypadku wielu innych aplikacji SaaS, oczekiwany wzorzec obciążenia dzierżawy charakteryzuje się nieprzewidywalnością i sporadycznością występowania. Innymi słowy, sprzedaż biletów może nastąpić w dowolnej chwili. Aby móc korzystać z tego wzorca użycia typowych bazy danych, baz danych mogą być skalowane górę i w dół do optymalizację kosztu rozwiązania. W przypadku tego typu wzorca ważne jest monitorować użycie zasobów bazy danych, aby upewnić się, że obciążenia rozsądne Równoważenie obciążenia między potencjalnie wiele baz danych. Należy również upewnić się, że pojedyncze bazy danych posiadają odpowiednie zasoby, a nie zbliżają ich [jednostek DTU](sql-database-purchase-models.md#dtu-based-purchasing-model) limity. W tym samouczku przedstawiono sposób monitorowania i zarządzania bazami danych i sposobu podejmowanie działań naprawczych w odpowiedzi na wahania obciążenia.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> 
> * Symulowanie użycia we fragmenty wielodostępnej bazie danych przez uruchomienie generatora obciążenia podana
> * Monitorowanie bazy danych odpowiada na wzrost obciążenia
> * Skalowanie w górę bazy danych w odpowiedzi na zwiększone obciążenie bazy danych
> * Aprowizacja dzierżawy do bazy danych w pojedynczej dzierżawy

Do wykonania zadań opisanych w tym samouczku niezbędne jest spełnienie następujących wymagań wstępnych:

* Aplikacja o nazwie Wingtip Tickets SaaS wielodostępnej w bazie danych jest wdrażana. Aby wdrożyć w mniej niż pięć minut, zobacz [wdrażanie i eksplorowanie aplikacji Wingtip Tickets SaaS wielodostępnej w bazie danych](saas-multitenantdb-get-started-deploy.md)
* Zainstalowany jest program Azure PowerShell. Aby uzyskać szczegółowe informacje, zobacz [Rozpoczynanie pracy z programem Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-saas-performance-management-patterns"></a>Wprowadzenie do wzorców zarządzania wydajnością SaaS

Zarządzanie wydajnością bazy danych polega na zbieraniu danych dotyczących wydajności i analizowaniu ich, a następnie reagowaniu na te dane przez dostosowanie parametrów w celu zachowania akceptowalnego czasu odpowiedzi aplikacji. 

### <a name="performance-management-strategies"></a>Strategie zarządzania wydajnością

* Aby uniknąć konieczności ręcznego monitorowania wydajności, jest najbardziej efektywne, aby **ustawić alerty, które wyzwolić w przypadku baz danych wyjdą poza przewidziane zakresy**.
* Aby na krótkoterminowe wahania obliczeń rozmiaru bazy danych, **poziom jednostek DTU może być skalowana w górę lub w dół**. Jeśli takie wahania wydawane regularnie lub są przewidywalne, **skalowanie bazy danych, można zaplanować automatyczne**. Na przykład skalowanie w dół może nastąpić, kiedy przewidywane jest niskie obciążenie — w nocy lub podczas weekendów.
* Aby odpowiedzieć na wahania długoterminowe lub zmiany w dzierżawach, **poszczególnych dzierżaw, można przenieść do innej bazy danych**.
* Aby odpowiedzieć na krótkoterminowe zwiększenie *poszczególnych* obciążenia dzierżawy **poszczególnych dzierżaw może być z bazy danych i przypisane rozmiar poszczególnych obliczeń**. Po zmniejszeniu obciążenia dzierżawy mogą być zwracane następnie wielodostępną bazą danych. Gdy jest to znane z wyprzedzeniem, dzierżawców można przenieść uprzedzając zdarzenie upewnij się, że baza danych zawsze ma zasoby, których potrzebuje i uniknąć wpływu na innych dzierżawców w wielodostępnej bazie danych. Jeśli takie wymaganie jest przewidywalne, na przykład w przypadku oczekiwania na wzmożone zakupy biletów na popularną imprezę, wówczas takie działanie funkcji zarządzania można uwzględnić w aplikacji.

Witryna [Azure Portal](https://portal.azure.com) udostępnia wbudowane funkcje monitorowania i alertów dla większości zasobów. Usługi SQL Database jest dostępna w bazach danych monitorowania i alertów. To wbudowane funkcje monitorowania i alertów zależy od konkretnych zasobów, dlatego jest łatwa w użyciu dla małej liczby zasobów, ale nie jest wygodne w przypadku pracy z wieloma zasobami.

Dla dużej liczby scenariuszy, w którym pracujesz z wieloma zasobami, [dzienniki usługi Azure Monitor](https://azure.microsoft.com/services/log-analytics/) mogą być używane. Jest to osobna usługa Azure oferuje analytics za pośrednictwem emitowany dzienników diagnostycznych i danych telemetrycznych zebranych w obszarze roboczym usługi Log Analytics. Dzienniki platformy Azure Monitor umożliwia zbieranie danych telemetrycznych z wielu usług i służyć do tworzenia zapytań i ustawiania alertów.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Pobierz kod źródłowy aplikacji Wingtip Tickets SaaS wielodostępnej w bazie danych i skryptów

Skryptów aplikacji Wingtip Tickets SaaS wielodostępnej w bazie danych i kod źródłowy aplikacji są dostępne w [WingtipTicketsSaaS MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) repozytorium GitHub. Zapoznaj się z [ogólne wskazówki dotyczące](saas-tenancy-wingtip-app-guidance-tips.md) kroków pobrać i odblokować skrypty SaaS o nazwie Wingtip Tickets.

## <a name="provision-additional-tenants"></a>Aprowizacja dodatkowych dzierżaw

Aby dobrze zrozumieć działanie monitorowania i zarządzania wydajnością na dużą skalę, ten samouczek wymaga posiadania wielu dzierżaw w podzielonej na fragmenty wielodostępnej bazie danych.

Jeśli masz aprowizowano już partię dzierżaw, pracy z poprzednim samouczkiem, przejdź do [symulowanie użycia we wszystkich bazach danych dzierżaw](#simulate-usage-on-all-tenant-databases) sekcji.

1. W **PowerShell ISE**, Otwórz... \\Learning Modules\\zarządzania i monitorowania wydajności\\*Demo-PerformanceMonitoringAndManagement.ps1*. Nie zamykaj tego skryptu, gdyż w ramach tego samouczka będzie konieczne uruchomienie kilku scenariuszy.
1. Ustaw zmienną **$DemoScenario** = **1**, _Provision a batch of tenants_ (Aprowizacja partii dzierżaw)
1. Naciśnij klawisz **F5**, aby uruchomić skrypt.

Skrypt wdroży 17 dzierżaw w wielodostępnej bazie danych w ciągu kilku minut. 

*New-TenantBatch* skrypt tworzy nowych dzierżaw z dzierżawą unikatowe klucze w ramach podzielonej na fragmenty wielodostępną bazą danych i inicjuje je przy użyciu dzierżawy nazwy i typu miejsca. Jest to zgodne ze sposobem jej aprowizacji nowej dzierżawy. 

## <a name="simulate-usage-on-all-tenant-databases"></a>Symulowanie użycia we wszystkich baz danych dzierżaw

*Demo-PerformanceMonitoringAndManagement.ps1* skrypt ma pod warunkiem symuluje obciążenia wielodostępną bazą danych. Obciążenie jest generowana z użyciem jednego ze scenariuszy dostępne obciążenia:

| Demonstracja | Scenariusz |
|:--|:--|
| 2 | Generowanie obciążenia o normalnym natężeniu (około 30 jednostek DTU) |
| 3 | Generowanie obciążenia o dłużej wzrosty każdego dzierżawcy|
| 4 | Generowanie obciążenia o wyższych wzrosty liczby jednostek DTU na dzierżawę (około 70 DTU)|
| 5 | Generuj wysokiej intensywność (około 90 DTU) na pojedynczej dzierżawy wraz z obciążeniem normalnym natężeniu innych dzierżaw |

Generator obciążenia stosuje obciążenie *syntetyczne* wyłącznie do procesorów dla każdej bazy danych dzierżawy. Generator uruchamia zadanie dla każdej bazy danych dzierżawy, co powoduje cykliczne wywołanie procedury składowanej, która generuje obciążenie. Poziomy obciążenia (w jednostkach Dtu), czas trwania i interwały są zróżnicowane dla wszystkich baz danych, symulując nieprzewidywalną aktywność dzierżawy.

1. W **PowerShell ISE**, Otwórz... \\Learning Modules\\zarządzania i monitorowania wydajności\\*Demo-PerformanceMonitoringAndManagement.ps1*. Nie zamykaj tego skryptu, gdyż w ramach tego samouczka będzie konieczne uruchomienie kilku scenariuszy.
1. Ustaw **$DemoScenario** = **2**, _obciążenia o normalnym natężeniu Generuj_
1. Naciśnij klawisz **F5** Aby zastosować obciążenie dla dzierżawców.

Firmy Wingtip Tickets SaaS wielodostępnej w bazie danych jest aplikacją SaaS, a rzeczywiste obciążenie dla aplikacji SaaS jest zazwyczaj sporadyczne i nieprzewidywalne. Aby to zasymulować, generator w sposób losowy obciąża wszystkie dzierżawy. Kilka minut, zanim są wymagane dla wzorca obciążenia się, więc uruchomienie generatora obciążenia 3 – 5 minut przed próbą monitorowania obciążenia w poniższych sekcjach.

> [!IMPORTANT]
> Generator obciążenia działa jako seria zadań w nowym oknie programu PowerShell. Jeśli zamkniesz sesji spowoduje zatrzymanie generatora obciążenia. Generator obciążenia pozostają w *wywoływanie zadania* stanu, w którym generuje obciążenie na nowych dzierżaw, które są udostępniane po uruchomieniu generator. Użyj *Ctrl-C* Aby zatrzymać wywoływania nowe zadania i zamknąć okno skryptu. Generator obciążenia będą nadal uruchomione, ale tylko w przypadku istniejących dzierżaw.

## <a name="monitor-resource-usage-using-the-azure-portal"></a>Monitorowanie wykorzystania zasobów przy użyciu witryny Azure portal

Aby monitorować użycie zasobów wynikające z zastosowanego obciążenia, należy otworzyć portal dla wielodostępnej bazie danych, **tenants1**, zawierający dzierżawy:

1. Otwórz [witryny Azure portal](https://portal.azure.com) i przejdź do serwera *tenants1-mt -&lt;użytkownika&gt;* .
1. Przewiń w dół, zlokalizuj baz danych i kliknij przycisk **tenants1**. Ta podzielonej na fragmenty wielodostępnych baza danych zawiera wszystkie dzierżawy utworzone dotychczas.

![Baza danych wykresu](./media/saas-multitenantdb-performance-monitoring/multitenantdb.png)

Obserwuj **jednostek DTU** wykresu.

## <a name="set-performance-alerts-on-the-database"></a>Ustawianie alertów wydajności dla bazy danych

Ustawianie alertu na bazę danych, który jest wyzwalany przy \>75% wykorzystania w następujący sposób:

1. Otwórz *tenants1* bazy danych (na *tenants1-mt -&lt;użytkownika&gt;*  serwera) w [witryny Azure portal](https://portal.azure.com).
1. Kliknij pozycję **Reguły alertów**, a następnie kliknij przycisk **+ Dodaj alert**:

   ![dodawanie alertu](media/saas-multitenantdb-performance-monitoring/add-alert.png)

1. Podaj nazwę, taką jak **Wysoki poziom jednostek DTU**,
1. Ustaw następujące wartości:
   * **Metryka = procent jednostek DTU**
   * **Warunek = większa niż**
   * **Próg = 75**.
   * **Okres = w ciągu ostatnich 30 minut**
1. Dodaj adres e-mail do *email(s) dodatkowego administratora* pole, a następnie kliknij przycisk **OK**.

   ![ustawianie alertu](media/saas-multitenantdb-performance-monitoring/set-alert.png)

## <a name="scale-up-a-busy-database"></a>Skalowanie w górę zajętej bazy danych

Jeśli poziom obciążenia zwiększa się w bazie danych do punktu wydłużyć bazy danych i osiągnie 100% użycie jednostek DTU, wydajność bazy danych ma wpływ i co potencjalnie czas odpowiedzi na zapytania.

**Rozwiązaniem krótkoterminowym**, należy rozważyć skalowanie w górę bazy danych w celu zapewnienia jej dodatkowych zasobów lub usunięcie dzierżawy z wielodostępną bazą danych (przeniesienie ich poza wielodostępną bazą danych do autonomicznej bazy danych).

**Dłuższy okres**, należy wziąć pod uwagę Optymalizacja zapytań lub użycie indeksu w celu poprawy wydajności bazy danych. W zależności od wrażliwości aplikacji na wydajność wystawia jego najlepszym rozwiązaniem jest skalowanie bazy danych w górę przed osiągnięciem przez nią użycie jednostek DTU w 100%. Użyj alertów do wczesnego ostrzegania.

Zajętej bazy danych można zasymulować, zwiększając obciążenia wytwarzane przez generator. Porzucanie dzierżaw, przejścia częściej, a także zwiększa obciążenie wielodostępną bazą danych, nie wpływając na wymagania poszczególnych dzierżaw. Skalowanie w górę bazy danych można łatwo przeprowadzić w portalu lub za pomocą programu PowerShell. W tym ćwiczeniu użyto portalu.

1. Ustaw *$DemoScenario* = **3**, _Generowanie obciążenia z dłuższymi i dłuższym wzrosty na bazę danych_ do zwiększenia intensywności zagregowanego obciążenia na Baza danych bez zmiany szczytowego obciążenia wymaganego przez każdego dzierżawcę.
1. Naciśnij klawisz **F5**, aby zastosować obciążenie do wszystkich baz danych dzierżaw.
1. Przejdź do **tenants1** bazy danych w witrynie Azure portal.

Monitoruj zwiększone wykorzystanie jednostek DTU na górnym wykresie bazy danych. Zajmuje kilka minut, zanim nowe wyższe obciążenie, ale szybko zauważysz się maksymalnego wykorzystania bazy danych, a jako obciążenie zbliżanie do nowego wzorca, nastąpi szybkie przeciążenie bazy danych.

1. Aby skalować w górę bazy danych, kliknij przycisk **warstwa cenowa (jednostki Dtu skalowania)** w bloku ustawienia.
1. Dostosuj **jednostek DTU** ustawienie **100**. 
1. Kliknij przycisk **Zastosuj** można przesłać żądanie skalowania bazy danych.

Wróć do **tenants1** > **Przegląd** Aby wyświetlić wykresy monitorowania. Monitorowanie efektu działania zapewniania większej ilości zasobów bazy danych (chociaż kilka dzierżaw i losowego obciążenia nie zawsze jest łatwo go dostrzec dopóki nie zostanie uruchomione przez pewien czas). Gdy chcesz wykresy należy sobie zdawać sprawę należy pamiętać, że 100% górnym wykresie odpowiada teraz 100 jednostek Dtu, gdy na dolnym wykresie 100% nadal jest 50 jednostek Dtu.

Bazy danych pozostają w trybie online i są w pełni dostępne podczas całego procesu. Kod aplikacji zawsze mają być zapisywane ponów przerwane połączenia, a więc połączy się ponownie z bazą danych.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Aprowizacja nowej dzierżawy w oddzielnej bazie danych 

Podzielonej na fragmenty modelu wielodostępnym umożliwia wybranie, czy do aprowizacji nowej dzierżawy w wielodostępnej bazie danych wraz z innych dzierżaw lub, aby ustanowić dzierżawy w bazie danych własne. Inicjowanie obsługi dzierżawę w oddzielnej bazie danych, korzysta on z izolacji w oddzielnej bazy danych, umożliwiając zarządzanie wydajnością tej dzierżawy, niezależnie od innych użytkowników, przywracanie tej dzierżawy, niezależnie od innych, itp. Na przykład można umieścić bezpłatną wersję próbną lub regularnych klientów w wielodostępnej bazie danych, a klienci wersji premium w poszczególnych bazach danych.  Jeśli są tworzone odizolowane bazy danych z pojedynczą dzierżawą, są nadal można zarządzać zbiorczo elastycznej puli, aby zoptymalizować koszty zasobów.

Jeśli zostały już aprowizowane nową dzierżawę w oddzielnej bazie danych, Pomiń kilka następnych kroków.

1. W **PowerShell ISE**, Otwórz... \\Learning Modules\\ProvisionTenants\\*ProvisionTenants.ps1 pokaz*. 
1. Modyfikowanie **$TenantName = "Wierzbowate Salsa"** i **$VenueType = "za"**
1. Ustaw **$Scenario** = **2**, _Aprowizować dzierżawy w nowej bazy danych z jedną dzierżawą_
1. Naciśnij klawisz **F5**, aby uruchomić skrypt.

Skrypt aprowizacji tej dzierżawy w oddzielnej bazy danych, Zarejestruj bazę danych i dzierżawy w wykazie i następnie otworzyć stronę zdarzenia dzierżawy w przeglądarce. Odśwież stronę Centrum zdarzeń, a zobaczysz, że "Wierzbowate Salsa" został dodany jako miejsca.

## <a name="manage-performance-of-an-individual-database"></a>Zarządzanie wydajnością poszczególnych baz danych

Jeśli obciążenie pojedynczej dzierżawy w wielodostępnej bazie danych, może to zwykle zdominować zasoby bazy danych i wpłynąć na innych dzierżawców w tej samej bazy danych. W przypadku prawdopodobnie przez pewien czas działanie dzierżawy mogą tymczasowo przeniesiony z bazy danych i do jego własnej bazy danych jedną dzierżawą. Aby dzierżawa dysponowała dodatkowe zasoby wymaga w pełni jednocześnie zostanie odizolowana od innych dzierżawców, a dzięki temu.

To ćwiczenie symuluje efekt Salsa wierzbowate występuje wysokie obciążenie, gdy promocją na popularną imprezę biletów.

1. Otwórz... \\ *Demo-PerformanceMonitoringAndManagement.ps1* skryptu.
1. Ustaw **$DemoScenario = 5**, _Generowanie normalnego obciążenia oraz wysokiego obciążenia dla jednej dzierżawy (około 90 DTU)._
1. Ustaw **$SingleTenantName = Salix Salsa**
1. Wykonaj skrypt, używając klawisza **F5**.

Przejdź do portalu i przejdź do **salixsalsa** > **Przegląd** Aby wyświetlić wykresy monitorowania. 

## <a name="other-performance-management-patterns"></a>Inne wzorce zarządzania wydajnością

**Samoobsługowe skalowanie przez dzierżawy**

Ponieważ skalowanie jest zadaniem łatwo wywoływanym za pośrednictwem interfejsu API, zarządzania należy można łatwo wbudować możliwość skalowania baz danych dzierżawy w aplikację używaną przez dzierżawcę i zaoferować ją jako funkcję usługi SaaS. Na przykład, można umożliwić dzierżawcom samodzielne administrowanie skalowaniem w górę i w dół, być może bezpośrednio powiązane z ich rozliczeniami!

**Skalowanie bazy danych w górę i w dół zgodnie z harmonogramem odpowiadającym wzorcom użycia**

W przypadku, gdy zagregowane wykorzystanie dzierżaw poniżej przewidywalnymi wzorcami użycia, można użyć usługi Azure Automation do skalowania bazy danych w górę i w dół zgodnie z harmonogramem. Na przykład skalowanie bazy danych w dół po godzinie 18: 00 i się ponownie przed 6: 00 w dni robocze, jeśli znasz już występuje spadek wymagań dotyczących zasobów.

## <a name="next-steps"></a>Kolejne kroki

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Symulowanie użycia we fragmenty wielodostępnej bazie danych przez uruchomienie generatora obciążenia podana
> * Monitorowanie bazy danych odpowiada na wzrost obciążenia
> * Skalowanie w górę bazy danych w odpowiedzi na zwiększone obciążenie bazy danych
> * Aprowizacja dzierżawy do bazy danych w pojedynczej dzierżawy

## <a name="additional-resources"></a>Dodatkowe zasoby

<!--* [Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment](saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
* [Azure Automation](../automation/automation-intro.md)