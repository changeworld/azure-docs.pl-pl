---
title: Optymalizuj czas wykonywania zapytania za pomocą strategii przechowywania tabeli wyskakującego powiadomienia w usłudze Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano, jak zoptymalizować czas wykonywania zapytania za pomocą strategii WYSKAKUJĄCE tabeli magazynu w usłudze Azure Database for PostgreSQL — pojedynczy serwer.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: ac1dc43a2b89bc1cc748947ec08e6ada87edbfcb
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65066971"
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