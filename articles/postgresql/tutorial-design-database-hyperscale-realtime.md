---
title: Projektowanie pulpit nawigacyjny w czasie rzeczywistym z usługą Azure Database for PostgreSQL — w Hiperskali (Citus) (wersja zapoznawcza) samouczek
description: W tym samouczku pokazano, jak tworzyć i wypełnić oraz zapytania rozproszone tabele w usłudze Azure Database for postgresql w warstwie na dużą skalę (Citus) (wersja zapoznawcza).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: tutorial
ms.date: 05/06/2019
ms.openlocfilehash: 7324ab1d7aa6e42100c9c6760c17b0ea6445f21d
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65080744"
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

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="create-an-azure-database-for-postgresql"></a>Tworzenie serwera usługi Azure Database for PostgreSQL

Wykonaj następujące kroki, aby utworzyć serwer usługi Azure Database for PostgreSQL:
1. W lewym górnym rogu witryny Azure Portal kliknij polecenie **Utwórz zasób**.
2. Na stronie **Nowy** wybierz pozycję **Bazy danych**, a następnie na stronie **Bazy danych** wybierz pozycję **Azure Database for PostgreSQL**.
3. Dla opcji wdrożenia kliknij **Utwórz** przycisku w obszarze **grupy serwerów na dużą skalę (Citus) — wersja ZAPOZNAWCZA.**
4. Wypełnij formularz szczegółów nowego serwera, używając następujących informacji:
   - Grupa zasobów: kliknij **Utwórz nową** link poniżej pola tekstowego dla tego pola. Wprowadź nazwę, taką jak **myresourcegroup**.
   - Nazwa grupy serwerów: **mydemoserver** (nazwa serwera, mapowana na nazwę DNS, która jest wymagana do być globalnie unikatowa).
   - Nazwa użytkownika administratora: **myadmin** (jego posłuży później do łączenia z bazą danych).
   - Hasło: musi być co najmniej ośmiu znaków i musi zawierać znaki z trzech z następujących kategorii — litery na wielkie litery angielskie, angielskiej małe litery, cyfry (0 – 9) i znaki inne niż alfanumeryczne (!, $, #, % itp.)
   - Lokalizacja: Użyj lokalizacji, która jest najbliżej Twoich użytkowników, aby zapewnić im najszybszy dostęp do danych.

   > [!IMPORTANT]
   > Nazwa logowania i hasło administratora serwera określone w tym miejscu będą wymagane do logowania do serwera i jego baz danych w dalszej części tego samouczka. Zapamiętaj lub zapisz te informacje do wykorzystania w przyszłości.

5. Kliknij przycisk **Konfigurowanie grupy serwerów**. Pozostaw ustawienia w tej sekcji bez zmian, a następnie kliknij przycisk **Zapisz**.
6. Kliknij przycisk **przeglądu + Utwórz** i następnie **Utwórz** aby aprowizować serwer. Aprowizacja zajmuje kilka minut.
7. Strony nastąpi przekierowanie do monitorowania wdrożenia. Zmiany stanu na żywo z **wdrożenia jest w toku** do **swoje wdrożenie jest ukończone**, kliknij przycisk **dane wyjściowe** element menu po lewej stronie.
8. Strony danych wyjściowych będzie zawierał nazwę hosta koordynatora za pomocą przycisku obok niej, aby skopiować wartość do Schowka. Zarejestruj te informacje w celu późniejszego użycia.

## <a name="configure-a-server-level-firewall-rule"></a>Konfigurowanie reguły zapory na poziomie serwera

Usługa Azure Database for PostgreSQL używa zapory na poziomie serwera. Domyślnie Zapora uniemożliwia wszystkim zewnętrznym aplikacjom i narzędzia do nawiązywania połączenia z serwerem i wszelkimi bazami danych na serwerze. Firma Microsoft należy dodać regułę otwierającą zaporę dla określonego zakresu adresów IP.

1. Z **dane wyjściowe** sekcji, w której został skopiowany wcześniej z nazwą hosta węzła koordynatora, kliknij przycisk Wstecz do **Przegląd** elementu menu.

2. Znajdź grupy skalowania dla danego wdrożenia, na liście zasobów, a następnie kliknij go. (Jego nazwa będzie zaczynała się od "sg-".)

3. Kliknij przycisk **zapory** w obszarze **zabezpieczeń** w menu po lewej stronie.

4. Kliknij łącze **+ Dodaj regułę zapory dla bieżącego adresu IP klienta**. Na koniec kliknij **Zapisz** przycisku.

5. Kliknij pozycję **Zapisz**.

   > [!NOTE]
   > Serwer Azure PostgreSQL komunikuje się przez port 5432. Jeśli próbujesz nawiązać połączenie z sieci firmowej, ruch wychodzący na porcie 5432 może być zablokowany przez zaporę sieciową. Jeśli wystąpi taka sytuacja, nie będzie można nawiązać połączenia z serwerem usługi Azure SQL Database, chyba że dział IT otworzy port 5432.
   >

## <a name="connect-to-the-database-using-psql-in-cloud-shell"></a>Połączenia z bazą danych za pomocą narzędzia psql w usłudze Cloud Shell

Teraz użyjmy narzędzia wiersza polecenia [psql](https://www.postgresql.org/docs/current/app-psql.html), aby nawiązać połączenie z serwerem usługi Azure Database for PostgreSQL.
1. Uruchom powłokę Azure Cloud Shell za pośrednictwem ikony terminala w górnym okienku nawigacji.

   ![Azure Database for PostgreSQL — ikona terminala powłoki Azure Cloud Shell](./media/tutorial-design-database-hyperscale-realtime/psql-cloud-shell.png)

2. Powłoka Azure Cloud Shell zostanie otwarta w przeglądarce, umożliwiając wpisywanie poleceń powłoki bash.

   ![Azure Database for PostgreSQL — znak zachęty powłoki Azure Shell Bash](./media/tutorial-design-database-hyperscale-realtime/psql-bash.png)

3. W wierszu polecenia powłoki Cloud Shell nawiąż połączenie z serwerem usługi Azure Database for PostgreSQL za pomocą poleceń psql. Następujący format służy do łączenia z serwerem usługi Azure Database for PostgreSQL przy użyciu narzędzia [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html):
   ```bash
   psql --host=<myserver> --username=myadmin --dbname=citus
   ```

   Na przykład następujące polecenie umożliwia nawiązanie domyślna baza danych o nazwie **citus** na Twoim serwerze PostgreSQL **mydemoserver.postgres.database.azure.com** za pomocą poświadczeń dostępu. Po wyświetleniu monitu wprowadź hasło administratora serwera.

   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --username=myadmin --dbname=citus
   ```

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

Przedstawimy również tworzenie tabeli, w którym będą przechowywane w naszych agregacji na minutę i tabelę, która zachowuje położenie naszej ostatniej zbiorczy. Uruchom następujące polecenie w także psql:

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
    PERFORM pg_sleep(random() * 0.25);
  END LOOP;
END $$;
```

Zapytanie dodaje wiersz mniej więcej co kwartał drugi. Wiersze są przechowywane w węzłach innego procesu roboczego, zgodnie z zaleceniami kolumny dystrybucji `site_id`.

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

## <a name="performing-rollups"></a>Wykonywanie pakietów zbiorczych

Powyższe zapytanie działa prawidłowo we wczesnych etapach, ale jego wydajność, zostaną obniżone w miarę skalowania danych. Za pomocą rozproszonego przetwarzania szybciej jest wstępnie obliczenia te dane, niż wielokrotnie Oblicz ponownie.

Możemy zapewnić, że pulpit nawigacyjny pozostanie szybkie, regularnie zestawianie nieprzetworzone dane do tabeli agregacji. W takim przypadku firma Microsoft będzie skumulowana w tabeli agregacji jedną minutę, ale może również zawierać agregacji wynoszącą 5 minut, 15 minut, godzinę i tak dalej.

Ponieważ firma Microsoft stale uruchomi roll-up zamierzamy utworzyć funkcję, która ma wykonać. Uruchom następujące polecenia w psql, aby utworzyć `rollup_http_request` funkcji.

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

I z naszych danych w postaci wstępnie zagregowane firma Microsoft może odpytać tabelę rollup w celu uzyskania tego samego raportu, jak wcześniej. Uruchom następujące polecenie:

```sql
SELECT site_id, ingest_time as minute, request_count,
       success_count, error_count, average_response_time_msec
  FROM http_request_1min
 WHERE ingest_time > date_trunc('minute', now()) - '5 minutes'::interval;
 ```

## <a name="expiring-old-data"></a>Wygasające stare dane

Pakiety zbiorcze szybciej zapytań, ale wciąż potrzebujemy wygaśnięcie starych danych, aby uniknąć kosztów magazynowania niepowiązanych. Po prostu zdecyduj, jak długo chcesz przechowywać dane dla każdej stopień szczegółowości i Użyj standardowego zapytania, aby usunąć dane wygasłe. W poniższym przykładzie podjęliśmy decyzję o nieprzetworzone dane przez jeden dzień, a na minutę agregacji przez jeden miesiąc:

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