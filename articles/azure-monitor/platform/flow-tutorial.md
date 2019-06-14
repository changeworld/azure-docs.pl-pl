---
title: Automatyzacja procesów dzienników usługi Azure Monitor z Microsoft Flow
description: Dowiedz się, jak Microsoft Flow umożliwia szybkie zautomatyzować powtarzalnych procesów za pomocą łącznika usługi Azure Log Analytics.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
ms.service: log-analytics
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/29/2017
ms.author: bwren
ms.openlocfilehash: c3732dd2fa87b00eec38f88ab828605b33567235
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60396600"
---
# <a name="automate-azure-monitor-log-processes-with-the-connector-for-microsoft-flow"></a>Zautomatyzować procesy dzienników usługi Azure Monitor z łącznikiem usługi Microsoft Flow
[Microsoft Flow](https://ms.flow.microsoft.com) pozwala tworzyć zautomatyzowane przepływy pracy przy użyciu setek akcji dla wielu usług. Dane wyjściowe z akcji może służyć jako dane wejściowe do innego, co pozwala na tworzenie integracja między różnymi usługami.  Łącznik usługi Azure Log Analytics dla Microsoft Flow, umożliwiają tworzenie przepływów pracy, które zawierają dane pobierane przez dziennika zapytań z obszaru roboczego usługi Log Analytics w usłudze Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Na przykład można użyć Microsoft Flow do korzystania z danych dziennika LAzure monitora na powiadomienie e-mail z usługi Office 365, Utwórz usterkę w DevOps platformy Azure lub opublikuj wiadomość Slack.  Możesz wyzwolić przepływu pracy przez harmonogram prosty lub niektóre akcje w przypadku połączonych usług, takich jak po odebraniu wiadomości e-mail lub tweet.  

Samouczek, w tym artykule pokazano, jak utworzyć przepływ, który automatycznie wysyła wyników kwerendy dzienników usługi Azure Monitor za pośrednictwem poczty e-mail, tylko jeden przykład sposobu korzystania z łącznika usługi Log Analytics w Microsoft Flow. 


## <a name="step-1-create-a-flow"></a>Krok 1: Tworzenie przepływu
1. Zaloguj się do [Microsoft Flow](https://flow.microsoft.com)i wybierz **moje przepływy**.
2. Kliknij przycisk **+ Utwórz z pustego**.

## <a name="step-2-create-a-trigger-for-your-flow"></a>Krok 2: Tworzenie wyzwalacza przepływu
1. Kliknij przycisk **Przeszukaj setki łączników i wyzwalaczy**.
2. Typ **harmonogram** w polu wyszukiwania.
3. Wybierz **harmonogram**, a następnie wybierz pozycję **harmonogram — cyklicznie**.
4. W **częstotliwość** polu Wybierz **dzień** i **interwał** wprowadź **1**.<br><br>![Okno dialogowe wyzwalacz Microsoft Flow](media/flow-tutorial/flow01.png)


## <a name="step-3-add-a-log-analytics-action"></a>Krok 3: Dodaj akcję usługi Log Analytics
1. Kliknij przycisk **+ nowy krok**, a następnie kliknij przycisk **Dodaj akcję**.
2. Wyszukaj **Log Analytics**.
3. Kliknij przycisk **usługi Azure Log Analytics — uruchom zapytanie, a wizualizacja wyników**.<br><br>![Uruchom okno zapytania w usłudze log Analytics](media/flow-tutorial/flow02.png)

## <a name="step-4-configure-the-log-analytics-action"></a>Krok 4: Skonfiguruj akcję usługi Log Analytics

1. Określ szczegóły dla Twojego obszaru roboczego, w tym subskrypcji identyfikator, grupy zasobów i nazwę obszaru roboczego.
2. Następujące zapytanie dziennika, aby dodać **zapytania** okna.  Jest to tylko przykładowe zapytanie, i możesz zastąpić dowolne inne zwracającej dane.
   ```
    Event
    | where EventLevelName == "Error" 
    | where TimeGenerated > ago(1day)
    | summarize count() by Computer
    | sort by Computer
   ```

2. Wybierz **tabeli HTML** dla **typ wykresu**.<br><br>![Akcja analizy dzienników](media/flow-tutorial/flow03.png)

## <a name="step-5-configure-the-flow-to-send-email"></a>Krok 5. Konfiguruj przepływ do wysyłania wiadomości e-mail

1. Kliknij przycisk **nowy krok**, a następnie kliknij przycisk **+ Dodaj akcję**.
2. Wyszukaj **usługi Office 365 Outlook**.
3. Kliknij przycisk **usługi Office 365 Outlook — Wyślij wiadomość e-mail**.<br><br>![Okno wyboru programu Outlook usługi Office 365](media/flow-tutorial/flow04.png)

4. Określ adres e-mail odbiorcy w **do** okna oraz temat wiadomości e-mail w **podmiotu**.
5. Kliknij w dowolnym miejscu **treści** pole.  A **zawartości dynamicznej** zostanie otwarte okno z wartościami z poprzednich akcji.  
6. Wybierz **treści**.  Jest to wyniki zapytania w działaniu usługi Log Analytics.
6. Kliknij przycisk **Pokaż opcje zaawansowane**.
7. W **HTML jest** wybierz opcję **tak**.<br><br>![Okna konfiguracji poczty e-mail usługi Office 365](media/flow-tutorial/flow05.png)

## <a name="step-6-save-and-test-your-flow"></a>Krok 6: Zapisz i przetestowanie przepływu
1. W **Nazwa przepływu** , Dodaj nazwę przepływowi, a następnie kliknij przycisk **Utwórz przepływ**.<br><br>![Zapisz przepływ](media/flow-tutorial/flow06.png)
2. Przepływ został utworzony i będzie działać po dniu, czyli harmonogram, który określono. 
3. Aby natychmiast przetestować przepływ, kliknij przycisk **Uruchom teraz** i następnie **uruchomić przepływ**.<br><br>![Uruchamianie przepływu](media/flow-tutorial/flow07.png)
3. Sprawdź wiadomość e-mail odbiorcy, który określiłeś, po zakończeniu przepływu.  Powinna zostać odebrana wiadomość e-mail z treścią podobny do następującego:<br><br>![Przykładowa wiadomość e-mail wysłana](media/flow-tutorial/flow08.png)


## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [rejestrowania zapytań w usłudze Azure Monitor](../log-query/log-query-overview.md).
- Dowiedz się więcej o [Microsoft Flow](https://ms.flow.microsoft.com).



