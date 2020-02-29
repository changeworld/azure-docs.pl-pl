---
title: Często zadawane pytania — tworzenie kopii zapasowych baz danych platformy SAP HANA na maszynach wirtualnych platformy Azure
description: W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące tworzenia kopii zapasowych SAP HANA baz danych przy użyciu usługi Azure Backup.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: d9d10e38885ba814045d8476b83671153feb7b8c
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2020
ms.locfileid: "77919689"
---
# <a name="frequently-asked-questions--back-up-sap-hana-databases-on-azure-vms"></a>Często zadawane pytania — tworzenie kopii zapasowych baz danych SAP HANA na maszynach wirtualnych platformy Azure

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące tworzenia kopii zapasowych SAP HANA baz danych przy użyciu usługi Azure Backup.

## <a name="backup"></a>Backup

### <a name="how-many-full-backups-are-supported-per-day"></a>Ile pełnych kopii zapasowych jest obsługiwanych dziennie?

Obsługujemy tylko jedną pełną kopię zapasową dziennie. Nie można utworzyć różnicowej kopii zapasowej i pełnej kopii zapasowej w tym samym dniu.

### <a name="do-successful-backup-jobs-create-alerts"></a>Czy zakończone pomyślnie zadania tworzenia kopii zapasowej tworzą alerty?

Nie. Zakończone pomyślnie zadania tworzenia kopii zapasowej nie generują alertów. Alerty są wysyłane tylko w przypadku zadań tworzenia kopii zapasowej, które się nie powiodły. Szczegółowe zachowanie alertów portalu zostało udokumentowane w [tym miejscu](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor). Jednak jeśli interesuje Cię alerty nawet w przypadku zadań zakończonych powodzeniem, możesz użyć [Azure monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

### <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>Czy można zobaczyć zaplanowane zadania tworzenia kopii zapasowej w menu zadania tworzenia kopii zapasowej?

W menu zadania tworzenia kopii zapasowej będą wyświetlane tylko zadania tworzenia kopii zapasowych ad hoc. W przypadku zaplanowanych zadań Użyj [Azure monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

### <a name="are-future-databases-automatically-added-for-backup"></a>Czy przyszłe bazy danych są automatycznie dodawane do kopii zapasowej?

Nie, nie jest to obecnie obsługiwane.

### <a name="if-i-delete-a-database-from-an-instance-what-will-happen-to-the-backups"></a>Czy po usunięciu bazy danych z wystąpienia będą wykonywane kopie zapasowe?

Jeśli baza danych zostanie porzucona z wystąpienia SAP HANA, nadal będą podejmowane kopie zapasowe bazy danych. Oznacza to, że usunięta baza danych zaczyna wyglądać jak w złej kondycji w obszarze **elementy kopii zapasowej** i nadal jest chroniona.
Prawidłowym sposobem zatrzymania ochrony tej bazy danych jest wykonanie **operacji zatrzymania wykonywania kopii zapasowej z usuwaniem danych z** tej bazy danych.

### <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-the-behavior-be"></a>Jeśli zmienię nazwę bazy danych po jej włączeniu, jakie będzie zachowanie?

Baza danych o zmienionej nazwie jest traktowana jako nowa baza danych. W związku z tym usługa będzie traktować tę sytuację tak, jakby baza danych nie została znaleziona, a kopie zapasowe kończą się niepowodzeniem. Baza danych o zmienionej nazwie będzie wyświetlana jako nowa baza danych i musi być skonfigurowana do ochrony.

### <a name="what-are-the-prerequisites-to-back-up-sap-hana-databases-on-an-azure-vm"></a>Jakie są wymagania wstępne dotyczące tworzenia kopii zapasowych baz danych SAP HANA na maszynie wirtualnej platformy Azure?

Zapoznaj się z [wymaganiami wstępnymi](tutorial-backup-sap-hana-db.md#prerequisites) i [co robi skrypt przed rejestracją](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) .

### <a name="what-permissions-should-be-set-for-azure-to-be-able-to-back-up-sap-hana-databases"></a>Jakie uprawnienia należy ustawić dla systemu Azure, aby można było tworzyć kopie zapasowe baz danych SAP HANA.

Uruchomienie skryptu przed rejestracją ustawia wymagane uprawnienia, aby system Azure mógł tworzyć kopie zapasowe baz danych SAP HANA. Możesz znaleźć więcej informacji na temat tego, co robi skrypt [](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)przed rejestracją.

### <a name="will-backups-work-after-migrating-sap-hana-from-10-to-20"></a>Czy kopie zapasowe będą wykonywane po przeprowadzeniu migracji SAP HANA z 1,0 do 2,0?

Zapoznaj się z [tą sekcją](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#upgrading-from-sap-hana-10-to-20) przewodnika rozwiązywania problemów.

## <a name="restore"></a>Przywracanie

### <a name="why-cant-i-see-the-hana-system-i-want-my-database-to-be-restored-to"></a>Dlaczego nie widzę systemu HANA, do którego ma zostać przywrócona baza danych?

Sprawdź, czy zostały spełnione wszystkie wymagania wstępne dotyczące przywracania SAP HANA wystąpienia docelowego. Aby uzyskać więcej informacji, zobacz [wymagania wstępne — przywracanie SAP HANA baz danych na maszynie wirtualnej platformy Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore#prerequisites).

### <a name="why-is-the-overwrite-db-restore-failing-for-my-database"></a>Dlaczego operacja zastępowania bazy danych nie powiedzie się w przypadku mojej usługi?

Upewnij się, że opcja **Wymuszaj zastępowanie** jest zaznaczona podczas przywracania.

### <a name="why-do-i-see-the-source-and-target-systems-for-restore-are-incompatible-error"></a>Dlaczego widzę błąd "systemy źródłowe i docelowe na potrzeby przywracania są niezgodne"?

Zapoznaj się z SAP HANA Uwaga [1642148](https://launchpad.support.sap.com/#/notes/1642148) , aby zobaczyć, jakie typy przywracania są obecnie obsługiwane.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [tworzyć kopie zapasowe baz danych SAP HANA](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database) działających na maszynach wirtualnych platformy Azure.
