---
title: 'Usługa Azure Backup: Monitorowanie usługi Azure Backup za pomocą usługi Azure Monitor'
description: Monitorowanie obciążeń usługi Azure Backup i tworzyć niestandardowe alerty przy użyciu usługi Azure Monitor.
services: backup
author: pvrk
manager: shivamg
keywords: Usługa log Analytics; Usługa Azure Backup; Alerty; Ustawienia diagnostyczne; Grupy akcji
ms.service: backup
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: pullabhk
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: e2d4a235737789f2f5852c00218427613db3d558
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786311"
---
# <a name="monitor-at-scale-by-using-azure-monitor"></a>Monitorowanie na dużą skalę za pomocą usługi Azure Monitor

Usługa Azure Backup udostępnia [wbudowane funkcje monitorowania i alertów](backup-azure-monitoring-built-in-monitor.md) w magazynie usługi Recovery Services. Te możliwości są dostępne bez jakiejkolwiek infrastruktury dodatkowego zarządzania. Ale to wbudowana usługa jest ograniczona w następujących scenariuszach:

- Jeśli dane z wielu magazynów usługi Recovery Services można monitorować w subskrypcjach
- Jeśli kanał powiadomień preferowane jest *nie* wiadomości e-mail
- Użytkownicy, którzy chcą alerty dla scenariuszy
- Jeśli chcesz wyświetlić informacje o składnika w środowisku lokalnym, takich jak System Center Data Protection Manager na platformie Azure, która nie była widoczna w portalu [ **zadania tworzenia kopii zapasowej** ](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault) lub [  **Alerty kopii zapasowej**](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault)

## <a name="using-log-analytics-workspace"></a>Korzystanie z obszaru roboczego usługi Log Analytics

> [!NOTE]
> Dane z kopii zapasowych maszyn wirtualnych platformy Azure, agent usługi Kopia zapasowa Azure, System Center Data Protection Manager, kopii zapasowych SQL na maszynach wirtualnych platformy Azure i kopiami zapasowymi udziałów plików platformy Azure jest kierowane do obszaru roboczego usługi Log Analytics, za pomocą ustawień diagnostycznych. 

Aby monitorowanie na dużą skalę, musisz mieć możliwości dwóch usług platformy Azure. *Ustawienia diagnostyczne* wysyłania danych z wielu zasobów usługi Azure Resource Manager do innego zasobu. *Log Analytics* generuje niestandardowe alerty, których można użyć grup akcji do definiowania innych kanałów powiadomień. 

W poniższych sekcjach opisano, jak używać usługi Log Analytics do monitorowania usługi Azure Backup na dużą skalę.

### <a name="configure-diagnostic-settings"></a>Konfigurowanie ustawień diagnostycznych

Zasoby usługi Resource Manager platformy Azure, takich jak magazyn usługi Recovery Services rejestrować informacje o zaplanowane operacje i użytkownik wyzwolił operacje jako dane diagnostyczne. 

W sekcji Monitorowanie, wybierz **ustawień diagnostycznych** i określ miejsce docelowe danych diagnostycznych w magazynie usługi Recovery Services.

![Ustawienia diagnostyczne magazynu usługi Recovery Services, przeznaczone dla usługi Log Analytics](media/backup-azure-monitoring-laworkspace/rs-vault-diagnostic-setting.png)

Można wskazać obszar roboczy usługi Log Analytics z innej subskrypcji. Aby monitorować magazyny w subskrypcjach w jednym miejscu, należy wybrać tego samego obszaru roboczego analizy dzienników dla wielu magazynów usługi Recovery Services. Aby channel wszystkie informacje dotyczące usługi Azure Backup do obszaru roboczego usługi Log Analytics, wybierz pozycję **AzureBackupReport** do logowania.

> [!IMPORTANT]
> Po zakończeniu konfiguracji, należy poczekać do wypychania danych początkowych zakończyć całą dobę. Po początkowej, wypychanie danych, wszystkie zdarzenia są przekazywane, zgodnie z opisem w dalszej części tego artykułu w [sekcji częstotliwość](#diagnostic-data-update-frequency).

### <a name="deploy-a-solution-to-the-log-analytics-workspace"></a>Wdrażanie rozwiązania do obszaru roboczego usługi Log Analytics

Po umieszczeniu danych wewnątrz obszaru roboczego usługi Log Analytics [wdrażanie szablonu usługi GitHub](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) do usługi Log Analytics w celu wizualizacji danych. Aby poprawnie zidentyfikować obszaru roboczego, upewnij się, że tworzonemu elementowi nadawana tej samej grupie zasobów, nazwę obszaru roboczego i lokalizacji obszaru roboczego. Następnie zainstaluj ten szablon, w obszarze roboczym.

> [!NOTE]
> Jeśli nie masz alerty, zadania tworzenia kopii zapasowej lub przywracania zadania w obszarze roboczym usługi Log Analytics, można napotkać kod błędu "BadArgumentError", w portalu. Ten błąd zignorować i kontynuować korzystanie z rozwiązania. Po odpowiednie dane typu rozpoczyna się do obszaru roboczego, wizualizacje będą odzwierciedlać takie same, i nie będzie już wyświetlany błąd.

### <a name="view-azure-backup-data-by-using-log-analytics"></a>Wyświetlanie danych usługi Azure Backup przy użyciu usługi Log Analytics

Po wdrożeniu szablonu rozwiązania do monitorowania usługi Azure Backup będą widoczne w obszarze Podsumowanie obszaru roboczego. Aby przejść do podsumowania, wykonaj jedną z tych ścieżek:

- **Azure Monitor**: W **Insights** zaznacz **więcej** , a następnie wybierz odpowiedni obszar roboczy.
- **Zaloguj się obszarów roboczych usługi Analytics**: Wybierz odpowiedni obszar roboczy, a następnie w obszarze **ogólne**, wybierz opcję **podsumowanie obszaru roboczego**.

![Na kafelku monitorowania usługi Log Analytics](media/backup-azure-monitoring-laworkspace/la-azurebackup-azuremonitor-tile.png)

Po wybraniu kafelka monitorowania, projektanta szablon zostanie otwarty serii wykresów o podstawowe dane monitorowania z usługi Azure Backup. Oto kilka wykresów, które zostaną wyświetlone:

* Wszystkie zadania tworzenia kopii zapasowej

   ![Log Analytics wykresów zadań tworzenia kopii zapasowej](media/backup-azure-monitoring-laworkspace/la-azurebackup-allbackupjobs.png)

* Przywracanie zadania

   ![Wykres analizy dziennika dla zadania przywracania](media/backup-azure-monitoring-laworkspace/la-azurebackup-restorejobs.png)

* Alerty wbudowane usługi Azure Backup dla zasobów platformy Azure

   ![Wykres analizy dzienników dla wbudowanych alertów usługi Azure Backup dla zasobów platformy Azure](media/backup-azure-monitoring-laworkspace/la-azurebackup-activealerts.png)

* Wbudowane alerty usługi Azure Backup dla zasobów lokalnych

   ![Wykres analizy dzienników dla wbudowanych alertów usługi Azure Backup dla zasobów w środowisku lokalnym](media/backup-azure-monitoring-laworkspace/la-azurebackup-activealerts-onprem.png)

* Aktywne źródła danych

   ![Wykres analizy dzienników dla aktywnych jednostek kopii zapasowej](media/backup-azure-monitoring-laworkspace/la-azurebackup-activedatasources.png)

* Magazyn w chmurze magazyn usługi Recovery Services

   ![Wykres analizy dzienników dla magazynu w chmurze magazynu usługi Recovery Services](media/backup-azure-monitoring-laworkspace/la-azurebackup-cloudstorage-in-gb.png)

Te wykresy są dostarczane z szablonem. Można edytować wykresy lub dodać więcej wykresów, jeśli potrzebujesz.

> [!IMPORTANT]
> Podczas wdrażania szablonu zasadniczo tworzysz blokadę tylko do odczytu. Aby edytować i zapisać szablon, musisz usunąć blokadę. Można usunąć blokady w **ustawienia** sekcji obszaru roboczego usługi Log Analytics na **blokuje** okienka.

### <a name="create-alerts-by-using-log-analytics"></a>Tworzenie alertów za pomocą usługi Log Analytics

W usłudze Azure Monitor możesz utworzyć alerty w obszarze roboczym usługi Log Analytics. W obszarze roboczym, możesz użyć *grup akcji platformy Azure* wybrać mechanizm Twojego preferowanego powiadomień. 

> [!IMPORTANT]
> Aby uzyskać informacje na koszt tworzenia tego zapytania, zobacz [cennika usługi Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

Wybierz dowolne wykresy, aby otworzyć **dzienniki** sekcji obszaru roboczego usługi Log Analytics. W **dzienniki** sekcji, edytowanie zapytań i tworzenia alertów na nich.

![Utwórz alert w obszarze roboczym usługi Log Analytics](media/backup-azure-monitoring-laworkspace/la-azurebackup-customalerts.png)

Po wybraniu **nową regułę alertu**, zostanie otwarta strona tworzenia alertu usługa Azure Monitor, jak pokazano na poniższej ilustracji. W tym miejscu zasobu jest już oznaczony jako obszar roboczy usługi Log Analytics, a podano integrację grup akcji.

![Strona tworzenia alertu usługi Log Analytics](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

#### <a name="alert-condition"></a>Warunek alertu

Definiowanie cech alertu jest jego warunku wyzwalania. Wybierz **warunek** automatycznie załadować zapytanie Kusto **dzienniki** stronie, jak pokazano na poniższej ilustracji. W tym miejscu można edytować warunku, zgodnie z potrzebami. Aby uzyskać więcej informacji, zobacz [zapytania Kusto przykładowe](#sample-kusto-queries).

![Definiowanie stanu alertu](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

Jeśli to konieczne, można edytować zapytania Kusto. Wybierz wartość progową, kropka i częstotliwości. Próg określa, kiedy zostanie wygenerowany alert. Okres to okno czasu, w którym jest uruchamiane zapytanie. Na przykład jeśli próg jest większa niż 0, okres wynosi 5 minut, a częstotliwość to 5 minut, następnie reguła wykonuje kwerendę co 5 minut, przeglądając poprzednich 5 minut. Jeśli liczba wyników jest większa niż 0, otrzymasz powiadomienie za pośrednictwem grupy wybranej akcji.

#### <a name="alert-action-groups"></a>Grupy akcji alertu

Użyj grupy akcji, aby określić kanału powiadomień. Aby wyświetlić powiadomienie o udostępnieniu mechanizmów, w obszarze **grup akcji**, wybierz opcję **Utwórz nowy**.

![Powiadomienie o udostępnieniu mechanizmów w oknie "Dodaj grupę akcji"](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

Może spełnić wszystkie alerty i monitorowanie wymagania od usługi Log Analytics samodzielnie, lub można użyć usługi Log Analytics, aby uzupełnić wbudowanych powiadomień.

Aby uzyskać więcej informacji, zobacz [tworzenie, wyświetlanie i zarządzanie alerty dzienników przy użyciu usługi Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) i [tworzenie grup akcji w witrynie Azure portal i zarządzanie nimi](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups).

### <a name="sample-kusto-queries"></a>Przykładowe zapytania Kusto

Wykresy domyślne umożliwiają zapytania Kusto w przypadku podstawowych scenariuszy, w których możesz tworzyć alerty. Można również zmodyfikować kwerendy w celu uzyskania danych czy chcesz otrzymywać alerty o. Wklej następujące przykładowe zapytania Kusto w **dzienniki** strony, a następnie tworzenie alertów dotyczących zapytania:

* Wszystkie zadania tworzenia kopii zapasowej zakończone pomyślnie

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Completed"
    ````
    
* Wszystkie zadania tworzenia kopii zapasowej nie powiodło się

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Failed"
    ````
    
* Wszystkie zadania tworzenia kopii zapasowej maszyny Wirtualnej platformy Azure, które zostały zakończone pomyślnie

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

* Wszystkie zadania zakończone pomyślnie SQL dziennika kopii zapasowej

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

* Wszystkie zadania zakończone pomyślnie agenta usługi Kopia zapasowa Azure

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

Dane diagnostyczne z magazynu jest kierowane do obszaru roboczego usługi Log Analytics, za pomocą pewne opóźnienie. Każde zdarzenie dociera do obszaru roboczego usługi Log Analytics *20 do 30 minut* po są wypychane z magazynu usługi Recovery Services. Poniżej przedstawiono więcej informacji o opóźnienie:

- We wszystkich rozwiązaniach usługi kopii zapasowej wbudowane alerty są wypychane zaraz po ich utworzeniu. Dlatego zwykle pojawiają się w obszarze roboczym usługi Log Analytics po 20 do 30 minut.
- We wszystkich rozwiązaniach ad-hoc zadania tworzenia kopii zapasowej i przywracania zadania są tak szybko, jak są wypychane *Zakończ*.
- Dla wszystkich rozwiązań z wyjątkiem kopii zapasowej SQL zaplanowanych zadań kopii zapasowej są wypychane tak szybko, jak one *Zakończ*.
- Do tworzenia kopii zapasowych SQL ponieważ kopie zapasowe dziennika, może wystąpić co 15 minut informacji dla wszystkich zakończonych zaplanowanych zadań kopii zapasowej, w tym dzienniki, jest partii i wypchnięto co 6 godzin.
- We wszystkich rozwiązaniach inne informacje, takie jak element kopii zapasowej, zasad, punkty odzyskiwania, magazynu i tak dalej jest przypisany co najmniej *raz dziennie.*
- Zmiany w konfiguracji kopii zapasowej (takie jak zmiana zasad lub edytowanie zasad) wyzwala powiadomienie wypychane wszystkich powiązanych informacji o kopii zapasowej.

## <a name="using-the-recovery-services-vaults-activity-logs"></a>Przy użyciu dzienników aktywności w magazynie usługi Recovery Services

> [!CAUTION]
> Poniższe kroki dotyczą tylko programu *kopii zapasowych maszyn wirtualnych platformy Azure.* Nie można użyć tych kroków w przypadku rozwiązań takich jak agent usługi Kopia zapasowa Azure, kopii zapasowych SQL w obrębie platformy Azure lub usługi Azure Files.

Dzienniki aktywności umożliwia również Otrzymuj powiadomienia dla zdarzenia, takie jak pomyślnego utworzenia kopii zapasowych. Aby rozpocząć, wykonaj następujące kroki:

1. Zaloguj się do witryny Azure portal.
1. Otwórz odpowiedni magazyn usługi Recovery Services. 
1. W oknie właściwości dla magazynu, należy otworzyć **dziennika aktywności** sekcji.

Aby zidentyfikować odpowiedniego dziennika i tworzenia alertów:

1. Sprawdź, czy trafiła do Ciebie Dzienniki aktywności dla pomyślnie tworzyć kopie zapasowe, stosując filtry pokazano na poniższej ilustracji. Zmiana **Timespan** wartość zgodnie z potrzebami, aby wyświetlić rekordy.

   ![Filtrowanie, aby znaleźć dzienników aktywności dla kopii zapasowych maszyn wirtualnych platformy Azure](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

1. Wybierz nazwę działania, aby wyświetlić odpowiednie dane.
1. Wybierz **Nowa reguła alertu** otworzyć **Utwórz regułę** strony. 
1. Utwórz alert, wykonując kroki opisane w [tworzenie, wyświetlanie i zarządzanie alertów dziennika aktywności przy użyciu usługi Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log).

   ![Nowa reguła alertu](media/backup-azure-monitoring-laworkspace/new-alert-rule.png)

W tym miejscu zasób jest samym magazynem usługi Recovery Services. Te same kroki należy powtórzyć dla wszystkich magazynów, w których chcesz otrzymywać powiadomienia za pomocą dzienników aktywności. Warunek nie będzie miał wartość progową, kropką ani częstotliwości, ponieważ ten alert jest oparty na zdarzeniach. Jak najszybciej zostanie wygenerowany dziennik odpowiednie działania, zgłaszany jest alert.

## <a name="using-log-analytics-to-monitor-at-scale"></a>Monitorowanie na dużą skalę za pomocą usługi Log Analytics

Możesz wyświetlić wszystkie alerty utworzone na podstawie dzienników aktywności i obszarów roboczych usługi Log Analytics w usłudze Azure Monitor. Po prostu otwórz **alerty** w okienku po lewej stronie.

Mimo że można uzyskać powiadomienia za pomocą dzienników aktywności, zdecydowanie zaleca się używanie usługi Log Analytics, a nie dzienników aktywności do monitorowania na dużą skalę. Oto Dlaczego:

- **Ograniczone scenariusze**: Powiadomienia o dziennikach aktywności mają zastosowanie tylko do tworzenia kopii zapasowych maszyn wirtualnych platformy Azure. Powiadomienia można ustawić dla każdego magazynu usługi Recovery Services.
- **Definicja Dopasuj**: Zaplanowane działania tworzenia kopii zapasowej nie mieści się przy użyciu najnowszej definicji dzienników aktywności. Zamiast tego powoduje wyrównanie z [dzienniki diagnostyczne](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview#what-you-can-do-with-diagnostic-logs). Wyrównanie tego powoduje, że niespodziewane skutki danych, które przechodzą przez działanie po zalogowaniu się zmiany kanału.
- **Problemy z kanałem dziennika aktywności**: W magazynach usługi Recovery Services dzienników aktywności, które są kierowane z usługi Azure Backup postępuj zgodnie z nowego modelu. Niestety ta zmiana ma wpływ na generowanie dzienników aktywności w Azure Government, Azure (Niemcy) i Azure (Chiny) — firmą 21Vianet. Alerty nie są wyzwalane, jeśli użytkownicy tych usług w chmurze tworzyć, konfigurować żadnych alertów, od dzienników aktywności w usłudze Azure Monitor. Ponadto w przypadku wszystkich publicznych regionach platformy Azure, jeśli użytkownik [służy do zbierania dzienników aktywności usługi Recovery Services do obszaru roboczego usługi Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs), dzienniki te nie są wyświetlane.

Używanie obszaru roboczego usługi Log Analytics do monitorowania i alertów na dużą skalę dla wszystkich obciążeń, które są chronione przez usługę Azure Backup.

## <a name="next-steps"></a>Kolejne kroki

Aby utworzyć zapytań niestandardowych, zobacz [modelu danych usługi Log Analytics](backup-azure-log-analytics-data-model.md).
