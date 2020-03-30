---
title: Monitorowanie wydajności podzielonej bazy danych z wieloma dzierżawami
description: Monitorowanie i zarządzanie wydajnością podzielonej wielodostępnej bazy danych SQL platformy Azure w aplikacji SaaS z wieloma dzierżawami
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
ms.openlocfilehash: 0af476b69f2effd836fe76d62059259076c16f53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214158"
---
# <a name="monitor-and-manage-performance-of-sharded-multi-tenant-azure-sql-database-in-a-multi-tenant-saas-app"></a>Monitorowanie i zarządzanie wydajnością podzielonej wielodostępnej bazy danych SQL platformy Azure w aplikacji SaaS z wieloma dzierżawami

W tym samouczku zbadano kilka kluczowych scenariuszy zarządzania wydajnością używanych w aplikacjach SaaS. Za pomocą generatora obciążenia do symulacji działania w podzielonych wielodostępnych baz danych, wbudowane funkcje monitorowania i alertów bazy danych SQL są demonstrowane.

Aplikacja Wingtip Tickets SaaS Multi-tenant Database używa podzielonego modelu danych z wieloma dzierżawcami, w którym dane miejsca (dzierżawy) są dystrybuowane przez identyfikator dzierżawy w potencjalnie wielu bazach danych. Podobnie jak w przypadku wielu innych aplikacji SaaS, oczekiwany wzorzec obciążenia dzierżawy charakteryzuje się nieprzewidywalnością i sporadycznością występowania. Innymi słowy, sprzedaż biletów może nastąpić w dowolnej chwili. Aby skorzystać z tego wzorca użycia typowej bazy danych, bazy danych można skalować w górę iw dół, aby zoptymalizować koszt rozwiązania. W przypadku tego typu wzorca ważne jest monitorowanie użycia zasobów bazy danych, aby upewnić się, że obciążenia są rozsądnie zrównoważone w potencjalnie wielu bazach danych. Należy również upewnić się, że poszczególne bazy danych mają odpowiednie zasoby i nie osiągają swoich limitów [jednostek DTU.](sql-database-purchase-models.md#dtu-based-purchasing-model) W tym samouczku opisano sposoby monitorowania baz danych i zarządzania nimi oraz podejmowanie działań naprawczych w odpowiedzi na różnice w obciążeniu.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> 
> * Symulowanie użycia w podzielonej bazie danych z wieloma dzierżawami przez uruchomienie dostarczonego generatora obciążenia
> * Monitoruj bazę danych, reagując na wzrost obciążenia
> * Skalowanie bazy danych w odpowiedzi na zwiększone obciążenie bazy danych
> * Aprowizowanie dzierżawy w bazie danych z jedną dzierżawą

Do wykonania zadań opisanych w tym samouczku niezbędne jest spełnienie następujących wymagań wstępnych:

* Wdrożono aplikację Wingtip Tickets SaaS Multi-tenant Database. Aby wdrożyć w mniej niż pięć minut, zobacz [Wdrażanie i eksplorowanie aplikacji Bazy danych wielodostępnych biletów Wingtip](saas-multitenantdb-get-started-deploy.md)
* Zainstalowany jest program Azure PowerShell. Aby uzyskać szczegółowe informacje, zobacz [Rozpoczynanie pracy z programem Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-saas-performance-management-patterns"></a>Wprowadzenie do wzorców zarządzania wydajnością SaaS

Zarządzanie wydajnością bazy danych polega na zbieraniu danych dotyczących wydajności i analizowaniu ich, a następnie reagowaniu na te dane przez dostosowanie parametrów w celu zachowania akceptowalnego czasu odpowiedzi aplikacji. 

### <a name="performance-management-strategies"></a>Strategie zarządzania wydajnością

* Aby uniknąć konieczności ręcznego monitorowania wydajności, najbardziej efektywne jest **ustawianie alertów, które wyzwalają, gdy bazy danych błądzą poza normalnymi zakresami.**
* Aby odpowiedzieć na krótkoterminowe wahania rozmiaru obliczeń bazy danych, **poziom jednostek DTU można skalować w górę lub w dół**. Jeśli ta fluktuacja występuje regularnie lub przewidywalnie, **skalowanie bazy danych może być zaplanowane automatycznie.** Na przykład skalowanie w dół może nastąpić, kiedy przewidywane jest niskie obciążenie — w nocy lub podczas weekendów.
* Aby odpowiedzieć na długoterminowe wahania lub zmiany w dzierżawach, **poszczególnych dzierżaw można przenieść do innej bazy danych.**
* Aby odpowiedzieć na krótkoterminowe wzrosty *obciążenia poszczególnych* dzierżaw, **poszczególnych dzierżawców można wyjąć z bazy danych i przypisać indywidualny rozmiar obliczeń.** Po zmniejszeniu obciążenia dzierżawy można następnie zwrócić do bazy danych z wieloma dzierżawcami. Gdy jest to znane z wyprzedzeniem, dzierżawy mogą być przenoszone prewymiścijnie, aby upewnić się, że baza danych zawsze ma zasoby, których potrzebuje, i aby uniknąć wpływu na innych dzierżawców w bazie danych z wieloma dzierżawcami. Jeśli takie wymaganie jest przewidywalne, na przykład w przypadku oczekiwania na wzmożone zakupy biletów na popularną imprezę, wówczas takie działanie funkcji zarządzania można uwzględnić w aplikacji.

Witryna [Azure Portal](https://portal.azure.com) udostępnia wbudowane funkcje monitorowania i alertów dla większości zasobów. W przypadku bazy danych SQL monitorowanie i alerty są dostępne w bazach danych. To wbudowane monitorowanie i alerty jest specyficzne dla zasobów, więc jest wygodny w użyciu dla niewielkiej liczby zasobów, ale nie jest wygodne podczas pracy z wieloma zasobami.

W przypadku scenariuszy o dużej objętości, w których pracujesz z wieloma zasobami, można użyć [dzienników usługi Azure Monitor.](https://azure.microsoft.com/services/log-analytics/) Jest to oddzielna usługa platformy Azure, która zapewnia analizę za pomocą emitowanych dzienników zebranych w obszarze roboczym usługi Log Analytics. Dzienniki usługi Azure Monitor mogą zbierać dane telemetryczne z wielu usług i służyć do wykonywania zapytań i ustawiania alertów.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Pobierz kod źródłowy aplikacji I skrypty aplikacji Wingtip Tickets SaaS Multi-tenant Database

Skrypty bazy danych wielodostępnych Wingtip Tickets SaaS i kod źródłowy aplikacji są dostępne w repozytorium [GitHub WingtipTicketsSaaS-MultitenantDB.](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) Zapoznaj się z [ogólnymi wskazówkami dotyczącymi](saas-tenancy-wingtip-app-guidance-tips.md) pobierania i odblokowywania skryptów SaaS biletów Wingtip.

## <a name="provision-additional-tenants"></a>Aprowizacja dodatkowych dzierżaw

Aby uzyskać dobre zrozumienie, jak monitorowanie wydajności i zarządzanie działa na dużą skalę, ten samouczek wymaga, aby mieć wielu dzierżaw w podzielonej bazy danych wielu dzierżawców.

Jeśli już zainicjowano aprowizacji partii dzierżawców w poprzednim samouczku, przejdź do [symulacji użycia na wszystkich bazach danych dzierżawy](#simulate-usage-on-all-tenant-databases) sekcji.

1. W **powershell ISE**, otwórz ... \\Moduły\\szkoleniowe Monitorowanie\\wydajności i zarządzanie*Demo-PerformanceMonitoringAndManagement.ps1*. Nie zamykaj tego skryptu, gdyż w ramach tego samouczka będzie konieczne uruchomienie kilku scenariuszy.
1. Ustaw **$DemoScenario** = **1**, _Aprowizuj partię dzierżawców_
1. Naciśnij klawisz **F5**, aby uruchomić skrypt.

Skrypt wdraża 17 dzierżaw w bazie danych z wieloma dzierżawami w ciągu kilku minut. 

Skrypt *New-TenantBatch* tworzy nowych dzierżawców z unikatowymi kluczami dzierżawy w podzielonej bazie danych z wieloma dzierżawcami i inicjuje je z nazwą dzierżawy i typem miejsca. Jest to zgodne ze sposobem, w jaki aplikacja przepisuje nową dzierżawę. 

## <a name="simulate-usage-on-all-tenant-databases"></a>Symulowanie użycia we wszystkich baz danych dzierżaw

*Skrypt Demo-PerformanceMonitoringAndManagement.ps1* jest pod warunkiem, że symuluje obciążenie uruchomione z wielodostępną bazą danych. Obciążenie jest generowane przy użyciu jednego z dostępnych scenariuszy obciążenia:

| Demonstracja | Scenariusz |
|:--|:--|
| 2 | Generowanie normalnego obciążenia intensywnością (około 30 DTU) |
| 3 | Generowanie obciążenia przy dłuższych seriach na dzierżawę|
| 4 | Generowanie obciążenia przy wyższych seriach DTU na dzierżawę (około 70 DTU)|
| 5 | Generowanie wysokiej intensywności (około 90 jednostek DTU) dla pojedynczej dzierżawy plus normalne obciążenie intensywnością dla wszystkich innych dzierżawców |

Generator obciążenia stosuje obciążenie *syntetyczne* wyłącznie do procesorów dla każdej bazy danych dzierżawy. Generator uruchamia zadanie dla każdej bazy danych dzierżawy, co powoduje cykliczne wywołanie procedury składowanej, która generuje obciążenie. Poziomy obciążenia (w UU), czas trwania i interwały są zróżnicowane we wszystkich bazach danych, symulując nieprzewidywalne działania dzierżawy.

1. W **powershell ISE**, otwórz ... \\Moduły\\szkoleniowe Monitorowanie\\wydajności i zarządzanie*Demo-PerformanceMonitoringAndManagement.ps1*. Nie zamykaj tego skryptu, gdyż w ramach tego samouczka będzie konieczne uruchomienie kilku scenariuszy.
1. Ustaw **$DemoScenario** = **2**, _Generowanie normalnego obciążenia natężenia_
1. Naciśnij **klawisz F5,** aby zastosować obciążenie do wszystkich dzierżawców.

Wingtip Tickets SaaS Multi-tenant Database to aplikacja SaaS, a rzeczywiste obciążenie aplikacji SaaS jest zazwyczaj sporadyczne i nieprzewidywalne. Aby to zasymulować, generator w sposób losowy obciąża wszystkie dzierżawy. Kilka minut są potrzebne do wzorca obciążenia pojawiają się, więc uruchomić generator obciążenia przez 3-5 minut przed podjęciem próby monitorowania obciążenia w poniższych sekcjach.

> [!IMPORTANT]
> Generator obciążenia działa jako seria zadań w nowym oknie programu PowerShell. Po zamknięciu sesji generator obciążenia zostanie zatrzymany. Generator obciążenia pozostaje w stanie *wywoływania zadań,* gdzie generuje obciążenie dla wszystkich nowych dzierżaw, które są aprowizacji po uruchomieniu generatora. Użyj *Ctrl-C,* aby zatrzymać wywoływanie nowych zadań i zakończyć skrypt. Generator obciążenia będzie nadal działać, ale tylko na istniejących dzierżaw.

## <a name="monitor-resource-usage-using-the-azure-portal"></a>Monitorowanie użycia zasobów za pomocą witryny Azure portal

Aby monitorować użycie zasobów wynikające z zastosowanego obciążenia, otwórz portal do bazy danych z wieloma **dzierżawcami, tenants1**, zawierającej dzierżawców:

1. Otwórz [portal Azure](https://portal.azure.com) i przejdź do *serwera&lt;tenants1-mt- USER&gt;*.
1. Przewiń w dół i znajdź bazy danych i kliknij **tenants1**. Ta podzielona baza danych z wieloma dzierżawami zawiera wszystkie dzierżawy utworzone do tej pory.

![wykres bazy danych](./media/saas-multitenantdb-performance-monitoring/multitenantdb.png)

Obserwuj wykres **DTU.**

## <a name="set-performance-alerts-on-the-database"></a>Ustawianie alertów o wydajności w bazie danych

Ustaw alert w bazie danych, \>który wyzwala na 75% wykorzystania w następujący sposób:

1. Otwórz bazę danych *tenants1* (na serwerze *&lt;tenants1-mt- USER)&gt; * w [witrynie Azure portal](https://portal.azure.com).
1. Kliknij pozycję **Reguły alertów**, a następnie kliknij przycisk **+ Dodaj alert**:

   ![dodawanie alertu](media/saas-multitenantdb-performance-monitoring/add-alert.png)

1. Podaj nazwę, taką jak **Wysoki poziom jednostek DTU**,
1. Ustaw następujące wartości:
   * **Metryka = procent DTU**
   * **Warunek = większy niż**
   * **Próg = 75**.
   * **Okres = W ciągu ostatnich 30 minut**
1. Dodaj adres e-mail do pola *Dodatkowe wiadomości e-mail administratora* i kliknij przycisk **OK**.

   ![ustawianie alertu](media/saas-multitenantdb-performance-monitoring/set-alert.png)

## <a name="scale-up-a-busy-database"></a>Skalowanie w górę zajętej bazy danych

Jeśli poziom obciążenia zwiększa się w bazie danych do tego stopnia, że maksymalnie bazy danych i osiągnie 100% użycia DTU, a następnie wpływa na wydajność bazy danych, potencjalnie spowalniając czas odpowiedzi kwerendy.

**Krótkoterminowe,** należy rozważyć skalowanie bazy danych w celu zapewnienia dodatkowych zasobów lub usunięcie dzierżawców z bazy danych z wieloma dzierżawami (przenoszenie ich z bazy danych z wieloma dzierżawami do autonomicznej bazy danych).

**W dłuższej perspektywie**, należy rozważyć optymalizację zapytań lub użycia indeksu w celu zwiększenia wydajności bazy danych. W zależności od wrażliwości aplikacji na problemy z wydajnością jego najlepsze rozwiązania, aby skalować bazę danych w górę, zanim osiągnie 100% użycia DTU. Użyj alertów do wczesnego ostrzegania.

Można symulować zajęty bazy danych, zwiększając obciążenie wytwarzane przez generator. Powoduje, że dzierżawcy pęknąć częściej i na dłużej, zwiększenie obciążenia w bazie danych wielu dzierżawców bez zmiany wymagań poszczególnych dzierżawców. Skalowanie bazy danych jest łatwo zrobić w portalu lub z programu PowerShell. W tym ćwiczeniu użyto portalu.

1. Ustaw *$DemoScenario* = **3**, _Generowanie obciążenia z dłuższymi i częstszymi seriami na bazę danych,_ aby zwiększyć intensywność obciążenia agregacji w bazie danych bez zmiany obciążenia szczytowego wymaganego przez każdą dzierżawę.
1. Naciśnij klawisz **F5**, aby zastosować obciążenie do wszystkich baz danych dzierżaw.
1. Przejdź do bazy danych **tenants1** w witrynie Azure portal.

Monitorowanie zwiększonego użycia DTU bazy danych na górnym wykresie. To trwa kilka minut dla nowego wyższego obciążenia, aby kopać w, ale należy szybko zobaczyć bazy danych rozpocząć trafić maksymalne wykorzystanie i jak steadies obciążenia do nowego wzorca, szybko przeciąża bazę danych.

1. Aby skalować bazę danych, kliknij **pozycję Warstwa cenowa (skalowanie DTU)** w bloku ustawień.
1. Dostosuj ustawienie **DTU** do **100**. 
1. Kliknij przycisk **Zastosuj,** aby przesłać żądanie skalowania bazy danych.

Wróć do**przeglądu** **dzierżawy1,** > aby wyświetlić wykresy monitorowania. Monitoruj efekt dostarczania bazy danych więcej zasobów (chociaż z kilku dzierżawców i losowo obciążenia nie zawsze jest łatwo zobaczyć ostatecznie, dopóki nie uruchomić przez jakiś czas). Podczas gdy patrzysz na wykresy należy pamiętać, że 100% na górnym wykresie reprezentuje obecnie 100 DTU, podczas gdy na dolnym wykresie 100% jest nadal 50 DTU.

Bazy danych pozostają w trybie online i są w pełni dostępne podczas całego procesu. Kod aplikacji powinien być zawsze zapisywany, aby ponowić próbę porzuconych połączeń, a więc ponownie połączy się z bazą danych.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Aprowizowanie nowej dzierżawy we własnej bazie danych 

Podzielony na fragmenty model wielodostępny pozwala wybrać, czy aprowizować nową dzierżawę w bazie danych z wieloma dzierżawcami wraz z innymi dzierżawcami, czy też aprowizować dzierżawcę w własnej bazie danych. Przez inicjowanie obsługi administracyjnej dzierżawy w własnej bazie danych, korzysta z izolacji związanej z oddzielnej bazy danych, co pozwala zarządzać wydajnością tej dzierżawy niezależnie od innych, przywrócić dzierżawę niezależnie od innych itp. Na przykład można wybrać opcję umieszczania bezpłatnych wersji próbnych lub zwykłych klientów w bazie danych z wieloma dzierżawami i klientów premium w poszczególnych bazach danych.  Jeśli tworzone są izolowane bazy danych z jedną dzierżawą, nadal można nimi zarządzać zbiorowo w puli elastycznej w celu optymalizacji kosztów zasobów.

Jeśli już zainicjowano aprowizacji nowej dzierżawy w własnej bazie danych, pomiń kilka następnych kroków.

1. W **powershell ISE**, otwórz ... \\Moduły\\szkoleniowe\\ProvisionTenants*Demo-ProvisionTenants.ps1*. 
1. Modyfikowanie **$TenantName = "Salix Salsa"** i **$VenueType = "dance"**
1. Ustaw **$Scenario** = **2**, _Aprowizuj dzierżawcę w nowej bazie danych z jedną dzierżawą_
1. Naciśnij klawisz **F5**, aby uruchomić skrypt.

Skrypt będzie aprowizować tej dzierżawy w oddzielnej bazie danych, zarejestrować bazę danych i dzierżawy z katalogu, a następnie otworzyć dzierżawy zdarzenia strony w przeglądarce. Odśwież stronę Centrum wydarzeń, a zobaczysz, że "Salix Salsa" został dodany jako miejsce.

## <a name="manage-performance-of-an-individual-database"></a>Zarządzanie wydajnością pojedynczej bazy danych

Jeśli pojedyncza dzierżawa w bazie danych z wieloma dzierżawcami doświadcza stałego wysokiego obciążenia, może mieć tendencję do zdominowania zasobów bazy danych i wpływu na innych dzierżaw w tej samej bazie danych. Jeśli działanie może być kontynuowane przez pewien czas, dzierżawca może zostać tymczasowo przeniesiony z bazy danych i do własnej bazy danych z jedną dzierżawą. Dzięki temu dzierżawcy mają dodatkowe zasoby, których potrzebuje i w pełni izoluje go od innych dzierżawców.

To ćwiczenie symuluje efekt Salix Salsa przeżywa duże obciążenie, gdy bilety trafiają do sprzedaży na popularne wydarzenie.

1. Otwórz ... \\ *Skrypt Demo-PerformanceMonitoringAndManagement.ps1.*
1. Ustaw **$DemoScenario = 5**, _Generowanie normalnego obciążenia plus duże obciążenie dla pojedynczej dzierżawy (około 90 jednostek DTU)._
1. Zestaw **$SingleTenantName = Salix Salsa**
1. Wykonaj skrypt, używając klawisza **F5**.

Przejdź do portalu i przejdź do **salixsalsa** > **Przegląd,** aby wyświetlić wykresy monitorowania. 

## <a name="other-performance-management-patterns"></a>Inne wzorce zarządzania wydajnością

**Skalowanie samoobsługowe dzierżawy**

Ponieważ skalowanie jest zadanie łatwo wywoływane za pośrednictwem interfejsu API zarządzania, można łatwo utworzyć możliwość skalowania baz danych dzierżawy do aplikacji skierowanej do dzierżawy i zaoferować je jako funkcję usługi SaaS. Na przykład, można umożliwić dzierżawcom samodzielne administrowanie skalowaniem w górę i w dół, być może bezpośrednio powiązane z ich rozliczeniami!

**Skalowanie bazy danych w górę i w dół zgodnie z harmonogramem, aby dopasować wzorce użycia**

W przypadku, gdy zagregowane użycie dzierżawy jest zgodne z przewidywalnymi wzorcami użycia, można użyć usługi Azure Automation do skalowania bazy danych w górę i w dół zgodnie z harmonogramem. Na przykład skalowanie bazy danych w dół po 6pm i ponownie przed 6 rano w dni powszednie, gdy wiadomo, że jest spadek wymagań dotyczących zasobów.

## <a name="next-steps"></a>Następne kroki

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Symulowanie użycia w podzielonej bazie danych z wieloma dzierżawami przez uruchomienie dostarczonego generatora obciążenia
> * Monitoruj bazę danych, reagując na wzrost obciążenia
> * Skalowanie bazy danych w odpowiedzi na zwiększone obciążenie bazy danych
> * Aprowizowanie dzierżawy w bazie danych z jedną dzierżawą

## <a name="additional-resources"></a>Zasoby dodatkowe

<!--* [Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment](saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
* [Azure Automation](../automation/automation-intro.md)