---
title: 'Samouczek: Projektowanie pulpitu nawigacyjnego w czasie rzeczywistym — hiperskali (Citus) — usługa Azure Database for PostgreSQL'
description: W tym samouczku pokazano, jak tworzyć, wypełniać i wysyłać zapytania tabele rozproszone w usłudze Azure Database for PostgreSQL Hyperscale (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: tutorial
ms.date: 05/14/2019
ms.openlocfilehash: f4eeb646de8b68c2c8d30586d0c75cece5317e40
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76716317"
---
# <a name="tutorial-design-a-real-time-analytics-dashboard-by-using-azure-database-for-postgresql--hyperscale-citus"></a>Samouczek: Projektowanie pulpitu nawigacyjnego analizy w czasie rzeczywistym przy użyciu usługi Azure Database for PostgreSQL — hiperskali (Citus)

W tym samouczku używasz usługi Azure Database for PostgreSQL — Hyperscale (Citus), aby dowiedzieć się, jak:

> [!div class="checklist"]
> * Tworzenie grupy serwerów hiperskali (Citus)
> * Tworzenie schematu za pomocą narzędzia psql
> * Tabele niezależnego fragmentu między węzłami
> * Generowanie danych przykładowych
> * Wykonywanie zestawień
> * Zapytanie o nieprzetworzone i zagregowane dane
> * Dane wygasane

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="use-psql-utility-to-create-a-schema"></a>Tworzenie schematu za pomocą narzędzia psql

Po połączeniu z usługą Azure Database for PostgreSQL — Hyperscale (Citus) przy użyciu psql można wykonać kilka podstawowych zadań. W tym samouczku można pochłonąć dane o ruchu z analizy sieci Web, a następnie zwinąć dane, aby zapewnić pulpity nawigacyjne w czasie rzeczywistym na podstawie tych danych.

Utwórzmy tabelę, która będzie zużywać wszystkie nasze surowe dane o ruchu internetowym. Uruchom następujące polecenia w terminalu psql:

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

Utworzymy również tabelę, która będzie zawierać nasze agregaty na minutę, oraz tabelę, która utrzymuje pozycję naszego ostatniego zestawienia. Uruchom następujące polecenia w psql, jak również:

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

Nowo utworzone tabele można teraz zobaczyć na liście tabel za pomocą tego polecenia psql:

```postgres
\dt
```

## <a name="shard-tables-across-nodes"></a>Tabele niezależnego fragmentu między węzłami

Wdrożenie w hiperskali przechowuje wiersze tabel w różnych węzłach na podstawie wartości kolumny wyznaczonej przez użytkownika. Ta "kolumna dystrybucji" oznacza, jak dane są podzielona między węzłami.

Ustawmy kolumnę dystrybucji jako\_identyfikator witryny, klucz niezależnego fragmentu. W psql uruchom następujące funkcje:

  ```sql
SELECT create_distributed_table('http_request',      'site_id');
SELECT create_distributed_table('http_request_1min', 'site_id');
```

## <a name="generate-sample-data"></a>Generowanie danych przykładowych

Teraz nasza grupa serwerów powinna być gotowa do pozyskiwania niektórych danych. Możemy uruchomić następujące lokalnie `psql` z naszego połączenia do ciągłego wstawiania danych.

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

Kwerenda wstawia około ośmiu wierszy co sekundę. Wiersze są przechowywane w różnych węzłach procesu roboczego zgodnie z kolumną dystrybucyjną . `site_id`

   > [!NOTE]
   > Pozostaw uruchomione zapytanie generowania danych i otwórz drugie połączenie psql dla pozostałych poleceń w tym samouczku.
   >

## <a name="query"></a>Zapytanie

Opcja hostingu na dużą skalę umożliwia wielu węzłom równoległe przetwarzanie zapytań pod kątem szybkości. Na przykład baza danych oblicza agregaty, takie jak SUMA i LICZENIE w węzłach procesu roboczego i łączy wyniki w ostateczną odpowiedź.

Oto zapytanie do liczenia żądań sieci web na minutę wraz z kilkoma statystykami.
Spróbuj uruchomić go w psql i obserwować wyniki.

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

## <a name="rolling-up-data"></a>Zwijowanie danych

Poprzednie zapytanie działa poprawnie we wczesnych etapach, ale jego wydajność spada w miarę skalowania danych. Nawet w przypadku przetwarzania rozproszonego jest szybsze wstępne obliczenie danych niż wielokrotne obliczenie.

Możemy zapewnić, że nasz pulpit nawigacyjny pozostaje szybki, regularnie zwijając surowe dane w tabelę agregacji. Można eksperymentować z czasem trwania agregacji. Użyliśmy tabeli agregacji na minutę, ale zamiast tego można podzielić dane na 5, 15 lub 60 minut.

Aby uruchomić ten roll-up łatwiej, mamy zamiar umieścić go w funkcji plpgsql. Uruchom te polecenia w psql, aby utworzyć `rollup_http_request` funkcję.

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

Z naszą funkcją w miejscu, wykonaj go, aby zwinąć dane:

```sql
SELECT rollup_http_request();
```

A dzięki naszym danym w wstępnie zagregowanym formularzu możemy zbadać tabelę zestawienia, aby uzyskać ten sam raport, co wcześniej. Uruchom zapytanie:

```sql
SELECT site_id, ingest_time as minute, request_count,
       success_count, error_count, average_response_time_msec
  FROM http_request_1min
 WHERE ingest_time > date_trunc('minute', now()) - '5 minutes'::interval;
 ```

## <a name="expiring-old-data"></a>Wygasające stare dane

Zestawienia kwerend szybciej, ale nadal musimy wygasnąć stare dane, aby uniknąć kosztów magazynu nieograniczonego. Zdecyduj, jak długo chcesz przechowywać dane dla każdej szczegółowości, i użyj standardowych zapytań, aby usunąć wygasłe dane. W poniższym przykładzie zdecydowaliśmy się zachować nieprzetworzone dane przez jeden dzień i agregacje na minutę przez jeden miesiąc:

```sql
DELETE FROM http_request WHERE ingest_time < now() - interval '1 day';
DELETE FROM http_request_1min WHERE ingest_time < now() - interval '1 month';
```

W produkcji można zawinąć te zapytania w funkcji i wywołać go co minutę w zadaniu cron.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

W poprzednich krokach utworzono zasoby platformy Azure w grupie serwerów. Jeśli nie oczekujesz, że te zasoby będą potrzebne w przyszłości, usuń grupę serwerów. Naciśnij przycisk *Usuń* na stronie *Przegląd* grupy serwerów. Po wyświetleniu monitu na stronie podręcznej potwierdź nazwę grupy serwerów i kliknij przycisk *Usuń.*

## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiesz się, jak aprowizować grupę serwerów hiperskali (Citus). Połączyłeś się z nim z psql, utworzono schemat i rozproszone dane. Nauczysz się wysyłać zapytania do danych w postaci nieprzetworzonej, regularnie agregować te dane, wysyłać zapytania do tabel zagregowanych i wygasać stare dane.

Następnie dowiedz się więcej o pojęciach hiperskali.
> [!div class="nextstepaction"]
> [Typy węzłów hiperskali](https://aka.ms/hyperscale-concepts)
