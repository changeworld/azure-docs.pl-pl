---
title: Odzyskiwanie po awarii dla aplikacji SaaS z replikacją geograficzną
description: Dowiedz się, jak odzyskać wielodostępną aplikację SaaS za pomocą replik geograficznych usługi Azure SQL Database w przypadku awarii
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: AyoOlubeko
ms.author: craigg
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 0668ccf5ceb972dd120e4e3f37be6d879a12d0a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73811706"
---
# <a name="disaster-recovery-for-a-multi-tenant-saas-application-using-database-geo-replication"></a>Odzyskiwanie po awarii dla wielodostępczej aplikacji SaaS przy użyciu replikacji geograficznej bazy danych

W tym samouczku można eksplorować scenariusz odzyskiwania po awarii dla aplikacji SaaS wielu dzierżawców zaimplementowanych przy użyciu modelu bazy danych na dzierżawcę. Aby chronić aplikację przed awarią, należy użyć [_replikacji geograficznej_](sql-database-geo-replication-overview.md) do tworzenia replik dla baz danych katalogu i dzierżawy w alternatywnym regionie odzyskiwania. W przypadku wystąpienia awarii, szybko można awaryjnie do tych replik, aby wznowić normalne operacje biznesowe. W pracy awaryjnej baz danych w oryginalnym regionie stają się repliki pomocnicze baz danych w regionie odzyskiwania. Gdy te repliki wrócić do trybu online automatycznie dogonić stan baz danych w regionie odzyskiwania. Po rozwiązaniu awarii powrót do baz danych w oryginalnym regionie produkcji zostanie powiódł się.

W tym samouczku przeanalizowane są przepływy pracy po awarii i powiększe. Omawiane tematy:
> [!div class="checklist"]
> 
> * Synchronizowanie informacji o konfiguracji bazy danych i puli elastycznej z katalogiem dzierżawy
> * Konfigurowanie środowiska odzyskiwania w alternatywnym regionie obejmującym aplikacje, serwery i pule
> * Replikacja baz danych katalogu i dzierżawy do regionu odzyskiwania za pomocą _replikacji_ geograficznej
> * Przejmuje się w błąd bazy danych aplikacji i katalogu i dzierżawy w regionie odzyskiwania 
> * Później po rozwiązaniu awarii bazy danych aplikacji, katalogu i dzierżawy z powrotem do oryginalnego regionu po rozwiązaniu awarii
> * Aktualizowanie katalogu, ponieważ każda baza danych dzierżawy jest przejęta w celu śledzenia podstawowej lokalizacji bazy danych każdej dzierżawy
> * Upewnij się, że aplikacja i podstawowa baza danych dzierżawy są zawsze współlokowane w tym samym regionie platformy Azure, aby zmniejszyć opóźnienia  
 

Przed rozpoczęciem tego samouczka upewnij się, że zostały ukończone następujące wymagania wstępne:
* Wdrożono bazę danych SaaS Wingtip Tickets na aplikację dzierżawy. Aby wdrożyć w mniej niż pięć minut, zobacz [Wdrażanie i eksplorowanie bazy danych SaaS biletów Wingtip na aplikację dzierżawy](saas-dbpertenant-get-started-deploy.md)  
* Zainstalowany jest program Azure PowerShell. Aby uzyskać szczegółowe informacje, zobacz [Rozpoczynanie pracy z programem Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-the-geo-replication-recovery-pattern"></a>Wprowadzenie do wzorca odzyskiwania replikacji geograficznej

![Architektura odzyskiwania](media/saas-dbpertenant-dr-geo-replication/recovery-architecture.png)
 
Odzyskiwanie po awarii (DR) jest ważnym czynnikiem dla wielu aplikacji, czy ze względu na zgodność lub ciągłości biznesowej. W przypadku długotrwałej przerwy w świadczeniu usług dobrze przygotowany plan odzyskiwania po awarii może zminimalizować zakłócenia w działalności. Za pomocą replikacji geograficznej zapewnia najniższy rpo i rto, utrzymując repliki bazy danych w regionie odzyskiwania, które mogą być awaryjne w krótkim czasie.

Plan odzyskiwania po awarii oparty na replikacji geograficznej składa się z trzech odrębnych części:
* Konfiguracja - tworzenie i konserwacja środowiska odzyskiwania
* Odzyskiwanie — przełączenie w błąd aplikacji i baz danych do środowiska odzyskiwania w przypadku wystąpienia awarii,
* Repatriacja — przełączenie w błąd aplikacji i baz danych z powrotem do oryginalnego regionu po rozwiązaniu aplikacji 

Wszystkie części należy dokładnie rozważyć, zwłaszcza jeśli działają na dużą skalę. Ogólnie rzecz biorąc, plan musi osiągnąć kilka celów:

* Konfiguracja
    * Ustanawianie i obsługa środowiska lustrzanego odbicia w regionie odzyskiwania. Tworzenie pul elastycznych i replikowanie wszystkich baz danych w tym środowisku odzyskiwania rezerwuje pojemność w regionie odzyskiwania. Obsługa tego środowiska obejmuje replikowanie nowych baz danych dzierżawy, ponieważ są one aprowizowane.  
* Odzyskiwanie
    * W przypadku gdy skalowane środowisko odzyskiwania jest używane do minimalizowania codziennych kosztów, pule i bazy danych muszą być skalowane w celu uzyskania pełnej zdolności operacyjnej w regionie odzyskiwania
    * Jak najszybciej włącz aprowicję nowej dzierżawy w regionie odzyskiwania  
    * Optymalizacja pod kątem przywracania najemców w kolejności priorytetowej
    * Bądź zoptymalizowany pod kątem jak najszybszego dostępu najemców do internetu, wykonując kroki równolegle tam, gdzie są praktyczne
    * Odporność na awarie, możliwość ponownego uruchomienia i idempotentność
    * Być możliwe, aby anulować proces w połowie lotu, jeśli oryginalny region wraca on-line.
* Repatriacji 
    * Fail over baz danych z regionu odzyskiwania do replik w oryginalnym regionie z minimalnym wpływem na dzierżawców: brak utraty danych i minimalny okres off-line na dzierżawę.   

W tym samouczku te wyzwania są rozwiązywane przy użyciu funkcji usługi Azure SQL Database i platformy Azure:

* [Szablony usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template), aby zarezerwować wszystkie potrzebne pojemności tak szybko, jak to możliwe. Szablony usługi Azure Resource Manager są używane do inicjowania lustrzanego odbicia serwerów produkcyjnych i pul elastycznych w regionie odzyskiwania.
* [Replikacja geograficzna](sql-database-geo-replication-overview.md), aby utworzyć asynchronicznie replikowane pomocnicze tylko do odczytu dla wszystkich baz danych. Podczas awarii, można awaryjnie do replik w regionie odzyskiwania.  Po rozwiązaniu awarii, można po awarii z powrotem do baz danych w regionie oryginalnym bez utraty danych.
* [Asynchroniczne](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) operacje pracy awaryjnej wysyłane w kolejności priorytetu dzierżawy, aby zminimalizować czas pracy awaryjnej dla dużej liczby baz danych.
* [Funkcje odzyskiwania zarządzania fragmentami](sql-database-elastic-database-recovery-manager.md), aby zmienić wpisy bazy danych w katalogu podczas odzyskiwania i repatriacji. Te funkcje umożliwiają aplikacji łączenie się z bazami danych dzierżawy niezależnie od lokalizacji bez ponownego konfigurowania aplikacji.
* [Aliasy DNS serwera SQL](dns-alias-overview.md), aby umożliwić bezproblemowe inicjowanie obsługi administracyjnej nowych dzierżaw, niezależnie od regionu, w którym aplikacja działa. Aliasy DNS są również używane w celu umożliwienia procesowi synchronizacji katalogu łączenia się z aktywnym katalogiem, niezależnie od jego lokalizacji.

## <a name="get-the-disaster-recovery-scripts"></a>Pobierz skrypty odzyskiwania po awarii 

> [!IMPORTANT]
> Podobnie jak wszystkie skrypty zarządzania bilety Wingtip skrypty odzyskiwania po awarii są jakości próbkowania i nie mają być używane w produkcji. 

Skrypty odzyskiwania używane w tym samouczku i kod źródłowy aplikacji Wingtip są dostępne w [bazie danych Wingtip Tickets SaaS na repozytorium GitHub dzierżawcy](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/). Zapoznaj się z [ogólnymi wskazówkami dotyczącymi](saas-tenancy-wingtip-app-guidance-tips.md) pobierania i odblokowywania skryptów zarządzania biletami Wingtip.

## <a name="tutorial-overview"></a>Omówienie samouczka
W tym samouczku najpierw użyj replikacji geograficznej do tworzenia replik aplikacji Wingtip Tickets i jej baz danych w innym regionie. Następnie można przejść w stan fail over do tego regionu, aby symulować odzyskiwanie po awarii. Po zakończeniu aplikacja jest w pełni funkcjonalna w regionie odzyskiwania.

Później w kroku ponownej repatriacji oddzielne, można awaryjnie katalogu i dzierżawy baz danych w regionie odzyskiwania do oryginalnego regionu. Aplikacja i bazy danych pozostają dostępne przez cały okres repatriacji. Po zakończeniu aplikacja jest w pełni funkcjonalna w oryginalnym regionie.

> [!Note]
> Aplikacja jest odzyskiwana do _sparowanego regionu_ regionu, w którym aplikacja jest wdrażana. Aby uzyskać więcej informacji, zobacz [Sparowane regiony platformy Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

## <a name="review-the-healthy-state-of-the-application"></a>Przejrzyj zdrowy stan aplikacji

Przed rozpoczęciem procesu odzyskiwania, przejrzyj normalny stan dobrej kondycji aplikacji.
1. W przeglądarce sieci Web otwórz Centrum zdarzeńhttp://events.wingtip-dpt.&ltWingtip Tickets Events Hub ( ;user&gt;.trafficmanager.net — zamień &lt;użytkownika&gt; na wartość użytkownika wdrożenia).
    * Przewiń do dołu strony i zwróć uwagę na nazwę serwera katalogu i lokalizację w stopce. Lokalizacja jest regionem, w którym wdrożono aplikację.
    *WSKAZÓWKA: Umieść wskaźnik myszy na lokalizacji, aby powiększyć wyświetlacz.* 
    Centrum zdarzeń w dobrym stanie w pierwotnym ![regionie](media/saas-dbpertenant-dr-geo-replication/events-hub-original-region.png)

2. Kliknij dzierżawę sali koncertowej Contoso i otwórz jej stronę wydarzenia.
    * W stopce zwróć uwagę na nazwę serwera dzierżawy. Lokalizacja będzie taka sama jak lokalizacja serwera katalogu.

3. W [witrynie Azure portal](https://portal.azure.com)otwórz grupę zasobów, w której aplikacja jest wdrażana
    * Zwróć uwagę na region, w którym są wdrażane serwery. 

## <a name="sync-tenant-configuration-into-catalog"></a>Synchronizowanie konfiguracji dzierżawy z katalogiem

W tym zadaniu należy rozpocząć proces, który synchronizuje konfigurację serwerów, pul elastycznych i baz danych z katalogiem dzierżawy. Proces utrzymuje te informacje na bieżąco w katalogu.  Proces działa z aktywnym katalogiem, czy w regionie oryginalnego lub w regionie odzyskiwania. Informacje o konfiguracji są używane jako część procesu odzyskiwania w celu zapewnienia, że środowisko odzyskiwania jest zgodne z oryginalnym środowiskiem, a następnie podczas repatriacji, aby upewnić się, że oryginalny region jest zgodny z wszelkimi zmianami wprowadzonymi w środowiska odzyskiwania. Katalog jest również używany do śledzenia stanu odzyskiwania zasobów dzierżawy

> [!IMPORTANT]
> Dla uproszczenia proces synchronizacji i inne długotrwałe procesy odzyskiwania i repatriacji są implementowane w tych samouczkach jako lokalne zadania programu PowerShell lub sesje uruchamiane w ramach logowania użytkownika klienta. Tokeny uwierzytelniania wystawione podczas logowania wygasną po kilku godzinach, a zadania zakończy się niepowodzeniem. W scenariuszu produkcyjnym długotrwałe procesy powinny być implementowane jako niezawodne usługi platformy Azure pewnego rodzaju, działające w ramach jednostki usługi. Zobacz [Tworzenie jednostki usługi Azure PowerShell za pomocą programu Azure PowerShell z certyfikatem.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal)

1. W programie _PowerShell ISE_otwórz plik ...\Learning Modules\UserConfig.psm1. Zamień `<resourcegroup>` i `<user>` na liniach 10 i 11 z wartością używaną podczas wdrażania aplikacji.  Zapisz plik!

2. W *środowisku ISE programu PowerShell*otwórz skrypt ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 i ustaw:
    * **$DemoScenario = 1**, Uruchom zadanie w tle, które synchronizuje serwer dzierżawy i informacje o konfiguracji puli do katalogu

3. Naciśnij **klawisz F5,** aby uruchomić skrypt synchronizacji. Nowa sesja programu PowerShell jest otwierana w celu synchronizacji konfiguracji zasobów dzierżawy.
![Proces synchronizacji](media/saas-dbpertenant-dr-geo-replication/sync-process.png)

Pozostaw okno programu PowerShell uruchomione w tle i kontynuuj resztę samouczka. 

> [!Note]
> Proces synchronizacji łączy się z katalogiem za pomocą aliasu DNS. Ten alias jest modyfikowany podczas przywracania i repatriacji, aby wskazać aktywny katalog. Proces synchronizacji aktualizuje katalog z dowolnymi zmianami konfiguracji bazy danych lub puli wprowadzonymi w regionie odzyskiwania.  Podczas repatriacji te zmiany są stosowane do równoważnych zasobów w regionie pierwotnym.

## <a name="create-secondary-database-replicas-in-the-recovery-region"></a>Tworzenie replik pomocniczej bazy danych w regionie odzyskiwania

W tym zadaniu należy uruchomić proces, który wdraża wystąpienie zduplikowanej aplikacji i replikuje katalog i wszystkie bazy danych dzierżawy do regionu odzyskiwania.

> [!Note]
> W tym samouczku dodano ochronę replikacji geograficznej do przykładowej aplikacji Wingtip Tickets. W scenariuszu produkcyjnym dla aplikacji, która używa replikacji geograficznej, każdy dzierżawca będzie aprowizowana z replikowanej geograficznie bazy danych od samego początku. Zobacz [Projektowanie usług o wysokiej dostępności przy użyciu bazy danych SQL usługi Azure](sql-database-designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)

1. W programie *PowerShell ISE*otwórz skrypt ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 i ustaw następujące wartości:
    * **$DemoScenario = 2**, Tworzenie środowiska odzyskiwania lustrzanego odbicia i replikowanie baz danych katalogu i dzierżawy

2. Naciśnij klawisz **F5**, aby uruchomić skrypt. Nowa sesja programu PowerShell jest otwierana w celu utworzenia replik.
![Proces synchronizacji](media/saas-dbpertenant-dr-geo-replication/replication-process.png)  

## <a name="review-the-normal-application-state"></a>Przejrzyj normalny stan aplikacji

W tym momencie aplikacja działa normalnie w oryginalnym regionie i teraz jest chroniony przez replikację geograficzną.  Repliki pomocnicze tylko do odczytu istnieją w regionie odzyskiwania dla wszystkich baz danych. 

1. W witrynie Azure portal przyjrzyj się grupom zasobów i zwróć uwagę, że grupa zasobów została utworzona z sufiksem -recovery w regionie odzyskiwania. 

2. Eksploruj zasoby w grupie zasobów odzyskiwania.  

3. Kliknij bazę danych Sali Koncertowej Contoso na serwerze _odzyskiwania dzierżawy1-dpt-&lt;user-recovery.&gt;_  Kliknij na Replikacja geograficzna po lewej stronie. 

    ![Łącze replikacji geograficznej contoso Concert](media/saas-dbpertenant-dr-geo-replication/contoso-geo-replication.png) 

Na mapie regiony platformy Azure zanotuj łącze replikacji geograficznej między podstawową w regionie oryginalnym a pomocniczą w regionie odzyskiwania.  

## <a name="fail-over-the-application-into-the-recovery-region"></a>Przejmuje aplikację w pracy awaryjnej w regionie odzyskiwania

### <a name="geo-replication-recovery-process-overview"></a>Omówienie procesu odzyskiwania replikacji geograficznej

Skrypt odzyskiwania wykonuje następujące zadania:

1. Wyłącza punkt końcowy usługi Traffic Manager dla aplikacji sieci web w oryginalnym regionie. Wyłączenie punktu końcowego uniemożliwia użytkownikom łączenie się z aplikacją w nieprawidłowym stanie, jeśli oryginalny region zostanie w trybie online podczas odzyskiwania.

1. Używa wymuszenia pracy awaryjnej bazy danych wykazu w regionie odzyskiwania, aby uczynić ją podstawową bazą danych, i aktualizuje alias _activecatalog,_ aby wskazać serwer wykazu odzyskiwania.

1. Aktualizuje _alias newtenant,_ aby wskazać serwer dzierżawy w regionie odzyskiwania. Zmiana tego aliasu gwarantuje, że bazy danych dla wszystkich nowych dzierżaw są aprowizowani w regionie odzyskiwania. 

1. Oznacza wszystkich istniejących dzierżawców w katalogu odzyskiwania w trybie offline, aby uniemożliwić dostęp do baz danych dzierżawy, zanim zostaną one przejęte awaryjnie.

1. Aktualizuje konfigurację wszystkich pul elastycznych i replikowanych pojedynczych baz danych w regionie odzyskiwania, aby dublować ich konfigurację w oryginalnym regionie. (To zadanie jest potrzebne tylko wtedy, gdy pule lub replikowane bazy danych w środowisku odzyskiwania są skalowane w dół podczas normalnych operacji w celu zmniejszenia kosztów).

1. Włącza punkt końcowy usługi Traffic Manager dla aplikacji sieci web w regionie odzyskiwania. Włączenie tego punktu końcowego umożliwia aplikacji aprowizowanie nowych dzierżaw. Na tym etapie istniejących dzierżaw są nadal w trybie offline.

1. Przesyła partie żądań, aby wymusić awaryjne bazy danych w kolejności priorytetu.
    * Partie są zorganizowane tak, aby bazy danych były awaryjne równolegle we wszystkich pulach.
    * Żądania pracy awaryjnej są przesyłane przy użyciu operacji asynchronicznych, dzięki czemu są one przesyłane szybko i wiele żądań mogą być przetwarzane jednocześnie.

   > [!Note]
   > W scenariuszu awarii podstawowe bazy danych w oryginalnym regionie są w trybie offline.  Wymuś przełączenie awaryjne w pomocniczym przerywa połączenie z podstawowym bez próby zastosowania żadnych transakcji w kolejce. W scenariuszu dredowania odzyskiwania po awarii, podobnie jak w tym samouczku, jeśli istnieje jakiekolwiek działanie aktualizacji w czasie pracy awaryjnej może wystąpić utrata danych. Później podczas repatriacji, gdy w trybie failover baz danych w regionie odzyskiwania z powrotem do regionu oryginalnego, normalne pracy awaryjnej jest używany w celu zapewnienia, że nie ma utraty danych.

1. Monitoruje usługę bazy danych SQL, aby określić, kiedy bazy danych zostały przejęte awaryjnie. Gdy baza danych dzierżawy jest w trybie failed, aktualizuje katalogu do rejestrowania stanu odzyskiwania bazy danych dzierżawy i oznaczyć dzierżawy jako w trybie online.
    * Bazy danych dzierżawy są dostępne przez aplikację, gdy tylko są one oznaczone w trybie online w katalogu.
    * Suma wartości wierszy w bazie danych dzierżawy jest przechowywana w katalogu. Ta wartość działa jako odcisk palca, który umożliwia procesowi repatriacji, aby ustalić, czy baza danych została zaktualizowana w regionie odzyskiwania.

### <a name="run-the-script-to-fail-over-to-the-recovery-region"></a>Uruchamianie skryptu w celu przebienia awaryjnego w regionie odzyskiwania

Teraz wyobraź sobie, że występuje awaria w regionie, w którym aplikacja jest wdrażana i uruchom skrypt odzyskiwania:

1. W programie *PowerShell ISE*otwórz skrypt ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 i ustaw następujące wartości:
    * **$DemoScenario = 3**, Odzyskiwanie aplikacji do regionu odzyskiwania przez niepowodzenie repliki

2. Naciśnij klawisz **F5**, aby uruchomić skrypt.  
    * Skrypt zostanie otwarty w nowym oknie programu PowerShell, a następnie uruchamia serię zadań programu PowerShell, które są uruchamiane równolegle. Te zadania pracy w pracy awaryjnej dzierżawy baz danych do regionu odzyskiwania.
    * Region odzyskiwania jest _sparowany region_ skojarzony z regionu platformy Azure, w którym wdrożono aplikację. Aby uzyskać więcej informacji, zobacz [Sparowane regiony platformy Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

3. Monitorowanie stanu procesu odzyskiwania w oknie programu PowerShell.
    ![proces pracy awaryjnej](media/saas-dbpertenant-dr-geo-replication/failover-process.png)

> [!Note]
> Aby eksplorować kod zadań odzyskiwania, przejrzyj skrypty programu PowerShell w folderze ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\RecoveryJobs folder.

### <a name="review-the-application-state-during-recovery"></a>Przejrzyj stan aplikacji podczas odzyskiwania

Gdy punkt końcowy aplikacji jest wyłączony w usłudze Traffic Manager, aplikacja jest niedostępna. Po awarii katalogu do regionu odzyskiwania i wszystkich dzierżaw oznaczonych w trybie offline, aplikacja jest przywracana do trybu online. Mimo że aplikacja jest dostępna, każda dzierżawa jest wyświetlana w trybie offline w centrum zdarzeń, dopóki jego baza danych nie zostanie przejęta po awarii. Ważne jest, aby zaprojektować aplikację do obsługi baz danych dzierżawy w trybie offline.

1. Niezwłocznie po odzyskaniu bazy danych katalogu odśwież Centrum zdarzeń Wingtip Tickets Events w przeglądarce sieci Web.
   * W stopce należy zauważyć, że nazwa serwera katalogu ma teraz sufiks _-recovery_ i znajduje się w regionie odzyskiwania.
   * Należy zauważyć, że dzierżawy, które nie zostały jeszcze przywrócone, są oznaczone jako w trybie offline i nie można wybrać.  

     > [!Note]
     > Mając tylko kilka baz danych do odzyskania, może nie być w stanie odświeżyć przeglądarki przed zakończeniem odzyskiwania, więc może nie być widoczny dzierżawy, gdy są one w trybie offline. 
 
     ![Centrum zdarzeń w trybie offline](media/saas-dbpertenant-dr-geo-replication/events-hub-offlinemode.png) 

   * Jeśli otworzysz stronę Zdarzenia dzierżawy w trybie offline bezpośrednio, wyświetla powiadomienie "dzierżawy w trybie offline". Jeśli na przykład sala koncertowa contoso http://events.wingtip-dpt.&ltjest&gt;w trybie ![offline, spróbuj otworzyć stronę ;user .trafficmanager.net/contosoconcerthall Contoso Offline](media/saas-dbpertenant-dr-geo-replication/dr-in-progress-offline-contosoconcerthall.png) 

### <a name="provision-a-new-tenant-in-the-recovery-region"></a>Aprowizowanie nowej dzierżawy w regionie odzyskiwania
Jeszcze zanim wszystkie istniejące bazy danych dzierżawy zostały przejęte awaryjnie, można aprowizować nowych dzierżaw w regionie odzyskiwania.  

1. W programie *PowerShell ISE*otwórz skrypt ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 i ustaw następującą właściwość:
    * **$DemoScenario = 4**, Aprowizowanie nowej dzierżawy w regionie odzyskiwania

2. Naciśnij **klawisz F5,** aby uruchomić skrypt i aprowizować nową dzierżawę. 

3. Strona wydarzeń Hawthorn Hall otwiera się w przeglądarce po jej zakończeniu. Uwaga ze stopki, że baza danych Hawthorn Hall jest aprowizowana w regionie odzyskiwania.
    ![Strona wydarzeń w Hawthorn Hall](media/saas-dbpertenant-dr-geo-replication/hawthornhallevents.png) 

4. W przeglądarce odśwież stronę Centrum zdarzeń biletów Wingtip, aby wyświetlić włączony Hawthorn Hall. 
    * Jeśli aprowizowana Hawthorn Hall bez oczekiwania na innych dzierżaw, aby przywrócić, innych dzierżaw może nadal być w trybie offline.


## <a name="review-the-recovered-state-of-the-application"></a>Przegląd odzyskanego stanu aplikacji

Po zakończeniu procesu odzyskiwania aplikacji i wszystkich dzierżaw są w pełni funkcjonalne w regionie odzyskiwania. 

1. Gdy wyświetlacz w oknie konsoli programu PowerShell wskazuje, że wszystkie dzierżawy są odzyskiwane, odśwież Centrum zdarzeń.  Najemcy pojawią się online, w tym nowy najemca, Hawthorn Hall.

    ![odzyskanych i nowych najemców w centrum wydarzeń](media/saas-dbpertenant-dr-geo-replication/events-hub-with-hawthorn-hall.png)

2. W [witrynie Azure portal](https://portal.azure.com)otwórz listę grup zasobów.  
    * Zwróć uwagę na wdrożoną grupę zasobów oraz grupę zasobów odzyskiwania z sufiksem _-recovery._  Grupa zasobów odzyskiwania zawiera wszystkie zasoby utworzone podczas procesu odzyskiwania oraz nowe zasoby utworzone podczas awarii.  

3. Otwórz grupę zasobów odzyskiwania i zwróć uwagę na następujące elementy:
   * Wersje odzyskiwania serwerów katalogu i tenants1 z sufiksem _-recovery._  Przywrócone bazy danych katalogu i dzierżawy na tych serwerach mają nazwy używane w oryginalnym regionie.

   * Serwer SQL _tenants2-dpt-&lt;user&gt;-recovery._  Ten serwer jest używany do inicjowania obsługi administracyjnej nowych dzierżaw podczas awarii.
   * Usługa app service o _nazwie, events-wingtip-dpt-&lt;recoveryregion&gt;-&lt;user&gt_;, która jest wystąpieniem odzyskiwania aplikacji Zdarzenia. 

     ![Zasoby odzyskiwania platformy Azure](media/saas-dbpertenant-dr-geo-replication/resources-in-recovery-region.png) 
    
4. Otwórz serwer SQL _tenants2-dpt-&lt;user&gt;-recovery._  Zwróć uwagę, że zawiera bazę danych _hawthornhall_ i basen elastyczny, _Pool1_.  Baza danych _hawthornhall_ jest skonfigurowana jako elastyczna baza danych w puli elastycznej _Pool1._

5. Przejdź z powrotem do grupy zasobów i kliknij bazę danych Contoso Concert Hall na serwerze _odzyskiwania&lt;user-recovery&gt;tenants1-dpt._ Kliknij na Replikacja geograficzna po lewej stronie.
    
    ![Baza danych Contoso po przemijaniu awaryjnym](media/saas-dbpertenant-dr-geo-replication/contoso-geo-replication-after-failover.png)

## <a name="change-tenant-data"></a>Zmienianie danych dzierżawy 
W tym zadaniu zaktualizujesz jedną z baz danych dzierżawy. 

1. W przeglądarce znajdź listę zdarzeń dla Sali Koncertowej Contoso i zanotuj nazwę ostatniego zdarzenia.
2. W programie *PowerShell ISE*w skrypcie ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 należy ustawić następującą wartość:
    * **$DemoScenario = 5** Usuwanie zdarzenia z dzierżawy w regionie odzyskiwania
3. Naciśnij **klawisz F5,** aby wykonać skrypt
4. Odśwież stronę zdarzeń salihttp://events.wingtip-dpt.&ltkoncertowej&gt;Contoso ( ;user &lt;&gt; .trafficmanager.net/contosoconcerthall — zastąp użytkownika wartością użytkownika wdrożenia) i zwróć uwagę, że ostatnie zdarzenie zostało usunięte.

## <a name="repatriate-the-application-to-its-original-production-region"></a>Repatriacja aplikacji do pierwotnego regionu produkcyjnego

To zadanie repatriuje aplikację do jego oryginalnego regionu. W rzeczywistym scenariuszu należy zainicjować repatriację po rozwiązaniu awarii.

### <a name="repatriation-process-overview"></a>Omówienie procesu repatriacji

![Architektura repatriacji](media/saas-dbpertenant-dr-geo-replication/repatriation-architecture.png)

Proces repatriacji:
1. Anuluje wszelkie żądania przywracania nierozliczonej bazy danych lub podczas lotu.
2. Aktualizuje _alias newtenant,_ aby wskazać serwer dzierżawców w regionie pochodzenia. Zmiana tego aliasu gwarantuje, że bazy danych dla wszystkich nowych dzierżaw będą teraz aprowizowana w regionie pochodzenia.
3. Nasiona wszelkich zmienionych danych dzierżawy do pierwotnego regionu
4. Awaryjnie przez dzierżawy baz danych w kolejności priorytetu.

Przewija się w błąd skutecznie przenosi bazę danych do oryginalnego regionu. Po zakończeniu pracy bazy danych wszystkie otwarte połączenia są odrzucane, a baza danych jest niedostępna przez kilka sekund. Aplikacje powinny być zapisywane z logiką ponawiania, aby upewnić się, że łączą się ponownie.  Chociaż to krótkie rozłączenie często nie jest zauważone, można wybrać repatriację baz danych poza godzinami pracy. 


### <a name="run-the-repatriation-script"></a>Uruchamianie skryptu repatriacji
Teraz wyobraźmy sobie awarię jest rozwiązany i uruchomić skrypt repatriacji.

1. W *programie PowerShell ISE*w skrypcie ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1.

2. Sprawdź, czy proces synchronizacji wykazu jest nadal uruchomiony w wystąpieniu programu PowerShell.  W razie potrzeby uruchom go ponownie, ustawiając:
    * **$DemoScenario = 1**, Rozpocznij synchronizowanie informacji o konfiguracji serwera dzierżawy, puli i bazy danych w katalogu
    * Naciśnij klawisz **F5**, aby uruchomić skrypt.

3.  Następnie, aby rozpocząć proces repatriacji, ustaw:
    * **$DemoScenario = 6**, Repatriacja aplikacji do pierwotnego regionu
    * Naciśnij **klawisz F5,** aby uruchomić skrypt odzyskiwania w nowym oknie programu PowerShell.  Repatriacja potrwa kilka minut i może być monitorowana w oknie programu PowerShell.
    ![Proces repatriacji](media/saas-dbpertenant-dr-geo-replication/repatriation-process.png)

4. Gdy skrypt jest uruchomiony, odśwież stronęhttp://events.wingtip-dpt.&ltCentrum&gt;zdarzeń ( ;user .trafficmanager.net)
    * Należy zauważyć, że wszystkie dzierżawy są online i dostępne w trakcie tego procesu.

5. Po zakończeniu repatriacji odśwież centrum wydarzenia i otwórz stronę wydarzeń dla Hawthorn Hall. Należy zauważyć, że ta baza danych została repatriowana do oryginalnego regionu.
    ![Repatriacja centrum zdarzeń](media/saas-dbpertenant-dr-geo-replication/events-hub-repatriated.png)


## <a name="designing-the-application-to-ensure-app-and-database-are-colocated"></a>Projektowanie aplikacji w celu zapewnienia współlokowania aplikacji i bazy danych 
Aplikacja jest zaprojektowana tak, aby zawsze łączy się z wystąpienia w tym samym regionie co baza danych dzierżawy. Ten projekt zmniejsza opóźnienia między aplikacją a bazą danych. Ta optymalizacja zakłada, że interakcja między aplikacjami a bazą danych jest bardziej rozmowna niż interakcja między użytkownikiem a aplikacją.  

Bazy danych dzierżawy mogą być rozłożone na odzyskiwanie i oryginalnych regionów przez pewien czas podczas repatriacji. Dla każdej bazy danych aplikacja wyszukuje region, w którym znajduje się baza danych, wykonując wyszukiwanie DNS na nazwę serwera dzierżawy. W bazie danych SQL nazwa serwera jest aliasem. Nazwa serwera aliasowanego zawiera nazwę regionu. Jeśli aplikacja nie znajduje się w tym samym regionie co baza danych, przekierowuje do wystąpienia w tym samym regionie co serwer bazy danych.  Przekierowanie do wystąpienia w tym samym regionie co baza danych minimalizuje opóźnienia między aplikacją a bazą danych. 

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> 
> * Synchronizowanie informacji o konfiguracji bazy danych i puli elastycznej z katalogiem dzierżawy
> * Konfigurowanie środowiska odzyskiwania w alternatywnym regionie obejmującym aplikacje, serwery i pule
> * Replikacja baz danych katalogu i dzierżawy do regionu odzyskiwania za pomocą _replikacji_ geograficznej
> * Przejmuje się w błąd bazy danych aplikacji i katalogu i dzierżawy w regionie odzyskiwania 
> * Po awarii powiększe bazy danych aplikacji, katalogu i dzierżawy do oryginalnego regionu po rozwiązaniu awarii

Więcej informacji na temat technologii, które zapewnia baza danych SQL platformy Azure, można znaleźć w dokumentacji [omówienia ciągłości](sql-database-business-continuity.md) biznesowej.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Dodatkowe samouczki, które opierają się na aplikacji Wingtip SaaS](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
