---
title: Zapytań wyszukiwania w usłudze Log Analytics | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera samouczek dla klientów zaczynających pracę Pisanie zapytań wyszukiwania w usłudze Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/06/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 2ccef960378190f10e64318f91039871657a1a46
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2018
ms.locfileid: "45603757"
---
# <a name="search-queries-in-log-analytics"></a>Zapytania wyszukiwania w usłudze Log Analytics

> [!NOTE]
> Należy wykonać [wprowadzenie do zapytań w usłudze Log Analytics](get-started-queries.md) przed wykonaniem tej lekcji.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Usługa Azure zapytań usługi Log Analytics można uruchomić w nazwie tabeli lub polecenie wyszukiwania. Ten samouczek obejmuje zapytań na podstawie wyszukiwania. Istnieją zalety łączenia poszczególnych metod.

Zapytania w oparciu o tabelę początek zakresu zapytania i dlatego zwykle bardziej efektywne niż zapytania wyszukiwania. Zapytania wyszukiwania są mniej strukturalnych, co sprawia, że ich lepszym rozwiązaniem podczas wyszukiwania określonej wartości w kolumnach lub tabel. **Wyszukiwanie** może skanować wszystkie kolumny w danej tabeli lub wszystkie tabele dla określonej wartości. Ilość przetwarzanych danych może być ogromną, dlatego te zapytania może potrwać dłużej i może zwrócić bardzo dużych zestawów wyników.

## <a name="search-a-term"></a>Szuka terminu
**Wyszukiwania** polecenie jest zwykle używane do wyszukiwania określonego wyrażenia. W poniższym przykładzie wszystkich kolumn wszystkich tabel są skanowane pod kątem termin "error":

```KQL
search "error"
| take 100
```

Gdy są one łatwe w użyciu, niewystępującego w zakresie zapytania, tak jak pokazano powyżej nie są wydajne i mogą zwrócić wiele wyników nie ma znaczenia. Lepsze rozwiązaniem byłoby wyszukiwania w odpowiedniej tabeli lub określonej kolumny.

### <a name="table-scoping"></a>Wyznaczanie zakresu tabeli
Aby wyszukać termin w określonej tabeli, należy dodać `in (table-name)` tuż za **wyszukiwania** operator:

```KQL
search in (Event) "error"
| take 100
```

lub w wielu tabelach:
```KQL
search in (Event, SecurityEvent) "error"
| take 100
```

### <a name="table-and-column-scoping"></a>Tabela i kolumna określać ich zakresy.
Domyślnie **wyszukiwania** oszacuje wszystkie kolumny w zestawie danych. Aby wyszukać tylko określone kolumny, użyj następującej składni:

```KQL
search in (Event) Source:"error"
| take 100
```

> [!TIP]
> Jeśli używasz `==` zamiast `:`, wyniki będą uwzględniać rekordy, w którym *źródła* kolumna zawiera dokładną wartość "error", a w tym dokładne dopasowanie wielkości liter. Za pomocą ":" nie będzie zawierać rekordy gdzie *źródła* zawiera wartości, takie jak "Kod błędu 404" lub "Error".

## <a name="case-sensitivity"></a>Uwzględnianie wielkości liter
Domyślnie termin wyszukiwania jest bez uwzględniania wielkości liter, dzięki czemu wyszukiwanie "dns" wygeneruje wyniki, takie jak "DNS", "dns" lub "Dns". Aby wprowadzić wyszukiwania jest uwzględniana wielkość liter, należy użyć `kind` opcji:

```KQL
search kind=case_sensitive in (Event) "DNS"
| take 100
```

## <a name="use-wild-cards"></a>Użyj symbole wieloznaczne
**Wyszukiwania** polecenie obsługuje symbole wieloznaczne, początek, koniec lub bliski termin.

Aby wyszukać warunki, rozpoczynające się od "win":
```KQL
search in (Event) "win*"
| take 100
```

Aby wyszukać warunki, które kończą się ".com":
```KQL
search in (Event) "*.com"
| take 100
```

Aby wyszukać warunki, które zawierają "www":
```KQL
search in (Event) "*www*"
| take 100
```

Warunki wyszukiwania, rozpoczyna się od "corp", które kończy się na ".com", takich jak "corp.mydomain.com" "

```KQL
search in (Event) "corp*.com"
| take 100
```

Można również uzyskać wszystko, co w tabeli za pomocą właśnie jest symbol wieloznaczny: `search in (Event) *`, ale ta będzie taki sam, jak pisanie, po prostu `Event`.

> [!TIP]
> Za pomocą `search *` można pobrać wszystkich kolumn z każdej tabeli, zalecane jest, zawsze określać zakres zapytań w taki sposób, aby określonych tabel. Poza zakresem zapytania może zająć trochę czasu i może zwrócić zbyt wiele wyników.

## <a name="add-and--or-to-search-queries"></a>Dodaj *i* / *lub* wyszukiwania zapytania
Użyj **i** można wyszukiwać rekordy, które zawierają wiele wersji warunków:

```KQL
search in (Event) "error" and "register"
| take 100
```

Użyj **lub** Aby uzyskać rekordy, które zawierają co najmniej jeden z warunków:

```KQL
search in (Event) "error" or "register"
| take 100
```

Jeśli masz wielu warunków wyszukiwania, można połączyć je do tego samego zapytania przy użyciu nawiasów:

```KQL
search in (Event) "error" and ("register" or "marshal*")
| take 100
```

Wyniki w tym przykładzie będzie rekordów, które zawierają termin "error", a także zawierać "register" lub coś, który rozpoczyna się od "marshal".

## <a name="pipe-search-queries"></a>Zapytania wyszukiwania potoku
Podobnie jak inne polecenia **wyszukiwania** mogą być przesyłane potokiem tak, aby wyniki wyszukiwania można filtrować, sortować i agregować. Na przykład, aby uzyskać liczbę *zdarzeń* rekordy, które zawierają "win":

```KQL
search in (Event) "win"
| count
```




## <a name="next-steps"></a>Kolejne kroki

- Zobacz więcej samouczków w [lokacji języka zapytań usługi Log Analytics](https://docs.loganalytics.io)