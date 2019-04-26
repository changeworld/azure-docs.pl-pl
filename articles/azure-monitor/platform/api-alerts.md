---
title: Za pomocą interfejsu API REST alertów usługi Log Analytics
description: Log Analytics alertu interfejsu API REST umożliwia tworzenie i Zarządzanie alertami w usłudze Log Analytics, która jest częścią usługi Log Analytics.  Ten artykuł zawiera szczegółowe informacje o interfejsie API i przykłady do wykonywania różnych operacji.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 628ad256-7181-4a0d-9e68-4ed60c0f3f04
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/10/2018
ms.author: bwren
ms.openlocfilehash: bee64909c7f3b295691ef1cb1840424aa7e3fe49
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60345910"
---
# <a name="create-and-manage-alert-rules-in-log-analytics-with-rest-api"></a>Tworzenie i zarządzanie nimi reguły alertów w usłudze Log Analytics przy użyciu interfejsu API REST
Log Analytics alertu interfejsu API REST umożliwia tworzenie i Zarządzanie alertami w usłudze Log Analytics.  Ten artykuł zawiera szczegółowe informacje o interfejsie API i przykłady do wykonywania różnych operacji.

Interfejsu API do REST wyszukiwania usługi Log Analytics jest zgodne ze specyfikacją REST i możliwy za pośrednictwem interfejsu REST API usługi Azure Resource Manager. W tym dokumencie można znaleźć przykłady gdzie interfejsu API jest dostępny z wiersza polecenia programu PowerShell przy użyciu [ARMClient](https://github.com/projectkudu/ARMClient), narzędzie wiersza polecenia typu open source, które upraszcza wywoływanie interfejsu API usługi Azure Resource Manager. Korzystanie z programu PowerShell i ARMClient jest jedną z wielu opcji, aby dostęp do interfejsu API wyszukiwania usługi Log Analytics. Za pomocą tych narzędzi może korzystać z usługi Azure Resource Manager interfejsu API RESTful do wykonywania wywołań do obszarów roboczych usługi Log Analytics i wykonywania poleceń wyszukiwania w nich. Interfejs API zwróci wyniki wyszukiwania dla użytkownika w formacie JSON, co pozwala na wykorzystanie wyniki wyszukiwania na wiele różnych sposobów programowo.

## <a name="prerequisites"></a>Wymagania wstępne
Obecnie alertów można tworzyć tylko za pomocą zapisanego kryterium wyszukiwania w usłudze Log Analytics.  Możesz zapoznać się z [interfejsu API REST wyszukiwania dziennika](../../azure-monitor/log-query/log-query-overview.md) Aby uzyskać więcej informacji.

## <a name="schedules"></a>Harmonogramy
Zapisane wyszukiwanie może mieć co najmniej jeden harmonogram. Harmonogram definiuje, jak często wyszukiwanie jest uruchamiania i przedział czasu, przez który jest identyfikowany kryteria.
Harmonogramy mają właściwości podane w poniższej tabeli.

| Właściwość | Opis |
|:--- |:--- |
| Interval |Jak często jest uruchamiany podczas wyszukiwania. Mierzony w ciągu kilku minut. |
| QueryTimeSpan |Przedział czasu, w którym jest oceniana kryteria. Musi być równy lub większy niż interwał. Mierzony w ciągu kilku minut. |
| Version |Używana wersja interfejsu API.  Obecnie to powinno być zawsze ustawiony na 1. |

Rozważmy na przykład zapytania dotyczącego zdarzenia z odstępie 15 minut i przedziału czasu 30 minut. W tym przypadku zapytanie będzie uruchamiany co 15 minut, a alert może zostać wyzwolony, jeśli kryteria nadal prowadzić do przez wartość true, zakres 30 minut.

### <a name="retrieving-schedules"></a>Trwa pobieranie harmonogramów
Aby pobrać wszystkie harmonogramy dla zapisanego kryterium wyszukiwania, należy użyć metody Get.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules?api-version=2015-03-20

Za pomocą metody Get identyfikator harmonogramu można pobrać konkretny harmonogram dla zapisanego wyszukiwania.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20

Oto przykładowa odpowiedź dla harmonogramu.

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
Za pomocą metody Put identyfikator unikatowy harmonogram do utworzenia nowego harmonogramu.  Należy zauważyć, że dwa harmonogramy nie ten sam identyfikator nawet, jeśli są one skojarzone z różnymi zapisane wyszukiwania.  Podczas tworzenia harmonogramu w konsoli usługi Log Analytics, identyfikator GUID jest tworzona dla identyfikator harmonogramu.

> [!NOTE]
> Nazwy wszystkich zapisanych wyszukiwań, harmonogramy i działań utworzonych za pomocą interfejsu API programu Log Analytics musi być pisane małymi literami.

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson

### <a name="editing-a-schedule"></a>Edytowanie harmonogramu
Za pomocą metody Put istniejący identyfikator harmonogramu dla tej samej zapisanego wyszukiwania do modyfikowania tego harmonogramu; w poniższym przykładzie harmonogram jest wyłączony. Treść żądania musi zawierać *etag* harmonogramu.

      $scheduleJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\""','properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Enabled':'false' } }"
      armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson


### <a name="deleting-schedules"></a>Usuwanie harmonogramów
Za pomocą metody Delete identyfikator harmonogramu można usunąć harmonogramu.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20


## <a name="actions"></a>Akcje
Harmonogram może mieć wiele akcji. Akcja może zdefiniować jeden lub więcej procesów do wykonywania takich jak wysłanie wiadomości e-mail lub uruchamianie elementu runbook lub mogą definiować, próg, która określa, kiedy wyniki wyszukiwania pasują do kryteriów.  Niektóre akcje definiują zarówno tak, aby procesy są wykonywane po osiągnięciu wartości progowej.

Wszystkie działania mają właściwości podane w poniższej tabeli.  Różne typy alertów mają różne dodatkowe właściwości, które zostały opisane poniżej.

| Właściwość | Opis |
|:--- |:--- |
| `Type` |Typ akcji.  Obecnie możliwe wartości to Alert i elementów Webhook. |
| `Name` |Nazwa wyświetlana alertu. |
| `Version` |Używana wersja interfejsu API.  Obecnie to powinno być zawsze ustawiony na 1. |

### <a name="retrieving-actions"></a>Pobieranie akcji

> [!NOTE]
> Począwszy od 14 maja 2018 r. wszystkie alerty w wystąpieniu chmury publicznej platformy Azure w obszarze roboczym usługi Log Analytics zostaną automatycznie rozszerzone na platformę Azure. Użytkownik dobrowolnie może zainicjować rozszerzanie alertów do platformy Azure przed 14 maja 2018 r. Aby uzyskać więcej informacji, zobacz [rozszerzanie alertów do platformy Azure z usługą Log Analytics](../../azure-monitor/platform/alerts-extend.md). Dla użytkowników, którzy rozszerzanie alertów do platformy Azure akcje są obecnie kontrolowane w grup akcji platformy Azure. Gdy obszar roboczy i jego alerty zostały rozszerzone na platformę Azure, można pobrać lub dodać akcje przy użyciu [API grupy akcji](https://docs.microsoft.com/rest/api/monitor/actiongroups).

Aby pobrać wszystkie akcje w przypadku harmonogramu, należy użyć metody Get.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules/{Schedule ID}/actions?api-version=2015-03-20

Za pomocą metody Get ID akcji można pobrać określoną akcję dla harmonogramu.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/actions/{Action ID}?api-version=2015-03-20

### <a name="creating-or-editing-actions"></a>Tworzenie lub edytowanie akcji
Za pomocą Identyfikatora akcji, która jest unikatowa w harmonogramie, aby utworzyć nową akcję, należy użyć metody Put.  Podczas tworzenia akcji w konsoli usługi Log Analytics, jest identyfikator GUID dla identyfikatora akcji.

> [!NOTE]
> Nazwy wszystkich zapisanych wyszukiwań, harmonogramy i działań utworzonych za pomocą interfejsu API programu Log Analytics musi być pisane małymi literami.

Za pomocą metody Put istniejący identyfikator akcji dla tego samego zapisanego wyszukiwania do modyfikowania tego harmonogramu.  Treść żądania musi zawierać element etag harmonogramu.

Format żądania do tworzenia nowej akcji zależy od typu akcji, dzięki czemu te przykłady znajdują się w poniższych sekcjach.

### <a name="deleting-actions"></a>Usuwanie akcji

> [!NOTE]
> Począwszy od 14 maja 2018 r. wszystkie alerty w wystąpieniu chmury publicznej platformy Azure w obszarze roboczym usługi Log Analytics zostaną automatycznie rozszerzone na platformę Azure. Użytkownik dobrowolnie może zainicjować rozszerzanie alertów do platformy Azure przed 14 maja 2018 r. Aby uzyskać więcej informacji, zobacz [rozszerzanie alertów do platformy Azure z usługą Log Analytics](../../azure-monitor/platform/alerts-extend.md). Dla użytkowników, którzy rozszerzanie alertów do platformy Azure akcje są obecnie kontrolowane w grup akcji platformy Azure. Gdy obszar roboczy i jego alerty zostały rozszerzone na platformę Azure, można pobrać lub dodać akcje przy użyciu [API grupy akcji](https://docs.microsoft.com/rest/api/monitor/actiongroups).

Za pomocą metody Delete ID akcji można usunąć danej akcji.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/Actions/{Action ID}?api-version=2015-03-20

### <a name="alert-actions"></a>Akcje alertu
Harmonogram powinien mieć jeden i tylko jeden Akcja alertu.  Akcje alertu mają co najmniej jeden z sekcji w poniższej tabeli.  Każdy opisano szczegółowo poniżej.

| Sekcja | Opis | Sposób użycia |
|:--- |:--- |:--- |
| Próg |Kryteria dla uruchomienia działania.| Wymagane dla każdego alertu przed lub po ich zostały rozszerzone na platformę Azure. |
| Ważność |Etykieta służy do klasyfikowania alertów po ich wyzwoleniu.| Wymagane dla każdego alertu przed lub po ich zostały rozszerzone na platformę Azure. |
| Pomiń |Opcję, aby wyłączyć powiadomienia z alertu. | Opcjonalne dla każdego alertu przed lub po ich zostały rozszerzone na platformę Azure. |
| Grupy akcji |Identyfikatory ActionGroup platformy Azure, w których akcje wymagane są określone, takie jak - wiadomości E-mail, Menedżer SMSs, połączenia głosowe, elementy Webhook, elementy Runbook usługi Automation, łączniki ITSM itd.| Wymagane, gdy alerty zostały rozszerzone na platformę Azure|
| Dostosuj akcje|Modyfikowanie standardowe dane wyjściowe akcji select z ActionGroup| Można opcjonalnie dla każdego alertu, po alerty zostały rozszerzone na platformę Azure. |
| EmailNotification |Wysyłanie wiadomości e-mail do wielu odbiorców. | Nie jest to wymagane, jeśli alerty zostały rozszerzone na platformę Azure|
| Korygowanie |Uruchom element runbook w usłudze Azure Automation, aby spróbować rozwiązać zidentyfikowany problem. |Nie jest to wymagane, jeśli alerty zostały rozszerzone na platformę Azure|
| Akcje elementu Webhook | Wypychanie danych z alertami, do żądanej usługi w formacie JSON |Nie jest to wymagane, jeśli alerty zostały rozszerzone na platformę Azure|

> [!NOTE]
> Począwszy od 14 maja 2018 r. wszystkie alerty w wystąpieniu chmury publicznej platformy Azure w obszarze roboczym usługi Log Analytics zostaną automatycznie rozszerzone na platformę Azure. Użytkownik dobrowolnie może zainicjować rozszerzanie alertów do platformy Azure przed 14 maja 2018 r. Aby uzyskać więcej informacji, zobacz [rozszerzanie alertów do platformy Azure z usługą Log Analytics](../../azure-monitor/platform/alerts-extend.md).

#### <a name="thresholds"></a>Progi
Akcji alertu powinna mieć tylko jedną wartość progową.  Gdy wyniki zapisanego kryterium wyszukiwania są zgodne próg w akcji skojarzonej z tym wyszukiwaniem, są uruchamiane inne procesy, w tym działaniu.  Akcji może również zawierać tylko wartości progowej, dzięki czemu można z działaniami innych typów, które nie zawierają wartości progowe.

Progi mają właściwości podane w poniższej tabeli.

| Właściwość | Opis |
|:--- |:--- |
| `Operator` |Operator porównania wartości progowej. <br> gt = większe niż <br> lt = mniej niż |
| `Value` |Wartość progu. |

Rozważmy na przykład zapytania dotyczącego zdarzenia w odstępie 15 minut, przedział czasu, 30 minut i próg większe niż 10. W tym przypadku zapytanie będzie uruchamiany co 15 minut, a alert może zostać wyzwolony, jeśli zwrócony 10 zdarzenia, które zostały utworzone w okresie 30-minutowe.

Oto przykładowa odpowiedź dla akcji o tylko progu.  

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

Za pomocą metody Put identyfikator unikatowy akcji do utworzenia nowej akcji próg dla harmonogramu.  

    $thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

Za pomocą metody Put istniejący identyfikator akcji do modyfikowania akcją próg dla harmonogramu.  Treść żądania musi zawierać element etag akcji.

    $thresholdJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

#### <a name="severity"></a>Ważność
Usługa log Analytics umożliwia klasyfikowanie alertów w kategorie, aby umożliwić łatwiejsze zarządzanie i klasyfikacji. Ważność alertu zdefiniowane jest: informacyjny, ostrzegawczy i krytyczny. Te są mapowane na skalę znormalizowane ważność alertów platformy Azure w postaci:

|Poziom ważności analizy dzienników  |Poziom ważności alertów platformy Azure  |
|---------|---------|
|`critical` |Ważność 0|
|`warning` |Ważność 1|
|`informational` | Ważność 2|

Oto przykładowa odpowiedź dla akcji przy użyciu progu i ważności. 

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

Za pomocą metody Put identyfikator unikatowy akcji do utworzenia nowej akcji dla harmonogramu o ważności.  

    $thresholdWithSevJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson

Za pomocą metody Put istniejący identyfikator akcji do modyfikowania akcją ważności dla harmonogramu.  Treść żądania musi zawierać element etag akcji.

    $thresholdWithSevJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson

#### <a name="suppress"></a>Pomiń
Usługa log Analytics na podstawie zapytania, które alerty będą uruchamiane za każdym razem, gdy spełnione lub przekroczeniu progu. Oparte na logice podany w kwerendzie, może to spowodować wprowadzenie wyzwolony w przypadku serii interwałów alert i dlatego powiadomienia również wysyłane stale. Aby zapobiec taki scenariusz, użytkownik może ustawić opcję Pomiń poinstruowanie usługi Log Analytics poczekaj, aż określony przedział czasu, po którym powiadomień jest uruchamiany po raz drugi dla reguły alertu. Pomiń więc jeśli jest ustawiona na 30 minut; alert będą uruchamiane po raz pierwszy i wysyłać powiadomienia skonfigurowane. Ale Zaczekaj 30 minut, zanim zostaną ponownie użyte powiadomień dla reguły alertu. W okresie przejściowym będzie kontynuował działanie reguły alertu — tylko powiadomienia są pomijane przez usługę Log Analytics określony czas, niezależnie od tego, ile razy regułę alertu są uruchamiane w tym okresie.

Pomiń właściwości usługi Log Analytics, reguła alertu jest określony, przy użyciu *ograniczania* wartość i pomijania okresu używania *DurationInMinutes* wartości.

Następujący jest przykładowej odpowiedzi na akcję o tylko progu, ważność i Pomiń właściwość

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

Za pomocą metody Put identyfikator unikatowy akcji do utworzenia nowej akcji dla harmonogramu o ważności.  

    $AlertSuppressJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson

Za pomocą metody Put istniejący identyfikator akcji do modyfikowania akcją ważności dla harmonogramu.  Treść żądania musi zawierać element etag akcji.

    $AlertSuppressJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson

#### <a name="action-groups"></a>Grupy akcji
Wszystkie alerty na platformie Azure, użyj akcji grupy jako domyślnego mechanizmu do obsługi akcji. Grupy akcji możesz określić swoje działania raz i skojarz grupę akcji do wielu alertów — na platformie Azure. Bez konieczności wielokrotnego wielokrotnie zadeklarować te same akcje. Grupy akcji obsługują wiele akcji — w tym wiadomości e-mail, wiadomości SMS, połączenie głosowe, połączenia narzędzia ITSM, element Runbook usługi Automation, identyfikator URI elementu Webhook i innych. 

Dla użytkowników, którzy rozszerzono alerty na platformie Azure — harmonogram powinno zostać udostępnionych szczegółów grupy akcji przekazywane wraz z wartości progowej, aby można było utworzyć alert. Szczegóły wiadomości e-mail, adresy URL elementu Webhook, szczegóły elementu Runbook usługi Automation i innych działań, muszą być zdefiniowane w stronie grupy akcji przed tworzenia alertu; można utworzyć [grupy akcji z usługi Azure Monitor](../../azure-monitor/platform/action-groups.md) w portalu lub użyj [API grupy akcji](https://docs.microsoft.com/rest/api/monitor/actiongroups).

Aby dodać skojarzenia grupy akcji alertu, należy określić unikatowy identyfikator usługi Azure Resource Manager do grupy akcji w definicji alertu. Ilustracja przykładowa znajduje się poniżej:

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

Za pomocą metody Put identyfikator unikatowy akcji do skojarzenia z istniejącej grupy akcji dla harmonogramu.  Poniżej przedstawiono ilustracji przykład użycia.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Za pomocą metody Put istniejący identyfikator akcji do modyfikowania grupy akcji skojarzony z harmonogramem.  Treść żądania musi zawierać element etag akcji.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

#### <a name="customize-actions"></a>Dostosuj akcje
Akcje domyślne, wykonując standardowego szablonu i format dla powiadomień. Jednak użytkownik może dostosować pewne działania, nawet wtedy, gdy są one kontrolowane przez grupy akcji. Obecnie Dostosowywanie jest temat wiadomości E-mail i ładunek elementu Webhook.

##### <a name="customize-e-mail-subject-for-action-group"></a>Dostosowywanie temat wiadomości E-Mail do grupy akcji
Domyślnie jest temat wiadomości e-mail dla alertów: Powiadomienia o alercie `<AlertName>` dla `<WorkspaceName>`. Ale to można dostosowywać, aby można było konkretnych słów lub tagi — pozwala łatwo stosować reguły filtrowania w Twojej skrzynce odbiorczej. Szczegółowe informacje o nagłówku Dostosuj wiadomości e-mail należy wysyłać wraz ze szczegółami ActionGroup, tak jak w poniższym przykładzie.

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

Za pomocą metody Put identyfikator unikatowy akcji do skojarzenia z istniejącej grupy akcji z dostosowywaniem dla harmonogramu.  Poniżej przedstawiono ilustracji przykład użycia.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Za pomocą metody Put istniejący identyfikator akcji do modyfikowania grupy akcji skojarzony z harmonogramem.  Treść żądania musi zawierać element etag akcji.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired' }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

##### <a name="customize-webhook-payload-for-action-group"></a>Dostosowywanie ładunek elementu Webhook dla grupy akcji
Domyślnie element webhook, który został wysłany za pośrednictwem grupy akcji dla usługi log analytics ma stałą strukturę. Jednak jeden dostosować ładunek w formacie JSON przy użyciu określonych zmiennych obsługiwane pod kątem wymagań punktem końcowym elementu webhook. Aby uzyskać więcej informacji, zobacz [Akcja elementu Webhook dla reguł alertów dzienników](../../azure-monitor/platform/alerts-log-webhook.md). 

Dostosuj szczegóły elementu webhook należy wysyłać wraz ze szczegółami ActionGroup i będzie można zastosować do wszystkich elementów Webhook identyfikatora URI określonego wewnątrz grupy akcji; tak jak w poniższym przykładzie.

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

Za pomocą metody Put identyfikator unikatowy akcji do skojarzenia z istniejącej grupy akcji z dostosowywaniem dla harmonogramu.  Poniżej przedstawiono ilustracji przykład użycia.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Za pomocą metody Put istniejący identyfikator akcji do modyfikowania grupy akcji skojarzony z harmonogramem.  Treść żądania musi zawierać element etag akcji.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}' }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

#### <a name="email-notification"></a>Powiadomienie e-mail
Powiadomienia e-mail Wyślij wiadomości e-mail do co najmniej jednego adresata.  Obejmują one właściwości w poniższej tabeli.

> [!NOTE]
> Począwszy od 14 maja 2018 r. wszystkie alerty w wystąpieniu chmury publicznej platformy Azure w obszarze roboczym usługi Log Analytics zostaną automatycznie rozszerzone na platformę Azure. Użytkownik dobrowolnie może zainicjować rozszerzanie alertów do platformy Azure przed 14 maja 2018 r. Aby uzyskać więcej informacji, zobacz [rozszerzanie alertów do platformy Azure z usługą Log Analytics](../../azure-monitor/platform/alerts-extend.md). Dla użytkowników, którzy rozszerzanie alertów do platformy Azure akcji, takich jak powiadomienia E-Mail są teraz kontrolowane w grup akcji platformy Azure. Gdy obszar roboczy i jego alerty zostały rozszerzone na platformę Azure, można pobrać lub dodać akcje przy użyciu [API grupy akcji](https://docs.microsoft.com/rest/api/monitor/actiongroups).
   

| Właściwość | Opis |
|:--- |:--- |
| Recipients |Lista adresów e-mail. |
| Subject |Temat wiadomości e-mail. |
| Attachment |Załączniki nie są obecnie obsługiwane, dzięki czemu zawsze będzie to miało wartość "None". |

Oto przykładowa odpowiedź dla akcji powiadomienia e-mail o progu.  

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My email action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "EmailNotification": {
            "Recipients": [
                "recipient1@contoso.com",
                "recipient2@contoso.com"
            ],
            "Subject": "This is the subject",
            "Attachment": "None"
        },
        "Version": 1
    }

Za pomocą metody Put identyfikator unikatowy akcji do utworzenia nowej akcji poczty e-mail dla harmonogramu.  Poniższy przykład tworzy wiadomość e-mail z powiadomieniem o progu, dzięki czemu wiadomości e-mail jest wysyłana, gdy wyniki zapisanego kryterium wyszukiwania przekracza wartość progową.

    $emailJson = "{'properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $emailJson

Za pomocą metody Put istniejący identyfikator akcji do modyfikowania akcji poczty e-mail dla harmonogramu.  Treść żądania musi zawierać element etag akcji.

    $emailJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $emailJson

#### <a name="remediation-actions"></a>Akcje naprawcze
Korygowania funkcję uruchamiania elementu runbook w usłudze Azure Automation, która podejmuje próbę rozwiązania problemu wskazanego przez alert.  Należy utworzyć element webhook dla elementu runbook użytego w akcje naprawcze, a następnie określ identyfikator URI, we właściwości WebhookUri.  Po utworzeniu tej akcji, używając witryny Azure portal, nowy element webhook jest tworzony automatycznie dla elementu runbook.

> [!NOTE]
> Począwszy od 14 maja 2018 r. wszystkie alerty w wystąpieniu chmury publicznej platformy Azure w obszarze roboczym usługi Log Analytics zostaną automatycznie rozszerzone na platformę Azure. Użytkownik dobrowolnie może zainicjować rozszerzanie alertów do platformy Azure przed 14 maja 2018 r. Aby uzyskać więcej informacji, zobacz [rozszerzanie alertów do platformy Azure z usługą Log Analytics](../../azure-monitor/platform/alerts-extend.md). Dla użytkowników, którzy rozszerzanie alertów do platformy Azure akcje, takie jak korygowania, korzystając z elementu runbook są teraz kontrolowane w grup akcji platformy Azure. Gdy obszar roboczy i jego alerty zostały rozszerzone na platformę Azure, można pobrać lub dodać akcje przy użyciu [API grupy akcji](https://docs.microsoft.com/rest/api/monitor/actiongroups).

Korygowania funkcję obejmują właściwości w poniższej tabeli.

| Właściwość | Opis |
|:--- |:--- |
| RunbookName |Nazwa elementu runbook. Musi on być zgodny opublikowanego elementu runbook na koncie usługi automation skonfigurowano w rozwiązaniu usługi Automation, w obszarze roboczym usługi Log Analytics. |
| WebhookUri |Identyfikator URI elementu webhook. |
| Expiry |Datę i godzinę wygaśnięcia elementu webhook.  Jeśli element webhook nie ma wygasania, może to być dowolną prawidłową datę przyszłości. |

Oto przykładowa odpowiedź dla akcji korygowania o progu.

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My remediation action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Remediation": {
            "RunbookName": "My-Runbook",
            "WebhookUri": "https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d",
            "Expiry": "2018-02-25T18:27:20"
            },
        "Version": 1
    }

Za pomocą metody Put identyfikator unikatowy akcji do utworzenia nowej akcji korygowania dla harmonogramu.  Poniższy przykład tworzy korygowania o progu, dzięki czemu element runbook jest uruchamiana, gdy wyniki zapisanego kryterium wyszukiwania przekracza wartość progową.

    $remediateJson = "{'properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

Aby zmodyfikować akcje naprawcze w przypadku harmonogramu, należy użyć metody Put przy użyciu istniejącego Identyfikatora akcji.  Treść żądania musi zawierać element etag akcji.

    $remediateJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

#### <a name="example"></a>Przykład
Poniżej znajduje się pełny przykład można utworzyć nowego alertu wiadomości e-mail.  Spowoduje to utworzenie nowego harmonogramu oraz akcję, zawierającego wartość progową i wiadomości e-mail.

    $subscriptionId = "3d56705e-5b26-5bcc-9368-dbc8d2fafbfc"
    $resourceGroup  = "MyResourceGroup"    
    $workspaceName    = "MyWorkspace"
    $searchId       = "MySearch"
    $scheduleId     = "MySchedule"
    $thresholdId    = "MyThreshold"
    $actionId       = "MyEmailAction"
    
    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/$workspaceName/savedSearches/$searchId/schedules/$scheduleId/?api-version=2015-03-20 $scheduleJson
    
    $emailJson = "{'properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Severity':'Warning', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/$workspaceName/savedSearches/$searchId/schedules/$scheduleId/actions/$actionId/?api-version=2015-03-20 $emailJson

#### <a name="webhook-actions"></a>Akcje elementu Webhook
Akcje elementu Webhook Uruchom proces, przez wywołanie adresu URL i opcjonalnie podania ładunku do wysłania.  Są one podobne do akcji korygowania, z wyjątkiem są przeznaczone dla elementów webhook, które może wywoływać procesy inne niż elementów runbook usługi Azure Automation.  Zapewniają także dodatkowe opcji związanych z udostępnianiem ładunek został dostarczony do zdalnego procesu.

> [!NOTE]
> Począwszy od 14 maja 2018 r. wszystkie alerty w wystąpieniu chmury publicznej platformy Azure w obszarze roboczym usługi Log Analytics zostaną automatycznie rozszerzone na platformę Azure. Użytkownik dobrowolnie może zainicjować rozszerzanie alertów do platformy Azure przed 14 maja 2018 r. Aby uzyskać więcej informacji, zobacz [rozszerzanie alertów do platformy Azure z usługą Log Analytics](../../azure-monitor/platform/alerts-extend.md). Dla użytkowników, którzy rozszerzanie alertów do platformy Azure akcje, takie jak element Webhook teraz są kontrolowane w grup akcji platformy Azure. Gdy obszar roboczy i jego alerty zostały rozszerzone na platformę Azure, można pobrać lub dodać akcje przy użyciu [API grupy akcji](https://docs.microsoft.com/rest/api/monitor/actiongroups).


Akcje elementu Webhook nie mają wartości progowej, ale zamiast tego powinny zostać dodane do harmonogram, który ma akcji alertu o progu.  

Poniżej przedstawiono przykładową odpowiedź w dla skojarzonych akcji alertu o progu i Akcja elementu webhook.

    {
        "__metadata": {},
        "value": [
            {
                "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/bwren/savedSearches/2d1b30fb-7f48-4de5-9614-79ee244b52de/schedules/b80f5621-7217-4007-b32d-165d14377093/Actions/72884702-acf9-4653-bb67-f42436b342b4",
                "etag": "W/\"datetime'2016-02-26T20%3A25%3A00.6862124Z'\"",
                "properties": {
                    "Type": "Webhook",
                    "Name": "My Webhook Action",
                    "WebhookUri": "https://oaaswebhookdf.cloudapp.net/webhooks?token=VfkYTIlpk%2fc%2bJBP",
                    "CustomPayload": "{\"fielld1\":\"value1\",\"field2\":\"value2\"}",
                    "Version": 1
                }
            },
            {
                "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/bwren/savedSearches/2d1b30fb-7f48-4de5-9614-79ee244b52de/schedules/b80f5621-7217-4007-b32d-165d14377093/Actions/90a27cf8-71b7-4df2-b04f-54ed01f1e4b6",
                "etag": "W/\"datetime'2016-02-26T20%3A25%3A00.565204Z'\"",
                "properties": {
                    "Type": "Alert",
                    "Name": "Threshold for my webhook action",
                    "Threshold": {
                        "Operator": "gt",
                        "Value": 10
                    },
                    "Version": 1
                }
            }
        ]
    }

##### <a name="create-or-edit-a-webhook-action"></a>Tworzenie lub edytowanie Akcja elementu webhook
Za pomocą metody Put identyfikator unikatowy akcji do utworzenia nowej akcji elementu webhook dla harmonogramu.  Poniższy przykład tworzy Akcja elementu Webhook i akcji alertu o progu, dzięki czemu element webhook zostaną wyzwolone, gdy wyniki zapisanego kryterium wyszukiwania przekracza wartość progową.

    $thresholdAction = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdAction

    $webhookAction = "{'properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction

Za pomocą metody Put istniejący identyfikator akcji do modyfikowania akcją elementu webhook dla harmonogramu.  Treść żądania musi zawierać element etag akcji.

    $webhookAction = "{'etag': 'W/\"datetime'2016-02-26T20%3A25%3A00.6862124Z'\"','properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction


## <a name="next-steps"></a>Kolejne kroki
* Użyj [interfejsu API REST, aby wykonać wyszukiwanie w dzienniku](../../azure-monitor/log-query/log-query-overview.md) w usłudze Log Analytics.
* Dowiedz się więcej o [alerty dzienników w alertów platformy azure](../../azure-monitor/platform/alerts-unified-log.md)

