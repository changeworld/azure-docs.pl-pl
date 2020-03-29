---
title: Statystyki w czasie rzeczywistym w usłudze Azure CDN | Dokumenty firmy Microsoft
description: Statystyki w czasie rzeczywistym zapewniają w czasie rzeczywistym dane dotyczące wydajności usługi Azure CDN podczas dostarczania zawartości klientom.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: c7989340-1172-4315-acbb-186ba34dd52a
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: d56007e5a196a0857f3b69ac51f5e3b5a88c4f6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593489"
---
# <a name="real-time-stats-in-microsoft-azure-cdn"></a>Statystyki w czasie rzeczywistym w sieci CDN platformy Microsoft Azure
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Omówienie
W tym dokumencie opisano statystyki w czasie rzeczywistym w sieci CDN platformy Microsoft Azure.  Ta funkcja zapewnia dane w czasie rzeczywistym, takie jak przepustowość, stany pamięci podręcznej i równoczesne połączenia z profilem sieci CDN podczas dostarczania zawartości klientom. Umożliwia to ciągłe monitorowanie kondycji usługi w dowolnym momencie, w tym wydarzeń na żywo.

Dostępne są następujące wykresy:

* [Przepustowość](#bandwidth)
* [Kody stanu](#status-codes)
* [Stany pamięci podręcznej](#cache-statuses)
* [Połączenia](#connections)

## <a name="accessing-real-time-stats"></a>Uzyskiwanie dostępu do statystyk w czasie rzeczywistym
1. W witrynie [Azure Portal](https://portal.azure.com)przejdź do swojego profilu usługi CDN.
   
    ![Ostrze profilu CDN](./media/cdn-real-time-stats/cdn-profile-blade.png)
2. W bloku profilu sieci CDN kliknij przycisk **Zarządzaj.**
   
    ![Przycisk zarządzania łatami profilu CDN](./media/cdn-real-time-stats/cdn-manage-btn.png)
   
    Zostanie otwarty portal zarządzania siecią CDN.
3. Umieść wskaźnik myszy na karcie **Analytics,** a następnie umieść wskaźnik myszy na **wysu wysu wysu** w czasie rzeczywistym.  Kliknij **duży obiekt HTTP**.
   
    ![Portal zarządzania siecią CDN](./media/cdn-real-time-stats/cdn-premium-portal.png)
   
    Wyświetlane są wykresy statystyk w czasie rzeczywistym.

Każdy z wykresów wyświetla statystyki czasu rzeczywistego dla wybranego przedziału czasu, począwszy od momentu załadowania strony.  Wykresy są aktualizowane automatycznie co kilka sekund.  Przycisk **Odśwież wykres,** jeśli jest obecny, wyczyści wykres, po czym wyświetli tylko wybrane dane.

## <a name="bandwidth"></a>Przepustowość
![Wykres przepustowości](./media/cdn-real-time-stats/cdn-bandwidth.png)

Wykres **Przepustowość** wyświetla przepustowość używaną dla bieżącej platformy w wybranym przedziale czasu. Zacieniowana część wykresu wskazuje użycie przepustowości. Dokładna ilość aktualnie używanej przepustowości jest wyświetlana bezpośrednio pod wykresem liniowym.

## <a name="status-codes"></a>Kody stanu
![Wykres kodu stanu](./media/cdn-real-time-stats/cdn-status-codes.png)

Wykres **Kody stanu** wskazuje, jak często niektóre kody odpowiedzi HTTP występują w wybranym przedziale czasu.

> [!TIP]
> Aby uzyskać opis każdej opcji kodu stanu HTTP, zobacz [Kody stanu HTTP usługi Azure CDN](/previous-versions/azure/mt759238(v=azure.100)).
> 
> 

Lista kodów stanu HTTP jest wyświetlana bezpośrednio nad wykresem. Ta lista wskazuje każdy kod stanu, który może być uwzględniony na wykresie liniowym i bieżącą liczbę wystąpień na sekundę dla tego kodu stanu. Domyślnie wiersz jest wyświetlany dla każdego z tych kodów stanu na wykresie. Można jednak monitorować tylko kody stanu, które mają szczególne znaczenie dla konfiguracji sieci CDN. Aby to zrobić, sprawdź żądane kody stanu i wyczyść wszystkie inne opcje, a następnie kliknij przycisk **Odśwież wykres**. 

Można tymczasowo ukryć zarejestrowane dane dla określonego kodu stanu.  Z legendy bezpośrednio pod wykresem kliknij kod stanu, który chcesz ukryć. Kod stanu zostanie natychmiast ukryty na wykresie. Ponowne kliknięcie tego kodu stanu spowoduje ponowne wyświetlenie tej opcji.

## <a name="cache-statuses"></a>Stany pamięci podręcznej
![Wykres stanów pamięci podręcznej](./media/cdn-real-time-stats/cdn-cache-status.png)

Wykres **Stany pamięci podręcznej** wskazuje, jak często niektóre typy stanów pamięci podręcznej występują w wybranym przedziale czasu. 

> [!TIP]
> Aby uzyskać opis każdej opcji kodu stanu pamięci podręcznej, zobacz [Kody stanu pamięci podręcznej cdn platformy Azure](/previous-versions/azure/mt759237(v=azure.100)).
> 
> 

Lista kodów stanu pamięci podręcznej jest wyświetlana bezpośrednio nad wykresem. Ta lista wskazuje każdy kod stanu, który może być uwzględniony na wykresie liniowym i bieżącą liczbę wystąpień na sekundę dla tego kodu stanu. Domyślnie wiersz jest wyświetlany dla każdego z tych kodów stanu na wykresie. Można jednak monitorować tylko kody stanu, które mają szczególne znaczenie dla konfiguracji sieci CDN. Aby to zrobić, sprawdź żądane kody stanu i wyczyść wszystkie inne opcje, a następnie kliknij przycisk **Odśwież wykres**. 

Można tymczasowo ukryć zarejestrowane dane dla określonego kodu stanu.  Z legendy bezpośrednio pod wykresem kliknij kod stanu, który chcesz ukryć. Kod stanu zostanie natychmiast ukryty na wykresie. Ponowne kliknięcie tego kodu stanu spowoduje ponowne wyświetlenie tej opcji.

## <a name="connections"></a>Połączenia
![Wykres połączeń](./media/cdn-real-time-stats/cdn-connections.png)

Ten wykres wskazuje, ile połączeń zostało nawiązanych do serwerów brzegowych. Każde żądanie zasobu, który przechodzi przez naszą sieć CDN, powoduje połączenie.

## <a name="next-steps"></a>Następne kroki
* Otrzymuj powiadomienia o [alertach w czasie rzeczywistym w usłudze Azure CDN](cdn-real-time-alerts.md)
* Kopiuj głębiej dzięki [zaawansowanym raportom HTTP](cdn-advanced-http-reports.md)
* Analizowanie [wzorców użycia](cdn-analyze-usage-patterns.md)

