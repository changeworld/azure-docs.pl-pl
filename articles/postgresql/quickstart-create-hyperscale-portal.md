---
title: Azure Database for PostgreSQL — w Hiperskali (Citus) (wersja zapoznawcza) Szybki Start
description: Przewodnik Szybki Start, tworzyć i wykonywać zapytania rozproszonego tabel w usłudze Azure Database for postgresql w warstwie na dużą skalę (Citus) (wersja zapoznawcza).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: quickstart
ms.date: 05/14/2019
ms.openlocfilehash: efc3801ab03f739761a41bec754f975fe43dcd8e
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65792021"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---hyperscale-citus-preview-in-the-azure-portal"></a>Szybki start: Tworzenie usługi Azure Database for PostgreSQL — na dużą skalę (Citus) (wersja zapoznawcza) w witrynie Azure portal

Azure Database for PostgreSQL to usługa zarządzana, która służy do uruchamiania i skalowania w chmurze baz danych PostgreSQL o wysokiej dostępności, a także zarządzania nimi. Ten przewodnik Szybki Start przedstawia sposób tworzenia usługi Azure Database for PostgreSQL — w Hiperskali (Citus) (wersja zapoznawcza) grupy serwerów przy użyciu witryny Azure portal. Dowiesz się danych rozproszonych: tabele fragmentowania między węzłami, wprowadzane przykładowych danych i wykonywania zapytań, które są wykonywane na wielu węzłach.

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="create-and-distribute-tables"></a>Tworzenie i rozpowszechnianie tabel

Po nawiązaniu połączenia do węzła koordynatora w hiperskali, za pomocą narzędzia psql, możesz wykonać niektóre podstawowe zadania.

W Hiperskali serwery dostępne są trzy typy tabel:

- Tabele rozproszone lub podzielonych na fragmenty (rozłożyć ułatwia skalowanie i wydajność oraz przetwarzania równoległego)
- Tabele odwołań (wiele kopii obsługiwane)
- Lokalne tablice (często używane dla tabel wewnętrznych administratora)

W tym przewodniku Szybki Start przede wszystkim skupimy się na rozproszone tabele i pobierania z nich korzystać.

Model danych, będziemy pracować jest prosty: dane użytkownika i zdarzeń z usługi GitHub. Zdarzenia obejmują tworzenie rozwidlenia, zatwierdzeń git związane z organizacji i nie tylko.

Po nawiązaniu połączenia za pomocą narzędzia psql, Utwórzmy naszych tabel. W konsoli programu psql Uruchom:

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

`payload` Pole `github_events` ma JSONB typu danych. JSONB jest typem danych JSON, w formacie binarnym w Postgres. Typ danych można łatwo przechowywać elastycznego schematu w jednej kolumnie.

Można utworzyć Postgres `GIN` indeks dla tego typu, który będzie indeks każdego klucza i wartości w nim. Przy użyciu indeksu, staje się szybkie i łatwe do wykonywania zapytań ładunku z różnych warunków. Rozpocznijmy i Utwórz kilka indeksów, zanim możemy załadować dane. W psql:

```sql
CREATE INDEX event_type_index ON github_events (event_type);
CREATE INDEX payload_index ON github_events USING GIN (payload jsonb_path_ops);
```

Następnie utworzymy podejmowanie tych tabel Postgres na węźle koordynatora i poinformować o dużej skali do fragmentu je przez pracowników. W tym celu uruchomimy zapytania dla każdej tabeli, określając klucz do fragmentu je na. W bieżącym przykładzie będziemy fragmentu zdarzeń i użytkownicy tabeli na `user_id`:

```sql
SELECT create_distributed_table('github_events', 'user_id');
SELECT create_distributed_table('github_users', 'user_id');
```

Możemy przystąpić do ładowania danych. W psql nadal powłoki do pobierania plików:

```sql
\! curl -O https://examples.citusdata.com/users.csv
\! curl -O https://examples.citusdata.com/events.csv
```

Następnie Załaduj dane z plików w tabelach rozproszonych:

```sql
\copy github_events from 'events.csv' WITH CSV
\copy github_users from 'users.csv' WITH CSV
```

## <a name="run-queries"></a>Uruchamianie zapytań

Teraz nadszedł czas na fun części, w rzeczywistości uruchamiania niektórych zapytań. Zacznijmy od prostego `count (*)` aby zobaczyć, jak dużo danych wczytany:

```sql
SELECT count(*) from github_events;
```

Który działało poprawnie. Firma Microsoft będzie wróć do tego sortowania agregacja w nieco, ale na razie Przyjrzyjmy się kilku innych zapytań. W ramach JSONB `payload` kolumna jest dobry bitu danych, ale różni się zależnie od typu zdarzenia. `PushEvent` zdarzenia zawierają rozmiar, która obejmuje liczbę unikatowych zatwierdzeń dla wypychania. Firma Microsoft służy do znajdowania łącznej liczby zatwierdzeń na godzinę:

```sql
SELECT date_trunc('hour', created_at) AS hour,
       sum((payload->>'distinct_size')::int) AS num_commits
FROM github_events
WHERE event_type = 'PushEvent'
GROUP BY hour
ORDER BY hour;
```

Do tej pory zapytania mają brać github\_zdarzenia wyłącznie, ale możemy połączyć te informacje za pomocą usługi github\_użytkowników. Ponieważ możemy podzielonej na fragmenty użytkowników i zdarzeń w tym samym identyfikatorze (`user_id`), wiersze z obu tabel ze zgodnymi identyfikatorami użytkownika będzie [wspólnie przechowywane](https://docs.citusdata.com/en/stable/sharding/data_modeling.html#colocation) w tej samej bazy danych węzłów i można łatwo łączyć.

Jeśli firma Microsoft odbędzie `user_id`, na dużą skalę można wypchnąć wykonania sprzężenia w dół do fragmentów w celu wykonania równoległego na węzłach procesu roboczego. Na przykład znajdźmy użytkowników, którzy utworzyli z największą liczbą repozytoriów:

```sql
SELECT login, count(*)
FROM github_events ge
JOIN github_users gu
ON ge.user_id = gu.user_id
WHERE event_type = 'CreateEvent' AND
      payload @> '{"ref_type": "repository"}'
GROUP BY login
ORDER BY count(*) DESC;
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

W poprzednich krokach utworzono zasoby platformy Azure w grupie serwerów. Jeśli nie będziesz już potrzebować tych zasobów w przyszłości, Usuń grupę serwera. Naciśnij klawisz **Usuń** znajdujący się w **Przegląd** strony grupy serwerów. Po wyświetleniu monitu na stronie wyskakujące, upewnij się, nazwę grupy serwerów i kliknij końcowe **Usuń** przycisku.

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki Start przedstawiono sposób aprowizowania grupy serwerów na dużą skalę (Citus). Dołączone do niego za pomocą narzędzia psql, utworzyć schemat i rozproszonych danych.

Następnie postępuj zgodnie z samouczka, aby tworzyć skalowalne aplikacje wielodostępne.
> [!div class="nextstepaction"]
> [Projektowanie bazy danych z wieloma dzierżawcami](https://aka.ms/hyperscale-tutorial-multi-tenant)
