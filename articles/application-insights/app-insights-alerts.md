---
title: Ustawianie alertów w usłudze Azure Application Insights | Dokumentacja firmy Microsoft
description: Otrzymywanie powiadomień o wydłużają czas odpowiedzi, wyjątki oraz inne wydajności lub użycia zmian w aplikacji sieci web.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.reviewer: lagayhar
ms.assetid: f8ebde72-f819-4ba5-afa2-31dbd49509a5
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: ac91632656e83fc4262f28240525c8fb106b99ec
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2018
ms.locfileid: "52679136"
---
# <a name="set-alerts-in-application-insights"></a>Ustawianie alertów w usłudze Application Insights
[Usługa Azure Application Insights] [ start] może generować alerty na zmiany w metrykach wydajności lub użycia aplikacji sieci web. 

Usługa Application Insights monitoruje aplikację na żywo na [różnymi platformami] [ platforms] ułatwiają diagnozowanie problemów z wydajnością i poznania wzorców użycia.

Istnieją trzy typy alertów:

* **Alerty metryki** informujące o tym, gdy Metryka przekracza wartość progową przez pewien — na przykład czasy odpowiedzi, liczby wyjątków, użycie procesora CPU lub wyświetlenia stron. 
* [**Testy sieci Web** ] [ availability] informujące o tym, gdy witryny jest niedostępna w Internecie lub odpowiada powoli. [Dowiedz się więcej][availability].
* [**Diagnostyka proaktywna** ](app-insights-proactive-diagnostics.md) są konfigurowane automatycznie Powiadomimy Cię o wydajności nietypowe wzorce.

Skupimy się na alertów metryk, w tym artykule.

## <a name="set-a-metric-alert"></a>Ustawianie alertu metryki
Otwórz blok reguł alertów, a następnie użyj przycisku Dodaj. 

![W bloku reguł alertów wybierz dodać Alert. Ustaw aplikację jako zasób do mierzenia, podaj nazwę alertu, a następnie wybierz metrykę.](./media/app-insights-alerts/01-set-metric.png)

* Ustaw zasób przed innymi właściwościami. **Wybierz zasób "(składniki)"** Jeśli chcesz ustawić alerty dotyczące metryk wydajności lub użycia.
* Nazwa która oferowanie alertu musi być unikatowa w obrębie grupy zasobów (nie tylko aplikację).
* Należy zachować ostrożność, należy pamiętać, jednostki, w których pojawi się prośba o podanie wartości progowej.
* Jeśli zaznaczysz pole "Adres E-mail właścicieli...", alerty są wysyłane za pośrednictwem poczty e-mail dla każdego, kto ma dostęp do tej grupy zasobów. Aby rozszerzyć ten zestaw osób, dodaj je do [grupy zasobów lub subskrypcji](app-insights-resources-roles-access-control.md) (nie zasobu).
* Jeśli określisz "Dodatkowe adresy e-mail", alerty są wysyłane do tych konkretnych osób lub grup (określa, czy zaznaczono pole "email właścicieli..."). 
* Ustaw [adres elementu webhook](../monitoring-and-diagnostics/insights-webhooks-alerts.md) Jeśli skonfigurujesz aplikację internetową, która odpowiada na alerty. Jest on nazywany zarówno po aktywowaniu alertu, jak i kiedy zostanie on rozwiązany. (Ale należy pamiętać, że obecnie parametry zapytania nie są przekazywane jako właściwości elementu webhook).
* Możesz wyłączyć lub włączyć alert: wyświetlanie przycisków w górnej części bloku.

*Przycisk Dodaj Alert nie jest widoczny.* 

* Czy używasz konta organizacyjnego Możesz ustawić alerty, jeśli masz dostęp do tego zasobu aplikacji współautora lub właściciela. Zapoznaj się z bloku kontroli dostępu. [Dowiedz się więcej na temat kontroli dostępu][roles].

> [!NOTE]
> W bloku alerty, zobaczysz, że istnieje już zestaw alertu: [Proactive Diagnostics](app-insights-proactive-failure-diagnostics.md). Alert automatycznego monitoruje jednego częstość niepowodzeń żądań metryki, określonej. Chyba że zdecydujesz się wyłączanie alertu aktywnego, nie trzeba ustawiać własne alert częstość niepowodzeń żądań. 
> 
> 

## <a name="see-your-alerts"></a>Wyświetlić alerty
Otrzymasz wiadomość e-mail, gdy stan alertu zmiany między nieaktywne i aktywne. 

Bieżący stan poszczególnych alertów jest wyświetlany w bloku reguł alertów.

Znajduje się podsumowanie, ostatnie działania w alertach listy rozwijanej:

![Alerty z listy rozwijanej](./media/app-insights-alerts/010-alert-drop.png)

Historię zmian stanu znajduje się w dzienniku aktywności:

![W bloku Przegląd kliknij pozycję Ustawienia, dzienniki inspekcji](./media/app-insights-alerts/09-alerts.png)

## <a name="how-alerts-work"></a>Jak działają alerty
* Alert ma trzy stany: "Nigdy nie aktywowano", "Aktywowano" i "Rozwiązany". Aktywowano oznacza, że określony warunek był ma wartość true, został ostatnio oceniane.
* Powiadomienie jest generowany, gdy zmieni się stan alertu. (Jeśli warunek alertu zostało już wartość true, podczas tworzenia alertu, można otrzymać powiadomienie dopóki warunek spadnie wartość false.)
* Każde powiadomienie generuje wiadomość e-mail, jeśli zaznaczono pole wiadomości e-mail lub podane adresy e-mail. Można również przeglądać listy powiadomień.
* Alert jest oceniany na każdym razem, gdy nadejdzie metrykę, a w przeciwnym przypadku nie.
* Ocena zagregowane metryki w okresie poprzedzającym i porównuje go z wartością progową, aby określić nowy stan.
* Okresu, w którym możesz wybrać, określa interwał, przez który metryki są agregowane. Nie wpływa na jak często alert jest oceniany:, zależy od częstotliwości przybycia metryki.
* Jeśli żadne dane dla konkretnej metryki przez pewien czas, przerwa ma różne efekty na ocenę alertu i na wykresach w Eksploratorze metryk. W Eksploratorze metryk Jeśli żadne dane nie są widoczne przez czas dłuższy niż interwał próbkowania dla wykresu, wykres pokazuje wartość 0. Ale alert w oparciu o metryki tej samej nie jest ponownie oceniane, i stan alertów pozostaje bez zmian. 
  
    Po odebraniu ostatecznie danych, wykres skacze z powrotem do zera. Ocenia alertu na podstawie danych, które są dostępne na okres określony. Jeśli nowy punkt danych jest tylko jeden w okresie, agregacji jest oparte tylko na punktu danych.
* Alert można zmieniał kolor często między Stanami alertów i działa prawidłowo, nawet wtedy, gdy ustawisz długiego okresu. Może to nastąpić, jeśli wartość metryki unosi się wokół wartości progowej. Istnieje nie histerezy przez wartość progową: przejście do alertu odbywa się na tę samą wartość jak przejście do dobrej kondycji.

## <a name="what-are-good-alerts-to-set"></a>Co to są dobre alerty można ustawić?
To zależy od aplikacji. Na początek z najlepiej jest nie chcesz ustawiać zbyt wiele metryk. Poświęcić trochę czasu, analizując wykresów metryki w uruchomionej aplikacji, aby uzyskać pewne pojęcie dla jak będzie pracował normalnie. Pomaga to znaleźć sposoby zwiększenia wydajności. Następnie skonfiguruj alerty informujące o tym, kiedy metryki wychodzenia poza normalne strefy. 

Popularne alerty zawierają:

* [Metryki przeglądarki][client], szczególnie przeglądarki **stronie czasów ładowania**, są odpowiednie dla aplikacji sieci web. Jeśli strona ma wiele skryptów, należy szukać **wyjątków przeglądarki**. Aby uzyskać te metryki i alerty, należy skonfigurować [monitorowania strony sieci web][client].
* **Czas odpowiedzi serwera** po stronie serwera aplikacji sieci web. A także Konfigurowanie alertów, Zwracaj uwagę na tę metrykę, aby zobaczyć, jeśli zależy to nieproporcjonalnie z kursów żądania wysokiej: odmiany może wskazywać, że Twoja aplikacja działa brakuje zasobów. 
* **Wyjątki serwera** — aby je zobaczy, należy wykonać niektóre [dodatkowe ustawienia](app-insights-asp-net-exceptions.md).

Nie należy zapominać, że [aktywna współczynnik Diagnostyka błędów](app-insights-proactive-failure-diagnostics.md) automatycznie monitorować współczynnik, w którym aplikacja ma odpowiadać na żądania z kodami błędów. 

## <a name="automation"></a>Automatyzacja
* [Automatyzowanie konfigurowania alertów za pomocą programu PowerShell](app-insights-powershell-alerts.md)
* [Używanie elementów webhook w celu zautomatyzowania reagowanie na alerty](../monitoring-and-diagnostics/insights-webhooks-alerts.md)

## <a name="video"></a>Połączenia wideo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="see-also"></a>Zobacz także
* [Testy sieci web dostępności](app-insights-monitor-web-app-availability.md)
* [Automatyzowanie konfigurowania alertów](app-insights-powershell-alerts.md)
* [Diagnostyka proaktywna](app-insights-proactive-diagnostics.md) 

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[platforms]: app-insights-platforms.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-overview.md

