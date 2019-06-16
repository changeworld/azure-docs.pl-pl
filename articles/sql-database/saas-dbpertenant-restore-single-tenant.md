---
title: Przywracanie bazy danych Azure SQL w wielodostępnej aplikacji SaaS | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przywrócić bazy danych SQL w pojedynczej dzierżawy po przypadkowym usunięciu danych
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 4059b0f979e7e6856905f1759129167d62d7b5f5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60326345"
---
# <a name="restore-a-single-tenant-with-a-database-per-tenant-saas-application"></a>Przywracanie pojedynczej dzierżawy w przypadku aplikacji SaaS database dla dzierżawcy

Model bazy danych dla dzierżawcy ułatwia przywracanie pojedynczej dzierżawy do wcześniejszego punktu w czasie, bez wywierania wpływu na innych dzierżawców.

W tym samouczku dowiesz się dwa wzorce odzyskiwania danych:

> [!div class="checklist"]
> * Przywracanie bazy danych do równoległego bazy danych (obok siebie).
> * Przywracanie bazy danych w miejscu, zastępując istniejącą bazę danych.

|||
|:--|:--|
| Przywracanie do równoległego bazy danych | Ten wzorzec może służyć do zadania, takie jak przeglądanie, inspekcja i zgodność umożliwia dzierżawy sprawdzić swoje dane z wcześniejszego punktu. Dzierżawcy bieżąca baza danych pozostaje online, jak i bez zmian. |
| Przywracanie w miejscu | Ten wzór jest zazwyczaj używany do odzyskania dzierżawy do wcześniejszego punktu po dzierżawy przypadkowo usunie lub wykonana spowoduje uszkodzenie danych. Oryginalna baza danych jest poświęcony wyłączony i zastąpiona przywróconą bazą danych. |
|||

Do wykonania zadań opisanych w tym samouczku niezbędne jest spełnienie następujących wymagań wstępnych:

* Aplikacja SaaS o nazwie Wingtip jest wdrażana. Aby wdrożyć w mniej niż pięć minut, zobacz [wdrażanie i eksplorowanie aplikacji SaaS o nazwie Wingtip](saas-dbpertenant-get-started-deploy.md).
* Zainstalowany jest program Azure PowerShell. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z programem Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-saas-tenant-restore-patterns"></a>Wprowadzenie do wzorców przywracania dzierżawy SaaS

Istnieją dwa wzorce proste do przywrócenia danych dzierżawy usługi. Ponieważ baz danych dzierżawy są od siebie odizolowane, przywracanie jednej dzierżawy nie ma wpływu na dane w dowolnej innej dzierżawie. Funkcja (Odzyskiwanie) punkt w czasie przywracania bazy danych SQL Azure jest używana w obu wzorców. Odzyskiwanie zawsze tworzy nową bazę danych.

* **Przywracanie w sposób równoległy**: Na pierwszy wzorzec równoległe nowej bazy danych jest tworzona wraz z bieżącej bazy danych dzierżawy. Dzierżawa następnie otrzymuje dostęp tylko do odczytu do przywróconej bazy danych. Przywróconych danych do przejrzenia i potencjalnie umożliwia zastąpienie bieżących wartości danych. To Ty projektanta aplikacji, aby określić, jak dzierżawcy uzyskuje dostęp do przywróconej bazy danych i jakie opcje odzyskiwania są dostępne. Po prostu umożliwieniu dzierżawcom przejrzeć swoje dane na wcześniejszą musi być wszystko, co jest wymagane w niektórych scenariuszach.

* **Przywracanie w miejscu**: Drugi wzorzec jest przydatny w przypadku, jeśli dane zostały utracone lub uszkodzone i dzierżawy chce powrócić do wcześniejszego punktu. Dzierżawy jest przełączany do trybu offline podczas przywróceniu bazy danych. Oryginalna baza danych zostanie usunięty, a następnie przywrócona baza danych została zmieniona. Łańcuch kopii zapasowych oryginalnej bazy danych pozostają dostępne po usunięciu, dzięki czemu można przywrócić bazy danych do wcześniejszego punktu w czasie, jeśli to konieczne.

Jeśli baza danych używa [aktywnej replikacji geograficznej](sql-database-active-geo-replication.md) i przywracanie w sposób równoległy, firma Microsoft zaleca, skopiuj wszystkie wymagane dane z przywróconej kopii do oryginalnej bazy danych. Jeśli zastąpić oryginalną bazę danych przywróconą bazą danych, należy ponownie skonfigurować i zsynchronizować replikację geograficzną.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Pobierz skrypty bazy danych dla dzierżawcy aplikacji SaaS o nazwie Wingtip Tickets

Skryptów aplikacji Wingtip Tickets SaaS wielodostępnej w bazie danych i kod źródłowy aplikacji są dostępne w [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) repozytorium GitHub. Aby uzyskać instrukcje dotyczące pobierania i odblokować skrypty SaaS o nazwie Wingtip Tickets, zobacz [ogólne wskazówki dotyczące](saas-tenancy-wingtip-app-guidance-tips.md).

## <a name="before-you-start"></a>Przed rozpoczęciem

Po utworzeniu bazy danych może potrwać 10 – 15 minut, zanim pierwsza pełna kopia zapasowa będzie można przywrócić z. Jeśli zainstalowano tylko aplikacji, może być konieczne Poczekaj kilka minut, zanim spróbujesz tego scenariusza.

## <a name="simulate-a-tenant-accidentally-deleting-data"></a>Symulowanie dzierżawy przypadkowemu usunięciu danych

Aby zademonstrować tych scenariuszy odzyskiwania, najpierw "przypadkowo" Usuń zdarzenie w jednej z baz danych dzierżaw. 

### <a name="open-the-events-app-to-review-the-current-events"></a>Otwórz aplikację zdarzeń, aby przejrzeć bieżące wydarzenia

1. Otwórz Centrum zdarzeń (http://events.wtp.&lt; użytkownika&gt;. trafficmanager.net) i wybierz **firmy Contoso Concert Hall**.

   ![Centrum zdarzeń](media/saas-dbpertenant-restore-single-tenant/events-hub.png)

2. Przewiń listę zdarzeń i zwróć uwagę na ostatnie zdarzenie na liście.

   ![Zostanie wyświetlone ostatnie zdarzenie](media/saas-dbpertenant-restore-single-tenant/last-event.png)

### <a name="accidentally-delete-the-last-event"></a>"Przypadkowo" Usuwanie ostatniego zdarzenia

1. W środowisku PowerShell ISE Otwórz... \\Learning Modules\\ciągłość biznesową i odzyskiwanie po awarii\\RestoreTenant\\*RestoreTenant.ps1 pokaz*i Ustaw następującą wartość:

   * **$DemoScenario** = **1**, *usuwania ostatniego zdarzenia (z bez sprzedaży biletów)* .
2. Naciśnij klawisz F5, aby uruchomić skrypt i usunąć ostatniego zdarzenia. Zostanie wyświetlony następujący komunikat potwierdzający:

   ```Console
   Deleting last unsold event from Contoso Concert Hall ...
   Deleted event 'Seriously Strauss' from Contoso Concert Hall venue.
   ```

3. Zostanie otwarta strona zdarzenia firmy Contoso. Przewiń w dół, a następnie sprawdź, czy zdarzenie jest usunięty. Jeśli zdarzenie jest nadal na liście, wybierz **Odśwież** i sprawdź, czy przeszedł.
   ![Ostatnie zdarzenie usunięte](media/saas-dbpertenant-restore-single-tenant/last-event-deleted.png)

## <a name="restore-a-tenant-database-in-parallel-with-the-production-database"></a>Przywracanie bazy danych dzierżawy równolegle z produkcyjnej bazy danych

To ćwiczenie przywraca bazy danych firmy Contoso Concert Hall do punktu w czasie, zanim zdarzenie zostało usunięte. W tym scenariuszu założono, że usunięto dane w bazie danych równoległych.

 *TenantInParallel.ps1 przywracania* skrypt tworzy równoległy bazy danych o nazwie *ContosoConcertHall\_stare*, za pomocą wpisu katalogu równoległych. Ten wzorzec przywracania najlepiej nadaje się do odzyskiwania przed utratą danych pomocniczych. Ten wzorzec może również użyć w Jeśli chcesz przeglądać dane dla celów inspekcji lub zgodności. Przedstawia zalecane podejście, gdy używasz [aktywnej replikacji geograficznej](sql-database-active-geo-replication.md).

1. Wykonaj [symulować dzierżawy przypadkowemu usunięciu danych](#simulate-a-tenant-accidentally-deleting-data) sekcji.
2. W środowisku PowerShell ISE Otwórz... \\Learning Modules\\ciągłość biznesową i odzyskiwanie po awarii\\RestoreTenant\\_RestoreTenant.ps1 pokaz_.
3. Ustaw **$DemoScenario** = **2**, *dzierżawy przywracania równolegle*.
4. Aby uruchomić skrypt, naciśnij klawisz F5.

Skrypt spowoduje przywrócenie bazy danych dzierżaw do punktu w czasie przed usunięciem zdarzenia. Przywróceniu bazy danych do nowej bazy danych o nazwie _ContosoConcertHall\_stare_. Metadane katalogu, który znajduje się w tej przywróconej bazy danych zostanie usunięty, a następnie baza danych zostanie dodany do katalogu przy użyciu klucza skonstruowany na podstawie *ContosoConcertHall\_stare* nazwy.

Pokaz skryptu zostanie otwarta strona zdarzenia dla tej nowej bazy danych dzierżawy w przeglądarce. Uwaga z adresu URL ```http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/contosoconcerthall_old``` , ta strona pokazuje dane z przywróconej bazy danych gdzie *_old* jest dodawany do nazwy.

Przewiń zdarzenia wyświetlane w przeglądarce, aby upewnić się, że zdarzenia usunięto w poprzedniej sekcji została przywrócona.

Udostępnianie przywróconej dzierżawy jako dodatkowe dzierżawy z własną aplikacją zdarzenia jest mało prawdopodobne, aby jak uzyskiwanie dostępu dzierżawy, aby przywróconych danych. Służy on do zilustrowania wzorzec przywracania. Zazwyczaj zapewnić dostęp tylko do odczytu do starych danych i zachowania przywróconej bazy danych w zdefiniowanym okresie. W tym przykładzie można usunąć wpisu przywróconej dzierżawy, po zakończeniu, uruchamiając _Usuń przywrócić dzierżawy_ scenariusza.

1. Ustaw **$DemoScenario** = **4**, *Usuń przywrócić dzierżawy*.
2. Aby uruchomić skrypt, naciśnij klawisz F5.
3. *ContosoConcertHall\_stare* zapisu teraz jest usuwany z katalogu. Zamknij stronę zdarzenia dla tej dzierżawy w przeglądarce.

## <a name="restore-a-tenant-in-place-replacing-the-existing-tenant-database"></a>Przywracanie dzierżawy w miejscu, zastępując istniejącą bazę danych dzierżawy

To ćwiczenie przywraca dzierżawy firmy Contoso Concert Hall do punktu przed usunięciem zdarzenia. *TenantInPlace przywracania* skrypt spowoduje przywrócenie bazy danych do nowej bazy danych i usuwa oryginalny. Ten wzorzec przywracania najlepiej nadaje się do odzyskiwania danych poważne uszkodzenie i dzierżawcy może być konieczne pomieścić utracie dużej ilości danych.

1. Otwórz w programie PowerShell ISE **RestoreTenant.ps1 pokaz** pliku.
2. Ustaw **$DemoScenario** = **5**, *dzierżawy przywracania w miejscu*.
3. Aby uruchomić skrypt, naciśnij klawisz F5.

Skrypt przywrócenie bazy danych dzierżaw do punktu przed usunięciem zdarzenia. Najpierw zajmuje dzierżawę firmy Contoso Concert Hall wyłączony, aby uniemożliwić dalsze aktualizacje. Równoległe bazy danych jest tworzona przez przywrócenie z punktu przywracania. Przywrócona baza danych ma nazwę z sygnatury czasowej do upewnij się, że nazwa bazy danych jest zgodny z istniejącej nazwy dzierżawy w bazie danych. Następnie jest usunąć stare bazy danych dzierżaw i przywróconej bazy danych została zmieniona na oryginalną nazwę bazy danych. Na koniec firmy Contoso Concert Hall jest udostępnione online, aby zezwolić na dostęp aplikacji do przywróconej bazy danych.

Pomyślnie przywrócono bazy danych do punktu w czasie, zanim zdarzenie zostało usunięte. Gdy **zdarzenia** zostanie otwarta strona, upewnij się, że ostatnie zdarzenie zostało przywrócone.

Po przywróceniu bazy danych trwa inny 10 do 15 minut, zanim pierwsza pełna kopia zapasowa będzie można przywrócić z ponownie.

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przywracanie bazy danych do równoległego bazy danych (obok siebie).
> * Przywracanie bazy danych w miejscu.

Spróbuj [schemat bazy danych dzierżawy Zarządzaj](saas-tenancy-schema-management.md) samouczka.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Dodatkowe samouczki, które są kompilowane w aplikacji SaaS o nazwie Wingtip](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Omówienie ciągłości działania usługi Azure SQL Database](sql-database-business-continuity.md)
* [Więcej informacji na temat tworzenia kopii zapasowych bazy danych SQL](sql-database-automated-backups.md)
