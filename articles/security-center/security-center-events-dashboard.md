---
title: Monitorowanie i przetwarzania zdarzeń zabezpieczeń w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: Dowiedz się, jak umożliwia pulpit nawigacyjny zdarzeń Centrum zabezpieczeń wyświetlane zdarzenia zabezpieczeń z maszyn wirtualnych platformy Azure i komputerów spoza platformy Azure.
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
ms.openlocfilehash: bc0fd83bd45e7c5c671b387d124cdddc75244ade
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64573509"
---
# <a name="monitoring-and-processing-security-events-in-azure-security-center"></a>Monitorowanie i przetwarzania zdarzeń zabezpieczeń w usłudze Azure Security Center
Pulpit nawigacyjny zdarzeń zawiera omówienie liczby zdarzeń związanych z zabezpieczeniami, zbierane wraz z upływem czasu i listę istotnych zdarzeń, które mogą wymagać Twojej uwagi.  

> [!NOTE]
> Pulpit nawigacyjny zdarzeń zabezpieczeń zostaną wycofane z dniem 31 lipca 2019 r. Aby uzyskać więcej informacji i alternatywne usług zobacz [wycofywania Centrum zabezpieczeń funkcji (2019 lipca)](security-center-features-retirement-july2019.md#menu_events).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="what-is-a-security-event"></a>Co to jest zdarzenie związane z zabezpieczeniami?
Usługa Security Center używa programu Microsoft Monitoring Agent do gromadzenia zabezpieczeń różnych konfiguracji związanych z i zdarzeń z maszyn i przechowuje te zdarzenia w obszarach roboczych usługi. Przykłady takich danych to: operacyjne dzienniki systemu (Windows dzienniki zdarzeń), uruchomione procesy i zdarzeń z rozwiązania w zakresie zabezpieczeń zintegrowanych z usługą Security Center. Program Microsoft Monitoring Agent kopiuje również pliki zrzutu awaryjnego do obszarów roboczych.

## <a name="requirements"></a>Wymagania
Aby użyć tej funkcji, obszar roboczy musi być uruchomiona usługa Log Analytics w wersji 2 i na warstwy standardowa usługi Security Center. Odwiedź Centrum zabezpieczeń [stronę z cennikiem](security-center-pricing.md) Aby uzyskać więcej informacji na temat warstwy standardowa.

## <a name="events-processed-dashboard"></a>Pulpit nawigacyjny zdarzeń przetworzone
Możesz uzyskać dostęp do **zdarzenia** pulpitu nawigacyjnego z poziomu menu głównego usługi Security Center lub usługa Security Center **Przegląd** bloku.  

![Pulpit nawigacyjny zdarzeń przetworzone][1]

**Zdarzenia** kafelka w obszarze **usługi Security Center** Wyświetla liczbę zdarzeń otrzymywanych przez Centrum zabezpieczeń z maszyn wirtualnych platformy Azure i komputerów spoza platformy Azure.

**Pulpit nawigacyjny zdarzeń** omówiono liczbę nadgodzinach przetworzonych zdarzeń i Lista zdarzeń.

 ![Pulpit nawigacyjny][2]

 W górnej połowie pulpitu nawigacyjnego trendy wszystkie zdarzenia przetwarzane w ciągu ostatniego tygodnia. Dolnej części pulpitu nawigacyjnego, który zawiera listę istotnych zdarzeń i wszystkie zdarzenia według typu:

 - **Istotne zdarzenia** obejmują zapytania dotyczące zdarzeń, które zapewnia usługa Security Center i zapytania dotyczące zdarzeń, które tworzysz i dodajesz. Pulpit nawigacyjny także szybki wgląd w liczbę każdego istotnego zdarzenia.
 - **Wszystkie zdarzenia według typu** przedstawia typy zdarzeń, które są odbierane oraz liczność dla każdego typu. Przykłady typ zdarzenia to SecurityEvent, CommonSecurityLog, WindowsFirewall i W3CIISLog.

> [!NOTE]
> Istotne zdarzenia obejmują [ocena internetowej](https://docs.microsoft.com/azure/operations-management-suite/oms-security-web-baseline-assessment). Ocena internetowej linii bazowej ma na celu znalezienie ustawień serwera internetowego, które mogą potencjalnie powodować zagrożenie.

## <a name="view-processed-event-details"></a>Wyświetl szczegóły przetwarzania zdarzeń
1. W obszarze **usługi Security Center** menu głównego, wybierz opcję **zdarzenia**.
2. **Pulpit nawigacyjny zdarzeń** selektor obszaru roboczego może zostać otwarty. Jeśli masz tylko jeden obszar roboczy, selektor obszaru roboczego, w tym nie jest widoczna. Jeśli masz więcej niż jeden obszar roboczy, musisz wybrać obszar roboczy, aby wyświetlić jego szczegóły przetwarzania zdarzeń. Wybierz obszar roboczy z listy, jeśli masz więcej niż jednego obszaru roboczego.

   ![Listy obszaru roboczego][3]

3. **Pulpit nawigacyjny zdarzeń** otwiera zawierająca szczegóły zdarzenia dla wybranego obszaru roboczego. Możesz wyświetlić istotne zdarzenia i wszystkie zdarzenia według typu.  W tym przykładzie Wybraliśmy **istotnych zdarzeń**.

   ![Istotne zdarzenie][4]

4. Można tworzyć zapytania większej ilości danych w obszarze roboczym, wybierając typ zdarzenia. W tym przykładzie Wybraliśmy **SecurityEvent**.

   ![Wybieranie typu zdarzenia][5]

5. **Wyszukiwania w dzienniku** otwiera o dodatkowe informacje szczegółowe na temat typu zdarzenia.

   ![Wyszukiwanie w dzienniku][6]

## <a name="add-a-notable-event"></a>Dodaj istotne zdarzenie
Usługa Security Center zapewnia out-of--box istotnych zdarzeń. Możesz dodać istotnych zdarzeń w oparciu o własne zapytania za pomocą [język zapytania Kusto](../log-analytics/log-analytics-search-reference.md). Wrócimy do **pulpit nawigacyjny zdarzeń** do Dodaj istotne zdarzenie.

1. Wybierz **Dodaj istotne zdarzenie**.

   ![Dodaj istotne zdarzenie][7]

2. **Dodaj istotne zdarzenie niestandardowe** zostanie otwarty.  W obszarze **nazwę wyświetlaną**, wprowadź nazwę swojej istotnego zdarzenia. W obszarze **zapytania wyszukiwania**, wprowadź kwerendę dla zdarzenia.

   ![Wprowadź kwerendę][8]

4. Kliknij przycisk **OK**.

## <a name="update-your-workspace-for-events-processing"></a>Zaktualizować obszar roboczy dla przetwarzania zdarzeń
Obszar roboczy musi być uruchomiona usługa Log Analytics w wersji 2 i na warstwy standardowa usługi Security Center umożliwia przetwarzanie zdarzeń w usłudze Security Center. **Pulpit nawigacyjny zdarzeń** selektor obszaru roboczego identyfikuje obszary robocze, które nie spełniają tych wymagań.

![Obszar roboczy nie spełnia wymagań][9]

Jeśli wiersz obszaru roboczego:

- Zawiera **wymaga aktualizacji** — trzeba zaktualizować obszar roboczy usługi Log Analytics w wersji 2
- Zawiera **PLAN uaktualnienia** — musisz uaktualnić obszar roboczy do warstwy standardowa usługi Security Center
- Jest puste - obszaru roboczego spełnia wymagania i wybranie obszaru roboczego spowoduje przejście do pulpitu nawigacyjnego

> [!NOTE]
> W obszarze **pulpit nawigacyjny zdarzeń**, **zdarzenia** kolumna wskazuje ilość zdarzenia w poszczególnych obszarach roboczych.  W tej kolumnie jest puste w przypadku niektórych obszarów roboczych, ponieważ Security Center w warstwie bezpłatna została zastosowana do tego obszaru roboczego. W ramach warstwy bezpłatna usługi Security Center będzie zbierać zdarzenia, ale zdarzenia nie są zapisywane w dziennikach w usłudze Azure Monitor i nie są dostępne na pulpicie nawigacyjnym.
>
>

## <a name="update-workspace-to-log-analytics-version-2"></a>Zaktualizować obszar roboczy w usłudze Log Analytics w wersji 2
1. Wybierz obszar roboczy, który **wymaga aktualizacji**.
2. **Wyszukaj uaktualnienie** zostanie otwarty. Wybierz **Uaktualnij teraz**.

   ![Podnieś poziom teraz][10]

## <a name="upgrade-to-security-centers-standard-tier"></a>Uaktualnienie do warstwy standardowa usługi Security Center
1. Wybierz obszar roboczy z **PLAN uaktualnienia**.
2. **Pulpit nawigacyjny zdarzeń** zostanie otwarty. Wybierz **pulpit nawigacyjny zdarzeń spróbuj**.

   ![Wypróbuj pulpit nawigacyjny][11]

3. W obszarze **dołączanie do zabezpieczeń zaawansowanych**, wybierz obszar roboczy, która jest uaktualniana.
4. W obszarze **ceny**, wybierz opcję **standardowa**.
5. Wybierz pozycję **Zapisz**.

   ![Uaktualnienie do warstwy standardowa][12]

## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób korzystania z pulpitu nawigacyjnego zdarzenie Security Center. Aby dowiedzieć się więcej o tym, jak działa pulpitu nawigacyjnego i pisać własne zapytania zdarzeń, zobacz:

- [Co to jest dzienniki usługi Azure Monitor?](../log-analytics/log-analytics-overview.md) — Rejestruje Omówienie usługi Azure Monitor
- [Understanding log przeszukuje Kusto](../log-analytics/log-analytics-log-search-new.md) — zawiera opis używania wyszukiwań w dziennikach w usłudze Azure Monitor i oferuje pojęcia, które należy rozumieć, przed utworzeniem przeszukiwania dzienników
- [Dokumentacja wyszukiwania Kusto](../log-analytics/log-analytics-search-reference.md) — Dowiedz się, jak pisać własne zapytania zdarzeń przy użyciu języka zapytań w Dzienniku

Aby dowiedzieć się więcej na temat usługi Security Center, zobacz:

- [Security Center — Przegląd](security-center-intro.md) — Centrum zabezpieczeń w tym artykule opisano kluczowe funkcje

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
