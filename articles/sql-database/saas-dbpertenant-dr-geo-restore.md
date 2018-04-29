---
title: Odzyskiwanie po awarii dla aplikacji SaaS przy użyciu kopii zapasowych geograficznie nadmiarowego Azure SQL Database | Dokumentacja firmy Microsoft
description: Korzystanie z magazynu geograficznie nadmiarowego kopie zapasowe bazy danych SQL Azure odzyskać wielodostępnych aplikacji SaaS w przypadku awarii
keywords: samouczek usługi sql database
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: saas apps
ms.topic: article
ms.date: 04/16/2018
ms.author: ayolubek
ms.openlocfilehash: a677e6eb583e293f83df824804aa4cd6f8f5d778
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="recover-a-multi-tenant-saas-application-using-geo-restore-from-database-backups"></a>Odzyskaj wielodostępnych aplikacji SaaS przy użyciu geograficznie — Przywracanie z kopii zapasowych bazy danych

W tym samouczku eksplorowania scenariusza odzyskiwania awaryjnego pełny dla wielodostępnych aplikacji SaaS implementowane przy użyciu modelu bazy danych dla dzierżawy. Możesz użyć [ _geograficzne_ ](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-recovery-using-backups) pozwoli na odzyskanie automatycznie utrzymywana geograficznie nadmiarowego kopie zapasowe bazy danych katalogu i dzierżawy w region alternatywny odzyskiwania. Po awarii nie zostanie rozwiązany, należy użyć [ _— replikacja geograficzna_ ](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-geo-replication-overview) wycofywania zmienione baz danych do ich oryginalnej regionu do.

![Architektura w przypadku przywracania Geo](media/saas-dbpertenant-dr-geo-restore/geo-restore-architecture.png)

Geograficzne jest rozwiązanie do odzyskiwania po awarii najniższy koszt bazy danych SQL.  Jednak przywracanie z kopii zapasowej geograficznie nadmiarowego może spowodować utratę danych do jednej godziny, a może zająć wiele czasu, zależnie od wielkości każdej bazy danych. **Aby odzyskać aplikacji o najniższym RPO możliwe i RTO, należy użyć — replikacja geograficzna zamiast geograficzne**.

W tym samouczku Eksploruje zarówno przywracania i repatriacji przepływów pracy. Omawiane kwestie:
> [!div class="checklist"]

>* Baza danych usługi synchronizacji i informacje o konfiguracji puli elastycznej do katalogu dzierżawcy
>* Konfigurowanie środowiska odbicie lustrzane w regionie "odzyskiwania", składającej się z aplikacji, serwerów i pule    
>* Odzyskiwanie baz danych katalogu i dzierżawcy przy użyciu _geograficzne_
>* Wycofywania katalogu dzierżawcy i dzierżawy zmienione baz danych przy użyciu _— replikacja geograficzna_ po awarii
>* Aktualizuj katalog, ponieważ każda baza danych jest przywrócona (lub zwrócony do kraju macierzystego) do śledzenia bieżącą lokalizację kopii aktywnej bazy danych z każdego dzierżawcy
>* Upewnij się, aplikacji i dzierżawcy bazy danych zawsze wspólnie znajdują się w tym samym regionie Azure, aby zmniejszyć opóźnienia  
 

Przed rozpoczęciem tego samouczka, upewnij się, że wykonywane są następujące wymagania wstępne:
* Baza danych SaaS biletów Wingtip każdej dzierżawy aplikacji jest wdrożona. Aby wdrożyć w mniej niż 5 minut, zobacz [Wdróż i eksplorowanie Wingtip biletów SaaS bazy danych na aplikację dzierżawy](saas-dbpertenant-get-started-deploy.md)  
* Zainstalowany jest program Azure PowerShell. Aby uzyskać szczegółowe informacje, zobacz [Rozpoczynanie pracy z programem Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-the-geo-restore-recovery-pattern"></a>Wprowadzenie do wzorca odzyskiwanie geograficzne

Odzyskiwania awaryjnego (DR) jest ważną kwestią dla wielu aplikacji, dla zachowania zgodności lub ciągłość prowadzenia działalności biznesowej. Powinno być awaria usługi długimi, dobrze przygotowana planu odzyskiwania po awarii można zminimalizować zakłócenia biznesowych. Plan odzyskiwania po awarii A oparty na geograficzne musi realizację kilku celów:
 * Wydajność wszystkich niezbędnych w regionie odzyskiwania wybranego rezerwowa tak szybko jak to możliwe, aby upewnić się, że jest ona dostępna do przywrócenia bazy danych dzierżawy.
 * Ustanawiania odbicie lustrzane środowiska odzyskiwania, które odzwierciedla oryginalna konfiguracja puli i bazy danych 
 * Musi być można anulować proces przywracania w locie pośredniej, jeśli oryginalny region powróci do stanu online.
 * Włącz szybkie Inicjowanie obsługi dzięki nowej dzierżawy przy dołączaniu można ponownie uruchomić jak najszybciej dzierżawy  
 * Być zoptymalizowany pod kątem przywracania dzierżaw w kolejności priorytetu
 * Być zoptymalizowany pod kątem pobierania dzierżawcy online tak szybko, jak to możliwe w przypadku, gdy praktyczne, wykonując kroki równolegle
 * Zapewnienie odporności na uszkodzenia, ponownego uruchamiania, a idempotentności
 * Wycofywania baz danych do ich oryginalnej regionu przy minimalnym wpływie dzierżawcom, po usunięciu awarii.  

> [!Note]
> Aplikacja jest odzyskiwana do _sparowanego region_ regionu, w którym aplikacja jest wdrażana. Aby uzyskać więcej informacji, zobacz [Azure łączyć regionów](https://docs.microsoft.com/en-us/azure/best-practices-availability-paired-regions).   



W tym samouczku są opisane te problemy przy użyciu funkcji bazy danych SQL Azure i platformy Azure:

* [Szablony usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template), aby zarezerwować wszystkie wymagane pojemności tak szybko jak to możliwe. Szablony usługi Azure Resource Manager służą do udostępniania odbicie lustrzane oryginalnego serwerów i pul elastycznych w regionie odzyskiwania. Oddzielenie serwera i puli są również tworzone dla inicjowania obsługi administracyjnej nowych dzierżaw. 
* [Biblioteka klienta usługi elastycznej bazy danych](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-elastic-database-client-library) (EDCL), można tworzyć i obsługiwać bazy danych wykaz dzierżawy.  Katalog jest rozszerzona, aby uwzględnić okresowo odświeżyć informacje o konfiguracji puli i bazy danych.
* [Funkcje odzyskiwania zarządzania niezależnego fragmentu](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-database-recovery-manager) z EDCL do obsługi bazy danych lokalizacji wpisów w katalogu podczas odzyskiwania i repatriacji.  
* [Geograficzne](https://docs.microsoft.com/azure/sql-database/sql-database-disaster-recovery), aby odzyskać bazy danych katalogu i dzierżawy z geograficznie nadmiarowego automatycznie utrzymywana w kopii zapasowej. 
* [Operacje asynchroniczne przywracania](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) wysyłane w kolejności priorytetu dzierżawy, co dla każdej puli w kolejce przez system i przetwarzane w partiach tak puli nie jest przeciążona. Te operacje mogą zostać anulowane przed lub w trakcie wykonywania, jeśli to konieczne.    
* [Replikacja geograficzna](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview), aby wycofywania baz danych do oryginalnej regionu po awarii. Przy użyciu replikacji geograficznej gwarantuje, że istnieje nie utraty danych i minimalny wpływ na dzierżawcy.
* [Aliasy programu SQL server DNS](https://docs.microsoft.com/azure/sql-database/dns-alias-overview) umożliwia nawiązanie połączenia z katalogiem active, niezależnie od jego lokalizacji procesu synchronizacji katalogu.  

## <a name="get-the-disaster-recovery--scripts"></a>Pobierz skrypty odzyskiwania po awarii 

Skrypty DR używane w tym samouczku są dostępne w [SaaS biletów Wingtip bazy danych, dla każdego dzierżawcy repozytorium GitHub](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant). Zapoznaj się z [ogólne wskazówki](saas-tenancy-wingtip-app-guidance-tips.md) dla czynności, aby pobrać i odblokować skrypty zarządzania Wingtip biletów.
> [!IMPORTANT]
> Podobnie jak wszystkie bilety Wingtip zarządzania skrypty skrypty DR są przykładowe jakości i nie powinna być używana w środowisku produkcyjnym.   

## <a name="review-the-healthy-state-of-the-application"></a>Sprawdź kondycję aplikacji
Przed rozpoczęciem procesu odzyskiwania należy przejrzeć normalnego stanu dobrej kondycji aplikacji.
1. W przeglądarce sieci web Otwórz Centrum zdarzeń biletów Wingtip (http://events.wingtip-dpt.&lt; Użytkownik&gt;. trafficmanager.net - Zastąp &lt;użytkownika&gt; z wartością użytkownika danego wdrożenia).
    * Przewiń w dół strony i zwróć uwagę, nazwę serwera katalogu i lokalizację w stopce.  Lokalizacja jest regionu, w którym aplikacja została wdrożona.    
        Porada: Umieść kursor myszy nad lokalizacji, aby powiększyć wyświetlania.

    ![Centrum zdarzeń dobrej kondycji w regionie oryginalnej](media/saas-dbpertenant-dr-geo-restore/events-hub-original-region.png)

1. Kliknij dzierżawy Hall porozumieniu Contoso i otwórz jej stronę zdarzeń.
    * W stopce Zwróć uwagę, nazwę serwera dzierżawcy. Lokalizacja będzie taka sama jak lokalizacja serwera katalogu.

    ![Region oryginalnego Hall porozumieniu firmy Contoso](media/saas-dbpertenant-dr-geo-restore/contoso-original-location.png)    
1. W [portalu Azure](https://portal.azure.com), przejrzyj i otwórz grupę zasobów, w którym aplikacja została wdrożona.
    * Zwróć uwagę, zasobów i region wdrożone serwery bazy danych SQL i składniki usługi aplikacji.

## <a name="sync-tenant-configuration-into-catalog"></a>Konfiguracji dzierżawy synchronizacji do katalogu

To zadanie służy do uruchamiania procesu synchronizacji konfiguracji serwerów, pule elastyczne i baz danych w katalogu dzierżawcy.  Te informacje jest później używane do konfigurowania środowiska odbicie lustrzane w regionie odzyskiwania.

> [!IMPORTANT]
> Dla uproszczenia procesu synchronizacji i innych długotrwała procesów odzyskiwania i repatriacji zostały wdrożone w te przykłady lokalnego zadania programu Powershell lub sesji, które Uruchom loginu użytkownika klienta. Tokeny uwierzytelniania wystawiony podczas logowania wygaśnie po kilku godzinach i zadania, następnie zakończy się niepowodzeniem. W scenariuszu produkcyjnym procesy długotrwałe powinny zostać wdrożone jako wiarygodnych usług Azure pewnego rodzaju uruchamiania nazwy głównej usługi. Zobacz [użycia programu Azure PowerShell do tworzenia nazwy głównej usługi przy użyciu certyfikatu](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal). 

1. W _PowerShell ISE_, otwórz plik Modules\UserConfig.psm1 ...\Learning. Zastąp `<resourcegroup>` i `<user>` w wierszach 10 i 11 o wartości używane w przypadku wdrażania aplikacji.  Zapisz plik!

2. W *PowerShell ISE*, Otwórz ...\Learning skryptu ciągłości Modules\Business i Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 po awarii.
    *  W tym samouczku zostanie uruchomiony poszczególnych scenariuszy, w tym programie PowerShell skryptu tak nie zamykaj tego pliku.

3. Ustaw następujące wartości:
    * **$DemoScenario = 1**, uruchom zadanie w tle synchronizowanej serwera dzierżawcy i puli informacje o konfiguracji do katalogu

3. Naciśnij klawisz **F5** do uruchamiania skryptu synchronizacji. 
    *  Te informacje jest używana później, aby upewnić się, że recovery tworzy odbicie lustrzane serwerów, pul i baz danych w regionie odzyskiwania.  
![Proces synchronizacji](media/saas-dbpertenant-dr-geo-restore/sync-process.png)

Pozostaw okno programu PowerShell działa w tle i kontynuować pozostałej części tego samouczka.

> [!Note]
> Proces synchronizacji nawiązuje połączenie z katalogiem za pomocą aliasu DNS. Alias jest modyfikowany podczas przywracania i repatriacji wskaż aktywnego katalogu. Proces synchronizacji zachowuje katalogu aktualne z bazy danych lub puli konfiguracji zmiany wprowadzone w regionie odzyskiwania.  Podczas repatriacji zmiany te są stosowane do równoważne zasoby w regionie oryginalnego.

## <a name="geo-restore-recovery-process-overview"></a>Omówienie procesu odzyskiwania geograficzne

Proces odzyskiwania geograficzne wdraża aplikację i przywrócenie bazy danych z kopii zapasowych w regionie odzyskiwania.

Proces odzyskiwania wykonuje następujące czynności:

1. Powoduje wyłączenie punktu końcowego Menedżera ruchu dla aplikacji sieci web w oryginalnym regionie. Wyłączenie punktu końcowego uniemożliwia użytkownikom łączenie się z aplikacji w nieprawidłowym stanie powinien regionu oryginalnego przejdzie w tryb online podczas odzyskiwania.

1. Udostępnia serwer wykazu odzyskiwania w regionie odzyskiwania przywraca geograficznie katalogu bazy danych i aktualizuje _activecatalog_ alias, aby wskazywały serwer katalogu przywrócone.  
    * Zmiana aliasu katalogu gwarantuje, że proces synchronizacji katalogu jest zawsze synchronizowane do aktywnego katalogu.

1. Oznacza wszystkie dzierżaw istniejących w wykaz odzyskiwania w trybie offline, aby uniemożliwić dostęp do dzierżawy baz danych, zanim zostaną one przywrócone.

1. Udostępnia wystąpienie aplikacji w regionie odzyskiwania i konfiguruje się go do korzystania z katalogu przywrócone w tym regionie.
    * Aby zachować czas oczekiwania do minimum, przykładowa aplikacja zaprojektowano tak, aby zawsze łączy się z bazą danych dzierżawy, w tym samym regionie.

1. Udostępnia serwer i elastyczna pula udostępniane nowi dzierżawcy. Tworzenie tych zasobów gwarantuje, że obsługa administracyjna nowi dzierżawcy nie zakłóca odzyskiwania istniejący dzierżawcy.

1. Aktualizuje alias nowej dzierżawy, aby wskazywały serwer dla nowej dzierżawy baz danych w regionie odzyskiwania. Zmiana tego aliasu zapewnia za bazy danych dla dowolnego nowego dzierżawcy w regionie odzyskiwania.
        
1. Serwery przepisów i pul elastycznych w regionie odzyskiwania do przywrócenia bazy danych dzierżawy. Te serwery i pule są lustrzanego w regionie oryginalnej konfiguracji.  Inicjowanie obsługi administracyjnej pul początkowych rezerwuje zdolności niezbędne do przywrócenia wszystkich baz danych.
    * Awaria w regionie umieścić znaczące wykorzystanie zasobów w regionie pary.  Jeśli użytkownik korzysta geograficzne do odzyskiwania po awarii, a następnie szybko rezerwacji zasobów jest zalecane. Należy rozważyć użycie replikacja geograficzna, jeśli jest bardzo istotne, że aplikacja musi zostać odzyskana w określonym regionie. 

1. Umożliwia punktu końcowego Menedżera ruchu dla aplikacji sieci Web w regionie odzyskiwania. Włączenie tego punktu końcowego umożliwia aplikacji do obsługi administracyjnej nowych dzierżaw. Na tym etapie nadal w trybie offline to jednak istniejących dzierżawców.

1. Przesyła partie żądania przywrócenia baz danych w kolejności priorytetu. 
    * Partie są zorganizowane tak, Aby baz danych są przywracane jednocześnie we wszystkich pulach.  
    * Przywracanie żądań jest przesyłany asynchronicznie, tak aby szybko przesłać i kolejki w celu wykonywania w każdej puli.
    * Ponieważ żądania przywracania są przetwarzane równolegle we wszystkich pul, warto rozpowszechniają ważne dzierżaw wiele pul. 

1. Monitoruje usługę bazy danych SQL do określenia przywrócenie bazy danych. Po przywróceniu bazy danych dzierżawy jest oznaczony w katalogu w trybie online, a sumę rowversion dla bazy danych dzierżawy jest rejestrowany. 
    * Dzierżawy baz danych są dostępne przez aplikację, jak są oznaczone w wykazie online.
    * Suma wartości rowversion w bazie danych dzierżawy są przechowywane w katalogu. Tej sumy działa jako linii papilarnych, umożliwiający procesu repatriacji, aby określić, jeśli bazy danych został zaktualizowany w regionie odzyskiwania.      

## <a name="run-the-recovery-script"></a>Uruchom skrypt odzyskiwania

> [!IMPORTANT]
> W tym samouczku przywraca baz danych z geograficznie nadmiarowego kopii zapasowych. Chociaż te kopie zapasowe są zwykle dostępne w ciągu 10 minut, może potrwać do godziny są dostępne. Skrypt wstrzyma pracę aż do ich udostępnieniu.  Czas, aby uzyskać kawy!

Teraz załóżmy istnieje awaria w regionie, w którym aplikacja jest wdrożona i uruchom skrypt odzyskiwania:

1. W *PowerShell ISE*, w ...\Learning skryptu ciągłości Modules\Business i Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 po awarii, ustaw następujące wartości:
    * **$DemoScenario = 2**, odzyskiwanie aplikacji do regionu odzyskiwania przez Przywracanie z kopii zapasowej geograficznie nadmiarowego

1. Naciśnij klawisz **F5**, aby uruchomić skrypt.  
    * Skrypt zostanie otwarty w nowym oknie programu PowerShell, a następnie uruchamia zestaw zadań programu PowerShell, które są uruchamiane równolegle.  Te zadania przywracania serwerów, pul i baz danych do regionu odzyskiwania. 
    * Region odzyskiwania jest _sparowanego region_ skojarzone z region platformy Azure, w której wdrożono aplikację. Aby uzyskać więcej informacji, zobacz [Azure łączyć regionów](https://docs.microsoft.com/en-us/azure/best-practices-availability-paired-regions). 

1. Monitorowanie stanu procesu odzyskiwania, w oknie programu PowerShell.

    ![Proces odzyskiwania](media/saas-dbpertenant-dr-geo-restore/dr-in-progress.png)

>Aby zapoznać się z kodu dla zadania odzyskiwania, przejrzyj skryptów programu PowerShell w folderze ...\Learning ciągłości Modules\Business i Recovery\DR-RestoreFromBackup\RecoveryJobs po awarii.

## <a name="review-the-application-state-during-recovery"></a>Sprawdź stan aplikacji podczas odzyskiwania
Gdy punkt końcowy aplikacji jest wyłączone w Menedżerze ruchu, że aplikacja jest niedostępna. Katalog jest przywracany i wszystkich dzierżawców są oznaczone w trybie offline.  Następnie włączono punkt końcowy aplikacji w regionie odzyskiwania, a aplikacja jest znowu w trybie online. Mimo, że aplikacja jest dostępna, dzierżawcy do momentu przywrócenia ich baz danych są wyświetlane w trybie offline w Centrum zdarzeń. Należy koniecznie Utwórz projekt swojej aplikacji do obsługi baz danych w trybie offline dzierżawy.

1. Po została odzyskana baza danych katalogu, ale przed dzierżawcy jest znowu w trybie online, Odśwież Centrum Wingtip biletów zdarzeń w przeglądarce sieci web.
    * W stopce, zwróć uwagę, że nazwa serwera katalogu ma teraz _-odzyskiwania_ sufiks i znajduje się w regionie odzyskiwania.
    * Zwróć uwagę, że dzierżawcami, które nie zostały jeszcze przywrócone została oznaczona jako w trybie offline i nie są można wybierać.   
 
    ![Proces odzyskiwania](media/saas-dbpertenant-dr-geo-restore/events-hub-tenants-offline-in-recovery-region.png)    

    * Po otwarciu strony zdarzenia dzierżawy bezpośrednio, gdy dzierżawa jest w trybie offline, zostaje wyświetlona strona powiadomienie dzierżawy w trybie offline. Na przykład, jeśli Hall porozumieniu Contoso jest w trybie offline, spróbuj otworzyć http://events.wingtip-dpt.&lt; Użytkownik&gt;.trafficmanager.net/contosoconcerthall ![proces odzyskiwania](media/saas-dbpertenant-dr-geo-restore/dr-in-progress-offline-contosoconcerthall.png)

## <a name="provision-a-new-tenant-in-the-recovery-region"></a>Udostępnianie nowej dzierżawy w regionie odzyskiwania
Nawet przed dzierżawy bazy danych zostaną przywrócone, można udostępnić nowych dzierżaw w regionie odzyskiwania. Nowe dzierżawy bazy danych udostępniane w regionie odzyskiwania zostanie później wywożone z odzyskanej bazy danych.   

1. W *PowerShell ISE*, w ...\Learning skryptu ciągłości Modules\Business i Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 po awarii, Ustaw następującą właściwość:
    * **$DemoScenario = 3**, obsługi administracyjnej nowej dzierżawy w regionie odzyskiwania

1. Naciśnij klawisz **F5**, aby uruchomić skrypt. 

1. Strona zdarzenia Hawthorn Hall otwiera w przeglądarce po zakończeniu inicjowania obsługi administracyjnej. 
    * Zwróć uwagę, że Hawthorn Hall baza danych znajduje się w regionie odzyskiwania.
    ![Hall hawthorn udostępniane w regionie odzyskiwania](media/saas-dbpertenant-dr-geo-restore/hawthorn-hall-provisioned-in-recovery-region.png)

1. W przeglądarce Odśwież stronę Centrum zdarzeń biletów Wingtip wyświetlić zawarte Hawthorn Hall. 
    * Alokacji Hawthorn Hall bez oczekiwania na innych dzierżawców do przywrócenia innych dzierżawców może nadal być w trybie offline.

## <a name="review-the-recovered-state-of-the-application"></a>Przejrzyj odzyskane stan aplikacji

Po zakończeniu procesu odzyskiwania aplikacji i wszystkich dzierżaw są w pełni funkcjonalne w regionie odzyskiwania. 

1. Gdy wyświetlana w oknie konsoli programu PowerShell oznacza, że wszystkie dzierżaw są odzyskiwane, Odśwież Centrum zdarzeń.  Dzierżawcy będą wyświetlone wszystkie online, łącznie z nowym dzierżawcą Hawthorn Hall.

    ![odzyskane i nowych dzierżaw w Centrum zdarzeń](media/saas-dbpertenant-dr-geo-restore/events-hub-with-hawthorn-hall.png)

1. Kliknij Hall porozumieniu Contoso i otwórz jej stronę zdarzenia.
    * W stopce należy zauważyć, że baza danych znajduje się na serwerze odzyskiwania znajduje się w regionie odzyskiwania.

    ![Firmy Contoso w regionie odzyskiwania](media/saas-dbpertenant-dr-geo-restore/contoso-recovery-location.png)

1. W [portalu Azure](https://portal.azure.com), Otwórz listę grup zasobów.  
    * Zwróć uwagę, grupy zasobów, która została wdrożona, oraz odzyskiwania grupy zasobów, z _-odzyskiwania_ sufiks.  Grupa zasobów odzyskiwania zawiera wszystkie zasoby utworzone podczas procesu odzyskiwania oraz nowe zasoby utworzone podczas awarii.  

1. Otwórz grupę zasobów, odzyskiwania i zwróć uwagę, następujące elementy:
    * Wersje odzyskiwania serwerów wykazu i tenants1 z _-odzyskiwania_ sufiks.  Przywrócony katalogu i dzierżawcy bazy danych na tych serwerach wszystkie mają nazw używanych w oryginalnym regionie.

    * _Tenants2-dpt -&lt;użytkownika&gt;-odzyskiwania_ programu SQL server.  Ten serwer jest używany do inicjowania obsługi administracyjnej nowych dzierżaw podczas awarii.
    *   O nazwie usługi App Service, _zdarzenia-wingtip-dpt -&lt;recoveryregion&gt;-&lt;gt & użytkownika_; co jest wystąpienie odzyskiwania aplikacji zdarzenia. 

    ![Firmy Contoso w regionie odzyskiwania](media/saas-dbpertenant-dr-geo-restore/resources-in-recovery-region.png)   
    
1. Otwórz _tenants2-dpt -&lt;użytkownika&gt;-odzyskiwania_ programu SQL server.  Powiadomienie zawiera bazy danych _hawthornhall_ i puli elastycznej _Pool1_.  _Hawthornhall_ bazy danych jest skonfigurowany jako elastycznej bazy danych w _Pool1_ puli elastycznej.

## <a name="change-tenant-data"></a>Zmień dane dzierżawy 
W tym zadaniu zaktualizuj jednej dzierżawy przywróconej bazy danych. Proces repatriacji skopiuje przywróconej bazy danych, które zostały zmienione w regionie oryginalnego. 

1. W przeglądarce, znajdź listę zdarzeń dla Contoso Hall porozumieniu, przewiń zdarzenia i zanotuj ostatnie zdarzenie _poważnie Strauss_.

1. W *PowerShell ISE*, w ...\Learning skryptu ciągłości Modules\Business i Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 po awarii, należy ustawić następującą wartość:
    * **$DemoScenario = 4**, Usuń zdarzenia z dzierżawy w regionie odzyskiwania
1. Naciśnij klawisz **F5** można wykonać skryptu.
1. Odśwież stronę zdarzenia Hall porozumieniu Contoso (http://events.wingtip-dpt.&lt; Użytkownik&gt;.trafficmanager.net/contosoconcerthall) i zwróć uwagę, Brak zdarzeń poważnie Strauss.

W tym momencie w samouczku odzyskano aplikacji, która jest teraz uruchomiona w regionie odzyskiwania.  Zostały udostępnione nowej dzierżawy w regionie odzyskiwania i modyfikować danych przywróconej dzierżaw.  

> [!Note]
> Innych samouczków w próbce nie są przeznaczone do uruchamiania aplikacji w stanie odzyskiwania. Jeśli chcesz zapoznać się z innych samouczków, należy najpierw wycofywania aplikacji.

## <a name="repatriation-process-overview"></a>Omówienie procesu repatriacji

Proces repatriacji aplikacji i jej baz danych do jej oryginalnej regionu zostanie przywrócona po usunięciu awarii.

![Repatriacji geograficzne](media/saas-dbpertenant-dr-geo-restore/geo-restore-repatriation.png) 


Proces:

1. Zatrzymuje wszystkie działania trwającą przywracania i anuluje wszystkie oczekujące lub locie bazy danych żądania przywracania.

1. Uaktywnia ponownie w oryginalnym region dzierżawy baz danych, które nie zostały zmienione od momentu awarii.  W tym baz danych, które nie zostało jeszcze odzyskane i baz danych, które zostały odzyskane, ale nie zostały zmienione później. Ponownie uaktywnione baz danych są dokładnie ostatnio uzyskiwał dzierżawcom.

1. Inicjuje lustrzanego nowych dzierżaw serwerem i elastyczne puli w oryginalnym regionie. Po zakończeniu tej akcji alias nowej dzierżawy zostało zaktualizowane do punktu z tym serwerem. Aktualizacja aliasu powoduje, że nowe dołączania dzierżawy w regionie oryginalnym zamiast region odzyskiwania.

1. Przy użyciu — replikacja geograficzna, przenosi katalogu do oryginalnego region z regionu odzyskiwania.

1. Konfigurowanie puli aktualizacji w regionie oryginalnej, aby były zgodne ze zmianami wprowadzonymi w regionie odzyskiwania podczas awarii.

1. Tworzy wymagane serwery i pule do hostowania żadnych nowych baz danych utworzonych podczas awarii.

1. Przy użyciu — replikacja geograficzna, repatriates przywracane dzierżawy, które są wykonywane po przywróceniu baz danych, które zostały zaktualizowane i wszystkich nowych dzierżawy baz danych udostępniane podczas awarii. 

1. Czyści zasoby utworzone w regionie odzyskiwania podczas procesu przywracania.

Aby ograniczyć liczbę baz danych dzierżawy, które muszą zostać zwrócony do kraju macierzystego, kroki 1 – 3 wykonuje się natychmiast.  

Krok 4 odbywa się tylko, jeśli katalog w regionie odzyskiwania został zmodyfikowany podczas awarii. Katalog jest aktualizowana, jeśli są tworzone nowe dzierżawcy lub żadnej konfiguracji bazy danych lub pula zostanie zmieniona w regionie odzyskiwania.

Jest ważne, czy krok 7 powoduje zakłócenia minimalnego dzierżawcom i nie zostały utracone żadne dane. Na osiągnięcie tego celu, używa procesu _— replikacja geograficzna_.

Przed każdą bazę danych replikacją geograficzną, odpowiednia baza danych w oryginalnej region jest usuwany. Bazy danych w regionie odzyskiwania jest następnie replikacją geograficzną, Tworzenie repliki pomocniczej w oryginalnym regionie. Po zakończeniu replikacji dzierżawcy jest oznaczony jako w trybie offline w katalogu, co spowoduje przerwanie wszystkie połączenia z bazą danych w regionie odzyskiwania. Bazy danych jest następnie tryb pracy awaryjnej, powodując wszelkie oczekujące transakcji do przetworzenia na serwerze pomocniczym, więc nie zostały utracone żadne dane. W tryb failover są wycofywane ról bazy danych.  Pomocniczy w regionie oryginalnego staje się podstawowej bazy danych do odczytu / zapisu, a bazy danych w regionie odzyskiwania staje się pomocniczego tylko do odczytu. Wpis dzierżawy w katalogu jest aktualizowana w celu odwołania w regionie oryginalnej bazy danych i dzierżawcy jest oznaczony w trybie online.  W tym momencie repatriacji bazy danych zostało ukończone. 

Aplikacje powinny być zapisywane z logikę ponawiania do upewnij się, że łączą się ponownie automatycznie podczas połączenia są uszkodzone.  Po ponownym broker połączenie za pomocą katalogu, łączą się z repatriated bazy danych w oryginalnej regionie. Mimo że często nie jest wystąpieniem krótki rozłączenia, istnieje możliwość wycofywania baz danych poza godzinami pracy. 

Gdy zwrócony do bazy danych jest kraju macierzystego, dodatkowej bazy danych w regionie odzyskiwania mogą zostać usunięte. Bazy danych w oryginalnej regionie wykorzystuje przywracaniem geograficznym ochrony DR ponownie.

W kroku 8 zasobów w regionie odzyskiwania, w tym serwerów odzyskiwania oraz pule, są usuwane.

## <a name="run-the-repatriation-script"></a>Uruchom skrypt repatriacji
Teraz załóżmy Wyobraź sobie awarii jest rozwiązany i uruchom skrypt repatriacji.

Po wykonaniu tego samouczka, skrypt natychmiast uaktywnia ponownie klub Jazz Fabrikam i Dojo dereń w regionie oryginalnego ponieważ one nie uległy zmianie. Repatriates następnie nowej dzierżawy Hawthorn Hall i Hall porozumieniu firmy Contoso, ponieważ został on zmodyfikowany. Skrypt repatriates również katalog, który został zaktualizowany po Hawthorn Hall zainicjowano obsługę administracyjną.
  
1. W *PowerShell ISE*, ...\Learning Modules\Business ciągłości i skryptu Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 po awarii.

1. Sprawdź, czy procesu synchronizacji katalogu jest nadal uruchomiona w jego wystąpienie programu PowerShell.  Jeśli to konieczne, uruchom go ponownie, ustawiając:
    * **$DemoScenario = 1**, rozpoczęcia synchronizacji serwera dzierżawy, puli i informacje o konfiguracji bazy danych do katalogu
    * Naciśnij klawisz **F5**, aby uruchomić skrypt.
1.  Następnie można uruchomić procesu repatriacji, należy ustawić:
    * **$DemoScenario = 5**, wycofywania aplikacji w jego oryginalnej region
    * Naciśnij klawisz **F5** do uruchamiania skryptu odzyskiwania w nowym oknie programu PowerShell.  Repatriacji może potrwać kilka minut i mogą być monitorowane w oknie programu PowerShell.
1. Skrypt jest uruchomiona, Odśwież stronę Centrum zdarzeń (http://events.wingtip-dpt.&lt; Użytkownik&gt;. trafficmanager.net)
    * Zwróć uwagę, że wszystkie dzierżaw są online i jest dostępny w trakcie tego procesu.
1. Polecenie klub Jazz firmy Fabrikam, aby go otworzyć. Jeśli nie zmienił tej dzierżawy, zawiadomienie stopce, czy serwer jest już przywrócone na oryginalnym serwerze.
1. Otwórz lub Odśwież stronę zdarzenia Hall porozumieniu Contoso i zwróć uwagę ze stopki, baza danych jest nadal na _-odzyskiwania_ serwera początkowo.  
1. . Odśwież stronę zdarzenia Hall porozumieniu Contoso po zakończeniu procesu repatriacji i zwróć uwagę, czy baza danych jest teraz w danym regionie oryginalnego.
1. Ponownie Odśwież Centrum zdarzeń i otworzyć Hawthorn Hall należy zauważyć, że jego bazy danych również znajduje się w regionie oryginalnego. 

## <a name="clean-up-recovery-region-resources-after-repatriation"></a>Oczyszczanie zasobów region odzyskiwania po repatriacji
Po zakończeniu repatriacji jest bezpiecznie usunąć zasoby w regionie odzyskiwania. 

> [!IMPORTANT]
> Te zasoby szybko, aby zatrzymać wszystkie rozliczeń dla nich zostaną usunięte.

Proces przywracania tworzy wszystkie zasoby odzyskiwania w grupie zasobów odzyskiwania. Procesu oczyszczania spowoduje to usunięcie tej grupy zasobów i usunąć wszystkie odwołania do zasobów z katalogu. 

1. W *PowerShell ISE*, w ...\Learning ciągłości Modules\Business i skryptu Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 po awarii, zestaw:
    * **$DemoScenario = 6**, przestarzałe zasoby zostaną usunięte z regionu odzyskiwania

1. Naciśnij klawisz **F5**, aby uruchomić skrypt.

Czyszczenie skryptów, aplikacji po wstecz gdzie jego uruchomienia.  Możesz ponownie uruchomić skrypt lub wypróbować inne samouczki w tym momencie.

## <a name="designing-the-application-to-ensure-app-and-database-are-colocated"></a>Projektowanie aplikacji, aby upewnić się, aplikacji i bazy danych są wspólnie przechowywane 
Aplikacja zaprojektowano tak, aby zawsze łączy się z wystąpieniem, w tym samym regionie co baza danych dzierżawy. W tym projekcie zmniejsza opóźnienia między aplikacją a bazy danych. Tego rodzaju optymalizacji przyjęto założenie, że interakcji aplikacji w bazie danych jest chattier niż interakcji aplikacji użytkownika.  

Dzierżawy baz danych mogą rozprzestrzeniać się przez odzyskiwania i regiony oryginalnego przez pewien czas podczas repatriacji.  Dla każdej bazy danych aplikacji wyszukuje regionu, w którym baza danych znajduje się w sposób wyszukiwania DNS dla nazwy serwera dzierżawy. W bazie danych SQL nazwa serwera jest alias. Nazwa aliasu serwera zawiera nazwę regionu. Jeśli aplikacja nie jest w tym samym regionie co baza danych, przekierowuje do wystąpienia, w tym samym regionie co serwer bazy danych.  Przekierowanie do wystąpienia, w tym samym regionie co baza danych minimalizuje opóźnienia między aplikacją i bazą danych.  

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku zawarto informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]

>* Użycie katalogu dzierżawcy w celu przechowywania informacji o konfiguracji okresowo odświeżane, dzięki czemu środowisko odzyskiwania odbicie lustrzane ma zostać utworzony w innym regionie
>* Użyj polecenia restore geograficznie, można odzyskać bazy danych Azure SQL w regionie odzyskiwania
>* Aktualizacja katalog dzierżawy, aby odzwierciedlić lokalizacje bazy danych przywróconej dzierżawy  
>* Użyj aliasu DNS, aby umożliwić nawiązanie połączenia z katalogiem dzierżawy w całym bez ponownej konfiguracji aplikacji
>* Replikacja geograficzna umożliwia wycofywania odzyskanej bazy danych do ich oryginalnej regionu po awarii

Teraz, spróbuj [odzyskiwania po awarii dla wielodostępnych aplikacji SaaS przy użyciu replikacji z bazy danych dla georeplikacji](saas-dbpertenant-dr-geo-replication.md) informacje na temat znacznie ograniczyć czas potrzebny do odzyskania dużych aplikacji wielodostępnych przy użyciu replikacji geograficznej.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Dodatkowe samouczki, które zależą od aplikacji Wingtip SaaS](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-wtp-overview#sql-database-wingtip-saas-tutorials)
