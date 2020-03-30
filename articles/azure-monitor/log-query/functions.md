---
title: Funkcje w kwerendach dziennika usługi Azure Monitor | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób używania funkcji do wywoływania kwerendy z innej kwerendy dziennika w usłudze Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/15/2018
ms.openlocfilehash: 7d94e53abbe8f4d2953729aa2363c3906ce94f74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670223"
---
# <a name="using-functions-in-azure-monitor-log-queries"></a>Korzystanie z funkcji w kwerendach dziennika usługi Azure Monitor

Aby użyć kwerendy dziennika z inną kwerendą, można zapisać ją jako funkcję. Dzięki temu można uprościć złożone zapytania, rozbijając je na części i umożliwia ponowne użycie wspólnego kodu z wieloma zapytaniami.

## <a name="create-a-function"></a>Tworzenie funkcji

Utwórz funkcję z usługą Log Analytics w witrynie Azure portal, klikając przycisk **Zapisz,** a następnie podając informacje w poniższej tabeli.

| Ustawienie | Opis |
|:---|:---|
| Nazwa           | Wyświetlana nazwa kwerendy w **Eksploratorze kwerend**. |
| Zapisz jako        | Funkcja |
| Alias funkcji | Krótka nazwa do używania funkcji w innych kwerendach. Może nie zawierać spacji i musi być unikatowa. |
| Kategoria       | Kategoria organizowania zapisanych zapytań i funkcji w **Eksploratorze zapytań**. |

> [!NOTE]
> Funkcja w usłudze Azure Monitor nie może zawierać innej funkcji.




## <a name="use-a-function"></a>Korzystanie z funkcji
Użyj funkcji, dołączając jej alias do innej kwerendy. Może być używany jak każdy inny stół.

## <a name="example"></a>Przykład
Poniższa przykładowa kwerenda zwraca wszystkie brakujące aktualizacje zabezpieczeń zgłoszone w ciągu ostatniego dnia. Zapisz tę kwerendę jako funkcję o aliasie _security_updates_last_day_. 

```Kusto
Update
| where TimeGenerated > ago(1d) 
| where Classification == "Security Updates" 
| where UpdateState == "Needed"
```

Utwórz inną kwerendę i odwołaj się do funkcji _security_updates_last_day,_ aby wyszukać potrzebne aktualizacje zabezpieczeń związane z sql.

```Kusto
security_updates_last_day | where Title contains "SQL"
```

## <a name="next-steps"></a>Następne kroki
Zobacz inne lekcje dotyczące pisania zapytań dziennika usługi Azure Monitor:

- [Operacje dotyczące ciągów](string-operations.md)
- [Operacje dotyczące daty i godziny](datetime-operations.md)
- [Funkcje agregacji](aggregations.md)
- [Agregacje zaawansowane](advanced-aggregations.md)
- [Notacja JSON i struktury danych](json-data-structures.md)
- [Łączy](joins.md)
- [Wykresy](charts.md)
