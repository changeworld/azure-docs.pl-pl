---
title: Optymalizuj czas kwerendy w usłudze Azure Database dla serwera PostgreSQL za pomocą strategii przechowywania tabeli WYSKAKUJĄCE
description: W tym artykule opisano, jak zoptymalizować przeszukiwania ze strategią WYSKAKUJĄCE tabeli magazynu w usłudze Azure Database dla serwera PostgreSQL.
author: WenJason
ms.author: v-jay
editor: jasonwhowell
ms.service: postgresql
ms.topic: conceptual
origin.date: 10/22/2018
ms.date: 02/18/2019
ms.openlocfilehash: 96793cb1785a7ffa86331285f401453641b50dac
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60421060"
---
# <a name="optimize-query-time-with-the-toast-table-storage-strategy"></a>Optymalizuj czas wykonywania zapytania za pomocą strategii przechowywania tabeli WYSKAKUJĄCE 
W tym artykule opisano, jak zoptymalizować czas zapytania za pomocą strategii przechowywania magazynu atrybutu zwiększony technika (WYSKAKUJĄCE) tabeli.

## <a name="toast-table-storage-strategies"></a>Strategie magazynu tabeli WYSKAKUJĄCE
Cztery różne strategie są używane do przechowywania kolumn na dysku, którego można użyć wyskakującego powiadomienia. Reprezentują one różne kombinacje między kompresji i storage końca wiersza. Strategia można ustawić na poziomie typu danych i na poziomie kolumny.
- **Zwykły** zapobiega magazynu kompresji lub końca wiersza. Jego wyłącza przyciski regulacji jednobajtowe nagłówki dla typów varlena. Zwykły jest jedyną możliwą strategii dla kolumn typów danych, które nie mogą używać wyskakującego powiadomienia.
- **Rozszerzony** umożliwia magazynu kompresję i końca wiersza. Rozszerzony jest ustawieniem domyślnym dla większości typów danych, które można użyć wyskakującego powiadomienia. Kompresja nastąpi próba. Magazyn końca wiersza zostanie podjęta, jeśli wiersz nadal jest zbyt duży.
- **Zewnętrzne** umożliwia magazyn końca wiersza, ale nie kompresji. Korzystanie z zewnętrznych sprawia, że operacje podciągu na szerokie tekstu i bytea kolumny szybciej. Szybkość tych operacji jest powiązana z spadek zwiększoną ilość miejsca. Te operacje są zoptymalizowane do pobrania wymagane części wartości typu końca wiersza, gdy nie jest skompresowany.
- **Main** umożliwia kompresję, ale Magazyn nie poza wierszem. Magazyn końca wiersza jest nadal wykonywane w przypadku takich kolumn, ale tylko w ostateczności. Nastąpi to po nie inny sposób czynią wystarczająco mała, aby zmieściły się na stronie.

## <a name="use-toast-table-storage-strategies"></a>Użyj strategii magazynu tabeli WYSKAKUJĄCE
Jeśli zapytania dostęp do typów danych, które można użyć WYSKAKUJĄCE, rozważ użycie strategii Main zamiast domyślnej opcji rozszerzonej zmniejszyć czas zapytania. Główny nie wykluczyć magazynu końca wiersza. Zapytania nie uzyskać dostęp do typów danych, które można użyć WYSKAKUJĄCE, może być korzystne Zachowaj opcję rozszerzonej. Większą część wiersze tabeli głównej mieści się w pamięci podręcznej buforu, która pomaga wydajności.

W przypadku obciążenia, który używa schematu z szerokich tabel i liczbę znaków o wysokiej należy rozważyć użycie tabel WYSKAKUJĄCE PostgreSQL. Przykład tabeli klientów ma większy niż 350 kolumn zawierających wiele kolumn, które liczbę 255 znaków. Po został skonwertowany do tabeli WYSKAKUJĄCE strategii Main, ich czas wykonywania zapytania testu wydajności zredukowana od 4203 sekund 467 sekund. To poprawę 89 procent.

## <a name="next-steps"></a>Kolejne kroki
Przejrzyj poprzednie charakterystyki obciążenia. 

Zapoznaj się z dokumentacją dotyczącą następujących PostgreSQL: 
- [68 rozdział, bazy danych magazynu fizycznego](https://www.postgresql.org/docs/current/storage-toast.html) 