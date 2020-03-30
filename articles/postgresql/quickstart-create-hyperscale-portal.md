---
title: Tworzenie tabel rozproszonych — hiperskala (Citus) — usługa Azure Database for PostgreSQL
description: Szybki start do tworzenia i przeszukiwania tabel rozproszonych w usłudze Azure Database dla postgreSQL Hyperscale (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: quickstart
ms.date: 05/14/2019
ms.openlocfilehash: 02e009e6fff2e717693d1579d409199ab179d941
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241514"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---hyperscale-citus-in-the-azure-portal"></a>Szybki start: tworzenie bazy danych usługi Azure dla postgreSQL — hiperskali (Citus) w witrynie Azure portal

Azure Database for PostgreSQL to usługa zarządzana, która służy do uruchamiania i skalowania w chmurze baz danych PostgreSQL o wysokiej dostępności, a także zarządzania nimi. Ten przewodnik Szybki start pokazuje, jak utworzyć grupę serwerów usługi Azure Database for PostgreSQL — Hyperscale (Citus) przy użyciu witryny Azure portal. Będziesz eksplorować rozproszone dane: tabele dzielenia na fragmenty między węzłami, pozyskiwania przykładowych danych i uruchamianie kwerend, które są wykonywane w wielu węzłach.

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="create-and-distribute-tables"></a>Tworzenie i rozpowszechnianie tabel

Po podłączeniu do węzła koordynatora hiperskali za pomocą psql można wykonać kilka podstawowych zadań.

W serwerach hyperscale istnieją trzy typy tabel:

- Tabele rozproszone lub podzielone na fragmenty (rozłożone, aby ułatwić skalowanie pod kątem wydajności i równoległości)
- Tabele odwołań (wiele kopii zachowanych)
- Tabele lokalne (często używane w wewnętrznych tabelach administracyjnych)

W tym przewodniku Szybki start skupimy się przede wszystkim na tabelach rozproszonych i zapoznaniu się z nimi.

Model danych, z którym będziemy pracować, jest prosty: dane użytkownika i zdarzenia z usługi GitHub. Zdarzenia obejmują tworzenie rozwidlików, git commits związane z organizacją i więcej.

Po połączeniu za pośrednictwem psql, stwórzmy nasze tabele. W konsoli psql uruchom:

```sql
CREATE TABLE github_events
(
    event_id bigint,
    event_type text,
    event_public boolean,
    repo_id bigint,
    payload jsonb,
    repo jsonb,
    user_id bigint,
    org jsonb,
    created_at timestamp
);

CREATE TABLE github_users
(
    user_id bigint,
    url text,
    login text,
    avatar_url text,
    gravatar_id text,
    display_login text
);
```

Pole `payload` `github_events` ma typ danych JSONB. JSONB jest rodzajem danych JSON w formie binarnej w postgres. Typ danych ułatwia przechowywanie elastycznego schematu w jednej kolumnie.

Postgres można `GIN` utworzyć indeks dla tego typu, który będzie indeksować każdy klucz i wartość w nim. Z indeksu, staje się szybkie i łatwe do zapytania ładunku w różnych warunkach. Przejdźmy do przodu i utwórz kilka indeksów, zanim załadujemy nasze dane. W psql:

```sql
CREATE INDEX event_type_index ON github_events (event_type);
CREATE INDEX payload_index ON github_events USING GIN (payload jsonb_path_ops);
```

Następnie weźmiemy te tabele Postgres w węźle koordynatora i powiedz hyperscale do niezależnego fragmentu ich przez pracowników. Aby to zrobić, uruchomimy kwerendę dla każdej tabeli określając klucz do niezależnego fragmentu. W bieżącym przykładzie będziemy odłamki zarówno zdarzeń `user_id`i użytkowników tabeli na:

```sql
SELECT create_distributed_table('github_events', 'user_id');
SELECT create_distributed_table('github_users', 'user_id');
```

Jesteśmy gotowi do załadowania danych. W psql nadal, shell się pobrać pliki:

```sql
\! curl -O https://examples.citusdata.com/users.csv
\! curl -O https://examples.citusdata.com/events.csv
```

Następnie należy załadować dane z plików do tabel rozproszonych:

```sql
SET CLIENT_ENCODING TO 'utf8';

\copy github_events from 'events.csv' WITH CSV
\copy github_users from 'users.csv' WITH CSV
```

## <a name="run-queries"></a>Uruchamianie zapytań

Teraz nadszedł czas na część zabawy, faktycznie uruchamiając kilka zapytań. Zacznijmy od prostego, `count (*)` aby zobaczyć, ile danych załadowaliśmy:

```sql
SELECT count(*) from github_events;
```

To działało ładnie. Wrócimy do tego rodzaju agregacji w nieco, ale na razie spójrzmy na kilka innych zapytań. W kolumnie `payload` JSONB jest sporo danych, ale różni się w zależności od typu zdarzenia. `PushEvent`zdarzenia zawierają rozmiar, który zawiera liczbę odrębnych zatwierdzeń dla wypychania. Możemy go użyć, aby znaleźć całkowitą liczbę zatwierdzeń na godzinę:

```sql
SELECT date_trunc('hour', created_at) AS hour,
       sum((payload->>'distinct_size')::int) AS num_commits
FROM github_events
WHERE event_type = 'PushEvent'
GROUP BY hour
ORDER BY hour;
```

Do tej pory zapytania dotyczyły\_wyłącznie zdarzeń github, ale możemy połączyć te informacje z użytkownikami github.\_ Ponieważ mamy podzielona zarówno użytkowników i zdarzeń`user_id`na ten sam identyfikator ( ), wiersze obu tabel z pasujących identyfikatorów użytkowników zostaną [współlokowane](https://docs.citusdata.com/en/stable/sharding/data_modeling.html#colocation) w tych samych węzłach bazy danych i można łatwo dołączyć.

Jeśli dołączymy `user_id`na , Hiperskala może wypchnąć wykonanie sprzężenia w dół do fragmentów do wykonania równolegle w węzłach procesu roboczego. Na przykład znajdźmy użytkowników, którzy utworzyli największą liczbę repozytoriów:

```sql
SELECT gu.login, count(*)
  FROM github_events ge
  JOIN github_users gu
    ON ge.user_id = gu.user_id
 WHERE ge.event_type = 'CreateEvent'
   AND ge.payload @> '{"ref_type": "repository"}'
 GROUP BY gu.login
 ORDER BY count(*) DESC;
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

W poprzednich krokach utworzono zasoby platformy Azure w grupie serwerów. Jeśli nie oczekujesz, że te zasoby będą potrzebne w przyszłości, usuń grupę serwerów. Naciśnij przycisk **Usuń** na stronie **Przegląd** grupy serwerów. Po wyświetleniu monitu na stronie podręcznej potwierdź nazwę grupy serwerów i kliknij przycisk **Usuń.**

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start opisano sposób aprowizowania grupy serwerów w skali hiperskali (Citus). Połączyłeś się z nim z psql, utworzono schemat i rozproszone dane.

Następnie wykonaj samouczek, aby utworzyć skalowalne aplikacje wielodostępne.
> [!div class="nextstepaction"]
> [Projektowanie wielodostępnych baz danych](https://aka.ms/hyperscale-tutorial-multi-tenant)
