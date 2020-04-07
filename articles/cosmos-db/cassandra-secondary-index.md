---
title: Indeksowanie konta interfejsu API usługi Azure Cosmos DB Cassandra
description: Dowiedz się, jak działa indeksowanie pomocnicze na koncie interfejsu API usługi Azure Cosmos DB Cassandra.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/04/2020
ms.author: thvankra
ms.reviewer: sngun
ms.openlocfilehash: 7de38097acdbfa1f9c9b90f3051c68dec5465b32
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80758029"
---
# <a name="secondary-indexing-in-azure-cosmos-db-cassandra-api"></a>Indeksowanie pomocnicze w interfejsie API Cassandra usługi Azure Cosmos DB

Interfejs API Cassandra w usłudze Azure Cosmos DB wykorzystuje podstawową infrastrukturę indeksowania, aby udostępnić siłę indeksowania, która jest nieodłącznym elementem platformy. Jednak w przeciwieństwie do podstawowego interfejsu API SQL interfejs API Cassandra w usłudze Azure Cosmos DB domyślnie nie indeksuje wszystkich atrybutów. Zamiast tego obsługuje indeksowanie pomocnicze, aby utworzyć indeks dla niektórych atrybutów, który zachowuje się tak samo jak Apache Cassandra.  

Ogólnie rzecz biorąc nie zaleca się wykonywania kwerend filtrowania w kolumnach, które nie są podzielone na partycje. Należy użyć zezwalaj na filtrowanie składni jawnie, co powoduje operację, która może nie działać dobrze. W usłudze Azure Cosmos DB można uruchomić takie zapytania na atrybuty niskiej kardynalności, ponieważ fan out między partycjami, aby pobrać wyniki.

Nie zaleca się tworzenia indeksu w często aktualizowanej kolumnie. Rozsądne jest utworzenie indeksu podczas definiowania tabeli. Dzięki temu dane i indeksy są w spójnym stanie. W przypadku utworzenia nowego indeksu na istniejących danych, obecnie nie można śledzić zmiany postępu indeksu dla tabeli. Jeśli chcesz śledzić postęp tej operacji, musisz poprosić o zmianę postępu za pośrednictwem [biletu pomocy technicznej.]( https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)


> [!NOTE]
> Indeks pomocniczy nie jest obsługiwany w następujących obiektach:
> - typów danych, takich jak typy kolekcji mrożone, dziesiętne i typy wariantów.
> - Kolumny statyczne
> - Klucze klastrowania

## <a name="indexing-example"></a>Przykład indeksowania

Najpierw utwórz przykładową przestrzeń kluczy i tabelę, uruchamiając następujące polecenia w wierszu powłoki CQL:

```shell
CREATE KEYSPACE sampleks WITH REPLICATION = {'class' : 'SimpleStrategy'};
CREATE TABLE sampleks.t1(user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=400;
``` 

Następnie wstaw przykładowe dane użytkownika za pomocą następujących poleceń:

```shell
insert into sampleks.t1(user_id,lastname) values (1, 'nishu');
insert into sampleks.t1(user_id,lastname) values (2, 'vinod');
insert into sampleks.t1(user_id,lastname) values (3, 'bat');
insert into sampleks.t1(user_id,lastname) values (5, 'vivek');
insert into sampleks.t1(user_id,lastname) values (6, 'siddhesh');
insert into sampleks.t1(user_id,lastname) values (7, 'akash');
insert into sampleks.t1(user_id,lastname) values (8, 'Theo');
insert into sampleks.t1(user_id,lastname) values (9, 'jagan');
```

Jeśli spróbujesz wykonać następującą instrukcję, napotkasz `ALLOW FILTERING`błąd, który prosi o użycie: 

```shell
select user_id, lastname from sampleks.t1 where lastname='nishu';
``` 

Chociaż interfejs API Cassandra obsługuje ZEZWALAJ NA FILTROWANIE, jak wspomniano w poprzedniej sekcji, nie jest zalecane. Zamiast tego należy utworzyć indeks w sposób pokazany w poniższym przykładzie:

```shell
CREATE INDEX ON sampleks.t1 (lastname);
```
Po utworzeniu indeksu w polu "lastname" można teraz pomyślnie uruchomić poprzednią kwerendę. Za pomocą interfejsu API Cassandra w usłudze Azure Cosmos DB nie trzeba podawać nazwy indeksu. Używany jest indeks `tablename_columnname_idx` domyślny z formatem. Na przykład ` t1_lastname_idx` jest nazwą indeksu dla poprzedniej tabeli.

## <a name="dropping-the-index"></a>Upuszczenie indeksu 
Musisz wiedzieć, co nazwa indeksu jest do spadku indeksu. Uruchom `desc schema` polecenie, aby uzyskać opis tabeli. Dane wyjściowe tego polecenia zawierają nazwę `CREATE INDEX tablename_columnname_idx ON keyspacename.tablename(columnname)`indeksu w formacie . Następnie można użyć nazwy indeksu, aby usunąć indeks, jak pokazano w poniższym przykładzie:

```shell
drop index sampleks.t1_lastname_idx;
```

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak działa [automatyczne indeksowanie](index-overview.md) w usłudze Azure Cosmos DB
* [Funkcje bazy danych Apache Cassandra obsługiwane przez interfejs API Cassandra usługi Azure Cosmos DB](cassandra-support.md)
