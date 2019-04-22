---
title: Statystyki w czasie rzeczywistym w usłudze Azure CDN | Dokumentacja firmy Microsoft
description: Statystyki w czasie rzeczywistym udostępnia w czasie rzeczywistym dane dotyczące wydajności usługi Azure CDN, podczas dostarczania zawartości do klientów.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: c7989340-1172-4315-acbb-186ba34dd52a
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: eb20630533735fb46ea7743be75448329281938a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58916567"
---
# <a name="real-time-stats-in-microsoft-azure-cdn"></a>Statystyki w czasie rzeczywistym w usłudze Microsoft Azure CDN
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Omówienie
W tym dokumencie wyjaśniono statystyki w czasie rzeczywistym w usłudze Microsoft Azure CDN.  Ta funkcja udostępnia w czasie rzeczywistym dane, takie jak przepustowość, stany pamięci podręcznej i równoczesnych połączeń do swojego profilu CDN podczas dostarczania zawartości do klientów. Dzięki temu ciągłego monitorowania kondycji usługi, w dowolnym momencie, w tym gotowej do pracy zdarzenia.

Dostępne są następujące wykresy:

* [Bandwidth](#bandwidth)
* [Kody stanu](#status-codes)
* [Stany pamięci podręcznej](#cache-statuses)
* [Połączenia](#connections)

## <a name="accessing-real-time-stats"></a>Uzyskiwanie dostępu do statystyki w czasie rzeczywistym
1. W [witryny Azure Portal](https://portal.azure.com), przejdź do swojego profilu CDN.
   
    ![Blok profilu CDN](./media/cdn-real-time-stats/cdn-profile-blade.png)
2. Z poziomu bloku profil CDN kliknij **Zarządzaj** przycisku.
   
    ![Blok profilu CDN Zarządzanie przycisku](./media/cdn-real-time-stats/cdn-manage-btn.png)
   
    Zostanie otwarty w portalu zarządzania usługi CDN.
3. Umieść kursor nad **Analytics** kartę, a następnie umieść kursor nad **statystyki w czasie rzeczywistym** okno wysuwane.  Kliknij pozycję **HTTP dużego obiektu**.
   
    ![Portal zarządzania usługi CDN](./media/cdn-real-time-stats/cdn-premium-portal.png)
   
    Wykresy statystyki w czasie rzeczywistym są wyświetlane.

Każda wykresów Wyświetla statystyki w czasie rzeczywistym dla zakresu wybrana wartość czasu, rozpoczynając od strona ładuje się.  Wykresy są aktualizowane automatycznie co kilka sekund.  **Odśwież wykres** przycisku, jeśli jest obecny, spowoduje to wyczyszczenie wykres, po czym zostanie wyświetlona tylko wybrane dane.

## <a name="bandwidth"></a>Przepustowość
![Wykres przepustowości](./media/cdn-real-time-stats/cdn-bandwidth.png)

**Przepustowości** wykresu wyświetlana jest ilość przepustowości używanej dla bieżącej platformy przez wybrany okres. Zacieniony fragment wykres wskazuje wykorzystanie przepustowości. Dokładne zalecenia dotyczące ilości przepustowości, które są obecnie używane jest wyświetlana bezpośrednio poniżej wykres liniowy.

## <a name="status-codes"></a>Kody stanu
![Wykres kodu stanu](./media/cdn-real-time-stats/cdn-status-codes.png)

**Kodów stanu** wykres wskazuje, jak często występują pewne kody odpowiedzi HTTP przez wybrany okres.

> [!TIP]
> Aby uzyskać opis każdej opcji Kod stanu HTTP, zobacz [kodów stanu HTTP usługi CDN Azure](/previous-versions/azure/mt759238(v=azure.100)).
> 
> 

Zostanie wyświetlona lista kodów stanu HTTP bezpośrednio powyżej wykresu. Ta lista wskazuje poszczególnych kodów stanu, które mogły zostać uwzględnione w wykres liniowy i bieżącą liczbę wystąpień na sekundę dla tego kodu stanu. Domyślnie wiersza jest wyświetlana dla każdego z tych kodów stanu na wykresie. Jednakże można monitorować tylko kody stanu, które mają specjalne znaczenie dla Twojej konfiguracji usługi CDN. W tym celu należy sprawdzić kody żądany stan i wyczyść wszystkie inne opcje, a następnie kliknij **Odśwież wykres**. 

Można tymczasowo ukryć zarejestrowanych danych dla kodu określonego stanu.  W legendzie bezpośrednio pod wykresem kliknij kod stanu, który chcesz ukryć. Kod stanu będą natychmiast ukryte dla wykresu. Ponowne kliknięcie kod stanu spowoduje, że ta opcja ma być wyświetlany ponownie.

## <a name="cache-statuses"></a>Stany pamięci podręcznej
![Wykres stany pamięci podręcznej](./media/cdn-real-time-stats/cdn-cache-status.png)

**Stany pamięci podręcznej** wykres wskazuje, jak często występują pewne rodzaje stany pamięci podręcznej przez wybrany okres. 

> [!TIP]
> Aby uzyskać opis każdej opcji Kod stanu pamięci podręcznej, zobacz [kodów stanu pamięci podręcznej usługi CDN Azure](/previous-versions/azure/mt759237(v=azure.100)).
> 
> 

Zostanie wyświetlona lista kodów stanu pamięci podręcznej bezpośrednio powyżej wykresu. Ta lista wskazuje poszczególnych kodów stanu, które mogły zostać uwzględnione w wykres liniowy i bieżącą liczbę wystąpień na sekundę dla tego kodu stanu. Domyślnie wiersza jest wyświetlana dla każdego z tych kodów stanu na wykresie. Jednakże można monitorować tylko kody stanu, które mają specjalne znaczenie dla Twojej konfiguracji usługi CDN. W tym celu należy sprawdzić kody żądany stan i wyczyść wszystkie inne opcje, a następnie kliknij **Odśwież wykres**. 

Można tymczasowo ukryć zarejestrowanych danych dla kodu określonego stanu.  W legendzie bezpośrednio pod wykresem kliknij kod stanu, który chcesz ukryć. Kod stanu będą natychmiast ukryte dla wykresu. Ponowne kliknięcie kod stanu spowoduje, że ta opcja ma być wyświetlany ponownie.

## <a name="connections"></a>Połączenia
![Wykres połączeń](./media/cdn-real-time-stats/cdn-connections.png)

Ten wykres wskazuje, ile połączeń ustanowionych z serwerami usługi edge. Każde żądanie dla elementu zawartości, który przechodzi przez naszych wyników usługi CDN, w przypadku połączenia.

## <a name="next-steps"></a>Następne kroki
* Otrzymaj powiadomienie [alerty w czasie rzeczywistym w usłudze Azure CDN](cdn-real-time-alerts.md)
* Postaraj się bardziej z [zaawansowane raporty HTTP](cdn-advanced-http-reports.md)
* Analizowanie [wzorców użycia](cdn-analyze-usage-patterns.md)

