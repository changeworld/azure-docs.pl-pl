---
title: Alerty usługi Azure CDN w czasie rzeczywistym | Dokumenty firmy Microsoft
description: Alerty w czasie rzeczywistym w sieci CDN platformy Microsoft Azure. Alerty w czasie rzeczywistym zapewniają powiadomienia o wydajności punktów końcowych w profilu sieci CDN.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 1e85b809-e1a9-4473-b835-69d1b4ed3393
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 4b8cbc27757cf6c321ea4b3c27720a129aa27c1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593478"
---
# <a name="real-time-alerts-in-microsoft-azure-cdn"></a>Alerty w czasie rzeczywistym w sieci CDN platformy Microsoft Azure
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Omówienie
W tym dokumencie opisano alerty w czasie rzeczywistym w sieci CDN platformy Microsoft Azure. Ta funkcja zapewnia powiadomienia w czasie rzeczywistym o wydajności punktów końcowych w profilu sieci CDN.  Alerty e-mail lub HTTP można skonfigurować na podstawie:

* Przepustowość
* Kody stanu
* Stany pamięci podręcznej
* Połączenia

## <a name="creating-a-real-time-alert"></a>Tworzenie alertu w czasie rzeczywistym
1. W [witrynie Azure portal](https://portal.azure.com)przejdź do swojego profilu usługi CDN.
   
    ![Profil CDN](./media/cdn-real-time-alerts/cdn-profile-blade.png)
1. W bloku profilu sieci CDN kliknij przycisk **Zarządzaj.**
   
    ![Przycisk zarządzania profilem CDN](./media/cdn-real-time-alerts/cdn-manage-btn.png)
   
    Zostanie otwarty portal zarządzania siecią CDN.
3. Umieść wskaźnik myszy na karcie **Analytics,** a następnie umieść wskaźnik myszy na **wysu wysu wysu** w czasie rzeczywistym.  Kliknij **alerty w czasie rzeczywistym**.
   
    ![Portal zarządzania siecią CDN](./media/cdn-real-time-alerts/cdn-premium-portal.png)
   
    Zostanie wyświetlona lista istniejących konfiguracji alertów (jeśli istnieją).
4. Kliknij przycisk **Dodaj alert.**
   
    ![Przycisk Dodaj alert](./media/cdn-real-time-alerts/cdn-add-alert.png)
   
    Zostanie wyświetlony formularz do utworzenia nowego alertu.
   
    ![Nowy formularz alertu](./media/cdn-real-time-alerts/cdn-new-alert.png)
5. Jeśli chcesz, aby ten alert był aktywny po **kliknięciu przycisku Zapisz,** zaznacz pole wyboru **Włączone alerty.**
6. Wprowadź opisową nazwę alertu w polu **Nazwa.**
7. Z listy rozwijanej **Typ nośnika** wybierz pozycję **Duży obiekt HTTP**.
   
    ![Typ nośnika z zaznaczonym dużym obiektem HTTP](./media/cdn-real-time-alerts/cdn-http-large.png)
   
   > [!IMPORTANT]
   > Jako **typ nośnika**należy wybrać **duży obiekt HTTP** .  Inne opcje nie są używane przez **usługę Azure CDN firmy Verizon**.  Niepowodzenie wybrania **dużego obiektu HTTP** powoduje, że alert nigdy nie zostanie wyzwolony.
   > 
   > 
8. Utwórz **wyrażenie** do monitorowania, wybierając **wartość metryki,** **operator**i **wyzwalacz.**
   
   * W przypadku **opcji Metryka**wybierz typ warunku, który ma być monitorowany.  **Przepustowość Mbps** to ilość wykorzystania przepustowości w megabitach na sekundę.  **Total Connections** to liczba równoczesnych połączeń HTTP z naszymi serwerami brzegowymi.  Definicje różnych stanów pamięci podręcznej i kodów stanu można znaleźć w [przykładach kodów stanu pamięci podręcznej usługi Azure CDN i](/previous-versions/azure/mt759237(v=azure.100)) [kodów stanu HTTP usługi Azure CDN](/previous-versions/azure/mt759238(v=azure.100))
   * **Operator** jest operatorem matematycznym, który ustanawia relację między metryką a wartością wyzwalacza.
   * **Wartość wyzwalacza** jest wartością progową, która musi zostać spełniona przed wysłaniem powiadomienia.
     
     W poniższym przykładzie utworzone wyrażenie wskazuje, że powiadomienie jest wysyłane, gdy liczba kodów stanu 404 jest większa niż 25.
     
     ![Przykładowe wyrażenie alertu w czasie rzeczywistym](./media/cdn-real-time-alerts/cdn-expression.png)
9. W polu **Interval**należy wprowadzić sposób, w jakiej często ma być oceniane wyrażenie.
10. Z listy rozwijanej **Powiadom** o tym wybierz, kiedy chcesz otrzymywać powiadomienia, kiedy wyrażenie jest prawdziwe.
    
    * **Warunek Start** wskazuje, że powiadomienie jest wysyłane po pierwszym wykryciu określonego warunku.
    * **Warunek End** wskazuje, że powiadomienie jest wysyłane, gdy określony warunek nie jest już wykrywany. To powiadomienie może zostać wyzwolone dopiero po wykryciu przez nasz system monitorowania sieci, że wystąpił określony warunek.
    * **Ciągły** wskazuje, że powiadomienie jest wysyłane za każdym razem, gdy system monitorowania sieci wykrywa określony warunek. Należy pamiętać, że system monitorowania sieci sprawdza tylko raz na interwał dla określonego warunku.
    * **Warunek Start i End** wskazuje, że powiadomienie jest wysyłane po raz pierwszy, że określony warunek jest wykrywany i ponownie, gdy warunek nie jest już wykrywany.
1. Jeśli chcesz otrzymywać powiadomienia pocztą e-mail, zaznacz pole wyboru **Powiadom przez e-mail.**  
    
    ![Powiadom za pomocą formularza e-mail](./media/cdn-real-time-alerts/cdn-notify-email.png)
    
    W polu **Do** wprowadź adres e-mail, na który chcesz wysłać powiadomienia. W przypadku **tematu** i **treści**możesz pozostawić wartość domyślną lub dostosować wiadomość za pomocą listy **Dostępne słowa kluczowe,** aby dynamicznie wstawiać dane alertu podczas wysyłania wiadomości.
    
    > [!NOTE]
    > Powiadomienie e-mail można przetestować, klikając przycisk **Testuj powiadomienie,** ale tylko po zapisaniu konfiguracji alertu.
    > 
    > 
12. Jeśli chcesz, aby powiadomienia były księgowane na serwerze sieci Web, zaznacz pole wyboru **Powiadom według protokołu HTTP.**
    
    ![Powiadom za pomocą formularza wpisu HTTP](./media/cdn-real-time-alerts/cdn-notify-http.png)
    
    W polu **Adres URL** wprowadź adres URL, pod którym chcesz opublikować wiadomość HTTP. W polu tekstowym **Nagłówki** wprowadź nagłówki HTTP, które mają zostać wysłane w żądaniu.  W przypadku **treści**można dostosować wiadomość za pomocą listy **Dostępne słowa kluczowe,** aby dynamicznie wstawiać dane alertu podczas wysyłania wiadomości.  **Nagłówki** i **treść** domyślnie do ładunku XML podobne do następującego przykładu:
    
    ```
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">
        <![CDATA[Expression=Status Code : 404 per second > 25&Metric=Status Code : 404 per second&CurrentValue=[CurrentValue]&NotificationCondition=Condition Start]]>
    </string>
    ```
    
    > [!NOTE]
    > Powiadomienie http post można przetestować, klikając przycisk **Testuj powiadomienie,** ale tylko po zapisaniu konfiguracji alertu.
    > 
    > 
13. Kliknij przycisk **Zapisz,** aby zapisać konfigurację alertów.  Jeśli w kroku 5 zaznaczono **włączono alert,** alert jest teraz aktywny.

## <a name="next-steps"></a>Następne kroki
* Analizowanie [statystyk czasu rzeczywistego w usłudze Azure CDN](cdn-real-time-stats.md)
* Kopiuj głębiej dzięki [zaawansowanym raportom HTTP](cdn-advanced-http-reports.md)
* Analizowanie [wzorców użycia](cdn-analyze-usage-patterns.md)

