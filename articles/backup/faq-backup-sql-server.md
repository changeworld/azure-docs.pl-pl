---
title: Często zadawane pytania — tworzenie kopii zapasowych baz danych programu SQL Server na maszynach wirtualnych platformy Azure
description: Znajdź odpowiedzi na typowe pytania dotyczące tworzenia kopii zapasowych baz danych programu SQL Server na maszynach wirtualnych platformy Azure za pomocą usługi Azure Backup.
ms.reviewer: vijayts
ms.topic: conceptual
ms.date: 04/23/2019
ms.openlocfilehash: a973761bf16e2d271d718e4a8b29e08624276987
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247711"
---
# <a name="faq-about-sql-server-databases-that-are-running-on-an-azure-vm-backup"></a>Często zadawane pytania dotyczące baz danych programu SQL Server uruchomionych w kopii zapasowej maszyny Wirtualnej platformy Azure

W tym artykule odniewany jest odpowiedzi na często zadawane pytania dotyczące tworzenia kopii zapasowych baz danych programu SQL Server, które są uruchamiane na maszynach wirtualnych platformy Azure i korzystają z usługi [Azure Backup.](backup-overview.md)

## <a name="can-i-use-azure-backup-for-iaas-vm-as-well-as-sql-server-on-the-same-machine"></a>Czy mogę używać kopii zapasowej platformy Azure dla maszyny Wirtualnej IaaS, a także programu SQL Server na tym samym komputerze?

Tak, na tej samej maszynie wirtualnej można mieć zarówno kopię zapasową maszyny Wirtualnej, jak i kopię zapasową SQL. W takim przypadku wewnętrznie wyzwolić kopię tylko pełnej kopii zapasowej na maszynie wirtualnej, aby nie obcinać dzienniki.

## <a name="does-the-solution-retry-or-auto-heal-the-backups"></a>Czy rozwiązanie ponowi próbę lub automatyczne leczenie kopii zapasowych?

W pewnych okolicznościach usługa Azure Backup wyzwala kopie zapasowe. Auto-heal może się zdarzyć dla każdego z sześciu warunków wymienionych poniżej:

- Jeśli kopia zapasowa dziennika lub różnicowa nie powiedzie się z powodu błędu sprawdzania poprawności LSN, następny dziennik lub różnicowa kopia zapasowa jest konwertowana na pełną kopię zapasową.
- Jeśli przed dziennikiem lub różnicową kopią zapasową nie ma pełnej kopii zapasowej, ten dziennik lub różnicowa kopia zapasowa jest konwertowana na pełną kopię zapasową.
- Jeśli ostatni pełny kopii zapasowej w czasie jest starszy niż 15 dni, następny dziennik lub różnicowa kopia zapasowa jest zamiast tego konwertowane do pełnej kopii zapasowej.
- Wszystkie zadania tworzenia kopii zapasowej, które zostaną anulowane z powodu uaktualnienia rozszerzenia są ponownie wyzwalane po zakończeniu uaktualniania i uruchomieniu rozszerzenia.
- Jeśli zdecydujesz się zastąpić bazę danych podczas przywracania, następna kopia zapasowa dziennika/różnicy nie powiedzie się i zamiast tego zostanie wyzwolona pełna kopia zapasowa.
- W przypadkach, gdy pełna kopia zapasowa jest wymagana do zresetowania łańcuchów dziennika z powodu zmiany w modelu odzyskiwania bazy danych, pełna zostanie wyzwalana automatycznie w następnym harmonogramie.

Funkcja automatycznego leczenia jako funkcja jest domyślnie włączona dla wszystkich użytkowników; Jednak w przypadku, gdy zdecydujesz się zrezygnować z niego, wykonaj następujące czynności:

- W wystąpieniu programu SQL Server w folderze *C:\Program Files\Azure Workload Backup\bin* utwórz lub edytuj plik **ExtensionSettingsOverrides.json.**
- W **pliku ExtensionSettingsOverrides.json**ustaw *{"EnableAutoHealer": false}*.
- Zapisz zmiany i zamknij plik.
- W wystąpieniu programu SQL Server otwórz **narzędzie Zarządzanie zadaniami,** a następnie uruchom ponownie usługę **AzureWLBackupCoordinatorSvc.**

## <a name="can-i-control-how-many-concurrent-backups-run-on-the-sql-server"></a>Czy można kontrolować, ile równoczesnych kopii zapasowych jest uruchamianych na serwerze SQL?

Tak. Można ograniczyć szybkość wykonywania zasad tworzenia kopii zapasowych, aby zminimalizować wpływ na wystąpienie programu SQL Server. Aby zmienić ustawienie:

1. W wystąpieniu programu SQL Server w folderze *C:\Program Files\Azure Workload Backup\bin* utwórz plik *ExtensionSettingsOverrides.json.*
2. W pliku *ExtensionSettingsOverrides.json* zmień ustawienie **DefaultBackupTasksThreshold** na niższą wartość (na przykład 5). <br>
  `{"DefaultBackupTasksThreshold": 5}`
<br>
Wartość domyślna DefaultBackupTasksThreshold wynosi **20**.

3. Zapisz zmiany i zamknij plik.
4. W wystąpieniu programu SQL Server otwórz **Menedżera zadań**. Uruchom ponownie usługę **AzureWLBackupCoordinatorSvc**.<br/> <br/>
 Chociaż ta metoda pomaga, jeśli aplikacja do tworzenia kopii zapasowych zużywa dużą ilość zasobów, SQL Server [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor?view=sql-server-2017) jest bardziej ogólny sposób określenia limitów ilości procesora CPU, fizycznej we/wy i pamięci, które przychodzące żądania aplikacji można użyć.

> [!NOTE]
> W UX nadal można iść do przodu i zaplanować jak najwięcej kopii zapasowych w danym momencie, jednak będą one przetwarzane w oknie przesuwnym powiedzmy, 5, jak na powyższym przykładzie.

## <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>Czy można uruchamiać pełną kopię zapasową z repliki pomocniczej?

Zgodnie z ograniczeniami SQL można uruchomić kopiuj tylko pełną kopię zapasową na replice pomocniczej; Jednak pełna kopia zapasowa nie jest dozwolona.

## <a name="can-i-protect-availability-groups-on-premises"></a>Czy mogę chronić grupy dostępności lokalnie?

Nie. Usługa Azure Backup chroni bazy danych programu SQL Server uruchomione na platformie Azure. Jeśli grupa dostępności (AG) jest rozłożona między platformą Azure i komputerami lokalnymi, ag może być chroniony tylko wtedy, gdy replika podstawowa jest uruchomiona na platformie Azure. Ponadto usługa Azure Backup chroni tylko węzły, które działają w tym samym regionie platformy Azure, co magazyn usług odzyskiwania.

## <a name="can-i-protect-availability-groups-across-regions"></a>Czy mogę chronić grupy dostępności między regionami?

Magazyn usług odzyskiwania kopii zapasowych platformy Azure może wykrywać i chronić wszystkie węzły, które znajdują się w tym samym regionie co magazyn. Jeśli grupa dostępności programu SQL Server Always On obejmuje wiele regionów platformy Azure, skonfiguruj kopię zapasową z regionu, który ma węzeł podstawowy. Usługa Azure Backup może wykrywać i chronić wszystkie bazy danych w grupie dostępności zgodnie z preferencjami dotyczącymi tworzenia kopii zapasowych. Gdy preferencje dotyczące tworzenia kopii zapasowej nie są spełnione, kopie zapasowe kończą się niepowodzeniem i zostanie otrzymasz alert o awarii.

## <a name="do-successful-backup-jobs-create-alerts"></a>Czy zakończone pomyślnie zadania tworzenia kopii zapasowej tworzą alerty?

Nie. Zakończone pomyślnie zadania tworzenia kopii zapasowej nie generują alertów. Alerty są wysyłane tylko w przypadku zadań tworzenia kopii zapasowej, które się nie powiodły. Szczegółowe zachowanie alertów portalu jest udokumentowane [w tym miejscu](backup-azure-monitoring-built-in-monitor.md). Jednak w przypadku, gdy jesteś zainteresowany mają alerty nawet dla udanych zadań, można użyć [monitorowania przy użyciu usługi Azure Monitor](backup-azure-monitoring-use-azuremonitor.md).

## <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>Czy w menu Zadania tworzenia kopii zapasowych są widoczne zaplanowane zadania tworzenia kopii zapasowych?

W menu **Zadanie kopii zapasowej** będą wyświetlane tylko zadania tworzenia kopii zapasowych na żądanie. Do zaplanowanego użycia zadania [Monitorowanie przy użyciu usługi Azure Monitor](backup-azure-monitoring-use-azuremonitor.md).

## <a name="are-future-databases-automatically-added-for-backup"></a>Czy przyszłe bazy danych są automatycznie dodawane do zadania tworzenia kopii zapasowej?

Tak, można osiągnąć tę możliwość za pomocą [automatycznej ochrony](backup-sql-server-database-azure-vms.md#enable-auto-protection).  

## <a name="if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups"></a>Jeśli usunę bazę danych z wystąpienia chronionego automatycznie, co się stanie z kopiami zapasowymi?

Jeśli baza danych zostanie porzucona z wystąpienia automatycznie chronionego, nadal podejmowana jest próba tworzenia kopii zapasowych bazy danych. Oznacza to, że usunięta baza danych zaczyna być pokazywalna jako w złej kondycji w obszarze **Elementy kopii zapasowej** i jest nadal chroniona.

Prawidłowym sposobem, aby zatrzymać ochronę tej bazy danych jest do **zatrzymania kopii zapasowej** z usunąć **dane** w tej bazie danych.  

## <a name="if-i-do-stop-backup-operation-of-an-autoprotected-database-what-will-be-its-behavior"></a>Jeśli zatrzymam wykonywanie kopii zapasowych automatycznie chronionej bazy danych, jakie będzie jej zachowanie?

Jeśli **zatrzymasz tworzenie kopii zapasowych z zachowaniem danych,** żadne przyszłe kopie zapasowe nie będą miały miejsca, a istniejące punkty odzyskiwania pozostaną nienaruszone. Baza danych będzie nadal uważana za chroniona i będzie wyświetlana w obszarze **Elementy kopii zapasowej**.

Jeśli **nie zatrzymać tworzenie kopii zapasowej z usunąć dane,** nie przyszłe kopie zapasowe będą miały miejsce i istniejących punktów odzyskiwania zostaną usunięte. Baza danych zostanie uznana za niezabezpieczona i będzie wyświetlana w wystąpieniu w obszarze Konfigurowanie kopii zapasowej. Jednak w przeciwieństwie do innych chronionych baz danych, które można wybrać ręcznie lub które można uzyskać autoprochronowane, ta baza danych jest wyszarzona i nie można jej wybrać. Jedynym sposobem, aby ponownie chronić tę bazę danych jest wyłączenie automatycznej ochrony w wystąpieniu. Teraz można wybrać tę bazę danych i skonfigurować ochronę na nim lub ponownie włączyć automatyczną ochronę w wystąpieniu ponownie.

## <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior"></a>Jeśli zmienię nazwę bazy danych po jej ochronie, jakie będzie zachowanie?

Zmieniona baza danych jest traktowana jako nowa baza danych. W związku z tym usługa będzie traktować tę sytuację tak, jakby baza danych nie zostały znalezione i z niepowodzeniem kopii zapasowych.

Można wybrać bazę danych, która jest teraz zmieniana i skonfigurować ochronę na nim. W przypadku, gdy automatyczna ochrona jest włączona w wystąpieniu, zmieniona baza danych zostanie automatycznie wykryta i chroniona.

## <a name="why-cant-i-see-an-added-database-for-an-autoprotected-instance"></a>Dlaczego nie widzę dodanej bazy danych dla wystąpienia chronionego automatycznie?

Baza danych [dodana do wystąpienia chronionego automatycznie](backup-sql-server-database-azure-vms.md#enable-auto-protection) może nie być natychmiast wyświetlana w obszarze chronionych elementów. Dzieje się tak, ponieważ odnajdywanie jest zazwyczaj uruchamiane co 8 godzin. Można jednak natychmiast odnajdyć i chronić nowe bazy danych po ręcznym uruchomieniu odnajdywania, wybierając opcję Odkryj ponownie rekordy na nowo, jak pokazano na poniższej **ilustracji:**

  ![Ręczne odnajdowanie nowo dodanej bazy danych](./media/backup-azure-sql-database/view-newly-added-database.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak zrobić [zdjętą z bazy danych programu SQL Server,](backup-azure-sql-database.md) która jest uruchomiona na maszynie Wirtualnej platformy Azure.
