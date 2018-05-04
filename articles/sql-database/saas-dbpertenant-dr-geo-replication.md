---
title: Odzyskiwanie po awarii dla aplikacji SaaS przy użyciu replikacja geograficzna bazy danych SQL Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak Użyj geograficznych bazy danych SQL Azure, aby odzyskać wielodostępnych aplikacji SaaS w przypadku awarii
keywords: samouczek usługi sql database
services: sql-database
author: AyoOlubeko
manager: craigg
ms.service: sql-database
ms.custom: saas apps
ms.topic: article
ms.date: 04/09/2018
ms.author: ayolubek
ms.openlocfilehash: 3b2b1b767b26d844046d545e3d587621c5d14995
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="disaster-recovery-for-a-multi-tenant-saas-application-using-database-geo-replication"></a>Odzyskiwanie po awarii dla wielodostępnych aplikacji SaaS przy użyciu replikacja geograficzna bazy danych

W tym samouczku eksplorowania scenariusza odzyskiwania awaryjnego pełny dla wielodostępnych aplikacji SaaS implementowane przy użyciu modelu bazy danych dla dzierżawy. Aby chronić aplikacji po awarii, należy użyć [ _— replikacja geograficzna_ ](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview) do tworzenia repliki dla bazy danych katalogu i dzierżawy w regionie alternatywny odzyskiwania. W przypadku wystąpienia awarii, możesz szybko przełączyć tych replik, aby wznowić operacje biznesowe normalnego. W tryb failover baz danych w oryginalnej region stać się replikach pomocniczych baz danych w regionie odzyskiwania. Po przejściu do trybu online trybu tych replik one automatycznie nadążyć stan bazy danych w regionie odzyskiwania. Po awarii nie zostanie rozwiązany, możesz powrót po awarii do baz danych w oryginalnej regionie produkcji.

W tym samouczku Eksploruje przepływy pracy awaryjnej i powrotu po awarii. Dowiesz się, jak:
> [!div classs="checklist"]

>* Baza danych usługi synchronizacji i informacje o konfiguracji puli elastycznej do katalogu dzierżawcy
>* Konfigurowanie środowiska odzyskiwania w regionie alternatywne, składającej się z aplikacji, serwerów i pule
>* Użyj _— replikacja geograficzna_ replikacji bazy danych katalogu i dzierżawy w regionie odzyskiwania
>* Tryb failover aplikacji i baz danych katalogu i dzierżawy w regionie odzyskiwania 
>* Później, w tryb failover aplikacji, katalogu i dzierżawcy baz danych z powrotem do oryginalnego region po awarii
>* Aktualizuj katalog, jak każda baza danych dzierżawy przeszła w tryb failover do śledzenia lokalizacji głównej bazy danych z każdego dzierżawcy
>* Upewnij się, bazy danych aplikacji i głównej dzierżawy są zawsze kolokowane w tym samym regionie Azure, aby zmniejszyć opóźnienia  
 

Przed rozpoczęciem tego samouczka, upewnij się, że wykonywane są następujące wymagania wstępne:
* Baza danych SaaS biletów Wingtip każdej dzierżawy aplikacji jest wdrożona. Aby wdrożyć w mniej niż 5 minut, zobacz [Wdróż i eksplorowanie Wingtip biletów SaaS bazy danych na aplikację dzierżawy](saas-dbpertenant-get-started-deploy.md)  
* Zainstalowany jest program Azure PowerShell. Aby uzyskać szczegółowe informacje, zobacz [Rozpoczynanie pracy z programem Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-the-geo-replication-recovery-pattern"></a>Wprowadzenie do wzorca odzyskiwania — replikacja geograficzna

![Architektura odzyskiwania](media/saas-dbpertenant-dr-geo-replication/recovery-architecture.png)
 
Odzyskiwania awaryjnego (DR) jest ważną kwestią dla wielu aplikacji, dla zachowania zgodności lub ciągłość prowadzenia działalności biznesowej. Powinno być awaria usługi długimi, dobrze przygotowana planu odzyskiwania po awarii można zminimalizować zakłócenia biznesowych. Replikacja geograficzna zapewnia najmniejsza RPO i RTO dzięki utrzymywaniu repliki bazy danych w regionie odzyskiwania, które można przełączyć się na krótkich Zwróć uwagę.

Plan odzyskiwania po awarii A oparty na — replikacja geograficzna składa się z trzech oddzielnych części:
* Konfiguracja — tworzenie i obsługa środowiska odzyskiwania systemu
* Odzyskiwanie - trybu failover, aplikacji i baz danych w środowisku odzyskiwania w przypadku wystąpienia awarii,
* Repatriacji - trybu failover, aplikacji i baz danych z powrotem do oryginalnego region po usunięciu aplikacji 

Wszystkie części powinny być traktowane ostrożnie, zwłaszcza jeśli działających na dużą skalę. Generalnie plan musi realizację celów kilka:

* Konfiguracja
    * Ustanów i obsługa środowisku lustrzanego w regionie odzyskiwania. Tworzenie elastycznych pul i replikacji żadnych baz danych autonomicznych, w tym środowisku odzyskiwania rezerwuje pojemności w regionie odzyskiwania. Obsługa tego środowiska obejmuje replikowanie nowych baz danych dzierżawy zgodnie z ich udostępnieniu.  
* Odzyskiwanie
    * W przypadku środowiska odzyskiwania skalowany w dół do minimum kosztów codziennych pul i autonomicznej bazy danych musi skalowanie uzyskanie pełnej przepustowości w regionie odzyskiwania
    * Włącz nowej dzierżawy Obsługa administracyjna w regionie odzyskiwania tak szybko, jak to możliwe  
    * Być zoptymalizowany pod kątem przywracania dzierżaw w kolejności priorytetu
    * Być zoptymalizowany pod kątem pobierania dzierżawcy online tak szybko jak to możliwe w przypadku, gdy praktyczne, wykonując kroki równolegle
    * Zapewnienie odporności na uszkodzenia, ponownego uruchamiania, a idempotentności
    * Możliwe anulować proces w locie pośredniej, jeśli oryginalny region powróci do stanu online.
* Repatriacji 
    * Tryb failover baz danych z regionu odzyskiwania z repliki w regionie oryginalnego przy minimalnym wpływie dzierżawcom: nie utraty danych i minimalny okres offline dla każdego dzierżawcy.   

W tym samouczku są opisane te problemy przy użyciu funkcji bazy danych SQL Azure i platformy Azure:

* [Szablony usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template), aby zarezerwować wszystkie wymagane pojemności tak szybko jak to możliwe. Szablony usługi Azure Resource Manager służą do udostępniania odbicie lustrzane serwerów produkcyjnych i pul elastycznych w regionie odzyskiwania.
* [Replikacja geograficzna](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview), aby utworzyć asynchronicznie replikowane tylko do odczytu pomocnicze bazy danych dla wszystkich baz danych. Podczas wystąpienia awarii można przełączyć do replik w regionie odzyskiwania.  Po awarii nie zostanie rozwiązany, możesz powrót po awarii do baz danych w oryginalnej regionie bez utraty danych.
* [Asynchroniczne](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) operacji trybu failover wysyłane w kolejności priorytetu dzierżawy, aby zminimalizować czas pracy awaryjnej dla dużej liczby baz danych.
* [Funkcje odzyskiwania zarządzania niezależnego fragmentu](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-database-recovery-manager), aby zmienić wpisy w katalogu w bazie danych podczas odzyskiwania i repatriacji. Te funkcje umożliwiają aplikacji do nawiązania połączenia bazy danych dzierżawy niezależnie od lokalizacji bez konieczności ponownej konfiguracji aplikacji.
* [Aliasy programu SQL server DNS](https://docs.microsoft.com/azure/sql-database/dns-alias-overview), aby umożliwić bezproblemowego inicjowania obsługi administracyjnej nowych dzierżaw niezależnie od regionu, którego aplikacja działa w. Aliasów DNS są również używane do procesu synchronizacji katalogu nawiązać połączenia z katalogu active niezależnie od jego lokalizacji.

## <a name="get-the-disaster-recovery-scripts"></a>Pobierz skrypty odzyskiwania po awarii 

> [!IMPORTANT]
> Podobnie jak wszystkie bilety Wingtip zarządzania skrypty skrypty DR są przykładowe jakości i nie powinna być używana w środowisku produkcyjnym. 

Odzyskiwanie skryptów używanych w tym samouczku i kodu źródłowego aplikacji Wingtip są dostępne w [SaaS biletów Wingtip bazy danych, dla każdego dzierżawcy repozytorium GitHub](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/). Zapoznaj się z [ogólne wskazówki](saas-tenancy-wingtip-app-guidance-tips.md) dla czynności, aby pobrać i odblokować skrypty zarządzania Wingtip biletów.

## <a name="tutorial-overview"></a>Omówienie samouczka
W tym samouczku najpierw użyć do utworzenia repliki biletów Wingtip aplikacji i jej baz danych w innym regionie — replikacja geograficzna. Następnie możesz przełączyć się do tego regionu, aby symulować odzyskiwanie po awarii. Po zakończeniu aplikacji jest w pełni funkcjonalny w regionie odzyskiwania.

Później w oddzielnych repatriacji kroku, możesz przełączyć bazy danych katalogu i dzierżawy w regionie odzyskiwania do oryginalnej regionu. Aplikacji i baz danych pozostają dostępne w całej repatriacji. Po zakończeniu aplikacji jest w pełni funkcjonalny w oryginalnym regionie.

> [!Note]
> Aplikacja jest odzyskiwana do _sparowanego region_ regionu, w którym aplikacja jest wdrażana. Aby uzyskać więcej informacji, zobacz [Azure łączyć regionów](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

## <a name="review-the-healthy-state-of-the-application"></a>Sprawdź kondycję aplikacji

Przed rozpoczęciem procesu odzyskiwania należy przejrzeć normalnego stanu dobrej kondycji aplikacji.
1. W przeglądarce sieci web Otwórz Centrum zdarzeń biletów Wingtip (http://events.wingtip-dpt.&lt; Użytkownik&gt;. trafficmanager.net - Zastąp &lt;użytkownika&gt; z wartością użytkownika danego wdrożenia).
    * Przewiń w dół strony i zwróć uwagę, nazwę serwera katalogu i lokalizację w stopce. Lokalizacja jest regionu, w którym aplikacja została wdrożona.
    *Porada: Umieść kursor myszy nad lokalizacji, aby powiększyć wyświetlania. * 
     ![Centrum zdarzeń dobrej kondycji w regionie oryginalnej](media/saas-dbpertenant-dr-geo-replication/events-hub-original-region.png)

2. Kliknij dzierżawy Hall porozumieniu Contoso i otwórz jej stronę zdarzeń.
    * W stopce Zwróć uwagę, nazwę serwera dzierżawy. Lokalizacja będzie taka sama jak lokalizacja serwera katalogu.

3. W [portalu Azure](https://portal.azure.com), otwórz grupę, w którym aplikacja jest wdrożona
    * Zwróć uwagę, region, w którym są wdrażane serwerów. 

## <a name="sync-tenant-configuration-into-catalog"></a>Konfiguracji dzierżawy synchronizacji do katalogu

To zadanie służy do uruchamiania procesu, który synchronizuje konfigurację serwerów, pule elastyczne i baz danych do katalogu dzierżawcy. Proces zachowuje te informacje aktualne w katalogu.  Proces działa z katalogu active w regionie oryginalnym lub w regionie odzyskiwania. Informacje o konfiguracji jest używany jako część procesu odzyskiwania, aby zapewnić środowisko odzyskiwania jest zgodna z oryginalne środowisko, a następnie później, podczas repatriacji, aby upewnić się, oryginalny region jest spójna ze zmianami wprowadzonymi w środowisko odzyskiwania. Katalogu jest również używane do śledzenia stanu odzyskiwania zasobów dla dzierżawcy

> [!IMPORTANT]
> Dla uproszczenia procesu synchronizacji i innych długotrwała procesów odzyskiwania i repatriacji są implementowane w tych samouczkach jako lokalnego zadania programu Powershell lub sesji, które Uruchom loginu użytkownika klienta. Tokeny uwierzytelniania, wystawiane, gdy wygaśnie po kilku godzinach logowania i zadania, następnie zakończy się niepowodzeniem. W scenariuszu produkcyjnym procesy długotrwałe powinny zostać wdrożone jako wiarygodnych usług Azure pewnego rodzaju uruchamiania nazwy głównej usługi. Zobacz [użycia programu Azure PowerShell do tworzenia nazwy głównej usługi przy użyciu certyfikatu](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal).

1. W _PowerShell ISE_, otwórz plik Modules\UserConfig.psm1 ...\Learning. Zastąp `<resourcegroup>` i `<user>` w wierszach 10 i 11 o wartości używane w przypadku wdrażania aplikacji.  Zapisz plik!

2. W *PowerShell ISE*, Otwórz ...\Learning skryptu ciągłości Modules\Business i Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 po awarii i ustaw:
    * **$DemoScenario = 1**, uruchom zadanie w tle synchronizowanej serwera dzierżawcy i puli informacje o konfiguracji do katalogu

3. Naciśnij klawisz **F5** do uruchamiania skryptu synchronizacji. Nowej sesji programu PowerShell jest otwarty do synchronizacji konfiguracji zasobów dzierżawy.
![Proces synchronizacji](media/saas-dbpertenant-dr-geo-replication/sync-process.png)

Pozostaw okno programu PowerShell działa w tle i kontynuować pozostałej części samouczka. 

> [!Note]
> Proces synchronizacji nawiązuje połączenie z katalogiem za pomocą aliasu DNS. Ten alias jest modyfikowany podczas przywracania i repatriacji wskaż aktywnego katalogu. Proces synchronizacji zachowuje katalogu aktualne z bazy danych lub puli konfiguracji zmiany wprowadzone w regionie odzyskiwania.  Podczas repatriacji zmiany te są stosowane do równoważne zasoby w regionie oryginalnego.

## <a name="create-secondary-database-replicas-in-the-recovery-region"></a>Tworzenie repliki pomocniczej bazy danych w obszarze odzyskiwania

To zadanie służy do uruchamiania procesu, który wdraża wystąpienia zduplikowane aplikacji i replikuje katalogu i wszystkich dzierżawy baz danych w regionie odzyskiwania.

> [!Note]
> W tym samouczku powoduje dodanie ochrony — replikacja geograficzna biletów Wingtip przykładowej aplikacji. W scenariuszu produkcyjnym dla aplikacji, która używa — replikacja geograficzna każdego dzierżawcy będą udostępniane z replikacją geograficzną bazy danych od samego początku. Zobacz [projektowanie usługi wysokiej dostępności przy użyciu bazy danych SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)

1. W *PowerShell ISE*, Otwórz ...\Learning skryptu ciągłości Modules\Business i Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 po awarii i ustaw następujące wartości:
    * **$DemoScenario = 2**, Utwórz środowisko odzyskiwania odbicie lustrzane i replikacji bazy danych katalogu i dzierżawy

2. Naciśnij klawisz **F5**, aby uruchomić skrypt. Nowej sesji programu PowerShell jest otwarty na utworzenie replik.
![Proces synchronizacji](media/saas-dbpertenant-dr-geo-replication/replication-process.png)  

## <a name="review-the-normal-application-state"></a>Sprawdź stan normalne aplikacji
W tym momencie aplikacji działa normalnie w regionie oryginalnego i teraz jest chroniony przez replikację geograficzną.  Tylko do odczytu replikach pomocniczych, istnieje w regionie odzyskiwania dla wszystkich baz danych. 
1. W portalu Azure, obejrzyj grup zasobów i należy pamiętać, że grupa zasobów została utworzona z - sufiks odzyskiwania w regionie odzyskiwania. 

1. Zapoznaj się z zasobami w grupie zasobów odzyskiwania.  

1. Kliknij w bazie danych Hall porozumieniu Contoso _tenants1-dpt -&lt;użytkownika&gt;-odzyskiwania_ serwera.  Polecenie Replikacja geograficzna po lewej stronie. 

    ![Łącze replikacji geograficznej porozumieniu firmy Contoso](media/saas-dbpertenant-dr-geo-replication/contoso-geo-replication.png) 

Mapa regionów platformy Azure Zanotuj łącze replikacji geograficznej między serwerem podstawowym w regionie oryginalnego i pomocniczej w regionie odzyskiwania.  

## <a name="fail-over-the-application-into-the-recovery-region"></a>Aplikacja do obszaru odzyskiwania w tryb failover

### <a name="geo-replication-recovery-process-overview"></a>Omówienie procesu odzyskiwania — replikacja geograficzna

Skrypt odzyskiwania wykonuje następujące zadania:

1. Powoduje wyłączenie punktu końcowego Menedżera ruchu dla aplikacji sieci web w oryginalnym regionie. Wyłączenie punktu końcowego uniemożliwia użytkownikom łączenie się z aplikacji w nieprawidłowym stanie powinien regionu oryginalnego przejdzie w tryb online podczas odzyskiwania.

1. Używa wymuszenie trybu failover bazy danych katalogu w regionie odzyskiwania dokonanie podstawowej bazy danych i aktualizuje _activecatalog_ alias, aby wskazywały serwer katalogu odzyskiwania.

1. Aktualizacje _newtenant_ alias, aby wskazywały serwer dzierżawy w regionie odzyskiwania. Zmiana tego aliasu zapewnia za bazy danych dla dowolnego nowego dzierżawcy w regionie odzyskiwania. 

1. Oznacza wszystkie dzierżaw istniejących w wykaz odzyskiwania w trybie offline, aby uniemożliwić dostęp do dzierżawy baz danych przed przejścia w tryb failover.

1. Aktualizuje konfigurację wszystkich pul elastycznych i baz danych replikowanych autonomicznego w regionie odzyskiwania ich konfiguracji w regionie oryginalnego dublowanego. (To zadanie jest wymagany tylko jeśli zreplikowanych baz danych w środowisku odzyskiwania lub pule są skalowane w dół podczas wykonywania normalnych operacji, aby zmniejszyć koszty).

1. Umożliwia punktu końcowego Menedżera ruchu dla aplikacji sieci web w regionie odzyskiwania. Włączenie tego punktu końcowego umożliwia aplikacji do obsługi administracyjnej nowych dzierżaw. Na tym etapie nadal w trybie offline to jednak istniejących dzierżawców.

1. Przesyła czy partie żądania do wymuszenia pracy awaryjnej baz danych w kolejności priorytetu.
    * Partie są zorganizowane tak, Aby baz danych są awaryjnie równolegle we wszystkich pulach.
    * Żądania trybu failover są przesyłane przy użyciu operacji asynchronicznych, dzięki czemu są one przesyłane szybko i wiele żądań, które mogą być przetwarzane współbieżnie.

   > [!Note]
   > W scenariuszu awarii podstawowej bazy danych w regionie oryginalnego znajdują się w trybie offline.  Wymuś w trybie Failover na dodatkowej podziały połączenie z podstawowym bez próby zastosowania wszystkich pozostałych umieszczonych w kolejce transakcji. W scenariuszu Przechodzenie do szczegółów DR podobnie jak w tym samouczku Jeśli występuje aktywność aktualizacji w czasie pracy awaryjnej mogą występować utratę danych. Później podczas repatriacji, gdy nie powiedzie się między bazami danych w regionie odzyskiwania oryginalnego regionu normalnej pracy awaryjnej służy do upewnij się, że istnieje nie powoduje utraty danych.

1. Monitoruje usługa bazy danych SQL, aby określić, kiedy baz danych zostały powiodły się za pośrednictwem. Po bazy danych dzierżawy przeszła w tryb failover, aktualizuje katalog, aby zarejestrować stan odzyskiwania bazy danych dzierżawy i oznaczanie dzierżawcą w trybie online.
    * Dzierżawy baz danych są dostępne przez aplikację, jak są oznaczone w wykazie online.
    * Suma wartości rowversion w bazie danych dzierżawy są przechowywane w katalogu. Ta wartość działa jako linii papilarnych, umożliwiający procesu repatriacji, aby określić, jeśli bazy danych został zaktualizowany w regionie odzyskiwania.

### <a name="run-the-script-to-fail-over-to-the-recovery-region"></a>Uruchom skrypt, aby przełączyć się region odzyskiwania

Teraz załóżmy istnieje awaria w regionie, w którym aplikacja jest wdrożona i uruchom skrypt odzyskiwania:

1. W *PowerShell ISE*, Otwórz ...\Learning skryptu ciągłości Modules\Business i Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 po awarii i ustaw następujące wartości:
    * **$DemoScenario = 3**, odzyskiwanie aplikacji do regionu odzyskiwania przez awarii replik

2. Naciśnij klawisz **F5**, aby uruchomić skrypt.  
    * Skrypt zostanie otwarty w nowym oknie programu PowerShell, a następnie uruchamia serię zadań programu PowerShell, które są uruchamiane równolegle. Te zadania w trybie Failover dzierżawy region odzyskiwania baz danych.
    * Region odzyskiwania jest _sparowanego region_ skojarzone z region platformy Azure, w której wdrożono aplikację. Aby uzyskać więcej informacji, zobacz [Azure łączyć regionów](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

3. Monitorowanie stanu procesu odzyskiwania, w oknie programu PowerShell.
    ![Proces trybu failover](media/saas-dbpertenant-dr-geo-replication/failover-process.png)

> [!Note]
> Aby zapoznać się z kodu dla zadania odzyskiwania, przejrzyj skryptów programu PowerShell w folderze ...\Learning ciągłości Modules\Business i Recovery\DR-FailoverToReplica\RecoveryJobs po awarii.

### <a name="review-the-application-state-during-recovery"></a>Sprawdź stan aplikacji podczas odzyskiwania
Gdy punkt końcowy aplikacji jest wyłączone w Menedżerze ruchu, że aplikacja jest niedostępna. Po katalogu przeszła w tryb failover w regionie odzyskiwania i wszystkich dzierżaw oznaczone w trybie offline, aplikacja zostanie przełączony w tryb online. Mimo że aplikacja jest dostępna, każdy dzierżawca pojawi się w trybie offline w Centrum zdarzeń do momentu jego bazy danych nie powiodło się za pośrednictwem. Należy koniecznie Utwórz projekt swojej aplikacji do obsługi baz danych w trybie offline dzierżawy.

1. Natychmiast po odzyskaniu bazy danych katalogu Odśwież Centrum Wingtip biletów zdarzeń w przeglądarce sieci web.
    * W stopce, zwróć uwagę, że nazwa serwera katalogu ma teraz _-odzyskiwania_ sufiks i znajduje się w regionie odzyskiwania.
    * Zwróć uwagę, że dzierżawy, które nie zostały jeszcze przywrócone, nie została oznaczona jako w trybie offline i nie są można wybierać.  

    > [!Note]
    > W przypadku tylko kilka baz danych do odzyskania może nie mieć możliwość odświeżyć przeglądarkę przed odzyskiwania została ukończona, dlatego nie mogą pojawić dzierżawcy, gdy są one w trybie offline. 
 
    ![Centrum zdarzeń w trybie offline](media/saas-dbpertenant-dr-geo-replication/events-hub-offlinemode.png) 

    * Po otwarciu strony zdarzenia dzierżawy w trybie offline bezpośrednio, wyświetla powiadomienie dzierżawy w trybie offline. Na przykład, jeśli Hall porozumieniu Contoso jest w trybie offline, spróbuj otworzyć http://events.wingtip-dpt.&lt; Użytkownik&gt;.trafficmanager.net/contosoconcerthall ![strona firmy Contoso w trybie Offline](media/saas-dbpertenant-dr-geo-replication/dr-in-progress-offline-contosoconcerthall.png) 

### <a name="provision-a-new-tenant-in-the-recovery-region"></a>Udostępnianie nowej dzierżawy w regionie odzyskiwania
Nawet przed wszystkich istniejących dzierżawy niesprawne bazy danych za pośrednictwem, można udostępnić nowych dzierżaw w regionie odzyskiwania.  

1. W *PowerShell ISE*, Otwórz ...\Learning skryptu ciągłości Modules\Business i Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 po awarii i Ustaw następującą właściwość:
    * **$DemoScenario = 4**, obsługi administracyjnej nowej dzierżawy w regionie odzyskiwania

2. Naciśnij klawisz **F5** Uruchom skrypt i udostępnić nową dzierżawę. 

3. Strona zdarzenia Hawthorn Hall otwiera w przeglądarce po jego ukończeniu. Ze stopki Pamiętaj, że Hawthorn Hall bazy danych zostanie zainicjowana w regionie odzyskiwania.
    ![Strona zdarzenia hawthorn Hall](media/saas-dbpertenant-dr-geo-replication/hawthornhallevents.png) 

4. W przeglądarce Odśwież stronę Centrum zdarzeń biletów Wingtip wyświetlić zawarte Hawthorn Hall. 
    * Alokacji Hawthorn Hall bez oczekiwania na innych dzierżawców do przywrócenia innych dzierżawców może nadal być w trybie offline.


## <a name="review-the-recovered-state-of-the-application"></a>Przejrzyj odzyskane stan aplikacji

Po zakończeniu procesu odzyskiwania aplikacji i wszystkich dzierżaw są w pełni funkcjonalne w regionie odzyskiwania. 

1. Gdy wyświetlana w oknie konsoli programu PowerShell oznacza, że wszystkie dzierżaw są odzyskiwane, Odśwież Centrum zdarzeń.  Dzierżawcy będą wyświetlone wszystkie online, łącznie z nowym dzierżawcą Hawthorn Hall.

    ![odzyskane i nowych dzierżaw w Centrum zdarzeń](media/saas-dbpertenant-dr-geo-replication/events-hub-with-hawthorn-hall.png)

2. W [portalu Azure](https://portal.azure.com), Otwórz listę grup zasobów.  
    * Zwróć uwagę, grupy zasobów, która została wdrożona, oraz odzyskiwania grupy zasobów, z _-odzyskiwania_ sufiks.  Grupa zasobów odzyskiwania zawiera wszystkie zasoby utworzone podczas procesu odzyskiwania oraz nowe zasoby utworzone podczas awarii.  

3. Otwórz grupę zasobów, odzyskiwania i zwróć uwagę, następujące elementy:
    * Wersje odzyskiwania serwerów wykazu i tenants1 z _-odzyskiwania_ sufiks.  Przywrócony katalogu i dzierżawcy bazy danych na tych serwerach wszystkie mają nazw używanych w oryginalnym regionie.

    * _Tenants2-dpt -&lt;użytkownika&gt;-odzyskiwania_ programu SQL server.  Ten serwer jest używany do inicjowania obsługi administracyjnej nowych dzierżaw podczas awarii.
    *   O nazwie usługi App Service, _zdarzenia-wingtip-dpt -&lt;recoveryregion&gt;-&lt;gt & użytkownika_; co jest wystąpienie odzyskiwania aplikacji zdarzenia. 

    ![Usługa Azure recovery zasobów ](media/saas-dbpertenant-dr-geo-replication/resources-in-recovery-region.png)    
    
4. Otwórz _tenants2-dpt -&lt;użytkownika&gt;-odzyskiwania_ programu SQL server.  Powiadomienie zawiera bazy danych _hawthornhall_ i puli elastycznej _Pool1_.  _Hawthornhall_ bazy danych jest skonfigurowany jako elastycznej bazy danych w _Pool1_ puli elastycznej.

5. Przejdź z powrotem do grupy zasobów i kliknij w bazie danych Hall porozumieniu Contoso _tenants1-dpt -&lt;użytkownika&gt;-odzyskiwania_ serwera. Polecenie Replikacja geograficzna po lewej stronie.
    
    ![Baza danych firmy Contoso po trybu failover](media/saas-dbpertenant-dr-geo-replication/contoso-geo-replication-after-failover.png)

## <a name="change-tenant-data"></a>Zmień dane dzierżawy 
W tym zadaniu zaktualizuj jednej z baz danych dzierżawy. 

1. W przeglądarce Znajdź na liście zdarzeń dla Hall porozumieniu Contoso i zanotuj nazwisko zdarzeń.
2. W *PowerShell ISE*, w ...\Learning skryptu ciągłości Modules\Business i Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 po awarii, należy ustawić następującą wartość:
    * **$DemoScenario = 5** usunięcia zdarzenia dzierżawy w regionie odzyskiwania
3. Naciśnij klawisz **F5** można wykonać skryptu
4. Odśwież stronę zdarzenia Hall porozumieniu Contoso (http://events.wingtip-dpt.&lt; Użytkownik&gt;.trafficmanager.net/contosoconcerthall - zastępuje &lt;użytkownika&gt; z wartością użytkownika danego wdrożenia) i zwróć uwagę, że ostatnie zdarzenie została usunięta.

## <a name="repatriate-the-application-to-its-original-production-region"></a>Wycofywania aplikacji do jej oryginalnej regionu produkcji

To zadanie repatriates aplikacji do jej oryginalnej regionu. W rzeczywistym scenariuszu będą zainicjować repatriacji po usunięciu awarii.

### <a name="repatriation-process-overview"></a>Omówienie procesu repatriacji

![Architektura repatriacji](media/saas-dbpertenant-dr-geo-replication/repatriation-architecture.png)

Proces repatriacji:
1. Anuluje wszystkie oczekujące lub locie bazy danych żądania przywracania.
2. Aktualizacje _newtenant_ alias, aby wskazywały serwer Umowę w regionie pochodzenia. Zmiana tego aliasu gwarantuje, że bazy danych dla dowolnego nowego dzierżawcy będą teraz udostępniane w regionie pochodzenia.
3. Nasiona zmienione dzierżawy danych do oryginalnej regionu
4. Nie powiedzie się między bazami danych dzierżawy w kolejności priorytetu.

Trybu failover skutecznie przenosi bazy danych do oryginalnej regionu. W przypadku awarii w bazie danych są usuwane wszystkie otwarte połączenia i bazy danych jest niedostępna na kilka sekund. Aplikacje powinny być zapisywane z logikę ponawiania do upewnij się, że łączą się ponownie.  Mimo że często nie jest wystąpieniem tego krótkiego rozłączenia, istnieje możliwość wycofywania baz danych poza godzinami pracy. 


### <a name="run-the-repatriation-script"></a>Uruchom skrypt repatriacji
Teraz załóżmy Wyobraź sobie awarii jest rozwiązany i uruchom skrypt repatriacji.

1. W *PowerShell ISE*, ...\Learning Modules\Business ciągłości i skryptu Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 po awarii.

2. Sprawdź, czy procesu synchronizacji katalogu jest nadal uruchomiona w jego wystąpienie programu PowerShell.  Jeśli to konieczne, uruchom go ponownie, ustawiając:
    * **$DemoScenario = 1**, rozpoczęcia synchronizacji serwera dzierżawy, puli i informacje o konfiguracji bazy danych do katalogu
    * Naciśnij klawisz **F5**, aby uruchomić skrypt.

3.  Następnie można uruchomić procesu repatriacji, należy ustawić:
    * **$DemoScenario = 6**, wycofywania aplikacji w jego oryginalnej region
    * Naciśnij klawisz **F5** do uruchamiania skryptu odzyskiwania w nowym oknie programu PowerShell.  Repatriacji może potrwać kilka minut i mogą być monitorowane w oknie programu PowerShell.
    ![Proces repatriacji](media/saas-dbpertenant-dr-geo-replication/repatriation-process.png)

4. Skrypt jest uruchomiona, Odśwież stronę Centrum zdarzeń (http://events.wingtip-dpt.&lt; Użytkownik&gt;. trafficmanager.net)
    * Zwróć uwagę, że wszystkie dzierżaw są online i jest dostępny w trakcie tego procesu.

5. Po zakończeniu repatriacji Odśwież Centrum zdarzeń i otwórz jej stronę zdarzenia Hawthorn Hall. Należy zauważyć, że ta baza danych została wywożone do oryginalnego regionu.
    ![Zwrócony do kraju macierzystego Centrum zdarzeń](media/saas-dbpertenant-dr-geo-replication/events-hub-repatriated.png)


## <a name="designing-the-application-to-ensure-app-and-database-are-colocated"></a>Projektowanie aplikacji, aby upewnić się, aplikacji i bazy danych są wspólnie przechowywane 
Aplikacja zaprojektowano tak, aby zawsze łączy się z wystąpieniem, w tym samym regionie co baza danych dzierżawy. W tym projekcie zmniejsza opóźnienia między aplikacją a bazy danych. Tego rodzaju optymalizacji przyjęto założenie, że interakcji aplikacji w bazie danych jest chattier niż interakcji aplikacji użytkownika.  

Dzierżawy baz danych mogą rozprzestrzeniać się przez odzyskiwania i regiony oryginalnego przez pewien czas podczas repatriacji. Dla każdej bazy danych aplikacji wyszukuje regionu, w którym baza danych znajduje się w sposób wyszukiwania DNS dla nazwy serwera dzierżawy. W bazie danych SQL nazwa serwera jest alias. Nazwa aliasu serwera zawiera nazwę regionu. Jeśli aplikacja nie jest w tym samym regionie co baza danych, przekierowuje do wystąpienia, w tym samym regionie co serwer bazy danych.  Przekierowanie do wystąpienia, w tym samym regionie co baza danych minimalizuje opóźnienia między aplikacją i bazą danych. 

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku zawarto informacje na temat wykonywania następujących czynności:
> [!div classs="checklist"]

>* Baza danych usługi synchronizacji i informacje o konfiguracji puli elastycznej do katalogu dzierżawcy
>* Konfigurowanie środowiska odzyskiwania w regionie alternatywne, składającej się z aplikacji, serwerów i pule
>* Użyj _— replikacja geograficzna_ replikacji bazy danych katalogu i dzierżawy w regionie odzyskiwania
>* Tryb failover aplikacji i baz danych katalogu i dzierżawy w regionie odzyskiwania 
>* Nastąpił powrót po awarii aplikacji, katalogu i dzierżawy baz danych do oryginalnej region po awarii

Dowiedz się więcej o technologii bazy danych Azure SQL udostępnia umożliwiające ciągłość prowadzenia działalności biznesowej w [omówienie ciągłości działalności biznesowej](sql-database-business-continuity.md) dokumentacji.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Dodatkowe samouczki, które zależą od aplikacji Wingtip SaaS](https://docs.microsoft.com/azure/sql-database/sql-database-wtp-overview#sql-database-wingtip-saas-tutorials)
