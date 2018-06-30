---
title: Automatyzowanie procesów Analiza dzienników Azure z Flow firmy Microsoft
description: Dowiedz się, jak można użyć Microsoft Flow można szybko zautomatyzować powtarzalnych procesów za pomocą łącznika usługi Analiza dzienników Azure.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
ms.service: log-analytics
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/29/2017
ms.author: bwren
ms.component: na
ms.openlocfilehash: 21cf7cf3d12902b02fcbf650a1623e78004d28b4
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37131704"
---
# <a name="automate-log-analytics-processes-with-the-connector-for-microsoft-flow"></a>Automatyzacji procesów analizy dzienników przy użyciu łącznika dla Flow firmy Microsoft
[Microsoft Flow](https://ms.flow.microsoft.com) służy do tworzenia automatycznych przepływów pracy za pomocą setki akcje dla różnych usług. Dane wyjściowe z jedną akcję może służyć jako dane wejściowe do innego pozwala na tworzenie integrację różnych usług.  Łącznik usługi Analiza dzienników Azure dla programu Microsoft Flow pozwalają na tworzenie przepływów pracy, które zawierają dane pobierane przez dziennik wyszukiwania analizy dzienników.

Na przykład można użyć Microsoft Flow korzystanie danych analizy dzienników powiadomienie e-mail z usługi Office 365, tworzenie usterki w programie Visual Studio Team Services lub wysłać wiadomość Slack.  Przez harmonogram prosty lub niektóre akcje w podłączonej usługi takie jak po odebraniu wiadomości e-mail lub tweet, może wyzwolić przepływu pracy.  

Samouczek, w tym artykule przedstawiono sposób tworzenia przepływu, który automatycznie wysyła wyniki wyszukiwania dziennika analizy dzienników pocztą e-mail, tylko jeden przykład stosowania analizy dzienników w Flow firmy Microsoft. 


## <a name="step-1-create-a-flow"></a>Krok 1: Tworzenie przepływu
1. Zaloguj się do [Microsoft Flow](http://flow.microsoft.com)i wybierz **Moje przepływa**.
2. Kliknij przycisk **+ Utwórz z puste**.

## <a name="step-2-create-a-trigger-for-your-flow"></a>Krok 2: Tworzenie wyzwalacza dla Twojego przepływu
1. Kliknij przycisk **wyszukiwania setki łączników i wyzwalaczy**.
2. Typ **harmonogram** w polu wyszukiwania.
3. Wybierz **harmonogram**, a następnie wybierz **harmonogram - cyklu**.
4. W **częstotliwość** polu Wybierz **dzień** i **interwał** wprowadź **1**.<br><br>![Okno dialogowe Microsoft Flow wyzwalacza](media/log-analytics-flow-tutorial/flow01.png)


## <a name="step-3-add-a-log-analytics-action"></a>Krok 3: Dodaj akcję analizy dzienników
1. Kliknij przycisk **+ nowy krok**, a następnie kliknij przycisk **Dodaj akcję**.
2. Wyszukaj **dziennika analizy**.
3. Kliknij przycisk **Azure Log Analytics — uruchomić zapytanie i wizualizacja wyników**.<br><br>![Uruchom okno kwerendy analizy dzienników](media/log-analytics-flow-tutorial/flow02.png)

## <a name="step-4-configure-the-log-analytics-action"></a>Krok 4: Konfigurowanie akcji analizy dzienników

1. Określ szczegóły dla obszaru roboczego, w tym subskrypcji identyfikator, grupy zasobów, a nazwa obszaru roboczego.
2. Następujące zapytanie analizy dzienników, aby dodać **zapytania** okna.  To jest tylko przykładowe zapytanie i możesz zastąpić dowolne inne zwracające dane.
```
    Event
    | where EventLevelName == "Error" 
    | where TimeGenerated > ago(1day)
    | summarize count() by Computer
    | sort by Computer
```

2. Wybierz **tabeli HTML** dla **typ wykresu**.<br><br>![Dziennik akcji analityka](media/log-analytics-flow-tutorial/flow03.png)

## <a name="step-5-configure-the-flow-to-send-email"></a>Krok 5: Konfigurowanie przepływu do wysyłania wiadomości e-mail

1. Kliknij przycisk **nowy krok**, a następnie kliknij przycisk **+ Dodaj akcję**.
2. Wyszukaj **Outlook pakietu Office 365**.
3. Kliknij przycisk **Office 365 Outlook — Wyślij usługi poczty e-mail**.<br><br>![Okno wyboru Outlook pakietu Office 365](media/log-analytics-flow-tutorial/flow04.png)

4. Określ adres e-mail odbiorcy w **do** okno i temat wiadomości e-mail w **podmiotu**.
5. Kliknij w dowolnym miejscu **treści** pole.  A **zawartości dynamicznej** zostanie otwarte okno z wartości z poprzedniej akcji.  
6. Wybierz **treści**.  Jest to wyniki zapytania w akcji analizy dzienników.
6. Kliknij przycisk **Pokaż zaawansowane opcje**.
7. W **HTML jest** wybierz opcję **tak**.<br><br>![Okno Konfiguracja poczty e-mail usługi Office 365](media/log-analytics-flow-tutorial/flow05.png)

## <a name="step-6-save-and-test-your-flow"></a>Krok 6: Zapisz i testowania przepływu użytkownika
1. W **nazwy przepływu** , Dodaj nazwę użytkownika przepływ, a następnie kliknij przycisk **utworzyć przepływ**.<br><br>![Zapisywanie przepływu](media/log-analytics-flow-tutorial/flow06.png)
2. Przepływ został utworzony i zostanie uruchomiona po dniu, czyli harmonogram, który można określić. 
3. Aby natychmiast przetestować przepływ, **Uruchom teraz** , a następnie **uruchomienia przepływu**.<br><br>![Uruchamianie przepływu](media/log-analytics-flow-tutorial/flow07.png)
3. Po zakończeniu przepływu, Sprawdź pocztę adresata, który można określić.  Powinna zostać odebrana poczty z treścią podobny do następującego:<br><br>![Przykładowej wiadomości e-mail](media/log-analytics-flow-tutorial/flow08.png)


## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [Zaloguj wyszukiwania analizy dzienników](log-analytics-log-search-new.md).
- Dowiedz się więcej o [Microsoft Flow](https://ms.flow.microsoft.com).



