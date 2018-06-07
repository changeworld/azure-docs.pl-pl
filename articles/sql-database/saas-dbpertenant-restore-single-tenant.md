---
title: Przywracanie bazy danych Azure SQL w wielodostępnych aplikacji SaaS | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przywrócić bazę danych SQL pojedynczej dzierżawy po przypadkowym usunięciu danych
keywords: samouczek usługi sql database
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.reviewer: billgib
ms.openlocfilehash: 319195ceb98b5b760d4d92055faa6e09c8fb0e3d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34644352"
---
# <a name="restore-a-single-tenant-with-a-database-per-tenant-saas-application"></a>Przywracanie pojedynczej dzierżawy z bazy danych na dzierżawy aplikacji SaaS

Model bazy danych dla dzierżawcy można łatwo przywrócić pojedynczej dzierżawy do wcześniejszego punktu w czasie bez wpływu na innych dzierżawców.

Z tego samouczka dowiesz się dwa wzorce odzyskiwania danych:

> [!div class="checklist"]

> * Przywróć bazę danych do równoległego bazy danych (obok siebie).
> * Przywracanie bazy danych w miejscu, zastępując istniejące bazy danych.


|||
|:--|:--|
| Przywracanie do równoległego bazy danych | Ten wzorzec umożliwia wykonywanie zadań takich jak przeglądu, inspekcji i zgodności Zezwalaj dzierżawcę, aby sprawdzić ich dane z wcześniejszego punktu. Dzierżawcy bieżąca baza danych pozostaje bez zmian i online. |
| Przywracanie w miejscu | Ten wzorzec jest zwykle używane do odzyskania dzierżawy do wcześniejszego punktu, po dzierżawcy przypadkowo usuwa lub uszkodzi danych. Oryginalnej bazy danych jest przełączona wyłączony oraz zastąpiony przywróconej bazy danych. |
|||

Do wykonania zadań opisanych w tym samouczku niezbędne jest spełnienie następujących wymagań wstępnych:

* Aplikacja Wingtip SaaS jest wdrażana. Aby wdrożyć w mniej niż 5 minut, zobacz [wdrażania i aplikacji Wingtip SaaS](saas-dbpertenant-get-started-deploy.md).
* Zainstalowany jest program Azure PowerShell. Aby uzyskać więcej informacji, zobacz [wprowadzenie do programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-saas-tenant-restore-patterns"></a>Wprowadzenie do wzorców przywracania dzierżawy SaaS

Istnieją dwa proste wzorce w celu przywrócenia danych dzierżawy usługi. Ponieważ dzierżawy baz danych są od siebie odizolowane, Przywracanie jednego dzierżawcy nie ma wpływu na dane żadnych innych dzierżawców. Funkcja (PITR) punkt w czasie przywracania bazy danych SQL Azure jest używana w oba wzorce. PITR zawsze tworzy nową bazę danych.   

* **Przywróć równolegle**: we wzorcu pierwszy równoległych nowej bazy danych jest tworzony obok dzierżawcy bieżącej bazy danych. Dzierżawca następnie otrzymuje dostęp tylko do odczytu do przywróconej bazy danych. Przywróconych danych można przejrzeć i potencjalnie pozwala zastąpić bieżące wartości danych. To projektanta aplikacji, aby określić sposób dzierżawcy uzyskuje dostęp do przywróconej bazy danych i jakie opcje odzyskiwania są udostępniane. Po prostu stosowanie dzierżawy przejrzeć swoje dane na wcześniejszą może być wszystkie, który jest wymagany w niektórych scenariuszach. 

* **Przywracanie w miejscu**: drugi wzorzec jest przydatna, jeśli dane zostały utracone lub uszkodzony i dzierżawcy chce przywrócenie do wcześniejszego punktu. Dzierżawca jest pobierana wyłączony podczas przywróceniu bazy danych. Oryginalnej bazy danych zostanie usunięta, a przywróconej bazy danych zostanie zmieniona. Aby przywrócić bazy danych do wcześniejszego punktu w czasie, w razie potrzeby, pozostaje dostępne po usunięciu, łańcuch kopii zapasowych oryginalnej bazy danych.

Jeśli baza danych używa [— replikacja geograficzna](sql-database-geo-replication-overview.md) i przywracanie równolegle, zalecamy skopiować wszystkie wymagane dane z przywróconej kopii do oryginalnej bazy danych. Można zastąpić oryginalnej bazy danych przywróconej bazy danych, musisz skonfigurować ponownie i przeprowadź ponowną synchronizację — replikacja geograficzna.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Pobierz skrypty bazy danych dla dzierżawy aplikacji SaaS biletów Wingtip

Skrypty Wingtip biletów SaaS wielodostępnej w bazie danych i kodu źródłowego aplikacji są dostępne w [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) repozytorium GitHub. Aby uzyskać instrukcje dotyczące pobierania i odblokować skrypty Wingtip biletów SaaS, zobacz [ogólne wskazówki](saas-tenancy-wingtip-app-guidance-tips.md).

## <a name="before-you-start"></a>Przed rozpoczęciem

Po utworzeniu bazy danych może potrwać 10 do 15 minut, zanim będzie można przywrócić z pierwsza pełna kopia zapasowa. Jeśli aplikacja został właśnie zainstalowany, może być konieczne Poczekaj kilka minut przed podjęciem próby tego scenariusza.

## <a name="simulate-a-tenant-accidentally-deleting-data"></a>Symulowanie dzierżawcy przypadkowego usunięcia danych

Aby zademonstrować tych scenariuszy odzyskiwania, najpierw "przypadkowo" usunąć zdarzenia w jednej z baz danych dzierżawy. 

### <a name="open-the-events-app-to-review-the-current-events"></a>Otwórz aplikację zdarzeń do przeglądania zdarzeń bieżącego

1. Otwórz Centrum zdarzeń (http://events.wtp.&lt; użytkownika&gt;. trafficmanager.net) i wybierz **Hall porozumieniu Contoso**.

   ![Centrum zdarzeń](media/saas-dbpertenant-restore-single-tenant/events-hub.png)

2. Przewiń listę zdarzeń i zanotuj ostatnie zdarzenie na liście.

   ![Zostanie wyświetlone ostatnie zdarzenie](media/saas-dbpertenant-restore-single-tenant/last-event.png)


### <a name="accidentally-delete-the-last-event"></a>"Przypadkowo" Usuń ostatnie zdarzenie

1. W programie PowerShell ISE Otwórz... \\Modułów uczenia\\ciągłość prowadzenia działalności biznesowej i odzyskiwania po awarii\\RestoreTenant\\*RestoreTenant.ps1 pokaz*i ustawić następującą wartość:

   * **$DemoScenario** = **1**, *Usuń ostatnie zdarzenie (ze sprzedaży biletów)*.
2. Naciśnij klawisz F5, aby uruchomić skrypt i usunąć ostatniego zdarzenia. Zostanie wyświetlony następujący komunikat potwierdzenia:

   ```Console
   Deleting last unsold event from Contoso Concert Hall ...
   Deleted event 'Seriously Strauss' from Contoso Concert Hall venue.
   ```

3. Zostanie otwarta strona zdarzenia Contoso. Przewiń w dół i sprawdź, czy zdarzenie został usunięty. Jeśli zdarzenie jest nadal na liście, wybierz **Odśwież** i upewnij się, że został usunięty.

   ![Ostatnie zdarzenie usunięte](media/saas-dbpertenant-restore-single-tenant/last-event-deleted.png)


## <a name="restore-a-tenant-database-in-parallel-with-the-production-database"></a>Przywróć bazę danych dzierżawy równolegle z produkcyjną bazę danych

Tego ćwiczenia przywraca bazę danych Hall porozumieniu Contoso do punktu w czasie, zanim zdarzenie zostało usunięte. W tym scenariuszu założono, że przejrzeć usunięte dane w bazie danych równoległych.

 *TenantInParallel.ps1 przywracania* skrypt tworzy równoległych dzierżawy bazy danych o nazwie *ContosoConcertHall\_starego*, z wpisu katalogu równoległych. Ten wzorzec przywracania najlepiej nadaje się do przywrócenia przed utratą danych pomocniczych. Można też użyć tego wzorca Jeśli musisz sprawdzić danych dotyczących zgodności lub potrzeby inspekcji. Korzystając z zalecanym podejściem jest [— replikacja geograficzna](sql-database-geo-replication-overview.md).

1. Zakończenie [symulować dzierżawcy przypadkowego usunięcia danych](#simulate-a-tenant-accidentally-deleting-data) sekcji.
2. W programie PowerShell ISE Otwórz... \\Modułów szkoleniowych\\ciągłość prowadzenia działalności biznesowej i odzyskiwania po awarii\\RestoreTenant\\_RestoreTenant.ps1 pokaz_.
3. Ustaw **$DemoScenario** = **2**, *dzierżawy przywracania równolegle*.
4. Aby uruchomić skrypt, naciśnij klawisz F5.

Skrypt przywraca bazę danych dzierżawy do punktu w czasie, aby usunąć zdarzenia. Bazy danych są odzyskiwane do nowej bazy danych o nazwie _ContosoConcertHall\_starego_. Metadane katalogu, w tym przywróconej bazy danych zostanie usunięta, a następnie bazy danych jest dodane do katalogu przy użyciu klucza utworzone na podstawie *ContosoConcertHall\_starego* nazwy.

Pokaz skryptu spowoduje otwarcie strony zdarzeń dla tej nowej bazy danych dzierżawy w przeglądarce. Uwaga z adresu URL ```http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/contosoconcerthall_old``` tej stronie znajdują się dane z bazy danych przywróconej gdzie *_old* jest dodawany do nazwy.

Przywrócono przewijania zdarzenia wyświetlane w przeglądarce, aby potwierdzić, że zdarzenie usunięte w poprzedniej sekcji.

Udostępnianie przywróconej dzierżawy jako dodatkowe dzierżawy, z własnej aplikacji zdarzeń jest mało prawdopodobne, za jaki zapewnia dzierżawcy dostęp do przywróconych danych. Służy ona zilustrować wzorzec przywracania. Zazwyczaj zapewniają dostęp tylko do odczytu do starych danych i zachowania przywróconej bazy danych w zdefiniowanym okresie. W przykładzie można usunąć wpis przywróconej dzierżawy, po zakończeniu przez uruchomienie _Usuń przywrócić dzierżawy_ scenariusza.

1. Ustaw **$DemoScenario** = **4**, *Usuń przywrócić dzierżawy*.
2. Aby uruchomić skrypt, naciśnij klawisz F5.
3. *ContosoConcertHall\_starego* wpis jest teraz usunięty z katalogu. Zamknij stronę zdarzeń dla tej dzierżawy w przeglądarce.


## <a name="restore-a-tenant-in-place-replacing-the-existing-tenant-database"></a>Przywróć dzierżawcy w miejscu, zastępując istniejące bazy danych dzierżawy

Tego ćwiczenia przywraca dzierżawy Hall porozumieniu Contoso do punktu, zanim zdarzenie zostało usunięte. *TenantInPlace przywracania* skryptu przywrócenie bazy danych dzierżawy nową bazę danych i usuwa oryginalną. Ten wzorzec przywracania najlepiej nadaje się do odzyskiwania danych poważne uszkodzenie, a dzierżawcy może być dostosowane utracie dużej ilości danych do.

1. Otwórz w programie PowerShell ISE **RestoreTenant.ps1 pokaz** pliku.
2. Ustaw **$DemoScenario** = **5**, *dzierżawy przywracania w miejscu*.
3. Aby uruchomić skrypt, naciśnij klawisz F5.

Skrypt przywraca bazę danych dzierżawy do punktu przed zdarzenie zostało usunięte. Trwa najpierw dzierżawy Hall porozumieniu Contoso wyłączony, aby uniknąć dalszych aktualizacji. Równoległe bazy danych jest tworzona przez przywrócenie z punktu przywracania. Przywrócona baza danych ma nazwę z sygnatury czasowej do upewnij się, że nazwa bazy danych nie koliduje to z istniejącej nazwy dzierżawy w bazie danych. Następnie utrzymując bazę danych dzierżawy zostanie usunięta, a następnie przywrócona baza danych została zmieniona na oryginalną nazwę bazy danych. Na koniec Hall porozumieniu Contoso jest przełączony w tryb online umożliwia aplikacji dostęp do przywróconej bazy danych.

Pomyślnie przywrócono bazy danych do punktu w czasie, zanim zdarzenie zostało usunięte. Gdy **zdarzenia** zostanie otwarta strona, upewnij się, że ostatnie zdarzenie zostało przywrócone.

Po przywróceniu bazy danych trwa inny 10 do 15 minut, zanim będzie można przywrócić z ponownie pierwsza pełna kopia zapasowa. 

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]

> * Przywróć bazę danych do równoległego bazy danych (obok siebie).
> * Przywracanie bazy danych w miejscu.

Spróbuj [schematu bazy danych dzierżawy Zarządzaj](saas-tenancy-schema-management.md) samouczka.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Dodatkowe samouczki, w aplikacji Wingtip SaaS](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Omówienie ciągłości działalności biznesowej z bazy danych SQL Azure](sql-database-business-continuity.md)
* [Więcej informacji na temat tworzenia kopii zapasowych bazy danych SQL](sql-database-automated-backups.md)
