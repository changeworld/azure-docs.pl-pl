---
title: Przy użyciu pakietu OMS dziennika analizy alertu REST API
description: Dziennik analizy alertu interfejsu API REST umożliwia tworzenie i Zarządzanie alertami w analizy dzienników, która jest częścią z Operations Management Suite (OMS).  Ten artykuł zawiera szczegółowe informacje o interfejsie API i kilka przykładów do wykonywania różnych operacji.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 628ad256-7181-4a0d-9e68-4ed60c0f3f04
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/12/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ed0ac6e2041ef503470f7317a5736deecd1d2b8f
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="create-and-manage-alert-rules-in-log-analytics-with-rest-api"></a>Tworzenie i zarządzanie nimi reguły alertów w analizy dzienników z interfejsu API REST
Dziennik analizy alertu interfejsu API REST umożliwia tworzenie i Zarządzanie alertami w Operations Management Suite (OMS).  Ten artykuł zawiera szczegółowe informacje o interfejsie API i kilka przykładów do wykonywania różnych operacji.

Interfejsu API REST Search analizy dziennika jest RESTful i jest dostępny za pośrednictwem interfejsu REST API usługi Azure Resource Manager. W tym dokumencie można znaleźć przykłady którym dostęp do interfejsu API z wiersza polecenia programu PowerShell przy użyciu [ARMClient](https://github.com/projectkudu/ARMClient), narzędzie wiersza polecenia open source, które upraszcza wywoływanie interfejsu API Azure Resource Manager. Korzystanie z programu PowerShell i ARMClient jest jedną z wielu opcji, aby uzyskać dostęp do interfejsu API Search analizy dziennika. Te narzędzia mogą korzystać z RESTful interfejs API Menedżera zasobów Azure do wykonywania wywołań do obszarów roboczych OMS i wykonywania poleceń wyszukiwania w nich. Interfejs API dane wyjściowe obejmują wyniki wyszukiwania do użytkownika w formacie JSON, co umożliwia używanie wyniki wyszukiwania na wiele różnych sposobów programowo.

## <a name="prerequisites"></a>Wymagania wstępne
Obecnie usługa alertów można tworzyć tylko z zapisanej operacji wyszukiwania w analizy dzienników.  Można to sprawdzić [interfejsu API REST wyszukiwania dziennika](log-analytics-log-search-api.md) Aby uzyskać więcej informacji.

## <a name="schedules"></a>Harmonogramy
Zapisane wyszukiwanie może mieć co najmniej jeden harmonogram. Harmonogram Określa, jak często wyszukiwania jest uruchamiania i przedział czasu, w którym określono kryteria.
Harmonogramy mają właściwości w poniższej tabeli.

| Właściwość | Opis |
|:--- |:--- |
| Interwał |Jak często jest wykonywane wyszukiwania. (W minutach). |
| QueryTimeSpan |Przedział czasu, w którym jest oceniana kryteria. Musi być równa lub większa od wartości interwału. (W minutach). |
| Wersja |Używana wersja interfejsu API.  Obecnie ta powinna zawsze być ustawiona na 1. |

Rozważmy na przykład kwerendy zdarzeń z interwałem 15 minut i zakres czasu 30 minut. W takim przypadku zapytanie zostanie uruchomione co 15 minut, a alert będzie uruchomiony, jeśli kryteria nadal rozpoznać na wartość true w 30-minutowych należy do zakresu.

### <a name="retrieving-schedules"></a>Trwa pobieranie harmonogramów
Pobierz wszystkie harmonogramy dla zapisanego wyszukiwania za pomocą metody Get.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules?api-version=2015-03-20

Pobierz konkretny harmonogram dla zapisanego wyszukiwania za pomocą metody Get o identyfikatorze harmonogramu.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20

Poniżej znajduje się przykładowa odpowiedź dla harmonogramu.

```json
{
    "value": [{
        "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace/savedSearches/0f0f4853-17f8-4ed1-9a03-8e888b0d16ec/schedules/a17b53ef-bd70-4ca4-9ead-83b00f2024a8",
        "etag": "W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\"",
        "properties": {
            "Interval": 15,
            "QueryTimeSpan": 15
        }
    }]
}
```

### <a name="creating-a-schedule"></a>Tworzenie harmonogramu
Za pomocą metody Put identyfikator unikatowy harmonogram do utworzenia nowego harmonogramu.  Należy zauważyć, że dwa harmonogramy nie może sam identyfikator nawet, jeśli są one powiązane z różnymi zapisane wyszukiwania.  Po utworzeniu harmonogramu w konsoli OMS identyfikator GUID jest tworzona dla identyfikator harmonogramu.

> [!NOTE]
> Nazwa dla wszystkich zapisanych wyszukiwań, harmonogramów i akcje utworzone przy użyciu interfejsu API analizy dziennika musi być pisane małymi literami.

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson

### <a name="editing-a-schedule"></a>Edytowanie harmonogramu
Za pomocą metody Put istniejący identyfikator harmonogramu dla tego samego zapisanego wyszukiwania można zmodyfikować tego harmonogramu.  Treść żądania musi zawierać element etag harmonogramu.

      $scheduleJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\""','properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' } }"
      armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson


### <a name="deleting-schedules"></a>Usuwanie harmonogramów
Za pomocą metody Delete identyfikator harmonogramu. Aby usunąć harmonogram.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20


## <a name="actions"></a>Akcje
Harmonogram może mieć wiele akcji. Akcja może określić jeden lub więcej procesów do wykonywania takich jak wysyłanie wiadomości e-mail lub uruchamianie elementu runbook lub mogą określić próg określający, jeśli wyniki wyszukiwania zgodny pewne kryteria.  Niektóre akcje definiują zarówno tak, aby procesy są wykonywane po spełnieniu wartość progową.

Wszystkie działania mają właściwości w poniższej tabeli.  Różne typy alertów mają różne dodatkowe właściwości, które są opisane poniżej.

| Właściwość | Opis |
|:--- |:--- |
| Typ |Typ akcji.  Obecnie możliwe wartości to Alert i elementu Webhook. |
| Name (Nazwa) |Nazwa wyświetlana alertu. |
| Wersja |Używana wersja interfejsu API.  Obecnie ta powinna zawsze być ustawiona na 1. |

### <a name="retrieving-actions"></a>Pobieranie akcji

> [!NOTE]
> Począwszy od 23 kwietnia 2018 wszystkie alerty w obszarze roboczym zostanie automatycznie rozszerzony do platformy Azure. Przed 23 kwietnia 2018 użytkownika dobrowolnie zainicjować rozszerzanie alerty na platformie Azure. Aby uzyskać więcej informacji, zobacz [rozszerzyć alerty na platformie Azure z usługą OMS](../monitoring-and-diagnostics/monitoring-alerts-extend.md). Dla użytkowników, rozszerzające alertach do platformy Azure akcje teraz są kontrolowane w grupach Azure akcji. Gdy obszar roboczy i jego alerty zostały rozszerzone na platformie Azure, można pobrać lub dodać akcje za pomocą [API grupy akcji](https://docs.microsoft.com/en-us/rest/api/monitor/actiongroups).

Pobierz wszystkie akcje dla harmonogramu za pomocą metody Get.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules/{Schedule ID}/actions?api-version=2015-03-20

Za pomocą metody Get identyfikator akcji można pobrać określonej akcji dla harmonogramu.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/actions/{Action ID}?api-version=2015-03-20

### <a name="creating-or-editing-actions"></a>Tworzenie lub edytowanie akcji
Za pomocą metody Put identyfikator akcji, która jest unikatowa w harmonogramie, aby utworzyć nową akcję.  Po utworzeniu akcji w konsoli OMS identyfikator GUID jest identyfikatora akcji.

> [!NOTE]
> Nazwa dla wszystkich zapisanych wyszukiwań, harmonogramów i akcje utworzone przy użyciu interfejsu API analizy dziennika musi być pisane małymi literami.

Za pomocą metody Put istniejącego Identyfikatora akcji dla tego samego zapisanego wyszukiwania można zmodyfikować tego harmonogramu.  Treść żądania musi zawierać element etag harmonogramu.

Format żądania do tworzenia nowej akcji zależy od typu akcji, te przykłady znajdują się w poniższych sekcjach.

### <a name="deleting-actions"></a>Usuwanie akcji

> [!NOTE]
> Począwszy od 23 kwietnia 2018 wszystkie alerty w obszarze roboczym zostanie automatycznie rozszerzony do platformy Azure. Przed 23 kwietnia 2018 użytkownika dobrowolnie zainicjować rozszerzanie alerty na platformie Azure. Aby uzyskać więcej informacji, zobacz [rozszerzyć alerty na platformie Azure z usługą OMS](../monitoring-and-diagnostics/monitoring-alerts-extend.md). Dla użytkowników, rozszerzające alertach do platformy Azure akcje teraz są kontrolowane w grupach Azure akcji. Gdy obszar roboczy i jego alerty zostały rozszerzone na platformie Azure, można pobrać lub dodać akcje za pomocą [API grupy akcji](https://docs.microsoft.com/en-us/rest/api/monitor/actiongroups).

Za pomocą metody Delete identyfikator akcji można usunąć akcji.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/Actions/{Action ID}?api-version=2015-03-20

### <a name="alert-actions"></a>Akcje alertu
Harmonogram ma jeden i tylko jeden Alert akcji.  Akcje alertu występuje jeden lub kilka z sekcji w poniższej tabeli.  Każdy jest opisany bardziej szczegółowo poniżej.

| Sekcja | Opis | Sposób użycia |
|:--- |:--- |:--- |
| Próg |Kryteria po uruchomieniu akcji.| Wymagany w przypadku każdego alertu przed lub po ich zostały rozszerzone na platformie Azure. |
| Ważność |Etykieta służy do klasyfikowania alert po wyzwoleniu.| Wymagany w przypadku każdego alertu przed lub po ich zostały rozszerzone na platformie Azure. |
| Grupy akcji |Identyfikatory Właściwość ActionGroup Azure określoną wymagane akcje, takie jak — wiadomości E-mail, Menedżer SMSs, połączeń głosowych, elementów Webhook, element Runbook usługi Automatyzacja, zarządzanie usługami IT — łączniki itp.| Wymagane, gdy alerty zostały rozszerzone na platformie Azure|
| Dostosuj akcje|Modyfikowanie standardowego wyjścia dla akcji wybierz właściwość ActionGroup| Można opcjonalnie dla każdego alertu, po alerty zostały rozszerzone na platformie Azure. |
| EmailNotification |Wysyłanie poczty do wielu adresatów. | Nie jest to wymagane, jeśli alerty zostały rozszerzone na platformie Azure|
| Korygowanie |Uruchom element runbook automatyzacji Azure, aby spróbować rozwiązać problem zidentyfikowane. |Nie jest to wymagane, jeśli alerty zostały rozszerzone na platformie Azure|
| Akcje elementu Webhook | Wypychanie danych z alertami, do żądanej usługi w formacie JSON |Nie jest to wymagane, jeśli alerty zostały rozszerzone na platformie Azure|

> [!NOTE]
> Począwszy od 23 kwietnia 2018 wszystkie alerty w obszarze roboczym zostanie automatycznie rozszerzony do platformy Azure. Przed 23 kwietnia 2018 użytkownika dobrowolnie zainicjować rozszerzanie alerty na platformie Azure. Aby uzyskać więcej informacji, zobacz [rozszerzyć alerty na platformie Azure z usługą OMS](../monitoring-and-diagnostics/monitoring-alerts-extend.md).

#### <a name="thresholds"></a>Progi
Akcja alertu powinna mieć jeden i tylko jeden próg.  Jeśli wyniki zapisanego kryterium wyszukiwania zgodne próg w akcji skojarzonej z tym wyszukiwania, są uruchamiane żadnych innych procesów w tej akcji.  Akcja może również zawierać tylko wartości progowej, aby mogą być używane z akcjami innych typów, które nie zawierają wartości progowe.

Progi mają właściwości w poniższej tabeli.

| Właściwość | Opis |
|:--- |:--- |
| Operator |Operator porównania wartości progowej. <br> gt = większe niż <br> lt = mniej niż |
| Wartość |Wartość progu. |

Rozważmy na przykład kwerendy interwał 15 minut, Timespan 30 minut, a próg większym niż 10. W takim przypadku zapytanie zostanie uruchomione co 15 minut, a alert będzie uruchomiony, jeśli zwróceniem 10 zdarzeń, które zostały utworzone w danym okresie 30 minut.

Poniżej znajduje się przykładowa odpowiedź dla akcji z tylko wartości progowej.  

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
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

Za pomocą metody Put istniejącego Identyfikatora akcji można zmodyfikować akcję próg dla harmonogramu.  Treść żądania musi zawierać element etag akcji.

    $thresholdJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

#### <a name="severity"></a>Ważność
Analiza dzienników umożliwia klasyfikowanie alertów w kategorie, aby umożliwić łatwiejsze zarządzanie i klasyfikacji. Ważność alertu zdefiniowane jest: informacyjne, ostrzegawcze i krytyczne. Te są mapowane na skali znormalizowane ważność alertów Azure jako:

|Poziom ważności analizy dzienników  |Poziom ważności alertów Azure  |
|---------|---------|
|Krytyczne |Ważność 0|
|ostrzeżenie |Ważność 1|
|Informacyjny | Ważność 2|

Poniżej znajduje się przykładowa odpowiedź dla akcji przy użyciu progu i ważności. 

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

Za pomocą metody Put identyfikator unikatowy akcji do utworzenia nowej akcji harmonogramu z ważności.  

    $thresholdWithSevJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson

Za pomocą metody Put istniejącego Identyfikatora akcji można zmodyfikować akcję ważności dla harmonogramu.  Treść żądania musi zawierać element etag akcji.

    $thresholdWithSevJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson

#### <a name="action-groups"></a>Grupy akcji
Wszystkie alerty na platformie Azure, użyj akcji grupy jako domyślnego mechanizmu obsługi akcji. Z grupy akcji można określić czynności użytkownika raz i następnie skojarzyć grupy akcji do wielu alertów — na platformie Azure. Bez konieczności wielokrotnego zadeklarować wielokrotnie te same akcje. Grupy akcji obsługuje wiele działań — w tym wiadomości e-mail, programu SMS, wywołania głosu, zarządzanie usługami IT — połączenie, elementu Runbook automatyzacji, identyfikator URI elementu Webhook i więcej. 

Dla użytkownika, przez który rozszerzono alerty na platformie Azure — harmonogram ma teraz szczegółów grupy akcji przekazywane wraz z wartości progowej, aby można było utworzyć alertu. Szczegóły poczty e-mail, adresy URL elementu Webhook szczegóły automatyzacji elementu Runbook i inne akcje, muszą być zdefiniowane w stronie grupy akcji przed tworzenia alertu; można utworzyć [grupy akcji z monitora Azure](../monitoring-and-diagnostics/monitoring-action-groups.md) w portalu lub użyj [API grupy akcji](https://docs.microsoft.com/en-us/rest/api/monitor/actiongroups).

Aby dodać skojarzenie grupy akcji do alertu, należy określić unikatowy identyfikator menedżera zasobów Azure grupy działań w definicji alertu. Ilustracja przykładowa znajduje się poniżej:

     "etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
      "properties": {
        "Type": "Alert",
        "Name": "test-alert",
        "Description": "I need to put a descriptio here",
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

Za pomocą metody Put identyfikator unikatowy akcji można skojarzyć już istniejącą grupę akcji dla harmonogramu.  Poniżej znajduje się przykładowy Ilustracja użycia.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Za pomocą metody Put istniejącego Identyfikatora akcji można zmodyfikować grupy akcji skojarzony z harmonogramem.  Treść żądania musi zawierać element etag akcji.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

#### <a name="customize-actions"></a>Dostosuj akcje
Akcje domyślne, wykonując standardowego szablonu i format powiadomień. Jednak użytkownik może dostosować niektóre akcje, nawet jeśli są one kontrolowane przez grupy akcji. Temat wiadomości E-mail i ładunku elementu Webhook jest obecnie dostosowania.

##### <a name="customize-e-mail-subject-for-action-group"></a>Temat wiadomości E-Mail do grupy akcji dostosowania
Domyślnie jest temat wiadomości e-mail dla alertów: powiadomień o alertach <AlertName> dla <WorkspaceName>. Jednak to można dostosować, tak aby można było słów lub tagi — pozwala łatwo stosować reguły filtrowania w Twojej skrzynce odbiorczej. Dostosuj szczegóły nagłówka wiadomości e-mail muszą do wysyłania oraz Właściwość ActionGroup uzyskać szczegółowe informacje, jak w poniższym przykładzie.

     "etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
      "properties": {
        "Type": "Alert",
        "Name": "test-alert",
        "Description": "I need to put a descriptio here",
        "Threshold": {
          "Operator": "gt",
          "Value": 12
        },
        "AzNsNotification": {
          "GroupIds": [
            "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
          ]
          "CustomEmailSubject": "Azure Alert fired"
        },
        "Severity": "critical",
        "Version": 1
      },

Za pomocą metody Put identyfikator unikatowy akcji do skojarzenia z istniejącej grupy akcji z dostosowywania harmonogramu.  Poniżej znajduje się przykładowy Ilustracja użycia.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Za pomocą metody Put istniejącego Identyfikatora akcji można zmodyfikować grupy akcji skojarzony z harmonogramem.  Treść żądania musi zawierać element etag akcji.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired' }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

##### <a name="customize-webhook-payload-for-action-group"></a>Dostosowywanie ładunku elementu Webhook dla grupy akcji
Domyślnie wysyłane za pośrednictwem grupy akcji dla analizy dzienników elementu webhook ma stałą strukturę. Ale co można dostosować ładunek JSON przy użyciu określonych zmiennych obsługiwane, aby spełnić wymagania dotyczące punktu końcowego elementu webhook. Aby uzyskać więcej informacji, zobacz [akcji elementu Webhook dla reguł alertów dziennika](../monitoring-and-diagnostics/monitor-alerts-unified-log-webhook.md). 

Szczegóły elementu webhook Dostosuj konieczne wysyłania oraz szczegóły Właściwość ActionGroup i będzie można zastosować do wszystkich Webhook identyfikator URI określony w grupie akcji; jak w poniższym przykładzie.

     "etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
      "properties": {
        "Type": "Alert",
        "Name": "test-alert",
        "Description": "I need to put a descriptio here",
        "Threshold": {
          "Operator": "gt",
          "Value": 12
        },
        "AzNsNotification": {
          "GroupIds": [
            "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
          ]
          "CustomWebhookPayload": "{\"field1\":\"value1\",\"field2\":\"value2\"}",
          "CustomEmailSubject": "Azure Alert fired"
        },
        "Severity": "critical",
        "Version": 1
      },

Za pomocą metody Put identyfikator unikatowy akcji do skojarzenia z istniejącej grupy akcji z dostosowywania harmonogramu.  Poniżej znajduje się przykładowy Ilustracja użycia.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Za pomocą metody Put istniejącego Identyfikatora akcji można zmodyfikować grupy akcji skojarzony z harmonogramem.  Treść żądania musi zawierać element etag akcji.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}' }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

#### <a name="email-notification"></a>Powiadomienie e-mail
Powiadomienia e-mail wysyłanie poczty do co najmniej jednego adresata.  Obejmują one właściwości w poniższej tabeli.

> [!NOTE]
> Począwszy od 23 kwietnia 2018 wszystkie alerty w obszarze roboczym zostanie automatycznie rozszerzony do platformy Azure. Przed 23 kwietnia 2018 użytkownika dobrowolnie zainicjować rozszerzanie alerty na platformie Azure. Aby uzyskać więcej informacji, zobacz [rozszerzyć alerty na platformie Azure z usługą OMS](../monitoring-and-diagnostics/monitoring-alerts-extend.md). Dla użytkowników, rozszerzające alertach do platformy Azure akcji, takich jak powiadomienia E-Mail teraz są kontrolowane w grupach Azure akcji. Gdy obszar roboczy i jego alerty zostały rozszerzone na platformie Azure, można pobrać lub dodać akcje za pomocą [API grupy akcji](https://docs.microsoft.com/en-us/rest/api/monitor/actiongroups).
   

| Właściwość | Opis |
|:--- |:--- |
| Adresaci |Lista adresów e-mail. |
| Temat |Temat wiadomości e-mail. |
| Załącznik |Załączniki nie są obecnie obsługiwane, więc zawsze będą mieć wartość "None." |

Poniżej znajduje się przykładowa odpowiedź dla akcji powiadomienia e-mail o progu.  

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

Za pomocą metody Put identyfikator unikatowy akcji można utworzyć nowej wiadomości e-mail akcji dla harmonogramu.  Poniższy przykład tworzy wiadomość e-mail z powiadomieniem o progu, więc poczty jest wysyłany, gdy wyniki wyszukiwania przekracza wartość progową.

    $emailJson = "{'properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $emailJson

Aby zmodyfikować działanie poczty e-mail dla harmonogramu, należy użyć metody Put z istniejącego Identyfikatora akcji.  Treść żądania musi zawierać element etag akcji.

    $emailJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $emailJson

#### <a name="remediation-actions"></a>Akcje naprawcze
Korygowaniami uruchomienia elementu runbook w automatyzacji Azure, które próbuje naprawić problem identyfikowana na podstawie alertu.  Należy utworzyć elementu webhook dla elementu runbook, używany w Akcja korygowania i następnie określ identyfikator URI we właściwości WebhookUri.  Podczas tworzenia tej akcji, używając konsoli OMS nowego elementu webhook jest tworzona automatycznie dla elementu runbook.

> [!NOTE]
> Począwszy od 23 kwietnia 2018 wszystkie alerty w obszarze roboczym zostanie automatycznie rozszerzony do platformy Azure. Przed 23 kwietnia 2018 użytkownika dobrowolnie zainicjować rozszerzanie alerty na platformie Azure. Aby uzyskać więcej informacji, zobacz [rozszerzyć alerty na platformie Azure z usługą OMS](../monitoring-and-diagnostics/monitoring-alerts-extend.md). Dla użytkowników, rozszerzające alertach do platformy Azure akcji, takich jak korygowania przy użyciu elementu runbook teraz są kontrolowane w grupach Azure akcji. Gdy obszar roboczy i jego alerty zostały rozszerzone na platformie Azure, można pobrać lub dodać akcje za pomocą [API grupy akcji](https://docs.microsoft.com/en-us/rest/api/monitor/actiongroups).

Korygowaniami obejmują właściwości w poniższej tabeli.

| Właściwość | Opis |
|:--- |:--- |
| RunbookName |Nazwa elementu runbook. To musi być zgodna opublikowanego elementu runbook w ramach konta automatyzacji skonfigurowane w rozwiązaniu automatyzacji w obszarze roboczym pakietu OMS. |
| WebhookUri |Identyfikator URI elementu webhook. |
| Data wygaśnięcia |Data wygaśnięcia i godzina elementu webhook.  Jeśli elementu webhook nie ma wygaśnięcia, może to być wszystkie prawidłową datą przyszłą. |

Poniżej znajduje się przykładowa odpowiedź dla akcji korygowania o progu.

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

Za pomocą metody Put identyfikator unikatowy akcji do utworzenia nowej akcji korygowania harmonogramu.  Poniższy przykład tworzy korygowania o progu, aby uruchomić elementu runbook, gdy wyniki wyszukiwania przekracza wartość progową.

    $remediateJson = "{'properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

Za pomocą metody Put istniejącego Identyfikatora akcji można zmodyfikować akcji korygowania, harmonogramu.  Treść żądania musi zawierać element etag akcji.

    $remediateJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

#### <a name="example"></a>Przykład
Poniżej znajduje się pełny przykład można utworzyć nowy alert e-mail.  Spowoduje to utworzenie nowego harmonogramu wraz z akcji zawierający próg i poczty e-mail.

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
Akcje elementu Webhook uruchomienia procesu podczas wywoływania adresu URL i opcjonalnie podając ładunku do wysłania.  Są one podobne do akcji korygowania, z wyjątkiem są przeznaczone dla elementów webhook, które może wywołać procesy inne niż elementy runbook automatyzacji Azure.  Zawierają także dodatkowe opcja podania ładunku mają zostać dostarczone do zdalnego procesu.

> [!NOTE]
> Począwszy od 23 kwietnia 2018 wszystkie alerty w obszarze roboczym zostanie automatycznie rozszerzony do platformy Azure. Przed 23 kwietnia 2018 użytkownika dobrowolnie zainicjować rozszerzanie alerty na platformie Azure. Aby uzyskać więcej informacji, zobacz [rozszerzyć alerty na platformie Azure z usługą OMS](../monitoring-and-diagnostics/monitoring-alerts-extend.md). Dla użytkowników, rozszerzające alertach do platformy Azure akcji, takich jak Webhook teraz są kontrolowane w grupach Azure akcji. Gdy obszar roboczy i jego alerty zostały rozszerzone na platformie Azure, można pobrać lub dodać akcje za pomocą [API grupy akcji](https://docs.microsoft.com/en-us/rest/api/monitor/actiongroups).


Akcje elementu Webhook nie mają tego progu, ale zamiast tego powinni zostać dodani do harmonogram, który ma akcję alertu o progu.  

Poniżej znajduje się przykładowa odpowiedź dla elementu webhook akcji i skojarzonych alertach o progu.

    {
        "__metadata": {},
        "value": [
            {
                "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/bwren/savedSearches/2d1b30fb-7f48-4de5-9614-79ee244b52de/schedules/b80f5621-7217-4007-b32d-165d14377093/Actions/72884702-acf9-4653-bb67-f42436b342b4",
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
                "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/bwren/savedSearches/2d1b30fb-7f48-4de5-9614-79ee244b52de/schedules/b80f5621-7217-4007-b32d-165d14377093/Actions/90a27cf8-71b7-4df2-b04f-54ed01f1e4b6",
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

##### <a name="create-or-edit-a-webhook-action"></a>Utwórz lub Edytuj akcji elementu webhook
Za pomocą metody Put identyfikator unikatowy akcji do utworzenia nowej akcji elementu webhook dla harmonogramu.  Poniższy przykład tworzy akcji elementu Webhook i alertów o progu, dzięki czemu elementu webhook będą wyzwalane, gdy wyniki wyszukiwania przekracza wartość progową.

    $thresholdAction = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdAction

    $webhookAction = "{'properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction

Za pomocą metody Put istniejącego Identyfikatora akcji można zmodyfikować akcję elementu webhook dla harmonogramu.  Treść żądania musi zawierać element etag akcji.

    $webhookAction = "{'etag': 'W/\"datetime'2016-02-26T20%3A25%3A00.6862124Z'\"','properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction


## <a name="next-steps"></a>Kolejne kroki
* Użyj [interfejsu API REST do wyszukiwania dziennika](log-analytics-log-search-api.md) w analizy dzienników.
* Dowiedz się więcej o [rejestrowania alertów w alertach azure](../monitoring-and-diagnostics/monitor-alerts-unified-log.md)

