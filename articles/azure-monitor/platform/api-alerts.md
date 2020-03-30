---
title: Korzystanie z interfejsu API REST alertów usługi Log Analytics
description: Interfejs API REST alertów usługi Log Analytics umożliwia tworzenie alertów i zarządzanie nimi w usłudze Log Analytics, która jest częścią usługi Log Analytics.  Ten artykuł zawiera szczegółowe informacje na temat interfejsu API i kilka przykładów wykonywania różnych operacji.
ms.subservice: logs
ms.topic: conceptual
ms.date: 07/29/2018
ms.openlocfilehash: a85dad2ba638505233e5df769e55fa5bd7b8dafd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665004"
---
# <a name="create-and-manage-alert-rules-in-log-analytics-with-rest-api"></a>Tworzenie reguł alertów i zarządzanie nimi w usłudze Log Analytics za pomocą interfejsu REST API 

Interfejs API REST alertów usługi Log Analytics umożliwia tworzenie alertów i zarządzanie nimi w usłudze Log Analytics.  Ten artykuł zawiera szczegółowe informacje na temat interfejsu API i kilka przykładów wykonywania różnych operacji.

> [!IMPORTANT]
> Zgodnie [z wcześniejszymi zapowiedziami,](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/)obszary robocze analizy dzienników utworzone po *1 czerwca 2019* r. — będą mogły zarządzać regułami alertów przy użyciu **tylko** interfejsu [API REST](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/)platformy Azure scheduledQueryRules , [szablonu manangera zasobów platformy Azure](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-azure-resource-template) i polecenia [cmdlet programu PowerShell](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-powershell). Klienci mogą łatwo [przełączyć preferowane środki zarządzania regułami alertów](../../azure-monitor/platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api) dla starszych obszarów roboczych, aby wykorzystać usługę Azure Monitor scheduledQueryRules jako domyślną i uzyskać wiele [nowych korzyści,](../../azure-monitor/platform/alerts-log-api-switch.md#benefits-of-switching-to-new-azure-api) takich jak możliwość używania natywnych poleceń cmdlet programu PowerShell, wydłużony okres odnośnego okresu w regułach, tworzenie reguł w oddzielnej grupie zasobów lub subskrypcji i wiele więcej.

Interfejs API REST wyszukiwania usługi Log Analytics jest restful i można uzyskać dostęp za pośrednictwem interfejsu API REST usługi Azure Resource Manager. W tym dokumencie znajdziesz przykłady, gdzie interfejs API jest dostępny z wiersza polecenia programu PowerShell przy użyciu [ARMClient](https://github.com/projectkudu/ARMClient), narzędzia wiersza polecenia open source, które upraszcza wywoływanie interfejsu API usługi Azure Resource Manager. Korzystanie z ARMClient i PowerShell jest jedną z wielu opcji dostępu do interfejsu API wyszukiwania usługi Log Analytics. Dzięki tym narzędziom można korzystać z interfejsu API restful usługi Azure Resource Manager do nawiązywać wywołania obszarów roboczych usługi Log Analytics i wykonywać polecenia wyszukiwania w nich. Interfejs API będzie wyprowadzać wyniki wyszukiwania w formacie JSON, co pozwala na korzystanie z wyników wyszukiwania na wiele różnych sposobów programowo.

## <a name="prerequisites"></a>Wymagania wstępne
Obecnie alerty można tworzyć tylko przy użyciu zapisanego wyszukiwania w usłudze Log Analytics.  Aby uzyskać więcej informacji, można znaleźć się w [interfejsie API REST wyszukiwania dziennika.](../../azure-monitor/log-query/log-query-overview.md)

## <a name="schedules"></a>Harmonogramy
Zapisane wyszukiwanie może mieć jeden lub więcej harmonogramów. Harmonogram określa, jak często wyszukiwanie jest uruchamiane i przedział czasu, w którym kryteria są identyfikowane.
Harmonogramy mają właściwości w poniższej tabeli.

| Właściwość | Opis |
|:--- |:--- |
| Interval |Jak często wyszukiwanie jest uruchamiane. Mierzona w minutach. |
| Program QueryTimeSpan |Przedział czasu, w którym kryteria są oceniane. Musi być równa lub większa niż Interwał. Mierzona w minutach. |
| Wersja |Używana wersja interfejsu API.  Obecnie należy to zawsze ustawić na 1. |

Rozważmy na przykład kwerendę zdarzeń z interwałem 15 minut i przedział czasu 30 minut. W takim przypadku kwerenda będzie uruchamiana co 15 minut, a alert zostanie wyzwolony, jeśli kryteria nadal będą spełniać wartość true w ciągu 30-minutowego zakresu.

### <a name="retrieving-schedules"></a>Pobieranie harmonogramów
Użyj Get metody, aby pobrać wszystkie harmonogramy dla zapisanego wyszukiwania.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules?api-version=2015-03-20

Użyj Get metody z identyfikatorem harmonogramu, aby pobrać określony harmonogram dla zapisanego wyszukiwania.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20

Poniżej znajduje się przykładowa odpowiedź dla harmonogramu.

```json
{
    "value": [{
        "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/sampleRG/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace/savedSearches/0f0f4853-17f8-4ed1-9a03-8e888b0d16ec/schedules/a17b53ef-bd70-4ca4-9ead-83b00f2024a8",
        "etag": "W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\"",
        "properties": {
            "Interval": 15,
            "QueryTimeSpan": 15,
            "Enabled": true,
        }
    }]
}
```

### <a name="creating-a-schedule"></a>Tworzenie harmonogramu
Użyj Put metody z unikatowym identyfikatorem harmonogramu, aby utworzyć nowy harmonogram.  Dwa harmonogramy nie mogą mieć tego samego identyfikatora, nawet jeśli są skojarzone z różnymi zapisanymi wyszukiwaniami.  Podczas tworzenia harmonogramu w konsoli usługi Log Analytics tworzony jest identyfikator GUID dla identyfikatora harmonogramu.

> [!NOTE]
> Nazwa wszystkich zapisanych wyszukiwań, harmonogramów i akcji utworzonych za pomocą interfejsu API usługi Log Analytics musi być mała literą.

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Enabled':'true' } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson

### <a name="editing-a-schedule"></a>Edytowanie harmonogramu
Użyj Put metody z istniejącym identyfikatorem harmonogramu dla tego samego zapisanego wyszukiwania, aby zmodyfikować ten harmonogram; w poniższym harmonogramie jest wyłączony. Treść wniosku musi zawierać *etag* harmonogramu.

      $scheduleJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\""','properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Enabled':'false' } }"
      armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson


### <a name="deleting-schedules"></a>Usuwanie harmonogramów
Użyj metody Usuń z identyfikatorem harmonogramu, aby usunąć harmonogram.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20


## <a name="actions"></a>Akcje
Harmonogram może mieć wiele akcji. Akcja może zdefiniować jeden lub więcej procesów do wykonania, takich jak wysyłanie poczty lub uruchamianie elementu runbook, lub może zdefiniować próg, który określa, kiedy wyniki wyszukiwania odpowiadają niektórym kryteriom.  Niektóre akcje zdefiniują oba tak, aby procesy były wykonywane po osiągnięciu progu.

Wszystkie akcje mają właściwości w poniższej tabeli.  Różne typy alertów mają różne dodatkowe właściwości, które są opisane poniżej.

| Właściwość | Opis |
|:--- |:--- |
| `Type` |Typ akcji.  Obecnie możliwe wartości to Alert i Webhook. |
| `Name` |Wyświetlana nazwa alertu. |
| `Version` |Używana wersja interfejsu API.  Obecnie należy to zawsze ustawić na 1. |

### <a name="retrieving-actions"></a>Pobieranie akcji

Użyj Get metody, aby pobrać wszystkie akcje dla harmonogramu.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules/{Schedule ID}/actions?api-version=2015-03-20

Użyj Get metody z identyfikatorem akcji, aby pobrać określoną akcję dla harmonogramu.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/actions/{Action ID}?api-version=2015-03-20

### <a name="creating-or-editing-actions"></a>Tworzenie lub edytowanie akcji
Użyj Put metody o identyfikatorze akcji, który jest unikatowy dla harmonogramu, aby utworzyć nową akcję.  Podczas tworzenia akcji w konsoli usługi Log Analytics identyfikator GUID jest dla identyfikatora akcji.

> [!NOTE]
> Nazwa wszystkich zapisanych wyszukiwań, harmonogramów i akcji utworzonych za pomocą interfejsu API usługi Log Analytics musi być mała literą.

Użyj Put metody z istniejącym identyfikatorem akcji dla tego samego zapisanego wyszukiwania, aby zmodyfikować ten harmonogram.  Treść wniosku musi zawierać etag harmonogramu.

Format żądania do tworzenia nowej akcji różni się w zależności od typu akcji, więc te przykłady znajdują się w poniższych sekcjach.

### <a name="deleting-actions"></a>Usuwanie akcji

Użyj Metody Usuń z identyfikatorem akcji, aby usunąć akcję.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/Actions/{Action ID}?api-version=2015-03-20

### <a name="alert-actions"></a>Akcje alertów
Harmonogram powinien mieć jedną i tylko jedną akcję alertu.  Akcje alertu mają jedną lub więcej sekcji w poniższej tabeli.  Każdy z nich jest opisany bardziej szczegółowo poniżej.

| Sekcja | Opis | Sposób użycia |
|:--- |:--- |:--- |
| Próg |Kryteria dotyczące uruchomienia akcji.| Wymagane dla każdego alertu, przed lub po ich rozszer. |
| Ważność |Etykieta używana do klasyfikowania alertu po wyzwoleniu.| Wymagane dla każdego alertu, przed lub po ich rozszer. |
| Pomiń |Opcja zatrzymania powiadomień z alertu. | Opcjonalnie dla każdego alertu, przed lub po ich rozszer. |
| Grupy akcji |Identyfikatory usługi Azure ActionGroup, w których określono wymagane akcje, takie jak - Wiadomości e-mail, SMSs, połączenia głosowe, elementy webhook, elementy runbook automatyzacji, łączniki ITSM itp.| Wymagane po rozszerym rozszerzaniu alertów na platformę Azure|
| Dostosowywanie akcji|Modyfikowanie standardowego wyjścia dla wybranych akcji z grupy ActionGroup| Opcjonalnie dla każdego alertu, może służyć po alerty są rozszerzone na platformę Azure. |

### <a name="thresholds"></a>Progi
Akcja alertu powinna mieć jeden i tylko jeden próg.  Gdy wyniki zapisanego wyszukiwania odpowiadają progu w akcji skojarzonej z tym wyszukiwaniem, uruchamiane są inne procesy w tej akcji.  Akcja może również zawierać tylko próg, dzięki czemu może być używana z akcjami innych typów, które nie zawierają progów.

Progi mają właściwości w poniższej tabeli.

| Właściwość | Opis |
|:--- |:--- |
| `Operator` |Operator dla porównania progów. <br> gt = większa niż <br> lt = mniej niż |
| `Value` |Wartość progu. |

Rozważmy na przykład kwerendę zdarzeń z interwałem 15 minut, przedział czasu 30 minut i próg większy niż 10. W takim przypadku kwerenda będzie uruchamiana co 15 minut, a alert zostanie wyzwolony, jeśli zwróci 10 zdarzeń, które zostały utworzone w ciągu 30-minutowego zakresu.

Poniżej przedstawiono przykładową odpowiedź dla akcji z tylko próg.  

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My threshold action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Version": 1
    }

Użyj Put metody z unikatowym identyfikatorem akcji, aby utworzyć nową akcję progową dla harmonogramu.  

    $thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

Użyj Put metody z istniejącym identyfikatorem akcji, aby zmodyfikować akcję progową dla harmonogramu.  Treść wniosku musi zawierać etag działania.

    $thresholdJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

#### <a name="severity"></a>Ważność
Usługa Log Analytics umożliwia klasyfikowanie alertów do kategorii, aby ułatwić zarządzanie i klasyfikowanie. Zdefiniowana ważność alertu to: informacje, ostrzeżenie i krytyczne. Są one mapowane do znormalizowanej skali ważności alertów platformy Azure w następujący sposób:

|Poziom ważności analizy dzienników  |Poziom ważności alertów platformy Azure  |
|---------|---------|
|`critical` |Sev 0 (Sev) 0|
|`warning` |Sev 1 (Włask.)|
|`informational` | Ważność 2|

Poniżej przedstawiono przykładową odpowiedź dla akcji z tylko próg i ważność. 

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My threshold action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Severity": "critical",
        "Version": 1    }

Użyj Put metody z unikatowym identyfikatorem akcji, aby utworzyć nową akcję dla harmonogramu z ważnością.  

    $thresholdWithSevJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson

Użyj Put metody z istniejącym identyfikatorem akcji, aby zmodyfikować akcję ważności dla harmonogramu.  Treść wniosku musi zawierać etag działania.

    $thresholdWithSevJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson

#### <a name="suppress"></a>Pomiń
Alerty zapytań oparte na usłudze Log Analytics będą uruchamiane za każdym razem, gdy próg zostanie osiągnięty lub przekroczony. Na podstawie logiki implikowane w kwerendzie, może to spowodować alert jest uruchamiany dla serii interwałów, a tym samym powiadomienia również wysyłane stale. Aby zapobiec takiemu scenariuszowi, użytkownik może ustawić opcję Pomijanie instruując usługi Log Analytics, aby czekała na określoną ilość czasu, zanim powiadomienie zostanie wyzwany po raz drugi dla reguły alertu. Więc jeśli wygasić jest ustawiony na 30 minut; alert zostanie wyzrzel za pierwszym razem i wyśle skonfigurowane powiadomienia. Ale następnie odczekaj 30 minut, zanim ponownie zostanie ponownie użyte powiadomienie o regule alertu. W okresie przejściowym reguła alertu będzie nadal działać — tylko powiadomienie jest pomijane przez usługi Log Analytics przez określony czas, niezależnie od tego, ile razy reguła alertu została odpalona w tym okresie.

Pomiń właściwość reguły alertu usługi Log Analytics jest określona przy użyciu wartości *ograniczania* przepustowości i okresu pomijania przy użyciu wartości *DurationInMinutes.*

Poniżej przedstawiono przykładową odpowiedź dla akcji z tylko progiem, ważnością i właściwością pomijania

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My threshold action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Throttling": {
          "DurationInMinutes": 30
        },
        "Severity": "critical",
        "Version": 1    }

Użyj Put metody z unikatowym identyfikatorem akcji, aby utworzyć nową akcję dla harmonogramu z ważnością.  

    $AlertSuppressJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson

Użyj Put metody z istniejącym identyfikatorem akcji, aby zmodyfikować akcję ważności dla harmonogramu.  Treść wniosku musi zawierać etag działania.

    $AlertSuppressJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson

#### <a name="action-groups"></a>Grupy akcji
Wszystkie alerty na platformie Azure, użyj grupy akcji jako domyślny mechanizm obsługi akcji. Za pomocą grupy akcji można określić akcje raz, a następnie skojarzyć grupę akcji z wieloma alertami — na platformie Azure. Bez konieczności, wielokrotnie deklarować te same działania w kółko. Grupy akcji obsługują wiele działań — w tym pocztę e-mail, SMS, połączenie głosowe, połączenie ITSM, system y runbook automatyzacji, identyfikator URI typu webhook i inne. 

Dla użytkowników, którzy rozszerzyli swoje alerty na platformie Azure — harmonogram powinien teraz mieć szczegóły grupy akcji przekazywane wraz z progiem, aby móc utworzyć alert. Szczegóły wiadomości e-mail, adresy URL elementu webhook, szczegóły automatyzacji elementu runbook i inne akcje muszą zostać zdefiniowane w grupie akcji najpierw po stronie grupy akcji przed utworzeniem alertu; można utworzyć [grupę akcji z usługi Azure Monitor](../../azure-monitor/platform/action-groups.md) w portalu lub użyć interfejsu API grupy [akcji](https://docs.microsoft.com/rest/api/monitor/actiongroups).

Aby dodać skojarzenie grupy akcji do alertu, określ unikatowy identyfikator usługi Azure Resource Manager grupy akcji w definicji alertu. Przykładowa ilustracja znajduje się poniżej:

     "etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
      "properties": {
        "Type": "Alert",
        "Name": "test-alert",
        "Description": "I need to put a description here",
        "Threshold": {
          "Operator": "gt",
          "Value": 12
        },
        "AzNsNotification": {
          "GroupIds": [
            "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
          ]
        },
        "Severity": "critical",
        "Version": 1
      },

Użyj Put metody z unikatowym identyfikatorem akcji, aby skojarzyć już istniejącą grupę akcji dla harmonogramu.  Poniżej przedstawiono przykładową ilustrację użycia.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']} } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Użyj Put metody z istniejącym identyfikatorem akcji, aby zmodyfikować grupę akcji skojarzoną z harmonogramem.  Treść wniosku musi zawierać etag działania.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': { 'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'] } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

#### <a name="customize-actions"></a>Dostosowywanie akcji
Domyślnie akcje należy przestrzegać standardowego szablonu i formatu powiadomień. Ale użytkownik może dostosować niektóre akcje, nawet jeśli są one kontrolowane przez grupy akcji. Obecnie dostosowywanie jest możliwe dla tematu poczty e-mail i ładunku webhook.

##### <a name="customize-e-mail-subject-for-action-group"></a>Dostosowywanie tematu wiadomości e-mail do grupy działań
Domyślnie tematem wiadomości e-mail dla `<AlertName>` alertów jest: Powiadomienie o alertach dla `<WorkspaceName>`. Ale to można dostosować, dzięki czemu można konkretne słowa lub tagi - aby umożliwić łatwe stosowanie reguł filtrowania w skrzynce odbiorczej. Szczegóły nagłówka niestandardowego adresu e-mail muszą być wysyłane wraz ze szczegółami ActionGroup, jak w poniższym przykładzie.

     "etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
      "properties": {
        "Type": "Alert",
        "Name": "test-alert",
        "Description": "I need to put a description here",
        "Threshold": {
          "Operator": "gt",
          "Value": 12
        },
        "AzNsNotification": {
          "GroupIds": [
            "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
          ],
          "CustomEmailSubject": "Azure Alert fired"
        },
        "Severity": "critical",
        "Version": 1
      },

Użyj Put metody z unikatowym identyfikatorem akcji, aby skojarzyć już istniejącą grupę akcji z dostosowaniem harmonogramu.  Poniżej przedstawiono przykładową ilustrację użycia.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired'} } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Użyj Put metody z istniejącym identyfikatorem akcji, aby zmodyfikować grupę akcji skojarzoną z harmonogramem.  Treść wniosku musi zawierać etag działania.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired' } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

##### <a name="customize-webhook-payload-for-action-group"></a>Dostosowywanie ładunku elementu Webhook dla grupy akcji
Domyślnie element webhook wysłany za pośrednictwem grupy akcji do analizy dzienników ma stałą strukturę. Ale można dostosować ładunek JSON przy użyciu określonych zmiennych obsługiwanych, aby spełnić wymagania punktu końcowego elementu webhook. Aby uzyskać więcej informacji, zobacz [Akcja elementu Webhook dla reguł alertów dziennika](../../azure-monitor/platform/alerts-log-webhook.md). 

Szczegóły dostosowywania elementu webhook muszą zostać wysłane wraz ze szczegółami ActionGroup i zostaną zastosowane do wszystkich identyfikatorów URI elementu webhook określonych wewnątrz grupy akcji; jak w poniższej próbce.

     "etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
      "properties": {
        "Type": "Alert",
        "Name": "test-alert",
        "Description": "I need to put a description here",
        "Threshold": {
          "Operator": "gt",
          "Value": 12
        },
        "AzNsNotification": {
          "GroupIds": [
            "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
          ],
          "CustomWebhookPayload": "{\"field1\":\"value1\",\"field2\":\"value2\"}",
          "CustomEmailSubject": "Azure Alert fired"
        },
        "Severity": "critical",
        "Version": 1
      },

Użyj Put metody z unikatowym identyfikatorem akcji, aby skojarzyć już istniejącą grupę akcji z dostosowaniem harmonogramu.  Poniżej przedstawiono przykładową ilustrację użycia.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}'} } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Użyj Put metody z istniejącym identyfikatorem akcji, aby zmodyfikować grupę akcji skojarzoną z harmonogramem.  Treść wniosku musi zawierać etag działania.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}' } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson


## <a name="next-steps"></a>Następne kroki

* Użyj [interfejsu API REST do wykonywania wyszukiwania dzienników](../../azure-monitor/log-query/log-query-overview.md) w usłudze Log Analytics.
* Dowiedz się więcej o [alertach dziennika na monitorze platformy Azure](../../azure-monitor/platform/alerts-unified-log.md)
* Jak [tworzyć, edytować lub zarządzać regułami alertów dziennika na monitorze platformy Azure](../../azure-monitor/platform/alerts-log.md)

