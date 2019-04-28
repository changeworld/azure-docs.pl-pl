---
title: Często zadawane pytania dotyczące tworzenia kopii zapasowej bazy danych programu SQL Server na maszynach wirtualnych platformy Azure z usługą Azure Backup
description: Znajdź odpowiedzi na często zadawane pytania dotyczące tworzenia kopii zapasowej bazy danych programu SQL Server na maszynach wirtualnych platformy Azure z usługą Azure Backup.
services: backup
author: sachdevaswati
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: sachdevaswati
ms.openlocfilehash: 649e50634d901ab48f1cb36c39d7331401c0cc51
ms.sourcegitcommit: a95dcd3363d451bfbfea7ec1de6813cad86a36bb
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62733552"
---
# <a name="faq-about-sql-server-databases-that-are-running-on-an-azure-vm-backup"></a>Często zadawane pytania dotyczące bazy danych programu SQL Server, działające w kopii zapasowej maszyny Wirtualnej platformy Azure

W tym artykule odpowiedzi na często zadawane pytania dotyczące tworzenia kopii zapasowych baz danych programu SQL Server z programem na maszynach wirtualnych Azure (maszyny wirtualne) i które używają [kopia zapasowa Azure](backup-overview.md) usługi.

## <a name="can-i-use-azure-backup-for-iaas-vm-as-well-as-sql-server-on-the-same-machine"></a>Czy można używać usługi Azure backup dla maszyn wirtualnych IaaS, a także programu SQL Server na tym samym komputerze?
Tak, może mieć zarówno kopii zapasowych maszyn wirtualnych, jak i obsługi kopii zapasowych SQL na tej samej maszyny Wirtualnej. W tym przypadku wewnętrznie wyzwalamy tylko do kopiowania pełnej kopii zapasowej na maszynie Wirtualnej nie Obcinaj dzienników.


## <a name="does-the-solution-retry-or-auto-heal-the-backups"></a>Rozwiązania ponów próbę lub automatycznego naprawiania kopie zapasowe?

W niektórych przypadkach usługa Azure Backup wyzwala zaradczych kopii zapasowych. Automatycznego naprawiania może wystąpić jeden z warunków sześć wymienione poniżej:

  - Jeśli dziennika lub różnicowej kopii zapasowej zakończy się niepowodzeniem z powodu błędu sprawdzania poprawności numeru LSN, dalej dziennika lub różnicowej kopii zapasowej zamiast tego jest konwertowana do pełnej kopii zapasowej.
  - Jeśli nie pełnej kopii zapasowej się nie stało przed dziennika lub różnicowej kopii zapasowej, dziennika lub różnicowej kopii zapasowej zamiast tego jest konwertowana do pełnej kopii zapasowej.
  - Jeśli najnowsze pełnej kopii zapasowej tego punktu w czasie jest starszy niż 15 dni, dalej dziennika lub różnicowej kopii zapasowej zamiast tego jest konwertowana do pełnej kopii zapasowej.
  - Wszystkie zadania tworzenia kopii zapasowych Pobierz anulowane z powodu uaktualnienia rozszerzenia ponownie są wyzwalane po zakończeniu uaktualniania i rozszerzenie została uruchomiona.
  - Jeśli istnieje możliwość zastąpienia bazy danych podczas przywracania następnej dziennika/różnicowej kopii zapasowej nie powiedzie się i pełnej kopii zapasowej jest wyzwalany, zamiast tego.
  - W przypadku, gdy pełna kopia zapasowa jest wymaganych do zresetowania łańcuchów dziennika ze względu na zmiany w modelu odzyskiwania bazy danych pełnego pobiera wyzwalane automatycznie zgodnie z harmonogramem dalej.

Automatyczne poprawianie jako funkcja jest domyślnie dla wszystkich użytkowników; Jednak w przypadku, gdy zdecydujesz się zrezygnować z jej, następnie wykonać poniżej:

  * W wystąpieniu programu SQL Server w *C:\Program Files\Azure obciążenia Backup\bin* folderu, tworzenia lub edytowania **ExtensionSettingsOverrides.json** pliku.
  * W **ExtensionSettingsOverrides.json**ustaw *{"EnableAutoHealer": false}*.
  * Zapisz zmiany i zamknij plik.
  * W wystąpieniu programu SQL Server otwórz **zadań zarządzania** i ponownie uruchom **AzureWLBackupCoordinatorSvc** usługi.  

## <a name="can-i-control-as-to-how-many-concurrent-backups-run-on-the-sql-server"></a>Czy mogę kontrolować to, aby uruchamiać liczbę jednoczesnych kopii zapasowych, w programie SQL server?

Tak. Możesz ograniczyć szybkość, o których uruchamiany jest zasad tworzenia kopii zapasowej, aby zminimalizować wpływ na wystąpieniu programu SQL Server. Aby zmienić ustawienie:
1. W wystąpieniu programu SQL Server w *C:\Program Files\Azure obciążenia Backup\bin* folderze utwórz *ExtensionSettingsOverrides.json* pliku.
2. W *ExtensionSettingsOverrides.json* pliku, zmień **DefaultBackupTasksThreshold** ustawienie niższej wartości (na przykład 5). <br>
  `{"DefaultBackupTasksThreshold": 5}`

3. Zapisz zmiany i zamknij plik.
4. W wystąpieniu programu SQL Server otwórz **Menedżera zadań**. Uruchom ponownie usługę **AzureWLBackupCoordinatorSvc**.<br/> <br/>
 Podczas tej metody pomaga w przypadku tworzenia kopii zapasowej aplikacja zużywa dużo zasobów, program SQL Server [zarządcy zasobów](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor?view=sql-server-2017) jest bardziej ogólny sposób określić ograniczenia ilości procesora CPU, fizycznych we/wy i pamięci, która może być przychodzących żądań aplikacji Użycie.

> [!NOTE]
> W Interfejsie, nadal możesz teraz i Zaplanuj dowolną liczbę kopii zapasowych w dowolnym momencie jednak będą przetwarzane w przesuwającego się okna, 5, zgodnie z powyższym przykładzie.

## <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>Czy można uruchamiać pełną kopię zapasową z repliki pomocniczej?
Zgodnie z ograniczeniami SQL można uruchomić Kopiuj tylko pełną kopię zapasową w replice pomocniczej; jednak tworzenie pełnej kopii zapasowej nie jest dozwolone.

## <a name="can-i-protect-availability-groups-on-premises"></a>Czy można chronić dostępności grupy w środowisku lokalnym?
Nie. Usługa Azure Backup chroni baz danych programu SQL Server działających na platformie Azure. Jeśli grupy dostępności (grupy dostępności) jest rozłożona się między maszynami platformy Azure i w środowisku lokalnym, AG mogą być chronione, tylko wtedy, gdy replika podstawowa jest uruchomiona na platformie Azure. Ponadto usługa Azure Backup chroni tylko węzły, które działają w tym samym regionie platformy Azure co magazyn usługi Recovery Services.

## <a name="can-i-protect-availability-groups-across-regions"></a>Można chronić grupy dostępności w regionach?
Magazyn usługi Azure Backup Recovery Services można wykryć i chronić wszystkie węzły, które znajdują się w tym samym regionie co magazyn. Jeśli grupy dostępności programu SQL Server Always On obejmuje wiele regionów systemu Azure, skonfigurować kopię zapasową z regionem, który ma węzła podstawowego. Usługa Azure Backup można wykryć i ochronę wszystkich baz danych w grupie dostępności, zgodnie z preferencjami kopii zapasowej. Gdy Twoja preferencję tworzenia kopii zapasowych nie zostanie spełniony, kończy się niepowodzeniem i otrzymasz alert o niepowodzeniu.

## <a name="do-successful-backup-jobs-create-alerts"></a>Czy zakończone pomyślnie zadania tworzenia kopii zapasowej tworzą alerty?
Nie. Zakończone pomyślnie zadania tworzenia kopii zapasowej nie generują alertów. Alerty są wysyłane tylko w przypadku zadań tworzenia kopii zapasowej, które się nie powiodły. Szczegółowe zachowanie dla alertów w portalu jest udokumentowany [tutaj](backup-azure-monitoring-built-in-monitor.md). Jednak w przypadku, gdy chcesz mieć alerty nawet w przypadku zadań zakończonych pomyślnie, można użyć [monitorowanie za pomocą usługi Azure Monitor](backup-azure-monitoring-use-azuremonitor.md).

## <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>Może wyświetlać zaplanowanych zadań kopii zapasowej w menu zadania tworzenia kopii zapasowej?
**Zadania tworzenia kopii zapasowej** menu zostaną wyświetlone tylko usługi ad-hoc zadania tworzenia kopii zapasowej. W przypadku zaplanowanego zadania użyj [monitorowanie za pomocą usługi Azure Monitor](backup-azure-monitoring-use-azuremonitor.md).

## <a name="are-future-databases-automatically-added-for-backup"></a>Bazy danych w przyszłości automatycznie dodawanych do utworzenia kopii zapasowej?
Tak, można osiągnąć tej funkcji za pomocą [automatycznej ochrony](backup-sql-server-database-azure-vms.md#enable-auto-protection).  

## <a name="if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups"></a>Jeśli usunę bazę danych z wystąpienia autoprotected, co się stanie z kopii zapasowych?
Jeśli bazy danych zostało porzucone z wystąpienia autoprotected, kopie zapasowe bazy danych nadal są próby. Oznacza to, że usuniętej bazy danych rozpoczyna się pojawienie się jako w złej kondycji w ramach **elementy kopii zapasowej** i nadal jest chroniona.

Poprawny sposób Zatrzymaj ochronę tej bazy danych jest przeprowadzenie **Zatrzymaj tworzenie kopii zapasowej** z **usuwania danych** dla tej bazy danych.  

## <a name="if-i-do-stop-backup-operation-of-an-autoprotected-database-what-will-be-its-behavior"></a>Jeśli operacji tworzenia kopii zapasowej bazy danych autoprotected są zatrzymywane, jaka będzie jego zachowanie?
Jeśli to zrobisz **Zatrzymaj tworzenie kopii zapasowych na przechowywanie danych**, żadne kopie zapasowe w przyszłości będą miały miejsce i istniejące punkty odzyskiwania pozostaną nienaruszone. Baza danych będzie nadal uznawany za chronione i wyświetlane w obszarze **kopii zapasowych elementów**.

Jeśli to zrobisz **zatrzymywanie tworzenia kopii zapasowych i usuwania danych**, żadne kopie zapasowe w przyszłości będą miały miejsce i istniejące punkty odzyskiwania zostaną również usunięte. Bazy danych będą uznawane za niezaznaczone chronionych i widoczny w ramach wystąpienia do konfigurowania kopii zapasowej. W przeciwieństwie do innych chronionych w górę baz danych można ręcznie wybrać, lub który można uzyskać autoprotected, ta baza danych pojawia się wyszarzonym i nie można go wybrać. Jedynym sposobem, aby ponownie włączyć ochronę tej bazy danych jest wyłączyć automatyczną ochronę w wystąpieniu. Możesz teraz wybierz tę bazę danych i skonfiguruj ochronę na nim lub ponownie włączyć automatyczną ochronę w wystąpieniu ponownie.

## <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior"></a>Jeśli po został objęty ochroną, zmienić nazwę bazy danych, jaka będzie zachowanie?
Ponownie nazwany baza danych jest traktowany jako nową bazę danych. W związku z tym usługa będzie traktować tę sytuację tak, jakby bazy danych nie zostały odnalezione i za pomocą niepowodzenie tworzenia kopii zapasowych.

Możesz wybrać bazy danych, która została zmieniona i skonfiguruj ochronę na nim. W przypadku, gdy automatyczna ochrona jest wyłączony w wystąpieniu, zmieniono nazwę bazy danych będzie mieć automatycznego wykrywania i chronione.

##  <a name="why-cant-i-see-an-added-database-for-an-autoprotected-instance"></a>Dlaczego nie widzę dodaną bazę danych dla wystąpienia autoprotected?
A bazy danych, które [dodać do wystąpienia autoprotected](backup-sql-server-database-azure-vms.md#enable-auto-protection) nie może być natychmiast widoczna w obszarze chronione elementy. Dzieje się tak, ponieważ odnajdywanie jest zazwyczaj uruchamiane co 8 godzin. Jednak można odnaleźć i od razu chronić nowych baz danych, jeśli ręcznie uruchomić odnajdywanie, wybierając **odzyskiwanie baz danych**, jak pokazano na poniższej ilustracji.

  ![Ręcznie odnaleźć nowo dodaną bazę danych](./media/backup-azure-sql-database/view-newly-added-database.png)


## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak [Utwórz kopię zapasową bazy danych programu SQL Server](backup-azure-sql-database.md) uruchomionego na Maszynie wirtualnej platformy Azure.
