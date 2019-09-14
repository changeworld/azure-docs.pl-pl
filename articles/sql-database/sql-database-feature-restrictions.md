---
title: Ograniczenia funkcji Azure SQL Database | Microsoft Docs
description: Ograniczenia funkcji Azure SQL Database zwiększają bezpieczeństwo bazy danych przez ograniczenie funkcji w bazie danych, które mogą być osobami atakującymi w celu uzyskania dostępu do zawartych w nich informacji.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: barmichal
ms.author: mibar
ms.reviewer: vanto
ms.date: 03/22/2019
ms.openlocfilehash: f2fd6cb73428c69fbb27cb93377f851a4e06221d
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70959131"
---
# <a name="azure-sql-database-feature-restrictions"></a>Ograniczenia funkcji Azure SQL Database

Jednym ze wspólnych źródeł ataków SQL Server są aplikacje sieci Web, które uzyskują dostęp do bazy danych, w której różne formy ataków iniekcji SQL są używane do zgłębiać informacji o bazie danych.  W idealnym przypadku kod aplikacji jest opracowywany, dlatego nie pozwala na wstrzyknięcie kodu SQL.  Jednak w dużych bazach kodu, które obejmują starszy i zewnętrzny kod, jedna z nich nie może mieć pewności, że wszystkie przypadki zostały rozkierowane, więc iniekcje SQL jest faktem, że musimy chronić przed.  Celem ograniczeń dotyczących funkcji jest uniknięcie pewnych metod iniekcji kodu SQL przed wyciekiem informacji o bazie danych, nawet jeśli iniekcja kodu SQL zakończyła się pomyślnie.

## <a name="enabling-feature-restrictions"></a>Włączanie ograniczeń funkcji

Włączenie ograniczeń funkcji odbywa się przy użyciu `sp_add_feature_restriction` procedury składowanej w następujący sposób:

```sql
EXEC sp_add_feature_restriction <feature>, <object_class>, <object_name>
```

Można ograniczyć następujące funkcje:

| Cecha          | Opis |
|------------------|-------------|
| N'ErrorMessages' | Po ograniczeniu dane użytkowników w komunikacie o błędzie będą maskowane. Zobacz [ograniczenie funkcji komunikatów o błędach](#error-messages-feature-restriction) |
| N'Waitfor'       | Gdy jest to ograniczone, polecenie zwróci natychmiast bez opóźnień. Zobacz [ograniczenie funkcji WAITFOR](#waitfor-feature-restriction) |

Wartość `object_class` może `object_name` być `N'User'` albo w celu określenia, czy jest nazwą użytkownika, czy nazwą roli w bazie danych. `N'Role'`

Poniższy przykład powoduje, że wszystkie komunikaty o błędach `MyUser` dla użytkownika mają być maskowane:

```sql
EXEC sp_add_feature_restriction N'ErrorMessages', N'User', N'MyUser'
```

## <a name="disabling-feature-restrictions"></a>Wyłączanie ograniczeń funkcji

Wyłączenie ograniczeń funkcji odbywa się przy użyciu `sp_drop_feature_restriction` procedury składowanej w następujący sposób:

```sql
EXEC sp_drop_feature_restriction <feature>, <object_class>, <object_name>
```

Poniższy przykład wyłącza maskowanie komunikatu o błędzie dla użytkownika `MyUser`:

```sql
EXEC sp_drop_feature_restriction N'ErrorMessages', N'User', N'MyUser'
```

## <a name="viewing-feature-restrictions"></a>Wyświetlanie ograniczeń funkcji

`sys.sql_feature_restrictions` Widok przedstawia wszystkie obecnie zdefiniowane ograniczenia funkcji w bazie danych. Ma następujące kolumny:

| Nazwa kolumny | Typ danych | Opis |
|-------------|-----------|-------------|
| class       | nvarchar (128) | Klasa obiektu, do którego stosuje się ograniczenie |
| object      | nvarchar(256) | Nazwa obiektu, do którego odnosi się ograniczenie |
| funkcja     | nvarchar (128) | Funkcja, która jest ograniczona |

## <a name="feature-restrictions"></a>Ograniczenia funkcji

### <a name="error-messages-feature-restriction"></a>Ograniczenie funkcji komunikatów o błędach

Jedną z typowych metod ataku iniekcji SQL jest wstrzyknięcie kodu, który powoduje błąd.  Sprawdzając komunikat o błędzie, osoba atakująca może uzyskać informacje o systemie, co pozwala na bardziej bardziej wymierzone ataki.  Atak ten może być szczególnie przydatny, gdy aplikacja nie wyświetla wyników zapytania, ale wyświetla komunikaty o błędach.

Rozważmy aplikację sieci Web, która ma żądanie w formie:

```html
http://www.contoso.com/employee.php?id=1
```

Który wykonuje następujące zapytanie bazy danych:

```sql
SELECT Name FROM EMPLOYEES WHERE Id=$EmpId
```

Jeśli wartość przeniesiona jako `id` parametr do żądania aplikacji sieci Web jest kopiowana, aby zastąpić $EmpID w kwerendzie bazy danych, osoba atakująca może wykonać następujące żądanie:

```html
http://www.contoso.com/employee.php?id=1 AND CAST(DB_NAME() AS INT)=0
```

Zostanie zwrócony następujący błąd, który umożliwi atakującemu poznanie nazwy bazy danych:

```sql
Conversion failed when converting the nvarchar value 'HR_Data' to data type int.
```

Po włączeniu przez funkcję komunikatów o błędach ograniczenia funkcji dla użytkownika aplikacji w bazie danych, zwracany komunikat o błędzie jest maskowany, tak aby żadne wewnętrzne informacje o bazie danych nie były wyciekami:

```sql
Conversion failed when converting the ****** value '******' to data type ******.
```

Podobnie osoba atakująca może wykonać następujące żądanie:

```html
http://www.contoso.com/employee.php?id=1 AND CAST(Salary AS TINYINT)=0
```

Zostanie zwrócony następujący błąd, który umożliwi atakującemu poznanie wynagrodzenia pracownika:

```sql
Arithmetic overflow error for data type tinyint, value = 140000.
```

Przy użyciu ograniczenia funkcji w komunikatach o błędach baza danych zwróci:

```sql
Arithmetic overflow error for data type ******, value = ******.
```

### <a name="waitfor-feature-restriction"></a>Ograniczenie funkcji WAITFOR

Ślepa iniekcja SQL jest niezależna od tego, czy aplikacja nie zapewnia osobie atakującej wyników wprowadzonej przez program SQL lub komunikat o błędzie, ale atakujący może wywnioskować informacje z bazy danych przez utworzenie zapytania warunkowego, w którym dwie gałęzie warunkowe Wykonaj inną ilość czasu. Porównując czas odpowiedzi, osoba atakująca może wiedzieć, która gałąź została wykonana, a tym samym uzyskać informacje o systemie. Najprostszą odmianą tego ataku jest użycie `WAITFOR` instrukcji w celu wprowadzenia opóźnienia.

Rozważmy aplikację sieci Web, która ma żądanie w formie:

```html
http://www.contoso.com/employee.php?id=1
```

który wykonuje następujące zapytanie bazy danych:

```sql
SELECT Name FROM EMPLOYEES WHERE Id=$EmpId
```

Jeśli wartość przeniesiona jako parametr identyfikatora do żądań aplikacji sieci Web jest kopiowana w celu zastąpienia $EmpId w kwerendzie bazy danych, osoba atakująca może wykonać następujące żądanie:

```html
http://www.contoso.com/employee.php?id=1; IF SYSTEM_USER='sa' WAITFOR DELAY '00:00:05'
```

A zapytanie zajmie więcej niż 5 sekund, jeśli `sa` konto było używane. Jeśli `WAITFOR` ograniczenie funkcji jest wyłączone w bazie danych `WAITFOR` , instrukcja zostanie zignorowana i nie zostaną ujawnione informacje za pomocą tego ataku.