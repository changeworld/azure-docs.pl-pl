---
title: Najlepsze rozwiązania dotyczące ładowania danych — usługa Azure SQL Data Warehouse | Microsoft Docs
description: Zalecenia dotyczące ładowania danych do usługi Azure SQL Data Warehouse i optymalizacji wydajności tego procesu.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: load-data
ms.date: 05/31/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seoapril2019
ms.openlocfilehash: bb170b53946a014d4aa69ce628c2e4bef7459b93
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595582"
---
# <a name="best-practices-for-loading-data-into-azure-sql-data-warehouse"></a>Najlepsze rozwiązania dotyczące ładowania danych do usługi Azure SQL Data Warehouse

Zalecenia dotyczące ładowania danych do usługi Azure SQL Data Warehouse i optymalizacji wydajności tego procesu.

## <a name="preparing-data-in-azure-storage"></a>Przygotowywanie danych w usłudze Azure Storage

Aby zminimalizować opóźnienie, warstwa magazynu i magazyn danych powinny znajdować się w jednej lokalizacji.

Podczas eksportowania danych do formatu plików ORC mogą pojawić się błędy braku pamięci Java, jeśli w danych znajdują się duże kolumny tekstu. Aby obejść to ograniczenie, można wyeksportować tylko podzestaw wszystkich kolumn.

Technologia PolyBase nie umożliwia ładowania wierszy zawierających ponad 1 000 000 bajtów danych. Dane umieszczane w plikach tekstowych w usłudze Azure Blob Storage lub Azure Data Lake Store muszą zawierać mniej niż 1 000 000 bajtów danych. Ograniczenie liczby bajtów jest niezależne od zdefiniowanego schematu tabeli.

Różne typy plików mają różną charakterystykę wydajności. Najszybsze ładowanie zapewnia użycie skompresowanych rozdzielanych plików tekstowych. Różnica w wydajności między kodowaniem UTF-8 a UTF-16 jest minimalna.

Duże pliki skompresowane można podzielić na mniejsze.

## <a name="running-loads-with-enough-compute"></a>Uruchamianie zadań ładowania przy użyciu wystarczających zasobów obliczeniowych

Aby uzyskać większą szybkość ładowania, uruchamiaj tylko jedno zadanie ładowania naraz. Jeśli nie jest to możliwe, uruchamiaj jak najmniejszą liczbę zadań ładowania jednocześnie. Jeśli spodziewasz się dużego zadania ładowania, rozważ skalowanie w górę magazynu danych przed uruchomieniem tego zadania.

Aby uruchamiać zadania ładowania przy użyciu odpowiednich zasobów obliczeniowych, utwórz użytkowników ładujących na potrzeby uruchamiania ładowania. Przypisz każdego użytkownika ładującego do określonej klasy zasobów. Aby uruchomić ładowanie, zaloguj się jako jeden z użytkowników ładujących, a następnie uruchom ładowanie. Ładowanie zostanie uruchomione przy użyciu klasy zasobów przypisanej do użytkownika.  Jest to prostsza metoda niż zmienianie klasy zasobów użytkownika odpowiednio do bieżących potrzeb.

### <a name="example-of-creating-a-loading-user"></a>Przykład tworzenia użytkownika ładującego

Ten przykład umożliwia utworzenie użytkownika ładującego dla klasy zasobów staticrc20. Pierwszym krokiem jest **nawiązanie połączenia z główną bazą danych** i utworzenie nazwy logowania.

```sql
   -- Connect to master
   CREATE LOGIN LoaderRC20 WITH PASSWORD = 'a123STRONGpassword!';
```

Nawiąż połączenie z bazą danych magazynu danych i utwórz użytkownika. W poniższym kodzie przyjęto założenie, że nawiązano połączenie z bazą danych o nazwie mySampleDataWarehouse. Widoczny jest w nim sposób utworzenia użytkownika o nazwie LoaderRC20 i nadanie mu uprawnień do kontroli w bazie danych. Następnie użytkownik jest dodawany jako członek roli bazy danych staticrc20.  

```sql
   -- Connect to the database
   CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
   GRANT CONTROL ON DATABASE::[mySampleDataWarehouse] to LoaderRC20;
   EXEC sp_addrolemember 'staticrc20', 'LoaderRC20';
```

Aby uruchomić ładowanie z zasobami należącymi do klasy zasobów staticRC20, zaloguj się jako użytkownik LoaderRC20 i uruchomić obciążenia.

Lepszym rozwiązaniem jest uruchamianie ładowania przy użyciu statycznych, a nie dynamicznych klas zasobów. Przy użyciu statycznych klas zasobów gwarantuje te same zasoby niezależnie od swojej [jednostkami magazynu danych](what-is-a-data-warehouse-unit-dwu-cdwu.md). W przypadku użycia dynamicznej klasy zasobów zasoby różnią się w zależności od poziomu usług. W przypadku dynamicznych klas zasobów niższy poziom usług oznacza, że prawdopodobnie konieczne będzie użycie większej klasy zasobów na potrzeby użytkownika ładującego.

## <a name="allowing-multiple-users-to-load"></a>Umożliwianie ładowania danych wielu użytkownikom

Często istnieje potrzeba umożliwienia ładowania danych do magazynu danych wielu użytkownikom. Ładowanie za pomocą [CREATE TABLE AS SELECT (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) wymaga uprawnień kontroli bazy danych.  Uprawnienia kontrolne (CONTROL) zapewniają dostęp z prawem kontroli do wszystkich schematów. Być może nie chcesz, aby wszyscy użytkownicy, którzy wykonują zadania ładowania, mieli dostęp z prawem kontroli do wszystkich schematów. Aby ograniczyć uprawnienia, użyj instrukcji DENY CONTROL.

Rozważmy na przykład dwa schematy bazy danych, schema_A dla działu A i schema_B dla działu B. Załóżmy, że użytkownicy bazy danych o nazwie user_A i user_B są użytkownikami ładującymi technologii PolyBase w działach A i B. Obaj użytkownicy otrzymali uprawnienia CONTROL do bazy danych. Osoby, które utworzyły schematy A i B, teraz blokują swoje schematy przy użyciu instrukcji DENY:

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```

Użytkownicy user_A i user_B nie mają teraz dostępu do schematu drugiego działu.

## <a name="loading-to-a-staging-table"></a>Ładowanie do tabeli przejściowej

Aby osiągnąć największą szybkość ładowania dla przenoszenie danych do tabeli magazynu danych, załaduj dane do tabeli przejściowej.  Zdefiniuj tabelę przejściową jako stertę i użyj opcji dystrybucji z działaniem okrężnym. 

Pamiętaj, że ładowanie to zwykle proces dwuetapowy, w którym dane są najpierw ładowane do tabeli przejściowej, a następnie wstawianie do tabeli produkcyjnej magazynu danych. Jeśli tabela produkcyjna korzysta z dystrybucji skrótów, łączny czas ładowania i wstawiania może być krótszy w przypadku zdefiniowania tabeli przejściowej za pomocą dystrybucji skrótów. Ładowanie do tabeli przejściowej trwa dłużej, ale drugi etap wstawiania wierszy do tabeli produkcyjnej nie powoduje przenoszenia danych między dystrybucjami.

## <a name="loading-to-a-columnstore-index"></a>Ładowanie do indeksu magazynu kolumn

Indeksy magazynu kolumn wymagają dużej ilości pamięci w celu skompresowania danych w grupy wierszy wysokiej jakości. Najlepsza wydajność kompresji i indeksu występuje wówczas, gdy indeks magazynu kolumn kompresuje maksymalnie 1 048 576 wierszy w każdej grupie wierszy. Jeśli pamięci jest za mało, indeks magazynu kolumn może nie osiągać maksymalnej szybkości kompresji. To z kolei wpływa na wydajność zapytań. Aby zapoznać się ze szczegółowymi informacjami, zobacz [Columnstore memory optimizations (Optymalizowanie pamięci na potrzeby magazynu kolumn)](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

- Aby mieć pewność, że użytkownik ładujący ma wystarczająco dużo pamięci do uzyskania maksymalnej szybkości kompresji, korzystaj z użytkowników ładujących będących członkami średnich lub dużych klas zasobów. 
- Załaduj wystarczającą liczbę wierszy, aby całkowicie wypełnić nowe grupy wierszy. Podczas ładowania zbiorczego każde 1 048 576 wierszy jest kompresowanych bezpośrednio do magazynu kolumn jako pełna grupa wierszy. W przypadku zadań ładowania z liczbą wierszy mniejszą niż 102 400 wiersze są przekazywane do magazynu delta, gdzie są przechowywane w indeksie b-drzewa. W przypadku załadowania zbyt małej liczby wierszy wszystkie wiersze mogą zostać przekazane do magazynu delta, a nie natychmiast skompresowane do formatu magazynu kolumn.

## <a name="handling-loading-failures"></a>Postępowanie w przypadku błędów ładowania

Ładowanie z użyciem tabeli zewnętrznej może zakończyć się wystąpieniem błędu *Zapytanie zostało przerwane — osiągnięto maksymalny próg odrzuceń podczas odczytu ze źródła zewnętrznego*. Ten komunikat wskazuje, że dane zewnętrzne zawierają zanieczyszczone rekordy. Rekord danych jest uznawany za zanieczyszczony, jeśli typy danych i liczba kolumn nie odpowiadają definicjom kolumn w tabeli zewnętrznej lub jeśli dane nie są zgodne z określonym zewnętrznym formatem pliku. 

Aby poprawić zanieczyszczone rekordy, upewnij się, że definicje tabeli zewnętrznej i zewnętrznego formatu pliku są prawidłowe, a dane zewnętrzne są zgodne z tymi definicjami. Jeśli podzestaw rekordów danych zewnętrznych jest zanieczyszczony, możesz odrzucić te rekordy w zapytaniach, używając opcji odrzucania w kodzie CREATE EXTERNAL TABLE.

## <a name="inserting-data-into-a-production-table"></a>Wstawianie danych do tabeli produkcyjnej

Jednorazowe ładowanie do małej tabeli za pomocą [instrukcji INSERT](/sql/t-sql/statements/insert-transact-sql) lub nawet okresowe ponowne ładowanie wyszukiwania to strategie, które mogą sprawdzić się w przypadku instrukcji takich jak `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  Jednak pojedyncze wstawienia są mniej wydajne niż ładowanie zbiorcze. 

Jeśli w ciągu dnia wykonujesz kilka tysięcy lub więcej wstawień, utwórz partię wstawień i załaduj je zbiorczo.  Opracuj proces dołączania pojedynczych wstawień do pliku, a następnie kolejny proces okresowo ładujący ten plik.

## <a name="creating-statistics-after-the-load"></a>Tworzenie statystyk po załadowaniu

Aby poprawić wydajność zapytań, należy utworzyć statystyki dla wszystkich kolumn wszystkich tabel po pierwszym załadowaniu danych, a następnie po każdej istotnej zmianie danych.  Można to zrobić ręcznie lub można włączyć [automatyczne tworzenie statystyk](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics#automatic-creation-of-statistic).

Aby zapoznać się ze szczegółowym opisem statystyk, zobacz temat [Statystyki](sql-data-warehouse-tables-statistics.md). Poniższy przykład pokazuje, jak ręcznie utworzyć statystyk dotyczących pięciu kolumn tabeli Customer_Speed.

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

Dla każdego konta magazynu, którego klucz został zmieniony, wydaj poświadczenie [ALTER DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/alter-database-scoped-credential-transact-sql).

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

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej na temat technologii PolyBase oraz projektowania procesu wyodrębniania, ładowania i transformacji (ELT, Extract, Load, and Transform), zobacz [Design ELT for SQL Data Warehouse (Projektowanie procesu ELT dla usługi SQL Data Warehouse)](design-elt-data-loading.md).
- Aby zapoznać się z samouczkiem dotyczącym ładowania, zobacz [Use PolyBase to load data from Azure blob storage to Azure SQL Data Warehouse (Ładowanie danych z usługi Azure Blob Storage do usługi Azure SQL Data Warehouse przy użyciu technologii PolyBase)](load-data-from-azure-blob-storage-using-polybase.md).
- Aby dowiedzieć się, jak monitorować ładowanie danych, zobacz [Monitor your workload using DMVs](sql-data-warehouse-manage-monitor.md) (Monitorowanie obciążenia przy użyciu widoków DMV).



