---
title: Monitorowanie i przetwarzania zdarzeń zabezpieczeń w Centrum zabezpieczeń Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można pulpit nawigacyjny zdarzenia Centrum zabezpieczeń wyświetlane zdarzenia zabezpieczeń z maszynami wirtualnymi Azure i komputerów z systemem innym niż Azure.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: terrylan
ms.openlocfilehash: 367067874b167268bd690a9e0b55412e92e08122
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23926529"
---
# <a name="monitoring-and-processing-security-events-in-azure-security-center"></a>Monitorowanie i przetwarzania zdarzeń zabezpieczeń w Centrum zabezpieczeń Azure
Pulpit nawigacyjny zdarzenia zawiera omówienie liczby zdarzeń zabezpieczeń zbierane wraz z upływem czasu i listę istotnych zdarzeń, które mogą wymagać Twojej uwagi.  

> [!NOTE]
> Aby użyć tej funkcji, obszaru roboczego musi być uruchomiony analizy dzienników w wersji 2 i znajdować się w Centrum zabezpieczeń warstwy standardowa. Zobacz temat w Centrum zabezpieczeń [cennikiem](security-center-pricing.md) Aby uzyskać więcej informacji na temat warstwy standardowa.
>
>

## <a name="what-is-a-security-event"></a>Co to jest zdarzenie zabezpieczeń?
Centrum zabezpieczeń używa programu Microsoft Monitoring Agent służąca do gromadzenia różnych zabezpieczeń związane z konfiguracji i zdarzenia z maszyny i zapisuje te zdarzenia w Twojej obszarów roboczych. Przykłady takich danych to: operacyjne dzienniki systemu (dzienniki zdarzeń systemu Windows), uruchomione procesy, oraz zdarzenia z rozwiązań zabezpieczeń zintegrowanych z Centrum zabezpieczeń. Program Microsoft Monitoring Agent kopiuje również pliki zrzutu awaryjnego do obszarów roboczych.

## <a name="events-processed-dashboard"></a>Pulpit nawigacyjny przetworzonych zdarzeń
Możesz uzyskać dostępu do **zdarzenia** pulpit nawigacyjny z menu głównego Centrum zabezpieczeń lub Centrum zabezpieczeń **omówienie** bloku.  

![Pulpit nawigacyjny przetworzonych zdarzeń][1]

**Zdarzenia** kafelka w obszarze **Centrum zabezpieczeń** Wyświetla liczbę zdarzeń otrzymywanych przez Centrum zabezpieczeń z maszynami wirtualnymi Azure i komputerów z systemem innym niż Azure.

**Pulpitu nawigacyjnego zdarzenia** zawiera omówienie liczba przetworzonych zdarzeń nadgodzinach oraz listę zdarzeń.

 ![Pulpit nawigacyjny][2]

 W górnej połowie pulpitu nawigacyjnego trendów wszystkie zdarzenia przetwarzane w ostatnim tygodniu. Dolnej połowie pulpitu nawigacyjnego wymieniono istotnych zdarzeń i wszystkie zdarzenia według typu:

 - **Zdarzenia zauważalne** obejmują zapytania dotyczące zdarzeń, które zapewnia Centrum zabezpieczeń oraz zapytania dotyczące zdarzeń, które możesz utworzyć i dodać. Pulpit nawigacyjny udostępnia zapewnia szybki wgląd w liczba każdego zauważalne zdarzenia.
 - **Wszystkie zdarzenia według typu** zawiera typy zdarzeń, które są odbierane i liczba dla każdego typu. Przykłady typu zdarzenia są SecurityEvent, CommonSecurityLog, WindowsFirewall i W3CIISLog.

> [!NOTE]
> Godne zdarzenia zawierają [sieci web oceny linii bazowej](https://docs.microsoft.com/azure/operations-management-suite/oms-security-web-baseline-assessment). Ocena internetowej linii bazowej ma na celu znalezienie ustawień serwera internetowego, które mogą potencjalnie powodować zagrożenie.

## <a name="view-processed-event-details"></a>Wyświetl szczegóły przetworzonych zdarzeń
1. W obszarze **Centrum zabezpieczeń** menu głównego, wybierz opcję **zdarzenia**.
2. **Pulpitu nawigacyjnego zdarzenia** selektora obszaru roboczego może zostać otwarty. Jeśli masz tylko jeden obszar roboczy selektor ten obszar roboczy nie jest widoczna. Jeśli masz więcej niż jeden obszar roboczy, musisz wybrać obszar roboczy, aby wyświetlić jego szczegóły przetworzonych zdarzeń. Wybierz obszar roboczy z listy, jeśli masz więcej niż jeden obszar roboczy.

  ![Lista obszaru roboczego][3]

3. **Pulpitu nawigacyjnego zdarzenia** otwiera pokazuje szczegóły zdarzenia dla wybranego obszaru roboczego. Można wyświetlać zdarzenia zauważalne i wszystkie zdarzenia według typu.  W tym przykładzie wybrano **istotnych zdarzeń**.

  ![Godne zdarzeń][4]

4. Mogą wykonywać kwerendę o większej ilości danych w obszarze roboczym, wybierając typ zdarzenia. W tym przykładzie wybrano **SecurityEvent**.

  ![Wybieranie typu zdarzenia][5]

5. **Zaloguj się wyszukiwania** otwiera z dodatkowych szczegółów na typ zdarzenia.

  ![Wyszukiwanie w dzienniku][6]

## <a name="add-a-notable-event"></a>Dodaj istotnych zdarzeń
Centrum zabezpieczeń zapewnia poza pole zauważalne zdarzenia. Możesz dodać istotnych zdarzeń oparte na własnych zapytania za pomocą [języka zapytań usługi Analiza dzienników](../log-analytics/log-analytics-search-reference.md). Wrócimy do **pulpitu nawigacyjnego zdarzenia** można dodać istotnych zdarzeń.

1. Wybierz **dodać zauważalne zdarzenie**.

  ![Dodaj istotnych zdarzeń][7]

2. **Dodaj niestandardowe zdarzenie zauważalne** otwiera.  W obszarze **Nazwa wyświetlana**, wprowadź nazwę zauważalne wydarzenia. W obszarze **zapytania wyszukiwania**, wprowadź kwerendę dla zdarzenia.

  ![Wprowadź kwerendę][8]

4. Kliknij przycisk **OK**.

## <a name="update-your-workspace-for-events-processing"></a>Zaktualizować obszar roboczy przetwarzania zdarzeń
Obszar roboczy musi być uruchomiony analizy dzienników w wersji 2 i musi być w Centrum zabezpieczeń warstwy standardowa, aby użyć przetwarzania zdarzeń w Centrum zabezpieczeń. **Pulpitu nawigacyjnego zdarzenia** selektora obszaru roboczego identyfikuje obszary robocze, które nie spełniają tych wymagań.

![Obszar roboczy nie spełnia wymagań][9]

Jeśli wiersz obszaru roboczego:

- Zawiera **wymaga aktualizacji** — należy zaktualizować obszar roboczy do analizy dzienników w wersji 2
- Zawiera **UAKTUALNIĆ PLAN** — należy uaktualnić obszaru roboczego do warstwy standardowa Centrum zabezpieczeń
- Jest pusty — obszaru roboczego spełnia wymagania i wybierając obszar roboczy przejście do pulpitu nawigacyjnego

> [!NOTE]
> W obszarze **pulpitu nawigacyjnego zdarzenia**, **zdarzenia** kolumna wskazuje ilość zdarzeń w każdym obszarze roboczym.  W tej kolumnie jest puste w przypadku niektórych obszarów roboczych, ponieważ poziom wolnego Centrum zabezpieczeń jest stosowany do tego obszaru roboczego. W obszarze warstwę bezpłatna Centrum zabezpieczeń będzie zbierać zdarzenia, ale zdarzenia nie są zapisywane w analizy dzienników i nie są dostępne na pulpicie nawigacyjnym.
>
>

## <a name="update-workspace-to-log-analytics-version-2"></a>Aktualizowanie obszaru roboczego do analizy dzienników w wersji 2
1. Wybierz obszar roboczy który **wymaga aktualizacji**.
2. **Wyszukaj uaktualnienia** otwiera. Wybierz **Uaktualnij teraz**.

  ![Uaktualnij teraz][10]

## <a name="upgrade-to-security-centers-standard-tier"></a>Uaktualnij do warstwy standardowa Centrum zabezpieczeń
1. Wybierz obszar roboczy z **UAKTUALNIĆ PLAN**.
2. **Pulpit nawigacyjny zdarzenia** otwiera. Wybierz **pulpitu nawigacyjnego spróbuj zdarzenia**.

  ![Spróbuj pulpitu nawigacyjnego][11]

3. W obszarze **dołączania z zabezpieczeniami zaawansowanymi**, wybierz obszar roboczy, który jest uaktualniany.
4. W obszarze **cennik**, wybierz pozycję **standardowe**.
5. Wybierz pozycję **Zapisz**.

  ![Uaktualnij do warstwy standardowa][12]

## <a name="next-steps"></a>Następne kroki
W tym artykule przedstawiono sposób za pomocą pulpitu nawigacyjnego zdarzenie Centrum zabezpieczeń. Aby dowiedzieć się więcej na temat działania pulpitu nawigacyjnego i pisanie zapytań zdarzeń, zobacz:

- [Co to jest usługa Log Analytics?](../log-analytics/log-analytics-overview.md) — Przegląd na analizy dzienników
- [Opis dziennika przeszukuje analizy dzienników](../log-analytics/log-analytics-log-search-new.md) — w tym artykule opisano, jak dziennik wyszukiwania są używane w analizy dzienników i udostępnia pojęcia, które należy zrozumieć przed utworzeniem wyszukiwania dziennika
- [Odwołanie wyszukiwania analizy dzienników](../log-analytics/log-analytics-search-reference.md) — Dowiedz się, jak napisać własny zapytania dotyczące zdarzeń przy użyciu języka zapytań w Dzienniku

Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz:

- [Omówienie Centrum zabezpieczeń](security-center-intro.md) — Centrum zabezpieczeń w tym artykule opisano kluczowe możliwości

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
