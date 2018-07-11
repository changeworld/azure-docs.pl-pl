---
title: Omówienie dzienniki diagnostyczne platformy Azure
description: Dowiedz się, czym są dzienniki diagnostyczne platformy Azure i jak można je zrozumieć zdarzenia występujące w obrębie zasobu platformy Azure.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: johnkem
ms.component: logs
ms.openlocfilehash: a6435f74141429cbe4f9a169fd2f234161d486c4
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2018
ms.locfileid: "37918744"
---
# <a name="collect-and-consume-log-data-from-your-azure-resources"></a>Zbieranie i używanie dane dzienników z zasobów platformy Azure

## <a name="what-are-azure-resource-diagnostic-logs"></a>Co to są dzienniki diagnostyczne zasobów platformy Azure

**Dzienniki diagnostyczne platformy Azure poziom zasobów** dzienników wyemitowane przez zasób, które zawierają dane w rozbudowane, często o działaniu tego zasobu. Zawartość tych dzienników jest zależna od typu zasobu. Na przykład dwie kategorie dzienników zasobów są liczniki reguł sieciowej grupy zabezpieczeń i inspekcji w usłudze Key Vault.

Zasób poziom dzienniki diagnostyczne różnią się od [dziennika aktywności](monitoring-overview-activity-logs.md). Dziennik aktywności zapewnia wgląd w operacje, które zostały wykonane na zasoby w subskrypcji przy użyciu usługi Resource Manager, na przykład tworzenia maszyny wirtualnej lub usuwanie aplikacji logiki. Dziennik aktywności jest dziennika poziomu subskrypcji. Zasób poziom dzienniki diagnostyczne udostępniają szczegółowe dane operacji wykonywanych w ramach tego samego zasobu, na przykład wprowadzenie klucza tajnego z usługi Key Vault.

Zasób poziom dzienniki diagnostyczne różnią się także z poziomu systemu operacyjnego gościa, dzienniki diagnostyczne. System operacyjny gościa, dzienniki diagnostyczne są te zebranych przez agenta uruchomionego na maszynie wirtualnej lub innych obsługiwany typ zasobu. Dzienniki diagnostyczne na poziomie zasobów wymagają nie agenta i przechwytywania danych specyficznych dla zasobów z platformą Azure, natomiast dzienniki diagnostyczne na poziomie systemu operacyjnego gościa przechwytywanie danych z systemu operacyjnego i aplikacji uruchomionych na maszynie wirtualnej.

Nie wszystkie zasoby obsługi nowego typu zasobu dzienniki diagnostyczne opisane w tym miejscu. Ten artykuł zawiera sekcję Lista typów zasobów, które obsługują nowe dzienniki diagnostyczne poziom zasobów.

![Dzienniki diagnostyczne zasobu a inne typy dzienników ](./media/monitoring-overview-of-diagnostic-logs/Diagnostics_Logs_vs_other_logs_v5.png)

## <a name="what-you-can-do-with-resource-level-diagnostic-logs"></a>Co można zrobić za pomocą zasobów poziom dzienniki diagnostyczne
Oto kilka rzeczy, które można zrobić za pomocą dzienników diagnostycznych zasobów:

![Logiczne położenie dzienników diagnostycznych zasobów](./media/monitoring-overview-of-diagnostic-logs/Diagnostics_Logs_Actions.png)

* Zapisywanie ich [ **konta magazynu** ](monitoring-archive-diagnostic-logs.md) do wglądu, inspekcji czy ręcznie. Można określić przy użyciu czasu (w dniach) przechowywania **ustawień diagnostycznych zasobu**.
* [Stream im **usługi Event Hubs** ](monitoring-stream-diagnostic-logs-to-event-hubs.md) dla pozyskiwania przez usługi innych firm lub rozwiązania analizy niestandardowych, takich jak usługi Power BI.
* Analizuj je za pomocą [usługi Log Analytics](../log-analytics/log-analytics-azure-storage.md)

Można użyć konta magazynu lub przestrzeni nazw usługi Event Hubs, która nie znajduje się w tej samej subskrypcji co emitowane dzienniki. Użytkownik, który konfiguruje ustawienie, musi mieć odpowiedni dostęp RBAC do obu subskrypcji.

> [!NOTE]
>  Obecnie nie można zarchiwizować dane do magazynu konta, do którego za zabezpieczonej sieci wirtualnej.

> [!WARNING]
> Format danych dziennika w ramach konta magazynu zmieni się na wiersze JSON od 1 listopada 2018 r. [Zobacz, w tym artykule, aby uzyskać opis wpływu i aktualizacji narzędzi do obsługi nowego formatu.](./monitor-diagnostic-logs-append-blobs.md) 
>
> 

## <a name="resource-diagnostic-settings"></a>Ustawienia diagnostyczne zasobów

Dzienniki diagnostyczne zasobu na potrzeby poza obliczeniowymi czy zasoby są skonfigurowane za pomocą ustawień diagnostycznych zasobu. **Ustawienia diagnostyczne zasobów** formantów zasobów:

* Gdzie dzienników diagnostycznych zasobów i metryki są wysyłane (konto magazynu, usługa Event Hubs i/lub usługi Log Analytics).
* Kategorie dziennika, które są wysyłane i tego, czy też są wysyłane dane metryk.
* Jak długo każda kategoria dziennika ma być przechowywana na koncie magazynu
    - Wpisanie wartości zero oznacza, że dzienniki są przechowywane w nieskończoność. W przeciwnym razie wartość może być dowolną liczbę dni z zakresu od 1 do 2147483647.
    - Jeśli ustawiono zasady przechowywania, ale przechowywania dzienników na koncie magazynu jest wyłączona (na przykład, jeśli tylko są zaznaczone opcje usługi Event Hubs lub usługi Log Analytics), zasad przechowywania przyniosło żadnego skutku.
    - Zasady przechowywania są stosowane dziennie, aby na koniec dnia (UTC), dzienniki w dzień, w którym jest teraz, po przekroczeniu przechowywania zasady zostaną usunięte. Na przykład jeśli masz zasady przechowywania w jeden dzień, na początku dnia już dziś dzienników z wczoraj zanim dnia zostaną usunięte. Proces usuwania rozpoczyna się od północy czasu UTC, ale należy pamiętać, że może upłynąć do 24 godzin dla dzienników są usuwane z konta magazynu.

Te ustawienia można łatwo skonfigurować za pomocą ustawień diagnostycznych dla zasobu w witrynie Azure portal, za pomocą programu Azure PowerShell i interfejsu wiersza polecenia lub za pośrednictwem [interfejsu API REST usługi Azure Monitor](https://msdn.microsoft.com/library/azure/dn931943.aspx).

> [!NOTE]
> Wysyłanie metryk wielowymiarowych za pomocą ustawień diagnostycznych nie jest obecnie obsługiwane. Metryki wielowymiarowe są eksportowane jako spłaszczone metryki jednowymiarowe z wartościami zagregowanymi we wszystkich wymiarach.
>
> *Na przykład*: metrykę „Komunikaty przychodzące” w centrum zdarzeń można przeglądać i przedstawiać na wykresie na poziomie pojedynczej kolejki. Jednak w przypadku eksportowania za pomocą ustawień diagnostycznych metryka ta jest przedstawiana jako wszystkie komunikaty przychodzące we wszystkich kolejkach w centrum zdarzeń.
>
>

> [!WARNING]
> Dzienniki diagnostyczne i metryki z warstwy system operacyjny gościa użycia zasobów (na przykład maszyny wirtualne lub usługi Service Fabric) obliczeń [oddzielny mechanizm do konfiguracji i wyboru danych wyjściowych](../azure-diagnostics.md).

## <a name="how-to-enable-collection-of-resource-diagnostic-logs"></a>Jak włączyć zbieranie dzienników diagnostycznych zasobów

Zbieranie dzienników diagnostycznych zasobów można włączyć [jako część tworzenia zasobu w szablonie usługi Resource Manager](./monitoring-enable-diagnostic-logs-using-template.md) lub po utworzeniu zasobu ze strony tego zasobu w portalu. Można również włączyć kolekcję w dowolnym momencie za pomocą poleceń programu Azure PowerShell lub interfejsu wiersza polecenia lub przy użyciu interfejsu API REST usługi Azure Monitor.

> [!TIP]
> Instrukcje te mogą nie mieć zastosowania bezpośrednio do każdego zasobu. Zobacz linki schematu u dołu tej strony, aby zrozumieć specjalne kroki, które mogą mieć zastosowanie do niektórych typów zasobów.

### <a name="enable-collection-of-resource-diagnostic-logs-in-the-portal"></a>Włączanie zbierania dzienników diagnostycznych zasobów w portalu

Po utworzeniu zasobu, przechodząc do określonego zasobu lub przechodząc do usługi Azure Monitor, można włączyć zbierania dzienników diagnostycznych zasobów w witrynie Azure portal. Aby je włączyć za pomocą usługi Azure Monitor:

1. W [witryny Azure portal](http://portal.azure.com), przejdź do usługi Azure Monitor i kliknij **ustawień diagnostycznych**

    ![Monitorowanie sekcji usługi Azure Monitor](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-blade.png)

2. Opcjonalnie filtrować listę według grupy zasobów lub typ zasobu, a następnie kliknij na zasób, dla którego chcesz skonfigurować ustawienie diagnostyczne.

3. Jeśli ustawienia nie istnieje w zasobie wybrany, zostanie wyświetlony monit, aby utworzyć ustawienie. Kliknij przycisk "Włącz diagnostykę."

   ![Dodaj ustawienie diagnostyczne — nie istniejące ustawienia](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-none.png)

   W przypadku ustawienia istniejące na zasób zostanie wyświetlona lista ustawień już skonfigurowany dla tego zasobu. Kliknij pozycję "Dodaj ustawienie diagnostyczne".

   ![Dodaj ustawienie diagnostyczne — istniejące ustawienia](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-multiple.png)

3. Nadaj nazwę ustawienia, zaznacz pola wyboru dla każdej lokalizacji docelowej, do której chcesz wysyłać dane i Konfiguruj zasobu, który jest używany dla każdej lokalizacji docelowej. Opcjonalnie można ustawić liczbę dni przechowywania tych dzienników przy użyciu **przechowywania (dni)** suwaki (dotyczy tylko do miejsca docelowego konta magazynu). Dzienniki na wpisanie wartości zero są przechowywane w nieskończoność.

   ![Dodaj ustawienie diagnostyczne — istniejące ustawienia](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-configure.png)

4. Kliknij pozycję **Zapisz**.

Po kilku chwilach nowe ustawienie jest wyświetlane na liście ustawień dla tego zasobu, a dzienniki diagnostyczne są wysyłane do określonego miejsca docelowe, zaraz po wygenerowaniu nowych danych zdarzenia.

### <a name="enable-collection-of-resource-diagnostic-logs-via-powershell"></a>Włączanie zbierania dzienników diagnostycznych zasobów za pomocą programu PowerShell

Aby włączyć zbieranie dzienników diagnostycznych zasobów za pomocą programu Azure PowerShell, użyj następujących poleceń:

Aby włączyć magazyn dzienniki diagnostyczne na koncie magazynu, użyj tego polecenia:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
```

Identyfikator konta magazynu jest identyfikator zasobu dla konta magazynu, do którego chcesz wysłać dzienniki.

Aby włączyć strumieniowe przesyłanie dzienników diagnostycznych do Centrum zdarzeń, użyj tego polecenia:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your Service Bus rule id] -Enabled $true
```

Identyfikator reguły magistrali usług jest ciągiem w formacie: `{Service Bus resource ID}/authorizationrules/{key name}`.

Aby włączyć wysyłanie dzienników diagnostycznych do obszaru roboczego usługi Log Analytics, użyj tego polecenia:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
```

Możesz uzyskać identyfikator zasobu obszaru roboczego usługi Log Analytics przy użyciu następującego polecenia:

```powershell
(Get-AzureRmOperationalInsightsWorkspace).ResourceId
```

Można połączyć te parametry, aby włączyć wiele opcji danych wyjściowych.

### <a name="enable-collection-of-resource-diagnostic-logs-via-azure-cli-20"></a>Włączanie zbierania dzienników diagnostycznych zasobów za pomocą interfejsu wiersza polecenia platformy Azure w wersji 2.0

Aby włączyć zbieranie dzienników diagnostycznych zasobów za pomocą interfejsu wiersza polecenia platformy Azure w wersji 2.0, należy użyć [tworzenie az monitor diagnostic-settings](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) polecenia.

Aby włączyć magazyn dzienników diagnostycznych w ramach konta magazynu:

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <name or ID of storage account> \
    --resource <target resource object ID> \
    --resource-group <storage account resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

`--resource-group` Argument tylko jest wymagany, jeżeli `--storage-account` nie jest identyfikator obiektu.

Aby włączyć strumieniowe przesyłanie dzienników diagnostycznych do Centrum zdarzeń:

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --event-hub <event hub name> \
    --event-hub-rule <event hub rule ID> \
    --resource <target resource object ID> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true
    }
    ]'
```

Identyfikator reguły jest ciągiem o następującym formacie: `{Service Bus resource ID}/authorizationrules/{key name}`.

Aby włączyć wysyłanie dzienników diagnostycznych do obszaru roboczego usługi Log Analytics:

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --workspace <log analytics name or object ID> \
    --resource <target resource object ID> \
    --resource-group <log analytics workspace resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true
    }
    ]'
```

`--resource-group` Argument tylko jest wymagany, jeżeli `--workspace` nie ma Identyfikatora obiektu

Za pomocą dowolnego polecenia możesz dodać dodatkowe kategorie do dziennik diagnostyczny, dodając słowników tablicę JSON przekazywane jako `--logs` parametru. Można połączyć `--storage-account`, `--event-hub`, i `--workspace` parametry, aby włączyć wiele opcji danych wyjściowych.

### <a name="enable-collection-of-resource-diagnostic-logs-via-rest-api"></a>Włączanie zbierania dzienników diagnostycznych zasobów za pomocą interfejsu API REST

Aby zmienić ustawień diagnostycznych przy użyciu interfejsu API REST usługi Azure Monitor, zobacz [w tym dokumencie](https://msdn.microsoft.com/library/azure/dn931931.aspx).

## <a name="manage-resource-diagnostic-settings-in-the-portal"></a>Ustawienia diagnostyczne zasobów w portalu zarządzania

Upewnij się, że wszystkie Twoje zasoby zostały skonfigurowane za pomocą ustawień diagnostycznych. Przejdź do **Monitor** w portalu i Otwórz **ustawień diagnostycznych**.

![Diagnostyczne dzienniki bloku portalu](./media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-nav.png)

Może być konieczne kliknięcie "Wszystkie usługi" można znaleźć w sekcji monitorowanie.

W tym miejscu możesz wyświetlić i filtrować wszystkie zasoby, które obsługują ustawień diagnostycznych, aby zobaczyć, jeśli mają włączoną diagnostykę. Zobacz też przejść również, jeśli wiele ustawień są ustawione na zasób i sprawdź, które konto magazynu, obszar nazw usługi Event Hubs i/lub obszar roboczy usługi Log Analytics, który dane przepływają do.

![Wyniki diagnostyki dzienników w portalu](./media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-blade.png)

Dodawanie ustawienia diagnostyczne powoduje wyświetlenie widoku ustawień diagnostycznych, w którym można włączyć, wyłączyć lub zmodyfikować ustawienia diagnostyczne dla wybranego zasobu.

## <a name="supported-services-categories-and-schemas-for-resource-diagnostic-logs"></a>Obsługiwane usługi, kategorie i schematy dla dzienników diagnostycznych zasobów

[Ten artykuł](monitoring-diagnostic-logs-schema.md) pełną listę obsługiwanych usług i Rejestruj kategorie i schematy używane przez te usługi.

## <a name="next-steps"></a>Kolejne kroki

* [Stream dzienników diagnostycznych zasobów do **usługi Event Hubs**](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [Zmień ustawienia diagnostyczne zasobów przy użyciu interfejsu API REST usługi Azure Monitor](https://msdn.microsoft.com/library/azure/dn931931.aspx)
* [Analizowanie dzienników z usługi Azure storage za pomocą usługi Log Analytics](../log-analytics/log-analytics-azure-storage.md)
