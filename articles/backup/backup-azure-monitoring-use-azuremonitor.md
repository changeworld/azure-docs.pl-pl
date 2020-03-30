---
title: Monitorowanie tworzenia kopii zapasowej platformy Azure za pomocą usługi Azure Monitor
description: Monitoruj obciążenia usługi Azure Backup i twórz niestandardowe alerty przy użyciu usługi Azure Monitor.
ms.topic: conceptual
ms.date: 06/04/2019
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: 547cef66be9902468f4e2755c31e5f586eccad5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79459518"
---
# <a name="monitor-at-scale-by-using-azure-monitor"></a>Monitoruj na dużą skalę za pomocą usługi Azure Monitor

Usługa Azure Backup udostępnia [wbudowane funkcje monitorowania i alertów](backup-azure-monitoring-built-in-monitor.md) w magazynie usług odzyskiwania. Te możliwości są dostępne bez dodatkowej infrastruktury zarządzania. Ale ta wbudowana usługa jest ograniczona w następujących scenariuszach:

- Jeśli monitorujesz dane z wielu magazynów usług odzyskiwania w ramach subskrypcji
- Jeśli preferowanym kanałem powiadomień *nie* jest
- Jeśli użytkownicy chcą alertów dla większej liczby scenariuszy
- Jeśli chcesz wyświetlić informacje z lokalnego składnika, takiego jak Menedżer ochrony danych w centrum systemu na platformie Azure, którego portal nie jest pokazywał w [**zadaniach tworzenia kopii zapasowych**](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault) lub [**alertach dotyczących kopii zapasowych**](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault)

## <a name="using-log-analytics-workspace"></a>Korzystanie z obszaru roboczego usługi Log Analytics

### <a name="create-alerts-by-using-log-analytics"></a>Tworzenie alertów przy użyciu usługi Log Analytics

W usłudze Azure Monitor można tworzyć własne alerty w obszarze roboczym usługi Log Analytics. W obszarze roboczym można użyć *grup akcji platformy Azure,* aby wybrać preferowany mechanizm powiadomień.

> [!IMPORTANT]
> Aby uzyskać informacje na temat kosztów utworzenia tej kwerendy, zobacz [Cennik usługi Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

Otwórz sekcję **Dzienniki** obszaru roboczego usługi Log Analytics i utwórz zapytanie dla własnych dzienników. Po wybraniu **nowej reguły alertu**zostanie otwarta strona tworzenia alertów usługi Azure Monitor, jak pokazano na poniższej ilustracji.

![Tworzenie alertu w obszarze roboczym usługi Log Analytics](media/backup-azure-monitoring-laworkspace/custom-alert.png)

W tym miejscu zasób jest już oznaczony jako obszar roboczy usługi Log Analytics i integracja grupy akcji jest dostępna.

![Strona tworzenia alertów usługi Log Analytics](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

#### <a name="alert-condition"></a>Warunek alertu

Cechą definiującą alertu jest jego warunek wyzwalania. Wybierz **Warunek,** aby automatycznie załadować kwerendę Kusto na stronie **Dzienniki,** jak pokazano na poniższej ilustracji. Tutaj możesz edytować warunek zgodnie z twoimi potrzebami. Aby uzyskać więcej informacji, zobacz [Przykładowe zapytania Kusto](#sample-kusto-queries).

![Konfigurowanie warunku alertu](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

W razie potrzeby można edytować kwerendę Kusto. Wybierz próg, kropkę i częstotliwość. Próg określa, kiedy alert zostanie podniesiony. Okres jest oknem czasu, w którym kwerenda jest uruchamiana. Na przykład jeśli próg jest większy niż 0, okres wynosi 5 minut, a częstotliwość wynosi 5 minut, a następnie reguła uruchamia kwerendę co 5 minut, przeglądając poprzednie 5 minut. Jeśli liczba wyników jest większa niż 0, zostaniesz powiadomiony za pośrednictwem wybranej grupy akcji.

#### <a name="alert-action-groups"></a>Grupy akcji alertów

Użyj grupy akcji, aby określić kanał powiadomień. Aby wyświetlić dostępne mechanizmy powiadomień, w obszarze **Grupy akcji**wybierz pozycję **Utwórz nowy**.

![Dostępne mechanizmy powiadomień w oknie "Dodawanie grupy akcji"](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

Możesz spełnić wszystkie wymagania dotyczące alertów i monitorowania z usługi Log Analytics samodzielnie lub można użyć usługi Log Analytics w celu uzupełnienia wbudowanych powiadomień.

Aby uzyskać więcej informacji, zobacz [Tworzenie, wyświetlanie i zarządzanie alertami dziennika przy użyciu usługi Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) oraz tworzenie grup akcji i zarządzanie nimi w portalu [Azure.](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)

### <a name="sample-kusto-queries"></a>Przykładowe zapytania Kusto

Domyślne wykresy daje zapytania Kusto dla podstawowych scenariuszy, na których można tworzyć alerty. Można również zmodyfikować kwerendy, aby uzyskać dane, które mają być alerty na. Wklej następujące przykładowe zapytania Kusto na stronie **Dzienniki,** a następnie utwórz alerty dotyczące kwerend:

- Wszystkie udane zadania tworzenia kopii zapasowych

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | where JobStatus=="Completed"
    ````

- Wszystkie zadania tworzenia kopii zapasowych nie powiodły się

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | where JobStatus=="Failed"
    ````

- Wszystkie pomyślne zadania tworzenia kopii zapasowych maszyn wirtualnych platformy Azure

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="VM" and BackupManagementType=="IaaSVM"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- Wszystkie pomyślne zadania tworzenia kopii zapasowych dziennika SQL

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup" and JobOperationSubType=="Log"
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="SQLDataBase" and BackupManagementType=="AzureWorkload"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- Wszystkie pomyślne zadania agenta usługi Azure Backup

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="FileFolder" and BackupManagementType=="MAB"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- Magazyn kopii zapasowej zużywany na element kopii zapasowej

    ````Kusto
    CoreAzureBackup
    //Get all Backup Items
    | where OperationName == "BackupItem"
    //Get distinct Backup Items
    | distinct BackupItemUniqueId, BackupItemFriendlyName
    | join kind=leftouter
    (AddonAzureBackupStorage
    | where OperationName == "StorageAssociation"
    //Get latest record for each Backup Item
    | summarize arg_max(TimeGenerated, *) by BackupItemUniqueId 
    | project BackupItemUniqueId , StorageConsumedInMBs)
    on BackupItemUniqueId
    | project BackupItemUniqueId , BackupItemFriendlyName , StorageConsumedInMBs 
    | sort by StorageConsumedInMBs desc
    ````


### <a name="diagnostic-data-update-frequency"></a>Częstotliwość aktualizacji danych diagnostycznych

Dane diagnostyczne z przechowalni są pompowane do obszaru roboczego usługi Log Analytics z pewnym opóźnieniem. Każde zdarzenie dociera do obszaru roboczego usługi Log Analytics *20 do 30 minut* po wypchnięciu go ze skarbca usług odzyskiwania. Oto dalsze szczegóły dotyczące opóźnienia:

- We wszystkich rozwiązaniach wbudowane alerty usługi tworzenia kopii zapasowych są wypychane natychmiast po ich utworzeniu. Dlatego zwykle pojawiają się w obszarze roboczym usługi Log Analytics po 20 do 30 minut.
- We wszystkich rozwiązaniach zadania tworzenia kopii zapasowych na żądanie i zadania przywracania są wypychane natychmiast *po ich zakończeniu.*
- W przypadku wszystkich rozwiązań z wyjątkiem kopii zapasowej SQL zaplanowane zadania tworzenia kopii zapasowych są wypychane natychmiast *po ich zakończeniu*.
- W przypadku kopii zapasowej SQL, ponieważ kopie zapasowe dziennika mogą występować co 15 minut, informacje o wszystkich ukończonych zaplanowanych zadaniach tworzenia kopii zapasowych, w tym dziennikach, są wsadowe i wypychane co 6 godzin.
- We wszystkich rozwiązaniach inne informacje, takie jak element kopii zapasowej, zasady, punkty odzyskiwania, magazyn i tak dalej, są wypychane co najmniej *raz dziennie.*
- Zmiana konfiguracji kopii zapasowej (na przykład zmiana zasad lub zasady edycji) wyzwala wypychanie wszystkich powiązanych informacji o kopii zapasowej.

## <a name="using-the-recovery-services-vaults-activity-logs"></a>Korzystanie z dzienników aktywności magazynu usług odzyskiwania

> [!CAUTION]
> Poniższe kroki dotyczą tylko *kopii zapasowych maszyn wirtualnych platformy Azure.* Nie można użyć tych kroków dla rozwiązań, takich jak agent usługi Azure Backup, kopie zapasowe SQL na platformie Azure lub usługi Azure Files.

Można również użyć dzienników aktywności, aby otrzymywać powiadomienia o zdarzeniach, takich jak sukces tworzenia kopii zapasowej. Aby rozpocząć, wykonaj następujące kroki:

1. Zaloguj się do witryny Azure Portal.
1. Otwórz odpowiedni magazyn usług odzyskiwania.
1. We właściwościach przechowalni otwórz sekcję **Dziennik aktywności.**

Aby zidentyfikować odpowiedni dziennik i utworzyć alert:

1. Sprawdź, czy otrzymujesz dzienniki aktywności dla pomyślnych kopii zapasowych, stosując filtry pokazane na poniższej ilustracji. Zmień wartość **Timespan** zgodnie z koniecznością wyświetlania rekordów.

   ![Filtrowanie w celu znalezienia dzienników aktywności dla kopii zapasowych maszyn wirtualnych platformy Azure](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

1. Wybierz nazwę operacji, aby wyświetlić odpowiednie szczegóły.
1. Wybierz **pozycję Nowa reguła alertu,** aby otworzyć stronę **Utwórz regułę.**
1. Utwórz alert, wykonując kroki opisane w [obszarze Tworzenie, wyświetlanie i zarządzanie alertami dziennika aktywności przy użyciu usługi Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log).

   ![Nowa reguła alertu](media/backup-azure-monitoring-laworkspace/new-alert-rule.png)

W tym miejscu zasób jest sam magazyn usług odzyskiwania. Powtórz te same kroki dla wszystkich magazynów, w których chcesz otrzymywać powiadomienia za pośrednictwem dzienników aktywności. Warunek nie będzie miał progu, okresu lub częstotliwości, ponieważ ten alert jest oparty na zdarzeniach. Jak tylko zostanie wygenerowany odpowiedni dziennik aktywności, alert jest wywoływany.

## <a name="using-log-analytics-to-monitor-at-scale"></a>Używanie usługi Log Analytics do monitorowania na dużą skalę

Można wyświetlić wszystkie alerty utworzone na podstawie dzienników aktywności i obszarów roboczych usługi Log Analytics w usłudze Azure Monitor. Wystarczy otworzyć okienko **Alerty** po lewej stronie.

Mimo że można otrzymywać powiadomienia za pośrednictwem dzienników aktywności, zdecydowanie zalecamy używanie usługi Log Analytics zamiast dzienników aktywności do monitorowania na dużą skalę. Oto dlaczego:

- **Ograniczone scenariusze:** Powiadomienia za pośrednictwem dzienników aktywności mają zastosowanie tylko do kopii zapasowych maszyn wirtualnych platformy Azure. Powiadomienia muszą być skonfigurowane dla każdego magazynu usług odzyskiwania.
- **Dopasowanie definicji:** zaplanowane działanie kopii zapasowej nie pasuje do najnowszej definicji dzienników aktywności. Zamiast tego jest wyrównywać z [dziennikami zasobów](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#what-you-can-do-with-platform-logs-in-a-workspace). To wyrównanie powoduje nieoczekiwane efekty, gdy dane przepływa przez kanał dziennika aktywności zmienia.
- **Problemy z kanałem dziennika aktywności:** W magazynach usług odzyskiwania dzienniki aktywności, które są pompowane z usługi Azure Backup, są zgodne z nowym modelem. Niestety ta zmiana wpływa na generowanie dzienników aktywności w usłudze Azure Government, Azure Germany i Azure China 21Vianet. Jeśli użytkownicy tych usług w chmurze utworzyć lub skonfigurować alerty z dzienników aktywności w usłudze Azure Monitor, alerty nie są wyzwalane. Ponadto we wszystkich regionach publicznych platformy Azure, jeśli użytkownik [zbiera działanie usługi odzyskiwania loguje się do obszaru roboczego usługi Log Analytics,](https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs)te dzienniki nie są wyświetlane.

Użyj obszaru roboczego usługi Log Analytics do monitorowania i ostrzegania na dużą skalę dla wszystkich obciążeń, które są chronione przez usługę Azure Backup.

## <a name="next-steps"></a>Następne kroki

Aby utworzyć kwerendy niestandardowe, zobacz [Model danych usługi Log Analytics](backup-azure-reports-data-model.md).
