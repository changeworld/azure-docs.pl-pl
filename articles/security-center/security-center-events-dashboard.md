---
title: Monitorowanie i przetwarzanie zdarzeń zabezpieczeń w Azure Security Center | Microsoft Docs
description: Dowiedz się, jak można użyć pulpitu nawigacyjnego zdarzeń Security Center, aby zobaczyć zdarzenia zabezpieczeń z maszyn wirtualnych platformy Azure i komputerów spoza platformy Azure.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: rkarlin
ms.openlocfilehash: 389aaee621251890cd3f75744a94b9c9b29c5695
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68662396"
---
# <a name="monitoring-and-processing-security-events-in-azure-security-center"></a>Monitorowanie i przetwarzanie zdarzeń zabezpieczeń w Azure Security Center
Pulpit nawigacyjny zdarzeń zawiera przegląd liczby zdarzeń zabezpieczeń zebranych w czasie oraz listę istotnych zdarzeń, które mogą wymagać Twojej uwagi.  

> [!NOTE]
> Pulpit nawigacyjny zdarzeń zabezpieczeń został wycofany z 31 lipca 2019. Aby uzyskać więcej informacji i alternatywnych usług, zobacz wycofywanie [funkcji Security Center (lipiec 2019)](security-center-features-retirement-july2019.md#menu_events).

## <a name="what-is-a-security-event"></a>Co to jest zdarzenie zabezpieczeń?
Security Center używa Microsoft Monitoring Agent do zbierania różnych konfiguracji związanych z zabezpieczeniami i zdarzeń z maszyn i przechowuje te zdarzenia w obszarach roboczych. Przykładami takich danych są: Dzienniki systemu operacyjnego (dzienniki zdarzeń systemu Windows), uruchomione procesy i zdarzenia z rozwiązań zabezpieczeń zintegrowanych z Security Center. Program Microsoft Monitoring Agent kopiuje również pliki zrzutu awaryjnego do obszarów roboczych.

## <a name="requirements"></a>Wymagania
Aby można było korzystać z tej funkcji, w obszarze roboczym musi działać Log Analytics wersja 2 i być Security Center warstwy Standardowa. Aby uzyskać więcej informacji na temat warstwy Standardowa, zobacz [stronę](security-center-pricing.md) z cennikiem Security Center.

## <a name="events-processed-dashboard"></a>Pulpit nawigacyjny przetworzonych zdarzeń
Dostęp do pulpitu nawigacyjnego **zdarzenia** można uzyskać z menu głównego Security Center lub z bloku **Przegląd** Security Center.  

![Pulpit nawigacyjny przetworzonych zdarzeń][1]

Kafelek **zdarzenia** w obszarze **Security Center** zawiera liczbę zdarzeń przepływających do Security Center z maszyn wirtualnych platformy Azure i komputerów spoza platformy Azure.

**Pulpit nawigacyjny zdarzeń** zawiera przegląd liczby przetworzonych zdarzeń w nadgodzinach i listę zdarzeń.

 ![Pulpit nawigacyjny][2]

 Górna połowa pulpitu nawigacyjnego przedstawia wszystkie zdarzenia przetworzone w ostatnim tygodniu. Dolna połowa pulpitu nawigacyjnego zawiera listę istotnych zdarzeń i wszystkich zdarzeń według typu:

 - Istotne **zdarzenia** obejmują zapytania dotyczące zdarzeń, które Security Center udostępniają i zapytania dotyczące zdarzeń, które tworzysz i dodajesz. Pulpit nawigacyjny zapewnia również szybki wgląd w liczbę poszczególnych istotnych zdarzeń.
 - **Wszystkie zdarzenia według typu** pokazują typy zdarzeń, które są odbierane, oraz liczbę dla każdego typu. Przykłady typu zdarzenia to SecurityEvent, CommonSecurityLog, WindowsFirewall i W3CIISLog.

> [!NOTE]
> Istotne zdarzenia obejmują [ocenę linii bazowej sieci Web](https://docs.microsoft.com/azure/operations-management-suite/oms-security-web-baseline-assessment). Ocena internetowej linii bazowej ma na celu znalezienie ustawień serwera internetowego, które mogą potencjalnie powodować zagrożenie.

## <a name="view-processed-event-details"></a>Wyświetl szczegóły przetworzonych zdarzeń
1. W menu głównym **Security Center** wybierz pozycję **zdarzenia**.
2. Może otworzyć selektor obszarów roboczych **pulpitu nawigacyjnego zdarzeń** . Jeśli masz tylko jeden obszar roboczy, ten selektor obszaru roboczego nie zostanie wyświetlony. Jeśli masz więcej niż jeden obszar roboczy, musisz wybrać obszar roboczy, aby wyświetlić jego szczegóły przetworzonych zdarzeń. Wybierz obszar roboczy z listy, jeśli masz więcej niż jeden obszar roboczy.

   ![Lista obszarów roboczych][3]

3. Zostanie otwarty **pulpit nawigacyjny zdarzeń** pokazujący szczegóły zdarzenia dla wybranego obszaru roboczego. Można wyświetlić istotne zdarzenia i wszystkie zdarzenia według typu.  W tym przykładzie zostały wybrane istotne **zdarzenia**.

   ![Istotne zdarzenie][4]

4. Możesz wykonać zapytanie o więcej danych w obszarze roboczym, wybierając typ zdarzenia. W tym przykładzie wybrano **SecurityEvent**.

   ![Wybieranie typu zdarzenia][5]

5. Zostanie otwarte **Wyszukiwanie w dzienniku** z dodatkowymi szczegółami dotyczącymi typu zdarzenia.

   ![Wyszukiwanie w dzienniku][6]

## <a name="add-a-notable-event"></a>Dodawanie istotnych zdarzeń
Security Center zapewnia nieaktualne zdarzenia. Można dodawać istotne zdarzenia na podstawie własnych zapytań przy użyciu [języka zapytań Kusto](../log-analytics/log-analytics-search-reference.md). Powrócimy do **pulpitu nawigacyjnego zdarzenia** w celu dodania istotnego zdarzenia.

1. Wybierz pozycję **Dodaj istotne zdarzenie**.

   ![Dodawanie istotnych zdarzeń][7]

2. Zostanie otwarte **niestandardowe zdarzenie** , które zostanie dodane.  W polu **Nazwa wyświetlana**wprowadź nazwę dla istotnego zdarzenia. W obszarze **zapytanie wyszukiwania**wprowadź zapytanie dotyczące zdarzenia.

   ![Wprowadź zapytanie][8]

4. Kliknij przycisk **OK**.

## <a name="update-your-workspace-for-events-processing"></a>Aktualizowanie obszaru roboczego na potrzeby przetwarzania zdarzeń
Aby można było korzystać z przetwarzania zdarzeń w Security Center, w obszarze roboczym musi działać Log Analytics wersja 2 i mieć Security Center warstwy Standardowa. Selektor obszarów roboczych **pulpitów nawigacyjnych zdarzeń** identyfikuje obszary robocze, które nie spełniają tych wymagań.

![Obszar roboczy nie spełnia wymagań][9]

Jeśli wiersz obszaru roboczego:

- Zawiera **wymaganie aktualizacji** — należy zaktualizować obszar roboczy do log Analytics wersji 2
- Zawiera **plan uaktualniania** — należy uaktualnić obszar roboczy do warstwy Standardowa Security Center
- Jest pusty — obszar roboczy spełnia wymagania i wybranie obszaru roboczego przenosi do pulpitu nawigacyjnego

> [!NOTE]
> W obszarze **pulpit nawigacyjny zdarzeń**kolumna **zdarzenia** wskazuje ilość zdarzeń w każdym obszarze roboczym.  Ta kolumna jest pusta dla niektórych obszarów roboczych, ponieważ do tego obszaru roboczego jest stosowana bezpłatna warstwa Security Center. W ramach warstwy Bezpłatna Security Center będą zbierać zdarzenia, ale zdarzenia nie są zapisywane w dziennikach Azure Monitor i nie są dostępne na pulpicie nawigacyjnym.
>
>

## <a name="update-workspace-to-log-analytics-version-2"></a>Aktualizowanie obszaru roboczego do Log Analytics wersji 2
1. Wybierz obszar roboczy, który **wymaga aktualizacji**.
2. Zostanie otwarte **uaktualnienie wyszukiwania** . Wybierz pozycję **Uaktualnij teraz**.

   ![Uaktualnij teraz][10]

## <a name="upgrade-to-security-centers-standard-tier"></a>Uaktualnij do warstwy Standardowa Security Center
1. Wybierz obszar roboczy z **planem uaktualniania**.
2. Zostanie otwarty **pulpit nawigacyjny zdarzeń** . Wybierz pozycję **Wypróbuj pulpit nawigacyjny zdarzeń**.

   ![Wypróbuj pulpit nawigacyjny][11]

3. W obszarze dołączanie **do zaawansowanych zabezpieczeń**wybierz obszar roboczy, który chcesz uaktualnić.
4. W obszarze **Cennik**wybierz pozycję **standardowa**.
5. Wybierz pozycję **Zapisz**.

   ![Uaktualnij do warstwy Standardowa][12]

## <a name="next-steps"></a>Następne kroki
W tym artykule przedstawiono sposób użycia pulpitu nawigacyjnego zdarzeń Security Center. Aby dowiedzieć się więcej o tym, jak działa Pulpit nawigacyjny i napisać własne zapytania dotyczące zdarzeń, zobacz:

- [Co to jest Azure Monitor Logs?](../log-analytics/log-analytics-overview.md) — Omówienie dzienników Azure Monitor
- [Opis przeszukiwania dzienników w programie Kusto](../log-analytics/log-analytics-log-search-new.md) — opis sposobu używania wyszukiwania w dziennikach w dziennikach Azure monitor i przedstawiono pojęcia, które należy zrozumieć przed utworzeniem przeszukiwania dzienników
- [Odwołanie wyszukiwania Kusto](../log-analytics/log-analytics-search-reference.md) — informacje o sposobie pisania własnych zapytań dotyczących zdarzeń przy użyciu języka zapytań w dzienniku

Aby dowiedzieć się więcej na temat usługi Security Center, zobacz:

- [Omówienie Security Center](security-center-intro.md) — opisuje kluczowe możliwości Security Center

<!--Image references-->
[1]: ./media/security-center-events-dashboard/events-processed.png
[2]: ./media/security-center-events-dashboard/dashboard.png
[3]: ./media/security-center-events-dashboard/view-processed-event.png
[4]: ./media/security-center-events-dashboard/notable-event.png
[5]: ./media/security-center-events-dashboard/events-by-type.png
[6]: ./media/security-center-events-dashboard/log-search-detail.png
[7]: ./media/security-center-events-dashboard/add-notable-event.png
[8]: ./media/security-center-events-dashboard/create-query.png
[9]: ./media/security-center-events-dashboard/requires-update.png
[10]: ./media/security-center-events-dashboard/search-upgrade.png
[11]: ./media/security-center-events-dashboard/try-dashboard.png
[12]: ./media/security-center-events-dashboard/onboard-workspace.png
