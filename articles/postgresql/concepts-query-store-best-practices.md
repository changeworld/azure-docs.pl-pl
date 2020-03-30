---
title: Najważniejsze wskazówki dotyczące magazynu zapytań w usłudze Azure Database for PostgreSQL — single server
description: W tym artykule opisano najlepsze rozwiązania dla magazynu zapytań w usłudze Azure Database dla postgreSQL — pojedynczy serwer.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 51239f4cf49784dd47470e1272b90508eaf25e6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70764231"
---
# <a name="best-practices-for-query-store"></a>Najważniejsze wskazówki dotyczące Magazynu zapytań

**Dotyczy:** Usługa Azure Database for PostgreSQL — pojedynczy serwer w wersjach 9.6, 10, 11

W tym artykule opisano najlepsze rozwiązania dotyczące korzystania z magazynu zapytań w usłudze Azure Database dla postgreSQL.

## <a name="set-the-optimal-query-capture-mode"></a>Ustawianie optymalnego trybu przechwytywania zapytań
Pozwól Query Store przechwycić dane, które są dla Ciebie ważne. 

|**pg_qs.query_capture_mode** | **Scenariusz**|
|---|---|
|_Wszystkie_  |Dokładnie analizuj obciążenie pracą pod kątem wszystkich zapytań i ich częstotliwości wykonywania i innych statystyk. Identyfikowanie nowych zapytań w obciążeniu. Wykryj, czy kwerendy ad hoc są używane do identyfikowania szans sprzedaży przez użytkownika lub automatycznego parametryzacji. _Wszystko_ to wiąże się ze zwiększonym kosztem zużycia zasobów. |
|_Do góry_  |Skoncentruj swoją uwagę na najlepszych zapytaniach - tych wystawianych przez klientów.
|_Brak_ |Masz już przechwycone zestaw zapytań i przedział czasu, które chcesz zbadać i chcesz wyeliminować zakłócenia, które mogą wprowadzać inne kwerendy. _Żaden z nich nie_ nadaje się do testowania i znakowania na ławce. _Brak_ należy używać z ostrożnością, jak można przegapić możliwość śledzenia i optymalizacji ważnych nowych zapytań. Nie można odzyskać danych w tych oknach czasu przeszłych. |

Query Store zawiera również magazyn statystyk oczekiwania. Istnieje dodatkowa kwerenda trybu przechwytywania, która reguluje statystyki oczekiwania: **pgms_wait_sampling.query_capture_mode** można ustawić na _brak_ lub _wszystkie_. 

> [!NOTE] 
> **pg_qs.query_capture_mode** zastępuje **pgms_wait_sampling.query_capture_mode**. Jeśli pg_qs.query_capture_mode nie ma _żadnego,_ ustawienie pgms_wait_sampling.query_capture_mode nie ma wpływu. 


## <a name="keep-the-data-you-need"></a>Zachowaj potrzebne dane
Parametr **pg_qs.retention_period_in_days** określa w dniach okres przechowywania danych dla Magazynu zapytań. Starsze dane kwerendy i statystyki zostaną usunięte. Domyślnie Query Store jest skonfigurowany do przechowywania danych przez 7 dni. Unikaj przechowywania danych historycznych, których nie zamierzasz używać. Zwiększ wartość, jeśli chcesz zachować dane dłużej.


## <a name="set-the-frequency-of-wait-stats-sampling"></a>Ustawianie częstotliwości próbkowania statystyk oczekiwania 
Parametr **pgms_wait_sampling.history_period** określa częstotliwość próbkowania zdarzeń oczekiwania (w milisekundach). Im krótszy okres, tym częstsze pobieranie próbek. Pobiera się więcej informacji, ale wiąże się to z kosztem większego zużycia zasobów. Zwiększ ten okres, jeśli serwer jest pod obciążeniem lub nie potrzebujesz szczegółowości


## <a name="get-quick-insights-into-query-store"></a>Szybkie szczegółowe informacje o Magazynie zapytań
Wgląd wydajności [kwerend w](concepts-query-performance-insight.md) witrynie Azure portal można użyć, aby uzyskać szybki wgląd w dane w Magazynie zapytań. Wizualizacje powierzchni najdłużej działających zapytań i najdłuższe zdarzenia oczekiwania w czasie.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak uzyskać lub ustawić parametry za pomocą [witryny Azure portal](howto-configure-server-parameters-using-portal.md) lub [interfejsu wiersza polecenia platformy Azure.](howto-configure-server-parameters-using-cli.md)