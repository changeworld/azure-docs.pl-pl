---
title: Pulpit nawigacyjny usługi Azure Application Insights przeglądu | Dokumentacja firmy Microsoft
description: Monitorowanie aplikacji za pomocą funkcji usługi Azure Application Insights i pulpit nawigacyjny przeglądu.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: mbullwin
ms.openlocfilehash: d1823779f8a8070149811e2349fc9f4281072d38
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66497164"
---
# <a name="application-insights-overview-dashboard"></a>Pulpit nawigacyjny aplikacji — omówienie szczegółowe informacje

Usługa Application Insights zawsze oferowała okienko Przegląd podsumowania, aby umożliwić szybkie, w skrócie ocenę kondycji i wydajności aplikacji. Nowy pulpit nawigacyjny przeglądu zapewnia szybsze, bardziej elastyczne środowisko.

## <a name="how-do-i-test-out-the-new-experience"></a>Jak przetestowania nowego środowiska

Nowy pulpit nawigacyjny przeglądu teraz uruchamia się domyślnie:

![Okienko podglądu — omówienie](./media/overview-dashboard/overview.png)

## <a name="better-performance"></a>Lepsza wydajność

Wybór zakresu czasu został uproszczony, aby za pomocą prostego interfejsu jednym kliknięciem.

![Przedział czasu](./media/overview-dashboard/app-insights-overview-dashboard-03.png)

Ogólna wydajność została znacznie zwiększona. Masz jednym kliknięciem dostęp do popularnych funkcji, takich jak **wyszukiwania** i **analizy**. Każda domyślna dynamiczne aktualizowanie kafelka kluczowy wskaźnik wydajności zapewnia wgląd w odpowiedniej funkcji usługi Application Insights. Aby dowiedzieć się więcej na temat nie powiodło się wybierz opcję żądania **błędów** w obszarze **zbadaj** nagłówka:

![Błędy](./media/overview-dashboard/app-insights-overview-dashboard-04.png)

## <a name="application-dashboard"></a>Pulpit nawigacyjny aplikacji

Pulpit nawigacyjny aplikacji korzysta z istniejących technologii pulpitu nawigacyjnego w obrębie platformy Azure, aby przedstawić widok można swobodnie dostosowywać jedną taflę wydajności i kondycji aplikacji.

Wybierz pulpit nawigacyjny domyślnego dostępu do _pulpit nawigacyjny aplikacji_ w lewym górnym rogu.

![Widok pulpitu nawigacyjnego](./media/overview-dashboard/app-insights-overview-dashboard-05.png)

Jeśli jest to dostęp do pulpitu nawigacyjnego po raz pierwszy, program uruchamia domyślny widok:

![Widok pulpitu nawigacyjnego](./media/overview-dashboard/0001-dashboard.png)

Możesz zachować widok domyślny, jeśli chcesz go. Lub można również dodawać i usuwać z poziomu pulpitu nawigacyjnego w sposób najlepiej odpowiadający potrzeb danego zespołu.

> [!NOTE]
> Wszyscy użytkownicy z dostępem do zasobu usługi Application Insights udostępnianie tego samego środowiska pulpitu nawigacyjnego aplikacji. Zmiany wprowadzone przez jeden użytkownik zmodyfikuje widoku dla wszystkich użytkowników.

Aby przejść z powrotem do środowiska — omówienie, po prostu wybierz opcję:

![Przycisk — omówienie](./media/overview-dashboard/app-insights-overview-dashboard-07.png)

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli wybierzesz **Konfigurowanie ustawień kafelków** i Ustaw niestandardowy zakres czasu przekraczające 31 dni pulpitu nawigacyjnego nie będą wyświetlane poza 31 dni dla danych, nawet w przypadku przechowywania danych domyślne 90 dni. Obecnie nie ma sposobu obejścia tego zachowania.

## <a name="next-steps"></a>Kolejne kroki

- [Lejki](../../azure-monitor/app/usage-funnels.md)
- [Przechowywanie](../../azure-monitor/app/usage-retention.md)
- [User Flows (Przepływy użytkowników)](../../azure-monitor/app/usage-flows.md)