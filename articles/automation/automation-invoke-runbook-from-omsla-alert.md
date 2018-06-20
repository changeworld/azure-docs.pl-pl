---
title: Wywoływanie elementu runbook usługi Azure Automation z alertu usługi Log Analytics
description: Ten artykuł zawiera omówienie sposobu Wywołaj element runbook usługi Automatyzacja z analizy dzienników alertu na platformie Azure.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 06/12/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 0094362ee083511b05027f22b37ed62d56d68d41
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36217137"
---
# <a name="call-an-azure-automation-runbook-from-a-log-analytics-alert"></a>Wywoływanie elementu runbook usługi Azure Automation z alertu usługi Log Analytics

W usłudze Azure Log Analytics można skonfigurować alert do tworzenia rekordu alertu, gdy wyniki spełniają określone kryteria. Alert ten może automatycznie uruchamiać element runbook usługi Automation w celu podjęcia próby automatycznego rozwiązania problemu. 

Alert może na przykład wskazywać długotrwały wzrost użycia procesora. Może też wskazywać, kiedy proces aplikacji o znaczeniu krytycznym dla funkcjonalności aplikacji biznesowej zakończy się niepowodzeniem. Element runbook może następnie zapisywać odpowiadające zdarzenie w dzienniku zdarzeń systemu Windows.  

Podczas konfigurowania alertu dostępne są dwie opcje wywołania elementu runbook:

* Skorzystanie z elementu webhook.
   * Jeśli obszaru roboczego analizy dzienników nie jest połączony z konto automatyzacji jest jedyną dostępną opcją.
   * Jeśli masz już konto automatyzacji połączone z obszaru roboczego analizy dzienników, ta opcja jest nadal dostępny.  

* Bezpośrednie wybranie elementu runbook.
   * Ta opcja jest dostępna tylko wtedy, gdy obszar roboczy analizy dzienników jest połączone z innym kontem automatyzacji.

## <a name="calling-a-runbook-by-using-a-webhook"></a>Wywoływanie elementu runbook przy użyciu elementu webhook

Elementu webhook można użyć do uruchomienia określonego elementu runbook w usłudze Azure Automation za pośrednictwem pojedynczego żądania HTTP. Przed skonfigurowaniem [akcji elementu Webhook dla alertów dziennika](../monitoring-and-diagnostics/monitor-alerts-unified-log-webhook.md) do wywołania elementu runbook przy użyciu elementu webhook jako Akcja alertu, musisz [tworzenia elementu webhook](automation-webhooks.md#creating-a-webhook) dla elementu runbook, która jest wywoływana za pomocą tej metody. Pamiętaj o zapisaniu adresu URL elementu webhook, aby podać go podczas konfigurowania reguły alertu.   

## <a name="calling-a-runbook-directly"></a>Bezpośrednie uruchamianie elementu runbook

Można zainstalować i skonfigurować Automatyzacja i kontrola oferty w obszarze roboczym analizy dzienników. Podczas konfigurowania opcji akcji elementu runbook dla alertu możesz wyświetlić wszystkie elementy runbook na liście rozwijanej **Wybierz element runbook** i wybrać konkretny element runbook, który ma być uruchamiany w odpowiedzi na alert. Wybrany element runbook można uruchomić w obszarze roboczym platformy Azure lub w hybrydowym procesie roboczym elementu runbook. 

Po utworzeniu alertu przy użyciu opcji elementu runbook dla elementu runbook jest tworzony element webhook. Element webhook można wyświetlić po przejściu do konta usługi Automation i otworzeniu okienka elementu webhook wybranego elementu runbook. 

Jeśli usuniesz alert, element webhook nie zostanie usunięty. To nie jest problem. Element webhook będzie po prostu elementem oddzielonym, który ostatecznie trzeba będzie usunąć ręcznie w celu utrzymania uporządkowanego konta usługi Automation.  

## <a name="characteristics-of-a-runbook"></a>Właściwości elementu runbook

Przed skonfigurowaniem reguł alertu należy zrozumieć cechy obu metod wywoływania elementu runbook z poziomu alertu usługi Log Analytics. 

Dane alertu są zapisane w formacie JSON w pojedynczej właściwości o nazwie **SearchResult**. Ten format jest używany w przypadku akcji elementów runbook i webhook ze standardowym ładunkiem. W przypadku akcji elementów webhook z niestandardowymi ładunkami (zawierających ciąg **IncludeSearchResults:True** w elemencie **RequestBody**) jest to właściwość **SearchResults**.

Musisz mieć parametr wejściowy elementu runbook o nazwie **WebhookData**, którego typ to **Obiekt**. Może on być wymagany lub opcjonalny. Przy użyciu tego parametru wejściowego alert przekazuje wyniki wyszukiwania do elementu runbook.

```powershell
param  
    (  
    [Parameter (Mandatory=$true)]  
    [object] $WebhookData  
    )
```
Musisz mieć również kod konwertujący parametr **WebhookData** na obiekt programu PowerShell.

```powershell
$SearchResult = (ConvertFrom-Json $WebhookData.RequestBody).SearchResult.value
```

Element **$SearchResult** jest tablicą obiektów. Każdy obiekt zawiera pola z wartościami z jednego wyniku wyszukiwania.


## <a name="example-walkthrough"></a>Przykładowy przewodnik

Poniższy przykład graficznego elementu runbook pokazuje, jak działa ten proces. Uruchamia on usługę systemu Windows.

![Graficzny element runbook do uruchamiania usługi systemu Windows](media/automation-invoke-runbook-from-omsla-alert/automation-runbook-restartservice.png)

Ten element runbook ma jeden parametr wejściowy typu **Obiekt** o nazwie **WebhookData**. Zawiera on dane elementu webhook przekazane z alertu zawierającego element **SearchResults**.

![Parametry wejściowe elementu Runbook](media/automation-invoke-runbook-from-omsla-alert/automation-runbook-restartservice-inputparameter.png)

Na potrzeby tego przykładu w usłudze Log Analytics utworzyliśmy dwa pola niestandardowe: **SvcDisplayName_CF** i **SvcState_CF**. Te pola wyodrębniają nazwę wyświetlaną usługi i stan usługi (uruchomiona lub zatrzymana) ze zdarzenia zapisanego w dzienniku zdarzeń systemu. Następnie utworzyliśmy regułę alertu z następującym zapytaniem wyszukiwania, dzięki czemu możemy wykryć, kiedy usługa Print Spooler zostanie zatrzymana w systemie Windows:

`Type=Event SvcDisplayName_CF="Print Spooler" SvcState_CF="stopped"` 

Może być to dowolna interesująca nas usługa. W tym przykładzie odwołujemy się do jednej ze wstępnie istniejących usług zawartych w systemie operacyjnym Windows. Akcja alertu jest skonfigurowana do wykonywania elementu runbook użytego w tym przykładzie i do działania w hybrydowym procesie roboczym elementu runbook, który jest włączany w systemie docelowym.   

Działanie kodu elementu runbook **Get Service Name from LA** przekonwertuje ciąg w formacie JSON na typ obiektu i zastosuje filtr do elementu **SvcDisplayName_CF**. Powoduje to wyodrębnienie nazwy wyświetlanej usługi systemu Windows, a następnie przekazuje ją do kolejnego działania, które przed podjęciem próby ponownego uruchomienia usługi sprawdzi, czy została ona zatrzymana. Element **SvcDisplayName_CF** to [pole niestandardowe](../log-analytics/log-analytics-custom-fields.md), które utworzyliśmy w usłudze Log Analytics na potrzeby tego przykładu.

```powershell
$SearchResult = (ConvertFrom-Json $WebhookData.RequestBody).SearchResult.value
$SearchResult.SvcDisplayName_CF  
```

Po zatrzymaniu usługi reguła alertu w usłudze Log Analytics wykrywa dopasowanie, wyzwala element runbook i wysyła kontekst alertu do tego elementu. Element runbook podejmuje próbę sprawdzenia, czy usługa została zatrzymana. Jeśli tak, element runbook próbuje ponownie uruchomić usługę, sprawdzić, czy usługa została uruchomiona prawidłowo, i wyświetlić wyniki.     

Alternatywnie Jeśli nie masz konta automatyzacji połączone z obszaru roboczego analizy dzienników można skonfigurować regułę alertu przy użyciu akcji elementu webhook. Ta akcja elementu webhook wyzwala element runbook. Konfiguruje również element runbook do konwertowania ciągu w formacie JSON oraz filtrowania elementu **SearchResult** zgodnie z podanymi wcześniej wskazówkami.    

>[!NOTE]
> Jeśli Twój obszar roboczy został uaktualniony do [nowego języka zapytań usługi Log Analytics](../log-analytics/log-analytics-log-search-upgrade.md), ładunek elementu webhook uległ zmianie. Szczegółowe informacje na temat tego formatu zawiera artykuł [Azure Log Analytics REST API](https://aka.ms/loganalyticsapiresponse) (Interfejs API REST usługi Azure Log Analytics).

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej na temat tworzenia przy użyciu wyszukiwania dziennika alertu Azure, zobacz [rejestrowania alertów w usłudze Azure](../monitoring-and-diagnostics/monitor-alerts-unified-log.md).

* Aby dowiedzieć się, jak wyzwalać elementy runbook przy użyciu elementu webhook, zobacz [Elementy webhook w usłudze Azure Automation](automation-webhooks.md).
