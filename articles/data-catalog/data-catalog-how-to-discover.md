---
title: Jak odnajdować źródła danych w usłudze Azure Data Catalog
description: W tym artykule opisano sposób odnajdowania zarejestrowanych zasobów danych za pomocą usługi Azure Data Catalog, w tym wyszukiwanie, filtrowanie i przy użyciu funkcji wyróżnianie trafień portalu usługi Azure Data Catalog.
services: data-catalog
author: steelanddata
ms.author: maroche
ms.assetid: f72ae3a3-6573-4710-89a7-f13555e1968c
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: f8e722073db967752747511e47921aec3c4a281a
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2018
ms.locfileid: "43053705"
---
# <a name="how-to-discover-data-sources-in-azure-data-catalog"></a>Jak odnajdować źródła danych w usłudze Azure Data Catalog
## <a name="introduction"></a>Wprowadzenie
Azure Data Catalog to w pełni zarządzana usługa w chmurze służąca jako system rejestracji i odnajdywania firmowych źródeł danych. Innymi słowy Data Catalog ułatwia odnajdywanie, zrozumienie i używanie źródeł danych, co zapewnia organizacjom osiąganie większych zysków z ich istniejących danych. Po zarejestrowaniu źródła danych z usługą Data Catalog jego metadane są indeksowane przez usługę, dzięki czemu można łatwo przeszukiwać odnajdywania danych, których potrzebujesz.

## <a name="searching-and-filtering"></a>Wyszukiwanie i filtrowanie danych
Funkcja odnajdowania w usłudze Data Catalog korzysta z dwóch głównych mechanizmów: wyszukiwania i filtrowania.

Wyszukiwanie zaprojektowano pod kątem intuicyjnej obsługi i zaawansowanych możliwości. Domyślnie wyszukiwane terminy są dopasowywane do wszystkich właściwości w wykazie, w tym adnotacji wprowadzonych przez użytkownika.

Filtrowanie służy jako uzupełnienie wyszukiwania. Możesz wybrać określone cechy, takie jak eksperci, typ źródła danych, typ obiektu i tagi. Możesz wyświetlić tylko pasujące zasoby danych i ograniczyć wyniki wyszukiwania do tych zasobów.

Dzięki połączeniu wyszukiwania i filtrowania, można szybko nawigować źródeł danych, które zostały zarejestrowane w usłudze Data Catalog odnajdować źródła danych, których potrzebujesz.

## <a name="search-syntax"></a>Wyszukiwanie w składni
Mimo że domyślne wyszukiwanie dowolnego tekstu jest proste i intuicyjne, umożliwia także składnię wyszukiwania wykazu danych lepszą kontrolę nad wynikami wyszukiwania. Wyszukiwanie w wykazie danych obsługuje następujących technik:

| Technika | Użycie | Przykład |
| --- | --- | --- |
| Wyszukiwanie podstawowe |Wyszukiwania podstawowego, który używa co najmniej jednego wyszukiwanego terminu. Wyniki są wszystkie zasoby, które pasuje do żadnej właściwości z co najmniej jednym terminem. |`sales data` |
| Wyznaczanie zakresu właściwości |Zwróć tylko źródła danych, gdy wyszukiwany termin zostanie dopasowany do określonej właściwości. |`name:finance` |
| Operatory logiczne |Poszerzają lub zawężają wyszukiwanie przy użyciu operacji logicznych. |`finance NOT corporate` |
| Grupowanie za pomocą nawiasów |Użyj nawiasów do grupowania części zapytania w celu uzyskania izolacji logicznej, szczególnie w połączeniu z operatorami logicznymi. |`name:finance AND (tags:Q1 OR tags:Q2)` |
| Operatory porównania |Używać innych porównań niż równość dla właściwości, które mają numeryczne i datowe typy danych. |`modifiedTime > "11/05/2014"` |

Aby uzyskać więcej informacji na temat wyszukiwania wykazu danych, zobacz [usługi Azure Data Catalog](https://msdn.microsoft.com/library/azure/mt267594.aspx) artykułu.

## <a name="hit-highlighting"></a>Wyróżnianie trafień
Podczas wyświetlania wyników wyszukiwania, wszystkie wyświetlone właściwości, które pasują do określonych kryteriów wyszukiwania (takich jak nazwa zasobu danych, opis i tagi), zostały wyróżnione ułatwiają łatwiejszy do zidentyfikowania, dlatego trwałego danych zwróciła podanym wyszukiwaniem.

> [!NOTE]
> Aby wyłączyć wyróżnianie trafień, należy użyć **wyróżnić** przełącznika w portalu usługi Data Catalog.
>
>

Podczas wyświetlania wyników wyszukiwania, go nie zawsze jest oczywiste Dlaczego zasobu danych są uwzględnione, nawet w przypadku włączone wyróżnianie trafień. Ponieważ domyślnie przeszukiwane są wszystkie właściwości, może zostać zwrócona do zasobu danych ze względu na dopasowanie we właściwości na poziomie kolumny. A ponieważ wielu użytkowników może dodawać adnotacje zarejestrowanych zasobów danych za pomocą własnych tagów i opisy, nie wszystkie metadane mogą być wyświetlane na liście wyników wyszukiwania.

Widok kafelków w domyślnym, każdy Kafelek wyświetlony w wynikach wyszukiwania zawiera **widoku wyszukiwany termin będzie zgodny** ikony, dzięki czemu można szybko wyświetlić liczbę dopasowań i ich lokalizacji i przejść do nich, jeśli chcesz.

 ![Wyróżnianie trafień i pasuje do wyszukiwania w portalu usługi Azure Data Catalog](./media/data-catalog-how-to-discover/search-matches.png)

## <a name="summary"></a>Podsumowanie
Ponieważ rejestrowanie źródła danych z usługą Data Catalog kopiuje metadane strukturalne i opisowy ze źródła danych do usługi katalogu, źródła danych staje się prostsze do odnalezienia i zrozumienia. Po zarejestrowaniu źródła danych, możesz je odnajdą za pomocą filtrowania i wyszukiwać z poziomu portalu usługi Data Catalog.

## <a name="next-steps"></a>Kolejne kroki
* Aby uzyskać instrukcje krok po kroku szczegółowe informacje o tym, jak odnajdować źródła danych, zobacz [Rozpoczynanie pracy z usługą Azure Data Catalog](data-catalog-get-started.md).
