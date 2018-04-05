---
title: Utwórz alerty dla usług Azure - CLI wieloplatformowych | Dokumentacja firmy Microsoft
description: Wyzwalacz wiadomości e-mail, powiadomienia, Wywołaj adresy URL witryny sieci Web (elementy webhook) lub automatyzacji po spełnieniu warunków, które określisz.
author: rboucher
manager: carmonm
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 5c6a2d27-7dcc-4f89-8752-9bb31b05ff35
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: robb
ms.openlocfilehash: 9ea7da35acefc139625e71904c8aa1b01b87e4df
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="create-classic-metric-alerts-in-azure-monitor-for-azure-services---cross-platform-cli"></a>Tworzenie klasycznego metryki alertów w monitorze Azure dla usług Azure - CLI między platformami
> [!div class="op_single_selector"]
> * [Portal](insights-alerts-portal.md)
> * [Program PowerShell](insights-alerts-powershell.md)
> * [Interfejs wiersza polecenia](insights-alerts-command-line-interface.md)
>
>

## <a name="overview"></a>Przegląd
> [!NOTE]
> W tym artykule opisano sposób tworzenia starsze klasycznego alerty metryki. Azure obsługuje teraz Monitor [nowszą, lepiej metryki alerty](monitoring-near-real-time-metric-alerts.md). Te alerty można monitorować wiele metryk i umożliwić alertów na wymiarów metryki. Obsługa interfejsu wiersza polecenia dla nowszej alertów metryki będzie dostępna wkrótce.
>
>

W tym artykule przedstawiono sposób konfigurowania za pomocą interfejsu wiersza polecenia i platform (CLI) Azure klasycznego alertów metryki.

> [!NOTE]
> Azure Monitor to nowa nazwa dla proponowaną "Azure Insights" do 25 września 2016 r. Jednak przestrzenie nazw, dlatego poniższe polecenia nadal zawierają "insights".
>
>

Możesz otrzymywać alertu na podstawie metryki monitorowania lub zdarzenia na usługami Azure.

* **Wartości metryki** — uruchamia alert, gdy wartość określonej metryki przekracza próg przypisać w żadnym kierunku. Oznacza to, że oba wyzwala po spełnieniu warunku zostanie najpierw i następnie później podczas warunku jest już spełniane.    
* **Zdarzenia dziennika aktywności** -alert może wyzwolić na *co* zdarzenia lub tylko wtedy, gdy wystąpi określone zdarzenie. Aby dowiedzieć się więcej o alertach dziennika aktywności [kliknij tutaj](monitoring-activity-log-alerts.md)

Można skonfigurować klasycznego alertu metryki wyzwala, wykonaj następujące czynności:

* wysyłanie powiadomień e-mail do administratora usługi i współadministratorzy
* Wyślij wiadomość e-mail do dodatkowych wiadomości e-mail przez użytkownika.
* Wywołanie elementu webhook
* Uruchamia wykonywanie elementów runbook platformy Azure (tylko z portalu Azure w tej chwili)

Można skonfigurować i uzyskać informacje na temat przy użyciu klasycznego metryki reguły alertów

* [Azure Portal](insights-alerts-portal.md)
* [Program PowerShell](insights-alerts-powershell.md)
* [Interfejs wiersza polecenia (CLI)](insights-alerts-command-line-interface.md)
* [Interfejs API REST Azure monitora](https://msdn.microsoft.com/library/azure/dn931945.aspx)

Zawsze może odbierać pomocy dla poleceń, wpisując polecenie i odkładanie — pomoc na końcu. Na przykład:

    ```console
    azure insights alerts -help
    azure insights alerts actions email create -help
    ```

## <a name="create-alert-rules-using-the-cli"></a>Tworzyć reguły alertów za pomocą interfejsu wiersza polecenia
1. Wykonaj warunki wstępne i logowania do platformy Azure. Zobacz [przykłady interfejsu wiersza polecenia Azure Monitor](insights-cli-samples.md). Krótko mówiąc instalowanie interfejsu wiersza polecenia i uruchom następujące polecenia. Ona się zalogować, Pokaż subskrypcję, a przygotowanie do uruchomienia poleceń Azure monitora.

    ```console
    azure login
    azure account show
    azure config mode arm

    ```

2. Aby wyświetlić listę istniejących reguł dla grupy zasobów, użyj następującej składni **insights azure alerty reguły listy** *[opcje] &lt;grupa zasobów&gt;*

   ```console
   azure insights alerts rule list myresourcegroupname

   ```
3. Aby utworzyć regułę, musisz mieć najpierw kilku ważnych informacji.
  * **Identyfikator zasobu** dla zasobu, aby ustawić alert dla
  * **Definicji metryk** dostępne dla tego zasobu

     Jednym ze sposobów Pobierz identyfikator zasobu jest korzystanie z portalu Azure. Zakładając, że zasób został już utworzony, wybierz go w portalu. Następnie w bloku dalej wybierz *właściwości* w obszarze *ustawienia* sekcji. *Identyfikator ZASOBU* jest polem w następnym bloku. Innym sposobem jest użycie [Eksploratora zasobów Azure](https://resources.azure.com/).

     Identyfikator zasobu przykład dla aplikacji sieci web jest

     ```console
     /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
     ```

     Aby uzyskać listę dostępnych metryk i jednostki dla tych metryk w poprzednim przykładzie zasobów, użyj następującego polecenia interfejsu wiersza polecenia:  

     ```console
     azure insights metrics list /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename PT1M
     ```

     *PT1M* jest stopień szczegółowości miary dostępne (1-minutowy). Przy użyciu różnych szczegółowości zapewnia różne opcje metryki.
4. Aby utworzyć regułę alertu metryki, użyj polecenia mają następującą postać:

    **Usługa Azure insights metryki zestaw reguł alertów** *[opcje] &lt;ruleName&gt; &lt;lokalizacji&gt; &lt;resourceGroup&gt; &lt;Rozmiar_okna&gt; &lt;operator&gt; &lt;próg&gt; &lt;element targetResourceId&gt; &lt;metricName&gt; &lt;timeAggregationOperator&gt;*

    Poniższy przykład powoduje ustawienie alertu dla zasobu witryny sieci web. Wyzwalacze alertu zawsze, gdy odbierze spójnie cały ruch do 5 minut i ponownie po otrzymaniu żaden ruch na 5 minut.

    ```console
    azure insights alerts rule metric set myrule eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total

    ```
5. Aby utworzyć elementu webhook lub Wyślij wiadomość e-mail po zgłoszeniu klasycznego alertu metryki, należy najpierw utworzyć wiadomości e-mail i/lub elementów webhook. Od razu utworzyć regułę później. Nie można skojarzyć elementu webhook lub wiadomości e-mail przy użyciu już utworzone zasady przy użyciu interfejsu wiersza polecenia.

    ```console
    azure insights alerts actions email create --customEmails myemail@contoso.com

    azure insights alerts actions webhook create https://www.contoso.com

    azure insights alerts rule metric set myrulewithwebhookandemail eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total
    ```

6. Aby sprawdzić, czy alerty zostały utworzone prawidłowo analizując poszczególnych reguł.

    ```console
    azure insights alerts rule list myresourcegroup --ruleName myrule
    ```
7. Aby usunąć zasady, użyj polecenia formularza:

    **szczegółowe informacje, Usuń regułę alertów** [opcje] &lt;resourceGroup&gt; &lt;ruleName&gt;

    Te polecenia usuwania reguł utworzonych wcześniej w tym artykule.

    ```console
    azure insights alerts rule delete myresourcegroup myrule
    azure insights alerts rule delete myresourcegroup myrulewithwebhookandemail
    azure insights alerts rule delete myresourcegroup myActivityLogRule
    ```

## <a name="next-steps"></a>Kolejne kroki
* [Omówienie monitorowania Azure](monitoring-overview.md) w tym typy informacji, można zbierać i monitorowania.
* Dowiedz się więcej o [konfigurowaniu elementów webhook w alertach](insights-webhooks-alerts.md).
* Dowiedz się więcej o [konfigurowania alertów na zdarzenia dziennika aktywności](monitoring-activity-log-alerts.md).
* Dowiedz się więcej o [elementów Runbook automatyzacji Azure](../automation/automation-starting-a-runbook.md).
* Pobierz [omówienie zbierania dzienników diagnostycznych](monitoring-overview-of-diagnostic-logs.md) zbierania szczegółowych o dużej częstotliwości metryk usługi.
* Pobierz [omówienie zbierania metryk](insights-how-to-customize-monitoring.md) się upewnić, że usługa jest dostępna i elastyczny.
