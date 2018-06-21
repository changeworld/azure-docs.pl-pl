---
title: Tworzenie klasycznej alertów dla usług Azure za pomocą wiersza polecenia platformy Azure i platform | Dokumentacja firmy Microsoft
description: Wyzwalanie powiadomienia lub wiadomości e-mail, lub zadzwoń adresów URL witryny sieci Web (elementy webhook) lub automatyzacji, po spełnieniu warunków, które określisz.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/24/2016
ms.author: robb
ms.component: alerts
ms.openlocfilehash: 8112b868bc8d2ca2a9478d38ee702d8b3350d48e
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287111"
---
# <a name="use-the-cross-platform-azure-cli-to-create-classic-metric-alerts-in-azure-monitor-for-azure-services"></a>Tworzenie klasycznego metryki alertów w monitorze Azure dla usług Azure za pomocą wiersza polecenia platformy Azure i platform 

> [!div class="op_single_selector"]
> * [Portal](insights-alerts-portal.md)
> * [Program PowerShell](insights-alerts-powershell.md)
> * [Interfejs wiersza polecenia](insights-alerts-command-line-interface.md)
>
>
> [!NOTE]
> W tym artykule opisano sposób tworzenia starsze klasycznego alerty metryki. Azure obsługuje teraz Monitor [nowszą, lepiej metryki alerty](monitoring-near-real-time-metric-alerts.md). Te alerty można monitorować wiele metryk i umożliwić alertów na wymiarów metryki. Azure CLI obsługę nowszych alerty metryki będzie dostępna wkrótce.
>
>

W tym artykule przedstawiono sposób skonfigurować Azure classic alerty metryki przy użyciu interfejsu wiersza polecenia i platform (Azure CLI).

> [!NOTE]
> Azure Monitor to nowa nazwa dla proponowaną "Azure Insights" do 25 września 2016 r. Jednak przestrzenie nazw, dlatego poleceń, które zostały opisane w tym miejscu nadal zawierać słowo "insights".

Możesz otrzymywać alert oparciu metryki dla usług Azure, lub na podstawie zdarzeń, które występują w systemie Azure.

* **Wartości metryki**: uruchamia alert, gdy wartość określonej metryki przekracza próg, który przypisywać w żadnym kierunku. Oznacza to, że oba wyzwala po raz pierwszy warunek jest spełniony, a następnie po już trwa spełnienia warunku.    

* **Zdarzenia dziennika aktywności**: alert może wyzwolić na *co* zdarzenia lub w przypadku wystąpienia określonych zdarzeń. Aby dowiedzieć się więcej o Dzienniki aktywności, zobacz [Utwórz działanie alertów dziennika (klasyczne)](monitoring-activity-log-alerts.md). 

Można skonfigurować klasycznego alertu metryki wyzwala, wykonaj następujące czynności:

* Administrator usługi i współadministratorzy wysyłania powiadomień e-mail. 
* Wysyłanie określonych adresów e-mail do wiadomości e-mail.
* Wywołanie elementu webhook.
* Uruchamia wykonywanie elementów runbook platformy Azure (tylko z portalu Azure w tym momencie).

Można skonfigurować i uzyskiwania informacji o klasycznym metryki reguły alertów za pomocą następujących czynności: 

* [Witryna Azure Portal](insights-alerts-portal.md)
* [Program PowerShell](insights-alerts-powershell.md)
* [Interfejs wiersza polecenia platformy Azure](insights-alerts-command-line-interface.md)
* [Interfejs API REST Azure monitora](https://msdn.microsoft.com/library/azure/dn931945.aspx)

Można również uzyskać pomoc dotyczącą poleceń, wpisując polecenie z **— Pomoc** na końcu. Poniżej przedstawiono przykładowy: 

```console
 azure insights alerts -help
 azure insights alerts actions email create -help
 ```

## <a name="create-alert-rules-by-using-azure-cli"></a>Tworzyć reguły alertów za pomocą wiersza polecenia platformy Azure
1. Po zainstalowaniu wymagań wstępnych, logowanie do platformy Azure. Zobacz [przykłady interfejsu wiersza polecenia Azure Monitor](insights-cli-samples.md) poleceń, które należy rozpocząć pracę. Te polecenia ułatwiające uzyskać zalogowany, opisano, jakie subskrypcji używasz i przygotowanie do uruchomienia poleceń Azure monitora.

    ```console
    azure login
    azure account show
    azure config mode arm

    ```

2. Aby wyświetlić listę istniejących reguł dla grupy zasobów, użyj następującego formatu: 

   **Azure insights alerty reguły listy** *[opcje] &lt;grupa zasobów&gt;*

   ```console
   azure insights alerts rule list myresourcegroupname

   ```
3. Aby utworzyć regułę, musisz mieć najpierw kilku ważnych informacji.
    * **Identyfikator zasobu** chcesz ustawić alert dla zasobu.
    * **Definicji metryk** dostępnych dla tego zasobu.

     Jednym ze sposobów Pobierz identyfikator zasobu jest korzystanie z portalu Azure. Przy założeniu, że zasób został już utworzony, wybierz je w portalu. Następnie w bloku dalej w **ustawienia** zaznacz **właściwości**. **Identyfikator ZASOBU** jest polem w następnym bloku. 
     
     Identyfikator zasobu można również uzyskać za pomocą [Eksploratora zasobów Azure](https://resources.azure.com/).

     Poniżej przedstawiono przykład identyfikator zasobu dla aplikacji sieci web:

     ```console
     /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
     ```

     Aby uzyskać listę dostępnych metryk i jednostki na podstawie metryk w poprzednim przykładzie zasobów, użyj następującego polecenia wiersza polecenia platformy Azure:  

     ```console
     azure insights metrics list /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename PT1M
     ```

     *PT1M* jest stopień szczegółowości dostępnych miar (w 1-minutowy). Masz różne opcje metryki, korzystając z różnych szczegółowości.
     
4. Aby utworzyć regułę alertu na podstawie metryki, należy użyć polecenia w następującym formacie:

    **Usługa Azure insights metryki zestaw reguł alertów** *[opcje] &lt;ruleName&gt; &lt;lokalizacji&gt; &lt;resourceGroup&gt; &lt;Rozmiar_okna&gt; &lt;operator&gt; &lt;próg&gt; &lt;element targetResourceId&gt; &lt;metricName&gt; &lt;timeAggregationOperator&gt;*

    Poniższy przykład powoduje ustawienie alertu dla zasobu witryny sieci Web. Wyzwalacze alertu zawsze, gdy odbierze spójnie cały ruch do 5 minut i ponownie po otrzymaniu żaden ruch na 5 minut.

    ```console
    azure insights alerts rule metric set myrule eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total

    ```
5. Aby utworzyć elementu webhook lub Wyślij wiadomość e-mail po zgłoszeniu klasycznego alertu metryki, należy najpierw utworzyć adres e-mail lub elementu webhook. Od razu utworzyć regułę później. Nie można skojarzyć elementów webhook lub wiadomości e-mail przy użyciu reguł, które zostały już utworzone.

    ```console
    azure insights alerts actions email create --customEmails myemail@contoso.com

    azure insights alerts actions webhook create https://www.contoso.com

    azure insights alerts rule metric set myrulewithwebhookandemail eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total
    ```

6. Aby sprawdzić, czy alerty zostały utworzone prawidłowo analizując poszczególnych reguł.

    ```console
    azure insights alerts rule list myresourcegroup --ruleName myrule
    ```
7. Aby usunąć zasady, użyj polecenia w następującym formacie:

    **szczegółowe informacje, Usuń regułę alertów** [opcje] &lt;resourceGroup&gt; &lt;ruleName&gt;

    Te polecenia usuwania reguł, które wcześniej zostały utworzone w tym artykule.

    ```console
    azure insights alerts rule delete myresourcegroup myrule
    azure insights alerts rule delete myresourcegroup myrulewithwebhookandemail
    azure insights alerts rule delete myresourcegroup myActivityLogRule
    ```

## <a name="next-steps"></a>Kolejne kroki
* [Omówienie monitorowania Azure](monitoring-overview.md), w tym typy informacji, można zbierać i monitorowania.
* Dowiedz się więcej o [konfigurowaniu elementów webhook w alertach](insights-webhooks-alerts.md).
* Dowiedz się więcej o [konfigurowania alertów na zdarzenia dziennika aktywności](monitoring-activity-log-alerts.md).
* Dowiedz się więcej o [elementu runbook usługi Automatyzacja Azure](../automation/automation-starting-a-runbook.md).
* Pobierz [omówienie zbierania dzienników diagnostycznych](monitoring-overview-of-diagnostic-logs.md) służąca do gromadzenia szczegółowych o dużej częstotliwości metryki dla usługi.
* Pobierz [omówienie zbierania metryk](insights-how-to-customize-monitoring.md) się upewnić, że usługa jest dostępna i elastyczny.
