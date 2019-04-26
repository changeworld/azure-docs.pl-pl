---
title: Alerty w czasie rzeczywistym usługi Azure CDN | Dokumentacja firmy Microsoft
description: Alerty w czasie rzeczywistym w usłudze Microsoft Azure CDN. W czasie rzeczywistym alerty zapewniają powiadomienia dotyczące wydajności do punktów końcowych w profilu sieci CDN.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 1e85b809-e1a9-4473-b835-69d1b4ed3393
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: e20161147aa16456e31aff2bd3cc6337c3690e89
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60335765"
---
# <a name="real-time-alerts-in-microsoft-azure-cdn"></a>Alerty w czasie rzeczywistym w usłudze Microsoft Azure CDN
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Omówienie
W tym dokumencie wyjaśniono alerty w czasie rzeczywistym w usłudze Microsoft Azure CDN. Ta funkcja udostępnia w czasie rzeczywistym powiadomienia dotyczące wydajności do punktów końcowych w profilu sieci CDN.  Można skonfigurować adres e-mail lub alerty HTTP na podstawie:

* Przepustowość
* Kody stanu
* Stany pamięci podręcznej
* Połączenia

## <a name="creating-a-real-time-alert"></a>Tworzenie alertu w czasie rzeczywistym
1. W [witryny Azure portal](https://portal.azure.com), przejdź do swojego profilu CDN.
   
    ![Profil CDN](./media/cdn-real-time-alerts/cdn-profile-blade.png)
1. Z poziomu bloku profil CDN kliknij **Zarządzaj** przycisku.
   
    ![Profil CDN Zarządzanie przycisku](./media/cdn-real-time-alerts/cdn-manage-btn.png)
   
    Zostanie otwarty w portalu zarządzania usługi CDN.
3. Umieść kursor nad **Analytics** kartę, a następnie umieść kursor nad **statystyki w czasie rzeczywistym** okno wysuwane.  Kliknij pozycję **alerty w czasie rzeczywistym**.
   
    ![Portal zarządzania usługi CDN](./media/cdn-real-time-alerts/cdn-premium-portal.png)
   
    Zostanie wyświetlona lista istniejących konfiguracji alertu (jeśli istnieje).
4. Kliknij przycisk **Dodaj Alert** przycisku.
   
    ![Dodawanie przycisku alertu](./media/cdn-real-time-alerts/cdn-add-alert.png)
   
    Zostanie wyświetlony formularz do tworzenia nowego alertu.
   
    ![Formularz nowego alertu](./media/cdn-real-time-alerts/cdn-new-alert.png)
5. Jeśli chcesz, aby ten alert na aktywny po kliknięciu **Zapisz**, sprawdź **Alert włączony** pola wyboru.
6. Wpisz nazwę opisową dla alertu w **nazwa** pola.
7. W **typ nośnika** listy rozwijanej wybierz **HTTP dużego obiektu**.
   
    ![Typ nośnika zaznaczony obiekt dużych HTTP](./media/cdn-real-time-alerts/cdn-http-large.png)
   
   > [!IMPORTANT]
   > Musisz wybrać **dużego obiektu HTTP** jako **typ nośnika**.  Inne opcje nie są używane przez **Azure CDN from Verizon**.  Nie można wybrać **dużego obiektu HTTP** powoduje, że Twój alert nigdy nie wyzwolone.
   > 
   > 
8. Tworzenie **wyrażenie** do monitorowania, wybierając **metryki**, **Operator**, i **wyzwolić wartość**.
   
   * Aby uzyskać **metryki**, wybierz typ warunku, które mają być monitorowane.  **MB/s przepustowości** ilość przepustowość w megabitach na sekundę.  **Łączna liczba połączeń** jest liczba równoczesnych połączeń HTTP z naszych serwerów granicznych.  Aby uzyskać definicje różne stany pamięci podręcznej i kodów stanu, zobacz [kodów stanu pamięci podręcznej usługi CDN Azure](/previous-versions/azure/mt759237(v=azure.100)) i [kodów stanu HTTP usługi CDN Azure](/previous-versions/azure/mt759238(v=azure.100))
   * **Operator** jest operatorów matematycznych, która ustanawia relację między metrykę i wartości wyzwalacza.
   * **Wyzwalanie wartość** jest wartość progowa, które muszą zostać spełnione przed wysłaniem powiadomienia.
     
     W poniższym przykładzie utworzono wyrażenie wskazuje na to, że powiadomienie jest wysyłane, gdy liczba kodów stanu 404 jest większy niż 25.
     
     ![Wyrażenie przykładowe alertów w czasie rzeczywistym](./media/cdn-real-time-alerts/cdn-expression.png)
9. Aby uzyskać **interwał**, wprowadź, jak często chcesz wyrażenia obliczonego.
10. W **powiadomienie na** listy rozwijanej wybierz opcję, jeśli chcesz otrzymywać powiadomienia, gdy wyrażenie jest prawdziwe.
    
    * **Stan rozpoczęcia** wskazuje, że powiadomienie jest wysyłane po pierwszym wykryciu określonego warunku.
    * **Warunku zakończenia** wskazuje, że powiadomienie jest wysyłane, gdy określony warunek nie zostanie wykryty. To powiadomienie może być uruchamiane wyłącznie po naszej sieci, monitorowanie system wykrył, że wystąpił określony warunek.
    * **Ciągłe** wskazuje, że powiadomienie jest wysyłane każdym razem, że system monitorowania sieci wykrywa określony warunek. Należy pamiętać, sprawdzający system monitorowania sieci tylko raz na interwał dla określonego warunku.
    * **Stan początkowy i końcowy** wskazuje, że powiadomienie jest wysyłane po raz pierwszy, czy określony warunek zostanie wykryte, i ponownie, gdy nie jest już zostanie wykryty warunek.
1. Jeśli chcesz otrzymywać powiadomienia pocztą e-mail, sprawdź **powiadamiania pocztą E-mail** pola wyboru.  
    
    ![Powiadom za pomocą formularza wiadomości E-mail](./media/cdn-real-time-alerts/cdn-notify-email.png)
    
    W **do** wprowadź adres e-mail, której chcesz powiadomienia wysłane. Dla **podmiotu** i **treści**, można pozostawić domyślne lub możesz dostosować komunikat przy użyciu **dostępne słowa kluczowe** listy, aby dynamicznie wstawić dane alertów po wiadomości są wysyłane.
    
    > [!NOTE]
    > Powiadomienia e-mail można sprawdzić, klikając **testowe powiadomienie E-mail** przycisk, ale tylko po zapisaniu podczas konfigurowania alertu.
    > 
    > 
12. Jeśli chcesz, aby powiadomienia do opublikowania na serwerze sieci web, sprawdź **powiadamiania przez żądania HTTP Post** pola wyboru.
    
    ![Powiadom za pomocą żądania HTTP Post formularza](./media/cdn-real-time-alerts/cdn-notify-http.png)
    
    W **adresu Url** wprowadź adres URL, którego komunikatu HTTP księgowania. W **nagłówki** polu tekstowym Wprowadź nagłówki HTTP, które mają być wysyłane w żądaniu.  Dla **treści**, może dostosować komunikat, używając **dostępne słowa kluczowe** listy, aby dynamicznie wstawić dane alertów po wysłaniu wiadomości.  **Nagłówki** i **treści** domyślnie ładunek XML, podobny do poniższego przykładu:
    
    ```
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">
        <![CDATA[Expression=Status Code : 404 per second > 25&Metric=Status Code : 404 per second&CurrentValue=[CurrentValue]&NotificationCondition=Condition Start]]>
    </string>
    ```
    
    > [!NOTE]
    > Powiadomienie Post protokołu HTTP można sprawdzić, klikając **testowe powiadomienie E-mail** przycisk, ale tylko po zapisaniu podczas konfigurowania alertu.
    > 
    > 
13. Kliknij przycisk **Zapisz** przycisk, aby zapisać konfigurację alertu.  Jeśli zaznaczono **Alert włączony** w kroku 5, Twój alert jest teraz aktywna.

## <a name="next-steps"></a>Następne kroki
* Analizowanie [statystyki w czasie rzeczywistym w usłudze Azure CDN](cdn-real-time-stats.md)
* Postaraj się bardziej z [zaawansowane raporty HTTP](cdn-advanced-http-reports.md)
* Analizowanie [wzorców użycia](cdn-analyze-usage-patterns.md)

