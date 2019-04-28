---
title: Odzyskiwanie po awarii dla aplikacji SaaS przy użyciu replikacji geograficznej bazy danych SQL Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak użyć usługi Azure SQL Database geograficznych, aby odzyskać wielodostępną aplikację SaaS w przypadku awarii
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: AyoOlubeko
ms.author: ayolubek
ms.reviewer: sstein
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: b6f0d25f621768f79e8262f38617152e91692a23
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62129858"
---
# <a name="disaster-recovery-for-a-multi-tenant-saas-application-using-database-geo-replication"></a>Odzyskiwanie po awarii dla aplikacji SaaS z wieloma dzierżawami przy użyciu replikacji geograficznej bazy danych

W tym samouczku możesz eksplorować scenariusza odzyskiwania awaryjnego pełny dla wielodostępnych aplikacji SaaS, który jest implementowany przy użyciu modelu bazy danych dla dzierżawcy. Aby chronić aplikacja ulegnie awarii, należy użyć [ _geografickou replikaci_ ](sql-database-geo-replication-overview.md) do tworzenia repliki dla bazy danych wykazu i dzierżawy w regionie odzyskiwanie do alternatywnej. Jeśli wystąpi awaria, szybkiego trybu failover na tych replik, aby wznowić zwykłych operacjach biznesowych. Po przejściu w tryb failover baz danych w regionie, oryginalnym się replik pomocniczych baz danych w regionie odzyskiwania. Po tych replik powróci do ich automatycznie nadrób zaległości do stanu bazy danych w regionie odzyskiwania. Po usunięciu awarii powrotu po awarii do baz danych w regionie produkcyjnym, oryginalnym.

W tym samouczku przedstawiono przepływy pracy trybu failover i powrotu po awarii. Omawiane tematy:
> [!div class="checklist"]
> 
> * Synchronizacji bazy danych i informacji o konfiguracji puli elastycznej do katalogu dzierżawy
> * Skonfiguruj środowisko odzyskiwania w alternatywnym regionie wchodzących w skład aplikacji, serwerów i pul
> * Użyj _geografickou replikaci_ Replikacja bazy danych wykazu i dzierżawy w regionie odzyskiwania
> * W trybie Failover aplikacji i bazy danych wykazu i dzierżawy w regionie odzyskiwania 
> * Później, w trybie Failover aplikacji, bazy danych wykazu i dzierżawy z powrotem do oryginalnego regionu po usunięciu awarii
> * Katalog aktualizacji, ponieważ każda baza danych dzierżawy jest w trybie Failover do lokalizacji głównej bazy danych z każdej dzierżawy śledzenia
> * Upewnij się, bazy danych dzierżaw w aplikacji i głównej zawsze będą wspólnie przechowywane, w tym samym regionie platformy Azure w celu zmniejszenia opóźnienia  
 

Przed rozpoczęciem tego samouczka, upewnij się, że zostały wykonane następujące wymagania wstępne:
* SaaS o nazwie Wingtip Tickets bazy danych dla każdego dzierżawcy aplikacji jest wdrażany. Aby wdrożyć w mniej niż pięć minut, zobacz [wdrażanie i eksplorowanie SaaS o nazwie Wingtip Tickets bazy danych dla każdego dzierżawcy aplikacji](saas-dbpertenant-get-started-deploy.md)  
* Zainstalowany jest program Azure PowerShell. Aby uzyskać szczegółowe informacje, zobacz [Rozpoczynanie pracy z programem Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-the-geo-replication-recovery-pattern"></a>Wprowadzenie do wzorca odzyskiwania chronionej replikacji geograficznej

![Architektura odzyskiwania](media/saas-dbpertenant-dr-geo-replication/recovery-architecture.png)
 
Odzyskiwanie po awarii (DR) jest ważną kwestią w przypadku wielu aplikacji dla zachowania zgodności lub ciągłość prowadzenia działalności biznesowej. Dobrze przygotowany plan odzyskiwania po awarii, powinno być przerwa w działaniu usługi długotrwały, można zminimalizować zakłócenia działania firmy. Zapewnia najniższy cel punktu odzyskiwania i cel czasu odzyskiwania przy zachowaniu repliki bazy danych w regionie odzyskiwania, które mogą być przełączone w tryb failover na krótki Zwróć uwagę, przy użyciu replikacji geograficznej.

Plan odzyskiwania po awarii A oparty na replikację geograficzną składa się z trzech oddzielnych części:
* Konfiguracja — tworzenie i zarządzanie środowiska odzyskiwania systemu
* Odzyskiwanie — trybu failover, aplikacji i baz danych w środowisku odzyskiwania po wystąpieniu awarii,
* Repatriacji - trybu failover, aplikacji i baz danych z powrotem do oryginalnego regionu po usunięciu aplikacji 

Wszystkie elementy powinny być traktowane dokładnie, zwłaszcza, jeśli działających na dużą skalę. Ogólnie plan musi osiągnąć cele kilka:

* Konfigurowanie
    * Utworzyć i utrzymywać w środowisku lustrzane odbicie w regionie odzyskiwania. Tworzenie pul elastycznych i wszelkimi bazami danych, w tym środowisku odzyskiwania replikacji rezerwy pojemności w regionie odzyskiwania. Obsługa tego środowiska obejmują replikowanie nowych baz danych dzierżawy, ponieważ są one udostępnione.  
* Odzyskiwanie
    * W przypadku, gdy środowisko odzyskiwania skalowane w dół jest używany do zminimalizowania kosztów codziennych, pul i baz danych należy przeskalować w górę do uzyskania pełnej przepustowości w regionie odzyskiwania
    * Włącz nową dzierżawę, inicjowanie obsługi administracyjnej w regionie odzyskiwania tak szybko, jak to możliwe  
    * Być zoptymalizowany pod kątem Przywracanie dzierżaw w kolejności priorytetu
    * Można zoptymalizować w celu uzyskania dzierżaw w trybie online, tak szybko, jak to możliwe, wykonując czynności w sposób równoległy, gdzie jest to praktyczne
    * Jest odporna na uszkodzenia, ponownego uruchamiania oraz idempotentne
    * Możliwe anulować ten proces w środku lotu, jeśli oryginalny region powróci do stanu online.
* Repatriacji 
    * W trybie Failover baz danych z regionu odzyskiwania replik w regionie oryginalnego z minimalnym wpływem na dzierżaw: bez utraty danych i minimalny okres offline na dzierżawę.   

W tym samouczku te problemy zostały rozwiązane, korzystanie z funkcji usługi Azure SQL Database i platforma Azure:

* [Szablony usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template), aby wszystkie potrzebne wydajność rezerwowa w możliwie jak najszybciej. Szablony usługi Azure Resource Manager są używane do obsługi administracyjnej obraz lustrzany serwerów produkcyjnych i pul elastycznych w regionie odzyskiwania.
* [Replikacja geograficzna](sql-database-geo-replication-overview.md), aby utworzyć asynchronicznie replikowane tylko do odczytu pomocniczych baz danych dla wszystkich baz danych. Podczas przestoju przejścia w tryb failover do repliki w regionie odzyskiwania.  Po usunięciu awarii powrotu po awarii do bazy danych w regionie oryginalnym bez utraty danych.
* [Asynchroniczne](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) operacji trybu failover wysyłane w kolejności priorytetu dzierżawy, aby zminimalizować czas pracy awaryjnej w przypadku dużej liczby baz danych.
* [Funkcje odzyskiwania zarządzania fragmentami](sql-database-elastic-database-recovery-manager.md), aby zmienić wpisy w katalogu w bazie danych podczas odzyskiwania i repatriacji. Te funkcje umożliwiają aplikacji nawiązać połączenia z baz danych dzierżaw, niezależnie od lokalizacji bez konieczności ponownej konfiguracji aplikacji.
* [Aliasów DNS serwera SQL](dns-alias-overview.md), aby umożliwić bezproblemowe aprowizowanie nowych dzierżaw, niezależnie od tego, w którym regionie aplikacja działa w. Aliasów DNS są również używane do procesu synchronizacji katalogu active wykazem niezależnie od jego lokalizacji.

## <a name="get-the-disaster-recovery-scripts"></a>Pobierz skrypty odzyskiwania po awarii 

> [!IMPORTANT]
> Podobnie jak wszystkie Wingtip Tickets zarządzania skryptów skrypty odzyskiwania po awarii są jakość próbkowania i nie może być używane w środowisku produkcyjnym. 

Odzyskiwanie skrypty używane w tym samouczku i kod źródłowy aplikacji Wingtip są dostępne w [SaaS o nazwie Wingtip Tickets bazy danych dla każdego dzierżawcy repozytorium GitHub](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/). Zapoznaj się z [ogólne wskazówki dotyczące](saas-tenancy-wingtip-app-guidance-tips.md) instrukcje pobierania i odblokować Wingtip Tickets skrypty zarządzania.

## <a name="tutorial-overview"></a>Omówienie samouczka
W tym samouczku najpierw używasz replikacji geograficznej do tworzenia replik aplikacji Wingtip Tickets i jego bazami danych w innym regionie. Następnie przełączysz do tego regionu, aby zasymulować, odzyskiwanie sprawności po awarii. Po zakończeniu aplikacja jest w pełni funkcjonalne w regionie odzyskiwania.

Później w kroku oddzielne repatriacji przejścia w tryb failover bazy danych wykazu i dzierżawy w regionie odzyskiwania do oryginalnej regionu. Aplikacji i baz danych pozostają dostępne w całej repatriacji. Po zakończeniu aplikacja jest w pełni funkcjonalne w regionie, oryginalnym.

> [!Note]
> Aplikacja jest przywracany do _sparowanym regionie_ regionu, w którym aplikacja jest wdrażana. Aby uzyskać więcej informacji, zobacz [sparowanych regionów platformy Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

## <a name="review-the-healthy-state-of-the-application"></a>Przegląd stanu dobrej kondycji aplikacji

Przed rozpoczęciem procesu odzyskiwania należy przejrzeć normalnego stanu dobrej kondycji aplikacji.
1. W przeglądarce sieci web Otwórz Centrum zdarzeń o nazwie Wingtip Tickets (http://events.wingtip-dpt.&lt; użytkownika&gt;. trafficmanager.net - Zastąp &lt;użytkownika&gt; wartością użytkownika wdrożenia).
    * Przewiń do dołu strony i zwróć uwagę, nazwę serwera wykazu i lokalizację w stopce. Lokalizacja jest region, w którym aplikacja została wdrożona.
    *PORADA: Umieść kursor myszy nad lokalizacją, aby powiększyć wyświetlania. * 
     ![Dobrej kondycji Centrum zdarzeń w regionie oryginalny](media/saas-dbpertenant-dr-geo-replication/events-hub-original-region.png)

2. Kliknij w dzierżawie Contoso Concert Hall, a następnie otwórz jego stronę zdarzeń.
    * W stopce Zwróć uwagę na nazwę serwera dzierżawy. Lokalizacja będzie taka sama jak lokalizacja serwera wykazu.

3. W [witryny Azure portal](https://portal.azure.com), otwórz grupę zasobów, w którym aplikacja jest wdrożona
    * Zwróć uwagę, region, w której są wdrożone serwery. 

## <a name="sync-tenant-configuration-into-catalog"></a>Synchronizacja konfiguracji dzierżawy do katalogu

To zadanie służy do uruchamiania procesu, który synchronizuje konfigurację serwerów, pul elastycznych i baz danych do katalogu dzierżawy. Proces zachowuje te informacje aktualne w wykazie.  Proces odbywa się z katalogiem active w regionie, oryginalnym lub w regionie odzyskiwania. Informacje o konfiguracji jest używany jako część procesu odzyskiwania, aby zapewnić środowisko odzyskiwania jest zgodna z oryginalne środowisko, a następnie później, podczas repatriacji, aby upewnić się, regionu oryginalnego jest spójna ze zmianami wprowadzonymi w środowisko odzyskiwania. Katalog jest również używane do śledzenia informacji o stanie odzyskiwania zasobów dzierżawy

> [!IMPORTANT]
> Dla uproszczenia procesu synchronizacji i inne długotrwałe procesy odzyskiwania i repatriacji są implementowane w tych samouczkach jako zadań programu PowerShell lokalnej lub sesji, uruchamianych w ramach swoich danych logowania użytkownika klienta. Tokeny uwierzytelniania podczas logowania wygasną po upływie kilku godzin i zadań, następnie zakończy się niepowodzeniem. W scenariuszu produkcji długotrwałe procesy powinny zostać wdrożone jako niezawodnych usług platformy Azure pewnego rodzaju działających w ramach jednostki usługi. Zobacz [użyciu programu Azure PowerShell utworzyć nazwę główną usługi za pomocą certyfikatu](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal).

1. W _PowerShell ISE_, otwórz plik Modules\UserConfig.psm1 ...\Learning. Zastąp `<resourcegroup>` i `<user>` w wierszach, 10 i 11 o wartości podanej podczas wdrażania aplikacji.  Zapisz plik!

2. W *PowerShell ISE*, Otwórz ...\Learning skryptu Modules\Business ciągłość działalności biznesowej i Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 po awarii i ustawić:
    * **$DemoScenario = 1**, uruchom zadanie w tle, synchronizującego się z serwerem dzierżaw i puli informacje o konfiguracji do katalogu

3. Naciśnij klawisz **F5** do uruchomienia skryptu synchronizacji. Nowej sesji programu PowerShell jest otwarty do synchronizacji konfiguracji zasobów dzierżawy.
![Proces synchronizacji](media/saas-dbpertenant-dr-geo-replication/sync-process.png)

Pozostaw okno programu PowerShell, uruchomione w tle, a następnie kontynuuj pozostałej części tego samouczka. 

> [!Note]
> Proces synchronizacji łączy do katalogu za pomocą aliasu DNS. Ten alias został zmodyfikowany podczas przywracania i repatriacji, aby wskazywał aktywnego katalogu. Proces synchronizacji przechowuje katalog aktualne z bazy danych lub puli zmiany konfiguracji wprowadzone w regionie odzyskiwania.  Podczas repatriacji zmiany te są stosowane do równoważne zasoby w regionie, oryginalnym.

## <a name="create-secondary-database-replicas-in-the-recovery-region"></a>Tworzenie repliki pomocniczej bazy danych w regionie odzyskiwania

To zadanie służy do uruchamiania procesu, który wdroży wystąpienie aplikacji zduplikowane i replikuje katalogu i wszystkich baz danych dzierżaw w regionie odzyskiwania.

> [!Note]
> W tym samouczku powoduje dodanie ochrony replikacji geograficznej do przykładowej aplikacji Wingtip Tickets. W scenariuszu produkcji dla aplikacji, która korzysta z replikacji geograficznej każdego dzierżawcy może być obsługiwana za pomocą bazy danych replikowanej geograficznie od samego początku. Zobacz [projektowania usług o wysokiej dostępności przy użyciu usługi Azure SQL Database](sql-database-designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)

1. W *PowerShell ISE*, Otwórz ...\Learning skryptu Modules\Business ciągłość działalności biznesowej i Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 po awarii i ustaw następujące wartości:
    * **$DemoScenario = 2**, Utwórz obraz środowiska odzyskiwania, a replikacja bazy danych wykazu i dzierżawy

2. Naciśnij klawisz **F5**, aby uruchomić skrypt. Nowej sesji programu PowerShell zostanie otwarty na utworzenie replik.
![Proces synchronizacji](media/saas-dbpertenant-dr-geo-replication/replication-process.png)  

## <a name="review-the-normal-application-state"></a>Sprawdź stan normalny

W tym momencie aplikacja działa normalnie w regionie, oryginalnym i teraz jest chroniony przez funkcję replikacji geograficznej.  Tylko do odczytu replikach pomocniczych, istnieje w regionie odzyskiwania dla wszystkich baz danych. 

1. W witrynie Azure portal, Przyjrzyj się grupy zasobów i zwróć uwagę, że grupa zasobów została utworzona przy użyciu - sufiks odzyskiwania w regionie odzyskiwania. 

2. Zapoznaj się z zasobami w grupie zasobów odzyskiwania.  

3. Kliknij w bazie danych firmy Contoso Concert Hall _tenants1-dpt -&lt;użytkownika&gt;-odzyskiwania_ serwera.  Po lewej stronie, kliknij na replikację geograficzną. 

    ![Contoso Concert link replikacji geograficznej](media/saas-dbpertenant-dr-geo-replication/contoso-geo-replication.png) 

Mapa regionów platformy Azure Zanotuj łącze replikację geograficzną między główną w regionie, oryginalnym dodatkowym w regionie odzyskiwania.  

## <a name="fail-over-the-application-into-the-recovery-region"></a>Aplikacja w regionie odzyskiwania w trybie Failover

### <a name="geo-replication-recovery-process-overview"></a>Omówienie procesu odzyskiwania replikacji geograficznej

Skryptu odzyskiwania wykonuje następujące zadania:

1. Powoduje wyłączenie punktu końcowego usługi Traffic Manager dla aplikacji sieci web w regionie, oryginalnym. Wyłączenie punktu końcowego uniemożliwia użytkownikom połączenie się z aplikacji w nieprawidłowym stanie powinny regionu oryginalnego trybu online podczas odzyskiwania.

1. Używa wymuszenie trybu failover bazy danych katalogu w regionie odzyskiwania zapewnienie podstawowej bazy danych i aktualizuje _activecatalog_ aliasu, aby wskazywały serwer wykazu odzyskiwania.

1. Aktualizacje _newtenant_ aliasu, aby wskazywały serwer dzierżawy w regionie odzyskiwania. Zmiana tego aliasu gwarantuje, że bazy danych dla nowych dzierżawach są aprowizowane w regionie odzyskiwania. 

1. Oznacza wszystkich dzierżaw istniejących w katalogu odzyskiwania w trybie offline, aby uniemożliwić dostęp do baz danych dzierżaw, zanim przełączone w tryb failover.

1. Aktualizuje konfigurację wszystkich pul elastycznych i replikowane pojedynczych baz danych w regionie odzyskiwania w celu zdublowania ich konfiguracji w regionie, oryginalnym. (To zadanie jest wymagane tylko, jeśli pule lub replikowanych baz danych w środowisku odzyskiwania są skalowane w dół podczas normalnego działania w celu zmniejszenia kosztów).

1. Włącza on punkt końcowy usługi Traffic Manager dla aplikacji sieci web w regionie odzyskiwania. Włączenie tego punktu końcowego umożliwia aplikacjom aprowizowanie nowych dzierżaw. Na tym etapie wciąż w trybie offline to jednak istniejących dzierżawców.

1. Przesyła czy partie żądania w celu wymuszenia pracy awaryjnej bazy danych w kolejności priorytetu.
    * Partie są zorganizowane tak, Aby baz danych są przełączone w tryb failover w sposób równoległy we wszystkich pul.
    * Tryb failover żądania są przesyłane przy użyciu operacji asynchronicznych, dzięki czemu są one przesyłane szybko i wiele żądań, które mogą być przetwarzane jednocześnie.

   > [!Note]
   > W scenariuszu awarii podstawowej bazy danych w regionie, oryginalnym są w trybie offline.  Wymuś tryb failover na dodatkowych podziałów połączenia do podstawowej bez próby zastosowania wszystkich pozostałych umieszczonych w kolejce transakcji. W przypadku testowania odzyskiwania po awarii odzyskiwania po awarii, np. w tym samouczku w przypadku wszelkich działań aktualizacji w czasie pracy w trybie Failover może być utrata danych. Później podczas repatriacji przechodzenia w tryb failover bazy danych w regionie odzyskiwania, oryginalnym regionu normalnego trybu failover służy do upewnij się, że bez utraty danych.

1. Monitoruje usługę bazy danych SQL w celu wykrycia, gdy bazy danych ma zostały przełączone w tryb failover. Gdy bazę danych dzierżawy jest w trybie Failover, aktualizuje katalogu do zapisywania stanu odzyskiwania bazy danych dzierżaw i Oznacz jako online dzierżawy.
    * Baz danych dzierżawy jest możliwy przez aplikację, tak szybko, jak są oznaczone w trybie online w wykazie.
    * Suma wartości rowversion w bazie danych dzierżawy jest przechowywany w katalogu. Ta wartość działa jako odcisku palca, który umożliwia procesu repatriacji określić, jeśli baza danych została zaktualizowana w regionie odzyskiwania.

### <a name="run-the-script-to-fail-over-to-the-recovery-region"></a>Uruchom skrypt, aby przełączyć tryb failover do regionu odzyskiwania

Teraz Wyobraź sobie nastąpi awaria w regionie, w którym aplikacja jest wdrażany i uruchamiany skryptu odzyskiwania:

1. W *PowerShell ISE*, Otwórz ...\Learning skryptu Modules\Business ciągłość działalności biznesowej i Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 po awarii i ustaw następujące wartości:
    * **$DemoScenario = 3**, odzyskanie aplikacji w regionie odzyskiwania przez przechodzenie w tryb failover do repliki

2. Naciśnij klawisz **F5**, aby uruchomić skrypt.  
    * Skrypt zostanie otwarty w nowym oknie programu PowerShell, a następnie rozpoczyna serię zadań programu PowerShell, które są uruchamiane równolegle. Te zadania pracy awaryjnej bazy danych dzierżaw w regionie odzyskiwania.
    * Region odzyskiwania jest _sparowanym regionie_ skojarzone z regionu platformy Azure, w których wdrożono aplikację. Aby uzyskać więcej informacji, zobacz [sparowanych regionów platformy Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

3. Monitoruj stan procesu odzyskiwania, w oknie programu PowerShell.
    ![Procesu pracy awaryjnej](media/saas-dbpertenant-dr-geo-replication/failover-process.png)

> [!Note]
> Aby poznać kod dla zadania odzyskiwania, należy sprawdzić skryptów programu PowerShell w folderze Modules\Business ciągłość działalności biznesowej i awarii Recovery\DR-FailoverToReplica\RecoveryJobs ...\Learning.

### <a name="review-the-application-state-during-recovery"></a>Sprawdź stan aplikacji podczas odzyskiwania

Gdy punkt końcowy aplikacji jest wyłączone w usłudze Traffic Manager, aplikacja jest niedostępna. Po katalogu jest w trybie Failover do regionu odzyskiwania i wszystkich dzierżaw oznaczone w trybie offline, aplikacja zostanie przełączony w tryb online. Mimo że aplikacja jest dostępna, każda dzierżawa pojawia się w trybie offline w Centrum zdarzeń do momentu jego bazy danych jest w trybie Failover. Należy tak zaprojektować aplikację do obsługi baz danych dzierżaw w trybie offline.

1. Po odzyskaniu bazy danych wykazów niezwłocznie odświeżyć w Centrum aplikacji Wingtip Tickets zdarzeń w przeglądarce sieci web.
   * W stopce, zwróć uwagę, że nazwa serwera katalogu ma teraz _-recovery_ sufiks i znajduje się w regionie odzyskiwania.
   * Zwróć uwagę, że dzierżawy, które nie zostały jeszcze przywrócone, są oznaczone jako w trybie offline i nie są można wybierać.  

     > [!Note]
     > Za pomocą tylko kilku baz danych do odzyskania nie można odświeżyć przeglądarkę przed ukończeniem odzyskiwania, więc nie może zostać wyświetlony dzierżawcy, pracując w trybie offline. 
 
     ![Centrum zdarzeń w trybie offline](media/saas-dbpertenant-dr-geo-replication/events-hub-offlinemode.png) 

   * Jeśli strona zdarzenia w trybie offline dzierżawy możesz otworzyć bezpośrednio, wyświetla powiadomienie dzierżawy w trybie offline. Na przykład, jeśli firmy Contoso Concert Hall jest w trybie offline, próby otwarcia http://events.wingtip-dpt.&lt; użytkownika&gt;.trafficmanager.net/contosoconcerthall ![strona firmy Contoso w trybie Offline](media/saas-dbpertenant-dr-geo-replication/dr-in-progress-offline-contosoconcerthall.png) 

### <a name="provision-a-new-tenant-in-the-recovery-region"></a>Aprowizacja nowej dzierżawy w regionie odzyskiwania
Nawet w przypadku, zanim wszystkie istniejące bazy danych dzierżaw zostały przełączone w tryb failover, można udostępnić nowych dzierżaw w regionie odzyskiwania.  

1. W *PowerShell ISE*, Otwórz ...\Learning skryptu Modules\Business ciągłość działalności biznesowej i Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 po awarii i Ustaw następującą właściwość:
    * **$DemoScenario = 4**, aprowizacja nowej dzierżawy w regionie odzyskiwania

2. Naciśnij klawisz **F5** do uruchamiania skryptu i aprowizacja nowej dzierżawy. 

3. Hawthorn Hall zdarzeń zostanie otwarta strona w przeglądarce po jego ukończeniu. Należy pamiętać, ze stopki czy Hawthorn Hall następuje aprowizacja bazy danych w regionie odzyskiwania.
    ![Strona zdarzenia hawthorn Hall](media/saas-dbpertenant-dr-geo-replication/hawthornhallevents.png) 

4. W przeglądarce Odśwież stronę Centrum zdarzeń usługi o nazwie Wingtip Tickets, aby zobaczyć, że uwzględnione Hawthorn Hall. 
    * Aprowizacji Hawthorn Hall bez oczekiwania na innych dzierżawców można przywrócić pozostałych dzierżaw mogą nadal być w trybie offline.


## <a name="review-the-recovered-state-of-the-application"></a>Przejrzyj odzyskane stan aplikacji

Po zakończeniu procesu odzyskiwania wszystkich dzierżaw i aplikacji są w pełni funkcjonalne w regionie odzyskiwania. 

1. Gdy jest wyświetlana w oknie konsoli programu PowerShell oznacza, że wszystkie dzierżawy mogą zostać odzyskane, Odśwież Centrum zdarzeń.  Dzierżawcy będą wyświetlane online, w tym nową dzierżawę, Hawthorn Hall.

    ![odzyskane i nowych dzierżaw w Centrum zdarzeń](media/saas-dbpertenant-dr-geo-replication/events-hub-with-hawthorn-hall.png)

2. W [witryny Azure portal](https://portal.azure.com), Otwórz listę grup zasobów.  
    * Zwróć uwagę, grupy zasobów, która została wdrożona, oraz odzyskiwania grupy zasobów, przy użyciu _-recovery_ sufiks.  Grupa zasobów odzyskiwania zawiera wszystkie zasoby utworzone w procesie odzyskiwania oraz nowych zasobów tworzonych podczas awarii.  

3. Otwórz grupę zasobów odzyskiwania i zwróć uwagę, następujące elementy:
   * Wersje odzyskiwania serwerów wykazu i tenants1, za pomocą _-odzyskiwania_ sufiks.  Przywrócona wykazu i dzierżawy bazy danych na tych serwerach wszystkie mają nazwy używane w regionie, oryginalnym.

   * _Tenants2-dpt -&lt;użytkownika&gt;-recovery_ programu SQL server.  Ten serwer jest używany do aprowizacji nowych dzierżaw w czasie awarii.
   * Usługi App Service o nazwie, _zdarzenia — wingtip-dpt —&lt;recoveryregion&gt;-&lt;użytkownik & gt_; co jest wystąpienie odzyskiwania aplikacji zdarzeń. 

     ![Zasoby usługi Azure recovery](media/saas-dbpertenant-dr-geo-replication/resources-in-recovery-region.png) 
    
4. Otwórz _tenants2-dpt -&lt;użytkownika&gt;-recovery_ programu SQL server.  Zwróć uwagę, znajduje się baza danych _hawthornhall_ oraz puli elastycznej _Pool1_.  _Hawthornhall_ baza danych jest skonfigurowana jako elastycznej bazy danych w _Pool1_ puli elastycznej.

5. Przejdź z powrotem do grupy zasobów i kliknij w bazie danych firmy Contoso Concert Hall _tenants1-dpt -&lt;użytkownika&gt;-odzyskiwania_ serwera. Po lewej stronie, kliknij na replikację geograficzną.
    
    ![Baza danych firmy Contoso, po włączeniu trybu failover](media/saas-dbpertenant-dr-geo-replication/contoso-geo-replication-after-failover.png)

## <a name="change-tenant-data"></a>Zmiana danych dzierżawy 
W tym zadaniu należy zaktualizować jednej z baz danych dzierżawy. 

1. W przeglądarce Znajdź na liście zdarzeń dla firmy Contoso Concert Hall i zanotuj nazwisko zdarzeń.
2. W *PowerShell ISE*, w ...\Learning skryptu Modules\Business ciągłość działalności biznesowej i Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 po awarii, należy ustawić następującą wartość:
    * **$DemoScenario = 5** Usuń zdarzenie z dzierżawy w regionie odzyskiwania
3. Naciśnij klawisz **F5** można wykonać skryptu
4. Odśwież stronę zdarzenia firmy Contoso Concert Hall (http://events.wingtip-dpt.&lt; użytkownika&gt;.trafficmanager.net/contosoconcerthall — zastępuje &lt;użytkownika&gt; wartością użytkownika wdrożenia) i zwróć uwagę, że ostatnie zdarzenie został usunięty.

## <a name="repatriate-the-application-to-its-original-production-region"></a>Wycofywania aplikacji, do jego oryginalnej regionu produkcji

To zadanie repatriates aplikacji do jego oryginalnej regionu. W przypadku rzeczywistych będzie zainicjowana repatriacji, gdy awaria zostanie usunięta.

### <a name="repatriation-process-overview"></a>Omówienie procesu repatriacji

![Architektura repatriacji](media/saas-dbpertenant-dr-geo-replication/repatriation-architecture.png)

Proces repatriacji:
1. Anuluje wszystkie żądania przywracania zaległe lub aktywne bazy danych.
2. Aktualizacje _newtenant_ aliasu, aby wskazywały serwer dzierżawców w regionie pochodzenia. Zmiana tego aliasu gwarantuje, że bazy danych dla nowych dzierżawach teraz będą udostępniane w regionie pochodzenia.
3. Inicjowania inicjuje wszystkie dane zmienione dzierżawy do oryginalnego regionu
4. Kończy się niepowodzeniem w bazach danych dzierżaw w kolejności priorytetów.

Tryb failover skutecznie przenosi bazę danych do oryginalnej regionu. W przypadku awarii za pośrednictwem znajduje się w bazie danych są usuwane wszystkie otwarte połączenia i baza danych jest niedostępne przez kilka sekund. Aplikacje powinny być zapisywane z logikę ponawiania próby, aby upewnić się, że łączą się ponownie.  Mimo że ten krótki rozłączenia jest często nie zauważyć, że istnieje możliwość wycofywania bazy danych poza godzinami. 


### <a name="run-the-repatriation-script"></a>Uruchom skrypt repatriacji
Teraz załóżmy awarii jest rozwiązany i uruchom skrypt repatriacji.

1. W *PowerShell ISE*wśród ...\Learning Modules\Business ciągłość działalności biznesowej i skrypt Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 po awarii.

2. Sprawdź, czy Proces synchronizacji katalogu nadal działa w jego wystąpienia.  Jeśli to konieczne, uruchom go ponownie, ustawiając:
    * **$DemoScenario = 1**, rozpoczęcia synchronizacji z serwerem dzierżaw, puli i informacje o konfiguracji bazy danych do katalogu
    * Naciśnij klawisz **F5**, aby uruchomić skrypt.

3.  Następnie można uruchomić procesu repatriacji, ustaw:
    * **$DemoScenario = 6**, wycofywania aplikacji w jego oryginalnym region
    * Naciśnij klawisz **F5** do uruchomienia skryptu odzyskiwania w nowym oknie programu PowerShell.  Może potrwać kilka minut i mogą być monitorowane w oknie programu PowerShell.
    ![Proces repatriacji](media/saas-dbpertenant-dr-geo-replication/repatriation-process.png)

4. Po uruchomieniu skryptu, Odśwież stronę Centrum zdarzeń (http://events.wingtip-dpt.&lt; użytkownika&gt;. trafficmanager.net)
    * Należy zauważyć, że wszystkie dzierżawy są online i jest dostępny w trakcie tego procesu.

5. Po zakończeniu repatriacji Odśwież Centrum zdarzeń, a następnie otwórz stronę zdarzenia Hawthorn Hall. Należy zauważyć, że ta baza danych ma został zwrócony do kraju macierzystego do oryginalnego regionu.
    ![Centrum zdarzeń, zwrócony do kraju macierzystego](media/saas-dbpertenant-dr-geo-replication/events-hub-repatriated.png)


## <a name="designing-the-application-to-ensure-app-and-database-are-colocated"></a>Projektowanie aplikacji, aby upewnić się, aplikacji i bazy danych są wspólnie przechowywane 
Aplikacja jest zaprojektowana tak, że zawsze nawiązuje połączenie z wystąpieniem, w tym samym regionie co baza danych dzierżawy. Ten projekt zmniejsza opóźnienie między aplikacją a bazą danych. Tego rodzaju optymalizacji przyjęto założenie, że interakcji aplikacji w bazie danych jest chattier niż interakcji aplikacji przez użytkownika.  

Baz danych dzierżawy może rozkładają się na odzyskiwania oraz regiony oryginalnego w przez pewien czas podczas repatriacji. Dla każdej bazy danych aplikacja wyszukuje region, w którym znajduje się bazy danych, wykonując wyszukiwanie DNS, nazwę serwera dzierżawy. W bazie danych SQL nazwa serwera jest aliasem. Nazwa serwera aliasu zawiera nazwę regionu. Jeśli aplikacja nie znajduje się w tym samym regionie co baza danych, zostanie przekierowany do wystąpienia w tym samym regionie co serwer bazy danych.  Trwa przekierowywanie do wystąpienia w tym samym regionie co baza danych minimalizuje opóźnienia między aplikacją a bazą danych. 

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku zawarto informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> 
> * Synchronizacji bazy danych i informacji o konfiguracji puli elastycznej do katalogu dzierżawy
> * Skonfiguruj środowisko odzyskiwania w alternatywnym regionie wchodzących w skład aplikacji, serwerów i pul
> * Użyj _geografickou replikaci_ Replikacja bazy danych wykazu i dzierżawy w regionie odzyskiwania
> * W trybie Failover aplikacji i bazy danych wykazu i dzierżawy w regionie odzyskiwania 
> * Powrót po awarii aplikacji, wykazu i dzierżawy baz danych do oryginalnej regionu po usunięciu awarii

Dowiedz się więcej o technologii Azure SQL database zapewnia ciągłość działania w [omówienie ciągłości działania](sql-database-business-continuity.md) dokumentacji.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Dodatkowe samouczki nawiązujące do aplikacji Wingtip SaaS](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
