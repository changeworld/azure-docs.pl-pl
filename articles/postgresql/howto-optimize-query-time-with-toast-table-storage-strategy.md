---
title: Optymalizuj czas kwerendy przy użyciu strategii magazynu tabel TOAST w usłudze Azure Database for PostgreSQL — single server
description: W tym artykule opisano sposób optymalizacji czasu kwerendy za pomocą strategii magazynu tabel TOAST w bazie danych azure dla postgreSQL — pojedynczy serwer.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: ac1dc43a2b89bc1cc748947ec08e6ada87edbfcb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65066971"
---
# <a name="optimize-query-time-with-the-toast-table-storage-strategy"></a>Optymalizuj czas kwerendy dzięki strategii przechowywania tabel TOAST 
W tym artykule opisano sposób optymalizacji czasów kwerendy za pomocą strategii magazynowania tabel z atrybutami przewymiarowanymi (TOAST).

## <a name="toast-table-storage-strategies"></a>Strategie przechowywania stołowych TOAST
Cztery różne strategie są używane do przechowywania kolumn na dysku, które mogą używać TOAST. Reprezentują one różne kombinacje między kompresją i magazynem poza linią. Strategię można ustawić na poziomie typu danych i na poziomie kolumny.
- **Zwykły** zapobiega kompresji lub pozawierszowej pamięci masowej. Wyłącza użycie nagłówków jedno bajtowych dla typów varlena. Zwykły jest jedyną możliwą strategią dla kolumn typów danych, które nie mogą używać TOAST.
- **Rozszerzony** umożliwia zarówno kompresję, jak i przechowywanie poza linią. Rozszerzony jest ustawieniem domyślnym dla większości typów danych, które mogą używać TOAST. Najpierw próbowano kompresję. Jest podejmowana próba magazynu poza wierszem, jeśli wiersz jest nadal zbyt duży.
- **Zewnętrzna** umożliwia przechowywanie poza linią, ale nie kompresji. Użycie zewnętrznego sprawia, że operacje podciągów na szerokich kolumnach tekstowych i bajtowych są szybsze. Ta prędkość wiąże się z karą zwiększenia przestrzeni dyskowej. Te operacje są zoptymalizowane do pobierania tylko wymagane części wartości poza wierszem, gdy nie jest skompresowany.
- **Main** umożliwia kompresję, ale nie poza linię pamięci masowej. Magazyn poza wierszem jest nadal wykonywany dla takich kolumn, ale tylko w ostateczności. Występuje, gdy nie ma innego sposobu, aby wiersz wystarczająco mały, aby zmieścić się na stronie.

## <a name="use-toast-table-storage-strategies"></a>Korzystanie ze strategii przechowywania tabel TOAST
Jeśli kwerendy uzyskują dostęp do typów danych, które można użyć TOAST, należy rozważyć użycie strategii głównej zamiast domyślnej opcji rozszerzonej, aby skrócić czas kwerendy. Main nie wyklucza przechowywania poza linią. Jeśli kwerendy nie mają dostępu do typów danych, które mogą używać TOAST, może być korzystne, aby zachować rozszerzoną opcję. Większa część wierszy tabeli głównej mieści się w pamięci podręcznej buforu udostępnionego, co pomaga w wydajności.

Jeśli masz obciążenie, które używa schematu z szerokimi tabelami i wysoką liczbą znaków, należy rozważyć użycie tabel POSTGRESQL TOAST. Przykładowa tabela klientów miała więcej niż 350 kolumn z kilkoma kolumnami, które obejmowały 255 znaków. Po przekonwertowaniu na tabelę TOAST Główna strategia, ich czas zapytania porównawczego skrócono z 4203 sekund do 467 sekund. To poprawa o 89 procent.

## <a name="next-steps"></a>Następne kroki
Przejrzyj obciążenie dla poprzednich cech. 

Zapoznaj się z następującą dokumentacją PostgreSQL: 
- [Rozdział 68, Pamięć fizyczna bazy danych](https://www.postgresql.org/docs/current/storage-toast.html) 