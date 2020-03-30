---
title: Korzystanie z dzienników usługi Azure Monitor za pomocą aplikacji logiki azure i automatyzacji zasilania
description: Dowiedz się, jak za pomocą aplikacji Azure Logic Apps i power automatyki szybko zautomatyzować powtarzalne procesy przy użyciu łącznika usługi Azure Monitor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/13/2020
ms.openlocfilehash: 6961b7bd94c9b3fe70365055851c488efa2cbeca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480015"
---
# <a name="azure-monitor-logs-connector-for-logic-apps-and-flow"></a>Łącznik dzienników usługi Azure Monitor dla aplikacji logiki i przepływu
[Usługi Azure Logic Apps](/azure/logic-apps/) i [Power Automate](https://ms.flow.microsoft.com) umożliwiają tworzenie zautomatyzowanych przepływów pracy przy użyciu setek akcji dla różnych usług. Łącznik dzienników usługi Azure Monitor umożliwia tworzenie przepływów pracy, które pobierają dane z obszaru roboczego usługi Log Analytics lub aplikacji usługi Application Insights w usłudze Azure Monitor. W tym artykule opisano akcje dołączone do łącznika i zawiera instruktaż do tworzenia przepływu pracy przy użyciu tych danych.

Na przykład można utworzyć aplikację logiki do korzystania z danych dziennika usługi Azure Monitor w powiadomieniu e-mail z usługi Office 365, utworzyć błąd w usłudze Azure DevOps lub opublikować komunikat Slack.  Przepływ pracy można wyzwolić za pomocą prostego harmonogramu lub z niektórych akcji w połączonej usłudze, takich jak odebranie poczty lub tweeta. 

## <a name="actions"></a>Akcje
W poniższej tabeli opisano akcje dołączone do łącznika dzienników monitora platformy Azure. Oba umożliwiają uruchamianie kwerendy dziennika dla obszaru roboczego usługi Log Analytics lub aplikacji usługi Application Insights. Różnica polega na sposobie zwracania danych.

> [!NOTE]
> Łącznik dzienników usługi Azure Monitor zastępuje [łącznik usługi Azure Log Analytics](https://docs.microsoft.com/connectors/azureloganalytics/) i łącznik usługi Azure Application [Insights.](https://docs.microsoft.com/connectors/applicationinsights/) Ten łącznik zapewnia taką samą funkcjonalność jak inne i jest preferowaną metodą uruchamiania kwerendy w obszarze roboczym usługi Log Analytics lub aplikacji usługi Application Insights.


| Akcja | Opis |
|:---|:---|
| [Uruchamianie kwerendy i listy wyników](https://docs.microsoft.com/connectors/azuremonitorlogs/#run-query-and-list-results) | Zwraca każdy wiersz jako własny obiekt. Tej akcji należy użyć, jeśli chcesz pracować z każdym wierszem oddzielnie w pozostałej części przepływu pracy. Akcja jest zazwyczaj następuje [dla każdego działania](../../logic-apps/logic-apps-control-flow-loops.md#foreach-loop). |
| [Uruchamianie kwerendy i wizualizowanie wyników](https://docs.microsoft.com/connectors/azuremonitorlogs/#run-query-and-visualize-results) | Zwraca wszystkie wiersze w zestawie wyników jako pojedynczy sformatowany obiekt. Tej akcji należy użyć zestawu wyników w pozostałej części przepływu pracy, na przykład wysyłania wyników pocztą.  |

## <a name="walkthroughs"></a>Wskazówki
Poniższe samouczki ilustrują użycie łączników usługi Azure Monitor w usłudze Azure Logic Apps. Można wykonać te same przykłady z Power Automate, jedyną różnicą jest to, jak utworzyć początkowy przepływ pracy i uruchomić go po zakończeniu. Konfiguracja przepływu pracy i akcje jest taka sama między obu. Aby rozpocząć, zobacz [Tworzenie przepływu z szablonu w umięśnym zautomatyzowym.](https://docs.microsoft.com/power-automate/get-started-logic-template)


### <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki

Przejdź do **aplikacji logiki** w witrynie Azure portal i kliknij przycisk **Dodaj**. Wybierz **subskrypcję,** **grupę zasobów**i **region,** aby zapisać nową aplikację logiki, a następnie nadaj jej unikatową nazwę. Możesz włączyć ustawienie **usługi Log Analytics,** aby zbierać informacje o danych i zdarzeniach środowiska wykonawczego zgodnie z opisem w [obszarze Konfigurowanie dzienników usługi Azure Monitor i zbieranie danych diagnostycznych dla aplikacji Azure Logic Apps.](../../logic-apps/monitor-logic-apps-log-analytics.md) To ustawienie nie jest wymagane do korzystania z łącznika dzienników monitora platformy Azure.

![Tworzenie aplikacji logiki](media/logicapp-flow-connector/create-logic-app.png)


Kliknij **pozycję Recenzja + utwórz,** a następnie **utwórz**. Po zakończeniu wdrażania kliknij przycisk **Przejdź do zasobu,** aby otworzyć **Projektanta aplikacji logiki**.

### <a name="create-a-trigger-for-the-logic-app"></a>Tworzenie wyzwalacza dla aplikacji logiki
W obszarze **Rozpocznij od wspólnego wyzwalacza**wybierz pozycję **Cykl**. Spowoduje to utworzenie aplikacji logiki, która automatycznie działa w regularnych odstępach czasu. W polu **Częstotliwość** akcji wybierz **pozycję Dzień** i w polu **Interwał** wprowadź **1,** aby uruchomić przepływ pracy raz dziennie.

![Akcja cyklu](media/logicapp-flow-connector/recurrence-action.png)

## <a name="walkthrough-mail-visualized-results"></a>Przewodnik: Wyniki wizualizacji poczty
Poniższy samouczek pokazuje, jak utworzyć aplikację logiki, która wysyła wyniki kwerendy dziennika usługi Azure Monitor przez poso pos. 

### <a name="add-azure-monitor-logs-action"></a>Akcja Dodaj dzienniki monitora platformy Azure
Kliknij **+ Nowy krok,** aby dodać akcję, która jest uruchamiana po akcji cyklu. W obszarze **Wybierz akcję**wpisz **azure monitor,** a następnie wybierz pozycję **Dzienniki monitora platformy Azure**.

![Akcja Dzienniki monitora platformy Azure](media/logicapp-flow-connector/select-azure-monitor-connector.png)

Kliknij pozycję **Usługa Azure Log Analytics — uruchom kwerendę i wizualizuj wyniki**.

![Uruchamianie kwerendy i wizualizacja akcji wyników](media/logicapp-flow-connector/select-query-action-visualize.png)


### <a name="add-azure-monitor-logs-action"></a>Akcja Dodaj dzienniki monitora platformy Azure

Wybierz **grupę subskrypcji** i **zasobów** dla obszaru roboczego usługi Log Analytics. Wybierz *obszar roboczy analizy dzienników* dla **typu zasobu,** a następnie wybierz nazwę obszaru roboczego w obszarze **Nazwa zasobu**.

Dodaj następującą kwerendę dziennika do okna **kwerendy.**  

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1day)
| summarize TotalErrors=count() by Computer
| sort by Computer asc   
```

Wybierz *pozycję Ustaw w kwerendzie* dla zakresu **czasu** i **tabeli HTML** dla typu **wykresu**.
   
![Uruchamianie kwerendy i wizualizacja akcji wyników](media/logicapp-flow-connector/run-query-visualize-action.png)

Wiadomość zostanie wysłana przez konto skojarzone z bieżącym połączeniem. Możesz określić inne konto, klikając pozycję **Zmień połączenie**.

### <a name="add-email-action"></a>Dodaj akcję e-mail

Kliknij **przycisk + Nowy krok**, a następnie kliknij przycisk + Dodaj **akcję**. W obszarze **Wybierz akcję**wpisz **program Outlook,** a następnie wybierz pozycję **Office 365 Outlook**.

![Wybieranie łącznika programu Outlook](media/logicapp-flow-connector/select-outlook-connector.png)

Wybierz **pozycję Wyślij wiadomość e-mail (V2)**.

![Okno wyboru programu Outlook w usłudze Office 365](media/logicapp-flow-connector/select-mail-action.png)

Kliknij dowolne miejsce w polu **Treść,** aby otworzyć okno **dynamicznej zawartości** otwiera się z wartościami z poprzednich akcji w aplikacji logiki. Wybierz **zobacz więcej,** a następnie **Body,** który jest wynikiem kwerendy w log Analytics akcji.

![Wybierz obiekt](media/logicapp-flow-connector/select-body.png)

Określ adres e-mail odbiorcy w oknie **Do** i temat wiadomości e-mail w **temacie**. 

![Akcja Poczta](media/logicapp-flow-connector/mail-action.png)


### <a name="save-and-test-your-logic-app"></a>Zapisywanie i testowanie aplikacji logiki
Kliknij **przycisk Zapisz,** a następnie **uruchom,** aby wykonać testową aplikację logiki.

![Zapisz i uruchom](media/logicapp-flow-connector/save-run.png)


Po zakończeniu aplikacji logiki sprawdź pocztę adresata, który został określony.  Powinieneś otrzymać wiadomość z treścią podobną do następującej:

![Przykładowa wiadomość e-mail](media/logicapp-flow-connector/sample-mail.png)



## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [zapytaniach dziennika w usłudze Azure Monitor](../log-query/log-query-overview.md).
- Dowiedz się więcej o [aplikacjach logiki](/azure/logic-apps/)
- Dowiedz się więcej o [usłudze Microsoft Flow](https://ms.flow.microsoft.com).

