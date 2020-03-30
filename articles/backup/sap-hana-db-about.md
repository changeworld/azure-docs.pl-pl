---
title: Kopia zapasowa bazy danych SAP HANA na maszynach wirtualnych platformy Azure
description: W tym artykule dowiedz się więcej o tworzenie kopii zapasowych baz danych SAP HANA, które są uruchomione na maszynach wirtualnych platformy Azure.
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 52c235c95cea73a0c51c62fcb55f7f711d2eff21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476461"
---
# <a name="about-sap-hana-database-backup-in-azure-vms"></a>Kopia zapasowa bazy danych SAP HANA na maszynach wirtualnych platformy Azure

Bazy danych SAP HANA to obciążenia o znaczeniu krytycznym, które wymagają celu punktu odzyskiwania o niskim poziomie odzyskiwania (RPO) i celu szybkiego czasu odzyskiwania (RTO). Teraz można wykonać [kopię zapasową baz danych SAP HANA uruchomionych na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db) przy użyciu usługi Azure [Backup.](https://docs.microsoft.com/azure/backup/backup-overview)

Usługa Azure Backup jest certyfikowana przez firmę SAP z [certyfikatem Backint,](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5) aby zapewnić natywną obsługę kopii zapasowych dzięki wykorzystaniu natywnych interfejsów API sap HANA. Ta oferta z usługi Azure Backup jest zgodna z mantrą kopii zapasowych usługi Azure Backup o **zerowej infrastrukturze,** eliminując konieczność wdrażania infrastruktury kopii zapasowych i zarządzania nią. Teraz można bezproblemowo wykonać kopię zapasową i przywrócić bazy danych SAP HANA uruchomione na maszynach wirtualnych platformy Azure[(maszyny wirtualne serii M](../virtual-machines/m-series.md) również obsługiwane teraz!) i korzystać z funkcji zarządzania przedsiębiorstwem, które zapewnia usługa Azure Backup.

## <a name="added-value"></a>Wartość dodana

Korzystanie z usługi Azure Backup do tworzenia kopii zapasowych i przywracania baz danych SAP HANA daje następujące korzyści:

* **15-minutowy cel punktu odzyskiwania (RPO)**: Odzyskiwanie krytycznych danych do 15 minut jest teraz możliwe.
* **Przywracanie w jednym kliknięciem:** Przywracanie danych produkcyjnych na alternatywnych serwerach HANA jest łatwe. Tworzenie kopii zapasów kopii zapasowych i katalogów do wykonywania przywracania jest zarządzane przez platformę Azure w tle.
* **Długoterminowe przechowywanie**: Dla rygorystycznych potrzeb w zakresie zgodności i audytu. Zachowaj kopie zapasowe przez lata, na podstawie czasu przechowywania, po przekroczeniu którego punkty odzyskiwania będą automatycznie przycinane przez wbudowane możliwości zarządzania cyklem życia.
* **Zarządzanie kopiami zapasowymi z platformy Azure:** korzystaj z funkcji zarządzania i monitorowania usługi Azure Backup, aby zwiększyć środowisko zarządzania. Obsługa interfejsu wiersza polecenia platformy Azure jest również obsługiwana.

Aby wyświetlić scenariusze tworzenia kopii zapasowych i przywracania, które obsługujemy dzisiaj, zapoznaj się z [macierzą obsługi scenariuszy SAP HANA](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support).

## <a name="backup-architecture"></a>Architektura kopii zapasowych

![Diagram architektury kopii zapasowej](./media/sap-hana-db-about/backup-architecture.png)

* Proces tworzenia kopii zapasowej rozpoczyna się od [utworzenia magazynu usług odzyskiwania na](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#create-a-recovery-service-vault) platformie Azure. Ten magazyn będzie używany do przechowywania kopii zapasowych i punktów odzyskiwania utworzonych w czasie.
* Maszyna wirtualna platformy Azure z uruchomionym serwerem SAP HANA jest zarejestrowana w przechowalni, a bazy danych, których kopię zapasową ma zostać utworzone, zostaną [odnalezione.](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#discover-the-databases) Aby umożliwić usługi Azure Backup odnajdowanie baz danych, [skrypt rejestracji wstępnej](https://aka.ms/scriptforpermsonhana) musi być uruchomiony na serwerze HANA jako użytkownik root.
* Ten skrypt tworzy użytkownika **AZUREWLBACKUPHANAUSER** DB i odpowiedni klucz o tej samej nazwie w **hdbuserstore**. Zapoznaj się z [sekcją Co skrypt rejestracji wstępnej robi,](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) aby dowiedzieć się więcej o tym, co robi skrypt.
* Usługa Azure Backup Service instaluje teraz **wtyczkę usługi Azure Backup dla hana** na zarejestrowanym serwerze SAP HANA.
* Użytkownik **bazy danych AZUREWLBACKUPHANAUSER** utworzony przez skrypt rejestracji wstępnej jest używany przez **wtyczkę usługi Azure Backup dla hana** do wykonywania wszystkich operacji tworzenia kopii zapasowych i przywracania. W przypadku próby skonfigurowania kopii zapasowej dla rejestratorów SAP HANA bez uruchamiania tego skryptu może pojawić się następujący błąd: **UserErrorHanaScriptNotRun**.
* Aby [skonfigurować tworzenie kopii zapasowych](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#configure-backup) w wykrytych bazach danych, wybierz wymagane zasady tworzenia kopii zapasowych i włącz tworzenie kopii zapasowych.

* Po skonfigurowaniu kopii zapasowej usługa Azure Backup konfiguruje następujące parametry programu Backint na poziomie bazy danych na chronionym serwerze SAP HANA:
  * [catalog_backup_using_backint:true]
  * [enable_accumulated_catalog_backup:false]
  * [parallel_data_backup_backint_channels:1]
  * [log_backup_timeout_s:900)]
  * [backint_response_timeout:7200]

>[!NOTE]
>Upewnij się, że te parametry *nie* są obecne na poziomie HOSTa. Parametry na poziomie hosta zastąpią te parametry i mogą spowodować nieoczekiwane zachowanie.
>

* **Wtyczka kopii zapasowej platformy Azure dla hana** przechowuje wszystkie harmonogramy tworzenia kopii zapasowych i szczegóły zasad. Wyzwala zaplanowane kopie zapasowe i komunikuje się z **aparatem kopii zapasowych HANA** za pośrednictwem interfejsów API backint.
* **Aparat kopii zapasowej HANA** zwraca strumień Backint z danymi, które mają być archiwizowane.
* Wszystkie zaplanowane kopie zapasowe i kopie zapasowe na żądanie (wyzwalane z witryny Azure portal), które są pełne lub różnicowe są inicjowane przez **wtyczkę azure backup dla hana**. Jednak kopie zapasowe dziennika są zarządzane i wyzwalane przez sam **aparat kopii zapasowych HANA.**
* Usługa Azure Backup for SAP HANA, będąca certyfikowanym rozwiązaniem BackInt, nie zależy od typów dysku lub maszyny wirtualnej. Kopia zapasowa jest wykonywana przez strumienie generowane przez HANA.

## <a name="using-azure-vm-backup-with-azure-sap-hana-backup"></a>Korzystanie z kopii zapasowej maszyny Wirtualnej platformy Azure za pomocą kopii zapasowej usługi Azure SAP HANA

Oprócz korzystania z kopii zapasowej SAP HANA na platformie Azure, która zapewnia tworzenie kopii zapasowych i odzyskiwanie na poziomie bazy danych, można użyć rozwiązania do tworzenia kopii zapasowych maszyn wirtualnych platformy Azure do tworzenia kopii zapasowych dysków systemu operacyjnego i innych niż bazy danych.

[Rozwiązanie do tworzenia kopii zapasowych usługi Azure SAP HANA z certyfikatem Backint](#backup-architecture) może służyć do tworzenia kopii zapasowych i odzyskiwania bazy danych.

[Tworzenie kopii zapasowej maszyny Wirtualnej platformy Azure](backup-azure-vms-introduction.md) może służyć do tworzenia kopii zapasowych systemu operacyjnego i innych dysków innych niż bazy danych. Kopia zapasowa maszyny Wirtualnej jest podejmowana raz dziennie i kopie zapasowe wszystkich dysków (z wyjątkiem **dysków Akceleratora Zapisu (WA)** i **UltraDisks).** Ponieważ kopia zapasowa bazy danych jest zabezpieczonych przy użyciu rozwiązania do tworzenia kopii zapasowych usługi Azure SAP HANA, można wykonać spójną kopię zapasową tylko dysków systemu operacyjnego i innych niż baza danych przy użyciu możliwości wykluczania dysku, która jest obecnie w wersji zapoznawczej.

>[!NOTE]
> Przy użyciu skryptów wstępnych z kopii zapasowej maszyny Wirtualnej platformy Azure pozwoli spójne aplikacje kopii zapasowych woluminów danych bazy danych. Jeśli jednak obszar dziennika znajduje się na dyskach WA, zrobinie migawki tych dysków może nie zagwarantować spójności obszaru dziennika. HANA ma jawny sposób generowania kopii zapasowych dziennika z tego dokładnego powodu. Włącz to samo w sap HANA i można wykonać kopię zapasową przy użyciu kopii zapasowej usługi Azure SAP HANA.

Aby przywrócić maszynę wirtualną z systemem SAP HANA, wykonaj następujące kroki:

* [Przywracanie nowej maszyny Wirtualnej z kopii zapasowej maszyny Wirtualnej platformy Azure](backup-azure-arm-restore-vms.md) z najnowszego punktu odzyskiwania. Możesz też utworzyć nową pustą maszynę wirtualną i dołączyć dyski z najnowszego punktu odzyskiwania.
* Ponieważ kopie zapasowe dysków WA nie są archiwizowane, nie są przywracane. Utwórz puste dyski WA i obszar dziennika.
* Po ustawieniu wszystkich innych konfiguracji (takich jak IP, nazwa systemu i tak dalej), maszyna wirtualna jest ustawiona do odbierania danych bazy danych z kopii zapasowej platformy Azure.
* Teraz przywróć bazę danych do maszyny Wirtualnej z [kopii zapasowej bazy danych usługi Azure SAP HANA](sap-hana-db-restore.md#restore-to-a-point-in-time-or-to-a-recovery-point) do żądanej funkcji punktu w czasie.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [przywrócić bazę danych SAP HANA działającą na maszynie Wirtualnej platformy Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* Dowiedz się, jak [zarządzać bazami danych SAP HANA, których kopia zapasowa jest archiwizna przy użyciu usługi Azure Backup](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)
