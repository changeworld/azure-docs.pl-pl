---
title: Azure CDN alertów w czasie rzeczywistym | Dokumentacja firmy Microsoft
description: Alertów w czasie rzeczywistym w usłudze Microsoft Azure CDN. Alerty w czasie rzeczywistym udostępniają powiadomienia dotyczące wydajności punktów końcowych w Twoim profilu CDN.
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
ms.openlocfilehash: 51dce1680be5f5f4387c2ba02827195bcdbe9b48
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23843154"
---
# <a name="real-time-alerts-in-microsoft-azure-cdn"></a>Alerty w czasie rzeczywistym w usłudze Microsoft Azure CDN
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Omówienie
W tym dokumencie opisano alertów w czasie rzeczywistym w usłudze Microsoft Azure CDN. Ta funkcja udostępnia w czasie rzeczywistym powiadomienia o wydajności z punktów końcowych w profilu CDN.  Można skonfigurować adres e-mail lub alerty HTTP na podstawie:

* Przepustowość
* Kody stanu
* Stany pamięci podręcznej
* Połączenia

## <a name="creating-a-real-time-alert"></a>Tworzenie w czasie rzeczywistym alertu
1. W [portalu Azure](https://portal.azure.com), przejdź do swojego profilu CDN.
   
    ![Profil CDN](./media/cdn-real-time-alerts/cdn-profile-blade.png)
1. Blok profilu CDN, kliknij **Zarządzaj** przycisku.
   
    ![Przycisk Zarządzaj profilu CDN](./media/cdn-real-time-alerts/cdn-manage-btn.png)
   
    Zostanie otwarty w portalu zarządzania usługi CDN.
3. Umieść kursor nad **Analytics** , a następnie umieść kursor nad **statystyki w czasie rzeczywistym** wysuwane okno.  Polecenie **alertów w czasie rzeczywistym**.
   
    ![Portal zarządzania usługi CDN](./media/cdn-real-time-alerts/cdn-premium-portal.png)
   
    Zostanie wyświetlona lista istniejących konfiguracji alertu (jeśli istnieje).
4. Kliknij przycisk **dodać alertu** przycisku.
   
    ![Dodawanie przycisku alertu](./media/cdn-real-time-alerts/cdn-add-alert.png)
   
    Zostanie wyświetlony formularz służący do tworzenia nowego alertu.
   
    ![Nowy formularz alertu](./media/cdn-real-time-alerts/cdn-new-alert.png)
5. Jeśli chcesz, aby ten alert jest aktywne po kliknięciu **zapisać**, sprawdź **włączone Alert** wyboru.
6. Wprowadź nazwę opisową dla alertu w **nazwa** pola.
7. W **typ nośnika** listy rozwijanej wybierz **HTTP dużego obiektu**.
   
    ![Typ nośnika z wybranego obiektu dużych HTTP](./media/cdn-real-time-alerts/cdn-http-large.png)
   
   > [!IMPORTANT]
   > Musisz wybrać **HTTP dużego obiektu** jako **typ nośnika**.  Inne opcje nie są używane przez **Azure CDN from Verizon**.  Nie można wybrać **HTTP dużego obiektu** powoduje, że nigdy nie wyzwolenie alertu.
   > 
   > 
8. Utwórz **wyrażenie** do monitorowania, wybierając **Metryka**, **Operator**, i **wyzwolenia wartość**.
   
   * Aby uzyskać **Metryka**, wybierz typ warunku, które mają być monitorowane.  **MB/s przepustowości** ilość przepustowości w megabitach na sekundę.  **Całkowita liczba połączeń** jest liczba równoczesnych połączeń HTTP nasze serwery krawędzi.  Definicje różne stany pamięci podręcznej i kodów stanu, zobacz [kodów stanu systemu Azure CDN pamięci podręcznej](https://msdn.microsoft.com/library/mt759237.aspx) i [kody stanu HTTP Azure w sieci CDN](https://msdn.microsoft.com/library/mt759238.aspx)
   * **Operator** jest operatorów matematycznych, która ustanawia relację między Metryka i wartość wyzwalacza.
   * **Wyzwalanie wartość** jest wartość progowa, które muszą zostać spełnione, aby wysłać powiadomienia.
     
     W poniższym przykładzie utworzone wyrażenie wskazuje, czy powiadomienie jest wysyłane, gdy liczba kodów stanu 404 jest większa niż 25.
     
     ![W czasie rzeczywistym alertu przykładowe wyrażenie](./media/cdn-real-time-alerts/cdn-expression.png)
9. Aby uzyskać **interwał**, wprowadź, jak często chcesz wyrażenia obliczonego.
10. W **powiadomienia na** listy rozwijanej wybierz opcję, jeśli chcesz otrzymać powiadomienie, gdy wyrażenie jest prawdziwe.
    
    * **Warunkiem rozpoczęcia** wskazuje, czy powiadomienie jest wysyłane po raz pierwszy wykryciu określony warunek.
    * **Warunek zakończenia** wskazuje, czy powiadomienie jest wysyłane, gdy określony warunek nie zostanie wykryty. To powiadomienie można wywoływać tylko po naszej system monitorowania sieci wykrył, że wystąpił określony warunek.
    * **Ciągłe** wskazuje, czy powiadomienie jest wysyłane zawsze wykryje, że system monitorowania sieci określony warunek. Należy pamiętać, że system monitorowania sieci sprawdza tylko raz na interwał określony warunek.
    * **Stan początkowy i końcowy** wskazuje, czy powiadomienie jest wysyłane po raz pierwszy, czy określony warunek zostanie wykryte, i ponownie, gdy nie jest już została wykryta warunku.
1. Jeśli chcesz otrzymywać powiadomienia pocztą e-mail, sprawdź **powiadamiania pocztą E-mail** wyboru.  
    
    ![Powiadomienia E-mail formularza](./media/cdn-real-time-alerts/cdn-notify-email.png)
    
    W **do** wprowadź adres e-mail, gdzie ma powiadomienia wysyłane. Dla **podmiotu** i **treści**, może pozostaw wartość domyślną, lub możesz dostosować komunikat przy użyciu **dostępne słowa kluczowe** listy, aby dynamicznie wstawić dane alertów po wiadomości są wysyłane.
    
    > [!NOTE]
    > Powiadomienia e-mail można sprawdzić, klikając **testowe powiadomienie E-mail** przycisku, ale tylko po zapisaniu alertu konfiguracji.
    > 
    > 
12. Jeśli chcesz, aby powiadomienia można opublikować na serwerze sieci web, sprawdź **powiadamiania przez HTTP Post** wyboru.
    
    ![Powiadom HTTP Post formularza](./media/cdn-real-time-alerts/cdn-notify-http.png)
    
    W **adres Url** wprowadź adres URL, gdzie ma komunikat HTTP opublikowane. W **nagłówki** pole tekstowe, wprowadź nagłówki HTTP do wysłania w żądaniu.  Dla **treści**, można dostosować komunikat przy użyciu **dostępne słowa kluczowe** listy, aby dynamicznie wstawić dane alertów, gdy komunikat jest wysyłany.  **Nagłówki** i **treści** domyślną ładunek XML podobny do poniższego przykładu:
    
    ```
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">
        <![CDATA[Expression=Status Code : 404 per second > 25&Metric=Status Code : 404 per second&CurrentValue=[CurrentValue]&NotificationCondition=Condition Start]]>
    </string>
    ```
    
    > [!NOTE]
    > Należy przetestować powiadomienia HTTP Post, klikając **testowe powiadomienie E-mail** przycisku, ale tylko po zapisaniu alertu konfiguracji.
    > 
    > 
13. Kliknij przycisk **zapisać** przycisk, aby zapisać konfigurację alertu.  Jeśli zaznaczono **włączone Alert** w kroku 5 alertu jest aktywna.

## <a name="next-steps"></a>Następne kroki
* Analizowanie [statystyki w czasie rzeczywistym w usłudze Azure CDN](cdn-real-time-stats.md)
* Dig głębiej z [zaawansowane raporty HTTP](cdn-advanced-http-reports.md)
* Analizowanie [wzorców użycia](cdn-analyze-usage-patterns.md)

