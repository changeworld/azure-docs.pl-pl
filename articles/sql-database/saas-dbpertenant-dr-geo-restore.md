---
title: Użyj geograficznie nadmiarowego kopie zapasowe bazy danych SQL Azure dla odzyskiwania po awarii aplikacji SaaS | Dokumentacja firmy Microsoft
description: Dowiedz się, aby odzyskać wielodostępnych aplikacji SaaS w przypadku wystąpienia awarii, użyj geograficznie nadmiarowego kopie zapasowe bazy danych SQL Azure
keywords: samouczek usługi sql database
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: saas apps
ms.topic: conceptual
ms.date: 04/16/2018
ms.author: ayolubek
ms.openlocfilehash: fc28622ad6d2569d455f82a30a84c47647baa1e2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34645338"
---
# <a name="use-geo-restore-to-recover-a-multitenant-saas-application-from-database-backups"></a>Użyj geograficzne, aby odzyskać wielodostępnych aplikacji SaaS z kopii zapasowej bazy danych

W tym samouczku Eksploruje scenariusza odzyskiwania awaryjnego pełny dla wielodostępnych aplikacji SaaS zaimplementowany przy użyciu bazy danych na model dzierżawy. Możesz użyć [geograficzne](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups) pozwoli na odzyskanie automatycznie utrzymywana geograficznie nadmiarowego kopie zapasowe bazy danych katalogu i dzierżawy w region alternatywny odzyskiwania. Po awarii nie zostanie rozwiązany, należy użyć [— replikacja geograficzna](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview) wycofywania zmienione baz danych do ich oryginalnej regionu do.

![Architektura w przypadku przywracania Geo](media/saas-dbpertenant-dr-geo-restore/geo-restore-architecture.png)

Geograficzne jest rozwiązanie do odzyskiwania po awarii najniższy koszt bazy danych SQL Azure. Jednak przywracanie z kopii zapasowej geograficznie nadmiarowego może spowodować utratę danych z maksymalnie jedną godzinę. Można go zająć wiele czasu, zależnie od wielkości każdej bazy danych. 

> [!NOTE]
> Odzyskać aplikacji o najniższym możliwym RPO i RTO przy użyciu — replikacja geograficzna zamiast przywracaniem geograficznym.

W tym samouczku Eksploruje zarówno przywracania i repatriacji przepływów pracy. Omawiane kwestie:
> [!div class="checklist"]

>* Baza danych usługi synchronizacji i informacje o konfiguracji puli elastycznej do katalogu dzierżawcy.
>* Konfigurowanie środowiska odbicie lustrzane w regionie odzyskiwania obejmuje aplikacji, serwerów i pul.   
>* Odzyskać bazy danych katalogu i dzierżawcy, używając przywracaniem geograficznym.
>* Replikacja geograficzna umożliwia wycofywania katalogu dzierżawcy i baz danych dzierżawy zmienione po awarii.
>* Aktualizuj katalog, ponieważ każda baza danych jest przywrócona (lub zwrócony do kraju macierzystego) do śledzenia bieżącą lokalizację kopii aktywnej bazy danych z każdego dzierżawcy.
>* Upewnij się, że aplikacja i dzierżawcy w bazie danych są zawsze kolokowane w tym samym regionie Azure, aby zmniejszyć opóźnienia. 
 

Przed rozpoczęciem tego samouczka, wykonaj następujące wymagania wstępne:
* Wdrażania bazy danych SaaS biletów Wingtip każdej dzierżawy aplikacji. Aby wdrożyć w mniej niż 5 minut, zobacz [Wdróż i eksplorowanie Wingtip biletów SaaS bazy danych na aplikację dzierżawy](saas-dbpertenant-get-started-deploy.md). 
* Zainstaluj program Azure PowerShell. Aby uzyskać więcej informacji, zobacz [wprowadzenie do programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-geo-restore-recovery-pattern"></a>Wprowadzenie do wzorca odzyskiwanie geograficzne

Odzyskiwania awaryjnego (DR) jest ważną kwestią dla wielu aplikacji, dla zachowania zgodności lub ciągłość prowadzenia działalności biznesowej. W przypadku awarii usługa długimi dobrze przygotowana planu odzyskiwania po awarii można zminimalizować zakłócenia biznesowych. Plan odzyskiwania po awarii A oparty na geograficzne musi realizację kilku celów:
 * Wszystkie niezbędne zdolności rezerwowej w regionie odzyskiwania wybranego tak szybko jak to możliwe, aby upewnić się, że jest ona dostępna do przywrócenia bazy danych dzierżawy.
 * Ustanawiania środowiska odzyskiwania odbicie lustrzane odzwierciedlający oryginalna konfiguracja puli i bazy danych. 
 * Umożliwia anulowanie procesu przywracania w locie pośredniej, jeśli oryginalny region wraca do trybu online.
 * Włącz szybkie Inicjowanie obsługi dzięki nowej dzierżawy przy dołączaniu można ponownie uruchomić jak najszybciej dzierżawy.
 * Zoptymalizowana do przywrócenia dzierżaw w kolejności priorytetu.
 * Zoptymalizowana można pobrać dzierżawcy online tak szybko, jak to możliwe w przypadku, gdy praktyczne, wykonując kroki równolegle.
 * Zapewnienie odporności na uszkodzenia, ponownego uruchamiania i idempotentności.
 * Wycofywania baz danych do ich oryginalnej regionu przy minimalnym wpływie dzierżawcom, po usunięciu awarii.  

> [!NOTE]
> Aplikacja jest odzyskiwana do sparowanego regionu, regionu, w którym aplikacja jest wdrażana. Aby uzyskać więcej informacji, zobacz [Azure łączyć regionów](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).   

W tym samouczku używane funkcje bazy danych SQL Azure i platformy Azure, aby rozwiązać te problemy:

* [Szablony usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template), aby zarezerwować wszystkie wymagane pojemności tak szybko jak to możliwe. Szablony usługi Azure Resource Manager służą do udostępniania odbicie lustrzane oryginalnego serwerów i pul elastycznych w regionie odzyskiwania. Oddzielenie serwera i puli są również tworzone dla inicjowania obsługi administracyjnej nowych dzierżaw.
* [Biblioteka klienta usługi elastycznej bazy danych](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-database-client-library) (EDCL), można tworzyć i obsługiwać bazy danych wykaz dzierżawy. Rozszerzone catalog zawiera okresowo odświeżyć informacje o konfiguracji puli i bazy danych.
* [Funkcje odzyskiwania zarządzania niezależnego fragmentu](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-database-recovery-manager) z EDCL, do obsługi bazy danych lokalizacji wpisów w katalogu podczas odzyskiwania i repatriacji.  
* [Geograficzne](https://docs.microsoft.com/azure/sql-database/sql-database-disaster-recovery), aby odzyskać bazy danych katalogu i dzierżawy z geograficznie nadmiarowego automatycznie utrzymywana w kopii zapasowej. 
* [Operacje asynchroniczne przywracania](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations), wysyłane w kolejności priorytetu dzierżawy, są umieszczone w kolejce dla każdej puli przez system i przetwarzane w partiach tak puli nie jest przeciążona. Te operacje mogą zostać anulowane przed lub w trakcie wykonywania, jeśli to konieczne.   
* [Replikacja geograficzna](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview), aby wycofywania baz danych do oryginalnej regionu po awarii. Brak nie utraty danych i minimalny wpływ na dzierżawcy używania — replikacja geograficzna.
* [Aliasy programu SQL server DNS](https://docs.microsoft.com/azure/sql-database/dns-alias-overview), aby umożliwić nawiązanie połączenia z katalogiem active, niezależnie od jego lokalizacji procesu synchronizacji katalogu.  

## <a name="get-the-disaster-recovery-scripts"></a>Pobierz skrypty odzyskiwania po awarii

Skrypty DR używane w tym samouczku są dostępne w [SaaS biletów Wingtip bazy danych, dla każdego dzierżawcy repozytorium GitHub](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant). Zapoznaj się z [ogólne wskazówki](saas-tenancy-wingtip-app-guidance-tips.md) dla czynności, aby pobrać i odblokować skrypty zarządzania Wingtip biletów.

> [!IMPORTANT]
> Podobnie jak wszystkie bilety Wingtip zarządzania skrypty skrypty DR są przykładowe jakości i nie powinna być używana w środowisku produkcyjnym.

## <a name="review-the-healthy-state-of-the-application"></a>Sprawdź kondycję aplikacji
Przed rozpoczęciem procesu odzyskiwania należy przejrzeć normalnego stanu dobrej kondycji aplikacji.

1. W przeglądarce sieci web Otwórz Centrum zdarzeń Wingtip biletów (http://events.wingtip-dpt.&lt; użytkownika&gt;. trafficmanager.net, Zastąp &lt;użytkownika&gt; z wartością użytkownika danego wdrożenia).
    
   Przewiń w dół strony i zwróć uwagę, nazwę serwera katalogu i lokalizację w stopce. Lokalizacja jest regionu, w którym aplikacja została wdrożona.    

   > [!TIP]
   > Umieść kursor myszy nad lokalizacji, aby powiększyć wyświetlania.

   ![Centrum zdarzeń dobrej kondycji w regionie oryginalnej](media/saas-dbpertenant-dr-geo-restore/events-hub-original-region.png)

2. Wybierz dzierżawy Hall porozumieniu Contoso i otwórz jej stronę zdarzeń.

   W stopce Zwróć uwagę, nazwę serwera dzierżawcy. Lokalizacja jest taka sama jak lokalizacja serwera katalogu.

   ![Region oryginalnego Hall porozumieniu firmy Contoso](media/saas-dbpertenant-dr-geo-restore/contoso-original-location.png) 

3. W [portalu Azure](https://portal.azure.com), przejrzyj i otwórz grupę zasobów, w którym aplikacja została wdrożona.

   Zwróć uwagę, zasobów i region wdrożone serwery bazy danych SQL i składniki usługi aplikacji.

## <a name="sync-the-tenant-configuration-into-the-catalog"></a>Synchronizowanie konfiguracji dzierżawy do katalogu

To zadanie służy do uruchamiania procesu synchronizacji konfiguracji serwerów, pule elastyczne i baz danych w katalogu dzierżawcy. Te informacje jest później używane do konfigurowania środowiska odbicie lustrzane w regionie odzyskiwania.

> [!IMPORTANT]
> Dla uproszczenia procesu synchronizacji i innych odzyskiwania długotrwałe repatriacji procesy zostały wdrożone w te przykłady lokalnego zadania programu PowerShell lub sesji, które Uruchom loginu użytkownika klienta. Tokeny uwierzytelniania wystawiony podczas logowania wygasa po kilku godzinach, a następnie powiedzie zadania. W scenariuszu produkcyjnym procesy długotrwałe powinny zostać wdrożone jako wiarygodnych usług Azure pewnego rodzaju uruchamiania nazwy głównej usługi. Zobacz [użycia programu Azure PowerShell do tworzenia nazwy głównej usługi przy użyciu certyfikatu](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal). 

1. W programie PowerShell ISE Otwórz plik Modules\UserConfig.psm1 ...\Learning. Zastąp `<resourcegroup>` i `<user>` w wierszach 10 i 11 o wartości używane w przypadku wdrażania aplikacji. Zapisz plik.

2. W programie PowerShell ISE Otwórz ...\Learning skryptu ciągłości Modules\Business i Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 po awarii.

    W tym samouczku Uruchom poszczególne scenariusze w tym skrypcie programu PowerShell, dlatego nie zamykaj tego pliku.

3. Ustaw następujące wartości:

    $DemoScenario = 1: Uruchom zadanie w tle synchronizowanej serwera dzierżawcy i informacje o konfiguracji puli do katalogu.

4. Aby uruchomić skrypt synchronizacji, wybierz F5. 

    Te informacje jest używana później, aby upewnić się, że recovery tworzy odbicie lustrzane serwerów, pul i baz danych w regionie odzyskiwania.  

    ![Proces synchronizacji](media/saas-dbpertenant-dr-geo-restore/sync-process.png)

Pozostaw okno programu PowerShell działa w tle i kontynuować pozostałej części tego samouczka.

> [!NOTE]
> Proces synchronizacji nawiązuje połączenie z katalogiem za pomocą aliasu DNS. Alias jest modyfikowany podczas przywracania i repatriacji wskaż aktywnego katalogu. Proces synchronizacji będzie zawsze katalogu aktualny i uwzględnia zmiany konfiguracji bazy danych lub puli, wprowadzone w regionie odzyskiwania. Podczas repatriacji zmiany te są stosowane do równoważne zasoby w regionie oryginalnego.

## <a name="geo-restore-recovery-process-overview"></a>Omówienie procesu odzyskiwania geograficzne

Proces odzyskiwania geograficzne wdraża aplikację i przywrócenie bazy danych z kopii zapasowych w regionie odzyskiwania.

Proces odzyskiwania wykonuje następujące czynności:

1. Powoduje wyłączenie punktu końcowego Menedżera ruchu Azure dla aplikacji sieci web w oryginalnym regionie. Wyłączenie punktu końcowego uniemożliwia użytkownikom łączenie się z aplikacji w nieprawidłowym stanie powinien regionu oryginalnego przejdzie w tryb online podczas odzyskiwania.

2. Przepisy a odzyskiwania katalogu serwera w obszarze odzyskiwania przywraca geograficznie bazy danych katalogu i aktualizuje alias activecatalog, aby wskazywały serwer katalogu przywrócone. Zmiana aliasu katalogu zapewnia, że proces synchronizacji katalogu zawsze synchronizowana z katalogu active.

3. Oznacza wszystkie dzierżaw istniejących w wykaz odzyskiwania w trybie offline, aby uniemożliwić dostęp do dzierżawy baz danych, zanim zostaną one przywrócone.

4. Udostępnia wystąpienie aplikacji w regionie odzyskiwania i konfiguruje się go do korzystania z katalogu przywrócone w tym regionie. Aby zachować czas oczekiwania do minimum, przykładowa aplikacja jest przeznaczona do zawsze połączenia z bazą danych dzierżawy, w tym samym regionie.

5. Udostępnia serwer i elastyczna pula przydzielania nowych dzierżaw. Tworzenie tych zasobów gwarantuje, że obsługa administracyjna nowi dzierżawcy nie zakłóca odzyskiwania istniejący dzierżawcy.

6. Aktualizuje nowy alias dzierżawy, aby wskazywały serwer dla nowej dzierżawy baz danych w regionie odzyskiwania. Zmiana tego aliasu zapewnia za bazy danych dla dowolnego nowego dzierżawcy w regionie odzyskiwania.
        
7. Serwery przepisów i pul elastycznych w regionie odzyskiwania do przywrócenia bazy danych dzierżawy. Te serwery i pule są lustrzanego w regionie oryginalnej konfiguracji. Inicjowanie obsługi administracyjnej pul góry rezerwuje zdolności niezbędne do przywrócenia wszystkich baz danych.

    Awaria w regionie może prowadzić do znaczących wykorzystania zasobów w regionie pary. Jeśli użytkownik korzysta geograficzne do odzyskiwania po awarii, a następnie szybko rezerwacji zasobów jest zalecane. Należy wziąć pod uwagę replikację geograficzną Jeśli bardzo ważne jest odzyskanie aplikacji w określonym regionie. 

8. Umożliwia punktu końcowego Menedżera ruchu dla aplikacji sieci web w regionie odzyskiwania. Włączenie tego punktu końcowego umożliwia aplikacji do obsługi administracyjnej nowych dzierżaw. Na tym etapie nadal w trybie offline to jednak istniejących dzierżawców.

9. Przesyła partie żądania przywrócenia baz danych w kolejności priorytetu. 

    * Partie są zorganizowane tak, Aby baz danych są przywracane jednocześnie we wszystkich pulach.  

    * Przywracanie żądań jest przesyłany asynchronicznie, tak aby szybko przesłać i kolejki w celu wykonywania w każdej puli.

    * Ponieważ żądania przywracania są przetwarzane równolegle we wszystkich pul, warto rozpowszechniają ważne dzierżaw wiele pul. 

10. Monitoruje usługi SQL Database, aby określić przywrócenie bazy danych. Po przywróceniu bazy danych dzierżawy jest oznaczony w katalogu w trybie online, a sumę rowversion dla bazy danych dzierżawy jest rejestrowany. 

    * Dzierżawy baz danych są dostępne przez aplikację, jak są oznaczone w wykazie online.

    * Suma wartości rowversion w bazie danych dzierżawy są przechowywane w katalogu. Suma ta działa jako linii papilarnych, który umożliwia procesu repatriacji, aby określić, czy bazy danych został zaktualizowany w regionie odzyskiwania.       

## <a name="run-the-recovery-script"></a>Uruchom skrypt odzyskiwania

> [!IMPORTANT]
> W tym samouczku przywraca baz danych z geograficznie nadmiarowego kopii zapasowych. Chociaż te kopie zapasowe są zwykle dostępne w ciągu 10 minut, może potrwać do godziny. Skrypt wstrzymuje, dopóki nie są one dostępne.

Załóżmy, że istnieje awaria w regionie, w którym aplikacja jest wdrożona i uruchom skrypt odzyskiwania:

1. W programie PowerShell ISE ...\Learning skryptu ciągłości Modules\Business i Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 po awarii, należy ustawić następującą wartość:

    $DemoScenario = 2: Odzyskiwanie aplikacji do regionu odzyskiwania przez Przywracanie z kopii zapasowej geograficznie nadmiarowy.

2. Aby uruchomić skrypt, wybierz F5.  

    * Skrypt zostanie otwarty w nowym oknie programu PowerShell, a następnie uruchamia zestaw zadań programu PowerShell, które są uruchamiane równolegle. Te zadania przywracania serwerów, pul i baz danych do regionu odzyskiwania.

    * Region odzyskiwania jest sparowanego regionu skojarzonego z region platformy Azure, w której wdrożono aplikację. Aby uzyskać więcej informacji, zobacz [Azure łączyć regionów](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

3. Monitorowanie stanu procesu odzyskiwania, w oknie programu PowerShell.

    ![Proces odzyskiwania](media/saas-dbpertenant-dr-geo-restore/dr-in-progress.png)

> [!NOTE]
> Aby zapoznać się z kodu dla zadania odzyskiwania, przejrzyj skryptów programu PowerShell w folderze ...\Learning ciągłości Modules\Business i Recovery\DR-RestoreFromBackup\RecoveryJobs po awarii.

## <a name="review-the-application-state-during-recovery"></a>Sprawdź stan aplikacji podczas odzyskiwania
Gdy punkt końcowy aplikacji jest wyłączone w Menedżerze ruchu, że aplikacja jest niedostępna. Katalog jest przywracany i wszystkich dzierżawców są oznaczone w trybie offline. Punkt końcowy aplikacji w regionie odzyskiwania następnie jest włączona, a aplikacja jest znowu w trybie online. Mimo, że aplikacja jest dostępna, dzierżawcy do momentu przywrócenia ich baz danych są wyświetlane w trybie offline w Centrum zdarzeń. Należy koniecznie Utwórz projekt swojej aplikacji do obsługi baz danych w trybie offline dzierżawy.

* Po została odzyskana baza danych katalogu, ale przed dzierżawcy jest znowu w trybie online, Odśwież biletów Wingtip Centrum zdarzeń w przeglądarce sieci web.

    * W stopce, zwróć uwagę, że nazwa serwera katalogu ma teraz sufiks - odzyskiwania i znajduje się w regionie odzyskiwania.

    * Zwróć uwagę, że dzierżawcami, które nie zostały jeszcze przywrócone została oznaczona jako w trybie offline i nie są można wybierać.   
 
    ![Proces odzyskiwania](media/saas-dbpertenant-dr-geo-restore/events-hub-tenants-offline-in-recovery-region.png)    

    * Po otwarciu strony zdarzenia dzierżawy bezpośrednio, gdy dzierżawa jest w trybie offline, zostaje wyświetlona strona dzierżawy powiadomienie w trybie offline. Na przykład, jeśli Hall porozumieniu Contoso jest w trybie offline, spróbuj otworzyć http://events.wingtip-dpt.&lt; użytkownika&gt;.trafficmanager.net/contosoconcerthall.

    ![Proces odzyskiwania](media/saas-dbpertenant-dr-geo-restore/dr-in-progress-offline-contosoconcerthall.png)

## <a name="provision-a-new-tenant-in-the-recovery-region"></a>Udostępnianie nowej dzierżawy w regionie odzyskiwania
Nawet przed dzierżawy bazy danych zostaną przywrócone, można udostępnić nowych dzierżaw w regionie odzyskiwania. Nowe dzierżawy bazy danych udostępniane w regionie odzyskiwania są zwrócony do kraju macierzystego z odzyskanej bazy danych później.   

1. W programie PowerShell ISE w skrypcie ciągłości Modules\Business i Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 po awarii ...\Learning, Ustaw następującą właściwość:

    $DemoScenario = 3: obsługi administracyjnej nowej dzierżawy w regionie odzyskiwania.

2. Aby uruchomić skrypt, wybierz F5.

3. Strona zdarzenia Hawthorn Hall otwiera w przeglądarce po zakończeniu obsługi. 

    Zwróć uwagę, że Hawthorn Hall baza danych znajduje się w regionie odzyskiwania.

    ![Hall hawthorn udostępniane w regionie odzyskiwania](media/saas-dbpertenant-dr-geo-restore/hawthorn-hall-provisioned-in-recovery-region.png)

4. W przeglądarce Odśwież stronę Centrum zdarzeń Wingtip biletów, tak aby Hawthorn Hall zawarte w temacie. 

    Alokacji Hawthorn Hall bez oczekiwania na innych dzierżawców do przywrócenia innych dzierżawców może nadal być w trybie offline.

## <a name="review-the-recovered-state-of-the-application"></a>Przejrzyj odzyskane stan aplikacji

Po zakończeniu procesu odzyskiwania aplikacji i wszystkich dzierżaw są w pełni funkcjonalne w regionie odzyskiwania. 

1. Po wyświetlana w oknie konsoli programu PowerShell oznacza, że wszystkie dzierżaw są odzyskiwane, Odśwież Centrum zdarzeń. 

    Online, łącznie z nowym dzierżawcą Hawthorn Hall wyświetlane wszystkich dzierżaw.

    ![odzyskane i nowych dzierżaw w Centrum zdarzeń](media/saas-dbpertenant-dr-geo-restore/events-hub-with-hawthorn-hall.png)

2. Kliknij Hall porozumieniu Contoso i otwórz jej stronę zdarzenia. 

    W stopce Zwróć uwagę, że baza danych znajduje się na serwerze odzyskiwania znajduje się w regionie odzyskiwania.

    ![Firmy Contoso w regionie odzyskiwania](media/saas-dbpertenant-dr-geo-restore/contoso-recovery-location.png)

3. W [portalu Azure](https://portal.azure.com), Otwórz listę grup zasobów.  

    Zwróć uwagę, grupy zasobów, która została wdrożona, a także grupy zasobów odzyskiwania, z sufiksem - odzyskiwania. Grupa zasobów odzyskiwania zawiera wszystkie zasoby utworzone podczas procesu odzyskiwania oraz nowe zasoby utworzone podczas awarii. 

4. Otwórz grupę zasobów, odzyskiwania i zwróć uwagę, następujące elementy:

    * Wersje odzyskiwania serwerów wykazu i tenants1 z sufiksem - odzyskiwania. Przywrócony katalogu i dzierżawcy bazy danych na tych serwerach wszystkie mają nazw używanych w oryginalnym regionie.

    * Tenants2-dpt -&lt;użytkownika&gt;— odzyskiwanie programu SQL server. Ten serwer jest używany do inicjowania obsługi administracyjnej nowych dzierżaw podczas awarii.

    * Zdarzenia o nazwie usługi aplikacji — wingtip-dpt -&lt;recoveryregion&gt;-&lt;użytkownika&gt;, który jest wystąpienie odzyskiwania aplikacji zdarzenia.

    ![Zasoby firmy Contoso w regionie odzyskiwania](media/saas-dbpertenant-dr-geo-restore/resources-in-recovery-region.png) 
    
5. Otwórz tenants2-dpt -&lt;użytkownika&gt;— odzyskiwanie programu SQL server. Zwróć uwagę, że zawiera on hawthornhall bazy danych i Pool1 puli elastycznej. Baza danych hawthornhall jest skonfigurowana jako elastycznej bazy danych w puli elastycznej Pool1.

## <a name="change-the-tenant-data"></a>Zmień dane dzierżawy 
W tym zadaniu zaktualizuj jednej dzierżawy przywróconej bazy danych. Kopie procesu repatriacji przywrócić bazy danych, które zostały zmienione w regionie oryginalnego. 

1. W przeglądarce Znajdź na liście zdarzeń dla Hall porozumieniu firmy Contoso, przewijać zdarzenia i zwróć uwagę, ostatnie zdarzenie poważnie Strauss.

2. W programie PowerShell ISE ...\Learning skryptu ciągłości Modules\Business i Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 po awarii, należy ustawić następującą wartość:

    $DemoScenario = 4: usuwanie zdarzenia dzierżawy w regionie odzyskiwania.

3. Można wykonać skryptu, wybierz F5.

4. Odśwież stronę zdarzenia Hall porozumieniu Contoso (http://events.wingtip-dpt.&lt; użytkownika&gt;.trafficmanager.net/contosoconcerthall) i zwróć uwagę, Brak zdarzeń poważnie Strauss.

W tym momencie w samouczku odzyskano aplikacji, która jest teraz uruchomiona w regionie odzyskiwania. Zostały udostępnione nowej dzierżawy w regionie odzyskiwania i modyfikować danych przywróconej dzierżaw.  

> [!NOTE]
> Innych samouczków w próbce nie są przeznaczone do uruchamiania aplikacji w stanie odzyskiwania. Jeśli chcesz zapoznać się z innych samouczków, należy najpierw wycofywania aplikacji.

## <a name="repatriation-process-overview"></a>Omówienie procesu repatriacji

Proces repatriacji aplikacji i jej baz danych do jej oryginalnej regionu zostanie przywrócona po usunięciu awarii.

![Repatriacji geograficzne](media/saas-dbpertenant-dr-geo-restore/geo-restore-repatriation.png) 

Proces:

1. Zatrzymuje wszystkie działania trwającą przywracania i anuluje wszystkie oczekujące lub locie bazy danych żądania przywracania.

2. Uaktywnia ponownie w oryginalnym region dzierżawy baz danych, które nie zostały zmienione od momentu awarii. Te bazy danych należą te nie jest jeszcze przywrócona i te odzyskane, ale nie można zmienić później. Ponownie uaktywnione baz danych są dokładnie ostatnio uzyskiwał dzierżawcom.

3. Inicjuje odbicie lustrzane puli serwerem i elastyczne nowej dzierżawy w oryginalnym regionie. Po zakończeniu tej akcji nowy alias dzierżawy zostało zaktualizowane do punktu z tym serwerem. Aktualizacja aliasu powoduje, że nowe dołączania dzierżawy w regionie oryginalnym zamiast region odzyskiwania.

3. Używa — replikacja geograficzna, aby przenieść katalogu do oryginalnego region z regionu odzyskiwania.

4. Konfigurowanie puli aktualizacji w regionie oryginalnej, aby były zgodne ze zmianami, które zostały wprowadzone w regionie odzyskiwania podczas awarii.

5. Tworzy wymagane serwery i pule host żadnych nowych baz danych utworzonych podczas awarii.

6. Używa — replikacja geograficzna do dnia wszystkich nowych dzierżawy bazy danych udostępniane w czasie awarii i wycofywania przywrócić dzierżawy, które są wykonywane po przywróceniu baz danych, które zostały zaktualizowane. 

7. Czyści zasoby utworzone w regionie odzyskiwania podczas procesu przywracania.

Aby ograniczyć liczbę baz danych dzierżawy, które muszą zostać zwrócony do kraju macierzystego, niezwłocznie są wykonywane kroki od 1 do 3.  

Krok 4 odbywa się tylko, jeśli katalog w regionie odzyskiwania został zmodyfikowany podczas awarii. Katalog jest aktualizowana, jeśli są tworzone nowe dzierżawcy lub żadnej konfiguracji bazy danych lub pula zostanie zmieniona w regionie odzyskiwania.

Jest ważne, czy krok 7 powoduje zakłócenia minimalnego dzierżawcom i nie zostały utracone żadne dane. Na osiągnięcie tego celu, proces używa replikacji geograficznej.

Przed każdą bazę danych replikacją geograficzną, odpowiednia baza danych w oryginalnej region jest usuwany. Bazy danych w regionie odzyskiwania jest następnie replikacją geograficzną, Tworzenie repliki pomocniczej w oryginalnym regionie. Po zakończeniu replikacji dzierżawcy jest oznaczony jako w trybie offline w katalogu, co spowoduje przerwanie wszystkie połączenia z bazą danych w regionie odzyskiwania. Bazy danych jest następnie Failover, powoduje wszelkie oczekujące transakcji do przetwarzania na serwerze pomocniczym, więc żadne dane nie zostały utracone. 

W tryb failover są wycofywane ról bazy danych. Pomocniczy w regionie oryginalnego staje się podstawowej bazy danych do odczytu / zapisu, a bazy danych w regionie odzyskiwania staje się pomocniczego tylko do odczytu. Wpis dzierżawy w katalogu jest aktualizowana w celu odwołania w regionie oryginalnej bazy danych, a dzierżawcy jest oznaczony w trybie online. W tym momencie repatriacji bazy danych zostało ukończone. 

Aplikacje powinny być zapisywane z logiką ponów próbę, aby upewnić się, że łączą się ponownie automatycznie podczas połączenia są uszkodzone. Jeśli korzystają katalogu do broker ponowne połączenie łączą się z repatriated bazy danych w oryginalnej regionie. Mimo że często nie jest wystąpieniem krótki rozłączenia, możesz wybrać wycofywania baz danych poza godzinami pracy.

Po zwrócony do bazy danych jest kraju macierzystego, dodatkowej bazy danych w regionie odzyskiwania mogą zostać usunięte. Bazy danych w oryginalnej regionie wykorzystuje ponownie przywracaniem geograficznym ochrony odzyskiwania po awarii.

W kroku 8 zasobów w regionie odzyskiwania, w tym serwerów odzyskiwania oraz pule, są usuwane.

## <a name="run-the-repatriation-script"></a>Uruchom skrypt repatriacji
Teraz załóżmy awarii jest rozwiązany i uruchom skrypt repatriacji.

Jeśli samouczka zostały wykonane, skrypt natychmiast uaktywnia ponownie klub Jazz Fabrikam i Dojo dereń w regionie oryginalnego ponieważ są one bez zmian. Repatriates następnie nowej dzierżawy Hawthorn Hall i Hall porozumieniu firmy Contoso, ponieważ został on zmodyfikowany. Skrypt repatriates również katalog, który został zaktualizowany po Hawthorn Hall zainicjowano obsługę administracyjną.
  
1. W programie PowerShell ISE w ...\Learning ciągłości Modules\Business i Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 po awarii skryptu Sprawdź procesu synchronizacji katalogu nadal działa w jego wystąpienia. Jeśli to konieczne, uruchom go ponownie, ustawiając:

    $DemoScenario = 1: Rozpocznij synchronizowanie serwera dzierżawy, puli i informacje o konfiguracji bazy danych do katalogu.

    Aby uruchomić skrypt, wybierz F5.

2.  Następnie można uruchomić procesu repatriacji, należy ustawić:

    $DemoScenario = 5: wycofywania aplikacji w jego oryginalnej region.

    Aby uruchomić skrypt odzyskiwania w nowym oknie programu PowerShell, wybierz F5. Repatriacji może zająć kilka minut i mogą być monitorowane w oknie programu PowerShell.

3. Skrypt jest uruchomiona, Odśwież stronę Centrum zdarzeń (http://events.wingtip-dpt.&lt; użytkownika&gt;. trafficmanager.net).

    Zwróć uwagę, że wszystkie dzierżaw są online i jest dostępny w trakcie tego procesu.

4. Wybierz klub Jazz firmy Fabrikam, aby go otworzyć. Jeśli nie zmodyfikujesz tej dzierżawy zawiadomienie stopce, czy serwer jest już przywrócone na oryginalnym serwerze.

5. Otwórz lub Odśwież stronę zdarzenia Hall porozumieniu Contoso. Zwróć uwagę, ze stopki, początkowo bazy danych jest nadal na serwerze - odzyskiwania. 

6. Odśwież po zakończeniu procesu repatriacji strony zdarzenia Hall porozumieniu Contoso i zwróć uwagę, że baza danych jest teraz w danym regionie oryginalnego.

7. Ponownie Odśwież Centrum zdarzeń, a następnie otwórz Hawthorn Hall. Należy zauważyć, że jego bazy danych również znajduje się w regionie oryginalnego. 

## <a name="clean-up-recovery-region-resources-after-repatriation"></a>Oczyszczanie zasobów region odzyskiwania po repatriacji
Po zakończeniu repatriacji jest bezpiecznie usunąć zasoby w regionie odzyskiwania. 

> [!IMPORTANT]
> Te zasoby szybko, aby zatrzymać wszystkie rozliczeń dla nich zostaną usunięte.

Proces przywracania tworzy wszystkie zasoby odzyskiwania w grupie zasobów odzyskiwania. Procesu oczyszczania usuwa tej grupy zasobów i wszystkie odwołania do zasobów z katalogu. 

1. W programie PowerShell ISE w skrypcie ciągłości Modules\Business i Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 po awarii ...\Learning, ustaw:
    
    $DemoScenario = 6: przestarzałe zasoby zostaną usunięte z regionu odzyskiwania.

2. Aby uruchomić skrypt, wybierz F5.

Czyszczenie skryptów, aplikacji po wstecz gdzie jego uruchomienia. W tym momencie możesz ponownie uruchomić skrypt lub wypróbować inne samouczki.

## <a name="designing-the-application-to-ensure-that-the-app-and-the-database-are-co-located"></a>Projektowanie aplikacji w celu zapewnienia wspólnie bazy danych i aplikacji 
Aplikacja jest przeznaczona do zawsze łączyć się z wystąpieniem, w tym samym regionie co dzierżawcy w bazie danych. W tym projekcie zmniejsza opóźnienia między aplikacją a bazy danych. Tego rodzaju optymalizacji przyjęto założenie, że interakcji aplikacji w bazie danych jest chattier niż interakcji aplikacji użytkownika.  

Bazy danych dzierżawy może było ich rozmieszczenie odzyskiwania i regiony oryginalnego przez pewien czas podczas repatriacji. Dla każdej bazy danych aplikacji wyszukuje regionu, w którym baza danych znajduje się w sposób wyszukiwania DNS dla nazwy serwera dzierżawy. W bazie danych SQL nazwa serwera jest alias. Nazwa aliasu serwera zawiera nazwę regionu. Jeśli aplikacja nie jest w tym samym regionie co baza danych, przekierowuje do wystąpienia, w tym samym regionie co serwer bazy danych. Przekierowywanie do wystąpienia, w tym samym regionie co baza danych zmniejsza opóźnienia między bazy danych i aplikacji.  

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]

>* Korzystając z wykazu dzierżawy do przechowywania informacji o konfiguracji okresowo odświeżane, dzięki czemu środowisko odzyskiwania odbicie lustrzane ma zostać utworzony w innym regionie.
>* Odzyskiwać bazy danych Azure SQL w regionie odzyskiwania przy użyciu przywracaniem geograficznym.
>* Zaktualizuj katalog dzierżawy, aby odzwierciedlić lokalizacje bazy danych przywróconej dzierżawy. 
>* Użyj aliasu DNS, aby umożliwić nawiązanie połączenia z katalogiem dzierżawy w całym bez ponownej konfiguracji aplikacji.
>* Replikacja geograficzna umożliwia wycofywania odzyskanej bazy danych do ich oryginalnej regionu po awarii.

Spróbuj [odzyskiwania po awarii dla wielodostępnych aplikacji SaaS przy użyciu replikacji z bazy danych dla georeplikacji](saas-dbpertenant-dr-geo-replication.md) samouczkiem, aby dowiedzieć się, jak na potrzeby znacznie ograniczyć czas potrzebny do odzyskania dużych aplikacji wielodostępnym — replikacja geograficzna.

## <a name="additional-resources"></a>Zasoby dodatkowe

[Dodatkowe samouczki, które zależą od aplikacji Wingtip SaaS](https://docs.microsoft.com/azure/sql-database/sql-database-wtp-overview#sql-database-wingtip-saas-tutorials)
