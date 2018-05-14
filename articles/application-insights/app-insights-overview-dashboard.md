---
title: Pulpit nawigacyjny aplikacji Insights omówienie | Dokumentacja firmy Microsoft
description: Monitorowanie aplikacji z funkcjami usługi Azure Application Insights i pulpitu nawigacyjnego przeglądu.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: mbullwin
ms.openlocfilehash: bccb56ad45d9054a437bf2d85e74a8d81fbc3db1
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2018
---
# <a name="overview-dashboard-preview"></a>Przegląd pulpit nawigacyjny (wersja zapoznawcza)

Usługa Application Insights zawsze udostępnił okienko Podsumowanie, aby umożliwić szybkie, w skrócie ocenę kondycji i wydajności aplikacji. Uruchamianie na 15 maja 2018 nowe, szybsze, bardziej elastyczne środowisko zostaną wydane jako podgląd. Na 29 maja 2018 środowiska klasycznego omówienie zostaną wycofane.

## <a name="how-do-i-test-out-the-new-experience"></a>Jak przetestować nowe środowisko

Od 15 maja nowe środowisko rozpocznie się pojawiają się w usłudze Application Insights w obszarze: _zbadaj_ > _(wersja zapoznawcza) — omówienie_.

![Omówienie podglądu](.\media\app-insights-overview-dashboard\01.png)

Spowoduje to uruchomienie nowego pulpitu nawigacyjnego przeglądu domyślne:

![Okienko podglądu — omówienie](.\media\app-insights-overview-dashboard\02.png)

## <a name="better-performance"></a>Lepszą wydajność

Prosty interfejs jednym kliknięciem uproszczonej wybranego zakresu czasu.

![Przedział czasu](.\media\app-insights-overview-dashboard\04.png)

Ogólna wydajność znacząco zwiększono. Każdy domyślne dynamiczne aktualizowanie kafelka kluczowego wskaźnika wydajności jest połączony z odpowiedniej funkcji usługi Application Insights. Na przykład uruchamiają zaznaczając żądań zakończonych niepowodzeniem _błędów_ okienka:

![Awarie](.\media\app-insights-overview-dashboard\03.png)

## <a name="application-dashboard"></a>Pulpit nawigacyjny aplikacji

Pulpit nawigacyjny aplikacji korzysta z istniejących technologii pulpit nawigacyjny w systemie Azure w celu zapewnienia przeglądu można swobodnie dostosowywać jednego okienka aplikacji kondycji i wydajności.

Wybierz pulpit nawigacyjny domyślnego dostępu do _pulpit nawigacyjny aplikacji_ w lewym górnym rogu.

![Widok pulpitu nawigacyjnego](.\media\app-insights-overview-dashboard\0009.png)

Jeśli jest to pierwszy raz podczas uzyskiwania dostępu do pulpitu nawigacyjnego uruchamia widok domyślny:

![Widok pulpitu nawigacyjnego](.\media\app-insights-overview-dashboard\06.png)

Podczas mogą zachować widok domyślny, jeśli chcesz tak, można również dodać, a następnie usuń z pulpitu nawigacyjnego, aby jak najlepiej zgodnie z potrzebami zespołu.

Aby wrócić do obsługi omówienie po prostu wybierz:

![Przycisk — omówienie](.\media\app-insights-overview-dashboard\07.png)

Istnieje również nowy przycisk o nazwie _części kodu Pin_.

![Przycisk — omówienie](.\media\app-insights-overview-dashboard\008.png)

To replikuje nieco znanych funkcji z klasycznym przeglądu, która pozwala się Kafelki ze starego środowisko omówienie _(alerty, dostępności, metryki na żywo, użycie, aktywne wykryć i mapowanie aplikacji)_ i Dodaj je do niestandardowych pulpitów nawigacyjnych. 

W przypadku domyślnie _pulpit nawigacyjny aplikacji_ tych kafelków zostało już dodane. Ale jeśli tworzenie dodatkowych niestandardowych pulpitów nawigacyjnych lub w zespole usuwanie klasycznego kafelka i chcesz dodać ją ponownie, _części kodu Pin_ zawiera te funkcje w żadnym łatwe do odnalezienia miejsca.
