---
title: Wywoływanie elementu webhook z klasycznym alertem metryki w usłudze Azure Monitor
description: Dowiedz się, jak przekierować alerty metryki platformy Azure do innych systemów innych systemów innych niż platformy Azure.
author: harelbr
ms.author: harelbr
ms.topic: conceptual
ms.date: 04/03/2017
ms.subservice: alerts
ms.openlocfilehash: 27510871f9a022cb27c6b03b812ce1d37b47312c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248985"
---
# <a name="call-a-webhook-with-a-classic-metric-alert-in-azure-monitor"></a>Wywoływanie elementu webhook z klasycznym alertem metryki w usłudze Azure Monitor

Za pomocą zestawów webhook można kierować powiadomienie alertu platformy Azure do innych systemów do przetwarzania końcowego lub akcji niestandardowych. Za pomocą elementu webhook w alertze można przekierować go do usług, które wysyłają wiadomości SMS, do rejestrowania błędów, powiadamiania zespołu za pośrednictwem czatu lub usług obsługi wiadomości lub do różnych innych działań. 

W tym artykule opisano sposób ustawiania elementu webhook w alertzie metryki platformy Azure. Pokazuje również, jak wygląda ładunek dla http post do elementu webhook. Aby uzyskać informacje na temat konfiguracji i schematu alertu dziennika aktywności platformy Azure (alert o zdarzeniach), zobacz [Wywoływanie elementu webhook w alertie dziennika aktywności platformy Azure.](alerts-log-webhook.md)

Alerty platformy Azure używają protokołu HTTP POST do wysyłania zawartości alertu w formacie JSON do identyfikatora URI elementu webhook, który jest udostępniany podczas tworzenia alertu. Schemat jest zdefiniowany w dalszej części tego artykułu. Identyfikator URI musi być prawidłowym punktem końcowym HTTP lub HTTPS. Platforma Azure księguje jeden wpis na żądanie po aktywowaniu alertu.

## <a name="configure-webhooks-via-the-azure-portal"></a>Konfigurowanie zestawów webhook za pośrednictwem portalu Azure
Aby dodać lub zaktualizować identyfikator URI elementu webhook, w [witrynie Azure portal](https://portal.azure.com/)przejdź do **witryny Tworzenie/aktualizowanie alertów**.

![Dodawanie okienka reguły alertu](./media/alerts-webhooks/Alertwebhook.png)

Można również skonfigurować alert do publikowania w identyfikatorze URI elementu webhook przy użyciu [poleceń cmdlet programu Azure PowerShell,](../../azure-monitor/platform/powershell-quickstart-samples.md#create-metric-alerts) [wieloplatformowego interfejsu wiersza polecenia](../../azure-monitor/platform/cli-samples.md#work-with-alerts)lub [interfejsów API rest usługi Azure Monitor.](https://msdn.microsoft.com/library/azure/dn933805.aspx)

## <a name="authenticate-the-webhook"></a>Uwierzytelnij element webhook
Element webhook można uwierzytelnić przy użyciu autoryzacji opartej na tokenie. Identyfikator URI elementu webhook jest zapisywany przy identyfikatorze tokenu. Na przykład: `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`

## <a name="payload-schema"></a>Schemat ładunku
Operacja POST zawiera następujący ładunek JSON i schemat dla wszystkich alertów opartych na metrykach:

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


| Pole | Obowiązkowy | Stały zestaw wartości | Uwagi |
|:--- |:--- |:--- |:--- |
| status |Tak |Aktywowany, rozwiązany |Stan alertu na podstawie ustawionych warunków. |
| kontekst |Tak | |Kontekst alertu. |
| sygnatura czasowa |Tak | |Czas, w którym alert został uruchomiony. |
| id |Tak | |Każda reguła alertów ma unikatowy identyfikator. |
| name |Tak | |Nazwa alertu. |
| description |Tak | |Opis alertu. |
| warunek Typ |Tak |Metryka, Zdarzenie |Obsługiwane są dwa typy alertów: metryka i zdarzenie. Alerty metryki są oparte na warunku metryki. Alerty o zdarzeniach są oparte na zdarzeniu w dzienniku aktywności. Ta wartość służy do sprawdzania, czy alert jest oparty na metryki lub zdarzenia. |
| Warunek |Tak | |Określone pola do sprawdzenia na podstawie **conditionType** wartość. |
| metricName |W przypadku alertów metrycznych | |Nazwa metryki, która definiuje, co monitoruje reguła. |
| metrycznajednostka |W przypadku alertów metrycznych |Bajty, BajtyPrzekłęd, Liczba, Liczba Na sekundę, Procent, Sekundy |Urządzenie dozwolone w metryce. Zobacz [dozwolone wartości](https://msdn.microsoft.com/library/microsoft.azure.insights.models.unit.aspx). |
| metricValue |W przypadku alertów metrycznych | |Rzeczywista wartość metryki, która spowodowała alert. |
| Próg |W przypadku alertów metrycznych | |Wartość progowa, przy której alert jest aktywowany. |
| windowSize (rozmiar) |W przypadku alertów metrycznych | |Okres czasu, który jest używany do monitorowania aktywności alertów na podstawie progu. Wartość musi wynosić od 5 minut do 1 dnia. Wartość musi być w formacie czasu trwania ISO 8601. |
| timeAggregation |W przypadku alertów metrycznych |Średnia, Ostatnia, Maksymalna, Minimalna, Brak, Razem |Jak należy łączyć zebrane dane w czasie. Wartością domyślną jest Średnia. Zobacz [dozwolone wartości](https://msdn.microsoft.com/library/microsoft.azure.insights.models.aggregationtype.aspx). |
| operator |W przypadku alertów metrycznych | |Operator, który jest używany do porównywania bieżących danych metryki do ustawionego progu. |
| subscriptionId |Tak | |Identyfikator subskrypcji platformy Azure. |
| resourceGroupName |Tak | |Nazwa grupy zasobów dla zasobu, którego dotyczy problem. |
| resourceName |Tak | |Nazwa zasobu, którego dotyczy problem. |
| resourceType |Tak | |Typ zasobu, którego dotyczy problem. |
| resourceId |Tak | |Identyfikator zasobu, którego dotyczy problem. |
| resourceRegion |Tak | |Region lub lokalizacja zasobu, którego dotyczy problem. |
| portalLink (link) |Tak | |Bezpośrednie łącze do strony podsumowania zasobów portalu. |
| properties |Nie |Optional (Opcjonalność) |Zestaw par klucz/wartość zawierający szczegółowe informacje o zdarzeniu. Na przykład `Dictionary<String, String>`. Pole właściwości jest opcjonalne. W niestandardowym interfejsie użytkownika lub obiegu pracy opartym na aplikacji logiki użytkownicy mogą wprowadzać pary klucz/wartość, które mogą być przekazywane za pośrednictwem ładunku. Alternatywnym sposobem przekazywania właściwości niestandardowych z powrotem do elementu webhook jest za pośrednictwem samego identyfikatora URI elementu webhook (jako parametry kwerendy). |

> [!NOTE]
> Pole **właściwości** można ustawić tylko przy użyciu [interfejsów API REST Usługi Azure Monitor](https://msdn.microsoft.com/library/azure/dn933805.aspx).
>
>

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o alertach platformy Azure i elementach webhook w klipie wideo [Integruj alerty platformy Azure z PagerDuty](https://go.microsoft.com/fwlink/?LinkId=627080).
* Dowiedz się, jak [wykonywać skrypty usługi Azure Automation (elementy runbook) w alertach platformy Azure.](https://go.microsoft.com/fwlink/?LinkId=627081)
* Dowiedz się, jak [wysłać wiadomość SMS za pośrednictwem usługi Twilio za pomocą aplikacji logiki z alertu platformy Azure.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
* Dowiedz się, jak [wysłać komunikat Slack z alertu platformy Azure za pomocą aplikacji logiki.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
* Dowiedz się, jak [wysłać wiadomość do kolejki platformy Azure z alertu platformy Azure za pomocą aplikacji logiki.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

