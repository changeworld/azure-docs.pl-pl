---
title: Azure Database for PostgreSQL — w Hiperskali (Citus) (wersja zapoznawcza) Szybki Start
description: Przewodnik Szybki Start, tworzyć i wykonywać zapytania rozproszonego tabel w usłudze Azure Database for postgresql w warstwie na dużą skalę (Citus) (wersja zapoznawcza).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: quickstart
ms.date: 05/06/2019
ms.openlocfilehash: 4271d94f07125a870cc4aa859b01db819d583f40
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2019
ms.locfileid: "65406445"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---hyperscale-citus-preview-in-the-azure-portal"></a>Szybki start: Tworzenie usługi Azure Database for PostgreSQL — na dużą skalę (Citus) (wersja zapoznawcza) w witrynie Azure portal

Azure Database for PostgreSQL to usługa zarządzana, która służy do uruchamiania i skalowania w chmurze baz danych PostgreSQL o wysokiej dostępności, a także zarządzania nimi. Ten przewodnik Szybki Start przedstawia sposób tworzenia usługi Azure Database for PostgreSQL — w Hiperskali (Citus) (wersja zapoznawcza) grupy serwerów przy użyciu witryny Azure portal. Będzie Eksplorowanie danych rozproszonych: tabele fragmentowania między węzłami, wprowadzane przykładowych danych i wykonywania zapytań, które są wykonywane na wielu węzłach.

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
   - Nazwa grupy serwerów: wprowadź unikatową nazwę dla nowej grupy serwerów będzie również używany dla poddomeny serwera.
   - Nazwa użytkownika administratora: wprowadź unikatową nazwę użytkownika, jego posłuży później do łączenia z bazą danych.
   - Hasło: musi być co najmniej ośmiu znaków i musi zawierać znaki z trzech z następujących kategorii — litery na wielkie litery angielskie, angielskiej małe litery, cyfry (0 – 9) i znaki inne niż alfanumeryczne (!, $, #, % itp.)
   - Lokalizacja: Użyj lokalizacji, która jest najbliżej Twoich użytkowników, aby zapewnić im najszybszy dostęp do danych.

   > [!IMPORTANT]
   > Login i hasło administratora serwera określone w tym miejscu będą wymagane do logowania do serwera i jego baz danych w późniejszej części tego przewodnika Szybki start. Zapamiętaj lub zapisz te informacje do wykorzystania w przyszłości.

5. Kliknij przycisk **Konfigurowanie grupy serwerów**. Pozostaw ustawienia w tej sekcji bez zmian, a następnie kliknij przycisk **Zapisz**.
6. Kliknij przycisk **przeglądu + Utwórz** i następnie **Utwórz** aby aprowizować serwer. Aprowizacja zajmuje kilka minut.
7. Strony nastąpi przekierowanie do monitorowania wdrożenia. Zmiany stanu na żywo z **wdrożenia jest w toku** do **swoje wdrożenie jest ukończone**, kliknij przycisk **dane wyjściowe** element menu po lewej stronie.
8. Strony danych wyjściowych będzie zawierał nazwę hosta koordynatora za pomocą przycisku obok niej, aby skopiować wartość do Schowka. Zarejestruj te informacje w celu późniejszego użycia.

## <a name="configure-a-server-level-firewall-rule"></a>Konfigurowanie reguły zapory na poziomie serwera

Usługi Azure Database for PostgreSQL — w Hiperskali (Citus) (wersja zapoznawcza) używa usługi zapory na poziomie serwera. Domyślnie Zapora uniemożliwia wszystkich zewnętrznym aplikacjom i narzędziom łączenie się z węzłem koordynatora i wszelkimi bazami danych wewnątrz. Firma Microsoft należy dodać regułę otwierającą zaporę dla określonego zakresu adresów IP.

1. Z **dane wyjściowe** sekcji, w której został skopiowany wcześniej z nazwą hosta węzła koordynatora, kliknij przycisk Wstecz do **Przegląd** elementu menu.

2. Nazwa grupy skalowania wdrożenia będzie zaczynała się od "sg-". Znaleźć go na liście zasobów, a następnie kliknij go.

3. Kliknij przycisk **zapory** w obszarze **zabezpieczeń** w menu po lewej stronie.

4. Kliknij łącze **+ Dodaj regułę zapory dla bieżącego adresu IP klienta**. Na koniec kliknij **Zapisz** przycisku.

5. Kliknij pozycję **Zapisz**.

   > [!NOTE]
   > Serwer Azure PostgreSQL komunikuje się przez port 5432. Jeśli próbujesz nawiązać połączenie z sieci firmowej, ruch wychodzący na porcie 5432 może być zablokowany przez zaporę sieciową. Jeśli wystąpi taka sytuacja, nie będzie można nawiązać połączenia z serwerem usługi Azure SQL Database, chyba że dział IT otworzy port 5432.
   >

## <a name="connect-to-the-database-using-psql-in-cloud-shell"></a>Połączenia z bazą danych za pomocą narzędzia psql w usłudze Cloud Shell

Teraz użyjmy narzędzia wiersza polecenia [psql](https://www.postgresql.org/docs/current/app-psql.html), aby nawiązać połączenie z serwerem usługi Azure Database for PostgreSQL.
1. Uruchom powłokę Azure Cloud Shell za pośrednictwem ikony terminala w górnym okienku nawigacji.

   ![Azure Database for PostgreSQL — ikona terminala powłoki Azure Cloud Shell](./media/quickstart-create-hyperscale-portal/psql-cloud-shell.png)

2. Powłoka Azure Cloud Shell zostanie otwarta w przeglądarce, umożliwiając wpisywanie poleceń powłoki bash.

   ![Azure Database for PostgreSQL — znak zachęty powłoki Azure Shell Bash](./media/quickstart-create-hyperscale-portal/psql-bash.png)

3. W wierszu polecenia powłoki Cloud Shell nawiąż połączenie z serwerem usługi Azure Database for PostgreSQL za pomocą poleceń psql. Następujący format służy do łączenia z serwerem usługi Azure Database for PostgreSQL przy użyciu narzędzia [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html):
   ```bash
   psql --host=<myserver> --username=myadmin --dbname=citus
   ```

   Na przykład następujące polecenie umożliwia nawiązanie domyślna baza danych o nazwie **citus** na Twoim serwerze PostgreSQL **mydemoserver.postgres.database.azure.com** za pomocą poświadczeń dostępu. Po wyświetleniu monitu wprowadź hasło administratora serwera.

   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --username=myadmin --dbname=citus
   ```

## <a name="create-and-distribute-tables"></a>Tworzenie i rozpowszechnianie tabel

Po nawiązaniu połączenia do węzła koordynatora w hiperskali, za pomocą narzędzia psql, możesz wykonać niektóre podstawowe zadania.

W Hiperskali serwery dostępne są trzy typy tabel:

- Tabele rozproszone lub podzielonych na fragmenty (rozłożyć ułatwia skalowanie i wydajność oraz przetwarzania równoległego)
- Tabele odwołań (wiele kopii obsługiwane)
- Lokalne tablice (często używane dla tabel wewnętrznych administratora)

W tym przewodniku Szybki Start przede wszystkim skupimy się na rozproszone tabele i pobierania z nich korzystać.

Model danych, będziemy pracować jest prosty: dane użytkownika i zdarzeń z usługi GitHub. Zdarzenia obejmują tworzenie rozwidlenia, zatwierdzeń git związane z organizacji i nie tylko.

Po nawiązaniu połączenia za pomocą narzędzia psql możemy utworzyć naszych tabel. W konsoli programu psql Uruchom:

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

`payload` Pole `github_events` ma JSONB typu danych. JSONB jest typem danych JSON, w formacie binarnym w Postgres. Dzięki temu można łatwo przechowywać więcej elastycznego schematu w jednej kolumnie.

Można utworzyć Postgres `GIN` indeksu dla tego typu, która indeksuje każdego klucza i wartości w nim. Przy użyciu indeksu, staje się szybkie i łatwe do wykonywania zapytań ładunku z różnych warunków. Rozpocznijmy i Utwórz kilka indeksów, zanim możemy załadować dane. W psql:

```sql
CREATE INDEX event_type_index ON github_events (event_type);
CREATE INDEX payload_index ON github_events USING GIN (payload jsonb_path_ops);
```

Następnie utworzymy podejmowanie tych tabel Postgres na węźle koordynatora i poinformować o dużej skali do fragmentu je przez pracowników. W tym celu uruchomimy zapytania dla każdej tabeli, określając klucz do fragmentu je na. W bieżącym przykładzie będziemy fragmentu zdarzeń i użytkownicy tabeli na `user_id`:

```sql
SELECT create_distributed_table('github_events', 'user_id');
SELECT create_distributed_table('github_users', 'user_id');
```

Możemy przystąpić do ładowania danych. Pobierz pliki dwa przykładowe [users.csv](https://examples.citusdata.com/users.csv) i [zdarzenia.csv](https://examples.citusdata.com/events.csv). Po pobraniu plików, połączyć z bazą danych za pomocą narzędzia psql, uważaj uruchomić narzędzie psql z katalog zawierający pliki, które zostały pobrane. Ładowanie danych za pomocą `\copy` polecenia:

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
