---
title: Masz klasycznego alertu metryki powiadomić systemu poza platformą Azure za pomocą elementu webhook
description: Dowiedz się, jak przekierować alertów dotyczących metryk platformy Azure z systemami innych, spoza platformy Azure.
author: snehithm
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/03/2017
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 264f3eb042a3c29523ed93df93dfa6d45c00ae87
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60345796"
---
# <a name="have-a-classic-metric-alert-notify-a-non-azure-system-using-a-webhook"></a>Masz klasycznego alertu metryki powiadomić systemu poza platformą Azure za pomocą elementu webhook
Elementy webhook umożliwia kierowanie powiadomień o alertach platformy Azure z innymi systemami przetwarzania końcowego lub niestandardowych akcji. Element webhook dla alertu dotyczącego służy do kierowania go do usługi, które wysyłanie wiadomości SMS, aby rejestrować błędy, aby powiadomić zespół za pośrednictwem czatu lub usług obsługi wiadomości lub różne inne akcje. 

W tym artykule opisano sposób ustawiania elementu webhook dla alertu dotyczącego metryki platformy Azure. Pokazano także, jak wygląda ładunek HTTP POST do elementu webhook. Informacje o instalacji i schematu dla działania platformy Azure alertu dziennika (alert zdarzeń), zobacz [wywoływania elementu webhook dla alertu dotyczącego dziennika aktywności platformy Azure](alerts-log-webhook.md).

Alerty platformy Azure za pomocą żądania HTTP POST wysyłać zawartość alertu w formacie JSON element webhook identyfikator URI, który podajesz podczas tworzenia alertu. Schemat jest zdefiniowana w dalszej części tego artykułu. Identyfikator URI musi być prawidłowy punkt końcowy HTTP lub HTTPS. Azure publikuje jednego wpisu na żądanie, gdy alert jest aktywny.

## <a name="configure-webhooks-via-the-azure-portal"></a>Konfigurowanie elementów webhook w witrynie Azure portal
Aby dodać lub zaktualizować elementu webhook identyfikatora URI w [witryny Azure portal](https://portal.azure.com/), przejdź do **tworzenia/aktualizacji alertów**.

![Dodawanie okienek reguły alertu](./media/alerts-webhooks/Alertwebhook.png)

Można również skonfigurować alertu do wysłania do elementu webhook identyfikatora URI za pomocą [poleceń cmdlet programu Azure PowerShell](../../azure-monitor/platform/powershell-quickstart-samples.md#create-metric-alerts), [międzyplatformowego interfejsu wiersza polecenia](../../azure-monitor/platform/cli-samples.md#work-with-alerts), lub [interfejsów API REST usługi Azure Monitor](https://msdn.microsoft.com/library/azure/dn933805.aspx).

## <a name="authenticate-the-webhook"></a>Uwierzytelnianie elementu webhook
Element webhook można uwierzytelniać za pomocą opartego na tokenach autoryzacji. Identyfikator URI elementu webhook jest zapisywany z tokenu identyfikatora. Na przykład: `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`

## <a name="payload-schema"></a>Ładunek schematu
Operację POST zawiera następujące ładunek w formacie JSON i schematu dla wszystkich alertów na podstawie metryki:

```JSON
{
    "status": "Activated",
    "context": {
        "timestamp": "2015-08-14T22:26:41.9975398Z",
        "id": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.insights/alertrules/ruleName1",
        "name": "ruleName1",
        "description": "some description",
        "conditionType": "Metric",
        "condition": {
            "metricName": "Requests",
            "metricUnit": "Count",
            "metricValue": "10",
            "threshold": "10",
            "windowSize": "15",
            "timeAggregation": "Average",
            "operator": "GreaterThanOrEqual"
        },
        "subscriptionId": "s1",
        "resourceGroupName": "useast",
        "resourceName": "mysite1",
        "resourceType": "microsoft.foo/sites",
        "resourceId": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1",
        "resourceRegion": "centralus",
        "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1"
    },
    "properties": {
        "key1": "value1",
        "key2": "value2"
    }
}
```


| Pole | Obowiązkowy | Ustalony zbiór wartości | Uwagi |
|:--- |:--- |:--- |:--- |
| status |Tak |Aktywna, rozwiązane |Stan alertu na podstawie warunków można ustawić. |
| Kontekst |Tak | |Kontekst alertu. |
| timestamp |Tak | |Czas wyzwolenia alertu. |
| id |Tak | |Każda reguła alertu ma unikatowy identyfikator. |
| name |Tak | |Nazwa alertu. |
| description |Tak | |Opis alertu. |
| conditionType |Tak |Metryki i zdarzenia |Obsługiwane są dwa typy alertów: metryk i zdarzeń. Alerty metryki są oparte na warunku metryki. Alerty zdarzeń są oparte na zdarzenie w dzienniku aktywności. Użyj tej wartości, aby sprawdzić, czy alert jest na podstawie metryki lub zdarzenia. |
| condition |Tak | |Na podstawie określonych pól do sprawdzenia **conditionType** wartości. |
| MetricName |Dla alertów dotyczących metryk | |Nazwa metryki, który definiuje reguły monitoruje. |
| metricUnit |Dla alertów dotyczących metryk |BytesPerSecond, Count, CountPerSecond, procent, w sekundach, w bajtach |Jednostka dozwolone w metrykę. Zobacz [dozwolone wartości](https://msdn.microsoft.com/library/microsoft.azure.insights.models.unit.aspx). |
| metricValue |Dla alertów dotyczących metryk | |Wartość rzeczywista metryk, który spowodował wygenerowanie alertu. |
| Próg |Dla alertów dotyczących metryk | |Wartość progowa, w którym aktywowano alert. |
| windowSize |Dla alertów dotyczących metryk | |Czas, który jest używany do monitorowania działania alertu, w oparciu o wartość progową. Wartość musi być od 5 minut do 1 dnia. Wartość musi być w formacie czasu trwania ISO 8601. |
| timeAggregation |Dla alertów dotyczących metryk |Średnia, ostatnie, maksimum, Minimum, None, łączna liczba |Jak można łączyć dane, które są zbierane wraz z upływem czasu. Wartość domyślna to średnia. Zobacz [dozwolone wartości](https://msdn.microsoft.com/library/microsoft.azure.insights.models.aggregationtype.aspx). |
| operator |Dla alertów dotyczących metryk | |Operator, który służy do porównywania bieżących danych metryki z wartością progową zestawu. |
| subscriptionId |Tak | |Identyfikator subskrypcji platformy Azure. |
| resourceGroupName |Tak | |Nazwa grupy zasobów dla odpowiedniego zasobu. |
| resourceName |Tak | |Nazwa zasobu zasobu. |
| Typ zasobu |Tak | |Typ zasobu zasobu. |
| resourceId |Tak | |Identyfikator zasobu zasobu. |
| resourceRegion |Tak | |Region lub lokalizacji zasobu. |
| portalLink |Tak | |Bezpośredni link do strony Podsumowanie zasobu portalu. |
| properties |Nie |Optional (Opcjonalność) |Zestaw par klucz wartość zawiera informacje o zdarzeniu. Na przykład `Dictionary<String, String>`. Pole właściwości jest opcjonalne. W przypadku niestandardowego interfejsu użytkownika lub przepływu pracy opartego na aplikacji logiki użytkownicy mogą wprowadzać pary klucz/wartość, które mogą być przekazywane za pośrednictwem ładunku. Alternatywny sposób, aby przekazywać niestandardowe właściwości elementu webhook jest za pomocą elementu webhook identyfikator URI sam (jako parametry kwerendy). |

> [!NOTE]
> Możesz ustawić **właściwości** pole tylko przy użyciu [interfejsów API REST usługi Azure Monitor](https://msdn.microsoft.com/library/azure/dn933805.aspx).
>
>

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej na temat alertów platformy Azure i elementami webhook w filmie wideo [integracji usługi Azure alerts przy użyciu usługi PagerDuty](https://go.microsoft.com/fwlink/?LinkId=627080).
* Dowiedz się, jak [wykonywanie skryptów w usłudze Azure Automation (elementy runbook) na alertów platformy Azure](https://go.microsoft.com/fwlink/?LinkId=627081).
* Dowiedz się, jak [wysyłać wiadomości SMS za pośrednictwem usługi Twilio, z poziomu alertu usługi Azure przy użyciu aplikacji logiki](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app).
* Dowiedz się, jak [wysyłać wiadomość Slack z poziomu alertu platformy Azure przy użyciu aplikacji logiki](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app).
* Dowiedz się, jak [użyj aplikacji logiki, aby wysłać komunikat do kolejki platformy Azure z poziomu alertu usługi Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app).

