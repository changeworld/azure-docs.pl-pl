---
title: Optymalizacja pamięci podręcznej Gen2
description: Dowiedz się, jak monitorować pamięć podręczną Gen2 za pomocą witryny Azure Portal.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: bb5560164af2b573e6aaffd4e4c62bbe0dc24a51
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350423"
---
# <a name="how-to-monitor-the-gen2-cache"></a>Jak monitorować pamięć podręczną Gen2

W tym artykule opisano sposób monitorowania i rozwiązywania problemów z powolną wydajnością zapytań, określając, czy obciążenie jest optymalnie wykorzystując pamięć podręczną Gen2.

Architektura magazynu Gen2 automatycznie warstwy najczęściej wybierane segmenty magazynu kolumn w pamięci podręcznej zamieszkałych na NVMe oparte dyski SSD przeznaczone dla magazynów danych Gen2. Większa wydajność jest realizowana, gdy zapytania pobierają segmenty, które znajdują się w pamięci podręcznej.
 
## <a name="troubleshoot-using-the-azure-portal"></a>Rozwiązywanie problemów podczas korzystania z witryny Azure portal

Za pomocą usługi Azure Monitor można wyświetlić metryki pamięci podręcznej Gen2 w celu rozwiązywania problemów z wydajnością kwerendy. Najpierw przejdź do witryny Azure portal i kliknij **Monitor**, **Metryki** i **+ Wybierz zakres:**

![Azure Monitor](./media/sql-data-warehouse-how-to-monitor-cache/cache-0.png)

Użyj pasków wyszukiwania i rozwijanych, aby zlokalizować magazyn danych. Następnie wybierz zastosuj.

![Azure Monitor](./media/sql-data-warehouse-how-to-monitor-cache/cache-1.png)

Kluczowe metryki rozwiązywania problemów z pamięcią podręczną Gen2 to **procent trafienia w pamięci podręcznej** i **procent użycia pamięci podręcznej.** Wybierz **opcję Procent trafienia w pamięci podręcznej,** a następnie użyj przycisku **dodaj metrykę,** aby dodać **wartość procentową używanej pamięci podręcznej**. 

![Metryki pamięci podręcznej](./media/sql-data-warehouse-how-to-monitor-cache/cache-2.png)

## <a name="cache-hit-and-used-percentage"></a>Wartość procentowa trafienia i użycia pamięci podręcznej

W poniższej macierzy opisano scenariusze oparte na wartościach metryk pamięci podręcznej:

|                                | **Procent trafienia w wysokiej pamięci podręcznej** | **Niska wartość procentowa trafień w pamięci podręcznej** |
| :----------------------------: | :---------------------------: | :--------------------------: |
| **Wysoka pamięć podręczna używana wartość procentowa** |          Scenariusz 1           |          Scenariusz 2          |
| **Niska wartość buforowa używana procentowa**  |          Scenariusz 3           |          Scenariusz 4          |

**Scenariusz 1:** Optymalnie korzystasz z pamięci podręcznej. [Rozwiązywanie problemów z](sql-data-warehouse-manage-monitor.md) innymi obszarami, które mogą spowalniać zapytania.

**Scenariusz 2:** Bieżący zestaw danych roboczych nie mieści się w pamięci podręcznej, co powoduje niski procent trafienia w pamięci podręcznej z powodu odczytów fizycznych. Należy wziąć pod uwagę skalowanie poziomu wydajności i ponownie uruchomić obciążenie, aby wypełnić pamięć podręczną.

**Scenariusz 3:** Jest prawdopodobne, że zapytanie działa wolno z przyczyn niezwiązanych z pamięci podręcznej. [Rozwiązywanie problemów z](sql-data-warehouse-manage-monitor.md) innymi obszarami, które mogą spowalniać zapytania. Można również rozważyć [skalowanie w dół wystąpienia,](sql-data-warehouse-manage-monitor.md) aby zmniejszyć rozmiar pamięci podręcznej, aby zaoszczędzić koszty. 

**Scenariusz 4:** Miałeś zimną pamięć podręczną, która może być powodem, dla którego zapytanie było powolne. Należy rozważyć ponowne uruchomienie kwerendy jako działający zestaw danych powinien być teraz w pamięci podręcznej. 

> [!IMPORTANT]
> Jeśli procent trafienia w pamięci podręcznej lub procent używanej pamięci podręcznej nie jest aktualizowany po ponownym zakończeniu obciążenia, zestaw roboczy może już znajdować się w pamięci. Buforowane są tylko tabele magazynu kolumn klastrowanych.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat ogólnego dostrajania wydajności kwerendy, zobacz [Monitorowanie wykonywania kwerendy](sql-data-warehouse-manage-monitor.md#monitor-query-execution).
