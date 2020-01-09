---
title: Informacje o kopii zapasowej bazy danych SAP HANA na maszynach wirtualnych platformy Azure
description: W tym artykule dowiesz się, jak tworzyć kopie zapasowe baz danych SAP HANA, które są uruchomione na maszynach wirtualnych platformy Azure.
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: aad9e7e89c54100f460a7f348702d0a59e88f519
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75479464"
---
# <a name="about-sap-hana-database-backup-in-azure-vms"></a>Informacje o kopii zapasowej bazy danych SAP HANA na maszynach wirtualnych platformy Azure

Bazy danych SAP HANA są obciążeniami o znaczeniu krytycznym, które wymagają małego celu punktu odzyskiwania (RPO) i szybkiego celu czasu odzyskiwania (RTO). Teraz można [tworzyć kopie zapasowe baz danych SAP HANA działających na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db) przy użyciu [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview).

Azure Backup jest [BACKINT certyfikowany](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5) przez SAP, aby zapewnić natywną obsługę kopii zapasowych, wykorzystując natywne interfejsy API SAP HANA. Ta oferta z Azure Backup jest wyrównania Azure Backup mantrą kopii zapasowych o **zerowej infrastrukturze** , eliminując konieczność wdrażania infrastruktury kopii zapasowych i zarządzania nią. Teraz można bezproblemowo tworzyć kopie zapasowe i przywracać SAP HANA bazy danych działające na maszynach wirtualnych platformy Azure ([maszyny wirtualne serii M](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#m-series) są również obsługiwane) i korzystać z możliwości zarządzania przedsiębiorstwem, które Azure Backup zapewnia.

## <a name="added-value"></a>Dodana wartość

Używanie Azure Backup do tworzenia kopii zapasowych i przywracania SAP HANA baz danych programu daje następujące korzyści:

* **15-minutowy cel punktu odzyskiwania (RPO)** : możliwe jest odzyskanie krytycznych danych z maksymalnie 15 minut.
* Przywracanie z **jednym kliknięciem do punktu w czasie**: przywrócenie danych produkcyjnych do alternatywnych serwerów Hana odbywa się w prosty sposób. Łańcuch tworzenia kopii zapasowych i wykazów do wykonania przywracania jest zarządzany przez platformę Azure w tle.
* **Długoterminowe przechowywanie**: w celu uzyskania rygorystycznych wymagań dotyczących zgodności i inspekcji. Przechowuj kopie zapasowe przez lata na podstawie czasu przechowywania, po upływie którego punkty odzyskiwania zostaną automatycznie oczyszczone przez wbudowaną funkcję zarządzania cyklem życia.
* **Zarządzanie kopiami zapasowymi z platformy Azure**: Użyj funkcji zarządzania i monitorowania Azure Backup, aby uzyskać Ulepszone środowisko zarządzania. Interfejs wiersza polecenia platformy Azure jest również obsługiwany.

Aby wyświetlić scenariusze tworzenia kopii zapasowych i przywracania, które obsługuje dzisiaj, zapoznaj się z artykułem [Obsługa scenariusza SAP HANA](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support).

## <a name="backup-architecture"></a>Architektura kopii zapasowych

![Diagram architektury tworzenia kopii zapasowych](./media/sap-hana-db-about/backup-architecture.png)

* Proces tworzenia kopii zapasowej rozpoczyna się od [utworzenia magazynu usługi Recovery Services](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#create-a-recovery-service-vault) na platformie Azure. Ten magazyn będzie używany do przechowywania kopii zapasowych i punktów odzyskiwania utworzonych w czasie.
* Maszyna wirtualna platformy Azure z systemem SAP HANA Server jest zarejestrowana w magazynie, a bazy danych do utworzenia kopii zapasowej są [odnajdywane](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#discover-the-databases). Aby umożliwić usłudze Azure Backup odnajdywanie baz danych, należy uruchomić [skrypt rejestracji](https://aka.ms/scriptforpermsonhana) na serwerze Hana jako użytkownik główny.
* Ten skrypt tworzy użytkownika **AZUREWLBACKUPHANAUSER** DB i odpowiadający mu klucz o tej samej nazwie w **hdbuserstore**. Zapoznaj się z [sekcją Konfigurowanie uprawnień](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#setting-up-permissions) , aby dowiedzieć się więcej na temat tego, co robi skrypt.
* Usługa Azure Backup teraz instaluje **wtyczkę Azure Backup dla platformy Hana** na zarejestrowanym serwerze SAP HANA.
* Użytkownik **AZUREWLBACKUPHANAUSER** DB utworzony przez skrypt przedrejestrowania jest używany przez **wtyczkę Azure Backup platformy Hana** do wykonywania wszystkich operacji tworzenia kopii zapasowej i przywracania. W przypadku próby skonfigurowania kopii zapasowej dla SAP HANA baz danych bez uruchamiania tego skryptu może zostać wyświetlony następujący błąd: **UserErrorHanaScriptNotRun**.
* Aby [skonfigurować tworzenie kopii zapasowych](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#configure-backup) w odnalezionych bazach danych, wybierz wymagane zasady tworzenia kopii zapasowych i Włącz kopie zapasowe.

* Po skonfigurowaniu kopii zapasowej usługa Azure Backup konfiguruje następujące parametry BACKINT na poziomie bazy danych na serwerze chronionym SAP HANA:
  * [catalog_backup_using_backint: true]
  * [enable_accumulated_catalog_backup: false]
  * [parallel_data_backup_backint_channels:1]
  * [log_backup_timeout_s: 900)]
  * [backint_response_timeout: 7200]

>[!NOTE]
>Upewnij się, że te parametry *nie* występują na poziomie hosta. Parametry na poziomie hosta przesłonią te parametry i mogą spowodować nieoczekiwane zachowanie.
>

* **Wtyczka Azure Backup dla platformy Hana** obsługuje wszystkie harmonogramy tworzenia kopii zapasowych i Szczegóły zasad. Wyzwala zaplanowaną kopię zapasową i komunikuje się z **aparatem tworzenia kopii zapasowych Hana** za pomocą interfejsów API BACKINT.
* **Aparat kopii zapasowych Hana** zwraca strumień BACKINT z danymi, których kopia zapasowa ma zostać utworzona.
* Wszystkie zaplanowane kopie zapasowe i kopie zapasowe na żądanie (wyzwolone za pomocą Azure Portal), które są pełnymi lub różnicowanymi, są inicjowane przez **wtyczkę Azure Backup dla platformy Hana**. Kopie zapasowe dzienników są jednak zarządzane i wyzwalane przez sam **Aparat kopii zapasowych Hana** .

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [przywrócić bazę danych SAP HANA działającej na maszynie wirtualnej platformy Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* Dowiedz się [, jak zarządzać bazami danych SAP HANA, których kopia zapasowa została utworzona przy użyciu Azure Backup](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)
