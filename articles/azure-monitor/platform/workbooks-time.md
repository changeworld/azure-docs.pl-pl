---
title: Parametry czasu skoroszytów monitora Azure Monitor
description: Uprość składanie złożonych raportów dzięki wstępnie utworzonym i niestandardowych sparametryzowanym skoroszytom
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 380b8a7ce286ab06b6935bf63bf3a0e82f371c2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658017"
---
# <a name="workbook-time-parameters"></a>Parametry czasu skoroszytu

Parametry czasu umożliwiają użytkownikom ustawienie kontekstu czasu analizy i jest używany przez prawie wszystkie raporty. Jest to stosunkowo proste w konfiguracji i obsłudze — pozwala autorom określić zakresy czasu, które mają być wyświetlane w menu rozwijanej, w tym opcję dla niestandardowych zakresów czasu. 

## <a name="creating-a-time-parameter"></a>Tworzenie parametru czasu
1. Zacznij od pustego skoroszytu w trybie edycji.
2. Wybierz _pozycję Dodaj parametry_ z łączy w skoroszycie.
3. Kliknij niebieski przycisk _Dodaj parametr._
4. W nowym okienku parametrów, które wyskakuje wpisać:
    1. Nazwa parametru:`TimeRange`
    2. Typ parametru:`Time range picker`
    3. Wymagane:`checked`
    4. Dostępne zakresy czasu: Ostatnia godzina, Ostatnie 12 godzin, Ostatnie 24 godziny, Ostatnie 48 godzin, Ostatnie 3 dni, Ostatnie 7 dni i Zezwalaj na wybór niestandardowego zakresu czasu
5. Wybierz "Zapisz" na pasku narzędzi, aby utworzyć parametr.

    ![Obraz przedstawiający tworzenie parametru zakresu czasu](./media/workbooks-time/time-settings.png)

Tak będzie wyglądał skoroszyt w trybie odczytu.

![Obraz przedstawiający parametr zakresu czasu w trybie odczytu](./media/workbooks-time/parameters-time.png)

## <a name="referencing-a-time-parameter"></a>Odwoływanie się do parametru czasu
### <a name="via-bindings"></a>Za pośrednictwem wiązań
1. Dodaj formant kwerendy do skoroszytu i wybierz zasób usługi Application Insights.
2. Większość formantów skoroszytu obsługuje selektor _zakresu czasu._ Otwórz list rozwijany _Zakres_ czasu `{TimeRange}` i wybierz w grupie parametry pokjąć czas na dole.
3. Wiąże to parametr zakresu czasu z zakresem czasu wykresu. Zakres czasu przykładowej kwerendy jest teraz Ostatnie 24 godziny.
4. Uruchom kwerendę, aby zobaczyć wyniki

    ![Obraz przedstawiający parametr zakresu czasu, do którego odwołuje się wiązania](./media/workbooks-time/time-binding.png)

### <a name="in-kql"></a>w KQL
1. Dodaj formant kwerendy do skoroszytu i wybierz zasób usługi Application Insights.
2. W KQL wprowadź filtr zakresu czasu przy użyciu parametru:`| where timestamp {TimeRange}`
3. Spowoduje to rozszerzenie czasu `| where timestamp > ago(1d)`oceny kwerendy do , który jest wartością zakresu czasu parametru.
4. Uruchom kwerendę, aby zobaczyć wyniki

    ![Obraz przedstawiający zakres czasu, do którego odwołuje się KQL](./media/workbooks-time/time-in-code.png)

### <a name="in-text"></a>W tekście 
1. Dodawanie formantu tekstowego do skoroszytu.
2. W znacznikach wprowadź`The chosen time range is {TimeRange:label}`
3. Wybierz _gotowe edytowanie_
4. Formant tekstu wyświetli tekst: _Wybrany zakres czasu to Ostatnie 24 godziny_

## <a name="time-parameter-options"></a>Opcje parametrów czasu
| Parametr | Wyjaśnienie | Przykład |
| ------------- |:-------------|:-------------|
| `{TimeRange}` | Etykieta zakresu czasu | Ostatnie 24 godziny |
| `{TimeRange:label}` | Etykieta zakresu czasu | Ostatnie 24 godziny |
| `{TimeRange:value}` | Wartość zakresu czasu | > temu(1d) |
| `{TimeRange:query}` | Kwerenda zakresu czasu | > temu(1d) |
| `{TimeRange:start}` | Czas rozpoczęcia zakresu czasu | 20.03.2019 16:18 |
| `{TimeRange:end}` | Czas zakończenia zakresu czasu | 21.03.2019 16:18 |
| `{TimeRange:grain}` | Zakres czasu ziarna | 30 m |


### <a name="using-parameter-options-in-a-query"></a>Korzystanie z opcji parametrów w kwerendzie
```kusto
requests
| make-series Requests = count() default = 0 on timestamp from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain}
```

## <a name="next-steps"></a>Następne kroki

* [Rozpocznij](workbooks-visualizations.md) naukę o skoroszytach wiele rozbudowanych opcji wizualizacji.
* [Kontroluj](workbooks-access-control.md) i udostępniaj dostęp do zasobów skoroszytu.
