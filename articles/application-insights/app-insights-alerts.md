---
title: Ustawianie alertów w usłudze Azure Application Insights | Dokumentacja firmy Microsoft
description: Bądź na bieżąco o wydłużają czas odpowiedzi, wyjątków i innych wydajności lub zmiany użycia w aplikacji sieci web.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: f8ebde72-f819-4ba5-afa2-31dbd49509a5
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: 87be1a48a6c3320187243e549a8fb8e5ecc9e006
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35293607"
---
# <a name="set-alerts-in-application-insights"></a>Ustawianie alertów w usłudze Application Insights
[Azure Application Insights] [ start] alertów na zmiany w metryki wydajności i użycia w aplikacji sieci web. 

Usługa Application Insights monitoruje aplikację na żywo na [różnymi platformami] [ platforms] ułatwiających diagnozowanie problemów z wydajnością i poznania wzorców użycia.

Istnieją trzy typy alertów:

* **Alerty metryki** informujące o tym, kiedy metrykę przecina wartość progową dla niektórych okres — takie jak czas reakcji, liczby wyjątków, użycie procesora CPU lub wyświetleń strony. 
* [**Testów sieci Web** ] [ availability] informujące o tym, kiedy ta lokacja jest niedostępny w Internecie lub odpowiada powoli. [Dowiedz się więcej][availability].
* [**Proaktywna Diagnostyka** ](app-insights-proactive-diagnostics.md) zostaną skonfigurowane automatycznie Pragniemy poinformować Cię o wydajności nietypowe wzorce.

Możemy skupić się na metryki alertów w tym artykule.

## <a name="set-a-metric-alert"></a>Ustaw metryki alertu
Otwarcie bloku reguły alertów, a następnie za pomocą przycisku Dodaj. 

![W bloku reguły alertów wybierz opcję Dodaj alertu. Ustaw aplikację jako zasób do mierzenia, podaj nazwę alertu i wybierz metrykę.](./media/app-insights-alerts/01-set-metric.png)

* Ustaw zasób przed inne właściwości. **Wybierz zasób "(składniki)"** Aby ustawić alertów dla metryki wydajności i użycia.
* Nazwa nadana alert musi być unikatowa w obrębie grupy zasobów (nie tylko aplikacji).
* Należy zachować ostrożność, należy pamiętać, jednostki, w których użytkownik jest proszony o wprowadź wartość progu.
* Po zaznaczeniu pola "E-mail właścicieli...", alerty są wysyłane pocztą e-mail do każdego, kto ma dostęp do tej grupy zasobów. Aby rozszerzyć ten zestaw osoby, dodaj je do [grupy zasobów lub subskrypcji](app-insights-resources-roles-access-control.md) (nie zasób).
* Jeśli określisz "Dodatkowe wiadomości e-mail" alerty są wysyłane do tych osób lub grup (czy zaznaczono pole "wiadomości e-mail właścicieli..."). 
* Ustaw [adresu elementu webhook](../monitoring-and-diagnostics/insights-webhooks-alerts.md) Jeśli zdefiniowano aplikacji sieci web, która odpowiada na alerty. Jest to zarówno gdy alert jest aktywny, jak i po jej usunięciu. (Ale należy pamiętać, że w chwili obecnej, parametry zapytania nie są przekazywane jako właściwości elementu webhook).
* Można wyłączyć lub włączyć alert: wyświetlanie przycisków w górnej części bloku.

*Przycisk Dodaj alertu nie jest widoczny.* 

* Używasz konta organizacyjnego? Można ustawić alerty, jeśli masz właścicielem lub współautorem dostęp do tego zasobu aplikacji. Spójrz na blok kontroli dostępu. [Więcej informacji na temat kontroli dostępu][roles].

> [!NOTE]
> W bloku alerty widać, że istnieje już zestaw alertu: [proaktywna Diagnostyka](app-insights-proactive-failure-diagnostics.md). Alert automatycznego monitoruje jednego określonego metryki, częstość niepowodzeń. Jeśli zdecydujesz się Wyłącz alert aktywny, nie trzeba ustawiać własne alert częstość niepowodzeń żądań. 
> 
> 

## <a name="see-your-alerts"></a>Zobacz alerty
Otrzymasz wiadomość e-mail, gdy stan alertu zmiany między stanem nieaktywnym i aktywnym. 

Bieżący stan poszczególnych alertów jest wyświetlany w bloku reguł alertów.

Brak podsumowanie ostatnią aktywność w alertach listy rozwijanej:

![Alerty z listy rozwijanej](./media/app-insights-alerts/010-alert-drop.png)

Historię zmian stanu znajduje się w dzienniku aktywności:

![W bloku Przegląd kliknij pozycję Ustawienia, dzienniki inspekcji](./media/app-insights-alerts/09-alerts.png)

## <a name="how-alerts-work"></a>Jak działają alerty
* Alert ma trzy stany: "Nigdy aktywowany", "Aktywny" i "Rozwiązany". Aktywowany oznacza, że określony warunek był ma wartość true, ostatniej oceny.
* Powiadomienie jest generowany, gdy alert zmieni stan. (Jeśli stan alertu został już wartość true, podczas tworzenia alertu, można otrzymać powiadomienie do momentu warunek przechodzi false.)
* Każde powiadomienie generuje wiadomość e-mail, jeśli zaznaczono pole wiadomości e-mail lub podane adresy e-mail. Można też przyjrzeć się na liście rozwijanej powiadomienia.
* Alert jest obliczane zawsze, gdy dociera metrykę, ale nie w inny sposób.
* Obliczanie agreguje Metryka w poprzednim okresie i porównywany do progu, aby określić nowy stan.
* Okresu, w którym możesz wybrać Określa interwał, w którym są agregowane metryki. Nie wpływa na częstotliwość oceny alertu: to zależy od częstotliwości odbioru metryki.
* Jeśli żadne dane dla określonej metryki przez pewien czas, przerwa ma efekty różnych na ocenę alertów i na wykresach w Eksploratorze metryk. W Eksploratorze metryk Jeśli występuje bez danych przez czas dłuższy niż interwał próbkowania wykresu, wykres zawiera wartość 0. Ale alertu opartego na tej samej Metryka nie jest ponownie oceniane, i stan alertów pozostaje niezmieniona. 
  
    Po odebraniu ostatecznie danych, wykres Przechodzi wstecz na wartość inną niż zero. Oblicza alertu na podstawie danych dostępnych w okresie określona. Jeśli nowy punkt danych jest tylko jeden dostępny w okresie, agregacji jest oparty tylko na punktu danych.
* Alert można często migotać między Stanami alertów i działa prawidłowo, nawet w przypadku ustawienia długiego okresu. Może to nastąpić, jeśli wartość metryki znajduje się wokół wartość progową. Brak nie histerezy przez wartość progową: przejście do alertu odbywa się na taką samą wartość jak przejście do dobrej kondycji.

## <a name="what-are-good-alerts-to-set"></a>Co to są dobrym alerty można ustawić?
To zależy od aplikacji. Do uruchomienia z najlepiej nie ustawiono zbyt wiele miar. Należy poświęcić trochę czasu spojrzenie na wykresach metryki uruchomionej aplikacji, aby uzyskać pewne pojęcie o jak działa normalnie. Takie rozwiązanie pomaga znaleźć sposób, aby zwiększyć jej wydajność. Następnie skonfiguruj alerty informujące o tym, kiedy metryki wykraczać poza normalne strefy. 

Alerty popularnych obejmują:

* [Metryki przeglądarki][client], szczególnie przeglądarki **czas ładowania strony**, są odpowiednie dla aplikacji sieci web. Jeśli Twoja strona zawiera wiele skryptów, należy szukać **wyjątki przeglądarki**. Aby uzyskać te metryki i alerty, musisz skonfigurować [monitorowania strony sieci web][client].
* **Czas odpowiedzi serwera** dla aplikacji sieci web po stronie serwera. A także Konfigurowanie alertów, śledzić tej metryki, aby zobaczyć, czy nieproporcjonalnie zależy szybkości przetwarzania żądań wysokiej: odmiany może wskazywać, że aplikacja działa poza zasobów. 
* **Wyjątki serwera** — do zapoznania się z nimi, musisz wykonać niektóre [dodatkowe ustawienia](app-insights-asp-net-exceptions.md).

Pamiętaj, że [diagnostyki szybkość niepowodzenia aktywnego](app-insights-proactive-failure-diagnostics.md) automatycznie monitorowanie szybkości, jaką aplikacji odpowiada na żądania z kodami awarii. 

## <a name="automation"></a>Automatyzacja
* [Za pomocą programu PowerShell można zautomatyzować konfigurowanie alertów](app-insights-powershell-alerts.md)
* [Można zautomatyzować, reagowanie na alerty za pomocą elementów webhook](../monitoring-and-diagnostics/insights-webhooks-alerts.md)

## <a name="video"></a>Połączenia wideo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="see-also"></a>Zobacz także
* [Dostępność testy sieci web](app-insights-monitor-web-app-availability.md)
* [Zautomatyzować konfigurowanie alertów](app-insights-powershell-alerts.md)
* [Proaktywna Diagnostyka](app-insights-proactive-diagnostics.md) 

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[platforms]: app-insights-platforms.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-overview.md

