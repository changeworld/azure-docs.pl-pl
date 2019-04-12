---
title: 'Usługa Azure Backup: Monitorowanie usługi Azure Backup za pomocą usługi Azure Monitor'
description: Monitorowanie obciążeń usługi Azure Backup i tworzyć niestandardowe alerty przy użyciu usługi Azure Monitor
services: backup
author: pvrk
manager: shivamg
keywords: Usługa log Analytics; Usługa Azure Backup; Alerty; Ustawienia diagnostyczne; Grupy akcji
ms.service: backup
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: pullabhk
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: 15bb64917fa58ba2d13c6f372640957508ab29c1
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/11/2019
ms.locfileid: "59494563"
---
# <a name="monitoring-at-scale-using-azure-monitor"></a>Monitorowanie na dużą skalę za pomocą usługi Azure Monitor

[Wbudowane funkcje monitorowania i zgłaszania alertów artykułu](backup-azure-monitoring-built-in-monitor.md) monitorowania i alertów w jednym magazynie usług odzyskiwania i która jest oferowana bez jakiejkolwiek infrastruktury zarządzania dodatkowe na liście. Jednak wbudowanej usługi, jest ograniczona w następujących scenariuszach.

- Dane monitorowania z wielu magazynów RS między subskrypcjami
- Jeśli adres e-mail nie jest kanału powiadomień preferowane
- Jeśli użytkownik chce otrzymywać alerty dla scenariuszy
- Jeśli chcesz wyświetlić informacje o lokalnych składnika, takiego jak System Center DPM (SC-DPM) na platformie Azure, która nie jest wyświetlany w [zadania tworzenia kopii zapasowej](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault) lub [alerty kopii zapasowej](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) w portalu.

## <a name="using-log-analytics-workspace"></a>Za pomocą obszaru roboczego usługi Log Analytics

> [!NOTE]
> Dane z usługi Azure kopie zapasowe maszyn wirtualnych, agenta MAB programu System Center DPM (SC-DPM) oraz kopii zapasowych SQL na maszynach wirtualnych Azure jest są kierowane do obszaru roboczego usługi Log Analytics, za pomocą ustawień diagnostycznych. Obsługa kopii zapasowych z udziału plików platformy Azure, Microsoft Azure Backup Server (MABS) będzie dostępna wkrótce.

Firma Microsoft korzysta z możliwości usług platformy Azure, dwóch - **ustawień diagnostycznych** (w celu wysyłania danych z wielu zasobów usługi Azure Resource Manager do innego zasobu) i **usługi Log Analytics** (LA — w celu wygenerowania niestandardowe alerty, w którym można zdefiniować innych kanałów powiadomień przy użyciu grup akcji) do monitorowania na dużą skalę. W poniższych sekcjach szczegółowo o tym, jak używać LA do monitorowania usługi Azure Backup na dużą skalę.

### <a name="configuring-diagnostic-settings"></a>Konfigurowanie ustawień diagnostycznych

Zasób usługi Azure Resource Manager, takie jak magazyn usługi Azure Recovery services rejestruje wszystkie dostępne informacje o zaplanowanych operacji i operacje użytkownika wyzwalane jako dane diagnostyczne. Kliknij przycisk "ustawienia diagnostyczne" w sekcji monitorowania i określ miejsce docelowe danych diagnostycznych magazynu RS.

![Ustawienia diagnostyczne magazynu RS z LA jako element docelowy](media/backup-azure-monitoring-laworkspace/rs-vault-diagnostic-setting.png)

Możesz wybrać obszar roboczy LA z innej subskrypcji, jako element docelowy. *Wybranie tego samego obszaru roboczego LA wiele magazynów RS, można monitorować magazyny w subskrypcjach w jednym miejscu.* Wybierz pozycję "AzureBackupReport" do logowania w witrynie Channel wszystkie informacje, do obszaru roboczego LA związane z usługi Azure Backup.

> [!IMPORTANT]
> Po zakończeniu konfiguracji należy Poczekaj 24 godziny do wypychania danych początkowych zakończyć. Dzięki temu wszystkie zdarzenia są wypychane zgodnie z opisem w [sekcji częstotliwość](#diagnostic-data-update-frequency).

### <a name="deploying-solution-to-log-analytics-workspace"></a>Wdrażanie rozwiązania do obszaru roboczego usługi Log Analytics

Gdy dane znajdują się w obszarze roboczym LA [wdrażanie szablonu usługi github](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) na LA umożliwiają wizualizację danych. Upewnij się, że nadasz tej samej grupie zasobów, nazwę obszaru roboczego i lokalizacji obszaru roboczego, prawidłowo identyfikować obszaru roboczego, a następnie zainstalować ten szablon na nim.

### <a name="view-azure-backup-data-using-log-analytics-la"></a>Wyświetl dane usługi Azure Backup przy użyciu Log Analytics (LA)

Po wdrożeniu szablonu rozwiązania do monitorowania usługi Azure Backup będą widoczne w obszarze Podsumowanie obszaru roboczego. Można przejść za pomocą

- Usługa Azure Monitor -> "Więcej" w sekcji "Insights" i wybierz odpowiedni obszar roboczy lub
- Obszary robocze usługi log Analytics -> Wybierz odpowiedni obszar roboczy -> "Workspace summary" w sekcji Ogólne.

![AzureBackupLAMonitoringTile](media/backup-azure-monitoring-laworkspace/la-azurebackup-azuremonitor-tile.png)

Po kliknięciu przycisku na kafelku, szablon projektanta otwiera serii wykresów dotyczących podstawowych danych monitorowania z usługi Azure Backup, takich jak

#### <a name="all-backup-jobs"></a>Wszystkie zadania tworzenia kopii zapasowej

![LABackupJobs](media/backup-azure-monitoring-laworkspace/la-azurebackup-allbackupjobs.png)

#### <a name="restore-jobs"></a>Przywracanie zadania

![LARestoreJobs](media/backup-azure-monitoring-laworkspace/la-azurebackup-restorejobs.png)

#### <a name="inbuilt-azure-backup-alerts-for-azure-resources"></a>Wbudowane alerty usługi Azure Backup dla zasobów platformy Azure

![LAInbuiltAzureBackupAlertsForAzureResources](media/backup-azure-monitoring-laworkspace/la-azurebackup-activealerts.png)

#### <a name="inbuilt-azure-backup-alerts-for-on-prem-resources"></a>Wbudowane alerty usługi Azure Backup dla zasobów lokalnych

![LAInbuiltAzureBackupAlertsForOnPremResources](media/backup-azure-monitoring-laworkspace/la-azurebackup-activealerts-onprem.png)

#### <a name="active-datasources"></a>Aktywne źródła danych

![LAActiveBackedUpEntities](media/backup-azure-monitoring-laworkspace/la-azurebackup-activedatasources.png)

#### <a name="rs-vault-cloud-storage"></a>RS Vault Cloud Storage

![LARSVaultCloudStorage](media/backup-azure-monitoring-laworkspace/la-azurebackup-cloudstorage-in-gb.png)

Wykresy powyżej są dostarczane z szablonem a klienta jest bezpłatny do edycji/Dodaj więcej wykresów.

> [!IMPORTANT]
> Podczas wdrażania szablonu zasadniczo tworzy blokadę tylko do odczytu i konieczne jest usunięcie go, aby edytować szablon i zapisać. Aby usunąć blokady, poszukaj w okienku "Blokady" Przejdź do sekcji "Ustawienia" w obszarze roboczym usługi Log Analytics.

### <a name="create-alerts-using-log-analytics"></a>Tworzenie alertów za pomocą usługi Log Analytics

Usługa Azure Monitor pozwala użytkownikom na tworzenie własnych alertów z obszaru roboczego LA, gdzie można *korzystać z grup akcji platformy Azure, aby wybrać mechanizm Twojego preferowanego powiadomień*. Kliknij dowolne wykresy, powyżej, aby otworzyć obszar roboczy LA części "Dzienniki" ***którym można edytować zapytania i tworzyć alerty oparte na nich***.

![LACreateAlerts](media/backup-azure-monitoring-laworkspace/la-azurebackup-customalerts.png)

Klikając przycisk "Nowy Alert regułą" jak pokazano powyżej otworzy ekran tworzenia alertu usługa Azure Monitor.

Jak można zauważyć, że poniżej, zasób jest już oznaczony jako obszar roboczy LA i podano integrację grup akcji.

![LAAzureBackupCreateAlert](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

> [!IMPORTANT]
> Należy pamiętać, że znajduje się odpowiedni wpływ na cenę tworzenia tego zapytania [tutaj](https://azure.microsoft.com/pricing/details/monitor/).

#### <a name="alert-condition"></a>Warunek alertu

Kluczowym aspektem jest warunek wyzwalający alert. Klikając polecenie "Warunek" zostanie automatycznie załadowany zapytanie Kusto na ekranie "Dzienniki", jak pokazano poniżej, a następnie można edytować go do potrzeb danego scenariusza. Pewne przykładowe zapytania Kusto znajdują się w [poniżej](#sample-kusto-queries).

![LAAzureBackupAlertCondition](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

Edytuj zapytanie Kusto, jeśli to konieczne, zaznacz odpowiednie wartości progowej, (który zdecyduje, gdy alert zostanie wyzwolone), prawy okres (przedział czasu dla której jest uruchamiane zapytanie) oraz częstotliwość. Na przykład: Jeśli wartość progową jest większa niż 0, okres wynosi 5 minut i częstotliwość to 5 minut, następnie reguła jest tłumaczony jako "Uruchom kwerendę co 5 minut do ostatnich 5 minut, a jeśli liczba wyników jest większa niż 0, Powiadom mnie za pośrednictwem grupy wybranej akcji"

#### <a name="action-group-integration"></a>Integracja grupy akcji

Grupy akcji Określ udostępniony użytkownikowi kanały powiadomień. W obszarze "Grupy akcji" klikając polecenie "Utwórz nowy" sekcja pokazuje listę dostępnych mechanizmów wysyłania powiadomień.

![LAAzureBackupNewActionGroup](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

Dowiedz się więcej o [alertów z obszaru roboczego LA](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) i o [grup akcji](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) w dokumentacji usługi Azure Monitor.

Dlatego można spełniać wszystkie alerty i monitorowanie wymagań LA samodzielnie lub użyj go jako dodatkowego techniki powiadomień wbudowane mechanizmy.

### <a name="sample-kusto-queries"></a>Przykładowe zapytania Kusto

Domyślnych grafów pozwoli uzyskać zapytania Kusto w przypadku podstawowych scenariuszy, w których możesz tworzyć alerty. Można także modyfikować ich w celu uzyskania danych, które mają być wykonywane po otrzymaniu na. W tym miejscu udostępniamy pewne przykładowe zapytania Kusto, które można wkleić w oknie "Dzienniki", a następnie Utwórz alert, na tym zapytaniu.

#### <a name="all-successful-backup-jobs"></a>Wszystkie zadania tworzenia kopii zapasowej zakończone pomyślnie

````Kusto
AzureDiagnostics
| where Category == "AzureBackupReport"
| where SchemaVersion_s == "V2"
| where OperationName == "Job" and JobOperation_s == "Backup"
| where JobStatus_s == "Completed"
````

#### <a name="all-failed-backup-jobs"></a>Wszystkie zadania tworzenia kopii zapasowej nie powiodło się

````Kusto
AzureDiagnostics
| where Category == "AzureBackupReport"
| where SchemaVersion_s == "V2"
| where OperationName == "Job" and JobOperation_s == "Backup"
| where JobStatus_s == "Failed"
````

#### <a name="all-successful-azure-vm-backup-jobs"></a>Wszystkie zadania tworzenia kopii zapasowej maszyny Wirtualnej platformy Azure, które zostały zakończone pomyślnie

````Kusto
AzureDiagnostics
| where Category == "AzureBackupReport"
| where SchemaVersion_s == "V2"
| extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
| where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s != "Log" and JobOperationSubType_s != "Recovery point_Log"
| join kind=inner
(
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where OperationName == "BackupItem"
    | where SchemaVersion_s == "V2"
    | where BackupItemType_s == "VM" and BackupManagementType_s == "IaaSVM"
    | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
    | project BackupItemUniqueId_s , BackupItemFriendlyName_s
)
on BackupItemUniqueId_s
| extend Vault= Resource
| project-away Resource
````

#### <a name="all-successful-sql-log-backup-jobs"></a>Wszystkie zadania tworzenia kopii zapasowej dziennika SQL, które zostały zakończone pomyślnie

````Kusto
AzureDiagnostics
| where Category == "AzureBackupReport"
| where SchemaVersion_s == "V2"
| extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
| where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s == "Log"
| join kind=inner
(
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where OperationName == "BackupItem"
    | where SchemaVersion_s == "V2"
    | where BackupItemType_s == "SQLDataBase" and BackupManagementType_s == "AzureWorkload"
    | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
    | project BackupItemUniqueId_s , BackupItemFriendlyName_s
)
on BackupItemUniqueId_s
| extend Vault= Resource
| project-away Resource
````

#### <a name="all-successful-mab-agent-backup-jobs"></a>Wszystkie zadania tworzenia kopii zapasowej agenta MAB, które zostały zakończone pomyślnie

````Kusto
AzureDiagnostics
| where Category == "AzureBackupReport"
| where SchemaVersion_s == "V2"
| extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
| where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s != "Log" and JobOperationSubType_s != "Recovery point_Log"
| join kind=inner
(
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where OperationName == "BackupItem"
    | where SchemaVersion_s == "V2"
    | where BackupItemType_s == "FileFolder" and BackupManagementType_s == "MAB"
    | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
    | project BackupItemUniqueId_s , BackupItemFriendlyName_s
)
on BackupItemUniqueId_s
| extend Vault= Resource
| project-away Resource
````

### <a name="diagnostic-data-update-frequency"></a>Częstotliwość aktualizacji danych diagnostycznych

Dane diagnostyczne z magazynu jest kierowane do obszaru roboczego LA z pewne opóźnienie. Każde zdarzenie dociera do obszaru roboczego LA ***z opóźnieniem 20 – 30 minut po są wypychane z magazynu RS.***

- Alerty wbudowane usługi kopii zapasowej (we wszystkich rozwiązaniach) są wypychane zaraz po ich utworzeniu. Co oznacza, że zwykle pojawiają się w obszarze roboczym LA po to opóźnienie 20 – 30 minut.
- Zadania tworzenia kopii zapasowych ad hoc i zadania przywracania (we wszystkich rozwiązaniach) są wypychane tak szybko, jak one ***odbywa się***.
- Zaplanowanych zadań kopii zapasowej z wszystkie rozwiązania (z wyjątkiem kopii zapasowej SQL) są wypychane tak szybko, jak one ***odbywa się***.
- Do tworzenia kopii zapasowych SQL ponieważ firma Microsoft mogą mieć kopie zapasowe dziennika co 15 minut dla wszystkich zakończonych zaplanowanych zadań kopii zapasowej, w tym dzienniki, informacje są partii i wypchnięto co 6 godzin.
- Wszystkie pozostałe informacje, takie jak element kopii zapasowej, zasad, punkty odzyskiwania, Magazyn itp. we wszystkich rozwiązaniach są wypychane **co najmniej raz dziennie.**
- Zmiany w konfiguracji kopii zapasowej, takie jak zmienianie docelowej edycji zasady itp. zasad wyzwala powiadomienie wypychane wszystkich powiązanych informacji o kopii zapasowej.

## <a name="using-rs-vaults-activity-logs"></a>Przy użyciu magazynu RS Dzienniki aktywności

Dzienniki aktywności umożliwia również Otrzymuj powiadomienia dla zdarzenia, takie jak pomyślnego utworzenia kopii zapasowych.

> [!CAUTION]
> **Należy pamiętać, że dotyczy to tylko kopii zapasowych maszyn wirtualnych platformy Azure.** Nie możesz użyć tego dla innych rozwiązań, takich jak Agent usługi Azure Backup, kopii zapasowych SQL na platformie Azure, Azure plików itp.

### <a name="sign-in-into-azure-portal"></a>Zaloguj się do witryny Azure portal

Zaloguj się do witryny Azure portal i przejść do odpowiedniego magazynu Azure Recovery Services i kliknij sekcję "Dziennik aktywności" we właściwościach.

### <a name="identify-appropriate-log-and-create-alert"></a>Zidentyfikować odpowiedni dziennik i Utwórz alert

Zastosuj filtry pokazano na poniższej ilustracji, aby sprawdzić, czy otrzymujesz dzienników aktywności dla pomyślnie tworzyć kopie zapasowe. Odpowiednio zmienić zakres czasu, aby wyświetlić rekordy.

![Dzienniki aktywności dla kopii zapasowych maszyn wirtualnych platformy Azure](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

Po kliknięciu segmentu "JSON", aby uzyskać więcej szczegółów i go wyświetlać przez kopiowanie wklejanie go do edytora tekstów. Powinna zostać wyświetlona szczegóły magazynu i elementu, który wyzwolił działania logowania oznacza to, że element kopii zapasowej.

Następnie kliknij przycisk "Dodaj alert dziennika aktywności" do generowania alertów dla wszystkich tych dzienników.

Możesz kliknąć "Dodaj alert dziennika aktywności" powyżej, co spowoduje otwarcie ekranu tworzenia alertu, który przypomina ekran tworzenia alertu [zgodnie z powyższym opisem](#create-alerts-using-log-analytics).

Oto zasób na sam magazyn RS i dlatego należy powtórzyć tę samą akcję dla wszystkich magazynów, w których chcesz powiadomienie za pośrednictwem dzienników aktywności. Warunek nie będzie żadnych wartości progowej, okres, częstotliwość to alertu opartego na zdarzeniach. Jak najszybciej zostanie wygenerowany dziennik odpowiednie działania, alert jest uruchamiany.

## <a name="recommendation"></a>Zalecenie

***Wszystkie alerty utworzone na podstawie dzienników aktywności i obszary robocze LA mogą być wyświetlane w usłudze Azure Monitor w okienku "Alerty" po lewej stronie.***

Powiadomienia za pomocą dzienników aktywności można zastosować, ***usługi Azure Backup zdecydowanie zaleca się na potrzeby monitorowania w skali i nie Dzienniki aktywności z następujących powodów LA***.

- **Ograniczonej liczbie scenariuszy:** Dotyczy tylko tworzenia kopii zapasowych maszyn wirtualnych platformy Azure i należy powtórzyć dla każdego magazynu RS.
- **Definicja dopasowania:** Zaplanowane działania tworzenia kopii zapasowej nie mieści się przy użyciu najnowszej definicji dzienników aktywności i zgodne z [dzienniki diagnostyczne](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview#what-are-azure-monitor-diagnostic-logs). To doprowadzić do nieoczekiwanego wpływ po zmianie danych przekazywanie za pośrednictwem kanału dzienników aktywności jako wskaźnik poniżej.
- **Problemy z kanałem dziennik aktywności:** Został przeniesiony do nowego modelu programu Przekazywanie dzienników aktywności usługi Azure Backup na magazynów usługi Recovery Services. Niestety przeniesienie ma wpływ na generowanie dzienników aktywności w chmurach suwerennych platformy Azure. Jeśli użytkownicy suwerennych w chmurze platformy Azure utworzona lub skonfigurowany alerty od dzienników aktywności za pośrednictwem usługi Azure Monitor, nie będą one wyzwolone. Ponadto w przypadku wszystkich publicznych regionach platformy Azure, jeśli użytkownika jest zbieranie dzienników aktywności usługi odzyskiwania do obszaru roboczego analizy dzienników, jak wspomniano wcześniej [tutaj](https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs), te dzienniki również nie było wyświetlane.

Dlatego zdecydowanie zaleca się używać obszaru roboczego analizy dzienników na potrzeby monitorowania i alertów na dużą skalę dla kopii zapasowej Azure chronionych obciążeń.

## <a name="next-steps"></a>Kolejne kroki

- Zapoznaj się [modelu danych usługi Log analytics](backup-azure-log-analytics-data-model.md) do tworzenia zapytań niestandardowych.
