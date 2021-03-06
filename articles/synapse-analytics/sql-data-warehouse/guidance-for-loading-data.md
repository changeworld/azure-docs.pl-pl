---
title: Najważniejsze wskazówki dotyczące ładowania danych dla puli SQL synapse
description: Zalecenia i optymalizacje wydajności do ładowania danych przy użyciu puli SYNAPSE SQL.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: e170a789727fb0de36705895245cc638d30ee3d7
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745496"
---
# <a name="best-practices-for-loading-data-using-synapse-sql-pool"></a>Najważniejsze wskazówki dotyczące ładowania danych przy użyciu puli synapse SQL

W tym artykule dowiesz się zalecenia i optymalizacje wydajności do ładowania danych przy użyciu puli SQL.

## <a name="preparing-data-in-azure-storage"></a>Przygotowywanie danych w usłudze Azure Storage

Aby zminimalizować opóźnienia, współlokuj warstwę magazynu i pulę SQL.

Podczas eksportowania danych do formatu plików ORC mogą pojawić się błędy braku pamięci Java, jeśli w danych znajdują się duże kolumny tekstu. Aby obejść to ograniczenie, można wyeksportować tylko podzestaw wszystkich kolumn.

PolyBase nie może załadować wierszy, które mają więcej niż 1 000 000 bajtów danych. Dane umieszczane w plikach tekstowych w usłudze Azure Blob Storage lub Azure Data Lake Store muszą zawierać mniej niż 1 000 000 bajtów danych. Ograniczenie liczby bajtów jest niezależne od zdefiniowanego schematu tabeli.

Różne typy plików mają różną charakterystykę wydajności. Najszybsze ładowanie zapewnia użycie skompresowanych rozdzielanych plików tekstowych. Różnica w wydajności między kodowaniem UTF-8 a UTF-16 jest minimalna.

Duże pliki skompresowane można podzielić na mniejsze.

## <a name="running-loads-with-enough-compute"></a>Uruchamianie zadań ładowania przy użyciu wystarczających zasobów obliczeniowych

Aby uzyskać większą szybkość ładowania, uruchamiaj tylko jedno zadanie ładowania naraz. Jeśli nie jest to możliwe, uruchom minimalną liczbę obciążeń jednocześnie. Jeśli oczekujesz zadania ładowania dużych, należy rozważyć skalowanie puli SQL przed obciążeniem.

Aby uruchamiać zadania ładowania przy użyciu odpowiednich zasobów obliczeniowych, utwórz użytkowników ładujących na potrzeby uruchamiania ładowania. Przypisz każdego użytkownika ładującego do określonej klasy zasobów lub grupy obciążenia. Aby uruchomić obciążenie, zaloguj się jako jeden z użytkowników ładowania, a następnie uruchom obciążenie. Ładowanie zostanie uruchomione przy użyciu klasy zasobów przypisanej do użytkownika.  

> [!NOTE]
> Jest to prostsza metoda niż zmienianie klasy zasobów użytkownika odpowiednio do bieżących potrzeb.

### <a name="example-of-creating-a-loading-user"></a>Przykład tworzenia użytkownika ładującego

Ten przykład umożliwia utworzenie użytkownika ładującego dla klasy zasobów staticrc20. Pierwszym krokiem jest **nawiązanie połączenia z główną bazą danych** i utworzenie nazwy logowania.

```sql
   -- Connect to master
   CREATE LOGIN LoaderRC20 WITH PASSWORD = 'a123STRONGpassword!';
```

Połącz się z pulą SQL i utwórz użytkownika. Poniższy kod zakłada, że masz połączenie z bazą danych o nazwie mySampleDataWarehouse. Pokazuje, jak utworzyć użytkownika o nazwie LoaderRC20 i daje uprawnienia kontroli użytkownika w bazie danych. Następnie dodaje użytkownika jako członka roli bazy danych staticrc20.  

```sql
   -- Connect to the database
   CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
   GRANT CONTROL ON DATABASE::[mySampleDataWarehouse] to LoaderRC20;
   EXEC sp_addrolemember 'staticrc20', 'LoaderRC20';
```

Aby uruchomić obciążenie z zasobami dla klas zasobów staticRC20, zaloguj się jako LoaderRC20 i uruchom obciążenie.

Lepszym rozwiązaniem jest uruchamianie ładowania przy użyciu statycznych, a nie dynamicznych klas zasobów. Za pomocą klas zasobów statycznych gwarantuje te same zasoby, niezależnie od [jednostek magazynu danych](what-is-a-data-warehouse-unit-dwu-cdwu.md). W przypadku użycia dynamicznej klasy zasobów zasoby różnią się w zależności od poziomu usług.

W przypadku dynamicznych klas zasobów niższy poziom usług oznacza, że prawdopodobnie konieczne będzie użycie większej klasy zasobów na potrzeby użytkownika ładującego.

## <a name="allowing-multiple-users-to-load"></a>Umożliwianie ładowania danych wielu użytkownikom

Często istnieje potrzeba, aby wielu użytkowników załadować dane do puli SQL. Ładowanie za pomocą [CREATE TABLE AS SELECT (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) wymaga uprawnień CONTROL bazy danych.  Uprawnienia kontrolne (CONTROL) zapewniają dostęp z prawem kontroli do wszystkich schematów.

Być może nie chcesz, aby wszyscy użytkownicy, którzy wykonują zadania ładowania, mieli dostęp z prawem kontroli do wszystkich schematów. Aby ograniczyć uprawnienia, użyj instrukcji DENY CONTROL.

Rozważmy na przykład dwa schematy bazy danych, schema_A dla działu A i schema_B dla działu B. Załóżmy, że użytkownicy bazy danych o nazwie user_A i user_B są użytkownikami ładującymi technologii PolyBase w działach A i B. Obaj użytkownicy otrzymali uprawnienia CONTROL do bazy danych. Osoby, które utworzyły schematy A i B, teraz blokują swoje schematy przy użyciu instrukcji DENY:

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```

User_A i user_B są teraz zablokowane ze schematu innego departamentu.

## <a name="loading-to-a-staging-table"></a>Ładowanie do tabeli przejściowej

Aby osiągnąć najszybszą szybkość ładowania dla przenoszenia danych do tabeli puli SQL, załaduj dane do tabeli przemieszczania.  Zdefiniuj tabelę przejściową jako stertę i użyj opcji dystrybucji z działaniem okrężnym.

Należy wziąć pod uwagę, że ładowanie jest zwykle procesem dwuetapowym, w którym najpierw można załadować do tabeli przemieszczania, a następnie wstawić dane do produkcyjnej tabeli puli SQL. Jeśli tabela produkcyjna korzysta z dystrybucji skrótów, łączny czas ładowania i wstawiania może być krótszy w przypadku zdefiniowania tabeli przejściowej za pomocą dystrybucji skrótów.

Ładowanie do tabeli przejściowej trwa dłużej, ale drugi etap wstawiania wierszy do tabeli produkcyjnej nie powoduje przenoszenia danych między dystrybucjami.

## <a name="loading-to-a-columnstore-index"></a>Ładowanie do indeksu magazynu kolumn

Indeksy magazynu kolumn wymagają dużej ilości pamięci w celu skompresowania danych w grupy wierszy wysokiej jakości. Najlepsza wydajność kompresji i indeksu występuje wówczas, gdy indeks magazynu kolumn kompresuje maksymalnie 1 048 576 wierszy w każdej grupie wierszy.

Jeśli pamięci jest za mało, indeks magazynu kolumn może nie osiągać maksymalnej szybkości kompresji. W tym scenariuszu z kolei efekty wydajności kwerendy. Aby zapoznać się ze szczegółowymi informacjami, zobacz [Columnstore memory optimizations (Optymalizowanie pamięci na potrzeby magazynu kolumn)](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

- Aby mieć pewność, że użytkownik ładujący ma wystarczająco dużo pamięci do uzyskania maksymalnej szybkości kompresji, korzystaj z użytkowników ładujących będących członkami średnich lub dużych klas zasobów.
- Załaduj wystarczającą liczbę wierszy, aby całkowicie wypełnić nowe grupy wierszy. Podczas ładowania zbiorczego każde 1 048 576 wierszy jest kompresowanych bezpośrednio do magazynu kolumn jako pełna grupa wierszy. W przypadku zadań ładowania z liczbą wierszy mniejszą niż 102 400 wiersze są przekazywane do magazynu delta, gdzie są przechowywane w indeksie b-drzewa.

> [!NOTE]
> Jeśli załadować zbyt mało wierszy, mogą one wszystkie trasy do deltastore i nie dostać skompresowane natychmiast do formatu magazynu kolumn.

## <a name="increase-batch-size-when-using-sqlbulkcopy-api-or-bcp"></a>Zwiększ rozmiar partii podczas korzystania z interfejsu API SqLBulkCopy lub bcp

Ładowanie z PolyBase zapewni najwyższą przepływność z puli SQL. Jeśli nie można użyć PolyBase do załadowania i należy użyć [Interfejsu API SqLBulkCopy](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) lub [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), należy rozważyć zwiększenie rozmiaru partii dla lepszej przepływności.

> [!TIP]
> Rozmiar partii od 100 K do 1M wierszy jest zalecaną linią bazową do określania optymalnej pojemności wielkości partii.

## <a name="handling-loading-failures"></a>Postępowanie w przypadku błędów ładowania

Ładowanie z użyciem tabeli zewnętrznej może zakończyć się wystąpieniem błędu *Zapytanie zostało przerwane — osiągnięto maksymalny próg odrzuceń podczas odczytu ze źródła zewnętrznego*. Ten komunikat wskazuje, że dane zewnętrzne zawierają zanieczyszczone rekordy.

Rekord danych jest uważany za za zabrudzony, jeśli spełnia jeden z następujących warunków:

- Typy danych i liczba kolumn nie są zgodne z definicjami kolumn tabeli zewnętrznej.
- Dane nie są zgodne z określonym formatem pliku zewnętrznego.

Aby poprawić zanieczyszczone rekordy, upewnij się, że definicje tabeli zewnętrznej i zewnętrznego formatu pliku są prawidłowe, a dane zewnętrzne są zgodne z tymi definicjami.

Jeśli podzbiór rekordów danych zewnętrznych jest zabrudzony, można odrzucić te rekordy dla kwerend, korzystając z opcji odrzucenia w [programie CREATE EXTERNAL TABLE (Transact-SQL).](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="inserting-data-into-a-production-table"></a>Wstawianie danych do tabeli produkcyjnej

Jednorazowe ładowanie do małej tabeli za pomocą [instrukcji INSERT](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) lub nawet okresowe ponowne ładowanie wyszukiwania to strategie, które mogą sprawdzić się w przypadku instrukcji takich jak `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  Jednak pojedyncze wstawienia są mniej wydajne niż ładowanie zbiorcze.

Jeśli w ciągu dnia wykonujesz kilka tysięcy lub więcej wstawień, utwórz partię wstawień i załaduj je zbiorczo.  Opracuj proces dołączania pojedynczych wstawień do pliku, a następnie kolejny proces okresowo ładujący ten plik.

## <a name="creating-statistics-after-the-load"></a>Tworzenie statystyk po załadowaniu

Aby poprawić wydajność zapytań, należy utworzyć statystyki dla wszystkich kolumn wszystkich tabel po pierwszym załadowaniu danych, a następnie po każdej istotnej zmianie danych. Tworzenie statystyk można wykonać ręcznie lub włączyć [AUTO_CREATE_STATISTICS](sql-data-warehouse-tables-statistics.md#automatic-creation-of-statistic).

Aby zapoznać się ze szczegółowym opisem statystyk, zobacz temat [Statystyki](sql-data-warehouse-tables-statistics.md). W poniższym przykładzie pokazano, jak ręcznie tworzyć statystyki na pięciu kolumnach tabeli Customer_Speed.

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="rotate-storage-keys"></a>Rotacja kluczy magazynu

Zalecanym rozwiązaniem w zakresie zabezpieczeń jest regularne zmienianie klucza dostępu do usługi Blob Storage. Istnieją dwa klucze magazynu dla konta usługi Blob Storage, co pozwala na przenoszenie tych kluczy.

Aby przeprowadzić rotację kluczy konta usługi Azure Storage:

Dla każdego konta magazynu, którego klucz został zmieniony, wydaj poświadczenie [ALTER DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/alter-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

Przykład:

Został utworzony klucz oryginalny

```sql
CREATE DATABASE SCOPED CREDENTIAL my_credential WITH IDENTITY = 'my_identity', SECRET = 'key1'
```

Zamień klucz 1 na klucz 2

```sql
ALTER DATABASE SCOPED CREDENTIAL my_credential WITH IDENTITY = 'my_identity', SECRET = 'key2'
```

Nie są potrzebne żadne inne zmiany podstawowych zewnętrznych źródeł danych.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat technologii PolyBase oraz projektowania procesu wyodrębniania, ładowania i transformacji (ELT, Extract, Load, and Transform), zobacz [Design ELT for SQL Data Warehouse (Projektowanie procesu ELT dla usługi SQL Data Warehouse)](design-elt-data-loading.md).
- Aby zapoznać się z samouczkiem dotyczącym ładowania, zobacz [Use PolyBase to load data from Azure blob storage to Azure SQL Data Warehouse (Ładowanie danych z usługi Azure Blob Storage do usługi Azure SQL Data Warehouse przy użyciu technologii PolyBase)](load-data-from-azure-blob-storage-using-polybase.md).
- Aby dowiedzieć się, jak monitorować ładowanie danych, zobacz [Monitor your workload using DMVs](sql-data-warehouse-manage-monitor.md) (Monitorowanie obciążenia przy użyciu widoków DMV).
