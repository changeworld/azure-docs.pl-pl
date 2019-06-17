---
author: brjohnstmsft
ms.service: search
ms.topic: include
ms.date: 06/13/2018
ms.author: brjohnst
ms.openlocfilehash: b4d0bc73e652a1cd6ef59589318b92f63727c7f5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079641"
---
| Typ danych | Dozwolone w wyrażeniach lambda przy użyciu funkcji `any` | Dozwolone w wyrażeniach lambda przy użyciu funkcji `all` |
|---|---|---|
| `Collection(Edm.ComplexType)` | Wszystkim, z wyjątkiem `search.ismatch` i `search.ismatchscoring` | Ten sam |
| `Collection(Edm.String)` | Porównania z wartością `eq` lub `search.in` <br/><br/> Łączenie wyrażeń podrzędnych za pomocą `or` | Porównania z wartością `ne` lub `not search.in()` <br/><br/> Łączenie wyrażeń podrzędnych za pomocą `and` |
| `Collection(Edm.Boolean)` | Porównania z wartością `eq` lub `ne` | Ten sam |
| `Collection(Edm.GeographyPoint)` | Za pomocą `geo.distance` z `lt` lub `le` <br/><br/> `geo.intersects` <br/><br/> Łączenie wyrażeń podrzędnych za pomocą `or` | Za pomocą `geo.distance` z `gt` lub `ge` <br/><br/> `not geo.intersects(...)` <br/><br/> Łączenie wyrażeń podrzędnych za pomocą `and` |
| `Collection(Edm.DateTimeOffset)`, `Collection(Edm.Double)`, `Collection(Edm.Int32)`, `Collection(Edm.Int64)` | Porównania przy użyciu `eq`, `ne`, `lt`, `gt`, `le`, lub `ge` <br/><br/> Łącząc porównania z innych wyrażeń podrzędnych za pomocą `or` <br/><br/> Łącząc porównań, z wyjątkiem `ne` przy użyciu innych wyrażeń podrzędnych za pomocą `and` <br/><br/> Przy użyciu kombinacji wyrażeń `and` i `or` w [rozłącznego normalny formularza (DNF)](https://en.wikipedia.org/wiki/Disjunctive_normal_form) | Porównania przy użyciu `eq`, `ne`, `lt`, `gt`, `le`, lub `ge` <br/><br/> Łącząc porównania z innych wyrażeń podrzędnych za pomocą `and` <br/><br/> Łącząc porównań, z wyjątkiem `eq` przy użyciu innych wyrażeń podrzędnych za pomocą `or` <br/><br/> Przy użyciu kombinacji wyrażeń `and` i `or` w [opcją łącznej normalny formularza (CNF)](https://en.wikipedia.org/wiki/Conjunctive_normal_form) |
