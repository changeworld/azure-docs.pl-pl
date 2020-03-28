---
title: 'Samouczek: Projektowanie bazy danych z wieloma dzierżawami — hiperskali (Citus) — usługa Azure Database for PostgreSQL'
description: W tym samouczku pokazano, jak tworzyć, wypełniać i wysyłać zapytania tabele rozproszone w usłudze Azure Database for PostgreSQL Hyperscale (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 05/14/2019
ms.openlocfilehash: 17ac29de243f4abfff1cfc83fc6424799978bf0e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74978155"
---
# <a name="tutorial-design-a-multi-tenant-database-by-using-azure-database-for-postgresql--hyperscale-citus"></a>Samouczek: projektowanie bazy danych z wieloma dzierżawami przy użyciu usługi Azure Database for PostgreSQL — hiperskali (Citus)

W tym samouczku używasz usługi Azure Database for PostgreSQL — Hyperscale (Citus), aby dowiedzieć się, jak:

> [!div class="checklist"]
> * Tworzenie grupy serwerów hiperskali (Citus)
> * Tworzenie schematu za pomocą narzędzia psql
> * Tabele niezależnego fragmentu między węzłami
> * Pozyskiwanie przykładowych danych
> * Zapytanie o dane dzierżawy
> * Udostępnianie danych między dzierżawcami
> * Dostosowywanie schematu na dzierżawcę

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="use-psql-utility-to-create-a-schema"></a>Tworzenie schematu za pomocą narzędzia psql

Po połączeniu z usługą Azure Database for PostgreSQL — Hyperscale (Citus) przy użyciu psql można wykonać kilka podstawowych zadań. W tym samouczku można utworzyć aplikację internetową, która umożliwia reklamodawcom śledzenie ich kampanii.

Wiele firm może korzystać z aplikacji, więc utwórzmy tabelę do przechowywania firm, a drugą dla ich kampanii. W konsoli psql uruchom następujące polecenia:

```sql
CREATE TABLE companies (
  id bigserial PRIMARY KEY,
  name text NOT NULL,
  image_url text,
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL
);

CREATE TABLE campaigns (
  id bigserial,
  company_id bigint REFERENCES companies (id),
  name text NOT NULL,
  cost_model text NOT NULL,
  state text NOT NULL,
  monthly_budget bigint,
  blacklisted_site_urls text[],
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL,

  PRIMARY KEY (company_id, id)
);
```

Każda kampania będzie opłacać się wyświetlać reklamy. Dodaj tabelę reklam też, uruchamiając następujący kod w psql po kod powyżej:

```sql
CREATE TABLE ads (
  id bigserial,
  company_id bigint,
  campaign_id bigint,
  name text NOT NULL,
  image_url text,
  target_url text,
  impressions_count bigint DEFAULT 0,
  clicks_count bigint DEFAULT 0,
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, campaign_id)
    REFERENCES campaigns (company_id, id)
);
```

Na koniec będziemy śledzić statystyki dotyczące kliknięć i wyświetleń dla każdej reklamy:

```sql
CREATE TABLE clicks (
  id bigserial,
  company_id bigint,
  ad_id bigint,
  clicked_at timestamp without time zone NOT NULL,
  site_url text NOT NULL,
  cost_per_click_usd numeric(20,10),
  user_ip inet NOT NULL,
  user_data jsonb NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, ad_id)
    REFERENCES ads (company_id, id)
);

CREATE TABLE impressions (
  id bigserial,
  company_id bigint,
  ad_id bigint,
  seen_at timestamp without time zone NOT NULL,
  site_url text NOT NULL,
  cost_per_impression_usd numeric(20,10),
  user_ip inet NOT NULL,
  user_data jsonb NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, ad_id)
    REFERENCES ads (company_id, id)
);
```

Nowo utworzone tabele można zobaczyć na liście tabel teraz w psql, uruchamiając:

```postgres
\dt
```

Aplikacje z wieloma dzierżawcami mogą wymuszać unikatowość tylko na dzierżawę, dlatego wszystkie klucze podstawowe i zagraniczne zawierają identyfikator firmy.

## <a name="shard-tables-across-nodes"></a>Tabele niezależnego fragmentu między węzłami

Wdrożenie w hiperskali przechowuje wiersze tabel w różnych węzłach na podstawie wartości kolumny wyznaczonej przez użytkownika. Ta "kolumna dystrybucji" oznacza, który dzierżawca jest właścicielem wierszy.

Ustawmy kolumnę dystrybucji na\_identyfikator firmy, identyfikator dzierżawy. W psql uruchom następujące funkcje:

```sql
SELECT create_distributed_table('companies',   'id');
SELECT create_distributed_table('campaigns',   'company_id');
SELECT create_distributed_table('ads',         'company_id');
SELECT create_distributed_table('clicks',      'company_id');
SELECT create_distributed_table('impressions', 'company_id');
```

## <a name="ingest-sample-data"></a>Pozyskiwanie przykładowych danych

Poza psql teraz, w normalnym wierszu polecenia, pobierz przykładowe zestawy danych:

```bash
for dataset in companies campaigns ads clicks impressions geo_ips; do
  curl -O https://examples.citusdata.com/mt_ref_arch/${dataset}.csv
done
```

Powrót wewnątrz psql, luzem załadować dane. Pamiętaj, aby uruchomić psql w tym samym katalogu, w którym pobrano pliki danych.

```sql
SET CLIENT_ENCODING TO 'utf8';

\copy companies from 'companies.csv' with csv
\copy campaigns from 'campaigns.csv' with csv
\copy ads from 'ads.csv' with csv
\copy clicks from 'clicks.csv' with csv
\copy impressions from 'impressions.csv' with csv
```

Te dane będą teraz rozłożone na węzły procesu roboczego.

## <a name="query-tenant-data"></a>Zapytanie o dane dzierżawy

Gdy aplikacja żąda danych dla pojedynczej dzierżawy, baza danych może wykonać kwerendę w jednym węźle procesu roboczego. Kwerendy z jedną dzierżawą są filtrowane według identyfikatora pojedynczej dzierżawy. Na przykład następujące filtry `company_id = 5` zapytań dla reklam i wyświetleń. Spróbuj uruchomić go w psql, aby zobaczyć wyniki.

```sql
SELECT a.campaign_id,
       RANK() OVER (
         PARTITION BY a.campaign_id
         ORDER BY a.campaign_id, count(*) desc
       ), count(*) as n_impressions, a.id
  FROM ads as a
  JOIN impressions as i
    ON i.company_id = a.company_id
   AND i.ad_id      = a.id
 WHERE a.company_id = 5
GROUP BY a.campaign_id, a.id
ORDER BY a.campaign_id, n_impressions desc;
```

## <a name="share-data-between-tenants"></a>Udostępnianie danych między dzierżawcami

Do tej pory wszystkie tabele były dystrybuowane przez `company_id`, ale niektóre dane nie naturalnie "należą" do żadnej dzierżawy w szczególności i mogą być udostępniane. Na przykład wszystkie firmy na przykładowej platformie reklamowej mogą chcieć uzyskać informacje geograficzne dla swoich odbiorców na podstawie adresów IP.

Utwórz tabelę do przechowywania udostępnionych informacji geograficznych. Uruchom następujące polecenia w psql:

```sql
CREATE TABLE geo_ips (
  addrs cidr NOT NULL PRIMARY KEY,
  latlon point NOT NULL
    CHECK (-90  <= latlon[0] AND latlon[0] <= 90 AND
           -180 <= latlon[1] AND latlon[1] <= 180)
);
CREATE INDEX ON geo_ips USING gist (addrs inet_ops);
```

Następnie `geo_ips` należy wykonać "tabelę odwołań", aby zapisać kopię tabeli w każdym węźle procesu roboczego.

```sql
SELECT create_reference_table('geo_ips');
```

Załaduj go z przykładowymi danymi. Pamiętaj, aby uruchomić to polecenie w psql z katalogu, w którym pobrano zestaw danych.

```sql
\copy geo_ips from 'geo_ips.csv' with csv
```

Łączenie tabeli kliknięć za pomocą geoips\_jest skuteczne we wszystkich węzłach.
Oto dołączenie, aby znaleźć lokalizacje wszystkich, którzy kliknął na reklamę
290. Spróbuj uruchomić kwerendę w psql.

```sql
SELECT c.id, clicked_at, latlon
  FROM geo_ips, clicks c
 WHERE addrs >> c.user_ip
   AND c.company_id = 5
   AND c.ad_id = 290;
```

## <a name="customize-the-schema-per-tenant"></a>Dostosowywanie schematu na dzierżawcę

Każda dzierżawa może być konieczne do przechowywania specjalnych informacji, które nie są potrzebne przez innych. Jednak wszystkie dzierżawy współużytkują wspólną infrastrukturę o identycznym schemacie bazy danych. Gdzie mogą trafić dodatkowe dane?

Jedną z sztuczek jest użycie typu kolumny otwartej, takiej jak JSONB PostgreSQL.  Nasz schemat ma pole JSONB o `clicks` nazwie `user_data`.
Firma (powiedzmy firma pięć), może użyć kolumny, aby śledzić, czy użytkownik jest na urządzeniu przenośnym.

Oto zapytanie, które można znaleźć, kto klika więcej: mobilnych lub tradycyjnych użytkowników.

```sql
SELECT
  user_data->>'is_mobile' AS is_mobile,
  count(*) AS count
FROM clicks
WHERE company_id = 5
GROUP BY user_data->>'is_mobile'
ORDER BY count DESC;
```

Możemy zoptymalizować tę kwerendę dla jednej firmy, tworząc [indeks częściowy.](https://www.postgresql.org/docs/current/static/indexes-partial.html)

```sql
CREATE INDEX click_user_data_is_mobile
ON clicks ((user_data->>'is_mobile'))
WHERE company_id = 5;
```

Ogólniej rzecz biorąc, możemy utworzyć [indeksy GIN](https://www.postgresql.org/docs/current/static/gin-intro.html) na każdym kluczu i wartości w kolumnie.

```sql
CREATE INDEX click_user_data
ON clicks USING gin (user_data);

-- this speeds up queries like, "which clicks have
-- the is_mobile key present in user_data?"

SELECT id
  FROM clicks
 WHERE user_data ? 'is_mobile'
   AND company_id = 5;
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

W poprzednich krokach utworzono zasoby platformy Azure w grupie serwerów. Jeśli nie oczekujesz, że te zasoby będą potrzebne w przyszłości, usuń grupę serwerów. Naciśnij przycisk *Usuń* na stronie *Przegląd* grupy serwerów. Po wyświetleniu monitu na stronie podręcznej potwierdź nazwę grupy serwerów i kliknij przycisk *Usuń.*

## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiesz się, jak aprowizować grupę serwerów hiperskali (Citus). Połączyłeś się z nim z psql, utworzono schemat i rozproszone dane. Nauczysz się wyszukiwać dane zarówno w obrębie, jak i między dzierżawcami oraz dostosowywać schemat na dzierżawę.

Następnie dowiedz się więcej o pojęciach hiperskali.
> [!div class="nextstepaction"]
> [Typy węzłów hiperskali](https://aka.ms/hyperscale-concepts)
