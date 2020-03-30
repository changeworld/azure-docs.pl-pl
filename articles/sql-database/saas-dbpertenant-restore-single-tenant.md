---
title: Przywracanie bazy danych w wielodostępnym ucho aplikacji SaaS
description: Dowiedz się, jak przywrócić bazę danych SQL jednej dzierżawcy po przypadkowym usunięciu danych
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
ms.date: 12/04/2018
ms.openlocfilehash: a54e8e5629f6f8ad688b6fe11bbf50fc038095bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73818528"
---
# <a name="restore-a-single-tenant-with-a-database-per-tenant-saas-application"></a>Przywracanie pojedynczej dzierżawy za pomocą aplikacji SaaS bazy danych na dzierżawcę

Model bazy danych na dzierżawcę ułatwia przywracanie pojedynczej dzierżawy do poprzedniego punktu w czasie bez wpływu na innych dzierżawców.

W tym samouczku dowiesz się dwóch wzorców odzyskiwania danych:

> [!div class="checklist"]
> * Przywracanie bazy danych do równoległej bazy danych (obok siebie).
> * Przywróć bazę danych w miejscu, zastępując istniejącą bazę danych.

|||
|:--|:--|
| Przywracanie do równoległej bazy danych | Ten wzorzec może służyć do zadań, takich jak przegląd, inspekcja i zgodność, aby umożliwić dzierżawcy do inspekcji swoich danych z wcześniejszego punktu. Bieżąca baza danych dzierżawy pozostaje w trybie online i pozostaje niezmieniona. |
| Przywróć na swoim miejscu | Ten wzorzec jest zazwyczaj używany do odzyskiwania dzierżawy do wcześniejszego punktu, po dzierżawy przypadkowo usuwa lub uszkadza dane. Oryginalna baza danych jest ściągnana i zastępowana przywróconą bazą danych. |
|||

Do wykonania zadań opisanych w tym samouczku niezbędne jest spełnienie następujących wymagań wstępnych:

* Aplikacja Wingtip SaaS jest wdrażana. Aby wdrożyć w mniej niż pięć minut, zobacz [Wdrażanie i eksplorowanie aplikacji SaaS Wingtip](saas-dbpertenant-get-started-deploy.md).
* Zainstalowany jest program Azure PowerShell. Aby uzyskać szczegółowe informacje, zobacz [Wprowadzenie do programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-saas-tenant-restore-patterns"></a>Wprowadzenie do wzorców przywracania dzierżawy SaaS

Istnieją dwa proste wzorce przywracania danych poszczególnych dzierżawców. Ponieważ bazy danych dzierżawy są odizolowane od siebie, przywracanie jednej dzierżawy nie ma wpływu na dane innych dzierżawców. Funkcja azure SQL Database point-in-time-restore (PITR) jest używana w obu wzorcach. PITR zawsze tworzy nową bazę danych.

* **Przywracanie równolegle:** W pierwszym wzorcu nowa równoległa baza danych jest tworzona obok bieżącej bazy danych dzierżawy. Dzierżawy jest następnie dostęp tylko do odczytu do przywróconej bazy danych. Przywrócone dane mogą być przeglądane i potencjalnie używane do zastępowanie bieżących wartości danych. To do projektanta aplikacji, aby określić, jak dzierżawca uzyskuje dostęp do przywróconej bazy danych i jakie opcje odzyskiwania są dostarczane. Po prostu pozwalając dzierżawcy do przeglądania ich danych we wcześniejszym punkcie może być wszystko, co jest wymagane w niektórych scenariuszach.

* **Przywracanie w miejscu:** Drugi wzorzec jest przydatne, jeśli dane zostały utracone lub uszkodzone i dzierżawcy chce powrócić do wcześniejszego punktu. Dzierżawca jest pobierana poza wiersz, gdy baza danych jest przywracana. Oryginalna baza danych zostanie usunięta, a przywrócona baza danych zostanie zmieniona. Łańcuch kopii zapasowej oryginalnej bazy danych pozostaje dostępny po usunięciu, dzięki czemu można przywrócić bazę danych do wcześniejszego punktu w czasie, jeśli to konieczne.

Jeśli baza danych używa [aktywnej replikacji geograficznej](sql-database-active-geo-replication.md) i przywracania równolegle, zaleca się skopiowanie wszelkich wymaganych danych z przywróconej kopii do oryginalnej bazy danych. Jeśli oryginalna baza danych zostanie zastąpiona przywróconą bazą danych, należy ponownie skonfigurować i ponownie zsynchronizować replikację geograficzną.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Pobierz skrypty aplikacji bazy danych SaaS dla bazy danych Wingtip na dzierżawcę

Skrypty wielodostępne bazy danych Wingtip Tickets SaaS i kod źródłowy aplikacji są dostępne w repozytorium [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub. Aby uzyskać kroki, aby pobrać i odblokować skrypty SaaS Wingtip Tickets, zobacz [ogólne wskazówki](saas-tenancy-wingtip-app-guidance-tips.md).

## <a name="before-you-start"></a>Przed rozpoczęciem

Po utworzeniu bazy danych może upłynąć od 10 do 15 minut, zanim pierwsza pełna kopia zapasowa będzie dostępna do przywrócenia. Jeśli właśnie zainstalowano aplikację, może być konieczne odczekanie kilku minut przed wypróbowaniem tego scenariusza.

## <a name="simulate-a-tenant-accidentally-deleting-data"></a>Symulowanie przypadkowego usunięcia danych przez dzierżawę

Aby zademonstrować te scenariusze odzyskiwania, najpierw "przypadkowo" usuń zdarzenie w jednej z baz danych dzierżawy. 

### <a name="open-the-events-app-to-review-the-current-events"></a>Otwórz aplikację Wydarzenia, aby przejrzeć bieżące zdarzenia

1. Otwórz Centrum wydarzeńhttp://events.wtp.&lt&gt;(;user .trafficmanager.net) i wybierz pozycję **Contoso Concert Hall**.

   ![Centrum zdarzeń](media/saas-dbpertenant-restore-single-tenant/events-hub.png)

2. Przewiń listę zdarzeń i zanotuj ostatnie zdarzenie na liście.

   ![Pojawia się ostatnie wydarzenie](media/saas-dbpertenant-restore-single-tenant/last-event.png)

### <a name="accidentally-delete-the-last-event"></a>"Przypadkowo" usuń ostatnie zdarzenie

1. W programie PowerShell ISE otwórz ... \\Moduły\\szkoleniowe Ciągłość\\działania i\\przywracanie po awariiTenant*Demo-RestoreTenant.ps1*i ustawić następującą wartość:

   * **$DemoScenario****1**, *Usuń ostatnie zdarzenie (bez sprzedaży biletów)*. = 
2. Naciśnij klawisz F5, aby uruchomić skrypt i usunąć ostatnie zdarzenie. Zostanie wyświetlony następujący komunikat potwierdzający:

   ```Console
   Deleting last unsold event from Contoso Concert Hall ...
   Deleted event 'Seriously Strauss' from Contoso Concert Hall venue.
   ```

3. Zostanie otwarta strona Zdarzenia Contoso. Przewiń w dół i sprawdź, czy zdarzenie nie ma. Jeśli zdarzenie nadal znajduje się na liście, wybierz pozycję **Odśwież** i sprawdź, czy go nie ma.
   ![Ostatnie zdarzenie usunięte](media/saas-dbpertenant-restore-single-tenant/last-event-deleted.png)

## <a name="restore-a-tenant-database-in-parallel-with-the-production-database"></a>Przywracanie bazy danych dzierżawy równolegle z produkcyjną bazą danych

To ćwiczenie przywraca contoso concert hall bazy danych do punktu w czasie przed zdarzenie zostało usunięte. W tym scenariuszu założono, że chcesz przejrzeć usunięte dane w równoległej bazie danych.

 Skrypt *Restore-TenantInParallel.ps1* tworzy równoległą bazę danych dzierżawy o nazwie *ContosoConcertHall\_stary*, z równoległym wpisem katalogu. Ten wzorzec przywracania najlepiej nadaje się do odzyskiwania po utracie danych niewielkie. Można również użyć tego wzorca, jeśli trzeba przejrzeć dane do celów zgodności lub inspekcji. Jest to zalecane podejście podczas korzystania z [aktywnej replikacji geograficznej](sql-database-active-geo-replication.md).

1. Ukończ [sekcję Symulowanie dzierżawcy przypadkowo usuwając dane.](#simulate-a-tenant-accidentally-deleting-data)
2. W programie PowerShell ISE otwórz ... \\Moduły\\szkoleniowe Ciągłość\\działania i\\przywracanie odzyskiwania po awariiTenant_Demo-RestoreTenant.ps1_.
3. Ustaw **$DemoScenario** = **2**, *Przywróć dzierżawę równolegle*.
4. Aby uruchomić skrypt, naciśnij klawisz F5.

Skrypt przywraca bazę danych dzierżawy do punktu w czasie przed usunięciem zdarzenia. Baza danych zostanie przywrócona do nowej bazy danych o nazwie _ContosoConcertHall\_stary_. Metadane katalogu, który istnieje w tej przywróconej bazy danych jest usuwany, a następnie baza danych jest dodawany do katalogu przy użyciu klucza skonstruowanego z *ContosoConcertHall\_starej* nazwy.

Skrypt demonstracyjny otwiera stronę zdarzeń dla tej nowej bazy danych dzierżawy w przeglądarce. Uwaga z ```http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/contosoconcerthall_old``` adresu URL, że ta strona pokazuje dane z przywróconej bazy *danych,* gdzie _old jest dodawany do nazwy.

Przewiń zdarzenia wymienione w przeglądarce, aby potwierdzić, że zdarzenie usunięte w poprzedniej sekcji zostało przywrócone.

Uwidacznianie przywróconej dzierżawy jako dodatkowej dzierżawy z własną aplikacją Zdarzenia, jest mało prawdopodobne, aby zapewnić dostęp do przywróconych danych dzierżawy. Służy do zilustrowania wzorca przywracania. Zazwyczaj dajesz dostęp tylko do odczytu do starych danych i zachowujesz przywróconą bazę danych przez zdefiniowany okres. W przykładzie można usunąć przywrócony wpis dzierżawy po zakończeniu, uruchamiając _scenariusz Usuń przywróconej dzierżawy._

1. Ustaw **$DemoScenario** = **4**, *Usuń odrestaurowaną dzierżawę*.
2. Aby uruchomić skrypt, naciśnij klawisz F5.
3. Stary wpis *ContosoConcertHall\_* jest teraz usuwany z katalogu. Zamknij stronę zdarzeń dla tej dzierżawy w przeglądarce.

## <a name="restore-a-tenant-in-place-replacing-the-existing-tenant-database"></a>Przywracanie dzierżawy w miejscu, zastępując istniejącą bazę danych dzierżawy

To ćwiczenie przywraca dzierżawy Sali Koncertowej Contoso do punktu przed usunięciem zdarzenia. Skrypt *Restore-TenantInPlace* przywraca bazę danych dzierżawy do nowej bazy danych i usuwa oryginał. Ten wzorzec przywracania najlepiej nadaje się do odzyskiwania po poważne uszkodzenie danych i dzierżawy może być konieczne uwzględnienie znacznej utraty danych.

1. W programie PowerShell ISE otwórz plik **Demo-RestoreTenant.ps1.**
2. Ustaw **$DemoScenario** = **5**, *Przywróć dzierżawę w miejscu*.
3. Aby uruchomić skrypt, naciśnij klawisz F5.

Skrypt przywraca bazę danych dzierżawy do punktu przed usunięciem zdarzenia. Najpierw zajmuje Contoso Concert Hall dzierżawy off line, aby zapobiec dalszym aktualizacji. Następnie równoległa baza danych jest tworzona przez przywracanie z punktu przywracania. Przywrócona baza danych ma nazwę sygnatury czasowej, aby upewnić się, że nazwa bazy danych nie jest w konflikcie z istniejącą nazwą bazy danych dzierżawy. Następnie stara baza danych dzierżawy zostanie usunięta, a przywrócona baza danych zostanie zmieniona na oryginalną nazwę bazy danych. Na koniec Contoso Concert Hall jest przełączony do trybu online, aby umożliwić aplikacji dostęp do przywróconej bazy danych.

Pomyślnie przywrócić bazę danych do punktu w czasie przed zdarzenie zostało usunięte. Po otwarciu strony **Zdarzenia** upewnij się, że zostało przywrócone ostatnie zdarzenie.

Po przywróceniu bazy danych, trwa kolejne 10 do 15 minut, zanim pierwsza pełna kopia zapasowa jest dostępna do przywrócenia z ponownie.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przywracanie bazy danych do równoległej bazy danych (obok siebie).
> * Przywracanie bazy danych w miejscu.

Wypróbuj samouczek [Schematu zarządzania bazą danych dzierżawy.](saas-tenancy-schema-management.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Dodatkowe samouczki, które opierają się na aplikacji Wingtip SaaS](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Omówienie zagadnień dotyczących ciągłości działalności biznesowej zapewnianej przez usługę Azure SQL Database](sql-database-business-continuity.md)
* [Dowiedz się więcej o kopiach zapasowych bazy danych SQL](sql-database-automated-backups.md)
