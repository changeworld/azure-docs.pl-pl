---
title: Często zadawane pytania dotyczące tworzenia kopii zapasowej bazy danych programu SQL Server na maszynach wirtualnych platformy Azure z usługą Azure Backup
description: Zawiera odpowiedzi na często zadawane pytania dotyczące tworzenia kopii zapasowej bazy danych programu SQL Server na maszynach wirtualnych platformy Azure z usługą Azure Backup.
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 8/16/2018
ms.author: sogup
ms.openlocfilehash: a14406733ff60d53d4bf7792ff0c9a015c57d9b3
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430780"
---
# <a name="faq-on-sql-server-running-on-azure-vm-backup"></a>Często zadawane pytania dotyczące programu SQL Server uruchomionego na kopii zapasowej maszyny Wirtualnej platformy Azure

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące tworzenia kopii zapasowych baz danych programu SQL Server uruchomiony na maszynach wirtualnych platformy Azure za pomocą [kopia zapasowa Azure](backup-overview.md) usługi.

> [!NOTE]
> Ta funkcja jest obecnie w publicznej wersji zapoznawczej.



## <a name="can-i-throttle-the-backup-speed"></a>Czy mogę ograniczyć szybkość tworzenia kopii zapasowych

Tak. Możesz ograniczyć szybkość, o których uruchamiany jest zasad tworzenia kopii zapasowej, aby zminimalizować wpływ na wystąpieniu programu SQL Server. Aby zmienić ustawienie:
1. W wystąpieniu programu SQL Server w *folderze C:\Program Files\Azure Workload Backup\bin* utwórz plik **ExtensionSettingsOverrides.json**.
2. W pliku **ExtensionSettingsOverrides.json** zmień ustawienie **DefaultBackupTasksThreshold** na niższa wartość (na przykład 5) <br>
  ` {"DefaultBackupTasksThreshold": 5}`

3. Zapisz zmiany. Zamknij plik.
4. W wystąpieniu programu SQL Server otwórz **Menedżera zadań**. Uruchom ponownie usługę **AzureWLBackupCoordinatorSvc**.

## <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>Czy można uruchamiać pełną kopię zapasową z repliki pomocniczej?
Nie. Ta funkcja nie jest obsługiwana.

## <a name="do-successful-backup-jobs-create-alerts"></a>Czy zakończone pomyślnie zadania tworzenia kopii zapasowej tworzą alerty?

Nie. Zakończone pomyślnie zadania tworzenia kopii zapasowej nie generują alertów. Alerty są wysyłane tylko w przypadku zadań tworzenia kopii zapasowej, które się nie powiodły.

## <a name="can-i-see-scheduled-backup-jobs-in-the-jobs-menu"></a>Może wyświetlać zaplanowanych zadań kopii zapasowej w menu zadania?

Nie. **Zadania tworzenia kopii zapasowej** menu pokazuje szczegóły zadania na żądanie, ale nie zaplanowanych zadań kopii zapasowej. Jeśli dowolne zaplanowane zadanie kopii zapasowej nie powiedzie się, szczegóły będą dostępne w alertach dotyczących zadania zakończonego niepowodzeniem. Aby monitorować wszystkie zadania tworzenia kopii zapasowej, zaplanowane i ad hoc, użyj programu [SQL Server Management Studio](manage-monitor-sql-database-backup.md).

## <a name="are-future-databases-automatically-added-for-backup"></a>Bazy danych w przyszłości automatycznie dodawanych do utworzenia kopii zapasowej?

Nie. W przypadku konfigurowania ochrony dla wystąpienia programu SQL Server i wybrania opcji poziomu serwera zostaną dodane wszystkie bazy danych. W przypadku dodawania baz danych do wystąpienia programu SQL Server po skonfigurowaniu ochrony należy ręcznie dodać nowe bazy danych w celu objęcia ich ochroną. Bazy danych nie są automatycznie uwzględniane w skonfigurowanej ochronie.

##  <a name="how-do-i-restart-protection-after-changing-recovery-type"></a>Jak ponownie uruchomić ochronę po zmianie typu odzyskiwania?

Wyzwól tworzenie pełnej kopii zapasowej. Kopie zapasowe dziennika rozpoczną się zgodnie z oczekiwaniami.

## <a name="can-i-protect-availability-groups-on-premises"></a>Czy można chronić grupy dostępności w środowisku lokalnym?

Nie. Usługa Azure Backup chroni serwery SQL Server działające na platformie Azure. Jeśli grupa dostępności jest rozproszona między maszynami platformy Azure i maszynami lokalnymi, grupy dostępności mogą być chronione tylko wtedy, gdy replika podstawowa zostanie uruchomiona na platformie Azure. Ponadto usługa Azure Backup chroni tylko węzły w tym samym regionie platformy Azure, co magazyn usługi Recovery Services.

## <a name="can-i-protect-availability-groups-across-regions"></a>Można chronić grupy dostępności w regionach?

Magazyn usługi Recovery Service w usłudze Azure Backup może wykryć i chronić wszystkie węzły, które znajdują się w tym samym regionie, co magazyn usługi Recovery Services. Jeśli masz zawsze włączoną grupę dostępności SQL obejmującą wiele regionów platformy Azure, musisz skonfigurować tworzenie kopii zapasowej z regionu zawierającego węzeł podstawowy. Usługa Azure Backup będzie mogła wykryć i chronić wszystkie bazy danych w grupie dostępności zgodnie z preferencjami dotyczącymi tworzenia kopii zapasowych. Jeśli preferencje tworzenia kopii zapasowych nie zostaną zaspokojone, tworzenie kopii zapasowych zakończy się niepowodzeniem i zostanie wyświetlony alert o niepowodzeniu.

## <a name="can-i-exclude-databases-with-auto-protection-enabled"></a>Za pomocą włączono automatyczną ochronę można wykluczyć bazy danych?

Nie, [automatyczna ochrona](backup-azure-sql-database.md#enable-auto-protection) ma zastosowanie do całego wystąpienia. Nie można selektywnie chronić baz danych za pomocą automatycznej ochrony wystąpienia.

## <a name="can-i-have-different-policies-in-an-auto-protected-instance"></a>W wystąpieniu objęte ochroną automatyczną można mieć różne zasady?

Jeśli masz już niektóre chronione bazy danych w wystąpieniu, będą one nadal chronione przy użyciu odpowiednich zasad, nawet po ustawieniu wartości **WŁ.** dla opcji [automatycznej ochrony](backup-azure-sql-database.md#enable-auto-protection). Wszystkie niechronione bazy danych oraz te, które zostaną dodane w przyszłości, będą jednak mieć tylko pojedyncze zasady definiowane w obszarze **konfigurowania kopii zapasowej** po wybraniu baz danych. W rzeczywistości, w przeciwieństwie do innych chronionych baz danych, nie można nawet zmienić zasad dla bazy danych w ramach wystąpienia chronionego automatycznie.
Jeśli chcesz to zrobić, jedynym sposobem jest tymczasowe wyłączenie automatycznej ochrony wystąpienia, a następnie zmiana zasad dla tej bazy danych. Możesz teraz ponownie włączyć ochronę automatyczną tego wystąpienia.

## <a name="if-i-delete-a-database-from-auto-protection-will-backups-stop"></a>Jeśli usunę bazę danych z grupy ochrony automatycznego tworzenia kopii zapasowych zatrzyma?

Nie, jeśli baza danych zostanie usunięta z wystąpienia chronionego automatycznie, nadal będą podejmowane próby utworzenia kopii zapasowych tej bazy danych. Oznacza to, że usunięta baza danych zacznie pojawiać się jako baza danych w złej kondycji w obszarze **Elementy kopii zapasowej** i nadal będzie traktowana jako chroniona.

Jedynym sposobem zatrzymania ochrony tej bazy danych jest tymczasowe wyłączenie [automatycznej ochrony](backup-azure-sql-database.md#enable-auto-protection) wystąpienia, a następnie wybranie opcji **Zatrzymaj tworzenie kopii zapasowej** w obszarze **Elementy kopii zapasowej** dla tej bazy danych. Możesz teraz ponownie włączyć ochronę automatyczną tego wystąpienia.

##  <a name="why-cant-i-see-an-added-database-for-an-auto-protected-instance"></a>Dlaczego nie widzę dodaną bazę danych dla wystąpienia chronione automatycznie?

Nie widzisz nowo dodaną bazę danych do [objęte ochroną automatyczną](backup-azure-sql-database.md#enable-auto-protection) wystąpienia chronione natychmiast, w obszarze chronione elementy. Dzieje się tak, ponieważ odnajdywanie jest zazwyczaj uruchamiane co 8 godzin. Użytkownik może jednak uruchamiać odnajdywanie ręczne przy użyciu opcji **Odzyskaj bazy danych** w celu natychmiastowego odnajdywania i chronienia baz danych, jak pokazano na poniższym obrazie:

  ![Wyświetlanie nowo dodanej bazy danych](./media/backup-azure-sql-database/view-newly-added-database.png)

## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się, jak](backup-azure-sql-database.md) Utwórz kopię zapasową bazy danych SQL Server uruchomiony na Maszynie wirtualnej platformy Azure.
