---
title: Jak odnajdować źródła danych w wykazie danych platformy Azure
description: W tym artykule przedstawiono sposób odnajdywania zarejestrowanych zasobów danych za pomocą usługi Azure Data Catalog, w tym wyszukiwania i filtrowania oraz korzystania z funkcji wyróżniania trafień w portalu usługi Azure Data Catalog.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: b12cb94832a1ea977fb13f5f2271984dc8780cee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68736373"
---
# <a name="how-to-discover-data-sources-in-azure-data-catalog"></a>Jak odnajdować źródła danych w wykazie danych platformy Azure

## <a name="introduction"></a>Wprowadzenie

Usługa Azure Data Catalog to w pełni zarządzana usługa w chmurze, która służy jako system rejestracji i odnajdowania dla źródeł danych przedsiębiorstwa. Innymi słowy, usługa Data Catalog pomaga użytkownikom wykrywać, rozumieć i używać źródeł danych. Pomaga organizacjom uzyskać większą wartość z istniejących danych. Po zarejestrowaniu źródła danych w wykazie danych jego metadane są indeksowane przez usługę, dzięki czemu można łatwo wyszukiwać w celu odnajdowania potrzebnych danych.

## <a name="searching-and-filtering"></a>Wyszukiwanie i filtrowanie danych

Odnajdowanie w wykazie danych używa dwóch podstawowych mechanizmów: wyszukiwania i filtrowania.

Wyszukiwanie zaprojektowano pod kątem intuicyjnej obsługi i zaawansowanych możliwości. Domyślnie wyszukiwane terminy są dopasowywane do wszystkich właściwości w wykazie, w tym adnotacji wprowadzonych przez użytkownika.

Filtrowanie służy jako uzupełnienie wyszukiwania. Można wybrać określone cechy, takie jak eksperci, typ źródła danych, typ obiektu i znaczniki. Można wyświetlać tylko pasujące zasoby danych i ograniczać wyniki wyszukiwania do pasujących zasobów.

Korzystając z kombinacji wyszukiwania i filtrowania, można szybko poruszać się po źródłach danych, które zostały zarejestrowane w wykazie danych, aby odnajdować potrzebne źródła danych.

## <a name="search-syntax"></a>Składnia wyszukiwania

Chociaż domyślne wyszukiwanie tekstu wolnego jest proste i intuicyjne, można również użyć składni wyszukiwania wykazu danych, aby uzyskać większą kontrolę nad wynikami wyszukiwania. Wyszukiwanie wykazu danych obsługuje następujące techniki:

| Technika | Użycie | Przykład |
| --- | --- | --- |
| Wyszukiwanie podstawowe |Wyszukiwanie podstawowe, które używa jednego lub więcej wyszukiwanych haseł. Wyniki są wszystkie zasoby, które pasują do dowolnej właściwości z co najmniej jednym z określonych warunków. |`sales data` |
| Zakres właściwości |Zwracanie tylko źródeł danych, w których wyszukiwany termin jest dopasowany do określonej właściwości. |`name:finance` |
| Operatory logiczne |Poszerz lub zawęź wyszukiwanie przy użyciu operacji logicznych. |`finance NOT corporate` |
| Grupowanie za pomocą nawiasu |Użyj nawiasów do grupowanie części kwerendy, aby osiągnąć izolację logiczną, szczególnie w połączeniu z operatorami logicznymi. |`name:finance AND (tags:Q1 OR tags:Q2)` |
| Operatory porównania |Użyj porównań innych niż równość dla właściwości, które mają typy danych liczbowych i dat. |`modifiedTime > "11/05/2014"` |

Aby uzyskać więcej informacji na temat wyszukiwania wykazu danych, zobacz artykuł [usługi Azure Data Catalog.](/rest/api/datacatalog/#search-syntax-reference)

## <a name="hit-highlighting"></a>Wyróżnianie trafień

Podczas wyświetlania wyników wyszukiwania wszystkie wyświetlane właściwości zgodne z określonymi terminami wyszukiwania (takie jak nazwa, opis i tagi zasobu danych) są wyróżnione, aby ułatwić identyfikację, dlaczego dany zasób danych został zwrócony przez dane wyszukiwania.

> [!NOTE]
> Aby wyłączyć wyróżnianie trafień, użyj przełącznika **Wyróżnij** w portalu wykazu danych.

Podczas wyświetlania wyników wyszukiwania nie zawsze jest oczywiste, dlaczego zasób danych jest uwzględniony, nawet przy włączonym wyróżnianiu trafień. Ponieważ wszystkie właściwości są przeszukiwane domyślnie, zasób danych może zostać zwrócony z powodu dopasowania we właściwości na poziomie kolumny. Ponieważ wielu użytkowników może dodawać adnotacje do zarejestrowanych zasobów danych za pomocą własnych tagów i opisów, nie wszystkie metadane są wyświetlane na liście wyników wyszukiwania.

W domyślnym widoku kafelka wyświetlanego w wynikach wyszukiwania zawiera ikonę **Wyświetl wyszukiwane terminy** dopasowania, dzięki czemu można szybko wyświetlić liczbę dopasowań i ich lokalizację oraz przejść do nich, jeśli chcesz.

 ![Naciśnięcie najważniejszych i wyszukiwania dopasowań w portalu usługi Azure Data Catalog](./media/data-catalog-how-to-discover/search-matches.png)

## <a name="summary"></a>Podsumowanie

Ponieważ rejestrowanie źródła danych w wykazie danych kopiuje metadane strukturalne i opisowe ze źródła danych do usługi katalogu, źródło danych staje się łatwiejsze do odnajdywania i zrozumienia. Po zarejestrowaniu źródła danych można je odnajdować, korzystając z filtrowania i wyszukiwania z poziomu portalu wykazu danych.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać szczegółowe informacje krok po kroku dotyczące odnajdywanie źródeł danych, zobacz Wprowadzenie do [usługi Azure Data Catalog](data-catalog-get-started.md).
