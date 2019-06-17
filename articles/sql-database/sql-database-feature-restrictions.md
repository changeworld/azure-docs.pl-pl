---
title: Ograniczeń funkcji bazy danych SQL platformy Azure | Dokumentacja firmy Microsoft
description: Ograniczeń funkcji bazy danych SQL platformy Azure zwiększa Twoje bezpieczeństwo bazy danych przez ograniczanie ich funkcji, które mogą być przez osoby atakujące w celu uzyskania dostępu do informacji w nich bazy danych.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: vainolo
ms.author: arib
ms.reviewer: vanto
manager: craigg
ms.date: 03/22/2019
ms.openlocfilehash: ac7a074e78def504a10b4daa07971f919f414a88
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66259455"
---
# <a name="azure-sql-database-feature-restrictions"></a>Ograniczeń funkcji bazy danych SQL platformy Azure

Jedno źródło typowych ataków programu SQL Server jest za pośrednictwem aplikacji sieci web, do których dostęp do bazy danych, której różne rodzaje atakami polegającymi na iniekcji SQL są używane do gromadzić informacje o bazie danych.  Najlepiej, jeśli kod aplikacji opracowania, więc nie zezwala na wstrzyknięcie kodu SQL.  Jednak w przypadku dużych-baz kodu, które zawierają kod starszej wersji i zewnętrznych, jeden nigdy nie może być się upewnić, że zostały rozwiązane wszystkie przypadki, więc wstrzyknięć kodu SQL są fakt życia, jaką należy zapewnić ochronę przed.  Celem ograniczeń funkcji jest zapobieganie niektóre rodzaje wstrzyknięcie kodu SQL przed wyciekiem informacje o bazie danych, nawet wtedy, gdy zakończy się wstrzyknięcie kodu SQL.

## <a name="enabling-feature-restrictions"></a>Włączenie ograniczeń funkcji

Włączenie ograniczeń funkcji jest wykonywane przy użyciu `sp_add_feature_restriction` procedurę składowaną w następujący sposób:

```sql
EXEC sp_add_feature_restriction <feature>, <object_class>, <object_name>
```

Następujące funkcje można ograniczyć:

| Cecha          | Opis |
|------------------|-------------|
| N'ErrorMessages' | Ograniczona, będzie maskowane wszystkie dane użytkownika w komunikacie o błędzie. Zobacz [komunikaty o błędach funkcji ograniczeń](#error-messages-feature-restriction) |
| N'Waitfor "       | Gdy z ograniczeniami, polecenie zwróci natychmiast bez żadnego opóźnienia. Zobacz [WAITFOR funkcji ograniczeń](#waitfor-feature-restriction) |

Wartość `object_class` może być `N'User'` lub `N'Role'` do oznaczania czy `object_name` jest nazwą użytkownika lub nazwy roli w bazie danych.

Poniższy przykład spowoduje, że wszystkie komunikaty o błędach dla użytkownika `MyUser` maskowane:

```sql
EXEC sp_add_feature_restriction N'ErrorMessages', N'User', N'MyUser'
```

## <a name="disabling-feature-restrictions"></a>Wyłączanie ograniczeń funkcji

Wyłączanie ograniczeń funkcji jest wykonywane przy użyciu `sp_drop_feature_restriction` procedurę składowaną w następujący sposób:

```sql
EXEC sp_drop_feature_restriction <feature>, <object_class>, <object_name>
```

Poniższy przykład wyłącza maskowania komunikat o błędzie dla użytkownika `MyUser`:

```sql
EXEC sp_drop_feature_restriction N'ErrorMessages', N'User', N'MyUser'
```

## <a name="viewing-feature-restrictions"></a>Ograniczenia funkcji przeglądania

`sys.sql_feature_restrictions` Widok przedstawia wszystkie ograniczenia funkcji aktualnie zdefiniowanych w bazie danych. Obowiązują następujące kolumny:

| Nazwa kolumny | Typ danych | Opis |
|-------------|-----------|-------------|
| class       | nvarchar(128) | Klasa obiektu, do którego mają zastosowanie ograniczenia |
| obiekt      | nvarchar(256) | Nazwa obiektu, do którego mają zastosowanie ograniczenia |
| Funkcja     | nvarchar(128) | Funkcja, która jest ograniczony |

## <a name="feature-restrictions"></a>Ograniczenia funkcji

### <a name="error-messages-feature-restriction"></a>Ograniczenia funkcji wiadomości błędu

Jednej wspólnej metody ataku iniekcji SQL jest wstrzyknięcie kodu, który powoduje, że wystąpił błąd.  Przeglądając komunikat o błędzie, osoba atakująca informacji można znaleźć informacje o systemie, włączenie dodatkowych ataków bardziej precyzyjnie.  Tego rodzaju atak może być szczególnie przydatne w przypadku, gdy aplikacja nie powoduje wyświetlenia wyników zapytania, ale wyświetla komunikaty o błędach.

Rozważmy aplikację internetową, która zawiera żądanie w postaci:

```html
http://www.contoso.com/employee.php?id=1
```

Które wykonuje następujące zapytanie bazy danych:

```sql
SELECT Name FROM EMPLOYEES WHERE Id=$EmpId
```

Jeśli wartość przekazywana jako `id` parametru żądania aplikacji sieci web jest kopiowany do zastąpienia $EmpId w zapytaniu bazy danych, osoba atakująca może spowodować, że następujące żądanie:

```html
http://www.contoso.com/employee.php?id=1 AND CAST(DB_NAME() AS INT)=0
```

I zwrócony następujący błąd może być, dzięki czemu osoba atakująca dowiedzieć się, nazwę bazy danych:

```sql
Conversion failed when converting the nvarchar value 'HR_Data' to data type int.
```

Po komunikaty o błędach Włączanie funkcji ograniczeń dla użytkownika aplikacji w bazie danych, zwracany komunikat o błędzie jest maskowany tak, aby przeciek żadne wewnętrzne informacje o bazie danych:

```sql
Conversion failed when converting the ****** value '******' to data type ******.
```

Podobnie osoba atakująca może spowodować, że następujące żądanie:

```html
http://www.contoso.com/employee.php?id=1 AND CAST(Salary AS TINYINT)=0
```

I następujący błąd będzie zwracany, dzięki czemu osoba atakująca dowiedzieć się więcej zarobków pracowników:

```sql
Arithmetic overflow error for data type tinyint, value = 140000.
```

Za pomocą ograniczeń funkcji komunikaty błędów, zwróci bazy danych:

```sql
Arithmetic overflow error for data type ******, value = ******.
```

### <a name="waitfor-feature-restriction"></a>Ograniczenia funkcji WAITFOR

Ukryta iniekcja SQL to, gdy aplikacja nie zapewnia atakującemu z wynikami wprowadzonego kodu SQL lub komunikat o błędzie, ale osoba atakująca może wywnioskować informacji z bazy danych, tworząc kwerendę warunkowego, w którym dwie gałęzie warunkowe wykonaj inną ilość czasu wykonania. Porównując czas odpowiedzi, osoba atakująca sprawdzać gałąź zostało wykonane, a tym samym Dowiedz się więcej informacji o systemie. Najprostsza wariant takiego ataku korzysta `WAITFOR` instrukcję, aby wprowadzić opóźnienie.

Rozważmy aplikację internetową, która zawiera żądanie w postaci:

```html
http://www.contoso.com/employee.php?id=1
```

Które wykonuje następujące zapytanie bazy danych:

```sql
SELECT Name FROM EMPLOYEES WHERE Id=$EmpId
```

Jeśli wartość przekazana jako parametr identyfikatora do żądań aplikacji sieci web jest kopiowany do zastąpienia $EmpId w zapytaniu bazy danych, osoba atakująca może wykonać następujące żądanie:

```html
http://www.contoso.com/employee.php?id=1; IF SYSTEM_USER='sa' WAITFOR DELAY '00:00:05'
```

I zapytania może przyjmować dodatkowe 5 sekund, jeśli `sa` użyto konta. Jeśli `WAITFOR` ograniczeń funkcji jest wyłączone w bazie danych `WAITFOR` instrukcji zostanie zignorowany, a informacje nie następuje przeciek za pomocą ataku tego typu.