---
title: Przegląd dzienników diagnostycznych platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, co to są dzienników diagnostycznych platformy Azure i jak ich używać zrozumienie zdarzenia występujące w obrębie zasobów platformy Azure.
author: johnkemnetz
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: fe8887df-b0e6-46f8-b2c0-11994d28e44f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2018
ms.author: johnkem; magoedte
ms.openlocfilehash: 884acc4885da3a321477c51f6d7b76748d797d9b
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="collect-and-consume-log-data-from-your-azure-resources"></a>Zbierania i wykorzystywania danych dziennika z zasobów platformy Azure

## <a name="what-are-azure-resource-diagnostic-logs"></a>Co to są dzienniki diagnostyczne zasobów platformy Azure

**Azure dzienników diagnostycznych poziom zasobów** są dzienniki emitowane przez zasób, które zawierają rozbudowane, często dane dotyczące operacji tego zasobu. Zawartość tych dzienników zależy od typu zasobu. Na przykład liczników reguły grupy zabezpieczeń sieci i usługi Key Vault inspekcje są dwie kategorie dzienników zasobów.

Dzienniki diagnostyczne poziom zasobów różnią się od [dziennik aktywności](monitoring-overview-activity-logs.md). Dziennik aktywności zapewnia wgląd w operacje wykonywane na zasobów w ramach subskrypcji, za pomocą Menedżera zasobów, na przykład tworzenia maszyny wirtualnej lub usuwanie aplikacji logiki. Dziennik aktywności jest dziennika poziomu subskrypcji. Dzienniki diagnostyczne poziom zasobów zapewniają wgląd w operacji wykonanych w ramach tego zasobu, na przykład pobierania klucza tajnego z magazynu kluczy.

Dzienniki diagnostyczne poziom zasobów również różnią się od dzienników diagnostycznych na poziomie systemu operacyjnego gościa. Dzienniki diagnostyczne systemu operacyjnego gościa znajdują się te zebranych przez agenta, które działają w ramach maszyny wirtualnej lub inne obsługiwane typu zasobu. Poziom zasobów dzienników diagnostycznych wymagają żadne dane specyficzne dla zasobów agenta do przechwycenia z platformą Azure, podczas dzienników diagnostycznych na poziomie systemu operacyjnego gościa przechwycenia danych z systemu operacyjnego i aplikacji uruchomionych na maszynie wirtualnej.

Nie wszystkie zasoby obsługi nowego typu zasobu dzienników diagnostycznych opisanych tutaj. Ten artykuł zawiera listę typów zasobów, które obsługują nowe dzienniki diagnostyczne zasobów na poziomie sekcji.

![Diagnostyka zasobów rejestruje vs innych typów dzienników ](./media/monitoring-overview-of-diagnostic-logs/Diagnostics_Logs_vs_other_logs_v5.png)

## <a name="what-you-can-do-with-resource-level-diagnostic-logs"></a>Co należy zrobić z dzienników diagnostycznych poziom zasobów
Poniżej podano niektóre czynności, które można wykonywać z dzienników diagnostycznych zasobu:

![Logiczne umieszczania dzienników diagnostycznych zasobu](./media/monitoring-overview-of-diagnostic-logs/Diagnostics_Logs_Actions.png)

* Zapisywanie ich [ **konta magazynu** ](monitoring-archive-diagnostic-logs.md) inspekcji inspekcji lub ręcznie. Można określić za pomocą czasu (w dniach) przechowywania **ustawień diagnostycznych zasobu**.
* [Do strumienia **usługi Event Hubs** ](monitoring-stream-diagnostic-logs-to-event-hubs.md) dla wprowadzanie przez usługi innej firmy lub rozwiązania analizy niestandardowych, takich jak usługi Power BI.
* Analizuj je za pomocą [analizy dzienników](../log-analytics/log-analytics-azure-storage.md)

Można użyć konta magazynu lub przestrzeni nazw usługi Event Hubs, która nie znajduje się w tej samej subskrypcji co jeden emitowanie dzienników. Użytkownik, który konfiguruje ustawienie musi mieć odpowiedni dostęp RBAC do obu subskrypcji.

## <a name="resource-diagnostic-settings"></a>Ustawienia diagnostyczne zasobu

Dzienniki diagnostyczne zasobu z systemem innym niż — obliczeń się, że zasoby są skonfigurowane przy użyciu ustawień diagnostycznych zasobu. **Ustawienia diagnostyczne zasobu** formantów zasobów:

* W przypadku dzienników diagnostycznych zasobu i metryki wysyłania (konto magazynu, centra zdarzeń i/lub Log Analytics).
* Kategorie dziennika, które są wysyłane i czy dane są także wysyłane.
* Jak długo każdej kategorii dziennika powinny zostać zachowane na koncie magazynu
    - Przechowywanie 0 oznacza, że dzienniki są przechowywane w nieskończoność. W przeciwnym razie wartość może być dowolną liczbę dni od 1 do 2147483647.
    - Jeśli zasady przechowywania są skonfigurowane, ale przechowywanie dzienniki na koncie magazynu jest wyłączone (na przykład, jeśli tylko są zaznaczone opcje usługi Event Hubs lub analizy dzienników), zasad przechowywania nie obowiązują.
    - Zasady przechowywania są zastosowane na dni, więc pod koniec dnia (UTC), dzienniki od dnia, która jest teraz poza przechowywania zasad są usuwane. Na przykład jeśli masz zasady przechowywania jeden dzień na początku dnia dzisiaj dzienniki na wczoraj zanim dzień zostaną usunięte.

Te ustawienia można łatwo skonfigurować za pomocą ustawień diagnostycznych dla zasobu w portalu Azure, za pomocą programu Azure PowerShell i polecenia interfejsu wiersza polecenia lub za pośrednictwem [interfejsu API REST Monitor Azure](https://msdn.microsoft.com/library/azure/dn931943.aspx).

> [!NOTE]
> Wysyłanie metryki wielowymiarowej za pomocą ustawień diagnostycznych nie jest obecnie obsługiwane. Metryka z wymiarów są eksportowane jako spłaszczone pojedynczego metryki wymiarów, zagregowane WE wartości wymiaru.
>
> *Na przykład*: metryka "Komunikatów przychodzących" w Centrum zdarzeń można przedstawione i wykresie na na poziomie kolejki. Jednak podczas eksportowania za pomocą ustawień diagnostycznych, które Metryka będą reprezentowane jako komunikaty przychodzące we wszystkich kolejek zdarzeń koncentratora.
>
>

> [!WARNING]
> Dzienniki diagnostyczne i metryki z warstwy systemu operacyjnego gościa użycia zasobów (na przykład maszyny wirtualne lub sieci szkieletowej usług) obliczeń [odrębny mechanizm konfiguracji i wybór wyjścia](../azure-diagnostics.md).

## <a name="how-to-enable-collection-of-resource-diagnostic-logs"></a>Jak włączyć kolekcję dzienników diagnostycznych zasobu

Można włączyć zbierania dzienników diagnostycznych zasobu [podczas tworzenia zasobu w szablonie usługi Resource Manager](./monitoring-enable-diagnostic-logs-using-template.md) lub po utworzeniu zasobu ze strony tego zasobu w portalu. Można również włączyć kolekcji w dowolnym momencie za pomocą poleceń programu Azure PowerShell lub interfejsu wiersza polecenia lub interfejsu API REST Azure monitora.

> [!TIP]
> Te instrukcje nie może zastosować bezpośrednio do każdego zasobu. Skorzystaj z łączy schematu u dołu tej strony, aby zrozumieć specjalne kroki, które mogą być zastosowane do określonych typów zasobów.

### <a name="enable-collection-of-resource-diagnostic-logs-in-the-portal"></a>Włącz zbieranie dzienników diagnostycznych zasobu w portalu

Po utworzeniu zasobu, przechodząc do określonego zasobu lub przechodząc do monitora Azure, można włączyć zbierania dzienników diagnostycznych zasobu w portalu Azure. Aby je włączyć za pomocą monitora Azure:

1. W [portalu Azure](http://portal.azure.com), przejdź do monitora Azure i kliknij na **ustawień diagnostycznych**

    ![Monitorowanie sekcji Azure Monitor](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-blade.png)

2. Opcjonalnie Filtruj listę według grupy zasobów lub typ zasobu, a następnie kliknij na zasobie, dla której chcesz skonfigurować ustawienie diagnostyczne.

3. Jeśli nie istnieją żadne ustawienia zasobu wybrany, zostanie wyświetlony monit, aby utworzyć ustawienie. Kliknij pozycję "Włącz diagnostykę."

   ![Dodaj ustawienie diagnostyczne - żadnych istniejących ustawień](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-none.png)

   W przypadku istniejących ustawień na zasobie, zostanie wyświetlona lista ustawień już skonfigurowana dla tego zasobu. Kliknij przycisk "Dodaj ustawienie diagnostyczne".

   ![Dodaj ustawienie diagnostyczne — istniejące ustawienia](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-multiple.png)

3. Sprawdź pola dla każdej lokalizacji docelowej, do którego chcesz wysyłać dane i skonfigurować, którego zasobu jest używana dla każdej lokalizacji docelowej, nadaj nazwę ustawienia. Opcjonalnie można ustawić liczbę dni, aby zachować te dzienniki przy użyciu **przechowywania (dni)** suwaki (dotyczy tylko miejsce docelowe konto magazynu). Przechowywanie zero dni są przechowywane dzienniki nieskończoność.

   ![Dodaj ustawienie diagnostyczne — istniejące ustawienia](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-configure.png)

4. Kliknij pozycję **Zapisz**.

Po kilku chwilach nowe ustawienie jest wyświetlane na liście ustawień dla tego zasobu, a dzienników diagnostycznych są wysyłane do określonego miejsca docelowe zaraz po wygenerowaniu nowych danych zdarzenia.

### <a name="enable-collection-of-resource-diagnostic-logs-via-powershell"></a>Włącz zbieranie dzienników diagnostycznych zasobów za pomocą programu PowerShell

Aby włączyć zbieranie dzienników diagnostycznych zasobów za pomocą programu Azure PowerShell, użyj następujących poleceń:

Aby włączyć magazyn dzienników diagnostycznych na konto magazynu, należy użyć tego polecenia:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
```

Identyfikator konta magazynu jest identyfikator zasobu dla konta magazynu, do którego chcesz wysłać dzienniki.

Do przesyłania strumieniowego dzienników diagnostycznych do Centrum zdarzeń, użyj tego polecenia:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your Service Bus rule id] -Enabled $true
```

Identyfikator reguły magistrali usług jest ciąg w formacie: `{Service Bus resource ID}/authorizationrules/{key name}`.

Aby włączyć wysyłanie dzienników diagnostycznych do obszaru roboczego analizy dzienników, użyj tego polecenia:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
```

Można uzyskać Identyfikatora zasobu obszaru roboczego analizy dzienników przy użyciu następującego polecenia:

```powershell
(Get-AzureRmOperationalInsightsWorkspace).ResourceId
```

Można połączyć tych parametrów, aby włączyć wiele opcji danych wyjściowych.

### <a name="enable-collection-of-resource-diagnostic-logs-via-azure-cli-20"></a>Włącz zbieranie dzienników diagnostycznych zasobów za pomocą usługi Azure CLI 2.0

Aby włączyć zbieranie dzienników diagnostycznych zasobu za pośrednictwem 2.0 interfejsu wiersza polecenia Azure, należy użyć [utworzyć ustawienia diagnostyki az monitora](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) polecenia.

Aby włączyć magazyn dzienników diagnostycznych na koncie magazynu:

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

`--resource-group` Argument jest tylko w przypadku wymaganych `--storage-account` nie jest identyfikatorem obiektu.

Aby włączyć przesyłanie strumieniowe dzienników diagnostycznych do Centrum zdarzeń:

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

Aby włączyć wysyłanie dzienników diagnostycznych do obszaru roboczego analizy dzienników:

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

`--resource-group` Argument jest tylko w przypadku wymaganych `--workspace` nie jest Identyfikatorem obiektu

Wszystkie polecenia, można dodać dodatkowe kategorie do dzienników diagnostycznych przez dodanie słowniki do tablicy JSON przekazany jako `--logs` parametru. Możesz połączyć ze sobą `--storage-account`, `--event-hub`, i `--workspace` można włączyć wiele opcji danych wyjściowych.

### <a name="enable-collection-of-resource-diagnostic-logs-via-rest-api"></a>Włącz zbieranie dzienników diagnostycznych zasobów za pomocą interfejsu API REST

Aby zmienić ustawienia diagnostyki za pomocą interfejsu API REST Monitor Azure, zobacz [tego dokumentu](https://msdn.microsoft.com/library/azure/dn931931.aspx).

## <a name="manage-resource-diagnostic-settings-in-the-portal"></a>Ustawienia diagnostyczne zasobu w portalu zarządzania

Upewnij się, że wszystkie zasoby zostały skonfigurowane przy użyciu ustawień diagnostycznych. Przejdź do **Monitor** w portalu i Otwórz **ustawień diagnostycznych**.

![Diagnostycznych dzienników bloku w portalu](./media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-nav.png)

Może być konieczne kliknięcie pozycji "Wszystkie usługi" nie można znaleźć sekcji monitora.

W tym miejscu możesz wyświetlić i filtrować wszystkie zasoby, które obsługują ustawień diagnostycznych, aby zobaczyć, czy mają włączoną diagnostykę. Możesz również przejść do Zobacz Jeśli wiele ustawień są ustawiane w zasobie i sprawdź, które konta magazynu, przestrzeni nazw usługi Event Hubs i/lub obszar roboczy analizy dzienników, które dane są przesyłane do.

![Wyniki diagnostyki dzienniki w portalu](./media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-blade.png)

Dodawanie ustawienie diagnostyczne wyświetlenie widoku ustawień diagnostycznych, w którym można włączyć, wyłączyć lub zmodyfikować ustawienia diagnostyczne dla wybranego zasobu.

## <a name="supported-services-categories-and-schemas-for-resource-diagnostic-logs"></a>Obsługiwane usługi, kategorie i schematy do dzienników diagnostycznych zasobu

[Znajduje się w artykule](monitoring-diagnostic-logs-schema.md) pełną listę obsługiwanych usług i kategorie dziennika i schematy używane przez te usługi.

## <a name="next-steps"></a>Kolejne kroki

* [Strumień zasobu dzienników diagnostycznych do **centra zdarzeń**](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [Zmień ustawienia diagnostyczne zasobu przy użyciu interfejsu API REST Azure monitora](https://msdn.microsoft.com/library/azure/dn931931.aspx)
* [Analizowanie dzienników z usługi Azure storage z analizy dzienników](../log-analytics/log-analytics-azure-storage.md)
