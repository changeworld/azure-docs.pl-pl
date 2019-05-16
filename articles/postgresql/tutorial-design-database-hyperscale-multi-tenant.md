---
title: Projektowanie wielodostępną bazą danych z usługą Azure Database for PostgreSQL — w Hiperskali (Citus) (wersja zapoznawcza) samouczek
description: W tym samouczku pokazano, jak tworzyć i wypełnić oraz zapytania rozproszone tabele w usłudze Azure Database for postgresql w warstwie na dużą skalę (Citus) (wersja zapoznawcza).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 05/14/2019
ms.openlocfilehash: 73d7aebf3dbff59320e0ef92cbd54811503c71b4
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65757626"
---
# <a name="tutorial-design-a-multi-tenant-database-by-using-azure-database-for-postgresql--hyperscale-citus-preview"></a>Samouczek: Projektowanie wielodostępną bazą danych przy użyciu usługi Azure Database for PostgreSQL — w Hiperskali (Citus) (wersja zapoznawcza)

W tym samouczku możesz używać usługi Azure Database for PostgreSQL — w Hiperskali (Citus) (wersja zapoznawcza), aby dowiedzieć się, jak:

> [!div class="checklist"]
> * Utwórz grupę serwerów (Citus) na dużą skalę
> * Utwórz schemat przy użyciu narzędzia psql
> * Tabele fragmentów w węzłach
> * Pozyskiwanie przykładowych danych
> * Zapytania o dane dzierżawy
> * Udostępnianie danych między dzierżawami
> * Dostosowywanie schematu dla dzierżawcy

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="use-psql-utility-to-create-a-schema"></a>Utwórz schemat przy użyciu narzędzia psql

Po nawiązaniu połączenia z usługi Azure Database for PostgreSQL — na dużą skalę (Citus) (wersja zapoznawcza) za pomocą narzędzia psql, można wykonać niektóre podstawowe zadania. Ten samouczek przeprowadzi Cię przez tworzenie aplikacji sieci web, umożliwiająca reklamodawców do śledzenia swoich kampanii.

Wiele firm można korzystać z aplikacji, więc Utwórzmy tabelę do przechowywania firm i inny wpis dla swoich kampanii. W konsoli narzędzia psql uruchom następujące polecenia:

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

Każdej kampanii za uruchamianie reklam. Dodaj tabelę dla reklam, uruchamiając poniższy kod w psql po powyższym kodzie:

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

Na koniec będzie Śledzimy statystyk określających, kliknięć i wyświetleń, w przypadku każdej reklamy:

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

Nowo utworzonego tabelami w listę tabel, teraz dostępna w psql można wyświetlić, uruchamiając:

```postgres
\dt
```

Aplikacje wielodostępne może wymuszać unikatowość tylko na dzierżawę, dlatego wszystkie klucze podstawowe i obce obejmują identyfikator firmy.

## <a name="shard-tables-across-nodes"></a>Tabele fragmentów w węzłach

Wdrożenia na dużą skalę wiersze tabeli są przechowywane w różnych węzłach, na podstawie wartości kolumny wyznaczony przez użytkownika. Tej dzierżawy, który jest właścicielem wiersze, które znakami "kolumna dystrybucji".

Ustawmy kolumny dystrybucji firmy\_id, identyfikator dzierżawy. W psql uruchom następujące funkcje:

```sql
SELECT create_distributed_table('companies',   'id');
SELECT create_distributed_table('campaigns',   'company_id');
SELECT create_distributed_table('ads',         'company_id');
SELECT create_distributed_table('clicks',      'company_id');
SELECT create_distributed_table('impressions', 'company_id');
```

## <a name="ingest-sample-data"></a>Pozyskiwanie przykładowych danych

Poza teraz psql w wierszu polecenia normalne pobieranie przykładowych zestawów danych:

```bash
for dataset in companies campaigns ads clicks impressions geo_ips; do
  curl -O https://examples.citusdata.com/mt_ref_arch/${dataset}.csv
done
```

Zwrotne wewnątrz psql zbiorczo załadować dane. Pamiętaj uruchomić wersja narzędzia psql w tym samym katalogu, w której pobrano pliki danych.

```sql
\copy companies from 'companies.csv' with csv
\copy campaigns from 'campaigns.csv' with csv
\copy ads from 'ads.csv' with csv
\copy clicks from 'clicks.csv' with csv
\copy impressions from 'impressions.csv' with csv
```

Tych danych będzie rozpowszechniana w węzłach procesu roboczego.

## <a name="query-tenant-data"></a>Zapytania o dane dzierżawy

Gdy aplikacja żąda danych dla jednej dzierżawy, bazy danych można wykonać zapytania w węźle pojedynczego procesu roboczego. Filtr zapytań jednej dzierżawy za pomocą identyfikatora pojedynczej dzierżawy. Na przykład poniższe zapytanie filtry `company_id = 5` reklam i wyświetleń. Spróbuj ją uruchomić w psql, aby wyświetlić wyniki.

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

## <a name="share-data-between-tenants"></a>Udostępnianie danych między dzierżawami

Do tej pory wszystkie tabele zostały przekazane przez `company_id`, ale niektóre dane nie naturalnie "należy" do dowolnej dzierżawy w szczególności i mogą być udostępniane. Na przykład wszystkich firm w przykładzie platformy ad może potrzebować informacji geograficznych dla swoich odbiorców na podstawie adresów IP.

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

Następnie należy `geo_ips` "Tabela odwołania" przechowywać kopię tabeli w każdym węźle procesu roboczego.

```sql
SELECT create_reference_table('geo_ips');
```

Załaduj do niej przykładowe dane. Pamiętaj, aby uruchomić to polecenie w psql z wewnątrz katalogu, w której pobrano zestaw danych.

```sql
\copy geo_ips from 'geo_ips.csv' with csv
```

Łączenie tabeli kliknięć za pomocą geograficznie\_adresy IP są wydajne we wszystkich węzłach.
Oto sprzężenia do znajdowania lokalizacji wszystkich osób, który kliknął ad
290. Spróbuj uruchomić zapytanie w psql.

```sql
SELECT c.id, clicked_at, latlon
  FROM geo_ips, clicks c
 WHERE addrs >> c.user_ip
   AND c.company_id = 5
   AND c.ad_id = 290;
```

## <a name="customize-the-schema-per-tenant"></a>Dostosowywanie schematu dla dzierżawcy

Każda dzierżawa może być konieczne przechowywanie informacji specjalnych nie jest wymagany przez inne osoby. Jednak wszystkie dzierżawy udostępniać wspólna infrastruktura schemat identyczne bazy danych. Gdzie można dodatkowe dane?

Jednym sposobem jest użycie typ kolumny nieograniczony, takich jak PostgreSQL JSONB.  Nasze schemat zawiera pole JSONB `clicks` o nazwie `user_data`.
Firmie (Załóżmy, że pięć), można użyć kolumny do sprawdzenia, czy użytkownik znajduje się na urządzeniu przenośnym.

Oto zapytanie w celu określenia, który kliknie przycisk więcej: mobilnych lub tradycyjny odwiedzających.

```sql
SELECT
  user_data->>'is_mobile' AS is_mobile,
  count(*) AS count
FROM clicks
WHERE company_id = 5
GROUP BY user_data->>'is_mobile'
ORDER BY count DESC;
```

Firma Microsoft można zoptymalizować to zapytanie dla jednej firmy, tworząc [częściowe indeksu](https://www.postgresql.org/docs/current/static/indexes-partial.html).

```sql
CREATE INDEX click_user_data_is_mobile
ON clicks ((user_data->>'is_mobile'))
WHERE company_id = 5;
```

Ogólnie rzecz biorąc, możemy utworzyć [indeksów GIN](https://www.postgresql.org/docs/current/static/gin-intro.html) na każdy klucz i wartość w kolumnie.

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

W poprzednich krokach utworzono zasoby platformy Azure w grupie serwerów. Jeśli nie będziesz już potrzebować tych zasobów w przyszłości, Usuń grupę serwera. Naciśnij klawisz *Usuń* znajdujący się w *Przegląd* strony grupy serwerów. Po wyświetleniu monitu na stronie wyskakujące, upewnij się, nazwę grupy serwerów i kliknij końcowe *Usuń* przycisku.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono sposób aprowizowania grupy serwerów na dużą skalę (Citus). Dołączone do niego za pomocą narzędzia psql, utworzyć schemat i rozproszonych danych. Pokazaliśmy ci, wykonywać zapytania względem danych w ramach i między dzierżawami i dostosowanie schematu na dzierżawę.

Następnie Dowiedz się więcej o koncepcjach na dużą skalę.
> [!div class="nextstepaction"]
> [Typy węzłów na dużą skalę](https://aka.ms/hyperscale-concepts)
