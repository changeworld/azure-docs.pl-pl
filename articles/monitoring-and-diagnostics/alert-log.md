---
title: Tworzenie, wyświetlanie i zarządzanie dziennika alertów za pomocą usługi Azure Monitor
description: Usługi Azure Monitor umożliwia tworzenie, wyświetlanie i zarządzanie reguł alertów dzienników na platformie Azure.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/15/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: c2f018915d5e27d4cf16447e403fcb2e1b3f55a6
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "52969519"
---
# <a name="create-view-and-manage-log-alerts-using-azure-monitor"></a>Tworzenie, wyświetlanie i zarządzanie alerty dzienników przy użyciu usługi Azure Monitor  

## <a name="overview"></a>Przegląd
W tym artykule pokazano, jak skonfigurować alerty dzienników przy użyciu interfejsu alertów w witrynie Azure portal. Definicja reguły alertu znajduje się w trzech części:
- Cel: Określony zasób platformy Azure, która ma być monitorowane
- Kryteria: Określony warunek lub Logic Apps, gdy widoczne w sygnał, powinny wyzwalać akcję
- Akcja: Wywołań wysyłanych do odbiorcy powiadomienia — Wyślij wiadomość e-mail, wiadomość SMS, element webhook itp.

Termin **alertów dzienników** do opisania alerty, gdy sygnał jest zapytanie niestandardowe na podstawie [usługi Log Analytics](../azure-monitor/learn/tutorial-viewdata.md) lub [usługi Application Insights](../application-insights/app-insights-analytics.md). Dowiedz się więcej o funkcji, terminologii i typów z [rejestrowania alertów — omówienie](monitor-alerts-unified-log.md).

> [!NOTE]
> Dane dzienników popularnych z [usługi Azure Log Analytics](../azure-monitor/learn/tutorial-viewdata.md) jest obecnie również dostępna na platformie metryki w usłudze Azure Monitor. Dla widoku szczegółów [alertu metryki dla dzienników](monitoring-metric-alerts-logs.md)

## <a name="managing-log-alerts-from-the-azure-portal"></a>Zarządzanie alertami dzienników w witrynie Azure portal

Szczegółowe dalej jest przewodnik krok po kroku za pomocą alertów dzienników przy użyciu interfejsu portalu platformy Azure.

### <a name="create-a-log-alert-rule-with-the-azure-portal"></a>Tworzenie reguł alertów dzienników w witrynie Azure portal
1. W [portal](https://portal.azure.com/), wybierz opcję **Monitor** i w sekcji monitorowanie — wybierz **alerty**.  
    ![Monitorowanie](media/alert-log/AlertsPreviewMenu.png)

1. Wybierz **nową regułę alertu** przycisk, aby utworzyć nowego alertu na platformie Azure.
    ![Dodaj Alert](media/alert-log/AlertsPreviewOption.png)

1. W sekcji Tworzenie alertu jest przedstawiana wraz z trzech części, składający się z: *Zdefiniuj warunek alertu*, *Zdefiniuj szczegóły alertu*, i *zdefiniuj grupę akcji*.

    ![Utwórz regułę](media/alert-log/AlertsPreviewAdd.png)

1.  Zdefiniuj warunek alertu przy użyciu **wybierz zasób** łącze i określenie docelowej wybierz zasób. Filtr, wybierając _subskrypcji_, _typ zasobu_oraz wymagane _zasobów_. 

    >[!NOTE]

    > Do tworzenia dziennika alertów — weryfikowanie **dziennika** sygnał jest dostępna dla wybranego zasobu, przed kontynuowaniem.
    ![Wybierz zasób](media/alert-log/Alert-SelectResourceLog.png)

 
1. *Alerty dzienników*: Upewnij się, **typ zasobu** jest źródłem analizy, takie jak *usługi Log Analytics* lub *usługi Application Insights* i sygnałów typ jako **dziennika** , następnie raz właściwe **zasobów** jest wybrana, kliknij przycisk *gotowe*. Dalej używać **Dodaj kryteria** przycisk, aby wyświetlić listę sygnałów dostępnymi dla zasobu, a z listy sygnału **przeszukiwania dzienników niestandardowych** opcję dla wybranego dziennika usługi monitor, takich jak *dziennika Analiza* lub *usługi Application Insights*.

   ![Wybierz zasób — wyszukiwanie dzienników niestandardowych](media/alert-log/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]

   > Listy można zaimportować zapytania usługi analytics jako typ sygnału — alerty **dziennika (zapisane zapytanie)**, jak pokazano powyżej ilustracji. Dzięki temu użytkownicy może zapytania w usłudze Analytics udoskonalania i zapisać je do użytku w przyszłości w alertach — więcej szczegółowych informacji na temat korzystania z Zapisywanie zapytań dostępne pod adresem [przy użyciu przeszukiwania dzienników w usłudze log analytics](../azure-monitor/log-query/log-query-overview.md) lub [udostępnionego zapytania w usłudze application insights Analiza](../azure-monitor/log-query/log-query-overview.md). 

1.  *Alerty dzienników*: po wybraniu zapytania dotyczące alertów może być wyrażona w **zapytania wyszukiwania** pola; Jeśli składnia zapytania jest nieprawidłowa, pola są wyświetlane w kolorze CZERWONYM błędu. Jeśli składnia zapytania jest poprawna, — do użytku w danych historycznych w określonej kwerendy jest wyświetlany jako wykres z opcją, aby dostosować okno czasowe z ostatnich sześciu godzin do ostatniego tygodnia.

 ![Konfigurowanie reguły alertu](media/alert-log/AlertsPreviewAlertLog.png)

 > [!NOTE]

    > Wizualizacja danych historycznych mogą być wyświetlane tylko jeśli wyniki zapytania mają szczegóły godziny. Jeśli zapytanie powoduje podsumowane dane lub wartości określonej kolumny — sama jest wyświetlany jako pojedynczej powierzchni.

    >  Dla typu pomiar metryki alertów dzienników za pomocą usługi Application insights, można określić, które określonej zmiennej, aby pogrupować dane przy użyciu **agregowane na** opcji; co przedstawiono poniżej:

    ![Agreguj według opcji](media/alert-log/aggregate-on.png)

1.  *Alerty dzienników*: wizualizacji w miejscu, **Alert Logic** można wybrać opcje pokazano warunku, agregacji i na koniec próg. Wskaż w logice, czas oceny pod kątem określonego warunku, przy użyciu **okres** opcji. Wraz z częstotliwość uruchamiania alertu, wybierając **częstotliwość**.
Aby uzyskać **alertów dzienników** alerty mogą być oparte na:
   - *Liczba rekordów*: alert jest tworzony, jeśli liczba rekordów zwróconych przez zapytanie jest większa lub mniejsza niż podana wartość.
   - *Pomiar metryki*: alert jest tworzony, jeśli każdy *agregować wartości* w wynikach przekracza wartość progową, pod warunkiem, i jest *pogrupowane według* wybrana wartość. Liczba naruszeń alertu jest liczba przypadków, gdy próg został przekroczony w wybranym okresie. Łączna liczba naruszeń w jakiejkolwiek kombinacji naruszeń można określić przez zestaw wyników lub kolejne naruszenia, aby wymagać, że naruszeń musi wystąpić w kolejnych próbek. Dowiedz się więcej o [alertów dzienników i ich typy](monitor-alerts-unified-log.md).


1. W drugim kroku należy zdefiniować nazwę alertu w **Nazwa reguły alertu** pola wraz z **opis** szczegółowych informacji na temat specyfiki alertu i **ważność** z wartości Opcje przekazane. Te informacje są używane ponownie w wiadomości e-mail alertów, powiadomień lub wypychania wykonywane przez usługi Azure Monitor. Ponadto użytkownik może wybrać od razu aktywować regułę alertu przy tworzeniu odpowiednio przełączając **Włącz regułę po utworzeniu** opcji.

    Aby uzyskać **alertów dzienników** tylko kilka dodatkowych funkcji jest dostępna w szczegółach alertu:

    - **Pomiń alerty**: po włączeniu funkcji pomijania reguła alertu akcje dla reguły są wyłączone w przypadku określona długość czasu, po utworzeniu nowego alertu. Reguła jest nadal uruchomione i tworzy rekordy alertów, pod warunkiem spełnienia kryteriów. Umożliwiając czasu, należy rozwiązać problem bez konieczności uruchamiania zduplikowane akcji.

        ![Pomiń alerty dla dziennika alertów](media/alert-log/AlertsPreviewSuppress.png)

        > [!TIP]
        > Określ wartość alertu Pomiń większe niż częstotliwość alertu, aby upewnić się, że powiadomienia zostały zatrzymane bez nakładania się

1. W trzeciej i ostatniej kroku określ ewentualne **grupy akcji** musi być wyzwalane dla reguły alertu, gdy zostanie spełniony warunek alertu. Można wybrać wszystkie istniejące grupy akcji z alertem lub Utwórz nową grupę akcji. Zgodnie z opisem w wybrane grupy akcji, gdy alert jest wyzwalacza Azure będzie: wysyłanie email(s), Wyślij SMS(s), wywoływać elementy webhook, korygowanie za pomocą elementów Runbook usługi Azure, wypychania narzędziem ITSM, itp. Dowiedz się więcej o [grup akcji](monitoring-action-groups.md).

    > [!NOTE]
    > Zapoznaj się [limity usług subskrypcji platformy Azure](../azure-subscription-service-limits.md) limity ładunków Runbook wyzwolone dla dziennika alertów za pośrednictwem grup akcji platformy Azure

    Aby uzyskać **alertów dzienników** kilka dodatkowych funkcji jest dostępny zastąpić domyślne akcje:

    - **Powiadomienia e-mail**: zastępuje *temat wiadomości e-mail* w wiadomości e-mail wysyłane za pośrednictwem grupy akcji, jeśli istnieje co najmniej jednej akcji poczty e-mail, wspomnianych grupy akcji. Nie można zmodyfikować treść wiadomości e-mail i to pole jest **nie** adresu e-mail.
    - **Uwzględnij niestandardowy ładunek Json**: zastępuje element webhook JSON używany przez grupy akcji; jeśli istnieje co najmniej jednej akcji elementu webhook, wspomnianych grupy akcji. Użytkownik może określić format JSON ma być używany dla wszystkich elementów webhook skonfigurowane w skojarzonej grupy akcji; Aby uzyskać więcej informacji dotyczących formatów elementu webhook, zobacz [Akcja elementu webhook dla dziennika alertów](monitor-alerts-unified-log-webhook.md). Opcja Widok do elementu Webhook jest udostępniane na Sprawdź format przy użyciu przykładowych danych JSON.

        ![Akcja zastąpienia dla dziennika alertów](media/alert-log/AlertsPreviewOverrideLog.png)


1. Jeśli wszystkie pola są prawidłowe i za pomocą znaczników zielony **Utwórz regułę alertu** kliknięcie przycisku, a alert jest tworzony w usłudze Azure Monitor — alerty. Wszystkie alerty mogą być wyświetlane z alerty pulpitu nawigacyjnego.

    ![Tworzenie reguł](media/alert-log/AlertsPreviewCreate.png)

    W ciągu kilku minut ten alert jest aktywny i wyzwala w sposób opisany wcześniej.

Użytkownicy mogą również zakończona ich zapytania usługi analytics w [analizy dzienników strony w witrynie Azure portal](../azure-monitor/log-query/portals.md#log-analytics-page
) i Wypchnij go do utworzenia alertu, klikając przycisk "Ustaw Alert" - następnie postępując zgodnie z instrukcjami krok 6 lub nowszym w tym samouczku powyżej.

 ![Log Analytics — Ustawianie alertu](media/alert-log/AlertsAnalyticsCreate.png)

### <a name="view--manage-log-alerts-in-azure-portal"></a>Wyświetlanie i zarządzanie alertów dzienników w witrynie Azure portal

1. W [portal](https://portal.azure.com/), wybierz opcję **Monitor** i w sekcji monitorowanie — wybierz **alerty**.  

1. **Pulpit nawigacyjny alertów** jest wyświetlany - polegającego wszystkie alerty platformy Azure (w tym alertów dzienników) są wyświetlane w pojedynczej tablicy; w tym gdy reguła alertu dziennika każde wystąpienie zostało uruchomione. Aby dowiedzieć się więcej, zobacz [zarządzania alertami](https://aka.ms/managealertinstances).
    > [!NOTE]
    > Reguł alertów dzienników składają się z niestandardowej logiki oparte na zapytaniach Użytkownicy podali i dlatego bez stanie rozwiązanym. Z powodu której za każdym razem, gdy są spełnione warunki określone w reguł alertów dzienników, jej wyzwoleniu. 


1. Wybierz **zarządzanie regułami** znajdujący się na górnym pasku, aby przejść do sekcji Zarządzanie reguły — gdzie znajduje się lista wszystkich reguł alertów utworzone; w tym alerty, które zostały wyłączone.
    ![ Zarządzaj regułami alertów](media/alert-log/manage-alert-rules.png)

## <a name="managing-log-alerts-using-azure-resource-template"></a>Zarządzanie alertami dziennika przy użyciu szablonu usługi Azure Resource
Obecnie dziennik, który alerty mogą być tworzone przy użyciu dwóch różnych szablonów zasobów oparte na jaką platformę analizy alert jest opierać się na (to znaczy) w usłudze Log Analytics lub usługi Application Insights.

Dlatego dalszej części tego artykułu zawierają szczegółowe informacje na temat używania szablonu usługi Resource dla dziennika alertów dla każdej z platform analitycznych.

### <a name="azure-resource-template-for-log-analytics"></a>Szablonu zasobów platformy Azure dla usługi Log Analytics
Alerty dzienników usługi Log Analytics są tworzone przez reguły alertów, uruchamianych zapisanej operacji wyszukiwania w regularnych odstępach czasu. Jeśli wyniki zapytania dopasowania określone kryteria, zostaje utworzony rekord alertu i są uruchamiane co najmniej jednej akcji. 

Szablon zasobu usługi Log analytics, zapisane wyszukiwania i alertów usługi Log analytics są dostępne w sekcji dokumentacji usługi Log Analytics. Aby dowiedzieć się więcej, zobacz [Dodawanie usługi Log Analytics zapisane wyszukiwania i alerty](../azure-monitor/insights/solutions-resources-searches-alerts.md); w tym przykłady ilustrujące, jak również szczegóły schematu.

### <a name="azure-resource-template-for-application-insights"></a>Szablonu zasobów platformy Azure dla usługi Application Insights
Alert dziennika dla zasobów usługi Application Insights ma typ `Microsoft.Insights/scheduledQueryRules/`. Aby uzyskać więcej informacji na temat tego typu zasobu, zobacz [usługi Azure Monitor — zaplanowane reguły dokumentację interfejsu API zapytań](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/).

Poniżej przedstawiono strukturę dla [tworzenia reguł zapytań zaplanowane](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate) na podstawie szablonu zasobów z zestawem danych przykładowych jako zmienne.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0", 
    "parameters": {      
    },   
    "variables": {
    "alertLocation": "southcentralus",
    "alertName": "samplelogalert",
    "alertTag": "hidden-link:/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/components/sampleAIapplication",
    "alertDesription": "Sample log search alert",
    "alertStatus": "true",
    "alertSource":{
        "Query":"requests",
        "SourceId": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/components/sampleAIapplication",
        "Type":"ResultCount"
         },
     "alertSchedule":{
         "Frequency": 15,
         "Time": 60
         },
     "alertActions":{
         "SeverityLevel": "4"
         },
      "alertTrigger":{
        "Operator":"GreaterThan",
        "Threshold":"1"
         },
       "actionGrp":{
        "ActionGroup": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/actiongroups/sampleAG",
        "Subject": "Customized Email Header",
        "Webhook": "{ \"alertname\":\"#alertrulename\", \"IncludeSearchResults\":true }"           
         }
  },
  "resources":[ {
    "name":"[variables('alertName')]",
    "type":"Microsoft.Insights/scheduledQueryRules",
    "apiVersion": "2018-04-16",
    "location": "[variables('alertLocation')]",
    "tags":{"[variables('alertTag')]": "Resource"},
    "properties":{
       "description": "[variables('alertDesription')]",
       "enabled": "[variables('alertStatus')]",
       "source": {
           "query": "[variables('alertSource').Query]",
           "dataSourceId": "[variables('alertSource').SourceId]",
           "queryType":"[variables('alertSource').Type]"
       },
      "schedule":{
           "frequencyInMinutes": "[variables('alertSchedule').Frequency]",
           "timeWindowInMinutes": "[variables('alertSchedule').Time]"    
       },
      "action":{
           "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.AlertingAction",
           "severity":"[variables('alertActions').SeverityLevel]",
           "aznsAction":{
               "actionGroup":"[array(variables('actionGrp').ActionGroup)]",
               "emailSubject":"[variables('actionGrp').Subject]",
               "customWebhookPayload":"[variables('actionGrp').Webhook]"
           },
       "trigger":{
               "thresholdOperator":"[variables('alertTrigger').Operator]",
               "threshold":"[variables('alertTrigger').Threshold]"
           }
       }
     }
   }
 ]
}
```
> [!IMPORTANT]
> Pole znacznika ukryte w Link do zasobu docelowego jest wymagane użycie [zaplanowane reguł zapytań ](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) szablonu połączenia lub zasobu interfejsu API. 

Przykładowy kod json powyżej, można zapisać jako (np.) sampleScheduledQueryRule.json na potrzeby tego przewodnika i można wdrożyć przy użyciu [usługi Azure Resource Manager w witrynie Azure portal](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).


## <a name="managing-log-alerts-using-powershell-cli-or-api"></a>Zarządzanie alertami dziennika przy użyciu programu PowerShell, interfejsu wiersza polecenia lub interfejsu API
Obecnie dziennik, który alerty mogą być tworzone przy użyciu dwóch różnych Menedżera zasobów zgodnych interfejsów API, oparte na jaką platformę analizy alert jest opierać się na (to znaczy) w usłudze Log Analytics lub usługi Application Insights.

Dlatego sekcji poniżej szczegółowo przy użyciu interfejsu API za pośrednictwem programu Powershell lub interfejsu wiersza polecenia dla dziennika alertów dla każdej z platform analitycznych.

### <a name="powershell-cli-or-api-for-log-analytics"></a>Program PowerShell, interfejsu wiersza polecenia lub interfejsu API dla usługi Log Analytics
Log Analytics alertu interfejsu API REST jest zgodne ze specyfikacją REST i możliwy za pośrednictwem interfejsu REST API usługi Azure Resource Manager. Dlatego są dostępne z wiersza polecenia środowiska PowerShell i interfejsu API, w którym zostanie zwróci wyniki wyszukiwania dla użytkownika w formacie JSON, co pozwala na wykorzystanie wyniki na wiele różnych sposobów programowo.

Dowiedz się więcej o [tworzenie i zarządzanie nimi reguły alertów w usłudze Log Analytics przy użyciu interfejsu API REST](../azure-monitor/platform/api-alerts.md), w tym przykłady uzyskiwania dostępu do interfejsu API z poziomu programu Powershell.

### <a name="powershell-cli-or-api-for-application-insights"></a>Program PowerShell, interfejsu wiersza polecenia lub interfejsu API usługi Application Insights
[Usługa Azure Monitor — zaplanowane reguł zapytań interfejsu API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) jest w pełni zgodna z usługą Azure Resource Manager REST API i interfejs API REST. Dlatego można używać za pośrednictwem programu Powershell przy użyciu polecenia cmdlet usługi Resource Manager, a także wiersza polecenia platformy Azure.

Przedstawione poniżej użycia za pomocą polecenia cmdlet programu PowerShell usługi Resource Manager platformy Azure, w przykładzie przedstawionym wcześniej szablon zasobu (sampleScheduledQueryRule.json) w [sekcji zasobów szablonu](#azure-resource-template-for-application-insights) :
```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile "D:\Azure\Templates\sampleScheduledQueryRule.json"
```
Przedstawione poniżej użycia za pomocą polecenia usługi Azure Resource Manager w interfejsie wiersza polecenia platformy Azure w przykładzie przedstawionym wcześniej szablon zasobu (sampleScheduledQueryRule.json) w [sekcji zasobów szablonu](#azure-resource-template-for-application-insights) :

```azurecli
az group deployment create --resource-group myRG --template-file sampleScheduledQueryRule.json
```
Na powodzenie operacji 201 zostanie przywrócony do stanu nowych Tworzenie reguły alertu lub 200 zostanie zwrócona, jeśli istniejącą regułę alertu został zmodyfikowany.


  
## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [alerty dzienników w alertów platformy Azure](monitor-alerts-unified-log.md)
* Zrozumienie [Akcje elementu Webhook dla alertów dzienników](monitor-alerts-unified-log-webhook.md)
* Dowiedz się więcej o [usługi Application Insights](../application-insights/app-insights-analytics.md)
* Dowiedz się więcej o [usługi Log Analytics](../azure-monitor/log-query/log-query-overview.md). 

