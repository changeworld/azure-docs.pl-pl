---
title: Optymalizowanie autovacuum na usługi Azure Database for postgresql w warstwie serwera
description: W tym artykule opisano, jak można optymalizować autovacuum na usługi Azure Database for postgresql w warstwie serwera.
author: WenJason
ms.author: v-jay
ms.service: postgresql
ms.topic: conceptual
origin.date: 10/22/2018
ms.date: 04/29/2019
ms.openlocfilehash: e1b4bf1f9fa956da7a7b0ca1521439002d1ce76b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60420274"
---
# <a name="optimize-autovacuum-on-an-azure-database-for-postgresql-server"></a>Optymalizowanie autovacuum na usługi Azure Database for postgresql w warstwie serwera 
W tym artykule opisano, jak skutecznie zoptymalizować autovacuum na usługi Azure Database for postgresql w warstwie serwera.

## <a name="overview-of-autovacuum"></a>Omówienie autovacuum
PostgreSQL używa mechanizmu kontroli współbieżności rozpoznającego wiele wersji (MVCC), aby umożliwić większej współbieżności bazy danych. Każda aktualizacja powoduje insert i delete i delete, co skutkuje wiersze są elastyczne oznaczone do usunięcia. Oznaczanie nietrwałego identyfikuje martwy krotek, który ma zostać przeczyszczony później. Aby wykonać te zadania, postgresql w warstwie uruchamia zadanie próżniowej.

Próżniowej zadania mogą być wyzwalane ręcznie lub automatycznie. Więcej martwych krotek istnieć, gdy baza danych napotyka dużych aktualizacji lub usuń operacje. Mniej krotek martwy istnieć, gdy baza danych jest w stanie bezczynności. Musisz próżniowe częściej, obciążenie bazy danych po bardzo duże, co sprawia, że uruchomione zadania próżniowej *ręcznie* nie można użyć.

Można skonfigurować Autovacuum i korzyści z dostrajania. Wartości domyślne, które PostgreSQL jest dostarczany z spróbuj upewnij się, że produkt działa we wszystkich rodzajach urządzeń. Do tych urządzeń należą urządzenia Raspberry instrukcje przetwarzania. Wartości optymalną konfigurację zależą od:
- Łączna liczba dostępnych zasobów, takich jak rozmiar jednostki SKU i magazynu.
- Użycie zasobów.
- Właściwości pojedynczego obiektu.

## <a name="autovacuum-benefits"></a>Autovacuum korzyści
Jeśli nie próżniowe od czasu do czasu, może spowodować martwy krotek, które są gromadzone:
- Wybrzuszanie danych, takich jak większych baz danych i tabel.
- Większe nieoptymalne indeksów.
- Zwiększona we/wy.

## <a name="monitor-bloat-with-autovacuum-queries"></a>Monitor rozrostu autovacuum zapytań
Następujące przykładowe zapytanie ma na celu określenie liczby martwe i aktywne krotek w tabeli o nazwie XYZ:
 
    'SELECT relname, n_dead_tup, n_live_tup, (n_dead_tup/ n_live_tup) AS DeadTuplesRatio, last_vacuum, last_autovacuum FROM pg_catalog.pg_stat_all_tables WHERE relname = 'XYZ' order by n_dead_tup DESC;'

## <a name="autovacuum-configurations"></a>Konfiguracje Autovacuum
Parametry konfiguracji, które kontrolują autovacuum są oparte na odpowiedzi na dwa ważne pytania:
- Kiedy powinno zostać uruchomione?
- Ile należy go wyczyścić po uruchomieniu?

Poniżej przedstawiono niektóre autovacuum konfiguracji, parametry, które mogą aktualizować zależnie od odpowiedzi na powyższe pytania, wraz z wskazówki.

Parametr|Opis|Wartość domyślna
---|---|---
autovacuum_vacuum_threshold|Określa minimalną liczbę zaktualizowanych lub usuniętych krotek, wymagane do wyzwalania próżniowej operacji w jednej tabeli. Wartość domyślna to 50 krotek. Ustaw ten parametr, tylko w pliku postgresql.conf lub w wierszu polecenia serwera. Aby zastąpić to ustawienie dla poszczególnych tabel, Zmień parametry magazynu tabel.|50
autovacuum_vacuum_scale_factor|Określa część rozmiar tabeli, można dodać do autovacuum_vacuum_threshold, podejmując decyzję o wyzwolić próżniowej operacji. Wartość domyślna to 0,2, czyli 20 procent rozmiaru tabeli. Ustaw ten parametr, tylko w pliku postgresql.conf lub w wierszu polecenia serwera. Aby zastąpić to ustawienie dla poszczególnych tabel, Zmień parametry magazynu tabel.|5 procent
autovacuum_vacuum_cost_limit|Określa wartość limitu kosztów, które są używane podczas automatycznej operacji próżniowej. Jeśli wartość -1 jest określony, co jest ustawieniem domyślnym, wartość vacuum_cost_limit regularne jest używana. Jeśli istnieje więcej niż jednego procesu roboczego, wartość jest rozłożona proporcjonalnie uruchomionych procesów roboczych autovacuum. Suma granic dla każdego procesu roboczego nie może przekraczać wartość tej zmiennej. Ustaw ten parametr, tylko w pliku postgresql.conf lub w wierszu polecenia serwera. Aby zastąpić to ustawienie dla poszczególnych tabel, Zmień parametry magazynu tabel.|-1
autovacuum_vacuum_cost_delay|Określa wartość opóźnienia kosztów w operacjach próżniowej automatyczne. Jeśli określono wartość -1, wartość vacuum_cost_delay regularne jest używana. Wartość domyślna to 20 MS. Ustaw ten parametr, tylko w pliku postgresql.conf lub w wierszu polecenia serwera. Aby zastąpić to ustawienie dla poszczególnych tabel, Zmień parametry magazynu tabel.|20 ms
autovacuum_nap_time|Określa, że minimalne opóźnienie między autovacuum działa na dowolnej określonej bazy danych. W każdej Rundy demona sprawdza, czy baza danych i wydaje polecenia ODKURZAJĄCY i ANALIZOWANIE, zgodnie z potrzebami dla tabel w tej bazie danych. Opóźnienie jest mierzony w sekundach, a wartość domyślna to jedna minuta (1 min). Ustaw ten parametr, tylko w pliku postgresql.conf lub w wierszu polecenia serwera.|15 s
autovacuum_max_workers|Określa maksymalną liczbę procesów autovacuum, innym niż uruchamiania autovacuum, która może działać w dowolnym momencie. Wartość domyślna to trzy. Ustaw ten parametr, tylko podczas uruchamiania serwera.|3

Aby zastąpić ustawienia dla poszczególnych tabel, Zmień parametry magazynu tabel. 

## <a name="autovacuum-cost"></a>Koszt Autovacuum
Poniżej przedstawiono "koszty" próżniowej operacji:

- Strony danych, których działa odkurzający są zablokowane.
- Zasobów obliczeniowych i pamięci są używane, gdy próżniowej zadanie jest uruchomione.

Co w efekcie nie uruchamiaj zadania próżniowej albo zbyt często lub zbyt rzadko. Próżniowej zadania musi dostosowanie się do obciążenia. Przetestuj wszystkie zmiany parametru autovacuum z powodu wady każdej z nich.

## <a name="autovacuum-start-trigger"></a>Autovacuum start wyzwalacza
Autovacuum jest wyzwalany, gdy liczba krotek martwy przekracza autovacuum_vacuum_threshold + autovacuum_vacuum_scale_factor * reltuples. W tym miejscu reltuples jest stałą.

Oczyszczenie z autovacuum musi przechowywać z obciążeniem bazy danych. W przeciwnym razie może zabraknąć magazynu i środowisko ogólne spowolnienie w zapytaniach. Amortyzowanego wraz z upływem czasu, szybkość jaką próżniowej operacji czyści martwy krotek powinna być równa szybkość jaką są tworzone martwy krotek.

Bazy danych z wielu aktualizacji i usunięć mają więcej martwych krotek i potrzeba więcej miejsca. Ogólnie rzecz biorąc baz danych z wieloma aktualizuje i usuwa korzyści z niskich wartości autovacuum_vacuum_scale_factor i autovacuum_vacuum_threshold. Niskich wartości uniemożliwiają długotrwały akumulacja martwy krotek. Wyższe wartości można użyć dla obu parametrów z mniejszych baz danych, ponieważ potrzeby vacuuming są mniej pilne. Częste vacuuming pochodzi kosztem zasobów obliczeniowych i pamięci.

Domyślny współczynnik skali równy 20% działa dobrze w tabelach z niską wartość procentową martwy krotek. Nie działa dobrze w tabelach mających wysoki odsetek martwy krotek. Na przykład w tabeli 20 GB, a ten współczynnik skali przekłada się na 4 GB pamięci martwy krotek. W tabeli 1 TB to 200 GB martwy krotek.

Z bazą danych PostgreSQL możesz ustawić te parametry na poziomie tabeli lub wystąpienia. Obecnie można ustawić te parametry na poziomie tabeli tylko w usłudze Azure Database for PostgreSQL.

## <a name="estimate-the-cost-of-autovacuum"></a>Oszacuj koszt autovacuum
Autovacuum uruchomione jest "kosztowne" i parametry sterujące środowiska uruchomieniowego próżniowej operacji. Następujące parametry pomocy, Oszacuj koszt uruchamiania odkurzający:
- vacuum_cost_page_hit = 1
- vacuum_cost_page_miss = 10
- vacuum_cost_page_dirty = 20

Proces próżniowej odczytuje strony fizyczne i sprawdza, czy martwy krotek. Shared_buffers każdej strony została uznana za koszt 1 (vacuum_cost_page_hit). Wszystkie strony, są traktowane jako kosztują 20 (vacuum_cost_page_dirty), jeśli istnieje krotek nieużywany lub 10 (vacuum_cost_page_miss), jeśli istnieje nie martwy krotek. Próżniowej operacji zatrzymuje, gdy proces przekracza autovacuum_vacuum_cost_limit. 

Po osiągnięciu limitu proces przejścia w tryb uśpienia przez czas określony przez parametr autovacuum_vacuum_cost_delay, zanim zostanie uruchomiony ponownie. Jeśli nie zostanie osiągnięty limit, autovacuum rozpoczyna się po wartość określoną przez parametr autovacuum_nap_time.

Podsumowanie parametrów autovacuum_vacuum_cost_delay i autovacuum_vacuum_cost_limit kontrolować, ile oczyszczania jest dopuszczalne na jednostkę czasu. Należy pamiętać, że wartości domyślne są zbyt mała dla większości warstw cenowych. Optymalne wartości tych parametrów są ceny zależne od warstwy i powinien być odpowiednio skonfigurowany.

Parametr autovacuum_max_workers określa maksymalną liczbę procesów autovacuum, które mogą być uruchomione jednocześnie.

Z bazą danych PostgreSQL możesz ustawić te parametry na poziomie tabeli lub wystąpienia. Obecnie można ustawić te parametry na poziomie tabeli tylko w usłudze Azure Database for PostgreSQL.

## <a name="optimize-autovacuum-per-table"></a>Optymalizowanie autovacuum na tabelę
Można skonfigurować wszystkie poprzednie parametry konfiguracji na tabelę. Oto przykład:
```sql
ALTER TABLE t SET (autovacuum_vacuum_threshold = 1000);
ALTER TABLE t SET (autovacuum_vacuum_scale_factor = 0.1);
ALTER TABLE t SET (autovacuum_vacuum_cost_limit = 1000);
ALTER TABLE t SET (autovacuum_vacuum_cost_delay = 10);
```

Autovacuum to proces synchroniczne na tabelę. Większy odsetek martwy krotek tabela ma większą "koszt" autovacuum. Możesz podzielić tabele, które mają wysoki wskaźnik aktualizacji i usunięć do wielu tabel. Dzielenie tabel pomaga zrównoleglić autovacuum i zmniejszyć "koszt" Aby ukończyć autovacuum w odniesieniu do jednej tabeli. Możesz także zwiększyć liczbę procesów roboczych autovacuum równolegle, aby upewnić się, że liberally zaplanowano pracowników.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat sposobu użycia i dostrajanie autovacuum, zobacz dokumentację PostgreSQL następujące:

 - [Rozdział 18, konfiguracja serwera](https://www.postgresql.org/docs/9.5/static/runtime-config-autovacuum.html)
 - [Rozdziału 24 zadania konserwacji bazy danych procedury](https://www.postgresql.org/docs/9.6/static/routine-vacuuming.html)
