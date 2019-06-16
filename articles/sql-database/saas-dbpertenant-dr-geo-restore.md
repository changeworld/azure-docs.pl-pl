---
title: 'Aplikacje SaaS: Baza danych SQL geograficznie nadmiarowych kopii zapasowych Azure do odzyskiwania po awarii | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak użyć geograficznie nadmiarowych kopii zapasowych usługi Azure SQL Database, aby odzyskać wielodostępnych aplikacji SaaS w przypadku awarii
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
ms.date: 01/14/2019
ms.openlocfilehash: c96f2dc2b44ea2118d9f0dd6c988017efcba5800
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60557073"
---
# <a name="use-geo-restore-to-recover-a-multitenant-saas-application-from-database-backups"></a>Funkcja przywracania geograficznego umożliwia odzyskiwanie wielodostępnych aplikacji SaaS z kopii zapasowych bazy danych

W tym samouczku przedstawiono scenariusza odzyskiwania awaryjnego pełny dla wielodostępnych aplikacji SaaS implementowana przy użyciu bazy danych dla każdego modelu dzierżawy. Możesz użyć [geoprzywracanie](sql-database-recovery-using-backups.md) do odzyskania bazy danych wykazu i dzierżawy z automatycznego zapewnienia geograficznie nadmiarowych kopii zapasowych w region odzyskiwanie do alternatywnej. Po awarii nie zostanie rozwiązany, należy użyć [geografickou replikaci](sql-database-geo-replication-overview.md) wycofywania zmienione baz danych do ich oryginalnej regionu do.

![Architektura w przypadku przywracania geograficznego](media/saas-dbpertenant-dr-geo-restore/geo-restore-architecture.png)

Funkcja przywracania geograficznego jest rozwiązanie odzyskiwania po awarii najniższy koszt usługi Azure SQL Database. Jednak przywracaniu z geograficznie nadmiarowych kopii zapasowych może spowodować utratę danych do jednej godziny. Może upłynąć pewien czasu w zależności od rozmiaru każdej bazy danych. 

> [!NOTE]
> Odzyskiwanie aplikacji o najniższym możliwym cel punktu odzyskiwania i cel czasu odzyskiwania przy użyciu replikacji geograficznej zamiast przywracania geograficznego.

W tym samouczku przedstawiono przywracania i repatriacji przepływów pracy. Omawiane kwestie:
> [!div class="checklist"]
> 
> * Synchronizacji bazy danych i informacji o konfiguracji puli elastycznej do katalogu dzierżawy.
> * Konfigurowanie środowiska odbicie lustrzane w regionie odzyskiwania, który zawiera aplikacji, serwerów i pul.   
> * Odzyskiwanie bazy danych wykazu i dzierżawy przy użyciu przywracania geograficznego.
> * Replikacja geograficzna umożliwia wycofywania katalogu dzierżawy i baz danych dzierżaw zmienione po usunięciu awarii.
> * Katalog aktualizacji, ponieważ każda baza danych przywrócona (lub zwrócony do kraju macierzystego) do śledzenia bieżącą lokalizację kopii aktywnej bazy danych z każdej dzierżawy.
> * Upewnij się, że aplikacji i baza danych dzierżawy są zawsze kolokowane w tym samym regionie platformy Azure w celu zmniejszenia opóźnienia. 
 

Przed rozpoczęciem tego samouczka należy spełnić następujące wymagania wstępne:
* Wdrażanie bazy danych SaaS o nazwie Wingtip Tickets dzierżawy aplikacji. Aby wdrożyć w mniej niż pięć minut, zobacz [wdrażanie i eksplorowanie SaaS o nazwie Wingtip Tickets bazy danych dla każdego dzierżawcy aplikacji](saas-dbpertenant-get-started-deploy.md). 
* Zainstaluj program Azure PowerShell. Aby uzyskać więcej informacji, zobacz [wprowadzenie do programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-geo-restore-recovery-pattern"></a>Wprowadzenie do wzorca odzyskiwania chronionej przywracania geograficznego

Odzyskiwanie po awarii (DR) jest ważną kwestią w przypadku wielu aplikacji dla zachowania zgodności lub ciągłość prowadzenia działalności biznesowej. W przypadku awarii usługi długotrwały dobrze przygotowany plan odzyskiwania po awarii, można zminimalizować zakłócenia działania firmy. Plan odzyskiwania po awarii A oparty na przywracania geograficznego musi osiągnąć cele kilka:
 * Zarezerwuj wszystkie niezbędne pojemności w regionie odzyskiwania wybranej tak szybko, jak to możliwe, aby upewnić się, że jest ona dostępna do przywrócenia baz danych dzierżaw.
 * Ustanów środowisko odzyskiwania obraz odzwierciedlający oryginalną konfigurację puli i bazy danych. 
 * Umożliwia anulowanie procesu przywracania w środku locie, jeśli oryginalny region powróci do trybu online.
 * Włącz dzierżawy szybkiego udostępniania, więc nowej dzierżawy przy dołączaniu można uruchomić ponownie tak szybko, jak to możliwe.
 * Można zoptymalizować, aby przywrócić dzierżaw w kolejności priorytetów.
 * Można zoptymalizować uzyskanie dzierżawy w trybie online tak szybko, jak to możliwe, wykonując czynności w sposób równoległy, gdzie jest to praktyczne.
 * Być odporna na uszkodzenia, ponownego uruchamiania oraz idempotentne.
 * Wycofywania baz danych do ich oryginalnej regionu z minimalnym wpływem na dzierżaw, po usunięciu awarii.  

> [!NOTE]
> Aplikacja jest odzyskiwana na sparowanym regionie regionu, w którym aplikacja jest wdrażana. Aby uzyskać więcej informacji, zobacz [sparowanych regionów platformy Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).   

W tym samouczku używa funkcji usługi Azure SQL Database i platforma Azure, aby rozwiązać te problemy:

* [Szablony usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template), aby wszystkie potrzebne wydajność rezerwowa w możliwie jak najszybciej. Szablony usługi Azure Resource Manager są używane do obsługi administracyjnej obraz lustrzany oryginalnego serwerów i pul elastycznych w regionie odzyskiwania. Oddzielenie serwera i puli, również są tworzone dla aprowizację nowych dzierżaw.
* [Biblioteka kliencka elastic Database](sql-database-elastic-database-client-library.md) EDCL (), aby tworzyć i obsługiwać wykaz baz danych dzierżawy. Wykaz rozszerzonych obejmuje okresowo odświeżane informacje o konfiguracji puli i bazy danych.
* [Funkcje odzyskiwania zarządzania fragmentami](sql-database-elastic-database-recovery-manager.md) z biblioteki EDCL, aby zachować wpisy lokalizacji bazy danych w katalogu podczas odzyskiwania i repatriacji.  
* [Przywracanie geograficzne](sql-database-disaster-recovery.md), aby odzyskać bazy danych wykazu i dzierżawy z automatycznego zapewnienia geograficznie nadmiarowych kopii zapasowych. 
* [Operacje asynchroniczne przywracania](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations), wysyłane w kolejności priorytetu dzierżawy, są umieszczane w kolejce dla każdej puli przez system i przetwarzane w partiach, więc puli nie jest przeciążona. Te operacje mogą zostać anulowane, przed lub w trakcie wykonywania, jeśli to konieczne.   
* [Replikacja geograficzna](sql-database-geo-replication-overview.md), aby wycofywania baz danych do oryginalnej regionu po awarii. Istnieje nie utraty danych i minimalny wpływ na dzierżawy podczas replikacji geograficznej.
* [Aliasów DNS serwera SQL](dns-alias-overview.md), aby zezwolić procesu synchronizacji katalogu active wykazem niezależnie od jego lokalizacji.  

## <a name="get-the-disaster-recovery-scripts"></a>Pobierz skrypty odzyskiwania po awarii

Skrypty odzyskiwania po awarii, w tym samouczku używane są dostępne w [SaaS o nazwie Wingtip Tickets bazy danych dla każdego dzierżawcy repozytorium GitHub](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant). Zapoznaj się z [ogólne wskazówki dotyczące](saas-tenancy-wingtip-app-guidance-tips.md) instrukcje pobierania i odblokować Wingtip Tickets skrypty zarządzania.

> [!IMPORTANT]
> Podobnie jak wszystkie Wingtip Tickets zarządzania skryptów skrypty odzyskiwania po awarii są jakość próbkowania i nie może być używane w środowisku produkcyjnym.

## <a name="review-the-healthy-state-of-the-application"></a>Przegląd stanu dobrej kondycji aplikacji
Przed rozpoczęciem procesu odzyskiwania należy przejrzeć normalnego stanu dobrej kondycji aplikacji.

1. W przeglądarce sieci web Otwórz Centrum zdarzeń o nazwie Wingtip Tickets (http://events.wingtip-dpt.&lt ; użytkownika&gt;. trafficmanager.net, Zastąp &lt; użytkownika&gt; wartością użytkownika wdrożenia).
    
   Przewiń do dołu strony i zwróć uwagę, nazwę serwera wykazu i lokalizację w stopce. Lokalizacja jest region, w którym aplikacja została wdrożona.    

   > [!TIP]
   > Umieść kursor myszy nad lokalizacją, aby powiększyć wyświetlania.

   ![Centrum zdarzeń dobrej kondycji w regionie oryginalny](media/saas-dbpertenant-dr-geo-restore/events-hub-original-region.png)

2. Wybierz dzierżawę firmy Contoso Concert Hall, a następnie otwórz jego stronę zdarzeń.

   W stopce Zwróć uwagę, nazwę serwera dzierżawcy. Lokalizacja jest taka sama jak lokalizacja serwera wykazu.

   ![Firmy Contoso Concert Hall oryginalnego regionu](media/saas-dbpertenant-dr-geo-restore/contoso-original-location.png) 

3. W [witryny Azure portal](https://portal.azure.com), przejrzyj i otwórz grupę zasobów, w którym aplikacja została wdrożona.

   Zwróć uwagę, zasobów i region, wdrożyć składniki usługi aplikacji i serwery usługi SQL Database.

## <a name="sync-the-tenant-configuration-into-the-catalog"></a>Synchronizacja z konfiguracją dzierżawy do katalogu

To zadanie służy do uruchamiania procesu synchronizacji konfiguracji serwerów, pul elastycznych i baz danych do katalogu dzierżawy. Informacja ta jest używany później skonfigurować środowisko odbicie lustrzane w regionie odzyskiwania.

> [!IMPORTANT]
> Dla uproszczenia procesu synchronizacji i innych odzyskiwania długoterminowych repatriacji procesy są implementowane w tych przykładach jako zadań programu PowerShell lokalnej lub sesji, uruchamianych w ramach swoich danych logowania użytkownika klienta. Tokeny uwierzytelniania wydane po zalogowaniu wygasają po upływie kilku godzin, a zadania, następnie zakończy się niepowodzeniem. W scenariuszu produkcji długotrwałe procesy powinny zostać wdrożone jako niezawodnych usług platformy Azure pewnego rodzaju działających w ramach jednostki usługi. Zobacz [użyciu programu Azure PowerShell utworzyć nazwę główną usługi za pomocą certyfikatu](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal). 

1. W programie PowerShell ISE Otwórz plik Modules\UserConfig.psm1 ...\Learning. Zastąp `<resourcegroup>` i `<user>` w wierszach, 10 i 11 o wartości podanej podczas wdrażania aplikacji. Zapisz plik.

2. Otwórz ...\Learning skryptu Modules\Business ciągłość działalności biznesowej i Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 po awarii w środowisku PowerShell ISE.

    W ramach tego samouczka możesz uruchamiać scenariusze w tym skrypcie programu PowerShell, więc nie zamykaj tego pliku.

3. Określ następujące ustawienia:

    $DemoScenario = 1: Uruchom zadanie w tle, synchronizowanej dzierżawy serwera i informacje o konfiguracji puli do katalogu.

4. Aby uruchomić skrypt synchronizacji, naciśnij klawisz F5. 

    Te informacje są używane później, aby upewnić się, że odzyskiwanie tworzy obraz lustrzany serwerów, pul i baz danych w regionie odzyskiwania.  

    ![Proces synchronizacji](media/saas-dbpertenant-dr-geo-restore/sync-process.png)

Pozostaw okno programu PowerShell, uruchomione w tle, a następnie kontynuuj pozostałej części tego samouczka.

> [!NOTE]
> Proces synchronizacji łączy do katalogu za pomocą aliasu DNS. Alias jest modyfikowana podczas przywracania i repatriacji, aby wskazywał aktywnego katalogu. Proces synchronizacji katalogu na bieżąco aktualizuje z bazy danych lub puli zmiany konfiguracji wprowadzone w regionie odzyskiwania. Podczas repatriacji zmiany te są stosowane do równoważne zasoby w regionie, oryginalnym.

## <a name="geo-restore-recovery-process-overview"></a>Omówienie procesu odzyskiwania przywracania geograficznego

Proces odzyskiwania przywracania geograficznego wdraża aplikację i przywrócenie bazy danych z kopii zapasowych w regionie odzyskiwania.

Proces odzyskiwania wykonuje następujące czynności:

1. Powoduje wyłączenie punktu końcowego usługi Azure Traffic Manager dla aplikacji sieci web w regionie, oryginalnym. Wyłączenie punktu końcowego uniemożliwia użytkownikom połączenie się z aplikacji w nieprawidłowym stanie powinny regionu oryginalnego trybu online podczas odzyskiwania.

2. Przepisy na odzyskiwania baza danych wykazu w katalogu serwera w regionie odzyskiwania, przywracanie geograficzne i aktualizuje alias activecatalog, aby wskazywały serwer wykazu przywróconej. Zmiana aliasu katalogu zapewnia, że proces synchronizacji katalogu zawsze synchronizuje się w katalogu active.

3. Oznacza wszystkich dzierżaw istniejących w katalogu odzyskiwania w trybie offline, aby uniemożliwić dostęp do baz danych dzierżaw, zanim zostaną one przywrócone.

4. Ustanowienie wystąpienia aplikacji w regionie odzyskiwania i konfiguruje go do korzystania z przywróconej katalogu, w tym regionie. Aby zachować czas oczekiwania do minimum, przykładowa aplikacja jest przeznaczona do zawsze łączyć się z bazą danych dzierżawy, w tym samym regionie.

5. Inicjuje obsługę serwera i elastycznej puli, w którym są aprowizowane nowych dzierżaw. Tworzenia tych zasobów gwarantuje, że aprowizację nowych dzierżaw nie zakłóca odzyskiwania istniejących dzierżaw.

6. Aktualizuje nowy alias dzierżawy, aby wskazywały serwer dla nowych baz danych dzierżaw w regionie odzyskiwania. Zmiana tego aliasu gwarantuje, że bazy danych dla nowych dzierżawach są aprowizowane w regionie odzyskiwania.
        
7. Inicjuje obsługę serwerów i pul elastycznych w regionie odzyskiwania do przywrócenia baz danych dzierżaw. Te serwery i pule są lustrzanym konfiguracji w regionie, oryginalnym. Inicjowanie obsługi administracyjnej pule na początku rezerwuje pojemności niezbędne do przywrócenia wszystkich baz danych.

    Awaria w regionie może umieścić znaczne wykorzystanie zasobów w sparowanym regionie. Jeśli użytkownik korzysta funkcja przywracania geograficznego dla odzyskiwania po awarii, a następnie szybko rezerwacji zasobów jest zalecane. Należy wziąć pod uwagę replikację geograficzną jeśli ważne jest odzyskanie aplikacji w określonym regionie. 

8. Włącza on punkt końcowy usługi Traffic Manager dla aplikacji sieci web w regionie odzyskiwania. Włączenie tego punktu końcowego umożliwia aplikacjom aprowizowanie nowych dzierżaw. Na tym etapie wciąż w trybie offline to jednak istniejących dzierżawców.

9. Przesyła partie żądania, aby przywrócić bazy danych w kolejności priorytetów. 

    * Partie są zorganizowane tak, aby bazy danych zostaną przywrócone jednocześnie dla wszystkich pul.  

    * Przywracanie żądań jest przesyłany asynchronicznie, dzięki czemu są one przesyłane szybko i w kolejce do wykonania w każdej puli.

    * Ponieważ żądania przywracania są przetwarzane równolegle we wszystkich pulach, to lepiej dystrybuować dzierżawy ważne dla wielu pul. 

10. Monitoruje usługi SQL Database, aby określić, kiedy są przywracane bazy danych. Po przywróceniu bazy danych dzierżawy jest oznaczony w wykazie w trybie online, a suma rowversion dla bazy danych dzierżawy jest rejestrowany. 

    * Baz danych dzierżawy jest możliwy przez aplikację, tak szybko, jak są oznaczone w trybie online w wykazie.

    * Suma wartości rowversion w bazie danych dzierżawy jest przechowywany w katalogu. Suma ta działa jako odcisku palca, który umożliwia procesu repatriacji określić, jeśli baza danych została zaktualizowana w regionie odzyskiwania.       

## <a name="run-the-recovery-script"></a>Uruchamianie skryptu odzyskiwania

> [!IMPORTANT]
> W tym samouczku spowoduje przywrócenie bazy danych z geograficznie nadmiarowych kopii zapasowych. Mimo że te kopie zapasowe są zwykle dostępne w ciągu 10 minut, może potrwać do godziny. Skrypt wstrzymuje, dopóki nie są one dostępne.

Załóżmy, że nastąpi awaria w regionie, w którym aplikacja jest wdrożona i uruchom skrypt odzyskiwania:

1. W środowisku PowerShell ISE ...\Learning skryptu Modules\Business ciągłość działalności biznesowej i Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 po awarii, należy ustawić następującą wartość:

    $DemoScenario = 2: Odzyskiwanie aplikacji w regionie odzyskiwania przez przywrócenie z geograficznie nadmiarowych kopii zapasowych.

2. Aby uruchomić skrypt, naciśnij klawisz F5.  

    * Skrypt zostanie otwarty w nowym oknie programu PowerShell, a następnie uruchamia zestaw zadań programu PowerShell, które są uruchamiane równolegle. Te zadania przywracania serwerów, pul i baz danych w regionie odzyskiwania.

    * Region odzyskiwania jest sparowanego regionu skojarzonego z regionu platformy Azure, w których wdrożono aplikację. Aby uzyskać więcej informacji, zobacz [sparowanych regionów platformy Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

3. Monitoruj stan procesu odzyskiwania, w oknie programu PowerShell.

    ![Proces odzyskiwania](media/saas-dbpertenant-dr-geo-restore/dr-in-progress.png)

> [!NOTE]
> Aby poznać kod dla zadania odzyskiwania, należy sprawdzić skryptów programu PowerShell w folderze Modules\Business ciągłość działalności biznesowej i awarii Recovery\DR-RestoreFromBackup\RecoveryJobs ...\Learning.

## <a name="review-the-application-state-during-recovery"></a>Sprawdź stan aplikacji podczas odzyskiwania
Gdy punkt końcowy aplikacji jest wyłączone w usłudze Traffic Manager, aplikacja jest niedostępna. Katalog jest przywracany i wszystkich dzierżawców są oznaczone w trybie offline. Następnie włączono punkt końcowy aplikacji w regionie odzyskiwania, a aplikacja jest wróci do trybu online. Mimo, że aplikacja jest dostępna, pojawiają się dzierżaw z trybu offline w Centrum zdarzeń, dopóki nie zostaną przywrócone swoich baz danych. Należy tak zaprojektować aplikację do obsługi baz danych dzierżaw w trybie offline.

* Po zostało odzyskane bazy danych wykazu, ale przed dzierżawców są wróci do trybu online, Odśwież Centrum zdarzeń Wtp w przeglądarce sieci web.

  * W stopce, zwróć uwagę, że nazwa serwera katalogu ma teraz sufiks - odzyskiwania i znajduje się w regionie odzyskiwania.

  * Należy zauważyć, że dzierżaw, które nie zostały jeszcze przywrócone są oznaczone jako w trybie offline i nie są można wybierać.   
 
    ![Proces odzyskiwania](media/saas-dbpertenant-dr-geo-restore/events-hub-tenants-offline-in-recovery-region.png)    

  * Po otwarciu strony zdarzenia dzierżawy bezpośrednio dzierżawy jest w trybie offline, strony wyświetli powiadomienie w trybie offline dzierżawy. Na przykład, jeśli firmy Contoso Concert Hall jest w trybie offline, próby otwarcia http://events.wingtip-dpt.&lt ; użytkownika&gt;.trafficmanager.net/contosoconcerthall.

    ![Proces odzyskiwania](media/saas-dbpertenant-dr-geo-restore/dr-in-progress-offline-contosoconcerthall.png)

## <a name="provision-a-new-tenant-in-the-recovery-region"></a>Aprowizacja nowej dzierżawy w regionie odzyskiwania
Nawet w przypadku, zanim zostaną przywrócone baz danych dzierżaw, można udostępnić nowych dzierżaw w regionie odzyskiwania. Nowe aprowizowane w regionie odzyskiwania baz danych dzierżawy są zwrócony do kraju macierzystego odzyskanej bazy danych później.   

1. W środowisku PowerShell ISE w skrypcie Modules\Business ciągłość działalności biznesowej i awarii Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 ...\Learning, Ustaw następującą właściwość:

    $DemoScenario = 3: Aprowizacja nowej dzierżawy w regionie odzyskiwania.

2. Aby uruchomić skrypt, naciśnij klawisz F5.

3. Hawthorn Hall zdarzeń zostanie otwarta strona w przeglądarce po zakończeniu aprowizacji. 

    Należy zauważyć, że Hawthorn Hall baza danych znajduje się w regionie odzyskiwania.

    ![Hall hawthorn aprowizowane w regionie odzyskiwania](media/saas-dbpertenant-dr-geo-restore/hawthorn-hall-provisioned-in-recovery-region.png)

4. W przeglądarce Odśwież na stronie Centrum zdarzeń Wingtip Tickets, aby zobaczyć, że uwzględnione Hawthorn Hall. 

    Aprowizacji Hawthorn Hall bez oczekiwania na innych dzierżawców można przywrócić pozostałych dzierżaw mogą nadal być w trybie offline.

## <a name="review-the-recovered-state-of-the-application"></a>Przejrzyj odzyskane stan aplikacji

Po zakończeniu procesu odzyskiwania wszystkich dzierżaw i aplikacji są w pełni funkcjonalne w regionie odzyskiwania. 

1. Po wyświetlana w oknie konsoli programu PowerShell oznacza, że wszystkie dzierżawy mogą zostać odzyskane, Odśwież Centrum zdarzeń. 

    Wszystkie dzierżawy są wyświetlane online, w tym nową dzierżawę, Hawthorn Hall.

    ![odzyskane i nowych dzierżaw w Centrum zdarzeń](media/saas-dbpertenant-dr-geo-restore/events-hub-with-hawthorn-hall.png)

2. Kliknij przycisk firmy Contoso Concert Hall, a następnie otwórz jego stronę zdarzenia. 

    W stopce Zwróć uwagę, że baza danych znajduje się na serwerze odzyskiwania znajduje się w regionie odzyskiwania.

    ![Firmy Contoso w regionie odzyskiwania](media/saas-dbpertenant-dr-geo-restore/contoso-recovery-location.png)

3. W [witryny Azure portal](https://portal.azure.com), Otwórz listę grup zasobów.  

    Zwróć uwagę, grupy zasobów, która została wdrożona, a także zasobów odzyskiwania konto należy do grupy przy użyciu sufiksu - odzyskiwania. Grupa zasobów odzyskiwania zawiera wszystkie zasoby utworzone w procesie odzyskiwania oraz nowych zasobów tworzonych podczas awarii. 

4. Otwórz grupę zasobów odzyskiwania i zwróć uwagę, następujące elementy:

   * Wersje odzyskiwania serwerów wykazu i tenants1 sufiksem odzyskiwania. Przywrócona wykazu i dzierżawy bazy danych na tych serwerach wszystkie mają nazwy używane w regionie, oryginalnym.

   * Tenants2-dpt -&lt;użytkownika&gt;— odzyskiwanie programu SQL server. Ten serwer jest używany do aprowizacji nowych dzierżaw w czasie awarii.

   * Usługi app service o nazwie zdarzenia-wingtip-dpt -&lt;recoveryregion&gt;-&lt;użytkownika&gt;, czyli wystąpienie odzyskiwania aplikacji zdarzeń.

     ![Zasoby firmy Contoso w regionie odzyskiwania](media/saas-dbpertenant-dr-geo-restore/resources-in-recovery-region.png) 
    
5. Otwórz tenants2-dpt -&lt;użytkownika&gt;— odzyskiwanie programu SQL server. Należy zauważyć, że zawiera on hawthornhall bazy danych i elastycznej puli Pool1. Hawthornhall bazy danych jest skonfigurowana jako elastycznej bazy danych w elastycznej puli Pool1.

## <a name="change-the-tenant-data"></a>Zmiana danych dzierżawy 
W tym zadaniu należy zaktualizować jednej z baz danych dzierżawy przywrócona. Kopiuje procesu repatriacji przywrócić bazy danych, które zostały zmienione w regionie oryginalnego. 

1. W przeglądarce Znajdź na liście zdarzeń dla firmy Contoso Concert Hall, przewijać zdarzenia i zwróć uwagę, ostatnie zdarzenie Strauss naszych użytkowników bardzo poważnie.

2. W środowisku PowerShell ISE ...\Learning skryptu Modules\Business ciągłość działalności biznesowej i Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 po awarii, należy ustawić następującą wartość:

    $DemoScenario = 4: Usuń zdarzenie z dzierżawy w regionie odzyskiwania.

3. Aby wykonać skrypt, naciśnij klawisz F5.

4. Odśwież stronę zdarzenia firmy Contoso Concert Hall (http://events.wingtip-dpt.&lt ; użytkownika&gt;.trafficmanager.net/contosoconcerthall) i zwróć uwagę, Brak zdarzeń Strauss naszych użytkowników bardzo poważnie.

W tym momencie w tym samouczku zostały odzyskane aplikację, która działa teraz w regionie odzyskiwania. Masz nową dzierżawę w regionie odzyskiwania i zmodyfikowane dane jednego z przywróconej dzierżaw.  

> [!NOTE]
> Inne samouczki w przykładzie nie są przeznaczone do uruchamiania aplikacji w stanie odzyskiwania. Jeśli chcesz zapoznać się z innych samouczków, należy najpierw wycofywania aplikacji.

## <a name="repatriation-process-overview"></a>Omówienie procesu repatriacji

Proces repatriacji aplikacji i jego baz danych do jej oryginalnej regionu zostanie przywrócona po usunięciu awarii.

![Przywracanie geograficzne repatriacji](media/saas-dbpertenant-dr-geo-restore/geo-restore-repatriation.png) 

Proces:

1. Zatrzymuje dowolne działanie trwającą przywracania i anuluje wszystkie żądania przywracania zaległe lub aktywne bazy danych.

2. Ponownie uaktywnia te błędy oryginalnego regionu dzierżawy baz danych, które nie zostały zmienione od czasu tej awarii. Te bazy danych obejmują osobami nie jest jeszcze przywrócona i te odzyskana, lecz nie można zmienić później. Ponownie uaktywnione baz danych są dokładnie ostatnio uzyskiwał dzierżawcom.

3. Aprowizuje obraz lustrzany nowej dzierżawy serwera i elastycznej puli w regionie, oryginalnym. Po wykonaniu tej akcji nowy alias dzierżawy jest poinformowani o tym serwerze. Aktualizacja aliasu powoduje, że nowe dołączanie dzierżawy nastąpi w oryginalnej regionie zamiast w regionie odzyskiwania.

3. Używa replikacji geograficznej, aby przenieść katalogu do oryginalnego regionu w regionie odzyskiwania.

4. Aktualizuje konfigurację puli w regionie, oryginalnym, aby były zgodne ze zmianami, które zostały wprowadzone w regionie odzyskiwania podczas awarii.

5. Tworzy wymagane serwery oraz pul, aby hostować żadnych nowych baz danych utworzonych podczas awarii.

6. Używa wycofywania, przywrócić wielodostępne wykonywane po przywróceniu bazy danych, które zostały zaktualizowane i wszystkich nowych baz danych dzierżaw wykorzystanych podczas awarii replikacji geograficznej. 

7. Czyści zasoby utworzone w regionie odzyskiwania podczas procesu przywracania.

Aby ograniczyć liczbę baz danych dzierżaw, które muszą zostać zwrócony do kraju macierzystego, niezwłocznie są wykonywane kroki od 1 do 3.  

Krok 4 odbywa się tylko, jeśli katalog w regionie odzyskiwania został zmodyfikowany podczas awarii. Katalog jest aktualizowana w przypadku tworzenia nowych dzierżaw, lub jeśli dowolna Konfiguracja bazy danych lub puli zostaje zmieniony w regionie odzyskiwania.

Jest ważne, czy krok 7 powoduje, że minimalnym zakłóceniu dzierżaw i zostaną utracone żadne dane. Aby osiągnąć ten cel, proces korzysta z replikacji geograficznej.

Przed replikacją geograficzną każda baza danych bazy danych w regionie, oryginalnym został usunięty. Bazy danych w regionie odzyskiwania jest replikowana geograficznie, tworząc replikę pomocniczą w regionie, oryginalnym. Po zakończeniu replikacji dzierżawy jest oznaczony jako w trybie offline w katalogu, który przerywa wszystkie połączenia z bazą danych w regionie odzyskiwania. Baza danych jest następnie przełączone w tryb failover, powodując wszystkie oczekujące transakcje do przetworzenia na pomocniczym, więc żadne dane nie zostaną utracone. 

W trybie failover role bazy danych zostały cofnięte. Pomocniczy w regionie, oryginalnym staje się podstawowej bazy danych odczytu i zapisu, a bazy danych w regionie odzyskiwania staje się pomocniczą tylko do odczytu. Wpis dzierżawy w wykazie zostanie zaktualizowany i będzie odwoływać się do bazy danych w regionie, oryginalnym i dzierżawy jest oznaczony w trybie online. W tym momencie repatriacji bazy danych zostało ukończone. 

Aplikacje powinny być zapisywane z logikę ponawiania próby, aby upewnić się, że łączą się ponownie automatycznie podczas połączenia zostają przerwane. Jeśli korzystają z katalogu brokera ponowne łączenie, łączą się z repatriated bazy danych w regionie, oryginalnym. Mimo że krótki rozłączenia jest często nie zauważyć, że można wycofywania bazy danych poza godzinami.

Po zwrócony do bazy danych jest kraju macierzystego, pomocniczej bazy danych w regionie odzyskiwania mogą zostać usunięte. Bazy danych w regionie, oryginalnym wykorzystuje ponownie funkcja przywracania geograficznego ochrony odzyskiwania po awarii.

W kroku 8 zasobów w regionie odzyskiwania, w tym na serwerach odzyskiwania oraz pule i są usuwane.

## <a name="run-the-repatriation-script"></a>Uruchom skrypt repatriacji
Wyobraźmy sobie awarii jest rozwiązany i uruchom skrypt repatriacji.

Po wykonaniu tego samouczka skryptu natychmiast ponownie uaktywnia te błędy Fabrikam Jazz Club i Dojo dereń w regionie, oryginalnym ponieważ są one bez zmian. Repatriates następnie nowej dzierżawy Hawthorn Hall i miejsca Contoso Concert Hall, ponieważ została zmodyfikowana. Skrypt repatriates również katalog, który został zaktualizowany, gdy Hawthorn Hall została aprowizowana.
  
1. W środowisku ISE programu PowerShell w ...\Learning skryptu Modules\Business ciągłość działalności biznesowej i Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 po awarii Sprawdź, czy Proces synchronizacji katalogu nadal działa w jego wystąpienia. Jeśli to konieczne, uruchom go ponownie, ustawiając:

    $DemoScenario = 1: Rozpocznij synchronizacji z serwerem dzierżaw, puli i informacje o konfiguracji bazy danych do katalogu.

    Aby uruchomić skrypt, naciśnij klawisz F5.

2.  Następnie można uruchomić procesu repatriacji, ustaw:

    $DemoScenario = 5: Wycofywania aplikacji w jego oryginalnym region.

    Aby uruchomić skryptu odzyskiwania w nowym oknie programu PowerShell, naciśnij klawisz F5. Repatriacji w ciągu kilku minut i mogą być monitorowane w oknie programu PowerShell.

3. Po uruchomieniu skryptu, Odśwież stronę Centrum zdarzeń (http://events.wingtip-dpt.&lt ; użytkownika&gt;. trafficmanager.net).

    Należy zauważyć, że wszystkie dzierżawy są online i jest dostępny w trakcie tego procesu.

4. Wybierz Fabrikam Jazz Club, aby go otworzyć. Jeśli nie zmodyfikujesz tą dzierżawą, powiadomienia w stopce, czy serwer jest już przywrócone na oryginalnym serwerze.

5. Otwórz lub Odśwież stronę firmy Contoso Concert Hall zdarzenia. Należy zauważyć, ze stopki, początkowo bazy danych jest nadal na serwerze — odzyskiwania. 

6. Odśwież stronę wydarzenia firmy Contoso Concert Hall, po zakończeniu procesu repatriacji i zwróć uwagę, że baza danych jest teraz w Twoim regionie oryginalnej.

7. Ponownie Odśwież Centrum zdarzeń, a następnie otwórz Hawthorn Hall. Należy zauważyć, że jego bazy danych znajdują się również w regionie, oryginalnym. 

## <a name="clean-up-recovery-region-resources-after-repatriation"></a>Oczyszczanie zasobów regionu odzyskiwania po repatriacji
Po zakończeniu repatriacji jest bezpieczne usunąć zasoby w regionie odzyskiwania. 

> [!IMPORTANT]
> Usunięcie tych zasobów, niezwłocznie po to, aby zatrzymać wszystkie rozliczenia dla nich.

Proces przywracania tworzy recovery zasoby w grupie zasobów odzyskiwania. Proces oczyszczania usuwa tę grupę zasobów i wszystkie odwołania do zasobów z wykazu. 

1. W środowisku PowerShell ISE w skrypcie Modules\Business ciągłość działalności biznesowej i awarii Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 ...\Learning, ustaw:
    
    $DemoScenario = 6: Usuń przestarzałe zasobów w regionie odzyskiwania.

2. Aby uruchomić skrypt, naciśnij klawisz F5.

Czyszczenie skryptów, aplikacji po ponownie gdzie jego uruchomienia. W tym momencie możesz ponownie uruchomić skrypt lub wypróbować inne samouczki.

## <a name="designing-the-application-to-ensure-that-the-app-and-the-database-are-co-located"></a>Projektowanie aplikacji, aby upewnić się, że aplikacja i bazy danych wspólnie 
Aplikacja jest przeznaczona do zawsze łączyć się z wystąpienia w tym samym regionie co baza danych dzierżawy. Ten projekt zmniejsza opóźnienie między aplikacją a bazą danych. Tego rodzaju optymalizacji przyjęto założenie, że interakcji aplikacji w bazie danych jest chattier niż interakcji aplikacji przez użytkownika.  

Baz danych dzierżawy mogą znajdować się w odzyskiwania oraz regiony oryginalnego przez pewien czas podczas repatriacji. Dla każdej bazy danych aplikacja wyszukuje region, w którym znajduje się bazy danych, wykonując wyszukiwanie DNS, nazwę serwera dzierżawy. W bazie danych SQL nazwa serwera jest aliasem. Nazwa serwera aliasu zawiera nazwę regionu. Jeśli aplikacja nie znajduje się w tym samym regionie co baza danych, zostanie przekierowany do wystąpienia w tym samym regionie co serwer bazy danych. Trwa przekierowywanie do wystąpienia w tym samym regionie co baza danych minimalizuje opóźnienia między aplikacją a bazą danych.  

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> 
> * Używać katalogu dzierżawy do przechowywania informacji o konfiguracji okresowo odświeżane, co pozwala odbicie lustrzane środowisko odzyskiwania do utworzenia w innym regionie.
> * Odzyskiwanie bazy danych Azure SQL w regionie odzyskiwania przy użyciu przywracania geograficznego.
> * Zaktualizuj wykaz dzierżaw w celu odzwierciedlenia dzierżawy przywróconej bazy danych lokalizacji. 
> * Użyj aliasu DNS, aby połączyć się z całym wykaz dzierżaw, bez ponownej konfiguracji aplikacji.
> * Replikacja geograficzna umożliwia wycofywania odzyskanej bazy danych do ich oryginalnej regionu po usunięciu awarii.

Spróbuj [odzyskiwania po awarii dla wielodostępnych aplikacji SaaS przy użyciu replikacji geograficznej bazy danych](saas-dbpertenant-dr-geo-replication.md) samouczka, aby dowiedzieć się, jak znacznie skrócić czas potrzebny do odzyskania na dużą skalę aplikacji wielodostępnej za pomocą replikacji geograficznej.

## <a name="additional-resources"></a>Dodatkowe zasoby

[Dodatkowe samouczki nawiązujące do aplikacji Wingtip SaaS](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
