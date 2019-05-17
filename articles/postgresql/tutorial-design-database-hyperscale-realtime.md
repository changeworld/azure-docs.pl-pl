---
title: Projektowanie pulpit nawigacyjny w czasie rzeczywistym z usługą Azure Database for PostgreSQL — w Hiperskali (Citus) (wersja zapoznawcza) samouczek
description: W tym samouczku pokazano, jak tworzyć i wypełnić oraz zapytania rozproszone tabele w usłudze Azure Database for postgresql w warstwie na dużą skalę (Citus) (wersja zapoznawcza).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: tutorial
ms.date: 05/14/2019
ms.openlocfilehash: a5e4b2073a29785ee851b2733c12d6331afe59d8
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65791329"
---
# <a name="tutorial-design-a-real-time-analytics-dashboard-by-using-azure-database-for-postgresql--hyperscale-citus-preview"></a>Samouczek: Projektowanie pulpit nawigacyjny analizy w czasie rzeczywistym za pomocą usługi Azure Database for PostgreSQL — w Hiperskali (Citus) (wersja zapoznawcza)

W tym samouczku możesz używać usługi Azure Database for PostgreSQL — w Hiperskali (Citus) (wersja zapoznawcza), aby dowiedzieć się, jak:

> [!div class="checklist"]
> * Utwórz grupę serwerów (Citus) na dużą skalę
> * Utwórz schemat przy użyciu narzędzia psql
> * Tabele fragmentów w węzłach
> * Generowanie danych przykładowych
> * Wykonywać zbiorcze
> * Wykonywanie zapytań o dane nieprzetworzone i zagregowane
> * Wygasanie danych

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="use-psql-utility-to-create-a-schema"></a>Utwórz schemat przy użyciu narzędzia psql

Po nawiązaniu połączenia z usługi Azure Database for PostgreSQL — na dużą skalę (Citus) (wersja zapoznawcza) za pomocą narzędzia psql, można wykonać niektóre podstawowe zadania. Ten samouczek przeprowadzi Cię przez ruch danych z funkcji web analytics, następnie zestawianie danych w celu zapewnienia pulpity nawigacyjne czasu rzeczywistego na podstawie tych danych.

Utwórzmy tabelę, która zużyje wszystkie nasze dane ruchu pierwotne sieci web. Uruchom następujące polecenia w terminalu psql:

```sql
CREATE TABLE http_request (
  site_id INT,
  ingest_time TIMESTAMPTZ DEFAULT now(),

  url TEXT,
  request_country TEXT,
  ip_address TEXT,

  status_code INT,
  response_time_msec INT
);
```

Przedstawimy również tworzenie tabeli, w którym będą przechowywane w naszych agregacji na minutę i tabelę, która zachowuje położenie naszej ostatniej zbiorczy. Uruchom następujące polecenia w także psql:

```sql
CREATE TABLE http_request_1min (
  site_id INT,
  ingest_time TIMESTAMPTZ, -- which minute this row represents

  error_count INT,
  success_count INT,
  request_count INT,
  average_response_time_msec INT,
  CHECK (request_count = error_count + success_count),
  CHECK (ingest_time = date_trunc('minute', ingest_time))
);

CREATE INDEX http_request_1min_idx ON http_request_1min (site_id, ingest_time);

CREATE TABLE latest_rollup (
  minute timestamptz PRIMARY KEY,

  CHECK (minute = date_trunc('minute', minute))
);
```

Możesz wyświetlić nowo utworzonego tabelami w listę tabel, teraz za pomocą tego polecenia psql:

```postgres
\dt
```

## <a name="shard-tables-across-nodes"></a>Tabele fragmentów w węzłach

Wdrożenia na dużą skalę wiersze tabeli są przechowywane w różnych węzłach, na podstawie wartości kolumny wyznaczony przez użytkownika. Ta "kolumna dystrybucji" oznacza to, jak dane są podzielone na fragmenty w węzłach.

Teraz należy ustawić kolumny dystrybucji lokacji jako\_identyfikator, klucz fragmentu. W psql uruchom następujące funkcje:

  ```sql
SELECT create_distributed_table('http_request',      'site_id');
SELECT create_distributed_table('http_request_1min', 'site_id');
```

## <a name="generate-sample-data"></a>Generowanie danych przykładowych

Teraz naszej grupy serwerów powinna być gotowa do odbierania niektórych danych. Firma Microsoft może uruchom następujące polecenie lokalnie z naszych `psql` połączenia stale wstawiania danych.

```sql
DO $$
  BEGIN LOOP
    INSERT INTO http_request (
      site_id, ingest_time, url, request_country,
      ip_address, status_code, response_time_msec
    ) VALUES (
      trunc(random()*32), clock_timestamp(),
      concat('http://example.com/', md5(random()::text)),
      ('{China,India,USA,Indonesia}'::text[])[ceil(random()*4)],
      concat(
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2)
      )::inet,
      ('{200,404}'::int[])[ceil(random()*2)],
      5+trunc(random()*150)
    );
    COMMIT;
    PERFORM pg_sleep(random() * 0.25);
  END LOOP;
END $$;
```

Zapytanie wstawia około ośmiu wierszy co sekundę. Wiersze są przechowywane w węzłach innego procesu roboczego, zgodnie z zaleceniami kolumny dystrybucji `site_id`.

   > [!NOTE]
   > Pozostaw zapytanie generowania danych, które są uruchomione i otwieraniu drugie połączenie psql dla pozostałych poleceń w ramach tego samouczka.
   >

## <a name="query"></a>Zapytanie

Opcja hostingu w hiperskali zezwala na wiele węzłów do przetworzenia zapytania równolegle dla danej szybkości. Na przykład baza danych oblicza agregacji typu Suma i liczba na węzłach procesu roboczego i łączy wyniki końcowe odpowiedzi.

Oto zapytanie, aby liczba żądań sieci web na minutę, wraz z kilkoma statystyki.
Spróbuj uruchomić go w psql i sprawdź wyniki.

```sql
SELECT
  site_id,
  date_trunc('minute', ingest_time) as minute,
  COUNT(1) AS request_count,
  SUM(CASE WHEN (status_code between 200 and 299) THEN 1 ELSE 0 END) as success_count,
  SUM(CASE WHEN (status_code between 200 and 299) THEN 0 ELSE 1 END) as error_count,
  SUM(response_time_msec) / COUNT(1) AS average_response_time_msec
FROM http_request
WHERE date_trunc('minute', ingest_time) > now() - '5 minutes'::interval
GROUP BY site_id, minute
ORDER BY minute ASC;
```

## <a name="rolling-up-data"></a>Zestawianie danych

Poprzednie zapytanie działa prawidłowo we wczesnych etapach, ale obniża wydajność w miarę skalowania danych. Nawet w przypadku przetwarzania rozproszonego, szybciej jest wstępnie obliczenia danych niż Aby ponownie obliczyć jej wielokrotnie.

Możemy zapewnić, że pulpit nawigacyjny pozostanie szybkie, regularnie zestawianie nieprzetworzone dane do tabeli agregacji. Możesz eksperymentować z czasem trwania agregacji. Użyliśmy tabeli agregacji na minutę, ale można podzielić dane na 5, 15 lub 60 minut zamiast tego.

Aby łatwiej uruchomić roll-up, którą zamierzamy umieścić go w funkcji plpgsql. Uruchom następujące polecenia w psql, aby utworzyć `rollup_http_request` funkcji.

```sql
-- initialize to a time long ago
INSERT INTO latest_rollup VALUES ('10-10-1901');

-- function to do the rollup
CREATE OR REPLACE FUNCTION rollup_http_request() RETURNS void AS $$
DECLARE
  curr_rollup_time timestamptz := date_trunc('minute', now());
  last_rollup_time timestamptz := minute from latest_rollup;
BEGIN
  INSERT INTO http_request_1min (
    site_id, ingest_time, request_count,
    success_count, error_count, average_response_time_msec
  ) SELECT
    site_id,
    date_trunc('minute', ingest_time),
    COUNT(1) as request_count,
    SUM(CASE WHEN (status_code between 200 and 299) THEN 1 ELSE 0 END) as success_count,
    SUM(CASE WHEN (status_code between 200 and 299) THEN 0 ELSE 1 END) as error_count,
    SUM(response_time_msec) / COUNT(1) AS average_response_time_msec
  FROM http_request
  -- roll up only data new since last_rollup_time
  WHERE date_trunc('minute', ingest_time) <@
          tstzrange(last_rollup_time, curr_rollup_time, '(]')
  GROUP BY 1, 2;

  -- update the value in latest_rollup so that next time we run the
  -- rollup it will operate on data newer than curr_rollup_time
  UPDATE latest_rollup SET minute = curr_rollup_time;
END;
$$ LANGUAGE plpgsql;
```

Za pomocą naszych funkcji w miejscu ją wykonać, aby rzutować dane:

```sql
SELECT rollup_http_request();
```

I z naszych danych w postaci wstępnie zagregowane firma Microsoft może odpytać tabelę rollup w celu uzyskania tego samego raportu, jak wcześniej. Uruchom następujące zapytanie:

```sql
SELECT site_id, ingest_time as minute, request_count,
       success_count, error_count, average_response_time_msec
  FROM http_request_1min
 WHERE ingest_time > date_trunc('minute', now()) - '5 minutes'::interval;
 ```

## <a name="expiring-old-data"></a>Wygasające stare dane

Pakiety zbiorcze szybciej zapytań, ale wciąż potrzebujemy wygaśnięcie starych danych, aby uniknąć kosztów magazynowania niepowiązanych. Zdecyduj, jak długo chcesz przechowywać dane dla każdej stopień szczegółowości i Użyj standardowego zapytania, aby usunąć dane wygasłe. W poniższym przykładzie podjęliśmy decyzję o nieprzetworzone dane przez jeden dzień, a na minutę agregacji przez jeden miesiąc:

```sql
DELETE FROM http_request WHERE ingest_time < now() - interval '1 day';
DELETE FROM http_request_1min WHERE ingest_time < now() - interval '1 month';
```

W środowisku produkcyjnym można opakować te zapytania w funkcji i wywołać go co minutę zadania cron.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

W poprzednich krokach utworzono zasoby platformy Azure w grupie serwerów. Jeśli nie będziesz już potrzebować tych zasobów w przyszłości, Usuń grupę serwera. Naciśnij klawisz *Usuń* znajdujący się w *Przegląd* strony grupy serwerów. Po wyświetleniu monitu na stronie wyskakujące, upewnij się, nazwę grupy serwerów i kliknij końcowe *Usuń* przycisku.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono sposób aprowizowania grupy serwerów na dużą skalę (Citus). Dołączone do niego za pomocą narzędzia psql, utworzyć schemat i rozproszonych danych. Regularnie przedstawiono przesyłać zapytania dotyczące danych zarówno w postaci pierwotnej agregowanie danych, wykonywać zapytania dotyczące tabel zagregowane i wygaśnięcie starych danych.

Następnie Dowiedz się więcej o koncepcjach na dużą skalę.
> [!div class="nextstepaction"]
> [Typy węzłów na dużą skalę](https://aka.ms/hyperscale-concepts)