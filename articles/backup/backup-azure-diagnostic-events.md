---
title: Korzystanie z ustawień diagnostyki dla magazynów usług odzyskiwania
description: Artykuł opisujący sposób używania starych i nowych zdarzeń diagnostycznych dla usługi Azure Backup
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: d10bedf3818559971eff12624152d0e797f6c3cc
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80672782"
---
# <a name="using-diagnostics-settings-for-recovery-services-vaults"></a>Używanie ustawień diagnostyki dla magazynów usługi Recovery Services

Usługa Azure Backup wysyła zdarzenia diagnostyczne, które mogą być zbierane i używane do celów analizy, alertów i raportowania. 

Ustawienia diagnostyki magazynu usług odzyskiwania można skonfigurować za pośrednictwem witryny Azure portal, przechodząc do przechowalni i klikając element menu **Ustawienia diagnostyczne.** Kliknięcie **przycisku + Dodaj ustawienie diagnostyczne** umożliwia wysłanie co najmniej jednego zdarzenia diagnostycznego do konta magazynu, centrum zdarzeń lub obszaru roboczego analizy dzienników (LA).

![Blok ustawień diagnostyki](./media/backup-azure-diagnostics-events/diagnostics-settings-blade.png)

## <a name="diagnostics-events-available-for-azure-backup-users"></a>Zdarzenia diagnostyczne dostępne dla użytkowników kopii zapasowej platformy Azure

Usługa Azure Backup udostępnia następujące zdarzenia diagnostyczne, z których każdy zawiera szczegółowe dane dotyczące określonego zestawu artefaktów związanych z tworzeniem kopii zapasowych:

* CoreAzureBackup (właśc.
* AddonAzureBackupAlerts
* AddonAzureBackupProtectedInstance
* AddonAzureBackupJobs
* AddonAzureBackupPolityka
* AddonAzureBackupStorage

[Model danych dla zdarzeń diagnostyki kopii zapasowych platformy Azure](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)

Dane dla tych zdarzeń mogą być wysyłane do konta magazynu, obszaru roboczego LA lub Centrum zdarzeń. Jeśli wysyłasz te dane do obszaru roboczego LA, musisz wybrać przełącznik **Specyficzne dla zasobu** na ekranie **Ustawienia diagnostyki** (zobacz więcej informacji w sekcjach poniżej).

## <a name="using-diagnostics-settings-with-log-analytics-la"></a>Korzystanie z ustawień diagnostyki z analizą dzienników (LA)

Dostosowując się do mapy drogowej usługi Azure Log Analytics, usługa Azure Backup umożliwia teraz wysyłanie danych diagnostycznych magazynu do dedykowanych tabel LA dla kopii zapasowej. Są one określane jako [tabele specyficzne dla zasobów](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#resource-specific).

Aby wysłać dane diagnostyczne przechowalni do la:

1.    Przejdź do skarbca i kliknij **ustawienia diagnostyczne**. Kliknij **przycisk + Dodaj ustawienie diagnostyczne**.
2.    Nadaj nazwę ustawieniu Diagnostyka.
3.    Zaznacz pole **Wyślij do usługi Log Analytics** i wybierz obszar roboczy usługi Log Analytics.
4.    Wybierz opcję **Specyficzne dla zasobu** w przełączniku i sprawdź następujące sześć zdarzeń - **CoreAzureBackup**, **AddonAzureBackupAlerts**, **AddonAzureBackupProtecInstance**, **AddonAzureBackupJobs**, **AddonAzureBackupPolicy**i **AddonAzureBackupStorage**.
5.    Kliknij **zapisz**.

![Tryb specyficzny dla zasobu](./media/backup-azure-diagnostics-events/resource-specific-blade.png)

Gdy dane przepływają do obszaru roboczego LA, dedykowane tabele dla każdego z tych zdarzeń są tworzone w obszarze roboczym. Można zbadać dowolną z tych tabel bezpośrednio, a także wykonać sprzężenia lub związki między tymi tabelami, jeśli to konieczne.

> [!IMPORTANT]
> Powyższe sześć zdarzeń, a mianowicie CoreAzureBackup, AddonAzureBackupAlerts, AddonAzureBackupProtectedInstance, AddonAzureBackupJobs, AddonAzureBackupPolicy i AddonAzureBackupStorage, są obsługiwane **tylko** w trybie specyficznym dla zasobu w [raportach kopii zapasowych](https://docs.microsoft.com/azure/backup/configure-reports). **Należy pamiętać, że jeśli spróbujesz wysłać dane dla tych sześciu zdarzeń w trybie diagnostyki platformy Azure, żadne dane nie będą widoczne w raportach kopii zapasowej.**

## <a name="legacy-event"></a>Starsze zdarzenie

Tradycyjnie wszystkie dane diagnostyczne związane z tworzeniem kopii zapasowych dla magazynu zostały zawarte w jednym zdarzeniu o nazwie "AzureBackupReport". Sześć zdarzeń opisanych powyżej są w istocie rozkładu wszystkich danych zawartych w usłudze AzureBackupReport. 

Obecnie nadal obsługuje zdarzenie AzureBackupReport dla zgodności z powrotem, w przypadkach, gdy użytkownicy mają istniejące zapytania niestandardowe w tym zdarzeniu, na przykład alerty dziennika niestandardowego, wizualizacje niestandardowe itp. Jednak **zaleca się przejście do [nowych zdarzeń](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events#diagnostics-events-available-for-azure-backup-users) tak wcześnie, jak to możliwe,** ponieważ dzięki temu dane znacznie łatwiejsze do pracy z w zapytaniach dziennika, zapewnia lepszą wykrywalność schematów i ich struktury, zwiększa wydajność zarówno w czasie opóźnienia pozyskiwania i zapytań. 

**Starsze zdarzenie w trybie diagnostyki platformy Azure zostanie ostatecznie przestarzałe, a zatem wybranie nowych zdarzeń może pomóc uniknąć złożonych migracji w późniejszym terminie.** Nasze [rozwiązanie raportowania,](https://docs.microsoft.com/azure/backup/configure-reports) które wykorzystuje usługi Log Analytics, również przestanie obsługiwać dane ze starszego zdarzenia.

### <a name="steps-to-move-to-new-diagnostics-settings-to-log-analytics-workspace"></a>Kroki, aby przejść do nowych ustawień diagnostycznych (do obszaru roboczego usługi Log Analytics)

1. Określ, które magazyny wysyłają dane do obszaru roboczego usługi Log Analytics przy użyciu starszego zdarzenia i subskrypcji, do których należą. Uruchom poniższe obszary robocze, aby zidentyfikować te magazyny i subskrypcje:

    ````Kusto
    let RangeStart = startofday(ago(3d));
    let VaultUnderAzureDiagnostics = (){
        AzureDiagnostics
        | where TimeGenerated >= RangeStart | where Category == "AzureBackupReport" and OperationName == "Vault" and SchemaVersion_s == "V2"
        | summarize arg_max(TimeGenerated, *) by ResourceId    
        | project ResourceId, Category};
    let VaultUnderResourceSpecific = (){
        CoreAzureBackup
        | where TimeGenerated >= RangeStart | where OperationName == "Vault" 
        | summarize arg_max(TimeGenerated, *) by ResourceId
        | project ResourceId, Category};
        // Some Workspaces will not have AzureDiagnostics Table, hence you need to use isFuzzy
    let CombinedVaultTable = (){
        CombinedTable | union isfuzzy = true 
        (VaultUnderAzureDiagnostics() ),
        (VaultUnderResourceSpecific() )
        | distinct ResourceId, Category};
    CombinedVaultTable | where Category == "AzureBackupReport"
    | join kind = leftanti ( 
    CombinedVaultTable | where Category == "CoreAzureBackup"
    ) on ResourceId
    | parse ResourceId with * "SUBSCRIPTIONS/" SubscriptionId:string "/RESOURCEGROUPS" * "MICROSOFT.RECOVERYSERVICES/VAULTS/" VaultName:string
    | project ResourceId, SubscriptionId, VaultName
    ````

2. Użyj [wbudowanej usługi Azure Backup,](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics) aby dodać nowe ustawienie diagnostyki dla wszystkich magazynów w określonym zakresie. Ta zasada dodaje nowe ustawienie diagnostyki do tych magazynów, które albo nie mają ustawienia diagnostyki (lub) mają tylko ustawienie diagnostyki starszej wersji. Te zasady można przypisać do całej subskrypcji lub grupy zasobów w czasie. Należy pamiętać, że będziesz potrzebować dostępu "Właściciel" do każdej subskrypcji, do której jest przypisana zasada.

Możesz wybrać oddzielne ustawienia diagnostyki dla usługi AzureBackupReport i sześć nowych zdarzeń, dopóki nie zostaną zmigrowane wszystkie zapytania niestandardowe, aby użyć danych z nowych tabel. Poniższy obraz przedstawia przykład przechowalni z dwoma ustawieniami diagnostycznymi. Pierwsze ustawienie o nazwie **Setting1** wysyła dane zdarzenia AzureBackupReport do obszaru roboczego LA w trybie AzureDiagnostics. Drugie ustawienie o nazwie **Setting2** wysyła dane sześciu nowych zdarzeń usługi Azure Backup do obszaru roboczego LA w trybie specyficznym dla zasobu.

![Dwa ustawienia](./media/backup-azure-diagnostics-events/two-settings-example.png)

> [!IMPORTANT]
> Zdarzenie AzureBackupReport jest obsługiwane **tylko** w trybie diagnostyki platformy Azure. **Należy pamiętać, że jeśli spróbujesz wysłać dane dla tego zdarzenia w trybie specyficznym dla zasobu, żadne dane nie będą przepływać do obszaru roboczego LA.**

> [!NOTE]
> Przełącznik dla diagnostyki platformy Azure / specyficzne dla zasobu pojawia się tylko wtedy, gdy użytkownik sprawdza **Wyślij do usługi Log Analytics**. Aby wysłać dane do konta magazynu lub Centrum zdarzeń, użytkownik może po prostu wybrać wymagane miejsce docelowe i sprawdzić dowolne żądane zdarzenia, bez żadnych dodatkowych danych wejściowych. Ponownie zaleca się, aby nie wybierać starszego zdarzenia AzureBackupReport, w przyszłości.

## <a name="users-sending-azure-site-recovery-events-to-log-analytics-la"></a>Użytkownicy wysyłający zdarzenia odzyskiwania witryny platformy Azure do usługi Log Analytics (LA)

Zdarzenia odzyskiwania usługi Azure Backup i usługi Azure Site Recovery są wysyłane z tej samej przechowalni usług odzyskiwania. Ponieważ usługa Azure Site Recovery nie jest obecnie dołączana do tabel specyficznych dla zasobów, użytkownicy, którzy chcą wysyłać zdarzenia odzyskiwania witryny platformy Azure do la, są kierowani do korzystania **tylko** z trybu diagnostyki platformy Azure (zobacz obrazek poniżej). **Wybranie trybu specyficznego dla zasobu dla zdarzeń odzyskiwania witryny platformy Azure uniemożliwi przesyłanie wymaganych danych do obszaru roboczego LA**.

![Zdarzenia odzyskiwania witryny](./media/backup-azure-diagnostics-events/site-recovery-settings.png)

Podsumowując powyższe niuanse:

* Jeśli masz już diagnostykę LA skonfigurowane z diagnostyki platformy Azure i pisemne zapytania niestandardowe na nim, zachować to ustawienie **nienaruszone,** dopóki nie migracji zapytań do korzystania z danych z nowych zdarzeń.
* Jeśli chcesz również dołączać do nowych tabel (zgodnie z zaleceniami), utwórz **nowe** ustawienie diagnostyki, wybierz **opcję Specyficzne dla zasobu** i wybierz sześć nowych zdarzeń, jak określono powyżej.
* Jeśli obecnie wysyłasz zdarzenia odzyskiwania witryny platformy Azure do la, **nie** należy wybierać trybu specyficzne dla zasobów dla tych zdarzeń, w przeciwnym razie dane dla tych zdarzeń nie będą przepływać do obszaru roboczego LA. Zamiast tego utwórz **dodatkowe ustawienie diagnostyczne**, wybierz **diagnostykę platformy Azure**i wybierz odpowiednie zdarzenia usługi Azure Site Recovery.

Poniższy obraz przedstawia przykład użytkownika, który ma trzy ustawienia diagnostyczne dla przechowalni. Pierwsze ustawienie o nazwie **Setting1** wysyła dane ze zdarzenia AzureBackupReport do obszaru roboczego LA w trybie AzureDiagnostics. Drugie ustawienie o nazwie **Setting2** wysyła dane z sześciu nowych zdarzeń usługi Azure Backup do obszaru roboczego LA w trybie specyficznym dla zasobu. Trzecie ustawienie o nazwie **Setting3**wysyła dane ze zdarzeń usługi Azure Site Recovery do obszaru roboczego LA w trybie diagnostyki platformy Azure.

![Trzy ustawienia](./media/backup-azure-diagnostics-events/three-settings-example.png)

## <a name="next-steps"></a>Następne kroki

[Poznaj model danych analizy dzienników dla zdarzeń diagnostycznych](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)
