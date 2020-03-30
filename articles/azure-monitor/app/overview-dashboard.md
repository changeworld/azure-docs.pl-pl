---
title: Pulpit nawigacyjny przeglądu usługi Azure Application Insights | Dokumenty firmy Microsoft
description: Monitoruj aplikacje za pomocą usługi Azure Application Insights i funkcji pulpitu nawigacyjnego przeglądu.
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: e5188972d9058b85a9765c7d33f6209b37245d7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669900"
---
# <a name="application-insights-overview-dashboard"></a>Pulpit nawigacyjny przeglądu usługi Application Insights

Usługa Application Insights zawsze udostępniała okienko przeglądu podsumowania, aby umożliwić szybką, szybką, szybką ocenę kondycji i wydajności aplikacji. Nowy pulpit nawigacyjny przeglądu zapewnia szybsze, bardziej elastyczne środowisko.

## <a name="how-do-i-test-out-the-new-experience"></a>Jak przetestować nowe doświadczenie?

Nowy pulpit nawigacyjny przeglądu jest teraz uruchamiany domyślnie:

![Okienko podglądu przeglądu](./media/overview-dashboard/overview.png)

## <a name="better-performance"></a>Lepsza wydajność

Wybór zakresu czasu został uproszczony do prostego interfejsu jednym kliknięciem.

![Przedział czasu](./media/overview-dashboard/app-insights-overview-dashboard-03.png)

Ogólna wydajność została znacznie zwiększona. Masz dostęp jednym kliknięciem do popularnych funkcji, takich jak **Wyszukiwanie** i **Analytics**. Każdy domyślny dynamiczny kafelek aktualizacji kluczowych wskaźników wydajności zapewnia wgląd w odpowiednie funkcje usługi Application Insights. Aby dowiedzieć się więcej o **nieudanych żądaniach,** wybierz błędy w nagłówku **Badanie:**

![Błędy](./media/overview-dashboard/app-insights-overview-dashboard-04.png)

## <a name="application-dashboard"></a>Pulpit nawigacyjny aplikacji

Pulpit nawigacyjny aplikacji wykorzystuje istniejącą technologię pulpitu nawigacyjnego na platformie Azure, aby zapewnić w pełni konfigurowalny widok pojedynczego okienka kondycji i wydajności aplikacji.

Aby uzyskać dostęp do domyślnego pulpitu nawigacyjnego, wybierz _pulpit nawigacyjny aplikacji_ w lewym górnym rogu.

![Widok pulpitu nawigacyjnego](./media/overview-dashboard/app-insights-overview-dashboard-05.png)

Jeśli jest to pierwszy dostęp do pulpitu nawigacyjnego, uruchomi widok domyślny:

![Widok pulpitu nawigacyjnego](./media/overview-dashboard/0001-dashboard.png)

Możesz zachować widok domyślny, jeśli ci się spodoba. Możesz też dodać i usunąć z pulpitu nawigacyjnego, aby jak najlepiej dopasować się do potrzeb zespołu.

> [!NOTE]
> Wszyscy użytkownicy z dostępem do zasobu usługi Application Insights współużytkują to samo środowisko pulpitu nawigacyjnego aplikacji. Zmiany wprowadzone przez jednego użytkownika zmodyfikują widok dla wszystkich użytkowników.

Aby wrócić do przeglądu, wystarczy wybrać:

![Przycisk Przegląd](./media/overview-dashboard/app-insights-overview-dashboard-07.png)

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli **wybierzesz pozycję Konfiguruj ustawienia kafli** i ustawisz niestandardowy zakres czasu przekraczający 31 dni, pulpit nawigacyjny nie będzie wyświetlany dłużej niż 31 dni danych, nawet przy domyślnym zatrzymywaniu danych przez 90 dni. Obecnie nie ma obejścia tego zachowania.

## <a name="next-steps"></a>Następne kroki

- [Lejki](../../azure-monitor/app/usage-funnels.md)
- [Przechowywanie](../../azure-monitor/app/usage-retention.md)
- [User Flows (Przepływy użytkowników)](../../azure-monitor/app/usage-flows.md)
