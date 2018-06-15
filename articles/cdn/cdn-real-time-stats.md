---
title: Statystyki w czasie rzeczywistym w usłudze Azure CDN | Dokumentacja firmy Microsoft
description: Statystyki w czasie rzeczywistym udostępnia w czasie rzeczywistym danych dotyczących wydajności usługi Azure CDN podczas dostarczania zawartości do klientów.
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
ms.openlocfilehash: e9b9522de6b2c54dc794b00100ffe358296ecfdd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23842881"
---
# <a name="real-time-stats-in-microsoft-azure-cdn"></a>Statystyki w czasie rzeczywistym w usłudze Microsoft Azure CDN
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Omówienie
W tym dokumencie opisano statystyki w czasie rzeczywistym w usłudze Microsoft Azure CDN.  Ta funkcja udostępnia w czasie rzeczywistym danych, takich jak przepustowości, pamięci podręcznej stanów i jednoczesnych połączeń z profilu CDN podczas dostarczania zawartości do klientów. Dzięki temu ciągłego monitorowania kondycji usługi, w dowolnym momencie, w tym zdarzenia dotyczące przejdź na żywo.

Dostępne są następujące wykresy:

* [Przepustowość](#bandwidth)
* [Kody stanu](#status-codes)
* [Stany pamięci podręcznej](#cache-statuses)
* [Połączenia](#connections)

## <a name="accessing-real-time-stats"></a>Uzyskiwanie dostępu do statystyki w czasie rzeczywistym
1. W [Azure Portal](https://portal.azure.com), przejdź do swojego profilu CDN.
   
    ![Blok profilu CDN](./media/cdn-real-time-stats/cdn-profile-blade.png)
2. Blok profilu CDN, kliknij **Zarządzaj** przycisku.
   
    ![Przycisk Zarządzaj blok profilu CDN](./media/cdn-real-time-stats/cdn-manage-btn.png)
   
    Zostanie otwarty w portalu zarządzania usługi CDN.
3. Umieść kursor nad **Analytics** , a następnie umieść kursor nad **statystyki w czasie rzeczywistym** wysuwane okno.  Polecenie **HTTP dużego obiektu**.
   
    ![Portal zarządzania usługi CDN](./media/cdn-real-time-stats/cdn-premium-portal.png)
   
    Są wyświetlane na wykresach statystyki w czasie rzeczywistym.

Każda wykresów Wyświetla statystyki w czasie rzeczywistym dla zakresu wybrana wartość czasu, uruchamianie podczas ładowania strony.  Wykresy automatycznie aktualizowane co kilka sekund.  **Odśwież wykres** przycisku, jeśli jest obecny, spowoduje wyczyszczenie wykresu, po czym zostanie wyświetlona tylko wybranych danych.

## <a name="bandwidth"></a>Przepustowość
![Wykres przepustowości](./media/cdn-real-time-stats/cdn-bandwidth.png)

**Przepustowości** wykres przedstawia ilość przepustowości używanej w ramach bieżącej platformy w wybranym okresie. Przyciemnione część wykres wskazuje użycia przepustowości. Dokładne ilość przepustowości aktualnie używane jest wyświetlana bezpośrednio pod wykres liniowy.

## <a name="status-codes"></a>Kody stanu
![Wykres kod stanu](./media/cdn-real-time-stats/cdn-status-codes.png)

**Kodów stanu** wykres wskazuje, jak często występuje niektórych kody odpowiedzi HTTP przez wybrany okres.

> [!TIP]
> Opis każdej opcji Kod stanu HTTP, zobacz [kody stanu HTTP CDN Azure](https://msdn.microsoft.com/library/mt759238.aspx).
> 
> 

Bezpośrednio powyżej wykresu zostanie wyświetlona lista kodów stanu HTTP. Ta lista wskazuje poszczególnych kodów stanu, który można umieścić w wykres liniowy i bieżącą liczbę wystąpień na sekundę dla tego kodu stanu. Domyślnie dla każdego z tych kodów stanu na wykresie jest wyświetlany wiersza. Można jednak tylko monitorowanie kodów stanu, które mają specjalne znaczenie dla danej konfiguracji sieci CDN. Aby to zrobić, sprawdź kody żądany stan i wyczyść wszystkie inne opcje, a następnie kliknij **Odśwież wykres**. 

Można ukryć danych zarejestrowanych dla kodu określonego stanu.  W legendzie bezpośrednio pod wykresem kliknij kod stanu, który chcesz ukryć. Kod stanu będą natychmiast ukryte wykresu. Ponowne kliknięcie tego kodu stanu spowoduje, że ta opcja będzie wyświetlana ponownie.

## <a name="cache-statuses"></a>Stany pamięci podręcznej
![Wykres stanów pamięci podręcznej](./media/cdn-real-time-stats/cdn-cache-status.png)

**Pamięci podręcznej stanów** wykres wskazuje, jak często występują niektóre rodzaje pamięci podręcznej stanów w wybranym okresie. 

> [!TIP]
> Opis opcji Kod stanu każdego pamięci podręcznej, zobacz [kodów stanu systemu Azure CDN pamięci podręcznej](https://msdn.microsoft.com/library/mt759237.aspx).
> 
> 

Zostanie wyświetlona lista kodów stanu pamięci podręcznej bezpośrednio powyżej wykresu. Ta lista wskazuje poszczególnych kodów stanu, który można umieścić w wykres liniowy i bieżącą liczbę wystąpień na sekundę dla tego kodu stanu. Domyślnie dla każdego z tych kodów stanu na wykresie jest wyświetlany wiersza. Można jednak tylko monitorowanie kodów stanu, które mają specjalne znaczenie dla danej konfiguracji sieci CDN. Aby to zrobić, sprawdź kody żądany stan i wyczyść wszystkie inne opcje, a następnie kliknij **Odśwież wykres**. 

Można ukryć danych zarejestrowanych dla kodu określonego stanu.  W legendzie bezpośrednio pod wykresem kliknij kod stanu, który chcesz ukryć. Kod stanu będą natychmiast ukryte wykresu. Ponowne kliknięcie tego kodu stanu spowoduje, że ta opcja będzie wyświetlana ponownie.

## <a name="connections"></a>Połączenia
![Wykres połączeń](./media/cdn-real-time-stats/cdn-connections.png)

Ten wykres wskazuje liczbę połączeń ustanowionych do serwerów krawędzi. Każde żądanie dla zasobu, który przechodzi przez naszych wyników sieci CDN w połączeniu.

## <a name="next-steps"></a>Następne kroki
* Bądź na bieżąco z [alertów w czasie rzeczywistym w usłudze Azure CDN](cdn-real-time-alerts.md)
* Dig głębiej z [zaawansowane raporty HTTP](cdn-advanced-http-reports.md)
* Analizowanie [wzorców użycia](cdn-analyze-usage-patterns.md)

