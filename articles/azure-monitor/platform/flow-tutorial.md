---
title: Automatyzacja procesów dzienników Azure Monitor przy użyciu Microsoft Flow
description: Dowiedz się, jak można użyć Microsoft Flow, aby szybko zautomatyzować powtarzalne procesy przy użyciu łącznika usługi Azure Log Analytics.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/29/2017
ms.openlocfilehash: a6097d38d3335be356ca75f5a9d0eadeed414b03
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77166949"
---
# <a name="automate-azure-monitor-log-processes-with-the-connector-for-microsoft-flow"></a>Automatyzacja procesów dzienników Azure Monitor przy użyciu łącznika dla Microsoft Flow
[Microsoft Flow](https://ms.flow.microsoft.com) umożliwia tworzenie zautomatyzowanych przepływów pracy przy użyciu setek akcji dla różnych usług. Dane wyjściowe z jednej akcji mogą służyć jako dane wejściowe, aby umożliwić tworzenie integracji między różnymi usługami.  Łącznik usługi Azure Log Analytics dla Microsoft Flow umożliwia tworzenie przepływów pracy zawierających dane pobierane przez zapytania dzienników z obszaru roboczego Log Analytics w Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Na przykład możesz użyć Microsoft Flow, aby użyć Azure Monitor danych dziennika w powiadomieniach e-mail z pakietu Office 365, utworzyć usterkę w usłudze Azure DevOps lub opublikować komunikat o zapasach.  Przepływ pracy można wyzwolić według prostego harmonogramu lub z niektórych akcji w połączonej usłudze, takiej jak w przypadku otrzymania wiadomości e-mail lub tweetu.  

W samouczku w tym artykule przedstawiono sposób tworzenia przepływu, który automatycznie wysyła wyniki zapytania dziennika Azure Monitor pocztą e-mail, tylko jeden przykład użycia łącznika Log Analytics w Microsoft Flow. 


## <a name="step-1-create-a-flow"></a>Krok 1. Tworzenie przepływu
1. Zaloguj się do [Microsoft Flow](https://flow.microsoft.com)i wybierz pozycję **Moje przepływy**.
2. Kliknij pozycję **+ Utwórz z pustego**.

## <a name="step-2-create-a-trigger-for-your-flow"></a>Krok 2. Tworzenie wyzwalacza dla przepływu
1. Kliknij przycisk **Przeszukaj setki łączników i wyzwalaczy**.
2. Wpisz **harmonogram** w polu wyszukiwania.
3. Wybierz pozycję **harmonogram**, a następnie wybierz pozycję **harmonogram — cykl**.
4. W polu **częstotliwość** wybierz pozycję **dzień** , a następnie w polu **Interwał** wprowadź wartość **1**.<br><br>okno dialogowe wyzwalacza Microsoft Flow ![](media/flow-tutorial/flow01.png)


## <a name="step-3-add-a-log-analytics-action"></a>Krok 3. Dodawanie akcji Log Analytics
1. Kliknij pozycję **+ nowy krok**, a następnie kliknij pozycję **Dodaj akcję**.
2. Wyszukaj **log Analytics**.
3. Kliknij pozycję **Azure log Analytics — uruchom zapytanie i Wizualizuj wyniki**.<br><br>![Log Analytics uruchomienia okna zapytania](media/flow-tutorial/flow02.png)

## <a name="step-4-configure-the-log-analytics-action"></a>Krok 4. Konfigurowanie akcji Log Analytics

1. Określ szczegóły obszaru roboczego, w tym identyfikator subskrypcji, grupę zasobów i nazwę obszaru roboczego.
2. Dodaj następujące zapytanie dziennika do okna **zapytania** .  Jest to tylko przykładowe zapytanie i można je zastąpić innymi, które zwracają dane.
   ```
    Event
    | where EventLevelName == "Error" 
    | where TimeGenerated > ago(1day)
    | summarize count() by Computer
    | sort by Computer
   ```

2. Wybierz **tabelę HTML** dla **typu wykresu**.<br><br>![akcję Log Analytics](media/flow-tutorial/flow03.png)

## <a name="step-5-configure-the-flow-to-send-email"></a>Krok 5. Konfigurowanie przepływu do wysyłania wiadomości e-mail

1. Kliknij pozycję **nowy krok**, a następnie kliknij pozycję **+ Dodaj akcję**.
2. Wyszukaj **pakiet Office 365 Outlook**.
3. Kliknij pozycję **Office 365 Outlook — Wyślij wiadomość e-mail**.<br><br>okno wyboru ![Office 365 Outlook](media/flow-tutorial/flow04.png)

4. Określ adres e-mail adresata w oknie **do** i temat wiadomości E-mail w **temacie**.
5. Kliknij w dowolnym miejscu w polu **treść** .  Zostanie otwarte okno **zawartości dynamicznej** z wartościami z poprzednich akcji.  
6. Wybierz pozycję **treść**.  To są wyniki zapytania w akcji Log Analytics.
6. Kliknij pozycję **Pokaż opcje zaawansowane**.
7. W polu **kod HTML** wybierz pozycję **tak**.<br><br>![okna konfiguracji poczty e-mail pakietu Office 365](media/flow-tutorial/flow05.png)

## <a name="step-6-save-and-test-your-flow"></a>Krok 6. Zapisywanie i testowanie przepływu
1. W polu **Nazwa przepływu** Dodaj nazwę przepływu, a następnie kliknij pozycję **Utwórz przepływ**.<br><br>](media/flow-tutorial/flow06.png) ![zapisywania przepływu
2. Przepływ jest teraz tworzony i będzie uruchamiany po dniu, w którym określony został harmonogram. 
3. Aby natychmiast przetestować przepływ, kliknij przycisk **Uruchom teraz** , a następnie **Uruchom przepływ**.<br><br>](media/flow-tutorial/flow07.png) przebiegu ![
3. Po zakończeniu przepływu Sprawdź pocztę określonego adresata.  Powinna zostać odebrana wiadomość e-mail z treścią podobną do następującej:<br><br>![Przykładowa wiadomość e-mail](media/flow-tutorial/flow08.png)


## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej [na temat zapytań dzienników w Azure monitor](../log-query/log-query-overview.md).
- Dowiedz się więcej o [Microsoft Flow](https://ms.flow.microsoft.com).


