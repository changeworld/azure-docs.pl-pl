---
title: Omówienie dzienniki diagnostyczne platformy Azure
description: Dowiedz się, czym są dzienniki diagnostyczne platformy Azure i jak można je zrozumieć zdarzenia występujące w obrębie zasobu platformy Azure.
author: nkiest
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: nikiest
ms.subservice: logs
ms.openlocfilehash: 890f2224a4053ec8cad65b44b85eab0e31be3b64
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60236767"
---
# <a name="collect-and-consume-log-data-from-your-azure-resources"></a>Zbieranie i używanie dane dzienników z zasobów platformy Azure

## <a name="what-are-azure-monitor-diagnostic-logs"></a>Co to są dzienniki diagnostyczne usługi Azure Monitor

**Dzienniki diagnostyczne platformy Azure Monitor** są dzienniki emitowane przez to usługa platformy Azure, które zapewniają bogate, często dane dotyczące działania usługi. Usługa Azure Monitor sprawia, że dwa rodzaje dzienników diagnostycznych:
* **Dzienniki dzierżawy** — te dzienniki pochodzą z usługi na poziomie dzierżawy, które znajdują się poza subskrypcją platformy Azure, takich jak dzienniki usługi Azure Active Directory.
* **Dzienniki zasobów** — te dzienniki pochodzą z usług platformy Azure, które wdrażanie zasobów w ramach subskrypcji platformy Azure, takich jak sieciowe grupy zabezpieczeń lub konta magazynu.

    ![Dzienniki diagnostyczne zasobu a inne typy dzienników](./media/diagnostic-logs-overview/Diagnostics_Logs_vs_other_logs_v5.png)

Zawartość tych dzienników jest zależna od typu usługi i zasobów platformy Azure. Na przykład dwa typy dzienników diagnostycznych są liczniki reguł sieciowej grupy zabezpieczeń i inspekcji w usłudze Key Vault.

Dzienniki te różnią się od [dziennika aktywności](activity-logs-overview.md). Dziennik aktywności zapewnia wgląd w operacje, które zostały wykonane na zasoby w subskrypcji przy użyciu usługi Resource Manager, na przykład tworzenia maszyny wirtualnej lub usuwanie aplikacji logiki. Dziennik aktywności jest dziennika poziomu subskrypcji. Zasób poziom dzienniki diagnostyczne udostępniają szczegółowe dane operacji wykonywanych w ramach tego samego zasobu, na przykład wprowadzenie klucza tajnego z usługi Key Vault.

Dzienniki te różnią się także z poziomu systemu operacyjnego gościa, dzienniki diagnostyczne. System operacyjny gościa, dzienniki diagnostyczne są te zebranych przez agenta uruchomionego na maszynie wirtualnej lub innych obsługiwany typ zasobu. Dzienniki diagnostyczne na poziomie zasobów wymagają nie agenta i przechwytywania danych specyficznych dla zasobów z platformą Azure, natomiast dzienniki diagnostyczne na poziomie systemu operacyjnego gościa przechwytywanie danych z systemu operacyjnego i aplikacji uruchomionych na maszynie wirtualnej.

Nie wszystkie usługi pomocy technicznej opisane w tym miejscu dzienniki diagnostyczne. [Ten artykuł zawiera listę sekcji usług, które obsługują dzienniki diagnostyczne](./../../azure-monitor/platform/diagnostic-logs-schema.md).

## <a name="what-you-can-do-with-diagnostic-logs"></a>Co można zrobić za pomocą dzienników diagnostycznych
Oto kilka rzeczy, które można zrobić za pomocą dzienników diagnostycznych:

![Logiczne umieszczania dzienników diagnostycznych](./media/diagnostic-logs-overview/Diagnostics_Logs_Actions.png)

* Zapisywanie ich [ **konta magazynu** ](../../azure-monitor/platform/archive-diagnostic-logs.md) do wglądu, inspekcji czy ręcznie. Można określić przy użyciu czasu (w dniach) przechowywania **ustawień diagnostycznych zasobu**.
* [Stream im **usługi Event Hubs** ](diagnostic-logs-stream-event-hubs.md) dla pozyskiwania przez usługi innych firm lub rozwiązania analizy niestandardowych, takich jak usługi Power BI.
* Analizuj je za pomocą [usługi Azure Monitor](../../azure-monitor/platform/collect-azure-metrics-logs.md), gdzie dane są zapisywane bezpośrednio do usługi Azure Monitor bez konieczności najpierw zapisać dane do magazynu.  

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Można użyć konta magazynu lub przestrzeni nazw usługi Event Hubs, która nie znajduje się w tej samej subskrypcji co emitowane dzienniki. Użytkownik, który konfiguruje ustawienie, musi mieć odpowiedni dostęp RBAC do obu subskrypcji.

> [!NOTE]
>  Obecnie nie można zarchiwizować dzienniki przepływu sieciowych do konta magazynu, który znajduje się za zabezpieczonej sieci wirtualnej.

## <a name="diagnostic-settings"></a>Ustawienia diagnostyczne

Dzienniki diagnostyczne zasobu są skonfigurowane za pomocą ustawień diagnostycznych zasobu. Dzienniki diagnostyczne dzierżawy są skonfigurowane przy użyciu ustawienia diagnostyczne dzierżawy. **Ustawienia diagnostyczne** kontroli usługi:

* Której dzienniki diagnostyczne i metryki są wysyłane (konto magazynu, usługa Event Hubs i/lub usługi Azure Monitor).
* Kategorie dziennika, które są wysyłane i tego, czy też są wysyłane dane metryk.
* Jak długo każda kategoria dziennika ma być przechowywana na koncie magazynu
    - Wpisanie wartości zero oznacza, że dzienniki są przechowywane w nieskończoność. W przeciwnym razie wartość może być dowolną liczbę dni z zakresu od 1 do 365.
    - Jeśli ustawiono zasady przechowywania, ale przechowywania dzienników na koncie magazynu jest wyłączona (na przykład, jeśli tylko są zaznaczone opcje usługi Event Hubs lub usługi Log Analytics), zasad przechowywania przyniosło żadnego skutku.
    - Zasady przechowywania są stosowane dziennie, aby na koniec dnia (UTC), dzienniki w dzień, w którym jest teraz, po przekroczeniu przechowywania zasady zostaną usunięte. Na przykład jeśli masz zasady przechowywania w jeden dzień, na początku dnia już dziś dzienników z wczoraj zanim dnia zostaną usunięte. Proces usuwania rozpoczyna się od północy czasu UTC, ale należy pamiętać, że może upłynąć do 24 godzin dla dzienników są usuwane z konta magazynu.

Te ustawienia są łatwo konfigurować na podstawie ustawień diagnostycznych w portalu, za pomocą poleceń programu Azure PowerShell i interfejsu wiersza polecenia lub przy użyciu [interfejsu API REST usługi Azure Monitor](https://docs.microsoft.com/rest/api/monitor/).

> [!NOTE]
> Wysyłanie metryk wielowymiarowych za pomocą ustawień diagnostycznych nie jest obecnie obsługiwane. Metryki wielowymiarowe są eksportowane jako spłaszczone metryki jednowymiarowe z wartościami zagregowanymi we wszystkich wymiarach.
>
> *Na przykład*: metrykę „Komunikaty przychodzące” w centrum zdarzeń można przeglądać i przedstawiać na wykresie na poziomie pojedynczej kolejki. Jednak w przypadku eksportowania za pomocą ustawień diagnostycznych metryka ta jest przedstawiana jako wszystkie komunikaty przychodzące we wszystkich kolejkach w centrum zdarzeń.
>
>

## <a name="how-to-enable-collection-of-diagnostic-logs"></a>Jak włączyć zbieranie dzienników diagnostycznych

Można włączyć zbierania dzienników diagnostycznych [jako część tworzenia zasobu w szablonie usługi Resource Manager](./../../azure-monitor/platform/diagnostic-logs-stream-template.md) lub po utworzeniu zasobu ze strony tego zasobu w portalu. Można również włączyć kolekcję w dowolnym momencie za pomocą poleceń programu Azure PowerShell lub interfejsu wiersza polecenia lub przy użyciu interfejsu API REST usługi Azure Monitor.

> [!TIP]
> Instrukcje te mogą nie mieć zastosowania bezpośrednio do każdego zasobu. Zobacz linki schematu u dołu tej strony, aby zrozumieć specjalne kroki, które mogą mieć zastosowanie do niektórych typów zasobów.

### <a name="enable-collection-of-diagnostic-logs-in-the-portal"></a>Włączanie zbierania dzienników diagnostycznych w portalu

Po utworzeniu zasobu, przechodząc do określonego zasobu lub przechodząc do usługi Azure Monitor, można włączyć zbierania dzienników diagnostycznych zasobów w witrynie Azure portal. Aby je włączyć za pomocą usługi Azure Monitor:

1. W [witryny Azure portal](https://portal.azure.com), przejdź do usługi Azure Monitor i kliknij **ustawień diagnostycznych**

    ![Monitorowanie sekcji usługi Azure Monitor](media/diagnostic-logs-overview/diagnostic-settings-blade.png)

2. Opcjonalnie filtrować listę według grupy zasobów lub typ zasobu, a następnie kliknij na zasób, dla którego chcesz skonfigurować ustawienie diagnostyczne.

3. Jeśli ustawienia nie istnieje w zasobie wybrany, zostanie wyświetlony monit, aby utworzyć ustawienie. Kliknij przycisk "Włącz diagnostykę."

   ![Dodaj ustawienie diagnostyczne — nie istniejące ustawienia](media/diagnostic-logs-overview/diagnostic-settings-none.png)

   W przypadku ustawienia istniejące na zasób zostanie wyświetlona lista ustawień już skonfigurowany dla tego zasobu. Kliknij pozycję "Dodaj ustawienie diagnostyczne".

   ![Dodaj ustawienie diagnostyczne — istniejące ustawienia](media/diagnostic-logs-overview/diagnostic-settings-multiple.png)

3. Nadaj nazwę ustawienia, zaznacz pola wyboru dla każdej lokalizacji docelowej, do której chcesz wysyłać dane i Konfiguruj zasobu, który jest używany dla każdej lokalizacji docelowej. Opcjonalnie można ustawić liczbę dni przechowywania tych dzienników przy użyciu **przechowywania (dni)** suwaki (dotyczy tylko do miejsca docelowego konta magazynu). Dzienniki na wpisanie wartości zero są przechowywane w nieskończoność.

   ![Dodaj ustawienie diagnostyczne — istniejące ustawienia](media/diagnostic-logs-overview/diagnostic-settings-configure.png)

4. Kliknij pozycję **Zapisz**.

Po kilku chwilach nowe ustawienie jest wyświetlane na liście ustawień dla tego zasobu, a dzienniki diagnostyczne są wysyłane do określonego miejsca docelowe, zaraz po wygenerowaniu nowych danych zdarzenia.

Ustawienia diagnostyczne dzierżawy można skonfigurować tylko w bloku portalu dzierżawcy usługi — te ustawienia nie są wyświetlane w bloku ustawień diagnostycznych usługi Azure Monitor. Na przykład dzienników inspekcji usługi Azure Active Directory są skonfigurowane, klikając **Eksportuj ustawienia danych** w bloku dzienników inspekcji.

![Ustawienia diagnostyczne usługi AAD](./media/diagnostic-logs-overview/diagnostic-settings-aad.png)

### <a name="enable-collection-of-resource-diagnostic-logs-via-powershell"></a>Włączanie zbierania dzienników diagnostycznych zasobów za pomocą programu PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Aby włączyć zbieranie dzienników diagnostycznych zasobów za pomocą programu Azure PowerShell, użyj następujących poleceń:

Aby włączyć magazyn dzienniki diagnostyczne na koncie magazynu, użyj tego polecenia:

```powershell
Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
```

Identyfikator konta magazynu jest identyfikator zasobu dla konta magazynu, do którego chcesz wysłać dzienniki.

Aby włączyć strumieniowe przesyłanie dzienników diagnostycznych do Centrum zdarzeń, użyj tego polecenia:

```powershell
Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your Service Bus rule id] -Enabled $true
```

Identyfikator reguły magistrali usług jest ciągiem w formacie: `{Service Bus resource ID}/authorizationrules/{key name}`.

Aby włączyć wysyłanie dzienników diagnostycznych do obszaru roboczego usługi Log Analytics, użyj tego polecenia:

```powershell
Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
```

Możesz uzyskać identyfikator zasobu obszaru roboczego usługi Log Analytics przy użyciu następującego polecenia:

```powershell
(Get-AzOperationalInsightsWorkspace).ResourceId
```

Można połączyć te parametry, aby włączyć wiele opcji danych wyjściowych.

Obecnie nie można skonfigurować dzierżawy ustawień diagnostycznych przy użyciu programu Azure PowerShell.

### <a name="enable-collection-of-resource-diagnostic-logs-via-the-azure-cli"></a>Włączanie zbierania dzienników diagnostycznych zasobów za pomocą wiersza polecenia platformy Azure

Aby włączyć zbieranie dzienników diagnostycznych zasobów za pomocą wiersza polecenia platformy Azure, należy użyć [tworzenie az monitor diagnostic-settings](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) polecenia.

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

Obecnie nie można skonfigurować dzierżawy ustawień diagnostycznych przy użyciu interfejsu wiersza polecenia.

### <a name="enable-collection-of-resource-diagnostic-logs-via-rest-api"></a>Włączanie zbierania dzienników diagnostycznych zasobów za pomocą interfejsu API REST

Aby zmienić ustawień diagnostycznych przy użyciu interfejsu API REST usługi Azure Monitor, zobacz [w tym dokumencie](https://docs.microsoft.com/rest/api/monitor/).

Obecnie nie można skonfigurować dzierżawy ustawień diagnostycznych przy użyciu interfejsu API REST usługi Azure Monitor.

## <a name="manage-resource-diagnostic-settings-in-the-portal"></a>Ustawienia diagnostyczne zasobów w portalu zarządzania

Upewnij się, że wszystkie Twoje zasoby zostały skonfigurowane za pomocą ustawień diagnostycznych. Przejdź do **Monitor** w portalu i Otwórz **ustawień diagnostycznych**.

![Diagnostyczne dzienniki bloku portalu](./media/diagnostic-logs-overview/diagnostic-settings-nav.png)

Może być konieczne kliknięcie "Wszystkie usługi" można znaleźć w sekcji monitorowanie.

W tym miejscu możesz wyświetlić i filtrować wszystkie zasoby, które obsługują ustawień diagnostycznych, aby zobaczyć, jeśli mają włączoną diagnostykę. Zobacz też przejść również, jeśli wiele ustawień są ustawione na zasób i sprawdź, które konto magazynu, obszar nazw usługi Event Hubs i/lub obszar roboczy usługi Log Analytics, który dane przepływają do.

![Wyniki diagnostyki dzienników w portalu](./media/diagnostic-logs-overview/diagnostic-settings-blade.png)

Dodawanie ustawienia diagnostyczne powoduje wyświetlenie widoku ustawień diagnostycznych, w którym można włączyć, wyłączyć lub zmodyfikować ustawienia diagnostyczne dla wybranego zasobu.

## <a name="supported-services-categories-and-schemas-for-diagnostic-logs"></a>Obsługiwane usługi, kategorie i schematy dla dzienników diagnostycznych

[Ten artykuł](../../azure-monitor/platform/diagnostic-logs-schema.md) pełną listę obsługiwanych usług i Rejestruj kategorie i schematy używane przez te usługi.

## <a name="next-steps"></a>Kolejne kroki

* [Stream dzienników diagnostycznych zasobów do **usługi Event Hubs**](diagnostic-logs-stream-event-hubs.md)
* [Zmień ustawienia diagnostyczne zasobów przy użyciu interfejsu API REST usługi Azure Monitor](https://docs.microsoft.com/rest/api/monitor/)
* [Analizowanie dzienników z usługi Azure storage z usługą Azure Monitor](collect-azure-metrics-logs.md)
