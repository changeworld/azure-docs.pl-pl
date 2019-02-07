---
title: Użytkownik zainicjował akcję elementu Runbook usługi Azure Automation w usłudze Log Analytics | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób uruchamiania elementu runbook usługi Automation od usługi Log Analytics Wyszukaj wynik na żądanie.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/06/2019
ms.author: magoedte
ms.openlocfilehash: 61d0f74f59b4d6f59b3fbc87556b260751d33baa
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55809637"
---
# <a name="take-action-with-an-automation-runbook-from-a-log-analytics-log-search-result"></a>Chroń za pomocą elementu Runbook usługi Automation z wyniki wyszukiwania dziennika usługi Log Analytics

Wyniki wyszukiwania dziennika w usłudze Azure Log Analytics, można teraz wybrać **reakcję** do uruchamiania elementu runbook usługi Automation. Element runbook może służyć do rozwiązania problemu lub wykonania określonego działania, takie jak zbierać informacje dotyczące rozwiązywania problemów, Wyślij wiadomość e-mail, lub Utwórz żądanie obsługi. 

## <a name="components-and-features-used"></a>Używane składniki i funkcje
* [Konto usługi Azure Automation](../../automation/automation-quickstart-create-account.md)
* [Obszar roboczy usługi Log Analytics](../../azure-monitor/log-query/log-query-overview.md)

## <a name="to-initiate-runbook-from-log-search"></a>Aby zainicjować element runbook z przeszukiwania dzienników

Podejmowanie akcji na zdarzenia i zainicjowania elementu runbook z wyników wyszukiwania w dzienniku, rozpoczyna się od utworzenia przeszukiwania dzienników, a następnie na liście wyników można wywołać elementu runbook na żądanie. Można to osiągnąć przez funkcję wyszukiwania dziennika klasyczne w [witryny Azure portal](../../azure-monitor/log-query/log-query-overview.md). W tym przykładzie wykonamy przeszukiwania dzienników w witrynie Azure portal przy użyciu podstawowych pokaz działania tej funkcji.

1. W witrynie Azure portal kliknij pozycję **wszystkich usług** i wybierz **usługi Log Analytics**.  
2. Wybierz swój obszar roboczy usługi Log Analytics.
3. W obszarze roboczym, wybierz **dzienniki (wersja klasyczna)**.  
4. Na stronie wyszukiwania w dziennikach wykonaj wyszukiwanie w dzienniku.  
5. Z poziomu wyników wyszukiwania dziennika, kliknij przycisk wielokropka z lewej strony pól i z menu podręczne, wybierz opcję **podejmowanie akcji na**.<br><br> ![Wybierz podjąć akcję z wyników wyszukiwania](./media/take-action/log-search-takeaction-menuoption.png) 
6. Wybierz **uruchomienia elementu runbook** i wybierz pozycję uruchomienia elementu runbook.  Na koncie usługi Automation, która jest połączona z obszarem roboczym usługi Log Analytics, możesz wybrać każdego elementu runbook.  Pamiętaj o następujących kwestiach:

    * Elementy Runbook są zorganizowane według tagów
    * Można wybrać wartości parametru wejściowego elementu Runbook bezpośrednio z pól w wynikach wyszukiwania.  Zostanie wyświetlona lista listy rozwijanej wyświetlane wszystkie dostępne pola z wyników, które można wybierać.  
    * Istnieje możliwość uruchomienia elementu runbook [hybrydowego procesu roboczego runbook](../../automation/automation-hybrid-runbook-worker.md) zainstalowanego na komputerze, który ma problem, jeśli odpowiednia grupa hybrydowego procesu roboczego Runbook, która zawiera tylko ten komputer jako członka.  Jeśli nazwa grupy hybrydowych procesów roboczych jest zgodna z nazwą komputera, na którym znajduje się w wynikach wyszukiwania dziennika, grupa jest wybrana automatycznie.    

6. Po kliknięciu **Uruchom**, aby możliwe było sprawdzać stan zadania zostanie otwarta strona zadania elementu runbook.   

W przypadku wybrania elementu runbook, który został skonfigurowany jako [wywoływane z poziomu alertu usługi Log Analytics](../../automation/automation-create-alert-triggered-runbook.md), posiada parametr wejściowy o nazwie **WebhookData** czyli **obiektu** typu.  Parametr wejściowy jest obowiązkowy, należy przekazać wyniki wyszukiwania do elementu runbook, aby ją przekonwertować ciąg w formacie JSON do typu obiektu, dzięki czemu można filtrować na określone elementy, które będziesz odwoływać się w działania elementu runbook.  Można to zrobić, wybierając **wynik (obiekt) wyszukiwania** z listy rozwijanej.<br><br> ![Wybierz obiekt danych elementu Webhook dla parametru elementu runbook](media/take-action/select-runbook-and-properties.png)   
    
## <a name="next-steps"></a>Kolejne kroki

* Przegląd [Zaloguj się odwołanie do wyszukiwania usługi Log Analytics](../../azure-monitor/log-query/log-query-overview.md) Aby wyświetlić wszystkie pola wyszukiwania i aspektów, które są dostępne w usłudze Log Analytics.
* Aby dowiedzieć się, jak automatycznie wywoływania elementu runbook usługi Automation, zapoznaj się z [wywoływanie elementu runbook usługi Azure Automation z alertu usługi Log Analytics](../../automation/automation-create-alert-triggered-runbook.md).  
