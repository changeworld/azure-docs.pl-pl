---
title: Optymalizuj czas kwerendy w usłudze Azure Database for PostgreSQL przy użyciu strategię magazynowania tabeli wyskakujące
description: W tym artykule opisano, jak zoptymalizować czas kwerendy za pomocą strategii przechowywania tabeli wyskakującego powiadomienia w usłudze Azure Database dla serwera PostgreSQL.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/22/2018
ms.openlocfilehash: 1fb818a65e26f969f72131b0f5265f3efdd36bb6
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2018
ms.locfileid: "53542224"
---
# <a name="optimizing-query-time-with-toast-table-storage-strategy"></a>Optymalizacja czas wykonywania zapytania za pomocą strategii przechowywania tabeli WYSKAKUJĄCE 
W tym artykule opisano, jak zoptymalizować czas zapytania za pomocą strategii przechowywania tabeli wyskakującego powiadomienia.

## <a name="toast-table-storage-strategies"></a>Strategie magazynu tabeli WYSKAKUJĄCE
Istnieją cztery różne strategie do przechowywania stanie wyskakujące kolumn na dysku, reprezentująca różne kombinacje między kompresji i storage końca wiersza. Strategia można ustawić na poziomie typu danych i na poziomie kolumny.
- **Zwykły** zapobiega kompresji lub magazyn końca wiersza; Ponadto powoduje wyłączenie na użytek nagłówków jednobajtowe varlena typów. **Zwykły** jest jedyną możliwą strategii dla kolumn typy danych nie może wyskakujące.
- **Rozszerzony** umożliwia magazynu kompresję i końca wiersza. **Rozszerzony** jest ustawieniem domyślnym dla większości typów danych może wyskakującego powiadomienia. Kompresja będzie próba magazynu pierwszy, a następnie końca wiersza, jeśli wiersz nadal jest zbyt duży.
- **Zewnętrzne** umożliwia magazyn końca wiersza, ale nie kompresji. Korzystanie z **zewnętrznych** spowoduje, że operacje podciągu na szerokości tekstu i kolumn bytea szybciej, w spadek zwiększoną ilość miejsca, ponieważ te operacje są optymalizowane w celu pobrania tylko wymagane części poza wiersz wartości, gdy go nie jest skompresowany.
- **Main** umożliwia kompresję, ale Magazyn nie poza wierszem. Magazynu końca wiersza będą nadal wykonywane dla takich kolumn, ale tylko w ostateczności po nie inny sposób czynią wystarczająco mała, aby zmieściły się na stronie.

## <a name="using-toast-table-storage-strategies"></a>Przy użyciu wyskakującego powiadomienia tabeli magazynu strategii
Jeśli zapytania dostępu do typów danych może wyskakujące, należy wziąć pod uwagę przy użyciu **Main** zamiast domyślnego **Extended** opcję, aby zmniejszyć czas zapytania. **Main** nie wyklucza magazynu końca wiersza. Z drugiej strony, jeśli zapytań nie uzyskać dostępu do typów danych może wyskakujące, może być korzystne zachować **Extended** opcji. Dlatego większą część wiersze tabeli głównej zmieści się w pamięci podręcznej buforu, pomagając wydajności.

W przypadku obciążenia przy użyciu schematu z szerokich tabel i liczby znaków wysokiej należy rozważyć użycie tabel WYSKAKUJĄCE PostgreSQL. Przykład tabeli klientów ma większy niż 350 kolumn z kilku kolumn obejmujące 255 znaków. Ich czas wykonywania zapytania testów porównawczych zmniejszonej z 4203 sekund do sekund 467, poprawę procent 89 po przekonwertowaniu strategii WYSKAKUJĄCE **Main**.

## <a name="next-steps"></a>Kolejne kroki
Przejrzyj swoje obciążenia na powyższej właściwości. 

Zapoznaj się z dokumentacją dotyczącą następujących PostgreSQL: [68 rozdział, bazy danych magazynu fizycznego](https://www.postgresql.org/docs/current/storage-toast.html) 