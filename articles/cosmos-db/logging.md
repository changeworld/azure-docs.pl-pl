---
title: Rejestrowanie diagnostyczne w usłudze Azure Cosmos DB
description: Dowiedz się więcej o różnych sposobach rejestrowania i monitorowania — dane przechowywane w usłudze Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: e43bc4b8eb1db91493f279f5c46681483e4b18c4
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71261395"
---
# <a name="diagnostic-logging-in-azure-cosmos-db"></a>Rejestrowanie diagnostyczne w usłudze Azure Cosmos DB 

Po rozpoczęciu korzystania z co najmniej jednej bazy danych usługi Azure Cosmos można monitorować sposób i czas uzyskiwania dostępu do baz danych. Ten artykuł zawiera omówienie dzienników, które są dostępne na platformie Azure. Dowiesz się, jak włączyć rejestrowanie diagnostyczne na potrzeby monitorowania w celu wysyłania dzienników do [usługi Azure Storage](https://azure.microsoft.com/services/storage/), przesyłania strumieniowego dzienników do [usługi Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)i eksportowania dzienników do [dzienników Azure monitor](https://azure.microsoft.com/services/log-analytics/).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="logs-available-in-azure"></a>Dzienniki dostępnych na platformie Azure

Zanim będziemy wyjaśniać, jak monitorować konto usługi Azure Cosmos DB, możemy wyjaśnić kilka kwestii dotyczących rejestrowania i monitorowania. Istnieją różne typy dzienników na platformie Azure. Istnieją [dzienników aktywności platformy Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs), [dzienniki diagnostyczne platformy Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs), [metryki platformy Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics), zdarzenia, pulsu, dzienniki operacji i tak dalej. Istnieje mnóstwo dzienniki. Pełną listę dzienników można znaleźć w [dziennikach Azure monitor](https://azure.microsoft.com/services/log-analytics/) w Azure Portal. 

Na poniższej ilustracji przedstawiono różne dostępne rodzaje dzienników platformy Azure:

![Różne rodzaje dzienniki platformy Azure](./media/logging/azurelogging.png)

Na ilustracji **zasoby obliczeniowe** reprezentują zasoby platformy Azure, w których mogą uzyskiwać dostęp systemu operacyjnego gościa firmy Microsoft. Na przykład maszyny wirtualne platformy Azure, maszyny wirtualnej zestawy skalowania, Azure Container Service i itd., czy zasoby obliczeniowe uważane. Obliczeniowe zasobów generować Dzienniki aktywności, dzienniki diagnostyczne i dzienniki aplikacji. Aby dowiedzieć się więcej, zapoznaj się [źródła danych na platformie Azure monitorowania](../azure-monitor/platform/data-sources.md) artykułu.

**Zasobów obliczeniowych bez** zasobów, w których nie dostęp do podstawowego systemu operacyjnego i pracy bezpośrednio z zasobem. Na przykład sieciowe grupy zabezpieczeń, Logic Apps i tak dalej. Usługa Azure Cosmos DB jest zasobem poza obliczeniowymi. Możesz wyświetlić dzienniki za zasoby obliczeniowe bez w dzienniku aktywności lub Włącz opcję dzienników diagnostycznych, w portalu. Aby dowiedzieć się więcej, zapoznaj się [źródeł danych w usłudze Azure Monitor](../azure-monitor/platform/data-sources.md) artykułu.

Dziennik aktywności rejestruje operacje na poziomie subskrypcji dla usługi Azure Cosmos DB. Operacje, takie jak klucze list, DatabaseAccounts zapisu i inne są rejestrowane. Dzienniki diagnostyczne zapewniają bardziej szczegółowego rejestrowania i pozwalają na DataPlaneRequests (tworzenia, odczytu, zapytania i tak dalej) i MongoRequests dziennika.


W tym artykule skupimy się na dziennika aktywności platformy Azure, dzienniki diagnostyczne platformy Azure i metryki platformy Azure. Jaka jest różnica między te trzy dzienniki? 

### <a name="azure-activity-log"></a>Dziennik aktywności platformy Azure

Dziennik aktywności platformy Azure jest Dziennik subskrypcji, który zapewnia wgląd w zdarzenia na poziomie subskrypcji, które miały miejsce w systemie Azure. Dziennik aktywności raporty zdarzeń płaszczyznę kontroli dla subskrypcji w obszarze kategorii administracyjnej. Dziennika aktywności można użyć, aby określić ", co, kto i kiedy" dla wszelkie zapisu operacji (PUT, POST, DELETE) dla zasobów w ramach subskrypcji. Dodatkowo użytkownik rozumie stanu operacji i inne odpowiednie właściwości. 

Dziennik aktywności różni się od dzienników diagnostycznych. Dziennik aktywności zawiera dane dotyczące operacji dla zasobu z zewnątrz ( _płaszczyznę kontroli_). W kontekście usługi Azure Cosmos DB płaszczyznę kontroli, które operacje obejmują tworzenie kontenera, klucze listy, usuwanie kluczy, bazy danych z listy i tak dalej. Dzienniki diagnostyczne są emitowane przez zasób i podaj informacje o działaniu tego zasobu ( _płaszczyzny danych_). Niektóre przykłady operacje płaszczyzny danych w dzienniku diagnostyczne są Delete, Insert i ReadFeed.

Dzienniki aktywności (operacje warstwy kontroli) może być bardziej rozbudowane z natury i mogą obejmować pełny adres e-mail obiektu wywołującego, adres IP obiektu wywołującego, nazwa zasobu, nazwy operacji, identyfikator dzierżawy i więcej. Dziennik aktywności zawiera kilka [kategorie](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-activity-log-schema) danych. Aby uzyskać szczegółowe informacje o wypełniana z tych kategorii, zobacz [schemat zdarzeń dziennika aktywności platformy Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-activity-log-schema). Jednak dzienniki diagnostyczne mogą być ograniczające z natury danych osobowych jest często usunięte z tych dzienników. Może mieć adres IP obiektu wywołującego, ale ostatni octant zostanie usunięty.

### <a name="azure-metrics"></a>Metryki platformy Azure

[Metryki platformy Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) mają typ najważniejszych danych telemetrycznych platformy Azure (nazywanych również _liczniki wydajności_) emitowanej zasobów najbardziej platformy Azure. Metryki umożliwiają wyświetlanie informacji o przepływności, magazynu, spójnością, dostępnością i opóźnieniem równym zasobami usługi Azure Cosmos DB. Aby uzyskać więcej informacji, zobacz [monitorowania i debugowania przy użyciu metryk w usłudze Azure Cosmos DB](use-metrics.md).

### <a name="azure-diagnostic-logs"></a>Dzienniki diagnostyczne platformy Azure

Dzienniki diagnostyczne platformy Azure są emitowane przez zasób i zapewniają bogate, często dane o działaniu tego zasobu. Te dzienniki są przechwytywane dla każdego żądania. Zawartość tych dzienników jest zależna od typu zasobu. Zasób poziom dzienniki diagnostyczne różnią się także z poziomu systemu operacyjnego gościa, dzienniki diagnostyczne. System operacyjny gościa, dzienniki diagnostyczne są zbierane przez agenta, który działa wewnątrz maszyny wirtualnej lub inne obsługiwane typu zasobu. Dzienniki diagnostyczne na poziomie zasobów wymagają nie agenta i przechwytywania danych specyficznych dla zasobów z samą platformę Azure. Dzienniki diagnostyczne na poziomie systemu operacyjnego gościa przechwytywać dane z systemu operacyjnego i aplikacji, które są uruchomione na maszynie wirtualnej.

![Rejestrowanie diagnostyczne do magazynu, Event Hubs lub dzienników Azure Monitor](./media/logging/azure-cosmos-db-logging-overview.png)

### <a name="what-is-logged-by-azure-diagnostic-logs"></a>Co to jest rejestrowany przez dzienniki diagnostyczne platformy Azure?

* Wszystkie żądania uwierzytelnionych w wewnętrznej bazie danych (TCP/REST) wszystkich interfejsów API, są rejestrowane, w tym żądań zakończonych niepowodzeniem w wyniku uprawnienia dostępu, błędów systemowych lub żądaniach. Pomoc techniczna dla żądania programu Graph, bazy danych Cassandra i interfejsu API tabel zainicjowanego przez użytkownika nie są obecnie dostępne.
* Operacje w bazie danych, które obejmują operacje CRUD na wszystkie dokumenty, kontenery i baz danych.
* Operacje na kluczach konta, które obejmują tworzenie, modyfikowanie lub usuwanie kluczy.
* Nieuwierzytelnione żądania, które powodują uzyskanie odpowiedzi 401. Na przykład żądania, które nie mają tokenu elementu nośnego, są nieprawidłowo sformułowane, wygasły lub mają nieprawidłowy token.

<a id="#turn-on"></a>
## <a name="turn-on-logging-in-the-azure-portal"></a>Włącz rejestrowanie w witrynie Azure portal

Wykonaj następujące kroki, aby włączyć rejestrowanie diagnostyczne w Azure Portal:

1. Zaloguj się do [Azure Portal](https://portal.azure.com). 

1. Przejdź do swojego konta usługi Azure Cosmos. Otwórz okienko **Ustawienia diagnostyczne** , a następnie wybierz opcję **Dodaj ustawienie diagnostyczne** .

    ![Włączanie rejestrowania diagnostycznego usługi Azure Cosmos DB w witrynie Azure portal](./media/logging/turn-on-portal-logging.png)

1. Na stronie **Ustawienia diagnostyczne** Wypełnij formularz następującymi szczegółami: 

    * **Nazwa**: Wprowadź nazwę dla dzienników w celu tworzenia.

    * Dzienniki można przechowywać w następujących usługach:

      * **Archiwizuj na koncie magazynu**: Aby użyć tej opcji, należy istniejące konto magazynu, aby nawiązać połączenie. Aby utworzyć nowe konto magazynu w portalu, zobacz artykuł [Tworzenie konta magazynu](../storage/common/storage-create-storage-account.md) . Następnie wróć do okienka ustawień diagnostycznych usługi Azure Cosmos DB w portalu, aby wybrać konto magazynu. Może upłynąć kilka minut, zanim konta nowo utworzonego magazynu pojawią się w menu rozwijanym.

      * **Przesyłaj strumieniowo do centrum zdarzeń**: Aby można było użyć tej opcji, musisz mieć istniejącą przestrzeń nazw Event Hubs i centrum zdarzeń, aby nawiązać połączenie. Aby utworzyć obszar nazw usługi Event Hubs, zobacz [tworzenie przestrzeni nazw usługi Event Hubs i Centrum zdarzeń przy użyciu witryny Azure portal](../event-hubs/event-hubs-create.md). Następnie wróć do tej strony w portalu, aby wybrać przestrzeń nazw centrum zdarzeń i nazwę zasad.

      * **Wyślij do log Analytics**: Aby użyć tej opcji, należy użyć istniejącego obszaru roboczego lub utworzyć nowy obszar roboczy Log Analytics, wykonując czynności opisane w sekcji [Tworzenie nowego obszaru roboczego](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace) w portalu. 

   * Można rejestrować następujące dane:

      * **DataPlaneRequests**: Wybierz tę opcję, aby rejestrować żądania wewnętrznej bazy danych do wszystkich interfejsów API, w tym kont SQL, grafów, MongoDB, Cassandra i interfejs API tabel w Azure Cosmos DB. Jeśli masz archiwizacji na koncie magazynu można wybrać okres przechowywania dla dzienników diagnostycznych. Dzienniki są usuwane automatycznie po upływie okresu przechowywania. Następujące dane JSON są przykładowymi danymi wyjściowymi informacji zarejestrowanych przy użyciu DataPlaneRequests. Właściwości klucza do uwagi: Requestcharge, statusCode, clientIPaddress i partitionID:

       ```
       { "time": "2019-04-23T23:12:52.3814846Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "DataPlaneRequests", "operationName": "ReadFeed", "properties": {"activityId": "66a0c647-af38-4b8d-a92a-c48a805d6460","requestResourceType": "Database","requestResourceId": "","collectionRid": "","statusCode": "200","duration": "0","userAgent": "Microsoft.Azure.Documents.Common/2.2.0.0","clientIpAddress": "10.0.0.24","requestCharge": "1.000000","requestLength": "0","responseLength": "372","resourceTokenUserRid": "","region": "East US","partitionId": "062abe3e-de63-4aa5-b9de-4a77119c59f8","keyType": "PrimaryReadOnlyMasterKey","databaseName": "","collectionName": ""}}
       ```

      * **MongoRequests**: Wybierz tę opcję, aby rejestrować żądania inicjowane przez użytkownika z frontonu, aby obsłużyć żądania do interfejsu API Azure Cosmos DB MongoDB. Żądania MongoDB będą wyświetlane w MongoRequests i DataPlaneRequests. Jeśli masz archiwizacji na koncie magazynu można wybrać okres przechowywania dla dzienników diagnostycznych. Dzienniki są usuwane automatycznie po upływie okresu przechowywania. Następujące dane JSON są przykładowymi danymi wyjściowymi informacji zarejestrowanych przy użyciu MongoRequests. Właściwości klucza do uwagi: Requestcharge, opCode:

       ```
       { "time": "2019-04-10T15:10:46.7820998Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "MongoRequests", "operationName": "ping", "properties": {"activityId": "823cae64-0000-0000-0000-000000000000","opCode": "MongoOpCode_OP_QUERY","errorCode": "0","duration": "0","requestCharge": "0.000000","databaseName": "admin","collectionName": "$cmd","retryCount": "0"}}
       ```

      * **QueryRuntimeStatistics**: Wybierz tę opcję, aby zarejestrować tekst zapytania, który został wykonany.  Następujące dane JSON są przykładowymi danymi wyjściowymi informacji zarejestrowanych przy użyciu QueryRuntimeStatistics:

       ```
       { "time": "2019-04-14T19:08:11.6353239Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "QueryRuntimeStatistics", "properties": {"activityId": "278b0661-7452-4df3-b992-8aa0864142cf","databasename": "Tasks","collectionname": "Items","partitionkeyrangeid": "0","querytext": "{"query":"SELECT *\nFROM c\nWHERE (c.p1__10 != true)","parameters":[]}"}}
       ```

      * **Żądania metryk**: Wybierz tę opcję, aby przechowywać pełne dane w [metrykach platformy Azure](../azure-monitor/platform/metrics-supported.md). Jeśli masz archiwizacji na koncie magazynu można wybrać okres przechowywania dla dzienników diagnostycznych. Dzienniki są usuwane automatycznie po upływie okresu przechowywania.

3. Wybierz pozycję **Zapisz**.

    Jeśli otrzymasz komunikat o błędzie, który jest wyświetlany komunikat "nie można zaktualizować diagnostykę dla \<nazwa obszaru roboczego >. Subskrypcja \<identyfikator subskrypcji > nie jest zarejestrowana do używania microsoft.insights, "wykonaj [Rozwiązywanie problemów z usługi Azure Diagnostics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) instrukcjami, aby zarejestrować konto, a następnie ponów próbę wykonania tej procedury.

    Jeśli chcesz zmienić, jak dzienników diagnostycznych są zapisywane w dowolnym momencie w przyszłości, wróć do tej strony, aby zmodyfikować ustawienia dziennika diagnostycznego konta.

## <a name="turn-on-logging-by-using-azure-cli"></a>Włącz rejestrowanie przy użyciu wiersza polecenia platformy Azure

Aby włączyć rejestrowanie diagnostyczne i metryki za pomocą wiersza polecenia platformy Azure, użyj następujących poleceń:

- Aby włączyć magazyn dzienników diagnostycznych w ramach konta magazynu, użyj tego polecenia:

   ```azurecli-interactive
   az monitor diagnostic-settings create --name DiagStorage --resource <resourceId> --storage-account <storageAccountName> --logs '[{"category": "QueryRuntimeStatistics", "enabled": true, "retentionPolicy": {"enabled": true, "days": 0}}]'
   ```

   `resource` Jest nazwą konta usługi Azure Cosmos DB. Zasób ma format "/subscriptions/`<subscriptionId>`/resourceGroups/`<resource_group_name>`/Providers/Microsoft.DocumentDB/databaseAccounts/ `storage-account` < Azure_Cosmos_account_name >" to jest nazwa konta magazynu, do którego chcesz wysłać dzienniki. Można rejestrować inne dzienniki, aktualizując wartości parametrów kategorii do "MongoRequests" lub "DataPlaneRequests". 

- Aby włączyć strumieniowe przesyłanie dzienników diagnostycznych do Centrum zdarzeń, użyj tego polecenia:

   ```azurecli-interactive
   az monitor diagnostic-settings create --name cdbdiagsett --resourceId <resourceId> --event-hub-rule <eventHubRuleID> --logs '[{"category":"QueryRuntimeStatistics","enabled":true,"retentionPolicy":{"days":6,"enabled":true}}]'
   ```

   `resource` Jest nazwą konta usługi Azure Cosmos DB. `event-hub-rule` Jest to identyfikator reguły centrum zdarzeń. 

- Aby włączyć wysyłanie dzienników diagnostycznych do obszaru roboczego usługi Log Analytics, użyj tego polecenia:

   ```azurecli-interactive
   az monitor diagnostic-settings create --name cdbdiagsett --resourceId <resourceId> --workspace <resource id of the log analytics workspace> --logs '[{"category":"QueryRuntimeStatistics","enabled":true,"retentionPolicy":{"days":6,"enabled":true}}]'
   ```

Można połączyć te parametry, aby włączyć wiele opcji danych wyjściowych.

## <a name="turn-on-logging-by-using-powershell"></a>Włącz rejestrowanie przy użyciu programu PowerShell

Aby włączyć rejestrowanie diagnostyczne przy użyciu programu PowerShell, musisz mieć programu Azure Powershell przy użyciu minimalnej wersji 1.0.1.

Aby zainstalować program Azure PowerShell i skojarzyć go z subskrypcją platformy Azure, zobacz [Sposób instalowania i konfigurowania programu Azure PowerShell](/powershell/azure/overview).

Jeśli został już zainstalowany program Azure PowerShell, a nie wiadomo, wersja, z typu konsoli programu PowerShell `(Get-Module azure -ListAvailable).Version`.  

### <a id="connect"></a>Nawiązywanie połączenia z subskrypcjami
Uruchom sesję programu PowerShell Azure i zaloguj się na konto platformy Azure przy użyciu następującego polecenia:  

```powershell
Connect-AzAccount
```

W podręcznym oknie przeglądarki wprowadź nazwę użytkownika i hasło dla konta platformy Azure. Program Azure PowerShell pobierze wszystkie subskrypcje, które są skojarzone z tym kontem i domyślnie użyje pierwszej.

Jeśli masz więcej niż jedną subskrypcję, może być do określenia konkretnej subskrypcji, który został użyty do utworzenia magazynu kluczy Azure. Aby zobaczyć subskrypcje dla swojego konta, wpisz następujące polecenie:

```powershell
Get-AzSubscription
```

Następnie aby określić subskrypcję, która jest skojarzona z konta usługi Azure Cosmos DB, która jest rejestruje, wpisz następujące polecenie:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Jeśli masz więcej niż jedną subskrypcję, która jest skojarzona z kontem, jest ważne, aby określić subskrypcję, dla której chcesz użyć.
>
>

Aby uzyskać więcej informacji o sposobie konfigurowania programu Azure PowerShell, zobacz [jak zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/overview).

### <a id="storage"></a>Tworzenie nowego konta magazynu dla dzienników
Chociaż można używać istniejącego konta magazynu dla dzienników, w tym samouczku, możemy utworzyć nowe konto magazynu, przeznaczonego dla dzienników usługi Azure Cosmos DB. Dla wygody, są przechowywane szczegóły konta magazynu w zmiennej o nazwie **sa**.

Aby uzyskać dodatkową łatwość zarządzania, w tym samouczku używamy tej samej grupy zasobów, która zawiera bazę danych usługi Azure Cosmos. Podstaw wartości **ContosoResourceGroup**, **contosocosmosdblogs**, i **północno-środkowe stany USA** parametrów, zgodnie z wymaganiami:

```powershell
$sa = New-AzStorageAccount -ResourceGroupName ContosoResourceGroup `
-Name contosocosmosdblogs -Type Standard_LRS -Location 'North Central US'
```

> [!NOTE]
> Jeśli zdecydujesz się użyć istniejącego konta magazynu, konto musi używać tej samej subskrypcji co Twoja subskrypcja usługi Azure Cosmos DB. To konto należy również użyć modelu wdrażania usługi Resource Manager, a nie klasycznego modelu wdrażania.
>
>

### <a id="identify"></a>Określ konto usługi Azure Cosmos DB dla dzienników
Ustaw nazwę konta usługi Azure Cosmos DB do zmiennej o nazwie **konta**, gdzie **ResourceName** jest nazwą konta usługi Azure Cosmos DB.

```powershell
$account = Get-AzResource -ResourceGroupName ContosoResourceGroup `
-ResourceName contosocosmosdb -ResourceType "Microsoft.DocumentDb/databaseAccounts"
```

### <a id="enable"></a>Włączanie rejestrowania
Aby włączyć rejestrowanie dla usługi Azure Cosmos DB, należy użyć `Set-AzDiagnosticSetting` polecenia cmdlet, za pomocą zmiennych dla nowego konta magazynu, konto usługi Azure Cosmos DB i kategorię Aby włączyć rejestrowanie. Uruchom następujące polecenie, a następnie ustaw **— włączone** flaga **$true**:

```powershell
Set-AzDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests
```

Dane wyjściowe polecenia powinien przypominać następujący przykład:

```powershell
    StorageAccountId            : /subscriptions/<subscription-ID>/resourceGroups/ContosoResourceGroup/providers`
    /Microsoft.Storage/storageAccounts/contosocosmosdblogs
    ServiceBusRuleId            :
    EventHubAuthorizationRuleId :
    Metrics
        TimeGrain       : PT1M
        Enabled         : False
        RetentionPolicy
        Enabled : False
        Days    : 0
    
    Logs
        Category        : DataPlaneRequests
        Enabled         : True
        RetentionPolicy
        Enabled : False
        Days    : 0
    
    WorkspaceId                 :
    Id                          : /subscriptions/<subscription-ID>/resourcegroups/ContosoResourceGroup/providers`
    /microsoft.documentdb/databaseaccounts/contosocosmosdb/providers/microsoft.insights/diagnosticSettings/service
    Name                        : service
    Type                        :
    Location                    :
    Tags                        :
```

Dane wyjściowe polecenia potwierdza, że włączono rejestrowanie dla bazy danych i informacji jest zapisywany do swojego konta magazynu.

Opcjonalnie można także ustawić zasady przechowywania dla dzienników, tak, aby starsze dzienniki są automatycznie usuwane. Na przykład ustawić zasady przechowywania, przy użyciu **- Retentionenable** flaga jest ustawiona na **$true**. Ustaw **- RetentionInDays** parametr **90** tak, aby dzienniki starsze niż 90 dni są automatycznie usuwane.

```powershell
Set-AzDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests`
  -RetentionEnabled $true -RetentionInDays 90
```

### <a id="access"></a>Uzyskiwanie dostępu do dzienników
Dzienniki Azure Cosmos DB dla kategorii **DataPlaneRequests** są przechowywane w kontenerze **Insights-Logs-DataPlaneRequests** na podanym koncie magazynu. 

Najpierw utwórz zmienną dla nazwy kontenera. Zmienna jest używana w tym przewodniku.

```powershell
    $container = 'insights-logs-dataplanerequests'
```

Aby wyświetlić listę wszystkich obiektów blob w tym kontenerze, wpisz:

```powershell
Get-AzStorageBlob -Container $container -Context $sa.Context
```

Dane wyjściowe polecenia powinien przypominać następujący przykład:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 10510193
ContentType       : application/octet-stream
LastModified      : 9/28/2017 7:49:04 PM +00:00
SnapshotTime      :
ContinuationToken:
Context           : Microsoft.WindowsAzure.Commands.Common.Storage.`
                    LazyAzureStorageContext
Name              : resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS`
/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/CONTOSOCOSMOSDB/y=2017/m=09/d=28/h=19/m=00/PT1H.json
```

Jak widać na tych danych wyjściowych, obiekty BLOB konwencją nazewnictwa: `resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<Database Account Name>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

Wartości daty i godziny używają czasu UTC.

Ponieważ tego samego konta magazynu może służyć do zbierania dzienników dla wielu zasobów, można użyć w pełni kwalifikowanego Identyfikatora zasobu w nazwie obiektu blob do konkretnych obiektów blob, które należy pobrać. Zanim do tego, firma Microsoft omówiony sposób pobierania wszystkich obiektów blob.

Najpierw utwórz folder w celu pobrania obiektów blob. Na przykład:

```powershell
New-Item -Path 'C:\Users\username\ContosoCosmosDBLogs'`
 -ItemType Directory -Force
```

Następnie Uzyskaj listę wszystkich obiektów blob:  

```powershell
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context
```

Prześlij tę listę, za pośrednictwem `Get-AzStorageBlobContent` polecenie, aby pobrać obiekty BLOB w folderze docelowym:

```powershell
$blobs | Get-AzStorageBlobContent `
 -Destination 'C:\Users\username\ContosoCosmosDBLogs'
```

Po uruchomieniu tego drugiego polecenia **/** ogranicznika w nazwach obiektów blob utworzy pełną strukturę folderów w folderze docelowym. Tej struktury folderów służy do pobierania i przechowywania obiektów blob jako plików.

Aby selektywnie pobierać obiekty blob, użyj symboli wieloznacznych. Na przykład:

* Jeśli masz wiele baz danych i chcesz pobrać dzienniki dla tylko jednej bazy danych o nazwie **CONTOSOCOSMOSDB3**, użyj polecenia:

    ```powershell
    Get-AzStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/DATABASEACCOUNTS/CONTOSOCOSMOSDB3
    ```

* Jeśli masz wiele grup zasobów i chcesz pobrać dzienniki dla tylko jednej grupy zasobów, użyj polecenia `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

    ```powershell
    Get-AzStorageBlob -Container $container `
    -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
    ```
* Jeśli chcesz pobrać wszystkie dzienniki na miesiąc lipca 2017 r., użyj polecenia `-Blob '*/year=2017/m=07/*'`:

    ```powershell
    Get-AzStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/year=2017/m=07/*'
    ```

Można również uruchomić następujące polecenia:

* Aby wykonać zapytanie o stan ustawień diagnostycznych dla zasobu bazy danych, użyj polecenia `Get-AzDiagnosticSetting -ResourceId $account.ResourceId`.
* Aby wyłączyć rejestrowanie **DataPlaneRequests** kategorię dla zasobu konta bazy danych, użyj polecenia `Set-AzDiagnosticSetting -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories DataPlaneRequests`.


Obiekty BLOB, które są zwracane w każdym z tych zapytań są przechowywane jako tekst i sformatowane jako obiekt blob JSON, jak pokazano w poniższym kodzie:

```json
{
    "records":
    [
        {
           "time": "Fri, 23 Jun 2017 19:29:50.266 GMT",
           "resourceId": "contosocosmosdb",
           "category": "DataPlaneRequests",
           "operationName": "Query",
           "resourceType": "Database",
           "properties": {"activityId": "05fcf607-6f64-48fe-81a5-f13ac13dd1eb",`
           "userAgent": "documentdb-dotnet-sdk/1.12.0 Host/64-bit MicrosoftWindowsNT/6.2.9200.0 AzureSearchIndexer/1.0.0",`
           "resourceType": "Database","statusCode": "200","documentResourceId": "",`
           "clientIpAddress": "13.92.241.0","requestCharge": "2.260","collectionRid": "",`
           "duration": "9250","requestLength": "72","responseLength": "209", "resourceTokenUserRid": ""}
        }
    ]
}
```

Aby uzyskać informacje dotyczące danych w poszczególnych obiektów blob JSON, zobacz [interpretowanie dzienników usługi Azure Cosmos DB](#interpret).

## <a name="manage-your-logs"></a>Zarządzasz dziennikami

Dzienniki diagnostyczne są udostępniane w ramach Twojego konta do dwóch godzin od momentu zgłaszający operacji usługi Azure Cosmos DB. To Ty zarządzasz dziennikami na swoim koncie magazynu:

* Za pomocą metod kontroli dostępu w usłudze Azure standard zabezpieczenia dzienników i ograniczyć, którzy mogą uzyskiwać do nich dostęp.
* Usuń dzienniki, których nie chcesz już przechowywać na koncie magazynu.
* Okres przechowywania dla żądań płaszczyzny danych, które są archiwizowane na konto magazynu jest skonfigurowane w portalu po **DataPlaneRequests dziennika** wybrane ustawienie. Aby zmienić to ustawienie, zobacz [mogą włączyć rejestrowanie w witrynie Azure portal](#turn-on-logging-in-the-azure-portal).


<a id="#view-in-loganalytics"></a>
## <a name="view-logs-in-azure-monitor-logs"></a>Wyświetlanie dzienników w usłudze Azure Monitor

W przypadku wybrania opcji **Wyślij do log Analytics** po włączeniu rejestrowania diagnostycznego dane diagnostyczne z kontenera są przekazywane do Azure monitor dzienników w ciągu dwóch godzin. Gdy przejdziesz do Azure Monitor dzienników natychmiast po włączeniu rejestrowania, nie zostaną wyświetlone żadne dane. Po prostu Zaczekaj dwie godziny i spróbuj ponownie. 

Przed wyświetleniem dzienników Sprawdź, czy obszar roboczy Log Analytics został uaktualniony do korzystania z nowego języka zapytań Kusto. Aby to sprawdzić, Otwórz [Azure Portal](https://portal.azure.com), wybierz pozycję **log Analytics obszary robocze** po lewej stronie, a następnie wybierz nazwę obszaru roboczego, jak pokazano na następnym obrazie. **Obszaru roboczego usługi Log Analytics** zostanie wyświetlona strona:

![Azure Monitor dzienników w Azure Portal](./media/logging/azure-portal.png)

>[!NOTE]
>Obszary robocze OMS są teraz nazywane obszarami roboczymi usługi Log Analytics.  

Jeśli zobaczysz następujący komunikat na **obszaru roboczego usługi Log Analytics** stronie obszaru roboczego, nie został uaktualniony do korzystania z nowego języka. Aby uzyskać więcej informacji na temat sposobu uaktualniania do nowego języka zapytań, zobacz [uaktualnienia obszaru roboczego usługi Azure Log Analytics na nową funkcją przeszukiwania dzienników](../log-analytics/log-analytics-log-search-upgrade.md). 

![Komunikat dotyczący aktualizacji dzienników Azure Monitor](./media/logging/upgrade-notification.png)

Aby wyświetlić dane diagnostyczne w dziennikach Azure Monitor, Otwórz stronę **przeszukiwanie dzienników** w menu po lewej lub obszarze **zarządzania** strony, jak pokazano na poniższej ilustracji:

![Opcje wyszukiwania dzienników w witrynie Azure portal](./media/logging/log-analytics-open-log-search.png)

Po włączeniu zbierania danych uruchomić następujące przykładowe wyszukiwanie w dziennikach za pomocą nowego języka zapytań, aby wyświetlić dzienniki ostatnich 10 `AzureDiagnostics | take 10`.

![Przykładowe wyszukiwanie w dzienniku 10 najnowszych dzienników](./media/logging/log-analytics-query.png)

<a id="#queries"></a>
### <a name="queries"></a>Zapytania

Poniżej znajdują się dodatkowe zapytania, które można wprowadzić w polu **wyszukiwania w dzienniku** , aby ułatwić monitorowanie kontenerów usługi Azure Cosmos. Te zapytania pracować [nowy język](../log-analytics/log-analytics-log-search-upgrade.md). 

Aby dowiedzieć się znaczenie danych, który jest zwracany przez każdy przeszukiwania dzienników, zobacz [interpretowanie dzienników usługi Azure Cosmos DB](#interpret).

* Aby wykonać zapytanie dotyczące wszystkich dzienników diagnostycznych z usługi Azure Cosmos DB w określonym przedziale czasu:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
    ```

* Aby wysłać zapytanie 10 najbardziej niedawno zarejestrowane zdarzenia:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | take 10
    ```

* Aby wykonać zapytanie dla wszystkich operacji, pogrupowane według typu operacji:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName
    ```

* Do wykonywania zapytań dla wszystkich operacji, pogrupowane według **zasobów**:

    ```
    AzureActivity | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```

* Aby wykonać zapytanie dla wszystkich działań użytkowników, pogrupowane według zasobu:

    ```
    AzureActivity | where Caller == "test@company.com" and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```
    > [!NOTE]
    > To polecenie jest dla dziennika aktywności nie dziennik diagnostyczny.

* Aby wykonać zapytanie, dla którego trwać dłużej niż 3 milisekundy operacje:

    ```
    AzureDiagnostics | where toint(duration_s) > 3 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by clientIpAddress_s, TimeGenerated
    ```

* Aby wykonać zapytanie, które agent jest uruchomiony operacje:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName, userAgent_s
    ```

* Aby wyszukać podczas długotrwałych operacji zostały wykonane:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | project TimeGenerated , duration_s | render timechart
    ```

Aby uzyskać więcej informacji o sposobach korzystania z nowego języka wyszukiwania w dziennikach, zobacz [Omówienie wyszukiwania w dzienniku w](../log-analytics/log-analytics-log-search-new.md)dziennikach Azure monitor. 

## <a id="interpret"></a>Interpretowanie dzienników usługi

Dane diagnostyczne przechowywane w usłudze Azure Storage i dziennikach Azure Monitor używają podobnego schematu. 

W poniższej tabeli opisano zawartość każdego wpisu dziennika.

| Usługa Azure Storage pola lub właściwości | Właściwość dzienników Azure Monitor | Opis |
| --- | --- | --- |
| **czas** | **TimeGenerated** | Data i godzina (UTC), gdy wystąpienia operacji. |
| **resourceId** | **Zasób** | Konto usługi Azure Cosmos DB, dla którego dzienniki są włączone.|
| **category** | **Kategoria** | W przypadku dzienników usługi Azure Cosmos DB **DataPlaneRequests** jest jedyną dostępną wartością. |
| **OperationName** | **OperationName** | Nazwa operacji. Ta wartość może być dowolną z następujących operacji: Tworzenie, aktualizowanie, odczytywanie, ReadFeed, usuwanie, zastępowanie, wykonywanie, SQLQuery, Query, JSQuery, kierownik, HeadFeed lub upsert.   |
| **Właściwości** | Nie dotyczy | Zawartość tego pola są opisane w kolejnych wierszy. |
| **Identyfikator działania** | **activityId_g** | Unikatowy identyfikator GUID dla zarejestrowanych operacji. |
| **userAgent** | **userAgent_s** | Ciąg, który określa agent użytkownika klienta, który wykonuje żądanie. Format to {nazwa agenta użytkownika} / {version}.|
| **requestResourceType** | **requestResourceType_s** | Typ dostęp do zasobów. Ta wartość może być dowolnym z następujących typów zasobów: Baza danych, kontener, dokument, załącznik, użytkownik, uprawnienie, StoredProcedure, wyzwalacz, UserDefinedFunction lub oferta. |
| **statusCode** | **statusCode_s** | Stan odpowiedzi operacji. |
| **requestResourceId** | **ResourceId** | Identyfikator zasobu, które odnoszą się do żądania. Wartość może wskazywać databaseRid, collectionRid lub documentRid w zależności od operacji wykonywanych.|
| **clientIpAddress** | **clientIpAddress_s** | Adres IP klienta. |
| **requestCharge** | **requestCharge_s** | Liczba jednostek żądań, które są używane przez operację |
| **collectionRid** | **collectionId_s** | Unikatowy identyfikator dla kolekcji.|
| **Czas trwania** | **duration_s** | Czas trwania operacji (w milisekundach). |
| **requestLength** | **requestLength_s** | Długość żądania, w bajtach. |
| **responseLength** | **responseLength_s** | Długość odpowiedzi w bajtach.|
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | Ta wartość jest pusta, gdy [tokenów zasobów](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens) są używane do uwierzytelniania. Wartość wskazuje identyfikator zasobu użytkownika. |

## <a name="next-steps"></a>Kolejne kroki

- Aby zrozumieć, jak włączyć rejestrowanie i kategorie metryk i dzienników, które są obsługiwane przez różne usługi platformy Azure, przeczytaj zarówno [Przegląd metryk w systemie Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md) i [Omówienie programu Azure dzienników diagnostycznych ](../azure-monitor/platform/resource-logs-overview.md) artykułów.
- Przeczytaj następujące artykuły, aby dowiedzieć się więcej o usłudze event hubs:
   - [Co to jest usługa Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
   - [Rozpoczynanie pracy z usługą Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
- Odczyt [pobrać metryk i dzienników diagnostycznych z usługi Azure Storage](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs).
- Przeczytaj [Opis wyszukiwania w dzienniku Azure monitor](../log-analytics/log-analytics-log-search-new.md).
