---
title: Optymalizowanie autovacuum w usłudze Azure Database dla serwera PostgreSQL
description: W tym artykule opisano, jak można optymalizować autovacuum w usłudze Azure Database for postgresql w warstwie serwera.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/22/2018
ms.openlocfilehash: 0f8db7dd3a90e06587a7e0e05f33cb6fba5c72e1
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2018
ms.locfileid: "53539793"
---
# <a name="optimizing-autovacuum-on-azure-database-for-postgresql-server"></a>Optymalizacja autovacuum w usłudze Azure Database dla serwera PostgreSQL 
W tym artykule opisano, jak skutecznie zoptymalizować autovacuum w usłudze Azure Database for PostgreSQL.

## <a name="overview-of-autovacuum"></a>Omówienie autovacuum
PostgreSQL używa MVCC, aby umożliwić większej współbieżności bazy danych. Każda aktualizacja powoduje insert i delete i delete, co skutkuje wiersze są elastyczne oznaczone do usunięcia. Oznaczanie nietrwałego powoduje identyfikacji martwy krotek, który ma zostać przeczyszczony później. PostgreSQL osiąga to wszystko, uruchamiając zadanie próżniowej.

Próżniowej zadania mogą być wyzwalane ręcznie lub automatycznie. Więcej martwych krotek będzie istnieć, gdy baza danych występuje duże aktualizacji lub usuń operacje i mniej podczas bezczynności.  Konieczność uruchamiania częściej odkurzający jest większy, gdy baza danych znajduje się pod dużym obciążeniem; co uruchomionych zadań próżniowej **ręcznie** nie można użyć.

Można skonfigurować Autovacuum i korzyści z dostrajania. Wartości domyślne, które PostgreSQL jest dostarczany z należy starać się produkt działa na wszelkiego rodzaju urządzeniami, takimi jak Raspberry instrukcje przetwarzania i optymalną konfigurację wartości są zależne od szeregu czynników:
- Łączna liczba zasobów dostępnych - jednostki SKU i rozmiaru magazynu.
- Użycie zasobów.
- Właściwości pojedynczego obiektu.

## <a name="autovacuum-benefits"></a>Autovacuum korzyści
Jeśli nie zostanie uruchomione odkurzający od czasu do czasu, może spowodować martwy krotek, które są gromadzone:
- Dane wybrzuszanie - większych baz danych i tabel.
- Większe nieoptymalne indeksów.
- Zwiększona we/wy.

## <a name="monitoring-bloat-with-autovacuum-queries"></a>Przeładowanie monitorowania za pomocą zapytań autovacuum
Następujące przykładowe zapytanie ma na celu określenie liczby martwe i aktywne krotek w tabeli o nazwie "XYZ": "Wybierz relname, n_dead_tup, n_live_tup, (n_dead_tup / n_live_tup) AS DeadTuplesRatio, last_vacuum, last_autovacuum FROM pg_catalog.pg_stat_all_tables relname gdzie ="XYZ"order przez n_dead_tup DESC;"

## <a name="autovacuum-configurations"></a>Konfiguracje Autovacuum
Parametry konfiguracji, które kontrolują autovacuum koncentrują się na dwa ważne pytania:
- Kiedy powinno zostać uruchomione?
- Ile należy go wyczyścić po uruchomieniu?

Poniżej niektóre autovacuum parametry konfiguracji, które mogą aktualizować opierają się na te pytania, wraz z wskazówek:
Parametr|Opis|Wartość domyślna
---|---|---
autovacuum_vacuum_threshold|Określa minimalną liczbę zaktualizowanych lub usuniętych krotek, wymagane do wyzwalania ODKURZAJĄCY w jednej tabeli. Wartość domyślna to 50 krotek. Ten parametr można ustawić tylko w pliku postgresql.conf lub w wierszu polecenia serwera. To ustawienie można przesłonić dla poszczególnych tabel, zmieniając parametry magazynu tabel.|50
autovacuum_vacuum_scale_factor|Określa część rozmiar tabeli, można dodać do autovacuum_vacuum_threshold przy podejmowaniu decyzji w celu wyzwolenia ODKURZAJĄCY. Wartość domyślna to 0.2 (20 procent rozmiaru tabeli). Ten parametr można ustawić tylko w pliku postgresql.conf lub w wierszu polecenia serwera. To ustawienie można przesłonić dla poszczególnych tabel, zmieniając parametry magazynu tabel.|5 procent
autovacuum_vacuum_cost_limit|Określa wartość limitu koszt, który będzie używany podczas automatycznej operacji ODKURZAJĄCY. Jeśli określono wartość -1, (jest to ustawienie domyślne), zostanie użyta wartość vacuum_cost_limit regularne. Wartość jest rozłożona proporcjonalnie uruchomionych procesów roboczych autovacuum, jeśli istnieje więcej niż jednego procesu roboczego. Suma granic dla każdego procesu roboczego nie może przekraczać wartość tej zmiennej. Ten parametr można ustawić tylko w pliku postgresql.conf lub w wierszu polecenia serwera. To ustawienie można przesłonić dla poszczególnych tabel, zmieniając parametry magazynu tabel.|-1
autovacuum_vacuum_cost_delay|Określa wartość opóźnienia kosztów, który będzie używany podczas automatycznej operacji ODKURZAJĄCY. Jeśli określono wartość -1, zostanie użyta wartość vacuum_cost_delay regularne. Wartość domyślna to 20 MS. Ten parametr można ustawić tylko w pliku postgresql.conf lub w wierszu polecenia serwera. To ustawienie można przesłonić dla poszczególnych tabel, zmieniając parametry magazynu tabel.|20 ms
autovacuum_nap_time|Określa, że minimalne opóźnienie między autovacuum działa na dowolnej określonej bazy danych. W każdej Rundy demona sprawdza, czy baza danych i wydaje polecenia ODKURZAJĄCY i ANALIZOWANIE, zgodnie z potrzebami dla tabel w tej bazie danych. Opóźnienie jest mierzony w sekundach, a wartość domyślna to jedna minuta (1 min). Ten parametr można ustawić tylko w pliku postgresql.conf lub w wierszu polecenia serwera.|15 s
autovacuum_max_workers|Określa maksymalną liczbę procesów autovacuum (inne niż uruchamiania autovacuum), które mogą być uruchomione jednocześnie. Wartość domyślna to trzy. Ten parametr można ustawić tylko podczas uruchamiania serwera.|3
Powyższe ustawienia można zastąpić dla poszczególnych tabel, zmieniając parametry magazynu tabel.  

## <a name="autovacuum-cost"></a>Koszt Autovacuum
Poniżej przedstawiono "koszty" próżniowej operacji:
- Blokady jest umieszczany na danych, których działa odkurzający stron.
- Zasobów obliczeniowych i pamięci są używane, gdy odkurzający jest uruchomiony.

Oznacza to, że odkurzający nie należy uruchomić albo zbyt często lub zbyt rzadko, musi on być adaptacyjne obciążenia. Zalecamy przeprowadzenie testowania wszystkie zmiany parametru autovacuum z powodu wady każdej z nich.

## <a name="autovacuum-start-trigger"></a>Autovacuum start wyzwalacza
Autovacuum jest wyzwalany, gdy liczba krotek martwy przekracza autovacuum_vacuum_threshold + autovacuum_vacuum_scale_factor * reltuples, w tym miejscu reltuples jest stałą.

Oczyszczenie z autovacuum należy na bieżąco z obciążeniem bazy danych, w przeciwnym razie może zabraknąć magazynu i środowisko ogólne spowolnienie w zapytaniach. Amortyzowanego wraz z upływem czasu, szybkość, z których odkurzający czyści dead krotek powinna być równa szybkość jaką są tworzone martwy krotek.

Bazy danych z wielu aktualizowania/usuwania mają więcej martwych krotek i potrzeba więcej miejsca. Ogólnie rzecz biorąc baz danych z wieloma aktualizowania/usuwania korzyści z niskich wartości autovacuum_vacuum_scale_factor i niskie wartości autovacuum_vacuum_threshold, aby zapobiec długotrwały gromadzeniu martwy krotek. Można użyć wyższe wartości obu parametrów za pomocą mniejszych baz danych, ponieważ potrzeby odkurzający są mniej pilne. Przypomnienie, że częste vacuuming pochodzi kosztem zasobów obliczeniowych i pamięci.

Domyślny współczynnik skali równy 20% działa dobrze w tabelach mających niskiej wartości martwy krotek, ale nie w tabelach mających wysoki procent martwy krotek. Na przykład w tabeli 20 GB, który przekłada się to 4 GB pamięci krotek martwe i w tabeli 1 TB jest 200 GB martwy krotek.

Z bazą danych PostgreSQL możesz ustawić te parametry na poziomie tabeli lub wystąpienia. Obecnie te parametry można ustawić na poziomie tabeli tylko w usłudze Azure Database for PostgreSQL.

## <a name="estimating-the-cost-of-autovacuum"></a>Szacowanie kosztów montażu autovacuum
Uruchomione autovacuum jest "kosztownych" andS istnieją parametry sterujące środowiska uruchomieniowego próżniowej operacji. Następujące parametry pomocy, Oszacuj koszt uruchamiania odkurzający:
- vacuum_cost_page_hit = 1
- vacuum_cost_page_miss = 10
- vacuum_cost_page_dirty = 20

Proces próżniowej odczytuje strony fizyczne i sprawdza, czy martwy krotek. Shared_buffers każdej strony została uznana za koszt 1 (vacuum_cost_page_hit), wszystkie strony są uznawane za żadnych opłat 20 (vacuum_cost_page_dirty), jeśli istnieje krotek nieużywany lub 10 (vacuum_cost_page_miss), jeśli istnieje nie martwy krotek. Próżniowej operacji zatrzymuje, gdy proces przekracza autovacuum_vacuum_cost_limit.  

Po osiągnięciu limitu proces przejścia w tryb uśpienia przez czas określony przez parametr autovacuum_vacuum_cost_delay przed ponownego uruchomienia. Jeśli nie zostanie osiągnięty limit, autovacuum rozpoczyna się po wartość określoną przez parametr autovacuum_nap_time.

Podsumowanie parametrów autovacuum_vacuum_cost_delay i autovacuum_vacuum_cost_limit kontrolować, ile oczyszczania jest dozwolony na jednostkę czasu. Należy pamiętać, że wartości domyślne są zbyt mała dla większości warstw cenowych. Optymalne wartości tych parametrów są ceny zależne od warstwy i powinien być odpowiednio skonfigurowany.

Parametr autovacuum_max_workers określa maksymalną liczbę procesów autovacuum, które mogą działać jednocześnie.

Z bazą danych PostgreSQL możesz ustawić te parametry na poziomie tabeli lub wystąpienia. Obecnie te parametry można ustawić na poziomie tabeli tylko w usłudze Azure Database for PostgreSQL.

## <a name="optimizing-autovacuum-per-table"></a>Optymalizacja autovacuum na tabelę
Powyższych parametrów konfiguracji może być skonfigurowany na tabelę, na przykład:
```sql
ALTER TABLE t SET (autovacuum_vacuum_threshold = 1000);
ALTER TABLE t SET (autovacuum_vacuum_scale_factor = 0.1);
ALTER TABLE t SET (autovacuum_vacuum_cost_limit = 1000);
ALTER TABLE t SET (autovacuum_vacuum_cost_delay = 10);
```

Jest Autovacuum na procesie synchronicznym tabeli. Większy procent martwy krotek tabeli ma większą "koszt" autovacuum.  Dzielenie tabel, które mają wysoki wskaźnik aktualizowania/usuwania na wiele tabel pomoże zrównoleglić autovacuum i zmniejszyć "koszt" Aby ukończyć autovacuum w odniesieniu do jednej tabeli. Można również zwiększyć liczbę równoległych autovacuum procesy robocze, aby upewnić się, że pracownicy liberally są zaplanowane.

## <a name="next-steps"></a>Kolejne kroki
Zapoznaj się z następujących documenatation PostgreSQL, aby dowiedzieć się więcej o używaniu i dostrajanie autovacuum:
 - Dokumentacja programu PostgreSQL — [rozdział 18, konfiguracja serwera](https://www.postgresql.org/docs/9.5/static/runtime-config-autovacuum.html)
 - Dokumentacja usługi PostgreSQL — [rozdziału 24, rutynowe zadania konserwacji bazy danych](https://www.postgresql.org/docs/9.6/static/routine-vacuuming.html)
