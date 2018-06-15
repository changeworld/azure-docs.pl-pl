---
title: Przykładowe akcji alertu elementu Webhook w OMS Log Analytics | Dokumentacja firmy Microsoft
description: Jedną z akcji można uruchomić w odpowiedzi na alert analizy dzienników jest * webhook *, dzięki czemu można wywołać procesu zewnętrznego przez pojedyncze żądanie HTTP. W tym artykule przedstawiono przykład tworzenia akcji elementu webhook w alercie analizy dzienników, przy użyciu zapas czasu.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 13c39f0f-fd3c-472d-8324-ddf7538be45e
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2017
ms.author: bwren
ms.openlocfilehash: 55b66132f7ec5c26c0a7cac1ec0a5c403dbd1082
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23855173"
---
# <a name="create-an-alert-webhook-action-in-oms-log-analytics-to-send-message-to-slack"></a>Utworzenie elementu webhook alertów w OMS analizy dzienników do wysyłania wiadomości zapas czasu
Jedną z akcji można uruchomić w odpowiedzi na [alert analizy dzienników](log-analytics-alerts.md) jest *webhook*, który umożliwia wywołanie procesu zewnętrznego przez pojedyncze żądanie HTTP.  Możesz przeczytać o szczegółach alertów i elementów webhook w [alertów w analizy dzienników](log-analytics-alerts.md)

W tym artykule omówimy przykładem tworzenia akcji elementu webhook w przypadku wystąpienia alertu analizy dzienników przy użyciu zapas czasu, która jest usługą obsługi wiadomości.

> [!NOTE]
> Musisz mieć konto Slack do ukończenia tego przykładu.  Można założyć bezpłatne konto w [slack.com](http://slack.com).
> 
> 

## <a name="step-1---enable-webhooks-in-slack"></a>Krok 1 — elementów webhook Włącz w zapas czasu
1. Zaloguj się do zapas czasu na [slack.com](http://slack.com).
2. Wybierz kanał w **kanałów** sekcji w okienku po lewej stronie.  Jest to kanał, który będzie można wysłać wiadomości do.  Można wybrać jedną z kanałów domyślne takich jak **ogólne** lub **losowe**.  W środowisku produkcyjnym, należy prawdopodobnie utworzyć kanał specjalne takie jak **criticalservicealerts**. <br>
   
   ![Kanałach Slack](media/log-analytics-alerts-webhooks/oms-webhooks01.png)
3. Kliknij przycisk **Dodawanie aplikacji lub niestandardowej integracji** można otworzyć katalogu aplikacji.
4. Typ *elementów webhook* w polu wyszukiwania, a następnie wybierz **przychodzące elementów Webhook**. <br>
   
   ![Kanałach Slack](media/log-analytics-alerts-webhooks/oms-webhooks02.png)
5. Kliknij przycisk **zainstalować** obok swojej nazwy zespołu.
6. Kliknij przycisk **Dodaj konfigurację**.
7. Wybierz kanał, który będzie w tym przykładzie, a następnie kliknij przycisk **integracji dodać przychodzące elementów Webhook**.  
8. Kopiuj **adresu URL elementu Webhook**.  Będzie można wklejane to w konfiguracji alertu. <br>
   
    ![Kanałach Slack](media/log-analytics-alerts-webhooks/oms-webhooks05.png)

## <a name="step-2---create-alert-rule-in-log-analytics"></a>Krok 2 — Tworzenie reguły alertu w analizy dzienników
1. [Tworzenie reguły alertu](log-analytics-alerts.md) z następującymi ustawieniami.
   * Zapytanie:```    Type=Event EventLevelName=error ```
   * Sprawdź, czy ten alert co: 5 minut
   * Liczba wyników: większe niż 10
   * W tym oknie: 60 minut
   * Wybierz **tak** dla **Webhook** i **nr** dla innych działań.
2. Wklej adres URL zapas czasu do **adresu URL elementu Webhook** pola.
3. Wybierz opcję **Uwzględnij niestandardowy ładunek JSON**.
4. Zapas oczekuje ładunku zapisany w formacie JSON z parametru o nazwie *tekstu*.  To jest tekst, który będzie wyświetlany w komunikacie, który tworzy.  Można użyć co najmniej jeden alert parametry, używając  *#*  symbol, taki jak w poniższym przykładzie.
   
    ```
    {
    "text":"#alertrulename fired with #searchresultcount records which exceeds the over threshold of #thresholdvalue ."
    }
    ```
   
    ![Przykładowy ładunek JSON](media/log-analytics-alerts-webhooks/oms-webhooks07.png)
5. Kliknij przycisk **zapisać** można zapisać reguły alertów.
6. Poczekaj wystarczającą ilość czasu dla tworzonego alertu można utworzyć, a następnie sprawdź zapas czasu dla wiadomości, które są podobne do następującego.
   
   ![przykład webhook w zapas czasu](media/log-analytics-alerts-webhooks/oms-webhooks08.png)

### <a name="advanced-webhook-payload-for-slack"></a>Zaawansowane ładunku elementu webhook dla zapas czasu
Często można dostosować komunikaty przychodzące z zapas czasu. Aby uzyskać więcej informacji, zobacz [przychodzące elementów Webhook](https://api.slack.com/incoming-webhooks) na Slack witryny sieci Web. Poniżej przedstawiono bardziej złożonych ładunku do tworzenia zaawansowanych wiadomości z formatowaniem.

    {
        "attachments": [
            {
                "title":"OMS Alerts Custom Payload",
                "fields": [
                    {
                        "title": "Alert Rule Name",
                        "value": "#alertrulename"},
                    {
                        "title": "Link To SearchResults",
                        "value": "<#linktosearchresults|OMS Search Results>"},
                    {
                        "title": "Search Interval",
                        "value": "#searchinterval"},
                    {
                        "title": "Threshold Operator",
                        "value": "#thresholdoperator"},
                    {
                        "title": "Threshold Value",
                        "value": "#thresholdvalue"}
                ],
                "color": "#F35A00"
            }
        ]
    }


Spowoduje to wygenerowanie komunikatu w zapas czasu podobny do następującego.

![Przykładowy komunikat w zapas czasu](media/log-analytics-alerts-webhooks/oms-webhooks09.png)

## <a name="summary"></a>Podsumowanie
Przy użyciu tej reguły alertów w miejscu trzeba komunikat wysłany do Slack za każdym razem, gdy są spełnione kryteria.  

To jest tylko jeden przykład działań, które można tworzyć w odpowiedzi na alert.  Można utworzyć akcję elementu webhook, która wywołuje innej usługi zewnętrzne, akcję elementu runbook, aby uruchomić element runbook automatyzacji Azure lub akcję poczty e-mail do wysyłania wiadomości e-mail do siebie lub innych odbiorców.   

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o innych [alertów akcje w analizy dzienników](log-analytics-alerts-actions.md) łącznie z czynności.


