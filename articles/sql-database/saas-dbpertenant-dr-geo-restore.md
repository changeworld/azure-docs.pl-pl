---
title: 'Aplikacje SaaS: geob nadmiarowe kopie zapasowe do odzyskiwania po awarii'
description: Dowiedz się, jak używać geograficznych kopii zapasowych usługi Azure SQL Database do odzyskiwania wielodostępnej aplikacji SaaS w przypadku awarii
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: AyoOlubeko
ms.author: craigg
ms.reviewer: sstein
ms.date: 01/14/2019
ms.openlocfilehash: 270fc157fa14efa19ed30d35b614fb769804b72e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73826471"
---
# <a name="use-geo-restore-to-recover-a-multitenant-saas-application-from-database-backups"></a>Odzyskiwanie wielodostępnych aplikacji SaaS za pomocą funkcji przywracania geograficznego z kopii zapasowych bazy danych

W tym samouczku eksploruje scenariusz odzyskiwania po awarii dla wielodostępnych aplikacji SaaS zaimplementowanych z bazą danych na model dzierżawy. [Przywracanie geograficzne](sql-database-recovery-using-backups.md) służy do odzyskiwania baz danych katalogu i dzierżawy z automatycznie obsługiwanych kopii zapasowych geograficznie nadmiarowych do alternatywnego regionu odzyskiwania. Po rozwiązaniu awarii [replikacja geograficzna](sql-database-geo-replication-overview.md) służy do repatriacji zmienionych baz danych do ich oryginalnego regionu.

![Architektura geo-restore](media/saas-dbpertenant-dr-geo-restore/geo-restore-architecture.png)

Przywracanie geograficzne jest najtańszym rozwiązaniem do odzyskiwania po awarii dla usługi Azure SQL Database. Jednak przywracanie z geograficznie nadmiarowych kopii zapasowych może spowodować utratę danych do jednej godziny. Może to zająć dużo czasu, w zależności od rozmiaru każdej bazy danych. 

> [!NOTE]
> Odzyskiwanie aplikacji o najniższym możliwym celu RPO i RTO przy użyciu replikacji geograficznej zamiast przywracania geograficznego.

W tym samouczku przeanalizowane są przepływy pracy przywracania i repatriacji. Omawiane kwestie:
> [!div class="checklist"]
> 
> * Synchronizowanie informacji o konfiguracji bazy danych i puli elastycznej z katalogiem dzierżawy.
> * Skonfiguruj środowisko lustrzanego odbicia w regionie odzyskiwania, który obejmuje aplikacje, serwery i pule.   
> * Odzyskiwanie baz danych katalogu i dzierżawy przy użyciu przywracania geograficznego.
> * Użyj replikacji geograficznej do repatriacji katalogu dzierżawy i zmienionych baz danych dzierżawy po usunięciu awarii.
> * Zaktualizuj katalog, gdy każda baza danych jest przywracana (lub repatriowana), aby śledzić bieżącą lokalizację aktywnej kopii bazy danych każdej dzierżawy.
> * Upewnij się, że baza danych aplikacji i dzierżawy są zawsze współlokuje w tym samym regionie platformy Azure, aby zmniejszyć opóźnienia. 
 

Przed rozpoczęciem tego samouczka należy wykonać następujące wymagania wstępne:
* Wdrażanie bazy danych SaaS biletów Wingtip na aplikację dzierżawy. Aby wdrożyć w mniej niż pięć minut, zobacz [Wdrażanie i eksplorowanie bazy danych SaaS Wingtip Tickets dla każdego dzierżawcy.](saas-dbpertenant-get-started-deploy.md) 
* Zainstaluj program Azure PowerShell. Aby uzyskać szczegółowe informacje, zobacz [Wprowadzenie do programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-geo-restore-recovery-pattern"></a>Wprowadzenie do wzorca odzyskiwania przywracania geograficznego

Odzyskiwanie po awarii (DR) jest ważnym czynnikiem dla wielu aplikacji, czy ze względu na zgodność lub ciągłości biznesowej. W przypadku przedłużającej się przerwy w świadczeniu usług dobrze przygotowany plan odzyskiwania po awarii może zminimalizować zakłócenia w działalności. Plan odzyskiwania po awarii oparty na przywracanie geograficznym musi osiągnąć kilka celów:
 * Zarezerwuj wszystkie potrzebne zdolności produkcyjne w wybranym regionie odzyskiwania tak szybko, jak to możliwe, aby upewnić się, że jest dostępna do przywracania baz danych dzierżawy.
 * Ustanowienie środowiska odzyskiwania lustrzanego odbicia, które odzwierciedla oryginalną konfigurację puli i bazy danych. 
 * Zezwalaj na anulowanie procesu przywracania w trakcie lotu, jeśli oryginalny region powróci do trybu online.
 * Włącz szybkie inicjowanie obsługi administracyjnej dzierżawy, aby nowe dołączanie do dzierżawy można było ponownie uruchomić tak szybko, jak to możliwe.
 * Należy zoptymalizować, aby przywrócić dzierżawy w kolejności priorytetu.
 * Bądź zoptymalizowany, aby uzyskać najemców online tak szybko, jak to możliwe, wykonując kroki równolegle, gdzie jest to praktyczne.
 * Być odporne na awarie, ponowne uruchomienie i idempotentność.
 * Repatriacja baz danych do ich oryginalnego regionu przy minimalnym wpływie na dzierżawców po rozwiązaniu awarii.  

> [!NOTE]
> Aplikacja jest odzyskiwana do sparowanego regionu regionu, w którym aplikacja jest wdrażana. Aby uzyskać więcej informacji, zobacz [Sparowane regiony platformy Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).   

W tym samouczku użyto funkcji usługi Azure SQL Database i platformy Azure w celu sprostania tym wyzwaniom:

* [Szablony usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template), aby zarezerwować wszystkie potrzebne pojemności tak szybko, jak to możliwe. Szablony usługi Azure Resource Manager są używane do inicjowania obsługi lustrzanej odbicia oryginalnych serwerów i pul elastycznych w regionie odzyskiwania. Oddzielny serwer i pula są również tworzone do inicjowania obsługi administracyjnej nowych dzierżaw.
* [Biblioteka klienta elastycznej bazy danych](sql-database-elastic-database-client-library.md) (EDCL), aby utworzyć i obsługiwać katalog bazy danych dzierżawy. Rozszerzony katalog zawiera okresowo odświeżane informacje o pulie i konfiguracji bazy danych.
* [Funkcje odzyskiwania zarządzania fragmentami](sql-database-elastic-database-recovery-manager.md) edcl, aby zachować wpisy lokalizacji bazy danych w katalogu podczas odzyskiwania i repatriacji.  
* [Przywracanie geograficzne](sql-database-disaster-recovery.md), aby odzyskać bazy danych katalogu i dzierżawy z automatycznie obsługiwanych kopii zapasowych geograficznie nadmiarowych. 
* [Operacje przywracania asynchronicznego](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations), wysyłane w kolejności priorytetu dzierżawy, są umieszczane w kolejce dla każdej puli przez system i przetwarzane w partiach, więc pula nie jest przeciążony. Te operacje można anulować przed lub w trakcie wykonywania, jeśli to konieczne.   
* [Replikacja geograficzna](sql-database-geo-replication-overview.md), aby repatriować bazy danych do oryginalnego regionu po awarii. Nie ma utraty danych i minimalny wpływ na dzierżawę podczas korzystania z replikacji geograficznej.
* [Aliasy DNS serwera SQL](dns-alias-overview.md), aby umożliwić procesowi synchronizacji katalogu łączenie się z aktywnym katalogiem niezależnie od jego lokalizacji.  

## <a name="get-the-disaster-recovery-scripts"></a>Pobierz skrypty odzyskiwania po awarii

Skrypty odzyskiwania po awarii używane w tym samouczku są dostępne w [bazie danych SaaS Wingtip Tickets na repozytorium GitHub dzierżawcy](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant). Zapoznaj się z [ogólnymi wskazówkami dotyczącymi](saas-tenancy-wingtip-app-guidance-tips.md) pobierania i odblokowywania skryptów zarządzania biletami Wingtip.

> [!IMPORTANT]
> Podobnie jak wszystkie skrypty zarządzania bilety Wingtip skrypty odzyskiwania po awarii są jakości próbkowania i nie mają być używane w produkcji.

## <a name="review-the-healthy-state-of-the-application"></a>Przejrzyj zdrowy stan aplikacji
Przed rozpoczęciem procesu odzyskiwania, przejrzyj normalny stan dobrej kondycji aplikacji.

1. W przeglądarce sieci Web otwórz centrum zdarzeńhttp://events.wingtip-dpt.&ltWingtip Tickets (&gt; &lt;;user .trafficmanager.net, zamień użytkownika&gt; na wartość użytkownika wdrożenia).
    
   Przewiń do dołu strony i zwróć uwagę na nazwę serwera katalogu i lokalizację w stopce. Lokalizacja jest regionem, w którym wdrożono aplikację.    

   > [!TIP]
   > Najedź kursorem myszy na lokalizację, aby powiększyć wyświetlacz.

   ![Centrum zdarzeń w dobrym stanie w pierwotnym regionie](media/saas-dbpertenant-dr-geo-restore/events-hub-original-region.png)

2. Wybierz dzierżawę sali koncertowej Contoso i otwórz jej stronę wydarzenia.

   W stopce zwróć uwagę na nazwę serwera dzierżawcy. Lokalizacja jest taka sama jak lokalizacja serwera katalogu.

   ![Contoso Concert Hall region oryginalny](media/saas-dbpertenant-dr-geo-restore/contoso-original-location.png) 

3. W witrynie [Azure portal](https://portal.azure.com)przejrzyj i otwórz grupę zasobów, w której wdrożono aplikację.

   Zwróć uwagę na zasoby i region, w którym są wdrażane składniki usługi aplikacji i serwery bazy danych SQL.

## <a name="sync-the-tenant-configuration-into-the-catalog"></a>Synchronizowanie konfiguracji dzierżawy z katalogiem

W tym zadaniu należy rozpocząć proces synchronizacji konfiguracji serwerów, pul elastycznych i baz danych z katalogiem dzierżawy. Te informacje są używane później do skonfigurowania środowiska lustrzanego odbicia w regionie odzyskiwania.

> [!IMPORTANT]
> Dla uproszczenia proces synchronizacji i inne długotrwałe procesy odzyskiwania i repatriacji są implementowane w tych przykładach jako lokalne zadania programu PowerShell lub sesje uruchamiane w ramach logowania użytkownika klienta. Tokeny uwierzytelniania wystawione podczas logowania wygasają po kilku godzinach, a zadania zakończy się niepowodzeniem. W scenariuszu produkcyjnym długotrwałe procesy powinny być implementowane jako niezawodne usługi platformy Azure pewnego rodzaju, działające w ramach jednostki usługi. Zobacz [Tworzenie jednostki usługi Azure PowerShell za pomocą programu Azure PowerShell z certyfikatem.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal) 

1. W programie PowerShell ISE otwórz plik ...\Learning Modules\UserConfig.psm1. Zamień `<resourcegroup>` i `<user>` na liniach 10 i 11 z wartością używaną podczas wdrażania aplikacji. Zapisz plik.

2. W środowisku ISE programu PowerShell otwórz skrypt ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1.

    W tym samouczku uruchom każdy ze scenariuszy w tym skrypcie programu PowerShell, więc należy zachować ten plik otwarty.

3. Ustaw następujące elementy:

    $DemoScenario = 1: Uruchom zadanie w tle, które synchronizuje informacje o konfiguracji serwera dzierżawy i puli z katalogiem.

4. Aby uruchomić skrypt synchronizacji, wybierz opcję F5. 

    Te informacje są używane później, aby upewnić się, że odzyskiwanie tworzy lustrzane odbicie serwerów, pul i baz danych w regionie odzyskiwania.  

    ![Proces synchronizacji](media/saas-dbpertenant-dr-geo-restore/sync-process.png)

Pozostaw okno programu PowerShell uruchomione w tle i kontynuuj resztę tego samouczka.

> [!NOTE]
> Proces synchronizacji łączy się z katalogiem za pomocą aliasu DNS. Alias jest modyfikowany podczas przywracania i repatriacji, aby wskazać aktywny katalog. Proces synchronizacji aktualizuje katalog z dowolnymi zmianami konfiguracji bazy danych lub puli wprowadzonymi w regionie odzyskiwania. Podczas repatriacji te zmiany są stosowane do równoważnych zasobów w regionie pierwotnym.

## <a name="geo-restore-recovery-process-overview"></a>Omówienie procesu odzyskiwania przywracania geograficznego

Proces odzyskiwania przywracania geograficznego wdraża aplikację i przywraca bazy danych z kopii zapasowych do regionu odzyskiwania.

Proces odzyskiwania wykonuje następujące czynności:

1. Wyłącza punkt końcowy usługi Azure Traffic Manager dla aplikacji sieci web w oryginalnym regionie. Wyłączenie punktu końcowego uniemożliwia użytkownikom łączenie się z aplikacją w nieprawidłowym stanie, jeśli oryginalny region zostanie w trybie online podczas odzyskiwania.

2. Apreguj serwer wykazu odzyskiwania w regionie odzyskiwania, przywraca geograficznie bazę danych wykazu i aktualizuje alias activecatalog, aby wskazywał przywrócony serwer katalogu. Zmiana aliasu katalogu gwarantuje, że proces synchronizacji katalogu jest zawsze synchronizowany z aktywnym katalogiem.

3. Oznacza wszystkich istniejących dzierżawców w katalogu odzyskiwania w trybie offline, aby uniemożliwić dostęp do baz danych dzierżawy przed ich przywróceniem.

4. Apreguj wystąpienie aplikacji w regionie odzyskiwania i konfiguruje go do używania przywróconego katalogu w tym regionie. Aby zmniejszyć opóźnienie do minimum, przykładowa aplikacja jest przeznaczona do zawsze łączyć się z bazy danych dzierżawy w tym samym regionie.

5. Aproweksy serwera i puli elastycznej, w którym nowe dzierżawy są aprowizacji. Tworzenie tych zasobów gwarantuje, że inicjowanie obsługi administracyjnej nowych dzierżaw nie koliduje z odzyskiwaniem istniejących dzierżaw.

6. Aktualizuje nowy alias dzierżawy, aby wskazać serwer dla nowych baz danych dzierżawy w regionie odzyskiwania. Zmiana tego aliasu gwarantuje, że bazy danych dla wszystkich nowych dzierżaw są aprowizowani w regionie odzyskiwania.
        
7. Apreguacje serwerów i pul elastycznych w regionie odzyskiwania do przywracania baz danych dzierżawy. Te serwery i pule są lustrzanym odbiciem konfiguracji w oryginalnym regionie. Inicjowanie obsługi administracyjnej pule z góry rezerwuje pojemność potrzebną do przywrócenia wszystkich baz danych.

    Awaria w regionie może wywierać znaczną presję na zasoby dostępne w sparowanym regionie. Jeśli korzystasz z przywracania geograficznego dla odzyskiwania po awarii, zaleca się szybkie rezerwowanie zasobów. Należy wziąć pod uwagę replikacji geograficznej, jeśli jest krytyczne, że aplikacja jest odzyskiwana w określonym regionie. 

8. Włącza punkt końcowy usługi Traffic Manager dla aplikacji sieci web w regionie odzyskiwania. Włączenie tego punktu końcowego umożliwia aplikacji aprowizowanie nowych dzierżaw. Na tym etapie istniejących dzierżaw są nadal w trybie offline.

9. Przesyła partie żądań, aby przywrócić bazy danych w kolejności priorytetu. 

    * Partie są zorganizowane tak, aby bazy danych były przywracane równolegle we wszystkich pulach.  

    * Żądania przywracania są przesyłane asynchronicznie, więc są one przesyłane szybko i w kolejce do wykonania w każdej puli.

    * Ponieważ żądania przywracania są przetwarzane równolegle we wszystkich pulach, lepiej jest dystrybuować ważne dzierżawy w wielu pulach. 

10. Monitoruje usługę bazy danych SQL, aby określić, kiedy bazy danych są przywracane. Po przywróceniu bazy danych dzierżawy jest oznaczona w trybie online w katalogu i rejestrowana jest suma wierszy dla bazy danych dzierżawy. 

    * Bazy danych dzierżawy są dostępne przez aplikację, gdy tylko są one oznaczone w trybie online w katalogu.

    * Suma wartości wierszy w bazie danych dzierżawy jest przechowywana w katalogu. Suma ta działa jako odcisk palca, który umożliwia procesowi repatriacji, aby ustalić, czy baza danych została zaktualizowana w regionie odzyskiwania.       

## <a name="run-the-recovery-script"></a>Uruchamianie skryptu odzyskiwania

> [!IMPORTANT]
> Ten samouczek przywraca bazy danych z geograficznie nadmiarowych kopii zapasowych. Mimo że te kopie zapasowe są zazwyczaj dostępne w ciągu 10 minut, może to potrwać do godziny. Skrypt jest wstrzymywał się, dopóki nie będą dostępne.

Wyobraź sobie, że występuje awaria w regionie, w którym aplikacja jest wdrażana, i uruchom skrypt odzyskiwania:

1. W środowisku ISE programu PowerShell w skrypcie ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 ustaw następującą wartość:

    $DemoScenario = 2: Odzyskaj aplikację do regionu odzyskiwania, przywracając z geograficznie nadmiarowych kopii zapasowych.

2. Aby uruchomić skrypt, wybierz klawisz F5.  

    * Skrypt zostanie otwarty w nowym oknie programu PowerShell, a następnie uruchamia zestaw zadań programu PowerShell, które są uruchamiane równolegle. Te zadania przywracają serwery, pule i bazy danych do regionu odzyskiwania.

    * Region odzyskiwania jest sparowany region skojarzony z regionu platformy Azure, w którym wdrożono aplikację. Aby uzyskać więcej informacji, zobacz [Sparowane regiony platformy Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

3. Monitorowanie stanu procesu odzyskiwania w oknie programu PowerShell.

    ![Proces odzyskiwania](media/saas-dbpertenant-dr-geo-restore/dr-in-progress.png)

> [!NOTE]
> Aby eksplorować kod dla zadań odzyskiwania, przejrzyj skrypty programu PowerShell w folderze ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\RecoveryJobs.

## <a name="review-the-application-state-during-recovery"></a>Przejrzyj stan aplikacji podczas odzyskiwania
Gdy punkt końcowy aplikacji jest wyłączony w usłudze Traffic Manager, aplikacja jest niedostępna. Katalog zostanie przywrócony, a wszyscy dzierżawcy są oznaczane w trybie offline. Punkt końcowy aplikacji w regionie odzyskiwania jest następnie włączona, a aplikacja jest z powrotem w trybie online. Mimo że aplikacja jest dostępna, dzierżawcy są wyświetlane w trybie offline w centrum zdarzeń, dopóki ich bazy danych nie zostaną przywrócone. Ważne jest, aby zaprojektować aplikację do obsługi baz danych dzierżawy w trybie offline.

* Po odzyskaniu bazy danych katalogu, ale zanim dzierżawcy są z powrotem w trybie online, odśwież Wingtip Tickets events hub w przeglądarce sieci web.

  * W stopce należy zauważyć, że nazwa serwera katalogu ma teraz sufiks -recovery i znajduje się w regionie odzyskiwania.

  * Należy zauważyć, że dzierżawy, które nie zostały jeszcze przywrócone są oznaczone jako w trybie offline i nie można wybrać.   
 
    ![Proces odzyskiwania](media/saas-dbpertenant-dr-geo-restore/events-hub-tenants-offline-in-recovery-region.png)    

  * Jeśli otworzysz stronę zdarzeń dzierżawy bezpośrednio, gdy dzierżawa jest w trybie offline, strona wyświetla powiadomienie dzierżawy w trybie offline. Jeśli na przykład sala koncertowa Contoso http://events.wingtip-dpt.&ltjest&gt;w trybie offline, spróbuj otworzyć program ;user .trafficmanager.net/contosoconcerthall.

    ![Proces odzyskiwania](media/saas-dbpertenant-dr-geo-restore/dr-in-progress-offline-contosoconcerthall.png)

## <a name="provision-a-new-tenant-in-the-recovery-region"></a>Aprowizowanie nowej dzierżawy w regionie odzyskiwania
Jeszcze przed przywróceniem baz danych dzierżawy można aprowizować nowych dzierżawców w regionie odzyskiwania. Nowe bazy danych dzierżawy aprowizowana w regionie odzyskiwania są repatriowane z odzyskanych baz danych później.   

1. W środowisku ISE programu PowerShell w skrypcie ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 należy ustawić następującą właściwość:

    $DemoScenario = 3: Aprowizowanie nowej dzierżawy w regionie odzyskiwania.

2. Aby uruchomić skrypt, wybierz klawisz F5.

3. Strona zdarzenia Hawthorn Hall otwiera się w przeglądarce po zakończeniu inicjowania obsługi administracyjnej. 

    Należy zauważyć, że baza danych Hawthorn Hall znajduje się w regionie odzyskiwania.

    ![Hawthorn Hall zaa uchwalony w regionie odbudowy](media/saas-dbpertenant-dr-geo-restore/hawthorn-hall-provisioned-in-recovery-region.png)

4. W przeglądarce odśwież stronę centrum zdarzeń Wingtip Tickets, aby zobaczyć hawthorn Hall włączone. 

    Jeśli aprowizowana Hawthorn Hall bez oczekiwania na innych dzierżaw do przywrócenia, innych dzierżaw może nadal być w trybie offline.

## <a name="review-the-recovered-state-of-the-application"></a>Przegląd odzyskanego stanu aplikacji

Po zakończeniu procesu odzyskiwania aplikacji i wszystkich dzierżaw są w pełni funkcjonalne w regionie odzyskiwania. 

1. Po wyświetleniu w oknie konsoli programu PowerShell wskazuje, że wszystkie dzierżawy są odzyskiwane, odśwież centrum zdarzeń. 

    Wszyscy najemcy pojawiają się w Internecie, w tym nowy najemca, Hawthorn Hall.

    ![Odzyskani i nowi dzierżawcy w centrum zdarzeń](media/saas-dbpertenant-dr-geo-restore/events-hub-with-hawthorn-hall.png)

2. Kliknij contoso Concert Hall i otwórz jej stronę wydarzeń. 

    W stopce należy zauważyć, że baza danych znajduje się na serwerze odzyskiwania znajdującym się w regionie odzyskiwania.

    ![Contoso w regionie odzyskiwania](media/saas-dbpertenant-dr-geo-restore/contoso-recovery-location.png)

3. W [witrynie Azure portal](https://portal.azure.com)otwórz listę grup zasobów.  

    Zwróć uwagę na wdrożoną grupę zasobów oraz grupę zasobów odzyskiwania z sufiksem -recovery. Grupa zasobów odzyskiwania zawiera wszystkie zasoby utworzone podczas procesu odzyskiwania oraz nowe zasoby utworzone podczas awarii. 

4. Otwórz grupę zasobów odzyskiwania i zwróć uwagę na następujące elementy:

   * Wersje odzyskiwania serwerów katalogu i tenants1 z sufiksem -recovery. Przywrócone bazy danych katalogu i dzierżawy na tych serwerach mają nazwy używane w oryginalnym regionie.

   * Serwer SQL tenants2-dpt-&lt;user&gt;-recovery. Ten serwer jest używany do inicjowania obsługi administracyjnej nowych dzierżaw podczas awarii.

   * Usługa aplikacji o nazwie&lt;events-wingtip-dpt- recoveryregion&gt;-&lt;user&gt;, która jest wystąpieniem odzyskiwania aplikacji zdarzeń.

     ![Zasoby Contoso w regionie odzyskiwania](media/saas-dbpertenant-dr-geo-restore/resources-in-recovery-region.png) 
    
5. Otwórz serwer SQL tenants2-dpt-&lt;user&gt;-recovery. Należy zauważyć, że zawiera hawthornhall bazy danych i puli elastycznej Pool1. Hawthornhall bazy danych jest skonfigurowany jako elastycznej bazy danych w puli elastycznej Pool1.

## <a name="change-the-tenant-data"></a>Zmienianie danych dzierżawy 
W tym zadaniu zaktualizujesz jedną z przywróconych baz danych dzierżawy. Proces repatriacji kopiuje przywrócone bazy danych, które zostały zmienione na oryginalny region. 

1. W przeglądarce znajdź listę zdarzeń sali koncertowej Contoso, przewiń wydarzenia i zwróć uwagę na ostatnie zdarzenie, Poważnie Strauss.

2. W środowisku ISE programu PowerShell w skrypcie ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 ustaw następującą wartość:

    $DemoScenario = 4: Usuń zdarzenie z dzierżawy w regionie odzyskiwania.

3. Aby wykonać skrypt, wybierz F5.

4. Odśwież stronę zdarzeń sali koncertowej Contoso (http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/contosoconcerthall) i zwróć uwagę, że zdarzenia poważnie Strauss brakuje.

W tym momencie w samouczku odzyskano aplikację, która jest teraz uruchomiona w regionie odzyskiwania. Udostępniono nową dzierżawę w regionie odzyskiwania i zmodyfikowano dane jednej z przywróconych dzierżawców.  

> [!NOTE]
> Inne samouczki w przykładzie nie są przeznaczone do uruchamiania z aplikacją w stanie odzyskiwania. Jeśli chcesz zapoznać się z innymi samouczkami, pamiętaj, aby najpierw repatriować aplikację.

## <a name="repatriation-process-overview"></a>Omówienie procesu repatriacji

Proces repatriacji przywraca aplikację i jej bazy danych do jej oryginalnego regionu po rozwiązaniu awarii.

![Repatriacja przywracania geograficznego](media/saas-dbpertenant-dr-geo-restore/geo-restore-repatriation.png) 

Proces:

1. Zatrzymuje bieżące działania przywracania i anuluje wszelkie zaległe lub żądania przywracania bazy danych w locie.

2. Ponownie aktywuje w oryginalnych bazach danych dzierżawy regionu, które nie zostały zmienione od czasu awarii. Te bazy danych obejmują te, które nie zostały jeszcze odzyskane i te odzyskane, ale nie zmieniły się później. Reaktywowane bazy danych są dokładnie tak, jak ostatnio dostępne przez ich dzierżawców.

3. Udostępnia lustrzane odbicie serwera nowej dzierżawy i elastyczną pulę w oryginalnym regionie. Po zakończeniu tej akcji nowy alias dzierżawy jest aktualizowany w celu wskazania tego serwera. Aktualizowanie aliasu powoduje, że nowe dołączanie do dzierżawy występuje w oryginalnym regionie, a nie w regionie odzyskiwania.

3. Używa replikacji geograficznej, aby przenieść katalog do oryginalnego regionu z regionu odzyskiwania.

4. Aktualizuje konfigurację puli w oryginalnym regionie, dzięki czemu jest zgodna ze zmianami wprowadzonymi w regionie odzyskiwania podczas awarii.

5. Tworzy wymagane serwery i pule do obsługi nowych baz danych utworzonych podczas awarii.

6. Używa replikacji geograficznej do repatriacji przywróconych baz danych dzierżawy, które zostały zaktualizowane po przywróceniu i wszystkie nowe bazy danych dzierżawy aprowizowana podczas awarii. 

7. Czyści zasoby utworzone w regionie odzyskiwania podczas procesu przywracania.

Aby ograniczyć liczbę baz danych dzierżawy, które muszą zostać repatriowane, kroki od 1 do 3 są wykonywane niezwłocznie.  

Krok 4 jest wykonywane tylko wtedy, gdy katalog w regionie odzyskiwania został zmodyfikowany podczas awarii. Katalog jest aktualizowany, jeśli są tworzone nowe dzierżawy lub jeśli dowolna konfiguracja bazy danych lub puli zostanie zmieniona w regionie odzyskiwania.

Ważne jest, że krok 7 powoduje minimalne zakłócenia dla dzierżawców i nie dane są tracone. Aby osiągnąć ten cel, proces używa replikacji geograficznej.

Przed każdym bazy danych jest replikowane geograficznie, odpowiedniej bazy danych w oryginalnym regionie jest usuwany. Baza danych w regionie odzyskiwania jest następnie replikowana geograficznie, tworząc replikę pomocniczą w oryginalnym regionie. Po zakończeniu replikacji dzierżawca jest oznaczony w trybie offline w katalogu, który przerywa wszelkie połączenia z bazą danych w regionie odzyskiwania. Baza danych jest następnie awaryjnie, powodując żadnych oczekujących transakcji do przetwarzania na pomocnicze, więc nie dane są tracone. 

W pracy awaryjnej role bazy danych są odwrócone. Pomocniczy w oryginalnym regionie staje się podstawową bazą danych odczytu i zapisu, a baza danych w regionie odzyskiwania staje się pomocniczą tylko do odczytu. Wpis dzierżawy w katalogu jest aktualizowany w celu odwoływania się do bazy danych w oryginalnym regionie, a dzierżawca jest oznaczony w trybie online. W tym momencie repatriacja bazy danych została zakończona. 

Aplikacje powinny być zapisywane z logiką ponawiania, aby upewnić się, że łączą się automatycznie po przerwaniu połączeń. Gdy używają katalogu do brokera ponownego połączenia, łączą się z repatriowaną bazą danych w oryginalnym regionie. Chociaż krótkie rozłączenie często nie jest zauważone, można wybrać repatriację baz danych poza godzinami pracy.

Po repatriacji bazy danych można usunąć pomocniczą bazę danych w regionie odzyskiwania. Baza danych w regionie oryginalnym następnie ponownie opiera się na przywracaniu geograficznego dla ochrony odzyskiwania po awarii.

W kroku 8 zasoby w regionie odzyskiwania, w tym serwery odzyskiwania i pule, są usuwane.

## <a name="run-the-repatriation-script"></a>Uruchamianie skryptu repatriacji
Wyobraźmy sobie, że awaria została rozwiązana i uruchom skrypt repatriacji.

Jeśli po samouczku skrypt natychmiast reaktywuje Fabrikam Jazz Club i Dogwood Dojo w oryginalnym regionie, ponieważ pozostają one niezmienione. Następnie repatriuje nowego dzierżawcę, Hawthorn Hall i Contoso Concert Hall, ponieważ został zmodyfikowany. Skrypt repatriuje również katalog, który został zaktualizowany, gdy Hawthorn Hall został aprowizował.
  
1. W środowisku POWERShell ISE w skrypcie ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 sprawdź, czy proces synchronizacji wykazu jest nadal uruchomiony w wystąpieniu programu PowerShell. W razie potrzeby uruchom go ponownie, ustawiając:

    $DemoScenario = 1: Rozpocznij synchronizowanie informacji o konfiguracji serwera dzierżawy, puli i bazy danych w katalogu.

    Aby uruchomić skrypt, wybierz klawisz F5.

2.  Następnie, aby rozpocząć proces repatriacji, ustaw:

    $DemoScenario = 5: Repatriacja aplikacji do jej oryginalnego regionu.

    Aby uruchomić skrypt odzyskiwania w nowym oknie programu PowerShell, wybierz opcję F5. Repatriacja trwa kilka minut i może być monitorowana w oknie programu PowerShell.

3. Gdy skrypt jest uruchomiony, odśwież stronęhttp://events.wingtip-dpt.&ltcentrum&gt;zdarzeń ( ;user .trafficmanager.net).

    Należy zauważyć, że wszystkie dzierżawy są online i dostępne w trakcie tego procesu.

4. Wybierz Fabrikam Jazz Club, aby go otworzyć. Jeśli ta dzierżawa nie została zmodyfikowana, zwróć uwagę ze stopki, że serwer został już przywrócony do oryginalnego serwera.

5. Otwórz lub odśwież stronę wydarzeń sali koncertowej Contoso. Zwróć uwagę ze stopki, że początkowo baza danych jest nadal na serwerze -recovery. 

6. Odśwież contoso Concert Hall zdarzenia strony po zakończeniu procesu repatriacji i zauważyć, że baza danych jest teraz w oryginalnym regionie.

7. Odśwież centrum wydarzeń ponownie i otwórz Hawthorn Hall. Należy zauważyć, że jego baza danych znajduje się również w regionie oryginalnym. 

## <a name="clean-up-recovery-region-resources-after-repatriation"></a>Oczyszczanie zasobów regionu odzyskiwania po repatriacji
Po zakończeniu repatriacji można bezpiecznie usunąć zasoby w regionie odzyskiwania. 

> [!IMPORTANT]
> Usuń te zasoby niezwłocznie, aby zatrzymać wszystkie rozliczenia za nie.

Proces przywracania tworzy wszystkie zasoby odzyskiwania w grupie zasobów odzyskiwania. Proces oczyszczania usuwa tę grupę zasobów i usuwa wszystkie odwołania do zasobów z katalogu. 

1. W środowisku ISE programu PowerShell w skrypcie ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1:
    
    $DemoScenario = 6: Usuń przestarzałe zasoby z regionu odzyskiwania.

2. Aby uruchomić skrypt, wybierz klawisz F5.

Po oczyszczeniu skryptów aplikacja powraca tam, gdzie została uruchomiona. W tym momencie można uruchomić skrypt ponownie lub wypróbować inne samouczki.

## <a name="designing-the-application-to-ensure-that-the-app-and-the-database-are-co-located"></a>Projektowanie aplikacji w celu zapewnienia, że aplikacja i baza danych są zlokalizowane 
Aplikacja jest przeznaczona do zawsze łączyć się z wystąpienia w tym samym regionie co dzierżawcy bazy danych. Ten projekt zmniejsza opóźnienia między aplikacją a bazą danych. Ta optymalizacja zakłada, że interakcja między aplikacjami a bazą danych jest bardziej rozmowna niż interakcja między użytkownikiem a aplikacją.  

Bazy danych dzierżawy mogą być rozłożone na odzyskiwanie i oryginalnych regionów przez pewien czas podczas repatriacji. Dla każdej bazy danych aplikacja wyszukuje region, w którym znajduje się baza danych, wykonując wyszukiwanie DNS na nazwę serwera dzierżawy. W bazie danych SQL nazwa serwera jest aliasem. Nazwa serwera aliasowanego zawiera nazwę regionu. Jeśli aplikacja nie znajduje się w tym samym regionie co baza danych, przekierowuje do wystąpienia w tym samym regionie co serwer bazy danych. Przekierowanie do wystąpienia w tym samym regionie co baza danych minimalizuje opóźnienia między aplikacją a bazą danych.  

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> 
> * Użyj katalogu dzierżawy do przechowywania okresowo odświeżonych informacji o konfiguracji, co umożliwia tworzenie środowiska odzyskiwania lustrzanego odbicia w innym regionie.
> * Odzyskiwanie baz danych SQL platformy Azure do regionu odzyskiwania przy użyciu przywracania geograficznego.
> * Zaktualizuj katalog dzierżawy, aby odzwierciedlić przywrócone lokalizacje bazy danych dzierżawy. 
> * Użyj aliasu DNS, aby umożliwić aplikacji łączenie się z katalogiem dzierżawy bez ponownej konfiguracji.
> * Użyj replikacji geograficznej, aby repatriować odzyskane bazy danych do ich oryginalnego regionu po rozwiązaniu awarii.

Wypróbuj [odzyskiwanie po awarii dla wielodostępnej aplikacji SaaS przy użyciu samouczka replikacji geograficznej bazy danych,](saas-dbpertenant-dr-geo-replication.md) aby dowiedzieć się, jak użyć replikacji geograficznej, aby znacznie skrócić czas potrzebny do odzyskania aplikacji wielodostępnej na dużą skalę.

## <a name="additional-resources"></a>Zasoby dodatkowe

[Dodatkowe samouczki, które opierają się na aplikacji Wingtip SaaS](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
