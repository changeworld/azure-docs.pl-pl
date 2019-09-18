---
title: Funkcje w kwerendach dzienników Azure Monitor | Microsoft Docs
description: W tym artykule opisano, jak używać funkcji do wywoływania zapytania z innego zapytania dziennika w Azure Monitor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: bwren
ms.openlocfilehash: 75beb7b66863efd2fb3679f034a3663dca4a6d2f
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076700"
---
# <a name="using-functions-in-azure-monitor-log-queries"></a>Używanie funkcji w kwerendach dzienników Azure Monitor

Aby użyć zapytania dziennika z innym zapytaniem, można je zapisać jako funkcję. Pozwala to uprościć złożone zapytania, dzieląc je na części i umożliwia ponowne użycie wspólnego kodu z wieloma zapytaniami.

## <a name="create-a-function"></a>Utwórz funkcję

Utwórz funkcję z Log Analytics w Azure Portal, klikając przycisk **Zapisz** , a następnie podając informacje w poniższej tabeli.

| Ustawienie | Opis |
|:---|:---|
| Name           | Nazwa wyświetlana zapytania w **Eksploratorze zapytań**. |
| Zapisz jako        | Funkcja |
| Alias funkcji | Krótka nazwa służąca do korzystania z funkcji w innych zapytaniach. Nie może zawierać spacji i musi być unikatowa. |
| Kategoria       | Kategoria do organizowania zapisanych zapytań i funkcji w **Eksploratorze zapytań**. |

> [!NOTE]
> Funkcja w Azure Monitor nie może zawierać innej funkcji.




## <a name="use-a-function"></a>Korzystanie z funkcji
Użyj funkcji, dołączając jej alias w innym zapytaniu. Może być używana tak jak jakakolwiek inna tabela.

## <a name="example"></a>Przykład
Następujące przykładowe zapytanie zwraca wszystkie brakujące aktualizacje zabezpieczeń zgłoszone w ciągu ostatniego dnia. Zapisz to zapytanie jako funkcję z aliasem _security_updates_last_day_. 

```Kusto
Update
| where TimeGenerated > ago(1d) 
| where Classification == "Security Updates" 
| where UpdateState == "Needed"
```

Utwórz kolejne zapytanie i odwołuje się do funkcji _security_updates_last_day_ w celu wyszukania wymaganych aktualizacji zabezpieczeń związanych z programem SQL.

```Kusto
security_updates_last_day | where Title contains "SQL"
```

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z innymi lekcjami dotyczącymi pisania Azure Monitor zapytań dziennika:

- [Operacje na ciągach](string-operations.md)
- [Operacje na dacie i godzinie](datetime-operations.md)
- [Funkcje agregacji](aggregations.md)
- [Agregacje zaawansowane](advanced-aggregations.md)
- [JSON i struktury danych](json-data-structures.md)
- [Łącze](joins.md)
- [Schematy](charts.md)
