---
title: Często zadawane pytania — tworzenie kopii zapasowych baz danych platformy SAP HANA na maszynach wirtualnych platformy Azure
description: W tym artykule odnajduj odpowiedzi na typowe pytania dotyczące tworzenia kopii zapasowych baz danych SAP HANA przy użyciu usługi Azure Backup.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: a46c4d6cccc00452a56567880400ef5779e6aed4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80155396"
---
# <a name="frequently-asked-questions--back-up-sap-hana-databases-on-azure-vms"></a>Często zadawane pytania — tworzenie kopii zapasowych baz danych SAP HANA na maszynach wirtualnych platformy Azure

Ten artykuł zawiera odpowiedzi na typowe pytania dotyczące tworzenia kopii zapasowych baz danych SAP HANA przy użyciu usługi Azure Backup.

## <a name="backup"></a>Tworzenie kopii zapasowych

### <a name="how-many-full-backups-are-supported-per-day"></a>Ile pełnych kopii zapasowych jest obsługiwanych dziennie?

Obsługujemy tylko jedną pełną kopię zapasową dziennie. Tego samego dnia nie można wyzwolić różnicowej kopii zapasowej i pełnej kopii zapasowej.

### <a name="do-successful-backup-jobs-create-alerts"></a>Czy zakończone pomyślnie zadania tworzenia kopii zapasowej tworzą alerty?

Nie. Zakończone pomyślnie zadania tworzenia kopii zapasowej nie generują alertów. Alerty są wysyłane tylko w przypadku zadań tworzenia kopii zapasowej, które się nie powiodły. Szczegółowe zachowanie alertów portalu jest udokumentowane [w tym miejscu](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor). Jeśli jednak chcesz otrzymywać alerty nawet w przypadku udanych zadań, możesz użyć usługi [Azure Monitor.](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)

### <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>Czy w menu Zadania tworzenia kopii zapasowych są widoczne zaplanowane zadania tworzenia kopii zapasowych?

W menu Zadanie kopii zapasowej będą wyświetlane tylko zadania tworzenia kopii zapasowych ad hoc. W przypadku zaplanowanych zadań użyj usługi [Azure Monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

### <a name="are-future-databases-automatically-added-for-backup"></a>Czy przyszłe bazy danych są automatycznie dodawane do zadania tworzenia kopii zapasowej?

Nie, nie jest to obecnie obsługiwane.

### <a name="if-i-delete-a-database-from-an-instance-what-will-happen-to-the-backups"></a>Jeśli usunę bazę danych z wystąpienia, co się stanie z kopiami zapasowymi?

Jeśli baza danych zostanie porzucona z wystąpienia SAP HANA, nadal podejmowane są próby tworzenia kopii zapasowych bazy danych. Oznacza to, że usunięta baza danych zaczyna być pokazywalna jako w złej kondycji w obszarze **Elementy kopii zapasowej** i jest nadal chroniona.
Prawidłowym sposobem, aby zatrzymać ochronę tej bazy danych jest wykonanie **Zatrzymaj kopię zapasową z usunąć dane** w tej bazie danych.

### <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-the-behavior-be"></a>Jeśli zmienię nazwę bazy danych po jej ochronie, jakie będzie zachowanie?

Zmieniona baza danych jest traktowana jako nowa baza danych. W związku z tym usługa będzie traktować tę sytuację tak, jakby baza danych nie zostały znalezione i z niepowodzeniem kopii zapasowych. Zmieniona baza danych pojawi się jako nowa baza danych i musi być skonfigurowana do ochrony.

### <a name="what-are-the-prerequisites-to-back-up-sap-hana-databases-on-an-azure-vm"></a>Jakie są wymagania wstępne do utworzenia kopii zapasowej baz danych SAP HANA na maszynie Wirtualnej platformy Azure?

Zapoznaj się z [wymaganiami wstępnymi](tutorial-backup-sap-hana-db.md#prerequisites) i [co skrypt rejestracji wstępnej nie](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) sekcje.

### <a name="what-permissions-should-be-set-for-azure-to-be-able-to-back-up-sap-hana-databases"></a>Jakie uprawnienia należy ustawić dla platformy Azure, aby móc wywsnąć w górę tworzenie kopii zapasowych baz danych SAP HANA?

Uruchomienie skryptu rejestracji wstępnej ustawia wymagane uprawnienia umożliwiające platformie Azure tworzenie kopii zapasowych baz danych SAP HANA. Więcej informacji na temat skryptu rejestracji wstępnej można znaleźć [tutaj.](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)

### <a name="will-backups-work-after-migrating-sap-hana-from-10-to-20"></a>Czy kopie zapasowe będą działać po migracji sap HANA z 1.0 do 2.0?

Zapoznaj się z [tą sekcją](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#upgrading-from-sap-hana-10-to-20) podręcznika rozwiązywania problemów.

### <a name="can-azure-hana-backup-be-set-up-against-a-virtual-ip-load-balancer-and-not-a-virtual-machine"></a>Czy można skonfigurować usługę Azure HANA Backup na wirtualnym adresie IP (moduł równoważenia obciążenia), a nie na maszynie wirtualnej?

Obecnie nie mamy możliwości skonfigurowania rozwiązania tylko dla wirtualnego adresu IP. Potrzebujemy maszyny wirtualnej do wykonania rozwiązania.

### <a name="i-have-a-sap-hana-system-replication-hsr-how-should-i-configure-backup-for-this-setup"></a>Mam SAP HANA System Replication (HSR), jak skonfigurować kopię zapasową dla tej konfiguracji?

Węzły podstawowe i pomocnicze hsr będą traktowane jako dwie indywidualne, niezwiązane maszyny wirtualne. Należy skonfigurować kopię zapasową w węźle podstawowym, a gdy nastąpi awaria, należy skonfigurować kopię zapasową w węźle pomocniczym (który teraz staje się węzłem podstawowym). Nie ma automatycznego "awaryjnego" tworzenia kopii zapasowej do drugiego węzła.

## <a name="restore"></a>Przywracanie

### <a name="why-cant-i-see-the-hana-system-i-want-my-database-to-be-restored-to"></a>Dlaczego nie widzę systemu HANA, do którego chcę przywrócić moją bazę danych?

Sprawdź, czy wszystkie wymagania wstępne dla przywrócenia do docelowego wystąpienia SAP HANA są spełnione. Aby uzyskać więcej informacji, zobacz [Wymagania wstępne — przywracanie baz danych SAP HANA na maszynie Wirtualnej platformy Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore#prerequisites).

### <a name="why-is-the-overwrite-db-restore-failing-for-my-database"></a>Dlaczego przywracanie zastępuj bazę danych nie działa dla mojej bazy danych?

Upewnij się, że opcja **Wymuś zastąpienie** jest zaznaczona podczas przywracania.

### <a name="why-do-i-see-the-source-and-target-systems-for-restore-are-incompatible-error"></a>Dlaczego widzę błąd "Systemy źródłowe i docelowe przywracania są niezgodne"?

Zapoznaj się z notatką SAP HANA [1642148,](https://launchpad.support.sap.com/#/notes/1642148) aby zobaczyć, jakie typy przywracania są obecnie obsługiwane.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [zrobić kopii zapasowej baz danych SAP HANA uruchomionych](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database) na maszynach wirtualnych platformy Azure.
