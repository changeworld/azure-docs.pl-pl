---
title: Azure DB rozwiązania Cosmos rejestrowania diagnostycznego | Dokumentacja firmy Microsoft
description: Użyj w tym samouczku ułatwią rozpoczęcie pracy z bazy danych Azure rozwiązania Cosmos rejestrowania.
services: cosmos-db
documentationcenter: ''
author: SnehaGunda
manager: kfile
tags: azure-resource-manager
ms.assetid: ''
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: sngun
ms.openlocfilehash: 0b49a23de62385a8aa362680dde845512d9302fc
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="azure-cosmos-db-diagnostic-logging"></a>Azure DB rozwiązania Cosmos rejestrowania diagnostycznego

Po uruchomieniu używać co najmniej jeden bazy danych Azure rozwiązania Cosmos baz danych, można monitorować, kiedy baz danych są dostępne. Ten artykuł zawiera omówienie dzienników, które są dostępne na platformie Azure. Sposób włączania rejestrowania diagnostycznego do celów, aby wysłać dzienniki monitorowania [usługi Azure Storage](https://azure.microsoft.com/services/storage/), jak strumienia dzienniki, aby [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)i jak dzienniki, aby wyeksportować [Analiza dzienników Azure ](https://azure.microsoft.com/services/log-analytics/).

## <a name="logs-available-in-azure"></a>Dzienniki dostępnej na platformie Azure

Przed omawianiu jak monitorować konta bazy danych rozwiązania Cosmos Azure umożliwia wyjaśnienia kilka rzeczy, o rejestrowania i monitorowania. Istnieją różne typy dzienników na platformie Azure. Istnieją [Dzienniki aktywności Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs), [dzienników diagnostycznych platformy Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs), [Azure metryki](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics), zdarzenia, monitorowanie pulsu, dzienniki operacji i tak dalej. Brak nadmiar dzienników. Można wyświetlić pełną listę dzienników w [Azure Log Analytics](https://azure.microsoft.com/en-us/services/log-analytics/) w portalu Azure. 

Na poniższej ilustracji przedstawiono różne rodzaje Azure dzienniki, które są dostępne:

![Różne rodzaje dzienników Azure](./media/logging/azurelogging.png)

Na ilustracji **zasoby obliczeniowe** reprezentują zasobów platformy Azure, dla których można uzyskać dostępu do systemu operacyjnego gościa firmy Microsoft. Na przykład zestawy, usługi kontenera platformy Azure, skalowania maszyn wirtualnych platformy Azure, maszyny wirtualnej i itd., są zasoby obliczeniowe rozważenia. Obliczenia bazy danych zasobów generować Dzienniki aktywności, dzienniki diagnostyczne i dzienniki aplikacji. Aby dowiedzieć się więcej, zapoznaj się [monitorowania Azure: zasoby obliczeniowe](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md#azure-monitor-sources---compute-subset) artykułu.

**Zasoby obliczeniowe bez** zasobów, w których nie można uzyskać dostępu do podstawowego systemu operacyjnego i pracować bezpośrednio z zasobu. Na przykład grup zabezpieczeń sieci, Logic Apps i tak dalej. Azure DB rozwiązania Cosmos jest zasobów z systemem innym niż obliczeń. Można wyświetlać dzienniki zasoby obliczeniowe z systemem innym niż w dzienniku aktywności lub Włącz opcję dzienników diagnostycznych w portalu. Aby dowiedzieć się więcej, zapoznaj się [monitorowania Azure: z systemem innym niż obliczeniowego zasoby](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md#azure-monitor-sources---everything-else) artykułu.

Dziennik aktywności rejestruje operacje na poziomie subskrypcji dla bazy danych Azure rozwiązania Cosmos. Operacje, takie jak ListKeys, DatabaseAccounts zapisu i inne są rejestrowane. Dzienniki diagnostyczne Podaj bardziej szczegółowe rejestrowanie i umożliwiają DataPlaneRequests (Tworzenie, Odczyt, kwerendy i tak dalej) i MongoRequests dziennika.


W tym artykule możemy skupić się na dziennika aktywności platformy Azure, dzienników diagnostycznych platformy Azure i metryki platformy Azure. Jaka jest różnica między te trzy dzienniki? 

### <a name="azure-activity-log"></a>Dziennik aktywności platformy Azure

Dziennik aktywności platformy Azure jest Dziennik subskrypcji, która zapewnia wgląd w zdarzenia na poziomie subskrypcji, które wystąpiły na platformie Azure. Dziennik aktywności raporty płaszczyzny kontroli zdarzeń dla subskrypcji z kategorii administracyjnej. Dziennik aktywności służy do określenia ", co, kto i kiedy" dla żadnego zapisu operacji (PUT, POST, DELETE) z zasobów w ramach subskrypcji. Można także zrozumienie stanu operacji i inne odpowiednie właściwości. 

Dziennik aktywności różni się od dzienników diagnostycznych. Dziennik zawiera dane dotyczące operacji na zasobie z zewnątrz ( _płaszczyzny kontroli_). W kontekście bazy danych Azure rozwiązania Cosmos płaszczyzny kontroli, które obejmują operacje tworzenia kolekcji, listy kluczy, usuń klucze, listy bazy danych i tak dalej. Dzienniki diagnostyczne są emitowane przez zasób i podaj informacje na temat operacji zasobu ( _płaszczyzna danych_). Przykłady operacje płaszczyzna danych dzienników diagnostycznych są Delete, Insert i ReadFeed.

Dzienniki aktywności (operacji kontroli płaszczyzny) może być bardziej rozbudowane charakteru i mogą obejmować pełny adres e-mail wywołującego, wywołujący adres IP, nazwę zasobu, nazwy operacji, identyfikatora dzierżawcy i więcej. Dziennik zawiera kilka [kategorii](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-activity-log-schema) danych. Aby uzyskać szczegółowe informacje na schematów z tych kategorii, zobacz [schematu zdarzeń dziennika aktywności platformy Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-activity-log-schema). Jednak dzienników diagnostycznych mogą być restrykcyjne charakteru danych dane osobowe często jest usuwany z tych dzienników. Może mieć adres IP obiektu wywołującego, ale jest usuwany ostatni octant.

### <a name="azure-metrics"></a>Metryki Azure

[Metryki Azure](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-overview-metrics) najważniejszych rodzaju danych telemetrycznych platformy Azure (nazywane również _liczniki wydajności_) które są emitowane przez zasobów najbardziej Azure. Metryki umożliwiają wyświetlanie informacji o przepływności, magazynu, spójności, dostępności i opóźnienia zasobów platformy Azure DB rozwiązania Cosmos. Aby uzyskać więcej informacji, zobacz [monitorowanie i debugowanie za pomocą metryki w usłudze Azure DB rozwiązania Cosmos](use-metrics.md).

### <a name="azure-diagnostic-logs"></a>Dzienniki diagnostyczne platformy Azure

Azure dzienników diagnostycznych są emitowane przez zasób i podaj rozbudowane, często danych o działaniu tego zasobu. Zawartość tych dzienników zależy od typu zasobu. Dzienniki diagnostyczne poziom zasobów również różnią się od dzienników diagnostycznych z poziomu systemu operacyjnego gościa. Dzienniki diagnostyczne systemu operacyjnego gościa są zbierane przez agenta, który działa wewnątrz maszyny wirtualnej lub inne obsługiwane typu zasobu. Poziom zasobów dzienników diagnostycznych wymagają żadne dane specyficzne dla zasobów agenta do przechwycenia z platformą Azure. Dzienniki diagnostyczne poziomu systemu operacyjnego gościa przechwycenia danych z systemu operacyjnego i aplikacji, które są uruchomione na maszynie wirtualnej.

![Rejestrowania diagnostycznego do magazynu usługi Event Hubs i analizy dzienników](./media/logging/azure-cosmos-db-logging-overview.png)

### <a name="what-is-logged-by-azure-diagnostic-logs"></a>Co to jest rejestrowane przez dzienników diagnostycznych platformy Azure?

* Wszystkie żądania uwierzytelnionego wewnętrznej bazy danych (TCP/REST) we wszystkich interfejsów API są rejestrowane, w tym żądań zakończonych niepowodzeniem w wyniku uprawnienia dostępu, błędów systemu lub błędów w żądaniach. Obsługa żądania wykresu, Cassandra i interfejsu API tabeli zainicjowane przez użytkownika nie są obecnie dostępne.
* Operacje w bazie danych, które obejmują operacje CRUD na wszystkie dokumenty, kontenery i baz danych.
* Operacje na klucze konta, które obejmują tworzenie, modyfikowanie lub usuwanie kluczy.
* Nieuwierzytelnione żądania, które powodują uzyskanie odpowiedzi 401. Na przykład żądania, które nie mają tokenu elementu nośnego, są nieprawidłowo sformułowane, wygasły lub mają nieprawidłowy token.

<a id="#turn-on"></a>
## <a name="turn-on-logging-in-the-azure-portal"></a>Włącz rejestrowanie w portalu Azure

Aby włączyć rejestrowanie danych diagnostycznych, musi mieć następujące zasoby:

* Istniejące bazy danych Azure rozwiązania Cosmos konta bazy danych i kontenera. Aby uzyskać instrukcje tworzenia tych zasobów, zobacz [Tworzenie konta bazy danych przy użyciu portalu Azure](create-sql-api-dotnet.md#create-a-database-account), [przykłady Azure CLI](cli-samples.md), lub [przykłady środowiska PowerShell](powershell-samples.md).

Aby włączyć rejestrowanie diagnostyczne w portalu Azure, wykonaj następujące czynności:

1. W [portalu Azure](https://portal.azure.com), w Azure rozwiązania Cosmos DB konta, wybierz **dzienniki diagnostyczne** w nawigacji po lewej stronie, a następnie wybierz **Włącz diagnostykę**.

    ![Włączanie rejestrowania diagnostyki dla bazy danych Azure rozwiązania Cosmos w portalu Azure](./media/logging/turn-on-portal-logging.png)

2. W **ustawień diagnostycznych** wykonaj następujące czynności: 

    * **Nazwa**: Wprowadź nazwę dzienniki, aby utworzyć.

    * **Archiwum na konto magazynu**: Aby użyć tej opcji, należy istniejące konto magazynu, aby nawiązać połączenie. Aby utworzyć nowe konto magazynu w portalu, zobacz [Utwórz konto magazynu](../storage/common/storage-create-storage-account.md) i postępuj zgodnie z instrukcjami, aby utworzyć usługi Azure Resource Manager, konta ogólnego przeznaczenia. Następnie wróć do tej strony w portalu, aby wybrać konta magazynu. Może upłynąć kilka minut dla kont magazynu nowo utworzony pojawią się w menu rozwijanym.
    * **Strumień do Centrum zdarzeń**: Aby użyć tej opcji, należy istniejących centra zdarzeń w przestrzeni nazw i zdarzenia koncentratora do nawiązania połączenia. Aby utworzyć przestrzeń nazw usługi Event Hubs, zobacz [tworzenie przestrzeni nazw usługi Event Hubs i Centrum zdarzeń za pomocą portalu Azure](../event-hubs/event-hubs-create.md). Następnie wróć do tej strony w portalu, aby wybrać nazwę przestrzeni nazw i zasad usługi Event Hubs.
    * **Wyślij do analizy dzienników**: Aby użyć tej opcji, użyj istniejący obszar roboczy lub utworzyć nowy obszar roboczy analizy dzienników, wykonując następujące kroki, aby [Utwórz nowy obszar roboczy](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace) w portalu. Aby uzyskać więcej informacji o wyświetlaniu dzienników w analizy dzienników, zobacz [Wyświetl dzienniki w analizy dzienników](#view-in-loganalytics).
    * **Zaloguj się DataPlaneRequests**: Wybierz tę opcję, aby rejestrować żądania zaplecza z podstawowej bazy danych Azure rozwiązania Cosmos platformy rozproszone dla kont SQL, wykres bazy danych MongoDB, Cassandra i interfejsu API tabeli. Jeśli w przypadku archiwizacji konta magazynu można wybrać okres przechowywania dzienników diagnostycznych. Dzienniki są usuwane automatycznie po wygaśnięciu okresu przechowywania.
    * **Zaloguj się MongoRequests**: Wybierz tę opcję, aby rejestrować żądania zainicjowanego przez użytkownika z bazy danych Azure rozwiązania Cosmos frontonu dla obsługi kont API bazy danych MongoDB. Jeśli w przypadku archiwizacji konta magazynu można wybrać okres przechowywania dzienników diagnostycznych. Dzienniki są usuwane automatycznie po wygaśnięciu okresu przechowywania.
    * **Metryka żądania**: Wybierz tę opcję, aby przechowywać pełne dane w [Azure metryki](../monitoring-and-diagnostics/monitoring-supported-metrics.md). Jeśli w przypadku archiwizacji konta magazynu można wybrać okres przechowywania dzienników diagnostycznych. Dzienniki są usuwane automatycznie po wygaśnięciu okresu przechowywania.

3. Wybierz pozycję **Zapisz**.

    Jeśli zostanie wyświetlony komunikat o błędzie informujący "nie można zaktualizować diagnostyki dla \<nazwa obszaru roboczego >. Subskrypcja \<identyfikator subskrypcji > nie jest zarejestrowany do użycia w elemencie microsoft.insights, "wykonaj [Rozwiązywanie problemów z diagnostyki Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) instrukcjami, aby zarejestrować konto, a następnie ponów próbę wykonania tej procedury.

    Jeśli chcesz zmienić sposób dzienników diagnostycznych są zapisywane w dowolnym momencie w przyszłości, wróć do tej strony, aby zmodyfikować ustawienia dziennika diagnostycznego dla Twojego konta.

## <a name="turn-on-logging-by-using-azure-cli"></a>Włącz rejestrowanie przy użyciu wiersza polecenia platformy Azure

Aby włączyć metryki i rejestrowanie danych diagnostycznych przy użyciu wiersza polecenia platformy Azure, użyj następujących poleceń:

- Aby włączyć magazyn dzienników diagnostycznych na konto magazynu, należy użyć tego polecenia:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   `resourceId` Jest nazwa konta bazy danych Azure rozwiązania Cosmos. `storageId` Jest nazwą konta magazynu, do którego chcesz wysłać dzienniki.

- Do przesyłania strumieniowego dzienników diagnostycznych do Centrum zdarzeń, użyj tego polecenia:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   `resourceId` Jest nazwa konta bazy danych Azure rozwiązania Cosmos. `serviceBusRuleId` Jest ciągiem o następującym formacie:

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Aby włączyć wysyłanie dzienników diagnostycznych do obszaru roboczego analizy dzienników, użyj tego polecenia:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

Można połączyć tych parametrów, aby włączyć wiele opcji danych wyjściowych.

## <a name="turn-on-logging-by-using-powershell"></a>Włącz rejestrowanie przy użyciu programu PowerShell

Aby włączyć funkcję rejestrowania diagnostycznego przy użyciu programu PowerShell, należy programu Azure Powershell z minimalnej wersji 1.0.1.

Aby zainstalować program Azure PowerShell i skojarzyć go z subskrypcją platformy Azure, zobacz [Sposób instalowania i konfigurowania programu Azure PowerShell](/powershell/azure/overview).

Jeśli został już zainstalowany program Azure PowerShell, a nie wiadomo, wersji, z konsoli programu PowerShell typu `(Get-Module azure -ListAvailable).Version`.  

### <a id="connect"></a>Nawiązywanie połączenia z subskrypcjami
Uruchom sesję programu PowerShell Azure i zaloguj się na konto platformy Azure przy użyciu następującego polecenia:  

```powershell
Login-AzureRmAccount
```

W podręcznym oknie przeglądarki wprowadź nazwę użytkownika i hasło dla konta platformy Azure. Program Azure PowerShell pobiera wszystkie subskrypcje, które są skojarzone z tym kontem i domyślnie używa pierwszego z nich.

Jeśli masz więcej niż jedną subskrypcję, może być konieczne określenie określonej subskrypcji, który został użyty do utworzenia magazynu kluczy Azure. Aby zobaczyć subskrypcje dla swojego konta, wpisz następujące polecenie:

```powershell
Get-AzureRmSubscription
```

Następnie aby określić subskrypcję skojarzoną z konta bazy danych Azure rozwiązania Cosmos jest rejestrowanie, wpisz następujące polecenie:

```powershell
Set-AzureRmContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Jeśli masz więcej niż jedną subskrypcję, który został skojarzony z Twoim kontem, jest ważne określić subskrypcję, która ma być używany.
>
>

Aby uzyskać więcej informacji o sposobie konfigurowania programu Azure PowerShell, zobacz [jak instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).

### <a id="storage"></a>Tworzenie nowego konta magazynu dla dzienników
Chociaż możesz użyć istniejącego konta magazynu dla dzienników, w tym samouczku, utworzymy nowe konto magazynu, mający na celu dzienników bazy danych Azure rozwiązania Cosmos. Dla wygody, są przechowywane szczegóły konta magazynu w zmiennej o nazwie **sa**.

W celu ułatwienia zarządzania, w tym samouczku używamy tej samej grupie zasobów, która zawiera bazę danych bazy danych Azure rozwiązania Cosmos. Podstaw wartości **ContosoResourceGroup**, **contosocosmosdblogs**, i **północno-środkowe stany** parametry zgodnie z wymaganiami:

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName ContosoResourceGroup `
-Name contosocosmosdblogs -Type Standard_LRS -Location 'North Central US'
```

> [!NOTE]
> Jeśli zdecydujesz się używać istniejącego konta magazynu, konto musi używać tej samej subskrypcji co subskrypcji bazy danych Azure rozwiązania Cosmos. Konto również użyć modelu wdrażania usługi Resource Manager, a nie w klasycznym modelu wdrażania.
>
>

### <a id="identify"></a>Zidentyfikowanie konta bazy danych rozwiązania Cosmos Azure dla dzienników
Ustaw nazwę konta Azure DB rozwiązania Cosmos do zmiennej o nazwie **konta**, gdzie **ResourceName** jest nazwa konta bazy danych Azure rozwiązania Cosmos.

```powershell
$account = Get-AzureRmResource -ResourceGroupName ContosoResourceGroup `
-ResourceName contosocosmosdb -ResourceType "Microsoft.DocumentDb/databaseAccounts"
```

### <a id="enable"></a>Włączanie rejestrowania
Aby włączyć rejestrowanie dla bazy danych rozwiązania Cosmos platformy Azure, użyj `Set-AzureRmDiagnosticSetting` polecenia cmdlet ze zmiennymi dla nowego konta magazynu, konto bazy danych Azure rozwiązania Cosmos i kategorię, aby włączyć rejestrowanie. Uruchom następujące polecenie, a następnie ustaw **-włączone** flaga **$true**:

```powershell
Set-AzureRmDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests
```

Dane wyjściowe polecenia powinien wyglądać w poniższym przykładzie:

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

Dane wyjściowe polecenia potwierdza, że włączono rejestrowanie dla bazy danych i informacji jest zapisywany do konta magazynu.

Opcjonalnie można także ustawić zasady przechowywania dla dzienników, tak, aby starsze dzienniki są automatycznie usuwane. Na przykład ustawić zasady przechowywania z **- RetentionEnabled** ustawić flagi **$true**. Ustaw **- RetentionInDays** parametr **90** tak, aby starsze niż 90 dni dzienniki są automatycznie usuwane.

```powershell
Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests`
  -RetentionEnabled $true -RetentionInDays 90
```

### <a id="access"></a>Uzyskiwanie dostępu do dzienników
Azure DB rozwiązania Cosmos w dziennikach **DataPlaneRequests** kategorii są przechowywane w **insights dzienniki danych płaszczyzny — żądania** kontenera w podanego konta magazynu. 

Najpierw utwórz zmienną dla nazwy kontenera. Zmienna zostanie użyta w całym przewodnika.

```powershell
    $container = 'insights-logs-dataplanerequests'
```

Aby wyświetlić listę wszystkich obiektów blob w tym kontenerze, wpisz:

```powershell
Get-AzureStorageBlob -Container $container -Context $sa.Context
```

Dane wyjściowe polecenia powinien wyglądać w poniższym przykładzie:

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

Jak widać w przedstawionych danych wyjściowych, obiekty BLOB zgodne z konwencją nazewnictwa: `resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<Database Account Name>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

Wartości daty i godziny używają czasu UTC.

Ponieważ to samo konto magazynu może służyć do zbierania dzienników dla wielu zasobów, można użyć identyfikator zasobu w pełni kwalifikowana nazwa obiektu blob określonych obiektów blob, które należy pobrać. Zanim przejdziemy, możemy opisano, jak pobrać wszystkie obiekty BLOB.

Najpierw utwórz folder w celu pobrania obiektów blob. Na przykład:

```powershell
New-Item -Path 'C:\Users\username\ContosoCosmosDBLogs'`
 -ItemType Directory -Force
```

Następnie Uzyskaj listę wszystkich obiektów blob:  

```powershell
$blobs = Get-AzureStorageBlob -Container $container -Context $sa.Context
```

Tej listy za pośrednictwem potoku `Get-AzureStorageBlobContent` polecenie, aby pobrać obiekty BLOB do folderu docelowego:

```powershell
$blobs | Get-AzureStorageBlobContent `
 -Destination 'C:\Users\username\ContosoCosmosDBLogs'
```

Po uruchomieniu tego drugiego polecenia **/** ogranicznik w nazwach obiektów blob utworzy pełną strukturę folderów w folderze docelowym. Ta struktura folderu służy do pobierania i przechowywania obiektów blob jako plików.

Aby selektywnie pobierać obiekty blob, użyj symboli wieloznacznych. Na przykład:

* Jeśli masz wiele baz danych i chcesz pobrać dzienniki dla tylko jednej bazy danych o nazwie **CONTOSOCOSMOSDB3**, użyj polecenia:

    ```powershell
    Get-AzureStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/DATABASEACCOUNTS/CONTOSOCOSMOSDB3
    ```

* Jeśli masz wiele grup zasobów i chcesz pobrać dzienniki dla tylko jednej grupy zasobów, użyj polecenia `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

    ```powershell
    Get-AzureStorageBlob -Container $container `
    -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
    ```
* Jeśli chcesz pobrać wszystkie dzienniki dla miesiąca 2017 lipca, użyj polecenia `-Blob '*/year=2017/m=07/*'`:

    ```powershell
    Get-AzureStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/year=2017/m=07/*'
    ```

Można też uruchomić następujące polecenia:

* Aby sprawdzić stan ustawień diagnostycznych dla zasobu bazy danych, użyj polecenia `Get-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`.
* Aby wyłączyć rejestrowanie **DataPlaneRequests** kategorii dla zasobu konta bazy danych, użyj polecenia `Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories DataPlaneRequests`.


Obiekty BLOB, które są zwracane w każdym z tych kwerend są przechowywane jako tekst i sformatowane jako obiektu blob JSON, jak pokazano w poniższym kodzie:

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

Aby dowiedzieć się więcej o danych w każdym obiekcie blob JSON, zobacz [interpretowanie dzienników bazy danych Azure rozwiązania Cosmos](#interpret).

## <a name="manage-your-logs"></a>Zarządzasz dziennikami

Dzienniki diagnostyczne są udostępniane w ramach konta dla dwóch godzin od momentu wykonano operację bazy danych Azure rozwiązania Cosmos. To Ty zarządzasz dziennikami na swoim koncie magazynu:

* Użyj metody kontroli dostępu platformy Azure standardowe zabezpieczenia dzienników i ograniczyć, którzy mogą uzyskiwać do nich dostęp.
* Usuń dzienniki, których nie chcesz już przechowywać na koncie magazynu.
* Okres przechowywania dla żądań płaszczyzna danych, które są archiwizowane na konto magazynu jest skonfigurowany w portalu po **DataPlaneRequests dziennika** ustawienie jest wybrane. Aby zmienić to ustawienie, zobacz [Włącz rejestrowanie w portalu Azure](#turn-on-logging-in-the-azure-portal).


<a id="#view-in-loganalytics"></a>
## <a name="view-logs-in-log-analytics"></a>Wyświetl dzienniki w analizy dzienników

W przypadku wybrania **wysyłać do analizy dzienników** opcję po włączeniu rejestrowania diagnostycznego diagnostycznych danych z kolekcji jest przekazywane do analizy dzienników w ciągu dwóch godzin. Po wyświetleniu analizy dzienników bezpośrednio po włączeniu rejestrowania, nie będą widzieć żadnych danych. Po prostu Zaczekaj dwie godziny i spróbuj ponownie. 

Aby wyświetlić dzienniki, sprawdź i zobacz, jeśli obszaru roboczego analizy dzienników został uaktualniony do użycia nowego języka zapytań usługi Analiza dzienników. Aby sprawdzić, otwórz [portalu Azure](https://portal.azure.com), wybierz pozycję **analizy dzienników** po lewej, następnie wybierz nazwę obszaru roboczego, jak pokazano w następnym obrazu. **Obszarem roboczym pakietu OMS** zostanie wyświetlona strona:

![Analiza dzienników w portalu Azure](./media/logging/azure-portal.png)

Jeśli zostanie wyświetlony następujący komunikat na **obszarem roboczym pakietu OMS** strony, obszar roboczy nie została ona uaktualniona do użycia w nowym języku. Aby uzyskać więcej informacji na temat sposobu uaktualnienia do nowego języka zapytań, zobacz [uaktualnienia obszaru roboczego analizy dzienników Azure do nowego wyszukiwania dziennika](../log-analytics/log-analytics-log-search-upgrade.md). 

![Komunikat uaktualnienia analizy dzienników](./media/logging/upgrade-notification.png)

Aby wyświetlić dane diagnostyczne w analizy dzienników, otwórz **wyszukiwania dziennika** strony z menu po lewej stronie lub **zarządzania** obszar strony, jak pokazano na poniższej ilustracji:

![Opcje wyszukiwania dziennika w portalu Azure](./media/logging/log-analytics-open-log-search.png)

Teraz, czy włączono zbieranie danych, uruchom w poniższym przykładzie wyszukiwania dziennika za pomocą nowego języka zapytań w dziennikach 10 najnowszych `AzureDiagnostics | take 10`.

![Przykładowy dziennik wyszukiwania 10 najnowszych dzienników](./media/logging/log-analytics-query.png)

<a id="#queries"></a>
### <a name="queries"></a>Zapytania

Poniżej przedstawiono niektóre dodatkowe zapytań, które można wprowadzić do **wyszukiwania dziennika** pole, aby ułatwić sobie monitorowanie kontenerów bazy danych Azure rozwiązania Cosmos. Te zapytania pracować z [nowy język](../log-analytics/log-analytics-log-search-upgrade.md). 

Aby uzyskać informacje o znaczeniu dane, które jest zwracana w wyniku wyszukiwania poszczególnych dziennika, zobacz [interpretowanie dzienników bazy danych Azure rozwiązania Cosmos](#interpret).

* Aby zapytania dla wszystkich dzienników diagnostycznych z bazy danych usługi Azure rozwiązania Cosmos w określonym czasie:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
    ```

* Kwerenda 10 najbardziej ostatnio zarejestrowane zdarzenia:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | take 10
    ```

* Dla wszystkich operacji pogrupowane według typu operacji kwerendy:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName
    ```

* W zapytaniu dla wszystkich operacji pogrupowane według **zasobów**:

    ```
    AzureActivity | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```

* Dla wszystkich działań użytkownika, pogrupowane według zasobu kwerendy:

    ```
    AzureActivity | where Caller == "test@company.com" and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```
    > [!NOTE]
    > To polecenie jest działanie dziennika diagnostycznego dziennika.

* Do zapytania, dla którego operacje trwać dłużej niż 3 w milisekundach:

    ```
    AzureDiagnostics | where toint(duration_s) > 30000 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by clientIpAddress_s, TimeGenerated
    ```

* Do zapytania, dla których agent działa operacje:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName, userAgent_s
    ```

* Kwerendy długotrwałe operacje zostały wykonane po:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | project TimeGenerated , toint(duration_s)/1000 | render timechart
    ```

Aby uzyskać więcej informacji o sposobie używania nowego języka wyszukiwania dziennika, zobacz [Omówienie wyszukiwania dziennika analizy dzienników](../log-analytics/log-analytics-log-search-new.md). 

## <a id="interpret"></a>Interpretowanie dzienników

Dane diagnostyczne są przechowywane w usłudze Azure Storage i analizy dzienników używa podobne schematu. 

W poniższej tabeli opisano zawartości każdego wpisu dziennika.

| Azure magazynu pola lub właściwości | Właściwości analizy dzienników | Opis |
| --- | --- | --- |
| **Czas** | **TimeGenerated** | Data i godzina (UTC), gdy podczas operacji. |
| **resourceId** | **Zasób** | Konto bazy danych Azure rozwiązania Cosmos, dla których dzienniki są włączone.|
| **category** | **Kategoria** | W przypadku dzienników bazy danych Azure rozwiązania Cosmos **DataPlaneRequests** jest jedyną dostępną wartością. |
| **OperationName** | **OperationName** | Nazwa operacji. Ta wartość może być dowolną z następujących operacji: tworzenia, aktualizacji, odczytu, ReadFeed, Usuń, Zastąp, Execute, SqlQuery, zapytania, JSQuery, Head, HeadFeed lub Upsert.   |
| **właściwości** | Nie dotyczy | Zawartość tego pola są opisane w kolejnych wierszach. |
| **Identyfikator działania** | **activityId_g** | Unikatowy identyfikator GUID dla zarejestrowanych operacji. |
| **userAgent** | **userAgent_s** | Ciąg określający agent użytkownika klienta, który wykonuje żądanie. Format to {nazwa agenta użytkownika} / {wersja}.|
| **resourceType** | **ResourceType** | Typ dostęp do zasobów. Ta wartość może być dowolny z następujących zasobów: bazy danych, kolekcji, dokument, załącznika, użytkownika, uprawnienia, procedura składowana, wyzwalacz, UserDefinedFunction lub oferty. |
| **statusCode** | **statusCode_s** | Stan odpowiedzi operacji. |
| **requestResourceId** | **ResourceId** | Element resourceId odnoszą się do żądania. Wartość może wskazywać databaseRid, collectionRid lub documentRid w zależności od operacji wykonywane.|
| **clientIpAddress** | **clientIpAddress_s** | Adres IP klienta. |
| **requestCharge** | **requestCharge_s** | Liczba RUs, które są używane przez operację |
| **collectionRid** | **collectionId_s** | Unikatowy identyfikator dla kolekcji.|
| **Czas trwania** | **duration_s** | Czas trwania działania w taktach. |
| **requestLength** | **requestLength_s** | Długość żądania, w bajtach. |
| **responseLength** | **responseLength_s** | Długość odpowiedzi w bajtach.|
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | Ta wartość jest pusta, gdy [tokenów zasobów](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens) są używane do uwierzytelniania. Wartość wskazuje identyfikator zasobu użytkownika. |

## <a name="next-steps"></a>Kolejne kroki

- Aby zrozumieć, jak włączyć rejestrowanie i metryki i dziennika kategorie, które są obsługiwane przez różne usługi platformy Azure, przeczytaj zarówno [omówienie metryk w Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md) i [Omówienie programu Azure dzienników diagnostycznych ](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) artykułów.
- Przeczytaj następujące artykuły, aby dowiedzieć się więcej o usłudze event hubs:
   - [Co to jest usługa Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
   - [Rozpoczynanie pracy z usługą Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
- Odczyt [pobrać metryki i dzienników diagnostycznych z usługi Magazyn Azure](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs).
- Odczyt [Omówienie wyszukiwania dziennika analizy dzienników](../log-analytics/log-analytics-log-search-new.md).
