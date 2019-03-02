---
title: Często zadawane pytania dotyczące tworzenia kopii zapasowej bazy danych programu SQL Server na maszynach wirtualnych platformy Azure z usługą Azure Backup
description: Znajdź odpowiedzi na często zadawane pytania dotyczące tworzenia kopii zapasowej bazy danych programu SQL Server na maszynach wirtualnych platformy Azure z usługą Azure Backup.
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 8/16/2018
ms.author: sogup
ms.openlocfilehash: 3b7649a029c6c44cd8a25ea553ff2091f816dd3c
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/01/2019
ms.locfileid: "57214830"
---
# <a name="faq-about-sql-server-databases-that-are-running-on-an-azure-vm-backup"></a>Często zadawane pytania dotyczące bazy danych programu SQL Server, działające w kopii zapasowej maszyny Wirtualnej platformy Azure

W tym artykule odpowiedzi na często zadawane pytania dotyczące tworzenia kopii zapasowych baz danych programu SQL Server z programem na maszynach wirtualnych Azure (maszyny wirtualne) i które używają [kopia zapasowa Azure](backup-overview.md) usługi.

> [!NOTE]
> Ta funkcja jest obecnie w publicznej wersji zapoznawczej.

## <a name="can-i-throttle-the-backup-speed"></a>Czy mogę ograniczyć szybkość tworzenia kopii zapasowych

Tak. Możesz ograniczyć szybkość, o których uruchamiany jest zasad tworzenia kopii zapasowej, aby zminimalizować wpływ na wystąpieniu programu SQL Server. Aby zmienić ustawienie:
1. W wystąpieniu programu SQL Server w *C:\Program Files\Azure obciążenia Backup\bin* folderze utwórz *ExtensionSettingsOverrides.json* pliku.
2. W *ExtensionSettingsOverrides.json* pliku, zmień **DefaultBackupTasksThreshold** ustawienie niższej wartości (na przykład 5). <br>
  ` {"DefaultBackupTasksThreshold": 5}`

3. Zapisz zmiany i zamknij plik.
4. W wystąpieniu programu SQL Server otwórz **Menedżera zadań**. Uruchom ponownie usługę **AzureWLBackupCoordinatorSvc**.

## <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>Czy można uruchamiać pełną kopię zapasową z repliki pomocniczej?
Nie. Ta funkcja nie jest obsługiwana.

## <a name="do-successful-backup-jobs-create-alerts"></a>Czy zakończone pomyślnie zadania tworzenia kopii zapasowej tworzą alerty?

Nie. Zakończone pomyślnie zadania tworzenia kopii zapasowej nie generują alertów. Alerty są wysyłane tylko w przypadku zadań tworzenia kopii zapasowej, które się nie powiodły.

## <a name="can-i-see-scheduled-backup-jobs-in-the-jobs-menu"></a>Może wyświetlać zaplanowanych zadań kopii zapasowej w menu zadania?

Nie. **Zadania tworzenia kopii zapasowej** menu pokazuje szczegóły zadania na żądanie, ale nie zaplanowanych zadań kopii zapasowej. W przypadku awarii dowolnego zaplanowanych zadań kopii zapasowej, znajdziesz szczegółowe informacje w alertach zadania zakończonego niepowodzeniem. Aby monitorować wszystkie planowanych i nieplanowanych zadania tworzenia kopii zapasowej, użyj [SQL Server Management Studio](manage-monitor-sql-database-backup.md).

## <a name="are-future-databases-automatically-added-for-backup"></a>Bazy danych w przyszłości automatycznie dodawanych do utworzenia kopii zapasowej?

Nie. Podczas konfigurowania ochrony dla wystąpienia programu SQL Server wszystkich baz danych są dodawane po wybraniu opcji na poziomie serwera. Po skonfigurowaniu ochrony należy ręcznie dodać nowych baz danych, aby chronić je. Nowe bazy danych nie są automatycznie chronione.

##  <a name="how-do-i-restart-protection-after-i-change-recovery-type"></a>Jak ponownie rozpocząć ochrony po zmianie typu odzyskiwania?

Wyzwól tworzenie pełnej kopii zapasowej. Kopie zapasowe dziennika rozpoczną się zgodnie z oczekiwaniami.

## <a name="can-i-protect-availability-groups-on-premises"></a>Czy można chronić dostępności grupy w środowisku lokalnym?

Nie. Usługa Azure Backup chroni baz danych programu SQL Server działających na platformie Azure. Jeśli grupy dostępności (grupy dostępności) jest rozłożona się między maszynami platformy Azure i w środowisku lokalnym, AG mogą być chronione, tylko wtedy, gdy replika podstawowa jest uruchomiona na platformie Azure. Ponadto usługa Azure Backup chroni tylko węzły, które działają w tym samym regionie platformy Azure co magazyn usługi Recovery Services.

## <a name="can-i-protect-availability-groups-across-regions"></a>Można chronić grupy dostępności w regionach?

Magazyn usługi Azure Backup Recovery Services można wykryć i chronić wszystkie węzły, które znajdują się w tym samym regionie co magazyn. Jeśli grupy dostępności programu SQL Server Always On obejmuje wiele regionów systemu Azure, skonfigurować kopię zapasową z regionem, który ma węzła podstawowego. Usługa Azure Backup można wykryć i ochronę wszystkich baz danych w grupie dostępności, zgodnie z preferencjami kopii zapasowej. Gdy Twoja preferencję tworzenia kopii zapasowych nie zostanie spełniony, kończy się niepowodzeniem i otrzymasz alert o niepowodzeniu.

## <a name="can-i-exclude-databases-with-autoprotection-enabled"></a>Czy można wykluczyć bazy danych, za pomocą autoprotection włączone?

Nie. Autoprotection [ma zastosowanie do całego wystąpienia](backup-azure-sql-database.md#enable-auto-protection). Nie można użyć autoprotection na potrzeby selektywnej ochrony baz danych w wystąpieniu.

## <a name="can-i-have-different-policies-in-an-autoprotected-instance"></a>W wystąpieniu autoprotected można mieć różne zasady?

Jeśli wystąpienie zawiera już niektóre chronionej bazy danych, będą one nadal mają być chronione w ramach ich odpowiednich zasad, nawet po [włączyć autoprotection](backup-azure-sql-database.md#enable-auto-protection). Wszystkie niechronionych baz danych i baz danych, które zostaną dodane później będzie jednak jednych zasad. Zdefiniowanie tych zasad w obszarze **konfigurowania kopii zapasowej** po wybraniu bazy danych. W rzeczywistości w przeciwieństwie do innych baz danych chronionych, nie można nawet zmienić zasady dla bazy danych, która jest w wystąpieniu autoprotected.
Jedynym sposobem, aby zmienić zasady tej bazy danych jest tymczasowo wyłączyć autoprotection dla tego wystąpienia. Następnie możesz ponownie włączyć autoprotection dla tego wystąpienia.

## <a name="if-i-delete-a-database-from-an-autoprotected-instance-will-backups-stop"></a>Jeśli usunę bazę danych z wystąpienia autoprotected kopii zapasowych zatrzyma?

Nie. Jeśli bazy danych zostało porzucone z wystąpienia autoprotected, kopie zapasowe bazy danych nadal są próby. Oznacza to, że usuniętej bazy danych rozpoczyna się pojawienie się jako w złej kondycji w ramach **elementy kopii zapasowej** i nadal jest chroniona.

Jedynym sposobem, aby zatrzymać ochronę tej bazy danych jest tymczasowo [wyłączyć autoprotection](backup-azure-sql-database.md#enable-auto-protection) w wystąpieniu. Następnie w obszarze **elementy kopii zapasowej** bazy danych, wybierz **Zatrzymaj tworzenie kopii zapasowej**. Następnie możesz ponownie włączyć autoprotection dla tego wystąpienia.

##  <a name="why-cant-i-see-an-added-database-for-an-autoprotected-instance"></a>Dlaczego nie widzę dodaną bazę danych dla wystąpienia autoprotected?

A bazy danych, które [dodać do wystąpienia autoprotected](backup-azure-sql-database.md#enable-auto-protection) nie może być natychmiast widoczna w obszarze chronione elementy. Dzieje się tak, ponieważ odnajdywanie jest zazwyczaj uruchamiane co 8 godzin. Jednak można odnaleźć i od razu chronić nowych baz danych, jeśli ręcznie uruchomić odnajdywanie, wybierając **odzyskiwanie baz danych**, jak pokazano na poniższej ilustracji.

  ![Ręcznie odnaleźć nowo dodaną bazę danych](./media/backup-azure-sql-database/view-newly-added-database.png)

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak [Utwórz kopię zapasową bazy danych programu SQL Server](backup-azure-sql-database.md) uruchomionego na Maszynie wirtualnej platformy Azure.
