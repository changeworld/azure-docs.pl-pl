---
title: Rejestrowanie diagnostyczne w Azure Cosmos DB
description: Poznaj różne sposoby rejestrowania i monitorowania danych przechowywanych w Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 1e9f852d01d60ead9979b6b1190e285b35d5c312
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2019
ms.locfileid: "72294044"
---
# <a name="diagnostic-logging-in-azure-cosmos-db"></a>Rejestrowanie diagnostyczne w Azure Cosmos DB 

Po rozpoczęciu korzystania z co najmniej jednej bazy danych usługi Azure Cosmos można monitorować sposób i czas uzyskiwania dostępu do baz danych. Ten artykuł zawiera omówienie dzienników dostępnych na platformie Azure. Dowiesz się, jak włączyć rejestrowanie diagnostyczne na potrzeby monitorowania w celu wysyłania dzienników do [usługi Azure Storage](https://azure.microsoft.com/services/storage/), przesyłania strumieniowego dzienników do [usługi Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)i eksportowania dzienników do [dzienników Azure monitor](https://azure.microsoft.com/services/log-analytics/).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="logs-available-in-azure"></a>Dzienniki dostępne na platformie Azure

Zanim będziemy mówić o sposobie monitorowania konta Azure Cosmos DB, wyjaśnij kilka rzeczy na temat rejestrowania i monitorowania. Istnieją różne typy dzienników na platformie Azure. Istnieją [dzienniki aktywności platformy Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs), [dzienniki diagnostyczne platformy](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)Azure, [metryki platformy Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics), zdarzenia, monitorowanie pulsu, dzienniki operacji i tak dalej. Istnieje mnóstwo dzienników. Pełną listę dzienników można znaleźć w [dziennikach Azure monitor](https://azure.microsoft.com/services/log-analytics/) w Azure Portal. 

Na poniższej ilustracji przedstawiono różne dostępne rodzaje dzienników platformy Azure:

![Różne rodzaje dzienników platformy Azure](./media/logging/azurelogging.png)

Na obrazie **zasoby obliczeniowe** reprezentują zasoby platformy Azure, do których można uzyskać dostęp do systemu operacyjnego gościa firmy Microsoft. Na przykład usługa Azure Virtual Machines, zestawy skalowania maszyn wirtualnych, Azure Container Service i tak dalej, są uznawane za zasoby obliczeniowe. Zasoby obliczeniowe generują dzienniki aktywności, dzienniki diagnostyczne i dzienniki aplikacji. Aby dowiedzieć się więcej, zapoznaj się ze [źródłami danych monitorowania w](../azure-monitor/platform/data-sources.md) artykule dotyczącym platformy Azure.

**Zasoby Nieobliczeniowe** są zasobami, w których nie można uzyskać dostępu do podstawowego systemu operacyjnego i pracują bezpośrednio z zasobem. Na przykład sieciowe grupy zabezpieczeń, Logic Apps i tak dalej. Azure Cosmos DB jest zasobem nieobliczeniowym. Dzienniki zasobów innych niż obliczeniowe można wyświetlić w dzienniku aktywności lub włączyć opcję dzienniki diagnostyczne w portalu. Aby dowiedzieć się więcej, zapoznaj się ze [źródłami danych w Azure monitor](../azure-monitor/platform/data-sources.md) artykule.

Dziennik aktywności rejestruje operacje na poziomie subskrypcji dla Azure Cosmos DB. Rejestrowane są operacje, takie jak ListKeys, Write DatabaseAccounts i inne. Dzienniki diagnostyczne zapewniają bardziej szczegółowe rejestrowanie i umożliwiają logowanie DataPlaneRequests (tworzenie, odczytywanie, wykonywanie zapytań itd.) i MongoRequests.


W tym artykule koncentrujemy się na dzienniku aktywności platformy Azure, w dziennikach diagnostycznych platformy Azure i w usłudze Azure Metrics. Jaka jest różnica między tymi trzema dziennikami? 

### <a name="azure-activity-log"></a>Dziennik aktywności platformy Azure

Dziennik aktywności platformy Azure to dziennik subskrypcji, który zapewnia wgląd w zdarzenia na poziomie subskrypcji, które wystąpiły na platformie Azure. Dziennik aktywności raportuje zdarzenia dotyczące kontroli dla subskrypcji w ramach kategorii administracyjnej. Możesz użyć dziennika aktywności, aby określić "co, kto i kiedy" dla operacji zapisu (PUT, POST, DELETE) zasobów w subskrypcji. Można także zrozumieć stan operacji i inne istotne właściwości. 

Dziennik aktywności różni się od dzienników diagnostycznych. Dziennik aktywności zawiera dane dotyczące operacji na zasobie z zewnątrz ( _płaszczyzny kontroli_). W kontekście Azure Cosmos DB operacje płaszczyzny kontroli obejmują tworzenie kontenerów, kluczy list, usuwanie kluczy, listy baz danych itd. Dzienniki diagnostyczne są emitowane przez zasób i zawierają informacje o działaniu tego zasobu ( _płaszczyzny danych_). Niektóre przykłady operacji płaszczyzny danych w dzienniku diagnostycznym to delete, INSERT i ReadFeed.

Dzienniki aktywności (operacje płaszczyzny kontroli) mogą być bogatsze i mogą obejmować pełny adres e-mail obiektu wywołującego, adres IP wywołującego, nazwę zasobu, nazwę operacji, TenantId i inne. Dziennik aktywności zawiera kilka [kategorii](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-activity-log-schema) danych. Aby uzyskać szczegółowe informacje dotyczące schematu tych kategorii, zobacz [schemat zdarzeń dziennika aktywności platformy Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-activity-log-schema). Jednak dzienniki diagnostyczne mogą być ograniczone, ponieważ dane osobowe są często usuwane z tych dzienników. Być może masz adres IP obiektu wywołującego, ale ostatni octant jest usuwany.

### <a name="azure-metrics"></a>Metryki platformy Azure

[Metryki platformy Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) mają najpopularniejszy typ danych telemetrycznych platformy Azure (nazywanych również _licznikami wydajności_), które są emitowane przez większość zasobów platformy Azure. Metryki umożliwiają wyświetlanie informacji o przepływności, przestrzeni dyskowej, spójności, dostępności i opóźnieniu zasobów Azure Cosmos DB. Aby uzyskać więcej informacji, zobacz [monitorowanie i debugowanie za pomocą metryk w Azure Cosmos DB](use-metrics.md).

### <a name="azure-diagnostic-logs"></a>Dzienniki diagnostyczne platformy Azure

Dzienniki diagnostyczne platformy Azure są emitowane przez zasób i zapewniają rozbudowane, częste dane dotyczące operacji tego zasobu. Te dzienniki są przechwytywane dla każdego żądania. Zawartość tych dzienników różni się w zależności od typu zasobu. Dzienniki diagnostyczne na poziomie zasobów różnią się również od dzienników diagnostycznych na poziomie systemu operacyjnego gościa. Dzienniki diagnostyczne systemu operacyjnego gościa są zbierane przez agenta, który jest uruchomiony w ramach maszyny wirtualnej lub innego obsługiwanego typu zasobu. Dzienniki diagnostyczne na poziomie zasobów nie wymagają agenta i nie przechwytuje danych specyficznych dla zasobów z platformy Azure. Dzienniki diagnostyczne na poziomie systemu operacyjnego gościa przechwytują dane z systemu operacyjnego i aplikacji uruchomionych na maszynie wirtualnej.

![Rejestrowanie diagnostyczne do magazynu, Event Hubs lub dzienników Azure Monitor](./media/logging/azure-cosmos-db-logging-overview.png)

### <a name="what-is-logged-by-azure-diagnostic-logs"></a>Co jest rejestrowane w dziennikach diagnostycznych platformy Azure?

* Wszystkie uwierzytelnione żądania zaplecza (TCP/REST) we wszystkich interfejsach API są rejestrowane, łącznie z żądaniami zakończonymi niepowodzeniem w wyniku uprawnień dostępu, błędów systemu lub nieprawidłowych żądań. Obsługa żądań grafów, Cassandra i interfejs API tabel inicjowanych przez użytkownika nie jest obecnie dostępna.
* Operacje na samej bazie danych, które obejmują operacje CRUD na wszystkich dokumentach, kontenerach i bazach danych.
* Operacje na kluczach konta, które obejmują tworzenie, modyfikowanie lub usuwanie kluczy.
* Nieuwierzytelnione żądania, które powodują uzyskanie odpowiedzi 401. Na przykład żądania, które nie mają tokenu okaziciela lub są źle sformułowane lub wygasłe lub mają nieprawidłowy token.

<a id="#turn-on"></a>
## <a name="turn-on-logging-in-the-azure-portal"></a>Włącz rejestrowanie w Azure Portal

Wykonaj następujące kroki, aby włączyć rejestrowanie diagnostyczne w Azure Portal:

1. Zaloguj się do [Azure Portal](https://portal.azure.com). 

1. Przejdź do swojego konta usługi Azure Cosmos. Otwórz okienko **Ustawienia diagnostyczne** , a następnie wybierz opcję **Dodaj ustawienie diagnostyczne** .

    ![Włącz rejestrowanie diagnostyczne dla Azure Cosmos DB w Azure Portal](./media/logging/turn-on-portal-logging.png)

1. Na stronie **Ustawienia diagnostyczne** Wypełnij formularz następującymi szczegółami: 

    * **Nazwa**: Wprowadź nazwę dzienników do utworzenia.

    * Dzienniki można przechowywać w następujących usługach:

      * **Archiwizowanie na koncie magazynu**: Aby użyć tej opcji, musisz mieć istniejące konto magazynu, z którym ma zostać nawiązane połączenie. Aby utworzyć nowe konto magazynu w portalu, zobacz artykuł [Tworzenie konta magazynu](../storage/common/storage-create-storage-account.md) . Następnie wróć do okienka ustawień diagnostycznych usługi Azure Cosmos DB w portalu, aby wybrać konto magazynu. Nowo utworzone konta magazynu mogą pojawić się w menu rozwijanym dopiero po kilku minutach.

      * **Przesyłaj strumieniowo do centrum zdarzeń**: Aby użyć tej opcji, musisz mieć istniejącą przestrzeń nazw Event Hubs i centrum zdarzeń, aby nawiązać połączenie. Aby utworzyć Event Hubs przestrzeń nazw, zobacz [Tworzenie przestrzeni nazw Event Hubs i centrum zdarzeń przy użyciu Azure Portal](../event-hubs/event-hubs-create.md). Następnie wróć do tej strony w portalu, aby wybrać przestrzeń nazw centrum zdarzeń i nazwę zasad.

      * **Wyślij do log Analytics**: Aby użyć tej opcji, użyj istniejącego obszaru roboczego lub Utwórz nowy obszar roboczy log Analytics, wykonując kroki umożliwiające [utworzenie nowego obszaru roboczego](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace) w portalu. 

   * Można rejestrować następujące dane:

      * **DataPlaneRequests**: Wybierz tę opcję, aby rejestrować żądania wewnętrznej bazy danych do wszystkich interfejsów API, które zawierają konta SQL, Graph, MongoDB, Cassandra i interfejs API tabel w Azure Cosmos DB. W przypadku archiwizowania na koncie magazynu można wybrać okres przechowywania dzienników diagnostycznych. Dzienniki zostaną usunięte po upływie okresu przechowywania. Następujące dane JSON są przykładowymi danymi wyjściowymi informacji zarejestrowanych przy użyciu DataPlaneRequests. Właściwości klucza do uwagi to: Requestcharge, statusCode, clientIPaddress i partitionID:

       ```
       { "time": "2019-04-23T23:12:52.3814846Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "DataPlaneRequests", "operationName": "ReadFeed", "properties": {"activityId": "66a0c647-af38-4b8d-a92a-c48a805d6460","requestResourceType": "Database","requestResourceId": "","collectionRid": "","statusCode": "200","duration": "0","userAgent": "Microsoft.Azure.Documents.Common/2.2.0.0","clientIpAddress": "10.0.0.24","requestCharge": "1.000000","requestLength": "0","responseLength": "372","resourceTokenUserRid": "","region": "East US","partitionId": "062abe3e-de63-4aa5-b9de-4a77119c59f8","keyType": "PrimaryReadOnlyMasterKey","databaseName": "","collectionName": ""}}
       ```

      * **MongoRequests**: Wybierz tę opcję, aby rejestrować żądania inicjowane przez użytkownika z frontonu, aby obsłużyć żądania do interfejsu API Azure Cosmos DB dla MongoDB. Żądania MongoDB będą wyświetlane w MongoRequests i DataPlaneRequests. W przypadku archiwizowania na koncie magazynu można wybrać okres przechowywania dzienników diagnostycznych. Dzienniki zostaną usunięte po upływie okresu przechowywania. Następujące dane JSON są przykładowymi danymi wyjściowymi informacji zarejestrowanych przy użyciu MongoRequests. Właściwości klucza do uwagi: Requestcharge, opCode:

       ```
       { "time": "2019-04-10T15:10:46.7820998Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "MongoRequests", "operationName": "ping", "properties": {"activityId": "823cae64-0000-0000-0000-000000000000","opCode": "MongoOpCode_OP_QUERY","errorCode": "0","duration": "0","requestCharge": "0.000000","databaseName": "admin","collectionName": "$cmd","retryCount": "0"}}
       ```

      * **QueryRuntimeStatistics**: Wybierz tę opcję, aby zarejestrować tekst zapytania, który został wykonany.  Następujące dane JSON są przykładowymi danymi wyjściowymi informacji zarejestrowanych przy użyciu QueryRuntimeStatistics:

       ```
       { "time": "2019-04-14T19:08:11.6353239Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "QueryRuntimeStatistics", "properties": {"activityId": "278b0661-7452-4df3-b992-8aa0864142cf","databasename": "Tasks","collectionname": "Items","partitionkeyrangeid": "0","querytext": "{"query":"SELECT *\nFROM c\nWHERE (c.p1__10 != true)","parameters":[]}"}}
       ```

      * **PartitionKeyStatistics**: ten dziennik przedstawia dane statystyczne kluczy partycji. Obecnie statystyki są reprezentowane z rozmiarem magazynu (KB) kluczy partycji. Dziennik jest emitowany w oparciu o trzy pierwsze klucze partycji, które zajmują większość magazynu danych.

       ```
       { "time": "2019-10-11T02:33:24.2018744Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "PartitionKeyStatistics", "properties": {"subscriptionId": "<your_subscription_ID>","regionName": "West US 2","databaseName": "KustoQueryResults","collectionname": "CapacityMetrics","partitionkey": "["CapacityMetricsPartition.136"]","sizeKb": "2048270"}}
       ```

      * **Żądania metryk**: Wybierz tę opcję, aby przechowywać pełne dane w [metrykach platformy Azure](../azure-monitor/platform/metrics-supported.md). W przypadku archiwizowania na koncie magazynu można wybrać okres przechowywania dzienników diagnostycznych. Dzienniki zostaną usunięte po upływie okresu przechowywania.

3. Wybierz pozycję **Zapisz**.

    Jeśli zostanie wyświetlony komunikat o błędzie "nie można zaktualizować diagnostyki \<workspace Name >. Subskrypcja \<subscription ID > nie jest zarejestrowana w celu korzystania z usługi Microsoft. Insights, "postępuj zgodnie z instrukcjami [Diagnostyka Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) , aby zarejestrować konto, a następnie ponów próbę wykonania tej procedury.

    Jeśli chcesz zmienić sposób zapisywania dzienników diagnostycznych w dowolnym momencie w przyszłości, Wróć do tej strony, aby zmodyfikować ustawienia dziennika diagnostycznego dla Twojego konta.

## <a name="turn-on-logging-by-using-azure-cli"></a>Włączanie rejestrowania przy użyciu interfejsu wiersza polecenia platformy Azure

Aby włączyć funkcję rejestrowania metryk i diagnostyki przy użyciu interfejsu wiersza polecenia platformy Azure, użyj następujących poleceń:

- Aby włączyć magazyn dzienników diagnostycznych na koncie magazynu, użyj tego polecenia:

   ```azurecli-interactive
   az monitor diagnostic-settings create --name DiagStorage --resource <resourceId> --storage-account <storageAccountName> --logs '[{"category": "QueryRuntimeStatistics", "enabled": true, "retentionPolicy": {"enabled": true, "days": 0}}]'
   ```

   @No__t-0 jest nazwą konta Azure Cosmos DB. Zasób ma format "/subscriptions/`<subscriptionId>`/resourceGroups/`<resource_group_name>`/Providers/Microsoft. DocumentDB/databaseAccounts/< Azure_Cosmos_account_name >" `storage-account` jest nazwą konta magazynu, do którego mają być wysyłane dzienniki. Można rejestrować inne dzienniki, aktualizując wartości parametrów kategorii do "MongoRequests" lub "DataPlaneRequests". 

- Aby włączyć przesyłanie strumieniowe dzienników diagnostycznych do centrum zdarzeń, użyj tego polecenia:

   ```azurecli-interactive
   az monitor diagnostic-settings create --name cdbdiagsett --resourceId <resourceId> --event-hub-rule <eventHubRuleID> --logs '[{"category":"QueryRuntimeStatistics","enabled":true,"retentionPolicy":{"days":6,"enabled":true}}]'
   ```

   @No__t-0 jest nazwą konta Azure Cosmos DB. @No__t-0 to identyfikator reguły centrum zdarzeń. 

- Aby włączyć wysyłanie dzienników diagnostycznych do obszaru roboczego Log Analytics, użyj tego polecenia:

   ```azurecli-interactive
   az monitor diagnostic-settings create --name cdbdiagsett --resourceId <resourceId> --workspace <resource id of the log analytics workspace> --logs '[{"category":"QueryRuntimeStatistics","enabled":true,"retentionPolicy":{"days":6,"enabled":true}}]'
   ```

Te parametry można połączyć, aby włączyć wiele opcji danych wyjściowych.

## <a name="turn-on-logging-by-using-powershell"></a>Włączanie rejestrowania przy użyciu programu PowerShell

Aby włączyć rejestrowanie diagnostyczne przy użyciu programu PowerShell, wymagany jest program Azure PowerShell z minimalną wersją 1.0.1.

Aby zainstalować program Azure PowerShell i skojarzyć go z subskrypcją platformy Azure, zobacz [Sposób instalowania i konfigurowania programu Azure PowerShell](/powershell/azure/overview).

Jeśli zainstalowano już Azure PowerShell i nie znasz wersji, w konsoli programu PowerShell wpisz `(Get-Module azure -ListAvailable).Version`.  

### <a id="connect"></a>Nawiązywanie połączenia z subskrypcjami
Uruchom sesję programu PowerShell Azure i zaloguj się na konto platformy Azure przy użyciu następującego polecenia:  

```powershell
Connect-AzAccount
```

W podręcznym oknie przeglądarki wprowadź nazwę użytkownika i hasło dla konta platformy Azure. Azure PowerShell pobiera wszystkie subskrypcje, które są skojarzone z tym kontem, i domyślnie używa pierwszego z nich.

Jeśli masz więcej niż jedną subskrypcję, być może trzeba będzie określić konkretną subskrypcję, która została użyta do utworzenia magazynu kluczy Azure. Aby wyświetlić subskrypcje dla konta, wpisz następujące polecenie:

```powershell
Get-AzSubscription
```

Aby określić subskrypcję skojarzoną z kontem Azure Cosmos DB, które jest rejestrowane, wpisz następujące polecenie:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Jeśli masz więcej niż jedną subskrypcję skojarzoną z Twoim kontem, ważne jest, aby określić subskrypcję, której chcesz użyć.
>
>

Więcej informacji o sposobie konfigurowania Azure PowerShell można znaleźć w temacie [How to Install and configure Azure PowerShell](/powershell/azure/overview).

### <a id="storage"></a>Tworzenie nowego konta magazynu dla dzienników
Chociaż możesz użyć istniejącego konta magazynu dla dzienników, w tym samouczku utworzysz nowe konto magazynu, które jest przeznaczone do Azure Cosmos DB dzienników. Dla wygody należy przechowywać szczegóły konta magazynu w zmiennej o nazwie **sa**.

Aby uzyskać dodatkową łatwość zarządzania, w tym samouczku używamy tej samej grupy zasobów, która zawiera bazę danych usługi Azure Cosmos. Zastąp wartości parametrów **ContosoResourceGroup**, **contosocosmosdblogs**i **Północno-środkowe stany USA** zgodnie z wymaganiami:

```powershell
$sa = New-AzStorageAccount -ResourceGroupName ContosoResourceGroup `
-Name contosocosmosdblogs -Type Standard_LRS -Location 'North Central US'
```

> [!NOTE]
> Jeśli zdecydujesz się użyć istniejącego konta magazynu, konto musi używać tej samej subskrypcji co subskrypcja Azure Cosmos DB. Konto musi również korzystać z modelu wdrażania Menedżer zasobów, a nie klasycznego modelu wdrażania.
>
>

### <a id="identify"></a>Zidentyfikuj konto Azure Cosmos DB dla dzienników
Ustaw nazwę konta Azure Cosmos DB na zmienną o nazwie **Account**, gdzie **resourceName** jest nazwą konta Azure Cosmos DB.

```powershell
$account = Get-AzResource -ResourceGroupName ContosoResourceGroup `
-ResourceName contosocosmosdb -ResourceType "Microsoft.DocumentDb/databaseAccounts"
```

### <a id="enable"></a>Włączanie rejestrowania
Aby włączyć rejestrowanie dla Azure Cosmos DB, należy użyć polecenia cmdlet `Set-AzDiagnosticSetting` z zmiennymi dla nowego konta magazynu, konta Azure Cosmos DB i kategorii, aby włączyć rejestrowanie. Uruchom następujące polecenie i Ustaw flagę **-Enabled** na **$true**:

```powershell
Set-AzDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests
```

Dane wyjściowe polecenia powinny wyglądać podobnie do poniższego przykładu:

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

Dane wyjściowe polecenia potwierdzają, że rejestrowanie jest teraz włączone dla bazy danych, a informacje są zapisywane na koncie magazynu.

Opcjonalnie można również ustawić zasady przechowywania dla dzienników, tak aby starsze dzienniki były automatycznie usuwane. Na przykład ustaw dla zasad przechowywania ustawioną flagę **-retentionenable** na **$true**. Ustaw parametr **-RetentionInDays** na **90** , aby dzienniki starsze niż 90 dni zostały automatycznie usunięte.

```powershell
Set-AzDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests`
  -RetentionEnabled $true -RetentionInDays 90
```

### <a id="access"></a>Uzyskiwanie dostępu do dzienników
Dzienniki Azure Cosmos DB dla kategorii **DataPlaneRequests** są przechowywane w kontenerze **Insights-Logs-DataPlaneRequests** na podanym koncie magazynu. 

Najpierw utwórz zmienną dla nazwy kontenera. Zmienna jest używana w całym przewodniku.

```powershell
    $container = 'insights-logs-dataplanerequests'
```

Aby wyświetlić listę wszystkich obiektów BLOB w tym kontenerze, wpisz:

```powershell
Get-AzStorageBlob -Container $container -Context $sa.Context
```

Dane wyjściowe polecenia powinny wyglądać podobnie do poniższego przykładu:

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

Jak widać na podstawie tych danych wyjściowych, obiekty blob są zgodne z konwencją nazewnictwa: `resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<Database Account Name>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

Wartości daty i godziny używają czasu UTC.

Ponieważ to samo konto magazynu może służyć do zbierania dzienników dla wielu zasobów, można użyć w pełni kwalifikowanego identyfikatora zasobu w nazwie obiektu BLOB w celu uzyskania dostępu i pobrania określonych obiektów blob, które są potrzebne. Przed wykonaniem tej czynności będziemy przykrić, jak pobrać wszystkie obiekty blob.

Najpierw utwórz folder w celu pobrania obiektów blob. Na przykład:

```powershell
New-Item -Path 'C:\Users\username\ContosoCosmosDBLogs'`
 -ItemType Directory -Force
```

Następnie Pobierz listę wszystkich obiektów blob:  

```powershell
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context
```

Potoku tej listy za pomocą polecenia `Get-AzStorageBlobContent` pobieranie obiektów BLOB do folderu docelowego:

```powershell
$blobs | Get-AzStorageBlobContent `
 -Destination 'C:\Users\username\ContosoCosmosDBLogs'
```

Po uruchomieniu drugiego polecenia ogranicznik **/** w nazwach obiektów BLOB tworzy pełną strukturę folderów w folderze docelowym. Ta struktura folderów służy do pobierania i przechowywania obiektów BLOB jako plików.

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
* Jeśli chcesz pobrać wszystkie dzienniki z lipca 2017, użyj polecenia `-Blob '*/year=2017/m=07/*'`:

    ```powershell
    Get-AzStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/year=2017/m=07/*'
    ```

Można również uruchomić następujące polecenia:

* Aby zbadać stan ustawień diagnostycznych dla zasobu bazy danych, użyj polecenia `Get-AzDiagnosticSetting -ResourceId $account.ResourceId`.
* Aby wyłączyć rejestrowanie kategorii **DataPlaneRequests** dla zasobu konta bazy danych, użyj polecenia `Set-AzDiagnosticSetting -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories DataPlaneRequests`.


Obiekty blob, które są zwracane w każdej z tych zapytań są przechowywane jako tekst i sformatowane jako obiekt BLOB JSON, jak pokazano w poniższym kodzie:

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

Aby dowiedzieć się więcej o danych w poszczególnych obiektach Blob JSON, zobacz [interpretowanie dzienników Azure Cosmos DB](#interpret).

## <a name="manage-your-logs"></a>Zarządzanie dziennikami

Dzienniki diagnostyczne są udostępniane na koncie przez dwie godziny od momentu wykonania operacji Azure Cosmos DB. To Ty zarządzasz dziennikami na swoim koncie magazynu:

* Użyj standardowych metod kontroli dostępu platformy Azure do zabezpieczenia dzienników i Ogranicz, kto ma do nich dostęp.
* Usuń dzienniki, których nie chcesz już przechowywać na koncie magazynu.
* Okres przechowywania dla żądań płaszczyzny danych, które są archiwizowane na koncie magazynu, jest konfigurowany w portalu, gdy jest zaznaczone ustawienie **DataPlaneRequests log** . Aby zmienić to ustawienie, zobacz [Włączanie rejestrowania w Azure Portal](#turn-on-logging-in-the-azure-portal).


<a id="#view-in-loganalytics"></a>
## <a name="view-logs-in-azure-monitor-logs"></a>Wyświetlanie dzienników w usłudze Azure Monitor

W przypadku wybrania opcji **Wyślij do log Analytics** po włączeniu rejestrowania diagnostycznego dane diagnostyczne z kontenera są przekazywane do Azure monitor dzienników w ciągu dwóch godzin. Gdy przejdziesz do Azure Monitor dzienników natychmiast po włączeniu rejestrowania, nie zostaną wyświetlone żadne dane. Po prostu poczekaj dwie godziny i spróbuj ponownie. 

Przed wyświetleniem dzienników Sprawdź, czy obszar roboczy Log Analytics został uaktualniony do korzystania z nowego języka zapytań Kusto. Aby to sprawdzić, Otwórz [Azure Portal](https://portal.azure.com), wybierz pozycję **log Analytics obszary robocze** po lewej stronie, a następnie wybierz nazwę obszaru roboczego, jak pokazano na następnym obrazie. Zostanie wyświetlona strona **obszaru roboczego log Analytics** :

![Azure Monitor dzienników w Azure Portal](./media/logging/azure-portal.png)

>[!NOTE]
>Obszary robocze OMS są teraz nazywane obszarami roboczymi usługi Log Analytics.  

Jeśli na stronie **obszaru roboczego log Analytics** zostanie wyświetlony następujący komunikat, obszar roboczy nie został uaktualniony do korzystania z nowego języka. Aby uzyskać więcej informacji na temat uaktualniania do nowego języka zapytań, zobacz [uaktualnianie obszaru roboczego usługi Azure log Analytics do nowego przeszukiwania dzienników](../log-analytics/log-analytics-log-search-upgrade.md). 

![Komunikat dotyczący aktualizacji dzienników Azure Monitor](./media/logging/upgrade-notification.png)

Aby wyświetlić dane diagnostyczne w dziennikach Azure Monitor, Otwórz stronę **przeszukiwanie dzienników** w menu po lewej lub obszarze **zarządzania** strony, jak pokazano na poniższej ilustracji:

![Opcje przeszukiwania dzienników w Azure Portal](./media/logging/log-analytics-open-log-search.png)

Teraz, gdy już włączono funkcję zbierania danych, uruchom następujące przykładowe polecenie przeszukiwania dzienników przy użyciu nowego języka zapytań, aby wyświetlić 10 najnowszych dzienników `AzureDiagnostics | take 10`.

![Przykładowe wyszukiwanie w dzienniku dla 10 najnowszych dzienników](./media/logging/log-analytics-query.png)

<a id="#queries"></a>
### <a name="queries"></a>Zapytania

Poniżej znajdują się dodatkowe zapytania, które można wprowadzić w polu **wyszukiwania w dzienniku** , aby ułatwić monitorowanie kontenerów usługi Azure Cosmos. Te zapytania działają w [nowym języku](../log-analytics/log-analytics-log-search-upgrade.md). 

Aby dowiedzieć się o znaczeniu danych zwracanych przez każde wyszukiwanie w dzienniku, zobacz [interpretowanie dzienników Azure Cosmos DB](#interpret).

* Aby wykonać zapytanie dotyczące wszystkich dzienników diagnostycznych z Azure Cosmos DB przez określony czas:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
    ```

* Aby wykonać zapytanie o 10 ostatnio zarejestrowanych zdarzeń:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | take 10
    ```

* Aby wykonać zapytanie o wszystkie operacje, pogrupowane według typu operacji:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName
    ```

* Aby wykonać zapytanie o wszystkie operacje, pogrupowane według **zasobu**:

    ```
    AzureActivity | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```

* Aby wyszukać wszystkie działania użytkownika pogrupowane według zasobu:

    ```
    AzureActivity | where Caller == "test@company.com" and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```
    > [!NOTE]
    > To polecenie dotyczy dziennika aktywności, a nie dziennika diagnostycznego.

* Aby wykonać zapytanie, dla którego operacje trwają dłużej niż 3 ms:

    ```
    AzureDiagnostics | where toint(duration_s) > 3 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by clientIpAddress_s, TimeGenerated
    ```

* Aby wyszukać agenta, dla którego Agent wykonuje operacje:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName, userAgent_s
    ```

* Aby wykonać zapytanie o czas wykonywania długotrwałych operacji:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | project TimeGenerated , duration_s | render timechart
    ```

Aby uzyskać więcej informacji o sposobach korzystania z nowego języka wyszukiwania w dziennikach, zobacz [Omówienie wyszukiwania w dzienniku w](../log-analytics/log-analytics-log-search-new.md)dziennikach Azure monitor. 

## <a id="interpret"></a>Interpretowanie dzienników

Dane diagnostyczne przechowywane w usłudze Azure Storage i dziennikach Azure Monitor używają podobnego schematu. 

W poniższej tabeli opisano zawartość każdego wpisu dziennika.

| Pole lub właściwość usługi Azure Storage | Właściwość dzienników Azure Monitor | Opis |
| --- | --- | --- |
| **pierwszym** | **TimeGenerated** | Data i godzina (UTC), gdy wystąpiła operacja. |
| **Identyfikator** | **Zasób** | Konto Azure Cosmos DB, dla którego są włączone dzienniki.|
| **kategorii** | **Kategoria** | W przypadku dzienników Azure Cosmos DB **DataPlaneRequests** jest jedyną dostępną wartością. |
| **operationName** | **OperationName** | Nazwa operacji. Ta wartość może być dowolną z następujących operacji: Tworzenie, aktualizowanie, Odczyt, ReadFeed, usuwanie, zastępowanie, wykonywanie, SQLQuery, Query, JSQuery, kierownik, HeadFeed lub upsert.   |
| **aœciwoœci** | nd. | Zawartość tego pola jest opisana w następujących wierszach. |
| **activityId** | **activityId_g** | Unikatowy identyfikator GUID dla zarejestrowanej operacji. |
| **userAgent** | **userAgent_s** | Ciąg określający agenta użytkownika klienta wykonującego żądanie. Format to {User Agent Name}/{Version}.|
| **requestResourceType** | **requestResourceType_s** | Typ zasobu, do którego uzyskuje się dostęp. Może to być dowolny z następujących typów zasobów: baza danych, kontener, dokument, załącznik, użytkownik, uprawnienie, StoredProcedure, wyzwalacz, UserDefinedFunction lub oferta. |
| **Stanu** | **statusCode_s** | Stan odpowiedzi operacji. |
| **requestResourceId** | **Identyfikator** | Identyfikator zasobu, który odnosi się do żądania. Wartość może wskazywać na databaseRid, collectionRid lub documentRid w zależności od wykonanej operacji.|
| **clientIpAddress** | **clientIpAddress_s** | Adres IP klienta. |
| **requestCharge** | **requestCharge_s** | Liczba jednostek ru, które są używane przez operację |
| **collectionRid** | **collectionId_s** | Unikatowy identyfikator kolekcji.|
| **trwania** | **duration_s** | Czas trwania operacji (w milisekundach). |
| **requestLength** | **requestLength_s** | Długość żądania w bajtach. |
| **responseLength** | **responseLength_s** | Długość odpowiedzi (w bajtach).|
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | Ta wartość nie jest pusta, gdy [tokeny zasobów](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens) są używane do uwierzytelniania. Wartość wskazuje identyfikator zasobu użytkownika. |

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak włączyć rejestrowanie, a także kategorie metryk i dzienników, które są obsługiwane przez różne usługi platformy Azure, zapoznaj się z [omówieniem metryk w Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md) i [Omówienie artykułów dotyczących dzienników diagnostycznych platformy Azure](../azure-monitor/platform/resource-logs-overview.md) .
- Przeczytaj te artykuły, aby dowiedzieć się więcej o centrach zdarzeń:
   - [Co to jest platforma Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
   - [Rozpoczynanie pracy z usługą Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
- Przeczytaj artykuł [pobieranie metryk i dzienników diagnostycznych z usługi Azure Storage](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs).
- Przeczytaj [Opis wyszukiwania w dzienniku Azure monitor](../log-analytics/log-analytics-log-search-new.md).
