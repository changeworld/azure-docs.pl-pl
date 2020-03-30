---
title: Zapytania wyszukiwania w dziennikach usługi Azure Monitor | Dokumenty firmy Microsoft
description: Ten artykuł zawiera samouczek dotyczący rozpoczynania korzystania z wyszukiwania w kwerendach dziennika usługi Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/06/2018
ms.openlocfilehash: e13f4abc37e348759e7d0b8a2f7d890c82fe0d15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77660244"
---
# <a name="search-queries-in-azure-monitor-logs"></a>Kwerendy wyszukiwania w dziennikach usługi Azure Monitor
Zapytania dziennika usługi Azure Monitor można rozpocząć od nazwy tabeli lub polecenia wyszukiwania. W tym samouczku omówiono zapytania oparte na wyszukiwaniu. Każda metoda ma zalety.

Kwerendy oparte na tabelach zaczynają się od określania zakresu zapytania i dlatego wydają się być bardziej wydajne niż zapytania wyszukiwania. Zapytania wyszukiwania są mniej ustrukturyzowane, co czyni je lepszym wyborem podczas wyszukiwania określonej wartości w kolumnach lub tabelach. **wyszukiwanie** może skanować wszystkie kolumny w danej tabeli lub we wszystkich tabelach, aby uzyskać określoną wartość. Ilość przetwarzanych danych może być ogromna, dlatego te zapytania mogą potrwać dłużej i mogą zwracać bardzo duże zestawy wyników.

## <a name="search-a-term"></a>Wyszukiwanie terminu
Polecenie **wyszukiwania** jest zwykle używane do wyszukiwania określonego terminu. W poniższym przykładzie wszystkie kolumny we wszystkich tabelach są skanowane pod kątem terminu "błąd":

```Kusto
search "error"
| take 100
```

Chociaż są one łatwe w użyciu, zapytania nieskopowe, takie jak ten, który pokazano powyżej, nie są skuteczne i mogą zwrócić wiele nieistotnych wyników. Lepszą praktyką byłoby wyszukiwanie w odpowiedniej tabeli, a nawet w określonej kolumnie.

### <a name="table-scoping"></a>Zakres tabeli
Aby wyszukać termin w `in (table-name)` określonej tabeli, dodaj tylko po **operatorze wyszukiwania:**

```Kusto
search in (Event) "error"
| take 100
```

lub w wielu tabelach:
```Kusto
search in (Event, SecurityEvent) "error"
| take 100
```

### <a name="table-and-column-scoping"></a>Zakres tabeli i kolumn
Domyślnie **wyszukiwanie** oceni wszystkie kolumny w zestawie danych. Aby wyszukać tylko określoną kolumnę (o nazwie *Źródło* w poniższym przykładzie), użyj tej składni:

```Kusto
search in (Event) Source:"error"
| take 100
```

> [!TIP]
> Jeśli używasz `==` zamiast `:`, wyniki będą zawierać rekordy, w których *źródło* kolumna ma dokładną wartość "błąd", a w tym przypadku dokładnie. Za pomocą ':' będzie zawierać rekordy, w których *Źródło* ma wartości, takie jak "kod błędu 404" lub "Błąd".

## <a name="case-sensitivity"></a>Sensitivity
Domyślnie wyszukiwanie terminów jest niewrażliwe na wielkości liter, więc wyszukiwanie "dns" może przynieść wyniki, takie jak "DNS", "dns" lub "Dns". Aby rozróżniać wielkość liter `kind` w wyszukiwaniu, użyj tej opcji:

```Kusto
search kind=case_sensitive in (Event) "DNS"
| take 100
```

## <a name="use-wild-cards"></a>Używanie dzikich kart
Polecenie **wyszukiwania** obsługuje symbole wieloznaczne na początku, końcu lub w połowie terminu.

Aby wyszukać terminy zaczynające się od "win":
```Kusto
search in (Event) "win*"
| take 100
```

Aby wyszukać terminy, które kończą się na ".com":
```Kusto
search in (Event) "*.com"
| take 100
```

Aby wyszukać terminy zawierające "www":
```Kusto
search in (Event) "*www*"
| take 100
```

Aby wyszukać terminy, które zaczynają się od "corp", a kończy na ".com", takie jak "corp.mydomain.com""

```Kusto
search in (Event) "corp*.com"
| take 100
```

Można również dostać wszystko w tabeli za `search in (Event) *`pomocą tylko dziką kartę: `Event`, ale to byłoby takie samo jak pisanie tylko .

> [!TIP]
> Chociaż można `search *` użyć, aby uzyskać każdą kolumnę z każdej tabeli, zaleca się, aby zawsze zakres zapytania do określonych tabel. Nieskopowe kwerendy mogą zająć trochę czasu, aby zakończyć i może zwrócić zbyt wiele wyników.

## <a name="add-and--or-to-search-queries"></a>Dodawanie *i* / *lub* wyszukiwanie zapytań
Użyj **i** wyszukaj rekordy zawierające wiele terminów:

```Kusto
search in (Event) "error" and "register"
| take 100
```

Użyj **lub** aby uzyskać rekordy, które zawierają co najmniej jeden z terminów:

```Kusto
search in (Event) "error" or "register"
| take 100
```

Jeśli masz wiele warunków wyszukiwania, możesz połączyć je w tę samą kwerendę przy użyciu nawiasów:

```Kusto
search in (Event) "error" and ("register" or "marshal*")
| take 100
```

Wyniki tego przykładu mogą być rekordy, które zawierają termin "błąd", a także zawierają "register" lub coś, co zaczyna się od "marshal".

## <a name="pipe-search-queries"></a>Zapytania wyszukiwania potoków
Podobnie jak każde inne polecenie, **wyszukiwanie** może być potokowane, aby wyniki wyszukiwania mogły być filtrowane, sortowane i agregowane. Na przykład, aby uzyskać liczbę rekordów *zdarzeń* zawierających "wygraj":

```Kusto
search in (Event) "win"
| count
```




## <a name="next-steps"></a>Następne kroki

- Zobacz dalsze tutoriale na [stronie języka zapytania Kusto](/azure/kusto/query/).
