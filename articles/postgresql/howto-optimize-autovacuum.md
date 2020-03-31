---
title: Optymalizuj autovacuum — usługa Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano, jak zoptymalizować autovacuum w bazie danych azure dla postgreSQL — single server
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 1917bd6744e100db54fe959292e29486f8a1784b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74770190"
---
# <a name="optimize-autovacuum-on-an-azure-database-for-postgresql---single-server"></a>Optymalizuj autovacuum w bazie danych azure dla postgreSQL — pojedynczy serwer
W tym artykule opisano sposób efektywnej optymalizacji autovacuum na serwerze usługi Azure Database dla postgreSql.

## <a name="overview-of-autovacuum"></a>Przegląd autovacuum
PostgreSQL używa kontroli współbieżności wieluwersji (MVCC), aby umożliwić większą współbieżność bazy danych. Każda aktualizacja powoduje wstawianie i usuwanie, a każde usunięcie powoduje, że wiersze są oznaczane jako nieuiszczone do usunięcia. Miękkie oznaczenie identyfikuje martwe krotki, które zostaną usunięte później. Aby wykonać te zadania, PostgreSQL uruchamia zadanie próżniowe.

Zadanie próżniowe można uruchomić ręcznie lub automatycznie. Więcej martwych krotek istnieje, gdy baza danych doświadcza operacji aktualizacji lub usuwania. Mniej martwych krotek istnieje, gdy baza danych jest bezczynna. Należy odkurzać częściej, gdy obciążenie bazy danych jest ciężkie, co sprawia, że praca *próżniowa jest ręcznie* niewygodna.

Autovacuum można skonfigurować i korzyści z strojenia. Wartości domyślne, które postgreSql jest dostarczany z try upewnić się, że produkt działa na wszystkich rodzajach urządzeń. Urządzenia te obejmują Raspberry Pis. Idealne wartości konfiguracyjne zależą od:
- Łączna liczba dostępnych zasobów, takich jak jednostka SKU i rozmiar magazynu.
- Użycie zasobów.
- Charakterystyka poszczególnych obiektów.

## <a name="autovacuum-benefits"></a>Korzyści autowakuum
Jeśli nie odkurzać od czasu do czasu, martwe krotek, które gromadzą się może spowodować:
- Nadęcie danych, takie jak większe bazy danych i tabele.
- Większe indeksy nieoptymalne.
- Zwiększono we/wy.

## <a name="monitor-bloat-with-autovacuum-queries"></a>Monitoruj nadęcie za pomocą zapytań autovacuum
Następująca przykładowa kwerenda ma na celu zidentyfikowanie liczby martwych i żywych krotek w tabeli o nazwie XYZ:
 
    'SELECT relname, n_dead_tup, n_live_tup, (n_dead_tup/ n_live_tup) AS DeadTuplesRatio, last_vacuum, last_autovacuum FROM pg_catalog.pg_stat_all_tables WHERE relname = 'XYZ' order by n_dead_tup DESC;'

## <a name="autovacuum-configurations"></a>Konfiguracje autowakuum
Parametry konfiguracji sterujące autovacuum są oparte na odpowiedziach na dwa kluczowe pytania:
- Kiedy powinien się rozpocząć?
- Ile powinien czyścić po uruchomieniu?

Oto kilka parametrów konfiguracji autovacuum, które można zaktualizować na podstawie poprzednich pytań, wraz z pewnymi wskazówkami.

Parametr|Opis|Wartość domyślna
---|---|---
autovacuum_vacuum_threshold|Określa minimalną liczbę zaktualizowanych lub usuniętych krotek potrzebnych do wyzwolenia operacji próżniowej w jednej tabeli. Wartość domyślna to 50 krotek. Ustaw ten parametr tylko w pliku postgresql.conf lub w wierszu polecenia serwera. Aby zastąpić ustawienie dla poszczególnych tabel, zmień parametry magazynu tabel.|50
autovacuum_vacuum_scale_factor|Określa ułamek rozmiaru tabeli, który ma być dodawany do autovacuum_vacuum_threshold przy podejmowaniu decyzji o uruchomieniu operacji próżniowej. Wartość domyślna to 0.2, czyli 20 procent rozmiaru tabeli. Ustaw ten parametr tylko w pliku postgresql.conf lub w wierszu polecenia serwera. Aby zastąpić ustawienie dla poszczególnych tabel, zmień parametry magazynu tabel.|5 proc.
autovacuum_vacuum_cost_limit|Określa wartość graniczną kosztów stosowaną w automatycznych operacjach próżniowych. Jeśli określono wartość -1, która jest wartością domyślną, używana jest zwykła wartość vacuum_cost_limit. Jeśli istnieje więcej niż jeden pracownik, wartość jest rozdzielana proporcjonalnie między uruchomionych pracowników autovacuum. Suma limitów dla każdego pracownika nie przekracza wartości tej zmiennej. Ustaw ten parametr tylko w pliku postgresql.conf lub w wierszu polecenia serwera. Aby zastąpić ustawienie dla poszczególnych tabel, zmień parametry magazynu tabel.|-1
autovacuum_vacuum_cost_delay|Określa wartość opóźnienia kosztów używaną w automatycznych operacjach próżniowych. Jeśli określono wartość -1, używana jest zwykła wartość vacuum_cost_delay. Wartość domyślna to 20 milisekund. Ustaw ten parametr tylko w pliku postgresql.conf lub w wierszu polecenia serwera. Aby zastąpić ustawienie dla poszczególnych tabel, zmień parametry magazynu tabel.|20 ms
autovacuum_nap_time|Określa minimalne opóźnienie między automatycznym wakumentem w dowolnej bazie danych. W każdej rundzie demon sprawdza bazę danych i wydaje polecenia VACUUM i ANALYZE zgodnie z potrzebami dla tabel w tej bazie danych. Opóźnienie jest mierzone w sekundach, a wartość domyślna to jedna minuta (1 min). Ustaw ten parametr tylko w pliku postgresql.conf lub w wierszu polecenia serwera.|15 s
autovacuum_max_workers|Określa maksymalną liczbę procesów autovacuum, innych niż uruchamianie autovacuum, które można uruchomić w dowolnym momencie. Wartość domyślna to trzy. Ustaw ten parametr tylko przy uruchamianiu serwera.|3

Aby zastąpić ustawienia poszczególnych tabel, zmień parametry magazynu tabel. 

## <a name="autovacuum-cost"></a>Koszt autokusuum
Oto "koszty" eksploatacji próżni:

- Strony danych, na których działa próżnia, są zablokowane.
- Obliczenia i pamięć są używane, gdy zadanie próżniowe jest uruchomione.

W rezultacie nie uruchamiaj zadań próżniowych zbyt często lub zbyt rzadko. Zadanie próżniowe musi dostosować się do obciążenia pracą. Przetestuj wszystkie zmiany parametrów autovacuum ze względu na kompromisy każdego z nich.

## <a name="autovacuum-start-trigger"></a>Wyzwalacz uruchamiania automatycznego waku
Autovacuum jest wyzwalany, gdy liczba martwych krotek przekracza autovacuum_vacuum_threshold + autovacuum_vacuum_scale_factor * reltuples. Tutaj reltuples jest stałą.

Oczyszczanie z autovacuum musi nadążyć za obciążeniem bazy danych. W przeciwnym razie może zabraknąć magazynu i wystąpić ogólne spowolnienie w kwerendach. Zamortyzowany w czasie szybkość, z jaką operacja próżniowa czyści martwe krotki, powinna być równa szybkości, z jaką tworzone są martwe krotek.

Bazy danych z wielu aktualizacji i usuwania mają więcej martwych krotek i potrzebują więcej miejsca. Ogólnie rzecz biorąc bazy danych z wieloma aktualizacjami i usuwaniami korzystają z niskich wartości autovacuum_vacuum_scale_factor i autovacuum_vacuum_threshold. Niskie wartości zapobiegają długotrwałemu gromadzeniu się martwych krotek. Można użyć wyższych wartości dla obu parametrów z mniejszych baz danych, ponieważ potrzeba odkurzania jest mniej pilne. Częste odkurzanie odbywa się kosztem obliczeń i pamięci.

Domyślny współczynnik skali 20 procent działa dobrze w tabelach z niskim procentem martwych krotek. Nie działa dobrze na tabelach z wysokim procentem martwych krotek. Na przykład w tabeli 20 GB ten współczynnik skali przekłada się na 4 GB martwych krotek. W tabeli 1 TB jest to 200 GB martwych krotek.

Za pomocą postgreSql można ustawić te parametry na poziomie tabeli lub wystąpienia. Obecnie można ustawić te parametry na poziomie tabeli tylko w usłudze Azure Database for PostgreSQL.

## <a name="estimate-the-cost-of-autovacuum"></a>Oszacuj koszt autovacuum
Uruchamianie autovacuum jest "kosztowne" i istnieją parametry do kontrolowania czasu wykonywania operacji próżniowych. Następujące parametry pomagają oszacować koszt pracy próżni:
- vacuum_cost_page_hit = 1
- vacuum_cost_page_miss = 10
- vacuum_cost_page_dirty = 20

Proces próżniowy odczytuje strony fizyczne i sprawdza, czy nie ma martwych krotek. Każda strona w shared_buffers jest uważana za koszt 1 (vacuum_cost_page_hit). Wszystkie inne strony są uważane za koszt 20 (vacuum_cost_page_dirty), jeśli istnieją martwe krotek lub 10 (vacuum_cost_page_miss), jeśli nie istnieją martwe krotek. Działanie próżniowe zatrzymuje się, gdy proces przekracza autovacuum_vacuum_cost_limit. 

Po osiągnięciu limitu proces przechodzi w tryb uśpienia przez czas określony przez parametr autovacuum_vacuum_cost_delay, zanim rozpocznie się ponownie. Jeśli limit nie zostanie osiągnięty, autovacuum rozpoczyna się po wartości określonej przez parametr autovacuum_nap_time.

Podsumowując, parametry autovacuum_vacuum_cost_delay i autovacuum_vacuum_cost_limit kontrolują, ile oczyszczania danych jest dozwolone na jednostkę czasu. Należy zauważyć, że wartości domyślne są zbyt niskie dla większości warstw cenowych. Optymalne wartości dla tych parametrów są zależne od warstwy cenowej i powinny być odpowiednio skonfigurowane.

Parametr autovacuum_max_workers określa maksymalną liczbę procesów autovacuum, które można uruchamiać jednocześnie.

Za pomocą postgreSql można ustawić te parametry na poziomie tabeli lub wystąpienia. Obecnie można ustawić te parametry na poziomie tabeli tylko w usłudze Azure Database for PostgreSQL.

## <a name="optimize-autovacuum-per-table"></a>Optymalizacja autovacuum na stół
Można skonfigurować wszystkie poprzednie parametry konfiguracji dla tabeli. Oto przykład:
```sql
ALTER TABLE t SET (autovacuum_vacuum_threshold = 1000);
ALTER TABLE t SET (autovacuum_vacuum_scale_factor = 0.1);
ALTER TABLE t SET (autovacuum_vacuum_cost_limit = 1000);
ALTER TABLE t SET (autovacuum_vacuum_cost_delay = 10);
```

Autovacuum jest procesem synchroniczalnym dla tabeli. Im większy procent martwych krotek, które ma tabela, tym wyższy "koszt" do automatycznego wygaszania. Można podzielić tabele, które mają wysoką szybkość aktualizacji i usuwa do wielu tabel. Dzielenie tabel pomaga zrównoleglić autovacuum i zmniejszyć "koszt", aby zakończyć autovacuum na jednym stole. Można również zwiększyć liczbę równoległych pracowników autovacuum, aby upewnić się, że pracownicy są liberalnie zaplanowane.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o używaniu i dostrajaniu autovacuum, zobacz następującą dokumentację PostgreSQL:

 - [Rozdział 18, Konfiguracja serwera](https://www.postgresql.org/docs/9.5/static/runtime-config-autovacuum.html)
 - [Rozdział 24, Rutynowe zadania związane z konserwacją bazy danych](https://www.postgresql.org/docs/9.6/static/routine-vacuuming.html)
